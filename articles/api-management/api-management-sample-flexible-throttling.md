---
title: Zaawansowane żądanie ograniczania usługi Azure API Management
description: Dowiedz się, jak utworzyć i zastosować elastyczne przydziałów i zasad w usłudze Azure API Management ograniczania szybkości.
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
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783792"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Zaawansowane żądanie ograniczania usługi Azure API Management
Możliwość ograniczania żądań przychodzących jest kluczową rolę usługi Azure API Management. Albo poprzez kontrolowanie współczynnika żądań lub łączna liczba żądań/przesyłane dane, usługa API Management umożliwia także dostawcami interfejsów API do ochrony swoich interfejsów API z nadużyć i utworzyć wartości dla różnych warstw produktu interfejsu API.

## <a name="product-based-throttling"></a>Na podstawie produktu ograniczania przepustowości
Do tej pory szybkość, z możliwości ograniczania zostały ograniczone do są ograniczone do określonej subskrypcji produktu, zdefiniowane w witrynie Azure portal. Jest to przydatne w przypadku dostawcy interfejsu API, które można zastosować limity deweloperów, którzy utworzyli konto w celu użycia ich interfejsu API, jednak nie pomaga, na przykład w ograniczania poszczególnych użytkowników końcowych interfejsu API. Jest to możliwe, że dla pojedynczego użytkownika dla deweloperów aplikacji do wykorzystania cały limit przydziału i następnie uniemożliwić inni klienci dewelopera do korzystania z aplikacji. Ponadto wielu klientów, którzy mogą generować dużą liczbę żądań może ograniczyć dostęp do użytkownicy korzystający rzadko.

## <a name="custom-key-based-throttling"></a>Klucz niestandardowy na podstawie ograniczenia przepustowości
Nowy [rate-limit przez key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) i [przydziału by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) zasady zapewniają bardziej elastyczne rozwiązanie do kontroli ruchu. Te nowe zasady umożliwiają definiowanie wyrażenia, aby zidentyfikować klucze, które są używane do śledzenia użycia ruchu. Easiest zilustrowano sposób, w jaki ta funkcja działa z przykładem. 

## <a name="ip-address-throttling"></a>Ograniczenie adresu IP
Następujące zasady ograniczyć adres IP jednego klienta tylko 10 wywołań na minutę, przy użyciu łącznie 1 000 000 wywołań i 10 000 kilobajtów przepustowości na miesiąc. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Jeśli wszyscy klienci w Internecie używany unikatowy adres IP, może to być skutecznym sposobem ograniczanie wykorzystania przez użytkownika. Jednak istnieje prawdopodobieństwo, że wielu użytkowników, którzy udostępniają jeden publiczny adres IP ze względu na ich dostęp do Internetu za pośrednictwem urządzenia translatora adresów Sieciowych. Mimo to interfejsy API, dzięki czemu dostęp nieuwierzytelniony `IpAddress` może być najlepszą opcją.

## <a name="user-identity-throttling"></a>Ograniczanie tożsamości użytkownika
Jeśli użytkownik końcowy jest uwierzytelniony, ograniczenie klucza mogą być generowane na podstawie informacji, który unikatowo identyfikuje ten użytkownik.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

W tym przykładzie przedstawiono sposób Ekstrakcja nagłówka autoryzacji, przekonwertuj je na `JWT` obiektu i używać podmiotu tokenu do identyfikacji użytkownika i używać go jako współczynnik, ograniczenie klucza. Jeśli tożsamość użytkownika jest przechowywany w `JWT` jako jeden z innych oświadczeń, następnie tę wartość można używać w jego miejsce.

## <a name="combined-policies"></a>Połączone zasady
Mimo, że nowe zasady ograniczania przepustowości zapewniają większą kontrolę niż istniejące zasady ograniczania przepustowości, ma nadal wartości łączenie obie funkcje. Ograniczanie według klucza subskrypcji produktu ([ograniczanie liczby wywołań według subskrypcji](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) i [Ustawianie przydziału użycia według subskrypcji](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) to świetny sposób, aby umożliwić znakując interfejsu API przez nałożenie na podstawie poziomów użycia. Bardziej szczegółową kontrolę, można ograniczyć przez użytkownika jest wzajemnie dopełniającego się i zapobiega pogarszania jakości środowiska innego zachowania jednego użytkownika. 

## <a name="client-driven-throttling"></a>Oparte na ograniczanie klienta
Po zdefiniowaniu ograniczenie klucza przy użyciu [wyrażenie zasad](/azure/api-management/api-management-policy-expressions), to dostawcy interfejsu API, który jest wybór sposobu ograniczania jest zakresem. Jednak deweloper może chcieć kontrolować, jak oni limit szybkości ich własnych klientów. To może zostać włączona przez dostawcę interfejsu API przez wprowadzenie nagłówka niestandardowego umożliwia aplikacji klienckiej dewelopera do komunikowania się klucz interfejsu API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Umożliwia to aplikacji klienckiej dewelopera wybierz, jak chcą utworzyć szybkości, ograniczenie klucza. Deweloperzy klienta można utworzyć własne warstwy stawki przydzielać użytkownikom zestawów kluczy i obracając użycia klucza.

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia szybkość i oferty ograniczania do ochrony i Dodaj wartość do usługi interfejsu API. Nowe zasady ograniczania za pomocą niestandardowej reguły określania zakresów pozwalają skuteczniejszą kontrolę nad te zasady, aby umożliwić klientom Twórz aplikacje jeszcze lepsze. Przykłady w niniejszym artykule pokazują użycie tych nowych zasad przez ograniczania klucze za pomocą adresów IP klientów, tożsamość użytkownika i wartości klienta generowany szybkości produkcji. Istnieją jednak wiele innych części komunikatu, która mogłaby być używana, takich jak agent użytkownika, fragmenty ścieżki adresu URL, rozmiar komunikatu.

## <a name="next-steps"></a>Kolejne kroki
Przekaż nam swoją opinię w wątku Disqus na potrzeby tego artykułu. Byłoby Cieszymy się o innych potencjalnych wartości klucza, które zostały logicznym wyborem w swoich scenariuszy.

