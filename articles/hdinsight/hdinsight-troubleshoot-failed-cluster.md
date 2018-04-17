---
title: Rozwiązywanie problemów z powolnym działaniem lub awarie klastra usługi HDInsight - Azure HDInsight | Dokumentacja firmy Microsoft
description: Diagnozowanie i rozwiązywanie problemów z powolnym działaniem lub awarie klastra usługi HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: ashishth
ms.openlocfilehash: bd026169062788fa7587cb6357b3764fbfdb24fb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Rozwiązywanie problemów dotyczących powolnego działania lub awarii klastra usługi HDInsight

Jeśli klaster usługi HDInsight jest działa wolno lub niepowodzeniem z kodem błędu, masz kilka opcji rozwiązywania problemów. Jeśli zadaniach trwa dłużej, niż oczekiwano lub jest wyświetlany wydłużają czas odpowiedzi, ogólnie rzecz biorąc, może być błędów powyżej z klastra, takie jak usługi, na których są uruchamiane w klastrze. Najczęstszą przyczyną spowolnienia tego jest jednak niewystarczające skalowania. Podczas tworzenia nowego klastra usługi HDInsight, wybierz odpowiednie [rozmiarów maszyn wirtualnych](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters)

Do diagnozowania klastra powolnym działaniem lub awarie, Zbierz informacje o wszystkich aspektów środowiska, takich jak powiązane usługi Azure, konfiguracji klastra i informacji o wykonanie zadania. Diagnostyka pomocne jest próba odtworzenia stanu błędu w innym klastrze.

* Krok 1: Zbieranie danych o problemie
* Krok 2: Weryfikowanie środowiska klastra usługi HDInsight 
* Krok 3: Wyświetl stan sieci klastra
* Krok 4: Przejrzyj stos środowiska i wersje
* Krok 5: Sprawdź pliki dziennika klastra
* Krok 6: Sprawdź ustawienia konfiguracji
* Krok 7: Występuje awaria w innym klastrze 

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Zbieranie danych o problemie

HDInsight oferuje wiele narzędzi, które służą do identyfikowania i rozwiązywania problemów z klastrami. W poniższych krokach przedstawiono te narzędzia oraz Zasugeruj trafić problem.

### <a name="identify-the-problem"></a>Zidentyfikować problem

Aby zidentyfikować problem, należy rozważyć następujące kwestie:

* Co spodziewać się stać? Co się stało zamiast niego?
* Jak długo proces wykonać, aby uruchomić? Jak długo mają uruchamiać tę?
* Masz zawsze działać wolniej, w tym klastrze zadań? Czy działają szybciej na innym klastrze?
* Jeśli ten problem wystąpił po raz pierwszy? Jak często miało miejsce od?
* Cokolwiek zmienił się w mojej konfiguracji klastra?

### <a name="cluster-details"></a>Szczegółów klastra

Zawiera informacje o klastrze ważne:

* Nazwa klastra.
* Klaster region - Sprawdź, czy [awarie region](https://azure.microsoft.com/status/).
* Typ klastra usługi HDInsight i wersji.
* Typ i numer wystąpienia usługi HDInsight określony dla węzłów head i proces roboczy.

Portalu Azure można podawać następujące informacje:

![Portal HDInsight Azure informacji](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Można także użyć wiersza polecenia platformy Azure:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Inną opcją jest przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [klastrów zarządzania Hadoop w HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Weryfikowanie środowiska klastra usługi HDInsight

Każdy klaster usługi HDInsight polega na różne usługi platformy Azure i na oprogramowanie open source, takie jak bazy danych Apache HBase i Apache Spark. Klastry usługi HDInsight można również wywołać na innych usług Azure, takich jak sieci wirtualnych Azure.  Niepowodzenie klastra może być spowodowane przez usług uruchomionych w klastrze lub zewnętrznej usługi.  Zmiana konfiguracji usługi klastrowania może również spowodować klastra.

### <a name="service-details"></a>Szczegóły usługi

* Sprawdzanie wersji biblioteki open source
* Sprawdź, czy [awarii usługi platformy Azure](https://azure.microsoft.com/status/) 
* Sprawdź, czy ograniczenia użycia usługi Azure 
* Sprawdź konfigurację podsieci sieci wirtualnej platformy Azure 

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetl ustawienia konfiguracji klastra przy użyciu interfejsu użytkownika narzędzia Ambari

Apache Ambari zapewnia zarządzania i monitorowania w klastrze HDInsight z interfejsu użytkownika sieci web i interfejsu API REST. Ambari znajduje się w klastrach HDInsight opartych na systemie Linux. Wybierz **pulpit nawigacyjny klastra** okienka na stronie Azure HDInsight portalu.  Wybierz **pulpit nawigacyjny klastra usługi HDInsight** okienko, aby otworzyć Interfejs użytkownika narzędzia Ambari, a następnie wprowadź poświadczenia logowania klastra.  

![Interfejs użytkownika narzędzia Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Aby otworzyć listę widoków usługi, wybierz **widoków Ambari** na stronie portalu Azure.  Lista zależy od tego, które biblioteki są zainstalowane. Może na przykład zobacz menedżera kolejek YARN, Hive View oraz widok Tez.  Wybierz łącze usługi, aby wyświetlić konfigurację i informacje o usłudze.

#### <a name="check-for-azure-service-outages"></a>Sprawdź, czy usługa Azure awarie

HDInsight polega na kilka usług Azure. Serwery wirtualne działa w usłudze Azure HDInsight, magazynów danych i skryptów w magazynie obiektów Blob platformy Azure lub magazynu danych Azure, i indeksów pliki dziennika w magazynie tabel platformy Azure. Zakłóceń w tych usług w sporadycznych przypadkach, mogą powodować problemy w usłudze HDInsight. Jeśli masz nieoczekiwane opóźnienia lub błędy w klastrze, sprawdź [pulpitu nawigacyjnego stan Azure](https://azure.microsoft.com/status/). Stan każdej usługi znajduje się w regionie. Sprawdź klastra regionu i również regiony dla wszystkich powiązanych usług.

#### <a name="check-azure-service-usage-limits"></a>Sprawdź ograniczenia użycia usługi Azure

Jeśli są uruchamianie dużych klastra lub jednocześnie uruchomić wiele klastrów, klastra może zakończyć się niepowodzeniem, jeśli Przekroczono limit usługi Azure. Ograniczenia usługi zależą od subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [subskrypcji platformy Azure i usługi limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Możesz poprosić o Microsoft zwiększać HDInsight zasobów (takich jak rdzeni maszyn wirtualnych i wystąpień maszyn wirtualnych) z [żądanie zwiększenia limitu przydziału rdzeni Resource Manager](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Kontrola wersji

Porównaj wersję klastra z najnowszej wersji usługi HDInsight. Każdej wersji usługi HDInsight zawiera ulepszenia, takie jak nowe aplikacje, funkcje, poprawki i poprawki. Problem, który ma wpływ na klaster może został rozwiązany w najnowszej wersji. Jeśli to możliwe Uruchom ponownie klaster przy użyciu najnowszej wersji usługi HDInsight oraz skojarzone biblioteki, takich jak bazy danych Apache HBase, Apache Spark i inne.

#### <a name="restart-your-cluster-services"></a>Uruchom ponownie usługi klastrowania

Jeśli spowolnienie występują w klastrze, należy rozważyć ponowne uruchomienie usług za pośrednictwem interfejsu użytkownika narzędzia Ambari lub wiersza polecenia platformy Azure. Klastra mogą wystąpić błędy przejściowych i ponowne uruchomienie jest najszybszym sposobem na ustabilizowania środowiska i może zwiększyć wydajność.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Wyświetl stan sieci klastra

Klastry HDInsight składają się z różnych typów węzłów uruchomiona na wystąpieniu maszyny wirtualnej. Można monitorować każdy węzeł do zablokowania zasobów, problemy z połączeniem sieciowym i inne problemy, które może to spowolnić klastra. Każdy klaster zawiera dwa węzły head, a większość typów klastra zawierają kombinację procesu roboczego i węzły krawędzi. 

Opis różnych węzłów używa każdy typ klastra, zobacz [Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i](hdinsight-hadoop-provision-linux-clusters.md).

W poniższych sekcjach opisano sposób sprawdzania kondycji każdego węzła i ogólną klastra.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Pobierz migawkę kondycji klastra przy użyciu pulpitu nawigacyjnego interfejsu użytkownika narzędzia Ambari

[Pulpit nawigacyjny interfejsu użytkownika narzędzia Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) zawiera przegląd kondycji klastra, takie jak czas działania, pamięci, sieci i użycie procesora CPU, użycia dysku systemu plików HDFS i tak dalej. W sekcji hosty Ambari umożliwia wyświetlanie zasobów na poziomie hosta. Można również zatrzymać i ponownie uruchomić usługi.

### <a name="check-your-webhcat-service"></a>Sprawdź usługi WebHCat

Błąd jest jeden scenariusz typowe zadania Hive, Pig lub Sqoop kończą się niepowodzeniem [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (lub *Templeton*) usługi. WebHCat to interfejs REST do wykonywania zdalnego zadania, takie jak Hive, Pig, koordynującego i MapReduce. WebHCat tłumaczy żądań przesyłanie zadań do aplikacji YARN i zwraca stan pochodną YARN stanu aplikacji.  W poniższych sekcjach opisano typowe kody stanów WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (kod stanu 502)

Jest to rodzajowy komunikat z węzłów bramy, najczęściej kod stanu błędu. Jedną z możliwych przyczyn tego jest usługi WebHCat w dół na aktywnym węźle głównym. Aby sprawdzić tę możliwość, użyj następującego polecenia CURL:

```bash
$ curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari wyświetla alert przedstawiający hosty, na których działa usługa WebHCat. Możesz spróbować przełączyć tę usługę WebHCat kopii zapasowych przez ponowne uruchomienie usługi na jej hosta.

![Uruchom ponownie serwer usługi WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Jeżeli serwer usługi WebHCat nadal nie znaleziona, sprawdź dziennik operacji komunikaty o błędach. Aby uzyskać szczegółowe informacje, sprawdź `stderr` i `stdout` pliki, do których odwołuje się w węźle.

#### <a name="webhcat-times-out"></a>Limit czasu usługi WebHCat

Brama usługi HDInsight upłynie limit czasu odpowiedzi, które trwać dłużej niż dwie minuty, zwracając `502 BadGateway`. WebHCat wysyła zapytanie do usługi YARN dla stany zadania, a jeśli YARN trwa dłużej niż dwie minuty odpowiedzieć, który żądanie może upłynął limit czasu.

W takim przypadku należy przejrzeć następujące dzienniki w `/var/log/webhcat` katalogu:

* **webhcat.log** dziennika log4j dzienniki zapisy serwera, który jest
* **webhcat console.log** jest stdout serwera przy uruchamianiu
* **webhcat konsoli error.log** jest to stderr procesu serwera

> [!NOTE]
> Każdy `webhcat.log` przerzuceniem codziennie, generowanie plików o nazwie `webhcat.log.YYYY-MM-DD`. Wybierz odpowiedni plik dla zakresu czasu, który rozpatrujesz.

W poniższych sekcjach opisano niektóre możliwe przyczyny WebHCat przekroczeń limitu czasu.

##### <a name="webhcat-level-timeout"></a>Limit czasu poziomu usługi WebHCat

Gdy WebHCat jest obciążony z więcej niż 10 Otwórz gniazda, trwa dłużej ustanawiać nowych połączeń gniazda, które mogą spowodować przekroczenie limitu czasu. Aby wyświetlić listę połączeń sieciowych do i z usługi WebHCat, należy użyć `netstat` na bieżącym headnode active:

```bash
$ netstat | grep 30111
```

30111 jest port, którego nasłuchuje WebHCat. Liczba otwartych gniazd powinna być mniejsza niż 10.

Jeśli nie ma żadnych otwartych gniazd, poprzednie polecenie nie dawać wynik. Sprawdź, czy Templeton działa i nasłuchuje na porcie 30111, użyj:

```bash
$ netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>YARN poziomu limitu czasu

Templeton wywołuje YARN do uruchomienia zadań i komunikacji między Templeton i YARN może spowodować przekroczenie limitu czasu.

Na poziomie YARN istnieją dwa typy przekroczeń limitu czasu:

1. Przesyłania zadania YARN może zająć wystarczająco długi, aby spowodować przekroczenie limitu czasu.

    Po otwarciu `/var/log/webhcat/webhcat.log` pliku dziennika i wyszukaj "zadanie w kolejce", będzie miało miejsce wiele wpisów gdy czas wykonania jest zbyt długa (> 2000 ms), zapisami przedstawiający zwiększenie oczekiwania razy.

    Czas na umieszczonych w kolejce zadania w dalszym ciągu zwiększa, ponieważ współczynnik, jaką przesłania nowe zadania jest większy niż częstotliwość ukończenia zadania stary. Pamięć YARN po 100% używane, *kolejki joblauncher* można już przekazania pojemności z *domyślnej kolejki*. W związku z tym nowe zadania nie mogą być akceptowane w kolejce joblauncher. Takie zachowanie może powodować dłuższego czasu oczekiwania i już, co powoduje błąd upływu limitu czasu zwykle występuje wiele innych.

    Na poniższej ilustracji przedstawiono kolejki joblauncher % 714.4 nadmiernego obciążenia. Jest to dopuszczalne, tak długo, jak w domyślnej kolejki do przekazania z jest nadal wolna pojemność. Jednak jeśli klaster jest w pełni wykorzystywane i YARN pamięci wynosi 100% pojemności, nowe zadania musi czekać, co ostatecznie powoduje, że limity czasu.

    ![Joblauncher kolejki](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Istnieją dwa sposoby rozwiązania tego problemu: Obniż szybkości nowych zadań przesyłanych lub zwiększenie szybkości użycie poprzedniego zadania skalując w górę w klastrze.

2. Przetwarzanie YARN może zająć dużo czasu, co może powodować przekroczeń limitu czasu.

    * Wyświetl listę wszystkich zadań: jest to wywołanie czasochłonna. To wywołanie wylicza aplikacje z YARN ResourceManager i dla każdej aplikacji ukończone, pobiera stan z YARN JobHistoryServer. Przy wyższej liczby zadań to wywołanie może upłynął limit czasu.

    * Listę zadań starsze niż siedmiu dni: JobHistoryServer YARN HDInsight jest skonfigurowany do przechowywania informacji zadanie zostało ukończone na siedem dni (`mapreduce.jobhistory.max-age-ms` wartości). Próby wyliczenia przeczyszczone zadania powoduje przekroczenie limitu czasu.

Aby zdiagnozować te problemy:

    1. Określić zakres czasu UTC, aby rozwiązać
    2. Wybierz odpowiednie `webhcat.log` plików
    3. Wyszukaj OSTRZEGAJ i komunikaty o błędach w tym czasie

#### <a name="other-webhcat-failures"></a>Inne błędy WebHCat

1. Kod stanu HTTP 500

    W większości przypadków, w którym WebHCat zwraca 500 komunikat o błędzie zawiera szczegółowe informacje o błędzie. W przeciwnym razie przejrzyj `webhcat.log` dla OSTRZEGAJ i komunikaty o błędach.

2. Błędy zadań

    Może to być przypadki, w których interakcji z usługą WebHCat zostały wykonane pomyślnie, ale zadania kończą się niepowodzeniem.

    Templeton zbiera dane wyjściowe zadania konsoli jako `stderr` w `statusdir`, która jest często przydatne podczas rozwiązywania problemów. `stderr` zawiera identyfikator aplikacji YARN rzeczywiste zapytania.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Przejrzyj stos środowiska i wersje

Interfejs użytkownika narzędzia Ambari **stosu i wersji** strony zawiera informacje o klastrze usług konfiguracji i usługi Historia wersji.  Niepoprawne wersje biblioteki usługi Hadoop może być przyczyna awarii klastra.  W Interfejsie użytkownika narzędzia Ambari, wybierz **Admin** menu, a następnie **stosy i wersje**.  Wybierz **wersji** kartę na stronę, aby wyświetlić informacje o wersji usługi:

![Stos i wersje](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Sprawdź pliki dziennika

Istnieje wiele typów dzienników, które są generowane na podstawie wielu usług i składniki wchodzące w skład klastra usługi HDInsight. [Pliki dziennika usługi WebHCat](#check-your-webhcat-service) opisane wcześniej. Istnieje kilka innych przydatne plików dziennika, które można zbadać Aby zawęzić problemy z klastrem, zgodnie z opisem w poniższych sekcjach.

![Przykładowy plik dziennika usługi HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

* Klastry usługi HDInsight składają się z kilku węzłów, z których większość są zlecił do uruchomienia zadań zostało przesłane. Zadania są uruchamiane jednocześnie, ale pliki dziennika mogą być wyświetlane tylko wyniki liniowo. HDInsight wykonuje nowe zadania, kończenie innych osób, które nie powiedzie się najpierw. To działanie jest rejestrowane `stderr` i `syslog` plików.

* Pliki dziennika akcji skryptu wyświetlanie błędów lub zmiany konfiguracji nieoczekiwany podczas procesu tworzenia klastra.

* Dzienniki usługi Hadoop krok zidentyfikować Hadoop zadania uruchomione w ramach kroku zawierające błędy.

### <a name="check-the-script-action-logs"></a>Sprawdź dzienniki akcji skryptu

HDInsight [skryptu akcje](hdinsight-hadoop-customize-cluster-linux.md) uruchamiać skrypty w klastrze ręcznie lub gdy określono. Na przykład akcji skryptu można zainstalować dodatkowe oprogramowanie w klastrze lub zmienić ustawienia konfiguracji z wartościami domyślnymi. Sprawdzanie dzienników akcji skryptu można zapewniają wgląd w błędów występujących podczas instalacji klastra i konfiguracji.  Stan akcji skryptu można wyświetlić, wybierając **ops** przycisk w Interfejsie użytkownika narzędzia Ambari lub uzyskiwania dostępu do dzienników z domyślnego konta magazynu.

Dzienniki akcji skryptu znajdują się w `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` katalogu.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Wyświetl dzienniki usługi HDInsight przy użyciu Ambari szybkie linki

Interfejs użytkownika narzędzia Ambari HDInsight obejmuje kilka **szybkie linki** sekcje.  Aby uzyskać dostęp do łącza dziennika dla określonej usługi w klastrze usługi HDInsight, Otwórz interfejs użytkownika narzędzia Ambari dla klastra, a następnie w link usługi na liście z lewej. Wybierz **szybkie linki** listy rozwijanej, a następnie węzła HDInsight zainteresowania, a następnie wybierz link jej dziennika skojarzonej.

Na przykład dla systemu plików HDFS dzienniki:

![Szybkie Ambari łącza do plików dziennika](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Wyświetlanie plików dziennika generowanych przez usługi Hadoop

Klaster usługi HDInsight generuje dzienniki, które są zapisywane w tabelach platformy Azure i magazynem obiektów Blob Azure. YARN tworzy własne dzienniki wykonywania. Aby uzyskać więcej informacji, zobacz [zarządzania dziennikami dla klastra usługi HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Przejrzyj zrzuty stosu

Zrzuty stosu zawierającego migawkę pamięci aplikacji, w tym wartości zmiennych w tym czasie, które są przydatne do diagnozowania problemów występujących w czasie wykonywania. Aby uzyskać więcej informacji, zobacz [zrzuty stosu Włącz usługi Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Sprawdź ustawienia konfiguracji

Klastry HDInsight są wstępnie skonfigurowane przy użyciu ustawień domyślnych dla powiązanych usług, takich jak Hadoop, Hive, HBase i tak dalej. W zależności od typu klastra, jego konfiguracja sprzętu, ich liczba węzłów, typy zadań używasz i danych, użytkownik pracuje z (i sposób przetwarzania danych), może być konieczne w celu zoptymalizowania konfiguracji.

Aby uzyskać szczegółowe instrukcje dotyczące optymalizacji wydajności konfiguracje dla większości scenariuszy, zobacz [zoptymalizowania konfiguracji klastra z Ambari](hdinsight-changing-configs-via-ambari.md). Podczas korzystania z platformy Spark, zobacz [zadań Spark optymalizacji wydajności](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Występuje awaria w innym klastrze

Aby pomóc w zdiagnozowaniu źródła błędu klastra, uruchomić nowego klastra z taką samą konfiguracją, a następnie prześlij ponownie zadanie zakończone niepowodzeniem kroki jeden po drugim. Sprawdź wyniki każdego kroku przed przetworzeniem kolejnego. Ta metoda zapewnia możliwość Popraw i ponownie uruchom pojedynczy krok zakończony niepowodzeniem. Ta metoda ma również zaletą tylko raz podczas ładowania danych wejściowych.

1. Utwórz nowy klaster testu z taką samą konfigurację jak klastra nie powiodło się.
2. Przedstawia pierwszego etap zadania do klastra testowego.
3. Po zakończeniu przetwarzania tego kroku Sprawdź błędy w plikach dziennika kroku. Łączenie z węzłem głównym klastra testowego i wyświetlanie plików dziennika. Pliki dziennika kroku są wyświetlane tylko po kroku jest uruchamiana przez pewien czas zakończeniu lub kończy się niepowodzeniem.
4. Pierwszym krokiem zakończyło się pomyślnie, należy uruchomić następnego kroku. Jeśli wystąpiły błędy, przeanalizuj następujący błąd w plikach dziennika. Jeśli został błąd w kodzie, wprowadzenia poprawek i uruchom ponownie tego kroku. 
5. Kontynuuj, aż wszystkie czynności wykonane bez błędów.
6. Gdy wszystko będzie gotowe debugowanie klastra testowego go usunąć.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)
* [Dziennik aplikacji YARN dostępu w usłudze HDInsight z systemem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włącz zrzuty stosu dla usługi Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Znane problemy w przypadku klastra Apache Spark w usłudze HDInsight](hdinsight-apache-spark-known-issues.md)
