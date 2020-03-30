---
title: Monitorowanie synchronizacji plików platformy Azure | Dokumenty firmy Microsoft
description: Jak monitorować synchronizację plików platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68699290"
---
# <a name="monitor-azure-file-sync"></a>Monitorowanie usługi Azure File Sync

Użyj usługi Azure File Sync, aby scentralizować udziały plików organizacji w usłudze Azure Files, zachowując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

W tym artykule opisano sposób monitorowania wdrażania usługi Azure File Sync przy użyciu usługi Azure Monitor, usługa synchronizacji magazynu i systemu Windows Server.

Następujące opcje monitorowania są obecnie dostępne.

## <a name="azure-monitor"></a>Azure Monitor

Użyj [usługi Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/overview) aby wyświetlić metryki i skonfigurować alerty dotyczące synchronizacji, warstw w chmurze i łączności z serwerem.  

### <a name="metrics"></a>Metryki

Metryki synchronizacji plików platformy Azure są domyślnie włączone i są wysyłane do usługi Azure Monitor co 15 minut.

Aby wyświetlić metryki usługi Azure File Sync w usłudze Azure Monitor, wybierz typ zasobu **usługi synchronizacji magazynu.**

Następujące metryki dla usługi Azure File Sync są dostępne w usłudze Azure Monitor:

| Nazwa metryki | Opis |
|-|-|
| Bajty zsynchronizowane | Rozmiar przesyłanych danych (przesyłanie i pobieranie).<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |
| Przywoływanie warstw w chmurze | Wielkość przywoływania danych.<br><br>**Uwaga:** Ta metryka zostanie usunięta w przyszłości. Użyj metryki rozmiar odwołania warstwowej chmury, aby monitorować rozmiar przywoływania danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiedni wymiar: Nazwa serwera |
| Rozmiar odwołania warstwowego chmury | Wielkość przywoływania danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiedni wymiar: nazwa serwera, nazwa grupy synchronizacji |
| Rozmiar odwołania warstwowego w chmurze według aplikacji | Rozmiar danych przywoływaniu przez aplikację.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiedni wymiar: nazwa aplikacji, nazwa serwera, nazwa grupy synchronizacji |
| Przepływność wycofywania warstwowego w chmurze | Rozmiar przepływności wycofywania danych.<br><br>Jednostka: Bajty<br>Typ agregacji: Suma<br>Odpowiedni wymiar: nazwa serwera, nazwa grupy synchronizacji |
| Pliki niezsynchronizowane | Liczba plików, których synchronizacja nie jest zsynchronizowana.<br><br>Jednostka: Liczba<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |
| Pliki zsynchronizowane | Liczba przesłanych plików (przesyłanie i pobieranie).<br><br>Jednostka: Liczba<br>Typ agregacji: Suma<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |
| Stan usługi online serwera | Liczba pulsu odebranych z serwera.<br><br>Jednostka: Liczba<br>Typ agregacji: maksymalna<br>Odpowiedni wymiar: Nazwa serwera |
| Wynik sesji synchronizacji | Wynik sesji synchronizacji (1=pomyślna sesja synchronizacji; 0=sesja synchronizacji nie powiodła się)<br><br>Jednostka: Liczba<br>Typy agregacji: maksymalna<br>Odpowiednie wymiary: Nazwa punktu końcowego serwera, Kierunek synchronizacji, Nazwa grupy synchronizacji |

### <a name="alerts"></a>Alerty

Aby skonfigurować alerty w usłudze Azure Monitor, wybierz usługę synchronizacji magazynu, a następnie wybierz [metrykę usługi Azure File Sync,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) która ma być używana dla alertu.  

W poniższej tabeli wymieniono kilka przykładowych scenariuszy do monitorowania i właściwą metrykę do użycia dla alertu:

| Scenariusz | Metryka używana do alertu |
|-|-|
| Kondycja punktu końcowego serwera w portalu = Błąd | Wynik sesji synchronizacji |
| Synchronizacja plików z serwerem lub punktem końcowym w chmurze | Pliki niezsynchronizowane |
| Zarejestrowany serwer nie może komunikować się z usługą synchronizacji magazynu | Stan usługi online serwera |
| Rozmiar wycofywania warstw w chmurze przekroczył 500GiB w ciągu jednego dnia  | Rozmiar odwołania warstwowego chmury |

Aby dowiedzieć się więcej o konfigurowaniu alertów w usłudze Azure Monitor, zobacz [Omówienie alertów na platformie Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Usługa synchronizacji magazynu

Aby wyświetlić zarejestrowaną kondycję serwera, kondycję punktu końcowego serwera i metryki, przejdź do usługi synchronizacji magazynu w witrynie Azure portal. Kondycję zarejestrowanego serwera można wyświetlić w kondycji bloku **zarejestrowanego serwera** i punktu końcowego serwera w bloku **Grupy synchronizacji.**

### <a name="registered-server-health"></a>Zarejestrowana kondycja serwera

- Jeśli zarejestrowany stan **serwera** jest **w trybie online,** serwer pomyślnie komunikuje się z usługą.
- Jeśli stan **zarejestrowanego serwera** jest **pojawia się w trybie offline,** sprawdź, czy jest uruchomiony proces Monitor synchronizacji magazynu (AzureStorageSyncMonitor.exe) na serwerze. Jeśli serwer znajduje się za zaporą lub serwerem proxy, zobacz [ten artykuł,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) aby skonfigurować zaporę i serwer proxy.

### <a name="server-endpoint-health"></a>Kondycja punktu końcowego serwera

- Kondycja punktu końcowego serwera w portalu jest oparta na zdarzeniach synchronizacji, które są rejestrowane w dzienniku zdarzeń telemetrii na serwerze (ID 9102 i 9302). Jeśli sesja synchronizacji nie powiedzie się z powodu błędu przejściowego, takiego jak anulowany błąd, synchronizacja może nadal być w dobrej kondycji w portalu, o ile bieżąca sesja synchronizacji postępuje. Identyfikator zdarzenia 9302 służy do określenia, czy pliki są stosowane. Aby uzyskać więcej informacji, zobacz [kondycję synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i [postęp synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Jeśli w portalu jest wyświetlany błąd synchronizacji, ponieważ synchronizacja nie jest postępująca, zapoznaj się z [dokumentacją rozwiązywania problemów,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) aby uzyskać wskazówki.

### <a name="metric-charts"></a>Wykresy metryczne

- Następujące wykresy metryki są widoczne w portalu usługi synchronizacji magazynu:

  | Nazwa metryki | Opis | Nazwa ostrza |
  |-|-|-|
  | Bajty zsynchronizowane | Rozmiar przesyłanych danych (przesyłanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Przywoływanie warstw w chmurze | Wielkość przywoływania danych | Zarejestrowane serwery |
  | Pliki niezsynchronizowane | Liczba plików, których synchronizacja nie została zsynchronizowana | Punkt końcowy serwera |
  | Pliki zsynchronizowane | Liczba przesłanych plików (przesyłanie i pobieranie) | Grupa synchronizacji, punkt końcowy serwera |
  | Stan usługi online serwera | Liczba uderzeń serca otrzymanych z serwera | Zarejestrowane serwery |

- Aby dowiedzieć się więcej, zobacz [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Wykresy w portalu usługi synchronizacji magazynu mają zakres czasu 24 godzin. Aby wyświetlić różne zakresy czasu lub wymiary, użyj usługi Azure Monitor.

## <a name="windows-server"></a>Windows Server

W systemie Windows Server można wyświetlać warstwy w chmurze, zarejestrowany serwer i kondycję synchronizacji.

### <a name="event-logs"></a>Dzienniki zdarzeń

Użyj dziennika zdarzeń Telemetrii na serwerze, aby monitorować zarejestrowany serwer, synchronizację i kondycję warstw w chmurze. Dziennik zdarzeń Telemetria znajduje się w Podglądzie zdarzeń w obszarze *Aplikacje i usługi\Microsoft\FileSync\Agent*.

Kondycja synchronizacji:

- Identyfikator zdarzenia 9102 jest rejestrowany po zakończeniu sesji synchronizacji. Użyj tego zdarzenia, aby określić, czy sesje synchronizacji zakończyły się pomyślnie (**HResult = 0**) i czy występują błędy synchronizacji na element. Aby uzyskać więcej informacji, zobacz dokumentację [kondycji synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) i [błędów na element.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)

  > [!Note]  
  > Czasami sesje synchronizacji nie powiedzie się lub mają non-zero PerItemErrorCount. Jednak nadal postępują w przyszłości, a niektóre pliki synchronizują się pomyślnie. Można to zobaczyć w polach Zastosowane, takie jak AppliedFileCount, AppliedDirCount, AppliedTombstoneCount i AppliedSizeBytes. Te pola informują, jaka część sesji zakończyła się pomyślnie. Jeśli widzisz wiele sesji synchronizacji nie w wierszu i mają one coraz Applied liczyć, dać czas synchronizacji, aby spróbować ponownie przed otwarciem biletu pomocy technicznej.

- Identyfikator zdarzenia 9302 jest rejestrowany co 5 do 10 minut, jeśli istnieje aktywna sesja synchronizacji. Użyj tego zdarzenia, aby określić, czy bieżąca sesja synchronizacji robi postęp (**AppliedItemCount > 0**). Jeśli synchronizacja nie jest postęp, sesja synchronizacji powinna ostatecznie zakończyć się niepowodzeniem, a identyfikator zdarzenia 9102 zostanie zarejestrowany z błędem. Aby uzyskać więcej informacji, zobacz [dokumentację postępu synchronizacji](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Zarejestrowana kondycja serwera:

- Identyfikator zdarzenia 9301 jest rejestrowany co 30 sekund, gdy serwer wysyła zapytanie do usługi o zadania. Jeśli GetNextJob kończy się **ze stanem = 0,** serwer jest w stanie komunikować się z usługą. Jeśli GetNextJob kończy się z błędem, sprawdź [dokumentację rozwiązywania problemów,](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) aby uzyskać wskazówki.

Kondycja warstw w chmurze:

- Aby monitorować aktywność warstwową na serwerze, użyj identyfikatora zdarzenia 9003, 9016 i 9029 w dzienniku zdarzeń Telemetria, który znajduje się w Podglądzie zdarzeń w obszarze *Aplikacje i usługi\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9003 zapewnia dystrybucję błędów dla punktu końcowego serwera. Na przykład: Całkowita liczba błędów i kod błędów. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9016 zapewnia wyniki ghosting dla woluminu. Na przykład: Procent wolnego miejsca jest, Liczba plików ghosted w sesji i liczba plików nie udało się ghost.
  - Identyfikator zdarzenia 9029 zawiera informacje o sesji ghosting dla punktu końcowego serwera. Na przykład: Liczba plików, które zostały usiłowane w sesji, Liczba plików warstwowych w sesji i Liczba plików już warstwowych.
  
- Aby monitorować aktywność odwołania na serwerze, użyj identyfikatora zdarzenia 9005, 9006, 9009 i 9059 w dzienniku zdarzeń Telemetria, który znajduje się w Podglądzie zdarzeń w obszarze *Aplikacje i usługi\Microsoft\FileSync\Agent*.

  - Identyfikator zdarzenia 9005 zapewnia niezawodność odwołania dla punktu końcowego serwera. Na przykład: Całkowita liczba unikatowych plików, do które można uzyskać dostęp, i całkowita liczba unikatowych plików z nieudanym dostępem.
  - Identyfikator zdarzenia 9006 zapewnia dystrybucję błędów odwołania dla punktu końcowego serwera. Na przykład: Całkowita liczba nieudanych żądań i kod błędu. Jedno zdarzenie jest rejestrowane na kod błędu.
  - Identyfikator zdarzenia 9009 zawiera informacje o sesji odwołania dla punktu końcowego serwera. Na przykład: DurationSeconds, CountFilesRecallSucceedededed i CountFilesRecallFailed.
  - Identyfikator zdarzenia 9059 zapewnia dystrybucję odwołania aplikacji dla punktu końcowego serwera. Na przykład: ShareId, Nazwa aplikacji i TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Liczniki wydajności

Użyj liczników wydajności usługi Azure File Sync na serwerze, aby monitorować aktywność synchronizacji.

Aby wyświetlić liczniki wydajności usługi Azure File Sync na serwerze, otwórz Monitor wydajności (Perfmon.exe). Liczniki można znaleźć w obszarze **obiektów AfS Bytes Transferred** i **AFS Sync Operations.**

Następujące liczniki wydajności dla usługi Azure File Sync są dostępne w Monitorze wydajności:

| Nazwa obiektu wydajności\Licznik | Opis |
|-|-|
| Bajty AFS przeniesione\Pobrane bajty/s | Liczba bajtów pobranych na sekundę. |
| Bajty AFS przeniesione\Przesłane bajty/s | Liczba bajtów przesłanych na sekundę. |
| Bajty AFS przeniesione\Całkowita liczba bajtów/s | Całkowita liczba bajtów na sekundę (przekazywanie i pobieranie). |
| Operacje synchronizacji AFS\Pobrane pliki synchronizacji/s | Liczba pobranych plików na sekundę. |
| Operacje synchronizacji AFS\przesłane pliki synchronizacji/s | Liczba plików przesłanych na sekundę. |
| Operacje synchronizacji AFS\Całkowita liczba operacji plików synchronizacji/s | Całkowita liczba zsynchronizowanych plików (przesyłanie i pobieranie). |

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Rozważ ustawienia zapory i serwera proxy](storage-sync-files-firewall-and-proxy.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
- [Często zadawane pytania dotyczące usług Azure Files](storage-files-faq.md)
