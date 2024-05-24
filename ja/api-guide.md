## Governance & Audit > Resource Watcher > APIガイド

> Resource WatcherにRESTful APIを呼び出してイベントやリソースの状態変更に伴う通知を設定することができます。

## User Access Key & Secret Access Key

REST APIを使うためには、まずUser Access KeyとSecret Access Keyを発行する必要があります。<br/>
User Access KeyとSecret Access Keyは**APIセキュリティ設定**で発行できます。<br/>
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
コンソール右側のURL & Appkeyをクリックして発行されたkey情報を確認できます。
![[図4] Public API URL & Appkey](http://static.toastoven.net/prod_resource_watcher/img49_JP.png)
<center>[図4] URL & Appkey</center>

## REST APIガイド

<a id="common-response-body"></a>
### Common Response Body

すべてのAPIリクエストに対してHTTPレスポンスコードは200でレスポンスします。<br/>
詳しいレスポンス結果はResponse Bodyのheader項目を参照してください。

| Key     | Type                              | Description |
|---------|-----------------------------------|-------------|
| header  | [Header](#common-response-header) | レスポンスヘッダ      |

- Header <a id="common-response-header"></a>

| Key           | 	Type    | 	Description                             |
|---------------|----------|------------------------------------------|
| isSuccessful  | 	boolean | 	成否(true, false)                      |
| resultCode    | 	int     | 	レスポンスコード。成功時は0、失敗時はエラーコードを返す            |
| resultMessage | 	String  | 	レスポンスメッセージ。成功時は"SUCCESS"、失敗時はエラーメッセージを返す  |

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
  }
}
```

### 1. Alarm

#### 1.1 Alarmを登録する

**[基本情報]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| POST   | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms |

| 権限                             | 	
|---------------------------------|
| ResourceWatcher:Alarms.Create  |

リソースで発生するイベントに対する通知を設定することができます。 <br/>


- 設定するイベント情報は[3.1. Eventリスト照会API](#list-event-response-event)のレスポンス結果を参照してください。<br/>
- アラーム対象にはメンバー、通知受信グループ、役割、 WebHookタイプがあり、各タイプごとに対象を設定できます。
- リソース対象には`リソースグループ/タグ`を設定できます。
- `イベント全体`に設定すると,特定のリソースで発生するすべてのイベントに対して通知を受信するように設定できます。
- `リソース全体`に設定すると,リソースに関係なく,特定のイベントの通知を受信するように設定できます。
- `イベント全体`と`リソース全体`は同時に設定できません。

**[Request Header]**

| Key                        | 	Value                           |
|----------------------------|----------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key      |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key    |

**[Path Variable]**

| Key    | 	Value                  |
|--------|-------------------------|
| appKey | 	コンソールで発行された商品Appkey   |


<a id="post-alarm-request"></a>
**[Request Body]**

| Key                         | 	Type                                             | 	Required | 	Description                                         |
|-----------------------------|---------------------------------------------------|-----------|------------------------------------------------------|
| alarm                       | [Alarm](#post-alarm-request-alarm)                | Yes       | 通知情報                                               |
| alarmTargets                | [AlarmTarget[]](#post-alarm-request-alarm-target) | Yes       | 通知受信対象情報                                         |
| events                      | [Event[]](#post-alarm-request-event)              | No        | 通知対象イベントリスト<br/> 発生するすべてのイベントに対して受信を希望する場合は設定しない。 |
| target                      | [Target](#post-alarm-request-target)              | No        | 対象リソース情報<br/> リソースに関係なくイベントを受信する場合は設定しない。         |

- Alarm <a id="post-alarm-request-alarm"></a>

| Key               | 	Type     | 	Required | 	Description                     |
|-------------------|-----------|-----------|----------------------------------|
| alarmName   | 	String   | 	Yes      | 	通知名 <br/> 最大255文字まで登録可能  |
| description | 	String   | 	No       | 	通知の説明 <br/> 最大1,000文字まで登録可能 |

- AlarmTarget <a id="post-alarm-request-alarm-target"></a>

| Key                 | 	Type     | 	Required | 	Description                                                                                                                                                                                 |
|---------------------|-----------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode | 	String   | 	Yes      | 通知対象タイプ <br/>1. `UUID`: 単一の組織メンバー対象(NHN cloud会員、 IAMメンバー)<br/>2. `ROLE`:役割対象(組織役割、プロジェクト役割グループ、プロジェクト役割)<br/>3. `ALARM_KEY`:通知受信グループ対象(組織通知受信グループ、プロジェクト通知受信グループ)<br/>4. `WEBHOOK`: Webフック |
| alarmTarget         | 	String   | 	No       | 	通知対象情報<br/><タイプ別設定値><br/> 1. UUID: `メンバーUUID` <br/>2. ROLE: `役割ID`(ex. ADMIN)<br/>3. ALARM\_KEY: `通知受信グループID`<br/>4. WEBHOOK: `入力しない`                                                 |
| emailAlarm          | 	String   | 	No       | 	メール受信の有無<br/>1. `Y`:メール受信<br/>2. `N`:メール受信しない<br/>WEBHOOK, ALARM\_KEYは入力しない。                                                                                                      |
| smsAlarm            | 	String   | 	No       | 	SMS受信の有無<br/>1. `Y`: SMS受信<br/>2. `N`: SMSを受信しない<br/>WEBHOOK, ALARM\_KEYは入力しない。                                                                                                      |
| webhookUrl          | 	String   | 	No       | 	Webhook URLアドレス<br/>http:// またはhttps://で開始する必要がある<br/> 通知対象タイプ`WEBHOOK`設定時に入力                                                                                                             |
| webhookSecret       | 	String   | 	No       | 	Webhook秘密鍵<br/> 通知対象タイプ`WEBHOOK`設定時に入力                                                                                                                                               |

- Event <a id="post-alarm-request-event"></a>

| Key        | 	Type    | 	Required | 	Description |
|------------|----------|-----------|--------------|
| productId  | 	String  | 	Yes      | 	サービスID      |
| eventId    | 	String  | 	Yes      | 	イベントID      |

productId, eventId値は[3.1イベントリスト照会APIレスポンス値](#list-event-response-event)を参照してください。

- Target <a id="post-alarm-request-target"></a>

| Key              | 	Type         | 	Required | 	Description      |
|------------------|---------------|-----------|-------------------|
| resourceGroupIds | 	String[]     | 	No       | 	リソースグループIDリスト    |
| resourceTagIds   | 	Long[]       | 	No       | 	リソースタグIDリスト    |

- 例)
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

<a id="post-alarm-response"></a>
**[Response Body]**

| Key                       | 	Type                                                                           | 	Description                 |
|---------------------------|---------------------------------------------------------------------------------|------------------------------|
| alarm                     | 	[Alarm](#post-alarm-response-alarm)                                            | 	通知情報       |
| alarmTargetAlarmKeys      | 	[AlarmTargetAlarmKey[]](#post-alarm-response-alarm-target-alarm-key)           | 	通知の受信対象リスト     |
| alarmTargetMemberProfiles | 	[AlarmTargetMemberProfile[]](#post-alarm-response-alarm-target-member-profile) | 	通知受信対象プロフィールリスト  |
| alarmTargetRoles          | 	[AlarmTargetRole[]](#post-alarm-response-alarm-target-role)                    | 	通知受信対象ROLEリスト |
| alarmTargets              | 	[AlarmTarget[]](#post-alarm-response-alarm-target)                             | 	通知受信対象リスト      |
| events                    | 	[Event[]](#post-alarm-response-event)                                          | 	通知イベントリスト                  |
| target                    | 	[Target](#post-alarm-response-target)                                          | 	対象リソース情報        |

- Alarm <a id="post-alarm-response-alarm"></a>

| Key                  | 	Type                                        | 	Description                                                                      |
|----------------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId              | String                                       | 通知ID                                                                             |
| alarmName            | String                                       | 通知名                                                                            |
| alarmRule            | [AlarmRule](#post-alarm-response-alarm-rule) | 通知ルール詳細情報                                                                      |
| alarmStatusCode      | String                                       | 通知ステータスコード<br/><br/><種類><br/>1. STABLE:有効化<br/>2. DISABLED:無効化<br/>3. CLOSED:削除 |
| appKey               | String                                       | Appkey情報                                                                        |
| cabAlarmKey          | String                                       | 通知受信グループID                                                                       |
| delDatetime          | Date                                         | 削除日時                                                                            |
| description          | String                                       | 通知の説明                                                                            |
| modDatetime          | Date                                         | 修正日時                                                                            |
| operatorUuid         | String                                       | 最終修正したユーザーUUID                                                                   |
| regDatetime          | Date                                         | 登録日時                                                                            |

- AlarmRule <a id="post-alarm-response-alarm-rule"></a>

| Key                     | 	Type    | 	Description                                                                           |
|-------------------------|----------|----------------------------------------------------------------------------------------|
| alarmRuleId             | String   | 通知ルールID                                                                               |
| alarmRuleStatusCode     | String   | 通知ルールステータスコード<br/><br/><種類><br/>1. STABLE:有効化<br/>2. DISABLED:無効化<br/>3. CLOSED:削除 |
| alarmRuleName           | String   | 通知ルールの名前                                                                              |
| alarmRuleDescription    | String   | 通知ルールの説明                                                                              |
| resourceTypes           | String[] | 通知ルールが適用する対象リソースタイプコードリスト<br/>空白の場合は、すべてのリソースタイプを対象とする<br/>Stringタイプリスト             |

- AlarmTargetAlarmKey <a id="post-alarm-response-alarm-target-alarm-key"></a>

| Key            | 	Type      | 	Description   |
|----------------|------------|----------------|
| alarmKey       | String     | アラームキー           |
| alarmGroupName | String     | 通知受信グループ名    |
| alarmGroupDesc | String     | 通知受信グループの説明   |


- AlarmTargetMemberProfile <a id="post-alarm-response-alarm-target-member-profile"></a>

| Key             | 	Type      | 	Description                                       |
|-----------------|------------|----------------------------------------------------|
| uuid            | String     | メンバーUUID                                            |
| memberType      | String     | メンバー区分<br/><br/><種類><br/>1. TOAST\_CLOUD<br/>2. IAM |
| name            | String     | メンバー名前                                             |
| corporationName | String     | 業者名前                                             |
| email           | String     | メンバーEmail                                           |
| userId          | String     | メンバーID                                              |

- AlarmTargetRole <a id="post-alarm-response-alarm-target-role"></a>

| Key           | 	Type      | 	Description |
|---------------|------------|--------------|
| type          | String     | 役割タイプ       |
| roleId        | String     | 役割ID        |
| roleName      | String     | 役割名         |
| description   | String     | 役割説明       |

- AlarmTarget <a id="post-alarm-response-alarm-target"></a>

| Key                     | 	Type        | 	Description                                                                                                                                                         |
|-------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| alarmTargetTypeCode     | String       | 通知対象タイプコード<br/><br/><種類><br/>1. UUID:組織メンバーNHN CLOUD, IAM<br/>2. ROLE:組織役割、プロジェクト役割グループ、プロジェクト役割<br/>3. ALARM\_KEY:組織通知受信グループ、プロジェクト通知受信グループ<br/>4. WEBHOOK: Webフック |
| alarmTarget             | String       | 通知対象情報                                                                                                                                                            |
| emailAlarm              | String       | メール受信の有無(Y, N)                                                                                                                                                      |
| smsAlarm                | string       | SMS受信の有無(Y, N)                                                                                                                                                      |
| webhookUrl              | string       | Webhook URLアドレス                                                                                                                                                      |
| webhookSecret           | string       | Webhook秘密鍵                                                                                                                                                        |

- Event <a id="post-alarm-response-event"></a>

| Key          | 	Type        | 	Description   |
|--------------|--------------|----------------|
| productId    | 	String      | 	商品ID         |
| eventId      | 	String      | 	イベントID        |


- Target <a id="post-alarm-response-target"></a>

| Key            | 	Type                                                  | 	Description     |
|----------------|--------------------------------------------------------|------------------|
| resourceGroups | [ResourceGroup[]](#post-alarm-response-resource-group) | リソースグループリスト       |
| resourceTags   | [ResourceTag[]](#post-alarm-response-resource-tag)     | リソースタグリスト       |


- ResourceGroup <a id="post-alarm-response-resource-group"></a>

| Key               | 	Type   | 	Description   |
|-------------------|---------|----------------|
| resourceGroupId   | String  | 	リソースグループIDリスト |
| resourceGroupName | String  | リソースタグIDリスト  |

- ResourceTag <a id="post-alarm-response-resource-tag"></a>

| Key               | 	Type       | 	Description   |
|-------------------|-------------|----------------|
| tagId             | Long        | リソースタグID      |
| tagName           | String      | リソースタグ名     |  

- 例)

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
        "tagName": ""
      }
    ]
  }
}
```

#### 1.2 Event Alarm照会

**[基本情報]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| GET    | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                            | 	
|--------------------------------|
| ResourceWatcher:Alarms.Get |

登録された通知について照会します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | 	Value             |
|---------|--------------------|
| appKey  | 	コンソールで発行されたAppkey |
| alarmId | 	照会する通知のID        |

**[Response Body]**

* ``参考`` : 1.1)イベント通知作成の[レスポンス](#post-alarm-response)と同じです。


#### 1.3 Event Alarmリスト照会

**[基本情報]**

| Method | 	URI                                                         |
|--------|--------------------------------------------------------------|
| POST   | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/search |

| 権限                            | 	
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
| alarmIds           | String[] | No       | 通知IDリスト<br/>Stringタイプリスト                                                                                                      |
| alarmNameAnyLike   | String   | No       | 通知名(入力値を含むすべての通知を検索)                                                                                                        |
| alarmRuleIds       | String[] | No       | 通知ルールIDリスト<br/>Stringタイプリスト                                                                                                   |
| alarmStatusCodes   | String[] | No       | 通知状の態<br/>Stringタイプリスト<br/>Default値: STABLE, DISABLED<br/><br/><種類><br/>1. STABLE:有効<br/>2. DISABLED:無効<br/>3. CLOSED:削除 |
| descriptionAnyLike | String   | No       | 通知の説明(入力値を含むすべての通知を検索)                                                                                                        |
| modDateFrom        | Date     | No       | 最終更新日時の開始                                                                                                                     |
| modDateTo          | Date     | No       | 最終更新日時の終了                                                                                                                    |
| operatorUuids      | String[] | No       | 最終修正者UUIDリスト<br/>Stringタイプリスト                                                                                               |
| resourceGroupIds   | String[] | No       | リソースグループリスト<br/>Stringタイプリスト                                                                                                     |

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
| alarms     | [Alarm[]](#list-alarm-response-alarm) | アラームリスト      |
| totalItems | Long                                  | 全体数      |


- Alarm <a id="list-alarm-response-alarm"></a>

| Key             | 	Type                                        | 	Description                                                                      |
|-----------------|----------------------------------------------|-----------------------------------------------------------------------------------|
| alarmId         | String                                       | 通知ID                                                                             |
| alarmName       | String                                       | 通知名                                                                            |
| alarmRule       | [AlarmRule](#list-alarm-response-alarm-rule) | 通知ルール詳細情報                                                                      |
| alarmStatusCode | String                                       | 通知ステータスコード<br/><br/><種類><br/>1. STABLE:有効化<br/>2. DISABLED:無効化<br/>3. CLOSED:削除 |
| appKey          | String                                       | Appkey情報                                                                        |
| cabAlarmKey     | String                                       | 通知受信グループID                                                                       |
| description     | String                                       | 通知の説明                                                                            |
| operatorUuid    | String                                       | 最終修正したユーザーUUID                                                                   |
| delDatetime     | Date                                         | 削除日時                                                                            |
| modDatetime     | Date                                         | 修正日時                                                                            |
| regDatetime     | Date                                         | 登録日時                                                                            |

- AlarmRule <a id ="list_alarm_response_alarm_rule"></a>

| Key                  | 	Type    | 	Description                                                                           |
|----------------------|----------|----------------------------------------------------------------------------------------|
| alarmRuleId          | String   | 通知ルールID                                                                               |
| alarmRuleStatusCode  | String   | 通知ルールステータスコード<br/><br/><種類><br/>1. STABLE:有効化<br/>2. DISABLED:無効化<br/>3. CLOSED:削除 |
| alarmRuleName        | String   | 通知ルールの名前                                                                              |
| alarmRuleDescription | String   | 通知ルールの説明                                                                              |
| resourceTypes        | String[] | 通知ルールが適用する対象リソースタイプコードリスト<br/>空白の場合、全リソースタイプを対象とする<br/>Stringタイプリスト             |

- 例)

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

#### 1.4. Event Alarm修正

**[基本情報]**

| Method | 	URI                                                            |
|--------|-----------------------------------------------------------------|
| PUT    | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                            | 	
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

| Key     | Value             |
|---------|-------------------|
| appKey  | コンソールで発行されたAppkey |
| alarmId | 修正する通知のID        |


**[Request Body]**

* ``参考`` : 1.1)イベント通知作成の[Request Body](#post-alarm-request)と同じです。

#### 1.5. Event Alarm削除

**[基本情報]**

| Method | 	URI                                                         |
|--------|--------------------------------------------------------------|
| DELETE | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| 権限                            | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

登録された通知を削除します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value               |
|---------|---------------------|
| appKey  | コンソールで発行されたAppkey   |
| alarmId | 削除する通知のID          |


**[Response Body]**

* ``参考`` : [Common Response Body](#common-response-body)と同じです。


#### 1.6. Event Alarmの複数件削除


**[基本情報]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| DELETE | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms |

| 権限                            | 	
|--------------------------------|
| ResourceWatcher:Alarms.Delete |

登録された通知を複数削除します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value             |
|---------|-------------------|
| appKey  | コンソールで発行されたAppkey |


**[Query Parameter]**

| Key      | Value                                     | Required |
|----------|-------------------------------------------|----------|
| alarmIds | 削除する通知IDのリスト<br/>必ず1つ以上の値を入力する必要があります。 | Yes      |

**[Response Body]**

* ``参考`` : [Common Response Body](#common-response-body)と同じです。

### 2. 通知ヒストリー

#### 2.1. 通知ヒストリー照会


**[基本情報]**

| Method | 	URI                                                                                     |
|--------|------------------------------------------------------------------------------------------|
| GET    | 	/resource-watcher/v1.0/appkeys/{appKey}/alarms/{alarmId}/alarm-history/{alarmHistoryId} |

| 権限                            | 	
|--------------------------------|
| ResourceWatcher:AlarmHistories.Get |

登録された通知が送信した履歴を単件照会します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value             |
|---------|-------------------|
| appKey  | コンソールで発行されたAppkey |
| alarmId        | 	ヒストリーを照会する通知のID |
| alarmHistoryId | 	照会する通知履歴のID    |


**[Response Body]**


| Key          | 	Type                                                      | 	Description    |
|--------------|------------------------------------------------------------|-----------------|
| alarm        | 	[Alarm](#get-alarm-history-response-alarm)                | 	アラーム            |
| alarmHistory | 	[AlarmHistory](#get-alarm-history-response-alarm-history) | 	アラーム履歴         |
| alarmRule    | 	[AlarmRule](#get-alarm-history-response-alarm-rule)       | 	Scope ID       |

- Alarm <a id="get-alarm-history-response-alarm"></a>

| Key         | Type   | 	Description |
|-------------|--------|--------------|
| alarmId     | String | 	通知ID       |
| alarmName   | String | 	通知名      |
| description | String | 通知の説明       |

- AlarmHistory <a id="get-alarm-history-response-alarm-history"></a>

| Key                     | Type                                             | 	Description                                                                       |
|-------------------------|--------------------------------------------------|------------------------------------------------------------------------------------|
| alarmHistoryId          | String                                           | 	アラーム履歴ID                                                                          |
| resource                | [Resource](#get-alarm-history-response-resource) | 	リソース情報                                                                           |
| event                   | [Event](#get-alarm-history-response-event)       | 	イベント情報                                                                           |
| alarmSendResultTypeCode | String                                           | 	通知送信結果<br/><br/><種類><br/>1. SUCCESS:成功<br/>2. FAILURE:失敗<br/>3. SENDING: 送信中 |

- AlarmRule <a id="get-alarm-history-response-alarm-rule"></a>

| Key                  | Type     | 	Description  |
|----------------------|----------|---------------|
| alarmRuleDescription | String   | 通知ルールの説明     |
| alarmRuleId          | String   | 通知ルールID      |
| alarmRuleName        | String   | 通知ルールの名前     |

- Resource <a id="get-alarm-history-response-resource"></a>

| Key                      | Type   | 	Description                                                      |
|--------------------------|--------|-------------------------------------------------------------------|
| appKey                   | String | 	アプリケーションキー                                                              |
| resourceKey              | String | 	リソース識別キー                                                         |
| orgId                    | String | 	組織ID                                                            |
| projectId                | String | 	プロジェクトID                                                          |
| projectName              | String | 	プロジェクト名                                                         |
| productId                | String | 	商品ID                                                            |
| resourceAppKey           | String | 	リソースが属する商品のアプリケーションキー                                                  |
| iaasTenantId             | String | 	IaaSテナントID                                                      |
| regionCode               | String | 	リージョンコード                                                           |
| resourceId               | String | 	リソースID                                                           |
| resourceName             | String | 	リソース名                                                          |
| resourceTypeCode         | String | 	リソースタイプ                                                          |
| resourceCreationTypeCode | String | 	リソース作成タイプ<br/><br/><種類><br/>1. SYSTEM:システム作成<br/>2. USER:会員作成 |
| resourceStatusCode       | String | 	リソースステータスコード<br/><br/><種類><br/>1. STABLE:正常<br/>2. CLOSED:削除     |
| description              | String | 	リソースの説明                                                          |
| cloudResourceName        | String | 	リソースのNHN Cloud Resource Name                                     |
| createDatetime           | Date   | 	イベント発生日時                                                       |
| regDatetime              | Date   | 	登録日時                                                           |
| modDatetime              | Date   | 	修正日時                                                           |
| delDatetime              | Date   | 	削除日時                                                           |

- Event <a id="get-alarm-history-response-event"></a>

| Key             | Type                                                    | 	Description      |
|-----------------|---------------------------------------------------------|-------------------|
| appKey          | String                                                  | 	イベントが発生したアプリケーションキー     |
| alarmId         | String                                                  | 	アラームID            |
| eventId         | String                                                  | 	イベントID           |
| eventSourceType | String                                                  | 	イベントを発生させた主体のタイプ |
| eventUser       | [EventUser](#get-alarm-history-response-event-user)     | 	イベントを発生させた会員    |
| eventTarget     | [EventTarget](#get-alarm-history-response-event-target) | 	発生したイベントの対象     |
| eventDatetime   | String                                                  | 	イベント発生時間       |
| regDatetime     | Date                                                    | 	イベント登録日時       |

- EventUser <a id="get-alarm-history-response-event-user"></a>

| Key       | Type     | 	Description        |
|-----------|----------|---------------------|
| userIdNo  | 	String  | 	イベントを発生させた会員のUUID |
| userId    | 	String  | 	イベントを発生させた会員の名前  |
| userIp    | 	String  | 	イベントを発生させた会員のIP   |
| userAgent | 	String  | 	イベントを発生させた会員のエージェント |

- EventTarget <a id="get-alarm-history-response-event-target"></a>

| Key             | Type                                                        | 	Description     |
|-----------------|-------------------------------------------------------------|------------------|
| targetMembers   | [TargetMember[]](#get-alarm-history-response-target-member) | 	発生したイベントの対象会員 |


- TargetMember <a id="get-alarm-history-response-target-member"></a>

| Key          | Type   | 	Description                     |
|--------------|--------|----------------------------------|
| idNo         | String | 	発生したイベントの対象会員のUUID            |
| name         | String | 	発生したイベントの対象会員の名前             |
| userCode     | String | 	発生したイベントの対象会員のID(IAM会員の場合) |
| emailAddress | String | 	発生したイベントの対象会員                 |

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
    "description": ""
  },
  "alarmHistory": {
    "alarmHistoryId": "",
    "alarmSendResultTypeCode": "",
    "event": {
      "alarmId": "",
      "appKey": "",
      "eventDatetime": "yyyy-MM-ddTHH:mm:SS",
      "eventId": "",
      "eventSourceType": "",
      "eventTarget": {
        "targetMembers": [
          {
            "emailAddress": "",
            "idNo": "",
            "name": "",
            "userCode": ""
          }
        ]
      },
      "eventUser": {
        "userAgent": "",
        "userId": "",
        "userIdNo": "",
        "userIp": ""
      },
      "regDatetime": "yyyy-MM-ddTHH:mm:SS"
    },
    "resource": {
      "appKey": "",
      "cloudResourceName": "",
      "createDatetime": "yyyy-MM-ddTHH:mm:SS",
      "delDatetime": "yyyy-MM-ddTHH:mm:SS",
      "description": "",
      "iaasTenantId": "",
      "modDatetime": "yyyy-MM-ddTHH:mm:SS",
      "orgId": "",
      "productId": "",
      "projectId": "",
      "projectName": "",
      "regDatetime": "yyyy-MM-ddTHH:mm:SS",
      "regionCode": "",
      "resourceAppKey": "",
      "resourceCreationTypeCode": "",
      "resourceId": "",
      "resourceKey": "",
      "resourceName": "",
      "resourceStatusCode": "",
      "resourceTypeCode": ""
    }
  },
  "alarmRule": {
    "alarmRuleDescription": "",
    "alarmRuleId": "",
    "alarmRuleName": ""
  },
  "projectName": ""
}
```


#### 2.2. 通知ヒストリーリスト照会


**[基本情報]**

| Method | 	URI                                                                    |
|--------|-------------------------------------------------------------------------|
| GET    | 	/resource-watcher/v1.0/appkeys/{appKey}/alarms/{alarmId}/alarm-history |

| 権限                            | 	
|--------------------------------|
| ResourceWatcher:AlarmHistories.List |

登録された通知が送信した履歴を全て照会します。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| X-TC-AUTHENTICATION-ID     | 	コンソールで発行されたUser Access Key   |
| X-TC-AUTHENTICATION-SECRET | 	コンソールで発行されたSecret Access Key |

**[Path Variable]**

| Key     | Value                    |
|---------|--------------------------|
| appKey  | コンソールで発行されたAppkey        |
| alarmId        | 	ヒストリーを照会する通知のID        |


**[Query Parameter]**

| Key                      | Value                                                 | Required |
|--------------------------|-------------------------------------------------------|----------|
| resourceKeys             | リソース識別キーリスト<br/>複数入力可能                           | No       |
| resourceNameAnyLike      | リソース識別キーリスト                                          | No       |
| eventIds                 | イベントIDリスト<br/>複数入力可能                             | No       |
| eventSourceTypes         | イベントソースリスト<br/>複数入力可能                             | No       |
| projectIds               | プロジェクトIDリスト<br/>複数入力可能                            | No       |
| productIds               | 商品IDリスト<br/>複数入力可能                              | No       |
| alarmSendResultTypeCodes | 通知送信結果リスト(SENDING, SUCCESS, FAILURE)<br/>複数入力可能 | No       |
| regDateFrom              | 照会する期間の開始日時<br/>yyyy-MM-ddTHH:mm:SS                 | No       |
| regDateTo                | 照会する期間の終了日時<br/>yyyy-MM-ddTHH:mm:SS                 | No       |
| page                     | 照会するページ番号<br/>Default値: 0                           | No       |
| size                     | 照会する通知数<br/>Default値: 10                           | No       |
| sort                     | ソート対象および方式                                           | No       |

**[Response Body]**

| Key            | 	Type                                                         | 	Description |
|----------------|---------------------------------------------------------------|--------------|
| alarmHistories | 	[AlarmHistory[]](#list-alarm-history-response-alarm-history) | 	アラーム履歴リスト   |
| totalItems     | 	Long                                                         | 	全体数      |

- AlarmHistory <a id="list-alarm-history-response-alarm-history"></a>

| Key                     | Type                                              | 	Description                                                                       |
|-------------------------|---------------------------------------------------|------------------------------------------------------------------------------------|
| alarmHistoriesId        | String                                            | アラーム履歴ID                                                                           |
| resource                | [Resource](#list-alarm-history-response-resource) | リソース情報                                                                            |
| event                   | [Event](#list-alarm-history-response-event)       | イベント情報                                                                            |
| alarmSendResultTypeCode | String                                            | 	通知送信結果<br/><br/><種類><br/>1. SUCCESS:成功<br/>2. FAILURE:失敗<br/>3. SENDING: 送信中 |

- Resource <a id="list-alarm-history-response-resource"></a>

| Key                      | Type   | 	Description                                                      |
|--------------------------|--------|-------------------------------------------------------------------|
| appKey                   | String | 	アプリケーションキー                                                              |
| resourceKey              | String | 	リソース識別キー                                                         |
| orgId                    | String | 	組織ID                                                            |
| projectId                | String | 	プロジェクトID                                                          |
| projectName              | String | 	プロジェクト名                                                         |
| productId                | String | 	商品ID                                                            |
| resourceAppKey           | String | 	リソースが属する商品のアプリケーションキー                                                  |
| iaasTenantId             | String | 	IaaSテナントID                                                      |
| regionCode               | String | 	リージョンコード                                                           |
| resourceId               | String | 	リソースID                                                           |
| resourceName             | String | 	リソース名                                                          |
| resourceTypeCode         | String | 	リソースタイプ                                                          |
| resourceCreationTypeCode | String | 	リソース作成タイプ<br/><br/><種類><br/>1. SYSTEM:システム作成<br/>2. USER:会員作成 |
| resourceStatusCode       | String | 	リソースステータスコード<br/><br/><種類><br/>1. STABLE :正常<br/>2. CLOSED :削除    |
| description              | String | 	リソースの説明                                                          |
| cloudResourceName        | String | 	リソースのNHN Cloud Resource Name                                     |
| createDatetime           | Date   | 	イベント発生日時                                                       |
| regDatetime              | Date   | 	登録日時                                                           |
| modDatetime              | Date   | 	修正日時                                                           |
| delDatetime              | Date   | 	削除日時                                                           |

- Event <a id="list-alarm-history-response-event"></a>

| Key             | Type                                                     | 	Description        |
|-----------------|----------------------------------------------------------|---------------------|
| appKey          | String                                                   | 	イベントが発生したアプリケーションキー       |
| alarmId         | String                                                   | 	アラームID              |
| eventId         | String                                                   | 	イベントID             |
| eventSourceType | String                                                   | 	イベントを発生させた主体のタイプ  |
| eventUser       | [EventUser](#list-alarm-history-response-event-user)     | 	イベントを発生させた会員      |
| eventTarget     | [EventTarget](#list-alarm-history-response-event-target) | 	発生したイベントの対象       |
| eventDatetime   | String                                                   | 	イベント発生時間         |
| regDatetime     | Date                                                     | 	イベント登録日時         |

- EventUser <a id="list-alarm-history-response-event-user"></a>

| Key                 | Type   | Description        |
|---------------------|--------|--------------------|
| userIdNo  | String | イベントを発生させた会員のUUID |
| userId    | String | イベントを発生させた会員の名前  |
| userIp    | String | イベントを発生させた会員のIP   |
| userAgent | String | イベントを発生させた会員のエージェント |

- EventTarget <a id="list-alarm-history-response-event-target"></a>

| Key             | Type                                                         | 	Description     |
|-----------------|--------------------------------------------------------------|------------------|
| targetMembers   | [TargetMember[]](#list-alarm-history-response-target-member) | 	発生したイベントの対象会員 |


- TargetMember <a id="list-alarm-history-response-target-member"></a>

| Key          | Type   | 	Description                    |
|--------------|--------|---------------------------------|
| idNo         | String | 	発生したイベントの対象会員のUUID           |
| name         | String | 	発生したイベントの対象会員の名前            |
| userCode     | String | 	発生したイベントの対象会員のID(IAM会員の場合) |
| emailAddress | String | 	発生したイベントの対象会員                |

```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": ""
  },
  "alarmHistories": [
    {
      "alarmHistoryId": "",
      "alarmSendResultTypeCode": "",
      "event": {
        "alarmId": "",
        "appKey": "",
        "eventDatetime": "yyyy-MM-ddTHH:mm:SS",
        "eventId": "",
        "eventSourceType": "",
        "eventTarget": {
          "targetMembers": [
            {
              "emailAddress": "",
              "idNo": "",
              "name": "",
              "userCode": ""
            }
          ]
        },
        "eventUser": {
          "userAgent": "",
          "userId": "",
          "userIdNo": "",
          "userIp": ""
        },
        "regDatetime": "yyyy-MM-ddTHH:mm:SS"
      },
      "resource": {
        "appKey": "",
        "cloudResourceName": "",
        "createDatetime": "yyyy-MM-ddTHH:mm:SS",
        "delDatetime": "yyyy-MM-ddTHH:mm:SS",
        "description": "",
        "iaasTenantId": "",
        "modDatetime": "yyyy-MM-ddTHH:mm:SS",
        "orgId": "",
        "productId": "",
        "projectId": "",
        "projectName": "",
        "regDatetime": "yyyy-MM-ddTHH:mm:SS",
        "regionCode": "",
        "resourceAppKey": "",
        "resourceCreationTypeCode": "",
        "resourceId": "",
        "resourceKey": "",
        "resourceName": "",
        "resourceStatusCode": "",
        "resourceTypeCode": ""
      }
    }
  ],
  "totalItems": 0
}
```


### 3. Event

#### 3.1. Eventリスト照会

**[基本情報]**

| Method | 	URI                                            |
|--------|-------------------------------------------------|
| GET    | 	/resource-watcher/v1.0/appkeys/{appKey}/events |


通知に設定できるイベントリストを照会します。
- イベントはCloudTrailに登録されたイベントIDをベースにします。
- イベントに対する検索機能を提供します。
- イベント名または商品名で検索する場合、langを設定することで、設定した値に合った言語で検索できます。

**[Request Header]**

| Key                        | 	Value                        |
|----------------------------|-------------------------------|
| lang                       | 	言語コード<br/>検索条件およびレスポンス値は言語コードに該当する値(ko, en, ja, zh)<br/>Default値: ko |

**[Path Variable]**

| Key     | Value                    |
|---------|--------------------------|
| appKey  | コンソールで発行されたAppkey        | 

**[Query Parameter]**


| Key             | Value                                                                | Required |
|-----------------|----------------------------------------------------------------------|----------|
| productIdList   | 検索する商品リストIDリスト<br/>複数入力可能                                     | No       |
| productNameLike | 検索する商品名                                                           | No       |
| eventNameLike   | 検索するイベント名                                                          | No       |
| page            | 照会するページ番号<br/>Default値: 0                                          | No       |
| size            | 照会する通知数<br/>Default値: 10                                          | No       |
| sort            | ソート対象および方式<br/>Default値: productName:ASC,eventName:ASC | No       |


**[Response Body]**

| Key        | 	Type                                  | 	Description |
|------------|----------------------------------------|--------------|
| events     | 	[Event[]](#list-event-response-event) | 	イベントリスト     |
| totalItems | 	Long                                  | 	全体数      |


- Event <a id="list-event-response-event"></a>

| Key         | 	Type   | 	Description |
|-------------|---------|--------------|
| eventId     | 	String | 	イベントID      |
| productId   | 	String | 	商品ID       |
| productName | 	String | 	商品名         |


```json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": ""
  },
  "eventPage": "",
  "events": [
    {
      "eventId": "",
      "productId": "",
      "productName": ""
    }
  ],
  "totalItems": 0
}
```
