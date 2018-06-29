---
title: Wprowadzenie do usługi Azure Data Lake magazynu Gen1 przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Tworzenie konta usługi Data Lake Store i wykonywanie podstawowych operacji przy użyciu programu Azure PowerShell
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: f208722d768e2bccf2e5b4d7b4543f8cbba4f185
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036613"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Wprowadzenie do usługi Azure Data Lake magazynu Gen1 przy użyciu programu Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak przy użyciu programu Azure PowerShell utworzyć konto usługi Azure Data Lake Store i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji o usłudze Data Lake Store, zobacz [omówienie z Data Lake magazynu Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Program Azure PowerShell 1.0 lub nowszy**. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
W tym artykule użyto prostszej metody uwierzytelniania w usłudze Data Lake Store, w przypadku której jest wyświetlany monit o wprowadzenie poświadczeń konta platformy Azure. Poziom dostępu do konta i systemu plików usługi Data Lake Store jest określany przez poziom dostępu zalogowanego użytkownika. Istnieją jednak inne metody uwierzytelniania w usłudze Data Lake Store: **uwierzytelnianie użytkowników końcowych** i **uwierzytelnianie między usługami**. Aby uzyskać instrukcje i więcej informacji na temat uwierzytelniania, zobacz [Uwierzytelnianie użytkowników końcowych](data-lake-store-end-user-authenticate-using-active-directory.md) lub [Uwierzytelnianie między usługami](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Tworzenie konta usługi Azure Data Lake Store
1. Z poziomu pulpitu otwórz nowe okno programu Windows PowerShell. Wprowadź poniższy fragment kodu, aby zalogować się na konto platformy Azure, skonfigurować subskrypcję i zarejestrować dostawcę usługi Data Lake Store. Po wyświetleniu monitu zaloguj się jako jeden z administratorów/właścicieli subskrypcji:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Konto usługi Azure Data Lake Store jest skojarzone z grupą zasobów platformy Azure. Rozpocznij od utworzenia grupy zasobów platformy Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Tworzenie grupy zasobów platformy Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Tworzenie grupy zasobów platformy Azure")
3. Utwórz konto usługi Azure Data Lake Store. Wybrana nazwa konta może zawierać tylko małe litery i cyfry.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Tworzenie konta usługi Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Tworzenie konta usługi Azure Data Lake Store")
4. Sprawdź, czy konto zostało utworzone pomyślnie.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Dane wyjściowe polecenia cmdlet powinny mieć wartość **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Tworzenie struktur katalogów w usłudze Azure Data Lake Store
W ramach konta usługi Azure Data Lake Store można tworzyć katalogi w celu przechowywania danych i zarządzania nimi.

1. Określ katalog główny.

        $myrootdir = "/"
2. Utwórz nowy katalog o nazwie **mynewdirectory** w określonym katalogu głównym.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Sprawdź, czy nowy katalog został utworzony pomyślnie.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Powinny być widoczne dane wyjściowe pokazane na poniższym zrzucie ekranu:

    ![Weryfikowanie katalogu](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Weryfikowanie katalogu")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Przekazywanie danych do usługi Azure Data Lake Store
Dane można przekazywać do usługi Data Lake Store bezpośrednio do katalogu głównego lub do katalogu utworzonego w ramach konta. Fragmenty kodu w tej sekcji przedstawiają sposób przekazywania przykładowych danych do katalogu (**mynewdirectory**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Zmienianie nazwy, pobieranie i usuwanie danych z usługi Data Lake Store
Aby zmienić nazwę pliku, użyj następującego polecenia:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Aby pobrać plik, użyj następującego polecenia.

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Aby usunąć plik, użyj następującego polecenia:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Po wyświetleniu monitu wpisz **Y**, aby usunąć element. Jeśli masz więcej niż jeden plik do usunięcia, możesz podać wszystkie ścieżki oddzielone przecinkami.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Usuwanie konta usługi Azure Data Lake Store
Użyj poniższego polecenia, aby usunąć konto usługi Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Kolejne kroki
* [Wskazówki dotyczące dostrajania wydajności w przypadku używania programu PowerShell z usługą Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Używanie usługi Azure Data Lake Store w zastosowaniach wymagających danych big data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
