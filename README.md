# Parking Application
![Parking portal home](https://github.com/user-attachments/assets/96363261-9005-4e27-bdbd-8c8a00388132)

A cusotom parking appliaction in servicenow, to help you manage your parking system in company more easly and consistently. This app is integratable with other systems via Api's and can support multiple parkings. Bellow is the in depth techical documentation about it.

There are two main roles needed to works with this application:
**x_1230661_parking_user, x_1230661_parking_user.**

To navigate in app in Core UI go to: All -> Parking Application. Like in picture bellow:
<<br/>
![image](https://github.com/user-attachments/assets/f93cace0-a580-4248-b72a-7d4ecabccc7f)

Bellow is the general scheme of obstract overview how application is set up (yea, not proffesional but I hope yyou get the idea :D):

<br/>

![schema](https://github.com/user-attachments/assets/f35362e2-ef0b-4dc9-8671-9fba9fbe1065)

<details>
<summary style="color: #e1b91d; font-size: 21px">Tables</summary>

<h3 style="color: #3cb996">Parking Lists Tables</h3>

This table stores all parkings of the company.
These are custom fields besides

| Label            | Description                                  |
| :--------------- | :------------------------------------------- |
| `Number`         | Unique number of parking                     |
| `Name`           | **Required**. Name of parking                |
| `Location`       | **Required**. Adress of the parking          |
| `Parking places` | **Required**. Total available parking places |
| `Active`         | Status of parking                            |

<h3 style="color: #3cb996"> Parking requests table</h3>

This table stores all parkings of the company.

| Label                | Description                                                       |
| :------------------- | :---------------------------------------------------------------- |
| `Plate number`       | **Required**. Car plate number                                    |
| `Approve status`     | Status of the request                                             |
| `Requested by`       | **Required**. User who requested parking. Referemce to user table |
| `Additional details` | Additional details about request                                  |
| `Reservation date`   | Parking reservation date                                          |
| `Parking name`       | Reference to parking list table record                            |
| `Rejection reason`   | Reference to parking list table record                            |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Rest Apis</summary>
To interact with apis, user needs to have above mentioned roles.

<details>
<summary style="color: #2dd92d; font-size: 18px">Rest Apis For Parking Lists table</summary>

<br/>
<h3 style="color: #3cb996"> Create</h3>

Create a parking in parking list table.

```http
  Post /api/x_1230661_parking/parking_list/create
```

#### Type: Json

#### Response: Json

Request body
| Field | Type | Description |
| :-------- | :------- | :------------------------- |
|`parkingName`| `string` | **Required**. Your parking name |
|`parkingLocation`| `string` | **Required**. Your parking adress |
|`parkingSlots`| `number` | **Required**. Your total parking places |

<details>
<summary>Example request:</summary>

```javascript
{
    "parkingName": "Parking test name",
    "parkingLocation": "Los Angels, 35-255",
    "parkingSlots": 120
}
```

#### Example response:

```javascript
{
    "result": {
        "message": "New parking was created: PAR0001016!"
    }
}
```

</details>
<br/><br/>
<h3 style="color: #3cb996"> Update </h3>

Update parking on parking list table

```http
  PATCH /api/x_1230661_parking/parking_list/update/{number}
```

#### Types: Requests->Json, Response-> Json

| Parameter | Type     | Description                               |
| :-------- | :------- | :---------------------------------------- |
| `number`  | `string` | **Required**. Number of parking to update |

Request body (can be one, couple or all)
| Field | Type | Description |
| :-------- | :------- | :------------------------- |
|`parkingName`| `string` | Your parking name |
|`parkingLocation`| `string` | Your parking adress |
|`parkingSlots`| `number` | Your total parking places |
|`parkingValid`| `string` | Make active or deactivate parking. "true" or "false"|

<details>
<summary>Example request:</summary>

```javascript
api/x_1230661_parking/parking_list/update/PAR0001016

{
    "parkingSlots": 100
}
```

</details>

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "parkingNumber": "PAR0001016",
        "parkingName": "Parking test name",
        "parkingLocation": "Los Angels, 35-255",
        "parkingSlots": "100"
    }
}
```

</details>

<br/><br/>

<h3 style="color: #3cb996"> Read single record</h3>

Fetch data about single parking from parking list table.

```http
  GET /api/x_1230661_parking/parking_list/read/{number}
```

#### Types: Requests->Json, Response-> Json

| Parameter | Type     | Description                            |
| :-------- | :------- | :------------------------------------- |
| `number`  | `string` | **Required**. Number of parking to get |

#### Example request:

```javascript
/api/x_1230661_parking/parking_list/read/PAR0001016
```

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "parkingNumber": "PAR0001016",
        "parkingName": "Parking test name",
        "parkingLocation": "Los Angels, 35-255",
        "parkingSlots": "100"
    }
}
```

</details>

<br/><br/>

<h3 style="color: #3cb996"> Read parking list </h3>

Read full list of available parkings, which are active from parking list table.

- **Point for improvement:** _This end point fetches all parking that are active. If in future this list would grow, it is safe to assume that limiting the resoponse would berequired. But for now did not added it as it is a small list to be expected._
- **Point for improvement:** _Also now it feches all fields for all. Anotehr improvement would be to add query which decideds what fields to fetch. Expecialy if table data would grow. For now as it has couple fields, no issue to bring all fields._

```http
  GET /api/x_1230661_parking/parking_list/readlist
```

<details>
<summary>Example response:
</summary>

```javascript
{
    "result": [
        {
            "parkingNumber": "PAR0001003",
            "parkingName": "Kaunas parking",
            "parkingLocation": "Kaunas, lukiskiu 38-5, Lietuva",
            "parkingSlots": "15"
        },
        {
            "parkingNumber": "PAR0001008",
            "parkingName": "Klaipedos parkingas",
            "parkingLocation": "Sveciu g, 37-55",
            "parkingSlots": "15"
        },
        {
            "parkingNumber": "PAR0001016",
            "parkingName": "Parking test name",
            "parkingLocation": "Los Angels, 35-255",
            "parkingSlots": "15"
        },

    ]
}
```

</details>
</details>

<details>
<summary style="color: #2dd92d; font-size: 18px">Rest Apis For Parking requests table</summary>
<br/>
<br/>
<h3 style="color: #3cb996"> Create request</h3>

Create reservation for parking in parking requests table.

```http
  POST /api/x_1230661_parking/parking_requests/create
```

#### Types: Request -> json, response -> json

Request body
| Field | Type | Description |
| :-------- | :------- | :------------------------- |
|`requestor` | `string` | **Required**. User id for whom reservating |
|`parking` | `string` | **Required**. Number of the parking |
|`plateNumber` | `string` | **Required**. Car plate number |
|`date` | `string` | **Required**. Reservation date in yyyy-MM-dd format |

<details>
<summary>Example request:</summary>

```javascript
{
    "requestor": "test",
    "parking": "PAR0001003",
    "plateNumber": "AAA445",
    "date": "2024-07-18"
}
```

</details>

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "message": "New parking request created: PREQ0001205."
    }
}
```

</details>

<br/><br/>

<h3 style="color: #3cb996"> Update request</h3>

Update reservation in parking requests table.

```http
  PATCH /api/x_1230661_parking/parking_requests/update/{requestNumber}
```

| Parameter       | Type     | Description                     |
| :-------------- | :------- | :------------------------------ |
| `requestNumber` | `string` | **Required**. Number of request |

#### Types: Requests->Json, Response-> Json

Request body
| Field | Type | Description |
| :-------- | :------- | :------------------------- |
|`requestor` | `string` | User id for whom reservating |
|`parking` | `string` | Number of the parking |
|`plateNumber` | `string` | Car plate number |
|`date` | `string` | Reservation date in yyyy-MM-dd format |
|`details` | `string` | Reservation date in yyyy-MM-dd format |

<details>
<summary>Example request:</summary>

```javascript
{
    "parking": "PAR0001013",
    "plateNumber": "AAA446",
    "date": "2024-07-20"
}
```

</details>

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "reqNumber": "PREQ0001205",
        "requestor": "TEST",
        "parking": "Skuodo parkingas",
        "status": "Approved",
        "plateNumber": "AAA446",
        "date": "2024-07-20",
        "details": "",
        "rejection": ""
    }
}
```

</details>

<br/><br/>

<h3 style="color: #3cb996"> Read record</h3>

This api returns single request data, based on request number. By default returns all fields.

```http
  GET /api/x_1230661_parking/parking_requests/read/{requestNumber}
```

| Parameter       | Type     | Description                     |
| :-------------- | :------- | :------------------------------ |
| `requestNumber` | `string` | **Required**. Number of request |

#### Types: Requests->Json, Response-> Json

Query param:

| Query parameter | Type     | Description                                                                                           |
| :-------------- | :------- | :---------------------------------------------------------------------------------------------------- |
| `fields`        | `string` | String of fields which to return:<i>'requestor,parking,status,plateNumber,date,details,rejection'</i> |

<details>
<summary>Example request without query param: </summary>

```javascript
/api/x_1230661_parking/parking_requests/read
```

</details>

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "reqNumber": "PREQ0001205",
        "requestor": "TEST",
        "parking": "Skuodo parkingas",
        "status": "Approved",
        "plateNumber": "AAA446",
        "date": "2024-07-20",
        "details": "",
        "rejection": ""
    }
}
```

</details>

<details>
<summary>Example request with query param: </summary>

```javascript
/api/x_1230661_parking/parking_requests/read/PREQ0001205?fields=plateNumber,details,date
```

</details>

<details>
<summary>Example response:</summary>

```javascript
{
    "result": {
        "plateNumber": "AAA446",
        "details": "",
        "date": "2024-07-20"
    }
}
```

</details>

<br/><br/>

<h3 style="color: #3cb996"> Read list</h3>

This api returns list of requests, which are approved and are valid from current to future date. By default limit is set to 100.

```http
  GET /api/x_1230661_parking/parking_requests/readlist
```

#### Types: Requests->Json, Response-> Json

Query param:

| Query parameter | Type     | Description                                                   |
| :-------------- | :------- | :------------------------------------------------------------ |
| `request_date`  | `string` | Date in yyyy-MM-dd format, for which you want to get requests |
| `userId`        | `string` | User id of user, for which you want to get requests           |
| `parking_nr`    | `string` | Parking number for which you want to get requests             |
| `limit`         | `number` | Change default response limit                                 |

<details>
<summary>Example request without query param response: </summary>

```javascript
 "result": [
        {
            "reqNumber": "PREQ0001196",
            "requestor": "admin",
            "parking": "Kaunas parking",
            "status": "Approved",
            "plateNumber": "sss555",
            "date": "2024-07-17",
            "details": "",
            "rejection": ""
        },
        {
            "reqNumber": "PREQ0001195",
            "requestor": "admin",
            "parking": "Kaunas parking",
            "status": "Canceled",
            "plateNumber": "aaa255",
            "date": "2024-07-17",
            "details": "test",
            "rejection": ""
        }
    ]
}
```

</details>

<details>
<summary>Example request with query param: </summary>

```javascript
/api/x_1230661_parking/parking_requests/readlist?parking_nr=PAR0001004

response

{
    "result": [
        {
            "reqNumber": "PREQ0001194",
            "requestor": "admin",
            "parking": "Vilnius Parking",
            "status": "Approved",
            "plateNumber": "bbb444",
            "date": "2024-07-17",
            "details": "",
            "rejection": ""
        }
    ]
}
```

</details>

</details>

<!-- Last wrapper-->
</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Script includes</summary>

<p>There are in total 6 script includes related to Parking application. You can update them, change them, make modifications of upgrade if needed. They are not locked. Feel free to adjust to your needs. The list is bellow:</p>

| Name                                   | Description                                                         |
| :------------------------------------- | :------------------------------------------------------------------ |
| `parkingAppUtils`                      | Methods related to core UI validation and Portal Widgets.           |
| `ScriptedRestParkingErrorUtils`        | Methods related to error handling in application.                   |
| `ScriptedRestParkingValidationUtils`   | Validation methods for Parking table apis.                          |
| `ScriptedRestParkingUtils`             | Methods to handle logic after validation in api for Parking table.  |
| `ScriptedRestParkingRequestUtil`       | Validation methods for Request table apis.                          |
| `ScriptedRestParkingRequestValidation` | Methods to handle logic after validation in api for Requests table. |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Business Rules</summary>

<p>There is only one business rule related to this application. It's purpose is to validate data when submiting parking request before it is submited into DB form core UI. This was created using serviceNow SDK (more thoughs about SDK in summary about what I learned.) so you can update it, but you will need to fetch app into your locacl machine or you can re-do logic into inside BR.</p>

| Name                               | Description                                                                                           |
| :--------------------------------- | :---------------------------------------------------------------------------------------------------- |
| `Validate fields before submiting` | Busines rule created using SDK. Meant to validate data for request submission before submiting to DB. |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Client Scripts</summary>

<p>There are 3 client scripts related to Parking application. You can update them, they are not locked. Feel free to adjust them. List is bellow:</p>

| Name                          | Description                                                                      |
| :---------------------------- | :------------------------------------------------------------------------------- |
| `reservation_date validation` | Meant to validate date on change, before submiting parking request.              |
| `parking_name validation`     | Meant to validate parking selection on change, before submiting parking request. |
| `plate_number validation`     | Meant to validate plate number on change, before submiting parking request.      |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Flows</summary>

<p>There is one flow related to Parking application. Main purpose is to approve or reject parking request after submission, deppending if there is allready reservation or parking is full for particular day. You can adjust it change it. Runs everytime when new parking request is created.</p>

| Name                             | Description                                                       |
| :------------------------------- | :---------------------------------------------------------------- |
| `Parking app: state calculation` | Meant to automate logic with approving or rejecting reservations. |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Notifications</summary>
 <br /> 
![email-exm](https://github.com/user-attachments/assets/4032a4bb-b812-4208-ad3d-4e8bca400929)

<p>There is a scheduled job <b>Triger parking notification daily</b> which is running daily and triggers custom <b>"x_1230661_parking.upcoming.parking</b> event every day for requests which are 1 day prior before reservation date. It trrigers then notification and sends email to requestor reminding that there is parking upcoming. You can chnage/update data/design in it. Additionaly there is parking template and layout created whre you can do changes to.</p>

| Name                                     | Description                                             |
| :--------------------------------------- | :------------------------------------------------------ |
| `Parking notification: Upcoming parking` | Meant to reming requestor that reservation is upcoming. |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">Portal</summary>
<br/>
![Parking portal home](https://github.com/user-attachments/assets/6fca1ef0-6ce2-4a9d-a857-c322c1e47287)
 <br/> 
![Reservation widget](https://github.com/user-attachments/assets/aca0af5e-300a-401e-a597-8c0f7bd16116)
 <br /> 
![My reservations page](https://github.com/user-attachments/assets/67ae498e-0451-48c6-bdfa-55240caf1b6c)

<p>There is custom portal created <b>Parking portal</b>. You can update it change it. Details bellow:</p>

Pages:

| Name               | Description                                                                                                                                                                                             |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `home `            | Home page for parking portal. Suffix /parking. Can create new request, see upcoming next request, check available places by specific date or cancel upcoming next reservation.                          |
| `my_reservations ` | My reservations page where you can see your requests by filtered status. Can see only from current date. You can cancel your requests here also. Suffix /parking/id=my_reservations&sysId={user sys id} |

<p>Additionaly there is custom theme cretaed with navigation so you can control color scheme form there.</p>

Widgets:

| Name                      | Description                                         |
| :------------------------ | :-------------------------------------------------- |
| `Parking menu widget `    | Custom menu for parking portal.                     |
| `Parking app header `     | Custom header for parking portal.                   |
| `My reservations `        | Widget to show current user reservations.           |
| `parking_reservations `   | Home page widget to show next uppcoming rservation. |
| `Chek available parking ` | Home page widget to check available parkings.       |
| `requestForm `            | Form for new request modal.                         |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">
ACL's
</summary>

<p>There are custom Acls created for this applciation. Most of these are for the in the beggining mentioned roles. Delete mostly for application admin. Also for parking list it is allowed to update for application admin. Additionaly to execute api, it is added t addtional acl. List bellow:</p>

| Name                                       |
| :----------------------------------------- |
| `x_1230661_parking_requests (create)`      |
| `x_1230661_parking_requests (read)`        |
| `x_1230661_parking_requests (write)`       |
| `x_1230661_parking_requests (delete)`      |
| `x_1230661_parking_list (delete)`          |
| `x_1230661_parking_list (read)`            |
| `x_1230661_parking_list (write)`           |
| `x_1230661_parking_list (create)`          |
| `Scripted Rest for Parking Apis (execute)` |

</details>

<details>
<summary style="color: #e1b91d; font-size: 21px">
UI Builder
</summary>

![UIB home](https://github.com/user-attachments/assets/15809fc2-5934-4858-adc4-75359f1084c1)
![UIB requests](https://github.com/user-attachments/assets/dc51262c-e066-40f5-b30c-2f152c21dde4)

<p>There are couple pages created in core UI for admin to managed Parkings and requests on them. It has rather limited options now, but it can be improved in future. You can update it. You can access it ALL - Parking Application - Parking Admin. Pages bellow:</p>

| Name                | Description                                                                                                                                                                                       |
| :------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `home `             | Suffix /parking-app/home. Can view all parkings. See they're total info. Update them.                                                                                                             |
| `parking_requests ` | Suffix /x/1230661/parking-app/parking_requests/{sysId reservation}. Can view all or filter reservations by specific date. Update data on them from list view. See avilable count for current day. |

</details>

<br/>

# What I learned

- Good things from SDK: It is nice improvement for developer experience and to what we can do in serviceNow. From a development side it is way more ejoible to write code in prefered IDE. Additionaly you can utilize TS and additional external librarys for better results. It really improves code quality and and can help to avoid stupid bugs/issues. Plus it is easy to set up. Additionaly can be set up with external version control system or it is easy to maintain and push changes to SN system.
- Possible issues with SDK:

1. It requires better level of knowlege in programing so more is expected from developer. Basicaly it is not a tool for citizen developer or other type users, who interact with SN as like low/no code environment. You need to have good knowledge in JS. Additionaly if you want unitilize all SDK benefits, also knowledge in TS.
2. It requires better oversight of the packages which might be utilize, because as it might help overal, but wihout any checks, it might get out of control as every developer might decide to add on library that he wants, but not nececerly needs.
3. It would be best before creating application, to decide, what appraoch to take developing it, if we go with SDK full, or SN code full, because if you gona write code in two diffrent places, it will be realy hard to maintain, update and track (Yeap, I did it now my self, so thats why I know :D).
4. SDK does not support client scripts for now, only server side. So a bit sad, because by default you will need to split your code in two places depending if you write server or client.

- Studio and version control with Github. For me, durring the whole process it was one of the best things, that allowed me to work on my app even with bigger gaps in time. It is easy to set up, easy to work with. Would reccomend everyone to utilize this.
- UI Builder. Even tho it got some upgrades, still for me, when it comes to full customizations and custom page creation, there are alot of limitations or places, where we can't go arround wihout re-configuration. In some cases, it is even way to hard to find documentation or to be more specific, usefull documentation about already existing OOB widgets. Then you need to spend hours sometimes just to figure our, how to bent widget into your needs. None the less, it is good to see improvements, related to UI changes, better tracking of available client sciprts/States/Data resources. This time I did not use custom widget creation, but in future I might. Just that it is realy hard to set up, and you can't do that for free as with SDK (which is also why I like SDK more).
- SN-utils. Just wanted to mention, that it was realy good experience where I tried to untilize more functionality from SN utils. For me it was good experience trying to navigate with it, learning new commands like /UIB, /SI, /BR ,adding queris to it and many more. It realy helps to save time when you learn those :D.
- Flow designer. It is realy usefull when you need to automate some kind of simple process. But it grows realy big and becomes hard to navigate, when logic becomes complexe and big, in which case I would rather use code approach than half-half with Flow designer, because for me then it is easier to maintain and debug.
- System properties. In this application you will not find any sys properties created from Parking app, as in this case I took another appraoch creating constans in code. For me it is better way to store global variables and control my code whne it comes to server side code. One thing that I would improve here is that I would create seperate script include with all Constants related to application. In that way, it would be even esier to maintain it. Additionaly from the way it is set up, I think you can see that this application is not meant to be updated or re-confidured by low/no code approach. That is because I saw how it becomes hard to maintain/update/change logic when it becomes seperated to support this functionality. Thats why I kept it more simple on developer side(can't say that my code is very readible but at least it is all in code :D).
