---
title: Dołączanie zasobów niestandardowych dostawców platformy Azure
description: Dowiedz się więcej na temat dołączania zasobów przy użyciu dostawców niestandardowych platformy Azure w celu zastosowania zarządzania lub konfiguracji do innych typów zasobów platformy Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609153"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Omówienie dołączania zasobów niestandardowych dostawców platformy Azure

Dołączanie zasobów niestandardowych dostawców platformy Azure jest modelem rozszerzalności dla typów zasobów platformy Azure. Pozwala to na stosowanie operacji lub zarządzania w istniejących zasobach platformy Azure na dużą skalę. Aby uzyskać więcej informacji, zobacz [jak Dostawcy niestandardowi platformy Azure mogą poszerzać platformę Azure](./custom-providers-overview.md). W tej dokumentacji opisano:

- Co można dołączyć do zasobów.
- Podstawowe informacje na temat dołączania zasobów i sposoby ich używania.
- Gdzie można znaleźć przewodniki i przykłady kodu, aby rozpocząć pracę.

> [!IMPORTANT]
> Dostawcy niestandardowi są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Co można dołączyć do zasobów

Podobnie jak w przypadku [niestandardowych zasobów dostawcy platformy Azure](./custom-providers-resources-endpoint-how-to.md), dołączanie do zasobów definiuje kontrakt, który będzie serwerem proxy żądania dołączania do punktu końcowego. W przeciwieństwie do zasobów niestandardowych, dołączanie do zasobów nie tworzy nowego typu zasobu, ale pozwala na rozszerzenie istniejących typów zasobów. Ponadto proces dołączania do zasobów współdziała z Azure Policy, więc zarządzanie i konfiguracja zasobów może odbywać się w odpowiedniej skali. Przykłady przepływów pracy związanych z dołączaniem zasobów:

- Instalowanie rozszerzeń Virtual Machines i zarządzanie nimi.
- Przekazuj i Konfiguruj wartości domyślne na kontach usługi Azure Storage.
- Włącz ustawienia diagnostyki linii bazowej w odpowiedniej skali.

## <a name="resource-onboarding-basics"></a>Podstawy dołączania zasobów

Dołączanie zasobów jest konfigurowane za pośrednictwem dostawców niestandardowych platformy Azure przy użyciu typów zasobów "Microsoft. CustomProviders/resourceProviders" i "Microsoft. CustomProviders/Association". Aby włączyć dołączanie zasobów dla niestandardowego dostawcy, podczas procesu konfiguracji należy utworzyć element **ResourceType** o nazwie "Associations" z atrybutem **routingtype** zawierającym wartość "Extension". Ponadto "Microsoft. CustomProviders/Associations" i "Microsoft. CustomProviders/resourceProviders" nie muszą należeć do tej samej grupy zasobów.

Przykładowy dostawca niestandardowy platformy Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Właściwość | Wymagany | Opis
---|---|---
name | *opcję* | Nazwa definicji punktu końcowego. W przypadku dołączania do zasobu Nazwa musi mieć wartość "Associations".
routingtype | *opcję* | Określa typ kontraktu z **punktem końcowym**. W przypadku dołączania do zasobów prawidłowy **routingTypes** to "proxy, pamięć podręczna, rozszerzenie" i "webhook, cache, Extension".
endpoint | *opcję* | Punkt końcowy, do którego będą kierowane żądania. Będzie to obsługiwać odpowiedź oraz wszystkie efekty uboczne żądania.

Po utworzeniu dostawcy niestandardowego z typem zasobu "Association" można określić obiekt docelowy przy użyciu "Microsoft. CustomProviders/Associations". "Microsoft. CustomProviders/Associations" to zasób rozszerzenia, który może rozszerzać każdy inny zasób platformy Azure. Gdy tworzone jest wystąpienie "Microsoft. CustomProviders/Associations", jego właściwość **element targetresourceid**powinna być PRAWIDŁOWYm identyfikatorem zasobu "Microsoft. CustomProviders/resourceProviders" lub "Microsoft. Solutions/Applications". W takich przypadkach żądanie zostanie przesłane do typu zasobu "Associations" w utworzonym wystąpieniu "Microsoft. CustomProviders/resourceProviders".

> [!Note]
> Jeśli identyfikator zasobu "Microsoft. Solutions/Applications" jest określony jako **element targetresourceid**, w zarządzanej grupie zasobów musi być wdrożony element "Microsoft. CustomProviders/resourceProviders" o nazwie "Public".

Przykładowe skojarzenie niestandardowego dostawcy platformy Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Właściwość | Wymagany | Opis
---|---|---
Element targetresourceid | *opcję* | Identyfikator zasobu "Microsoft. CustomProviders/resourceProviders" lub "Microsoft. Solutions/Applications".

## <a name="how-to-use-resource-onboarding"></a>Jak korzystać z dołączania zasobów

Proces dołączania do zasobów działa przez rozszerzanie innych zasobów przy użyciu zasobu rozszerzenia "Microsoft. CustomProviders/Association". W poniższym przykładzie żądanie zostanie wykonane dla maszyny wirtualnej, ale można rozszerzyć dowolny zasób.

Najpierw należy utworzyć zasób niestandardowego dostawcy z typem zasobu "Association". Spowoduje to zadeklarowanie adresu URL wywołania zwrotnego, który będzie używany podczas tworzenia odpowiedniego zasobu "Microsoft. CustomProviders/Association", który jest przeznaczony dla dostawcy niestandardowego.

Przykładowe żądanie utworzenia "Microsoft. CustomProviders/resourceProviders":

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Po utworzeniu niestandardowego dostawcy można teraz kierować inne zasoby i zastosować efekty uboczne niestandardowego dostawcy.

Przykładowe żądanie utworzenia "Microsoft. CustomProviders/Associations":

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

To żądanie zostanie następnie przekazane do punktu końcowego określonego w początkowym utworzonym dostawcy niestandardowym, do którego odwołuje się wartość "element targetresourceid" w postaci:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Punkt końcowy powinien odpowiedzieć na wartość "Application/JSON" `Content-Type` i prawidłową treść odpowiedzi JSON. Pola, które są zwracane w ramach obiektu "właściwości" w formacie JSON, zostaną dodane do odpowiedzi zwrotnej skojarzenia.

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące opracowywania niestandardowego dostawcy zasobów platformy Azure, spróbuj zadać [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag ```azure-custom-providers```, aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o dostawcach niestandardowych. Przejdź do następnego artykułu, aby utworzyć niestandardowego dostawcę.

- [Samouczek: dołączanie zasobów z dostawcami niestandardowymi](./tutorial-custom-providers-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-custom-providers-101.md)
- [Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
