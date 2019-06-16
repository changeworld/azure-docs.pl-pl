---
title: Monitorowanie i rozwiązywanie problemów z platformy HANA stronie na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA na platformie SAP HANA na platformie Azure (duże wystąpienia).
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
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 15b3fb4ae483e5b0e4f930d0dc08de6d198d0e5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61128971"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA

Aby skutecznie analizować problemy związane z platformą SAP HANA na platformie Azure (duże wystąpienia), warto zawęzić główną przyczynę problemu. SAP opublikował dużą ilość dokumentację, aby pomóc.

Zastosowanie — często zadawane pytania dotyczące wydajności platformy SAP HANA można znaleźć w następujących SAP Notes:

- [Uwaga SAP #2222200 — często zadawane pytania: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [Uwaga SAP #2100040 — często zadawane pytania: SAP HANA Z PROCESORA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Uwaga SAP #199997 — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/2177064)
- [Uwaga SAP #200000 — często zadawane pytania: Optymalizacja wydajności programu SAP HANA](https://launchpad.support.sap.com/#/notes/2000000)
- [Uwaga SAP #199930 — często zadawane pytania: Analiza operacji We/Wy programu SAP HANA](https://launchpad.support.sap.com/#/notes/1999930)
- [Uwaga SAP #2177064 — często zadawane pytania: SAP HANA usługi, uruchom ponownie i ulega awarii](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA alertów

Pierwszym krokiem Sprawdź dzienniki alertu bieżącej platformy SAP HANA. W systemie SAP HANA Studio przejdź do **konsoli administracyjnej: Alerty: Pokaż: wszystkie alerty**. Na tej karcie, zostaną wyświetlone wszystkie alerty platformy SAP HANA dla określonych wartości (ilości wolnej pamięci fizycznej, użycie procesora CPU itp.), wykraczających poza Ustaw progi minimalną i maksymalną. Domyślnie testy są odświeżane automatycznie co 15 minut.

![W systemie SAP HANA Studio przejdź do konsoli administracyjnej: Alerty: Pokaż: wszystkie alerty](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor CPU

Alertu wyzwolona ze względu na ustawienie progu niewłaściwy rozwiązanie jest resetowane do wartości domyślnej lub bardziej uzasadnionego wartość progowa.

![Przywróć wartości domyślne lub bardziej uzasadnionego wartość progowa](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Następujące alerty mogą wskazywać problemy zasobów procesora CPU:

- Użycie procesora CPU hosta (alertu 5)
- Operacja punktu zapisu najnowsze (28 alertów)
- Czas trwania punktu zapisu (54 alertów)

Można zauważyć wysokie użycie procesora CPU w bazie danych SAP HANA z jednej z następujących czynności:

- Alertu 5 (użycie Procesora hosta) jest zgłaszane w bieżącym lub ostatnim użycia procesora CPU
- Użycie procesora CPU wyświetlanych na ekran Przegląd

![Wyświetlane użycie procesora CPU na ekran Przegląd](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Załaduj wykres może wyświetlać wysokie użycie procesora CPU lub wysokie zużycie w przeszłości:

![Załaduj wykres może wyświetlać wysokie użycie procesora CPU lub wysokie zużycie w przeszłości](./media/troubleshooting-monitoring/image4-load-graph.png)

Alert wywołany ze względu na wysokie wykorzystanie procesora CPU może być spowodowane przez kilka powodów, w tym między innymi: wykonanie niektórych transakcji, ładowania danych, zadania, które nie odpowiadają, długie wykonywanie instrukcji SQL i wydajności zapytania (na przykład za pomocą BW on HANA modułów).

Zapoznaj się [SAP HANA rozwiązywania problemów: Powiązane powoduje procesora CPU i rozwiązania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) lokacji, aby uzyskać szczegółowe kroki rozwiązywania problemów.

## <a name="operating-system"></a>System operacyjny

Jednym z najważniejszych kontroli w odniesieniu do oprogramowania SAP HANA w systemie Linux jest upewnij się, wyłączenia przezroczyste ogromna stron, zobacz [2131662 # Uwaga SAP — przezroczyste ogromna stron (THP) na serwerach z SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Możesz sprawdzić, czy przezroczyste ogromna strony są włączone za pomocą następującego polecenia w systemie Linux: **cat /sys/kernel/mm/transparent\_hugepage lub nie włączono**
- Jeśli _zawsze_ jest ujęty w nawiasy kwadratowe zgodnie z poniższymi instrukcjami, oznacza to, włączenia przezroczyste stron ogromna: [zawsze] madvise nigdy nie; Jeśli _nigdy nie_ jest ujęty w nawiasy kwadratowe zgodnie z poniższymi instrukcjami, oznacza to, że przezroczysty duży Strony są wyłączone: zawsze madvise [nigdy nie]

Następujące polecenie w systemie Linux należy zwrócenia żadnych wyników: **obr. / min — odpowiedzi na pytania | grep ulimit.** Jeśli zostanie wyświetlona _ulimit_ jest zainstalowane, odinstaluj je bezpośrednio.

## <a name="memory"></a>Memory (Pamięć)

Można zaobserwować, że ilość pamięci przydzielonej przez bazę danych SAP HANA jest większa niż oczekiwano. Następujące alerty wskazują na problemy z użyciem dużą ilość pamięci:

- Użycie pamięci fizycznej hosta (Alert 1)
- Użycie pamięci serwera nazw (12 alertów)
- Łączne użycie pamięci Store kolumny tabel (40 alertów)
- Użycie pamięci przez usługi (43 alertów)
- Użycie pamięci przez głównego magazynu tabel Store kolumny (45 alertów)
- Pliki zrzutu środowiska uruchomieniowego (46 alertów)

Zapoznaj się [SAP HANA rozwiązywania problemów: Problemy z pamięcią](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) lokacji, aby uzyskać szczegółowe kroki rozwiązywania problemów.

## <a name="network"></a>Sieć

Zapoznaj się [2081065 # Uwaga SAP — Rozwiązywanie problemów z sieci programu SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) i wykonać rozwiązywania problemów w ta uwaga SAP sieciowych.

1. Analizowanie czas obustronnej konwersji między serwerem a klientem.
  A. Uruchom skrypt SQL [ _HANA\_sieci\_klientów_](https://launchpad.support.sap.com/#/notes/1969700) _._
  
2. Analizuj komunikacji między węzłami.
  A. Uruchom skrypt SQL [ _HANA\_sieci\_usług_](https://launchpad.support.sap.com/#/notes/1969700) _._

3. Uruchom polecenie Linux **ifconfig** (dane wyjściowe pokazują, jeśli występują jakiekolwiek straty pakietów).
4. Uruchom polecenie Linux **tcpdump**.

Ponadto, należy użyć "open source" [dotyczące programu Iperf;](https://iperf.fr/) narzędzia (lub podobny) do pomiaru wydajności sieci w rzeczywistej aplikacji.

Zapoznaj się [SAP HANA rozwiązywania problemów: Wydajność sieci i problemy z łącznością](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) lokacji, aby uzyskać szczegółowe kroki rozwiązywania problemów.

## <a name="storage"></a>Magazyn

Z perspektywy użytkownika końcowego aplikacji (system jako całość) działa wolno, nie odpowiada lub nawet może wydawać się przestanie odpowiadać, jeśli występują problemy z wydajnością operacji We/Wy. W **woluminów** kartę w systemie SAP HANA Studio można zobaczyć dołączone woluminy, a woluminy są używane przez poszczególne usługi.

![Na karcie woluminy w systemie SAP HANA Studio widać, dołączone woluminy, a woluminy są używane przez poszczególne usługi](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Dołączone woluminy w dolnej części ekranu można zobaczyć szczegółowe informacje o woluminów, takich jak pliki i statystyki operacje We/Wy.

![Dołączone woluminy w dolnej części ekranu można zobaczyć szczegółowe informacje o woluminów, takich jak pliki i statystyki operacje We/Wy](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Zapoznaj się [SAP HANA rozwiązywania problemów: Operacje We/Wy dotyczące głównej przyczyny i potencjalne rozwiązania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) i [SAP HANA rozwiązywania problemów: Powiązane główne przyczyny i rozwiązania dysku](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) lokacji, aby uzyskać szczegółowe kroki rozwiązywania problemów.

## <a name="diagnostic-tools"></a>Narzędzia diagnostyczne

Wykonaj sprawdzenie kondycji programu SAP HANA przy użyciu platformy HANA\_konfiguracji\_Minichecks. To narzędzie zwraca potencjalnie krytycznych problemów technicznych, które powinny mieć już zgłoszenia jako alerty w programie SAP HANA Studio.

Zapoznaj się [1969700 # Uwaga SAP — zbierania instrukcji SQL dla oprogramowania SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) i Pobierz plik SQL Statements.zip dołączony do tej uwagi. Store ten plik zip na lokalnym dysku twardym.

W systemie SAP HANA Studio na **informacje o systemie** kartę, kliknij prawym przyciskiem myszy **nazwa** kolumny i wybierz pozycję **instrukcje SQL importu**.

![W SAP HANA Studio na karcie informacji o systemie kliknij prawym przyciskiem myszy w kolumnie Nazwa, a następnie wybierz Importuj instrukcje SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Wybierz plik SQL Statements.zip przechowywane lokalnie, a nie zostaną zaimportowane do folderu z odpowiedniej instrukcji SQL. W tym momencie można uruchomić wiele różnych sprawdzeń diagnostyki za pomocą tych instrukcji SQL.

Na przykład, aby sprawdzić wymagania dotyczące przepustowości replikacji systemu SAP HANA, kliknij prawym przyciskiem myszy **przepustowości** instrukcji w obszarze **replikacji: Przepustowość** i wybierz **Otwórz** w konsoli programu SQL.

Pełną instrukcję SQL zostanie otwarty, umożliwiając parametry wejściowe (modyfikację sekcji), aby zmienione, a następnie wykonywane.

![Pełną instrukcję SQL zostanie otwarty, umożliwiając parametry wejściowe (modyfikację sekcji), aby zmienione, a następnie wykonywane](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Innym przykładem jest kliknąć prawym przyciskiem myszy w instrukcjach w obszarze **replikacji: Omówienie**. Wybierz **Execute** z menu kontekstowego:

![Innym przykładem jest kliknąć prawym przyciskiem myszy w instrukcjach w ramach replikacji: Przegląd. Wybierz opcję wykonania z poziomu menu kontekstowego](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Skutkuje to informacje, które pomaga w rozwiązywaniu problemów:

![Spowoduje to informacje, które ułatwią rozwiązywanie problemów](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Wykonaj te same czynności dla platformy HANA\_konfiguracji\_Minichecks i sprawdź, czy dla każdego _X_ oznacza w _C_ kolumny (krytyczne).

Przykładowe dane wyjściowe:

**HANA\_konfiguracji\_MiniChecks\_Rev102.01 + 1** kontroli ogólnego platformy SAP HANA.

![HANA\_konfiguracji\_MiniChecks\_Rev102.01 + 1 dla ogólnych kontroli platformy SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**HANA\_usług\_Przegląd** omówienie co usługi SAP HANA są aktualnie uruchomione.

![HANA\_usług\_Przegląd omówienie platformy SAP HANA usług aktualnie uruchomionych](./media/troubleshooting-monitoring/image12-services-overview.png)

**HANA\_usług\_statystyki** platformy SAP Hana usługi informacji (procesor CPU, pamięć itp.).

![HANA\_usług\_informacje o usłudze statystyki dla oprogramowania SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**HANA\_konfiguracji\_Przegląd\_Rev110 +** ogólne informacje na temat wystąpienia platformy SAP HANA.

![HANA\_konfiguracji\_Przegląd\_Rev110 +, aby uzyskać ogólne informacje na temat wystąpienia platformy SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**HANA\_konfiguracji\_parametry\_Rev70 +** Aby sprawdzić parametry platformy SAP HANA.

![HANA\_konfiguracji\_parametry\_Rev70 +, aby sprawdzić parametry platformy SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Następne kroki**

- Zapoznaj się [wysokiej dostępności w SUSE przy użyciu pomocą metody STONITH](ha-setup-with-stonith.md).