---
title: Zarządzanie Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie zarządzania kont usługi Data Lake Analytics, źródła danych, zadaniami i użytkownikami przy użyciu wiersza polecenia platformy Azure
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: a78a7ea619be28f01372a7b80d3cb4a5d35bd50e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Zarządzanie Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzać kontami usługi Azure Data Lake Analytics, źródeł danych, użytkowników i zadań przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić tematy dotyczące zarządzania przy użyciu innych narzędzi, kliknij łącze powyżej.


**Wymagania wstępne**

Przed rozpoczęciem tego samouczka wymagane są następujące zasoby:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Zobacz temat [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Aby ukończyć ten przewodnik, pobierz i zainstaluj **wersję wstępną** [narzędzi interfejsu wiersza polecenia platformy Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Uwierzytelnianie przy użyciu `az login` poleceń i wybierz subskrypcję, która ma być używany. Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Można teraz uzyskać dostępu do poleceń usługi Data Lake Analytics i Data Lake Store. Uruchom następujące polecenie, aby wyświetlić listę poleceń usługi Data Lake Store i usługi Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Zarządzanie kontami

Przed uruchomieniem zadania usługi Data Lake Analytics, musisz mieć konto usługi Data Lake Analytics. W przeciwieństwie do usługi Azure HDInsight nie płatności dla konta usługi Analytics zadania nie jest uruchomiona. Płacisz tylko za czas, gdy jest uruchomione zadanie.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Tworzenie kont

Uruchom następujące polecenie, aby utworzyć konto usługi Data Lake 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizowanie kont

Polecenie aktualizuje właściwości istniejącego konta Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Wyświetlanie listy kont

Lista Data Lake Analytics kont w ramach określonej grupy zasobów

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Uzyskiwanie szczegółowych informacji o koncie

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Usuń konto

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Zarządzaj źródłami danych

Data Lake Analytics obsługuje obecnie następujących dwóch źródeł danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Podczas tworzenia konta usługi Analytics należy wyznaczyć konta usługi Azure Data Lake Storage jako domyślne konto magazynu. Data Lake domyślne konto magazynu jest używany do przechowywania metadanych i zadanie inspekcji z dziennikami zadań. Po utworzeniu konta usługi Analytics można dodać dodatkowe konta usługi Data Lake Storage i/lub konta usługi Magazyn Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Znajdź domyślnego konta usługi Data Lake Store

Możesz wyświetlić domyślne konto usługi Data Lake Store używane przez uruchomienie `az dla account show` polecenia. Domyślna nazwa konta jest wymieniony w obszarze właściwości defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Dodawanie dodatkowych kont magazynu obiektów Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Są obsługiwane tylko obiektu Blob magazynu krótkiej nazwy. Nie używaj nazwy FQDN, na przykład "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Dodawanie dodatkowych kont usługi Data Lake Store

Polecenie aktualizuje określonego konta usługi Data Lake Analytics przy użyciu konta usługi Data Lake Store, dodatkowe:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizowanie istniejącego źródła danych

Aby zaktualizować istniejący klucz konta magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista źródeł danych:

Aby wyświetlić listę kont usługi Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Aby wyświetlić listę konta magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics listy źródła danych](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Usuń źródła danych:
Aby usunąć konto usługi Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Aby usunąć konto magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Zarządzanie zadaniami
Aby można było utworzyć zadanie, musisz mieć konto usługi Data Lake Analytics.  Aby uzyskać więcej informacji, zobacz [kont zarządzania usługi Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Lista zadań

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics listy źródła danych](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Uzyskiwanie szczegółów zadania

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Przesyłanie zadań

> [!NOTE]
> Domyślnie priorytet zadania wynosi 1000, a domyślny stopień równoległości zadania jest 1.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Anulowanie zadań
Aby znaleźć identyfikator zadania, a następnie użyć Anuluj, aby anulować zadanie, należy użyć polecenia listy.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="use-azure-resource-manager-groups"></a>Użyj grup usługi Azure Resource Manager
Aplikacje składają się zazwyczaj z wielu składników, na przykład aplikacji sieci Web, bazy danych, serwera bazy danych, magazynu i usług firm zewnętrznych. Usługa Azure Resource Manager umożliwia pracę z zasobami w aplikacji w grupie, określany jako grupa zasobów platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby aplikacji w jednej, skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Można również uprościć rozliczenia w organizacji, wyświetlając zestawienia kosztów całej grupy. Aby uzyskać więcej informacji, zobacz temat [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Usługa Data Lake Analytics może obejmować następujące składniki:

* Usługa Azure Data Lake Analytics konta
* Wymagane domyślne konto usługi Azure Data Lake Storage
* Dodatkowe usługi Azure Data Lake kont magazynu
* Dodatkowe konta usługi Azure Storage

Możesz utworzyć te składniki w jednej grupy Resource Manager, aby ułatwić zarządzanie.

![Usługa Azure Data Lake Analytics konta i magazynu](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Konto usługi Data Lake Analytics i kont magazynu zależnego muszą znajdować się w tym samym centrum danych Azure.
Jednak grupy Resource Manager może znajdować się w centrum danych.  

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

