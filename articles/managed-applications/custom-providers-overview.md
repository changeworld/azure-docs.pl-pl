---
title: Omówienie usługi Azure Resource niestandardowych dostawców
description: Więcej informacji o dostawcy zasobów niestandardowych usługi Azure i jak rozszerzyć płaszczyzny interfejsu API usługi Azure, aby dopasować przepływów pracy.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796080"
---
# <a name="azure-custom-resource-providers-overview"></a>Przegląd dostawców zasobów niestandardowych platformy Azure

Dostawcy zasobów usługi Azure Custom to platforma rozszerzalność na platformie Azure. Umożliwia on zdefiniowany do tworzenia niestandardowych interfejsów API, który może służyć w celu wzbogacenia domyślne środowisko platformy Azure. W tej dokumentacji opisano:

- Sposób tworzenia i wdrażania niestandardowego dostawcę zasobów platformy Azure.
- Jak korzystać z usługi Azure Resource niestandardowi rozszerzenie istniejących przepływów pracy.
- Gdzie można znaleźć, przewodniki i przykłady kodu, aby rozpocząć pracę.

![Omówienie niestandardowego dostawcy](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Niestandardowi dostawcy jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Co można zrobić dostawców zasobów niestandardowych

Poniżej przedstawiono kilka przykładów można osiągnąć przez z dostawcami zasobów niestandardowych usługi Azure:

- Rozszerzenie interfejsu REST API usługi Resource Manager platformy Azure obejmujący usługi wewnętrznych i zewnętrznych.
- Włącz niestandardowych scenariuszy na podstawie istniejących przepływów pracy platformy Azure.
- Dostosowywanie formantu szablonów usługi Azure Resource Manager i efekt.

## <a name="what-is-a-custom-resource-provider"></a>Co to jest dostawca zasobów niestandardowych

Dostawcy zasobów usługi platformy Azure niestandardowe są wykonywane przez tworzenie kontraktu między platformą Azure i punktu końcowego. Ten kontrakt definiuje listę nowych zasobów i akcji za pomocą nowego zasobu **Microsoft.CustomProviders/resourceProviders**. Dostawca zasobów niestandardowych następnie udostępni te nowe interfejsy API na platformie Azure. Usługa Azure niestandardowych dostawców zasobów składają się z trzech części: dostawca zasobów niestandardowych **punktów końcowych**i zasobów niestandardowych.

## <a name="how-to-build-custom-resource-providers"></a>Jak tworzyć dostawców zasobów niestandardowych

Dostawcy zasobów niestandardowych są listy umów między platformą Azure i punktów końcowych. Ten kontrakt opisano, jak Azure powinny wchodzić w interakcje z punktem końcowym. Działa dostawca zasobów, takich jak serwer proxy i przekaże żądania i odpowiedzi do i z określonym **punktu końcowego**. Dostawca zasobów można określić dwa typy kontraktów: [ **wartości resourcetype** ](./custom-providers-resources-endpoint-how-to.md) i [ **akcje**](./custom-providers-action-endpoint-how-to.md). Są one włączone za pomocą definicji punktów końcowych. Definicja punktu końcowego składa się z trzech pól: **nazwa**, **routingType**, i **punktu końcowego**.

Przykładowy punkt końcowy:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Właściwość | Wymagane | Opis
---|---|---
name | *Tak* | Nazwa definicji punktu końcowego. Azure udostępni tę nazwę, za pośrednictwem jego interfejsu API w obszarze "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *Brak* | Określa typ kontraktu z **punktu końcowego**. Jeśli nie zostanie określony, zostaną domyślnie "Proxy".
endpoint | *Tak* | Punkt końcowy, aby kierować żądania do. Obsłuży odpowiedzi, a także wszelkie efekty uboczne żądania.

### <a name="building-custom-resources"></a>Tworzenie zasobów niestandardowych

**Wartości resourcetype** opisano nowe zasoby niestandardowe, które są dodawane do usługi Azure. Ujawnić te podstawowe metody RESTful CRUD. Zobacz [więcej informacji na temat tworzenia niestandardowych zasobów](./custom-providers-resources-endpoint-how-to.md)

Przykładowy niestandardowego dostawcę zasobów za pomocą **wartości resourcetype**:

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

HttpMethod | Przykładowy identyfikator URI | Opis
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Wywołanie interfejsu API REST platformy Azure w celu utworzenia nowego zasobu.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Wywołanie interfejsu API REST platformy Azure, można usunąć istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | Wywołania interfejsu API REST platformy Azure, który można pobrać istniejącego zasobu.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? api-version = 2018-09-01-preview | Wywołania interfejsu API REST platformy Azure, który można pobrać listy istniejących zasobów.

### <a name="building-custom-actions"></a>Tworzenie akcji niestandardowych

**Akcje** opisano nowe akcje, które są dodawane do platformy Azure. Te mogą być udostępniane na podstawie dostawcy zasobów lub zagnieżdżony w **resourceType**. Zobacz [więcej informacji na temat tworzenia niestandardowych akcji](./custom-providers-action-endpoint-how-to.md)

Przykładowy niestandardowego dostawcę zasobów za pomocą **akcje**:

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

HttpMethod | Przykładowy identyfikator URI | Opis
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? api-version = 2018-09-01-preview | Wywołanie interfejsu API REST platformy Azure można aktywować akcji.

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące programowania dostawcy zasobów niestandardowych platformy Azure, Zadaj pytanie [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobne pytania mogą już zostały zadawane i odpowiedzi, więc wyboru przed publikowanie. Dodaj tag ```azure-custom-providers``` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono niestandardowych dostawców. Przejdź do następnego artykułu, aby utworzyć niestandardowego dostawcę.

- [Szybki start: Tworzenie niestandardowego dostawcy zasobów platformy Azure i wdrażanie zasobów niestandardowych](./create-custom-provider.md)
- [Samouczek: Tworzenie akcji niestandardowych i zasobów na platformie Azure](./tutorial-custom-providers-101.md)
- [Instrukcje: Dodawanie akcji niestandardowych do interfejsu API REST platformy Azure](./custom-providers-action-endpoint-how-to.md)
- [Instrukcje: Dodawanie zasobów niestandardowych do interfejsu API REST platformy Azure](./custom-providers-resources-endpoint-how-to.md)
