---
title: Rozpoczynanie pracy przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Użyj wiersza polecenia platformy Azure do utworzenia konta Data Lake Storage Gen1 i wykonywać podstawowe operacje
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880580"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Rozpoczynanie pracy z usługą Azure Data Lake Store, przy użyciu wiersza polecenia platformy Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure, aby utworzyć konto usługi Azure Data Lake Storage Gen1 i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji na temat Data Lake Storage Gen1 zobacz [przegląd z Data Lake Storage Gen1](data-lake-store-overview.md).

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można go używać w systemach macOS, Linux i Windows. Aby uzyskać więcej informacji, zobacz [przegląd z wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure). Można również przeglądać [odwołanie do wiersza polecenia platformy Azure Data Lake Storage Gen1](https://docs.microsoft.com/cli/azure/dls) pełną listę poleceń i składni.


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Interfejs wiersza polecenia Azure** — zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli) instrukcje.

## <a name="authentication"></a>Authentication

Tym artykule użyto prostszej metody uwierzytelniania w usłudze Data Lake magazynu Gen1, gdzie możesz zalogować się jako użytkownik końcowy. Poziom dostępu do Gen1 magazynu Data Lake, konta i systemu plików jest określany przez poziom dostępu zalogowanego użytkownika. Istnieją inne metody uwierzytelniania w usłudze Data Lake Storage Gen1, które są **uwierzytelnianie użytkowników końcowych** lub **service-to-service authentication**. Instrukcje i dodatkowe informacje na temat uwierzytelniania można znaleźć w następujących artykułach: [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Uwierzytelnianie użytkowników końcowych) lub [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Uwierzytelnianie między usługami).


## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    ```

    Uzyskasz kod do użycia w następnym kroku. Użyj przeglądarki sieci web, aby otworzyć stronę https://aka.ms/devicelogin i wprowadź kod w celu uwierzytelnienia. Zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń.

2. Po zalogowaniu w oknie zostanie wyświetlona lista wszystkich subskrypcji platformy Azure, które są skojarzone z Twoim kontem. Za pomocą następującego polecenia użyj konkretnej subskrypcji.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Azure Data Lake Storage Gen1

1. Utwórz nową grupę zasobów. W poniższym poleceniu podaj wartości parametrów, których chcesz użyć. Jeśli nazwa lokalizacji zawiera spacje, umieść ją w cudzysłowie. Na przykład „Wschodnie stany USA 2”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Tworzenie konta Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Tworzenie folderów w ramach konta Data Lake Storage Gen1

Można tworzyć foldery w ramach konta usługi Azure Data Lake Storage Gen1 do przechowywania danych i zarządzania nimi. Użyj następującego polecenia, aby utworzyć folder o nazwie **mojnowyfolder** w katalogu głównym konta Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` gwarantuje, że polecenie utworzy folder. Jeśli ten parametr nie jest obecny, to polecenie tworzy pusty plik o nazwie mojnowyfolder w katalogu głównym konta Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Przekazywanie danych do konta Data Lake Storage Gen1

Dane można przekazywać do Data Lake Storage Gen1 bezpośrednio na poziomie katalogu głównego lub do folderu, który został utworzony w ramach konta. Poniższe fragmenty kodu przedstawiają sposób przekazywania przykładowych danych do folderu (**mojnowyfolder**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> W przypadku podawania miejsca docelowego należy określić pełną ścieżkę, łącznie z nazwą pliku.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Wyświetlanie listy plików w ramach konta Data Lake Storage Gen1

Użyj następującego polecenia, aby wyświetlić listę plików w ramach konta Data Lake Storage Gen1.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Dane wyjściowe będą mieć postać podobną do następującej:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Zmienianie nazwy, pobieranie i usuwanie danych z konta Data Lake Storage Gen1 

* **Aby zmienić nazwę pliku**, użyj następującego polecenia:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Aby pobrać plik**, użyj następującego polecenia. Upewnij się, że ścieżka docelowa już istnieje.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Polecenie tworzy folder docelowy, jeśli nie istnieje.
    > 
    >

* **Aby usunąć plik**, użyj następującego polecenia:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Jeśli chcesz usunąć folder **mojnowyfolder** i plik **vehicle1_09142014_copy.csv** za pomocą jednego polecenia, użyj parametru --recurse

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Praca z uprawnieniami i listami kontroli dostępu dla konta Data Lake Storage Gen1

W tej sekcji informacje o sposobie zarządzania listy kontroli dostępu i uprawnień przy użyciu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe omówienie dotyczące sposobu implementacji list ACL w usłudze Azure Data Lake magazynu Gen1, zobacz [kontrola dostępu w usługach Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Aby zaktualizować właściciela pliku/folderu**, użyj następującego polecenia:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Aby zaktualizować uprawnienia do pliku/folderu**, użyj następującego polecenia:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Aby uzyskać listy ACL dla danej ścieżki**, użyj następującego polecenia:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Dane wyjściowe będą podobne do następujących:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Aby ustawić pozycję listy ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Aby usunąć pozycję z listy ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Aby usunąć całą domyślną listę ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Aby usunąć całą inną niż domyślną listę ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Data Lake Storage Gen1
Użyj następującego polecenia, można usunąć konta Data Lake Storage Gen1.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Kolejne kroki
* [Użyj usługi Azure Data Lake Storage Gen1 dla wymagających danych big Data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Za pomocą usług Azure Data Lake Analytics Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usługa Azure HDInsight za pomocą programu Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
