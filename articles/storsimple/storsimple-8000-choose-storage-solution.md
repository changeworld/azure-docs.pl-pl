---
title: Opcje przesyłania danych na platformę Azure przy użyciu urządzenia | Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać odpowiednie urządzenie do przesyłania danych na platformę Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 9456d975ffb6d7726166f9badbcb8fe0ccbe5256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965361"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Compare StorSimple with Azure File Sync and Data Box Edge data transfer options (Porównanie usługi StorSimple z usługami Azure File Sync i opcjami transferu danych usługi Data Box Edge) 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ten dokument zawiera omówienie opcji lokalnego transferu danych na platformę Azure, porównując: Data Box Edge vs. Azure File Sync vs. StorSimple 8000 series.

- **[Data Box Edge](/azure/databox-online/data-box-edge-overview)** — Data Box Edge to lokalne urządzenie sieciowe, które przenosi dane do i z platformy Azure i ma obliczenia edge z obsługą sztucznej inteligencji do wstępnego przetwarzania danych podczas przekazywania. Data Box Gateway to wirtualna wersja urządzenia z tymi samymi możliwościami transferu danych.
- **[Usługa Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)** — usługa Azure File Sync może służyć do scentralizowania udziałów plików organizacji w usłudze Azure Files, przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Ogólna dostępność usługi Azure File Sync została ogłoszona wcześniej w 2018 r.
- **[StorSimple](/azure/storsimple/storsimple-overview)** — StorSimple to urządzenie hybrydowe, które pomaga przedsiębiorstwom konsolidować swoją infrastrukturę magazynu w celu podstawowego przechowywania, ochrony danych, archiwizacji i odzyskiwania po awarii w jednym rozwiązaniu, ściśle integrując się z magazynem platformy Azure. Cykl życia produktu storsimple można znaleźć [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Podsumowanie porównawcze

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Omówienie         |Warstwowa hybrydowa pamięć masowa i archiwizacja|Ogólne miejsce na serwerze plików z warstwą chmury i synchronizacją wielu lokacji.  |Rozwiązanie magazynu do wstępnego przetwarzania danych i wysyłania ich za pośrednictwem sieci na platformę Azure.        |
|Scenariusze        |Serwer plików, archiwizacja, miejsce docelowe kopii zapasowej |Serwer plików, archiwalny (wielowitaminowy)   |Przesyłanie danych, wstępne przetwarzanie danych, w tym wnioskowanie o ml, IoT, archiwizacja    |
|Funkcja obliczeniowa Edge     |Niedostępne |Niedostępne |Obsługuje uruchamianie kontenerów przy użyciu usługi Azure IoT Edge    |
|Faktor      |Urządzenie fizyczne   |Agent zainstalowany w systemie Windows Server |Urządzenie fizyczne   |
|Sprzęt         |Urządzenie fizyczne dostarczone przez firmę Microsoft w ramach usługi | Klient pod warunkiem, |Urządzenie fizyczne dostarczone przez firmę Microsoft w ramach usługi  |
|Format danych      |Format niestandardowy   |Pliki         |Obiekty BLOB lub pliki    |
|Obsługa protokołów |iSCSI          |SMB, NFS    | SMB lub NFS      |
|Cennik          |[Magazyn StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Data Box Edge](/azure/databox-online/data-box-edge-overview) i [bramie usługi Azure Data Box](/azure/databox-online/data-box-gateway-overview)
- Dowiedz się więcej o [usłudze Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
