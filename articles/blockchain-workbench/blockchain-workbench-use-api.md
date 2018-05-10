---
title: Przy użyciu interfejsu API REST Azure Blockchain Workbench
description: Scenariusze dotyczące sposobu korzystania z interfejsu API REST Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 27ed94b3ce14c57e369b0c80d4c53b72a5ae40a8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Przy użyciu interfejsu API REST Azure Blockchain Workbench 

Azure interfejsu API REST Workbench Blockchain umożliwia deweloperom i pracownicy przetwarzający informacje do tworzenia zaawansowanych integracji aplikacji blockchain. Ten dokument przeprowadzi Cię przez kilka metod klucza interfejsu API REST Workbench. Rozważmy scenariusz, w którym Deweloper chce utworzyć klienta blockchain niestandardowych, dzięki czemu zalogowanych użytkowników wyświetlić i interakcyjnie ich aplikacji blockchain przypisane. Klienta pozwala użytkownikom na wyświetlanie wystąpień kontraktu i podjąć działania w umowach inteligentne. Klient używa interfejsu API REST Workbench w kontekście zalogowanego użytkownika w celu wykonaj następujące czynności:

* Lista aplikacji
* Przepływ pracy dla listy aplikacji
* Listę wystąpień inteligentne kontraktu dla przepływu pracy
* Lista dostępnych akcji dla kontraktu
* Wykonanie akcji dla kontraktu

## <a name="list-applications"></a>Lista aplikacji

Pierwszym zadaniem po użytkownik zalogował się do klienta blockchain, jest pobrać wszystkie aplikacje blockchain Blockchain Workbench dla użytkownika. W tym scenariuszu użytkownik ma dostęp do dwóch aplikacji:

1.  Przeniesienie zasobów
2.  Chłodniczych transportu

Użyj [aplikacji interfejsu API GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

Odpowiedź zawiera listę wszystkich aplikacji blockchain, do których użytkownik ma dostęp w Blockchain Workbench. Administratorzy Blockchain Workbench Pobierz wszystkie aplikacje blockchain, podczas Workbench z systemem innym niż administratorzy pobrać wszystkie blockchains, do których mają co najmniej jedną aplikację skojarzone rola lub wystąpienia skojarzone inteligentne kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Przepływ pracy dla listy aplikacji

Gdy użytkownik wybierze blockchain dotyczy aplikacji, w tym przypadku transferu zawartości, klient blockchain pobiera wszystkie przepływy pracy blockchain określonych aplikacji. Użytkownicy mogą następnie wybrać odpowiednie przepływu pracy przed pokazywane wszystkie wystąpienia inteligentne kontraktu dla przepływu pracy. Każda aplikacja blockchain ma co najmniej jeden przepływ, a każdy przepływ pracy ma zero lub inteligentne wystąpień kontraktu. Podczas kompilowania blockchain aplikacji klienckich, zalecane jest aby pominąć przepływu środowisko użytkownika, dzięki czemu użytkownicy mogą wybrać odpowiedni przepływu pracy, jeśli istnieje tylko jeden przepływ pracy dla aplikacji blockchain. W takim przypadku przeniesienia zasobów zawiera tylko jeden przepływ pracy, nazywane również przeniesienie zasobów.

Użyj [przepływów pracy aplikacji interfejsu API GET](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Odpowiedź zawiera listę wszystkich przepływów pracy aplikacji blockchain określony, do którego użytkownik ma dostęp w Blockchain Workbench. Administratorzy Blockchain Workbench pobrania wszystkich blockchain przepływów pracy, gdy Workbench z systemem innym niż administratorzy pobrać wszystkie przepływy pracy, do których mają co najmniej jedną rolę skojarzonej aplikacji lub skojarzone z rolą wystąpienia inteligentne kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listę wystąpień inteligentne kontraktu dla przepływu pracy

Gdy użytkownik wybierze odpowiednie przepływu pracy tego przypadku zasobów transferu, klient blockchain pobierze wszystkie wystąpienia inteligentne kontraktu dla określonego przepływu pracy. Te informacje służy do wyświetlania wszystkich wystąpień inteligentne kontraktu dla przepływu pracy i umożliwiają użytkownikom nowości w każdym wystąpień pokazano inteligentne kontraktu. W tym przykładzie należy rozważyć użytkownik chce interakcję z jednym z wystąpień inteligentne kontraktu podejmowania żadnych działań.

Użyj [umów GET interfejsu API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Odpowiedź zawiera listę wszystkich wystąpień inteligentne kontraktu określonego przepływu pracy. Administratorzy Workbench uzyskać wszystkich wystąpień inteligentne kontraktu, podczas pobrania Workbench z systemem innym niż administratorzy wszystkich inteligentne wystąpień kontraktu, do których mają co najmniej jedną rolę skojarzonej aplikacji lub skojarzone z rolą wystąpienia inteligentne kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Lista dostępnych akcji dla kontraktu

Gdy użytkownik zdecydował się nowości w jednym umów, klienta blockchain następnie można wyświetlić wszystkie dostępne akcje użytkownika danego stanu kontraktu. W tym przykładzie użytkownik potrzebuje na wszystkich dostępnych akcji dla nowego kontraktu inteligentne, którym utworzone:

* Zmień: Umożliwia użytkownikowi zmodyfikuj opis i cen zasobów.
* Zakończenie: Umożliwia użytkownikowi przerwanie kontrakt elementu zawartości.

Użyj [kontraktu akcję GET interfejsu API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Odpowiedź zawiera listę wszystkich akcji do których użytkownik może zająć podane bieżący stan wystąpienia określonego inteligentne kontraktu. Użytkownicy otrzymują wszystkie odpowiednie akcje, jeśli użytkownik ma rolę skojarzonej aplikacji lub jest skojarzony z rolą wystąpienia inteligentne kontraktu dla bieżącego stanu wystąpienia określonego inteligentne kontraktu.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Wykonanie akcji dla kontraktu

Użytkownik następnie można zdecydować, podjęcie działań dla wystąpienia określonego inteligentne kontraktu. W takim przypadku Rozważmy scenariusz, w których użytkownik chcesz zmodyfikować, opis i cen zasób do następujących:

* Opis: "Mój zaktualizowane samochód"
* Cena: 54321

Użyj [kontraktu akcji POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Użytkownicy mogą tylko do wykonania akcji danego bieżącego stanu wystąpienia określonego inteligentne kontraktu i Rola skojarzona z nim aplikacja lub inteligentne kontraktu wystąpienia roli. Jeśli wpis zakończy się pomyślnie, odpowiedź HTTP 200 OK zostało zwrócone z treści odpowiedzi.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja interfejsu API REST Workbench Blockchain Azure](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)