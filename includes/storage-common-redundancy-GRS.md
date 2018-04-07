---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: f5b6e0e74bbab33b9ae6fbacca5c55ea434d3e41
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
Magazyn geograficznie nadmiarowy (GRS) ma na celu zapewnienie co najmniej 99.99999999999999% (16 na 9) trwałość obiektów w danym roku przez replikowanie danych w regionie pomocniczym będący setki odległości od regionu podstawowego. Jeśli konto magazynu ma GRS włączone, dane są trwałe nawet w przypadku pełnej regionalnej awarii lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania.

Jeśli wybrać grs w warstwie dostępne są dwa powiązane opcje do wyboru:

* Grs w warstwie replikuje dane do innego centrum danych w regionie pomocniczym, jednak, że dane są dostępne do odczytu tylko jeśli Microsoft zainicjuje tryb failover z serwera podstawowego w regionie pomocniczym. 
* Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) jest oparty na GRS. RA-GRS są replikowane do innego regionu pomocniczego centrum danych i zapewnia także opcję, aby odczytać z regionu pomocniczego. Z RA-GRS można czytać z lokacji dodatkowej, niezależnie od tego, czy Microsoft inicjuje trybu failover z serwera podstawowego na serwerze pomocniczym. 

Dla konta magazynu z GRS lub RA-GRS włączone wszystkie dane są replikowane najpierw z magazyn lokalnie nadmiarowy (LRS). Aktualizacja najpierw został przekazany do lokalizacji głównej i replikowane z wykorzystaniem LRS. Aktualizacja jest następnie replikowane asynchronicznie w regionie pomocniczym przy użyciu GRS. Gdy dane są zapisywane do lokalizacji dodatkowej, również są replikowane w tej lokalizacji przy użyciu LRS. 

Regiony podstawowych i pomocniczych zarządzania replik w domenach awarii oddzielne i uaktualnienia domen w obrębie jednostki skalowania magazynu. Jednostka skalowania magazynu to jednostka podstawowej replikacji w centrum danych. Replikacja na tym poziomie jest zapewniana przez LRS; Aby uzyskać więcej informacji, zobacz [magazyn lokalnie nadmiarowy (LRS): niedrogich nadmiarowość danych usługi Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Podczas wybierania opcji replikacji należy pamiętać o tych punktów:

* Magazyn strefowo nadmiarowy (ZRS) zapewnia wysoką dostępność Replikacja synchroniczna i może być lepszym rozwiązaniem dla niektórych scenariuszy niż GRS lub RA-GRS. Aby uzyskać więcej informacji o ZRS, zobacz [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Ponieważ asynchroniczną replikację obejmuje opóźnienia, w przypadku regionalnej awarii jest możliwe, że zmiany, które nie zostały jeszcze zreplikowane w regionie pomocniczym zostaną utracone, jeśli nie można odzyskać dane z regionu podstawowego.
* W wypadku magazynu GRS replika nie jest dostępna, chyba że Microsoft inicjuje trybu failover w regionie pomocniczym. Jeśli Microsoft Zainicjuj tryb failover w regionie pomocniczym, będą mieć odczytu i zapisu do tych danych po przejściu w tryb failover została ukończona. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Jeśli aplikacja wymaga do odczytu z regionu pomocniczego, należy włączyć RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Dostęp do odczytu magazynu geograficznie nadmiarowego

Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) maksymalizację dostępności dla konta magazynu. RA-GRS zapewnia dostęp tylko do odczytu do danych w dodatkowej lokalizacji, oprócz — replikacja geograficzna dwóch regionach.

Po włączeniu dostępu tylko do odczytu do danych w regionie pomocniczym, dane są dostępne na dodatkowej punktu końcowego, a także podstawowy punkt końcowy dla konta magazynu. Pomocniczy punkt końcowy jest podobny do podstawowego punktu końcowego, ale dołącza sufiks `–secondary` do nazwy konta. Na przykład, jeśli jest podstawowym punktu końcowego usługi Blob `myaccount.blob.core.windows.net`, to jest punkt końcowy dodatkowej `myaccount-secondary.blob.core.windows.net`. Klawisze dostępu dla konta magazynu są takie same dla obu głównych i dodatkowych punktów końcowych.

Niektóre kwestie należy wziąć pod uwagę w przypadku korzystania z RA-GRS:

* Aplikacja musi zarządzać którym punktem końcowym, używając RA-GRS prowadzi interakcję z.
* Ponieważ asynchroniczną replikację obejmuje opóźnienia, zmiany, które nie zostały jeszcze zreplikowane w regionie pomocniczym mogą zostać utracone, jeśli nie można odzyskać danych z regionu podstawowego, na przykład w przypadku regionalnej awarii.
* Można sprawdzić czas ostatniej synchronizacji z konta magazynu. Czas ostatniej synchronizacji jest wartość daty/godziny GMT. Wszystkie zapisy głównej przed czas ostatniej synchronizacji zostały pomyślnie zapisane w lokalizacji dodatkowej, co oznacza, że są one dostępne do odczytu z lokalizacji dodatkowej. Podstawowy zapisuje po czas ostatniej synchronizacji może lub mogą nie być dostępne dla odczytów jeszcze. Można badać przy użyciu tej wartości [portalu Azure](https://portal.azure.com/), [programu Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), albo z jednego z biblioteki klienta magazynu Azure.
* Jeśli Microsoft inicjuje trybu failover w regionie pomocniczym, będą mieć odczytu i zapisu do tych danych po przejściu w tryb failover została ukończona. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Aby uzyskać informacje na temat przełączyć się do regionu pomocniczego, zobacz [co robić w przypadku wystąpienia awarii usługi Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS jest przeznaczony dla celów wysokiej dostępności. Wskazówki dotyczące skalowalności, można przejrzeć [Lista kontrolna wydajności](../articles/storage/common/storage-performance-checklist.md).
* Masz sugestie dotyczące projektowania wysokiej dostępności z RA-GRS, zobacz [Projektowanie wysoce dostępnych aplikacji przy użyciu magazynu RA-GRS](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co to jest RPO i RTO z GRS?
**Cel punktu odzyskiwania (RPO):** w GRS i RA-GRS, magazyn usługi asynchronicznie geograficznie są replikowane dane z serwera podstawowego do lokalizacji dodatkowej. W przypadku poważnej awarii regionalnych w regionie podstawowym Microsoft wykonuje trybu failover w regionie pomocniczym. W przypadku trybu failover najnowszych zmian, które nie zostały jeszcze replikacją geograficzną mogą zostać utracone. Liczba minut potencjalnych utraconych danych jest określana jako cel punktu odzyskiwania i wskazuje punkt w czasie, do którego można odzyskać dane. Magazyn Azure zazwyczaj ma RPO mniej niż 15 minut, mimo że nie ma żadnych SLA na jak długo replikacja geograficzna przyjmuje.

**Celu czasu odzyskiwania (RTO):** Docelowy jest miarą czas pracy w trybie failover i konto magazynu do trybu online. Czas pracy w trybie failover zawiera następujące akcje:

   * Czas firmy Microsoft wymaga, aby określić, czy dane można odzyskać w lokalizacji głównej, czy tryb failover jest konieczne.
   * Czas pracy w trybie failover konta magazynu, zmieniając głównej wpisy DNS, aby wskazać lokalizację dodatkowej.

   Microsoft przyjmuje odpowiedzialność poważnie zachowania danych. Jeśli jakakolwiek możliwość odzyskania danych w regionie podstawowym, firma Microsoft będzie opóźnienie pracy awaryjnej i skupić się na odzyskanie danych. Przyszłych wersji usługi pozwoli wyzwolić tryb failover na poziomie konta, aby użytkownik Docelowy można kontrolować.

## <a name="paired-regions"></a>Sparowanego regionów 

Po utworzeniu konta magazynu, należy wybrać regionie podstawowym konta. Sparowanego region pomocniczy jest określany na podstawie regionu podstawowego i nie można zmienić. Aby uzyskać aktualne informacje o obsługiwane przez platformę Azure, zobacz [firm ciągłości i odzyskiwanie po awarii (BCDR): łączyć regiony platformy Azure](../articles/best-practices-availability-paired-regions.md).
