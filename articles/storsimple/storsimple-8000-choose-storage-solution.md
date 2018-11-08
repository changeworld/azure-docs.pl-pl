---
title: Opcje danych transferu na platformie Azure przy użyciu urządzenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać odpowiednie urządzenie do transferu danych na platformie Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 11/01/2018
ms.author: alkohli
ms.openlocfilehash: 0cb1a0bccbb989506988f36c515d59cddb832265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263547"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Porównaj StorSimple przy użyciu usługi Azure File Sync i krawędź pola danych opcje transferu danych 
 
Ten dokument zawiera omówienie opcji do transferu danych lokalnych do platformy Azure, porównanie: vs krawędź pola danych usługi Azure File Sync (AFS) w porównaniu z serii StorSimple 8000.

- **[Krawędź pola danych](/azure/databox-online/data-box-edge-overview.md)**  — krawędź pola danych jest lokalne urządzenie sieci, dane są przenoszone do i z platformy Azure, która ma włączone sztucznej Inteligencji krawędzi obliczeń wstępnie przetworzyć dane podczas przekazywania. Ona zostało ogłoszone na konferencji Ignite 2018 i w publicznej wersji zapoznawczej. Brama pole danych jest wersja wirtualna urządzenia przy użyciu tych samych możliwości transferu danych.
- **[Usługa Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)**  — usługi Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Ogólna dostępność AFS ogłoszono we wcześniejszej części 2018 r.
- **[Usługa StorSimple](/azure/storsimple/storsimple-overview.md)**  — StorSimple to urządzenie hybrydowych, które pomaga firmom konsolidować infrastruktury magazynu dla magazynu podstawowego, ochrony danych, archiwizacji i odzyskiwania po awarii w ramach jednego rozwiązania, ścisła integracja z usługą Usługa Azure storage. Można znaleźć cyklu życia produktu dla usługi StorSimple [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Podsumowanie porównania

|                           |StorSimple 8000   |Azure File Sync   |Krawędź pola danych (wersja zapoznawcza)           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Przegląd         |Hybrydowy magazyn warstwowy i archiwizowanie|Magazynem serwera plików ogólnego z usługą cloud sync warstw i połączenia obejmujące wiele lokacji.  |Rozwiązanie magazynu, aby wstępnie przetworzyć dane i wyślij go za pośrednictwem sieci na platformie Azure.        |
|Scenariusze        |Serwer plików, element docelowy archiwizacji, wykonywania kopii zapasowych |Serwer plików, archiwizacji (wielolokalizacyjnego)   |Transfer danych, dane przetwarzania wstępnego tym ML wnioskowania, IoT, archiwizacji    |
|Funkcja obliczeniowa Edge     |Niedostępne |Niedostępne |Obsługuje uruchamianie kontenerów za pomocą usługi Azure IoT Edge    |
|Współczynnik postaci      |Urządzenie fizyczne   |Agent jest zainstalowany w systemie Windows Server |Urządzenie fizyczne   |
|Sprzęt         |Urządzenie fizyczne, dostarczonych przez firmę Microsoft jako część usługi | Dostarczone przez klienta |Urządzenie fizyczne, dostarczonych przez firmę Microsoft jako część usługi  |
|Format danych      |Format niestandardowy   |Pliki         |Obiekty BLOB i plików    |
|Obsługa protokołów |iSCSI          |PROTOKÓŁ SMB, SYSTEMU PLIKÓW NFS    | SMB lub NFS      |
|Cennik          |[Ceny usługi StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Cennik AFS](https://azure.microsoft.com/pricing/details/storage/files/)  |[Cennik krawędź pola danych](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [krawędzi urządzenia Azure Data Box](/azure/databox-online/data-box-edge-overview.md) i [urządzenia Azure Data Box bramy](/azure/databox-online/data-box-gateway-overview.md)
- Dowiedz się więcej o [usługi Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide.md)