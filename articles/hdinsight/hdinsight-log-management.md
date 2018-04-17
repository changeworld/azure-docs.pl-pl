---
title: Zarządzanie dziennikami dla klastra usługi HDInsight - Azure HDInsight | Dokumentacja firmy Microsoft
description: Określ typy, rozmiarów i zasady przechowywania plików dziennika aktywności HDInsight.
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
ms.openlocfilehash: 165464bd6851bf5fa76632731455722c393060c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Zarządzanie dziennikami klastra usługi HDInsight

Klaster usługi HDInsight zapewnia różne pliki dziennika. Na przykład Apache Hadoop i powiązanych usług, takich jak Apache Spark tworzy dzienniki wykonywania zadania szczegółowe. Zarządzanie plikami dziennika jest częścią utrzymanie dobrej kondycji klastra usługi HDInsight. Można także przepisami dotyczącymi archiwizacji dziennika.  Z powodu liczby i rozmiaru plików dziennika Optymalizacja magazynu dziennika i archiwizowania pomaga z usługi kosztów zarządzania.

Zarządzanie dziennikami klastra usługi HDInsight obejmuje zachowywanie informacji o wszystkie aspekty środowiska klastra. Informacje te obejmują wszystkie skojarzone dzienniki usługi Azure, konfiguracji klastra, informacji o wykonanie zadania, wszelkie stany błąd i innych danych, zgodnie z potrzebami.

Typowe kroki w zarządzanie dziennikiem HDInsight są:

* Krok 1: Określanie zasad przechowywania dziennika
* Krok 2: Zarządzanie dzienniki konfiguracji wersjach usługi klastra
* Krok 3: Zarządzanie plikami dziennika wykonywania zadań klastra
* Krok 4: Prognozy rozmiaru magazynu woluminu dziennika i kosztów
* Krok 5: Określanie dziennika archiwum zasadami i procesami

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Określanie zasad przechowywania dziennika

Pierwszym krokiem w opracowaniu strategii zarządzania dziennik klastra usługi HDInsight jest zbieranie informacji o różnych scenariuszy biznesowych i wymagania magazynu historii wykonywania zadania.

### <a name="cluster-details"></a>Szczegółów klastra

Poniższe szczegóły klastra są przydatne w pomoc w celu zebrania informacji w strategii zarządzania dziennika. Zebranie tych informacji ze wszystkich klastrów usługi HDInsight, utworzony w szczególności konto platformy Azure.

* Nazwa klastra
* Region i strefy Azure dostępności klastra
* Stan klastra, łącznie ze szczegółami dotyczącymi Ostatnia zmiana stanu
* Typ i numer wystąpienia usługi HDInsight określony wzorzec, podstawowe i węzły zadań

Możesz uzyskać większość tych informacji najwyższego poziomu przy użyciu portalu Azure.  Alternatywnie można użyć wiersza polecenia platformy Azure można pobrać informacji o z klastrem usługi HDInsight:

```
    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>
```

Można również wyświetlić te informacje za pomocą programu PowerShell.  Aby uzyskać więcej informacji, zobacz [klastrów zarządzania Hadoop w usłudze HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Zrozumienie obciążeń działających w klastrach

Należy zrozumieć typy obciążenia uruchomionego na z klastrem usługi HDInsight do odpowiednich rejestrowania strategii dla każdego typu projektu.

* Czy obciążeń eksperymentalne (na przykład deweloperskich lub testowania) lub jakości produkcji?
* Jak często są obciążeń wysokiej jakości zwykle uruchamiane?
* Istnieją obciążeń intensywnie i/lub długotrwałe?
* Należy używać żadnego obciążeń złożonego zestawu usług Hadoop, dla których wiele typów dzienniki są tworzone?
* Czy skojarzono żadnego obciążeń przepisami wykonywania elementy powiązane wymagania?

### <a name="example-log-retention-patterns-and-practices"></a>Przykładowy dziennik przechowywania wzorców i rozwiązania

* Należy wziąć pod uwagę w zachowaniu elementy danych powiązane śledzenia przez dodanie identyfikator każdego wpisu dziennika, czy za pośrednictwem innych technik. Dzięki temu wywodzić oryginalne źródło danych i wykonać operację, i danych za pośrednictwem każdego etapu, aby poznać jego spójności i ważności.

* Należy rozważyć sposób zbierania dzienników z klastra lub więcej niż jednego klastra i sortowanie ich do celów, takich jak inspekcji, monitorowania, planowania i alerty. Można użyć niestandardowego rozwiązania dostępu i pobierania plików dziennika na bieżąco, łączenie i analizować je, aby zapewnić wyświetlania pulpitu nawigacyjnego. Można również dodać dodatkowe możliwości alertów zabezpieczeń lub wykrywania awarii. Można tworzyć tych narzędzi, za pomocą programu PowerShell, zestawy SDK HDInsight lub kod, który uzyskuje dostęp do usługi Azure klasycznym modelu wdrażania.

* Należy rozważyć, czy do rozwiązania lub usługi monitorowania będzie przydatne korzyści. Microsoft System Center zawiera [pakiet administracyjny HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Narzędzia innych firm, takich jak Chukwa i Ganglia umożliwia również zbieranie oraz na scentralizowanie dzienniki. Wiele firm oferują usługi monitorowania rozwiązań opartych na platformie Hadoop danych big data, na przykład Centerity, Compuware APM, Sematext SPM i Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Krok 2: Zarządzanie wersjach usługi klastra i sprawdź dzienniki akcji skryptu

Typowy klastra usługi HDInsight korzysta kilka usług i pakiety oprogramowania typu open source (takie jak bazy danych Apache HBase, Apache Spark i tak dalej). Dla niektórych obciążeń, takich jak Bioinformatyka może być wymagane zachowania usługi Historia dziennika konfiguracji oprócz dzienniki wykonywania zadania.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetl ustawienia konfiguracji klastra przy użyciu interfejsu użytkownika narzędzia Ambari

Apache Ambari upraszcza zarządzanie, konfigurowanie oraz monitorowanie klastra usługi HDInsight, zapewniając sieci web interfejsu użytkownika i interfejsu API REST. Ambari znajduje się w klastrach HDInsight opartych na systemie Linux. Wybierz **pulpit nawigacyjny klastra** okienka na stronie portalu HDInsight Azure, aby otworzyć**"pulpitów nawigacyjnych klastrów** łącza strony.  Następnie wybierz pozycję **pulpit nawigacyjny klastra usługi HDInsight** okienko, aby otworzyć Interfejs użytkownika narzędzia Ambari.  Zostanie wyświetlony monit o podanie poświadczeń logowania klastra.

Aby otworzyć listę widoków usługi, wybierz **widoków Ambari** okienka na stronie portalu platformy Azure dla usługi HDInsight.  Tej listy różni się w zależności od tego, które biblioteki po zainstalowaniu.  Może na przykład zobacz menedżera kolejek YARN, Hive View oraz widok Tez.  Zaznacz dowolne łącze usługi, aby wyświetlić konfigurację i informacje o usłudze.  Interfejs użytkownika narzędzia Ambari **stosu i wersji** zawiera informacje o konfiguracji usługi klastrowania i Historia wersji usługi. Aby przejść do tej sekcji interfejsu użytkownika narzędzia Ambari, wybierz **Admin** menu, a następnie **stosy i wersje**.  Wybierz **wersji** kartę, aby wyświetlić informacje o wersji usługi.

![Stos i wersje](./media/hdinsight-log-management/stack-versions.png)

Za pomocą interfejsu użytkownika narzędzia Ambari, można pobrać konfiguracji usługi (lub wszystkim) uruchomione na określonym hoście (lub węzła) w klastrze.  Wybierz **hostów** menu, a następnie dołączyć hosta zainteresowań. Na tym hoście stronie zaznacz **akcje hosta** przycisk, a następnie **Pobierz klienta Configs**. 

![Configs klienta hosta](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Wyświetl dzienniki akcji skryptu

HDInsight [skryptu akcje](hdinsight-hadoop-customize-cluster-linux.md) uruchamiać skrypty w klastrze, ręcznie lub gdy określono. Na przykład akcji skryptu można zainstalować dodatkowe oprogramowanie w klastrze lub zmienić ustawienia konfiguracji z wartościami domyślnymi. Dzienniki akcji skryptu zapewniają wgląd w błędów występujących podczas instalacji klastra i zmiany ustawień konfiguracji, które mogą wpłynąć na wydajność klastra i dostępność.  Aby wyświetlić stan akcji skryptu, wybierz **ops** przycisk na interfejsu użytkownika narzędzia Ambari lub dostęp do stanu loguje domyślne konto magazynu. Dzienniki magazynu są dostępne pod adresem `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Zarządzanie plikami dziennika uzyskiwania informacji na temat wykonywania zadania klastra

Następnym krokiem jest przeglądanie plików dziennika wykonywania zadania dla różnych usług.  Usługi mogą obejmować bazy danych Apache HBase, Apache Spark i wielu innych. Klastra usługi Hadoop tworzy dużą liczbę pełnych dzienników, więc określania dzienniki, które są przydatne (i które nie są) może być czasochłonne.  Opis systemu rejestrowania jest ważne dla docelowej zarządzanie plikami dziennika.  Oto przykładowy plik dziennika.

![Przykładowy plik dziennika usługi HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Dostęp do plików dziennika usługi Hadoop

HDInsight przechowuje pliki dziennika, zarówno w systemie plików klastra i w magazynie Azure. Pliki dziennika w klastrze można sprawdzić, otwierając połączenia SSH z klastrem i przeglądanie systemu plików lub przy użyciu portalu usługi Hadoop YARN stanu na serwerze zdalnym węzła głównego. Można sprawdzić pliki dziennika w magazynie Azure przy użyciu dowolnego narzędzia, które mogą uzyskać dostęp i pobierania danych z usługi Azure storage. Przykłady są AZCopy, CloudXplorer i Eksploratora serwera na Visual Studio. Aby uzyskać dostęp do danych w magazynie obiektów blob platformy Azure, można użyć programu PowerShell i biblioteki klienta magazynu Azure lub z zestawów SDK .NET usługi Azure.

Pracy jako zadań uruchamia Hadoop *zadań prób* w różnych węzłach w klastrze. HDInsight można zainicjować prób rozważana zadań, kończące inne próby zadań, które nie są wykonywane najpierw. Powoduje to znaczne działanie, które jest rejestrowane kontrolera stderr i syslog dziennika pliki na bieżąco. Ponadto jednocześnie jest uruchomionych wiele zadań prób, ale plik dziennika mogą być wyświetlane tylko wyniki liniowo.

#### <a name="hdinsight-logs-written-to-azure-tables"></a>Dzienniki usługi HDInsight zapisywane w tabelach platformy Azure

Dzienniki zapisywane do tabel Azure wgląd w działania wykonywane z klastrem usługi HDInsight. Podczas tworzenia klastra usługi HDInsight opartej na systemie Linux sześciu tabele są tworzone automatycznie w magazynie tabel domyślne:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Dzienniki usługi HDInsight zapisywane do magazynu obiektów Blob platformy Azure

Klastry HDInsight są skonfigurowane do zapisywania dzienników zadania konta magazynu obiektów Blob platformy Azure dla dowolnego zadania, w której zostało przesłane za pomocą poleceń cmdlet programu Azure PowerShell lub interfejsów API przesyłania zadania .NET.  Po przesłaniu zadania za pośrednictwem protokołu SSH z klastrem wykonywania informacje są przechowywane w tabelach platformy Azure zgodnie z opisem w poprzedniej sekcji.

Oprócz podstawowe pliki dziennika wygenerowane przez HDInsight Instalowanie usług, takich jak YARN wygeneruje również pliki dziennika wykonywania zadania.  Liczba i typ plików dziennika jest zależna od zainstalowanymi usługami.  Wspólne usługi są bazy danych Apache HBase, Apache Spark i tak dalej.  Sprawdź plik wykonanie zadania dziennika dla każdej usługi zrozumieć ogólny rejestrowania plików dostępnych w klastrze.  Każda usługa ma własny unikatowy metody rejestrowania i lokalizacji do przechowywania plików dziennika.  Na przykład w poniższej sekcji omówiono szczegóły, aby uzyskać dostęp do najbardziej typowe pliki dziennika usługi (z YARN).

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight dzienniki generowane przez YARN

YARN łączy dzienników wszystkich kontenerów w węźle procesu roboczego i zapisuje te dzienniki jako jeden zagregowany plik dziennika na węzła procesu roboczego. Tego dziennika są przechowywane na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może korzystać z setkami lub tysiącami kontenerów, ale dzienniki dla wszystkich kontenerów, które są uruchamiane w węźle pojedynczego procesu roboczego zawsze są agregowane w jednym pliku. Istnieje tylko jeden dziennika na węzła procesu roboczego używany przez aplikację. Agregacja dziennika jest domyślnie włączone w klastrach usługi HDInsight w wersji 3.0 lub nowszym. Zagregowane Dzienniki znajdują się w domyślny magazyn dla klastra.

```
    /app-logs/<user>/logs/<applicationId>
```

Dzienniki zagregowane nie mogą bezpośrednio do odczytu, ponieważ są one zapisywane w formacie binarnym TFile indeksowane według kontenera. Umożliwia wyświetlanie dzienników jako zwykły tekst dla aplikacji lub kontenery odsetek w dzienników YARN ResourceManager lub narzędzi interfejsu wiersza polecenia.

#### <a name="yarn-cli-tools"></a>Narzędzia YARN interfejsu wiersza polecenia

Użyj narzędzi interfejsu wiersza polecenia YARN, możesz nawiązać klastra usługi HDInsight przy użyciu protokołu SSH. Określ `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, i `<worker-node-address>` informacje po uruchomieniu tych poleceń. Dzienniki można wyświetlić jako zwykły tekst z jednym z następujących poleceń:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

Interfejsie użytkownika YARN ResourceManager działa na głównym węzłem klastra i jest dostępny za pośrednictwem interfejsu użytkownika sieci web Ambari. Aby wyświetlić dzienniki YARN, wykonaj następujące kroki:

1. W przeglądarce sieci web, przejdź do `https://CLUSTERNAME.azurehdinsight.net`. Zamień na nazwę klastra usługi HDInsight CLUSTERNAME.
2. Z listy usług po lewej stronie wybierz YARN.
3. Z listy rozwijanej szybkie linki, wybierz jedną z głównymi węzłami klastra, a następnie wybierz **dzienniki ResourceManager**. Jest wyświetlana lista linków do dzienników YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Prognozy rozmiaru magazynu woluminu dziennika i kosztów

Po wykonaniu poprzednich kroków, masz zrozumienia typów i woluminów, plików dziennika, które produkuje z klastrem usługi HDInsight.

Następnie analizować ilość danych dziennika w lokalizacji przechowywania klucza dziennika w danym okresie czasu. Na przykład można analizować wielkość i wzrost ponad 30 – 60-90 liczbę dekad.  Zapisz te informacje w arkuszu kalkulacyjnym lub użyć innych narzędzi, takich jak Visual Studio, Eksploratora usługi Storage platformy Azure lub Power Query dla programu Excel. Aby uzyskać więcej informacji, zobacz [HDInsight analizowanie dzienników](hdinsight-debug-jobs.md).  

Masz teraz wystarczających informacji do utworzenia dziennika strategii zarządzania Dzienniki klucza.  Użyj arkusza kalkulacyjnego (lub wybranych narzędzi) prognozy zarówno wzrost rozmiaru dziennika i dziennika magazynu usługi Azure koszty idąc dalej.  Należy wziąć pod uwagę także wszelkie dziennika przechowywania dla zestawu dzienników, które są badanie.  Teraz można reforecast dziennika przyszłych kosztów magazynowania, po ustaleniu, które pliki dziennika można usunąć (jeśli istnieje) i dzienniki, które powinny być przechowywane i archiwizowane do tańszego magazynu Azure.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: Określanie dziennika archiwum zasadami i procesami

Po ustaleniu, które pliki dziennika można usunąć, można dostosować parametry rejestrowania na wiele usług Hadoop, aby automatycznie usuwać pliki dziennika, po upływie określonego czasu.

Dla niektórych plików dziennika można użyć pliku dziennika tańszych archiwizacji podejście. W przypadku dzienników usługi Azure Resource Manager działania Ci poznać platformę tej metody, przy użyciu portalu Azure.  Określ opcje archiwizacji dzienników ARM, wybierając **dziennik aktywności**"łącze w portalu Azure dla swojego wystąpienia usługi HDInsight.  W górnej części strony wyszukiwania dziennik aktywności, wybierz **wyeksportować** element menu, aby otworzyć **Eksport dziennika aktywności** okienka.  Wypełnij subskrypcji, region, czy eksportowany do konta magazynu i liczbę dni przechowywania dzienników. Na tym samym okienku można również określić, czy można wyeksportować do Centrum zdarzeń. 

![Eksportowanie plików dziennika](./media/hdinsight-log-management/archive.png)

Alternatywnie można skrypt Archiwizowanie dziennika przy użyciu programu PowerShell.  Na przykład skrypt programu PowerShell, zobacz [dzienniki usługi Automatyzacja Azure archiwum do magazynu obiektów Blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Uzyskiwanie dostępu do magazynu Azure metryk

Usługa Azure storage można skonfigurować do dostępu i operacje magazynu dziennika. Można użyć tych bardzo szczegółowe dzienniki, pojemności, monitorowania i planowania i inspekcji żądań do magazynu. Zarejestrowane informacje zawiera szczegóły opóźnienia, co umożliwia monitorowanie i dostrojenie wydajności rozwiązań.
Aby sprawdzić pliki dziennika wygenerowane dla magazynu Azure, która przechowuje dane dla klastra usługi HDInsight, można użyć zestawu .NET SDK dla platformy Hadoop.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Kontrolowanie rozmiaru i liczby kopii zapasowej indeksów dla stare pliki dziennika

Aby kontrolować rozmiar i przechowywane pliki dziennika, ustaw następujące właściwości `RollingFileAppender`:

* `maxFileSize` jest krytyczne rozmiar pliku, nad którym jest wycofywany pliku. Wartość domyślna to 10 MB.
* `maxBackupIndex` Określa liczbę plików kopii zapasowej należy utworzyć domyślną 1.

### <a name="other-log-management-techniques"></a>Inne metody zarządzania dziennika

Aby uniknąć braku miejsca na dysku, można użyć niektóre narzędzia systemu operacyjnego takie jak `logrotate` do zarządzania obsługi plików dziennika. Można skonfigurować `logrotate` do uruchomienia codziennie, kompresowanie plików i dziennika usuwania starych. Swoje podejście zależy od wymagań, takie jak czas przechowywania plików dziennika na węzłach lokalnego. 

Można również sprawdzić, czy rejestrowania debugowania jest włączone dla co najmniej jedna usługa, co znacznie zwiększa rozmiar dziennika danych wyjściowych. 

Aby zebrać dzienniki ze wszystkich węzłów w jednej centralnej lokalizacji, można utworzyć przepływu danych, takich jak wprowadzania wszystkich wpisów dziennika do Solr.

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie i rejestrowanie rozwiązań dla usługi HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Dziennik aplikacji YARN dostępu w usłudze HDInsight z systemem Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak kontrolować rozmiar plików dziennika dla poszczególnych składników usługi Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
