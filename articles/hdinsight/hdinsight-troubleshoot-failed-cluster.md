---
title: Rozwiązywanie problemów z powolnego działania lub awarii klastra HDInsight — usługi Azure HDInsight
description: Diagnozowanie i rozwiązywanie problemów z klastrem HDInsight powolne lub awarie.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 785eac065e10c64b99839ab8667e9b613f62aeb9
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764045"
---
# <a name="troubleshoot-a-slow-or-failing-hdinsight-cluster"></a>Rozwiązywanie problemów dotyczących powolnego działania lub awarii klastra usługi HDInsight

Jeśli klaster usługi HDInsight jest działa wolno lub kończy się niepowodzeniem z kodem błędu, masz kilka opcji rozwiązywania problemów. Jeśli Twoje zadania trwa dłużej niż oczekiwano lub widzisz wydłużają czas odpowiedzi ogólnie rzecz biorąc, może być błędy nadrzędne z klastra, takiej jak usługi, na których są uruchamiane w klastrze. Najczęstszą przyczyną tych spowolnień jest jednak skalowanie niewystarczające. Podczas tworzenia nowego klastra HDInsight, wybierz odpowiedni [rozmiarów maszyn wirtualnych](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Aby zdiagnozować powolnego działania lub awarii klastra, Zbierz informacje dotyczące wszystkich aspektów środowiska, takie jak powiązane usługi platformy Azure, konfiguracji klastra i informacje o wykonaniu zadania. Diagnostyka pomocne jest spróbuj odtworzyć stan błędu w innym klastrze.

* Krok 1: Zbierz dane o problemie.
* Krok 2: Sprawdzania, czy środowisko klastra HDInsight.
* Krok 3: Wyświetl stan usługi klastra.
* Krok 4: Przejrzyj stos środowiska i wersji.
* Krok 5. Sprawdź pliki dziennika klastra.
* Krok 6: Sprawdź ustawienia konfiguracji.
* Krok 7: Odtwórz błąd w innym klastrze.

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Zbieranie danych o problemie

HDInsight oferuje wiele narzędzi, które służą do identyfikowania i rozwiązywania problemów z klastrami. Poniższe kroki ułatwiają za pomocą tych narzędzi i sugestie dotyczące precyzyjne określenie problemu.

### <a name="identify-the-problem"></a>Zidentyfikuj problem

Aby ułatwić zidentyfikowanie problemu, należy wziąć pod uwagę następujące kwestie:

* Co oczekiwać odbywały się? Co się stało zamiast?
* Jak długo proces trwa do uruchomienia? Jak długo należy go zostało uruchomionych?
* Masz Moje zadania zawsze spowolnienie działania w tym klastrze? One szybsze w innym klastrze?
* Jeśli ten problem najpierw wystąpił? Jak często zostało przeprowadzone od?
* Wszystko zmieniło w mojej konfiguracji klastra?

### <a name="cluster-details"></a>Szczegóły klastra

Zawiera ważne informacje klastra:

* Nazwa klastra.
* Klaster region - Sprawdź [awarii region](https://azure.microsoft.com/status/).
* Typ klastra HDInsight i wersji.
* Typ i liczbę wystąpień HDInsight określona dla węzły główne i proces roboczy.

Witryna Azure portal można podać te informacje:

![Portal HDInsight Azure informacji](./media/hdinsight-troubleshoot-failed-cluster/portal.png)

Można również użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Innym rozwiązaniem jest przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [klastrów zarządzania Apache Hadoop w HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Sprawdzania, czy środowisko klastra HDInsight

Każdy klaster HDInsight opiera się na różne usługi platformy Azure i oprogramowania typu open source, takich jak Apache HBase i Apache Spark. Klastry HDInsight można również wywołać od innych usług platformy Azure, takich jak Azure Virtual Networks.  Niepowodzenie klastra może być spowodowany przez dowolną z usług uruchomionych w klastrze lub zewnętrznej usługi.  Zmiana konfiguracji klastra usługi również może spowodować awarii klastra.

### <a name="service-details"></a>Szczegóły usługi

* Sprawdzanie wersji biblioteki typu open source.
* Wyszukaj [przerwy w działaniu usługi platformy Azure](https://azure.microsoft.com/status/).  
* Sprawdź, czy limity użycia usługi Azure. 
* Sprawdź konfigurację podsieci sieci wirtualnej platformy Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika systemu Ambari

Apache Ambari udostępnia zarządzania i monitorowania klastra HDInsight z interfejsu użytkownika sieci web i interfejs API REST. Ambari znajduje się w klastrach HDInsight opartych na systemie Linux. Wybierz **pulpit nawigacyjny klastra** okienko HDInsight stronę witryny Azure portal.  Wybierz **pulpit nawigacyjny klastra HDInsight** okienko, aby otworzyć interfejsu użytkownika Ambari, a następnie wprowadź poświadczenia logowania klastra.  

![Interfejs użytkownika systemu Ambari](./media/hdinsight-troubleshoot-failed-cluster/ambari-ui.png)

Aby otworzyć listę widoków usługi, wybierz **widoków Ambari** na stronie portalu Azure.  Ta lista zależy od tego, które biblioteki są instalowane. Na przykład może zostać wyświetlony Menedżer kolejki YARN, Hive View i widok aplikacji Tez.  Wybierz link usługi Aby wyświetlić konfigurację i informacje o usłudze.

#### <a name="check-for-azure-service-outages"></a>Wyszukaj przerwy w działaniu usługi platformy Azure

HDInsight opiera się na kilka usług platformy Azure. Działa serwerów wirtualnych na platformie Azure HDInsight, magazynów danych i skryptów w usłudze Azure Blob storage lub usługi Azure Data Lake Storage, i indeksy pliki dziennika w usłudze Azure Table storage. Przerw w działaniu do tych usług, mimo że jest to rzadkie, mogą powodować problemy w HDInsight. W przypadku nieoczekiwanych opóźnień lub błędów w klastrze, sprawdź [pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status/). Uporządkowane według regionów znajduje się stan każdej usługi. Sprawdź klastra region i również regiony dla wszystkich powiązanych usług.

#### <a name="check-azure-service-usage-limits"></a>Sprawdzanie limitów użycia usług platformy Azure

Czy uruchamianie dużego klastra, czy wprowadzony wielu klastrów jednocześnie, klaster może zakończyć się niepowodzeniem w przypadku przekroczenia limitu usługi platformy Azure. Limity usługi się różnić w zależności od subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [subskrypcji platformy Azure i limity, przydziały i ograniczenia](https://docs.microsoft.com/azure/azure-subscription-service-limits).
Możesz poprosić o Microsoft zwiększać HDInsight zasobów (takich jak rdzeni maszyn wirtualnych i wystąpień maszyn wirtualnych) z [żądanie zwiększenia limitu przydziału rdzeni usługi Resource Manager](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Sprawdź wersję wydania

Porównaj z najnowszą wersją HDInsight w wersji klastra. Każda wersja HDInsight zawiera ulepszenia, takie jak nowe aplikacje, funkcje, poprawki i poprawki błędów. Problem, który ma wpływ na klastrze mogły zostać naprawione w najnowszej wersji. Jeśli to możliwe Uruchom ponownie klaster przy użyciu najnowszej wersji, HDInsight i skojarzonymi z nimi bibliotekami, takich jak bazy danych Apache HBase, Apache Spark i inne.

#### <a name="restart-your-cluster-services"></a>Uruchom ponownie usługi klastrowania

Jeśli spowolnienie występują w klastrze, należy rozważyć ponowne uruchamianie usługi za pośrednictwem interfejsu użytkownika Ambari lub klasycznego wiersza polecenia platformy Azure. Klaster mogą występować błędy przejściowe i ponowne uruchomienie jest najszybszym sposobem na stabilizowaniu środowiska i być może zwiększyć wydajność.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Wyświetl stan sieci klastra

Klastry HDInsight składają się z różnego rodzaju węzłami działającymi na wystąpieniach maszyn wirtualnych. Każdy węzeł można monitorować zablokowania zasobów, problemy z połączeniem sieciowym i innych problemów, które spowalniają działanie klastra. Każdy klaster zawiera dwa węzły główne, a większość typów klastrów zawierają kombinację procesu roboczego oraz węzłów krawędzi. 

Aby uzyskać opis różnych węzłów, które są używane przez każdy typ klastra, zobacz [konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i](hdinsight-hadoop-provision-linux-clusters.md).

Poniżej opisano sposób sprawdzić kondycję każdego węzła i ogólną klastra.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Pobierz migawkę kondycji klastra za pomocą pulpitu nawigacyjnego interfejsu użytkownika Ambari

[Pulpit nawigacyjny interfejsu użytkownika Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) (`https://<clustername>.azurehdinsight.net`) zawiera przegląd kondycji klastra, takie jak czas działania, pamięci, sieci i użycie procesora CPU, użycie dysku systemu plików HDFS i tak dalej. Użyj sekcji hosty Ambari do wyświetlania zasobów na poziomie hosta. Można również zatrzymać i ponownie uruchomić usługi.

### <a name="check-your-webhcat-service"></a>Sprawdź usługi WebHCat

Jeden typowy scenariusz, Apache Hive, Apache Pig i Apache Sqoop zadań kończy się niepowodzeniem jest błąd za pomocą [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (lub *Templeton*) usługi. Usługi WebHCat jest interfejsem REST do zdalnego wykonania, takie jak Hive, Pig, koordynującego i MapReduce. WebHCat tłumaczy żądania przesłania zadania do aplikacji Apache Hadoop YARN i zwraca stan pochodną stan aplikacji usługi YARN.  W poniższych sekcjach opisano typowe kody stanu WebHCat HTTP.

#### <a name="badgateway-502-status-code"></a>BadGateway (kod stanu 502)

To jest ogólny komunikat z węzłów bramy i jest najczęściej kod stanu błędu. Jedną z możliwych przyczyn tego jest zablokowany w dół z aktywnego węzła głównego usługi WebHCat. Aby sprawdzić taką ewentualność, użyj następującego polecenia CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari wyświetla alert przedstawiający hosty, na których usługi WebHCat jest wyłączona. Możesz spróbować przełączyć tę usługę WebHCat Utwórz kopię zapasową, uruchamiając ponownie usługę sieciową hosta.

![Uruchom ponownie serwer usługi WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat.png)

Jeśli serwer usługi WebHCat nadal nie pojawiania się nowych Sprawdź komunikaty o błędach w Dzienniku działań. Aby uzyskać szczegółowe informacje, sprawdź `stderr` i `stdout` pliki, do których odwołuje się w węźle.

#### <a name="webhcat-times-out"></a>Limit czasu usługi WebHCat

Bramy usługi HDInsight upłynie limit czasu odpowiedzi, które zajmują więcej niż dwie minuty, zwracając `502 BadGateway`. WebHCat zapytania usługi YARN dla zadania stanów, a jeśli YARN trwa dłużej niż dwie minuty, aby odpowiedzieć, żądania, limit czasu może.

W takim przypadku sprawdź następujące dzienniki w `/var/log/webhcat` katalogu:

* **webhcat.log** jest dziennika log4j dzienniki zapisuje serwerów, które
* **dziennika console.log webhcat** to stdout serwera po uruchomieniu
* **webhcat konsoli error.log** jest stderr procesu serwera

> [!NOTE]  
> Każdy `webhcat.log` przerzuceniem codziennie, generowanie plików o nazwie `webhcat.log.YYYY-MM-DD`. Wybierz plik odpowiednie dla zakresu czasu, w którym wystąpiło badane.

W poniższych sekcjach opisano niektóre z możliwych przyczyn WebHCat przekroczeń limitu czasu.

##### <a name="webhcat-level-timeout"></a>Limit czasu poziomu usługi WebHCat

Po użyciu usługi WebHCat pod obciążeniem, z ponad 10 Otwórz gniazd, co zajmuje więcej czasu ustanawiać nowych połączeń gniazd, które może powodować przekroczenie limitu czasu. Aby wyświetlić listę połączeń sieciowych, do i z usługi WebHCat, należy użyć `netstat` na bieżącego aktywnego węzła głównego:

```bash
netstat | grep 30111
```

30111 jest port, który nasłuchuje WebHCat. Liczba otwartych gniazd powinna być mniejsza niż 10.

Jeśli nie ma żadnych otwartych gniazd, poprzednie polecenie nie rezultatów. Aby sprawdzić, czy Templeton znajduje się i nasłuchuje na porcie 30111, użyj:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Limit czasu poziomu usługi YARN

Templeton wywołuje YARN do uruchamiania zadań i komunikacji między Templeton i YARN może spowodować przekroczenie limitu czasu.

Na poziomie usługi YARN istnieją dwa rodzaje przekroczeń limitu czasu:

1. Trwa przesyłanie zadania YARN może być wystarczająco długi, by powodować przekroczenie limitu czasu.

    Jeśli otworzysz `/var/log/webhcat/webhcat.log` plik dziennika i wyszukaj "w kolejce zadanie" może zostać wyświetlony wiele wpisów gdzie czas wykonywania jest zbyt długa (> 2000 ms), z wpisy przedstawiający zwiększenie oczekiwania razy.

    Czas w kolejce zadań w dalszym ciągu zwiększa, ponieważ szybkość jaką przesłania nowe zadania jest wyższa niż szybkość jaką stare zadania zostały wykonane. 100% używana, po pamięci usługi YARN *joblauncher kolejki* nie będzie można "pożyczać" pojemność z *kolejką domyślną*. W związku z tym żadne nowe zadania mogą być akceptowane w kolejce joblauncher. To zachowanie może powodować czasu oczekiwania na dłuższe i, co powoduje przekroczenie limitu czasu jest zwykle następuje wiele innych.

    Na poniższej ilustracji przedstawiono kolejkę joblauncher % 714.4 nadmiernie obciążany. Jest to dopuszczalne, tak długo, jak jest nadal wolna pojemność w kolejce domyślne zapożyczonych z. Jednak jeśli pamięci usługi YARN wynosi 100% pojemności klastra jest w pełni wykorzystane, nowe zadania musi czekać, co ostatecznie powoduje, że limity czasu.

    ![Kolejka Joblauncher](./media/hdinsight-troubleshoot-failed-cluster/joblauncher-queue.png)

    Istnieją dwa sposoby, aby rozwiązać ten problem: Zmniejsz szybkość nowych zadań przesyłanych lub zwiększenie szybkości użycie starych zadania, skalując w górę do klastra.

2. Przetwarzanie YARN może potrwać dłuższy czas, co może powodować przekroczeń limitu czasu.

    * Wyświetl wszystkie zadania: To jest czasochłonne wywołanie. To wywołanie wylicza aplikacje z Menedżera zasobów YARN i dla każdej ukończonej aplikacji, pobiera stan z YARN JobHistoryServer. Za pomocą większej liczby zadań to wywołanie może limit czasu.

    * Lista zadań sprzed siedmiu dni: HDInsight YARN JobHistoryServer jest skonfigurowany do przechowywania informacji ukończone zadanie przez siedem dni (`mapreduce.jobhistory.max-age-ms` wartości). Podczas próby wyliczenia przeczyszczone zadania powoduje przekroczenie limitu czasu.

Aby zdiagnozować te problemy:

1. Określić zakres czasu UTC, rozwiązywać problemy z
2. Wybierz odpowiedni `webhcat.log` pliki
3. Wyszukaj OSTRZEGAJ i komunikaty o BŁĘDACH w tym czasie

#### <a name="other-webhcat-failures"></a>Inne błędy usługi WebHCat

1. Kod stanu HTTP 500

    W większości przypadków, w których WebHCat zwraca 500 komunikat o błędzie zawiera szczegóły dotyczące błędu. W przeciwnym razie przejrzyj `webhcat.log` dla OSTRZEGAJ i komunikaty o BŁĘDACH.

2. Niepowodzenia zadań

    Można wykluczyć sytuacji, gdy interakcji z usługą WebHCat zostały wykonane pomyślnie, ale zadania kończą się niepowodzeniem.

    Templeton zbiera dane wyjściowe zadania konsoli jako `stderr` w `statusdir`, co ma często przydatne podczas rozwiązywania problemów. `stderr` zawiera identyfikator aplikacji usługi YARN rzeczywiste zapytania.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Przejrzyj stos środowiska i wersje

Interfejsu użytkownika Ambari **stosu i wersji** strona zawiera informacje o klastrze usługi konfiguracji i usługi Historia wersji.  Nieprawidłowe wersje biblioteki usługi Hadoop można przyczyny awarii klastra.  W Interfejsie użytkownika Ambari, wybierz **administratora** menu i następnie **stosy i wersje**.  Wybierz **wersji** karty na stronie, aby wyświetlić informacje o wersji usługi:

![Stos i wersje](./media/hdinsight-troubleshoot-failed-cluster/stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5. Przejrzyj pliki dziennika

Istnieje wiele typów dzienników, które są generowane na podstawie wielu usług i składników wchodzących w skład klastra usługi HDInsight. [Pliki dziennika usługi WebHCat](#check-your-webhcat-service) są opisane wcześniej. Istnieje kilka innych przydatne plików dziennika, które można zbadać Aby zawęzić problemy związane z klastrem, zgodnie z opisem w poniższych sekcjach.

* Klastry HDInsight składa się z kilku węzłów, z których większość są nadzorowania do uruchomienia przesłane zadania. Zadania są uruchamiane równolegle, ale pliki dziennika mogą być wyświetlane tylko wyniki liniowo. HDInsight wykonuje nowych zadań, kończące inne scenariusze, które się nie powieść, aby najpierw zostało wykonane. To działanie jest rejestrowane `stderr` i `syslog` plików.

* Pliki dziennika akcji skryptu umożliwia wyświetlenie błędów lub zmiany konfiguracji nieoczekiwany podczas procesu tworzenia klastra.

* Dzienniki usługi Hadoop w kroku zidentyfikować zadań usługi Hadoop, uruchomione jako część krokiem zawierające błędy.

### <a name="check-the-script-action-logs"></a>Sprawdź dzienniki akcji skryptu

HDInsight [akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) uruchamianie skryptów w klastrze, ręcznie lub czas określony. Na przykład akcji skryptu można zainstalować dodatkowe oprogramowanie w klastrze lub zmienić ustawienia konfiguracji z wartościami domyślnymi. Sprawdzanie dzienników akcji skryptu zapewniają wgląd w błędów występujących podczas instalacji klastra i konfiguracji.  Stan akcji skryptu można wyświetlić, wybierając **ops** przycisk w Interfejsie użytkownika Ambari lub uzyskując dostęp do dzienników z domyślnego konta magazynu.

Dzienniki akcji skryptów znajdują się w `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` katalogu.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Wyświetlanie dzienników HDInsight przy użyciu Ambari szybkie łącza

Interfejsu użytkownika Ambari HDInsight obejmuje pewną liczbę **szybkich łączy** sekcje.  Dostępu łącza dziennika dla określonej usługi w klastrze usługi HDInsight, Otwórz interfejs użytkownika systemu Ambari dla klastra, a następnie wybierz link usługi z listy po lewej stronie. Wybierz **szybkich łączy** listy rozwijanej, a następnie węzeł HDInsight zainteresowań, a następnie wybierz link do jego dziennika skojarzonej.

Na przykład dzienniki systemu plików HDFS:

![Ambari szybkie łącza do plików dziennika](./media/hdinsight-troubleshoot-failed-cluster/quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Wyświetlanie plików dziennika generowanych przez usługi Hadoop

Klaster usługi HDInsight generuje dzienników, które są zapisywane w tabelach platformy Azure i usługi Azure Blob storage. YARN tworzy własne dzienniki wykonywania. Aby uzyskać więcej informacji, zobacz [Zarządzanie dziennikami klastra usługi HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Przejrzyj zrzutów stosu

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w tym czasie, które są przydatne do diagnozowania problemów, które występują w czasie wykonywania. Aby uzyskać więcej informacji, zobacz [zrzutów stosów włączyć na potrzeby usługi Apache Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Sprawdź ustawienia konfiguracji

Klastry HDInsight są wstępnie skonfigurowane przy użyciu ustawień domyślnych dla powiązanych usług, takich jak Hadoop, Hive, HBase i tak dalej. W zależności od typu klastra, jej konfiguracja sprzętowa, jego liczbę węzłów, typy zadań używasz i dane, użytkownik pracuje z (i sposób przetwarzania danych), może być konieczne zoptymalizować konfigurację.

Aby uzyskać szczegółowe instrukcje dotyczące optymalizacji wydajności konfiguracji w przypadku większości scenariuszy, zobacz [zoptymalizowania konfiguracji klastra za pomocą systemu Apache Ambari](hdinsight-changing-configs-via-ambari.md). Korzystając z platformy Spark, zobacz [zadania optymalizacji Apache Spark dla wydajności](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Odtwórz błąd w innym klastrze

Aby ułatwić diagnozowanie źródła błędu klastra, uruchom nowy klaster z taką samą konfiguracją i ponownie prześlij zadanie zakończone niepowodzeniem kroki jedno po drugim. Sprawdź wyniki każdego kroku przed przetworzeniem kolejny. Ta metoda zapewnia możliwość rozwiązać, a następnie ponownie uruchom pojedynczy krok zakończony niepowodzeniem. Ta metoda również ma tę zaletę tylko raz podczas ładowania danych wejściowych.

1. Tworzenie nowego klastra testowego z taką samą konfigurację jak klastra nie powiodło się.
2. Prześlij pierwszym krokiem zadania do klastra testowego.
3. Po zakończeniu przetwarzania kroku sprawdź, czy błędy w plikach dziennika kroku. Łączenie z węzłem głównym klastra testowego i wyświetlanie plików dziennika. Pliki dziennika kroku są wyświetlane tylko po kroku jest uruchamiane przez pewien czas, zostanie zakończone, lub nie powiedzie się.
4. Pierwszym krokiem zakończyło się pomyślnie, należy uruchomić następny krok. Jeśli wystąpiły błędy, zbadanie błędów w plikach dziennika. Jeśli był błąd w kodzie, wprowadzanie poprawek i ponownie uruchom ten krok.
5. Kontynuuj, dopóki wszystkie czynności wykonane bez błędów.
6. Po zakończeniu debugowania klastra testowego, usuń go.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)
* [Dostęp do Apache Hadoop YARN application logowania HDInsight opartych na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włączanie zrzutów sterty dla usługi Apache Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Znane problemy w przypadku klastra Apache Spark w HDInsight](hdinsight-apache-spark-known-issues.md)
