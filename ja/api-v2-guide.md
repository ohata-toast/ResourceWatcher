## Governance & Audit > Resource Watcher > API V2ガイド

> Resource WatcherにRESTful APIを呼び出して、イベントやリソースの状態変更に伴う通知を受けるように設定できます。

## User Access Key & Secret Access Key

RESTful APIを使うためには、まず、User Access KeyとSecret Access Keyを発行する必要があります。<br/>
User Access KeyとSecret Access Keyは **APIセキュリティ設定**で発行できます。<br/>
コンソール右上のアカウントにマウスポインタを合わせると表示されるドロップダウンメニューから**APIセキュリティ設定**を選択した後、**User Access Key ID作成**をクリックします。<br/>
セキュリティのため、User Access KeyとSecret Access Keyの両方を作成することを推奨します。

![[図1] APIセキュリティ設定位置](http://static.toastoven.net/prod_resource_watcher/img46_JP.png)
<center>[図1] APIセキュリティ設定位置</center>

![[図2] APIセキュリティ設定ページ](http://static.toastoven.net/prod_resource_watcher/img47_JP.png)
<center>[図2] APIセキュリティ設定ページ</center>

![[図3] UserAccessKeyとSecretAccessKey作成](http://static.toastoven.net/prod_resource_watcher/img48_JP.png)
<center>[図3] User Access KeyとSecret Access Key作成</center>

## Public API URL & Appkey確認
RESTful APIを使用するにはAppkeyが必要です。<br/>
コンソール右側の**URL & Appkey**をクリックすると、発行されたキー情報を確認できます。
![[図4] Public API URL & Appkey](http://static.toastoven.net/prod_resource_watcher/img49_JP.png)
<center>[図4] URL & Appkey</center>

## RESTful APIガイド

<a id="common-response-body"></a>
### Common Response Body

すべてのAPIリクエストに対してHTTPレスポンスコードは200でレスポンスします。<br/>
詳しいレスポンス結果はResponse Bodyのheader項目を参照してください。

| Key     | Type                              | Description |
|---------|-----------------------------------|-------------|
| header  | [Header](#common-response-header) | レスポンスヘッダ     |

- Header <a id="common-response-header"></a>

| Key           | 	Type    | 	Description                             |
|---------------|----------|------------------------------------------|
| isSuccessful  | 	boolean | 	成否(true, false)                      |
| resultCode    | 	int     | 	レスポンスコード。成功時は0、失敗時はエラーコードを返す           |
| resultMessage | 	String  | 	レスポンスメッセージ。成功時は"SUCCESS"、失敗時はエラーメッセージを返す |

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
  }
}
```

### 1. 通知

#### 1.1通知を登録する

**[基本情報]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| POST   | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms |

| 権限 | 	
|---------------------------------|
| ResourceWatcher:Alarms.Create  |

リソースで発生するイベントに対する通知を設定することができます。 <br/>


- 設定するイベント情報は**APIガイド > 3.1. Eventリスト照会API**のレスポンス結果を参照してください。<br/>
- 通知対象にはメンバー、通知受信グループ、ロール、 Webフックタイプがあり、タイプごとに対象を設定できます。
 リソース対象には **リソースグループ/タグ**を設定できます。
- `イベント全体`に設定すると、特定のリソースで発生するすべてのイベントに対して通知を受信するように設定できます。
- `リソース全体`に設定すると、リソースに関係なく、特定のイベントの通知を受信するように設定できます。
- `イベント全体`と`リソース全体`は同時に設定できません。

**[Request Header]**

| Key                        | 	Value                           |
|----------------------------|----------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key    | 	Value                |
|--------|-----------------------|
| appKey  | コンソールで発行されたAppkey |


<a id="post-alarm-request"></a>
**[Request Body]**

| Key                         | 	Type                                             | 	Required | 	Description                                         |
|-----------------------------|---------------------------------------------------|-----------|------------------------------------------------------|
| alarm                       | [Alarm](#post-alarm-request-alarm)                | Yes       | 通知情報                                              |
| alarmTargets                | [AlarmTarget[]](#post-alarm-request-alarm-target) | Yes       | 通知受信対象情報                                        |
| events                      | [Event[]](#post-alarm-request-event)              | No        | 通知対象イベントリスト<br/> 発生するすべてのイベントに対して受信を希望する場合は設定しない。 |
| target                      | [Target](#post-alarm-request-target)              | No        | 対象リソース情報<br/> リソースに関係なくイベントを受信したい場合は設定しない。       |

- Alarm <a id="post-alarm-request-alarm"></a>

| Key               | 	Type     | 	Required | 	Description                     |
|-------------------|-----------|-----------|----------------------------------|
| alarmName   | 	String   | 	Yes      | 	通知名 <br/> 最大255文字まで登録可能 |
| description | 	String   | 	No       | 	通知説明 <br/> 最大1,000文字まで登録可能 |

- AlarmTarget <a id="post-alarm-request-alarm-target"></a>

| Key                 | 	Type          | 	Required | 	Description                                                                                                                                                                                         |
|---------------------|----------------|-----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode     | String     | Yes       | 通知対象タイプコード<br/><br/><種類><br/>1. UUID:単一組織メンバー対象(NHN Cloud会員、 IAMメンバー)<br/>2. ROLE:組織ロール、プロジェクトロールグループ、プロジェクトロール<br/>3. ALARM\_KEY:組織通知受信グループ、プロジェクト通知受信グループ<br/>4. WEBHOOK: Webフック |
| alarmTarget         | 	String        | 	No       | 	通知対象情報<br/><タイプ別設定値><br/> 1. **UUID**:メンバーUUID <br/>2. **ROLE**:役割ID(例: ADMIN)<br/>3. **ALARM\_KEY**:通知受信グループID<br/>4. **WEBHOOK**:入力しない                                                 |
| emailAlarm          | 	String        | 	No       | 	メール受信有無<br/>1. **Y**:メール受信<br/>2. **N**:メールを受信しない<br/>WEBHOOK, ALARM\_KEYは入力しない                                                                                                         |
| smsAlarm            | 	String        | 	No       | 	SMS受信有無<br/>1. **Y**: SMS受信<br/>2. **N**: SMSを受信しない<br/>WEBHOOK, ALARM\_KEYは入力しない                                                                                                         |
| webhookUrl          | 	String        | 	No       | WebフックURLアドレス<br/>http:// またはhttps://で始まる必要がある<br/> 通知対象タイプ **WEBHOOK** 設定時に入力                                                                                                            |
| webhookSecret       | 	String        | 	No       | 	Webフック秘密鍵<br/> 通知対象タイプ **WEBHOOK** 設定時に入力                                                                                                                                              |

- Event <a id="post-alarm-request-event"></a>

| Key        | 	Type    | 	Required | 	Description |
|------------|----------|-----------|--------------|
| productId  | 	String  | 	Yes      | 	サービスID      |
| eventId    | 	String  | 	Yes      | 	イベントID      |

productId, eventId値は **APIガイド > 3.1 Eventリスト照会APIレスポンス値**を参考してください。

- Target <a id="post-alarm-request-target"></a>

| Key              | 	Type         | 	Required | 	Description      |
|------------------|---------------|-----------|-------------------|
| resourceGroupIds | 	String[]     | 	No       | 	リソースグループIDリスト   |
| resourceTagIds   | 	Long[]       | 	No       | 	リソースタグIDリスト   |

- 例:
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

* 参考: [Common Response Body](#common-response-body)と同じです。

#### 1.2通知照会

**[基本情報]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| GET    | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                           | 	
|--------------------------------|
| ResourceWatcher:Alarms.Get |

登録された通知について照会します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | 	Value            |
|---------|-------------------|
| appKey  | 	コンソールで発行されたAppkey |
| alarmId | 	照会する通知のID       |

**[Response Body]**


| Key                       | 	Type                                                                           | 	Description                 |
|---------------------------|---------------------------------------------------------------------------------|------------------------------|
| alarm                     | 	[Alarm](#post-alarm-response-alarm)                                            | 	通知情報      |
| alarmTargetAlarmKeys      | 	[AlarmTargetAlarmKey[]](#post-alarm-response-alarm-target-alarm-key)           | 	通知受信対象ALARM_KEY情報リスト    |
| alarmTargetMemberProfiles | 	[AlarmTargetMemberProfile[]](#post-alarm-response-alarm-target-member-profile) | 	通知受信対象UUID情報リスト |
| alarmTargetRoles          | 	[AlarmTargetRole[]](#post-alarm-response-alarm-target-role)                    | 	通知受信対象ROLEリスト |
| alarmTargets              | 	[AlarmTarget[]](#post-alarm-response-alarm-target)                             | 	通知受信対象リスト     |
| events                    | 	[Event[]](#post-alarm-response-event)                                          | 	通知イベントリスト                 |
| target                    | 	[Target](#post-alarm-response-target)                                          | 	対象リソース情報       |

- Alarm <a id="post-alarm-response-alarm"></a>

| Key                  | 	Type                                        | 	Description                                                                      |
|----------------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId              | String                                       | 通知ID                                                                             |
| alarmName            | String                                       | 通知名                                                                           |
| alarmRule            | [AlarmRule](#post-alarm-response-alarm-rule) | 通知ルールの詳細情報                                                                     |
| alarmStatusCode      | String                                       | 通知ステータスコード<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| appKey               | String                                       | Appkey情報                                                                       |
| cabAlarmKey          | String                                       | 通知受信グループID                                                                       |
| delDatetime          | Date                                         | 削除日時                                                                           |
| description          | String                                       | 通知の説明                                                                           |
| modDatetime          | Date                                         | 修正日時                                                                           |
| operatorUuid         | String                                       | 最終修正したユーザーUUID                                                                   |
| regDatetime          | Date                                         | 登録日時                                                                           |

- AlarmRule <a id="post-alarm-response-alarm-rule"></a>

| Key                     | 	Type    | 	Description                                                                           |
|-------------------------|----------|----------------------------------------------------------------------------------------|
| alarmRuleId             | String   | 通知ルールID                                                                               |
| alarmRuleStatusCode     | String   | 通知ルールのステータスコード<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| alarmRuleName           | String   | 通知ルールの名前                                                                             |
| alarmRuleDescription    | String   | 通知ルールの説明                                                                             |
| resourceTypes           | String[] | 通知ルールが適用される対象リソースタイプコードリスト<br/>空白の場合、全リソースタイプを対象とする<br/>Stringタイプリスト            |

- AlarmTargetAlarmKey <a id="post-alarm-response-alarm-target-alarm-key"></a>

| Key            | 	Type      | 	Description |
|----------------|------------|--------------|
| alarmKey       | String     | 通知キー       |
| alarmGroupName | String     | 通知受信グループ名  |
| alarmGroupDesc | String     | 通知受信グループの説明 |


- AlarmTargetMemberProfile <a id="post-alarm-response-alarm-target-member-profile"></a>

| Key             | 	Type      | 	Description                                       |
|-----------------|------------|----------------------------------------------------|
| uuid            | String     | メンバーUUID                                            |
| memberType      | String     | メンバー区分<br/><br/><種類><br/>1. TOAST\_CLOUD<br/>2. IAM |
| name            | String     | メンバー名                                            |
| corporationName | String     | 業者名                                            |
| email           | String     | メンバーのメールアドレス                                           |
| userId          | String     | メンバーID                                              |

- AlarmTargetRole <a id="post-alarm-response-alarm-target-role"></a>

| Key           | 	Type      | 	Description |
|---------------|------------|--------------|
| type          | String     | ロールタイプ      |
| roleId        | String     | ロールID        |
| roleName      | String     | ロール名        |
| description   | String     | ロールの説明      |

- AlarmTarget <a id="post-alarm-response-alarm-target"></a>

| Key                     | 	Type        | 	Description                                                                                                                                                         |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode     | String       | 通知対象タイプコード<br/><br/><種類><br/>1. UUID:単一組織メンバー対象(NHN Cloud会員、 IAMメンバー)<br/>2. ROLE:組織ロール、プロジェクトロールグループ、プロジェクトロール<br/>3. ALARM\_KEY:組織通知受信グループ、プロジェクト通知受信グループ<br/>4. WEBHOOK: Webフック |
| alarmTarget             | String       | 通知対象情報                                                                                                                                                           |
| emailAlarm              | String       | メール受信の有無(Y, N)                                                                                                                                                      |
| smsAlarm                | string       | SMS受信の有無(Y, N)                                                                                                                                                      |
| webhookUrl              | string       | WebフックURLアドレス |
| webhookSecret           | string       | Webフック秘密鍵                                                                                                                                                       |

- Event <a id="post-alarm-response-event"></a>

| Key          | 	Type        | 	Description   |
|--------------|--------------|----------------|
| productId    | 	String      | 	商品ID         |
| eventId      | 	String      | 	イベントID        |


- Target <a id="post-alarm-response-target"></a>

| Key            | 	Type                                                  | 	Description     |
|----------------|--------------------------------------------------------|------------------|
| resourceGroups | [ResourceGroup[]](#post-alarm-response-resource-group) | リソースグループリスト      |
| resourceTags   | [ResourceTag[]](#post-alarm-response-resource-tag)     | リソースタグリスト      |


- ResourceGroup <a id="post-alarm-response-resource-group"></a>

| Key               | 	Type   | 	Description   |
|-------------------|---------|----------------|
| resourceGroupId   | String  | 	リソースグループIDリスト |
| resourceGroupName | String  | リソースタグIDリスト |

- ResourceTag <a id="post-alarm-response-resource-tag"></a>

| Key        | 	Type                                                       | 	Description               |
|------------|-------------------------------------------------------------|----------------------------|
| tagId      | Long                                                        | リソースタグID                  |
| tagGroupId | Long                                                        | リソースタググループID               |
| tagName    | String                                                      | リソースタグ名                |  
| resourceTagTypeCode    | String                                                      | リソースタグタイプコード(DEFAULT, NORMAL) |
| regDatetime    | Date                                                        | リソースタグ登録日時             |
| modDatetime    | Date                                                        | リソースタグ修正日時             |
| resourceTagGroup    | [ResourceTagGroup](#post-alarm-response-resource-tag-group) | リソースタググループ                |

- ResourceTagGroup <a id="post-alarm-response-resource-tag-group"></a>

| Key        | 	Type  | 	Description        |
|------------|--------|---------------------|
| tagGroupId      | Long   | リソースタググループID        |
| tagGroupKey | String | リソースタグキー           |
| appKey    | String | アプリキー                |  
| creationTypeCode    | String | 作成タイプ(USER, SYSTEM) |
| regDatetime    | Date   | リソースタググループの登録日時   |
| modDatetime    | Date | リソースタググループの修正日時 |

- 例:

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


#### 1.3通知リスト照会

**[基本情報]**

| Method | 	URI                                                         |
|--------|--------------------------------------------------------------|
| POST   | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/search |

| 権限                           | 	
|--------------------------------|
| ResourceWatcher:Alarms.List |

登録したイベント通知リストを照会します。
- リクエストに検索条件を指定して、目的の通知リストを照会できます。
- ページングをサポートします。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key    | 	Value             |
|--------|--------------------|
| appKey | 	コンソールで発行されたAppkey |


**[Query Parameter]**

| Key  | 	Value                                      | Required |
|------|---------------------------------------------|----------|
| page | 	照会するページ番号<br/>Default値: 0                | No       |
| size | 	照会する通知数<br/>Default値: 10                | No       |
| sort | ソート対象および方式<br/>Default値: modDatetime, DESC | No       |

**[Request Body]**

| Key                | 	Type    | Required | 	Description                                                                                                                     |
|--------------------|----------|----------|----------------------------------------------------------------------------------------------------------------------------------|
| alarmIds           | String[] | No       | 通知IDリスト<br/>Stringタイプリスト                                                                                                     |
| alarmNameAnyLike   | String   | No       | 通知名(入力値を含むすべての通知の検索)                                                                                                       |
| alarmRuleIds       | String[] | No       | 通知ルールIDリスト<br/>Stringタイプリスト                                                                                                  |
| alarmStatusCodes   | String[] | No       | 通知状の態<br/>Stringタイプリスト<br/>Default値: STABLE, DISABLED<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| descriptionAnyLike | String   | No       | 通知の説明(入力値を含むすべての通知の検索)                                                                                                       |
| modDateFrom        | Date     | No       | 最終更新日時の開始                                                                                                                    |
| modDateTo          | Date     | No       | 最終更新日時の終了                                                                                                                   |
| operatorUuids      | String[] | No       | 最終修正者UUIDリスト<br/>Stringタイプリスト                                                                                              |
| resourceGroupIds   | String[] | No       | リソースグループリスト<br/>Stringタイプリスト                                                                                                    |

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
| alarms     | [Alarm[]](#list-alarm-response-alarm) | 通知リスト     |
| totalItems | Long                                  | 全体数     |


- Alarm <a id="list-alarm-response-alarm"></a>

| Key             | 	Type                                        | 	Description                                                                      |
|-----------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId         | String                                       | 通知ID                                                                             |
| alarmName       | String                                       | 通知名                                                                           |
| alarmRule       | [AlarmRule](#list-alarm-response-alarm-rule) | 通知ルールの詳細情報                                                                     |
| alarmStatusCode | String                                       | 通知ステータスコード<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| appKey          | String                                       | Appkey情報                                                                       |
| cabAlarmKey     | String                                       | 通知受信グループID                                                                       |
| description     | String                                       | 通知の説明                                                                           |
| operatorUuid    | String                                       | 最終修正したユーザーUUID                                                                   |
| delDatetime     | Date                                         | 削除日時                                                                           |
| modDatetime     | Date                                         | 修正日時                                                                           |
| regDatetime     | Date                                         | 登録日時                                                                           |

- AlarmRule <a id ="list_alarm_response_alarm_rule"></a>

| Key                  | 	Type    | 	Description                                                                         |
|----------------------|----------|--------------------------------------------------------------------------------------|
| alarmRuleId          | String   | 通知ルールID                                                                             |
| alarmRuleStatusCode  | String   | 通知ルールステータスコード<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| alarmRuleName        | String   | 通知ルール名                                                                           |
| alarmRuleDescription | String   | 通知ルールの説明                                                                           |
| resourceTypes        | String[] | 通知ルールが適用される対象リソースタイプのコードリスト<br/>空白の場合、全リソースタイプを対象とする<br/>Stringタイプリスト          |

- 例:

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

#### 1.4.通知の修正

**[基本情報]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| PUT    | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                           | 	
|--------------------------------|
| ResourceWatcher:Alarms.Update |

登録された通知を修正します。
- リクエストした内容を全て変更するため、**変更がない内容も既存の設定値を送信する必要があります**。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value            |
|---------|------------------|
| appKey  | コンソールで発行されたAppkey |
| alarmId | 修正する通知のID       |


**[Request Body]**

* 参考: [Common Response Body](#common-response-body)と同じです。

#### 1.5. 通知の削除

**[基本情報]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| DELETE | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                           | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

登録された通知を削除します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value              |
|---------|--------------------|
| appKey  | コンソールで発行されたAppkey   |
| alarmId | 削除する通知のID         |


**[Response Body]**

* 参考: [Common Response Body](#common-response-body)と同じです。


#### 1.6. 複数の通知を削除


**[基本情報]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| DELETE | 	/resource-watcher/v2.0/appkeys/{appKey}/event-alarms |

| 権限                           | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

登録された通知を複数削除します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value            |
|---------|------------------|
| appKey  | コンソールで発行されたAppkey |


**[Query Parameter]**

| Key      | Value                                     | Required |
|----------|-------------------------------------------|----------|
| alarmIds | 削除する通知IDのリスト<br/>必ず1つ以上の値を入力する必要があります。 | Yes      |

**[Response Body]**

* 参考: [Common Response Body](#common-response-body)と同じです。
