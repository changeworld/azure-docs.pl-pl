---
title: HANA procedury trybu failover do lokacji po awarii dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak przeprowadzić trybu failover do lokacji odzyskiwania po awarii dla oprogramowania SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca4d5912d75dd7b33737f61737a209284b7a5a47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60338556"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura przechodzenia w tryb failover odzyskiwania po awarii


>[!IMPORTANT]
>Ta dokumentacja jest nie można zastąpić dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekuje się, że czytelnik ma stałych zrozumienie i doświadczeń w administracyjnej platformy SAP HANA oraz operacji, szczególnie w przypadku tematów, tworzenie kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii. W tej dokumentacji przedstawiono zrzuty ekranu z SAP HANA Studio. Zawartości, struktury i charakter ekrany narzędzia do administrowania SAP i narzędzi, sami mogą ulec zmianie z platformy SAP HANA wersji.

Istnieją dwa przypadki, które należy uwzględnić podczas przechodzenia w tryb failover do lokacji odzyskiwania po awarii:

- Potrzebujesz bazy danych SAP HANA, aby wrócić do najnowszego stanu danych. W tym przypadku jest skryptem samoobsługi za pomocą którego można wykonać przełączenie w tryb failover bez konieczności kontaktu z firmą Microsoft. Jednak do powrotu po awarii, musisz pracować z firmą Microsoft.
- Chcesz przywrócić migawkę pamięci masowej, który nie jest najnowsza wersja migawki replikowanych. W takim przypadku potrzebne do pracy z firmą Microsoft. 

>[!NOTE]
>Poniższe kroki muszą być wykonywane na jednostce dużych wystąpień HANA, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze migawek replikowanego magazynu, wykonaj następujące czynności: 

1. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii w dużych wystąpień HANA, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
1. Upewnij się, że są uruchomione żadne procesy platformy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Powinny zostać wyświetlone dane wyjściowe **hdbdaemon** przetwarzanie w stanie zatrzymania, a żadne inne procesy HANA w stanie uruchomiona lub jest uruchomiona.
1. W jednostce dużych wystąpień HANA lokacji odzyskiwania po awarii, uruchom skrypt *azure_hana_dr_failover.pl*. Skrypt jest zapytaniem dla identyfikatora SID SAP HANA, należy przywrócić. Gdy żądanie, wpisz w jednym lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* plikiem w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 

      Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA w trybie Failover, należy uruchomić skrypt kilka razy. Gdy żądanie, wpisz identyfikator SID HANA SAP, aby tryb failover i przywrócić. Po zakończeniu skrypt pokazuje listę punktów instalacji woluminów, które są dodawane do jednostki dużych wystąpień HANA. Ta lista zawiera również przywróconych woluminów odzyskiwania po awarii.

1. Zainstalować woluminu odzyskiwania po awarii przywróconej przy użyciu poleceń systemu operacyjnego Linux do jednostki duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 
1. Rozpocznij nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
1. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowych dziennika transakcji w celu skrócenia czasu cel punktu odzyskiwania, musisz scalić te kopie zapasowe dziennika transakcji w nowo zainstalowanego odzyskiwania po awarii/hana lub logbackups katalogu. Nie Nadpisz istniejące kopie zapasowe. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane przy użyciu najnowszych replikacji migawki magazynu.
1. Można także przywrócić pojedynczych plików z migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie odzyskiwania po awarii platformy Azure. 

Możesz również testowanie trybu failover odzyskiwania po awarii, bez wywierania wpływu na relacji replikacji rzeczywiste. Aby wykonać test trybu failover, wykonaj wcześniejsze kroki 1 i 2, a następnie kontynuuj następujący krok 3.

>[!IMPORTANT]
>Czy *nie* uruchamiania dowolnej transakcji do produkcji w wystąpieniu, który został utworzony w lokacji odzyskiwania po awarii w procesie **testowania pracy w trybie failover** ze skryptem wprowadzone w kroku 3. To polecenie tworzy zestaw woluminów, które nie mają relacji z lokacją główną. W wyniku synchronizacji do lokacji głównej jest *nie* możliwe. 

Krok 3 dla testu trybu failover

W jednostce dużych wystąpień HANA lokacji odzyskiwania po awarii, uruchom skrypt **azure_hana_test_dr_failover.pl**. Ten skrypt jest *nie* zatrzymywanie relacji replikacji między lokacją podstawową a lokacją odzyskiwania po awarii. Zamiast tego należy ten skrypt jest Klonowanie woluminów magazynu odzyskiwania po awarii. Po pomyślnym procesu klonowania, sklonowany woluminy są przywrócony do stanu najnowszej migawki, a następnie instalowane do jednostki odzyskiwania po awarii. Skrypt jest zapytaniem dla identyfikatora SID SAP HANA, należy przywrócić. Wpisz jeden lub tylko SAP HANA SID, został zreplikowany, która jest przechowywana w *HANABackupCustomerDetails.txt* plikiem w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 

Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA do testowania, należy uruchomić skrypt kilka razy. Gdy żądanie, wpisz identyfikator SID HANA SAP wystąpienie, które chcesz przetestować tryb failover. Po ukończeniu skryptu pokazuje listę punktów instalacji woluminów, które są dodawane do jednostki dużych wystąpień HANA. Ta lista zawiera również sklonowany woluminów odzyskiwania po awarii.

Przejdź do kroku 4.

   >[!NOTE]
   >Jeśli potrzebujesz do trybu failover do lokacji odzyskiwania po awarii i ratunkowe niektórych danych została usunięta, godz. temu woluminów odzyskiwania po awarii, należy ustawić wcześniejszej migawki, ta procedura ma zastosowanie. 

1. Zamknij wystąpienie nieprodukcyjnych HANA w jednostce odzyskiwania po awarii w dużych wystąpień HANA, której używasz. Jest to spowodowane istnieje wstępnie zainstalowane wystąpienie nieaktywni produkcji HANA.
1. Upewnij się, że są uruchomione żadne procesy platformy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Powinny zostać wyświetlone dane wyjściowe **hdbdaemon** przetwarzanie w stanie zatrzymania i żadne inne procesy HANA w stanie uruchomiona lub jest uruchomiona.
1. Określ, do którego nazwy migawki lub identyfikator kopii zapasowej platformy SAP HANA mają lokacji odzyskiwania po awarii, które są przywracane. W przypadku odzyskiwania po awarii w rzeczywistych ta migawka jest zwykle najnowszą migawkę. Jeśli potrzebujesz odzyskać utracone dane, wybierz wcześniejszej migawki.
1. Skontaktuj się z działem pomocy technicznej platformy Azure za pośrednictwem żądania pomocy technicznej o wysokim priorytecie. Poproś o przywrócenie tej migawki (przy użyciu nazwy i Data migawki) lub identyfikator kopii zapasowej platformy HANA w lokacji odzyskiwania po awarii. Wartość domyślna to, że po stronie Operacje przywraca/hana/ilość danych tylko. Jeśli chcesz mieć/hana/logbackups woluminy również należy wyraźnie określają, że. *Nie należy przywracać /hana/shared woluminu.* Zamiast tego należy wybrać konkretne pliki, takie jak global.ini poza **.snapshot** katalogu i jego podkatalogach po można ponownie zainstalować/hana/udostępniony wolumin PRD. 

   Na stronie operacje wykonane następujące kroki:

   a. Replikacji migawek woluminu odzyskiwania po awarii z woluminu produkcji jest zatrzymana. Ta przerw w działaniu może już się to zdarzyć w przypadku wystąpienia awarii w czasie produkcji Przyczyna, że musisz wykonać procedurę odzyskiwania po awarii.
   
   b. Magazyn migawek nazwy ani tworzyć migawek identyfikatorem kopii zapasowej została wybrana opcja został przywrócony na woluminu odzyskiwania po awarii.
   
   c. Po przywróceniu woluminu odzyskiwania po awarii są dostępne dla zamontowania jednostkom duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii.
      
1. Instalowanie woluminów odzyskiwania po awarii do jednostki duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 
1. Rozpocznij nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
1. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowych dziennika transakcji w celu skrócenia czasu cel punktu odzyskiwania, musisz scalić te kopie zapasowe dziennika transakcji w nowo zainstalowanego odzyskiwania po awarii/hana lub logbackups katalogu. Nie Nadpisz istniejące kopie zapasowe. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane przy użyciu najnowszych replikacji migawki magazynu.
1. Można także przywrócić pojedynczych plików z migawek, które zostały zreplikowane do woluminu /hana/shared/PRD w regionie odzyskiwania po awarii platformy Azure.

Dalej sekwencji kroków obejmuje odzyskiwanie wystąpienia produkcyjnych oprogramowania SAP HANA na podstawie migawek magazynu przywrócona i kopie zapasowe dziennika transakcji, które są dostępne:

1. Zmień lokalizację kopii zapasowej na **/hana/logbackups** przy użyciu programu SAP HANA Studio.
   ![Zmiana lokalizacji kopii zapasowej do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skanuje za pośrednictwem lokalizacji kopii zapasowej plików i sugeruje najnowszej kopii zapasowej dziennika transakcji do przywrócenia. Skanowanie może potrwać kilka minut, aż do ekranu, tak jak pojawia się następujący komunikat: ![Lista kopie zapasowe dziennika transakcji do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Dostosuj niektóre z domyślnych ustawień:

      - Wyczyść **użycie różnicowych kopii zapasowych**.
      - Wybierz **zainicjować dziennika obszaru**.

   ![Ustaw obszar zainicjować dziennika](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wybierz pozycję **Finish** (Zakończ).

   ![Zakończ Przywracanie odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinna pojawić się okno postępu, jak pokazano w tym miejscu. Należy pamiętać, że w przykładzie występuje przywracania odzyskiwania po awarii w konfiguracji platformy SAP HANA skalowalnego w poziomie trzema węzłami.

![Przywracanie w toku](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

W przypadku przywracania wydaje się, że przestanie odpowiadać na **Zakończ** ekranu i nie pokazuj ekran pokazujący postęp, upewnij się, że są uruchomione wszystkie wystąpienia oprogramowania SAP HANA na węzłach procesu roboczego. Jeśli to konieczne, należy ręcznie uruchomić wystąpieniami platformy SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym
Można powrotu po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym. Przyjrzyjmy się scenariusz, w którym przełączenie w tryb failover do lokacji odzyskiwania po awarii zostało spowodowane przez problemy w środowisku produkcyjnym region platformy Azure i nie trzeba odzyskać utracone dane. Uruchomiony używanych obciążeń produkcyjnych SAP od pewnego czasu w lokacji odzyskiwania po awarii. Jak są rozwiązywane problemy z witryny produkcyjnej, chcesz powrócić po awarii do witryn produkcyjnych. Ponieważ nie może utracić dane, krok do miejsca produkcji obejmuje kilka kroków i ścisłej współpracy z platformą SAP HANA w zespole operacji na platformie Azure. To Ty możesz wyzwolić zespół operacyjny, należy rozpocząć synchronizowanie wróć do witryny produkcyjnej po problemy są rozwiązywane.

Jest to sekwencja czynności do wykonania:

1. SAP HANA w zespole operacji na platformie Azure pobiera wyzwalacz, aby zsynchronizować woluminów magazynu produkcji z woluminów magazynu odzyskiwania po awarii, które teraz reprezentują stan produkcji. W tym stanie jednostkę dużych wystąpień HANA w środowisku produkcyjnym lokacji jest wyłączana.
1. SAP HANA w zespole operacji na platformie Azure monitoruje replikacji i upewnia się, że jej padł przed informujące o tym.
1. Aplikacje, które używają wystąpienie oprogramowania HANA w środowisku produkcyjnym w lokacji odzyskiwania po awarii zostanie zamknięta. Następnie należy wykonać kopii zapasowej dziennika transakcji platformy HANA. Następnie można zatrzymać wystąpienie oprogramowania HANA uruchomionych na jednostkach duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii.
1. Po zamknięciu wystąpienie oprogramowania HANA w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii zespół operacyjny ręcznie synchronizuje woluminy dysków ponownie.
1. SAP HANA w zespole operacji na platformie Azure ponownie uruchamia jednostki duże wystąpienie oprogramowania HANA w witryny produkcyjnej i przekazuje użytkownikowi. Upewnij się, wystąpienie SAP HANA jest w czasie uruchamiania dużych wystąpień HANA jednostki w stanie zamknięcie.
1. Możesz wykonać te same kroki przywracania bazy danych, tak jak w przypadku poprzednio przejść w tryb failover do lokacji odzyskiwania po awarii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Możesz monitorować stan postęp replikacji magazynu, wykonując skrypt `azure_hana_replication_status.pl`. Ten skrypt należy uruchomić z jednostki w lokalizacji odzyskiwania danych po awarii, które działają zgodnie z oczekiwaniami. Skrypt działa niezależnie od tego, czy replikacja jest aktywna. Skrypt można uruchomić dla każdej jednostki dużych wystąpień HANA dzierżawy w lokalizacji odzyskiwania danych po awarii. Nie można uzyskać szczegółowe informacje o woluminie rozruchowym.

Wywołania skryptu za pomocą następującego polecenia:
```
./azure_hana_replication_status.pl
```

Dane wyjściowe są podzielone według woluminu na następujące sekcje:  

- Stan linku
- Bieżące działanie replikacji
- Ostatnia migawka replikowane 
- Rozmiar najnowszej migawki
- Bieżący czas opóźnienia między migawkami (od ostatniej replikacji migawki ukończone, a teraz)

Stan łącza jest wyświetlany jako **Active** chyba że łącza między lokacjami nie działa lub jest zdarzeniem aktualnie trwająca trybu failover. Działanie replikacji adresów, czy wszystkie dane, jest obecnie replikowana lub jest w stanie bezczynności, czy obecnie występują inne działania łącza. Ostatnia migawka replikowane tylko powinny się wyświetlać jako `snapmirror…`. Rozmiar ostatnia migawka zostanie wyświetlona. Na koniec jest wyświetlany czas opóźnienia. Czas opóźnienia reprezentuje czas zaplanowanej replikacji, aby po zakończeniu replikacji. Opóźnienie może być większa niż godziny replikacji danych, szczególnie w przypadku replikacji początkowej, mimo że uruchomieniu replikacji. Czas opóźnienia w dalszym ciągu zwiększyć zakończenie trwającej replikacji.

Oto przykładowe dane wyjściowe:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```

**Następne kroki**
- Zapoznaj się [monitorowanie i rozwiązywanie problemów ze strony HANA](hana-monitor-troubleshoot.md).
