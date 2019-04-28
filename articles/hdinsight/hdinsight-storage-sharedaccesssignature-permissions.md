---
title: Ograniczanie dostępu przy użyciu sygnatury dostępu współdzielonego — Azure HDInsight
description: Dowiedz się, jak ograniczyć HDInsight dostęp do danych przechowywanych w obiektach blob usługi Azure storage za pomocą sygnatur dostępu współdzielonego.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 7fa46e3a5f0ed6504e4bc927caa0378d75fcc4a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763395"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Ograniczania dostępu do danych w HDInsight za pomocą sygnatur dostępu współdzielonego magazynu platformy Azure

HDInsight ma pełny dostęp do danych w ramach kont usługi Azure Storage skojarzonym z klastrem. Sygnatur dostępu współdzielonego dla kontenera obiektów blob umożliwia ograniczanie dostępu do danych. Dostęp do sygnatur Współdzielonego są funkcji konta usługi Azure storage, która pozwala na ograniczanie dostępu do danych. Na przykład zapewniając dostęp do danych tylko do odczytu.

> [!IMPORTANT]  
> Rozwiązanie przy użyciu struktury Apache Ranger należy rozważyć użycie HDInsight przyłączone do domeny. Aby uzyskać więcej informacji, zobacz [HDInsight przyłączone do domeny skonfiguruj](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]  
> HDInsight musi mieć pełny dostęp do domyślnego magazynu klastra.

## <a name="requirements"></a>Wymagania

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Subskrypcja platformy Azure
* C# lub języka Python. Przykładowy kod języka C# jest dostarczany jako rozwiązanie programu Visual Studio.

  * Program Visual Studio musi być w wersji 2013, 2015 lub 2017
  * Python musi być w wersji 2.7 lub nowszego

* Klaster HDInsight opartych na systemie Linux lub [programu Azure PowerShell] [ powershell] — Jeśli masz istniejący klaster oparty na systemie Linux, można użyć Apache Ambari do dodania sygnaturę dostępu współdzielonego do klastra. Jeśli nie użyjesz programu Azure PowerShell do utworzenia klastra i Dodaj sygnaturę dostępu współdzielonego podczas tworzenia klastra.

    > [!IMPORTANT]  
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Przykład plików ze [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). To repozytorium zawiera następujące elementy:

  * Projekt programu Visual Studio, który można utworzyć kontenera magazynu, zapisane zasady i interfejsie SAS do użycia z usługą HDInsight
  * Skrypt w języku Python, który można utworzyć kontenera magazynu, zapisane zasady i interfejsie SAS do użycia z usługą HDInsight
  * Skrypt programu PowerShell, który można utworzyć klaster HDInsight i skonfigurować go do korzystania z sygnatury dostępu Współdzielonego.

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Istnieją dwa rodzaje sygnatur dostępu współdzielonego:

* Ad hoc: Czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są określone w identyfikatorze URI sygnatury dostępu Współdzielonego.

* Przechowywanych zasad dostępu: Przechowywanych zasad dostępu jest zdefiniowany w kontenerze, zasobów, takich jak kontener obiektów blob. Zasady może służyć do zarządzania ograniczeniami dla jednego lub więcej sygnatur dostępu współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest kojarzony z przechowywanych zasad dostępu, sygnatury dostępu Współdzielonego dziedziczy ograniczenia — godzina rozpoczęcia, czas wygaśnięcia i uprawnienia — zdefiniowane dla przechowywanych zasad dostępu.

Różnica między dwoma formami jest ważna dla jednego kluczowy scenariusz: odwołania. Sygnatury dostępu Współdzielonego jest adresem URL, dzięki czemu każdy, kto uzyskuje sygnaturę dostępu Współdzielonego, używać niezależnie od tego, kto zażądano rozpoczynać się. Sygnatury dostępu Współdzielonego zostanie opublikowany publicznie, może służyć przez dowolną osobę na całym świecie. Sygnatury dostępu Współdzielonego, który jest rozpowszechniany jest prawidłowy, dopóki jedna z następujących czterech zdarzeń sytuacji:

1. Czas wygaśnięcia, określone w sygnatury dostępu Współdzielonego zostanie osiągnięty.

2. Czas wygaśnięcia, określone w zasadach dostępu składowana odwołuje się sygnatury dostępu Współdzielonego zostanie osiągnięty. Następujące scenariusze spowodować, że czas wygaśnięcia osiągnąć:

    * Przedział czasu upłynął.
    * Zasady dostępu przechowywane zostanie zmodyfikowany na potrzeby ma czas wygaśnięcia w przeszłości. Zmiana czas wygaśnięcia jest jednym ze sposobów, aby można było odwołać sygnatury dostępu Współdzielonego.

3. Usunąć przechowywanych zasad dostępu odwołuje się sygnatury dostępu Współdzielonego, który jest inny sposób, aby można było odwołać sygnatury dostępu Współdzielonego. Jeśli ponownie przechowywane zasady dostępu o takiej samej nazwie, wszystkie tokeny sygnatur dostępu Współdzielonego dla poprzednich zasad są prawidłowe, (Jeśli nie minął czas wygaśnięcia na sygnatury dostępu Współdzielonego). Zamierzasz odwołać sygnatury dostępu Współdzielonego, należy użyć innej nazwy, jeśli ponownie zasady dostępu z czasem wygaśnięcia w przyszłości.

4. Klucz konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego zostanie ponownie wygenerowany. Trwa ponowne generowanie klucza powoduje, że wszystkie aplikacje, które używają poprzedniej klucz niepowodzenia uwierzytelniania. Zaktualizuj wszystkie składniki do nowego klucza.

> [!IMPORTANT]  
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta używane do tworzenia podpisu, a skojarzone przechowywane zasady dostępu (jeśli istnieje). Jeśli nie przechowywanych zasad dostępu jest określona, jedynym sposobem, aby można było odwołać sygnatury dostępu współdzielonego jest należy zmienić wartość klucza konta.

Firma Microsoft zaleca, zawsze używaj przechowywanych zasad dostępu. Korzystając z zasady przechowywane, możesz odwołać podpisów lub przedłużyć datę wygaśnięcia, zgodnie z potrzebami. Kroki opisane w tym dokumencie używane przechowywane zasady dostępu do generowania sygnatury dostępu Współdzielonego.

Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [opis modelu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Utwórz zasady przechowywane i sygnatury dostępu Współdzielonego przy użyciu języka C\#

1. Otwórz rozwiązanie w programie Visual Studio.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SASToken** projektu, a następnie wybierz **właściwości**.

3. Wybierz **ustawienia** i Dodaj wartości dla następujących pozycji:

   * StorageConnectionString: Parametry połączenia dla konta magazynu, który chcesz utworzyć zasady przechowywane i sygnatury dostępu Współdzielonego dla. Powinna być w formacie `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` gdzie `myaccount` jest nazwą konta magazynu i `mykey` jest kluczem do konta magazynu.

   * ContainerName: Kontener na koncie magazynu, którą chcesz ograniczyć dostęp do.

   * SASPolicyName: Nazwa używać przechowywanych zasad w celu utworzenia.

   * FileToUpload: Ścieżka do pliku, który zostanie przekazany do kontenera.

4. Uruchom projekt. Po wygenerowaniu sygnatury dostępu Współdzielonego, są wyświetlane informacje podobne do następującego tekstu:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Zapisz zasady tokenu sygnatury dostępu Współdzielonego, nazwa konta magazynu i nazwa kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastrem usługi HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Tworzenie sygnatury dostępu Współdzielonego przy użyciu języka Python i zapisane zasady

1. Otwórz plik SASToken.py i Zmień następujące wartości:

   * zasady\_nazwy: Nazwa używać przechowywanych zasad w celu utworzenia.

   * Magazyn\_konta\_nazwy: Nazwa konta magazynu.

   * Magazyn\_konta\_klucza: Klucz konta magazynu.

   * Magazyn\_kontenera\_nazwy: Kontener na koncie magazynu, którą chcesz ograniczyć dostęp do.

   * przykład\_pliku\_ścieżki: Ścieżka do pliku, który zostanie przekazany do kontenera.

2. Uruchom skrypt. Po ukończeniu działania skryptu, wyświetla tokenu sygnatury dostępu Współdzielonego, podobny do następującego tekstu:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Zapisz zasady tokenu sygnatury dostępu Współdzielonego, nazwa konta magazynu i nazwa kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastrem usługi HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Sygnatura dostępu Współdzielonego za pomocą HDInsight

Podczas tworzenia klastra usługi HDInsight, należy określić konto magazynu podstawowego i opcjonalnie można określić dodatkowe konta magazynu. Obie te metody dodawania magazynu wymagają pełnego dostępu do konta magazynu i kontenerów, które są używane.

Aby użyć sygnaturę dostępu współdzielonego, aby ograniczyć dostęp do kontenera, Dodaj niestandardowy wpis, aby **lokacji podstawowej** konfigurację klastra.

* Aby uzyskać **oparte na Windows** lub **opartych na systemie Linux** klastrów HDInsight, można dodać wpisu podczas tworzenia klastra przy użyciu programu PowerShell.
* Aby uzyskać **opartych na systemie Linux** klastrów HDInsight, zmień konfigurację po utworzeniu klastra przy użyciu narzędzia Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Tworzenie klastra, który korzysta z sygnatury dostępu Współdzielonego

Przykładem tworzenia klastra usługi HDInsight, który korzysta z sygnatury dostępu Współdzielonego są objęte `CreateCluster` katalogu repozytorium. Z niej korzystać, wykonaj następujące kroki:

1. Otwórz `CreateCluster\HDInsightSAS.ps1` plik w edytorze tekstów i zmodyfikuj następujące wartości na początku dokumentu.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Na przykład zmienić `'mycluster'` na nazwę klastra, którą chcesz utworzyć. Wartości sygnatur dostępu Współdzielonego powinny odpowiadać wartości z poprzednich kroków, podczas tworzenia konta magazynu i tokenu sygnatury dostępu Współdzielonego.

    Po zmianie wartości, Zapisz plik.

2. Otwórz nowy wiersz programu Azure PowerShell. Jeśli znasz programu Azure PowerShell lub nie został zainstalowany, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell][powershell].

1. W wierszu polecenia użyj następującego polecenia do uwierzytelniania w Twojej subskrypcji platformy Azure:

    ```powershell
    Connect-AzAccount
    ```

    Po wyświetleniu monitu zaloguj się przy użyciu konta dla subskrypcji platformy Azure.

    Jeśli Twoje konto jest skojarzone z wieloma subskrypcjami platformy Azure, może być konieczne użycie `Select-AzSubscription` Wybierz subskrypcję, o których chcesz użyć.

4. W wierszu polecenia Zmień katalogi do `CreateCluster` katalog zawierający plik HDInsightSAS.ps1. Następnie użyj następującego polecenia do uruchomienia skryptu

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Po uruchomieniu skryptu, rejestruje dane wyjściowe do wiersza polecenia programu PowerShell podczas tworzenia kont grupy i storage zasobu. Monit o podanie użytkownika HTTP dla klastra HDInsight. To konto służy do bezpiecznego dostępu HTTP/HTTPS do klastra.

    Jeśli tworzysz klaster oparty na systemie Linux pojawia się monit o podanie nazwy konta użytkownika SSH i hasła. To konto jest używane do zdalnego logowania do klastra.

   > [!IMPORTANT]  
   > Po wyświetleniu monitu o HTTP/HTTPS lub nazwa użytkownika SSH i hasło, należy podać hasło, które spełnia następujące kryteria:
   >
   > * Musi być co najmniej 10 znaków.
   > * Musi zawierać co najmniej jedną cyfrę.
   > * Musi zawierać co najmniej jeden znak inny niż alfanumeryczny.
   > * Musi zawierać co najmniej jedną wielką lub małą literę.

Zajmuje trochę czasu ten skrypt, aby ukończyć, zwykle około 15 minut. Po zakończeniu działania skryptu bez żadnych błędów został utworzony klaster.

### <a name="use-the-sas-with-an-existing-cluster"></a>Sygnatura dostępu Współdzielonego za pomocą istniejącego klastra

Jeśli masz istniejący klaster oparty na systemie Linux, można dodać na sygnatur dostępu Współdzielonego **lokacji podstawowej** konfiguracji wykonując następujące kroki:

1. Otwórz interfejs webowy Ambari dla klastra. Adres na tej stronie jest https://YOURCLUSTERNAME.azurehdinsight.net. Po wyświetleniu monitu uwierzytelniania w klastrze przy użyciu nazwy administratora (Administrator) i hasło używane podczas tworzenia klastra.

2. Z lewej strony interfejsu użytkownika sieci web Ambari, wybierz **HDFS** , a następnie wybierz **Configs** kartę pośrodku strony.

3. Wybierz **zaawansowane** kartę, a następnie przewiń do momentu znalezienia **lokacji podstawowej niestandardowe** sekcji.

4. Rozwiń **lokacji podstawowej niestandardowe** sekcji, a następnie przewiń do zakończenia, a następnie wybierz pozycję **Dodaj właściwość...**  łącza. Użyj następujących wartości dla **klucz** i **wartość** pola:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Wartość**: Sygnatura dostępu Współdzielonego zwrócony przez C# lub wcześniej uruchomiono aplikację w języku Python

     Zastąp **CONTAINERNAME** o nazwie kontenera używany przy użyciu aplikacji języka C# lub sygnatury dostępu Współdzielonego. Zastąp **STORAGEACCOUNTNAME** nazwą konta magazynu została użyta.

5. Kliknij przycisk **Dodaj** przycisk, aby zapisać ten klucz i wartość, a następnie kliknij przycisk **Zapisz** przycisk, aby zapisać zmiany konfiguracji. Po wyświetleniu monitu, Dodaj opis zmiany ("Dodawanie dostępu do magazynu sygnatur dostępu Współdzielonego" na przykład), a następnie kliknij przycisk **Zapisz**.

    Kliknij przycisk **OK** kiedy zmiany zostały ukończone.

   > [!IMPORTANT]  
   > Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić kilka usług.

6. W sieci web Ambari interfejsu użytkownika, wybierz **systemu plików HDFS** z listy po lewej stronie, a następnie wybierz **ponowne uruchomienie wszystkich wpływ** z **działania usługi** listę rozwijaną listę po prawej stronie. Po wyświetleniu monitu wybierz __upewnij się, uruchom ponownie wszystkie__.

    Powtórz ten proces dla MapReduce2 i YARN.

7. Po ponownym uruchomieniu usługi, wybierz każdego z nich i Wyłącz tryb konserwacji z **działania usługi** listy rozwijanej.

## <a name="test-restricted-access"></a>Test z ograniczonym dostępem

Aby sprawdzić, czy mają ograniczony dostęp, za pomocą protokołu SSH Połącz się z klastrem. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia z klastrem, wykonaj następujące kroki, aby zweryfikować, możliwe jest tylko odczyt i listy elementów na koncie magazynu sygnatur dostępu Współdzielonego:

1. Aby wyświetlić listę zawartości kontenera, użyj następującego polecenia w wierszu polecenia: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Zastąp **SASCONTAINER** o nazwie kontenera utworzone dla sygnatury dostępu Współdzielonego konta magazynu. Zastąp **SASACCOUNTNAME** nazwą konta magazynu używanego dla sygnatury dostępu Współdzielonego.

    Lista zawiera plik przekazane, gdy utworzono kontener i sygnatury dostępu Współdzielonego.

2. Użyj następującego polecenia, aby sprawdzić, czy możesz odczytać zawartość pliku. Zastąp **SASCONTAINER** i **SASACCOUNTNAME** jak w poprzednim kroku. Zastąp **FILENAME** o nazwie pliku, wyświetlane w poprzednim poleceniu:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    To polecenie wyświetla zawartość pliku.

3. Aby pobrać plik do lokalnego systemu plików, użyj następującego polecenia:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    To polecenie pobiera plik w lokalnym pliku o nazwie **testfile.txt**.

4. Użyj następującego polecenia do przekazania pliku lokalnego do nowego pliku o nazwie **testupload.txt** magazynu sygnatur dostępu Współdzielonego:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Pojawi się komunikat podobny do następującego tekstu:

        put: java.io.IOException

    Ten błąd występuje, ponieważ lokalizacja magazynu jest odczytu + tylko z listy. Aby umieścić dane na domyślny magazyn dla klastra, który jest zapisywalny, użyj następującego polecenia:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Tym razem operacja powinna zakończyć się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-task-was-canceled"></a>Zadanie zostało anulowane

**Objawy**: Podczas tworzenia klastra przy użyciu skryptu programu PowerShell, mogą pojawić się następujący komunikat o błędzie:

    New-AzHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Przyczyna**: Ten błąd może wystąpić, jeśli używasz hasła dla użytkownika administracyjnego/HTTP dla klastra lub (w przypadku klastrów opartych na systemie Linux) użytkownika SSH.

**Rozwiązanie**: Użyj hasła spełniającego następujące kryteria:

* Musi być co najmniej 10 znaków.
* Musi zawierać co najmniej jedną cyfrę.
* Musi zawierać co najmniej jeden znak inny niż alfanumeryczny.
* Musi zawierać co najmniej jedną wielką lub małą literę.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się inne sposoby pracy z danymi w klastrze, skoro wiesz jak dodać ograniczony dostęp do magazynu w klastrze usługi HDInsight:

* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Używanie technologii MapReduce z HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
