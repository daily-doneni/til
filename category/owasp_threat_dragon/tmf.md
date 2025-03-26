# TMF(Threat Model File) Format

## 개요
- Threat Dragon 도구에서 사용하는 파일 포맷
    - 위협 모델링 정보를 구조화해서 저장함
- `.json` 확장자로 관리
- v1, v2 서로 호환되지 않음

## 구조
- `summary`: 위협 모델의 요약 정보
    - `title`
    - `owner`
    - `description`
    - `id`
- `detail`: 시스템적 배경 정보와 메타데이터
    - `contributers`
    - `diagrams`
        - `id`
        - `title`: 해당 다이어그램의 제목
        - `diagramType`: 다이어그램 유형 → CIA, DIE, LINDDUN, PLOT4ai, STRIDE, Generic 중 하나
        - `placeholder`: 임시 안내 텍스트
        - `thumbnail`: 썸네일 경로 (Threat Dragon 내부 서버 상의 경로)
        - `version`: 다이어그램 포맷이 생성된 Threat Dragon 버전
        - `cells`: 다이어그램 안의 모든 구성요소를 배열로 가짐
            - `position`: 캔버스 상 위치
                - `x`
                - `y`
            - `size`: 캔버스 상 크기
                - `width`
                - `height`
            - `attrs`: UI 스타일 관련 속성
                - `text`
                    - `text`
                - `body`
                    - `stroke`: 외곽선 색상
                    - `strokeWidth`: 외곽선 두께께
                    - `strokeDasharray`: 외곽선의 점선 패턴을 정의하는 속성 ("선의 길이 공백 길이")
            - `visible`: 다이어그램 캔버스에 표시 여부
            - `shape`: UI 형태 및 논리적 역할을 결정 (actor, store, process, flow, boundary)
                <details>
                    <summary><b>종류</b></summary>
                    <div>
                        <li>actor: 사용자/외부 시스템</li>
                        <li>store: 데이터 저장소</li>
                        <li>process: 내부 처리 기능</li>
                        <li>flow: 흐름선(연결선)</li>
                        <li>boundary: 트러스트 경계</li>
                    </div>
                </details>

            - `ports`: 데이터 흐름을 연결할 수 있는 지점(포트) 정의
                - `groups`: 포트 방향별 설정 → 방향별 위치, 스타일 정의
                    - `top`: 상단 포트
                        - `position`: "top"
                        - `attrs`
                            - `circle`: 포트 모양이 항상 동그라미
                                - `r`: 반지름 크기(px)
                                - `magnet`: 연결선 붙일 수 있는지 (bool)
                                - `stroke`: 외곽선 색상(16진수)
                                - `strokeWidth`: 외곽선 두께
                                - `fill`: 채우기 색상(16진수)
                                - `style`
                                    - `visibility`: 표시 여부 → 포트는 기본적으로 "hidden" (마우스 오버 시 보임)
                    - `right`: 우측 포트
                        - `position`: "right"
                        - `attrs`
                            - `r`
                            - `magnet`
                            - `stroke`
                            - `strokeWidth`
                            - `fill`
                            - `style`
                                - `visibility`: "hidden"
                    - `bottom`: 하단 포트
                        - `position`: "bottom"
                        - `attrs`
                            - `r`
                            - `magnet`
                            - `stroke`
                            - `strokeWidth`
                            - `fill`
                            - `style`
                                - `visibility`: "hidden"
                    - `left`:  좌측 포트
                        - `position`: "left"
                        - `attrs`
                            - `r`
                            - `magnet`
                            - `stroke`
                            - `strokeWidth`
                            - `fill`
                            - `style`
                                - `visibility`: "hidden"
                - `items`: 각 포트의 ID 목록 리스트
                    - `group`: "top", "right", "bottom", "left" 중 하나
                    - `id`: 포트의 고유 UUID → `flow`에서 참조됨
            - `id`: 해당 cell의 고유 UUID
            - `zIndex`: 렌더링 z축 순서 (숫자가 클수록 더 위에)
            - `data`
                - `type`: 요소의 모델 타입 (tm.Actor, tm.Process, tm.Score, tm.Flow)
                - `name`: UI에 표시될 이름 (Properties—Name)
                - `description`: 간략 설명 (Properties—Description)
                - `outOfScope`: 요소가 모델 범위 외인지 bool (Properties—Out of Scope 체크박스)
                - `reasonOutOfScope`: 범위 밖인 이후 (Properties—Reason for out of scope)
                - `hasOpenThreats`: 미해결(open)된 위협이 있는지 여부 bool
                - `provideAuthentication`: (Actor에서만) 인증 기능 제공 여부 bool (Properties—Provides Authentication)
                - `threats`: 위협 목록
                    - `id`: 위협 고유 식별 UUID
                    - `title`: 위협의 제목 (Threats—Title)
                    - `status`: 위협의 이름. "N/A", "Open", "Migigated" 중 하나 (Threats—Status) 
                    - `severity`: 심각도. "TBD", "Low", "Medium", "High", "Critical" 중 하나 (Threats—Priority)
                    - `type`: STRIDE 분류 중 하나.
                                - `shape`: UI 형태 및 논리적 역할을 결정 (actor, store, process, flow, boundary)
                        <details>
                            <summary><b>종류</b></summary>
                            <div>
                                <li>Spoofing</li>
                                <li>Tampering</li>
                                <li>Repudiation</li>
                                <li>Information disclosure</li>
                                <li>Denial of service</li>
                                <li>Elavation of privilege</li>
                            </div>
                        </details>
                                       
                    - `description`: 위협 설명 (Threats—Description)
                    - `mitigation`: 대응 방법 (Threats—Mitigation)
                    - `modelType`: "STRIDE"
                    - `new`: 새 위협임을 강조할지 bool
                    - `number`: 다이어그램 내 위협 번호. 넘버링을 위해.
                    - `score`: CVSS 등급처럼 사용할 수 있음. → 빈 문자열 가능
                - `threatFrequency`: 유형별 위협이 몇 개 있는지 나타냄
            - `tools`: UI 보조 기능(도구 버튼/옵션)을 제어하기 위한 부가적인 정보
                - `items`: UI 상 제공되는 도구 버튼의 목록
                        <details>
                            <summary><b>종류</b></summary>
                            <div>
                                <li>boundary: 요소 크기 조절 및 회전</li>
                                <li>button-remove: 요소 삭제</li>
                                <li>button-clone: 요소 복제</li>
                                <li>button-link</li>
                                <li>button-settings</li>
                            </div>
                        </details>
                - `name`: null

    - `diagramsTop`: 여러 다이어그램 중 시작이 되는 다이어그램 (렌더링)
    - `reviewer`
    - `threatTop`: 위협 계층의 시작점 (렌더링)
- `version`

## 백로그
### **1. 검색 용이성 + 식별 편리성을 위해 id를 다시 입혀주어야 함**
- 현재의 uuid 형식은 해당 요소가 어떤 타입인지(Actor/Store/Flow/... 혹은 Port) 구별 불가
- 이를 구분할 수 있도록 요소의 타입인지(+Port의 경우 어떤 요소의 어떤 방향 연결점인지) 알 수 있도록 넣어주어야 함

**결론: `cells` 내 요소들의 id를 다시 만들어주자**

### **2. Trust Boundary 대신 C/S/O 라벨링**
- dragon의 trust boundary는 단순한 선분 긋기임
    - 시각적으로 나타낼 뿐, json 상에는 경계를 알기 어려움
    - 굳이 이를 이용하자면, 좌표를 이용해 계산을 하는 방법이 있음
- trust boundary가 있는 이유 -> 요소(또는 요소의 모음) 사이에 신뢰도의 차이가 있기 때문에 이를 나누어주기 위해서!
    - 이 목적을 달성하기 위해 C/S/O 라벨링이 있다
    - 따라서 굳이 trust boundary를 신경써서 넣을 것 없다

**결론: trust boundary는 시각적 표시를 위해서만 쓰고, 신뢰도의 차이는 C/S/O 레벨을 부여해서 나타내자** 