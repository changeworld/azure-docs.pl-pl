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
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f7d4f6216b4a57796ab5c0296713316dd97c47a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64987887"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura przechodzenia w tryb failover odzyskiwania po awarii


>[!IMPORTANT]
>W tym artykule nie jest zamiennikiem dokumentacji administracyjnej platformy SAP HANA lub SAP Notes. Oczekujemy, że masz pełny opis i doświadczenia w zakresie oprogramowania SAP HANA administrację i operacji, szczególnie w przypadku kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii (DR). W tym artykule przedstawiono zrzuty ekranu z SAP HANA Studio. Zawartości, struktury i charakter ekrany narzędzia do administrowania SAP i narzędzi, sami mogą ulec zmianie z platformy SAP HANA wersji.

Istnieją dwa przypadki, które należy rozważyć podczas przejścia w tryb failover do lokacji odzyskiwania po awarii:

- Potrzebujesz bazy danych SAP HANA, aby wrócić do najnowszego stanu danych. W tym przypadku jest skryptem samoobsługi za pomocą którego można wykonać przełączenie w tryb failover bez konieczności kontaktu z firmą Microsoft. Podczas powrotu po awarii musisz pracować z firmą Microsoft.
- Chcesz przywrócić migawkę pamięci masowej, który nie jest najnowsza wersja migawki replikowanych. W takim przypadku potrzebne do pracy z firmą Microsoft. 

>[!NOTE]
>Poniższe kroki należy wykonać w jednostce dużych wystąpień HANA, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze replikowanego magazynu migawek, postępuj zgodnie z instrukcjami w "Wykonaj pełny trybu failover odzyskiwania po awarii — azure_hana_dr_failover" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA w trybie Failover, uruchom polecenie azure_hana_dr_failover kilka razy. Gdy żądanie, wprowadź identyfikator SID HANA SAP, aby tryb failover i przywrócić. 


Możesz również testowanie trybu failover odzyskiwania po awarii, bez wywierania wpływu na relacji replikacji rzeczywiste. Aby wykonać test trybu failover, postępuj zgodnie z instrukcjami w "Wykonaj test trybu failover odzyskiwania po awarii — azure_hana_test_dr_failover" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Czy *nie* uruchamiania dowolnej transakcji do produkcji w wystąpieniu, który został utworzony w lokacji odzyskiwania po awarii w procesie **testowania pracy w trybie failover**. Azure_hana_test_dr_failover polecenie tworzy zestaw woluminów, które nie mają relacji z lokacją główną. W wyniku synchronizacji do lokacji głównej jest *nie* możliwe. 

Jeśli chcesz mieć wiele wystąpień oprogramowania SAP HANA, aby sprawdzić, uruchom skrypt kilka razy. Gdy żądanie, wprowadź identyfikator SID HANA SAP wystąpienie, które chcesz przetestować tryb failover. 

>[!NOTE]
>Ta procedura ma zastosowanie, jeśli musisz przełączyć tryb failover do lokacji odzyskiwania po awarii do ratunkowe niektóre dane, który został usunięty godz. temu i wymagają woluminów odzyskiwania po awarii, należy ustawić wcześniejszej migawki. 

1. Zamknij wystąpienie obniżenia HANA w jednostce odzyskiwania po awarii w dużych wystąpień HANA, której używasz. Jest preinstalowany nieaktywni wystąpienie oprogramowania HANA w środowisku produkcyjnym.
1. Upewnij się, że są uruchomione żadne procesy platformy SAP HANA. Dla tego sprawdzenia, użyj następującego polecenia:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Powinny zostać wyświetlone dane wyjściowe **hdbdaemon** przetwarzanie w stanie zatrzymania i żadne inne procesy HANA w stanie uruchomiona lub jest uruchomiona.
1. Określ, do którego nazwy migawki lub identyfikator kopii zapasowej platformy SAP HANA mają lokacji odzyskiwania po awarii, które są przywracane. W przypadku odzyskiwania po awarii w rzeczywistych ta migawka jest zwykle najnowszą migawkę. Jeśli potrzebujesz odzyskać utracone dane, wybierz wcześniejszej migawki.
1. Skontaktuj się z działem pomocy technicznej platformy Azure za pośrednictwem żądania pomocy technicznej o wysokim priorytecie. Poproś o przywrócenie tej migawki o nazwie i datę migawki lub identyfikator kopii zapasowej platformy HANA w lokacji odzyskiwania po awarii. Wartość domyślna to, że po stronie Operacje przywraca/hana/ilość danych tylko. Jeśli chcesz mieć/hana/logbackups woluminów zbyt należy wyraźnie określają, że. *Nie należy przywracać /hana/shared woluminu.* Zamiast tego wybierz konkretne pliki, takie jak global.ini poza **.snapshot** katalogu i jego podkatalogach po można ponownie zainstalować/hana/udostępniony wolumin PRD. 

   Na stronie operacje wykonane następujące kroki:

   a. Replikacji migawek woluminu odzyskiwania po awarii z woluminu produkcji jest zatrzymana. Ta przerw w działaniu może już się to zdarzyć w przypadku wystąpienia awarii w czasie produkcji Przyczyna, że musisz wykonać procedurę odzyskiwania po awarii.
   
   b. Magazyn migawek nazwy ani tworzyć migawek identyfikatorem kopii zapasowej została wybrana opcja został przywrócony na woluminu odzyskiwania po awarii.
   
   c. Po przywróceniu woluminu odzyskiwania po awarii są dostępne dla zamontowania jednostkom duże wystąpienie oprogramowania HANA w regionie odzyskiwania po awarii.
      
1. Instalowanie woluminów odzyskiwania po awarii do jednostki duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii. 
1. Rozpocznij nieaktywni wystąpienie SAP HANA w środowisku produkcyjnym.
1. Jeśli wybrano opcję Kopiuj dzienniki kopii zapasowych dziennika transakcji w celu skrócenia czasu cel punktu odzyskiwania, scalić nowo zainstalowanego odzyskiwania po awarii/hana lub logbackups katalogu kopie zapasowe dziennika transakcji. Nie Nadpisz istniejące kopie zapasowe. Skopiuj nowszych kopii zapasowych, które nie zostały zreplikowane przy użyciu najnowszych replikacji migawki magazynu.
1. Można także przywrócić pojedynczych plików z migawek, które nie zostały zreplikowane do woluminu /hana/shared/PRD w regionie odzyskiwania po awarii platformy Azure.

Poniższe kroki pokazują jak odzyskać wystąpienia produkcyjnych oprogramowania SAP HANA na podstawie migawek magazynu przywrócona i kopie zapasowe dziennika transakcji, które są dostępne.

1. Zmień lokalizację kopii zapasowej na **/hana/logbackups** przy użyciu programu SAP HANA Studio.

   ![Zmiana lokalizacji kopii zapasowej do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skanuje za pośrednictwem lokalizacji kopii zapasowej plików i sugeruje najnowszej kopii zapasowej dziennika transakcji do przywrócenia. Skanowanie może potrwać kilka minut, aż do ekranu, tak jak pojawia się następujący komunikat:

   ![Lista kopie zapasowe dziennika transakcji do odzyskania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Dostosuj niektóre z domyślnych ustawień:

      - Wyczyść **użycie różnicowych kopii zapasowych**.
      - Wybierz **zainicjować dziennika obszaru**.

   ![Ustaw obszar zainicjować dziennika](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wybierz pozycję **Finish** (Zakończ).

   ![Zakończ Przywracanie odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinna pojawić się okno postępu, jak pokazano w tym miejscu. Należy pamiętać, że w przykładzie występuje przywracania odzyskiwania po awarii w konfiguracji platformy SAP HANA skalowalnego w poziomie trzema węzłami.

![Przywracanie w toku](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Jeśli przywracania przestaje odpowiadać podczas **Zakończ** ekranu i nie pokazuj ekran pokazujący postęp, upewnij się, że są uruchomione wszystkie wystąpienia oprogramowania SAP HANA na węzłach procesu roboczego. Jeśli to konieczne, należy ręcznie uruchomić wystąpieniami platformy SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym
Można powrotu po awarii z odzyskiwania po awarii do lokacji w środowisku produkcyjnym. Przyjrzyjmy się scenariusz, w którym przełączenie w tryb failover do lokacji odzyskiwania po awarii zostało spowodowane przez problemy w środowisku produkcyjnym region platformy Azure i nie trzeba odzyskać utracone dane. 

Działała używanych obciążeń produkcyjnych SAP od pewnego czasu w lokacji odzyskiwania po awarii. Jak są rozwiązywane problemy z witryny produkcyjnej, chcesz powrócić po awarii do witryn produkcyjnych. Ponieważ nie może utracić dane, krok do miejsca produkcji obejmuje kilka kroków i ścisłej współpracy z platformą SAP HANA w zespole operacji na platformie Azure. To Ty możesz wyzwolić zespół operacyjny, należy rozpocząć synchronizowanie wróć do witryny produkcyjnej po problemy są rozwiązywane.

Wykonaj następujące kroki:

1. SAP HANA w zespole operacji na platformie Azure pobiera wyzwalacz, aby zsynchronizować woluminów magazynu produkcji z woluminów magazynu odzyskiwania po awarii, które teraz reprezentują stan produkcji. W tym stanie jednostkę dużych wystąpień HANA w środowisku produkcyjnym lokacji jest wyłączana.
1. SAP HANA w zespole operacji na platformie Azure monitoruje replikacji i upewnia się, że zostanie przechwycony przed ich informujące.
1. Aplikacje, które używają wystąpienie oprogramowania HANA w środowisku produkcyjnym w lokacji odzyskiwania po awarii zostanie zamknięta. Następnie należy wykonać kopii zapasowej dziennika transakcji platformy HANA. Następnie można zatrzymać wystąpienie oprogramowania HANA, które działa w jednostkach duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii.
1. Po wystąpienie oprogramowania HANA, które działa w jednostce duże wystąpienie oprogramowania HANA w lokacji odzyskiwania po awarii jest zamknięta, zespół operacyjny ręcznie synchronizuje woluminy dysków ponownie.
1. SAP HANA w zespole operacji na platformie Azure jednostki duże wystąpienie oprogramowania HANA w środowisku produkcyjnym lokacji zostanie ponownie uruchomiona. Mogą przekazać go do Ciebie. Należy upewnić się, wystąpienie SAP HANA jest w czasie uruchamiania dużych wystąpień HANA jednostki w stanie zamknięcie.
1. Możesz wykonać te same kroki przywracania bazy danych, których nie było po użytkownik wcześniej przełączone w tryb failover do lokalizacji odzyskiwania po awarii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Aby monitorować stan postęp replikacji magazynu, uruchom skrypt `azure_hana_replication_status`. To polecenie należy uruchomić z jednostki, która działa w lokalizacji odzyskiwania po awarii, aby działać zgodnie z oczekiwaniami. Polecenie działa niezależnie od tego, czy replikacja jest aktywna. Polecenie można uruchomić dla każdej jednostki dużych wystąpień HANA dzierżawy w lokalizacji odzyskiwania danych po awarii. Nie można uzyskać szczegółowe informacje o woluminie rozruchowym. 

Aby uzyskać więcej informacji na polecenia i jego danych wyjściowych, zobacz "Pobierz stan replikacji dla odzyskiwania po awarii — azure_hana_replication_status" w [Microsoft migawki narzędzia dla oprogramowania SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Kolejne kroki
- Zobacz [monitorowanie i rozwiązywanie problemów ze strony HANA](hana-monitor-troubleshoot.md).
