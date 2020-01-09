---
title: Magazyn Geograficznie nadmiarowy (GRS) na potrzeby trwałości między regionami
titleSuffix: Azure Storage
description: Magazyn Geograficznie nadmiarowy (GRS) replikuje dane między dwoma regionami oddalonymi o setki kilometrów. GRS chroni przed awariami sprzętu w centrum danych, a także regionalnymi awariami.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 6bb93c3fb6599a05978e11ef5fbc179ccfaa9ec2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614901"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>Magazyn Geograficznie nadmiarowy (GRS): replikacja w wielu regionach dla usługi Azure Storage

[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>Magazyn geograficznie nadmiarowy dostępny do odczytu

Magazyn Geograficznie nadmiarowy dostępny do odczytu (RA-GRS) maksymalizuje dostępność dla konta magazynu. RA-GRS zapewnia dostęp tylko do odczytu do danych w lokalizacji pomocniczej, a także do replikacji geograficznej w dwóch regionach.

Po włączeniu dostępu tylko do odczytu do danych w regionie pomocniczym dane są dostępne w pomocniczym punkcie końcowym, a także w podstawowym punkcie końcowym dla konta magazynu. Pomocniczy punkt końcowy jest podobny do podstawowego punktu końcowego, ale dołącza sufiks `–secondary` do nazwy konta. Na przykład jeśli podstawowy punkt końcowy Blob service jest `myaccount.blob.core.windows.net`, zostanie `myaccount-secondary.blob.core.windows.net`pomocniczy punkt końcowy. Klucze dostępu dla konta magazynu są takie same dla podstawowych i pomocniczych punktów końcowych.

Zagadnienia, które należy wziąć pod uwagę podczas korzystania z usługi RA-GRS:

- Aplikacja musi zarządzać tym, z którym punktem końcowym korzysta podczas korzystania z usługi RA-GRS.
- Ponieważ replikacja asynchroniczna obejmuje opóźnienie, zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego, mogą zostać utracone, jeśli nie można odzyskać danych z regionu podstawowego.
- Możesz sprawdzić Właściwość **godzina ostatniej synchronizacji** dla konta magazynu. **Czas ostatniej synchronizacji** to wartość daty/godziny GMT. Wszystkie zapisy podstawowe wykonane przed upływem **ostatniej synchronizacji** zostały pomyślnie zapisany w lokalizacji dodatkowej, co oznacza, że są dostępne do odczytu z lokalizacji dodatkowej. Zapisy podstawowe po **ostatniej synchronizacji** mogą być jeszcze niedostępne dla operacji odczytu. Można wysłać zapytanie do tej wartości przy użyciu programu PowerShell, interfejsu wiersza polecenia platformy Azure lub jednej z bibliotek klienta usługi Azure Storage. Aby uzyskać więcej informacji, zobacz **pobieranie czasu ostatniej synchronizacji** w [projektowaniu aplikacji o wysokiej dostępności przy użyciu magazynu geograficznie nadmiarowego do odczytu](storage-designing-ha-apps-with-ragrs.md#getting-the-last-sync-time).
- Jeśli zainicjujesz tryb failover konta (wersja zapoznawcza) konta GRS lub RA-GRS do regionu pomocniczego, dostęp do zapisu do tego konta zostanie przywrócony po zakończeniu pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [odzyskiwanie po awarii i tryb failover konta magazynu (wersja zapoznawcza)](storage-disaster-recovery-guidance.md).
- RA-GRS jest przeznaczony do celów o wysokiej dostępności. Aby uzyskać wskazówki dotyczące skalowalności, przejrzyj [listę kontrolną wydajności](storage-performance-checklist.md).
- Aby uzyskać informacje na temat sposobu projektowania pod kątem wysokiej dostępności za pomocą usługi RA-GRS, zobacz [projektowanie wysoce dostępnych aplikacji przy użyciu magazynu RA-GRS](storage-designing-ha-apps-with-ragrs.md).

## <a name="what-is-the-rpo-and-rto-with-grs"></a>Co to jest cel punktu odzyskiwania i RTO z GRS?

**Cel punktu odzyskiwania (RPO):** W GRS i RA-GRS usługa magazynu asynchronicznie replikuje dane z lokalizacji podstawowej do lokacji dodatkowej. W przypadku niedostępności regionu podstawowego można wykonać konto w trybie failover (wersja zapoznawcza) do regionu pomocniczego. Po zainicjowaniu trybu failover ostatnie zmiany, które nie zostały jeszcze zreplikowane geograficznie, mogą zostać utracone. Liczba minut ewentualnych utraconych danych jest określana jako cel punktu odzyskiwania. Cel punktu odzyskiwania wskazuje punkt w czasie, do którego można odzyskać dane. Usługa Azure Storage zazwyczaj ma cel punktu odzyskiwania krótszy niż 15 minut, chociaż obecnie nie ma umowy SLA dotyczącej czasu trwania replikacji geograficznej.

**Cel czasu odzyskiwania (RTO):** RTO to miara czasu potrzebnego na przełączenie w tryb failover i przywrócenie konta magazynu z powrotem do trybu online. Czas do przeprowadzenia przejścia w tryb failover obejmuje następujące akcje:

- Czas do momentu, gdy Klient zainicjuje przejście w tryb failover konta magazynu z podstawowego do regionu pomocniczego.
- Czas wymagany przez platformę Azure do przeprowadzenia przejścia w tryb failover przez zmianę podstawowych wpisów DNS w taki sposób, aby wskazywała lokalizację dodatkową.

## <a name="paired-regions"></a>Sparowane regiony

Podczas tworzenia konta magazynu należy wybrać region podstawowy dla konta. Sparowany region pomocniczy jest określany na podstawie regionu podstawowego i nie można go zmienić. Aby uzyskać aktualne informacje dotyczące regionów obsługiwanych przez platformę Azure, zobacz temat [ciągłość działania i odzyskiwanie po awarii (BCDR): wielosparowane regiony platformy Azure](../../best-practices-availability-paired-regions.md).

## <a name="see-also"></a>Zobacz także

- [Replikacja usługi Azure Storage](storage-redundancy.md)
- [Magazyn lokalnie nadmiarowy (LRS): niski koszt nadmiarowości danych dla usługi Azure Storage](storage-redundancy-lrs.md)
- [Magazyn strefowo nadmiarowy (ZRS): aplikacje usługi Azure Storage o wysokiej dostępności](storage-redundancy-zrs.md)
