---
title: Rozwiązywanie problemów z powolnym lub nieudanym zadaniem w klastrze usługi Azure HDInsight
description: Diagnozowanie i rozwiązywanie problemów z powolnym lub nieudanym zadaniem w klastrze usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: be991b63784a2c72a51bfbdc8506f3b4695ed6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895314"
---
# <a name="troubleshoot-a-slow-or-failing-job-on-a-hdinsight-cluster"></a>Rozwiązywanie problemów dotyczących wolnego działania lub niepowodzenia zadania w klastrze usługi HDInsight

Jeśli aplikacja przetwarzająca dane w klastrze HDInsight działa wolno lub kończy się niepowodzeniem z kodem błędu, masz kilka opcji rozwiązywania problemów. Jeśli zadania są trwa dłużej niż oczekiwano lub widzisz powolne czasy odpowiedzi w ogóle, mogą występować błędy w górę listy z klastra, takich jak usługi, na których działa klaster. Jednak najczęstszą przyczyną tych spowolnień jest niewystarczające skalowanie. Podczas tworzenia nowego klastra HDInsight wybierz odpowiednie [rozmiary maszyn wirtualnych](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters).

Aby zdiagnozować powolny lub nieudany klaster, należy zebrać informacje o wszystkich aspektach środowiska, takich jak skojarzone usługi azure, konfiguracja klastra i informacje o wykonaniu zadania. Pomocna diagnostyka jest próba odtworzenia stanu błędu w innym klastrze.

* Krok 1: Zbierz dane dotyczące problemu.
* Krok 2: Sprawdź poprawność środowiska klastra HDInsight.
* Krok 3: Wyświetlanie kondycji klastra.
* Krok 4: Przejrzyj stos środowiska i wersje.
* Krok 5: Sprawdź pliki dziennika klastra.
* Krok 6: Sprawdź ustawienia konfiguracji.
* Krok 7: Odtwórz błąd w innym klastrze.

## <a name="step-1-gather-data-about-the-issue"></a>Krok 1: Zbieranie danych o problemie

Usługa HDInsight udostępnia wiele narzędzi, za pomocą których można identyfikować i rozwiązywać problemy z klastrami. Poniższe kroki prowadzą użytkownika przez te narzędzia i zawierają sugestie dotyczące wskazywania problemu.

### <a name="identify-the-problem"></a>Identyfikowanie problemu

Aby pomóc w zidentyfikowaniu problemu, należy wziąć pod uwagę następujące pytania:

* Czego się spodziewałem? Co się stało zamiast tego?
* Jak długo trwał proces? Jak długo powinien działać?
* Czy moje zadania zawsze działają wolno w tym klastrze? Czy działały szybciej w innym klastrze?
* Kiedy ten problem wystąpił po raz pierwszy? Jak często zdarzało się to od tego czasu?
* Czy w konfiguracji klastra coś się zmieniło?

### <a name="cluster-details"></a>Szczegóły klastra

Ważne informacje klastra obejmują:

* Nazwa klastra.
* Region klastra - sprawdź [awarie regionu](https://azure.microsoft.com/status/).
* Typ i wersja klastra HDInsight.
* Typ i liczba wystąpień HDInsight określonych dla węzłów głowy i procesu roboczego.

Portal Azure może dostarczyć tych informacji:

![Informacje portalu usługi HDInsight Azure](./media/hdinsight-troubleshoot-failed-cluster/hdi-azure-portal-info.png)

Można również użyć [interfejsu wiersza polecenia platformy Azure:](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Inną opcją jest użycie programu PowerShell. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami Apache Hadoop w programie HDInsight za pomocą programu Azure PowerShell](hdinsight-administer-use-powershell.md).

## <a name="step-2-validate-the-hdinsight-cluster-environment"></a>Krok 2: Sprawdzanie poprawności środowiska klastra HDInsight

Każdy klaster USŁUGI HDInsight opiera się na różnych usługach platformy Azure i na oprogramowaniu typu open source, takim jak Apache HBase i Apache Spark. Klastry USŁUGI HDInsight mogą również wywoływać inne usługi platformy Azure, takie jak sieci wirtualne platformy Azure.  Awaria klastra może być spowodowana przez dowolną z uruchomionych usług w klastrze lub przez usługę zewnętrzną.  Zmiana konfiguracji usługi klastrowania może również spowodować niepowodzenie klastra.

### <a name="service-details"></a>Szczegóły usługi

* Sprawdź wersje biblioteki open source.
* Sprawdź [awarie usługi Azure](https://azure.microsoft.com/status/).  
* Sprawdź, czy limity użycia usługi Azure. 
* Sprawdź konfigurację podsieci sieci wirtualnej platformy Azure.  

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika Ambari

Apache Ambari zapewnia zarządzanie i monitorowanie klastra HDInsight za pomocą interfejsu użytkownika sieci Web i interfejsu API REST. Ambari jest dołączony do klastrów HDInsight opartych na systemie Linux. Wybierz **okienko pulpitu nawigacyjnego klastra** na stronie Usługi HDInsight portalu Azure.  Wybierz **okienko pulpitu nawigacyjnego klastra HDInsight,** aby otworzyć interfejs użytkownika ambari, i wprowadź poświadczenia logowania klastra.  

![Apache Ambari — omówienie pulpitu nawigacyjnego](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-overview.png)

Aby otworzyć listę widoków usług, wybierz pozycję **Widoki Ambari** na stronie portalu Azure.  Ta lista zależy od tego, które biblioteki są zainstalowane. Na przykład może zostać wyświetlony Menedżer kolejek YARN, Widok gałęzi i Widok Tez.  Wybierz łącze usługi, aby wyświetlić informacje o konfiguracji i usłudze.

#### <a name="check-for-azure-service-outages"></a>Sprawdzanie, czy wystąpiły awarie usługi platformy Azure

Usługa HDInsight opiera się na kilku usługach platformy Azure. Uruchamia serwery wirtualne na platformie Azure HDInsight, przechowuje dane i skrypty w magazynie obiektów Blob platformy Azure lub usłudze Azure Data Lake Storage oraz indeksuje pliki dziennika w magazynie tabel platformy Azure. Zakłócenia tych usług, choć rzadkie, mogą powodować problemy w umiań HDInsight. Jeśli masz nieoczekiwane spowolnienia lub błędy w klastrze, sprawdź [pulpit nawigacyjny stanu platformy Azure](https://azure.microsoft.com/status/). Stan każdej usługi jest wyświetlany według regionu. Sprawdź region klastra, a także regiony dla wszystkich powiązanych usług.

#### <a name="check-azure-service-usage-limits"></a>Sprawdzanie limitów użycia usługi platformy Azure

Jeśli uruchamiasz duży klaster lub uruchomiono wiele klastrów jednocześnie, klaster może zakończyć się niepowodzeniem, jeśli przekroczono limit usługi platformy Azure. Limity usług różnią się w zależności od subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure subscription and service limits, quotas, and constraints (Limity, przydziały i ograniczenia usług i subskrypcji platformy Azure)](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
Można zażądać, aby firma Microsoft zwiększyła liczbę dostępnych zasobów HDInsight (takich jak rdzenie maszyn wirtualnych i wystąpienia maszyn wirtualnych) za pomocą [żądania zwiększenia przydziału podstawowego Menedżera zasobów](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

#### <a name="check-the-release-version"></a>Sprawdź wersję

Porównaj wersję klastra z najnowszą wersją HDInsight. Każda wersja HDInsight zawiera ulepszenia, takie jak nowe aplikacje, funkcje, poprawki i poprawki błędów. Problem, który ma wpływ na klaster może zostały rozwiązane w najnowszej wersji. Jeśli to możliwe, uruchom ponownie klaster przy użyciu najnowszej wersji programu HDInsight i skojarzonych bibliotek, takich jak Apache HBase, Apache Spark i innych.

#### <a name="restart-your-cluster-services"></a>Ponowne uruchamianie usług klastrowania

Jeśli występują spowolnienia w klastrze, należy rozważyć ponowne uruchomienie usług za pośrednictwem interfejsu użytkownika Ambari lub interfejsu wiersza polecenia klasycznego platformy Azure. Klaster może występować błędy przejściowe, a ponowne uruchomienie jest najszybszym sposobem stabilizacji środowiska i ewentualnie zwiększyć wydajność.

## <a name="step-3-view-your-clusters-health"></a>Krok 3: Wyświetlanie kondycji klastra

Klastry HDInsight składają się z różnych typów węzłów uruchomionych w wystąpieniach maszyn wirtualnych. Każdy węzeł może być monitorowany pod kątem głodu zasobów, problemów z łącznością sieciową i innych problemów, które mogą spowolnić klaster. Każdy klaster zawiera dwa węzły głównego, a większość typów klastra zawiera kombinację węzłów roboczych i brzegowych. 

Aby uzyskać opis różnych węzłów używanych przez każdy typ klastra, zobacz [Konfigurowanie klastrów w systemie HDInsight za pomocą aplikacji Apache Hadoop, Apache Spark, Apache Kafka i innych](hdinsight-hadoop-provision-linux-clusters.md).

W poniższych sekcjach opisano sposób sprawdzania kondycji każdego węzła i całego klastra.

### <a name="get-a-snapshot-of-the-cluster-health-using-the-ambari-ui-dashboard"></a>Pobierz migawkę kondycji klastra przy użyciu pulpitu nawigacyjnego interfejsu użytkownika Ambari

Pulpit nawigacyjny interfejsu użytkownika`https://<clustername>.azurehdinsight.net` [Ambari](#view-cluster-configuration-settings-with-the-ambari-ui) ( ) zawiera przegląd kondycji klastra, takich jak czas pracy bez przestojów, pamięć, użycie sieci i procesora, użycie dysku HDFS i tak dalej. Użyj sekcji Hosts w Ambari, aby wyświetlić zasoby na poziomie hosta. Można również zatrzymać i ponownie uruchomić usługi.

### <a name="check-your-webhcat-service"></a>Sprawdź swoją usługę WebHCat

Jednym z typowych scenariuszy dla Apache Hive, Apache Pig lub Apache Sqoop zadania nie jest niepowodzenie z [WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md) (lub *Templeton*) usługi. WebHCat to interfejs REST do zdalnego wykonywania zadań, takich jak Hive, Pig, Scoop i MapReduce. WebHCat tłumaczy żądania przesyłania zadań na aplikacje Apache Hadoop YARN i zwraca stan pochodzący ze stanu aplikacji YARN.  W poniższych sekcjach opisano typowe kody stanu HTTP WebHCat.

#### <a name="badgateway-502-status-code"></a>BadGateway (kod stanu 502)

Ten kod jest ogólny komunikat z węzłów bramy i jest najczęstszym kody stanu awarii. Jedną z możliwych przyczyn jest usługa WebHCat jest w dół na aktywnym węzłem głównym. Aby sprawdzić tę możliwość, użyj następującego polecenia CURL:

```bash
curl -u admin:{HTTP PASSWD} https://{CLUSTERNAME}.azurehdinsight.net/templeton/v1/status?user.name=admin
```

Ambari wyświetla alert pokazujący hosty, na których usługa WebHCat jest wyłączona. Możesz spróbować przywrócić usługę WebHCat, uruchamiając ponownie usługę na jej hoście.

![Apache Ambari Uruchom ponownie serwer WebHCat](./media/hdinsight-troubleshoot-failed-cluster/restart-webhcat-server.png)

Jeśli serwer WebHCat nadal nie pojawi się, sprawdź w dzienniku operacji komunikaty o błędach. Aby uzyskać bardziej szczegółowe `stderr` `stdout` informacje, sprawdź pliki i pliki, do których odwołuje się węzeł.

#### <a name="webhcat-times-out"></a>Czas oczekiwania webhcat

Usługa HDInsight Gateway wydłuża odpowiedzi, które `502 BadGateway`zdają więcej niż dwie minuty. WebHCat wysyła zapytania do usług YARN dla stanów zadań, a jeśli YARN trwa dłużej niż dwie minuty, aby odpowiedzieć, to żądanie może się przetysuć.

W takim przypadku przejrzyj `/var/log/webhcat` następujące dzienniki w katalogu:

* **webhcat.log** to log4j log do którego serwer zapisuje logi
* **webhcat-console.log** jest stdout serwera po uruchomieniu
* **webhcat-console-error.log** jest stderr procesu serwera

> [!NOTE]  
> Każdy `webhcat.log` z nich jest przewracany codziennie, generując pliki o nazwie `webhcat.log.YYYY-MM-DD`. Wybierz odpowiedni plik dla badanych zakresów czasowych.

W poniższych sekcjach opisano niektóre możliwe przyczyny limitów czasu WebHCat.

##### <a name="webhcat-level-timeout"></a>Limit czasu poziomu WebHCat

Gdy WebHCat jest pod obciążeniem, z więcej niż 10 otwartych gniazd, trwa dłużej, aby ustanowić nowe połączenia gniazda, co może spowodować limit czasu. Aby wyświetlić listę połączeń sieciowych do `netstat` i z WebHCat, użyj bieżącego aktywnego headnode:

```bash
netstat | grep 30111
```

30111 to port, na który nasłuchuje WebHCat. Liczba otwartych gniazd powinna być mniejsza niż 10.

Jeśli nie ma żadnych otwartych gniazd, poprzednie polecenie nie daje wyniku. Aby sprawdzić, czy Templeton jest w górę i nasłuchuje na porcie 30111, użyj:

```bash
netstat -l | grep 30111
```

##### <a name="yarn-level-timeout"></a>Limit czasu poziomu YARN

Templeton wzywa YARN do wykonywania zadań, a komunikacja między Templeton i YARN może spowodować przekroczenie limitu czasu.

Na poziomie YARN istnieją dwa typy limitów czasu:

1. Przesłanie zadania YARN może potrwać wystarczająco długo, aby spowodować przesunięcie limitu czasu.

    Jeśli otworzysz `/var/log/webhcat/webhcat.log` plik dziennika i wyszukasz "zadanie w kolejce", może zostać wyświetlonych wiele wpisów, w których czas wykonywania jest zbyt długi (>2000 ms), z wpisami przedstawiającymi wydłużanie czasu oczekiwania.

    Czas pracy w kolejce nadal wzrasta, ponieważ szybkość przesyłania nowych zadań jest wyższa niż szybkość, z jaką stare zadania są wykonywane. Gdy pamięć YARN jest w 100% używana, *kolejka joblauncher* nie może już pożyczać pojemności z *kolejki domyślnej.* W związku z tym nie więcej nowych miejsc pracy mogą być akceptowane w kolejce joblauncher. To zachowanie może spowodować, że czas oczekiwania stają się dłuższe i dłuższe, powodując błąd limitu czasu, który jest zwykle następuje wiele innych.

    Na poniższej ilustracji przedstawiono kolejkę joblauncher na 714,4% nadużywane. Jest to dopuszczalne, o ile w domyślnej kolejce nadal istnieje wolna pojemność do zapożyczenia. Jednak gdy klaster jest w pełni wykorzystywane i pamięci YARN jest na 100% pojemności, nowe zadania muszą czekać, co ostatecznie powoduje limity czasu.

    ![Widok kolejki uruchamiania zadań HDInsight](./media/hdinsight-troubleshoot-failed-cluster/hdi-job-launcher-queue.png)

    Istnieją dwa sposoby rozwiązania tego problemu: zmniejszyć szybkość przesyłania nowych zadań lub zwiększyć szybkość zużycia starych zadań przez skalowanie klastra.

2. Przetwarzanie przędzy może zająć dużo czasu, co może spowodować przesunięcie limitu czasu.

    * Lista wszystkich zadań: Jest to czasochłonne wywołanie. To wywołanie wylicza aplikacje z YARN ResourceManager i dla każdej ukończonej aplikacji pobiera stan z YARN JobHistoryServer. Przy większej liczbie zadań, to wywołanie może limit czasu.

    * Lista zadań starszych niż siedem dni: Funkcja HDInsight YARN JobHistoryServer jest`mapreduce.jobhistory.max-age-ms` skonfigurowana do przechowywania informacji o ukończonym zadaniu przez siedem dni (wartość). Próba wyliczenia przeczyszonych zadań powoduje przeterminowanie.

Aby zdiagnozować te problemy:

1. Określanie zakresu czasu UTC do rozwiązywania problemów
2. Wybierz odpowiednie `webhcat.log` pliki
3. Poszukaj komunikatów WARN i ERROR w tym czasie

#### <a name="other-webhcat-failures"></a>Inne błędy WebHCat

1. Kod stanu HTTP 500

    W większości przypadków, gdy WebHCat zwraca 500, komunikat o błędzie zawiera szczegółowe informacje na temat błędu. W przeciwnym `webhcat.log` razie zajrzyj do komunikatów WARN i ERROR.

2. Niepowodzenia zadania

    Mogą wystąpić przypadki, w których interakcje z WebHCat zakończyć się pomyślnie, ale zadania są nieskuteczne.

    Templeton zbiera dane wyjściowe `stderr` `statusdir`konsoli zadań, jak w , co jest często przydatne do rozwiązywania problemów. `stderr`zawiera identyfikator aplikacji YARN rzeczywistej kwerendy.

## <a name="step-4-review-the-environment-stack-and-versions"></a>Krok 4: Przejrzyj stos środowiska i wersje

Ambari **UI Stack i wersja** strony zawiera informacje o konfiguracji usług klastrowania i historii wersji usługi.  Niepoprawne wersje biblioteki usługi Hadoop może być przyczyną awarii klastra.  W interfejsie użytkownika Ambari wybierz menu **Administrator,** a następnie **polecenia Stosy i wersje**.  Wybierz kartę **Wersje** na stronie, aby wyświetlić informacje o wersji usługi:

![Apache Ambari Stos i wersje](./media/hdinsight-troubleshoot-failed-cluster/ambari-stack-versions.png)

## <a name="step-5-examine-the-log-files"></a>Krok 5: Sprawdź pliki dziennika

Istnieje wiele typów dzienników, które są generowane z wielu usług i składników, które składają się na klaster HDInsight. [Pliki dziennika WebHCat](#check-your-webhcat-service) są opisane wcześniej. Istnieje kilka innych przydatnych plików dziennika, które można zbadać, aby zawęzić problemy z klastrem, zgodnie z opisem w poniższych sekcjach.

* Klastry HDInsight składają się z kilku węzłów, z których większość ma za zadanie uruchamiać przesłane zadania. Zadania są uruchamiane jednocześnie, ale pliki dziennika mogą wyświetlać wyniki tylko liniowo. Program HDInsight wykonuje nowe zadania, kończąc inne, które nie ukończyć najpierw. Wszystkie te działania są `stderr` rejestrowane `syslog` w plikach i.

* Pliki dziennika akcji skryptu pokazują błędy lub nieoczekiwane zmiany konfiguracji podczas procesu tworzenia klastra.

* Dzienniki kroków Hadoop identyfikują zadania Hadoop uruchomione w ramach kroku zawierającego błędy.

### <a name="check-the-script-action-logs"></a>Sprawdzanie dzienników akcji skryptu

[Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight uruchamiają skrypty w klastrze ręcznie lub po określeniu. Na przykład akcje skryptu mogą służyć do instalowania dodatkowego oprogramowania w klastrze lub do zmiany ustawień konfiguracji z wartości domyślnych. Sprawdzanie dzienników akcji skryptu może zapewnić wgląd w błędy, które wystąpiły podczas konfigurowania i konfigurowania klastra.  Stan akcji skryptu można wyświetlić, wybierając przycisk **ops** w interfejsie użytkownika Ambari lub uzyskując dostęp do dzienników z domyślnego konta magazynu.

Dzienniki akcji skryptu znajdują `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` się w katalogu.

### <a name="view-hdinsight-logs-using-ambari-quick-links"></a>Wyświetlanie dzienników HDInsight przy użyciu szybkich łączy Ambari

Interfejs HDInsight Ambari zawiera kilka sekcji **Szybkich łączy.**  Aby uzyskać dostęp do łączy dziennika dla określonej usługi w klastrze HDInsight, otwórz interfejs użytkownika Ambari dla klastra, a następnie wybierz łącze usługi z listy po lewej stronie. Wybierz pozycję rozwijaną **Szybkie łącza,** a następnie interesujący węzeł HDInsight, a następnie wybierz łącze dla skojarzonego z nim dziennika.

Na przykład dla dzienników HDFS:

![Ambari Szybkie łącza do plików dziennika](./media/hdinsight-troubleshoot-failed-cluster/apache-ambari-quick-links.png)

### <a name="view-hadoop-generated-log-files"></a>Wyświetlanie plików dziennika generowanych przez Hadoop

Klaster USŁUGI HDInsight generuje dzienniki zapisywane w tabelach platformy Azure i magazynie obiektów blob platformy Azure. YARN tworzy własne dzienniki wykonywania. Aby uzyskać więcej informacji, zobacz [Zarządzanie dziennikami dla klastra HDInsight](hdinsight-log-management.md#access-the-hadoop-log-files).

### <a name="review-heap-dumps"></a>Przejrzyj zrzuty sterty

Zrzuty sterty zawierają migawkę pamięci aplikacji, w tym wartości zmiennych w tym czasie, które są przydatne do diagnozowania problemów występujących w czasie wykonywania. Aby uzyskać więcej informacji, zobacz [Włączanie zrzutów sterty dla usług Apache Hadoop w systemie HDInsight opartym na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md).

## <a name="step-6-check-configuration-settings"></a>Krok 6: Sprawdź ustawienia konfiguracji

Klastry HDInsight są wstępnie skonfigurowane z ustawieniami domyślnymi dla powiązanych usług, takich jak Hadoop, Hive, HBase i tak dalej. W zależności od typu klastra, jego konfiguracji sprzętowej, liczby węzłów, typów uruchomionych zadań i danych, z którymi pracujesz (i sposobu przetwarzania tych danych), może być konieczne zoptymalizowanie konfiguracji.

Aby uzyskać szczegółowe instrukcje dotyczące optymalizacji konfiguracji wydajności w większości scenariuszy, zobacz [Optymalizowanie konfiguracji klastra za pomocą programu Apache Ambari](hdinsight-changing-configs-via-ambari.md). Podczas korzystania z programu Spark zobacz [Optymalizowanie zadań platformy Apache Spark pod kątem wydajności](spark/apache-spark-perf.md). 

## <a name="step-7-reproduce-the-failure-on-a-different-cluster"></a>Krok 7: Odtwórz błąd w innym klastrze

Aby ułatwić diagnozowanie źródła błędu klastra, uruchom nowy klaster z tą samą konfiguracją, a następnie ponownie przedsuń kroki zadania, które nie powiodło się jeden po drugim. Sprawdź wyniki każdego kroku przed przetworzeniem następnego. Ta metoda daje możliwość skorygowania i ponownego uruchomienia pojedynczego kroku nie powiodło się. Ta metoda ma również tę zaletę, że tylko jednocześnie ładuje dane wejściowe.

1. Utwórz nowy klaster testowy o tej samej konfiguracji co klaster, który nie powiódł się.
2. Prześlij pierwszy krok zadania do klastra testowego.
3. Po zakończeniu przetwarzania kroku sprawdź, czy w plikach dziennika kroków nie ma błędów. Połącz się z węzłem głównym klastra testowego i wyświetl tam pliki dziennika. Pliki dziennika kroków pojawiają się tylko po tym, jak krok jest uruchamiany przez pewien czas, kończy się lub kończy się niepowodzeniem.
4. Jeśli pierwszy krok się powiedzie, uruchom następny krok. Jeśli wystąpiły błędy, zbadaj błąd w plikach dziennika. Jeśli był to błąd w kodzie, należy dokonać korekty i ponownie uruchomić krok.
5. Kontynuuj, aż wszystkie kroki będą działać bez błędu.
6. Po zakończeniu debugowania klastra testowego usuń go.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Analizowanie dzienników hdinsight](hdinsight-debug-jobs.md)
* [Uzyskaj dostęp do aplikacji Apache Hadoop YARN w systemie HDInsight opartym na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włącz zrzuty sterty dla usług Apache Hadoop w systemie HDInsight opartym na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Znane problemy dla klastra Apache Spark w programie HDInsight](hdinsight-apache-spark-known-issues.md)
