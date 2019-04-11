---
title: Opracowywanie zawartości przy użyciu interfejsów API w wersji 3 — Azure | Dokumentacja firmy Microsoft
description: W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471906"
---
# <a name="developing-with-media-services-v3-apis"></a>Tworzenie aplikacji za pomocą usługi Media Services v3 interfejsów API

W tym artykule omówiono reguły mające zastosowanie do interfejsów API oraz jednostek, podczas tworzenia za pomocą usługi Media Services v3.

## <a name="naming-conventions"></a>Konwencje nazewnictwa

Nazwy zasobów w usłudze Azure Media Services w wersji 3 (na przykład Zasoby, Zadania, Przekształcenia) podlegają ograniczeniom nazewnictwa usługi Azure Resource Manager. Zgodnie z zasadami usługi Azure Resource Manager nazwy zasobów są zawsze unikatowe. W związku z tym jako nazw zasobów można używać dowolnych ciągów będących unikatowymi identyfikatorami (na przykład identyfikatorów GUID). 

Nazwy zasobów usługi Media Services nie mogą zawierać znaków „<”, „>”, „%”, „&”, „:”, „&#92;”, „?”, „/”, „*”, „+”, „.”, pojedynczych cudzysłowów ani żadnych znaków sterujących. Wszystkie inne znaki są dozwolone. Maksymalna długość nazwy zasobu to 260 znaków. 

Aby uzyskać więcej informacji na temat nazewnictwa w usłudze Azure Resource Manager zobacz: [Wymagania dotyczące nazewnictwa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) i [Konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>zasady projektowania interfejsu API w wersji 3 i RBAC

Jedną z najważniejszych zasad projektowania interfejsów API w wersji 3 jest lepsze zabezpieczenie interfejsu API. interfejsy API w wersji 3 nie zwracają wpisów tajnych lub poświadczeń na **uzyskać** lub **listy** operacji. Klucze mają zawsze wartość null, są puste lub oczyszczone z odpowiedzi. Użytkownik musi wywołać metodę oddzielnej akcji służącej do pobrania wpisów tajnych lub poświadczenia. **Czytnika** roli nie można wywołać operacji, więc nie można wywoływać operacje, takie jak ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas, StreamingLocator.ListContentKeys,. Masz oddzielne działania umożliwia ustawianie bardziej szczegółowych uprawnień zabezpieczeń RBAC do roli niestandardowej, w razie potrzeby.

Aby uzyskać więcej informacji, zobacz:

- [Definicje ról wbudowanych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Zarządzanie dostępem przy użyciu kontroli RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Kontrola dostępu oparta na rolach dla konta usługi Media Services](rbac-overview.md)
- [Pobieranie zawartości klucza zasad — .NET](get-content-key-policy-dotnet-howto.md).

## <a name="long-running-operations"></a>Długotrwałych operacji

Operacje oznaczone `x-ms-long-running-operation` w usłudze Azure Media Services [swagger pliki](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) długie długotrwałych operacji. 

Aby uzyskać szczegółowe informacje na temat śledzenie operacji asynchronicznych na platformie Azure, zobacz [operacje asynchroniczne](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Usługa Media Services obsługuje następujące operacje długotrwałych:

* Utwórz element LiveEvent
* Element LiveEvent aktualizacji
* Usuń element LiveEvent
* Element LiveEvent Start
* Zatrzymaj element LiveEvent
* Resetuj element LiveEvent
* Utwórz LiveOutput
* Usuń LiveOutput
* Utwórz StreamingEndpoint
* Aktualizuj StreamingEndpoint
* Usuń StreamingEndpoint
* Rozpocznij StreamingEndpoint
* Zatrzymaj StreamingEndpoint
* StreamingEndpoint skalowania

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Stronicowanie filtrowania, sortowania, jednostek usługi Media Services

Zobacz [filtrowanie, porządkowanie, stronicowanie jednostek usługi Azure Media Services](entities-overview.md)

## <a name="next-steps"></a>Kolejne kroki

[Rozpoczęcie programowania przy użyciu interfejsu API usługi Media Services 3 za pomocą zestawów SDK i narzędzi](developers-guide.md)
