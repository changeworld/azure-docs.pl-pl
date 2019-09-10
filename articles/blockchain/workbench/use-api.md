---
title: Korzystanie z interfejsu API REST usługi Azure łańcucha bloków Workbench Preview
description: Scenariusze korzystania z interfejsu API REST usługi Azure łańcucha bloków Workbench Preview
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 412b78363be70918969734cd8890a8ee940df431
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843814"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Korzystanie z interfejsu API REST usługi Azure łańcucha bloków Workbench Preview

Interfejs API REST usługi Azure łańcucha bloków Workbench w wersji zapoznawczej oferuje deweloperom i pracownikom przetwarzającym informacje możliwość tworzenia rozbudowanych integracji z aplikacjami łańcucha bloków. Ten dokument zawiera opis kilku najważniejszych metod interfejsu API REST usługi Workbench. Załóżmy na przykład, że deweloper chce utworzyć niestandardowego klienta łańcucha bloków. Ten klient łańcucha bloków umożliwia zalogowanym użytkownikom wyświetlanie ich przypisanych aplikacji łańcucha bloków i korzystanie z nich. Klient umożliwia użytkownikom wyświetlanie wystąpień kontraktu i podejmowanie działań dotyczących kontraktów inteligentnych. Klient korzysta z interfejsu API REST Workbench w kontekście zalogowanego użytkownika w celu wykonania następujących czynności:

* Wyświetlanie listy aplikacji
* Wyświetlanie listy przepływów pracy dla aplikacji
* Wyświetlanie listy wystąpień kontraktu inteligentnego dla przepływu pracy
* Wyświetlanie listy dostępnych akcji dla kontraktu
* Wykonywanie akcji dla kontraktu

Przykładowe aplikacje łańcucha bloków używane w scenariuszach można [pobrać z witryny GitHub](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Wyświetlanie listy aplikacji

Po zalogowaniu się użytkownika do klienta usługi łańcucha bloków pierwsze zadanie to pobranie wszystkich aplikacji łańcucha bloków Workbench dla użytkownika. W tym scenariuszu użytkownik ma dostęp do dwóch aplikacji:

1. [Transfer zasobów](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Transport chłodny](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Użyj [interfejsu API pobierania aplikacji](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

Odpowiedź zawiera listę wszystkich aplikacji łańcucha bloków, do których użytkownik ma dostęp w usłudze łańcucha bloków Workbench. Łańcucha bloków Workbench administratorzy uzyskują każdą aplikację łańcucha bloków. Administratorzy niebędący Workbench otrzymują wszystkie blockchains, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub skojarzoną rolę wystąpienia kontraktu inteligentnego.

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

## <a name="list-workflows-for-an-application"></a>Wyświetlanie listy przepływów pracy dla aplikacji

Gdy użytkownik wybierze odpowiednią aplikację łańcucha bloków (na przykład **transfer zasobów**), klient łańcucha bloków pobiera wszystkie przepływy pracy określonej aplikacji łańcucha bloków. Użytkownicy mogą następnie wybrać odpowiedni przepływ pracy, zanim zostaną im wyświetlone wszystkie wystąpienia kontraktu inteligentnego dla przepływu pracy. Każda aplikacja łańcucha bloków ma co najmniej jeden przepływ pracy, a każdy przepływ pracy ma zero lub więcej wystąpień kontraktu inteligentnego. W przypadku aplikacji klienckiej łańcucha bloków, która ma tylko jeden przepływ pracy, zalecamy Pominięcie przepływu pracy użytkownika, który umożliwia użytkownikom wybranie odpowiedniego przepływu. W takim przypadku **transfer zasobów** ma tylko jeden przepływ pracy, nazywany również **transferem zasobów**.

Użyj [interfejsu API pobierania przepływów pracy aplikacji](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Odpowiedź wyświetla listę wszystkich przepływów pracy określonej aplikacji łańcucha bloków, do których użytkownik ma dostęp w usłudze Blockchain Workbench. Łańcucha bloków Workbench Administratorzy mogą uzyskać każdy przepływ pracy łańcucha bloków. Administratorzy niebędący Workbench otrzymują wszystkie przepływy pracy, dla których mają co najmniej jedną skojarzoną rolę aplikacji lub są skojarzone z rolą wystąpienia kontraktu inteligentnego.

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

## <a name="list-smart-contract-instances-for-a-workflow"></a>Wyświetlanie listy wystąpień kontraktu inteligentnego dla przepływu pracy

Gdy użytkownik wybierze odpowiedni przepływ pracy, ten przypadek **transferu zasobów**spowoduje pobranie wszystkich wystąpień kontraktu inteligentnego dla określonego przepływu pracy. Te informacje służą do wyświetlania wszystkich wystąpień inteligentnych kontraktów dla przepływu pracy. Można też pozwolić użytkownikom na głębokie szczegółowe w dowolne z pokazanych wystąpień kontraktu inteligentnego. W tym przykładzie zakładamy, ze użytkownik chce wejść w interakcję z jednym z wystąpień kontraktu inteligentnego, aby wykonać akcję.

Użyj [interfejsu API pobierania kontraktów](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Odpowiedź zawiera listę wszystkich wystąpień kontraktu inteligentnego określonego przepływu pracy. Administratorzy Workbench uzyskują wszystkie wystąpienia inteligentne kontraktu. Administratorzy niebędący Workbench otrzymują każde wystąpienie kontraktu inteligentnego, dla którego ma co najmniej jedną skojarzoną rolę aplikacji lub jest skojarzone z rolą inteligentnego wystąpienia kontraktu.

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

## <a name="list-available-actions-for-a-contract"></a>Wyświetlanie listy dostępnych akcji dla kontraktu

Gdy użytkownik zdecyduje się na głębokie szczegółowe kontraktu, klient łańcucha bloków może wyświetlić dostępne akcje użytkownika z uwzględnieniem stanu kontraktu. W tym przykładzie użytkownik przegląda wszystkie dostępne akcje dla nowo utworzonego kontraktu inteligentnego:

* Zmodyfikować Zezwala użytkownikowi na modyfikowanie opisu i ceny elementu zawartości.
* Kończyć Umożliwia użytkownikowi zakończenie kontraktu elementu zawartości.

Użyj [interfejsu API pobierania akcji kontraktu](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Odpowiedź zawiera listę wszystkich akcji, które użytkownik może wykonać dla danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego. Użytkownicy uzyskują wszystkie odpowiednie akcje, jeśli użytkownik ma skojarzoną rolę aplikacji lub jest skojarzony z rolą wystąpienia kontraktu inteligentnego dla bieżącego stanu określonego wystąpienia kontraktu inteligentnego.

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

## <a name="execute-an-action-for-a-contract"></a>Wykonywanie akcji dla kontraktu

Użytkownik może następnie zdecydować się na podjęcie działania dla określonego wystąpienia kontraktu inteligentnego. W tym przypadku Rozważmy scenariusz, w którym użytkownik chce zmodyfikować opis i cenę elementu zawartości do następującej akcji:

* Opis: "Mój zaktualizowany samochód"
* Cena: 54321

Użyj [interfejsu API tworzenia akcji kontraktu](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

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

Użytkownicy mogą wykonać akcję tylko w przypadku danego bieżącego stanu określonego wystąpienia kontraktu inteligentnego i skojarzonej roli aplikacji lub roli wystąpienia kontraktu inteligentnego użytkownika. Jeśli tworzenie zakończy się pomyślnie, zwrócona zostanie odpowiedź HTTP 200 OK bez treści odpowiedzi.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST usługi Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)