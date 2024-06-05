## Governance & Audit > Resource Watcher > API Guide

> You can call the RESTful API to Resource Watcher to set alarm for changes in the status of events and resources.

## User Access Key & Secret Access Key

To use REST API, you have to first obtain User Access Key and Secret Access Key.<br/>
User Access Key and Secret Access Key can be issued from **API Security Settings**.<br/>
Put the mouse pointer to an account in the top right of console, then select **API Security Settings** from the drop-down menu that appears and click on **Create User Access Key ID**.<br/>
For security purposes, it is recommended to create both User Access Key and Secret Access Key.

![[Figure 1] API Security Settings Location](http://static.toastoven.net/prod_resource_watcher/img46_EN.png)
<center>[Figure 1] API Security Settings Location</center>

![[Figure 2] API Security Settings Page](http://static.toastoven.net/prod_resource_watcher/img47_EN.png)
<center>[Figure 2] API Security Settings Page</center>

![[Figure 3] Create UserAccessKey and SecretAccessKey](http://static.toastoven.net/prod_resource_watcher/img48_EN.png)
<center>[Figure 3] Create User Access Key and Secret Access Key</center>

## Check Public API URL & Appkey
Appkey is required to use RESTful API.<br/>
You can check the issued key information by clicking URL & Appkey on the right side of console. 
![[ Figure 4] Public API URL & Appkey](http://static.toastoven.net/prod_resource_watcher/img49_EN.png)
<center>[ Figure 4] URL & Appkey</center>

## REST API Guide

<a id="common-response-body"></a>
### Common Response Body

For all API requests, HTTP response code responds with 200.<br/>
For detailed response results, refer to header item in Response Body. 

| Key | Type | Description | 
|---------|-----------------------------------|-------------| 
| header | [Header](#common-response-header) | Response header |

- Header <a id="common-response-header"></a>

| Key | Type | Description | 
|---------------|----------|------------------------------------------| 
| isSuccessful | boolean | success (true, false) | 
| resultCode | int | response code. If succeeds 0, if fails return the error code | 
| resultMessage | String | Response message. If succeeds "SUCCESS", if fails return the error message |

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

#### 1.1 Register Alarm

**[Basic Information]**

| Method | 	URI                                                  |
|--------|-------------------------------------------------------|
| POST   | 	/resource-watcher/v1.0/appkeys/{appKey}/event-alarms |

| Permission                              | 	
|---------------------------------|
| ResourceWatcher:Alarms.Create  |

You can set alarms for events that occur in a resource. <br/>


- Event information to set is [3.1. Refer to the response results from the Event List lookup API](#list-event-response-event).<br/>
- Alarm targets include members, alarm receiving groups, roles, and WebHook types, and you can set targets for each type
- Resource targets can be set to `resource group/tag`.
- If set to `All events`, you can receive alarm for all events occurring in a particular resource.
- If set to `All resources`, you can receive alarm for specific events regardless of resources.
- `All events` and `All resources` cannot be set together.

**[Request Header]**

| Key | Value | 
|----------------------------|----------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued from console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued from console |

**[Path Variable]**

| Key | Value | 
|--------|-------------------------| 
| appKey | Appkey issued from console |


<a id="post-alarm-request"></a> 
**[Request Body]**

| Key | Type | Required | Description | 
|-----------------------------|---------------------------------------------------|-----------|------------------------------------------------------| 
| alarm | [Alarm](#post-alarm-request-alarm) | Yes | Alarm Information| 
| alarmTargets | [AlarmTarget[]](#post-alarm-request-alarm-target) | Yes | Alarm receiving target information | 
| events | [Event[]](#post-alarm-request-event) | No | Alarm target event list<br/> Do not set if want to receive all events that occur. | 
| target | [target](#post-alarm-request-target) | No | Target Resource Information<br/> Do not set if receiving an event regardless of resources. |

- Alarm <a id="post-alarm-request-alarm"></a>

| Key | Type | Required | Description | 
|-------------------|-----------|-----------|----------------------------------| 
| alarmName | String | Yes | alarm name <br/> Can be registered maximum 255 characters | 
| Description | String | No | Alarm Description <br/> Can be registered maximum 1,000 characters

- AlarmTarget <a id="post-alarm-request-alarm-target"></a>

| Key | Type | Required | Description | 
|---------------------|-----------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| alarmTargetTypeCode | String | Yes | Alarm target type <br/>1. `UUID`: Single Organization Member Target (NHN cloud member, IAM member)<br/>2. `ROLE`: Role targets (organizational roles, project role groups, project roles)<br/>3.  `ALARM_KEY`: Alarm receiving group target (organization alarm receiving group, project alarm receiving group)<br/>4. `WEBHOOK`: Webhook | 
| alarmTarget | String | No | Alarm target information<br/><Setting values by type><br/> 1. UUID: `Member UUID` <br/>2. ROLE: `Role ID`(ex. ADMIN)<br/>3.  ALARM\_KEY: `alarm receiving group ID`<br/>4. WEBHOOK: `Do not enter`
| e-mail Alarm | String | No | Whether received email or not<br/>1. `Y`: Mail Reception<br/>2. `N`: Mail not received <br/>Do not enter WEBHOOK, ALARM\_KEY | 
| smsAlarm | String | No | Whether received SMS or not<br/>1. `Y`: SMS Reception<br/>2. `N`: SMS not received<br/>Do not enter WEBHOOK, ALARM\_KEY | 
| webhookUrl | String | No | Webhook URL address<br/>Have to start with http:// or https://<br/> Alarm target type`WEBHOOK` Enter when setting |
| webhookSecret | String | No | WebhookSecret Key<br/> Alarm target type `WEBHOOK` Enter when setting |

- Event <a id="post-alarm-request-event"></a>

| Key | Type | Required | Description | 
|------------|----------|-----------|--------------| 
| productId | String | Yes | service ID | 
| eventId | String | Yes | event ID |

For values of productId, eventid, refer to [3.1 Event List lookup API Response Value](#list-event-response-event) 

- Target <a id="post-alarm-request-target"></a>

| Key | Type | Required | Description | 
|------------------|---------------|-----------|-------------------| 
| resourceGroupIds | String[] | No | Resource Group ID list | 
| resourceTagIds | Long[] | No | Resource tag ID list |

- Example)
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

| Key | Type | Description | 
|---------------------------|---------------------------------------------------------------------------------|------------------------------| 
| alarm | [Alarm](#post-alarm-response-alarm) | Alarm Information| 
| alarmTargetAlarmKeys | [AlarmTargetAlarmKey[]](#post-alarm-response-alarm-target-alarm-key) | Alarm receiving target list | 
| alarmTargetMemberProfiles | [AlarmTargetMemberProfile[]](#post-alarm-response-alarm-target-member-profile) | Alarm receiving target profile list | 
| alarmTargetRoles | [AlarmTargetRole[]](#post-alarm-response-alarm-target-role) | Alarm receiving target ROLE list | 
| alarmTargets | [AlarmTarget[]](#post-alarm-response-alarm-target) | Alarm receiving target list | 
| events | [Event[]](#post-alarm-response-event) | Alarm event list| 
| target | [Target](#post-alarm-response-target) | target resource information |

- Alarm <a id="post-alarm-response-alarm"></a>

| Key | Type | Description | 
|----------------------|----------------------------------------------|-----------------------------------------------------------------------------------| 
| alarmId | String | Alarm ID | 
| alarmName | String | Alarm name | 
| alarmRule | [AlarmRule](#post-alarm-response-alarm-rule) | Alarm rule detailed information | 
| alarmStatusCode | String | Alarm status code<br/><br/>Type<br/>1. STABLE: Enable<br/>2. DISABLED: Deactivate<br/>3.  CLOSED: delete | 
| appKey | String | Appkey information | 
| cabAlarmKey | String | Alarm receiving group ID | 
| delDatetime | Date | date and time of deletion | 
| description | String | Alarm description | 
| modDatetime | Date | date and time of modification | 
| operatorUuid | String | last modified user UUID | 
| regDatetime | Date | date and time of registration |

- AlarmRule <a id="post-alarm-response-alarm-rule"></a>

| Key | Type | Description | 
|-------------------------|----------|----------------------------------------------------------------------------------------| 
| alarmRuleId | String | alarm rule ID | 
| alarmRuleStatusCode | String | alarm rule status code<br/><br/>Type<br/>1. STABLE: Enable<br/>2. DISABLED: Deactivate<br/>3.  CLOSED: delete |
| alarmRuleName | String | alarm rule name | 
| alarmRuleDescription | String | alarm rule Description | 
| resourceTypes | String[] | List of target resource type codes that alarm rules apply<br/>If empty value, it targets all resource types<br/>String type list |

- AlarmTargetAlarmKey <a id="post-alarm-response-alarm-target-alarm-key"></a>

| Key | Type | Description | 
|----------------|------------|----------------| 
| alarmKey | String | alarm Key | 
| alarmGroupName | String | alarm receiving group name | 
| alarmGroupDesc | String | alarm receiving group description |


- AlarmTargetMemberProfile <a id="post-alarm-response-alarm-target-member-profile"></a>

| Key | Type | Description | 
|-----------------|------------|----------------------------------------------------| 
| uuid | String | member UUID | 
| memberType | String | member type<br/><br/>Type<br/>1. TOAST\_CLOUD<br/>2. IAM | 
| name | String | member name | 
| corporationName | String | corporation name | 
| email | String | member Email | 
| userId | String | member ID |

- AlarmTargetRole <a id="post-alarm-response-alarm-target-role"></a>

| Key | Type | Description | 
|---------------|------------|--------------| 
| type | String | role type | 
| roleId | String | role ID | 
| roleName | String | role name | 
| description | String | role description |

- AlarmTarget <a id="post-alarm-response-alarm-target"></a>

| Key | Type | Description | 
|-------------------------|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------| 
| alarmTargetTypeCode | String | alarm Target Type Code<br/><br/>Type<br/>1. UUID: Organization member NHN CLOUD, IAM<br/>2. ROLE: Organizational roles, project role groups, project roles<br/>3.  ALARM\_KEY: organization alarm receiving group, project alarm receiving group<br/>4. WEBHOOK: Webhook | 
| alarmTarget | String | alarm Target information| 
| emailAlarm | String | whether received email or not(Y, N) | 
| smsAlarm | string | whether received SMS or not (Y, N) | 
| webhookUrl | string | Webhook URL address | 
| webhookSecret | string | Webhook secret key |

- Event <a id="post-alarm-response-event"></a>

| Key | Type | Description | 
|--------------|--------------|----------------| 
| productId | String | product ID | 
| eventId | String | event ID |


- Target <a id="post-alarm-response-target"></a>

| Key | Type | Description | 
|----------------|--------------------------------------------------------|------------------| 
| resourceGroups | [ResourceGroup[]](#post-alarm-response-resource-group) | Resource group lsit | 
| resourceTags | [ResourceTag[]](#post-alarm-response-resource-tag) | Resource tag list |


- ResourceGroup <a id="post-alarm-response-resource-group"></a>

| Key | Type | Description | 
|-------------------|---------|----------------| 
| resourceGroupId | String | resource group ID list | 
| resourceGroupName | String | resource tag ID list |

- ResourceTag <a id="post-alarm-response-resource-tag"></a>

| Key | Type | Description | 
|-------------------|-------------|----------------| 
| tagId | Long | resource tag ID | 
| tagName | String | resource tag name |  

- Example

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

#### 1.2 Lookup Event Alarm 

**[Basic Information]**

| Method | URI | 
|--------|-----------------------------------------------------------------| 
| GET | /resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| Permission | 
|--------------------------------| 
| ResourceWatcher:Alarms.Get |

Lookup registered alrms.

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|--------------------| 
| appKey | Appkey issued from the console | 
| alarmId | ID of the alarm to look up |

**[Response Body]**

* ``Note``: 1.1) Same as [Response](#post-alarm-response) in Create event alarm.


#### 1.3 Lookup Event Alarm list

**[Basic Information]**

| Method | URI | 
|--------|--------------------------------------------------------------| 
| POST | /resource-watcher/v1.0/appkeys/{appKey}/event-alarms/search |

| Permission | 
|--------------------------------| 
| ResourceWatcher:Alarms.List |

Look up the list of registered event alarms. 
- You can look up the list of alarms that you want through search criteria in the request.
- Paging is supported.

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|--------|--------------------| 
| appKey | Appkey issued from console |


**[Query Parameter]**

| Key | Value | Required | 
|------|---------------------------------------------|----------| 
| page | Page number to look up<br/>Default value: 0 | No | 
| size | Number of alarms to view<br/>Default value: 10 | No | 
| sort | What and how to sort<br/>Default value: modDatetime, DESC | No |

**[Request Body]**

| Key | Type | Required | Description | 
|--------------------|----------|----------|----------------------------------------------------------------------------------------------------------------------------------| 
| alarmIds | String[] | No | List of alarm IDs<br/>String type list | 
| alarmNameAnyLike | String | No | Alarm Name (Search for all alamrs including input value)| 
| alarmRuleIds | String[] | No | alarm rule ID list<br/>String type list | 
| alarmStatusCodes | String[] | No | alarm status<br/>String type list<br/>Default value: STABLE, DISABLED<br/><br/>Type<br/>1. STABLE: Enable<br/>2. DISABLED: Deactivate<br/>3.  CLOSED: Delete | 
| descriptionAnyLike | String | No | Alarm description (Search for all alamrs including input value)| 
| modDateFrom | Date | No | starting date and time of last modification | 
| modDateTo | Date | No | ending date and time of last modification | 
| operatorUuids | String[] | No | Last modifier UUID list<br/>String type list | 
| resourceGroupIds | String[] | No | Resource group list<br/>String type list |

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

| Key | Type | Description | 
|------------|---------------------------------------|-------------| 
| alarms | [Alarm[]](#list-alarm-response-alarm) | alarm list | 
| totalItems | Long | Total count |


- Alarm <a id="list-alarm-response-alarm"></a>

| Key | Type | Description | 
|-----------------|----------------------------------------------|-----------------------------------------------------------------------------------| 
| alarmId | String | Alarm ID | 
| alarmName | String | alarm Name | 
| alarmRule | [AlarmRule](#list-alarm-response-alarm-rule) | alarm rule detailed information | 
| alarmStatusCode | String | alarm status code<br/><br/>Type<br/>1. STABLE: Enable<br/>2. DISABLED: Deactivate<br/>3.  CLOSED: delete | 
| appKey | String | Appkey information | 
| cabAlarmKey | String | alarm receiving group ID | 
| description | String | alarm description | 
| operatorUuid | String | last modified user UUID | 
| delDatetime | Date | date and time of deletion | 
| modDatetime | Date | date and time of modification | 
| regDatetime | Date | date and time of registration |

- AlarmRule <a id ="list_alarm_response_alarm_rule"></a>

| Key | Type | Description | 
|----------------------|----------|----------------------------------------------------------------------------------------| 
| alarmRuleId | String | alarm rule ID | 
| alarmRuleStatusCode | String | alarm rule status code<br/><br/>Type<br/>1. STABLE: Enable<br/>2. DISABLED: Deactivate<br/>3.  CLOSED: delete | 
| alarmRuleName | String | alarm rule name | 
| alarmRuleDescription | String | alarm rule Description | 
| resourceTypes | String[] | List of target resource type codes that alarm rules apply<br/>If empty value, it targets all resource types<br/>String type list |

- Example

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

#### 1.4. Modify Event Alarms

**[Basic Information]**

| Method | URI                                                            | 
|--------|----------------------------------------------------------------| 
| PUT | /resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| Permission | 
|--------------------------------| 
| ResourceWatcher:Alarms.Update |

Modify registered alarms. 
- As all requests will be changed, **you must send existing settings even if there are no changes**.

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|-------------------| 
| appKey | Appkey issued from console | 
| alarmId | Alarm ID to modify|


**[Request Body]**

* ``Note``: 1.1) Same as [ Request Body ](#post-alarm-request) in Create event alarm.

#### 1.5. Delete Event Alarm

**[Basic Information]**

| Method | URI | 
|--------|--------------------------------------------------------------| 
| DELETE | /resource-watcher/v1.0/appkeys/{appKey}/event-alarms/{alarmId} |

| Permission | 
|--------------------------------| 
| ResourceWatcher:Alarms.Delete |

Delete Registered Alarm

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|---------------------| 
| appKey | Appkey issued from console| 
| alarmId | alarm ID to delete|


**[Response Body]**

* ``Note``: Same [as Common Response Body](#common-response-body).


#### 1.6. Delete mutiple Event Alarm


**[Basic Information]**

| Method | URI | 
|--------|-------------------------------------------------------| 
| DELETE | /resource-watcher/v1.0/appkeys/{appKey}/event-alarms |

| Permission | 
|--------------------------------| 
| ResourceWatcher:Alarms.Delete |

Delete multiple Registered Alarm

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|-------------------| 
| appKey | Appkey issued from console|


**[Query Parameter]**

| Key | Value | Required | 
|----------|-------------------------------------------|----------| 
| alarmIds | Alarm ID list to delete<br/>Must enter more than 1 value. | Yes |

**[Response Body]**

* ``Note``: Same [as Common Response Body](#common-response-body).

### 2. alarm history 

#### 2.1. Look up alarm history


**[Basic Information]**

| Method | URI | 
|--------|------------------------------------------------------------------------------------------| 
| GET | /resource-watcher/v1.0/appkeys/{appKey}/alarms/{alarmId}/alarm-history/{alarmHistoryId} |

| Permission | 
|--------------------------------| 
| ResourceWatcher:AlarmHistories.Get |

Look up the history of the registered alarm for one time.

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|-------------------| 
| appKey |Appkey issued from console |
| alarmId | Alarm ID to lookup history| 
| alarmHistoryId | alarm history ID to lookup|


**[Response Body]**


| Key | Type | Description | 
|--------------|------------------------------------------------------------|-----------------| 
| alarm | [Alarm](#get-alarm-history-response-alarm) | alarm | 
| alarmHistory | [AlarmHistory](#get-alarm-history-response-alarm-history) | alarm history | 
| alarmRule | [AlarmRule](#get-alarm-history-response-alarm-rule) | Scope ID |

- Alarm <a id="get-alarm-history-response-alarm"></a>

| Key | Type | Description | 
|-------------|--------|--------------| 
| alarmId | String | alarm ID | 
| alarmName | String | alarm Name | 
| description | String | alarm description |

- AlarmHistory <a id="get-alarm-history-response-alarm-history"></a>

| Key | Type | Description | 
|-------------------------|--------------------------------------------------|------------------------------------------------------------------------------------| 
| alarmHistoryId | String | alarm history ID | 
| resource | [Resource](#get-alarm-history-response-resource) | resource information | 
| event | [Event](#get-alarm-history-response-event) | event information | 
| alarmSendResultTypeCode | String | alarm sending result<br/><br/>Type<br/>1. SUCCESS: Success<br/>2. FAILURE: Failed<br/>3.  SENDING: sending |

- AlarmRule <a id="get-alarm-history-response-alarm-rule"></a>

| Key | Type | Description | 
|----------------------|----------|---------------| 
| alarmRuleDescription | String | alarm rule description | 
| alarmRuleId | String | alarm rule ID | 
| alarmRuleName | String | alarm rule name |

- Resource <a id="get-alarm-history-response-resource"></a>

| Key | Type | Description | 
|--------------------------|--------|-------------------------------------------------------------------| 
| appKey | String | App Key | 
| resourceKey | String | Resource Identification Key | 
| orgId | String | Organization ID | 
| projectId | String | Project ID | 
| projectName | String | Project Name | 
| productId | String | Product ID | 
| resourceAppKey | String | Product App Key of the resource. AppKey | 
| iaasTenantId | String | IaaS tenant ID | 
| regionCode | String | region code | 
| resourceId | String | resource ID | 
| resourceName | String | resource name | 
| resourceTypeCode | String | resource type | 
| resourceCreationTypeCode | String | resource creation type<br/><br/>Type<br/>1. SYSTEM: Create a system<br/>2. USER: Create Member | 
| resourceStatusCode | String | Resource Status Code<br/><br/>Type<br/>1. STABLE: Normal<br/>2. CLOSED: Delete | 
| description | String | Resource description | 
| cloudResourceName | String | NHN Cloud Resource Name of the resource | 
| createDatetime | Date | Event occurrence time |
| regDatetime | Date | Registration time | 
| modDatetime | Date | Modification time | 
| delDatetime | Date | Delete time |

- Event <a id="get-alarm-history-response-event"></a>

| Key | Type | Description | 
|-----------------|---------------------------------------------------------|-------------------| 
| appKey | String | Appkey that event occured | 
| alarmId | String | alarmID |
| eventId | String | event ID | 
| eventSourceType | String | Subject type that made event occur | 
| eventUser | [EventUser](#get-alarm-history-response-event-user) | member that made event occur | 
| eventTarget | [EventTarget](#get-alarm-history-response-event-target) | target of event occurred | 
| eventDatetime | String | time that event occurred | 
| regDatetime | Date | date and time of event registration |

- EventUser <a id="get-alarm-history-response-event-user"></a>

| Key | Type | Description | 
|-----------|----------|---------------------| 
| userIdNo | String | user’s UUID who made event occur | 
| userId | String | user’s name who made event occur |
| userIp | String | user’s IP who made event occur | 
| userAgent | String | user’s agent who made event occur |

- EventTarget <a id="get-alarm-history-response-event-target"></a>

| Key | Type | Description | 
|-----------------|-------------------------------------------------------------|------------------| 
| targetMembers | [TargetMember[]](#get-alarm-history-response-target-member) | target member of the event occurred |


- TargetMember <a id="get-alarm-history-response-target-member"></a>

| Key | Type | Description | 
|--------------|--------|----------------------------------| 
| idNo | String | target user’s UUID that event occurred | 
| name | String | target user’s name that event occurred | 
| userCode | String | target user’s ID that event occurred(For IAM member) | 
| emailAddress | String | target user that event occurred |

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


#### 2.2. Lookup Alarm History List 


**[Basic Information]**

| Method | URI | 
|--------|-------------------------------------------------------------------------|
| GET | /resource-watcher/v1.0/appkeys/{appKey}/alarms/{alarmId}/alarm-history |

| Permission | 
|--------------------------------| 
| ResourceWatcher:AlarmHistories.List |

Lookup all the history that registered alarm sent.

**[Request Header]**

| Key | Value | 
|----------------------------|-------------------------------| 
| X-TC-AUTHENTICATION-ID | User Access Key issued by the console | 
| X-TC-AUTHENTICATION-SECRET | Secret Access Key issued by the console |

**[Path Variable]**

| Key | Value | 
|---------|--------------------------| 
| appKey | Appkey issued from console| 
| alarmId | alarm ID that lookup history |


**[Query Parameter]**

| Key | Value | Required | 
|--------------------------|-------------------------------------------------------|----------| 
| resourceKeys | list of resurce identification key<br/>Multiple input available | No | 
| resourceNameAnyLike | list of resurce identification key | No | 
| eventIds | event ID list<br/>Multiple input available | No | 
| eventSourceTypes | event source list<br/>Multiple input available | No | 
| projectIds | Project ID list<br/>Multiple input available | No | 
| productIds | Product ID list<br/>Multiple input available | No | 
| alarmSendResultTypeCodes | Alarm sending result list(SENDING, SUCCESS, FAILURE)<br/>Multiple input available | No | 
| regDateFrom | Starting date and time of period to lookup<br/>yyyy-MM-ddTHH:mm:SS | No | 
| regDateTo | Ending date and time of period to lookup<br/>yyyy-MM-ddTHH:mm:SS | No | 
| page | Page unmber to lookup<br/>Default value: 0 | No | 
| size | Number of alarms to view<br/>Default value: 10 | No | 
| sort | Sorting target and method | No |

**[Response Body]**

| Key | Type | Description |
|----------------|---------------------------------------------------------------|--------------| 
| alarmHistories | [AlarmHistory[]](#list-alarm-history-response-alarm-history) | Alarm histoy list | 
| totalItems | Long | total count |

- AlarmHistory <a id="list-alarm-history-response-alarm-history"></a>

| Key | Type | Description | 
|-------------------------|---------------------------------------------------|------------------------------------------------------------------------------------| 
| alarmHistoriesId | String | alarm history ID | 
| resource | [Resource](#list-alarm-history-response-resource) | Resource information | 
| event | [Event](#list-alarm-history-response-event) | Event information | 
| alarmSendResultTypeCode | String | Alarm sending results<br/><br/>Type<br/>1. SUCCESS: Success<br/>2. FAILURE: Failed<br/>3.  SENDING: sending |

- Resource <a id="list-alarm-history-response-resource"></a>

| Key | Type | Description | 
|--------------------------|--------|-------------------------------------------------------------------| 
| appKey | String | App Key | 
| resourceKey | String | Resource Identification Key | 
| orgId | String | Organization ID | 
| projectId | String | Project ID | 
| projectName | String | Project Name |
| productId | String | Product ID | 
| resourceAppKey | String | Product App Key of the resource. AppKey | 
| iaasTenantId | String | IaaS tenant ID | 
| regionCode | String | region code | 
| resourceId | String | resource ID | 
| resourceName | String | resource name | 
| resourceTypeCode | String | resource type | 
| resourceCreationTypeCode | String | resource creation type<br/><br/>Type<br/>1. SYSTEM: Create a system<br/>2. USER: Create Member | 
| resourceStatusCode | String | Resource Status Code<br/><br/>Type<br/>1. STABLE :normal<br/>2. CLOSED :delete| 
| description | String | resource description |
| cloudResourceName | String | NHN Cloud Resource Name of resource| 
| createDatetime | Date | Event occurrence time | 
| regDatetime | Date | Registration time |
| modDatetime | Date | Modification time | 
| delDatetime | Date | Delete time |

- Event <a id="list-alarm-history-response-event"></a>

| Key | Type | Description | 
|-----------------|----------------------------------------------------------|---------------------| 
| appKey | String | Appkey that event occured | 
| alarmId | String | alarm ID | 
| eventId | String | event ID | 
| eventSourceType | String | Subject type that made event occur | 
| eventUser | [EventUser](#list-alarm-history-response-event-user) | member that made event occur | 
| eventTarget | [EventTarget](#list-alarm-history-response-event-target) | target of event occurred | 
| eventDatetime | String | time that event occurred | 
| regDatetime | Date | date and time of event registration |

- EventUser <a id="list-alarm-history-response-event-user"></a>

| Key | Type | Description |
|---------------------|--------|--------------------| 
| userIdNo | String | user’s UUID that made event occur | 
| userId | String | user’s name that made event occur | 
| userIp | String | user’s IP that made event occur | 
| userAgent | String | user’s agent that made event occur |

- EventTarget <a id="list-alarm-history-response-event-target"></a>

| Key | Type | Description |
|-----------------|--------------------------------------------------------------|------------------| 
| targetMembers | [TargetMember[]](#list-alarm-history-response-target-member) | taregt membr for the event occurred |


- TargetMember <a id="list-alarm-history-response-target-member"></a>

| Key | Type | Description | 
|--------------|--------|---------------------------------| 
| idNo | String | target User’s UUID for the event occurred |
| name | String | target User’s name for the event occurred | 
| userCode | String | target User’s ID for the event occurred (For IAM member) |
| emailAddress | String | target member for the event occurred |

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


### 3.  Event

#### 3.1. Lookup Event list

**[Basic Information]**

| Method | URI | 
|--------|-------------------------------------------------| 
| GET | /resource-watcher/v1.0/appkeys/{appKey}/events |

Look up the list of events that can be set in alarm. 
- Event is based on the event ID registered in CloudTrail. 
- Provides search features for events. 
- When searching by event name or product name, you can set lang to search in the language that matches the set value.

**[Request Header]**

| Key                        | 	Value                                                                   |
|----------------------------|--------------------------------------------------------------------------|
| lang                       | 	Language code<br/>Search conditions and response values are values corresponding to the language code (ko, en, ja, zh)<br/>Default value: ko |

**[Path Variable]**

| Key     | Value                    |
|---------|--------------------------|
| appKey  | Appkey issued by the console       | 

**[Query Parameter]**

| Key | Value | Required | 
|-----------------|----------------------------------------------------------------------|----------| 
| productIdList | List of product IDs to search for<br/>Multiple input available | No | 
| productNameLike | Product name to search | No |
| eventNameLike | Event name to search | No | 
| page | Page number to lookup<br/>Default value: 0 | No | 
| size | Number of alarms to view<br/>Default value: 10 | No | 
| sort | What and how to sort<br/>Default value : productName:ASC,eventName:ASC | No |

**[Response Body]**

| Key | Type | Description | 
|------------|----------------------------------------|--------------| 
| events | [Event[]](#list-event-response-event) | list of events | 
| total Items | Long | total count |


- Event <a id="list-event-response-event"></a>

| Key | Type | Description |
|-------------|---------|--------------| 
| eventId | String | Event ID | 
| productId | String | Product ID | 
| productName | String | Product name |


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