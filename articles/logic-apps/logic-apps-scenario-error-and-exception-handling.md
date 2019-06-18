---
title: Obsługa wyjątków & Błąd rejestrowania scenariusz — Azure Logic Apps | Dokumentacja firmy Microsoft
description: W tym miejscu jest przypadkiem użycia w rzeczywistości o obsługę zaawansowanych wyjątków i rejestrowania błędów w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: b-hoedid
ms.reviewer: estfan, LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 58e59e4faa135e24124f494d90437b49caa30129
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60599508"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenariusz: Obsługa wyjątków i rejestrowania błędów dla usługi logic apps

W tym scenariuszu opisano, jak rozszerzyć aplikację logiki w celu lepszej obsługi wyjątków. Przypadek użycia rzeczywistych była używana do odpowiedzi na pytanie: "Azure Logic Apps obsługuje wyjątek i obsługa błędów?"

> [!NOTE]
> Bieżący schemat usługi Azure Logic Apps udostępnia standardowego szablonu dla akcji odpowiedzi. Ten szablon obejmuje zarówno w weryfikacji wewnętrznych, jak i w odpowiedzi na błędy zwrócone przez aplikację interfejsu API.

## <a name="scenario-and-use-case-overview"></a>Scenariusz i użyj przypadków — omówienie

Poniżej przedstawiono historię jako przypadek użycia, w tym scenariuszu: 

Dobrze znane organizacji opieki zdrowotnej zaangażowanych w tworzeniu rozwiązania platformy Azure, które mogą utworzyć portal dla pacjentów przy użyciu programu Microsoft Dynamics CRM Online. One potrzebne do wysyłania terminu rekordów usługi Dynamics CRM Online stanowi portal dla pacjentów i Salesforce. Firma Microsoft była wyświetlony monit o użycie [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) standardowe dla wszystkich kartoteki.

Projekt miał dwóch najważniejszych wymagań:  

* Metody do rejestrowania rekordów wysyłane z portalu usługi Dynamics CRM Online
* Sposób, aby wyświetlić wszystkie błędy, które wystąpiły w ramach przepływu pracy

> [!TIP]
> Wysokiego poziomu wideo dotyczące tego projektu, zobacz [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integration User Group").

## <a name="how-we-solved-the-problem"></a>Jak możemy rozwiązać problem

Wybraliśmy [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "usługi Azure Cosmos DB") jako repozytorium dla rekordów dziennika i błędów (Cosmos DB odnosi się do rekordów jako dokumenty). Ponieważ usługi Azure Logic Apps zawiera standardowy szablon wszystkie odpowiedzi, nie mamy utworzyć niestandardowy schemat. Utworzymy aplikację interfejsu API do **Wstaw** i **zapytania** rekordów dziennikami i błędami. Także definiują schematu dla każdej aplikacji interfejsu API.  

Innym elementem było przeczyszczania rekordów po określonej dacie. Usługa cosmos DB ma właściwość o nazwie [czas wygaśnięcia](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "czas wygaśnięcia") czasu wygaśnięcia (TTL), które umożliwiło nam ustaw **czas wygaśnięcia** wartość dla każdego rekordu lub kolekcji. Ta funkcja wyeliminować konieczność ręcznego usuwania rekordów w usłudze Cosmos DB.

> [!IMPORTANT]
> Aby ukończyć ten samouczek, musisz utworzyć bazę danych Cosmos DB i dwie kolekcje (rejestrowanie i błędów).

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

Pierwszym krokiem jest do tworzenia aplikacji logiki i otworzyć aplikację w Projektancie aplikacji logiki. W tym przykładzie używamy aplikacji logiki nadrzędny podrzędny. Załóżmy, że utworzono już element nadrzędny i przed jego utworzeniem aplikacji logiki podrzędnych.

Ponieważ w tym do logowania się w rekordzie pochodzące z usługi Dynamics CRM Online, Zacznijmy u góry. Trzeba użyć **żądania** wyzwalacza, ponieważ aplikacja logiki nadrzędna wyzwala tego dziecka.

### <a name="logic-app-trigger"></a>Wyzwalacz aplikacji Logic app

Używamy **żądania** wyzwalacza, jak pokazano w poniższym przykładzie:

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

Firma Microsoft należy zalogować się źródła (żądanie) pacjentów rekordu z poziomu portalu Dynamics CRM Online.

1. Firma Microsoft należy uzyskać nowy rekord terminu z usługi Dynamics CRM Online.

   Wyzwalacz pochodzących z systemów CRM zapewnia nam za pomocą **CRM PatentId**, **typu rekordu**, **nowe lub zaktualizowane rekordu** (nowej lub zaktualizuj wartość logiczna), i  **SalesforceId**. **SalesforceId** może mieć wartości null, ponieważ jest ona używana tylko dla aktualizacji.
   Uzyskujemy rekord CRM przy użyciu programu CRM **PatientID** i **typu rekordu**.

2. Następnie należy dodać naszą aplikację interfejsu API SQL usługi Azure Cosmos DB **InsertLogEntry** operacji, jak pokazano poniżej w Projektancie aplikacji logiki.

   **Wstaw wpis dziennika**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Wstaw wpis błędu**

   ![Wstaw wpis dziennika](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Wyszukaj tworzenie rekordu błędu**

   ![Warunek](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Kod źródłowy aplikacji logiki

> [!NOTE]
> Poniższe przykłady są wyłącznie przykładów. Ponieważ ten samouczek opiera się na implementacji, obecnie dostępna w środowisku produkcyjnym, wartość **węzeł źródłowy** mogą wyświetlać właściwości, które są związane z planowaniem terminu. > 

### <a name="logging"></a>Rejestrowanie

Poniższy przykład kodu aplikacji logiki pokazuje, jak obsługiwać rejestrowanie.

#### <a name="log-entry"></a>Wpis dziennika

Poniżej przedstawiono kod źródłowy aplikacji logiki do wstawiania wpisu dziennika.

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

#### <a name="log-request"></a>Dziennik żądań

Oto komunikat żądania dzienników, opublikowany w aplikacji interfejsu API.

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


#### <a name="log-response"></a>Zarejestruj odpowiedź

Oto komunikat odpowiedzi dzienników z aplikacji interfejsu API.

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

Poniższy przykład kodu aplikacji logiki pokazuje, jak można implementować obsługi błędów.

#### <a name="create-error-record"></a>Utwórz rekord błędu

Poniżej przedstawiono kod źródłowy aplikacji logiki do tworzenia rekord błędu.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Błąd wstawiania do usługi Cosmos DB — żądanie

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

#### <a name="insert-error-into-cosmos-db--response"></a>Błąd wstawiania do usługi Cosmos DB — odpowiedzi

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

#### <a name="salesforce-error-response"></a>Odpowiedzi na błąd usługi SalesForce

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Zwróć odpowiedź z powrotem do aplikacji logiki nadrzędnego

Po otrzymaniu odpowiedzi można przekazać odpowiedź z powrotem do aplikacji logiki nadrzędnej.

#### <a name="return-success-response-to-parent-logic-app"></a>Powodzenie odpowiedź zwrócona do nadrzędnej aplikacji logiki

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

#### <a name="return-error-response-to-parent-logic-app"></a>Błąd odpowiedź zwrócona do nadrzędnej aplikacji logiki

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


## <a name="cosmos-db-repository-and-portal"></a>Repozytorium usługi cosmos DB i portal

Nasze rozwiązanie dodaje możliwości [usługi Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Błąd portalu zarządzania

Aby wyświetlić błędy, można utworzyć aplikację sieci web MVC, która wyświetla rekordy błędów z usługi Cosmos DB. **Listy**, **szczegóły**, **Edytuj**, i **Usuń** operacje znajdują się w bieżącej wersji.

> [!NOTE]
> Próba operacji edycji: Usługa cosmos DB zamienia cały dokument. Rekordy objętego **listy** i **szczegółów** widoki są tylko przykłady. Nie są one rzeczywisty termin pacjentów rekordów.

Poniżej przedstawiono przykłady naszych szczegóły aplikacji MVC utworzone przy użyciu podejścia opisany wcześniej.

#### <a name="error-management-list"></a>Błąd listy zarządzania.
![Lista błędów](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Widok szczegółów zarządzania błędów
![Szczegóły błędu](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal zarządzania dziennika

Aby wyświetlić dzienniki, możemy również utworzona aplikacja internetowa MVC. Poniżej przedstawiono przykłady naszych szczegóły aplikacji MVC utworzone przy użyciu podejścia opisany wcześniej.

#### <a name="sample-log-detail-view"></a>Widok szczegółowy dziennik przykładowy
![Widok szczegółowy dziennik](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>Szczegóły aplikacji interfejsu API

#### <a name="logic-apps-exception-management-api"></a>Interfejs API zarządzania wyjątków aplikacji logiki

Nasza aplikacja typu open source usługi Azure Logic Apps wyjątek interfejsu API zarządzania zapewnia funkcje, zgodnie z opisem w tym miejscu — istnieją dwa kontrolery:

* **ErrorController** wstawia rekord błędu (dokumenty) w kolekcji usługi Azure Cosmos DB.
* **LogController** wstawia rekord dziennika (dokumenty) w kolekcji usługi Azure Cosmos DB.

> [!TIP]
> Użyj obu kontrolerów `async Task<dynamic>` działalnością operacyjną, umożliwiając operacji w czasie wykonywania, dzięki czemu możemy utworzyć schemat usługi Azure Cosmos DB w treści operacji. 
> 

Każdy dokument w usłudze Azure Cosmos DB musi mieć unikatowy identyfikator. Używamy `PatientId` i dodanie sygnatury czasowej, która jest konwertowana na wartość sygnatura czasowa systemu Unix (podwójny). Firma Microsoft obciąć wartości do usunięcia wartości ułamkowe.

Można wyświetlić kodu źródłowego kontrolera błąd interfejsu API z [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Możemy wywołać interfejs API z aplikacji logiki przy użyciu następującej składni:

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

* Można łatwo zaimplementować, rejestrowanie i obsługa błędów w aplikacji logiki.
* Rekordy dziennika i błędów (dokumenty), można użyć usługi Azure Cosmos DB jako repozytorium.
* MVC umożliwia tworzenie portalu, aby wyświetlić rekordy dziennika i błędów.

### <a name="source-code"></a>Kod źródłowy

Kod źródłowy, Logic Apps Management wyjątków aplikacji interfejsu API jest dostępny w tym [repozytorium GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API Management wyjątków aplikacji logiki").

## <a name="next-steps"></a>Kolejne kroki

* [Wyświetl więcej przykłady aplikacji logiki i scenariusze](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Dowiedz się więcej o monitorowaniu aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Tworzenie szablonów automatycznego wdrażania dla usługi logic apps](../logic-apps/logic-apps-create-deploy-template.md)
