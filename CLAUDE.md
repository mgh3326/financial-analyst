# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

Google ADK(Agent Development Kit)를 사용하여 구축된 멀티 에이전트 아키텍처 기반의 금융 분석 에이전트 시스템입니다. LiteLLM을 통해 OpenAI 모델에 접근하고, Firecrawl을 통한 웹 검색 기능이 통합되어 있습니다.

## 개발 환경 설정

### 환경 요구사항
- Python >=3.13
- `.env` 파일에 필요한 환경 변수:
  - `FIRECRAWL_API_KEY`: 웹 검색 기능을 위한 API 키
  - LiteLLM 인증 정보 (OpenAI 접근용)

### 설치
```bash
# uv(권장) 또는 pip을 사용하여 의존성 설치
uv sync
# 또는
pip install -e .
```

### 개발 의존성
```bash
# 개발 의존성 포함 설치
uv sync --group dev
```

## 아키텍처

### 멀티 에이전트 시스템
Google ADK를 사용한 계층적 에이전트 아키텍처 구현:

- **루트 에이전트**: `weather_agent` (`financial_advisor/agent.py:35`에 정의)
  - 날씨 관련 도구를 가진 주요 에이전트
  - 모델: LiteLLM을 통한 OpenAI GPT-4o

- **서브 에이전트**: `geo_agent` (`financial_advisor/agent.py:15`에 정의)
  - 지리 관련 질문 처리
  - 지리 쿼리 감지 시 부모 에이전트로부터 전환됨

### 에이전트 구성 패턴
에이전트는 다음 요소로 생성됩니다:
- `name`: 에이전트 식별자
- `instruction`: 에이전트 동작을 정의하는 시스템 프롬프트
- `model`: LiteLLM 모델 래퍼
- `tools`: 에이전트가 사용할 수 있는 호출 가능한 함수 리스트
- `sub_agents`: 전환 가능한 하위 에이전트
- `description`: 부모 에이전트가 전환 시점을 결정하는데 사용

### 도구 시스템
도구는 docstring이 있는 일반 Python 함수로 정의됩니다(`financial_advisor/agent.py:7-12` 참조). ADK가 자동으로 에이전트가 호출 가능한 도구로 변환합니다.

### 웹 검색 통합
`tools.py`의 `web_search_tool`은 Firecrawl을 사용하여:
1. 쿼리로 웹 검색
2. 최대 5개 결과 스크래핑
3. 콘텐츠를 Markdown으로 변환
4. 정제된 구조화 데이터 반환 (title, url, markdown)

## 주요 파일

- `financial_advisor/agent.py`: 계층 구조를 가진 멀티 에이전트 시스템 정의
- `tools.py`: Firecrawl 기반 웹 검색 도구 구현
- `pyproject.toml`: 프로젝트 의존성 및 설정
- `.env`: API 키 및 인증 정보 (git에서 추적되지 않음)

## 참고사항

- 프로젝트명은 금융 분석을 시사하지만, 현재 구현은 날씨/지리 예제입니다
- `main.py`는 PyCharm 보일러플레이트이며 실제 애플리케이션의 일부가 아닙니다
- 에이전트 시스템은 `financial_advisor/__init__.py`를 통해 agent 모듈을 import하여 노출됩니다
