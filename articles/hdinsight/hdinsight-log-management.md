---
title: Zarządzanie dziennikami dla klastra HDInsight — Usługa Azure HDInsight
description: Określ typy, rozmiary i zasady przechowywania plików dziennika aktywności HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 8c3cbf4c18b32a94abfe95e77be768020b44fda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272307"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Zarządzanie dziennikami klastra usługi HDInsight

Klaster HDInsight tworzy wiele plików dziennika. Na przykład Apache Hadoop i powiązanych usług, takich jak Apache Spark, tworzenie szczegółowych dzienników wykonywania zadań. Zarządzanie plikami dziennika jest częścią utrzymania klastra HDInsight w dobrej kondycji. Mogą również istnieć wymagania prawne dotyczące archiwizacji dzienników.  Ze względu na liczbę i rozmiar plików dziennika optymalizacja przechowywania dzienników i archiwizacji pomaga w zarządzaniu kosztami usług.

Zarządzanie dziennikami klastra HDInsight obejmuje zachowywanie informacji o wszystkich aspektach środowiska klastra. Te informacje obejmują wszystkie skojarzone dzienniki usługi Azure Service, konfigurację klastra, informacje o wykonaniu zadania, wszelkie stany błędów i inne dane w razie potrzeby.

Typowe kroki w zarządzaniu dziennikami HDInsight to:

* Krok 1: Określanie zasad przechowywania dzienników
* Krok 2: Zarządzanie dziennikami konfiguracji wersji usługi klastrowania
* Krok 3: Zarządzanie plikami dziennika wykonywania zadań klastra
* Krok 4: Prognozowanie wielkości i kosztów magazynu dziennika
* Krok 5: Określanie zasad i procesów archiwum dzienników

## <a name="step-1-determine-log-retention-policies"></a>Krok 1: Określanie zasad przechowywania dzienników

Pierwszym krokiem w tworzeniu strategii zarządzania dziennikiem klastra HDInsight jest zebranie informacji o scenariuszach biznesowych i wymaganiach dotyczących przechowywania historii wykonywania zadań.

### <a name="cluster-details"></a>Szczegóły klastra

Następujące szczegóły klastra są przydatne w pomocy w zbieraniu informacji w strategii zarządzania dziennikiem. Zbierz te informacje ze wszystkich klastrów usługi HDInsight utworzonych na określonym koncie platformy Azure.

* Nazwa klastra
* Region klastra i strefa dostępności platformy Azure
* Stan klastra, w tym szczegóły ostatniej zmiany stanu
* Typ i liczba wystąpień hdinsight określonych dla węzłów głównych, rdzeniowych i zadań

Większość tych informacji najwyższego poziomu można uzyskać za pomocą witryny Azure portal.  Alternatywnie można użyć [narzędzia Azure CLI,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) aby uzyskać informacje o klastrze hdinsight:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Można również użyć programu PowerShell, aby wyświetlić te informacje.  Aby uzyskać więcej informacji, zobacz [Apache Manage Hadoop klastrów w programie HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Opis obciążeń uruchomionych w klastrach

Ważne jest, aby zrozumieć typy obciążeń uruchomionych w klastrze HDInsight, aby zaprojektować odpowiednie strategie rejestrowania dla każdego typu.

* Czy obciążenia eksperymentalne (takie jak program rozwoju lub test) lub jakość produkcji?
* Jak często normalnie uruchamiają się obciążenia o jakości produkcji?
* Czy którykolwiek z obciążeń jest zasobochłonne i/lub długotrwałe?
* Czy którykolwiek z obciążeń korzysta ze złożonego zestawu usług Hadoop, dla których produkowane są wiele typów dzienników?
* Czy którykolwiek z obciążeń ma skojarzone wymagania dotyczące linii wykonania przepisów?

### <a name="example-log-retention-patterns-and-practices"></a>Przykładowe wzorce i praktyki przechowywania dzienników

* Należy rozważyć utrzymanie śledzenia linii danych, dodając identyfikator do każdego wpisu dziennika lub za pomocą innych technik. Dzięki temu można prześledzić oryginalne źródło danych i operacji i śledzić dane za pośrednictwem każdego etapu, aby zrozumieć jego spójność i ważność.

* Należy wziąć pod uwagę, jak można zbierać dzienniki z klastra lub z więcej niż jednego klastra i sortować je do celów, takich jak inspekcja, monitorowanie, planowanie i alerty. Można użyć niestandardowego rozwiązania, aby regularnie uzyskiwać dostęp do plików dziennika i pobierać je, a także łączyć je i analizować, aby zapewnić wyświetlanie pulpitu nawigacyjnego. Można również dodać dodatkowe możliwości alertów dotyczących zabezpieczeń lub wykrywania awarii. Można utworzyć te narzędzia przy użyciu programu PowerShell, HDInsight SDKs lub kodu, który uzyskuje dostęp do klasycznego modelu wdrażania platformy Azure.

* Należy rozważyć, czy rozwiązanie do monitorowania lub usługa byłaby użyteczną korzyścią. Program Microsoft System Center udostępnia [pakiet administracyjny HDInsight](https://www.microsoft.com/download/details.aspx?id=42521). Możesz również użyć narzędzi innych firm, takich jak Apache Chukwa i Ganglia, aby zbierać i centralizować dzienniki. Wiele firm oferuje usługi monitorowania rozwiązań opartych na hadoopie w zakresie dużych zbiorów danych, na przykład: Centerity, Compuware APM, Sematext SPM i Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Krok 2: Zarządzanie wersjami usługi klastrowania i dziennikami wyświetlania

Typowy klaster HDInsight używa kilku usług i pakietów oprogramowania typu open source (takich jak Apache HBase, Apache Spark i tak dalej). W przypadku niektórych obciążeń, takich jak bioinformatyka, może być wymagane zachowanie historii dziennika konfiguracji usługi oprócz dzienników wykonywania zadań.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika Ambari

Apache Ambari upraszcza zarządzanie, konfigurację i monitorowanie klastra HDInsight, udostępniając internetowy interfejs użytkownika i interfejs API REST. Ambari jest dołączony do klastrów HDInsight opartych na systemie Linux. Wybierz **okienko pulpitu nawigacyjnego klastra** na stronie USŁUGI HDInsight usługi Azure portal, aby otworzyć stronę łącza **Pulpity nawigacyjne klastra.**  Następnie wybierz okienko **pulpitu nawigacyjnego klastra HDInsight,** aby otworzyć interfejs użytkownika Ambari.  Zostanie wyświetlony monit o podanie danych logowania do klastra.

Aby otworzyć listę widoków usługi, wybierz okienko **Widoki Ambari** na stronie portalu Azure dla usługi HDInsight.  Ta lista różni się w zależności od zainstalowanych bibliotek.  Na przykład może zostać wyświetlony Menedżer kolejek YARN, Widok gałęzi i Widok Tez.  Wybierz dowolne łącze usługi, aby wyświetlić informacje o konfiguracji i usłudze.  Ambari **UI Stack i wersja** strony zawiera informacje o konfiguracji usług klastrowania i historii wersji usługi. Aby przejść do tej sekcji interfejsu użytkownika ambari, wybierz menu **Administrator,** a następnie **stosy i wersje**.  Wybierz kartę **Wersje,** aby wyświetlić informacje o wersji usługi.

![Apache Ambari admin Stos i wersje](./media/hdinsight-log-management/ambari-stack-versions.png)

Za pomocą interfejsu użytkownika Ambari można pobrać konfigurację dla dowolnej (lub wszystkich) usług uruchomionych na określonym hoście (lub węźle) w klastrze.  Wybierz menu **Hosts,** a następnie łącze dla gospodarza zainteresowania. Na stronie tego hosta wybierz przycisk **Akcje hosta,** a następnie **pobierz konfiguracje klientów**.

![Apache Ambari pobierz konfisje klienta hosta](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Wyświetlanie dzienników akcji skryptu

[Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md) HDInsight uruchamiają skrypty w klastrze ręcznie lub po określeniu. Na przykład akcje skryptu mogą służyć do instalowania dodatkowego oprogramowania w klastrze lub do zmiany ustawień konfiguracji z wartości domyślnych. Dzienniki akcji skryptu mogą zapewniać wgląd w błędy, które wystąpiły podczas instalacji klastra, a także zmiany ustawień konfiguracji, które mogą mieć wpływ na wydajność i dostępność klastra.  Aby wyświetlić stan akcji skryptu, wybierz przycisk **ops** w interfejsie użytkownika Ambari lub uzyskaj dostęp do dzienników stanu na domyślnym koncie magazynu. Dzienniki pamięci masowej `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE`są dostępne pod adresem .

### <a name="view-ambari-alerts-status-logs"></a>Wyświetlanie dzienników stanu alertów Ambari

Apache Ambari zapisuje zmiany `ambari-alerts.log`stanu alertu na . Pełna ścieżka `/var/log/ambari-server/ambari-alerts.log`jest . Aby włączyć debugowanie dla dziennika, `/etc/ambari-server/conf/log4j.properties.` zmień właściwość `# Log alert state changes` w Zmień, a następnie wpisuj w obszarze:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3: Zarządzanie plikami dziennika wykonywania zadań klastra

Następnym krokiem jest przeglądanie plików dziennika wykonywania zadań dla różnych usług.  Usługi mogą obejmować Apache HBase, Apache Spark i wiele innych. Klaster Hadoop tworzy dużą liczbę pełnych dzienników, więc określenie, które dzienniki są przydatne (a które nie) może być czasochłonne.  Zrozumienie systemu rejestrowania jest ważne dla ukierunkowanego zarządzania plikami dziennika.  Poniższy obraz jest przykładowym plikiem dziennika.

![Przykładowe dane wyjściowe pliku dziennika HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Dostęp do plików dziennika Hadoop

Usługa HDInsight przechowuje swoje pliki dziennika zarówno w systemie plików klastra, jak i w usłudze Azure Storage. Można sprawdzić pliki dziennika w klastrze, otwierając połączenie [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) z klastrem i przeglądając system plików lub korzystając z portalu Stanu YARN hadoop na serwerze zdalnego węzła głowy. Można sprawdzić pliki dziennika w usłudze Azure Storage przy użyciu dowolnego narzędzia, które można uzyskać dostęp i pobrać dane z usługi Azure Storage. Przykładami są [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)i Visual Studio Server Explorer. Można również użyć programu PowerShell i bibliotek klienta usługi Azure Storage lub zestawów SDK platformy Azure .NET, aby uzyskać dostęp do danych w magazynie obiektów blob platformy Azure.

Hadoop uruchamia pracę zadań jako *próby zadania* na różnych węzłach w klastrze. HdInsight może inicjować próby zadania spekulacyjne, kończąc wszystkie inne próby zadania, które nie zostały zakończone wcześniej. Generuje to znaczną aktywność, która jest rejestrowana w plikach dziennika kontrolera, stderr i syslog on-the-fly. Ponadto wiele prób zadania są uruchomione jednocześnie, ale plik dziennika można wyświetlić tylko liniowo.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Dzienniki usługi HDInsight zapisane w magazynie obiektów blob platformy Azure

Klastry usługi HDInsight są skonfigurowane do zapisywania dzienników zadań na koncie magazynu obiektów Blob platformy Azure dla dowolnego zadania przesłanego przy użyciu poleceń cmdlet programu Azure PowerShell lub interfejsów API przesyłania zadań .NET.  Jeśli prześlesz zadania za pośrednictwem SSH do klastra, informacje rejestrowania wykonania są przechowywane w tabelach platformy Azure, jak omówiono w poprzedniej sekcji.

Oprócz podstawowych plików dziennika generowanych przez HDInsight, zainstalowane usługi, takie jak YARN również generować pliki dziennika wykonywania zadań.  Liczba i typ plików dziennika zależy od zainstalowanych usług.  Typowe usługi to Apache HBase, Apache Spark i tak dalej.  Zbadaj pliki wykonywania dziennika zadań dla każdej usługi, aby zrozumieć ogólne pliki rejestrowania dostępne w klastrze.  Każda usługa ma swoje unikalne metody rejestrowania i lokalizacje do przechowywania plików dziennika.  Na przykład szczegóły dotyczące uzyskiwania dostępu do najpopularniejszych plików dziennika usługi (z YARN) zostały omówione w poniższej sekcji.

### <a name="hdinsight-logs-generated-by-yarn"></a>Dzienniki HDInsight generowane przez YARN

YARN agreguje dzienniki we wszystkich kontenerach w węźle procesu roboczego i przechowuje te dzienniki jako jeden zagregowany plik dziennika na węzeł procesu roboczego. Ten dziennik jest przechowywany w domyślnym systemie plików po zakończeniu aplikacji. Aplikacja może używać setek lub tysięcy kontenerów, ale dzienniki dla wszystkich kontenerów, które są uruchamiane w węźle procesu roboczego są zawsze agregowane do pojedynczego pliku. Istnieje tylko jeden dziennik na węzeł procesu roboczego używany przez aplikację. Agregacja dzienników jest domyślnie włączona w klastrach HDInsight w wersji 3.0 lub wyższej. Zagregowane dzienniki znajdują się w domyślnym magazynie dla klastra.

```
/app-logs/<user>/logs/<applicationId>
```

Zagregowane dzienniki nie są bezpośrednio czytelne, ponieważ są one zapisywane w formacie binarnym TFile indeksowane przez kontener. Użyj dzienników Menedżera zasobów YARN lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

#### <a name="yarn-cli-tools"></a>Narzędzia CLI przędzy

Aby korzystać z narzędzi interfejsu wiersza polecenia YARN, należy najpierw połączyć się z klastrem HDInsight przy użyciu funkcji SSH. Określ `<applicationId>` `<user-who-started-the-application>`, `<containerId>`, `<worker-node-address>` i informacje podczas uruchamiania tych poleceń. Dzienniki można wyświetlać jako zwykły tekst za pomocą jednego z następujących poleceń:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Interfejs użytkownika menedżera zasobów YARN

Interfejs użytkownika YARN ResourceManager działa w węźle głównym klastra i jest dostępny za pośrednictwem interfejsu użytkownika sieci Web Ambari. Aby wyświetlić dzienniki YARN, należy wykonać następujące czynności:

1. W przeglądarce internetowej przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight:
2. Z listy usług po lewej stronie wybierz pozycję YARN.
3. Z listy rozwijanej Szybkie łącza wybierz jeden z węzłów głównego klastra, a następnie wybierz pozycję **Dzienniki ResourceManager**. Zostanie wyświetlona lista linków do dzienników YARN.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: Prognozowanie wielkości i kosztów magazynu dziennika

Po wykonaniu poprzednich kroków użytkownik ma do zrozumienia typy i woluminy plików dziennika, które są wytwarzane przez klaster HDInsight.

Następnie przeanalizuj ilość danych dziennika w lokalizacjach magazynu dziennika kluczy w danym okresie czasu. Na przykład można analizować objętość i wzrost w okresach 30-60-90 dni.  Nagrywaj te informacje w arkuszu kalkulacyjnym lub korzystaj z innych narzędzi, takich jak Visual Studio, Eksplorator usługi Azure Storage lub Power Query dla programu Excel. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników HDInsight](hdinsight-debug-jobs.md).  

Masz teraz wystarczająco dużo informacji, aby utworzyć strategię zarządzania dziennikami dla dzienników kluczy.  Użyj arkusza kalkulacyjnego (lub wybranego narzędzia), aby prognozować zarówno wzrost rozmiaru dziennika, jak i koszty usługi platformy Azure w dzienniku w przyszłości.  Należy również wziąć pod uwagę wszelkie wymagania dotyczące przechowywania dziennika dla zestawu dzienników, które są sprawdzane.  Teraz można ponownie audywać przyszłych kosztów magazynu dziennika, po określeniu, które pliki dziennika mogą być usuwane (jeśli istnieją) i które dzienniki powinny być przechowywane i archiwizowane do tańszych usług Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5: Określanie zasad i procesów archiwum dzienników

Po określeniu, które pliki dziennika mogą zostać usunięte, można dostosować parametry rejestrowania w wielu usługach Hadoop, aby automatycznie usuwać pliki dziennika po określonym czasie.

W przypadku niektórych plików dziennika można użyć metody archiwizacji plików dziennika o niższej cenie. W przypadku dzienników aktywności usługi Azure Resource Manager można eksplorować tę podejście przy użyciu witryny Azure portal.  Skonfiguruj archiwizację dzienników Menedżera zasobów, wybierając łącze **Dziennik aktywności** w witrynie Azure portal dla wystąpienia usługi HDInsight.  U góry strony wyszukiwania dziennik aktywności wybierz pozycję menu **Eksportuj,** aby otworzyć okienko **dziennika aktywności Eksportuj.**  Wypełnij subskrypcję, region, czy eksportować do konta magazynu i ile dni zachować dzienniki. W tym samym okienku można również wskazać, czy mają być eksportowane do centrum zdarzeń.

![Podgląd dziennika aktywności eksportu portalu Azure](./media/hdinsight-log-management/hdi-export-log-files.png)

Alternatywnie można archiwizować dzienniki skryptów za pomocą programu PowerShell.  Aby zapoznać się z przykładowym skryptem programu PowerShell, zobacz [Archiwizuj dzienniki usługi Azure Automation w usłudze Azure Blob Storage.](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8)

### <a name="accessing-azure-storage-metrics"></a>Uzyskiwanie dostępu do metryk usługi Azure Storage

Usługa Azure Storage można skonfigurować do rejestrowania operacji magazynu i dostępu. Można użyć tych bardzo szczegółowych dzienników do monitorowania pojemności i planowania oraz do inspekcji żądań do magazynu. Zarejestrowane informacje zawierają szczegóły opóźnienia, co umożliwia monitorowanie i dostosowywanie wydajności rozwiązań.
Za pomocą zestawu .NET SDK for Hadoop można sprawdzić pliki dziennika wygenerowane dla usługi Azure Storage, która przechowuje dane dla klastra HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Sterowanie rozmiarem i liczbą indeksów kopii zapasowych dla starych plików dziennika

Aby kontrolować rozmiar i liczbę zachowanych plików dziennika, ustaw `RollingFileAppender`następujące właściwości:

* `maxFileSize`jest krytycznym rozmiarem pliku, powyżej którego plik jest przewracany. Wartość domyślna to 10 MB.
* `maxBackupIndex`określa liczbę plików kopii zapasowej, które mają zostać utworzone, domyślnie 1.

### <a name="other-log-management-techniques"></a>Inne techniki zarządzania dziennikami

Aby uniknąć wyczerpania miejsca na dysku, można użyć niektórych narzędzi systemu operacyjnego, takich jak [logrotate](https://linux.die.net/man/8/logrotate) do zarządzania obsługą plików dziennika. Można skonfigurować `logrotate` do codziennego uruchamiania, kompresowania plików dziennika i usuwania starych. Podejście zależy od wymagań, takich jak jak długo zachować pliki dziennika w węzłach lokalnych.  

Można również sprawdzić, czy rejestrowanie DEBUG jest włączone dla jednej lub więcej usług, co znacznie zwiększa rozmiar dziennika wyjściowego.  

Aby zebrać dzienniki ze wszystkich węzłów do jednej centralnej lokalizacji, można utworzyć przepływ danych, takich jak pozyskiwanie wszystkich wpisów dziennika do Solr.

## <a name="next-steps"></a>Następne kroki

* [Praktyka monitorowania i rejestrowania dla HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Dostęp do dzienników aplikacji Apache Hadoop YARN w systemie HDInsight opartym na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak kontrolować rozmiar plików dziennika dla różnych składników Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
