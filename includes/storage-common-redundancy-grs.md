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
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399961"
---
Magazyn geograficznie nadmiarowy (GRS) pozwala uzyskać co najmniej 99,99999999999999% (16 9) trwałości obiektów w danym roku przez replikowanie danych do regionu pomocniczego, który znajduje się setki odległości od regionu podstawowego. Jeśli Twoje konto magazynu ma GRS włączone, dane są trwałe nawet w przypadku pełną, regionalnej niedostępności lub awarii, w którym regionie podstawowym nie jest możliwe do odzyskania.

Jeśli postanowisz magazynu grs, masz dwie powiązane opcje do wyboru:

* GRS replikuje dane do innego centrum danych w regionie pomocniczym, ale dane są dostępne do odczytu tylko jeśli firma Microsoft zainicjuje tryb failover z serwera podstawowego do regionu pomocniczego. 
* Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) jest oparty na GRS. RA-GRS replikuje dane do innego centrum danych w regionie pomocniczym, a także zapewnia możliwość odczytu z regionu pomocniczego. W RA-GRS mogą odczytywać z pomocniczych, niezależnie od tego, czy firma Microsoft zainicjuje tryb failover z serwera podstawowego do regionu pomocniczego. 

Dla konta magazynu przy użyciu GRS lub RA-GRS jest włączone wszystkie dane są najpierw replikowane z magazynem lokalnie nadmiarowym (LRS). Aktualizacja została zatwierdzona do lokalizacji głównej i replikowane z wykorzystaniem LRS. Aktualizacja jest następnie replikowane asynchronicznie w regionie pomocniczym, przy użyciu GRS. Dane są zapisywane do lokalizacji dodatkowej, jest również replikowana w tej lokalizacji przy użyciu magazynu LRS. 

Region podstawowy i pomocniczy zarządzania replik w oddzielnych domenach błędów i uaktualnień w jednostce skalowania magazynu. Jednostki skali magazynu jest jednostką podstawowej replikacji w centrum danych. Replikacja na tym poziomie jest świadczona przez Magazyn LRS; Aby uzyskać więcej informacji, zobacz [magazyn lokalnie nadmiarowy (LRS): nadmiarowość danych niedrogiej usługi Azure Storage](../articles/storage/common/storage-redundancy-lrs.md).

Podczas wybierania opcji replikacji, należy pamiętać o następujących punktów:

* Magazyn strefowo nadmiarowy (ZRS) oferuje o wysokiej dostępności przy użyciu replikacji synchronicznej i może być lepszym rozwiązaniem w przypadku niektórych scenariuszy niż GRS lub RA-GRS. Aby uzyskać więcej informacji na temat magazynu ZRS, zobacz [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Ponieważ Replikacja asynchroniczna wiąże się z opóźnieniem, w przypadku regionalnej awarii jest możliwe, że zmiany, które nie zostały jeszcze zreplikowane w regionie pomocniczym zostaną utracone, jeśli nie można odzyskać dane z regionu podstawowego.
* W przypadku magazynu GRS replika nie jest dostępny, chyba że firma Microsoft zainicjuje tryb failover do regionu pomocniczego. Jeśli Microsoft Zainicjuj tryb failover do regionu pomocniczego, użytkownik będzie po ich przeczytaniu i do zapisu danych po pracy w trybie failover zostało ukończone. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Jeśli Twoja aplikacja potrzebuje do odczytu z regionu pomocniczego, należy włączyć RA-GRS.

## <a name="read-access-geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy z dostępem do odczytu

Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) maksymalizuje dostępność konta magazynu. RA-GRS zapewnia dostęp tylko do odczytu do danych w lokalizacji pomocniczej, oprócz replikacji geograficznej w dwóch regionach.

Po włączeniu dostępu tylko do odczytu do danych w regionie pomocniczym, dane będą dostępne, pomocniczego punktu końcowego, a także podstawowego punktu końcowego konta magazynu. Pomocniczy punkt końcowy jest podobny do podstawowego punktu końcowego, ale dołącza sufiks `–secondary` do nazwy konta. Na przykład, jeśli jest podstawowego punktu końcowego usługi Blob `myaccount.blob.core.windows.net`, pomocniczego punktu końcowego jest `myaccount-secondary.blob.core.windows.net`. Klucze dostępu dla konta magazynu są takie same dla obu głównych i dodatkowych punktów końcowych.

Niektóre kwestie należy pamiętać, korzystając z RA-GRS:

* Twoja aplikacja ma zarządzać punktu końcowego, który prowadzi interakcję z, korzystając z RA-GRS.
* Ponieważ wiąże się opóźnienie replikacji asynchronicznej, zmian, które nie zostały jeszcze zreplikowane do regionu pomocniczego mogą zostać utracone, jeśli nie można odzyskać danych z regionu podstawowego, na przykład w przypadku regionalnej awarii.
* Możesz sprawdzić czas ostatniej synchronizacji konta magazynu. Czas ostatniej synchronizacji jest wartością daty/godziny GMT. Wszystkie zapisy głównej przed czas ostatniej synchronizacji zostały pomyślnie zapisane w lokalizacji dodatkowej, co oznacza, że są one dostępne do odczytu z lokalizacji pomocniczej. Podstawowy zapisuje po czas ostatniej synchronizacji może być lub może nie być dostępne dla odczytów jeszcze. Można tworzyć zapytania przy użyciu tej wartości [witryny Azure portal](https://portal.azure.com/), [programu Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), albo z jednego z biblioteki klienta usługi Azure Storage.
* Jeśli firma Microsoft zainicjuje tryb failover do regionu pomocniczego, użytkownik będzie po ich przeczytaniu i do zapisu danych po pracy w trybie failover zostało ukończone. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Aby uzyskać informacje na temat przełączyć się do regionu pomocniczego, zobacz [co należy zrobić po wystąpieniu awarii usługi Azure Storage](../articles/storage/common/storage-disaster-recovery-guidance.md).
* RA-GRS jest przeznaczona na potrzeby wysokiej dostępności. Aby uzyskać wskazówki dotyczące skalowalności, przejrzyj [Lista kontrolna dotycząca wydajności](../articles/storage/common/storage-performance-checklist.md).
* Aby uzyskać sugestie dotyczące projektowania w celu zapewnienia wysokiej dostępności przy użyciu RA-GRS, zobacz [projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](../articles/storage/common/storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co to jest cel punktu odzyskiwania i cel czasu odzyskiwania w przypadku magazynu GRS?
**Cel punktu odzyskiwania (RPO):** GRS i RA-GRS, magazyn usługi asynchronicznie geograficznej są replikowane dane z podstawowej do dodatkowej lokalizacji. W przypadku regionalnej awarii w regionie podstawowym firma Microsoft wykona przejściu w tryb failover do regionu pomocniczego. W przypadku przejścia w tryb failover najnowsze zmiany, które nie zostały jeszcze replikacją geograficzną mogą zostać utracone. Liczba minut potencjalnych utraconych danych jest określany jako cel punktu odzyskiwania i wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zazwyczaj ma RPO mniej niż 15 minut, mimo, że obecnie nie ma umowy SLA na jak długo geografickou replikaci przyjmuje.

**Cel czasu odzyskiwania (RTO):** czas RTO jest mierzony czas wykonywania pracy w trybie failover i pobrać konta magazynu z powrotem do trybu online. Moment na wykonanie pracy w trybie failover obejmuje następujące czynności:

   * Czas firma Microsoft wymaga, aby określić, czy dane można odzyskać w lokalizacji podstawowej lub przejścia w tryb failover jest konieczne.
   * Czas Przejdź w tryb failover konta magazynu, zmieniając głównej wpisy DNS, aby wskazywały do lokalizacji dodatkowej.

   Firma Microsoft podejmuje odpowiedzialność naszych użytkowników bardzo poważnie zachowania danych. W przypadku każdej okazji odzyskanie danych w regionie podstawowym, firma Microsoft będzie opóźnienie przełączenie w tryb failover i skoncentrować się na odzyskanie danych. Przyszłych wersjach usługi pozwoli Wyzwól tryb failover na poziomie konta, dzięki czemu możesz cel czasu odzyskiwania można kontrolować.

## <a name="paired-regions"></a>Sparowane regiony 

Podczas tworzenia konta magazynu, możesz wybrać region podstawowy dla konta. Sparowanym regionie pomocniczym jest określana na podstawie podstawowego regionu i nie można jej zmienić. Aby uzyskać aktualne informacje na temat regionów obsługiwanych przez platformę Azure, zobacz [firm ciągłości działania i odzyskiwania po awarii (BCDR): regiony sparowane platformy Azure](../articles/best-practices-availability-paired-regions.md).
