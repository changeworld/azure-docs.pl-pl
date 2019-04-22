---
title: Łączny koszt Ownership(TCO) przy użyciu usługi Azure Cosmos DB
description: W tym artykule porównano całkowity koszt posiadania w usłudze Azure Cosmos DB z rozwiązania IaaS i lokalnych baz danych
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c3a3305197802906077dab330a6b51c1195c6c36
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879475"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Łączny koszt Ownership(TCO) przy użyciu usługi Azure Cosmos DB

Usługa Azure Cosmos DB zaprojektowano ład szczegółowe wielodostępu i zasobów. Ten projekt umożliwia usłudze Azure Cosmos DB działa znacznie niższe koszty i pomagać użytkownikom, Zapisz. Obecnie usługi Azure Cosmos DB obsługuje ponad 280 obciążeń klientów na jednym komputerze z gęstość ciągłe zwiększanie i tysiące obciążeń klientów w ramach klastra. Jego równoważy replik obciążeń klientów na różnych komputerach w klastrze i w wielu klastrach w obrębie centrum danych, aby dowiedzieć się więcej, zobacz [usługi Azure Cosmos DB: Wypychanie granicy globalnie rozproszonych baz danych](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Ze względu na zarządzanie zasobami, wielodostępu i natywnej integracji z pozostałą częścią infrastruktury platformy Azure Azure Cosmos DB jest średnio 4 do 6 razy tańsze niż bazy danych MongoDB, Cassandra lub innych NoSQL OSS uruchomioną w usłudze IaaS i maksymalnie 10 razy tańsze niż bazy danych aparaty lokalnych. Zobacz dokument na [całkowity koszt posiadania (nie), usługi w chmurze baza danych NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

Rozwiązania bazy danych typu NoSQL, takich jak Apache Cassandra, MongoDB, HBase, aparaty zostały zaprojektowane dla środowiska lokalnego. Gdy są one równoważne do szablonu usługi Resource Manager z bazą danych dzierżawy do monitorowania pomocy technicznej i zarządzania klastrami elastycznie oferowana jako usługa zarządzana. Architektury OSS NoSQL wymagają znaczne koszty operacyjne i wiedzę, może być trudne i kosztowna można znaleźć. Z drugiej strony usługi Azure Cosmos DB to usługa w pełni zarządzana usługa w chmurze, co pozwala deweloperom skupić się na innowacjach firmy, a nie na zarządzanie i obsługa infrastruktury bazy danych. 

W przeciwieństwie do bazy danych natywnych dla chmury usługi Azure Cosmos DB baz danych OSS NoSQL nie zostały zaprojektowany i zbudowany z nadzór nad zasobami lub szczegółowych wielodostępu jako podstawowe zasady architektury. Aparatów baz danych typu NoSQL, takie jak Cassandra i bazy danych MongoDB, że podstawowe założenie, że wszystkie zasoby maszyny wirtualnej, na którym są uruchomione są dostępne do użytku. Wiele z tych baz danych nie może działać, jeśli ilość zasobów spadnie poniżej określonego progu. Na przykład mała maszyna wirtualna wystąpienia i ich dostępnych przy użyciu zalecane przez dostawcę konfiguracji, sugerując zazwyczaj na dużą skalę maszyny wirtualne z wyższe koszty. Tak nie jest możliwe do hostowania NoSQL typu lub wszelkich innych lokalnych aparat bazy danych i udostępnić go przy użyciu modelu ładowania na podstawie użycia takich jak żądania na drugi lub użyte magazynu.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Całkowity koszt posiadania w usłudze Azure Cosmos DB 

Bez użycia serwera modelu aprowizacji usługi Azure Cosmos DB eliminuje potrzebę nadmiernej aprowizacji infrastruktury bazy danych. Zasoby platformy Azure Cosmos DB są podane bez potrzeby specjalistyczne konfiguracje lub licencjonowania. W rezultacie aplikacji opartych na usłudze Azure Cosmos DB można uruchomić z możliwie jak 70 procent całkowitego kosztu posiadania oszczędności w porównaniu do bazy danych OSS NoSQL. W czasie rzeczywistym znajdują się przykłady, zobacz [przypadki użycia klienta](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Inne korzyści model cen usługi Azure Cosmos DB to m.in.:

* **Cenną funkcję w cenie:** Analitycy na rynku, klientów i partnerów potwierdzenia większą wartość ze wszystkich funkcji oferowanych przez usługę Azure Cosmos DB dla znacznie niższa cena w porównaniu do czego klienci mogą uzyskać podczas implementowania tych rozwiązań, własnych lub za pośrednictwem innych dostawców. Funkcje bazy danych, takie dystrybucji globalnej, modele spójności wielu wzorców, dobrze zdefiniowany i intuicyjne, automatycznego indeksowania znacznie są uproszczone dzięki usłudze Azure Cosmos DB bez złożoności, obciążenie, lub przestojów.

* **Wymagane jest nie administracji NoSQL DevOps:** Usługa Azure Cosmos DB jeden nie trzeba stosować metodyki DevOps, aby zarządzać wdrożeniami, przeprowadzenia konserwacji, skalowania lub poprawki. Istnieje możliwość wykonania wszystkich obciążeń, które należy wykonać przy użyciu OSS NoSQL klastra hostowanych lokalnie lub w infrastrukturze chmury.

![Koszt posiadania w usłudze Azure Cosmos DB](./media/total-cost-ownership/tco.png)

* **Możliwość elastycznego skalowania:** Przepływność usługi Azure Cosmos DB może być skalowana w górę i w dół, co pozwala zmniejszyć koszt posiadania okresach poza szczytem. Klastry typu NoSQL, wdrożone w infrastrukturze chmury oferują elastyczność ograniczone i wdrożeń lokalnych nie są elastyczne, zgodnie z definicją. W usłudze Azure Cosmos DB Jeśli aprowizować więcej przepływności przepływność jest gwarantowane skalowane liniowo. Gwarancja ta kopia zapasowa jest tworzona przez finansowe umowy SLA i w 99. percentylu w dowolnej skali.

* **Oszczędności skali:** Zarządzana usługa, takich jak usługi Azure Cosmos DB działa z dużą liczbą węzłów, natywnie zintegrowane z sieci, magazynu i jednostki obliczeniowe. Ze względu na usłudze Azure Cosmos DB dużej skali i normalizacji możliwe obniżenie kosztów.

* **Optymalizacja na potrzeby chmury:** Usługa Azure Cosmos DB została zaprojektowana od podstaw przy użyciu szczegółowych izolacji wielu dzierżawców i wydajności. Umożliwia to umieszczenie optymalnie, wykonywaniu i równoważenie tysiące dzierżawcy i ich obciążeń klastrów i centrów danych. Z kolei bieżącej generacji bazy danych OSS NoSQL działają w środowisku lokalnym za pomocą całą maszynę wirtualną, przyjmuje się, że uruchamianie obciążenia pojedynczej dzierżawy. Te bazy danych także nie są przeznaczone na wykorzystanie infrastruktury i sprzętu w pełnym zakresie dostawcy chmury. Na przykład uaktualnić rutynowych obraz NoSQL OSS aparatu bazy danych nie jest świadomy różnic między maszyny wirtualnej trwa szczegółów programu Vs lub fakt ten dysk w warstwie premium jest już trzy kierunkową replikowane. Nie mogą korzystać z zalet tych korzyści i przekazać korzyści i oszczędności dla klientów.

* **Opłaty są naliczane za godzinę:** W przypadku na dużą skalę obciążeń wymagających skalowania w dowolnym momencie w czasie, opłaty są naliczane tylko za godzinę. Obciążenia aplikacji różni się zwykle w razy w roku i na podstawie danych, którego dotyczy kwerenda. Za pomocą usługi Azure Cosmos DB można skalować w górę lub w dół zgodnie z potrzebami i płać tylko za potrzebne. Ze środowiska lokalnego lub hostowanego w modelu IaaS systemów nie może dopasować ten model, ponieważ nie istnieje sposób do likwidacji sprzętu co godzinę. W takich przypadkach można potencjalnie zapisać od 10 do 14 razy średnio w usłudze Azure Cosmos DB.

* **Bezpłatnie Uzyskaj wiele funkcji:** W usłudze Azure Cosmos DB zapisać obciążenia są znacznie tańsze w porównaniu do alternatywnej bazy danych usług. Ponadto usługi Azure Cosmos DB oferuje funkcje, takie jak takich jak [automatycznego indeksowania](indexing-policies.md), [czas wygaśnięcia (TTL)](time-to-live.md), [kanału informacyjnego zmian](change-feed.md) i innym osobom bez żadnych dodatkowych kosztów coś, co inne usługi bazy danych zazwyczaj pobierają opłaty.

* **Zastosowań unified walutę dla różnych obciążeniach:** W przeciwieństwie do szerokiej oferty w usłudze Azure Cosmos DB nie trzeba obciążeń segmentu, na przykład do operacji odczytu i zapisu. Lub aprowizowanie przepływności na na typ obciążenia, który jest wczytywany przepływność a przepływność zapisu. W usłudze Azure Cosmos DB aprowizowana przepływność jest zarezerwowana, ujednolicone i znormalizowane waluty pod względem jednostek żądań lub jednostek RU/s. usługi Azure Cosmos DB nie wymusza Przypisz priorytet do Twoich obciążeń, planowania pojemności lub płatność dla każdego typu pojemności oddzielnie. Takie podejście umożliwia łatwe wymiany tych samych jednostek RU/s, między różnymi operacji i typów obciążeń.

* **Nie wymagaj inicjowania obsługi administracyjnej maszyn wirtualnych w celu skalowania:** Większość operacyjnych baz danych wymagają przejścia z dużych maszyn wirtualnych, aby uniknąć zakłócenia sąsiadów i utracić wprowadzone nadzór nad zasobami, jeśli chcesz, aby skali. Spowoduje to umieszczenie obciążenia i zobowiązań z góry kosztów do klientów. Za pomocą usługi Azure Cosmos DB można zacząć od małej i przejść do rozmiary obciążeń dużej skali bezproblemowo oraz bez przestojów ani wpływu na dostępność danych.

* **Korzystanie z aprowizowaną przepływność na maksymalny limit:** Mocy podrzędnych podstawowe funkcje multipleksowania w usłudze Azure Cosmos DB można saturate aprowizowaną przepływność w większym stopniu niż IaaS hostowanych opcji lub ofert innych firm. Ta metoda zapisuje dużo więcej niż alternatywne rozwiązania.

* **Głęboka Integracja usługi Azure Cosmos DB z innymi usługami Azure.** Usługa Azure Cosmos DB ma natywnej integracji z siecią, obliczeniowych, (bez użycia serwera) w usłudze Azure Functions, Azure IoT i innych usług platformy Azure. Integracja ta zapewnia najlepszą wydajność, szybkość replikacji danych na całym świecie przy użyciu gwarantuje niezawodne. Rozwiązania innych firm nie będzie mógł odpowiadać lub będzie zazwyczaj pobierają opłaty premium oferować takie funkcje.

* **Automatycznie uzyskują wysokiej dostępności, z co najmniej 10-20 domen błędów, domyślnie:** Usługa Azure Cosmos DB obsługuje dystrybucji obciążeń w domenach błędów, funkcji, który ma kluczowe znaczenie w celu zapewnienia wysokiej dostępności. On oferuje 99.999 wysoką dostępność dla operacji odczytu i zapisu w 99. percentylu, w dowolnym miejscu na świecie. Kosztów wdrożenia podobny do poniższego, samodzielnie lub za pośrednictwem rozwiązania innych firm, będzie wysoka.

* **Automatycznie uzyskujesz wszystkie możliwości dla przedsiębiorstw, bez ponoszenia dodatkowych kosztów.** Usługa Azure Cosmos DB oferuje najbardziej kompleksowy zbiór związane ze zgodnością, bezpieczeństwo i szyfrowanie w spoczynku, jak i w ruchu bez ponoszenia dodatkowych kosztów (w porównaniu z naszą konkurencję). Automatycznie uzyskasz dostępności regionalnej w dowolnym miejscu na świecie. Może obejmować bazę danych w dowolnej liczbie regionów świadczenia usługi Azure i dodawanie lub usuwanie regionów, w dowolnym momencie.

* **Rezerwowanie pojemności można zapisać do 65% kosztów:** Usługa Azure Cosmos DB [wydajności rezerwowej](cosmos-db-reserved-capacity.md) pomaga zaoszczędzić płacąc wstępnie dla zasobów usługi Azure Cosmos DB za rok lub trzy lata. Można znacznie zmniejszyć koszty przy użyciu jednego roku lub trzech lat ponoszonych z góry zobowiązań i zapisać między 20 65% zniżki w porównaniu do normalnej ceny. Na obciążeń o znaczeniu krytycznym możesz uzyskać lepsze umowy SLA pod kątem aprowizacji pojemności.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [jak usługi Azure Cosmos DB modelu cen jest opłacalna dla klientów](total-cost-ownership.md)
* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji koszt odczyty i zapisy](optimize-cost-reads-writes.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów multiregionalne konta usługi Cosmos](optimize-cost-regions.md)
* Dowiedz się więcej o [całkowity koszt posiadania (inne niż), usługi w chmurze baza danych NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
