---
title: Procedura pracy awaryjnej hana do lokacji awarii dla sap HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jak wykonać tryb failover do lokacji odzyskiwania po awarii dla SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617135"
---
# <a name="disaster-recovery-failover-procedure"></a>Procedura przechodzenia w tryb failover odzyskiwania po awarii


>[!IMPORTANT]
>Ten artykuł nie zastępuje dokumentacji administracyjnej SAP HANA ani notatek SAP. Oczekujemy, że masz solidne zrozumienie i wiedzę w zakresie administracji i operacji SAP HANA, szczególnie w zakresie tworzenia kopii zapasowych, przywracania, wysokiej dostępności i odzyskiwania po awarii (DR). W tym artykule wyświetlane są zrzuty ekranu z SAP HANA Studio. Zawartość, struktura i charakter ekranów narzędzi administracyjnych SAP i samych narzędzi może ulec zmianie z wersji SAP HANA do wydania.

Istnieją dwa przypadki, które należy wziąć pod uwagę podczas pracy awaryjnej w witrynie odzyskiwania po awarii:

- Potrzebujesz bazy danych SAP HANA, aby wrócić do najnowszego stanu danych. W takim przypadku istnieje samoobsługowy skrypt, za pomocą którego można wykonać funkcję failover bez konieczności kontaktowania się z firmą Microsoft. W przypadku powrotu po awarii należy współpracować z firmą Microsoft.
- Chcesz przywrócić migawkę magazynu, która nie jest najnowszą replikowaną migawką. W takim przypadku należy współpracować z firmą Microsoft. 

>[!NOTE]
>Następujące kroki należy wykonać na jednostce dużych wystąpień HANA, która reprezentuje jednostkę odzyskiwania po awarii. 
 
Aby przywrócić najnowsze migawki z replikowanego magazynu, wykonaj kroki opisane w "Wykonaj pełną funkcję failover — azure_hana_dr_failover odzyskiwania po awarii" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf) 

Jeśli chcesz mieć wiele wystąpień SAP HANA w razie awarii, uruchom polecenie azure_hana_dr_failover kilka razy. Gdy zażądasz, wprowadź identyfikator SID SAP HANA, który chcesz zakończyć w pracy awaryjnej i przywrócić. 


Można przetestować pracy awaryjnej odzyskiwania po awarii również bez wpływu na relację replikacji rzeczywistej. Aby wykonać test pracy awaryjnej, wykonaj kroki opisane w "Wykonywanie testowego odzyskiwania po awarii — azure_hana_test_dr_failover" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf) 

>[!IMPORTANT]
>*Nie* należy uruchamiać żadnych transakcji produkcyjnych w wystąpieniu utworzonym w lokacji odzyskiwania po awarii w procesie **testowania pracy awaryjnej.** Polecenie azure_hana_test_dr_failover tworzy zestaw woluminów, które nie mają związku z lokacją główną. W rezultacie synchronizacja z powrotem do lokacji głównej *nie* jest możliwa. 

Jeśli chcesz mieć wiele wystąpień SAP HANA do testowania, uruchom skrypt kilka razy. Gdy zażądano, wprowadź identyfikator SID SAP HANA wystąpienia, które chcesz przetestować pod kątem pracy awaryjnej. 

>[!NOTE]
>Jeśli musisz wykonać awaryjną do lokacji odzyskiwania po awarii, aby uratować niektóre dane, które zostały usunięte kilka godzin temu i trzeba woluminów odzyskiwania po awarii, które mają być ustawione na wcześniejszą migawkę, ta procedura ma zastosowanie. 

1. Zamknij nieprodukcyjne wystąpienie HANA w jednostce odzyskiwania po awarii dużych wystąpień HANA, które są uruchomione. Uśpione wystąpienie produkcyjne HANA jest preinstalowane.
1. Upewnij się, że nie są uruchomione żadne procesy SAP HANA. Użyj następującego polecenia dla tego sprawdzenia:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Dane wyjściowe powinny wyświetlać proces **hdbdaemon** w stanie zatrzymania i żadnych innych procesów HANA w stanie uruchomionym lub uruchomionym.
1. Określ, do której nazwy migawki lub identyfikator kopii zapasowej SAP HANA chcesz przywrócić lokację odzyskiwania po awarii. W przypadkach odzyskiwania po awarii ta migawka jest zwykle najnowszą migawką. Jeśli chcesz odzyskać utracone dane, wybierz wcześniejszą migawkę.
1. Skontaktuj się z pomocą techniczną platformy Azure za pośrednictwem żądania pomocy technicznej o wysokim priorytecie. Poproś o przywrócenie tej migawki z nazwą i datą migawki lub identyfikatorem kopii zapasowej HANA w witrynie odzyskiwania po awarii. Domyślnie po stronie operacji przywraca tylko wolumin /hana/data. Jeśli chcesz mieć /hana/logbackups woluminów zbyt, należy w szczególności stwierdzić, że. *Nie należy przywracać woluminu /hana/shared.* Zamiast tego po ponownym zamontowaniu woluminu /hana/shared dla PRD wybierz określone pliki, takie jak global.ini z katalogu **.snapshot** i jego podkatalogów. 

   Po stronie operacji występują następujące kroki:

   a. Replikacja migawek z woluminu produkcyjnego do woluminów odzyskiwania po awarii jest zatrzymana. To zakłócenie mogło już mieć miejsce, jeśli awaria w lokacji produkcyjnej jest powodem konieczności wykonania procedury odzyskiwania po awarii.
   
   b. Nazwa migawki magazynu lub migawka z wybranym identyfikatorem kopii zapasowej jest przywracana na woluminach odzyskiwania po awarii.
   
   d. Po przywróceniu woluminy odzyskiwania po awarii są dostępne do zamontowania do jednostek dużych wystąpień HANA w regionie odzyskiwania po awarii.
      
1. Zainstaluj woluminy odzyskiwania po awarii do jednostki dużych wystąpień HANA w lokacji odzyskiwania po awarii. 
1. Uruchom uśpione wystąpienie produkcyjne SAP HANA.
1. Jeśli wybrano kopiowanie dzienników kopii zapasowych dziennika transakcji w celu skrócenia czasu operacji RPO, należy scalić kopie zapasowe dziennika transakcji w nowo zainstalowanym katalogu DR /hana/logbackups. Nie zastępuj istniejących kopii zapasowych. Kopiowanie nowszych kopii zapasowych, które nie zostały zreplikowane przy obliczu najnowszej replikacji migawki magazynu.
1. Można również przywrócić pojedyncze pliki z migawek, które nie zostały zreplikowane do woluminu /hana/shared/PRD w regionie platformy DR Azure.

Poniższe kroki pokazują, jak odzyskać wystąpienie produkcyjne SAP HANA na podstawie przywróconej migawki magazynu i kopii zapasowych dziennika transakcji, które są dostępne.

1. Zmień lokalizację kopii zapasowej na **/hana/logbackups** przy użyciu sap HANA Studio.

   ![Zmienianie lokalizacji kopii zapasowej w celu odzyskiwania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA skanuje lokalizacje plików kopii zapasowych i sugeruje najnowszą kopię zapasową dziennika transakcji, aby przywrócić. Skanowanie może potrwać kilka minut, aż pojawi się ekran podobny do następującego:

   ![Lista kopii zapasowych dziennika transakcji do odzyskiwania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Dostosuj niektóre ustawienia domyślne:

      - Wyczyść **użyj kopii zapasowych delta**.
      - Wybierz **opcję Inicjuj obszar dziennika**.

   ![Ustawianie obszaru dziennika inicjowania](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wybierz **pozycję Zakończ**.

   ![Zakończenie przywracania odzyskiwania po awarii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Powinno pojawić się okno postępu, takie jak pokazane tutaj. Należy pamiętać, że w przykładzie jest przywracanie odzyskiwania po awarii konfiguracji SAP HANA skalowania trzech węzłów w poziomie.

![Przywracanie postępu](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Jeśli przywracanie przestaje **odpowiadać** na ekranie Zakończ i nie wyświetla ekranu postępu, upewnij się, że wszystkie wystąpienia SAP HANA w węzłach procesu roboczego są uruchomione. W razie potrzeby uruchom wystąpienia SAP HANA ręcznie.


## <a name="failback-from-a-dr-to-a-production-site"></a>Powrót po awarii z odzyskiwania po awarii do zakładu produkcyjnego
Można przywrócić po awarii z odzyskiwania po awarii do zakładu produkcyjnego. Przyjrzyjmy się scenariuszowi, w którym praca awaryjna w lokacji odzyskiwania po awarii została spowodowana przez problemy w regionie platformy Azure produkcyjnej, a nie przez potrzebę odzyskania utraconych danych. 

Od jakiegoś czasu uruchamiasz obciążenie produkcyjne sap w lokacji odzyskiwania po awarii. Ponieważ problemy w lokacji produkcyjnej są rozwiązywane, chcesz wrócić po awarii do lokacji produkcyjnej. Ponieważ nie można utracić danych, krok z powrotem do lokacji produkcyjnej obejmuje kilka kroków i ścisłej współpracy z SAP HANA na zespół operacyjny platformy Azure. To do Ciebie, aby wyzwolić zespół operacyjny, aby rozpocząć synchronizację z powrotem do lokacji produkcyjnej po rozwiązaniu problemów.

Wykonaj następujące kroki:

1. Sap HANA na platformie Azure operations team pobiera wyzwalacz do synchronizowania woluminów magazynu produkcyjnego z woluminów magazynu odzyskiwania po awarii, które obecnie reprezentują stan produkcji. W tym stanie jednostka dużych wystąpień HANA w lokacji produkcyjnej jest zamykana.
1. Zespół operacyjny SAP HANA on Azure monitoruje replikację i upewnia się, że zostanie przechwycona, zanim cię poinformuje.
1. Zamknij aplikacje, które używają produkcyjnego wystąpienia HANA w lokacji odzyskiwania po awarii. Następnie należy wykonać kopię zapasową dziennika transakcji HANA. Następnie należy zatrzymać wystąpienie HANA, który jest uruchomiony na jednostek dużych wystąpień HANA w lokacji odzyskiwania po awarii.
1. Po wystąpieniu HANA, który jest uruchomiony w jednostce dużych wystąpień HANA w lokacji odzyskiwania po awarii jest zamknięty, zespół operacyjny ręcznie synchronizuje woluminy dysku ponownie.
1. Zespół operacyjny SAP HANA on Azure ponownie uruchamia jednostkę dużych wystąpień HANA w lokacji produkcyjnej. Przekazują ci go. Upewnij się, że wystąpienie SAP HANA jest w stanie zamknięcia w czasie uruchamiania jednostki dużych wystąpień HANA.
1. Kroki przywracania bazy danych są wykonywane podczas wykonywania po awarii lokacji odzyskiwania po awarii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorowanie replikacji odzyskiwania po awarii

Aby monitorować stan postępu replikacji magazynu, `azure_hana_replication_status`uruchom skrypt . To polecenie musi być uruchamiane z jednostki, która działa w lokalizacji odzyskiwania po awarii, aby działał zgodnie z oczekiwaniami. Polecenie działa niezależnie od tego, czy replikacja jest aktywna. Polecenie można uruchomić dla każdej jednostki hana duże wystąpienie dzierżawy w lokalizacji odzyskiwania po awarii. Nie można użyć do uzyskania szczegółowych informacji na temat woluminu rozruchowego. 

Aby uzyskać więcej informacji na temat polecenia i jego danych wyjściowych, zobacz "Pobierz stan replikacji odzyskiwania po awarii — azure_hana_replication_status" w [narzędziach migawek firmy Microsoft dla systemu SAP HANA na platformie Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf).


## <a name="next-steps"></a>Następne kroki
- Zobacz [Monitorowanie i rozwiązywanie problemów po stronie HANA](hana-monitor-troubleshoot.md).
