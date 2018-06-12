---
title: Obsługa wyjątków i błędów rejestrowania scenariusz — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje przypadek użycia rzeczywistych o zaawansowanych wyjątków i rejestrowania błędów dla usługi Azure Logic Apps
keywords: ''
services: logic-apps
author: hedidin
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: cb80423266d3e9c0b3cac31821965ad92c0420d9
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299362"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenariusz: Obsługa wyjątków i rejestrowania błędów dla usługi logic apps

W tym scenariuszu opisano, jak można rozszerzyć aplikację logiki, aby lepiej obsługi wyjątków. Odpowiedzi na pytanie użyliśmy przypadek użycia rzeczywistych: "Aplikacje logiki platformy Azure obsługuje wyjątek i obsługa błędów?"

> [!NOTE]
> Bieżący schemat Azure Logic Apps udostępnia standardowy szablon dla akcji odpowiedzi. Ten szablon zawiera wewnętrznego sprawdzania poprawności i odpowiedzi na błędy zwrócone przez aplikację interfejsu API.

## <a name="scenario-and-use-case-overview"></a>Omówienie scenariusza i użyj case

Oto wątku jako przypadek użycia dla tego scenariusza: 

Dobrze znane organizacji opieki zdrowotnej zaangażowane w tworzenie Azure rozwiązania, które mogą utworzyć pacjenta portalu przy użyciu programu Microsoft Dynamics CRM Online. One potrzebne do wysyłania rekordów termin Dynamics CRM Online pacjenta portalu i usługi Salesforce. Firma Microsoft zostały poproszony [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standardowe dla wszystkich pacjenta rekordów.

Projekt ma dwa główne wymagania:  

* Metoda pod kątem rejestrowania rekordów wysyłane z portalu usługi Dynamics CRM Online
* Można wyświetlić wszelkie błędy, które wystąpiły w przepływie pracy

> [!TIP]
> Film wysokiego poziomu dotyczących tego projektu, zobacz [grupy użytkowników integracji](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "grupy użytkowników integracji").

## <a name="how-we-solved-the-problem"></a>Jak możemy rozwiązuje problem

Wybraliśmy [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/ "bazy danych Azure rozwiązania Cosmos") jako repozytorium dla rekordów dziennika i błąd (DB rozwiązania Cosmos odwołuje się do rekordów jako dokumentów). Ponieważ aplikacje logiki platformy Azure ma standardowy szablon wszystkie odpowiedzi, nie mamy utworzyć schematu niestandardowego. Można utworzyć aplikację interfejsu API do **Wstaw** i **zapytania** rekordów zarówno błędu, jak i dziennika. Również definiowania schematu dla każdego z nich w aplikacji interfejsu API.  

Innym wymogiem było przeczyścić rekordów po określonej dacie. Rozwiązania cosmos bazy danych ma właściwość o nazwie [czas wygaśnięcia](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "czas wygaśnięcia") (TTL), które mogą nam można ustawić **czas wygaśnięcia** wartość dla każdego rekordu lub kolekcji. Ta funkcja została wyeliminowana trzeba ręcznie usunąć rekordy w bazie danych rozwiązania Cosmos.

> [!IMPORTANT]
> Do ukończenia tego samouczka, należy utworzyć bazę danych DB rozwiązania Cosmos i dwie kolekcje (rejestrowania i błędów).

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Pierwszym krokiem jest utworzenie aplikacji logiki i Otwórz w Projektancie aplikacji logiki aplikacji. W tym przykładzie użyto aplikacje logiki nadrzędny podrzędny. Załóżmy, że firma Microsoft utworzono już element nadrzędny, a aby utworzyć jedną aplikację logiki podrzędnych.

Ponieważ zamierzamy rekord wystawała Dynamics CRM Online dziennika, Zacznijmy u góry. Możemy użyć **żądania** wyzwolenia ponieważ aplikacji logiki nadrzędnego wyzwala tego dziecka.

### <a name="logic-app-trigger"></a>Wyzwalacz aplikacji logiki

Używamy **żądania** wyzwalania, jak pokazano w poniższym przykładzie:

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


## <a name="steps"></a>Kroki

Firma Microsoft muszą się logować źródła (żądanie) pacjenta rekordu z portalu usługi Dynamics CRM Online.

1. Firma Microsoft musi uzyskać nowy rekord terminu z programu Dynamics CRM Online.

   Wyzwalacz pochodzące z CRM zapewnia nam z **CRM PatentId**, **typu rekordu**, **nowe lub zaktualizowane rekordu** (nowej lub zaktualizuj wartość logiczna), i **SalesforceId**. **SalesforceId** może mieć wartości null, ponieważ jest ona używana tylko dla aktualizacji.
   Uzyskujemy rekordu CRM przy użyciu programu CRM **PatientID** i **typu rekordu**.

2. Następnie należy dodać aplikacji interfejsu API Azure rozwiązania Cosmos bazy danych SQL **InsertLogEntry** operacji, jak pokazano w Projektancie aplikacji logiki.

   **Wstaw wpis dziennika**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Wstaw wpis błędu**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sprawdź, czy utworzyć rekord błędu**

   ![Warunek](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Kod źródłowy aplikacji logiki

> [!NOTE]
> Poniższe przykłady są tylko próbek. Ponieważ w tym samouczku jest oparty na implementacji w środowisku produkcyjnym, wartość **węzeł źródłowy** może nie wyświetlać właściwości, które są związane z planowaniem terminu. > 

### <a name="logging"></a>Rejestrowanie

Poniższy przykład kodu aplikacji logiki pokazuje, jak do obsługi rejestrowania.

#### <a name="log-entry"></a>Wpis dziennika

Oto kod źródłowy aplikacji logiki Wstawianie wpis dziennika.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Żądanie dziennika

Oto komunikat żądania dziennika opublikowane w aplikacji interfejsu API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Odpowiedź dziennika

Oto dziennika komunikat odpowiedzi z aplikacji interfejsu API.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Teraz Przyjrzyjmy się kroków obsługi błędów.

### <a name="error-handling"></a>Obsługa błędów

Poniższy przykład kodu aplikacji logiki pokazuje, jak można zaimplementować obsługi błędów.

#### <a name="create-error-record"></a>Utwórz rekord błędu

Oto kod źródłowy aplikacji logiki do tworzenia rekord błędu.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Wstaw błędu w bazie danych rozwiązania Cosmos--żądania

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Wstaw błędu w bazie danych rozwiązania Cosmos--odpowiedzi

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Odpowiedzi na błąd SalesForce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Zwraca odpowiedź z powrotem do aplikacji logiki nadrzędnego

Po uzyskaniu odpowiedzi, należy przekazać odpowiedź z powrotem do aplikacji logiki nadrzędnej.

#### <a name="return-success-response-to-parent-logic-app"></a>Powodzenie odpowiedź zwrócona do aplikacji logiki nadrzędnego

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Błąd odpowiedź zwrócona do aplikacji logiki nadrzędnego

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Repozytorium rozwiązania cosmos bazy danych i portal

Nasze rozwiązanie dodane możliwości za pomocą [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Błąd portalu zarządzania

Aby wyświetlić błędy, można utworzyć aplikacji sieci web MVC, aby wyświetlić błąd rekordy z bazy danych rozwiązania Cosmos. **Listy**, **szczegóły**, **Edytuj**, i **usunąć** operacji znajdują się w bieżącej wersji.

> [!NOTE]
> Operacji edycji: rozwiązania Cosmos DB zamienia cały dokument. Rejestruje pokazano **listy** i **szczegółów** widoki są tylko próbek. Nie są one rzeczywiste termin pacjenta rekordów.

Poniżej przedstawiono przykłady naszych szczegóły aplikacji MVC utworzone za pomocą metody opisany wcześniej.

#### <a name="error-management-list"></a>Błąd listy zarządzania.
![Lista błędów](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Błąd: widok szczegółów zarządzania
![Szczegóły błędu](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal zarządzania dziennika

Aby wyświetlić dzienniki, również utworzono aplikację sieci web MVC. Poniżej przedstawiono przykłady naszych szczegóły aplikacji MVC utworzone za pomocą metody opisany wcześniej.

#### <a name="sample-log-detail-view"></a>Przykładowy widok szczegółów dziennika
![Dziennik: widok szczegółów](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Szczegóły aplikacji interfejsu API

#### <a name="logic-apps-exception-management-api"></a>Zarządzanie wyjątkami logiki aplikacji interfejsu API

Open source aplikacji interfejsu API zarządzania wyjątek usługi Azure Logic Apps udostępnia funkcje, zgodnie z opisem w tym miejscu — istnieją dwa kontrolery:

* **ErrorController** wstawia rekord błędu (dokument) w kolekcji usługi Azure DB rozwiązania Cosmos.
* **LogController** wstawia rekordu dziennika (dokument) w kolekcji usługi Azure DB rozwiązania Cosmos.

> [!TIP]
> Zarówno kontrolery `async Task<dynamic>` czynności operacji rozwiązywać w czasie wykonywania, dlatego utworzymy schematu bazy danych Azure rozwiązania Cosmos w treści operacji. 
> 

Każdy dokument w usłudze Azure DB rozwiązania Cosmos musi mieć unikatowy identyfikator. Używamy `PatientId` i Dodawanie znaczników czasu, który jest konwertowany na wartość sygnatury czasowej systemu Unix (o podwójnej precyzji). Firma Microsoft obciąć wartość na usuwanie ułamkowa wartość.

Można wyświetlić kodu źródłowego kontrolera błąd interfejsu API [z usługi GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Nazywamy interfejsu API z aplikacji logiki przy użyciu następującej składni:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Sprawdza, czy wyrażenie w poprzednim przykładzie kodu *Create_NewPatientRecord* stan.

## <a name="summary"></a>Podsumowanie

* Można łatwo zaimplementować rejestrowania i obsługi błędów w aplikacji logiki.
* Bazy danych rozwiązania Cosmos Azure można użyć jako repozytorium rekordów dziennika i błąd (dokumentów).
* MVC umożliwia tworzenie portalu do wyświetlania rekordów dziennika i błędów.

### <a name="source-code"></a>Kod źródłowy

Kod źródłowy Zarządzanie wyjątkami aplikacje logiki aplikacji interfejsu API jest dostępna w tym [repozytorium GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API zarządzania wyjątków aplikacji logiki").

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetl więcej logiki aplikacji przykłady i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Więcej informacji na temat monitorowania aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Tworzenie szablonów automatycznego wdrażania dla usługi logic apps](../logic-apps/logic-apps-create-deploy-template.md)
