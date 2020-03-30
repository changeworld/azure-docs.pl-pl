---
title: Całkowity koszt posiadania (TCO) z usługą Azure Cosmos DB
description: W tym artykule porównano całkowity koszt posiadania usługi Azure Cosmos DB z usługą IaaS i lokalnymi bazami danych
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754788"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Całkowity koszt posiadania (TCO) z usługą Azure Cosmos DB

Usługa Azure Cosmos DB została zaprojektowana z myślą o precyzyjnej obsłudze wielu dzierżawców i zarządzania zasobami. Dzięki takiej konstrukcji korzystanie z usługi Azure Cosmos DB jest znacznie tańsze, a użytkownicy mogą zaoszczędzić pieniądze. Obecnie usługa Azure Cosmos DB obsługuje ponad 280 obciążeń klientów na pojedynczej maszynie z ciągle rosnącą gęstością oraz tysiące obciążeń klientów w ramach klastra. Równoważy ona repliki obciążeń klientów na różnych maszynach w klastrze oraz na wielu klastrach w centrum danych. Aby dowiedzieć się więcej, zobacz [Usługa Azure Cosmos DB: Przesuwanie granicy globalnie rozproszonych baz danych.](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/) Ze względu na zarządzanie zasobami, wielonajem i natywną integrację z resztą infrastruktury platformy Azure usługa Azure Cosmos DB jest średnio 4 do 6 razy tańsza niż MongoDB, Cassandra lub inny system OSS NoSQL uruchomiony na iaaS i do 10 razy tańszy niż baza danych silników działających w pomieszczeniach. Zobacz artykuł na [Temat Całkowity koszt (nie) własności usługi w chmurze bazy danych NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Rozwiązania bazy danych OSS NoSQL, takie jak Apache Cassandra, MongoDB, HBase, silniki zostały zaprojektowane do lokalnych. Gdy są oferowane jako usługa zarządzana są równoważne szablonu Menedżera zasobów z bazą danych dzierżawy do zarządzania aprowizacji klastrów i monitorowania obsługi. Architektury OSS NoSQL wymagają znacznych nakładów operacyjnych, a wiedza specjalistyczna może być trudna i kosztowna w znalezieniu. Z drugiej strony usługa Azure Cosmos DB to w pełni zarządzana usługa w chmurze, która umożliwia deweloperom skupienie się na innowacjach biznesowych, a nie na zarządzaniu i utrzymaniu infrastruktury bazy danych. 

W przeciwieństwie do usługi bazy danych natywnej dla chmury usługi Usługi Azure Cosmos DB, aparaty baz danych OSS NoSQL nie zostały zaprojektowane i zbudowane z zarządzaniem zasobami lub szczegółowym multi-dzierżawy jako podstawowe zasady architektury. Aparaty baz danych OSS NoSQL, takie jak Cassandra i MongoDB, zakładają, że wszystkie zasoby maszyny wirtualnej, na której są uruchomione, są dostępne do ich użycia. Wiele z tych aparatów bazy danych nie może działać, jeśli ilość zasobów spadnie poniżej pewnego progu. Na przykład dla małych wystąpień maszyn wirtualnych i są one dostępne z konfiguracji zalecanych przez dostawcę, sugerując zazwyczaj dużych maszyn wirtualnych o wyższych kosztach. Dlatego nie jest możliwe hostowanie OSS NoSQL lub innego lokalnego aparatu bazy danych i udostępnienie go przy użyciu modelu ładowania opartego na zużyciu, takiego jak żądania na sekundę lub zużytego magazynu.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Całkowity koszt posiadania usługi Azure Cosmos DB 

Model inicjowania obsługi administracyjnej bezserwerowej usługi Azure Cosmos DB eliminuje potrzebę nadmiernej aprowizacji infrastruktury bazy danych. Zasoby usługi Azure Cosmos DB są dostarczane bez konieczności konfigurowania wyspecjalizowanych konfiguracji lub licencjonowania. W rezultacie aplikacje wspierane przez usługę Azure Cosmos DB mogą działać z maksymalną wartością 70 procent Całkowity koszt oszczędności własności w porównaniu z bazami danych OSS NoSQL. Niektóre przykłady w czasie rzeczywistym można znaleźć w [przypadkach użycia klientów.](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc) Inne zalety modelu cenowego usługi Azure Cosmos DB obejmują:

* **Świetna cena:** Analitycy rynku, klienci i partnerzy potwierdzili większą wartość wszystkich funkcji oferowanych przez usługę Azure Cosmos DB za znacznie niższą cenę w porównaniu z tym, co klienci mogą uzyskać podczas wdrażania tych rozwiązań samodzielnie lub za pośrednictwem innych dostawców. Baza danych oferuje takie globalne modele dystrybucji, multi-master, dobrze zdefiniowane i intuicyjne, automatyczne indeksowanie są znacznie uproszczone z usługą Azure Cosmos DB bez żadnych złożoności, obciążenie lub przestoje.

* **Nie jest wymagana administracja DeZQL DevOps:** Dzięki usłudze Azure Cosmos DB nie trzeba wykorzystywać devops do zarządzania wdrożeniami, wykonywania konserwacji, skalowania lub wprowadzania poprawek. Można wykonać wszystkie obciążenia, które można wykonać z klastrem OSS NoSQL hostowanym lokalnie lub w infrastrukturze chmury.

![Koszt posiadania usługi Azure Cosmos DB](./media/total-cost-ownership/tco.png)

* **Zdolność do elastycznego skalowania:** Przepływność usługi Azure Cosmos DB można skalować w górę i w dół, co pozwala zmniejszyć koszt posiadania w godzinach poza szczytem. Klastry OSS NoSQL wdrożone w infrastrukturze chmury oferują ograniczoną elastyczność, a wdrożenia lokalne nie są elastyczne z definicji. W usłudze Azure Cosmos DB, jeśli aprowizować większą przepływność, przepływność jest gwarantowana do skalowania liniowo. Gwarancja ta jest poparta finansowymi łatwami i 99 percentylem w dowolnej skali.

* **Korzyści skali:** Usługa zarządzana, taka jak usługa Azure Cosmos DB, działa z dużą liczbą węzłów, zintegrowana natywnie z siecią, magazynem i obliczeniami. Ze względu na dużą skalę usługi Azure Cosmos DB, standaryzacja można zaoszczędzić koszty.

* **Zoptymalizowane pod kątem chmury:** Usługa Azure Cosmos DB została zaprojektowana od podstaw z precyzyjnieziarnistą wielonajem i izolacją wydajności. Pozwala to na optymalne umieszczanie, wykonywanie i równoważenie tysięcy dzierżawców i ich obciążeń w klastrach i centrach danych. Z drugiej strony bieżąca generacja baz danych OSS NoSQL działa lokalnie, a cała maszyna wirtualna zakłada, że uruchamia obciążenie pojedynczej dzierżawy. Te bazy danych nie są również przeznaczone do wykorzystania infrastruktury i sprzętu dostawcy chmury w pełnym zakresie. Na przykład aparat bazy danych OSS NoSQL nie jest świadomy różnic między maszyną wirtualną jest w dół vs rutynowego uaktualnienia obrazu lub fakt, że dysk premium jest już replikowane trójdrożnie. Nie może skorzystać z tych korzyści i przekazać korzyści i oszczędności klientom.

* **Płacisz za godzinę:** W przypadku obciążeń na dużą skalę, które muszą być skalowane w dowolnym momencie, są naliczane tylko przez godzinę. Obciążenia w aplikacji zazwyczaj różnią się w zależności od czasu w roku i przez dane, które są wyszukiwane. Dzięki usłudze Azure Cosmos DB możesz skalować w górę lub w dół zgodnie z potrzebami i płacić tylko za to, czego potrzebujesz. W systemach lokalnych lub hostowanych przez IaaS nie można dopasować tego modelu, ponieważ nie ma sposobu na wycofanie sprzętu co godzinę. W takich przypadkach można potencjalnie zapisać od 10 do 14 razy średnio z usługi Azure Cosmos DB.

* **Otrzymasz wiele funkcji za darmo:** W usłudze Azure Cosmos DB obciążenia zapisu są znacznie tańsze w porównaniu do usług alternatywnych baz danych. Ponadto usługa Azure Cosmos DB oferuje funkcje, takie jak [automatyczne indeksowanie,](indexing-policies.md) [czas wygaśnięcia (TTL),](time-to-live.md) [zmienianie kanału informacyjnego](change-feed.md) i inne bez żadnych dodatkowych opłat, co zwykle pobierają inne usługi bazy danych.

* **Używa ujednoliconej waluty dla różnych obciążeń:** W przeciwieństwie do alternatywnych ofert w usłudze Azure Cosmos DB nie trzeba segmentować obciążeń, na przykład w odczyty i zapisy. Lub aprowizuj przepływność dla typu obciążenia, który jest odczytywany przepływność vs przepływność zapisu. W usłudze Azure Cosmos DB aprowizowana przepływność jest zarezerwowana przy użyciu ujednoliconej i znormalizowanej waluty pod względem jednostek żądań lub usługi RU/s. Usługa Azure Cosmos DB nie zmusza użytkownika do przypisywania priorytetu do obciążeń, wykonywania planowania zdolności produkcyjnych lub płacenia za każdy typ pojemności oddzielnie. Takie podejście umożliwia łatwą wymianę tych samych ru/s między różnymi operacjami i typami obciążenia.

* **Nie wymaga inicjowania obsługi administracyjnej maszyn wirtualnych do skalowania:** Większość operacyjnych baz danych wymaga, aby przejść z dużych maszyn wirtualnych, aby uniknąć hałaśliwych sąsiadów i luźne zarządzanie zasobami, jeśli chcesz skali. To nakłada na klientów ciężar i początkowe zaangażowanie w koszty. Dzięki usłudze Azure Cosmos DB można rozpocząć małe i rozwijać się w dużych rozmiarach obciążenia bezproblemowo i bez żadnych przestojów lub wpływu na dostępność danych.

* **Można wykorzystać aprowizowaną przepływność do maksymalnego limitu:** Dzięki multipleksowaniu podrdzeniowym w usłudze Azure Cosmos DB można nasycić aprowizowaną przepływność w większym stopniu niż opcje hostowane usługi IaaS lub oferty innych firm. Ta metoda pozwala zaoszczędzić o wiele więcej niż alternatywne rozwiązania.

* **Głęboka integracja usługi Azure Cosmos DB z innymi usługami platformy Azure.** Usługa Azure Cosmos DB ma natywną integrację z usługami sieciowymi, obliczeniowymi, usługami platformy Azure (bezserwerowymi), azure ioT i innymi usługami platformy Azure. Dzięki tej integracji uzyskasz najlepszą wydajność i szybkość replikacji danych na całym świecie z solidnymi gwarancjami. Rozwiązania innych firm nie będą w stanie dopasować lub zazwyczaj pobierają premię za oferowanie takich funkcji.

* **Automatycznie otrzymujesz wysoką dostępność, domyślnie co najmniej 10-20 domen błędów:** Usługa Azure Cosmos DB obsługuje dystrybucję obciążeń między domenami błędów, funkcja, która ma kluczowe znaczenie dla wysokiej dostępności. Oferuje 99.999 wysoką dostępność dla odczytów i zapisuje na 99 percentyl w dowolnym miejscu na świecie. Koszt wdrożenia czegoś takiego na własną rękę lub za pośrednictwem rozwiązania innej firmy, byłby wysoki.

* **Automatycznie otrzymujesz wszystkie możliwości przedsiębiorstwa, bez dodatkowych kosztów.** Usługa Azure Cosmos DB oferuje najbardziej kompleksowy zestaw certyfikatów zgodności, zabezpieczeń i szyfrowania w spoczynku i w ruchu bez dodatkowych kosztów (w porównaniu z konkurencją). Automatycznie otrzymujesz regionalną dostępność w dowolnym miejscu na świecie. Baza danych można obejmować w dowolnej liczbie regionów platformy Azure i dodawać lub usuwać regiony w dowolnym momencie.

* **Możesz zaoszczędzić do 65% kosztów dzięki zarezerwowanej pojemności:** [Pojemność zarezerwowana](cosmos-db-reserved-capacity.md) usługi Azure Cosmos DB pomaga zaoszczędzić pieniądze, płacąc z góry za zasoby usługi Azure Cosmos DB przez rok lub trzy lata. Możesz znacznie obniżyć koszty dzięki rocznym lub trzyletnim zobowiązaniom z góry i zaoszczędzić od 20 do 65% rabatów w porównaniu do regularnych cen. W przypadku obciążeń o znaczeniu krytycznym można uzyskać lepsze ula pod względem zdolności inicjowania obsługi administracyjnej.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tym, jak model cenowy usługi Azure Cosmos DB jest opłacalny dla klientów](total-cost-ownership.md)
* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów odczytów i zapisów](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont cosmosu wieloregionowego](optimize-cost-regions.md)
* Dowiedz się więcej o [całkowitym koszcie (nie) własności usługi w chmurze bazy danych NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
