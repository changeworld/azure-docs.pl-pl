---
title: 'Debugowanie usługi Hadoop w HDInsight: wyświetlanie dzienników i interpretowanie komunikatów o błędach - Azure '
description: Więcej informacji na temat komunikatów o błędach, które może zostać wyświetlony, gdy administrowanie usługą HDInsight przy użyciu programu PowerShell oraz czynności, które można wykonać w celu odzyskania.
services: hdinsight
editor: jasonwhowell
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 00d09619db11ea0026f5386048f1c10a8f831948
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592785"
---
# <a name="analyze-hadoop-logs"></a>Analizowanie dzienników usługi Hadoop

Każdy klaster Hadoop w usłudze Azure HDInsight ma konto usługi Azure storage używany jako domyślny system plików. Konto magazynu jest określane jako domyślne konto magazynu. Klaster używa usługi Azure Table storage i Blob storage na domyślne konto magazynu do przechowywania swoich dzienników.  Aby dowiedzieć się, domyślne konto magazynu dla klastra, zobacz [Zarządzanie klastrów usługi Hadoop w HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Dzienniki nadal na koncie magazynu, nawet po klaster jest usuwany.

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
* ZNACZNIK CZASU:
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Narzędzia do uzyskiwania dostępu do dzienników
Brak dostępnych wiele narzędzi do uzyskiwania dostępu do danych w tych tabelach:

* Visual Studio
* Eksplorator usługi Azure Storage
* Dodatku Power Query dla programu Excel

#### <a name="use-power-query-for-excel"></a>Użyj dodatku Power Query dla programu Excel
Dodatek Power Query mogą być instalowane z [dodatku Microsoft Power Query dla programu Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). Zobacz stronę pobierania pakietu z wymaganiami systemowymi.

**Otwórz i analizowanie dzienników usługi za pomocą dodatku Power Query**

1. Otwórz **programu Microsoft Excel**.
2. Z **dodatku Power Query** menu, kliknij przycisk **z platformy Azure**, a następnie kliknij przycisk **z Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery otworzyć usługi Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Wprowadź nazwę konta magazynu, krótką nazwę lub nazwę FQDN.
4. Wprowadź klucz konta magazynu. Zostanie wyświetlona lista tabel:
   
    ![Dzienniki usługi HDInsight Hadoop, przechowywane w usłudze Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kliknij prawym przyciskiem myszy w tabeli hadoopservicelog **Nawigator** okienka, a następnie wybierz **Edytuj**. Zostanie wyświetlona czterech kolumn. Opcjonalnie Usuń **klucza partycji**, **klucz wiersza**, i **sygnatura czasowa** kolumn, wybierając je, a następnie klikając **Usuń kolumny** z Opcje na Wstążce.
6. Kliknij ikonę rozwijania zawartości kolumny wybierz kolumny, które chcesz zaimportować do arkusza kalkulacyjnego programu Excel. W tej prezentacji wybrano TraceLevel i ComponentName: go możesz podać mi pewne podstawowe informacje, na którym składników ma problemy.
   
    ![Dzienniki usługi HDInsight Hadoop wybierz kolumny](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Kliknij przycisk **OK** do importowania danych.
8. Wybierz **TraceLevel**, roli i **ComponentName** kolumny, a następnie kliknij **Group By** formantu na Wstążce.
9. Kliknij przycisk **OK** w oknie dialogowym Group By
10. Kliknij pozycję ** Zastosuj & Zamknij **.

Można teraz używać programu Excel do filtrowania i sortowania, zgodnie z potrzebami. Może być uwzględnienie innych kolumn (np. komunikat o), aby przejść do problemów, gdy występują one, ale wybierając i grupowanie kolumn opisanych powyżej zapewnia znośnego obrazu, co się dzieje z usługi Hadoop. Ten sam pomysł można zastosować do tabel setuplog i hadoopinstalllog.

#### <a name="use-visual-studio"></a>Korzystanie z programu Visual Studio
**Używanie programu Visual Studio**

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
[Dzienniki zapisane tabele platformy Azure](#log-written-to-azure-tables) zapewniają wgląd w tym, co dzieje się z klastrem usługi HDInsight o jeden poziom. Jednak te tabele są oferowane dzienniki poziom zadania, które mogą być pomocne w przechodzenia do szczegółów do problemów w momencie ich wystąpienia. Aby zapewnić tym następny poziom szczegółów, klastry HDInsight są skonfigurowane na zapisywanie dzienników zadań do swojego konta magazynu obiektów Blob w ramach zadania, który jest przesyłany za pośrednictwem Templeton. W praktyce oznacza to, zadania przesłane za pomocą poleceń cmdlet programu PowerShell Azure firmy Microsoft lub zadanie przesyłania interfejsów API platformy .NET, bez zadań przesyłać za pośrednictwem protokołu RDP/wiersza polecenia dostępu do klastra. 

Aby wyświetlić dzienniki, zobacz [aplikacji usługi YARN dostępu loguje HDInsight opartych na systemie Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Aby uzyskać więcej informacji na temat dzienników aplikacji, zobacz [uproszczenie zarządzania logowania użytkownika i dostępu w YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

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

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Opis**: Podaj szczegóły bazy danych Azure SQL dla co najmniej jeden składnik, aby można było używać niestandardowych ustawień dla gałęzi i Oozie magazyny metadanych.
* **Środki zaradcze**: użytkownik musi podać prawidłowe metadanych SQL Azure i ponowić próbę żądania.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Opis**: nie można utworzyć klaster w regionie *nameOfYourRegion*. Użyj ważnym przekraczanym HDInsight, a następnie ponowić żądanie.
* **Środki zaradcze**: klienta należy utworzyć w regionie klastra, który obsługuje obecnie: Azja południowo-wschodnia, Europa Zachodnia, Europa Północna, wschodnie stany USA i zachodnie stany USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Opis**: serwer nie można odnaleźć rekordu żądany klaster.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Opis**: nazwa DNS klastra *yourDnsName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się ciągiem alfanumerycznym i może zawierać tylko "-" znaków specjalnych  
* **Środki zaradcze**: Upewnij się, że używasz prawidłowej nazwy DNS dla Twojego klastra, który zaczyna się i kończy się ciągiem alfanumerycznym i zawiera żadnych specjalnych znaki inne niż kreski "-", a następnie ponów operację.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Opis**: Nazwa klastra *yourClusterName* jest niedostępny. Wybierz inną nazwę.  
* **Środki zaradcze**: użytkownik powinien określić clustername, która jest unikatowa i nie istnieje i spróbuj ponownie. Jeśli użytkownik korzysta z portalu, interfejsu użytkownika powiadomi je, jeśli nazwa klastra jest już używana podczas wykonywania czynności tworzenia.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Opis**: hasło klastra jest nieprawidłowe. Hasło musi zawierać co najmniej 10 znaków i musi zawierać co najmniej jednej cyfry, wielkie litery, małe litery i znaków specjalnych, bez spacji i nie może zawierać nazwy użytkownika jako jej część.  
* **Środki zaradcze**: Podaj hasło prawidłowym klastrem, a następnie spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Opis**: nazwa użytkownika klastra jest nieprawidłowa. Upewnij się, że nazwa użytkownika nie zawiera znaków specjalnych ani spacji.  
* **Środki zaradcze**: Podaj nazwę użytkownika klastra prawidłową i spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Opis**: nazwa DNS klastra *yourDnsClusterName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się ciągiem alfanumerycznym i może zawierać tylko "-" znaków specjalnych  
* **Środki zaradcze**: Podaj prawidłową nazwę użytkownika klastra DNS i spróbuj ponownie wykonać operację.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Opis**: nazwa kontenera w identyfikatorze URI *yourcontainerURI* i nazwa DNS *yourDnsName* w żądaniu treść musi być taka sama.  
* **Środki zaradcze**: Upewnij się, że nazwa kontenera i nazwy DNS są takie same i spróbuj ponownie wykonać operację.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć żadnych definicji węzła danych w rozmiar węzła.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Opis**: usunięcie wdrożenia nie powiodło się dla klastra  
* **Środki zaradcze**: spróbuj ponownie wykonać operację usuwania.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Opis**: błąd konfiguracji usługi. Nie można odnaleźć wymaganych informacji mapowanie DNS.  
* **Środki zaradcze**: usunięcie klastra i utworzenie nowego klastra.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Opis**: zduplikowana próba utworzenia kontenera klastra. Istnieje rekord dla *nameOfYourContainer* , ale nie są zgodne elementy ETag.
* **Środki zaradcze**: Podaj unikatową nazwę dla kontenera i ponów próbę wykonania operacji tworzenia.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Opis**: Usługa hostowana *nameOfYourHostedService* zawiera już klaster. Usługa hostowana nie może zawierać wielu klastrów  
* **Środki zaradcze**: hosta klastra w innej usługi hostowanej.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Opis**: serwer nie może zaktualizować stan wdrożenia klastra.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację. Jeśli występuje on wiele razy, skontaktuj się z działem CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Opis**: klastra *yourClusterName* została usunięta w ramach konserwacji. Utwórz ponownie klaster.
* **Środki zaradcze**: Utwórz ponownie klaster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć w rozmiary węzłów Konfiguracja wymagana węzła głównego.
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Opis**: nie można utworzyć usługi hostowanej *nameOfYourHostedService*. Ponów próbę żądania.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Opis**: usługi hostowanej *nameOfYourHostedService* jest już w przypadku wdrożenia produkcyjnego. Usługa hostowana nie może zawierać wiele wdrożeń produkcyjnych. Ponów próbę wykonania żądania o inną nazwę klastra.
* **Środki zaradcze**: Użyj inną nazwę klastra i ponowić próbę żądania.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Opis**: usługi hostowanej *nameOfYourHostedService* dla klastra nie został odnaleziony.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Opis**: usługi hostowanej *nameOfYourHostedService* ma żadnych skojarzonych wdrożeń.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Opis**: SubscriptionId *yourSubscriptionId* nie ma rdzeni w lewo do utworzenia klastra *yourClusterName*. Wymagane: *resourcesRequired*, która jest dostępna: *resourcesAvailable*.  
* **Środki zaradcze**: Zwolnij część zasobów w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Opis**: identyfikator subskrypcji *yourSubscriptionId* nie ma limitu przydziału dla nowej usłudze hostowanej utworzyć klaster *yourClusterName*.  
* **Środki zaradcze**: Zwolnij część zasobów w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Opis**: serwer napotkał błąd wewnętrzny. Ponów próbę żądania.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Opis**: lokalizacji usługi Azure Storage *dataRegionName* nie jest prawidłową lokalizację. Upewnij się, że region jest poprawna, a następnie ponowić żądanie.
* **Środki zaradcze**: Wybierz lokalizację magazynu, który obsługuje HDInsight, sprawdź, czy klaster jest w tej samej lokalizacji i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Opis**: rozmiar nieprawidłowy maszyny Wirtualnej dla węzłów danych. Tylko rozmiar duża maszyna wirtualna jest obsługiwany dla wszystkich węzłów danych.  
* **Środki zaradcze**: Określ rozmiar węzła obsługiwanych dla węzła danych i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Opis**: rozmiar maszyny Wirtualnej nieprawidłowy dla węzła głównego. Tylko rozmiar "ExtraLarge maszyny Wirtualnej" jest obsługiwany dla węzła głównego.  
* **Środki zaradcze**: Określ rozmiar węzła obsługiwanych węzła głównego, a następnie spróbuj ponownie wykonać operację

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Opis**: identyfikator subskrypcji *yourSubscriptionId* używane nie ma wystarczających uprawnień do wykonania operacji usuwania dla klastra *yourClusterName*.  
* **Środki zaradcze**: Jeśli klaster znajduje się w stanie błąd, upuść go, a następnie spróbuj ponownie.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Opis**: nazwa kontenera obiektów blob konta magazynu zewnętrznego *yourContainerName* jest nieprawidłowy. Upewnij się, że nazwa zaczyna się od litery i zawiera tylko małe litery, cyfry i kreski.  
* **Środki zaradcze**: Określ prawidłową nazwę konta magazynu obiektów blob kontenera, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Opis**: Konfiguracja dla konta magazynu zewnętrznego *yourStorageAccountName* musi mieć wpisu tajnego klucza szczegóły, aby ustawić.  
* **Środki zaradcze**: Określ prawidłowy klucz tajny dla konta magazynu, a następnie spróbuj ponownie wykonać operację.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Opis**: nagłówek wersji *yourVersionHeader* nie jest w prawidłowym formacie rrrr mm-dd.  
* **Środki zaradcze**: Określ prawidłowy format w nagłówku wersji, a następnie ponowić próbę żądania.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Opis**: Konfiguracja klastra nieprawidłowy. Znaleziono więcej niż jedną konfigurację węzła głównego.  
* **Środki zaradcze**: Zmień konfigurację, dzięki czemu jest określony tylko jeden węzeł główny.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Opis**: nie można ukończyć operacji w dozwolonym czasie lub maksymalna liczba ponownych prób to możliwe. Ponów próbę żądania.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Opis**: parametr *yourParameterName* nie może być zerowa lub pusta.  
* **Środki zaradcze**: Określ prawidłową wartość dla parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Opis**: co najmniej jeden z danych wejściowych żądania tworzenia klastra jest nieprawidłowa. Upewnij się, wartości wejściowe są poprawne i ponów żądanie.  
* **Środki zaradcze**: Upewnij się, że wartości wejściowe są poprawne i ponów żądanie.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Opis**: funkcja Region nie jest dostępna w regionie *yourRegionName* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Określ region, który obsługuje klastry HDInsight. Są publicznie obsługiwanych regionów: Południowo-Wschodnia Azja, Europa Zachodnia, Europa Północna, wschodnie stany USA i zachodnie stany USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Opis**: Konto magazynu *yourStorageAccountName* znajduje się w regionie *currentRegionName*. Powinien być taki sam jak region klastra *yourClusterRegionName*.  
* **Środki zaradcze**: Określ konto magazynu w tym samym regionie, należącym do klastra lub jeśli danych jest już w ramach konta magazynu, Utwórz nowy klaster, w tym samym regionie, co istniejące konto magazynu. Jeśli używasz portalu, interfejsu użytkownika będzie powiadamiać użytkowników o tym problemie z wyprzedzeniem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Opis**: podany identyfikator subskrypcji *yourSubscriptionId* nie jest aktywny.  
* **Środki zaradcze**: ponownie aktywować swoją subskrypcję lub Uzyskaj nowe prawidłową subskrypcję.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Opis**: identyfikator subskrypcji *yourSubscriptionId* nie został odnaleziony.  
* **Środki zaradcze**: Sprawdź, czy identyfikator subskrypcji jest prawidłowy, a następnie spróbuj ponownie wykonać operację.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Opis**: nie można rozpoznać DNS *yourDnsUrl*. Upewnij się, że podano w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL prawidłowy obiektu blob. Adres URL musi być w pełni prawidłowe, włącznie, począwszy od *http://* i kończący się w *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Opis**: nie można zweryfikować lokalizację zasobu *yourDnsUrl*. Upewnij się, że podano w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL prawidłowy obiektu blob. Adres URL musi być w pełni prawidłowe, włącznie, począwszy od *http://* i kończący się w *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Opis**: możliwości wersji nie jest dostępna dla wersji *specifiedVersion* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Wybierz wersję, która jest dostępna i spróbuj ponownie wykonać operację.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Opis**: wersja *specifiedVersion* nie jest obsługiwane.
* **Środki zaradcze**: Wybierz wersję, która jest obsługiwana, a następnie spróbuj ponownie wykonać operację.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Opis**: wersja *specifiedVersion* nie jest dostępna w regionie świadczenia usługi Azure *specifiedRegion*.  
* **Środki zaradcze**: Wybierz wersję, która jest obsługiwana w regionie określony, a następnie spróbuj ponownie wykonać operację.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Opis**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć w konta zewnętrzne wymaganej konfiguracji konta WASB.  
* **Środki zaradcze**: Sprawdź, czy konto istnieje i jest poprawnie określona w konfiguracji, a następnie spróbuj ponownie wykonać operację.

## <a name="next-steps"></a>Kolejne kroki

* [Debugowanie zadań tez przy na HDInsight przy użyciu widoków Ambari](../hdinsight-debug-ambari-tez-view.md)
* [Włączanie zrzutów sterty dla usług Hadoop w HDInsight opartych na systemie Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web systemu Ambari](../hdinsight-hadoop-manage-ambari.md)
