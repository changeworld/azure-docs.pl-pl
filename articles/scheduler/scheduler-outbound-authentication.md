---
title: Uwierzytelnianie danych wychodzących
description: Dowiedz się, jak skonfigurować lub usunąć uwierzytelnianie wychodzące dla harmonogramu platformy Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: bcd14e618323aec1c7ce47fcebb25099fa96be81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898510"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Uwierzytelnianie wychodzące dla harmonogramu platformy Azure

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

Zadania harmonogramu platformy Azure mogą wymagać wywoływania usług wymagających uwierzytelniania, takich jak inne usługi platformy Azure, Salesforce.com, Facebook i bezpiecznych niestandardowych witryn sieci Web. Wywoływana usługa może określić, czy zadanie harmonogramu może uzyskać dostęp do żądanych zasobów. 

Harmonogram obsługuje następujące modele uwierzytelniania: 

* Uwierzytelnianie *certyfikatu klienta* podczas korzystania z certyfikatów klienta SSL/TLS
* *Uwierzytelnianie podstawowe*
* *Uwierzytelnianie OAuth usługi Active Directory*

## <a name="add-or-remove-authentication"></a>Dodawanie lub usuwanie uwierzytelniania

* Aby dodać uwierzytelnianie do zadania harmonogramu, podczas tworzenia `authentication` lub aktualizowania zadania należy dodać element `request` podrzędny notacji obiektu JavaScript (JSON) do elementu. 

  Odpowiedzi nigdy nie zwracają wpisów tajnych, które są przekazywane do usługi `authentication` harmonogramu za pośrednictwem put, patch lub post żądania w obiekcie. 
  Odpowiedzi ustawić tajne informacje na wartość null lub może używać tokenu publicznego, który reprezentuje uwierzytelnioną jednostkę. 

* Aby usunąć uwierzytelnianie z zadania harmonogramu, jawnie uruchom żądanie PUT `authentication` lub PATCH w zadaniu i ustaw obiekt na wartość null. Odpowiedź nie będzie zawierać żadnych właściwości uwierzytelniania.

## <a name="client-certificate"></a>Certyfikat klienta

### <a name="request-body---client-certificate"></a>Treść żądania — certyfikat klienta

Podczas dodawania `ClientCertificate` uwierzytelniania przy użyciu modelu, należy określić te dodatkowe elementy w treści żądania.  

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania przy użyciu certyfikatu klienta SSL |
| **Typu** | Tak | Typ uwierzytelniania. W przypadku certyfikatów klientów SSL wartość to `ClientCertificate`. |
| **Pfx** | Tak | Zakodowana w base64 zawartość pliku PFX |
| **hasło** | Tak | Hasło dostępu do pliku PFX |
||| 

### <a name="response-body---client-certificate"></a>Treść odpowiedzi — certyfikat klienta 

Gdy żądanie jest wysyłane z informacjami uwierzytelniania, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis | 
|---------|-------------| 
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania przy użyciu certyfikatu klienta SSL |
| **Typu** | Typ uwierzytelniania. W przypadku certyfikatów klientów SSL wartość to `ClientCertificate`. |
| **certificateThumbprint** |Odcisk palca certyfikatu |
| **certificateSubjectName** |Nazwa wyróżniająca podmiotu certyfikatu |
| **certyfikatExpiration** | Data ważności certyfikatu |
||| 

### <a name="sample-rest-request---client-certificate"></a>Przykładowe żądanie REST — certyfikat klienta

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Przykładowa odpowiedź REST — certyfikat klienta

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Podstawowa (Basic)

### <a name="request-body---basic"></a>Treść żądania — podstawowa

Podczas dodawania `Basic` uwierzytelniania przy użyciu modelu, należy określić te dodatkowe elementy w treści żądania.

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania do korzystania z uwierzytelniania podstawowego | 
| **Typu** | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania `Basic`podstawowego wartość to . | 
| **Nazwę użytkownika** | Tak | Nazwa użytkownika do uwierzytelnienia | 
| **hasło** | Tak | Hasło do uwierzytelnienia |
|||| 

### <a name="response-body---basic"></a>Organ odpowiedzi - Podstawowy

Gdy żądanie jest wysyłane z informacjami uwierzytelniania, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis | 
|---------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania do korzystania z uwierzytelniania podstawowego |
| **Typu** | Typ uwierzytelniania. W przypadku uwierzytelniania `Basic`podstawowego wartość to . |
| **Nazwę użytkownika** | Uwierzytelniona nazwa użytkownika |
||| 

### <a name="sample-rest-request---basic"></a>Przykładowe żądanie REST — podstawowe

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Przykładowa odpowiedź REST - Podstawowa

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Treść żądania — OAuth usługi Active Directory 

Podczas dodawania `ActiveDirectoryOAuth` uwierzytelniania przy użyciu modelu, należy określić te dodatkowe elementy w treści żądania.

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Tak | Obiekt uwierzytelniania do korzystania z uwierzytelniania ActiveDirectoryOAuth |
| **Typu** | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość to `ActiveDirectoryOAuth`. |
| **Dzierżawy** | Tak | Identyfikator dzierżawy dzierżawy dzierżawy usługi Azure AD. Aby znaleźć identyfikator dzierżawy dzierżawy dzierżawy usługi Azure AD, uruchom `Get-AzureAccount` w programie Azure PowerShell. |
| **Publiczności** | Tak | Ta wartość jest `https://management.core.windows.net/`ustawiona na . | 
| **Clientid** | Tak | Identyfikator klienta dla aplikacji usługi Azure AD | 
| **Tajne** | Tak | Klucz tajny dla klienta, który żąda tokenu | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Treść odpowiedzi - OAuth usługi Active Directory

Gdy żądanie jest wysyłane z informacjami uwierzytelniania, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis |
|---------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania do korzystania z uwierzytelniania ActiveDirectoryOAuth |
| **Typu** | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość to `ActiveDirectoryOAuth`. | 
| **Dzierżawy** | Identyfikator dzierżawy dzierżawy dzierżawy usługi Azure AD |
| **Publiczności** | Ta wartość jest `https://management.core.windows.net/`ustawiona na . |
| **Clientid** | Identyfikator klienta dla aplikacji usługi Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Przykładowe żądanie REST — OAuth usługi Active Directory

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Przykładowa odpowiedź REST — OAuth usługi Active Directory

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)