---
title: Rozwiązywanie problemów i monitorowanie SAP HANA na platformie Azure (wystąpienia duże) | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów i monitorowanie SAP HANA na platformie Azure (wystąpienia duże).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41fbeb848d7d97e5ee41a2221b69cc88380dc1e1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657200"
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>Rozwiązywanie problemów i monitorować SAP HANA (duże wystąpień) w systemie Azure


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>Monitorowanie SAP HANA na platformie Azure (wystąpienia duże)

SAP HANA na platformie Azure (wystąpienia duże) nie różni się od innych wdrożeń IaaS — należy monitorować systemu operacyjnego i aplikacji czynności oraz jak korzystać z tych następujące zasoby:

- Procesor CPU
- Memory (Pamięć)
- Przepustowość sieci
- Miejsce na dysku

Zgodnie z maszyn wirtualnych Azure należy ustalić czy klasy zasobów o nazwie powyżej są wystarczające, lub czy pobrać te wyczerpany. Poniżej przedstawiono więcej szczegółów na każdym z różnych klas:

**Zużycie zasobów procesora CPU:** współczynnik SAP zdefiniowane dla niektórych obciążeń przed HANA jest wymuszana aby upewnić się, że powinien być wystarczająco dużo zasobów procesora CPU do pracy za pośrednictwem dane, które są przechowywane w pamięci. Niemniej jednak może być przypadki, w którym HANA zużywa dużo Procesora wykonywania zapytań ze względu na brakujące indeksy lub podobne problemy. Oznacza to, że należy monitorować zużycia zasobów procesora CPU HANA dużych wystąpienia jednostki, a także zasobów Procesora używanych przez określone usługi HANA.

**Zużycie pamięci:** ważne jest, aby monitorować z wewnątrz HANA, a także poza HANA w jednostce. W ramach HANA monitorowanie, jak danych zajmuje przydzielonej pamięci, aby wypełnić założenia wymagane zmiany rozmiaru SAP HANA. Należy monitorować zużycie pamięci na poziomie wystąpienia duży do upewnij się, że dodatkowe zainstalowanych z systemem innym niż — HANA oprogramowania nie zużywa zbyt dużej ilości pamięci i w związku z tym konkurują ze HANA pamięci.

**Przepustowość sieci:** bramy sieci wirtualnej Azure ma ograniczoną przepustowość danych do sieci wirtualnej Azure, więc jest przydatne do monitorowania danych odebranych przez wszystkie maszyny wirtualne Azure w ramach sieci wirtualnej, aby dowiedzieć się, jak blisko zostaną limitów jednostka SKU bramy Azure możesz zaznaczenia ected. W jednostce wystąpienia dużych HANA go sensu przychodzący i wychodzący ruch sieciowy również monitorować i do śledzenia w woluminach, które są obsługiwane w czasie.

**Miejsce na dysku:** użycie miejsca na dysku zwykle zwiększa się wraz z upływem czasu. Istnieje wiele powodów, ale większość wszystkich: wzrasta ilość danych, wykonywania kopii zapasowej dziennika transakcji, przechowywanie plików śledzenia i wykonywania migawek magazynu. Dlatego jest ważne monitorować użycie miejsca na dysku i zarządzać nimi skojarzonego z jednostką HANA wystąpienia duża ilość miejsca na dysku.

Dla **jednostki SKU II typu** HANA wystąpień duży, serwer jest dostarczany z narzędzia diagnostyczne systemu wstępnie. Można korzystać z tych narzędzi diagnostycznych sprawdzania kondycji systemu. Uruchom następujące polecenie w celu wygenerowania kondycji Sprawdź plik dziennika na /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Podczas pracy z zespołu Support firmy Microsoft, aby rozwiązać problem, użytkownik również może poproszony o podanie pliki dziennika przy użyciu tych narzędzi diagnostycznych. Można skompresować plik przy użyciu następującego polecenia.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```


## <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorowanie i rozwiązywanie problemów z HANA strony

Aby skutecznie analizować problemy związane z SAP HANA na platformie Azure (wystąpienia duże), jest przydatne do zawężania główną przyczynę problemu. SAP został opublikowany dużą ilość dokumentację ułatwiającą.

Dotyczy — często zadawane pytania dotyczące wydajności SAP HANA znajdują się w uwagach SAP do następujących:

- [Uwaga SAP #2222200 — często zadawane pytania: SAP HANA sieci](https://launchpad.support.sap.com/#/notes/2222200)
- [Uwaga SAP #2100040 — często zadawane pytania: SAP HANA Procesora](https://launchpad.support.sap.com/#/notes/0002100040)
- [Uwaga SAP #199997 — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Uwaga #200000 — często zadawane pytania: Optymalizacja wydajności HANA SAP](https://launchpad.support.sap.com/#/notes/2000000)
- [Uwaga SAP #199930 — często zadawane pytania: SAP HANA we/wy analizy](https://launchpad.support.sap.com/#/notes/1999930)
- [Uwaga SAP #2177064 — często zadawane pytania: Uruchom ponownie SAP HANA usługi i awarii](https://launchpad.support.sap.com/#/notes/2177064)

**SAP HANA alertów**

Pierwszym krokiem zapoznaj się z dziennikami alertu bieżącego SAP HANA. W systemie SAP HANA Studio przejdź do **konsoli administracyjnej: alerty: Pokaż: wszystkie alerty**. Na tej karcie zostaną wyświetlone wszystkie alerty SAP HANA określone wartości (wolnej pamięci fizycznej, użycie procesora CPU, itp.), które dzielą się poza Ustaw progi minimalną i maksymalną. Domyślnie sprawdza są odświeżane automatycznie co 15 minut.

![W systemie SAP HANA Studio przejdź do konsoli administracyjnej: alerty: Pokaż: wszystkie alerty](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

Alert wywołany z powodu ustawienia nieprawidłowej wartości progowej rozwiązanie jest resetowane do wartości domyślnej lub bardziej przystępne wartość progową.

![Zresetuj wartość domyślną lub bardziej przystępne wartość progu](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Następujące alerty może wskazywać na problemy z zasobami Procesora:

- Użycie procesora CPU hosta (alertu 5)
- Operacja punktu zapisu najnowszych (28 alertów)
- Czas trwania punktu zapisu (54 alertów)

Można zauważyć wysokie użycie procesora CPU w Twojej bazie danych SAP HANA z jednego z następujących czynności:

- Alert 5 (użycie procesora CPU hosta) jest wywoływane bieżące lub wcześniejsze użycia procesora CPU
- Użycie procesora CPU wyświetlanych na ekranie — omówienie

![Wyświetlane użycie procesora CPU na ekran Przegląd](./media/troubleshooting-monitoring/image3-cpu-usage.png)

W przeszłości wykres obciążenia mogą być wyświetlane wysokie użycie procesora CPU lub wysokie zużycie:

![Wykres obciążenia mogą być wyświetlane w przeszłości wysokie użycie procesora CPU lub wysokie zużycie](./media/troubleshooting-monitoring/image4-load-graph.png)

Alert wyzwolony ze względu na wysokie wykorzystanie procesora CPU może być spowodowane przez kilka przyczyn, w tym między innymi: wykonanie pewnych transakcji, ładowanie danych, wysunięć zadania, długotrwałą instrukcji SQL i wydajności zapytania (na przykład z BW na HANA moduły).

Zapoznaj się [SAP HANA Rozwiązywanie problemów z: pokrewne powoduje procesora CPU i rozwiązań](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) lokacji szczegółowe kroki rozwiązywania problemów.

**System operacyjny**

Jednym z najważniejszych wyszukuje SAP HANA w systemie Linux jest upewnij się, że wyłączono przezroczysty dużych stron, zobacz [2131662 # Uwaga SAP — przezroczysty dużych stron (THP) na serwerach z SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Możesz sprawdzić, czy przezroczyste dużych stron są włączone, za pomocą następującego polecenia Linux: **cat /sys/kernel/mm/transparent\_hugepage lub nie włączono**
- Jeśli _zawsze_ jest ujęta w nawiasy kwadratowe zgodnie z poniższymi instrukcjami, oznacza to, czy przezroczyste dużych stron są włączone: [zawsze] madvise nigdy nie; Jeśli _nigdy nie_ jest ujęta w nawiasy kwadratowe zgodnie z poniższymi instrukcjami, oznacza, że ogromnych przezroczysty Strony są wyłączone: zawsze madvise [nigdy nie]

Polecenie Linux powinien zwrócić nic: **obr. / min — odpowiedzi na pytania | grep ulimit.** Jeśli zostanie wyświetlona _ulimit_ jest zainstalowane, odinstaluj go bezpośrednio.

**Pamięci**

Można zauważyć, że ilość pamięci przydzielonej przez bazę danych SAP HANA jest większa niż oczekiwano. Następujące alerty wskazują na problemy z wysokie użycie pamięci:

- Użycie pamięci fizycznej hosta (alertu 1)
- Użycie pamięci serwera nazw (12 alertów)
- Łączne użycie pamięci magazynu kolumn w tabelach (40 alertów)
- Użycie pamięci usług (43 alertów)
- Użycie pamięci magazynu głównego magazynu kolumn tabel (45 alertów)
- Pliki zrzutu środowiska uruchomieniowego (46 alertów)

Zapoznaj się [SAP HANA rozwiązywania problemów: problemy z pamięcią](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) lokacji szczegółowe kroki rozwiązywania problemów.

**Sieć**

Zapoznaj się [SAP Uwaga #2081065 — Rozwiązywanie problemów z programem SAP HANA sieci](https://launchpad.support.sap.com/#/notes/2081065) i wykonywać rozwiązywania problemów w tej uwagi SAP sieciowych.

1. Analizowanie czasu Rundy między serwerem a klientem.
  A. Uruchom skrypt SQL [ _HANA\_sieci\_klientów_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Przeanalizuj komunikacji między węzłami.
  A. Uruchom skrypt SQL [ _HANA\_sieci\_usług_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Uruchom polecenie Linux **ifconfig** (dane wyjściowe zawierają, jeśli występują strat pakietów).
4. Uruchom polecenie Linux **tcpdump**.

Należy także użyć typu open source [dotyczące programu Iperf;](https://iperf.fr/) narzędzia (lub podobny) do mierzenia wydajności sieci rzeczywistej aplikacji.

Zapoznaj się [SAP HANA rozwiązywania problemów: wydajność sieci i występują problemy z łącznością](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) lokacji szczegółowe kroki rozwiązywania problemów.

**Storage**

Z perspektywy użytkownika końcowego aplikacja (lub system jako całość) działa wolno, nie odpowiada lub nawet może wydawać się zawieszenie, jeśli występują problemy z wydajnością we/wy. W **woluminów** kartę w SAP HANA Studio widoczne dołączone woluminy i woluminy są używane przez każdej usługi.

![Na karcie woluminy w SAP HANA Studio widoczne dołączone woluminy i woluminy są używane przez każdego usługi](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Dołączone woluminy w dolnej części ekranu można wyświetlić szczegóły woluminów, takie jak pliki i statystyki we/wy.

![Dołączone woluminy w dolnej części ekranu można wyświetlić szczegóły woluminów, takie jak pliki i statystyki we/wy](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Zapoznaj się [SAP HANA Rozwiązywanie problemów z: We/Wy pokrewne główne przyczyny i rozwiązania](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) i [SAP HANA Rozwiązywanie problemów z: dysku pokrewne główne przyczyny i rozwiązania](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) lokacji szczegółowe kroki rozwiązywania problemów.

**Narzędzia diagnostyczne**

Wykonaj sprawdzenie kondycji programu SAP HANA za pośrednictwem HANA\_konfiguracji\_Minichecks. To narzędzie zwraca potencjalnie krytycznych problemów technicznych, które powinny już mieć został zgłoszony jako ostrzeżenia w SAP HANA Studio.

Zapoznaj się [1969700 # Uwaga SAP — kolekcja instrukcji SQL dla SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) i Pobierz plik SQL Statements.zip dołączony do tej uwagi. Należy zapisać ten plik zip na lokalnym dysku twardym.

W systemie SAP HANA Studio na **informacje o systemie** karcie, kliknij prawym przyciskiem myszy w **nazwa** kolumny i wybierz **instrukcje SQL importu**.

![W SAP HANA Studio, na karcie informacji o systemie kliknij prawym przyciskiem myszy nazwę kolumny, a następnie wybierz importu instrukcje SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Wybierz plik SQL Statements.zip przechowywane lokalnie, a nie zostaną zaimportowane do folderu z odpowiedniej instrukcji SQL. W tym momencie można uruchomić wiele różnych kontroli diagnostycznych z tych instrukcji SQL.

Na przykład, aby sprawdzić wymagania dotyczące przepustowości replikacji systemu SAP HANA, kliknij prawym przyciskiem myszy **przepustowości** instrukcji w obszarze **replikacji: przepustowości** i wybierz **Otwórz** w Konsola programu SQL.

Pełną instrukcję SQL zostanie otwarty, umożliwiając parametrów wejściowych (modyfikacji sekcja) zostać zmienione, a następnie wykonać.

![Otwiera pełną instrukcję SQL stosowanie parametrów wejściowych (modyfikacji sekcja) zostać zmienione, a następnie wykonać](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Innym przykładem jest kliknięcie prawym przyciskiem myszy w instrukcjach w obszarze **replikacji: omówienie**. Wybierz **Execute** z menu kontekstowego:

![Innym przykładem jest kliknięcie prawym przyciskiem myszy w instrukcjach w ramach replikacji: omówienie. Wybierz z menu kontekstowego Execute](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Powoduje to informacje pomocne przy rozwiązywaniu problemów:

![Spowoduje to informacje, które ułatwią rozwiązywanie problemów](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Wykonaj te same czynności dla HANA\_konfiguracji\_Minichecks i zaznacz pole wyboru dla dowolnej _X_ oznacza w _C_ kolumny (krytyczne).

Przykładowe wyniki:

**HANA\_konfiguracji\_MiniChecks\_Rev102.01 + 1** kontroli ogólne SAP HANA.

![HANA\_konfiguracji\_MiniChecks\_Rev102.01 + 1 dla ogólnych kontroli SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_usług\_omówienie** omówienie co SAP HANA usługi są aktualnie uruchomione.

![HANA\_usług\_omówienie omówienie co SAP HANA usługi są aktualnie uruchomione](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_usług\_statystyki** dla SAP HANA usługi informacji (CPU, pamięci, itd.).

![HANA\_usług\_statystyki dla SAP HANA informacje o usługach ](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfiguracji\_omówienie\_Rev110 +** ogólne informacje na temat wystąpienia SAP HANA.

![HANA\_konfiguracji\_omówienie\_Rev110 + ogólne informacje na temat wystąpienia SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfiguracji\_parametry\_Rev70 +** do sprawdzenia parametrów SAP HANA.

![HANA\_konfiguracji\_parametry\_Rev70 +, aby sprawdzić SAP HANA parametrów](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

