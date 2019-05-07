---
title: Magazyn geograficznie nadmiarowy (GRS) w celu zapewnienia trwałości między regionami w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Magazyn geograficznie nadmiarowy (GRS) replikuje dane między dwoma regionami oddalonymi o setki mil od siebie. Magazyn GRS zapewnia ochronę przed awariami sprzętu w centrum danych, a także regionalnej awarii.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 09b553f3ca64d8f5217f023c776ec848215366f9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150994"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Magazyn geograficznie nadmiarowy (GRS): Replikacji między regionami dla usługi Azure Storage
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy z dostępem do odczytu
Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS) maksymalizuje dostępność konta magazynu. RA-GRS zapewnia dostęp tylko do odczytu do danych w lokalizacji pomocniczej, oprócz replikacji geograficznej w dwóch regionach.

Po włączeniu dostępu tylko do odczytu do danych w regionie pomocniczym, dane będą dostępne, pomocniczego punktu końcowego, a także podstawowego punktu końcowego konta magazynu. Pomocniczy punkt końcowy jest podobny do podstawowego punktu końcowego, ale dołącza sufiks `–secondary` do nazwy konta. Na przykład, jeśli jest podstawowego punktu końcowego usługi Blob `myaccount.blob.core.windows.net`, pomocniczego punktu końcowego jest `myaccount-secondary.blob.core.windows.net`. Klucze dostępu dla konta magazynu są takie same dla obu głównych i dodatkowych punktów końcowych.

Niektóre kwestie należy pamiętać, korzystając z RA-GRS:

* Twoja aplikacja ma zarządzać punktu końcowego, który prowadzi interakcję z, korzystając z RA-GRS.
* Ponieważ wiąże się opóźnienie replikacji asynchronicznej, zmian, które nie zostały jeszcze zreplikowane do regionu pomocniczego mogą zostać utracone, jeśli nie można odzyskać danych z regionu podstawowego.
* Możesz sprawdzić czas ostatniej synchronizacji konta magazynu. Czas ostatniej synchronizacji jest wartością daty/godziny GMT. Wszystkie zapisy głównej przed czas ostatniej synchronizacji zostały pomyślnie zapisane w lokalizacji dodatkowej, co oznacza, że są one dostępne do odczytu z lokalizacji pomocniczej. Podstawowy zapisuje po czas ostatniej synchronizacji może być lub może nie być dostępne dla odczytów jeszcze. Można tworzyć zapytania przy użyciu tej wartości [witryny Azure portal](https://portal.azure.com/), [programu Azure PowerShell](storage-powershell-guide-full.md), albo z jednego z biblioteki klienta usługi Azure Storage.
* Jeśli zainicjujesz konta pracy w trybie failover (wersja zapoznawcza) konta GRS lub RA-GRS w regionie pomocniczym, dostęp do zapisu do tego konta zostanie przywrócony po zakończeniu pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [awaryjnego odzyskiwania i przechowywania konta pracy awaryjnej (wersja zapoznawcza)](storage-disaster-recovery-guidance.md).
* RA-GRS jest przeznaczona na potrzeby wysokiej dostępności. Aby uzyskać wskazówki dotyczące skalowalności, przejrzyj [Lista kontrolna dotycząca wydajności](storage-performance-checklist.md).
* Aby uzyskać sugestie dotyczące projektowania w celu zapewnienia wysokiej dostępności przy użyciu RA-GRS, zobacz [projektowanie wysoko dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co to jest cel punktu odzyskiwania i cel czasu odzyskiwania w przypadku magazynu GRS?

**Cel punktu odzyskiwania (RPO):** GRS i RA-GRS magazyn usługi asynchronicznie geograficznej są replikowane dane z podstawowej do dodatkowej lokalizacji. W przypadku, gdy region podstawowy staje się niedostępny, można wykonać failover konta (wersja zapoznawcza) do regionu pomocniczego. Po zainicjowaniu przejścia w tryb failover ostatnie zmiany, które nie zostały jeszcze replikacją geograficzną mogą zostać utracone. Liczba minut na potencjalne danych, który utracił jest określany jako cel punktu odzyskiwania. Cel punktu odzyskiwania wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zazwyczaj ma RPO mniej niż 15 minut, mimo, że obecnie nie ma umowy SLA na jak długo geografickou replikaci przyjmuje.

**Cel czasu odzyskiwania (RTO):** Cel czasu odzyskiwania jest miarą jak długo trwa Przejdź w tryb failover i pobrać konta magazynu z powrotem do trybu online. Moment na wykonanie pracy w trybie failover obejmuje następujące czynności:

   * Czas do momentu klienta przełączenia w tryb failover konta magazynu z serwera podstawowego do regionu pomocniczego.
   * Czas wymagany przez platformę Azure do wykonania pracy w trybie failover, zmieniając głównej wpisy DNS, aby wskazywały do lokalizacji dodatkowej.

## <a name="paired-regions"></a>Sparowane regiony 
Podczas tworzenia konta magazynu, możesz wybrać region podstawowy dla konta. Sparowanym regionie pomocniczym jest określana na podstawie podstawowego regionu i nie można jej zmienić. Aby uzyskać aktualne informacje na temat regionów obsługiwanych przez platformę Azure, zobacz [firm ciągłości działania i odzyskiwania po awarii (BCDR): Sparowanych regionów platformy Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Zobacz także
- [Replikacja usługi Azure Storage](storage-redundancy.md)
- [Magazyn lokalnie nadmiarowy (LRS): Nadmiarowość danych niedrogiej usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS): Aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md)
