## Governance & Audit > Resource Watcher > Console Guide

### Getting Started

Resource Watcher is a service provided by default when an organization is activated.
Therefore, after creating an organization, you can use the service by selecting **Resource Watcher** from **Select Service** without any additional action.

To be notified of various changes that occur in resources,
you have to first define a resource group or resource tag before creating notifications.
You can receive notifications on resources that have been created or changed after setting notifications.


### Resource Tag

You can manage resources by setting resource tags freely.

If you change resource tag names, you can also change tags that are already set on the resource. Resource Watcher automatically changes tags that are set on resources after modifying tag names for convenience and consistency.
You can set more than one resource tags for a resource, and a resource tag ca be set for more than one resources.
By using resource tags, you can select target resources from resource groups and notifications.

Resource tags can be created and set to resources by the user, and can also be set to resources automatically.

| Resource Tag Type | Description |
|---|---|
| User Type | <li>Resource tag created by the user.</li><li>You can change the resource tag name. </li><li>You can freely set or release resource tags.</li>  |
| System Type | <li>Resource tag created by system. </li><li>When a resource is created, the resource tag is set automatically by the system.</li><li>Users cannot set or release system type resource tags.</li> |

> Resource tags in system type is going to support Tags in current project name, allowing to manage project information for each Resource as Tags. We plan to continue to add system type resource tags.

#### Search resource tag

Search resource tag consists of as follows.

![resourcewatcher_11](https://static.toastoven.net/prod_resource_watcher/img11_EN.png)

| Items | Description |
|---|---|
| Resource Tag Search Conditions | You can search for list of appropriate resource tags by specifying the following search criteria:<li>Resource Tag Separation: USER (User Type), SYSTEM (System Type)</li><li>Resource Tag Key: Select resource tag that contains the name entered</li><li>Last Modified Date: Select resource tag for Last Modified Period |
| Create and Delete Resource Tags | You can add new resource tag or select resource tag that appears in the list to delete it. |
| Select Whole Resource Tag | Select or deselect all resource tags displayed on the screen. |
| Display Resource Tag Item | Displays name, classification, and last modified date and time information of resource tag on screen. You can select and delete each resource tag. |
| Query Resource list by resource tag | You can view list of Resources that sets resource tags. |
| Modify  | You can change resource tag name.  |

Click **View Resource list by resource tag** to display a list of resources at the bottom of screen as follows

![resourcewatcher_12](https://static.toastoven.net/prod_resource_watcher/img12_EN.png)

| Items | Description |
|---|---|
| Tag Name | Display Selected Tag name.  |
| Resource Search Condition | You can search for Resources list in resource tag with following search criteria<li>Project Name: Displays projects list that Resource was created.</li><li>Services: Displays list of service in use by the corresponding organization.</li><li>Resource Type: Displays list of Resource types provided by NHN Cloud service in use.</li><li>Region: Displays list of regions available to the corresponding organization.</li><li>Resource Name: Select Resource that contains the search term entered.</li><li>Resource creation date and time—Specifies the period the Resource was created. |
| Resource List | Displays list of selected resource tag resources. |

> Resource Tag Search screen only provides queries for the list of resources that have set up resource tag.
> Features to set tags to resources is provided from **Resource** menu.

#### Create Resource Tag

To create resource tag, press **+ Create resource tag** on Search resource tag screen.

![resourcewatcher_13](https://static.toastoven.net/prod_resource_watcher/img13_EN.png)

On Create resource tag screen, enter the tag name you want to add and press **Confirm** to create resource tag.
If the same resource tag already existed, it will not be created additionally.

#### Modify Resource Tag

In resource tags list from Search resource tags screen, , press **Modify** for the item want to change.
Modify tag name and press **Confirm** to reflect the changes.

> When modifying resource tag, tag settings for resource on which the corresponding tag is set are also to be changed.

#### Delete Resource Tag

On Search resource tag screen, you can delete an item by selecting the check box want to delete from resource tag list.  
You can also delete multiple items at once.

Precautions for deleting resource tags are as follows.
- Deleting resource tag removes tag settings values from all resources on which the corresponding tag is set.
- If **Select Resource** is set as resource tag and the tag is set as Target item when creating a resource group, the tag entry is also removed from the resource group.
- When creating notifications, if **Target Resource** is set to **Select resource tag** and the corresponding tag is set, the tag entry is also removed from the notification target.

![resourcewatcher_14](https://static.toastoven.net/prod_resource_watcher/img14_EN.png)



### Resource Group

You can create resource groups in different ways to separate and manage resources.
You can create resource groups by specifying one of the resource group creation methods in the table below.

| Resource Group Creation Method | Description |
|---|---|
| Resource Type | <li>Include resources in resource group that matches the specified resource type.</li><li>When a resource is created, the resource is automatically added to the resource group that matches the resource type.</li>  |
| Resource Tag | <li>Include resources with specified resource tag in the resource group.</li><li>When added a resource tag to resource, the resource is automatically added to the resource group created based on the same tag.</li>  |
| Select Resource Directly | <li>Select and specify target resources to include in the resource group on the screen.</li> |

> Resource can be included and managed in more than one resource groups.
> When a resource is deleted, it is automatically removed from the resource group.

#### Search Resource Group

Search Resource Group screen consists of the following.

![resourcewatcher_01](https://static.toastoven.net/prod_resource_watcher/img01_EN.png)

| Items | Description |
|---|---|
| Resource Group Search Conditions | You can search for appropriate resource groups by specifying following search criteria.<li>Resource Group Creation Type</li><li>Resource Group Name/Resource Group Description: Select a resource group that contains the search term entered</li><li>Last Modified Date: Select a resource group for Last Modified Period |
| Create and Delete Resource Group | You can add new resource group or select the resource group that appears in the list to delete it.  |
| Select The Whole Resource Group | Select or deselect all resource groups displayed on the screen. |
| Display Resource Group Items | Displays name, description, creation type, last modified date and time information of resource group on screen. You can select and delete each resource group. |
| Resource List Query for each Resource Group | You can view the resource lists that are included in resource group. |
| Modify  | You can modify resource group settings. |

Click **View Resource List by resource group** to display the list of resources at the bottom of the screen as follows

![resourcewatcher_02](https://static.toastoven.net/prod_resource_watcher/img02_EN.png)

| Items | Description |
|---|---|
| Resource Search Condition | You can search for list of resources in the  resource group with the following Search criteria:<li>Project Name: Displays projects list that resource was created.</li><li>Services: Displays list of service in use by the corresponding organization.</li><li>Resource Type: Displays list of Resource types provided by NHN Cloud service in use.</li><li>Region: Displays list of regions available to the corresponding organization.</li><li>Resource Name: Select Resource that contains the search term entered.</li><li>Resource creation date and time—Specifies the period the Resource was created. |
| Resource List | Displays list of Resources included in the selected resource group. |


#### Create Resource Group

To create a resource group, on Search resource group screen, click **Create Resource Group**.

![resourcewatcher_03](https://static.toastoven.net/prod_resource_watcher/img03_EN.png)

| Items | Description |
|---|---|
| Create | Specifies basic information and creation method for resource groups. |
| Preview Resource | View the list of Resources in resource group by the creation method set on the **Create** tab. |
| Select Resource | Specifie how a resource group includes Resources. You can specify one out of **Resource type**, **resource tag**, or **Select resource directly** and select target items. |
| Display Selected Items | Displays the items that user specified in the selectable items. You can remove it from the selected item by pressing [X] button to the right of name. |
| Display Selectable Items | Display List of items that can be set according to how the Resource is selected. **Resource types** and **resource tags** can be filtered by Search terms. |

If you specify **Select Resources** method on **Create** tab, you can view the list of selected Resources on ** Preview Resource ** tab.

![resourcewatcher_04](https://static.toastoven.net/prod_resource_watcher/img04_EN.png)

| Items | Description |
|---|---|
| Resource List | Displays list of resources selected by resource group creation method. |
| Resource Search Condition | You can filter the list of Resources. |


#### Modify Resource Group

You can change name, description, Resource creation type of resource group.
You can modify it the same way created resource group.

#### Delete Resource Group

You can delete an item by selecting the check box for the item you want to delete from resource groups list on the Search Resource Group screen.
You can also delete multiple items at once.

![resourcewatcher_05](https://static.toastoven.net/prod_resource_watcher/img05_EN.png)


### Notification

You can detect changes in resources of interest, send notifications including email, SMS, and Webhook based on conditions, and manage history of notifications sent.

Target Resource for notification can be set using resource groups or resource tags.
Therefore, you must create resource groups or resource tags before creating notifications.
When selecting notification targets, if Resource is added after notification is created by re-tagging a resource group or resource tag that was already used for another notifications, it is automatically included in notifications once the corresponding Resource is included in a resource group or resource tag that have set when the notification was created.

알림을 생성할 때 이벤트를 선택하고 대상 리소스를 지정하면 해당 리소스에서 해당하는 이벤트가 발생했을 때 사용자에게 알림을 전송합니다.
이때 선택하는 이벤트는 CloudTrail 서비스에서 제공하는 이벤트 목록과 동일합니다.

#### Search Notifications

Search Notifications screen consists of the following.

![resourcewatcher_31](https://static.toastoven.net/prod_resource_watcher/img31_EN.png)

| Items | Description |
|---|---|
| Search Notifications Condition | You can search for Resources by specifying the following search criteria.<li>Notification Status: Specifies notification of enabled, disabled status.</li><li>Search Notification Name/Notification Description: Search for Notifications that contain Search term entered.</li><li>Last Modified Date of Notification: Search notifications for the last modified period.</li> |
| Create Notification, Delete Notification | Creates Notification or deletes the selected Notification. |
| Select whole Notifications | Select or deselect whole Notifications displayed on screen.  |
| Display Notification items.  | Displays information about Searched notifications on screen. |
| Change Notification Status | You can change status of notification to toggle switch button.<li>Blue color indicates that notification is activated, and when an event occurs that meets the notification criteria, it sends notifications. </li><li>Gray color indicates that notification is disabled and does not send notifications when an event occurs.</li> |
| View Notifications in Detail | Click **View** to view notification history and target Resource information for that notification. |
| Modify Notifications | You can modify name and description of Notifications, target Resource, and recipient of the Notifications. |

Click **View** to view notification history and target Resource information for that notification at the bottom of screen.

![resourcewatcher_32](https://static.toastoven.net/prod_resource_watcher/img32_EN.png)

| Items | Description |
|---|---|
| Notification name | Displays the name of selected Notification. |
| View Notification history, View Resource list | You can check notification history and target Resource information. |
| Notification history Search condition | You can filter list of notification history.  |
| list of notification history | Displays information about searched notification history on screen. You can check the date and time the notification occurred, Resource name where the notification occurred, project, and result of sending notification. |

> Results of notification history are as follows.
> - Successful: Request to send notification to the notification recipient that you set when creating the notification was successful.
> - Failed: Request to send notification to some or all of the recipients of notification failed.

Click **View Resource List** tab to display list of Resources to be notified at the bottom of screen.

![resourcewatcher_33](https://static.toastoven.net/prod_resource_watcher/img33_EN.png)

| Items | Description |
|---|---|
| Resource Search Condition | You can filter list of Resources to be notified. |
| Resource List | Displays list of Searched notification target resources on screen.  |

#### Create Notifications

To Create notifications, press ** Create notifications** on Search Notifications screen.

![resourcewatcher_35](https://static.toastoven.net/prod_resource_watcher/img35_EN.png)

| Items                           | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|---------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create notifications            | Press ** Create notifications ** to enter notifications information.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Select Events                   | Specify Events that wants to get notified.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Target Resource                 | Specify Resources of interest that wants to get notified.<li>You can specify **Resource Group** and **Resource Tag**. Press **Add** to select it.</li><li>When multiple items are selected, all Resources set for each item are targeted.</li><li>Press [X] button on the right to remove the selected target.</li>                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Notifications Recipients Target | Set the recipient to forward email, SMS when sending notifications. Types of Notifications recipients are as follows.<li>Organization members: NHN Cloud membership, IAM membership can be selected by themselves.</li><li>Notifications Recipients Groups – Can select predefined recipients from the list of Notifications Recipients Groups in one’s organization. If multiple Notifications require the same notification recipient settings, we recommend that you register and use them in **Console**>**Organization**>** Notifications Recipients Groups Management**.</li><li>Role: Sends notifications to all members with the selected role. You can choose from organizational roles, project roles, project common role groups, and service roles.</li> |
| WebHook                         | You can forward WebHook requests when sending notifications.<li>Supports https protocol. </li><li>For WebHook Payload Spec, refer to [Console Usage Guide].</li><li>When entered secret key, the HMAC credentials are sent to the header. Refer to [Console Usage Guide].</li>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Confirm                         | Set all notifications rules, target resources, notifications recipients, or webhooks required to generate notifications, and press **OK** to generate notifications.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |

> If select type `role` for notification target, the notification is sent only to members whose roles you select are set up.  
> For example, if select `ADMIN` role, members with `OWNER` role will not be notified.
>
> If select project role, notifications are sent only to the members of the project to which the selected role set among project members the Resource that event occurred belong to.  
> For example, if you have two projects created and you select ADMIN member, notification is sent to the project member who has ADMIN role of Project A when notifications occur on the Resource created in Project A.

#### Modify Notifications

You can change name, description, events, target resource of the notification, notification recipient, or the webhook settings.
- Modify notification settings that have already been created in the list, and then click **Add** to reflect modified values.
  - Example: Select Email only and if the value is saved, select SMS from the list and press **Add** to update it.

#### Delete Notifications

You can delete item by selecting check box for the item you want to delete from notification list on Search Notification screen. You can also delete multiple items at once.

![resourcewatcher_34](https://static.toastoven.net/prod_resource_watcher/img34_EN.png)

> If delete an alert, you will no longer receive notification.  
> Notification history information that occurred in that notification will be deleted together and cannot be recovered.
Deleting Notification does not affect Resources, resource groups, or resource tags.

### Resource

Manage Resources managed by various services within NHN Cloud organization.
If multiple projects use the same service, Resource Watcher service allows to view list of Resources at a glance and view events for each Resource without having to visit the service console for respective project.
You can also set resource tags for Resources and set resource groups to categorize them from different perspectives.

Resources are managed as part of the following schemes.

| Items | Description |
|---|---|
| Service | <li>Refers to NHN Cloud Service in use.</li><li>Examples include default infrastructure services, Kubernetes, Deploy, and so on. | 
| Resource Type | <li>Resource type is simple and basic information that categorizes physically created Resources.</li><li>Each service can consist of different Resource types.</li><li>For example, default infrastructure service consists of Compute Instance, Network, and it provides users with Resource types such as Instance and VPC.</li><li>Resource types are part of classification criteria that you provide with resource tags, and are called information about the Resource's inherent properties. | 
| Resource | <li>Materialized targets created and managed by each service.</li><li>For example, it can be Instance managed by Compute > Instance service or VPC managed by Network > VPC service.</li><li>Each Resource has configuration information about itself, such as name or implementation type.</li><li>It may also be associated with other Resources. |
| Event | <li>There are various events that can occur for each Resource.</li><li>CloudTrail allows to view and manage various events created by multiple Resources at once.</li><li>Resource Watcher provides more detailed view of the different events that occur for each individual Resource. |

Resource consists of following information.

| Items | Description |
|---|---|
| Resource name | Name defined by user or automatically given by system. |
| Resource ID | Identification information given when Resource was created.<br>As cope of uniqueness may vary from service to service, it is difficult to use as unique identification information  |
| NCRN | NHN Cloud Resource Name (NCRN) is the only identification key information that can identify Resources across NHN Cloud services.<br>Display format: `Organization ID`:`Project ID`:`Resource Type`:`Appkey`:`Region Code`:`Resource ID`<br>Example: `IU1gUaQki7fDv97V:5FlHkACb:INSTANCE:9hR8Mh5nG4inePQh:KR1:f75ad8ad-a97e-47b6-acec-8c09304290c5` |
| Project | Name of the project where the resource was created.<br>Do not display information if it is organizational product resource. |
| Service | Service information to which Resource belongs to. |
| Resource Type | Service type information to which Resource belongs to. | 
| Region | Region Information where the resource was created. <br>Do not display resources for services that do not distinguish regions. |
| Resource Group List | Resource Group List including resources |
| Resource Tag List | Resource Tag List which was set in Resource | 


#### Search Resources

Search Resource screen consists of as follows.

![resourcewatcher_41](https://static.toastoven.net/prod_resource_watcher/img41_EN.png)

| Items | Description |
|---|---|
| Resource Search Condition | You can search for resources by specifying the following search criteria.<li>Project name</li><li>Service</li><li>Resource Type</li><li>Region</li><li>Resource Group</li><li>Resource Tag</li><li>Resource Name</li><li>Resource Creation date and time |
| Resource Tag Settings | Sets resource tag for selected Resource. |
| Select whole Resource | Select or deselect all Resources displayed on screen. |
| Display Resource Items | Displays information of Searched Resource on screen. Click **View** to check Resource information in detail. |
| Resource Group | Display one of the resource groups that contain the corresponding Resource and displays the number of other set resource groups. To view complete list of resource groups, click the location.  |
| Resource Tag | Display one of the resource tags that contain the corresponding resource tags and displays the number of other set resource tags. To view complete list of resource tags, click the location. |
| Hovering Menu | Display the full list of resource groups or resource tags. |
| View in Detail | Query `basic information` and `events` for the corresponding resource. |

> When create or delete each Resource from NHN Cloud service, Resource Watcher service automatically synchronizes and exposes Resource information.

When creating a resource, it is exposed to the list, and when you delete the resource, it is removed from the list and not to be exposed. We will provide features to check deleted resources in the future.

Press **View** to display list of basic information and events for Resource at the bottom of screen as follows.

![resourcewatcher_42](https://static.toastoven.net/prod_resource_watcher/img42_EN.png)

| Items | Description |
|---|---|
| Basic Information, Event Query | Provides tabs for querying basic information about Resources and for querying events that occur in Resources. |
| Resource basic information item | Displays basic information for the selected Resource. |

Click **Event Query** tab to display list of events that occurred in Resource at the bottom of screen.

![resourcewatcher_43](https://static.toastoven.net/prod_resource_watcher/img43_EN.png)

| Items | Description |
|---|---|
| Search Event Condition | You can search for list of events that occurred in Resource with following search criteria.<li>Events: Specifies list of possible events for that corresponding resource type.</li><li>Date and time of event occurrence: Specifies period that event occurred. |
| Event List | Displays the list of events that occurred on that resource. |

#### Resource Tag Setting

To set resource tags for Resource, click **Set resource tags** on Search Resource screen.

![resourcewatcher_45](https://static.toastoven.net/prod_resource_watcher/img45_EN.png)

| Items | Description |
|---|---|
| Select Resource | Select check box for Resource for which want to set resource tag. Select check box to activate **Set resource tag** button. |
| Resource Tag Setting | Press **Set resource tag** to pop up Settings screen. |
| Select Resource Tag | Search list of target resource tags to set. View list of tags of which names partially match resource tags |
| Display Selectable Items | Display list of all resource tags, and, if resource tag exists set for that Resource, enables check box for respective tag item. |
| Confirm | Reflect the content of the **Resource Tag Settings** screen. |

Precautions for Setting resource tags
> When setting resource tags, the list of previously set resource tags is initialized and set as the newly specified resource tag.
