---
title: Monitorowanie i rozwiązywanie problemów po stronie HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Monitorowanie i rozwiązywanie problemów ze strony HANA na sap HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 047ea4d07f2b497ac8c7deb90c056d63976094f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617074"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA

Aby skutecznie analizować problemy związane z SAP HANA na platformie Azure (duże wystąpienia), warto zawęzić główną przyczynę problemu. Firma SAP opublikowała dużą ilość dokumentacji, która pomoże Ci.

Odpowiednie często zadawane pytania dotyczące wydajności SAP HANA można znaleźć w następujących uwagach SAP:

- [SAP Note #2222200 – CZĘSTO zadawane pytania: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – CZĘSTO ZADAWANE PYTANIA: PROCESOR SAP HANA](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – CZĘSTO zadawane pytania: Pamięć SAP HANA](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – CZĘSTO zadawane pytania: Sap HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – CZĘSTO ZADAWANE PYTANIA: Analiza we/wy SAP HANA](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 — często zadawane pytania: Ponowne uruchomienie usługi SAP HANA i awarie](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alerty SAP HANA

W pierwszym kroku sprawdź bieżące dzienniki alertów SAP HANA. W SAP HANA Studio przejdź do **konsoli administracyjnej: Alerty: Pokaż: wszystkie alerty**. Ta karta będzie wyświetlać wszystkie alerty SAP HANA dla określonych wartości (wolna pamięć fizyczna, wykorzystanie procesora CPU, itp.), które mieszczą się poza ustawionych minimalnych i maksymalnych progów. Domyślnie czeki są automatycznie odświeżane co 15 minut.

![W sap HANA Studio przejdź do konsoli administracyjnej: Alerty: Pokaż: wszystkie alerty](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor CPU

W przypadku alertu wyzwalanego z powodu nieprawidłowego ustawienia progu rozdzielczość polega na zresetowaniu do wartości domyślnej lub bardziej rozsądnej wartości progowej.

![Resetowanie do wartości domyślnej lub bardziej rozsądnej wartości progowej](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Następujące alerty mogą wskazywać na problemy z zasobami procesora:

- Użycie procesora CPU hosta (alert 5)
- Najnowsza operacja punktu zapisu (Alert 28)
- Czas trwania punktu zapisu (alert 54)

Można zauważyć wysokie zużycie procesora w bazie danych SAP HANA z jednego z następujących czynności:

- Alert 5 (Użycie procesora hosta) jest wywoływany dla bieżącego lub przeszłego użycia procesora
- Wyświetlane użycie procesora na ekranie przeglądowym

![Wyświetlane użycie procesora na ekranie przeglądu](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Wykres obciążenia może wykazywać wysokie zużycie procesora CPU lub wysokie zużycie w przeszłości:

![Wykres obciążenia może wykazywać wysokie zużycie procesora lub wysokie zużycie w przeszłości](./media/troubleshooting-monitoring/image4-load-graph.png)

Alert wyzwalany z powodu wysokiego wykorzystania procesora CPU może być spowodowany kilkoma przyczynami, w tym między innymi: wykonywaniem niektórych transakcji, ładowaniem danych, zadaniami, które nie odpowiadają, długotrwałymi instrukcjami SQL i złą wydajnością zapytań (na przykład z BW na kostkach HANA).

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie Rozwiązywanie problemów z sap [hana: przyczyny i rozwiązania związane z procesorem.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)

## <a name="operating-system"></a>System operacyjny

Jednym z najważniejszych kontroli SAP HANA w systemie Linux jest upewnienie się, że przezroczyste ogromne strony są wyłączone, zobacz [SAP Note #2131662 – Transparent Huge Pages (THP) na serwerach SAP HANA.](https://launchpad.support.sap.com/#/notes/2131662)

- Możesz sprawdzić, czy przezroczyste ogromne strony są włączone za pomocą następującego polecenia Linux: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Jeśli _zawsze_ jest ujęty w nawiasy, jak poniżej, oznacza to, że przezroczyste ogromne strony są włączone: [zawsze] madvise nigdy; jeśli _nigdy nie_ jest zamknięty w nawiasach, jak poniżej, oznacza to, że przezroczyste ogromne strony są wyłączone: zawsze madvise [nigdy]

Następujące polecenie Linuksa nie powinno zwracać niczego: **rpm -qa | grep ulimit.** Jeśli wydaje _się, ulimit_ jest zainstalowany, odinstaluj go natychmiast.

## <a name="memory"></a>Memory (Pamięć)

Można zaobserwować, że ilość pamięci przydzielonej przez bazę danych SAP HANA jest wyższa niż oczekiwano. Następujące alerty wskazują problemy z wysokim użyciem pamięci:

- Użycie pamięci fizycznej hosta (alert 1)
- Użycie pamięci serwera nazw (Alert 12)
- Całkowite użycie pamięci w tabelach magazynu kolumn (alert 40)
- Wykorzystanie pamięci usług (Alert 43)
- Użycie pamięci w głównym magazynie tabel magazynu kolumn (Alert 45)
- Pliki zrzutu środowiska wykonawczego (alert 46)

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie [Rozwiązywanie problemów z pamięcią SAP HANA: Problemy z pamięcią.](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)

## <a name="network"></a>Network (Sieć)

Zapoznaj się z [#2081065 notatki SAP — rozwiązywanie problemów z siecią SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) i wykonaj kroki rozwiązywania problemów z siecią w tej notatce SAP.

1. Analizowanie czasu podróży w obie strony między serwerem a klientem.
  A. Uruchom skrypt SQL [_HANA\_\_Network Clients_](https://launchpad.support.sap.com/#/notes/1969700)_._
  
2. Analizowanie komunikacji międzyodowej.
  A. Uruchom skrypt SQL [_HANA\_\_Network Services_](https://launchpad.support.sap.com/#/notes/1969700)_._

3. Uruchom polecenie Linux **ifconfig** (dane wyjściowe pokazują, czy występują jakiekolwiek straty pakietów).
4. Uruchom polecenie Linux **tcpdump**.

Ponadto użyj narzędzia [IPERF](https://iperf.fr/) typu open source (lub podobnego) do pomiaru rzeczywistej wydajności sieci aplikacji.

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie [Rozwiązywanie problemów z siecią HANA: Wydajność i problemy z łącznością](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) w sieci.

## <a name="storage"></a>Magazyn

Z punktu widzenia użytkownika końcowego aplikacja (lub system jako całość) działa wolno, nie odpowiada, a nawet może wydawać się przestać odpowiadać, jeśli występują problemy z wydajnością we/wy. Na karcie **Woluminy** w SAP HANA Studio można zobaczyć dołączone woluminy i woluminy używane przez każdą usługę.

![Na karcie Woluminy w SAP HANA Studio można zobaczyć dołączone woluminy i woluminy używane przez każdą usługę](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Dołączone woluminy w dolnej części ekranu można zobaczyć szczegóły woluminów, takie jak pliki i statystyki we/wy.

![Dołączone woluminy w dolnej części ekranu można zobaczyć szczegóły woluminów, takie jak pliki i statystyki we/wy](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie [Sap HANA Troubleshooting: We/Wy Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) oraz [SAP HANA Troubleshooting: Disk Related Root Causes and Solutions](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) site.

## <a name="diagnostic-tools"></a>Narzędzia diagnostyczne

Wykonaj kontrolę kondycji SAP HANA\_\_za pośrednictwem minicheczek konfiguracyjnych HANA. To narzędzie zwraca potencjalnie krytyczne problemy techniczne, które powinny być już podniesione jako alerty w SAP HANA Studio.

Zapoznaj się z [SAP Note #1969700 — kolekcja instrukcji SQL dla sap HANA](https://launchpad.support.sap.com/#/notes/1969700) i pobierz plik SQL Statements.zip dołączony do tej notatki. Zapisz ten plik zip na lokalnym dysku twardym.

W programie SAP HANA Studio na karcie **Informacje o systemie** kliknij prawym przyciskiem myszy **kolumnę Nazwa** i wybierz polecenie **Importuj instrukcje SQL**.

![W programie SAP HANA Studio na karcie Informacje o systemie kliknij prawym przyciskiem myszy kolumnę Nazwa i wybierz polecenie Importuj instrukcje SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Wybierz plik SQL Statements.zip przechowywany lokalnie, a folder z odpowiednimi instrukcjami SQL zostanie zaimportowany. W tym momencie wiele różnych kontroli diagnostycznych można uruchomić za pomocą tych instrukcji SQL.

Na przykład, aby przetestować wymagania dotyczące przepustowości systemu SAP HANA System Replication, kliknij prawym przyciskiem myszy **instrukcję Przepustowość** w obszarze **Replikacja: Przepustowość** i wybierz pozycję **Otwórz** w konsoli SQL.

Zostanie otwarta pełna instrukcja SQL umożliwiająca zmianę parametrów wejściowych (sekcja modyfikacji), a następnie wykonanie.

![Zostanie otwarta pełna instrukcja SQL umożliwiająca zmianę parametrów wejściowych (sekcja modyfikacji), a następnie wykonanie](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Innym przykładem jest kliknięcie prawym przyciskiem myszy instrukcji w obszarze **Replikacja: Przegląd**. Z menu kontekstowego wybierz **polecenie Wykonaj:**

![Innym przykładem jest kliknięcie prawym przyciskiem myszy instrukcji w obszarze Replikacja: Przegląd. Z menu kontekstowego wybierz polecenie Wykonaj](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Powoduje to informacje, które pomagają w rozwiązywaniu problemów:

![Spowoduje to informacje, które pomogą w rozwiązywaniu problemów](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Zrób to samo\_dla\_minicheczek konfiguracyjnych HANA i sprawdź, czy nie ma żadnych znaków _X_ w kolumnie _C_ (Krytyczne).

Przykładowe wyjścia:

**Hana\_\_Konfiguracja\_MiniChecks Rev102.01 +1** dla ogólnych kontroli SAP HANA.

![Hana\_\_Konfiguracja MiniChecks\_Rev102.01+1 dla ogólnych kontroli SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Omówienie\_\_usług HANA, aby** zapoznać się z aktualnie uruchomionymi usługami SAP HANA.

![Omówienie\_\_usług HANA, aby zapoznać się z aktualnie uruchomionymi usługami SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

**Statystyki\_usług\_HANA** dla informacji o usłudze SAP HANA (procesor, pamięć itp.).

![Statystyki\_usług\_HANA dla informacji o usługach SAP HANA](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Omówienie\_\_\_konfiguracji HANA Rev110+** dla ogólnych informacji na temat wystąpienia SAP HANA.

![Omówienie\_\_\_konfiguracji HANA Rev110+ w celu uzyskania ogólnych informacji na temat wystąpienia SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Parametry\_\_konfiguracji\_HANA Rev70+** w celu sprawdzenia parametrów SAP HANA.

![Parametry\_\_konfiguracji\_HANA Rev70+ w celu sprawdzenia parametrów SAP HANA](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Następne kroki**

- Poleć [Wysoka dostępność skonfigurowana w SUSE przy użyciu stonith](ha-setup-with-stonith.md).