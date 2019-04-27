---
title: Opcje danych transferu na platformie Azure przy użyciu urządzenia | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać odpowiednie urządzenie do transferu danych na platformie Azure
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: d727ed62f5a869f6eb67400281d2660607756abf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637505"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>Porównaj StorSimple przy użyciu usługi Azure File Sync i krawędź pola danych opcje transferu danych 
 
Ten dokument zawiera omówienie opcji do transferu danych lokalnych do platformy Azure, porównanie: Vs krawędź pola danych. Usługa Azure File Sync programu vs. Seria StorSimple 8000.

- **[Krawędź pola danych](/azure/databox-online/data-box-edge-overview)**  — krawędź pola danych jest lokalne urządzenie sieci, dane są przenoszone do i z platformy Azure, która ma włączone sztucznej Inteligencji krawędzi obliczeń wstępnie przetworzyć dane podczas przekazywania. Brama pole danych jest wersja wirtualna urządzenia przy użyciu tych samych możliwości transferu danych.
- **[Usługa Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)**  — usługi Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Ogólna dostępność usługi Azure File Sync ogłoszono we wcześniejszej części 2018 r.
- **[Usługa StorSimple](/azure/storsimple/storsimple-overview)**  — StorSimple to urządzenie hybrydowych, które pomaga firmom konsolidować infrastruktury magazynu dla magazynu podstawowego, ochrony danych, archiwizacji i odzyskiwania po awarii w ramach jednego rozwiązania, ścisła integracja z usługą Usługa Azure storage. Można znaleźć cyklu życia produktu dla usługi StorSimple [tutaj](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Podsumowanie porównania

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|Omówienie         |Hybrydowy magazyn warstwowy i archiwizowanie|Magazynem serwera plików ogólnego z usługą cloud sync warstw i połączenia obejmujące wiele lokacji.  |Rozwiązanie magazynu, aby wstępnie przetworzyć dane i wyślij go za pośrednictwem sieci na platformie Azure.        |
|Scenariusze        |Serwer plików, element docelowy archiwizacji, wykonywania kopii zapasowych |Serwer plików, archiwizacji (wielolokalizacyjnego)   |Transfer danych, dane przetwarzania wstępnego tym ML wnioskowania, IoT, archiwizacji    |
|Funkcja obliczeniowa Edge     |Niedostępne |Niedostępne |Obsługuje uruchamianie kontenerów za pomocą usługi Azure IoT Edge    |
|Współczynnik postaci      |Urządzenie fizyczne   |Agent jest zainstalowany w systemie Windows Server |Urządzenie fizyczne   |
|Sprzęt         |Urządzenie fizyczne, dostarczonych przez firmę Microsoft jako część usługi | Dostarczone przez klienta |Urządzenie fizyczne, dostarczonych przez firmę Microsoft jako część usługi  |
|Format danych      |Format niestandardowy   |Pliki         |Obiekty BLOB i plików    |
|Obsługa protokołów |iSCSI          |SMB, NFS    | SMB lub NFS      |
|Cennik          |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [krawędzi urządzenia Azure Data Box](/azure/databox-online/data-box-edge-overview) i [urządzenia Azure Data Box bramy](/azure/databox-online/data-box-gateway-overview)
- Dowiedz się więcej o [usługi Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide)
