# LangChain Tool Calling & Pydantic Master Guide

> P03 실습을 Gemini와 공부하며 만든 정리. 코드 예시는 **미확인** (직접 실행해보지 않음).
> 실습 원본: [P03 tool-calling](../../practices/P03-tool-calling/) · 이론 요약: [02. Tool Calling & MCP](../../02-tool-calling-and-mcp/README.md)

이 문서는 파이썬 코드, `@tool` 데코레이터, Pydantic 스키마, 그리고 LLM 간의 내부 통신 메커니즘과 데이터 흐름에 대해 나눈 모든 대화 내용 및 핵심 개념을 완벽히 정리한 종합 가이드입니다.

---

## 1. 문제 의식 및 오해의 해소

### 1.1 JSON 딕셔너리 직접 작성 방식의 문제점
* **형태**: `{ "type": "object", "properties": { ... } }`와 같이 수동으로 JSON 스키마를 딕셔너리로 작성하여 `@tool(args_schema=...)`에 주입하는 방식.
* **단점 및 한계**:
  * **중복 작업**: 파이썬 함수 정의부(`def get_weather(...)`)와 JSON 스키마 내부에 동일한 인자명과 타입을 이중으로 적어야 함.
  * **유지보수의 어려움**: 인자 추가/수정 시 양쪽 코드를 모두 수정해야 하므로 오타나 타입 불일치 등 휴먼 에러가 발생하기 쉬움.

### 1.2 "코드가 알아서 자동 완성되나요?"에 대한 명확한 구분
* **개발자가 직접 작성해야 하는 부분 (수동)**:
  1. 입력값의 규칙과 스키마를 정의하는 Pydantic 클래스 (`WeatherInput`)
  2. 실제 비즈니스 로직을 수행하고 결과값을 리턴하는 파이썬 실행 함수 (`get_weather`)
* **프레임워크(Pydantic & LangChain)가 자동으로 처리하는 부분 (자동화)**:
  1. 파이썬 클래스를 읽어 LLM이 이해할 수 있는 정교한 **JSON Schema(규격서)**로 자동 변환.
  2. LLM이 생성한 입력 데이터가 함수로 전달되기 전 **런타임 데이터 검증(Validation)** 수행.

---

## 2. 핵심 메커니즘: 파이썬-LLM 간의 통역기 (Pydantic)

### 2.1 "코드가 곧 프롬프트가 된다"
LLM은 파이썬 코드(클래스, 객체지향 문법)를 직접 읽어서 실행할 수 없습니다. LLM이 이해할 수 있는 언어는 정해진 규격의 **JSON 스키마(텍스트/토큰)**입니다.

* **개발자의 언어**: 파이썬 문법, 타입 힌트(`str`, `bool`), Pydantic `BaseModel`, `Field(description=...)`, `Literal`
* **번역기(Bridge)**: **Pydantic + LangChain**
* **LLM의 언어**: JSON Schema (`properties`, `enum`, `required`, `title`, `description` 등)

개발자가 작성한 파이썬 코드와 `Field` 내의 설명글이 Pydantic을 거치며 완벽한 JSON 메뉴판으로 번역되어 LLM에 전달됩니다. 따라서 **"개발자의 코드가 곧 LLM의 프롬프트"** 역할을 수행하게 됩니다.

---

## 3. Pydantic 클래스 기반 스키마 정의의 우수성

`BaseModel`을 상속받아 도구의 입력 스키마를 정의할 때 얻는 핵심 이점은 다음과 같습니다.

1. **가독성 및 파이썬다운 스타일 (Pythonic Code)**:
   * 지저분한 JSON 딕셔너리 대신 파이썬 개발자에게 익숙한 클래스 구조로 명확하게 정의.
2. **타입 안전성 및 자동 완성**:
   * IDE(VS Code 등)에서 코드 작성 시 자동 완성을 지원하며, 타입 오류를 즉시 감지.
3. **강력한 런타임 데이터 검증 (Validation)**:
   * `Literal["celsius", "fahrenheit"]`와 같이 허용할 값을 명확히 제한할 수 있음.
   * LLM이 잘못된 타입이나 허용되지 않은 값을 보낼 경우, 실제 파이썬 함수가 실행되기 전에 Pydantic이 즉시 차단(ValidationError)하여 시스템 안정성 보장.
4. **풍부한 스키마 자동 생성 (`model_json_schema()`)**:
   * 수동 작성 대비 `enum`, `default`, `description`, `title` 등의 메타데이터가 누락 없이 아주 상세하고 완전한 JSON 형태로 출력됨.

---

## 4. 전체 데이터 흐름 (Execution Flow)

```
[1. 개발자] ──> Pydantic 클래스(WeatherInput) + 함수(get_weather) 작성
                     │
                     ▼
[2. LangChain/Pydantic] ──> 파이썬 코드를 읽어 LLM용 'JSON Schema' 자동 번역
                     │
                     ▼
[3. LLM] ──> JSON Schema 메뉴판을 읽고, 필요한 도구(get_weather)와 인자(JSON) 결정
                     │
                     ▼
[4. Pydantic] ──> LLM이 보낸 JSON 인자 값의 타입 및 제약조건 자동 검증(Validation)
                     │
                     ▼
[5. 파이썬 함수] ──> 검증된 안전한 데이터를 인자로 받아서 진짜 비즈니스 로직 실행 및 결과 반환
```

---

## 5. 실전 코드 예시 및 변환 결과

### 5.1 개발자가 작성하는 파이썬 코드

```python
from langchain_core.tools import tool
from pydantic import BaseModel, Field
from typing import Literal

# 1. 입력값 설계도 (Pydantic 클래스)
class WeatherInput(BaseModel):
    """Input for weather queries."""
    location: str = Field(description="City name or coordinates")
    units: Literal["celsius", "fahrenheit"] = Field(
        default="celsius",
        description="Temperature unit preference"
    )
    include_forecast: bool = Field(
        default=False,
        description="Include 5-day forecast"
    )

# 2. 실행 함수 및 툴 결합
@tool(args_schema=WeatherInput)
def get_weather(location: str, units: str = "celsius", include_forecast: bool = False) -> str:
    """Get current weather and optional forecast."""
    temp = 22 if units == "celsius" else 72
    result = f"Current weather in {location}: {temp} degrees {units[0].upper()}"
    if include_forecast:
        result += "\nNext 5 days: Sunny"
    return result
```

### 5.2 내부적으로 자동 생성되어 LLM에게 전달되는 JSON Schema
(`get_weather.args_schema.model_json_schema()` 실행 시 반환되는 데이터)

```json
{
  "title": "WeatherInput",
  "description": "Input for weather queries.",
  "type": "object",
  "properties": {
    "location": {
      "title": "Location",
      "description": "City name or coordinates",
      "type": "string"
    },
    "units": {
      "title": "Units",
      "description": "Temperature unit preference",
      "default": "celsius",
      "enum": ["celsius", "fahrenheit"],
      "type": "string"
    },
    "include_forecast": {
      "title": "Include Forecast",
      "description": "Include 5-day forecast",
      "default": false,
      "type": "boolean"
    }
  },
  "required": ["location"]
}
```

> 📌 이건 Pydantic이 만든 스키마 그대로다. 실제로 LLM API에 보낼 때는 LangChain이 이걸 한 번 더 감싸서
> `{"type": "function", "function": {"name": "get_weather", "description": "...", "parameters": {위 스키마}}}`
> 같은 공급자 형식으로 바꾼다 (02 README의 "함수 정의(스키마) 필드" 표와 같은 모양).
> 확인하려면 `from langchain_core.utils.function_calling import convert_to_openai_tool` 후 `convert_to_openai_tool(get_weather)` 출력.

---

## 6. 결론 요약

* 멀티모달이나 LLM 기술이 아무리 발전하더라도 **파이썬 코드 자체를 직접 실행하는 마법은 존재하지 않으며**, 중간에서 데이터 규격을 맞춰주는 프레임워크가 필수적입니다.
* 개발자는 **Pydantic**을 통해 가장 편하고 안전하게 **설계도**를 짜고, **LangChain**은 이를 LLM이 알아들을 수 있는 **JSON 프롬프트로 변환**해 주며, 최종적으로 파이썬 함수가 동작하도록 다리를 놓아줍니다.