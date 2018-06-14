---
title: Zaawansowane żądanie ograniczania z usługą Azure API Management
description: Dowiedz się, jak utworzyć i zastosować wskaźnik, ograniczając zasad z usługą Azure API Management i elastyczny przydział.
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
ms.openlocfilehash: c7fcbd57021134631e9f10dcbb2d40e4c130af02
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29800027"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Zaawansowane żądanie ograniczania z usługą Azure API Management
Możliwość ograniczania żądań przychodzących jest kluczową rolę usługi Azure API Management. Albo kontrolując współczynnika żądań lub całkowita liczba żądań/transferu danych, zarządzanie interfejsami API umożliwia dostawcom usług interfejsu API ochrony ich interfejsów API z nadużyć i utworzyć wartości dla różnych warstw produktu interfejsu API.

## <a name="product-based-throttling"></a>Na podstawie produktu ograniczania przepustowości
Data, szybkość, z funkcji ograniczania przepustowości zostały maksymalnie są ograniczone do określonej subskrypcji produktu zdefiniowane w portalu Azure. Jest to przydatne w przypadku dostawcy interfejsu API w celu zastosowania ograniczeń na deweloperów, którzy utworzyli konto do użycia interfejsu API, jednak nie pomaga, na przykład w ograniczania poszczególnych użytkowników końcowych interfejsu API. Jest to możliwe, że dla pojedynczego użytkownika dewelopera aplikacji, aby korzystać z całego przydziału, a następnie uniemożliwić dewelopera innych klientów do korzystania z aplikacji. Ponadto wielu klientów, którzy mogą generować dużą liczbę żądań może ograniczyć dostęp do okazjonalne użytkowników.

## <a name="custom-key-based-throttling"></a>Klucz niestandardowy na podstawie ograniczania przepustowości
Nowy [szybkość limit przez klucz](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) i [limitu przydziału przez klucz](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) zasady zapewniają bardziej elastyczne rozwiązanie do kontroli ruchu. Te nowe zasady umożliwiają definiowanie wyrażenia, aby zidentyfikować kluczy, które są używane do śledzenia użycia ruchu. To działania easiest przedstawiono przykład. 

## <a name="ip-address-throttling"></a>Ograniczenia adresów IP
Następujące zasady ograniczyć adresów IP jednego klienta do wywołania tylko 10 co minutę, łączna liczba wywołań 1 000 000 i 10 000 kilobajtów przepustowości miesięcznie. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Jeśli wszyscy klienci w Internecie używany unikatowy adres IP, może to być efektywnym sposobem ograniczenia użycia przez użytkownika. Jednak prawdopodobnie wielu użytkowników udostępnia jeden publiczny adres IP z powodu ich dostęp do Internetu za pośrednictwem urządzenia translatora adresów Sieciowych. Mimo to dla interfejsów API, który umożliwia nieuwierzytelnione dostępu `IpAddress` może być najlepszym rozwiązaniem.

## <a name="user-identity-throttling"></a>Ograniczenie tożsamości użytkownika
Jeśli użytkownik jest uwierzytelniony, ograniczenie klucza mogą być generowane na podstawie informacji, który unikatowo identyfikuje użytkownika.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

W tym przykładzie pokazano, jak wyodrębnić nagłówek autoryzacji, należy konwertować go do `JWT` obiektu i Użyj podmiotu tokenu do identyfikacji użytkownika i używać go jako szybkość ograniczenie klucza. Jeśli tożsamości użytkownika jest przechowywany w `JWT` wśród innych oświadczeń, następnie tego można użyć wartości w jego miejscu.

## <a name="combined-policies"></a>Łączna zasad
Mimo że nowe zasady ograniczania przepustowości zapewniają większą kontrolę niż istniejące zasady ograniczania przepustowości, występuje nadal łączenie możliwości obu wartości. Ograniczanie przez klucz produktu subskrypcji ([Limit szybkości wywołanie przez subskrypcji](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) i [przydział użycia zestawu subskrypcji](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) to dobry sposób, aby umożliwić monetizing interfejsu API, pobierając oparte na poziomy użycia. Skuteczniejszą kontrolę o możliwość ograniczania przez użytkownika jest uzupełniające i uniemożliwia pogorszenia jakości związku środowisko innego zachowanie jednego użytkownika. 

## <a name="client-driven-throttling"></a>Klient zmiennych ograniczania przepustowości
Po klucz ograniczania przepustowości jest definiowana za pomocą [wyrażenie zasad](https://msdn.microsoft.com/library/azure/dn910913.aspx), wówczas jest dostawcy interfejsu API, który jest wybranie jak zakres to ograniczenie. Jednak dewelopera mogą chcieć kontrolować sposób ich limit szybkości własnych klientów. To może zostać włączona przez dostawcę interfejsu API dzięki zastosowaniu niestandardowy nagłówek umożliwia dewelopera aplikacji klienckiej do komunikowania się klucz interfejsu API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Dzięki temu aplikacja kliencka dewelopera wybierz, jak chcą, aby utworzyć szybkość ograniczenie klucza. Deweloperzy klienta można utworzyć własne warstwy stawki przydzielając zestawy kluczy dla użytkowników i obracanie użycia klucza.

## <a name="summary"></a>Podsumowanie
Zarządzanie interfejsami API Azure zapewnia szybkość i oferty ograniczania do ochrony i Dodaj wartość do usług interfejsu API. Nowe zasady ograniczania przepustowości przy użyciu niestandardowych reguł zakresu pozwalają skuteczniejszą kontrolę nad tych zasad, aby umożliwić klientom tworzenie jeszcze bardziej poprawić jakość aplikacji. Przykłady w tym artykule przedstawiają sposób używania tych nowych zasad przez współczynnik produkcyjnym ograniczanie klucze z adresów IP klientów, tożsamość użytkownika i wartości klientów wygenerowany. Istnieją jednak wiele innych części komunikat, który może zostać użyty, takich jak agent użytkownika, adres URL ścieżki fragmenty, rozmiar wiadomości.

## <a name="next-steps"></a>Kolejne kroki
Daj nam swoją opinię w wątku usługi Disqus dla tego tematu. Jest bardzo otrzymywać informacje o innych potencjalnych wartości kluczy, które zostały logicznej wybór w swoim scenariuszu.

