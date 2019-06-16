---
title: Zarządzanie dziennikami klastra usługi HDInsight — usługi Azure HDInsight
description: Określ typy, rozmiary i zasad przechowywania dla plików dziennika działań HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: hrasheed
ms.openlocfilehash: b42eb51b510423ffc0d15ee3a646bca3d4392f7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686851"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Zarządzanie dziennikami klastra usługi HDInsight

Klaster usługi HDInsight tworzy różnych plikach dziennika. Na przykład Apache Hadoop i powiązanych usług, takich jak Apache Spark, generować dzienniki wykonywania zadań szczegółowe. Zarządzanie plikami dziennika jest częścią utrzymanie dobrej kondycji klastra HDInsight. Można także wymogów prawnych dotyczących Archiwizowanie dziennika.  Ze względu na liczbę i rozmiar plików dziennika optymalizację magazynu dziennika i archiwizowanie pomaga za pomocą usługi cost management firmy.

Zarządzanie dziennikami klastra HDInsight obejmuje zachowywanie informacji o wszystkie aspekty środowiska klastra. Informacje te obejmują wszystkie skojarzone dzienniki usługi Azure, konfigurację klastra, informacje o wykonaniu zadania, wszystkie stany błędu i inne dane, stosownie do potrzeb.

Typowe kroki w Zarządzanie dziennikami HDInsight to:

* Krok 1: Określić zasady przechowywania dzienników
* Krok 2: Zarządzanie dziennikami konfiguracji wersjach usługi klastra
* Krok 3: Zarządzanie plikami dziennika wykonywania zadań klastra
* Krok 4: Prognozowanie rozmiaru magazynu na wolumin dziennika i kosztów
* Krok 5. Określenie dziennika archiwum zasady i procesy

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Określić zasady przechowywania dzienników

Pierwszym krokiem w tworzeniu strategii zarządzania dziennik klastra HDInsight jest do zbierania informacji o scenariuszach biznesowych, a wymagania magazynu historii wykonywania zadania.

### <a name="cluster-details"></a>Szczegóły klastra

Poniższe szczegóły klastra są przydatne w ułatwienia do zebrania informacji strategii zarządzania dziennika. Zebranie tych informacji ze wszystkich klastrów HDInsight, utworzonego w ramach określonego konta platformy Azure.

* Nazwa klastra
* Klaster regionu i strefy dostępności platformy Azure
* Stan klastra, łącznie ze szczegółami dotyczącymi Ostatnia zmiana stanu
* Typ i liczbę wystąpień HDInsight określony dla głównego, core oraz węzły zadania

Możesz uzyskać większość z tych informacji najwyższego poziomu za pomocą witryny Azure portal.  Alternatywnie, można użyć [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) Aby uzyskać informacje na temat klastry usługi HDInsight:

```azurecli
    az hdinsight list --resource-group <ResourceGroup>
    az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Można również użyć programu PowerShell Aby wyświetlić te informacje.  Aby uzyskać więcej informacji, zobacz [Apache Hadoop Zarządzanie klastrami w HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Zrozumienie obciążeń działających w klastrach

Należy zapoznać się z typami obciążenia uruchomionego na klastry usługi HDInsight do odpowiednich rejestrowania strategii dla każdego typu projektu.

* Czy obciążeń eksperymentalne (takich jak deweloperskie lub testowe) lub jakości produkcji?
* Jak często obciążeń o jakości produkcyjnej są zazwyczaj uruchamiane?
* Istnieją obciążeń intensywnie korzystających z zasobów i/lub długotrwałe?
* Należy używać dowolnego obciążenia złożonego zestawu usług Hadoop, dla których wiele typów dzienniki są tworzone?
* Czy obciążenia mają skojarzonych z przepisami wykonywania pochodzenie wymagania?

### <a name="example-log-retention-patterns-and-practices"></a>Przykładowy dziennik przechowywania wzorców i praktyk

* Należy wziąć pod uwagę, utrzymywanie pochodzenie danych śledzenia, dodając identyfikator do każdego wpisu dziennika lub przy użyciu innych technik. Dzięki temu można wywodzić oryginalnego źródła danych i operacji, a następnie wykonaj danych przy użyciu każdego etapu, aby zrozumieć jej spójności i ważności.

* Należy wziąć pod uwagę, jak można zbieranie dzienników z klastra lub z więcej niż jednego klastra i sortowanie ich do celów takich jak inspekcje, monitorowania, planowania i alertów. Można użyć niestandardowego rozwiązania dostępu i pobrania plików dziennika na bieżąco, łączenie i analizowanie ich w celu zapewnienia wyświetlania pulpitu nawigacyjnego. Można również dodać dodatkowe możliwości dla alertów w połączeniu z zabezpieczeń i wykrywanie awarii. Możesz tworzyć te narzędzia, za pomocą programu PowerShell, zestawów SDK HDInsight lub kod, który uzyskuje dostęp do klasycznego modelu wdrażania platformy.

* Należy rozważyć, czy rozwiązanie monitorowania lub usługa będzie przydatna korzyści. Microsoft System Center zapewnia [pakiet administracyjny HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Narzędzia innych producentów, takie jak Apache Chukwa i Ganglia umożliwia również zbieranie i scentralizować dzienniki. Wiele firm oferty usługi do monitorowania rozwiązania danych big data oparte na usłudze Hadoop, na przykład: Centerity Compuware APM, Sematext SPM i Zettaset programu Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-script-action-logs"></a>Krok 2: Zarządzanie wersjami usługi klastra oraz wyświetlanie dzienników akcji skryptu

Typowe klastra HDInsight korzysta z kilku usług i pakietów oprogramowania typu open source (np. Apache HBase, Apache Spark i tak dalej). Dla niektórych obciążeń, takich jak Bioinformatyka mogą wymagać przechowywania historii dziennika konfiguracji usługi oprócz dzienniki wykonywania zadań.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika systemu Ambari

Apache Ambari upraszcza zarządzanie, konfigurowanie oraz monitorowanie klastrów HDInsight, zapewniając internetowego interfejsu użytkownika i interfejs API REST. Ambari znajduje się w klastrach HDInsight opartych na systemie Linux. Wybierz **pulpit nawigacyjny klastra** okienku na stronie portalu HDInsight systemu Azure, aby otworzyć **pulpity nawigacyjne klastra** strona łącza.  Następnie wybierz pozycję **pulpit nawigacyjny klastra HDInsight** okienku, aby otworzyć interfejsu użytkownika Ambari.  Zostanie wyświetlony monit o poświadczenia logowania do klastra.

Aby otworzyć listę widoków usługi, wybierz **widoków Ambari** okienku na stronie portalu usługi Azure HDInsight.  Tej listy różni się w zależności od tego, które biblioteki po zainstalowaniu.  Na przykład może zostać wyświetlony Menedżer kolejki YARN, Hive View i widok aplikacji Tez.  Wybierz dowolny link usługi Aby wyświetlić konfigurację i informacje o usłudze.  Interfejsu użytkownika Ambari **stosu i wersji** strona zawiera informacje o konfiguracji usługi klastrowania i Historia wersji usługi. Aby przejść do tej części interfejsu użytkownika Ambari, wybierz **administratora** menu i następnie **stosy i wersje**.  Wybierz **wersji** kartę, aby wyświetlić informacje o wersji usługi.

![Stos i wersje](./media/hdinsight-log-management/stack-versions.png)

Za pomocą interfejsu użytkownika Ambari, możesz pobrać konfiguracji usługi (lub wszystkim) uruchomione na określonym hoście (lub węzła) w klastrze.  Wybierz **hosty** menu, a następnie łącze hosta zainteresowania. Na tym hoście stronie wybierz pozycję **akcje hosta** przycisku i następnie **Pobierz konfiguracje klienta**. 

![Konfiguracje klienta hosta](./media/hdinsight-log-management/client-configs.png)

### <a name="view-the-script-action-logs"></a>Wyświetl dzienniki akcji skryptu

HDInsight [akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) uruchamianie skryptów w klastrze, ręcznie lub czas określony. Na przykład akcji skryptu można zainstalować dodatkowe oprogramowanie w klastrze lub zmienić ustawienia konfiguracji z wartościami domyślnymi. Dzienniki akcji skryptu zapewniają wgląd w błędów, które wystąpiły podczas konfiguracji klastra, a także zmiany ustawień konfiguracji, które mogą wpłynąć na wydajność klastra i dostępność.  Aby wyświetlić stan akcji skryptu, wybierz pozycję **ops** przycisku na interfejsu użytkownika Ambari lub dostęp do stanu loguje się domyślne konto magazynu. Dzienniki magazynu są dostępne pod adresem `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`.

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Zarządzanie plikami dziennika wykonywania zadań klastra

Następnym krokiem jest przeglądanie plików dziennika wykonywania zadania dla różnych usług.  Usługi mogą obejmować bazy danych Apache HBase, Apache Spark i wiele innych. Klaster Hadoop generuje dużą liczbę pełne dzienniki, określająca, które dzienniki (i nie są) może być czasochłonne.  Opis systemu rejestrowania jest ważna dla docelowych zarządzanie plikami dziennika.  Oto przykładowy plik dziennika.

![Przykład pliku dziennika HDInsight](./media/hdinsight-troubleshoot-failed-cluster/logs.png)

### <a name="access-the-hadoop-log-files"></a>Dostęp do plików dziennika usługi Hadoop

HDInsight są przechowywane pliki dzienników, zarówno w systemie plików klastra, jak i w usłudze Azure storage. Pliki dziennika w klastrze można sprawdzić, otwierając [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) połączenia do klastra i przeglądania systemu plików lub przy użyciu portalu platformy Hadoop YARN stanu na serwerze zdalnym węzła głównego. Można sprawdzić pliki dziennika w usłudze Azure storage przy użyciu dowolnego narzędzia, które mogą uzyskać dostęp i pobierania danych z usługi Azure storage. Należą do nich [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)i w Eksploratorze serwera programu Visual Studio. Dostęp do danych w usłudze Azure blob storage, można użyć programu PowerShell i biblioteki klienta magazynu Azure lub zestawów Azure .NET SDK.

Hadoop uruchamia pracy zadania jako *zadań prób* w różnych węzłach w klastrze. HDInsight można zainicjować prób spekulacyjnego zadań, kończące inne próby zadania, które nie zakończą się najpierw. Spowoduje to wygenerowanie znaczące działania, który jest zalogowany do kontrolera, stderr i syslog dziennika pliki na bieżąco. Ponadto jednocześnie jest uruchomionych wiele prób zadania, ale plik dziennika mogą być wyświetlane tylko wyniki liniowo.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>HDInsight Dzienniki zapisane w usłudze Azure Blob storage

Klastry HDInsight są skonfigurowane na zapisywanie dzienników zadań na konto magazynu obiektów Blob platformy Azure w ramach zadania przesłane za pomocą poleceń cmdlet programu Azure PowerShell lub przesłania zadania interfejsów API platformy .NET.  Po przesłaniu zadania za pośrednictwem protokołu SSH z klastrem, wykonywania informacje są przechowywane w tabelach platformy Azure zgodnie z opisem w poprzedniej sekcji.

Oprócz podstawowe pliki dziennika generowane przez HDInsight należy zainstalować usługi takie jak YARN również generować pliki dziennika wykonywania zadania.  Liczba i typ plików dziennika, zależy od zainstalowanych usług.  Wspólne usługi są bazy danych Apache HBase, Apache Spark i tak dalej.  Zbadaj zadania dzienniki wykonywania dla poszczególnych usług i zrozumienie, że rejestrowanie ogólną plików w klastrze.  Każda usługa ma swój własny unikatowy metody rejestrowania i lokalizacji przechowywania plików dziennika.  Na przykład szczegóły dotyczące uzyskiwania dostępu do najbardziej typowe pliki dziennika usługi (z YARN) są omówione w poniższej sekcji.

### <a name="hdinsight-logs-generated-by-yarn"></a>HDInsight dzienników generowanych przez usługi YARN

YARN agreguje dzienników dla wszystkich kontenerów na węzeł procesu roboczego i przechowuje te dzienniki w postaci jednego pliku dziennika zagregowane na węzeł procesu roboczego. Ten dziennik jest przechowywany na domyślny system plików, po zakończeniu działania aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienników dla wszystkich kontenerów, które są uruchamiane w węźle pojedynczego procesu roboczego zawsze są agregowane do pojedynczego pliku. Istnieje tylko jeden dziennik na węzeł procesu roboczego używanych przez aplikację. Agregacja dziennik jest domyślnie włączona, klastrów HDInsight w wersji 3.0 lub nowszej. Zagregowane Dzienniki znajdują się w domyślny magazyn dla klastra.

```
    /app-logs/<user>/logs/<applicationId>
```

Zagregowane dzienniki nie są bezpośrednio do odczytu, ponieważ są one zapisywane w formacie binarnym TFile indeksowane przez kontener. Użyj dzienników YARN ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

#### <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia usługi YARN

Do korzystania z narzędzi interfejsu wiersza polecenia usługi YARN, musi najpierw połączyć się z klastrem HDInsight przy użyciu protokołu SSH. Określ `<applicationId>`, `<user-who-started-the-application>`, `<containerId>`, i `<worker-node-address>` informacje po uruchomieniu tych poleceń. Można przeglądać dzienniki w postaci zwykłego tekstu przy użyciu jednego z następujących poleceń:

```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Interfejsie użytkownika YARN ResourceManager

Interfejsie użytkownika YARN ResourceManager działa na głównym węzłem klastra i jest dostępny za pośrednictwem interfejsu użytkownika sieci web Ambari. Aby wyświetlić dzienniki usługi YARN, wykonaj następujące kroki:

1. W przeglądarce internetowej przejdź do `https://CLUSTERNAME.azurehdinsight.net`. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight.
2. Z listy usług po lewej stronie wybierz usługi YARN.
3. Z listy rozwijanej z szybkich łączy wybierz jedną z głównymi węzłami klastra, a następnie wybierz pozycję **dzienniki ResourceManager**. Zostanie wyświetlona lista linków do dzienniki usługi YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Prognozowanie rozmiaru magazynu na wolumin dziennika i kosztów

Po wykonaniu poprzednich kroków, masz zrozumienia typów i woluminów, plików dziennika, które produkuje klastry usługi HDInsight.

Następnie analizować dane dziennika w lokalizacji miejsc przechowywania dziennika klucza w okresie czasu. Na przykład można analizować wielkość i wzrost ponad 30 – 60-90 dniowych okresów.  Zapisz te informacje w arkuszu kalkulacyjnym lub użyć innych narzędzi, takich jak Visual Studio, Eksplorator usługi Azure Storage lub dodatku Power Query dla programu Excel. Aby uzyskać więcej informacji, zobacz [HDInsight analizowanie dzienników](hdinsight-debug-jobs.md).  

Masz teraz wystarczających informacji do utworzenia strategię zarządzania dziennika dla klucza dzienników.  Użyj arkusza kalkulacyjnego (lub wybranym narzędziu) dziennika koszty usługi Azure storage idąc dalej i prognozowanie zarówno wzrost rozmiaru dziennika.  Należy wziąć pod uwagę także wszelkie dziennik przechowywania dla zestawu dzienników, które są badanie.  Teraz można reforecast przyszłych log koszty magazynowania, po ustaleniu, które pliki dziennika można usunąć (jeśli istnieje) i które dzienniki powinny być przechowywane i archiwizowanie tańszego magazynu platformy Azure.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5. Określenie dziennika archiwum zasady i procesy

Po ustaleniu, które pliki dziennika można usunąć, można dostosować parametry rejestrowania wielu usług Hadoop, aby automatycznie usuwać pliki dziennika, po upływie określonego czasu.

Dla niektórych plików dziennika można użyć podejście archiwizacji pliku dziennika niższej cenie. W przypadku dzienników aktywności usługi Azure Resource Manager możesz eksplorować tej metody za pomocą witryny Azure portal.  Konfigurowanie archiwizowanie dzienników ARM, wybierając **dziennika aktywności**"link w witrynie Azure portal dla wystąpienia usługi HDInsight.  Górnej części strony wyszukiwanie w dzienniku aktywności, wybierz **wyeksportować** element menu, aby otworzyć **Eksportuj Dziennik aktywności** okienka.  Wypełnij subskrypcji, region, czy eksportowany do konta magazynu i ile dni przechowywania dzienników. W tym samym okienku można również określić, czy można wyeksportować do Centrum zdarzeń. 

![Eksportuj pliki dziennika](./media/hdinsight-log-management/archive.png)

Alternatywnie można tworzyć skrypty dla dziennika archiwizowanie za pomocą programu PowerShell.  Aby uzyskać przykładowy skrypt programu PowerShell, zobacz [archiwum usługi Azure Automation dzienników w usłudze Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Dostęp do metryk usługi Azure storage

Usługa Azure storage można skonfigurować do dziennika operacji magazynu i dostępem. Bardzo szczegółowe dzienniki można użyć do pojemności, monitorowania i planowania oraz do inspekcji żądań w usłudze storage. Rejestrowane informacje obejmują opóźnienia uzyskać szczegółowe informacje, dzięki czemu można do monitorowania i dostrajania wydajności rozwiązań.
Aby sprawdzić pliki dziennika wygenerowany dla usługi Azure storage, która przechowuje dane dla klastra usługi HDInsight, można użyć zestawu .NET SDK dla platformy Hadoop.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Kontrolowanie rozmiaru i liczby kopii zapasowych indeksów dla stare pliki dziennika

Do kontrolowania rozmiaru i liczby plików dziennika, przechowywane, ustaw następujące właściwości `RollingFileAppender`:

* `maxFileSize` jest to krytyczne rozmiar pliku, nad którym plik jest wycofywany. Wartość domyślna to 10 MB.
* `maxBackupIndex` Określa liczbę plików kopii zapasowej, które ma zostać utworzony, wartość domyślna: 1.

### <a name="other-log-management-techniques"></a>Inne metody zarządzania dziennika

Aby uniknąć korzystającym z miejsca na dysku, można użyć niektórych narzędzi systemu operacyjnego takie jak [logrotate](https://linux.die.net/man/8/logrotate) Zarządzanie obsługi plików dziennika. Można skonfigurować `logrotate` do uruchomienia codziennie, kompresowanie plików i dziennika usuwania starych. Swoje podejście zależy od wymagań, takich jak jak długo można przechowywać plików dziennika na węzłach lokalnym.  

Można również sprawdzić, czy rejestrowanie debugowania jest włączone dla co najmniej jedna usługa, która znacznie zwiększa rozmiar dziennika danych wyjściowych.  

Gromadzić dzienniki ze wszystkich węzłów w jednej centralnej lokalizacji, możesz utworzyć przepływ danych, pozyskiwanie wszystkich wpisów dziennika do platformy Solr.

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie i rejestrowanie praktyki w zakresie HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Dzienniki aplikacji Apache Hadoop YARN dostępu w HDInsight opartych na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak kontrolować rozmiar plików dziennika dla różnych składników usługi Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
