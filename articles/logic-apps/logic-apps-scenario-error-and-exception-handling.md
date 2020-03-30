---
title: Scenariusz rejestrowania błędów obsługi wyjątków &
description: Rzeczywisty przypadek użycia i scenariusz zaawansowanej obsługi wyjątków i rejestrowania błędów w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 1bb6e28c9dcae01f3233178706d2a24156fa509a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76902706"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenariusz: Obsługa wyjątków i rejestrowanie błędów dla aplikacji logiki

W tym scenariuszu opisano, jak można rozszerzyć aplikację logiki do lepszej obsługi wyjątków. Użyliśmy rzeczywistego przypadku użycia, aby odpowiedzieć na pytanie: "Czy usługa Azure Logic Apps obsługuje obsługę wyjątków i błędów w usłudze Azure Logic Apps?"

> [!NOTE]
> Bieżący schemat usługi Azure Logic Apps zawiera standardowy szablon dla odpowiedzi na działania. Ten szablon zawiera zarówno wewnętrzne sprawdzanie poprawności i odpowiedzi na błędy zwrócone z aplikacji interfejsu API.

## <a name="scenario-and-use-case-overview"></a>Omówienie scenariuszy i przypadków użycia

Oto historia jako przypadek użycia dla tego scenariusza: 

Znana organizacja opieki zdrowotnej zaangażowała nas do opracowania rozwiązania platformy Azure, które stworzyłoby portal dla pacjentów przy użyciu programu Microsoft Dynamics CRM Online. Musieli wysłać rekordy terminów między portalem pacjentów Dynamics CRM Online a Salesforce. Poproszono nas o zastosowanie standardu [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) dla wszystkich rejestrów pacjentów.

Projekt miał dwa główne wymagania:  

* Metoda rejestrowania rekordów wysyłanych z portalu Dynamics CRM Online
* Sposób wyświetlania błędów, które wystąpiły w przepływie pracy

> [!TIP]
> Aby uzyskać film wideo wysokiego poziomu o tym projekcie, zobacz [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Grupa użytkowników integracji").

## <a name="how-we-solved-the-problem"></a>Jak rozwiązać problem

Wybraliśmy [usługę Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") jako repozytorium rekordów dziennika i błędów (usługa Cosmos DB odwołuje się do rekordów jako dokumentów). Ponieważ usługa Azure Logic Apps ma standardowy szablon dla wszystkich odpowiedzi, nie trzeba tworzyć schematu niestandardowego. Możemy utworzyć aplikację interfejsu API do **wstawiania** i **kwerendy** dla rekordów błędów i dzienników. Możemy również zdefiniować schemat dla każdego w aplikacji interfejsu API.  

Innym wymogiem było przeczyszczenie rekordów po określonej dacie. Usługa Cosmos DB ma właściwość o nazwie [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Czas na życie") (TTL), która pozwoliła nam ustawić wartość czas na **aktywne** dla każdego rekordu lub kolekcji. Ta funkcja wyeliminowała konieczność ręcznego usuwania rekordów w usłudze Cosmos DB.

> [!IMPORTANT]
> Aby ukończyć ten samouczek, należy utworzyć bazę danych usługi Cosmos DB i dwie kolekcje (rejestrowanie i błędy).

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Pierwszym krokiem jest utworzenie aplikacji logiki i otworzyć aplikację w logic app designer. W tym przykładzie używamy aplikacji logiki nadrzędny podrzędny. Załóżmy, że utworzyliśmy już nadrzędny i zamierzamy utworzyć jedną aplikację logiki podrzędnej.

Ponieważ mamy zamiar zarejestrować rekord wychodzi z Dynamics CRM Online, zacznijmy od góry. Musimy użyć **wyzwalacza żądania,** ponieważ nadrzędna aplikacja logiki wyzwala ten element podrzędny.

### <a name="logic-app-trigger"></a>Wyzwalacz aplikacji logiki

Używamy wyzwalacza **żądania,** jak pokazano w poniższym przykładzie:

``` json
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

```


## <a name="steps"></a>Kroki

Musimy zarejestrować źródło (żądanie) rekordu pacjenta z portalu Dynamics CRM Online.

1. Musimy uzyskać nowy rekord terminu z Dynamics CRM Online.

   Wyzwalacz pochodzący z programu CRM zapewnia nam **kod CRM PatentId,** **typ rekordu,** **nowy lub zaktualizowany rekord** (nowa lub zaktualizowana wartość logiczna) i **SalesforceId.** **SalesforceId** może mieć wartość null, ponieważ jest używany tylko do aktualizacji.
   Otrzymujemy rekord CRM za pomocą CRM **PatientID** i **typu rekordu**.

2. Następnie musimy dodać naszą usługę Azure Cosmos DB SQL API app **InsertLogEntry** operacji, jak pokazano w tym miejscu w Logic App Designer.

   **Wstawianie wpisu dziennika**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Wstaw wpis błędu**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Sprawdzanie, czy nie ma błędu tworzenia rekordu**

   ![Warunek](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Kod źródłowy aplikacji logiki

> [!NOTE]
> Poniższe przykłady są tylko próbki. Ponieważ ten samouczek jest oparty na implementacji teraz w produkcji, wartość **węzła źródłowego** może nie wyświetlać właściwości, które są związane z planowaniem terminu.> 

### <a name="logging"></a>Rejestrowanie

Poniższy przykład kodu aplikacji logiki pokazuje, jak obsługiwać rejestrowanie.

#### <a name="log-entry"></a>Wpis dziennika

Oto kod źródłowy aplikacji logiki do wstawiania wpisu dziennika.

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

Oto komunikat odpowiedzi dziennika z aplikacji interfejsu API.

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

Teraz przyjrzyjmy się krokom obsługi błędów.

### <a name="error-handling"></a>Obsługa błędów

Poniższy przykład kodu aplikacji logiki pokazuje, jak można zaimplementować obsługę błędów.

#### <a name="create-error-record"></a>Utwórz rekord błędu

Oto kod źródłowy aplikacji logiki do tworzenia rekordu błędu.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Wstaw błąd do usługi Cosmos DB - żądanie

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

#### <a name="insert-error-into-cosmos-db--response"></a>Wstaw błąd do usługi Cosmos DB - odpowiedź

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

#### <a name="salesforce-error-response"></a>Odpowiedź na błąd salesforce

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Zwracanie odpowiedzi z powrotem do nadrzędnej aplikacji logiki

Po otrzymasz odpowiedź, można przekazać odpowiedź z powrotem do nadrzędnej aplikacji logiki.

#### <a name="return-success-response-to-parent-logic-app"></a>Zwracanie odpowiedzi na sukces do aplikacji logiki nadrzędnej

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

#### <a name="return-error-response-to-parent-logic-app"></a>Zwracanie odpowiedzi na błąd do nadrzędnej aplikacji logiki

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


## <a name="cosmos-db-repository-and-portal"></a>Repozytorium i portal Cosmos DB

Nasze rozwiązanie dodało możliwości dzięki [usłudze Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db)

### <a name="error-management-portal"></a>Portal zarządzania błędami

Aby wyświetlić błędy, można utworzyć aplikację sieci web MVC, aby wyświetlić rekordy błędów z usługi Cosmos DB. Operacje **Lista**, **Szczegóły,** **Edycja**i **Usuń** są uwzględnione w bieżącej wersji.

> [!NOTE]
> Operacja edycji: Usługa Cosmos DB zastępuje cały dokument. Rekordy wyświetlane w widokach **Lista** i **Szczegóły** są tylko próbkami. Nie są one rzeczywistymi zapisami wizyt pacjenta.

Oto przykłady naszych szczegółów aplikacji MVC utworzonych przy tak opisanym wcześniej podejściu.

#### <a name="error-management-list"></a>Lista zarządzania błędami
![Lista błędów](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Widok szczegółów zarządzania błędami
![Szczegóły błędu](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal zarządzania dziennikami

Aby wyświetlić dzienniki, utworzyliśmy również aplikację internetową MVC. Oto przykłady naszych szczegółów aplikacji MVC utworzonych przy tak opisanym wcześniej podejściu.

#### <a name="sample-log-detail-view"></a>Przykładowy widok szczegółów dziennika
![Widok szczegółów dziennika](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Szczegóły aplikacji interfejsu API

#### <a name="logic-apps-exception-management-api"></a>Interfejs API zarządzania wyjątkami aplikacji logiki

Nasza aplikacja interfejsu API zarządzania wyjątkami azure logic apps typu open source zapewnia funkcje opisane w tym miejscu — istnieją dwa kontrolery:

* **ErrorController** wstawia rekord błędu (dokument) w kolekcji usługi Azure Cosmos DB.
* **Kontroler dziennika** Wstawia rekord dziennika (dokument) w kolekcji usługi Azure Cosmos DB.

> [!TIP]
> Oba kontrolery `async Task<dynamic>` używają operacji, umożliwiając operacje do rozwiązania w czasie wykonywania, dzięki czemu możemy utworzyć schemat usługi Azure Cosmos DB w treści operacji. 
> 

Każdy dokument w usłudze Azure Cosmos DB musi mieć unikatowy identyfikator. Używamy `PatientId` i dodajemy sygnaturę czasową, która jest konwertowana na wartość sygnatury czasowej Unix (double). Obcinamy wartość, aby usunąć wartość ułamkową.

Możesz wyświetlić kod źródłowy naszego interfejsu API kontrolera błędów z [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Nazywamy interfejs API z aplikacji logiki przy użyciu następującej składni:

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

Wyrażenie w poprzednim przykładzie kodu sprawdza *stan Create_NewPatientRecord* **nie powiodło się**.

## <a name="summary"></a>Podsumowanie

* Można łatwo zaimplementować rejestrowanie i obsługi błędów w aplikacji logiki.
* Usługi Azure Cosmos DB można używać jako repozytorium rekordów dziennika i błędów (dokumentów).
* Za pomocą MVC można utworzyć portal do wyświetlania rekordów dzienników i błędów.

### <a name="source-code"></a>Kod źródłowy

Kod źródłowy aplikacji interfejsu API zarządzania wyjątkami aplikacji Logic Apps jest dostępny w tym [repozytorium GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Interfejs API zarządzania wyjątkami aplikacji logiki").

## <a name="next-steps"></a>Następne kroki

* [Zobacz więcej przykładów i scenariuszy aplikacji logiki](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Monitorowanie aplikacji logiki](../logic-apps/monitor-logic-apps.md)
* [Tworzenie wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
