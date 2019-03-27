---
title: 'Debugowanie usługi Apache Hadoop: Wyświetlanie dzienników i interpretowanie komunikatów o błędach — Azure HDInsight'
description: Więcej informacji na temat komunikatów o błędach, które może zostać wyświetlony, gdy administrowanie usługą HDInsight przy użyciu programu PowerShell oraz czynności, które można wykonać w celu odzyskania.
services: hdinsight
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: a035789af08aa4c0d877a06295d9bd6fdedf6844
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449481"
---
# <a name="analyze-apache-hadoop-logs"></a>Analizowanie dzienników usługi Apache Hadoop

Każdy klaster Apache Hadoop w usłudze Azure HDInsight ma konto usługi Azure storage używany jako domyślny system plików. Konto magazynu jest określane jako domyślne konto magazynu. Klaster używa usługi Azure Table storage i Blob storage na domyślne konto magazynu do przechowywania swoich dzienników.  Aby dowiedzieć się, domyślne konto magazynu dla klastra, zobacz [klastrów zarządzania Apache Hadoop w HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Dzienniki nadal na koncie magazynu, nawet po klaster jest usuwany.

## <a name="logs-written-to-azure-tables"></a>Dzienniki zapisane tabele platformy Azure

Dzienniki zapisane tabele platformy Azure zapewniają wgląd w tym, co dzieje się z klastrem usługi HDInsight o jeden poziom.

Podczas tworzenia klastra usługi HDInsight sześciu tabel są tworzone automatycznie dla klastrów opartych na systemie Linux w usłudze Table storage domyślne:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Nazwy plików w tabeli są **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Te tabele zawierają następujące pola:

* ClusterDnsName
* ComponentName
* eventTimestamp
* Host
* MALoggingHash
* Komunikat
* Nie
* PreciseTimeStamp
* Rola
* RowIndex
* Dzierżawa
* SYGNATURA CZASOWA
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Narzędzia do uzyskiwania dostępu do dzienników
Brak dostępnych wiele narzędzi do uzyskiwania dostępu do danych w tych tabelach:

* Visual Studio
* Eksplorator usługi Azure Storage
* Dodatku Power Query dla programu Excel

#### <a name="use-power-query-for-excel"></a>Użyj dodatku Power Query dla programu Excel
Dodatek Power Query mogą być instalowane z [dodatku Microsoft Power Query dla programu Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Zobacz stronę pobierania pakietu z wymaganiami systemowymi.

**Otwórz i analizowanie dzienników usługi za pomocą dodatku Power Query**

1. Otwórz **programu Microsoft Excel**.
2. Z **dodatku Power Query** menu, kliknij przycisk **z platformy Azure**, a następnie kliknij przycisk **z Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery otworzyć usługi Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Wprowadź nazwę konta magazynu, krótką nazwę lub nazwę FQDN.
4. Wprowadź klucz konta magazynu. Zostanie wyświetlona lista tabel:
   
    ![Dzienniki usługi HDInsight Hadoop, przechowywane w usłudze Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kliknij prawym przyciskiem myszy w tabeli hadoopservicelog **Nawigator** okienka, a następnie wybierz **Edytuj**. Zostanie wyświetlona czterech kolumn. Opcjonalnie Usuń **klucza partycji**, **klucz wiersza**, i **sygnatura czasowa** kolumn, wybierając je, a następnie klikając **Usuń kolumny** z Opcje na Wstążce.
6. Kliknij ikonę rozwijania zawartości kolumny wybierz kolumny, które chcesz zaimportować do arkusza kalkulacyjnego programu Excel. W tej prezentacji wybrano TraceLevel i ComponentName: Możesz podać mi pewne podstawowe informacje, na którym składników ma problemy.
   
    ![Dzienniki usługi HDInsight Hadoop wybierz kolumny](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Kliknij przycisk **OK** do importowania danych.
8. Wybierz **TraceLevel**, roli i **ComponentName** kolumny, a następnie kliknij **Group By** formantu na Wstążce.
9. Kliknij przycisk **OK** w oknie dialogowym Group By
10. Kliknij pozycję ** Zastosuj & Zamknij **.

Można teraz używać programu Excel do filtrowania i sortowania, zgodnie z potrzebami. Może być uwzględnienie innych kolumn (np. komunikat o), aby przejść do problemów, gdy występują one, ale wybierając i grupowanie kolumn opisanych powyżej zapewnia znośnego obrazu, co się dzieje z usługi Hadoop. Ten sam pomysł można zastosować do tabel setuplog i hadoopinstalllog.

#### <a name="use-visual-studio"></a>Korzystanie z programu Visual Studio
**Korzystanie z programu Visual Studio**

1. Otwórz program Visual Studio.
2. Z **widoku** menu, kliknij przycisk **programu Cloud Explorer**. Lub po prostu kliknij **CTRL +\, CTRL + X**.
3. Z **programu Cloud Explorer**, wybierz opcję **typów zasobów**.  Jest dostępną opcją **grup zasobów**.
4. Rozwiń **kont magazynu**, domyślne konto magazynu dla klastra, a następnie **tabel**.
5. Kliknij dwukrotnie **hadoopservicelog**.
6. Dodaj filtr. Na przykład:
   
        TraceLevel eq 'ERROR'
   
    ![Dzienniki usługi HDInsight Hadoop wybierz kolumny](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Aby uzyskać więcej informacji na temat tworzenia filtrów, zobacz [konstruowania parametry filtru dla projektanta tabel](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Dzienniki zapisywane w magazynie obiektów Blob platformy Azure
Dzienniki zapisane tabele platformy Azure zapewniają wgląd w tym, co dzieje się z klastrem usługi HDInsight o jeden poziom. Jednak te tabele są oferowane dzienniki poziom zadania, które mogą być pomocne w przechodzenia do szczegółów do problemów w momencie ich wystąpienia. Aby zapewnić tym następny poziom szczegółów, klastry HDInsight są skonfigurowane na zapisywanie dzienników zadań do swojego konta magazynu obiektów Blob w ramach zadania, który jest przesyłany za pośrednictwem Templeton. W praktyce oznacza to, zadania przesłane za pomocą poleceń cmdlet programu PowerShell Azure firmy Microsoft lub zadanie przesyłania interfejsów API platformy .NET, bez zadań przesyłać za pośrednictwem protokołu RDP/wiersza polecenia dostępu do klastra. 

Aby wyświetlić dzienniki, zobacz [dostępu Apache Hadoop YARN application loguje HDInsight opartych na systemie Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Aby uzyskać więcej informacji na temat dzienników aplikacji, zobacz [uproszczenie zarządzania logowania użytkownika i dostępem w programie Apache Hadoop YARN](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Wyświetl dzienniki klastra kondycji i zadania
### <a name="access-the-ambari-ui"></a>Dostęp do interfejsu użytkownika Ambari
W witrynie Azure portal kliknij nazwę klastra HDInsight, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpit nawigacyjny**.

![Uruchom pulpit nawigacyjny klastra](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Dostęp do interfejsu użytkownika usługi Yarn
W witrynie Azure portal kliknij nazwę klastra HDInsight, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpit nawigacyjny**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Ambari, wybierz **YARN** z listy usług po lewej stronie. Na wyświetlonej stronie wybierz **szybkich łączy**, następnie wybierz wpis aktywnego węzła głównego i Menedżera zasobów.

Można użyć Interfejsie użytkownika YARN, wykonaj następujące czynności:

* **Pobieranie stanu klastra**. W lewym okienku rozwiń **klastra**i kliknij przycisk **o**. Ta istnieje klastra Szczegóły stanu, takie jak całkowitej przydzielonej pamięci i rdzeni używanych, stan, Menedżer zasobów klastra, wersja klastra i tak dalej.
  
    ![Uruchom pulpit nawigacyjny klastra](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Pobierz stan węzła**. W lewym okienku rozwiń **klastra**i kliknij przycisk **węzłów**. Ta lista zawiera wszystkie węzły w adres HTTP każdego węzła klastra zasoby przydzielone do każdego węzła, itp.
* **Monitorowanie stanu zadania**. W lewym okienku rozwiń **klastra**, a następnie kliknij przycisk **aplikacje** Aby wyświetlić listę wszystkich zadań w klastrze. Jeśli chcesz przyjrzeć się zadań w określonym stanie (na przykład uruchomiony nowy, przesłane, itp.), kliknąć odpowiednie łącze umieszczone w obszarze **aplikacji**. Dodatkowo możesz kliknąć nazwę zadania, aby dowiedzieć się więcej o zadaniu takie, w tym dane wyjściowe, dzienników itp.

### <a name="access-the-hbase-ui"></a>Dostęp do interfejsu użytkownika HBase
W witrynie Azure portal kliknij nazwę klastra HDInsight HBase, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpit nawigacyjny**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. W Ambari wybierz bazy danych HBase z listy usług. Wybierz **szybkich łączy** górnej części strony, wskaż aktywny link węzła dozorcy, a następnie kliknij przycisk głównego interfejsu użytkownika HBase.

## <a name="hdinsight-error-codes"></a>Kody błędów HDInsight
Aby ułatwić użytkownikom usługi Hadoop w usłudze Azure HDInsight zrozumieć możliwe warunki wystąpienia błędu, które mogą wystąpić podczas administrowania tą usługą przy użyciu programu Azure PowerShell i zaleca im na temat kroków, które mogą być wykonywane znajdują się komunikaty o błędach, wymienione w tej sekcji Aby odzyskać sprawność po błędzie.

Niektóre z tych komunikatów o błędach można także znaleźć w witrynie Azure portal, gdy jest używany do zarządzania klastrami HDInsight. Jednak mogą występować inne komunikaty o błędach są mniej szczegółowe ze względu na ograniczenia dotyczące działań zaradczych, w tym kontekście. Inne komunikaty o błędach są udostępniane w kontekstach, gdzie jest oczywiste, środki zaradcze. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Opis**: Podaj szczegóły bazy danych Azure SQL dla co najmniej jeden składnik, aby można było używać niestandardowych ustawień dla gałęzi i Oozie magazyny metadanych.
* **Środki zaradcze**: Użytkownik musi podać prawidłowe metadanych SQL Azure i ponów próbę żądania.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Opis**: Nie można utworzyć klaster w regionie *nameOfYourRegion*. Użyj ważnym przekraczanym HDInsight, a następnie ponowić żądanie.
* **Środki zaradcze**: Klienta należy utworzyć w regionie klastra, który obsługuje obecnie: Azja południowo-wschodnia, Europa Zachodnia, Europa Północna, wschodnie stany USA i zachodnie stany USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Opis**: Serwer nie można odnaleźć rekordu żądany klaster.  
* **Środki zaradcze**: Ponów operację.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Opis**: Nazwy DNS klastra *yourDnsName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się ciągiem alfanumerycznym i może zawierać tylko "-" znaków specjalnych  
* **Środki zaradcze**: Upewnij się, że używasz prawidłowej nazwy DNS dla Twojego klastra, który zaczyna się i kończy się ciągiem alfanumerycznym i zawiera żadnych specjalnych znaki inne niż kreski "-", a następnie ponów operację.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Opis**: Nazwa klastra *yourClusterName* jest niedostępny. Wybierz inną nazwę.  
* **Środki zaradcze**: Użytkownik powinien określić clustername, która jest unikatowa i nie istnieje i spróbuj ponownie. Jeśli użytkownik korzysta z portalu, interfejsu użytkownika powiadomi je, jeśli nazwa klastra jest już używana podczas wykonywania czynności tworzenia.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Opis**: Hasło klastra jest nieprawidłowa. Hasło musi zawierać co najmniej 10 znaków i musi zawierać co najmniej jednej cyfry, wielkie litery, małe litery i znaków specjalnych, bez spacji i nie może zawierać nazwy użytkownika jako jej część.  
* **Środki zaradcze**: Podaj hasło klastra prawidłową i spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Opis**: Nazwa użytkownika klastra jest nieprawidłowa. Upewnij się, że nazwa użytkownika nie zawiera znaków specjalnych ani spacji.  
* **Środki zaradcze**: Podaj nazwę użytkownika klastra prawidłową i spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Opis**: Nazwy DNS klastra *yourDnsClusterName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się ciągiem alfanumerycznym i może zawierać tylko "-" znaków specjalnych  
* **Środki zaradcze**: Podaj prawidłową nazwę użytkownika klastra DNS i spróbuj ponownie wykonać operację.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Opis**: Nazwa kontenera w identyfikatorze URI *yourcontainerURI* i nazwa DNS *yourDnsName* w żądaniu treść musi być taka sama.  
* **Środki zaradcze**: Upewnij się, że nazwa kontenera i nazwy DNS są takie same i spróbuj ponownie wykonać operację.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć żadnych definicji węzła danych w rozmiar węzła.  
* **Środki zaradcze**: Ponów operację.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Opis**: Usunięcie wdrożenia nie powiodło się dla klastra  
* **Środki zaradcze**: Spróbuj ponownie wykonać operację usuwania.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Opis**: Błąd konfiguracji usługi. Nie można odnaleźć wymaganych informacji mapowanie DNS.  
* **Środki zaradcze**: Usuwanie klastra i utworzenie nowego klastra.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Opis**: Podjęto próbę utworzenia kontenera zduplikowane klastra. Istnieje rekord dla *nameOfYourContainer* , ale nie są zgodne elementy ETag.
* **Środki zaradcze**: Podaj unikatową nazwę kontenera, a następnie ponów próbę wykonania operacji tworzenia.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Opis**: Usługa hostowana *nameOfYourHostedService* zawiera już klaster. Usługa hostowana nie może zawierać wielu klastrów  
* **Środki zaradcze**: Hosta klastra w innej usługi hostowanej.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Opis**: Serwer nie może zaktualizować stan wdrożenia klastra.  
* **Środki zaradcze**: Ponów operację. Jeśli występuje on wiele razy, skontaktuj się z działem CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Opis**: Klaster *yourClusterName* została usunięta w ramach konserwacji. Utwórz ponownie klaster.
* **Środki zaradcze**: Utwórz ponownie klaster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć w rozmiary węzłów Konfiguracja wymagana węzła głównego.
* **Środki zaradcze**: Ponów operację.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Opis**: Nie można utworzyć usługi hostowanej *nameOfYourHostedService*. Ponów próbę żądania.  
* **Środki zaradcze**: Ponów żądanie.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Opis**: Usługa hostowana *nameOfYourHostedService* jest już w przypadku wdrożenia produkcyjnego. Usługa hostowana nie może zawierać wiele wdrożeń produkcyjnych. Ponów próbę wykonania żądania o inną nazwę klastra.
* **Środki zaradcze**: Użyj inną nazwę klastra i ponowić próbę żądania.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Opis**: Usługa hostowana *nameOfYourHostedService* dla klastra nie został odnaleziony.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Opis**: Usługa hostowana *nameOfYourHostedService* ma żadnych skojarzonych wdrożeń.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Opis**: Identyfikator subskrypcji *yourSubscriptionId* nie ma rdzeni w lewo do utworzenia klastra *yourClusterName*. Wymagane: *resourcesRequired*, która jest dostępna: *resourcesAvailable*.  
* **Środki zaradcze**: Zwolnij część zasobów w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Opis**: Identyfikator subskrypcji *yourSubscriptionId* nie ma limitu przydziału dla nowej usłudze hostowanej utworzyć klaster *yourClusterName*.  
* **Środki zaradcze**: Zwolnij część zasobów w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Opis**: Serwer napotkał błąd wewnętrzny. Ponów próbę żądania.  
* **Środki zaradcze**: Ponów żądanie.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Opis**: Lokalizacja magazynu platformy Azure *dataRegionName* nie jest prawidłową lokalizację. Upewnij się, że region jest poprawna, a następnie ponowić żądanie.
* **Środki zaradcze**: Wybierz lokalizację magazynu, który obsługuje HDInsight, sprawdź, czy klaster jest w tej samej lokalizacji i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Opis**: Nieprawidłowy rozmiar maszyny Wirtualnej dla węzłów danych. Tylko rozmiar duża maszyna wirtualna jest obsługiwany dla wszystkich węzłów danych.  
* **Środki zaradcze**: Określ rozmiar węzła obsługiwanych dla węzła danych i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Opis**: Nieprawidłowy rozmiar maszyny Wirtualnej węzła głównego. Tylko rozmiar "ExtraLarge maszyny Wirtualnej" jest obsługiwany dla węzła głównego.  
* **Środki zaradcze**: Określ rozmiar węzła obsługiwanych węzła głównego, a następnie spróbuj ponownie wykonać operację

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Opis**: Identyfikator subskrypcji *yourSubscriptionId* używane nie ma wystarczających uprawnień do wykonania operacji usuwania dla klastra *yourClusterName*.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błąd, należy usunąć ją, a następnie spróbuj ponownie.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Opis**: Nazwa kontenera obiektów blob konta magazynu zewnętrznego *yourContainerName* jest nieprawidłowy. Upewnij się, że nazwa zaczyna się od litery i zawiera tylko małe litery, cyfry i kreski.  
* **Środki zaradcze**: Określ prawidłową nazwę konta magazynu obiektów blob kontenera, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Opis**: Konfiguracja dla konta magazynu zewnętrznego *yourStorageAccountName* musi mieć wpisu tajnego klucza szczegóły, aby ustawić.  
* **Środki zaradcze**: Określ prawidłowy klucz tajny dla konta magazynu, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Opis**: Nagłówek wersji *yourVersionHeader* nie jest w prawidłowym formacie rrrr mm-dd.  
* **Środki zaradcze**: Określ prawidłowy format w nagłówku wersji, a następnie ponowić próbę żądania.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Opis**: Konfiguracja klastra nieprawidłowy. Znaleziono więcej niż jedną konfigurację węzła głównego.  
* **Środki zaradcze**: Edytuj konfigurację, dzięki czemu jest określony tylko jeden węzeł główny.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Opis**: Nie można ukończyć operacji w dozwolonym czasie lub maksymalna liczba ponownych prób to możliwe. Ponów próbę żądania.  
* **Środki zaradcze**: Ponów żądanie.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Opis**: Parametr *yourParameterName* nie może być zerowa lub pusta.  
* **Środki zaradcze**: Określ prawidłową wartość dla parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Opis**: Co najmniej jeden z danych wejściowych żądania tworzenia klastra jest nieprawidłowy. Upewnij się, wartości wejściowe są poprawne i ponów żądanie.  
* **Środki zaradcze**: Upewnij się, wartości wejściowe są poprawne i ponów żądanie.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Opis**: Funkcja region nie jest dostępna w regionie *yourRegionName* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Określ region, który obsługuje klastry HDInsight. Publicznie obsługiwane regiony są: Azja południowo-wschodnia, Europa Zachodnia, Europa Północna, wschodnie stany USA i zachodnie stany USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Opis**: Konto magazynu *yourStorageAccountName* znajduje się w regionie *currentRegionName*. Powinien być taki sam jak region klastra *yourClusterRegionName*.  
* **Środki zaradcze**: Określ konto magazynu w tym samym regionie, należącym do klastra lub jeśli danych jest już w ramach konta magazynu, Utwórz nowy klaster, w tym samym regionie, co istniejące konto magazynu. Jeśli używasz portalu, interfejsu użytkownika będzie powiadamiać użytkowników o tym problemie z wyprzedzeniem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Opis**: Podany identyfikator subskrypcji *yourSubscriptionId* nie jest aktywny.  
* **Środki zaradcze**: Ponownie aktywować swoją subskrypcję lub Uzyskaj nowe prawidłową subskrypcję.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Opis**: Identyfikator subskrypcji *yourSubscriptionId* nie został odnaleziony.  
* **Środki zaradcze**: Sprawdź, czy identyfikator subskrypcji jest prawidłowy i spróbuj ponownie wykonać operację.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Opis**: Nie można rozpoznać DNS *yourDnsUrl*. Upewnij się, że podano w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL prawidłowy obiektu blob. Adres URL musi być w pełni prawidłowe, włącznie, począwszy od *http://* i kończący się w *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Opis**: Nie można zweryfikować lokalizację zasobu *yourDnsUrl*. Upewnij się, że podano w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL prawidłowy obiektu blob. Adres URL musi być w pełni prawidłowe, włącznie, począwszy od *http://* i kończący się w *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Opis**: Możliwości wersji nie jest dostępna dla wersji *specifiedVersion* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Wybierz wersję, która jest dostępna i spróbuj ponownie wykonać operację.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Opis**: Wersja *specifiedVersion* nie jest obsługiwane.
* **Środki zaradcze**: Wybierz wersję, która jest obsługiwana i spróbuj ponownie wykonać operację.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Opis**: Wersja *specifiedVersion* nie jest dostępna w regionie świadczenia usługi Azure *specifiedRegion*.  
* **Środki zaradcze**: Wybierz wersję, która jest obsługiwana w regionie określony, a następnie spróbuj ponownie wykonać operację.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć w konta zewnętrzne wymaganej konfiguracji konta WASB.  
* **Środki zaradcze**: Sprawdź, czy konto istnieje i jest poprawnie określona w konfiguracji, a następnie spróbuj ponownie wykonać operację.

## <a name="next-steps"></a>Kolejne kroki

* [Włączanie zrzutów sterty dla usługi Apache Hadoop w HDInsight opartych na systemie Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
