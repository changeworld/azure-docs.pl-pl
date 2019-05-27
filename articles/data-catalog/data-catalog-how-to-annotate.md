---
title: Jak dodawać adnotacje do źródeł danych w usłudze Azure Data Catalog
description: Artykuł porad wyróżnianie jak dodawać adnotacje do zasobów danych w usłudze Azure Data Catalog, w tym przyjazne nazwy, tagów, opisów i ekspertów.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 18dfd9eb66c1dc71ecb14bb748d9cdd6afeb00a7
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953176"
---
# <a name="how-to-annotate-data-sources"></a>Jak dodawać adnotacje do źródeł danych
## <a name="introduction"></a>Wprowadzenie
**Microsoft Azure Data Catalog** to usługa w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i system odnajdowania firmowych źródeł danych. Innymi słowy Data Catalog jest łatwiejsze postrzeganie odnajdywania, zrozumieć i używać źródła danych i pomagając organizacjom pozwala zwiększyć wartość uzyskiwaną z ich istniejących danych. Po zarejestrowaniu źródła danych z usługą Data Catalog jego metadane jest kopiowana i indeksowane przez tę usługę, ale wątku nie kończy się. Data Catalog pozwala użytkownikom w celu zapewnienia ich własnych opisowymi metadanymi — takich jak opisy i znaczniki — stanowią uzupełnienie metadanych wyodrębnionych ze źródła danych i wprowadzić bardziej zrozumiały źródła danych większej liczbie użytkowników.

## <a name="annotation-and-crowdsourcing"></a>Adnotacja i crowdsourcing
Każdy ma opinii. I jest dobrym znakiem.
Wykaz danych rozpoznaje, że różni użytkownicy mają z różnych perspektyw w źródłach danych przedsiębiorstwa oraz czy każdy z tych perspektyw może być przydatne. Rozważmy następujący scenariusz:

* Administrator systemu zna Umowa dotycząca poziomu usług dla serwerów lub usług tego hosta źródła danych.
* Administrator bazy danych wie, że harmonogram tworzenia kopii zapasowych dla każdej bazy danych i windows dozwolonych przetwarzania ETL.
* Właściciela systemu zna procesu żądania dostępu do źródła danych przez użytkowników.
* Zarządca danych wie, jak zasoby i atrybuty w źródle danych mapy do modelu danych przedsiębiorstwa.
* Analityk wie, jak dane są używane w kontekście procesów biznesowych, które obsługują.

Każdy z tych perspektyw jest przydatne, a Data Catalog korzysta z użyciem podejścia crowdsourcing metadanych, który umożliwia każdej z nich może być przechwytywane i użycie go do udostępnienia pełny obraz zarejestrowanych źródeł danych. Korzystając z portalu usługi Data Catalog, każdy użytkownik mogą dodawać i edytować własne adnotacje, będąc mogli wyświetlać adnotacje dostarczone przez innych użytkowników.

## <a name="different-types-of-annotations"></a>Różne rodzaje adnotacji
Data Catalog obsługuje następujące typy adnotacji:

| Adnotacja | Uwagi |
| --- | --- |
| Przyjazna nazwa |Przyjazne nazwy mogą być dostarczane na poziomie zasobów danych, do zasobów danych, bardziej zrozumiały. Przyjazne nazwy są najbardziej przydatne, gdy nazwę obiektu bazowego jest one niezrozumiałe, skróconej lub w przeciwnym razie nie zrozumiałe dla użytkowników. |
| Opis |Opisy mogą być podawane w zasobie danych i atrybutach / poziomy kolumny. Opisy są krótki tekst w dowolnej postaci adnotacji, które opisują użytkownika perspektyw na zasobie danych lub jego użycia. |
| Tagi (tagi użytkownika) |Tagi mogą być podawane w zasobie danych i atrybutach / poziomy kolumny. Tagi użytkownika są zdefiniowane przez użytkownika etykiety, które mogą służyć do kategoryzowania atrybuty lub zasobów danych. |
| Tagi (Słownik znaczniki) |Tagi mogą być podawane w zasobie danych i atrybutach / poziomy kolumny. Tagi słownika są terminy słownika centralnego definiowania, które mogą służyć do kategoryzowania zasobów danych lub atrybutów przy użyciu wspólnej taksonomii biznesowej. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Konfigurowanie słownika biznesowego na potrzeby znakowania zarządzanego). |
| Eksperci |Eksperci mogą być dostarczane na poziomie zasobów danych. Eksperci zidentyfikować użytkowników lub grup mają specjalistyczne spojrzenie na dane i może służyć jako punkty kontaktu dla użytkowników, którzy Odnajdywanie źródeł danych zarejestrowanych i pytań, które nie odpowiedziano w istniejących adnotacji. |
| Żądaj dostępu |Informacje o żądaniu dostępu mogą być dostarczane na poziomie zasobów danych. Te informacje są dla użytkowników, którzy wykrywają, ich jeszcze nie masz uprawnień dostępu do źródła danych. Użytkownicy mogą wprowadź adres e-mail użytkownika lub grupy, która udziela dostępu do adresu URL procesu lub narzędzia, które użytkownicy muszą uzyskać dostęp, lub można wprowadzić go sam proces jako tekst. |
| Dokumentacja |Dokumentacja usługi mogą być dostarczane na poziomie zasobów danych. Dokumentacja zasobów jest informacji tekstu sformatowanego, który może zawierać linki i obrazy i która zapewnia wszystkie informacje, które nie są przekazywane za pośrednictwem opisy i tagi. |

## <a name="annotating-multiple-assets"></a>Dodawanie adnotacji do wielu zasobów
Po wybraniu wielu zasobów danych w portalu usługi Data Catalog, użytkownicy mogą dodawać adnotacje do wszystkich wybranych zasobów w ramach jednej operacji. Adnotacje będą stosowane do wszystkich wybranych zasobów, co ułatwia wybierz i podaj opis spójne i zestawy tagów i ekspertów dane dotyczące zasobów.

> [!NOTE]
> Znaczniki i ekspertów może być również dostarczona podczas rejestrowania zasoby danych przy użyciu danych wykazu danych źródłowych narzędzie do rejestracji.
>
>

Po wybraniu wiele tabel i widoków, tylko kolumny, czy wszystkie wybrane dane, które są wspólne dla zasobów będą wyświetlane w portalu usługi Data Catalog. Dzięki temu użytkownicy mogą podać tagów i opisów dla wszystkich kolumn z taką samą nazwę dla wszystkich wybranych zasobów.

## <a name="annotations-and-discovery"></a>Adnotacje i odnajdywania
Zgodnie z metadanych wyodrębnionych ze źródła danych podczas rejestracji jest dodawany do indeksu wyszukiwania wykazu danych, dostarczone przez użytkownika metadane są również indeksowane. To oznacza, że nie tylko adnotacje ułatwić użytkownikom zrozumienie, dane, które odkryje, adnotacje także ułatwiają użytkownikom odnajdywanie zasobów danych z adnotacjami wyszukiwanie przy użyciu warunków, które mają sens dla nich.

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródła danych z usługą Data Catalog sprawia, że wykrywalny tych danych przez skopiowanie metadane strukturalne i opisowy ze źródła danych do usługi katalogu. Po zarejestrowaniu źródła danych, użytkownicy mogą podać adnotacjami ułatwiają odnajdywanie i zrozumienie z w portalu usługi Data Catalog.

## <a name="see-also"></a>Zobacz także
* [Rozpoczynanie pracy z usługą Azure Data Catalog](data-catalog-get-started.md) samouczek krok po kroku szczegółowe informacje o tym, jak dodawać adnotacje do źródeł danych.
