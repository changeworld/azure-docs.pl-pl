---
title: Dołączanie zasobów
description: Dowiedz się więcej o wykonywaniu dołączania zasobów przy użyciu dostawców niestandardowych platformy Azure w celu zastosowania zarządzania lub konfiguracji do innych typów zasobów platformy Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650411"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Omówienie dołączania zasobów dostawców niestandardowych platformy Azure

Dołączanie zasobów dostawców niestandardowych platformy Azure jest modelem rozszerzalności dla typów zasobów platformy Azure. Umożliwia stosowanie operacji lub zarządzania w istniejących zasobach platformy Azure na dużą skalę. Aby uzyskać więcej informacji, zobacz [Jak dostawcy niestandardowi platformy Azure mogą rozszerzać platformę Azure](overview.md). W tym artykule opisano:

- Co zasób dołączania może zrobić.
- Podstawy dołączania zasobów i sposób ich używania.
- Gdzie można znaleźć przewodniki i przykłady kodu, aby rozpocząć.

> [!IMPORTANT]
> Dostawcy niestandardowi są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie zaleca się jej dla obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwały lub mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Do czego może dorównać dołączanie zasobów?

Podobnie jak [w niestandardowych dostawcach platformy Azure,](./custom-providers-resources-endpoint-how-to.md)dołączanie zasobów definiuje kontrakt, który będzie proxy "dołączanie" żądań do punktu końcowego. W przeciwieństwie do zasobów niestandardowych dołączanie zasobów nie tworzy nowego typu zasobu. Zamiast tego umożliwia rozszerzenie istniejących typów zasobów. Dołączanie zasobów działa z usługą Azure Policy, dzięki czemu zarządzanie i konfiguracja zasobów można wykonywać na dużą skalę. Kilka przykładów przepływów pracy dołączania zasobów:

- Instalowanie rozszerzeń maszyn wirtualnych i zarządzanie nimi.
- Przekaż i skonfiguruj wartości domyślne na kontach magazynu platformy Azure.
- Włącz podstawowe ustawienia diagnostyczne na dużą skalę.

## <a name="resource-onboarding-basics"></a>Podstawy dołączania zasobów

Konfigurowanie dołączania zasobów za pośrednictwem dostawców niestandardowych platformy Azure przy użyciu microsoft.CustomProviders/resourceProviders i Microsoft.CustomProviders/skojarzenia typów zasobów. Aby włączyć dołączanie zasobów dla dostawcy niestandardowego, podczas procesu konfiguracji utwórz **typ zasobu** o nazwie "skojarzenia" z **typem routingu,** który zawiera "Rozszerzenie". Microsoft.CustomProviders/skojarzenia i Microsoft.CustomProviders/resourceProviders nie muszą należeć do tej samej grupy zasobów.

Oto przykładowy dostawca niestandardowy platformy Azure:

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

Właściwość | Wymagana? | Opis
---|---|---
name | Tak | Nazwa definicji punktu końcowego. W przypadku dołączania zasobów nazwa musi być "skojarzeniami".
typ routingu | Tak | Określa typ umowy z punktem końcowym. W przypadku dołączania zasobów **prawidłowe typy routingu** to "Proxy,Cache,Extension" i "Webhook,Cache,Extension".
endpoint | Tak | Punkt końcowy do kierowania żądań do. Spowoduje to obsługę odpowiedzi i wszelkie skutki uboczne żądania.

Po utworzeniu dostawcy niestandardowego z typem zasobu skojarzeń można kierować reklamy za pomocą witryn Microsoft.CustomProviders/associations. Microsoft.CustomProviders/skojarzenia jest zasobem rozszerzenia, które można rozszerzyć dowolne inne zasoby platformy Azure. Po utworzeniu wystąpienia microsoft.CustomProviders/skojarzenia, zajmie właściwość **targetResourceId**, który powinien być prawidłowy Microsoft.CustomProviders/resourceProviders lub Microsoft.Solutions/applications resource ID. W takich przypadkach żądanie zostanie przekazane do typu zasobu skojarzeń w utworzonym wystąpieniu Microsoft.CustomProviders/resourceProviders.

> [!NOTE]
> Jeśli identyfikator zasobu Microsoft.Solutions/applications jest podany jako **obiekt docelowyResourceId,** musi istnieć microsoft.CustomProviders/resourceProviders wdrożone w grupie zasobów zarządzanych o nazwie "public".

Przykładowe skojarzenie dostawców niestandardowych platformy Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Właściwość | Wymagana? | Opis
---|---|---
obiekt docelowyResourceId | Tak | Identyfikator zasobu microsoft.CustomProviders/resourceProviders lub Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Jak korzystać z dołączania zasobów

Dołączanie zasobów działa poprzez rozszerzenie innych zasobów za pomocą zasobu rozszerzenia Microsoft.CustomProviders/associations. W poniższym przykładzie żądanie jest dla maszyny wirtualnej, ale każdy zasób można rozszerzyć.

Najpierw należy utworzyć niestandardowy zasób dostawcy z typem zasobu skojarzeń. Spowoduje to zadeklarowanie adresu URL wywołania zwrotnego, który będzie używany podczas tworzenia odpowiedniego zasobu Microsoft.CustomProviders/associations, który jest przeznaczony dla dostawcy niestandardowego.

Przykładowe żądania tworzenia żądania przez firmę Microsoft.CustomProviders/resourceProviders:

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

Po utworzeniu dostawcy niestandardowego można kierować inne zasoby i zastosować do nich skutki uboczne dostawcy niestandardowego.

Przykładowe żądanie tworzenia żądania przez microsoft.customproviders/skojarzenia:

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

To żądanie zostanie następnie przekazane do punktu końcowego określonego w utworzonym dostawcy niestandardowym, do którego odwołuje się **identyfikator targetResourceId** w tym formularzu:

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

Punkt końcowy powinien odpowiadać za `Content-Type` pomocą aplikacji/json i prawidłowej treści odpowiedzi JSON. Pola, które są zwracane w obiekcie **właściwości** JSON zostaną dodane do odpowiedzi zwracanej skojarzenia.

## <a name="getting-help"></a>Uzyskiwanie pomocy

Jeśli masz pytania dotyczące tworzenia dostawców zasobów niestandardowych platformy Azure, spróbuj zadać je w [usłudze Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-custom-providers). Na podobne pytanie można było już odpowiedzieć, więc sprawdź najpierw przed opublikowaniem. Dodaj tag, ```azure-custom-providers``` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostawcach niestandardowych. Więcej informacji można znaleźć w następujących artykułach:

- [Samouczek: Dołączanie zasobów z dostawcami niestandardowymi](./tutorial-resource-onboarding.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Szybki start: tworzenie niestandardowego dostawcy zasobów i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
