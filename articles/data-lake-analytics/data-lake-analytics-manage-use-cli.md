---
title: Zarządzanie usługi Azure Data Lake Analytics przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym artykule opisano, jak zarządzać, zadania, źródła danych i użytkowników usługi Data Lake Analytics przy użyciu wiersza polecenia platformy Azure.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fa7d46d45c350435c0ffba8f3755ad8bea651c3e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387062"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Zarządzanie przy użyciu interfejsu wiersza polecenia platformy Azure (CLI) usługi Azure Data Lake Analytics

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Dowiedz się, jak zarządzać kontami usługi Azure Data Lake Analytics, źródła danych, użytkowników i zadań przy użyciu wiersza polecenia platformy Azure. Aby wyświetlić tematy dotyczące zarządzania przy użyciu innych narzędzi, kliknij łącze powyżej.


**Wymagania wstępne**

Przed rozpoczęciem tego samouczka, musisz mieć następujące zasoby:

* Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* Interfejs wiersza polecenia platformy Azure. Zobacz temat [Instalowanie i konfigurowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Aby ukończyć ten przewodnik, pobierz i zainstaluj **wersję wstępną** [narzędzi interfejsu wiersza polecenia platformy Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases).

* Uwierzytelnianie przy użyciu `az login` poleceń i wybierz subskrypcję, której chcesz użyć. Więcej informacji na temat uwierzytelniania za pomocą konta służbowego lub szkolnego znajdziesz w temacie [Połączenie z subskrypcją platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Możesz teraz uzyskać dostęp do poleceń usługi Data Lake Analytics i Data Lake Store. Uruchom następujące polecenie, aby wyświetlić listę poleceń Data Lake Store i Data Lake Analytics:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Zarządzanie kontami

Przed uruchomieniem zadania usługi Data Lake Analytics, musisz mieć konto usługi Data Lake Analytics. W przeciwieństwie do usługi Azure HDInsight nie płać za konto usługi Analytics nie działa zadanie. Płaci się tylko raz, gdy działa zadanie.  Aby uzyskać więcej informacji, zobacz [Azure Data Lake Analytics Przegląd](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Tworzenie kont

Uruchom następujące polecenie, aby utworzyć konto usługi Data Lake 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Aktualizowanie kont

Następujące polecenie aktualizuje właściwości istniejącego konta usługi Data Lake Analytics

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Wyświetlanie listy kont

Lista Data Lake Analytics kont w ramach określonej grupie zasobów

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Pobierz szczegóły konta

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Usuwanie konta

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Zarządzanie źródłami danych

Usługa Data Lake Analytics obsługuje obecnie następujących dwóch źródeł danych:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Podczas tworzenia konta usługi Analytics, należy najpierw wyznaczyć konta usługi Azure Data Lake Storage jako domyślne konto magazynu. Usługa Data Lake domyślne konto magazynu służy do przechowywania dzienników inspekcji metadanych i zadania zadanie. Po utworzeniu konta usługi Analytics można dodać dodatkowe konta usługi Data Lake Storage i/lub konta usługi Azure Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Znajdowanie domyślnego konta Data Lake Store

Możesz wyświetlić domyślnego konta Data Lake Store używane przez uruchomienie `az dla account show` polecenia. Domyślna nazwa konta znajduje się w obszarze właściwości defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Dodawanie dodatkowych kont usługi Blob storage

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Tylko obiekt Blob magazynu krótkich nazw są obsługiwane. Nie używaj nazwy FQDN, na przykład "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Dodać dodatkowe konta Data Lake Store

Następujące polecenie aktualizuje określone konto usługi Data Lake Analytics przy użyciu dodatkowego konta Data Lake Store:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Zaktualizuj istniejące źródło danych

Aby zaktualizować istniejący klucz konta magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista źródeł danych:

Aby wyświetlić listę kont Data Lake Store:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Aby wyświetlić listę obiektów Blob konta magazynu:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Usługa Data Lake Analytics listy źródła danych](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Usuń źródła danych:
Aby usunąć konta Data Lake Store:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Aby usunąć konto magazynu obiektów Blob:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Zarządzanie zadaniami
Aby można było utworzyć zadanie, musi mieć konto usługi Data Lake Analytics.  Aby uzyskać więcej informacji, zobacz [konta Zarządzanie Data Lake Analytics](#manage-accounts).

### <a name="list-jobs"></a>Lista zadań

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Usługa Data Lake Analytics listy źródła danych](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Pobierz szczegóły zadania

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Przesyłanie zadań

> [!NOTE]
> Domyślny priorytet zadania wynosi 1000, a domyślny stopień równoległości zadania jest 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Anuluj zadania
Użyj polecenia listy, aby znaleźć identyfikator zadania, a następnie użyj Anuluj, aby anulować zadania.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Potoki i cykle

**Uzyskaj informacje na temat potoków i cykli**

Użyj poleceń `az dla job pipeline`, aby wyświetlić informacje o potoku wcześniej przesłanych zadań.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Użyj poleceń `az dla job recurrence`, aby wyświetlić informacje o cyklu wcześniej przesłanych zadań.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Monitorowanie zadań usługi Azure Data Lake Analytics i rozwiązywanie problemów przy użyciu witryny Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

