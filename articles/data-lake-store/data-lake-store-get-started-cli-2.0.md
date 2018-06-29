---
title: Interfejsu użyj 2.0 wiersza polecenia platformy Azure, aby zacząć korzystać z usługi Azure Data Lake magazynu Gen1 | Dokumentacja firmy Microsoft
description: Korzystanie z międzyplatformowego wiersza polecenia platformy Azure 2.0 w celu utworzenia konta usługi Data Lake Store i wykonywania podstawowych operacji
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: acd1182fdc66374e9abbc4964207417a7de3aadb
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036577"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-cli-20"></a>Wprowadzenie do usługi Azure Data Lake magazynu Gen1 używa interfejsu wiersza polecenia platformy Azure w wersji 2.0
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Program PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Dowiedz się, jak przy użyciu interfejsu wiersza polecenia platformy Azure 2.0 utworzyć konto usługi Azure Data Lake Store i wykonywać podstawowe operacje, takie jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji o usłudze Data Lake Store, zobacz [omówienie z Data Lake magazynu Gen1](data-lake-store-overview.md).

Interfejs wiersza polecenia platformy Azure 2.0 to nowe środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można go używać w systemach macOS, Linux i Windows. Aby uzyskać więcej informacji, zobacz [Overview of Azure CLI 2.0](https://docs.microsoft.com/cli/azure) (Przegląd interfejsu wiersza polecenia platformy Azure 2.0). Aby wyświetlić pełną listę poleceń i składnię, zobacz artykuł [Azure Data Lake Store CLI 2.0 reference](https://docs.microsoft.com/cli/azure/dls) (Informacje o interfejsie wiersza polecenia usługi Azure Data Lake Store 2.0).


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Interfejs wiersza polecenia platformy Azure 2.0** — instrukcje znajdują się w artykule [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0).

## <a name="authentication"></a>Authentication

W tym artykule użyto prostszej metody uwierzytelniania w usłudze Data Lake Store, w przypadku której logujesz się jako użytkownik końcowy. Poziom dostępu do konta i systemu plików usługi Data Lake Store jest określany przez poziom dostępu zalogowanego użytkownika. Istnieją jednak inne metody uwierzytelniania w usłudze Data Lake Store: **uwierzytelnianie użytkowników końcowych** i **uwierzytelnianie między usługami**. Instrukcje i dodatkowe informacje na temat uwierzytelniania można znaleźć w następujących artykułach: [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Uwierzytelnianie użytkowników końcowych) lub [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Uwierzytelnianie między usługami).


## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    ```

    Uzyskasz kod do użycia w następnym kroku. Aby otworzyć stronę przy użyciu przeglądarki sieci web https://aka.ms/devicelogin i wprowadź kod uwierzytelniania. Zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń.

2. Po zalogowaniu w oknie zostanie wyświetlona lista wszystkich subskrypcji platformy Azure, które są skojarzone z Twoim kontem. Za pomocą następującego polecenia użyj konkretnej subskrypcji.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Tworzenie konta usługi Azure Data Lake Store

1. Utwórz nową grupę zasobów. W poniższym poleceniu podaj wartości parametrów, których chcesz użyć. Jeśli nazwa lokalizacji zawiera spacje, umieść ją w cudzysłowie. Na przykład „Wschodnie stany USA 2”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Utwórz konto usługi Data Lake Store.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Tworzenie folderów w ramach konta usługi Data Lake Store

Na koncie usługi Azure Data Lake Store można tworzyć foldery w celu przechowywania danych i zarządzania nimi. Za pomocą następującego polecenia utwórz folder o nazwie **mojnowyfolder** w katalogu głównym usługi Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` gwarantuje, że polecenie utworzy folder. Jeśli ten parametr zostanie pominięty, polecenie utworzy w katalogu głównym konta usługi Data Lake Store pusty plik o nazwie mojnowyfolder.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Przekazywanie danych do konta usługi Data Lake Store

Dane można przekazywać do konta usługi Data Lake Store bezpośrednio do katalogu głównego lub do folderu utworzonego w ramach konta. Poniższe fragmenty kodu przedstawiają sposób przekazywania przykładowych danych do folderu (**mojnowyfolder**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> W przypadku podawania miejsca docelowego należy określić pełną ścieżkę, łącznie z nazwą pliku.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Wyświetlanie listy plików w ramach konta usługi Data Lake Store

Użyj poniższego polecenia, aby wyświetlić listę plików na koncie usługi Data Lake Store.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
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

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Zmienianie nazwy, pobieranie i usuwanie danych z konta usługi Data Lake Store 

* **Aby zmienić nazwę pliku**, użyj następującego polecenia:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Aby pobrać plik**, użyj następującego polecenia. Upewnij się, że ścieżka docelowa już istnieje.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Polecenie tworzy folder docelowy, jeśli nie istnieje.
    > 
    >

* **Aby usunąć plik**, użyj następującego polecenia:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Jeśli chcesz usunąć folder **mojnowyfolder** i plik **vehicle1_09142014_copy.csv** za pomocą jednego polecenia, użyj parametru --recurse

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Praca z uprawnieniami i listami kontroli dostępu dla konta usługi Data Lake Store

W tej sekcji zawarto informacje o sposobie zarządzania listami kontroli dostępu (ACL) i uprawnieniami przy użyciu interfejsu wiersza polecenia platformy Azure 2.0. Szczegółowe omówienie sposobu implementacji list ACL w usłudze Azure Data Lake Store znajduje się w artykule [Kontrola dostępu w usłudze Azure Data Lake Store](data-lake-store-access-control.md).

* **Aby zaktualizować właściciela pliku/folderu**, użyj następującego polecenia:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Aby zaktualizować uprawnienia do pliku/folderu**, użyj następującego polecenia:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Aby uzyskać listy ACL dla danej ścieżki**, użyj następującego polecenia:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
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
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Aby usunąć pozycję z listy ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Aby usunąć całą domyślną listę ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Aby usunąć całą inną niż domyślną listę ACL**, użyj następującego polecenia:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Usuwanie konta usługi Data Lake Store
Użyj poniższego polecenia, aby usunąć konto usługi Data Lake Store.

```azurecli
az dls account delete --account mydatalakestore
```

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Kolejne kroki
* [Używanie usługi Azure Data Lake Store w zastosowaniach wymagających danych big data](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
