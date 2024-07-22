## Governance & Audit > Resource Watcher > API V2 가이드

> Resource Watcher에 RESTful API를 호출하여 이벤트 및 리소스들의 상태 변경에 따른 알림을 받도록 설정할 수 있습니다.

## User Access Key & Secret Access Key

RESTful API를 사용하려면 먼저 User Access Key와 Secret Access Key를 발급받아야 합니다.<br/>
User Access Key와 Secret Access Key는 **API 보안 설정**에서 발급받을 수 있습니다.<br/>
콘솔 우측 상단의 계정에 마우스 포인터를 올리면 표시되는 드롭다운 메뉴에서 **API 보안 설정**을 선택한 뒤 **User Access Key ID 생성**을 클릭합니다.<br/>
보안을 위해 User Access Key와 Secret Access Key를 모두 생성하는 것을 권장합니다.

![[그림 1] API 보안 설정 위치](http://static.toastoven.net/prod_resource_watcher/img46_1_KO.png)
<center>[그림 1] API 보안 설정 위치</center>

![[그림 2] API 보안 설정 페이지](http://static.toastoven.net/prod_resource_watcher/img47_1_KO.png)
<center>[그림 2] API 보안 설정 페이지</center>

![[그림 3] UserAccessKey와 SecretAccessKey 생성](http://static.toastoven.net/prod_resource_watcher/img48_1_KO.png)
<center>[그림 3] User Access Key와 Secret Access Key 생성</center>

## Public API URL & Appkey 확인
RESTful API를 사용하려면 Appkey가 필요합니다.<br/>
콘솔 우측의 **URL & Appkey**를 클릭해 발급된 키 정보를 확인할 수 있습니다.
![[그림 4] Public API URL & Appkey](http://static.toastoven.net/prod_resource_watcher/img49_1_KO.png)
<center>[그림 4] URL & Appkey</center>

## RESTful API 가이드

<a id="common-response-body"></a>
### Common Response Body

모든 API 요청에 대해 HTTP 응답 코드는 200으로 응답합니다.<br/>
자세한 응답 결과는 Response Body의 header 항목을 참고합니다.

| Key     | Type                              | Description |
|---------|-----------------------------------|-------------|
| header  | [Header](#common-response-header) | 응답 헤더       |

- Header <a id="common-response-header"></a>

| Key           | 	Type    | 	Description                             |
|---------------|----------|------------------------------------------|
| isSuccessful  | 	boolean | 	성공 여부(true, false)                      |
| resultCode    | 	int     | 	응답 코드. 성공 시 0, 실패 시 오류 코드 반환            |
| resultMessage | 	String  | 	응답 메시지. 성공 시 "SUCCESS", 실패 시 오류 메시지 반환  |

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
  }
}
```

### 1. 알림

#### 1.1 알림 등록하기

**[기본 정보]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| POST   | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms |

| 권한 | 	
|---------------------------------|
| ResourceWatcher:Alarms.Create |

리소스에서 발생하는 이벤트에 대한 알림을 설정할 수 있습니다. <br/>


- 설정할 이벤트 정보는 **API 가이드 > 3.1. Event 목록 조회 API**의 응답 결과를 참고하십시오.<br/>
- 알림 대상에는 멤버, 알림 수신 그룹, 역할, 웹훅 타입이 있으며, 각 타입별로 대상을 설정할 수 있습니다
  리소스 대상에는 **리소스 그룹/태그**를 설정할 수 있습니다.
- **이벤트 전체**로 설정하면 특정 리소스에서 발생하는 모든 이벤트에 대해 알림을 수신하도록 설정할 수 있습니다.
- **리소스 전체**로 설정하면 리소스에 관계없이 특정 이벤트에 대한 알림을 수신하도록 설정할 수 있습니다.
- **이벤트 전체**와 **리소스 전체**는 동시에 설정할 수 없습니다.

**[Request Header]**

| Key                        | 	Value                           |
|----------------------------|----------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key    | 	Value                |
|--------|-----------------------|
| appKey  | 콘솔에서 발급받은 Appkey |


<a id="post-alarm-request"></a>
**[Request Body]**

| Key                         | 	Type                                             | 	Required | 	Description                                         |
|-----------------------------|---------------------------------------------------|-----------|------------------------------------------------------|
| alarm                       | [Alarm](#post-alarm-request-alarm)                | Yes       | 알림 정보                                                |
| alarmTargets                | [AlarmTarget[]](#post-alarm-request-alarm-target) | Yes       | 알림 수신 대상 정보                                          |
| events                      | [Event[]](#post-alarm-request-event)              | No        | 알림 대상 이벤트 목록<br/> 발생하는 모든 이벤트에 대해 수신을 원할 경우 설정하지 않음. |
| target                      | [Target](#post-alarm-request-target)              | No        | 대상 리소스 정보<br/> 리소스 관계없이 이벤트 수신을 원할 경우 설정하지 않음.       |

- Alarm <a id="post-alarm-request-alarm"></a>

| Key               | 	Type     | 	Required | 	Description                     |
|-------------------|-----------|-----------|----------------------------------|
| alarmName   | 	String   | 	Yes      | 	알림 이름 <br/> 최대 255자까지 등록 가능   |
| description | 	String   | 	No       | 	알림 설명 <br/> 최대 1,000자까지 등록 가능 |

- AlarmTarget <a id="post-alarm-request-alarm-target"></a>

| Key                 | 	Type          | 	Required | 	Description                                                                                                                                                                                         |
|---------------------|----------------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode     | String     | Yes       | 알림 대상 타입 코드<br/><br/><종류><br/>1. UUID: 단일 조직 멤버 대상(NHN Cloud 회원, IAM 멤버)<br/>2. ROLE: 조직 역할, 프로젝트 역할 그룹, 프로젝트 역할<br/>3. ALARM\_KEY: 조직 알림 수신 그룹, 프로젝트 알림 수신 그룹<br/>4. WEBHOOK: 웹훅 |
| alarmTarget         | 	String        | 	No       | 	알림 대상 정보<br/><타입별 설정 값><br/> 1. **UUID**: 멤버 UUID <br/>2. **ROLE**: 역할 ID(예. ADMIN)<br/>3. **ALARM\_KEY**: 알림 수신 그룹 ID<br/>4. **WEBHOOK**: 입력하지 않음                                                  |
| emailAlarm          | 	String        | 	No       | 	이메일 수신 여부<br/>1. **Y**: 이메일 수신<br/>2. **N**: 이메일 수신하지 않음<br/>WEBHOOK, ALARM\_KEY는 입력하지 않음.                                                                                                          |
| smsAlarm            | 	String        | 	No       | 	SMS 수신 여부<br/>1. **Y**: SMS 수신<br/>2. **N**: SMS 수신하지 않음<br/>WEBHOOK, ALARM\_KEY는 입력하지 않음.                                                                                                          |
| webhookUrl          | 	String        | 	No       | 웹훅 URL 주소<br/>http:// 또는 https://로 시작해야 함.<br/> 알림 대상 타입 **WEBHOOK** 설정 시 입력                                                                                                              |
| webhookSecret       | 	String        | 	No       | 	웹훅 비밀 키<br/> 알림 대상 타입 **WEBHOOK** 설정 시 입력                                                                                                                                                |

- Event <a id="post-alarm-request-event"></a>

| Key        | 	Type    | 	Required | 	Description |
|------------|----------|-----------|--------------|
| productId  | 	String  | 	Yes      | 	서비스 ID      |
| eventId    | 	String  | 	Yes      | 	이벤트 ID      |

productId, eventId 값은 **API 가이드 > 3.1 Event 목록 조회 API 응답 값**을 참고하세요.

- Target <a id="post-alarm-request-target"></a>

| Key              | 	Type         | 	Required | 	Description      |
|------------------|---------------|-----------|-------------------|
| resourceGroupIds | 	String[]     | 	No       | 	리소스 그룹 ID 목록     |
| resourceTagIds   | 	Long[]       | 	No       | 	리소스 태그 ID 목록     |

- 예제:
```json
{
  "alarm": {
    "alarmName": "",
    "description": ""
  },
  "alarmTargets": [
    {
      "alarmTarget": "",
      "alarmTargetTypeCode": "",
      "emailAlarm": "",
      "smsAlarm": "",
      "webhookSecret": "",
      "webhookUrl": ""
    }
  ],
  "events": [
    {
      "eventId": "",
      "productId": ""
    }
  ],
  "target": {
    "resourceGroupIds": [
      ""
    ],
    "resourceTagIds": [
      ""
    ]
  }
}
```

**[Response Body]**

* 참고 : [Common Response Body](#common-response-body)와 동일합니다.

#### 1.2 알림 조회

**[기본 정보]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| GET    | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 권한                             | 	
|--------------------------------|
| ResourceWatcher:Alarms.Get |

등록된 알림에 대해 조회합니다.

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key     | 	Value            |
|---------|-------------------|
| appKey  | 	콘솔에서 발급받은 Appkey |
| alarmId | 	조회할 알림의 ID       |

**[Response Body]**


| Key                       | 	Type                                                                           | 	Description                 |
|---------------------------|---------------------------------------------------------------------------------|------------------------------|
| alarm                     | 	[Alarm](#post-alarm-response-alarm)                                            | 	알림 정보        |
| alarmTargetAlarmKeys      | 	[AlarmTargetAlarmKey[]](#post-alarm-response-alarm-target-alarm-key)           | 	알림 수신 대상 ALARM_KEY 정보 목록      |
| alarmTargetMemberProfiles | 	[AlarmTargetMemberProfile[]](#post-alarm-response-alarm-target-member-profile) | 	알림 수신 대상 UUID 정보 목록   |
| alarmTargetRoles          | 	[AlarmTargetRole[]](#post-alarm-response-alarm-target-role)                    | 	알림 수신 대상 ROLE 목록  |
| alarmTargets              | 	[AlarmTarget[]](#post-alarm-response-alarm-target)                             | 	알림 수신 대상 목록       |
| events                    | 	[Event[]](#post-alarm-response-event)                                          | 	알림 이벤트 목록                   |
| target                    | 	[Target](#post-alarm-response-target)                                          | 	대상 리소스 정보         |

- Alarm <a id="post-alarm-response-alarm"></a>

| Key                  | 	Type                                        | 	Description                                                                      |
|----------------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId              | String                                       | 알림 ID                                                                             |
| alarmName            | String                                       | 알림 이름                                                                             |
| alarmRule            | [AlarmRule](#post-alarm-response-alarm-rule) | 알림 규칙 상세 정보                                                                       |
| alarmStatusCode      | String                                       | 알림 상태 코드<br/><br/><종류><br/>1. STABLE: 활성화<br/>2. DISABLED: 비활성화<br/>3. CLOSED: 삭제 |
| appKey               | String                                       | Appkey 정보                                                                         |
| cabAlarmKey          | String                                       | 알림 수신 그룹 ID                                                                       |
| delDatetime          | Date                                         | 삭제 일시                                                                             |
| description          | String                                       | 알림 설명                                                                             |
| modDatetime          | Date                                         | 수정 일시                                                                             |
| operatorUuid         | String                                       | 최종 수정한 사용자 UUID                                                                   |
| regDatetime          | Date                                         | 등록 일시                                                                             |

- AlarmRule <a id="post-alarm-response-alarm-rule"></a>

| Key                     | 	Type    | 	Description                                                                           |
|-------------------------|----------|----------------------------------------------------------------------------------------|
| alarmRuleId             | String   | 알림 규칙 ID                                                                               |
| alarmRuleStatusCode     | String   | 알림 규칙 상태 코드<br/><br/><종류><br/>1. STABLE: 활성화<br/>2. DISABLED: 비활성화<br/>3. CLOSED: 삭제 |
| alarmRuleName           | String   | 알림 규칙 이름                                                                               |
| alarmRuleDescription    | String   | 알림 규칙 설명                                                                               |
| resourceTypes           | String[] | 알림 규칙이 적용되는 대상 리소스 타입 코드 목록<br/>빈 값이면 전체 리소스 타입을 대상으로 함<br/>String 타입 리스트              |

- AlarmTargetAlarmKey <a id="post-alarm-response-alarm-target-alarm-key"></a>

| Key            | 	Type      | 	Description |
|----------------|------------|--------------|
| alarmKey       | String     | 알림 키         |
| alarmGroupName | String     | 알림 수신 그룹명    |
| alarmGroupDesc | String     | 알림 수신 그룹 설명  |


- AlarmTargetMemberProfile <a id="post-alarm-response-alarm-target-member-profile"></a>

| Key             | 	Type      | 	Description                                       |
|-----------------|------------|----------------------------------------------------|
| uuid            | String     | 멤버 UUID                                            |
| memberType      | String     | 멤버 구분<br/><br/><종류><br/>1. TOAST\_CLOUD<br/>2. IAM |
| name            | String     | 멤버 이름                                              |
| corporationName | String     | 업체 이름                                              |
| email           | String     | 멤버 이메일                                             |
| userId          | String     | 멤버 ID                                              |

- AlarmTargetRole <a id="post-alarm-response-alarm-target-role"></a>

| Key           | 	Type      | 	Description |
|---------------|------------|--------------|
| type          | String     | 역할 타입        |
| roleId        | String     | 역할 ID        |
| roleName      | String     | 역할명          |
| description   | String     | 역할 설명        |

- AlarmTarget <a id="post-alarm-response-alarm-target"></a>

| Key                     | 	Type        | 	Description                                                                                                                                                         |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode     | String       | 알림 대상 타입 코드<br/><br/><종류><br/>1. UUID: 단일 조직 멤버 대상(NHN Cloud 회원, IAM 멤버)<br/>2. ROLE: 조직 역할, 프로젝트 역할 그룹, 프로젝트 역할<br/>3. ALARM\_KEY: 조직 알림 수신 그룹, 프로젝트 알림 수신 그룹<br/>4. WEBHOOK: 웹훅 |
| alarmTarget             | String       | 알림 대상 정보                                                                                                                                                             |
| emailAlarm              | String       | 이메일 수신 여부(Y, N)                                                                                                                                                      |
| smsAlarm                | string       | SMS 수신 여부(Y, N)                                                                                                                                                      |
| webhookUrl              | string       | 웹훅 URL 주소 |
| webhookSecret           | string       | 웹훅 비밀 키                                                                                                                                                         |

- Event <a id="post-alarm-response-event"></a>

| Key          | 	Type        | 	Description   |
|--------------|--------------|----------------|
| productId    | 	String      | 	상품 ID         |
| eventId      | 	String      | 	이벤트 ID        |


- Target <a id="post-alarm-response-target"></a>

| Key            | 	Type                                                  | 	Description     |
|----------------|--------------------------------------------------------|------------------|
| resourceGroups | [ResourceGroup[]](#post-alarm-response-resource-group) | 리소스 그룹 목록        |
| resourceTags   | [ResourceTag[]](#post-alarm-response-resource-tag)     | 리소스 태그 목록        |


- ResourceGroup <a id="post-alarm-response-resource-group"></a>

| Key               | 	Type   | 	Description   |
|-------------------|---------|----------------|
| resourceGroupId   | String  | 	리소스 그룹 ID 목록  |
| resourceGroupName | String  | 리소스 태그 ID 목록   |

- ResourceTag <a id="post-alarm-response-resource-tag"></a>

| Key        | 	Type                                                       | 	Description               |
|------------|-------------------------------------------------------------|----------------------------|
| tagId      | Long                                                        | 리소스 태그 ID                  |
| tagGroupId | Long                                                        | 리소스 태그 그룹 ID               |
| tagName    | String                                                      | 리소스 태그 이름                  |  
| resourceTagTypeCode    | String                                                      | 리소스 태그 유형 코드(DEFAULT, NORMAL) |
| regDatetime    | Date                                                        | 리소스 태그 등록 일시               |
| modDatetime    | Date                                                        | 리소스 태그 수정 일시               |
| resourceTagGroup    | [ResourceTagGroup](#post-alarm-response-resource-tag-group) | 리소스 태그 그룹                  |

- ResourceTagGroup <a id="post-alarm-response-resource-tag-group"></a>

| Key        | 	Type  | 	Description        |
|------------|--------|---------------------|
| tagGroupId      | Long   | 리소스 태그 그룹 ID        |
| tagGroupKey | String | 리소스 태그 키            |
| appKey    | String | 앱키                  |  
| creationTypeCode    | String | 생성 유형(USER, SYSTEM) |
| regDatetime    | Date   | 리소스 태그 그룹 등록 일시     |
| modDatetime    | Date | 리소스 태그 그룹 수정 일시   |

- 예제:

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": ""
  },
  "alarm": {
    "alarmId": "",
    "alarmName": "",
    "alarmRule": {
      "alarmRuleDescription": "",
      "alarmRuleId": "",
      "alarmRuleName": "",
      "alarmRuleStatusCode": "",
      "resourceTypes": [
        ""
      ]
    },
    "alarmStatusCode": "",
    "appKey": "",
    "cabAlarmKey": "",
    "delDatetime": "yyyy-MM-ddTHH:mm:SS",
    "description": "",
    "modDatetime": "yyyy-MM-ddTHH:mm:SS",
    "operatorUuid": "",
    "regDatetime": "yyyy-MM-ddTHH:mm:SS"
  },
  "alarmTargetAlarmKeys": [
    {
      "alarmGroupDesc": "",
      "alarmGroupName": "",
      "alarmKey": ""
    }
  ],
  "alarmTargetMemberProfiles": [
    {
      "corporationName": "",
      "email": "",
      "memberType": "",
      "name": "",
      "userId": "",
      "uuid": ""
    }
  ],
  "alarmTargetRoles": [
    {
      "description": "",
      "roleId": "",
      "roleName": "",
      "type": ""
    }
  ],
  "alarmTargets": [
    {
      "alarmTarget": "",
      "alarmTargetTypeCode": "",
      "emailAlarm": "",
      "smsAlarm": "",
      "webhookSecret": "",
      "webhookUrl": ""
    }
  ],
  "events": [
    {
      "eventId": "",
      "productId": ""
    }
  ],
  "target": {
    "resourceGroups": [
      {
        "resourceGroupId": "",
        "resourceGroupName": ""
      }
    ],
    "resourceTags": [
      {
        "tagId": 0,
        "tagGroupId" : 0,
        "tagName": "",
        "resourceTagTypeCode" : "",
        "regDatetime": "yyyy-MM-ddTHH:mm:SS",
        "modDatetime": "yyyy-MM-ddTHH:mm:SS",
        "resourceTagGroup" : {
            "tagGroupId": 0,
            "tagGroupKey": "",
            "appKey": "",
            "creationTypeCode": "",
            "regDatetime": "yyyy-MM-ddTHH:mm:SS",
            "modDatetime": "yyyy-MM-ddTHH:mm:SS"
        }
      }
    ]
  }
}
```


#### 1.3 알림 리스트 조회

**[기본 정보]**

| Method | 	URI                                                         |
|--------|--------------------------------------------------------------|
| POST   | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/search |

| 권한                             | 	
|--------------------------------|
| ResourceWatcher:Alarms.List |

등록한 이벤트 알림 리스트를 조회합니다.
- 요청에 검색 조건을 통해 원하는 알림 목록을 조회할 수 있습니다.
- 페이징을 지원합니다.

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key    | 	Value             |
|--------|--------------------|
| appKey | 	콘솔에서 발급받은 Appkey |


**[Query Parameter]**

| Key  | 	Value                                      | Required |
|------|---------------------------------------------|----------|
| page | 	조회할 페이지 번호<br/>Default 값: 0                | No       |
| size | 	조회할 알림 개수<br/>Default 값: 10                | No       |
| sort | 정렬 대상 및 방식<br/>Default 값: modDatetime, DESC | No       |

**[Request Body]**

| Key                | 	Type    | Required | 	Description                                                                                                                     |
|--------------------|----------|----------|----------------------------------------------------------------------------------------------------------------------------------|
| alarmIds           | String[] | No       | 알림 ID 목록<br/>String 타입 리스트                                                                                                       |
| alarmNameAnyLike   | String   | No       | 알림 이름(입력 값을 포함하는 모든 알림 검색)                                                                                                       |
| alarmRuleIds       | String[] | No       | 알림 규칙 ID 목록<br/>String 타입 리스트                                                                                                    |
| alarmStatusCodes   | String[] | No       | 알림 상태<br/>String 타입 리스트<br/>Default 값: STABLE, DISABLED<br/><br/><종류><br/>1. STABLE: 활성화<br/>2. DISABLED: 비활성화<br/>3. CLOSED: 삭제 |
| descriptionAnyLike | String   | No       | 알림 설명(입력 값을 포함하는 모든 알림 검색)                                                                                                       |
| modDateFrom        | Date     | No       | 마지막 수정 일시 시작                                                                                                                     |
| modDateTo          | Date     | No       | 마지막 수정 일시 종료                                                                                                                     |
| operatorUuids      | String[] | No       | 마지막 수정자 UUID 목록<br/>String 타입 리스트                                                                                                |
| resourceGroupIds   | String[] | No       | 리소스 그룹 목록<br/>String 타입 리스트                                                                                                      |

```json
{
  "alarmIds": [
    ""
  ],
  "alarmNameAnyLike": "",
  "alarmRuleIds": [
    ""
  ],
  "alarmStatusCodes": [
    ""
  ],
  "descriptionAnyLike": "",
  "modDateFrom": "yyyy-MM-ddTHH:mm:SS",
  "modDateTo": "yyyy-MM-ddTHH:mm:SS",
  "operatorUuids": [
    ""
  ],
  "resourceGroupIds": [
    ""
  ]
}
```


**[Response Body]**

| Key        | 	Type                                 | Description |
|------------|---------------------------------------|-------------|
| alarms     | [Alarm[]](#list-alarm-response-alarm) | 알림 목록       |
| totalItems | Long                                  | 전체 개수       |


- Alarm <a id="list-alarm-response-alarm"></a>

| Key             | 	Type                                        | 	Description                                                                      |
|-----------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId         | String                                       | 알림 ID                                                                             |
| alarmName       | String                                       | 알림 이름                                                                             |
| alarmRule       | [AlarmRule](#list-alarm-response-alarm-rule) | 알림 규칙 상세 정보                                                                       |
| alarmStatusCode | String                                       | 알림 상태 코드<br/><br/><종류><br/>1. STABLE: 활성화<br/>2. DISABLED: 비활성화<br/>3. CLOSED: 삭제 |
| appKey          | String                                       | Appkey 정보                                                                         |
| cabAlarmKey     | String                                       | 알림 수신 그룹 ID                                                                       |
| description     | String                                       | 알림 설명                                                                             |
| operatorUuid    | String                                       | 최종 수정한 사용자 UUID                                                                   |
| delDatetime     | Date                                         | 삭제 일시                                                                             |
| modDatetime     | Date                                         | 수정 일시                                                                             |
| regDatetime     | Date                                         | 등록 일시                                                                             |

- AlarmRule <a id ="list_alarm_response_alarm_rule"></a>

| Key                  | 	Type    | 	Description                                                                         |
|----------------------|----------|--------------------------------------------------------------------------------------|
| alarmRuleId          | String   | 알림 규칙 ID                                                                             |
| alarmRuleStatusCode  | String   | 알림 규칙 상태 코드<br/><br/><종류><br/>1. STABLE: 활성화<br/>2. DISABLED: 비활성화<br/>3. CLOSED: 삭제 |
| alarmRuleName        | String   | 알림 규칙 이름                                                                             |
| alarmRuleDescription | String   | 알림 규칙 설명                                                                             |
| resourceTypes        | String[] | 알림 규칙이 적용되는 대상 리소스 타입 코드 목록<br/>빈 값이면 전체 리소스 타입을 대상으로 함<br/>String 타입 리스트            |

- 예제:

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": ""
  },
  "alarms": [
    {
      "alarmId": "",
      "alarmName": "",
      "alarmRule": {
        "alarmRuleDescription": "",
        "alarmRuleId": "",
        "alarmRuleName": "",
        "alarmRuleStatusCode": "",
        "resourceTypes": [
          ""
        ]
      },
      "alarmStatusCode": "",
      "appKey": "",
      "cabAlarmKey": "",
      "delDatetime": "yyyy-MM-ddTHH:mm:SS",
      "description": "",
      "modDatetime": "yyyy-MM-ddTHH:mm:SS",
      "operatorUuid": "",
      "regDatetime": "yyyy-MM-ddTHH:mm:SS"
    }
  ],
  "totalItems": 0
}
```

#### 1.4. 알림 수정

**[기본 정보]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| PUT    | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 권한                             | 	
|--------------------------------|
| ResourceWatcher:Alarms.Update |

등록된 알림을 수정합니다.
- 요청한 내용을 모두 변경하므로 **변경사항이 없는 내용도 기존 설정 값을 전송해야 합니다**.

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key     | Value            |
|---------|------------------|
| appKey  | 콘솔에서 발급받은 Appkey |
| alarmId | 수정할 알림의 ID       |


**[Request Body]**

* 참고 : [Common Response Body](#common-response-body)와 동일합니다.

#### 1.5. 알림 삭제

**[기본 정보]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| DELETE | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 권한                             | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

등록된 알림을 삭제합니다.

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key     | Value              |
|---------|--------------------|
| appKey  | 콘솔에서 발급받은 Appkey   |
| alarmId | 삭제할 알림의 ID         |


**[Response Body]**

* 참고 : [Common Response Body](#common-response-body)와 동일합니다.


#### 1.6. 알림 다건 삭제


**[기본 정보]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| DELETE | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms |

| 권한                             | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

등록된 알림을 여러 개 삭제합니다.

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	콘솔에서 발급받은 User Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	콘솔에서 발급받은 Secret Access Key |

**[Path Variable]**

| Key     | Value            |
|---------|------------------|
| appKey  | 콘솔에서 발급받은 Appkey |


**[Query Parameter]**

| Key      | Value                                     | Required |
|----------|-------------------------------------------|----------|
| alarmIds | 삭제할 알림 ID의 목록<br/>반드시 1개 이상의 값을 입력해야 합니다. | Yes      |

**[Response Body]**

* 참고 : [Common Response Body](#common-response-body)와 동일합니다.