---
title: 'Debugowanie Apache Hadoop: Wyświetlanie dzienników i Interpretowanie komunikatów o błędach — Azure HDInsight'
description: Informacje o komunikatach o błędach, które mogą zostać wyświetlone podczas administrowania usługą HDInsight przy użyciu programu PowerShell, oraz czynności, które można wykonać w celu odzyskania.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 847b36c2aab761383a4a25bd4da5c626c4744ce1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935477"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Analizowanie dzienników Apache Hadoop w usłudze Azure HDInsight

Każdy klaster Apache Hadoop w usłudze Azure HDInsight ma konto usługi Azure Storage używane jako domyślny system plików. Konto magazynu jest określane jako domyślne konto magazynu. Klaster używa magazynu tabel platformy Azure i magazynu obiektów BLOB na domyślnym koncie magazynu do przechowywania dzienników.  Aby sprawdzić domyślne konto magazynu dla klastra, zobacz [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Dzienniki są przechowywane na koncie magazynu nawet po usunięciu klastra.

## <a name="logs-written-to-azure-tables"></a>Dzienniki zapisane w tabelach platformy Azure

Dzienniki zapisane w tabelach platformy Azure zapewniają jeden poziom wglądu w informacje o tym, co dzieje się z klastrem usługi HDInsight.

Podczas tworzenia klastra usługi HDInsight są automatycznie tworzone sześć tabel dla klastrów opartych na systemie Linux w domyślnym magazynie tabel:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Nazwy plików tabeli to **u\<clustername > DDMonYYYYatHHMMSSsss\<tablename >** .

Te tabele zawierają następujące pola:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Wiadomość
* N
* PreciseTimeStamp
* Rola
* RowIndex
* Dzierżawa
* ZNACZNIK czasu
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Narzędzia do uzyskiwania dostępu do dzienników
Dostępnych jest wiele narzędzi do uzyskiwania dostępu do danych w tych tabelach:

* Visual Studio
* Eksplorator magazynu Azure
* Power Query dla programu Excel

#### <a name="use-power-query-for-excel"></a>Użyj Power Query dla programu Excel
Power Query można zainstalować z [Microsoft Power Query dla programu Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Wymagania systemowe można znaleźć na stronie pobierania.

**Użyj Power Query, aby otworzyć i przeanalizować Dziennik usługi**

1. Otwórz **program Microsoft Excel**.
2. W menu **Power Query** kliknij pozycję **z platformy Azure**, a następnie kliknij pozycję **z Microsoft Azure Storage Table**.
   
    ![HDInsight Hadoop Excel PowerQuery otwórz magazyn tabel Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Wprowadź nazwę konta magazynu (krótką) lub nazwę FQDN.
4. Wprowadź klucz konta magazynu. Zostanie wyświetlona lista tabel:
   
    ![Dzienniki usługi HDInsight Hadoop przechowywane w usłudze Azure Table Storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kliknij prawym przyciskiem myszy tabelę hadoopservicelog w okienku **Nawigator** i wybierz pozycję **Edytuj**. Zobaczysz cztery kolumny. Opcjonalnie Usuń **klucz partycji**, **klucz wiersza**i kolumny **sygnatur czasowych** , zaznaczając je, a następnie klikając pozycję **Usuń kolumny** na Wstążce.
6. Kliknij ikonę rozwijania w kolumnie zawartość, aby wybrać kolumny, które chcesz zaimportować do arkusza kalkulacyjnego programu Excel. Dla tej demonstracji wybieram element TraceLevel i ComponentName: może to dać mi podstawowe informacje o składnikach, w których wystąpiły problemy.
   
    ![Dzienniki usługi HDInsight Hadoop Wybieranie kolumn Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "Dzienniki usługi HDInsight Hadoop Wybieranie kolumn Excel")
7. Kliknij przycisk **OK** , aby zaimportować dane.
8. Wybierz kolumny **TraceLevel**, role i **ComponentName** , a następnie kliknij pozycję **Grupuj według** kontrolki na Wstążce.
9. Kliknij przycisk **OK** w oknie dialogowym Grupuj według
10. Kliknij przycisk * * Zastosuj & Zamknij * *.

Teraz można użyć programu Excel do filtrowania i sortowania według potrzeb. Możesz chcieć uwzględnić inne kolumny (takie jak komunikat), aby przejść do szczegółów problemów występujących podczas ich występowania, ale wybierając i grupując kolumny opisane powyżej, zawiera znośnego obraz tego, co dzieje się z usługami Hadoop. To samo pomysłu można zastosować do tabel SetupLog i hadoopinstalllog.

#### <a name="use-visual-studio"></a>Korzystanie z programu Visual Studio
**Korzystanie z programu Visual Studio**

1. Otwórz program Visual Studio.
2. W menu **Widok** kliknij pozycję **Eksplorator chmury**. Lub po prostu kliknij **kombinację klawiszy CTRL +\, Ctrl + X**.
3. W programie **Cloud Explorer**wybierz pozycję **typy zasobów**.  Druga dostępna opcja to **grupy zasobów**.
4. Rozwiń węzeł **konta magazynu**, domyślne konto magazynu dla klastra, a następnie wybierz pozycję **tabele**.
5. Kliknij dwukrotnie pozycję **hadoopservicelog**.
6. Dodaj filtr. Na przykład:
   
        TraceLevel eq 'ERROR'
   
    ![Dzienniki usługi HDInsight Hadoop wybierają kolumny a](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "Dzienniki usługi HDInsight Hadoop wybierają kolumny a")
   
    Aby uzyskać więcej informacji na temat konstruowania filtrów, zobacz [konstruowanie ciągów filtru dla projektanta tabel](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Dzienniki zapisane na platformie Azure Blob Storage

Dzienniki zapisane w tabelach platformy Azure zapewniają jeden poziom wglądu w informacje o tym, co dzieje się z klastrem usługi HDInsight. Jednak te tabele nie udostępniają dzienników na poziomie zadania, które mogą być pomocne w dalszych krokach w przypadku wystąpienia problemów. Aby zapewnić ten kolejny poziom szczegółowości, klastry usługi HDInsight są skonfigurowane do zapisywania dzienników zadań na koncie usługi Blob Storage dla każdego zadania przesyłanego za poorednictwem Templeton. Praktycznie oznacza to, że zadania są przesyłane za pomocą poleceń cmdlet Microsoft Azure PowerShell lub interfejsów API przesyłania zadań platformy .NET, a nie do klastra przesłanych za pośrednictwem protokołu RDP/wiersza polecenia. 

Aby wyświetlić dzienniki, zobacz [dostęp Apache Hadoop dzienników aplikacji przędzy w usłudze HDInsight opartej na systemie Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

## <a name="view-cluster-health-and-job-logs"></a>Wyświetlanie kondycji klastra i dzienników zadań

### <a name="access-the-ambari-ui"></a>Dostęp do interfejsu użytkownika Ambari

W Azure Portal kliknij nazwę klastra usługi HDInsight, aby otworzyć okienko klaster. W okienku klaster kliknij pozycję **pulpit nawigacyjny**.

![Pulpit nawigacyjny uruchamiania klastra usługi HDInsight](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Dostęp do interfejsu użytkownika przędzy
W Azure Portal kliknij nazwę klastra usługi HDInsight, aby otworzyć okienko klaster. W okienku klaster kliknij pozycję **pulpit nawigacyjny**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. W Ambari wybierz pozycję **przędza** z listy usług po lewej stronie. Na wyświetlonej stronie wybierz pozycję **szybkie linki**, a następnie wybierz pozycję aktywny węzeł główny i Menedżer zasobów interfejs użytkownika.

Za pomocą interfejsu użytkownika PRZĘDZy można wykonać następujące czynności:

* **Pobieranie stanu klastra**. W okienku po lewej stronie rozwiń węzeł **klaster**, a następnie kliknij pozycję **informacje**. To obecne Szczegóły stanu klastra, takie jak całkowita przypisana pamięć, używane rdzenie, stan Menedżera zasobów klastra, wersja klastra i tak dalej.
  
    ![Przędza pulpitu nawigacyjnego klastra uruchamiania usługi HDInsight](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "Przędza pulpitu nawigacyjnego klastra uruchamiania usługi HDInsight")
* **Pobierz stan węzła**. W okienku po lewej stronie rozwiń węzeł **klaster**, a następnie kliknij pozycję **węzły**. Spowoduje to wyświetlenie listy wszystkich węzłów w klastrze, adres HTTP każdego węzła, zasoby przydzielono do każdego węzła itd.
* **Monitorowanie stanu zadania**. W okienku po lewej stronie rozwiń węzeł **klaster**, a następnie kliknij pozycję **aplikacje** , aby wyświetlić listę wszystkich zadań w klastrze. Jeśli chcesz przejrzeć zadania w określonym stanie (na przykład nowe, przesłane, uruchomione itp.), kliknij odpowiednie łącze w obszarze **aplikacje**. Możesz również kliknąć nazwę zadania, aby dowiedzieć się więcej na temat zadania, takiego jak dane wyjściowe, dzienniki itp.

### <a name="access-the-hbase-ui"></a>Dostęp do interfejsu użytkownika HBase
W Azure Portal kliknij nazwę klastra usługi HDInsight HBase, aby otworzyć okienko klaster. W okienku klaster kliknij pozycję **pulpit nawigacyjny**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. W Ambari wybierz pozycję HBase z listy usług. W górnej części strony wybierz pozycję **szybkie linki** , wskaż łącze aktywny węzeł dozorcy, a następnie kliknij pozycję interfejs użytkownika HBase Master.

## <a name="hdinsight-error-codes"></a>Kody błędów usługi HDInsight
Komunikaty o błędach opisane w tej sekcji są udostępniane, aby pomóc użytkownikom usługi Hadoop w usłudze Azure HDInsight zrozumieć możliwe warunki błędu, które mogą napotkać podczas administrowania usługą za pomocą Azure PowerShell i doradzania w przypadku czynności, które można podjąć Aby odzyskać sprawność po błędzie.

Niektóre z tych komunikatów o błędach mogą być również widoczne w Azure Portal, gdy jest używany do zarządzania klastrami usługi HDInsight. Jednak inne komunikaty o błędach, które mogą wystąpić, są mniej szczegółowe ze względu na ograniczenia dotyczące akcji zaradczych możliwych w tym kontekście. Inne komunikaty o błędach są udostępniane w kontekstach, w których ograniczenie jest oczywiste. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Opis**: Podaj szczegóły usługi Azure SQL Database dla co najmniej jednego składnika, aby użyć niestandardowych ustawień dla programu Hive i magazynów metadanych Oozie.
* Środki **zaradcze**: użytkownik musi podać prawidłowy Magazyn bazy danych SQL Azure i ponowić próbę wykonania żądania.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Opis**: nie można utworzyć klastra w regionie *nameOfYourRegion*. Użyj prawidłowego regionu usługi HDInsight i ponów próbę żądania.
* Środki **zaradcze**: klient powinien utworzyć region klastra, który jest w tej chwili obsługiwany: Azja Południowo-Wschodnia, Europa Północna, Europa Zachodnia, Wschodnie stany USA lub zachodnie stany USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Opis**: serwer nie może znaleźć żądanego rekordu klastra.  
* Środki **zaradcze**: spróbuj ponownie wykonać operację.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Opis**: nazwa DNS klastra *yourDnsName* jest nieprawidłowa. Upewnij się, że nazwa zaczyna się od znaków alfanumerycznych i zawiera znaki specjalne "-".  
* Środki **zaradcze**: Upewnij się, że użyto prawidłowej nazwy DNS dla klastra rozpoczynającego się i kończącego znakiem alfanumerycznym i nie zawiera żadnych znaków specjalnych innych niż kreska "-", a następnie spróbuj ponownie wykonać operację.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Opis**: Nazwa klastra *yourClusterName* jest niedostępna. Wybierz inną nazwę.  
* Środki **zaradcze**: użytkownik powinien określić wartość ClusterName, która jest unikatowa i nie istnieje, i ponowić próbę. Jeśli użytkownik korzysta z portalu, interfejs użytkownika będzie powiadamiał o tym, czy nazwa klastra jest już używana podczas tworzenia kroków.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Opis**: hasło klastra jest nieprawidłowe. Hasło musi składać się z co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, wielkie litery, małe litery i znaki specjalne bez spacji i nie może zawierać nazwy użytkownika w ramach tej funkcji.  
* Środki **zaradcze**: Podaj prawidłowe hasło klastra, a następnie spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Opis**: Nazwa użytkownika klastra jest nieprawidłowa. Upewnij się, że nazwa użytkownika nie zawiera znaków specjalnych ani spacji.  
* Środki **zaradcze**: Podaj prawidłową nazwę użytkownika klastra i spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Opis**: nazwa DNS klastra *yourDnsClusterName* jest nieprawidłowa. Upewnij się, że nazwa zaczyna się od znaków alfanumerycznych i zawiera znaki specjalne "-".  
* Środki **zaradcze**: Podaj prawidłową nazwę użytkownika klastra DNS i spróbuj ponownie wykonać operację.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Opis**: nazwa kontenera w identyfikatorze URI *YOURCONTAINERURI* i nazwa DNS *yourDnsName* w treści żądania muszą być takie same.  
* Środki **zaradcze**: Upewnij się, że nazwa kontenera i nazwa DNS są takie same, a następnie spróbuj ponownie wykonać operację.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Opis**: nieprawidłowa konfiguracja klastra. Nie można znaleźć żadnych definicji węzłów danych w rozmiarze węzła.  
* Środki **zaradcze**: spróbuj ponownie wykonać operację.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Opis**: usuwanie wdrożenia nie powiodło się dla klastra  
* Środki **zaradcze**: ponów próbę wykonania operacji usuwania.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Opis**: błąd konfiguracji usługi. Nie znaleziono wymaganych informacji dotyczących mapowania DNS.  
* Środki **zaradcze**: Usuń klaster i Utwórz nowy klaster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Opis**: podjęto próbę utworzenia duplikatu kontenera klastra. Rekord istnieje dla elementu *nameOfYourContainer* , ale elementy ETag nie są zgodne.
* Środki **zaradcze**: Podaj unikatową nazwę dla kontenera, a następnie spróbuj ponownie wykonać operację tworzenia.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Opis**: *nameOfYourHostedService* usługi hostowanej zawiera już klaster. Usługa hostowana nie może zawierać wielu klastrów  
* Środki **zaradcze**: hostowanie klastra w innej usłudze hostowanej.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Opis**: serwer nie może zaktualizować stanu wdrożenia klastra.  
* Środki **zaradcze**: spróbuj ponownie wykonać operację. Jeśli wystąpi wiele razy, skontaktuj się z CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Opis**: *yourClusterName* klastra został usunięty w ramach konserwacji. Utwórz ponownie klaster.
* Środki **zaradcze**: Utwórz ponownie klaster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Opis**: nieprawidłowa konfiguracja klastra. W rozmiarach węzłów nie znaleziono wymaganej konfiguracji węzła głównego.
* Środki **zaradcze**: spróbuj ponownie wykonać operację.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Opis**: nie można utworzyć *nameOfYourHostedService*usługi hostowanej. Spróbuj ponownie wykonać żądanie.  
* Środki **zaradcze**: ponów próbę wykonania żądania.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Opis**: usługa hostowana *nameOfYourHostedService* ma już wdrożenie produkcyjne. Usługa hostowana nie może zawierać wielu wdrożeń produkcyjnych. Spróbuj ponownie wykonać żądanie, używając innej nazwy klastra.
* Środki **zaradcze**: Użyj innej nazwy klastra i ponów próbę wykonania żądania.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Opis**: nie można odnaleźć *nameOfYourHostedService* usługi hostowanej dla klastra.  
* Środki **zaradcze**: Jeśli klaster jest w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Opis**: *nameOfYourHostedService* usługi hostowanej nie ma skojarzonego wdrożenia.  
* Środki **zaradcze**: Jeśli klaster jest w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Opis**: *yourSubscriptionId* identyfikatora subskrypcji nie ma rdzeni, aby utworzyć *yourClusterName*klastra. Wymagane: *resourcesRequired*, dostępne: *resourcesAvailable*.  
* Środki **zaradcze**: Zwolnij zasoby w ramach subskrypcji lub Zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Opis**: Identyfikator subskrypcji *yourSubscriptionId* nie ma przydziału dla nowego usłudze hostowanej do tworzenia *yourClusterName*klastra.  
* Środki **zaradcze**: Zwolnij zasoby w ramach subskrypcji lub Zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Opis**: Serwer napotkał błąd wewnętrzny. Spróbuj ponownie wykonać żądanie.  
* Środki **zaradcze**: ponów próbę wykonania żądania.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Opis**: Lokalizacja usługi Azure Storage *dataregionname* nie jest prawidłową lokalizacją. Upewnij się, że region jest poprawny, i ponów próbę żądania.
* Środki **zaradcze**: Wybierz lokalizację magazynu obsługującą usługi HDInsight, sprawdź, czy klaster znajduje się na tym samym dysku, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Opis**: Nieprawidłowy rozmiar maszyny wirtualnej dla węzłów danych. Tylko rozmiar dużej maszyny wirtualnej jest obsługiwany dla wszystkich węzłów danych.  
* Środki **zaradcze**: Określ obsługiwany rozmiar węzła dla węzła danych, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Opis**: Nieprawidłowy rozmiar maszyny wirtualnej dla węzła głównego. Tylko rozmiar maszyny wirtualnej "ExtraLarge" jest obsługiwany dla węzła głównego.  
* Środki **zaradcze**: Określ obsługiwany rozmiar węzła dla węzła głównego, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Opis**: używany identyfikator subskrypcji *yourSubscriptionId* nie ma wystarczających uprawnień do wykonania operacji usuwania dla *yourClusterName*klastra.  
* Środki **zaradcze**: Jeśli klaster jest w stanie błędu, Porzuć go i spróbuj ponownie.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Opis**: nazwa kontenera obiektów BLOB zewnętrznego konta magazynu *yourContainerName* jest nieprawidłowa. Upewnij się, że nazwa rozpoczyna się od litery i zawiera tylko małe litery, cyfry i myślnik.  
* Środki **zaradcze**: Określ prawidłową nazwę kontenera obiektów BLOB konta magazynu i spróbuj ponownie wykonać operację.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Opis**: Konfiguracja konta magazynu zewnętrznego *yourStorageAccountName* jest wymagana, aby można było ustawić szczegóły klucza tajnego.  
* Środki **zaradcze**: Określ prawidłowy klucz tajny dla konta magazynu, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Opis**: *yourVersionHeader* nagłówka wersji jest w nieprawidłowym formacie RRRR-MM-DD.  
* Środki **zaradcze**: Określ prawidłowy format nagłówka wersji i ponów próbę żądania.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Opis**: nieprawidłowa konfiguracja klastra. Znaleziono więcej niż jedną konfigurację węzła głównego.  
* Środki **zaradcze**: edytuj konfigurację, aby określić tylko jeden węzeł główny.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Opis**: nie można ukończyć operacji w dozwolonym czasie lub maksymalna liczba ponownych prób. Spróbuj ponownie wykonać żądanie.  
* Środki **zaradcze**: ponów próbę wykonania żądania.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Opis**: parametr *yourParameterName* nie może mieć wartości null ani być pusty.  
* Środki **zaradcze**: Określ prawidłową wartość parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Opis**: co najmniej jeden z danych wejściowych żądania utworzenia klastra jest nieprawidłowy. Upewnij się, że wartości wejściowe są poprawne, i ponów próbę żądania.  
* Środki **zaradcze**: Upewnij się, że wartości wejściowe są poprawne, i ponów próbę żądania.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Opis**: funkcja regionu jest niedostępna dla regionu *YourRegionName* i identyfikatora subskrypcji *yourSubscriptionId*.  
* Środki **zaradcze**: Określ region obsługujący klastry usługi HDInsight. Ogólnie Obsługiwane regiony to: Azja Południowo-Wschodnia, Europa Zachodnia, Europa Północna, Wschodnie stany USA lub zachodnie stany USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Opis**: konto magazynu *yourStorageAccountName* jest w regionie *currentRegionName*. Powinien być taki sam jak region klastra *yourClusterRegionName*.  
* Środki **zaradcze**: Określ konto magazynu w tym samym regionie, w którym znajduje się klaster, lub jeśli dane są już na koncie magazynu, Utwórz nowy klaster w tym samym regionie, w którym znajduje się istniejące konto magazynu. Jeśli używasz portalu, interfejs użytkownika powiadomi o tym problem z wyprzedzeniem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Opis**: dany identyfikator subskrypcji *yourSubscriptionId* nie jest aktywny.  
* Środki **zaradcze**: Aktywuj ponownie subskrypcję lub Uzyskaj nową prawidłową subskrypcję.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Opis**: nie można odnaleźć identyfikatora subskrypcji *yourSubscriptionId* .  
* Środki **zaradcze**: Sprawdź, czy identyfikator subskrypcji jest prawidłowy, a następnie spróbuj ponownie wykonać operację.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Opis**: nie można rozpoznać *yourDnsUrl*DNS. Upewnij się, że podano w pełni kwalifikowany adres URL dla punktu końcowego obiektu BLOB.  
* Środki **zaradcze**: Podaj prawidłowy adres URL obiektu BLOB. Adres URL musi być w pełni prawidłowy, w tym zaczynający się od *http://* i kończącego na *. com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Opis**: nie można zweryfikować lokalizacji *yourDnsUrl*zasobów. Upewnij się, że podano w pełni kwalifikowany adres URL dla punktu końcowego obiektu BLOB.  
* Środki **zaradcze**: Podaj prawidłowy adres URL obiektu BLOB. Adres URL musi być w pełni prawidłowy, w tym zaczynający się od *http://* i kończącego na *. com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Opis**: funkcja wersji nie jest dostępna dla wersji *specifiedVersion* i subskrypcji o identyfikatorze *yourSubscriptionId*.  
* Środki **zaradcze**: Wybierz wersję, która jest dostępna, i spróbuj ponownie wykonać operację.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Opis**: wersja *specifiedVersion* nie jest obsługiwana.
* Środki **zaradcze**: wybierz obsługiwaną wersję, a następnie spróbuj ponownie wykonać operację.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Opis**: wersja *specifiedVersion* jest niedostępna w regionie usługi Azure *specifiedRegion*.  
* Środki **zaradcze**: Wybierz wersję obsługiwaną w określonym regionie, a następnie spróbuj ponownie wykonać operację.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Opis**: nieprawidłowa konfiguracja klastra. Nie znaleziono wymaganej konfiguracji konta WASB na koncie zewnętrznym.  
* Środki **zaradcze**: Sprawdź, czy konto istnieje i czy jest poprawnie określone w konfiguracji, a następnie spróbuj ponownie wykonać operację.

## <a name="next-steps"></a>Następne kroki

* [Włączanie zrzutów sterty dla usług Apache Hadoop w usłudze HDInsight opartej na systemie Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
