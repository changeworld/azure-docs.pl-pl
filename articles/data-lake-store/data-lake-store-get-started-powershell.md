---
title: Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Tworzenie konta usługi Azure Data Lake Storage Gen1 i wykonywać podstawowe operacje przy użyciu programu Azure PowerShell
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 5bec627f114a20033ca4364c39c048763df36b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161488"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Rozpoczynanie pracy z usługą Azure Data Lake Storage Gen1 przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak utworzyć konto usługi Azure Data Lake Storage Gen1 i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych przy użyciu programu Azure PowerShell, usuwanie konta itp. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1 zobacz [przegląd z Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Tym artykule użyto prostszej metody uwierzytelniania w usłudze Data Lake magazynu Gen1, gdy zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure. Poziom dostępu do magazynu Gen1 jeziora danych, konta i systemu plików jest określany przez poziom dostępu zalogowanego użytkownika. Istnieją inne metody uwierzytelniania w usłudze Data Lake Storage Gen1, które są **uwierzytelnianie użytkowników końcowych** lub **service-to-service authentication**. Instrukcje i dodatkowe informacje na temat uwierzytelniania można znaleźć w następujących artykułach: [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Uwierzytelnianie użytkowników końcowych) lub [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Uwierzytelnianie między usługami).

## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta Data Lake Storage Gen1
1. Z poziomu pulpitu otwórz nowe okno programu Windows PowerShell. Wprowadź poniższy fragment kodu, aby zalogować się do konta platformy Azure, skonfigurować subskrypcję i zarejestrować dostawcę Data Lake Storage Gen1. Po wyświetleniu monitu, aby zalogować się, upewnij się, że logujesz się jako jeden z administratorów/właściciel subskrypcji:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Konta Data Lake Storage Gen1 jest skojarzona z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Tworzenie grupy zasobów platformy Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Tworzenie grupy zasobów platformy Azure")
3. Tworzenie konta Data Lake Storage Gen1. Wybrana nazwa konta może zawierać tylko małe litery i cyfry.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Tworzenie konta Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Tworzenie konta Data Lake Storage Gen1")
4. Sprawdź, czy konto zostało utworzone pomyślnie.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Dane wyjściowe polecenia cmdlet powinny mieć wartość **True**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Tworzenie struktur katalogów na koncie usługi Data Lake Storage Gen1
Można tworzyć katalogi w ramach konta usługi Data Lake Storage Gen1 do przechowywania danych i zarządzania nimi.

1. Określ katalog główny.

        $myrootdir = "/"
2. Utwórz nowy katalog o nazwie **mynewdirectory** w określonym katalogu głównym.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Sprawdź, czy nowy katalog został utworzony pomyślnie.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Powinny być widoczne dane wyjściowe pokazane na poniższym zrzucie ekranu:

    ![Weryfikowanie katalogu](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Weryfikowanie katalogu")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Przekazywanie danych do konta usługi Data Lake Storage Gen1
Możesz przekazać dane do Data Lake Storage Gen1 bezpośrednio na poziomie katalogu głównego lub do katalogu, który został utworzony w ramach konta. Fragmenty kodu w tej sekcji przedstawiają sposób przekazywania przykładowych danych do katalogu (**mynewdirectory**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Zmienianie nazwy, pobieranie i usuwanie danych z konta usługi Data Lake Storage Gen1
Aby zmienić nazwę pliku, użyj następującego polecenia:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Aby pobrać plik, użyj następującego polecenia.

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Aby usunąć plik, użyj następującego polecenia:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Po wyświetleniu monitu wpisz **Y**, aby usunąć element. Jeśli masz więcej niż jeden plik do usunięcia, możesz podać wszystkie ścieżki oddzielone przecinkami.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Usuwanie konta usługi Data Lake Storage Gen1
Użyj następującego polecenia, aby usunąć konto usługi Data Lake Storage Gen1.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Kolejne kroki
* [Wskazówki dotyczące przy użyciu programu PowerShell przy użyciu usługi Azure Data Lake Storage Gen1 dostrajania wydajności](data-lake-store-performance-tuning-powershell.md)
* [Użyj usługi Azure Data Lake Storage Gen1 dla wymagających danych big Data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
