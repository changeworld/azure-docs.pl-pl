---
title: Azure Blockchain Workbench konfiguracji odwołania
description: Przegląd konfiguracji aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 178c2c1d4f727241338d6d933cd5eecbbffe65bb
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303818"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench konfiguracji odwołania

 Azure Blockchain Workbench aplikacje są wieloosobowa przepływów pracy zdefiniowanych przez metadanych konfiguracji i kodu kontraktu inteligentne. Metadane konfiguracji definiują wysokiego poziomu przepływów pracy i modelu interakcji blockchain aplikacji. Kontrakty inteligentne określają logiki biznesowej aplikacji blockchain. Workbench używa konfiguracji i kodu kontraktu inteligentne do generowania wrażenia użytkowników blockchain aplikacji.

Metadane konfiguracji określa następujące informacje dla każdej aplikacji blockchain: 

* Nazwa i opis aplikacji blockchain
* Unikatowe role dla użytkowników, którzy mogą pełnić lub uczestniczyć w aplikacji blockchain
* Co najmniej jeden przepływ pracy. Każdy przepływ pracy działa jako komputera stanu do sterowania przepływem logiki biznesowej. Przepływy pracy mogą być niezależne lub współdziałać ze sobą.

Każdego zdefiniowanego przepływu pracy określa:

* Nazwa i opis przepływu pracy
* Stany przepływu pracy.  Każdy stan jest etapem przepływu sterowania logiki biznesowej. 
* Działania w celu przejścia do następnego stanu
* Dozwolone ról użytkownika, aby każda akcja
* Inteligentne umowy, które reprezentują logiki biznesowej w plikach kodu

## <a name="application"></a>Aplikacja

Aplikacja blockchain zawiera role metadanych, przepływy pracy i użytkownika konfiguracji, którzy mogą pełnić lub uczestniczyć w aplikacji.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| ApplicationName | Unikatową nazwę aplikacji. Odpowiednie inteligentne kontraktu musi używać tego samego **ApplicationName** dla klasy dotyczy kontraktu.  | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną aplikacji. | Yes |
| Opis | Opis aplikacji. | Nie |
| ApplicationRoles | Kolekcja [ApplicationRoles](#application-roles). Role użytkowników, którzy mogą pełnić lub uczestniczyć w aplikacji.  | Yes |
| Przepływy | Kolekcja [przepływy pracy](#workflows). Każdy przepływ pracy działa jako komputera stanu do sterowania przepływem logiki biznesowej. | Yes |

Na przykład zobacz [przykładowy plik konfiguracji](#configuration-file-example).

## <a name="workflows"></a>Przepływy

Logiki biznesowej aplikacji może być modelowana jako gdy powoduje podjęcie działania przepływu logiki biznesowej, aby przenieść z jednego stanu do innego komputera stanu. Przepływ pracy jest kolekcją akcji i stanów. Każdy przepływ pracy składa się z co najmniej jeden kontrakty inteligentne, które reprezentują logiki biznesowej plików kodu. Kontrakt pliku wykonywalnego jest wystąpienia przepływu pracy.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Name (Nazwa) | Nazwa unikatowa przepływu pracy. Odpowiednie inteligentne kontraktu musi używać tego samego **nazwa** dla klasy dotyczy kontraktu. | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną przepływu pracy. | Yes |
| Opis | Opis przepływu pracy. | Nie |
| Inicjatorów | Kolekcja [ApplicationRoles](#application-roles). Role, które są przypisane do użytkowników autoryzowanych do utworzenia kontraktów w przepływie pracy. | Yes |
| StartState | Nazwa stanu początkowego przepływu pracy. | Yes |
| Właściwości | Kolekcja [identyfikatory](#identifiers). Reprezentuje dane, które mogą być odczytywane poza łańcuch lub wizualizowanego użytkownika wystąpić narzędzia. | Yes |
| Konstruktor | Definiuje parametry wejściowe w celu utworzenia wystąpienia przepływu pracy. | Yes |
| Funkcje | Kolekcja [funkcje](#functions) mogą być wykonywane w przepływie pracy. | Yes |
| Stany | Kolekcja przepływu pracy [stanów](#states). | Yes |

Na przykład zobacz [przykładowy plik konfiguracji](#configuration-file-example).

## <a name="type"></a>Typ

Obsługiwane typy danych.

| Typ | Opis |
|-------|-------------|
| Adres  | Blockchain adres typu, takich jak *kontrakty* lub *użytkowników* |
| wartość logiczna     | Boolean — typ danych |
| kontrakt | Adres typu kontraktu |
| int      | Integer — typ danych |
| oszczędność pieniędzy    | Typ danych Money |
| state    | Stan przepływu pracy |
| ciąg   | String — typ danych |
| Użytkownik     | Adres typu użytkownika |
| time     | Typ danych czasowych |
|`[ Application Role Name ]`| Dowolna nazwa określona w roli aplikacji. Ogranicza użytkowników do tego typu roli. |

## <a name="constructor"></a>Konstruktor

Definiuje parametry wejściowe dla wystąpienia przepływu pracy.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Parametry | Kolekcja [identyfikatory](#identifiers) wymagane do zainicjowania inteligentne kontraktu. | Yes |

### <a name="constructor-example"></a>Przykład — Konstruktor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Funkcje

Definiuje funkcje, które mogą być wykonywane w przepływie pracy.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Name (Nazwa) | Unikatowa nazwa funkcji. Odpowiednie inteligentne kontraktu musi używać tego samego **nazwa** dla odpowiedniej funkcji. | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną funkcji. | Yes |
| Opis | Opis funkcji | Nie |
| Parametry | Kolekcja [identyfikatory](#identifiers) odpowiadający parametrów funkcji. | Yes |

### <a name="functions-example"></a>Przykład funkcji

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Stany

Kolekcja unikatowych stanów w przepływie pracy. Każdy stan przechwytuje krok w przepływie sterowania logiki biznesowej. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Name (Nazwa) | Unikatowa nazwa stanu. Odpowiednie inteligentne kontraktu musi używać tego samego **nazwa** dotyczy stanu. | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną stanu. | Yes |
| Opis | Opis stanu. | Nie |
| ProcentWykonania | Dodatnia wartość wyświetlana w interfejsie użytkownika narzędzia Blockchain Workbench pokazywania postępu w ramach przepływu sterowania logiki biznesowej. | Yes |
| Styl | Wskazówka Visual wskazująca, czy stan reprezentuje stan powodzenia lub niepowodzenia. Istnieją dwa prawidłowe wartości: `Success` lub `Failure`. | Yes |
| Przejścia | Kolekcja dostępnych [przejścia](#transitions) od bieżącego stanu do następnego zestawu stanów. | Nie |

### <a name="states-example"></a>Przykład stanów

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Przejścia

Dostępne akcje do następnego stanu. Co najmniej jednej roli użytkownika może wykonywać działania na każdy stan, w którym akcji można przejść stanu do innego stanu w przepływie pracy. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| AllowedRoles | Lista ról aplikacje mogą zainicjować przejścia. Wszyscy użytkownicy w określonej roli można wykonać akcji. | Nie |
| AllowedInstanceRoles | Lista ról użytkownika uczestniczących lub określone w umowie inteligentne mogą inicjować przejścia. Wystąpienie role są definiowane w **właściwości** w przepływach pracy. AllowedInstanceRoles reprezentują użytkownik należący do wystąpienia inteligentne kontraktu. AllowedInstanceRoles zapewniają możliwość ograniczenia podjęcie działania do roli użytkownika w przypadku kontraktu.  Na przykład tylko możesz zezwolić użytkownikowi, który utworzył kontraktu (InstanceOwner), aby można było zakończyć zamiast wszystkich użytkowników w roli typu (właściciela), jeśli określona rola w AllowedRoles. | Nie |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną przejścia. | Yes |
| Opis | Opis przejścia. | Nie |
| Funkcja | Nazwa funkcji w celu zainicjowania przejścia. | Yes |
| NextStates | Kolekcja potencjalnych stanów dalej po pomyślnym przejściu. | Yes |

### <a name="transitions-example"></a>Przykład przejść

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Role aplikacji

Role aplikacji definiują zestaw ról, które można przypisać do użytkowników, którzy chcą działania lub uczestniczyć w aplikacji. Ról aplikacji pozwala ograniczyć akcje i udział w blockchain aplikacji i odpowiednie przepływów pracy. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Name (Nazwa) | Unikatowa nazwa roli aplikacji. Odpowiednie inteligentne kontraktu musi używać tego samego **nazwa** dotyczy roli. Typ podstawowy nazwy są zarezerwowane. Nie można nadać nazwy roli aplikacji o takiej samej nazwie jak [typu](#type)| Yes |
| Opis | Opis roli aplikacji. | Nie |

### <a name="application-roles-example"></a>Przykładowa aplikacja ról

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identyfikatory

Identyfikatory reprezentuje kolekcję informacje używane do opisywania właściwości przepływu pracy, Konstruktor i parametry funkcji. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Name (Nazwa) | Unikatowa nazwa właściwości lub parametr. Odpowiednie inteligentne kontraktu musi używać tego samego **nazwa** dla odpowiednich właściwości lub parametr. | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną dla właściwości lub parametr. | Yes |
| Opis | Opis właściwości lub parametr. | Nie |

### <a name="identifiers-example"></a>Przykład identyfikatorów

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Przykładowy plik konfiguracji

Przeniesienia zasobów jest inteligentne kontraktu scenariusz kupować i sprzedaż aktywów wysokiej wartości, które wymagają inspektora i appraiser. Sprzedawców można wyświetlić ich zasoby przy uruchamianiu kontrakt inteligentny transfer zasobów. Kupujący ułatwia oferty, wykonując czynności inteligentne kontraktu, a inne strony można podjąć działania w celu inspekcji lub oceny elementu zawartości. Gdy zasób jest oznaczony zarówno inspekcji i oceniane, sprzedawcy i nabywcy zostanie Potwierdź sprzedaży ponownie przed kontrakt jest ustawione jako zakończone. W każdym punkcie w procesie wszystkich uczestników mieć wgląd w stan kontrakt jest aktualizowana. 

Aby uzyskać więcej informacji, włącznie z plikami kodu, zobacz [próby przeniesienia zasobów dla Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Następujący plik konfiguracji jest przykładowej przeniesienia zasobów:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been cancelled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API REST Workbench Blockchain Azure](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

