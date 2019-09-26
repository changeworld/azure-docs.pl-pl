---
title: Uwierzytelnianie wychodzące — usługa Azure Scheduler
description: Dowiedz się, jak skonfigurować lub usunąć uwierzytelnianie wychodzące dla usługi Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 2ea09330fb8d3d97da5fbc197dba9668f1a4f685
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300846"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Uwierzytelnianie wychodzące dla usługi Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która jest [wycofywana](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Aby kontynuować pracę z zadaniami skonfigurowanymi w usłudze Scheduler, [Przeprowadź migrację do Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) najszybciej, jak to możliwe.

Zadania usługi Azure Scheduler mogą wymagać wywołania usług, które wymagają uwierzytelniania, takich jak inne usługi platformy Azure, Salesforce.com, Facebook i Secure Custom websites. Wywołana usługa może określić, czy zadanie usługi Scheduler ma dostęp do żądanych zasobów. 

Usługa Scheduler obsługuje te modele uwierzytelniania: 

* Uwierzytelnianie *certyfikatu klienta* w przypadku korzystania z certyfikatów klienta SSL/TLS
* Uwierzytelnianie *podstawowe*
* *Active Directory uwierzytelniania OAuth*

## <a name="add-or-remove-authentication"></a>Dodaj lub Usuń uwierzytelnianie

* Aby dodać uwierzytelnianie do zadania usługi Scheduler, podczas tworzenia lub aktualizowania zadania Dodaj `authentication` element podrzędny JavaScript Object Notation (JSON) `request` do elementu. 

  Odpowiedzi nigdy nie zwracają wpisów tajnych, które są przesyłane do usługi Scheduler przez umieszczenie, poprawkę lub żądanie post w `authentication` obiekcie. 
  Odpowiedzi ustawiają informacje o kluczach tajnych na wartość null lub mogą korzystać z tokenu publicznego reprezentującego uwierzytelnioną jednostkę. 

* Aby usunąć uwierzytelnianie z zadania usługi Scheduler, jawnie Uruchom żądanie Put lub patch w zadaniu, a następnie ustaw `authentication` dla obiektu wartość null. Odpowiedź nie będzie zawierać żadnych właściwości uwierzytelniania.

## <a name="client-certificate"></a>Certyfikat klienta

### <a name="request-body---client-certificate"></a>Treść żądania — certyfikat klienta

Podczas dodawania uwierzytelniania przy użyciu `ClientCertificate` modelu należy określić te dodatkowe elementy w treści żądania.  

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania używany przez certyfikat klienta SSL |
| **type** | Tak | Typ uwierzytelniania. W przypadku certyfikatów klienta SSL wartość jest `ClientCertificate`. |
| **pfx** | Tak | Zawartość pliku PFX zakodowana algorytmem Base64 |
| **Hasło** | Tak | Hasło do uzyskiwania dostępu do pliku PFX |
||| 

### <a name="response-body---client-certificate"></a>Treść odpowiedzi — certyfikat klienta 

Gdy żądanie jest wysyłane z informacjami o uwierzytelnianiu, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis | 
|---------|-------------| 
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania używany przez certyfikat klienta SSL |
| **type** | Typ uwierzytelniania. W przypadku certyfikatów klienta SSL wartość jest `ClientCertificate`. |
| **certificateThumbprint** |Odcisk palca certyfikatu |
| **certificateSubjectName** |Nazwa wyróżniająca podmiotu certyfikatu |
| **certificateExpiration** | Data wygaśnięcia certyfikatu |
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

## <a name="basic"></a>Podstawowa

### <a name="request-body---basic"></a>Treść żądania — podstawowa

Podczas dodawania uwierzytelniania przy użyciu `Basic` modelu należy określić te dodatkowe elementy w treści żądania.

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania używany do uwierzytelniania podstawowego | 
| **type** | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania podstawowego wartość jest `Basic`równa. | 
| **Nazwa użytkownika** | Tak | Nazwa użytkownika do uwierzytelnienia | 
| **Hasło** | Tak | Hasło do uwierzytelnienia |
|||| 

### <a name="response-body---basic"></a>Treść odpowiedzi — podstawowa

Gdy żądanie jest wysyłane z informacjami o uwierzytelnianiu, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis | 
|---------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania używany do uwierzytelniania podstawowego |
| **type** | Typ uwierzytelniania. W przypadku uwierzytelniania podstawowego wartość jest `Basic`równa. |
| **Nazwa użytkownika** | Uwierzytelniona nazwa użytkownika |
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

### <a name="sample-rest-response---basic"></a>Przykładowa odpowiedź REST — podstawowa

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

## <a name="active-directory-oauth"></a>Uwierzytelnianie OAuth usługi Active Directory

### <a name="request-body---active-directory-oauth"></a>Treść żądania — Active Directory uwierzytelniania OAuth 

Podczas dodawania uwierzytelniania przy użyciu `ActiveDirectoryOAuth` modelu należy określić te dodatkowe elementy w treści żądania.

| Element | Wymagany | Opis |
|---------|----------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Tak | Obiekt uwierzytelniania używany do uwierzytelniania ActiveDirectoryOAuth |
| **type** | Tak | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość jest `ActiveDirectoryOAuth`równa. |
| **dzierżaw** | Tak | Identyfikator dzierżawy dzierżawy usługi Azure AD. Aby znaleźć identyfikator dzierżawy dzierżawy usługi Azure AD, uruchom `Get-AzureAccount` polecenie w Azure PowerShell. |
| **audience** | Tak | Ta wartość jest ustawiona na `https://management.core.windows.net/`. | 
| **clientId** | Tak | Identyfikator klienta aplikacji usługi Azure AD | 
| **wpisu** | Tak | Wpis tajny klienta żądającego tokenu | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Treść odpowiedzi — Active Directory uwierzytelniania OAuth

Gdy żądanie jest wysyłane z informacjami o uwierzytelnianiu, odpowiedź zawiera te elementy uwierzytelniania.

| Element | Opis |
|---------|-------------|
| **uwierzytelnianie** (element nadrzędny) | Obiekt uwierzytelniania używany do uwierzytelniania ActiveDirectoryOAuth |
| **type** | Typ uwierzytelniania. W przypadku uwierzytelniania ActiveDirectoryOAuth wartość jest `ActiveDirectoryOAuth`równa. | 
| **dzierżaw** | Identyfikator dzierżawy dla dzierżawy usługi Azure AD |
| **audience** | Ta wartość jest ustawiona na `https://management.core.windows.net/`. |
| **clientId** | Identyfikator klienta aplikacji usługi Azure AD |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Przykładowe żądanie REST — Active Directory uwierzytelniania OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>Przykładowa odpowiedź REST — Active Directory uwierzytelniania OAuth

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

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Interfejs API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
