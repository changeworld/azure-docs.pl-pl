---
title: Monitorowanie usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Jak monitorować usługę Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 032b39846d19e34f2eb87c1311feeb4bb890cb24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467462"
---
# <a name="monitor-azure-file-sync"></a>Monitorowanie usługi Azure File Sync

Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

W tym artykule opisano sposób monitorowania wdrożenia usługi Azure File Sync za pomocą witryny Azure portal i systemu Windows Server.

Obecnie dostępne są następujące opcje monitorowania:

## <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal można wyświetlić kondycji zarejestrowanego serwera, kondycja punktu końcowego serwera (kondycja sync) i metryki.

### <a name="storage-sync-service"></a>Usługa synchronizacji magazynu

Aby wyświetlić zarejestrowanego serwera i kondycja punktu końcowego serwera, przejdź do usługi synchronizacji magazynu w witrynie Azure portal. Kondycja serwera zarejestrowane jest widoczne w bloku serwery zarejestrowane. Kondycja punktu końcowego serwera jest widoczne w bloku grupy synchronizacji.

Kondycji zarejestrowanego serwera
- Stan serwera zarejestrowanej jest Online, serwer jest pomyślnie komunikowania się z usługą.
- Jeśli stan serwera zarejestrowane pojawia się w trybie Offline, sprawdź, czy Proces Monitor synchronizacji magazynu (AzureStorageSyncMonitor.exe) na serwerze jest uruchomiony. Jeśli serwer znajduje się za zaporą lub serwera proxy, należy skonfigurować zapory i serwera proxy na [dokumentacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Kondycja punktu końcowego serwera
- Kondycja punktu końcowego serwera w portalu jest oparty na Synchronizuj zdarzenia, które są rejestrowane w dzienniku zdarzeń Telemetrii na serwerze (identyfikator 9102 i: 9302; lista). Jeśli sesję synchronizacji kończy się niepowodzeniem ze względu na błąd przejściowy (na przykład błąd anulowane), synchronizacja może nadal widoczne dobrej kondycji w witrynie portal tak długo, jak w bieżącej sesji synchronizacji jest postępy (identyfikator zdarzenia w: 9302; lista służy do określenia, jeśli pliki są stosowane). Zobacz następujące dokumentację, aby uzyskać więcej informacji: [Synchronizuj kondycji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [synchronizacji postępu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Jeśli w portalu jest wyświetlany błąd synchronizacji zaplanowanej synchronizacji nie wprowadzania postępu, sprawdź [dokumentacja dotycząca rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) wskazówki.

### <a name="azure-monitor"></a>Azure Monitor

Użyj [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) synchronizacji monitora, obsługi warstw w chmurze i łączność z serwerem. Metryki dla usługi Azure File Sync są domyślnie włączone i są wysyłane do usługi Azure Monitor co 15 minut.

Aby wyświetlić metryki usługi Azure File Sync w usłudze Azure Monitor, wybierz typ zasobu usługi synchronizacji magazynu.

Następujące metryki dla usługi Azure File Sync są dostępne w usłudze Azure Monitor:

| Nazwa metryki | Opis |
|-|-|
| Bajty synchronizowane | Rozmiar danych transferowanych (przekazywania i pobierania).<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Wycofanie obsługi warstw w chmurze | Rozmiar danych odwołać.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Wymiar dotyczy: Nazwa serwera |
| Pliki nie są synchronizowane | Liczba plików, które kończą się niepowodzeniem do synchronizacji.<br><br>Jednostka: Licznik<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Pliki synchronizowane | Liczba plików przekazane oraz pobrane.<br><br>Jednostka: Licznik<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Puls serwera | Liczba pulsów otrzymany z serwera.<br><br>Jednostka: Licznik<br>Typ agregacji: Maksimum<br>Wymiar dotyczy: Nazwa serwera |
| Wynik sesji synchronizacji | Synchronizuj wynik sesji (1 = pomyślna synchronizacja sesji; 0 = sesję synchronizacji nie powiodło się)<br><br>Jednostka: Licznik<br>Typy agregacji: Maksimum<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |

## <a name="windows-server"></a>Windows Server

W systemie Windows Server można wyświetlić obsługi warstw, w chmurze zarejestrowanego serwera i kondycji synchronizacji.

### <a name="event-logs"></a>Dzienniki zdarzeń

Użyj dziennika zdarzeń Telemetrii na serwerze monitorowania zarejestrowanego serwera synchronizacji i obsługi warstw kondycji w chmurze. W dzienniku zdarzeń Telemetrii znajduje się w aplikacji i Services\Microsoft\FileSync\Agent w Podglądzie zdarzeń.

Kondycja synchronizacji
- Identyfikator zdarzenia 9102 jest rejestrowane po zakończeniu sesji synchronizacji. To zdarzenie powinien być używany do określenia, jeśli sesji synchronizacji są uniemożliwiający pomyślne (HResult = 0) i czy-item błędy synchronizacji. Zobacz następujące dokumentację, aby uzyskać więcej informacji: [Synchronizuj kondycji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [błędy elementu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Czasami sesje synchronizacji ogólną się nie powieść lub mieć PerItemErrorCount różna od zera, ale nadal postęp do przodu, z niektórych plików, na synchronizowanie pomyślnie. Można to zaobserwować w polach zastosowane (AppliedFileCount AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes), które informujące o tym, ile sesji powodzeniem. Jeśli widzisz wiele sesji synchronizacji w wierszu, które kończą się niepowodzeniem, ale liczba zastosowanych rosnąca, powinien zapewnić synchronizację czasu, aby spróbować ponownie, przed otwarciem biletu pomocy technicznej.

- Identyfikator zdarzenia: 9302; lista jest rejestrowane co 5 – 10 minut, jeśli występuje sesję synchronizacji usługi active. To zdarzenie powinien być używany do określenia, jeśli bieżącą sesję synchronizacji jest postępy (AppliedItemCount > 0). Jeśli synchronizacja nie jest postępy, sesję synchronizacji po pewnym czasie powinna zakończyć się niepowodzeniem i 9102 identyfikator zdarzenia będą rejestrowane z powodu błędu. Zobacz następujące dokumentację, aby uzyskać więcej informacji: [Postęp synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Kondycji zarejestrowanego serwera
- Identyfikator zdarzenia 9301 jest rejestrowane co 30 sekund, gdy serwer kwerendę usługi dla zadania. Jeśli GetNextJob zakończy się ze stanem = 0, serwer jest w stanie komunikować się z usługą. Jeśli GetNextJob zakończy się z powodu błędu, sprawdź [dokumentacja dotycząca rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) wskazówki.

Kondycja obsługi warstw w chmurze
- Aby monitorować aktywność obsługi warstw na serwerze, należy użyć Event ID 9003 9016 i 9029 w dzienniku zdarzeń Telemetrii (znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

  - Identyfikator zdarzenia 9003 zapewnia rozkład błędów dla punktu końcowego serwera. Np. Łączna liczba błędów, kod błędu, itd. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9016 zapewnia duplikowania wyniki dla woluminu. Na przykład wolnego miejsca, które jest procent, liczba plików jest zduplikowany w sesji, liczba plików nie powiodło się z ghost itp.
  - Identyfikator zdarzenia 9029 informacje duplikowania sesji dla punktu końcowego serwera. Na przykład warstwy liczbę plików, które podjęto w danej sesji, liczba plików w danej sesji, liczby plików już warstwowe, itp.
  
- Aby monitorować aktywność odwołania na serwerze, należy użyć Event ID 9005 9006 i 9009 9059 w dzienniku zdarzeń Telemetrii (znajdujący się w aplikacji i Services\Microsoft\FileSync\Agent w Podglądzie zdarzeń).

  - Identyfikator zdarzenia 9005 zapewnia niezawodność odwołania dla punktu końcowego serwera. Na przykład łączna liczba unikatowych plików uzyskiwać dostęp, a łączna liczba unikatowych plików nieudanych prób dostępu itd.
  - Identyfikator zdarzenia 9006 zapewnia rozkład błędów odwołań dla punktu końcowego serwera. Np. Łączna liczba żądań nie powiodło się, kod błędu, itd. Uwaga: jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9009 informacje wycofaniu sesji dla punktu końcowego serwera. Na przykład DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, itd.
  - Identyfikator zdarzenia 9059 zapewnia dystrybucję wycofaniu aplikacji dla punktu końcowego serwera. Na przykład ShareId, nazwę aplikacji i TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Liczniki wydajności

Używanie liczników wydajności usługi Azure File Sync na serwerze, na monitorowanie działania synchronizacji.

Aby wyświetlić wydajność usługi Azure File Sync liczniki na serwerze, uruchom Monitor wydajności (Perfmon.exe) i liczniki można znaleźć w obiektach AFS Bajty przesłane i AFS operacji synchronizacji.

Następujące liczniki wydajności dla usługi Azure File Sync są dostępne w Monitorze wydajności:

| Nazwa Object\Counter wydajności | Opis |
|-|-|
| AFS bajtów Transferred\Downloaded bajtów na sekundę | Liczba pobranych bajtów na sekundę. |
| AFS bajtów Transferred\Uploaded bajtów na sekundę | Liczba bajtów, które są przekazywane na sekundę. |
| AFS bajtów Transferred\Total bajtów na sekundę | Całkowita liczba bajtów na sekundę (przekazywania i pobierania). |
| AFS synchronizacji Operations\Downloaded synchronizacji plików na sekundę | Liczba plików pobranych na sekundę. |
| AFS synchronizacji Operations\Uploaded synchronizacji plików na sekundę | Liczba plików przekazanych na sekundę. |
| AFS synchronizacji Operations\Total synchronizacji plików operacje na sekundę | Całkowita liczba plików zsynchronizowane (przekazywania i pobierania). |

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Należy wziąć pod uwagę ustawień zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Rozwiązywanie problemów z usługi Azure File Sync](storage-sync-files-troubleshoot.md)
- [Usługa Azure Files — często zadawane pytania](storage-files-faq.md)
