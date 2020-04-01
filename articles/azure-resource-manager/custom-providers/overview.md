---
title: Przegląd dostawców niestandardowych
description: Dowiedz się więcej o dostawcach zasobów niestandardowych platformy Azure i o tym, jak rozszerzyć płaszczyznę interfejsu API platformy Azure, aby dopasować ją do przepływów pracy.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398477"
---
# <a name="azure-custom-resource-providers-overview"></a>Omówienie dostawców zasobów niestandardowych platformy Azure

Dostawcy zasobów niestandardowych platformy Azure to platforma rozszerzalności na platformę Azure. Umożliwia definiowanie niestandardowych interfejsów API, które mogą służyć do wzbogacenia domyślnego środowiska platformy Azure. W tej dokumentacji opisano:

- Jak skompilować i wdrożyć dostawcę zasobów niestandardowych platformy Azure.
- Jak korzystać z usług Azure Custom Resource Providers, aby rozszerzyć istniejące przepływy pracy.
- Gdzie można znaleźć przewodniki i przykłady kodu, aby rozpocząć.

![Omówienie dostawcy niestandardowego](./media/overview/overview.png)

> [!IMPORTANT]
> Dostawcy niestandardowi są obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Co mogą zrobić dostawcy zasobów niestandardowych

Oto kilka przykładów, co można osiągnąć za pomocą dostawców zasobów niestandardowych platformy Azure:

- Rozszerz interfejs API REST usługi Azure Resource Manager, aby uwzględnić usługi wewnętrzne i zewnętrzne.
- Włącz scenariusze niestandardowe na istniejących przepływach pracy platformy Azure.
- Dostosuj kontrolę i efekt szablonów usługi Azure Resource Manager.

## <a name="what-is-a-custom-resource-provider"></a>Co to jest niestandardowy dostawca zasobów

Dostawcy zasobów niestandardowych platformy Azure są dokonywane przez utworzenie umowy między platformą Azure i punktu końcowego. Ten kontrakt definiuje listę nowych zasobów i akcji za pośrednictwem nowego zasobu, **Microsoft.CustomProviders/resourceProviders**. Dostawca zasobów niestandardowych będzie następnie uwidaczniać te nowe interfejsy API na platformie Azure. Dostawcy zasobów niestandardowych platformy Azure składają się z trzech części: dostawcy zasobów niestandardowych, **punktów końcowych**i zasobów niestandardowych.

## <a name="how-to-build-custom-resource-providers"></a>Jak tworzyć dostawców zasobów niestandardowych

Dostawcy zasobów niestandardowych są listą umów między platformą Azure a punktami końcowymi. W tym kontrakcie opisano, jak platforma Azure powinna wchodzić w interakcje z punktem końcowym. Dostawca zasobów działa jak serwer proxy i będzie przesyłać dalej żądania i odpowiedzi do i z określonego **punktu końcowego**. Dostawca zasobów może określić dwa typy kontraktów: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) i [**actions**](./custom-providers-action-endpoint-how-to.md). Są one włączone za pomocą definicji punktów końcowych. Definicja punktu końcowego składa się z trzech pól: **name**, **routingType**i **endpoint**.

Przykładowy punkt końcowy:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Właściwość | Wymagany | Opis
---|---|---
name | *Tak* | Nazwa definicji punktu końcowego. Platforma Azure udostępni tę nazwę za pośrednictwem interfejsu API w obszarze "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
typ routingu | *nie* | Określa typ kontraktu z **punktem końcowym**. Jeśli nie zostanie określony, domyślnie będzie to "Proxy".
endpoint | *Tak* | Punkt końcowy do kierowania żądań do. Będzie to obsługiwać odpowiedzi, jak również wszelkie skutki uboczne żądania.

### <a name="building-custom-resources"></a>Tworzenie zasobów niestandardowych

**Typy zasobów** opisują nowe zasoby niestandardowe, które są dodawane do platformy Azure. Uwidaczniają one podstawowe metody RESTful CRUD. Zobacz [więcej o tworzeniu zasobów niestandardowych](./custom-providers-resources-endpoint-how-to.md)

Przykładowy dostawca zasobów niestandardowych z **zasobamiTypy:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Interfejsy API dodane do platformy Azure dla powyższego przykładu:

HttpMethod (httpmethod) | Przykładowy identyfikator URI | Opis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Wywołanie interfejsu API interfejsu AZURE REST w celu utworzenia nowego zasobu.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Wywołanie interfejsu API interfejsu AZURE REST w celu usunięcia istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Wywołanie interfejsu API usługi Azure REST w celu pobrania istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Wywołanie interfejsu API usługi Azure REST, aby pobrać listę istniejących zasobów.

### <a name="building-custom-actions"></a>Tworzenie akcji niestandardowych

**Akcje** opisują nowe akcje, które są dodawane do platformy Azure. Mogą one być widoczne na dostawcy zasobów lub zagnieżdżone w obszarze **resourceType**. Zobacz [więcej o tworzeniu akcji niestandardowych](./custom-providers-action-endpoint-how-to.md)

Przykładowy dostawca zasobów niestandardowych z **akcjami:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Interfejsy API dodane do platformy Azure dla powyższego przykładu:

HttpMethod (httpmethod) | Przykładowy identyfikator URI | Opis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupGroupName}/<br>dostawcy/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Wywołanie interfejsu API interfejsu AZURE REST, aby aktywować akcję.

## <a name="looking-for-help"></a>Szukam pomocy

Jeśli masz pytania dotyczące tworzenia dostawcy zasobów niestandardowych platformy Azure, spróbuj zadać pytanie w sprawie [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobne pytanie mogło już zostać zadane i udzielono odpowiedzi, więc sprawdź najpierw przed wysłaniem. Dodaj tag, ```azure-custom-providers``` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o dostawcach niestandardowych. Przejdź do następnego artykułu, aby utworzyć dostawcę niestandardowego.

- [Szybki start: tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie niestandardowych akcji i zasobów na platformie Azure](./tutorial-get-started-with-custom-providers.md)
- [Jak: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Jak: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
