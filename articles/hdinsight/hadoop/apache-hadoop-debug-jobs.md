---
title: 'Debugowanie Hadoop w usłudze HDInsight: Sprawdź dzienniki i interpretować komunikaty o błędach - Azure | Dokumentacja firmy Microsoft'
description: Więcej informacji na temat komunikaty o błędach, które może pojawić się w przypadku administrowania HDInsight przy użyciu programu PowerShell i czynności, które można wykonać w celu odzyskania.
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: ashishthaps
documentationcenter: ''
ms.assetid: 7e6ceb0e-8be8-4911-bc80-20714030a3ad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8952e1381d81d153a170f35dd3cfd3f60cd4f73a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31404760"
---
# <a name="analyze-hadoop-logs"></a>Analizowanie dzienników usługi Hadoop

Każdy klaster Hadoop w usłudze Azure HDInsight ma konto magazynu Azure używany jako domyślny system plików. Konto magazynu jest określane jako domyślne konto magazynu. Klaster używa magazynu tabel Azure i magazynu obiektów Blob na domyślne konto magazynu do przechowywania jej dzienników.  Aby dowiedzieć się, domyślne konto magazynu dla klastra, zobacz [klastrów zarządzania Hadoop w HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Dzienniki zachowują się na koncie magazynu, nawet po usunięciu klastra.

## <a name="logs-written-to-azure-tables"></a>Dzienniki zapisywane do tabel platformy Azure

Dzienniki zapisywane do tabel Azure zapewnia jeden poziom wgląd w działania wykonywane z klastrem usługi HDInsight.

Podczas tworzenia klastra usługi HDInsight sześciu tabele są tworzone automatycznie dla opartych na systemie Linux klastrów w magazynie tabel domyślne:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Nazwy plików tabeli są **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Te tabele zawierają następujące pola:

* ClusterDnsName
* NazwaSkładnika
* eventTimestamp
* Host
* MALoggingHash
* Komunikat
* N
* PreciseTimeStamp
* Rola
* RowIndex
* Dzierżawa
* ZNACZNIK CZASU
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Narzędzia do uzyskiwania dostępu do dzienników
Brak dostępnych wiele narzędzi do uzyskiwania dostępu do danych w tych tabelach:

* Visual Studio
* Eksplorator usługi Azure Storage
* Dodatek Power Query dla programu Excel

#### <a name="use-power-query-for-excel"></a>Użyj dodatku Power Query dla programu Excel
Dodatek Power Query mogą być instalowane z [Microsoft Power Query dla programu Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). Odwiedź stronę pobierania wymagania systemowe.

**Użyj dodatku Power Query, aby otworzyć i analizowanie dzienników usługi**

1. Otwórz **programu Microsoft Excel**.
2. Z **dodatku Power Query** menu, kliknij przycisk **Azure z**, a następnie kliknij przycisk **z Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery otworzyć magazynu tabel Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Wprowadź nazwę konta magazynu, krótką nazwę lub nazwę FQDN.
4. Wprowadź klucz konta magazynu. Zostanie wyświetlona lista tabel:
   
    ![HDInsight Hadoop dzienników przechowywanych w magazynie tabel platformy Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kliknij prawym przyciskiem myszy tabelę hadoopservicelog w **Nawigator** okienka, a następnie wybierz **Edytuj**. Zostanie wyświetlona cztery kolumny. Opcjonalnie można usunąć **klucza partycji**, **klucz wiersza**, i **sygnatury czasowej** kolumn, wybierając je, a następnie klikając **Usuń kolumny** z Opcje na Wstążce.
6. Kliknij ikonę rozwijania w kolumnie zawartości wybierz kolumny, które chcesz zaimportować do arkusza kalkulacyjnego programu Excel. Dla tego pokazu wybrano TraceLevel i NazwaSkładnika: ją zapewnić mnie kilku podstawowych informacji, na którym składników ma problemy.
   
    ![Dzienniki usługi Hadoop w HDInsight Wybieranie kolumn](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Kliknij przycisk **OK** do importowania danych.
8. Wybierz **TraceLevel**, roli i **NazwaSkładnika** kolumny, a następnie kliknij przycisk **Group By** formantu na Wstążce.
9. Kliknij przycisk **OK** w oknie dialogowym Grupuj według
10. Kliknij przycisk ** zastosować & Zamknij **.

Można teraz używać programu Excel do filtrowania i sortowania w razie potrzeby. Możesz dołączyć innych kolumn (np. wiadomości), aby przejść do problemów, jeśli występują one, ale zaznaczanie i grupowanie kolumn opisane powyżej zapewnia zadowalający obraz, co się dzieje z usługi Hadoop. Informacje o tym samym może odnosić się do tabel setuplog i hadoopinstalllog.

#### <a name="use-visual-studio"></a>Korzystanie z programu Visual Studio
**Program Visual Studio**

1. Otwórz program Visual Studio.
2. Z **widoku** menu, kliknij przycisk **Eksplorator chmury**. Lub po prostu kliknij **CTRL +\, CTRL + X**.
3. Z **Eksplorator chmury**, wybierz pozycję **typów zasobów**.  Jest dostępna opcja **grup zasobów**.
4. Rozwiń węzeł **kont magazynu**, domyślne konto magazynu dla klastra, a następnie **tabel**.
5. Kliknij dwukrotnie **hadoopservicelog**.
6. Dodaj filtr. Na przykład:
   
        TraceLevel eq 'ERROR'
   
    ![Dzienniki usługi Hadoop w HDInsight Wybieranie kolumn](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Aby uzyskać więcej informacji na temat tworzenia filtrów, zobacz [utworzenia filtru ciągów dla projektanta tabel](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Dzienniki zapisywane do magazynu obiektów Blob platformy Azure
[Dzienniki zapisywane do tabel Azure](#log-written-to-azure-tables) zapewnia jeden poziom wgląd w działania wykonywane z klastrem usługi HDInsight. Jednak te tabele nie udostępniają dzienniki poziomie zadania, które mogą być pomocne w przechodzenia na wyższy poziom na problemy podczas występowania. Aby zapewnić tym następnego poziomu szczegółów, klastry usługi HDInsight są skonfigurowane do zapisywanie dzienników zadań na konta magazynu obiektów Blob dla dowolnego zadania, który jest przesyłany przez Templeton. W praktyce oznacza to zadania przesłane za pomocą interfejsu API przesyłania zadania .NET, nie zadania przesłane za pośrednictwem protokołu RDP/polecenia-wiersza dostęp do klastra lub poleceń cmdlet programu PowerShell usługi Microsoft Azure. 

Aby wyświetlić dzienniki, zobacz [logowania YARN dostęp do aplikacji opartych na systemie Linux usługi HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Aby uzyskać więcej informacji o dziennikach aplikacji, zobacz [uproszczenie zarządzania logowania użytkownika i dostępu w YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Wyświetl dzienniki klastra kondycji i zadania
### <a name="access-the-ambari-ui"></a>Dostęp do interfejsu użytkownika narzędzia Ambari
W portalu Azure kliknij nazwę klastra usługi HDInsight, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpitu nawigacyjnego**.

![Uruchomienie klastra pulpitu nawigacyjnego](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Dostęp Yarn interfejsu użytkownika
W portalu Azure kliknij nazwę klastra usługi HDInsight, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpitu nawigacyjnego**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. Ambari, wybierz **YARN** z listy usług po lewej stronie. Na stronie zostanie wyświetlona, wybierz **szybkie linki**, następnie wybierz wpis aktywnego węzła głównego i interfejs użytkownika Menedżera zasobów.

Interfejs użytkownika YARN umożliwia wykonaj następujące czynności:

* **Pobierz stan klastra**. W lewym okienku rozwiń **klastra**i kliknij przycisk **o**. To obecnie klastra Szczegóły stanu, takie jak Suma przydzielonej pamięci, użyto, rdzeni stan Menedżera zasobów klastra, wersja klastra i tak dalej.
  
    ![Uruchomienie klastra pulpitu nawigacyjnego](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Pobierz stan węzła**. W lewym okienku rozwiń **klastra**i kliknij przycisk **węzłów**. Ta lista zawiera wszystkie węzły w adres HTTP każdego węzła klastra zasoby przydzielone do każdego węzła itp.
* **Monitorowanie stanu zadania**. W lewym okienku rozwiń **klastra**, a następnie kliknij przycisk **aplikacji** listę wszystkich zadań w klastrze. Jeśli chcesz przyjrzeć się zadań w określonym stanie (na przykład uruchomienie nowej, przesłane, itp.), kliknij odpowiednie łącze w obszarze **aplikacji**. Można dalsze kliknij nazwę zadania, aby dowiedzieć się więcej o zadaniu takie łącznie z danych wyjściowych dzienników, itp.

### <a name="access-the-hbase-ui"></a>Dostęp do bazy danych HBase interfejsu użytkownika
W portalu Azure kliknij nazwę klastra HDInsight HBase, aby otworzyć okienko klastra. W okienku klastra, kliknij **pulpitu nawigacyjnego**. Po wyświetleniu monitu wprowadź poświadczenia administratora klastra. W Ambari wybierz bazy danych HBase na liście usług. Wybierz **szybkie linki** górnej części strony, wskaż polecenie aktywnego łącza dozorcy w węźle, a następnie kliknij głównego HBase interfejsu użytkownika.

## <a name="hdinsight-error-codes"></a>Kody błędów HDInsight
Komunikaty o błędach wymienione w tej sekcji znajdują się ułatwiają użytkownikom platformy Hadoop w usłudze Azure HDInsight, zrozumienie możliwe błędy napotykane przez nich można podczas administrowania usługą przy użyciu programu Azure PowerShell i poinformować ich o krokach, które można podjąć Aby odzyskać sprawność po błędzie.

Niektóre z tych komunikaty o błędach można także znaleźć w portalu Azure, gdy jest używany do zarządzania klastrami usługi HDInsight. Inne komunikaty o błędach mogą wystąpić, ale jest mniej szczegółowe ze względu na ograniczenia dotyczące czynności zaradczych możliwe w tym kontekście. Inne komunikaty o błędach są zawarte w kontekstach, gdzie łagodzenie jest widoczne. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Opis elementu**: Aby użyć niestandardowych ustawień dla gałęzi i Oozie magazyny Podaj szczegóły bazy danych Azure SQL dla co najmniej jeden składnik.
* **Środki zaradcze**: użytkownik musi podać prawidłową potrzeby magazynu metadanych usługi SQL Azure i ponów żądanie.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Opis elementu**: nie można utworzyć klastra w regionie *nameOfYourRegion*. Nieprawidłowy region usługi HDInsight przy użyciu, a następnie ponów żądanie.
* **Środki zaradcze**: klienta należy utworzyć regionu klastra, który obsługuje obecnie: Azja południowo-wschodnia, Europa Zachodnia, Europa Północna, wschodnie stany USA lub zachodnie stany USA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Opis elementu**: serwer nie można odnaleźć rekordu żądany klaster.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Opis elementu**: nazwa DNS klastra *yourDnsName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się wyrazem alfanumerycznego i może zawierać tylko "-" znaki specjalne  
* **Środki zaradcze**: Upewnij się, że używasz prawidłowej nazwy DNS dla klastra, który rozpoczyna się i kończy się wyrazem alfanumeryczne zawiera żadnych specjalnych znaki inne niż kreska "-", a następnie spróbuj ponownie wykonać operację.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Opis elementu**: Nazwa klastra *yourClusterName* jest niedostępny. Wybierz inną nazwę.  
* **Środki zaradcze**: użytkownik powinien Określ NazwaKlastra, która jest unikatowa i nie istnieje i spróbuj ponownie. Jeśli użytkownik korzysta z portalu, interfejs użytkownika będzie powiadamiać użytkowników, jeśli nazwa klastra jest używany podczas tworzenia.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Opis elementu**: hasło klastra jest nieprawidłowe. Hasło musi zawierać co najmniej 10 znaków i musi zawierać co najmniej jedną cyfrę, wielką literę, małą literę i znak specjalny nie może zawierać spacji i nie powinna zawierać nazwę użytkownika, jako części.  
* **Środki zaradcze**: Podaj hasło prawidłowym klastrem, a następnie spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Opis elementu**: nazwa użytkownika klastra jest nieprawidłowy. Upewnij się, że nazwa użytkownika nie zawiera znaków specjalnych ani spacji.  
* **Środki zaradcze**: Podaj klastra prawidłową nazwę użytkownika i spróbuj ponownie wykonać operację.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Opis elementu**: nazwa DNS klastra *yourDnsClusterName* jest nieprawidłowy. Sprawdź, czy nazwa rozpoczyna się i kończy się wyrazem alfanumerycznego i może zawierać tylko "-" znaki specjalne  
* **Środki zaradcze**: Podaj prawidłową nazwę klastra DNS użytkownika i spróbuj ponownie wykonać operację.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Opis elementu**: nazwa kontenera w identyfikatorze URI *yourcontainerURI* i nazwa DNS *yourDnsName* w żądaniu treści musi być taka sama.  
* **Środki zaradcze**: Upewnij się, że Twoje nazwa kontenera i nazwy DNS są takie same i spróbuj ponownie wykonać operację.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Opis elementu**: Konfiguracja klastra nieprawidłowy. Nie można znaleźć żadnych danych węzła definicji rozmiaru węzła.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Opis elementu**: usunięcie wdrożenia nie powiodło się dla klastra  
* **Środki zaradcze**: spróbuj ponownie wykonać operację usuwania.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Opis elementu**: błąd konfiguracji usługi. Nie można odnaleźć wymaganych informacji mapowanie DNS.  
* **Środki zaradcze**: Usuwanie klastra i utworzenie nowego klastra.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Opis elementu**: zduplikowana próba utworzenia kontenera klastra. Istnieje rekord dla *nameOfYourContainer* , ale nie są zgodne elementy ETag.
* **Środki zaradcze**: Podaj unikatową nazwę dla kontenera i spróbuj ponownie wykonać operację tworzenia.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Opis elementu**: Usługa hostowana *nameOfYourHostedService* zawiera już klastra. Usługa hostowana nie może zawierać wielu klastrów  
* **Środki zaradcze**: hosta klastra w innej usługi hostowanej.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Opis elementu**: serwer nie można zaktualizować stanu wdrożenia klastra.  
* **Środki zaradcze**: spróbuj ponownie wykonać operację. Jeśli występuje wiele razy, skontaktuj się z CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Opis elementu**: klastra *yourClusterName* została usunięta w ramach obsługi. Utwórz ponownie klaster.
* **Środki zaradcze**: Utwórz ponownie klaster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Opis elementu**: Konfiguracja klastra nieprawidłowy. Nie można znaleźć w węźle rozmiary konfiguracji wymaganego węzła głównego.
* **Środki zaradcze**: spróbuj ponownie wykonać operację.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Opis elementu**: nie można utworzyć usługi hostowanej *nameOfYourHostedService*. Ponów żądanie.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Opis elementu**: usługi hostowanej *nameOfYourHostedService* ma już wdrożenia produkcyjnego. Usługa hostowana nie może zawierać wielu wdrożeń produkcyjnych. Ponów żądanie w inną nazwę klastra.
* **Środki zaradcze**: Użyj inną nazwę klastra i ponów żądanie.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Opis elementu**: usługi hostowanej *nameOfYourHostedService* dla nie można znaleźć klastra.  
* **Środki zaradcze**: Jeśli klaster jest w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Opis elementu**: usługi hostowanej *nameOfYourHostedService* ma nie skojarzonego wdrożenia.  
* **Środki zaradcze**: Jeśli klaster jest w stanie błędu, usuń go, a następnie spróbuj ponownie.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Opis elementu**: SubscriptionId *yourSubscriptionId* nie ma rdzeni pozostało do utworzenia klastra *yourClusterName*. Wymagane: *resourcesRequired*, dostępne: *resourcesAvailable*.  
* **Środki zaradcze**: zwolnić zasoby w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Opis elementu**: identyfikator subskrypcji *yourSubscriptionId* nie ma limitu przydziału dla nowej usługi hostowanej utworzyć klaster *yourClusterName*.  
* **Środki zaradcze**: zwolnić zasoby w ramach subskrypcji lub zwiększ zasoby dostępne dla subskrypcji i spróbuj ponownie utworzyć klaster.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Opis elementu**: serwer napotkał błąd wewnętrzny. Ponów żądanie.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Opis elementu**: lokalizacja magazynu Azure *dataRegionName* nie jest prawidłową lokalizacją. Upewnij się, że region jest poprawny i ponów żądanie.
* **Środki zaradcze**: Wybierz lokalizację magazynu, który obsługuje HDInsight, sprawdź, czy klaster wspólnie i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Opis elementu**: rozmiar nieprawidłowy maszyny Wirtualnej dla węzły danych. Tylko rozmiar "Dużych maszyna wirtualna" jest obsługiwany dla wszystkich węzłów danych.  
* **Środki zaradcze**: Określ rozmiar obsługiwanych węzła węzeł danych i spróbuj ponownie wykonać operację.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Opis elementu**: rozmiar nieprawidłowy maszyny Wirtualnej dla węzła głównego. Tylko rozmiar "ExtraLarge maszyna wirtualna" jest obsługiwana dla węzła głównego.  
* **Środki zaradcze**: Określ rozmiar obsługiwanych węzła węzła głównego, a następnie spróbuj ponownie wykonać operację

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Opis elementu**: identyfikator subskrypcji *yourSubscriptionId* używany nie ma wystarczających uprawnień do wykonania operacji usuwania dla klastra *yourClusterName*.  
* **Środki zaradcze**: Jeśli klaster jest w stanie błędu, Porzuć go, a następnie spróbuj ponownie.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Opis elementu**: nazwa kontenera obiektów blob konta magazynu zewnętrznego *yourContainerName* jest nieprawidłowy. Upewnij się, nazwa rozpoczyna się od litery i zawiera tylko małe litery, cyfry i łączniki.  
* **Środki zaradcze**: Określ prawidłową nazwę konta magazynu obiektów blob kontenera i spróbuj ponownie wykonać operację.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Opis elementu**: Konfiguracja dla konta magazynu zewnętrznego *yourStorageAccountName* musi mieć tajny szczegółów klucza należy ustawić wartość.  
* **Środki zaradcze**: Określ prawidłowy klucz tajny dla konta magazynu i spróbuj ponownie wykonać operację.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Opis elementu**: nagłówek wersji *yourVersionHeader* nie jest w nieprawidłowym formacie rrrr mm-dd.  
* **Środki zaradcze**: Określ prawidłowy format nagłówka wersji i ponów żądanie.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Opis elementu**: Konfiguracja klastra nieprawidłowy. Znaleziono więcej niż jedną konfigurację węzła głównego.  
* **Środki zaradcze**: Zmień konfigurację tak określono tylko jeden węzeł head.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Opis elementu**: nie można ukończyć operacji w dozwolonym czasie lub maksymalną liczbę ponownych prób to możliwe. Ponów żądanie.  
* **Środki zaradcze**: ponowić żądanie.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Opis elementu**: parametr *yourParameterName* nie może mieć wartości null ani być pusta.  
* **Środki zaradcze**: Określ prawidłową wartość dla parametru.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Opis elementu**: co najmniej jednego z wejść żądania tworzenia klastra jest nieprawidłowy. Upewnij się, wartości wejściowe są poprawne i ponów żądanie.  
* **Środki zaradcze**: Upewnij się, że wartości wejściowe są poprawne i ponów żądanie.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Opis elementu**: możliwości Region nie jest dostępna dla regionu *yourRegionName* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Określ region, który obsługuje klastry usługi HDInsight. Publicznie obsługiwane regiony są: Azja południowo-wschodnia, Europa Zachodnia, Europa Północna, wschodnie stany USA lub zachodnie stany USA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Opis elementu**: Konto magazynu *yourStorageAccountName* znajduje się w regionie *currentRegionName*. Powinien być taki sam jak region klastra *yourClusterRegionName*.  
* **Środki zaradcze**: Określ konto magazynu w tym samym regionie, który znajduje się w klastrze lub jeśli dane są już na koncie magazynu, Utwórz nowy klaster, w tym samym regionie co istniejące konto magazynu. Jeśli używasz portalu interfejsu użytkownika będzie powiadamiać użytkowników, tego problemu z wyprzedzeniem.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Opis elementu**: podany identyfikator subskrypcji *yourSubscriptionId* nie jest aktywny.  
* **Środki zaradcze**: ponownie aktywować subskrypcję lub pobrać nowe prawidłowej subskrypcji.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Opis elementu**: identyfikator subskrypcji *yourSubscriptionId* nie można odnaleźć.  
* **Środki zaradcze**: Sprawdź, czy identyfikator subskrypcji jest prawidłowy, a następnie spróbuj ponownie wykonać operację.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Opis elementu**: nie można rozpoznać DNS *yourDnsUrl*. Upewnij się, że jest dostępne w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL nieprawidłowy obiekt blob. Adres URL powinien być pełni prawidłową, w tym począwszy *http://* i kończący się w *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Opis elementu**: nie można sprawdzić poprawności lokalizacji zasobów *yourDnsUrl*. Upewnij się, że jest dostępne w pełni kwalifikowany adres URL punktu końcowego obiektu blob.  
* **Środki zaradcze**: Podaj adres URL nieprawidłowy obiekt blob. Adres URL powinien być pełni prawidłową, w tym począwszy *http://* i kończący się w *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Opis elementu**: możliwości wersji nie jest dostępna dla wersji *specifiedVersion* i identyfikator subskrypcji *yourSubscriptionId*.  
* **Środki zaradcze**: Wybierz wersję, która jest dostępna i spróbuj ponownie wykonać operację.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Opis elementu**: wersja *specifiedVersion* nie jest obsługiwane.
* **Środki zaradcze**: Wybierz obsługiwaną wersję, a następnie spróbuj ponownie wykonać operację.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Opis elementu**: wersja *specifiedVersion* nie jest dostępna w regionie Azure *specifiedRegion*.  
* **Środki zaradcze**: Wybierz wersję, który jest obsługiwany przez określonego regionu i spróbuj ponownie wykonać operację.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Opis elementu**: Konfiguracja klastra nieprawidłowy. Nie można odnaleźć w kont zewnętrznych wymaganej konfiguracji konta WASB.  
* **Środki zaradcze**: Sprawdź, czy konto istnieje i jest poprawnie określona w konfiguracji, a następnie spróbuj ponownie wykonać operację.

## <a name="next-steps"></a>Kolejne kroki

* [Debugowanie aplikacji Tez zadania w usłudze HDInsight przy użyciu widoków Ambari](../hdinsight-debug-ambari-tez-view.md)
* [Włącz zrzuty stosu dla usługi Hadoop w HDInsight opartych na systemie Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](../hdinsight-hadoop-manage-ambari.md)
