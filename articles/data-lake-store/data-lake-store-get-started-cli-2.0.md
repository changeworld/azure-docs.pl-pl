---
title: Korzystanie z interfejsu wiersza polecenia platformy Azure w celu rozpoczęcia korzystania z usługi Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft
description: Tworzenie konta Gen1 magazynu usługi Data Lake i wykonywanie podstawowych operacji za pomocą interfejsu wiersza polecenia platformy Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60885350"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Wprowadzenie do usługi Azure Data Lake Store przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [Powershell](data-lake-store-get-started-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia konta usługi Azure Data Lake Storage Gen1 i wykonywania podstawowych operacji, takich jak tworzenie folderów, przekazywanie i pobieranie plików danych, usuwanie konta itp. Aby uzyskać więcej informacji na temat usługi Data Lake Storage Gen1, zobacz [Omówienie gen1 magazynu aplikacji Data Lake](data-lake-store-overview.md).

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można go używać w systemach macOS, Linux i Windows. Aby uzyskać więcej informacji, zobacz [Omówienie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure). Można również spojrzeć na [odwołanie interfejsu wiersza polecenia usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/cli/azure/dls) dla pełnej listy poleceń i składni.


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem korzystania z informacji zawartych w tym artykule należy dysponować następującymi elementami:

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Interfejsu wiersza polecenia platformy Azure** — instrukcje można znaleźć w witrynie [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="authentication"></a>Uwierzytelnianie

W tym artykule użyto prostszego podejścia do uwierzytelniania w u źródła danych Usługi Storage Gen1, w którym logujesz się jako użytkownik końcowy. Poziom dostępu do konta i systemu plików Usługi Data Lake Storage Gen1 jest następnie regulowany poziomem dostępu zalogowanego użytkownika. Istnieją jednak również inne podejścia do uwierzytelniania za pomocą usługi Data Lake Storage Gen1, które są **uwierzytelnianiem użytkowników końcowych** lub **uwierzytelnianiem usługi**do usługi. Instrukcje i dodatkowe informacje na temat uwierzytelniania można znaleźć w następujących artykułach: [End-user authentication](data-lake-store-end-user-authenticate-using-active-directory.md) (Uwierzytelnianie użytkowników końcowych) lub [Service-to-service authentication](data-lake-store-authenticate-using-active-directory.md) (Uwierzytelnianie między usługami).


## <a name="log-in-to-your-azure-subscription"></a>Logowanie się do subskrypcji platformy Azure

1. Zaloguj się do subskrypcji platformy Azure.

    ```azurecli
    az login
    ```

    Uzyskasz kod do użycia w następnym kroku. Otwórz stronę https://aka.ms/devicelogin w przeglądarce internetowej i wprowadź kod, aby się uwierzytelnić. Zostanie wyświetlony monit o zalogowanie się przy użyciu poświadczeń.

2. Po zalogowaniu w oknie zostanie wyświetlona lista wszystkich subskrypcji platformy Azure, które są skojarzone z Twoim kontem. Za pomocą następującego polecenia użyj konkretnej subskrypcji.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Azure Data Storage Gen1

1. Utwórz nową grupę zasobów. W poniższym poleceniu podaj wartości parametrów, których chcesz użyć. Jeśli nazwa lokalizacji zawiera spacje, umieść ją w cudzysłowie. Na przykład „Wschodnie stany USA 2”. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Utwórz konto Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Tworzenie folderów na koncie Gen1 magazynu usługi Data Lake

Foldery można tworzyć w ramach konta usługi Azure Data Lake Storage Gen1 do zarządzania i przechowywania danych. Użyj następującego polecenia, aby utworzyć folder o nazwie **mynewfolder** w katalogu głównym konta Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` gwarantuje, że polecenie utworzy folder. Jeśli ten parametr nie jest obecny, polecenie tworzy pusty plik o nazwie mynewfolder w katalogu głównym konta Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Przekazywanie danych do konta Data Lake Storage Gen1

Dane można przesyłać do usługi Data Lake Storage Gen1 bezpośrednio na poziomie głównym lub do folderu utworzonego na koncie. Poniższe fragmenty kodu przedstawiają sposób przekazywania przykładowych danych do folderu (**mojnowyfolder**), który został utworzony w poprzedniej sekcji.

Jeśli szukasz przykładowych danych do przekazania, możesz pobrać folder **Ambulance Data** z [repozytorium Git usługi Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Pobierz plik i zapisz go w katalogu lokalnym na komputerze, na przykład C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> W przypadku podawania miejsca docelowego należy określić pełną ścieżkę, łącznie z nazwą pliku.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Lista plików na koncie Data Lake Storage Gen1

Użyj następującego polecenia, aby wyświetlić listę plików na koncie Gen1 magazynu usługi Data Lake.

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

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Zmienianie nazw, pobieranie i usuwanie danych z konta Usługi Data Lake Storage Gen1 

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

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Praca z uprawnieniami i listami ACL dla konta Gen1 magazynu usługi Data Lake

W tej sekcji dowiesz się, jak zarządzać listami ACL i uprawnieniami przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe omówienie sposobu implementacji list ACL w usłudze Azure Data Lake Storage Gen1, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

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
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Gen1 magazynu usługi Data Lake
Użyj następującego polecenia, aby usunąć konto Gen1 magazynu usługi Data Lake.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Po wyświetleniu monitu wpisz **Y**, aby usunąć konto.

## <a name="next-steps"></a>Następne kroki
* [Korzystanie z usługi Azure Data Lake Storage Gen1 dla wymagań dotyczących dużych zbiorów danych](data-lake-store-data-scenarios.md) 
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
