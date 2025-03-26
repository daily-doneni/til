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
            - `position`: (*!Flow*)캔버스 상 위치
                - `x`
                - `y`
            - `source`: (*Flow*) 출발 위치
                - `x`
                - `y`
            - `target`: (*Flow*) 도착 위치
                - `cell`
                - `port`
            - `vertices`: (*Flow*) 선분 상의 경로를 구부리기 위한 중간 꺾임 포인트
            - `size`: (*!Flow*)캔버스 상 크기
                - `width`
                - `height`
            - `width`: (*Flow*) 너비
            - `height`: (*Flow*) 높이
            - `connector`: (*Flow*) 직선("normal") 또는 곡선("smooth")
            - `attrs`: UI 스타일 관련 속성
                - `text`: 
                    - `text`
                - `body`: (*Actor*) 도형
                    - `stroke`: 외곽선 색상
                    - `strokeWidth`: 외곽선 두께께
                    - `strokeDasharray`: 외곽선의 점선 패턴을 정의하는 속성 ("선의 길이 공백 길이")
                - `topLine`: (*Store*) 상단 선분
                    - `stroke`
                    - `strokeWidth`
                    - `strokeDasharray`
                - `bottomLine`: (*Store*) 하단단 선분
                    - `stroke`
                    - `strokeWidth`
                    - `strokeDasharray`
                - `line`: (*Flow*) 연결선
                    - `stroke`: 선분 색상 16진수
                    - `targetMarker`: 끝에 붙는 화살표 스타일
                        - `name`: 종류 <u>"block"</u>, "", ...
                    - `sourceMarker`: 머리에 붙는 화살표 스타일
                        - `name`: <u>""</u>
                    - `strokeDasharray`
            - `visible`: 다이어그램 캔버스에 표시 여부
            - `shape`: UI 형태 및 논리적 역할을 결정 ("actor", "store", "process", "flow", "boundary")
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
            - `labels`: (*Flow*) 선분에 표시되는 이름
            - `data`
                - `type`: 요소의 모델 타입 (tm.Actor, tm.Process, tm.Score, tm.Flow)
                - `name`: UI에 표시될 이름 (Properties—Name)
                - `description`: 간략 설명 (Properties—Description)
                - `outOfScope`: 요소가 모델 범위 외인지 bool (Properties—Out of Scope 체크박스)
                - `reasonOutOfScope`: 범위 밖인 이후 (Properties—Reason for out of scope)
                - `hasOpenThreats`: 미해결(open)된 위협이 있는지 여부 bool
                - `provideAuthentication`: (*Actor*) 인증 기능 제공 여부 bool (Properties—Provides Authentication)
                - `isALog`: (*Store*) 로그 여부 bool (Properties—is a Log)
                - `isEncrypted`: (*Store*) 저장소 암호화 여부 bool (Properties—Encrypted)
                - `storesInventory`: (*Store*) 재고정보(=운영적 가치가 높은 데이터) 저장 여부 bool (Properties—Stores Inventory)
                - `storesCredentials`: (*Store*) 민감한 자격정보 저장 여부 bool (Properties—Stores Credentials)
                - `isSigned`: (*Store*) 데이터 무결성 보호(서명) 여부 bool (Properties—Signed)
                - `privilegeLevel`: (*Process*) 권한 수준 string(자유) (Properties—Privilege Level)
                - `handleCardPayment`: (*Process*) 결제 정보 처리 여부 bool (Properties—Card Payment)
                - `isWebApplication`: (*Process*) 웹앱 기반 여부 bool (Properties—Web Application)
                - `handleGoodsOrServices`: (*Process*) 실제 상품/서비스 처리 여부 (Properties—Goods or Services)
                - `isBidirectional` : (*Flow*) 양방향 통신 여부 bool
                - `isEncrypted`: (*Flow*) 암호화 여부 bool
                - `isPublicNetwork`: (*Flow*) 공용망 여부 bool
                - `protocol` : (*Flow*) 사용하는 프로토콜 string("" 가능)
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
                        <details>
                            <summary><b>종류</b></summary>
                            <div>
                                <li>spoofing</li>
                                <li>tampering</li>
                                <li>repudiation</li>
                                <li>informationDisclosure</li>
                                <li>denialOfService</li>
                                <li>elavationOfPrevilege</li>
                            </div>
                        </details>

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

### 유형별 섹션 정리
#### 1. `tm.Actor`
- position
- size
- attrs
    - text
    - body
- visible
- shape
- port
    - groups
        - top
            - position
            - attrs
                - circle
                    - r
                    - magnet
                    - stroke
                    - strokeWidth
                    - fill
                    - style
                        - visibility
        - right (이하 동일)
        - bottom (이하 동일)
        - left (이하 동일)
    - items 
        - group
        - id
- id
- zIndex
- data
    - type
    - name
    - description
    - outOfScope
    - reasonOutOfScope
    - hasOpenThreats
    - providesAuthentication
    - threats
        - id
        - title
        - status
        - severity
        - type
        - description
        - mitigation
        - modelType
        - new
        - number
        - score
    - threatFrequency
        - spoofing
        - tampering
        - repudiation
        - informationDiscloure
        - denialOfService
        - elaviationOfPrivilege
- tools
 - items
 - name
#### 2. `tm.Store`
- position
- size
- attrs
    - text
    - topLine
    - bottomLine
- visible
- shape
- port
    - groups
        - top
            - position
            - attrs
                - circle
                    - r
                    - magnet
                    - stroke
                    - strokeWidth
                    - fill
                    - style
                        - visibility
        - right (이하 동일)
        - bottom (이하 동일)
        - left (이하 동일)
    - items 
        - group
        - id
- id
- zIndex
- data
    - type
    - name
    - description
    - outOfScope
    - reasonOutOfScope
    - hasOpenThreats
    - isALog
    - isEncrypted
    - isSigned
    - storesCredentials
    - storesInventory
    - threats
        - id
        - title
        - status
        - severity
        - type
        - description
        - mitigation
        - modelType
        - new
        - number
        - score
    - threatFrequency
        - spoofing
        - tampering
        - repudiation
        - informationDiscloure
        - denialOfService
        - elaviationOfPrivilege
- tools
 - items
 - name



### 유형별 섹션 정리표 (참고용)
| 필드                   | tm.Actor | tm.Store | tm.Process | tm.Flow |
|------------------------|----------|----------|------------|---------|
| description            | ✅       | ✅       | ✅         | ✅      |
| handlesCardPayment     |          |          | ✅         |         |
| handlesGoodsOrServices |          |          | ✅         |         |
| hasOpenThreats         | ✅       | ✅       | ✅         | ✅      |
| isALog                 |          | ✅       |            |         |
| isBidirectional        |          |          |            | ✅      |
| isEncrypted            |          | ✅       |            | ✅      |
| isPublicNetwork        |          |          |            | ✅      |
| isSigned               |          | ✅       |            |         |
| isWebApplication       |          |          | ✅         |         |
| name                   | ✅       | ✅       | ✅         | ✅      |
| outOfScope             | ✅       | ✅       | ✅         | ✅      |
| privilegeLevel         |          |          | ✅         |         |
| providesAuthentication | ✅       |          |            |         |
| reasonOutOfScope       | ✅       | ✅       | ✅         | ✅      |
| storesCredentials      |          | ✅       |            |         |
| storesInventory        |          | ✅       |            |         |
| threats                | ✅       | ✅       | ✅         | ✅      |
| threatFrequency        | ✅       | ✅       | ✅         | ✅      |
| type                   | ✅       | ✅       | ✅         | ✅      |

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