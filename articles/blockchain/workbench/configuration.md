---
title: Informacje o konfiguracji platformy Azure Blockchain Workbench
description: Omówienie konfiguracji aplikacji Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578984"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Informacje o konfiguracji platformy Azure Blockchain Workbench

Aplikacji Azure Blockchain Workbench są wieloosobowa przepływy pracy, zdefiniowana za pomocą metadanych konfiguracji i kodu kontraktu inteligentne. Metadane konfiguracji definiują ogólne przepływy pracy i model interakcji aplikacji łańcucha bloków. Kontrakty inteligentne zdefiniowanie logiki biznesowej, aplikacji łańcucha bloków. Usługa Workbench używa konfiguracji i kodu kontraktu inteligentnych do generowania środowisk użytkownika aplikacji łańcucha bloków.

Metadane konfiguracji określa następujące informacje dla każdej aplikacji łańcucha bloków:

* Nazwa i opis aplikacji łańcucha bloków
* Unikatowe role dla użytkowników, którzy mogą działać lub wziąć udział w ramach aplikacji łańcucha bloków
* Co najmniej jeden przepływy pracy. Każdy przepływ pracy działa jako automatu stanów, aby sterować przepływem logiki biznesowej. Przepływy pracy mogą być niezależne lub współdziałają ze sobą.

Każdy zdefiniowany przepływ pracy określa:

* Nazwa i opis przepływu pracy
* Stany przepływu pracy.  Każdy stan jest etapu w przepływie sterowania logiki biznesowej. 
* Akcje, do którego nastąpi przejście do następnego stanu
* Role użytkownika może zainicjować każdej akcji
* Kontrakty inteligentne, które reprezentują logikę biznesową w plikach kodu

## <a name="application"></a>Aplikacja

Aplikacji łańcucha bloków zawiera konfiguracji metadanych, przepływy pracy i ról użytkowników, którzy mogą działać lub wziąć udział w aplikacji.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| ApplicationName | Unikatowa nazwa aplikacji. Odpowiedni kontrakt inteligentnych muszą używać tego samego **ApplicationName** dla klasy stosownej umowy.  | Yes |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną aplikacji. | Yes |
| Opis | Opis aplikacji. | Nie |
| ApplicationRoles | Kolekcja [ApplicationRoles](#application-roles). Role użytkowników, którzy mogą działać lub wziąć udział w aplikacji.  | Yes |
| Przepływy | Kolekcja [przepływy pracy](#workflows). Każdy przepływ pracy działa jako automatu stanów, aby sterować przepływem logiki biznesowej. | Yes |

Aby uzyskać przykład, zobacz [przykładowy plik konfiguracji](#configuration-file-example).

## <a name="workflows"></a>Przepływy

Logika biznesowa aplikacji może być modelowane jako automatu stanów, których podjęcie działania powoduje, że przepływ logikę biznesową, aby przenieść z jednego stanu do drugiego. Przepływ pracy jest kolekcją tych stanów i akcje. Każdy przepływ pracy składa się z co najmniej jeden kontraktów inteligentnych, które reprezentują logikę biznesową w plikach kodu. Kontrakt pliku wykonywalnego jest wystąpieniem przepływu pracy.

| Pole | Opis | Wymagane | Maksymalna długość |
|-------|-------------|:--------:|-----------:|
| Name (Nazwa) | Nazwa unikatowa przepływu pracy. Odpowiedni kontrakt inteligentnych muszą używać tego samego **nazwa** dla klasy stosownej umowy. | Yes | 50 |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną przepływu pracy. | Yes | 255 |
| Opis | Opis przepływu pracy. | Nie | 255 |
| Inicjatory | Kolekcja [ApplicationRoles](#application-roles). Role, które są przypisane do użytkowników autoryzowanych do utworzenia kontraktów w przepływie pracy. | Yes | |
| StartState | Nazwa stanu początkowego przepływu pracy. | Yes | |
| Właściwości | Kolekcja [identyfikatory](#identifiers). Reprezentuje dane mogą być odczytywane poza łańcuchem i wizualizowany w użytkownika środowiska narzędzi. | Yes | |
| Konstruktor | Definiuje parametry wejściowe w celu utworzenia wystąpienia przepływu pracy. | Yes | |
| Funkcje | Kolekcja [funkcje](#functions) mogą być wykonywane w przepływie pracy. | Yes | |
| Stany | Kolekcja przepływu pracy [stany](#states). | Yes | |

Aby uzyskać przykład, zobacz [przykładowy plik konfiguracji](#configuration-file-example).

## <a name="type"></a>Type

Obsługiwane typy danych.

| Type | Opis |
|-------|-------------|
| adres  | Łańcuch bloków adresów typu, takie jak *umów* lub *użytkowników*. |
| tablica    | Pojedynczą tablicę poziomu typu integer, bool, pieniędzy i czasu. Tablice mogą być statyczne lub dynamiczne. Użyj **ElementType** na określony typ danych elementów w tablicy. Zobacz [Przykładowa konfiguracja](#example-configuration-of-type-array). |
| bool     | Typ danych logicznych. |
| kontrakt | Adres typ kontraktu. |
| Wyliczenia     | Wyliczany zestaw nazwanych wartości. Korzystając z typu wyliczeniowego, możesz również określić listy EnumValues. Każda wartość jest ograniczone do 255 znaków. Znaki prawidłowe wartości to górna i małe litery (A – Z, a – z) i cyfry (0 – 9). Zobacz [przykładową konfigurację i użycie w Solidity](#example-configuration-of-type-enum). |
| int      | Integer — typ danych. |
| money    | Typ danych walutowych. |
| state    | Stan przepływu pracy. |
| string  | String — typ danych. Maksymalna liczba znaków 4000. Zobacz [Przykładowa konfiguracja](#example-configuration-of-type-string). |
| Użytkownik     | Adres typu użytkownika. |
| time     | Typ danych w czasie. |
|`[ Application Role Name ]`| Dowolna nazwa określona w roli aplikacji. Ogranicza użytkownikom dostęp do tego typu roli. |

### <a name="example-configuration-of-type-array"></a>Przykładowa konfiguracja nagłówkową typu tablicy

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Przy użyciu właściwości typu tablicowego

Jeśli właściwość jest zdefiniowana jako typ tablicy w konfiguracji, należy uwzględnić funkcję get jawne, aby zwrócić publiczny właściwości typu tablicy w trwałość. Na przykład:

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Przykładowa konfiguracja typu String

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Przykładowa konfiguracja typu enum

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Przy użyciu typu wyliczeniowego w trwałość

Po zdefiniowaniu wyliczenia w konfiguracji można użyć typów wyliczeń w trwałość. Na przykład można zdefiniować wyliczenie o nazwie PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Lista ciągów musi zgodne z konfiguracją i inteligentne kontraktu jest prawidłowy i spójny deklaracji w aplikacji Blockchain Workbench.

Przykład przypisania:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Przykład parametrów funkcji: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Konstruktor

Definiuje parametry wejściowe dla wystąpienia przepływu pracy.

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| Parametry | Kolekcja [identyfikatory](#identifiers) musi inicjować inteligentne kontraktu. | Yes |

### <a name="constructor-example"></a>Przykład konstruktora

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

| Pole | Opis | Wymagane | Maksymalna długość |
|-------|-------------|:--------:|-----------:|
| Name (Nazwa) | Unikatowa nazwa funkcji. Odpowiedni kontrakt inteligentnych muszą używać tego samego **nazwa** dla odpowiednich funkcji. | Yes | 50 |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną funkcji. | Yes | 255 |
| Opis | Opis funkcji | Nie | 255 |
| Parametry | Kolekcja [identyfikatory](#identifiers) odpowiadający parametrów funkcji. | Yes | |

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

| Pole | Opis | Wymagane | Maksymalna długość |
|-------|-------------|:--------:|-----------:|
| Name (Nazwa) | Unikatowa nazwa stanu. Odpowiedni kontrakt inteligentnych muszą używać tego samego **nazwa** dotyczy stanu. | Yes | 50 |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną stanu. | Yes | 255 |
| Opis | Opis stanu. | Nie | 255 |
| ProcentWykonania | Wartość całkowitą, wyświetlana w interfejsie użytkownika aplikacji Blockchain Workbench, aby wyświetlić postęp na przepływ sterowania logiki biznesowej. | Yes | |
| Styl | Wskazówka Visual wskazująca, czy stan reprezentuje stan powodzenia lub niepowodzenia. Istnieją dwa prawidłowe wartości: `Success` lub `Failure`. | Yes | |
| Przejścia | Kolekcja dostępnych [przejścia](#transitions) od bieżącego stanu do następnego zestawu stanów. | Nie | |

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
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Przejścia

Dostępne akcje do następnego stanu. Co najmniej jedna rola użytkownika może wykonywać działania na każdy stan, gdy akcja może przejście stanu do innego stanu w przepływie pracy. 

| Pole | Opis | Wymagane |
|-------|-------------|:--------:|
| AllowedRoles | Lista ról aplikacji dzienniki może inicjować przejścia. Wszyscy użytkownicy w określonej roli może być możliwe do wykonania akcji. | Nie |
| AllowedInstanceRoles | Lista ról użytkownika, udział lub określone w umowie inteligentne mogą inicjować przejścia. Wystąpienia roli są zdefiniowane w **właściwości** w ramach przepływów pracy. AllowedInstanceRoles reprezentuje użytkownika z uczestnictwa w wystąpieniu inteligentne kontraktu. AllowedInstanceRoles zapewniają możliwość ograniczenia podjęcie działania do roli użytkownika w wystąpieniu kontraktu.  Na przykład może być tylko chcesz zezwolić użytkownika, który utworzył kontraktu (InstanceOwner), aby można było zakończyć zamiast wszystkich użytkowników w Typ roli (właściciela), jeśli określona rola w AllowedRoles. | Nie |
| Nazwa wyświetlana | Przyjazną nazwę wyświetlaną przejścia. | Yes |
| Opis | Opis przejścia. | Nie |
| Funkcja | Nazwa funkcji do inicjowania przejścia. | Yes |
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

Role aplikacji zdefiniować zestaw ról, które można przypisać do użytkowników, którzy chcą działania lub wziąć udział w aplikacji. Role aplikacji może służyć do ograniczania akcje strumieniowo rozgrywki i uczestniczenia w ramach łańcucha bloków aplikacji i odpowiednie przepływy pracy. 

| Pole | Opis | Wymagane | Maksymalna długość |
|-------|-------------|:--------:|-----------:|
| Name (Nazwa) | Unikatowa nazwa roli aplikacji. Odpowiedni kontrakt inteligentnych muszą używać tego samego **nazwa** dla odpowiednich ról. Nazwy typów podstawowych są zastrzeżone. Nie można nadać nazwy roli aplikacji z taką samą nazwę jak [typu](#type)| Yes | 50 |
| Opis | Opis roli aplikacji. | Nie | 255 |

### <a name="application-roles-example"></a>Przykład ról aplikacji

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

Identyfikatory reprezentują kolekcję informacje używane do opisywania właściwości przepływu pracy, Konstruktor i parametrów funkcji. 

| Pole | Opis | Wymagane | Maksymalna długość |
|-------|-------------|:--------:|-----------:|
| Name (Nazwa) | Unikatowa nazwa właściwości lub parametru. Odpowiedni kontrakt inteligentnych muszą używać tego samego **nazwa** dla odpowiednich właściwości lub parametru. | Yes | 50 |
| Nazwa wyświetlana | Przyjazna nazwa wyświetlana dla właściwości lub parametru. | Yes | 255 |
| Opis | Opis właściwości lub parametru. | Nie | 255 |

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

Przeniesienia zasobów jest inteligentnych kontraktu kupna i sprzedaży zasobów o wysokiej wartości, które wymagają Inspektor i appraiser. Sprzedawcy można wyświetlić listę swoich zasobów przez utworzenie wystąpienia kontrakt inteligentnego transferu zawartości. Kupujący może być oferty, wykonując akcję w kontrakcie inteligentne i innych firm mogą podejmować działania sprawdzić lub oceny elementu zawartości. Gdy element zawartości jest oznaczony zarówno inspekcji i oceniane, nabywców i sprzedawcy będzie upewnij się, sprzedaż ponownie przed ustawieniem umowy do ukończenia. W każdym punkcie w procesie wszyscy uczestnicy mieć wgląd w stan zamówienia jest aktualizowany. 

Aby uzyskać więcej informacji, łącznie z plikami kodu, zobacz [próbki transferu zawartości dla aplikacji Azure Blockchain Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

Następujący plik konfiguracji jest przykład transferu zawartości:

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
          "Description": "Asset transfer has been canceled",
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
> [Dokumentacja interfejsu API REST usługi Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

