---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak używać interfejsu wiersza polecenia platformy Azure do zarządzania zadaniami usługi Data Lake Analytics, źródłami danych, & użytkownikami.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 69a48952ef273acb8cf7eb0ec5968e12b962b622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454367"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzać kontami usługi Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu interfejsu wiersza polecenia platformy Azure. Aby wyświetlić tematy zarządzania przy użyciu innych narzędzi, kliknij kartę wybierz powyżej.


**Wymagania wstępne**

Przed rozpoczęciem tego samouczka należy mieć następujące zasoby:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Interfejsu wiersza polecenia platformy Azure. Zobacz temat [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Aby ukończyć ten przewodnik, pobierz i zainstaluj **wersję wstępną** [narzędzi interfejsu wiersza polecenia platformy Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Uwierzytelnij się `az login` za pomocą polecenia i wybierz subskrypcję, której chcesz użyć. Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Teraz możesz uzyskać dostęp do poleceń Data Lake Analytics i Data Lake Store. Uruchom następujące polecenie, aby wyświetlić listę poleceń Data Lake Store i Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Zarządzanie kontami

Przed uruchomieniem jakichkolwiek zadań usługi Data Lake Analytics musisz mieć konto Usługi Data Lake Analytics. W przeciwieństwie do usługi Azure HDInsight nie płacisz za konto Analytics, gdy nie jest ono uruchomione. Płacisz tylko za czas, kiedy jest uruchomiony zadanie.  Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Tworzenie kont

Uruchom następujące polecenie, aby utworzyć konto usługi Data Lake, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizowanie kont

Następujące polecenie aktualizuje właściwości istniejącego konta usługi Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Wyświetlanie listy kont

Wyświetlanie listy kont usługi Data Lake Analytics w określonej grupie zasobów

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Uzyskaj szczegółowe informacje o koncie

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Usuwanie konta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Usługa Data Lake Analytics obsługuje obecnie następujące dwa źródła danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Podczas tworzenia konta Analytics, należy wyznaczyć konto usługi Azure Data Lake Storage, aby być domyślnym kontem magazynu. Domyślne konto magazynu usługi Data Lake służy do przechowywania metadanych zadań i dzienników inspekcji zadań. Po utworzeniu konta Analytics można dodać dodatkowe konta usługi Data Lake Storage i/lub konto usługi Azure Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Znajdowanie domyślnego konta w sklepie Data Lake Store

Można wyświetlić domyślne konto usługi Data `az dla account show` Lake Store używane przez uruchomienie polecenia. Domyślna nazwa konta jest wyświetlana w obszarze defaultDataLakeStoreAccount właściwości.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Dodawanie dodatkowych kont magazynu obiektów Blob

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Obsługiwane są tylko krótkie nazwy magazynu obiektów blob. Nie używaj FQDN, na przykład "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Dodawanie dodatkowych kont w magazynie Data Lake Store

Następujące polecenie aktualizuje określone konto usługi Data Lake Analytics za pomocą dodatkowego konta w sklepie Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Aktualizowanie istniejącego źródła danych

Aby zaktualizować istniejący klucz konta magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista źródeł danych:

Aby wyświetlić listę kont w magazynie Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Aby wyświetlić listę konta magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Źródło danych listy Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Usuwanie źródeł danych:
Aby usunąć konto w sklepie Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Aby usunąć konto magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Zarządzanie zadaniami
Aby utworzyć zadanie, musisz mieć konto Usługi Data Lake Analytics.  Aby uzyskać więcej informacji, zobacz [Zarządzanie kontami usługi Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Lista zadań

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Źródło danych listy Data Lake Analytics](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Uzyskaj informacje o pracy

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Przesyłanie ofert pracy

> [!NOTE]
> Domyślnym priorytetem zadania jest 1000, a domyślny stopień równoległości dla zadania wynosi 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Anuluj zadania
Użyj polecenia listy, aby znaleźć identyfikator zadania, a następnie użyj anuluj, aby anulować zadanie.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Potoki i cykle

**Uzyskaj informacje na temat potoków i cykli**

Użyj poleceń `az dla job pipeline`, aby wyświetlić informacje o potoku wcześniej przesłanych zadań.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Użyj poleceń `az dla job recurrence`, aby wyświetlić informacje o cyklu wcześniej przesłanych zadań.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Zobacz też
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-manage-use-portal.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

