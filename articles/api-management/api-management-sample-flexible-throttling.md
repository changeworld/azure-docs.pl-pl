---
title: Advanced request throttling with Azure API Management (Zaawansowane ograniczanie żądań za pomocą usługi Azure API Management)
description: Dowiedz się, jak tworzyć i stosować elastyczne zasady ograniczania przydziałów i szybkości za pomocą usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066768"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Advanced request throttling with Azure API Management (Zaawansowane ograniczanie żądań za pomocą usługi Azure API Management)
Możliwość ograniczania przychodzących żądań jest kluczową rolą usługi Azure API Management. Kontrolując liczbę żądań lub całkowitą liczbę przesłanych żądań/danych, usługa API Management umożliwia dostawcom interfejsu API ochronę interfejsów API przed nadużyciami i tworzenie wartości dla różnych warstw produktów interfejsu API.

## <a name="product-based-throttling"></a>Ograniczanie przepustowości na podstawie produktu
Do tej pory możliwości ograniczania szybkości zostały ograniczone do zakresu do określonej subskrypcji produktu, zdefiniowanej w witrynie Azure portal. Jest to przydatne dla dostawcy interfejsu API do stosowania limitów na deweloperów, którzy zarejestrowali się do korzystania z ich interfejsu API, jednak nie pomaga, na przykład, w ograniczaniu poszczególnych użytkowników końcowych interfejsu API. Jest możliwe, że dla pojedynczego użytkownika aplikacji dewelopera do korzystania z całego przydziału, a następnie uniemożliwić innym klientom dewelopera możliwość korzystania z aplikacji. Ponadto kilku klientów, którzy mogą generować dużą liczbę żądań, może ograniczyć dostęp do okazjonalnych użytkowników.

## <a name="custom-key-based-throttling"></a>Ograniczanie na podstawie klucza niestandardowego

> [!NOTE]
> Zasady `rate-limit-by-key` `quota-by-key` i zasady nie są dostępne w warstwie Zużycie usługi Azure API Management. 

Nowe zasady [limit stawki po kluczu](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) i [przydział po kluczu](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) zapewniają bardziej elastyczne rozwiązanie do kontroli ruchu. Te nowe zasady umożliwiają definiowanie wyrażeń w celu identyfikowania kluczy, które są używane do śledzenia użycia ruchu. Sposób, w jaki to działa, jest najłatwiejsze zilustrowane przykładem. 

## <a name="ip-address-throttling"></a>Ograniczanie adresów IP
Poniższe zasady ograniczają adres IP jednego klienta tylko do 10 wywołań co minutę, w sumie 1 000 000 wywołań i 10 000 kilobajtów przepustowości miesięcznie. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Jeśli wszyscy klienci w Internecie używali unikatowego adresu IP, może to być skuteczny sposób ograniczenia użycia przez użytkownika. Jest jednak prawdopodobne, że wielu użytkowników udostępnia jeden publiczny adres IP ze względu na dostęp do Internetu za pośrednictwem urządzenia NAT. Mimo to dla interfejsów API, które umożliwiają nieuwierzyty dostęp `IpAddress` może być najlepszym rozwiązaniem.

## <a name="user-identity-throttling"></a>Ograniczanie tożsamości użytkownika
Jeśli użytkownik końcowy jest uwierzytelniony, następnie klucz ograniczania przepustowości mogą być generowane na podstawie informacji, które jednoznacznie identyfikuje tego użytkownika.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

W tym przykładzie pokazano, jak wyodrębnić nagłówek autoryzacji, przekonwertować go na `JWT` obiekt i użyć tematu tokenu do zidentyfikowania użytkownika i użycia go jako klucza ograniczającego szybkość. Jeśli tożsamość użytkownika jest `JWT` przechowywana w jednym z innych oświadczeń, ta wartość może być używana w jego miejscu.

## <a name="combined-policies"></a>Połączone zasady
Mimo że nowe zasady ograniczania przepustowości zapewniają większą kontrolę niż istniejące zasady ograniczania przepustowości, nadal istnieje wartość łącząca obie możliwości. Ograniczanie według klucza subskrypcji produktu[(Ogranicz szybkość wywołania według subskrypcji](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) i [Ustaw przydział użycia według subskrypcji)](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)to świetny sposób na umożliwienie zarabiania na interfejsie API przez ładowanie na podstawie poziomów użycia. Drobniejsze kontroli jest w stanie przepustnicy przez użytkownika jest komplementarne i zapobiega zachowanie jednego użytkownika z degradacji doświadczenia innego. 

## <a name="client-driven-throttling"></a>Ograniczanie przepustowości z napędem klienta
Gdy klucz ograniczania jest zdefiniowany przy użyciu [wyrażenia zasad,](/azure/api-management/api-management-policy-expressions)to dostawca interfejsu API wybiera sposób ograniczania przepustowości. Jednak deweloper może chcieć kontrolować, jak rate ograniczyć własnych klientów. Może to być włączone przez dostawcę interfejsu API, wprowadzając niestandardowy nagłówek, aby umożliwić aplikacji klienckiej dewelopera do komunikowania klucza do interfejsu API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Dzięki temu aplikacji klienckiej dewelopera, aby wybrać, jak chcą utworzyć klucz ograniczania szybkości. Deweloperzy klienta można utworzyć własne warstwy stawek przez przydzielanie zestawów kluczy do użytkowników i obracanie użycia klucza.

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia ograniczanie szybkości i kwotowania w celu ochrony i zwiększenia wartości usługi interfejsu API. Nowe zasady ograniczania przepustowości z niestandardowymi regułami zakresu umożliwiają lepszą kontrolę nad tymi zasadami, aby umożliwić klientom tworzenie jeszcze lepszych aplikacji. Przykłady w tym artykule zademonstrować użycie tych nowych zasad przez produkcję szybkości ograniczania kluczy z adresami IP klienta, tożsamości użytkownika i wartości wygenerowane przez klienta. Istnieje jednak wiele innych części wiadomości, które mogą być używane, takie jak agent użytkownika, fragmenty ścieżki adresu URL, rozmiar wiadomości.

## <a name="next-steps"></a>Następne kroki
Prześlij nam swoją opinię jako problem z gitHubem na ten temat. Byłoby wspaniale usłyszeć o innych potencjalnych kluczowych wartości, które były logicznym wyborem w scenariuszach.

