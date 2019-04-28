---
title: Monitorowanie usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Jak monitorować usługę Azure File Sync.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759462"
---
# <a name="monitor-azure-file-sync"></a>Monitorowanie usługi Azure File Sync

Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

W tym artykule opisano sposób monitorowania wdrożenia usługi Azure File Sync za pomocą witryny Azure portal i systemu Windows Server.

Obecnie dostępne są następujące opcje monitorowania.

## <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal można wyświetlić kondycji zarejestrowanego serwera, kondycja punktu końcowego serwera (kondycja sync) i metryki.

### <a name="storage-sync-service"></a>Usługa synchronizacji magazynu

Aby wyświetlić kondycji zarejestrowanego serwera, kondycja punktu końcowego serwera i metryki, przejdź do usługi synchronizacji magazynu w witrynie Azure portal. Możesz wyświetlić kondycji zarejestrowanego serwera w **zarejestrowane serwery** bloku i serwera kondycji punktu końcowego w **synchronizacji grupy** bloku.

Kondycji zarejestrowanego serwera:

- Jeśli **serwera zarejestrowane** stan **Online**, serwera pomyślnie komunikowania się z usługą.
- Jeśli **serwera zarejestrowane** stan **pojawia się w trybie Offline**, sprawdź, czy Proces Monitor synchronizacji magazynu (AzureStorageSyncMonitor.exe) na serwerze jest uruchomiony. Jeśli serwer znajduje się za zaporą lub serwera proxy, zobacz [w tym artykule](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) skonfigurować zapory i serwera proxy.

Kondycja punktu końcowego serwera:

- Kondycja punktu końcowego serwera w portalu jest oparty na Synchronizuj zdarzenia, które są rejestrowane w dzienniku zdarzeń Telemetrii na serwerze (identyfikator 9102 i: 9302; lista). Jeśli sesję synchronizacji kończy się niepowodzeniem ze względu na błąd przejściowy, takie jak błąd zostało anulowane, synchronizacji może nadal widoczna dobrej kondycji w witrynie portal tak długo, jak w bieżącej sesji synchronizacji jest postępy. Identyfikator zdarzenia: 9302; lista jest używana do określenia, jeśli pliki są stosowane. Aby uzyskać więcej informacji, zobacz [synchronizacji kondycji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i [synchronizacji postępu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Jeśli w portalu jest wyświetlany błąd synchronizacji, ponieważ synchronizacja nie jest postępy, zobacz [dokumentacja dotycząca rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) wskazówki.

Metryki:

- Następujące metryki są wyświetlane w portalu usługi synchronizacji magazynu:

  | Nazwa metryki | Opis | Nazwa bloku |
  |-|-|-|
  | Bajty synchronizowane | Rozmiar danych transferowanych (przekazywania i pobierania) | Grupy synchronizacji, punkt końcowy serwera |
  | Wycofanie obsługi warstw w chmurze | Rozmiar danych przypomnieć | Zarejestrowane serwery |
  | Pliki nie są synchronizowane | Liczba plików, które kończą się niepowodzeniem do synchronizacji | Punkt końcowy serwera |
  | Pliki synchronizowane | Liczba plików przesłanych (przekazywania i pobierania) | Grupy synchronizacji, punkt końcowy serwera |
  | Stan online Server | Liczba pulsów otrzymany z serwera | Zarejestrowane serwery |

- Aby dowiedzieć się więcej, zobacz [usługi Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Wykresy w portalu usługi synchronizacji magazynu ma okresie 24 godzin. Aby wyświetlić zakresów w innym czasie lub wymiarów, należy użyć usługi Azure Monitor.

### <a name="azure-monitor"></a>Azure Monitor

Użyj [usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) synchronizacji monitora, obsługi warstw w chmurze i łączność z serwerem. Metryki dla usługi Azure File Sync są domyślnie włączone i są wysyłane do usługi Azure Monitor co 15 minut.

Zaznacz, aby wyświetlić metryki usługi Azure File Sync w usłudze Azure Monitor **usługi synchronizacji magazynu** typu zasobu.

Następujące metryki dla usługi Azure File Sync są dostępne w usłudze Azure Monitor:

| Nazwa metryki | Opis |
|-|-|
| Bajty synchronizowane | Rozmiar danych transferowanych (przekazywania i pobierania).<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Wycofanie obsługi warstw w chmurze | Rozmiar danych odwołać.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Wymiar dotyczy: Nazwa serwera |
| Pliki nie są synchronizowane | Liczba plików, które kończą się niepowodzeniem do synchronizacji.<br><br>Jednostka: Licznik<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Pliki synchronizowane | Liczba plików przesłanych (przekazywania i pobierania).<br><br>Jednostka: Licznik<br>Typ agregacji: Suma<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |
| Stan online Server | Liczba pulsów otrzymany z serwera.<br><br>Jednostka: Licznik<br>Typ agregacji: Maksimum<br>Wymiar dotyczy: Nazwa serwera |
| Wynik sesji synchronizacji | Synchronizuj wynik sesji (1 = pomyślna synchronizacja sesji; 0 = sesję synchronizacji nie powiodło się)<br><br>Jednostka: Licznik<br>Typy agregacji: Maksimum<br>Wymiary stosowane: Serwer punktu końcowego nazwy, synchronizacja kierunku, nazwa grupy synchronizacji |

## <a name="windows-server"></a>Windows Server

W systemie Windows Server można wyświetlić obsługi warstw, w chmurze zarejestrowanego serwera i kondycji synchronizacji.

### <a name="event-logs"></a>Dzienniki zdarzeń

Użyj dziennika zdarzeń Telemetrii na serwerze monitorowania zarejestrowanego serwera synchronizacji i obsługi warstw kondycji w chmurze. W dzienniku zdarzeń Telemetrii znajduje się w Podglądzie zdarzeń w obszarze *aplikacji i Services\Microsoft\FileSync\Agent*.

Zdrowie synchronizacji:

- Identyfikator zdarzenia 9102 jest rejestrowane po zakończeniu sesji synchronizacji. Użyj tego zdarzenia, aby ustalić, czy pomyślnie sesje synchronizacji (**HResult = 0**) i czy-item błędy synchronizacji. Aby uzyskać więcej informacji, zobacz [synchronizacji kondycji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i [błędy elementu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentacji.

  > [!Note]  
  > Czasami sesje synchronizacji ogólną się nie powieść lub masz PerItemErrorCount różna od zera. Jednak nadal wprowadzić postęp, a niektóre pliki synchronizację. Widać to w polach zastosowane, takich jak AppliedFileCount AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes. Te pola sprawdzić, ile sesji zakończyła się pomyślnie. Jeśli widzisz wiele sesji synchronizacji, które się nie powieść w wierszu i mają rosnąca liczba zastosowanych, należy podać czas synchronizacji, aby spróbować ponownie, zanim otworzysz zgłoszenie do pomocy technicznej.

- Identyfikator zdarzenia: 9302; lista jest rejestrowane co 5 – 10 minut, jeśli występuje sesję synchronizacji usługi active. To zdarzenie służy do określenia, jeśli bieżącą sesję synchronizacji jest postępy (**AppliedItemCount > 0**). Jeśli synchronizacja nie jest postępy, sesję synchronizacji po pewnym czasie powinna zakończyć się niepowodzeniem i 9102 identyfikator zdarzenia będą rejestrowane z powodu błędu. Aby uzyskać więcej informacji, zobacz [synchronizacji dokumentacji postępu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Kondycji zarejestrowanego serwera:

- Identyfikator zdarzenia 9301 jest rejestrowane co 30 sekund, gdy serwer kwerendę usługi dla zadania. Jeśli kończy GetNextJob **stan = 0**, serwer jest w stanie komunikować się z usługą. Jeśli GetNextJob zakończy się z powodu błędu, sprawdź [dokumentacja dotycząca rozwiązywania problemów](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) wskazówki.

Kondycja obsługi warstw w chmurze:

- Aby monitorować aktywność obsługi warstw na serwerze, należy użyć Event ID 9003 9016 i 9029 w dzienniku zdarzeń Telemetrii, który znajduje się w Podglądzie zdarzeń *aplikacji i Services\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9003 zapewnia rozkład błędów dla punktu końcowego serwera. Na przykład: Łączna liczba błędów i kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9016 zapewnia duplikowania wyniki dla woluminu. Na przykład: Procent wolnego miejsca to liczba plików zduplikowany w sesji, a liczba plików nie powiodło się zduplikowanych.
  - Identyfikator zdarzenia 9029 informacje duplikowania sesji dla punktu końcowego serwera. Na przykład: Liczba plików, które podjęto w danej sesji, liczby plików warstwowego w sesji i liczby plików już warstwy.
  
- Aby monitorować aktywność odwołania na serwerze, należy użyć Event ID 9005 9006 i 9009 9059 w dzienniku zdarzeń Telemetrii, który znajduje się w Podglądzie zdarzeń *aplikacji i Services\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9005 zapewnia niezawodność odwołania dla punktu końcowego serwera. Na przykład: Całkowita liczba unikatowych plików używanych i łączna liczba unikatowych plików przy użyciu dostępu nie powiodło się.
  - Identyfikator zdarzenia 9006 zapewnia rozkład błędów odwołań dla punktu końcowego serwera. Na przykład: Łączna liczba żądań zakończonych niepowodzeniem i kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9009 informacje wycofaniu sesji dla punktu końcowego serwera. Na przykład: DurationSeconds CountFilesRecallSucceeded i CountFilesRecallFailed.
  - Identyfikator zdarzenia 9059 zapewnia dystrybucję wycofaniu aplikacji dla punktu końcowego serwera. Na przykład: ShareId, nazwę aplikacji i TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Liczniki wydajności

Używanie liczników wydajności usługi Azure File Sync na serwerze, na monitorowanie działania synchronizacji.

Aby wyświetlić liczniki wydajności usługi Azure File Sync na serwerze, otwórz Monitor wydajności (Perfmon.exe). Możesz znaleźć liczniki w obszarze **AFS Bajty przesłane** i **operacji synchronizacji AFS** obiektów.

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
