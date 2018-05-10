---
title: Azure Blockchain Workbench konfiguracji odwołania
description: Przegląd konfiguracji aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 5569a7608a61b4e79a03264e0ccf62682782264b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
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
| użytkownik     | Adres typu użytkownika |
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
| AllowedInstanceRoles | Lista ról użytkownika uczestniczących lub określone w umowie inteligentne mogą inicjować przejścia. Wystąpienie role są definiowane w **właściwości** w przepływach pracy. Ci użytkownicy reprezentują uczestniczącym użytkownika lub określone w umowie inteligentne od wszystkich użytkowników typu roli. | Nie |
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

W poniższym przykładzie zdefiniowano aplikacji podstawowe żądanie odpowiedź, w którym obiekt żądający wysyła żądanie i obiekt odpowiadający wysyłania odpowiedzi na żądanie.

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie usługi Azure Workbench Blockchain](blockchain-workbench-deploy.md)

