---
title: Ograniczanie dostępu przy użyciu sygnatury dostępu współdzielonego - Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć HDInsight dostępu do danych przechowywanych w obiektach blob magazynu Azure za pomocą sygnatury dostępu współdzielonego.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: d3e37ba0f590cf0572b84a53bdd407af63a19d36
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Użyj sygnatury dostępu współdzielonego magazynu Azure, aby ograniczyć dostęp do danych w usłudze HDInsight

HDInsight ma pełny dostęp do danych na kontach magazynu Azure skojarzony z klastrem. Sygnatury dostępu współdzielonego w kontenerze obiektów blob służy do ograniczania dostępu do danych. Na przykład, aby zapewnić dostęp tylko do odczytu do danych. Udostępniony sygnatur dostępu (SAS) to funkcja kontami magazynu Azure, która umożliwia ograniczenie dostępu do danych. Na przykład zapewniając dostęp do danych tylko do odczytu.

> [!IMPORTANT]
> Jako rozwiązanie przy użyciu Apache zakres należy rozważyć użycie HDInsight przyłączonych do domeny. Aby uzyskać więcej informacji, zobacz [HDInsight przyłączonych do domeny skonfiguruj](./domain-joined/apache-domain-joined-configure.md) dokumentu.

> [!WARNING]
> HDInsight musi mieć pełny dostęp do magazynu domyślnego dla klastra.

## <a name="requirements"></a>Wymagania

* Subskrypcja platformy Azure
* C# lub języka Python. Przykład kodu C# stanowi rozwiązanie Visual Studio.

  * Visual Studio musi być w wersji 2013, 2015 lub 2017
  * Python musi być w wersji 2.7 lub wyższej

* Klaster usługi HDInsight opartej na systemie Linux lub [programu Azure PowerShell] [ powershell] — Jeśli masz istniejący klaster opartych na systemie Linux, można użyć narzędzia Ambari do dodania do klastra sygnaturę dostępu współdzielonego. Jeśli nie używasz programu Azure PowerShell do utworzenia klastra i dodać sygnaturę dostępu współdzielonego podczas tworzenia klastra.

    > [!IMPORTANT]
    > Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

* Przykład plików ze [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). To repozytorium zawiera następujące elementy:

  * Projektu programu Visual Studio, który można utworzyć kontenera magazynu, zapisane zasady i skojarzenia zabezpieczeń do użycia z usługą HDInsight
  * Skrypt w języku Python, który można utworzyć kontenera magazynu, zapisane zasady i skojarzenia zabezpieczeń do użycia z usługą HDInsight
  * Skrypt programu PowerShell umożliwiające tworzenie klastra usługi HDInsight i skonfigurować go do używania sygnatury dostępu Współdzielonego.

## <a name="shared-access-signatures"></a>Sygnatury dostępu współdzielonego

Istnieją dwa rodzaje sygnatur dostępu współdzielonego:

* Ad hoc: godzina rozpoczęcia, czas wygaśnięcia i uprawnienia do sygnatury dostępu Współdzielonego są określone w identyfikatorze URI sygnatury dostępu Współdzielonego.

* Przechowywane zasad dostępu: zasady dostępu do przechowywanych jest zdefiniowany w kontenerze zasobów, takich jak kontenera obiektów blob. Zasady mogą służyć do zarządzania ograniczeniami dla jednego lub więcej sygnatur dostępu współdzielonego. Po skojarzeniu sygnatury dostępu Współdzielonego za pomocą zasad dostępu przechowywane, sygnatury dostępu Współdzielonego dziedziczy ograniczenia — czas rozpoczęcia, czas wygaśnięcia i uprawnienia - zdefiniowane zasady dostępu przechowywane.

Różnica między dwoma formularzami jest ważne dla klucza scenariusz: odwołania. Sygnatury dostępu Współdzielonego jest adres URL, więc każdy, kto uzyskuje sygnatury dostępu Współdzielonego, można użyć niezależnie od tego, kto zażądał rozpoczynać się od znaku. Sygnatury dostępu Współdzielonego zostanie opublikowana publicznie, może służyć wszystkim osobom na świecie. Sygnatury dostępu Współdzielonego, który jest rozpowszechniany jest prawidłowy, dopóki jedna z następujących czterech zdarzeń sytuacji:

1. Czas wygaśnięcia określony na sygnatury dostępu Współdzielonego zostanie osiągnięty.

2. Czas wygaśnięcia określone w zasadach dostępu składowana odwołuje się sygnatury dostępu Współdzielonego zostanie osiągnięty. Następujące scenariusze spowodować czas wygaśnięcia osiągnąć:

    * Upłynął limit czasu interwału.
    * Zasady dostępu przechowywane są modyfikowane w celu czas wygaśnięcia w przeszłości. Zmiana czasu wygaśnięcia jest jednym ze sposobów odwołać dostępu Współdzielonego.

3. Zasady dostępu składowana odwołuje się sygnatury dostępu Współdzielonego jest usunięte, sposób, aby można było odwołać dostępu Współdzielonego. Jeśli możesz ponownie utworzyć zasady dostępu do przechowywanych o takiej samej nazwie, wszystkie tokeny sygnatury dostępu Współdzielonego dla poprzednich zasad są prawidłowe, (Jeśli nie minął czas wygaśnięcia na sygnatury dostępu Współdzielonego). Jeśli zamierzasz odwołać dostępu Współdzielonego, należy użyć innej nazwy, jeśli należy ponownie utworzyć zasady dostępu z upływem czasu w przyszłości.

4. Ponownego wygenerowania klucza konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego. Trwa ponowne generowanie klucza powoduje, że wszystkie aplikacje korzystające z poprzedniego klucza niepowodzenia uwierzytelniania. Zaktualizuj wszystkie składniki do nowego klucza.

> [!IMPORTANT]
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta używane do tworzenia podpisu i skojarzonych przechowywane zasad dostępu (jeśli istnieje). Jeśli określono żadnych zasad dostępu przechowywane, jedynym sposobem, aby odwołać sygnatury dostępu współdzielonego jest zmienić klucz konta.

Firma Microsoft zaleca zawsze używać zasad dostępu przechowywane. Podczas korzystania z zasady przechowywane, możesz odwołać podpisów lub przedłużyć datę wygaśnięcia, zgodnie z potrzebami. Kroki opisane w tym dokumencie przechowywane zasady dostępu do generowania sygnatury dostępu Współdzielonego.

Aby uzyskać więcej informacji dotyczących sygnatur dostępu współdzielonego, zobacz [opis modelu sygnatur dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Tworzenie zasad przechowywane i sygnatury dostępu Współdzielonego, za pomocą C\#

1. Otwórz rozwiązanie w programie Visual Studio.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SASToken** projekt i wybierz **właściwości**.

3. Wybierz **ustawienia** i Dodaj wartości dla następujących pozycji:

   * StorageConnectionString: Parametry połączenia dla konta magazynu, który chcesz utworzyć zasady przechowywane i sygnatury dostępu Współdzielonego dla. Format powinien być `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` gdzie `myaccount` jest nazwą konta magazynu i `mykey` klucza dla konta magazynu.

   * Właściwość ContainerName: Kontener na koncie magazynu, który chcesz ograniczyć dostęp do.

   * SASPolicyName: Nazwa do użycia dla przechowywanych zasad do utworzenia.

   * FileToUpload: Ścieżka do pliku, który jest przekazywany do kontenera.

4. Uruchom projekt. Po wygenerowaniu sygnatury dostępu Współdzielonego, są wyświetlane informacje podobne do następującego tekstu:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Zapisz zasady tokenu sygnatury dostępu Współdzielonego, nazwa konta magazynu i nazwa kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastra usługi HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Tworzenie zasad przechowywane i SAS używany język Python

1. Otwórz plik SASToken.py i Zmień następujące wartości:

   * zasady\_Nazwa: Nazwa do użycia dla przechowywanych zasad do utworzenia.

   * Magazyn\_konta\_Nazwa: Nazwa konta magazynu.

   * Magazyn\_konta\_klucz: klucz dla konta magazynu.

   * Magazyn\_kontenera\_name: kontenera na koncie magazynu, który chcesz ograniczyć dostęp do.

   * przykład\_pliku\_ścieżka: ścieżka do pliku, który jest przekazywany do kontenera.

2. Uruchom skrypt. Po ukończeniu działania skryptu Wyświetla tokenu sygnatury dostępu Współdzielonego podobny do następującego tekstu:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Zapisz zasady tokenu sygnatury dostępu Współdzielonego, nazwa konta magazynu i nazwa kontenera. Te wartości są używane podczas kojarzenia konta magazynu z klastra usługi HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Użyj sygnatury dostępu Współdzielonego z usługą HDInsight

Podczas tworzenia klastra usługi HDInsight, należy określić konto magazynu głównego i opcjonalnie możesz określić dodatkowe konta magazynu. Obie te metody dodawania magazynu wymagają pełnego dostępu do konta magazynu i kontenerów, które są używane.

Aby użyć sygnaturę dostępu współdzielonego, aby ograniczyć dostęp do kontenera, należy dodać wpis niestandardowych do **lokacji podstawowej** konfiguracji klastra.

* Aby uzyskać **opartych na systemie Windows** lub **opartych na systemie Linux** klastrów usługi HDInsight, można dodać wpisu podczas tworzenia klastra przy użyciu programu PowerShell.
* Aby uzyskać **opartych na systemie Linux** klastrów usługi HDInsight zmiany konfiguracji po utworzeniu klastra przy użyciu narzędzia Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Tworzenie klastra, który wykorzystuje sygnatury dostępu Współdzielonego

Przykład tworzenia klastra usługi HDInsight, która używa sygnatury dostępu Współdzielonego jest uwzględniona w `CreateCluster` katalogu repozytorium. Aby go użyć, wykonaj następujące kroki:

1. Otwórz `CreateCluster\HDInsightSAS.ps1` plik w edytorze tekstu i zmodyfikuj następujące wartości na początku dokumentu.

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

    Na przykład zmienić `'mycluster'` nazwę klastra, w którym chcesz utworzyć. Wartości SAS powinna odpowiadać wartości z poprzednich kroków, podczas tworzenia konta magazynu i tokenu sygnatury dostępu Współdzielonego.

    Po zmianie wartości, Zapisz plik.

2. Otwórz nowy wiersz programu Azure PowerShell. Jeśli masz doświadczenia w pracy z programem Azure PowerShell lub nie został zainstalowany, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell][powershell].

1. W wierszu polecenia wpisz następujące polecenie do uwierzytelniania do subskrypcji platformy Azure:

    ```powershell
    Login-AzureRmAccount
    ```

    Po wyświetleniu monitu zaloguj się za pomocą konta dla subskrypcji platformy Azure.

    Jeśli konto jest skojarzone z wieloma subskrypcjami platformy Azure, może być konieczne użycie `Select-AzureRmSubscription` do Wybierz subskrypcję, o których chcesz użyć.

4. W wierszu polecenia przejdź do `CreateCluster` katalog zawierający plik HDInsightSAS.ps1. Następnie użyj następującego polecenia do uruchomienia skryptu

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Jak skrypt jest uruchamiany, rejestruje dane wyjściowe do wiersza polecenia programu PowerShell podczas tworzenia zasobu konta grupy i magazynu. Monit o wprowadzenie użytkownika HTTP dla klastra usługi HDInsight. To konto jest używane do zabezpieczania dostępu protokołu HTTP/s do klastra.

    W przypadku tworzenia opartych na systemie Linux klaster, zostanie wyświetlony monit o nazwę konta użytkownika SSH i hasło. To konto jest używane do zdalnego logowania do klastra.

   > [!IMPORTANT]
   > Po wyświetleniu monitu dla protokołu HTTP/s lub nazwa użytkownika SSH i hasło, należy podać hasło, które spełnia następujące kryteria:
   >
   > * Musi być co najmniej 10 znaków
   > * Musi zawierać co najmniej jedną cyfrę
   > * Musi zawierać co najmniej jeden znak inny niż alfanumeryczny
   > * Musi zawierać co najmniej jedną wielką lub małą literę

Trwa trochę czasu zanim ten skrypt zakończyć, zazwyczaj około 15 minut. Po ukończeniu działania skryptu bez żadnych błędów, klaster został utworzony.

### <a name="use-the-sas-with-an-existing-cluster"></a>Użyj sygnatury dostępu Współdzielonego z istniejącego klastra

Jeśli masz istniejący klaster opartych na systemie Linux, można dodać dostępu Współdzielonego w celu **lokacji podstawowej** konfiguracji przy użyciu następujących kroków:

1. Otwórz Ambari web UI dla klastra. Adres na tej stronie jest https://YOURCLUSTERNAME.azurehdinsight.net. Po wyświetleniu monitu uwierzytelniania w klastrze za pomocą nazwy admin (Administrator) i hasło użyte podczas tworzenia klastra.

2. Po lewej stronie interfejsu użytkownika sieci web Ambari, wybierz **HDFS** , a następnie wybierz **Configs** kartę środku strony.

3. Wybierz **zaawansowane** , a następnie przewiń do momentu znalezienia **niestandardowe core-site** sekcji.

4. Rozwiń węzeł **niestandardowe core-site** sekcji, a następnie przewiń do zakończenia i wybierz **Dodaj właściwość...**  łącza. Użyj poniższych wartości **klucza** i **wartość** pola:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Wartość**: SAS zwrócony przez aplikację języka C# lub Python został przeprowadzony wcześniej

     Zastąp **CONTAINERNAME** o nazwie kontenera używane w aplikacji C# lub SAS. Zastąp **STORAGEACCOUNTNAME** nazwą konta magazynu, należy użyć.

5. Kliknij przycisk **Dodaj** przycisk, aby zapisać ten klucz i wartość, a następnie kliknij przycisk **zapisać** przycisk, aby zapisać zmiany konfiguracji. Po wyświetleniu monitu, Dodaj opis zmian ("Dodawanie dostępu do pamięci masowej SAS" na przykład), a następnie kliknij przycisk **zapisać**.

    Kliknij przycisk **OK** po zakończeniu zmiany.

   > [!IMPORTANT]
   > Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić kilka usług.

6. Ambari web UI, wybierz **HDFS** na liście z lewej strony, a następnie wybierz **ponowne uruchomienie wszystkich wpływ** z **akcji usługi** listy rozwijanej liście po prawej stronie. Po wyświetleniu monitu wybierz __upewnij się, uruchom ponownie wszystkie__.

    Powtórz ten proces dla MapReduce2 i YARN.

7. Po ponownym uruchomieniu usługi, wybierz każdego z nich i Wyłącz tryb konserwacji z **akcji usługi** listy rozwijanej.

## <a name="test-restricted-access"></a>Testowanie dostęp ograniczony

Aby sprawdzić, czy mają ograniczony dostęp do, użyj następujących metod:

* Aby uzyskać **opartych na systemie Windows** klastrów usługi HDInsight, połącz się z klastrem za pomocą pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługi HDInsight za pomocą protokołu RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

    Po nawiązaniu połączenia użyj **Hadoop wiersza polecenia** ikony na pulpicie, aby otworzyć wiersz polecenia.

* Aby uzyskać **opartych na systemie Linux** klastrów usługi HDInsight używanie protokołu SSH, aby połączyć się z klastrem. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Po nawiązaniu połączenia z klastrem, wykonaj następujące kroki, aby sprawdzić możliwe tylko odczyt i listy elementów na koncie magazynu SAS:

1. Aby wyświetlić listę zawartości kontenera, użyj następującego polecenia w wierszu: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Zastąp **SASCONTAINER** o nazwie kontenera utworzona dla konta magazynu SAS. Zastąp **SASACCOUNTNAME** z nazwą konta magazynu używany dla sygnatury dostępu Współdzielonego.

    Lista zawiera plik przekazany po utworzenia kontenera i sygnatury dostępu Współdzielonego.

2. Użyj następującego polecenia, aby sprawdzić, czy można odczytać zawartości pliku. Zastąp **SASCONTAINER** i **SASACCOUNTNAME** jak w poprzednim kroku. Zastąp **FILENAME** o nazwie pliku wyświetlanego w poprzednim poleceniu:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    To polecenie wyświetla zawartość pliku.

3. Aby pobrać plik do lokalnego systemu plików, użyj następującego polecenia:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    To polecenie pobiera plik do lokalnego pliku o nazwie **plik_testowy.txt**.

4. Użyj następującego polecenia do przekazania pliku lokalnego do nowego pliku o nazwie **testupload.txt** w pamięci masowej SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Pojawi się komunikat podobny do następującego tekstu:

        put: java.io.IOException

    Ten błąd występuje, ponieważ lokalizacja magazynu jest odczytu + tylko z listy. Umieszczanie danych na domyślny magazyn dla klastra, który jest zapisywalny, użyj następującego polecenia:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Tym razem operacja powinna zakończyć się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="a-task-was-canceled"></a>Zadanie zostało anulowane

**Objawy**: podczas tworzenia klastra przy użyciu skryptu programu PowerShell, może zostać wyświetlony następujący komunikat o błędzie:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Przyczyna**: ten błąd może wystąpić, jeśli używasz hasła dla użytkownika administracyjnego/HTTP dla klastra lub (w przypadku klastrów z systemem Linux) użytkownika SSH.

**Rozdzielczość**: Użyj hasła, które spełnia następujące kryteria:

* Musi być co najmniej 10 znaków
* Musi zawierać co najmniej jedną cyfrę
* Musi zawierać co najmniej jeden znak inny niż alfanumeryczny
* Musi zawierać co najmniej jedną wielką lub małą literę

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz jak dodać magazyn ograniczonego dostępu do klastra usługi HDInsight, Dowiedz się inne sposoby pracy z danymi w klastrze:

* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystać z usługi MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
