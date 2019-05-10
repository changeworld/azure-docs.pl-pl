---
title: Jak utworzyć udział plików Azure | Microsoft Docs
description: Jak utworzyć udział plików na platformie Azure w usłudze Azure Files przy użyciu witryny Azure Portal, programu PowerShell i interfejsu wiersza polecenia Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d945d5b79c274aa8e142203c56b27eb673e36741
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510516"
---
# <a name="create-a-file-share-in-azure-files"></a>Tworzenie udziału plików w usłudze Azure Files
Udziały plików platformy Azure można tworzyć przy użyciu witryny  [Azure Portal](https://portal.azure.com/), poleceń cmdlet programu PowerShell usługi Azure Storage, bibliotek klienckich usługi Azure Storage lub interfejsu API REST usługi Azure Storage. Z tego samouczka dowiesz się:
* Jak utworzyć udział plików platformy Azure przy użyciu witryny Azure Portal
* [Jak utworzyć udział plików platformy Azure przy użyciu programu PowerShell](#create-file-share-through-powershell)
* [Jak utworzyć udział plików platformy Azure przy użyciu interfejsu wiersza polecenia](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć udział plików Azure, można użyć istniejącego konta magazynu lub [utworzyć nowe konto usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby utworzyć udział plików Azure przy użyciu programu PowerShell, konieczny jest klucz konta i nazwa konta magazynu. Jeśli planujesz użyć programu PowerShell lub interfejsu wiersza polecenia, niezbędny będzie klucz konta usługi Storage.

## <a name="create-a-file-share-through-the-azure-portal"></a>Tworzenie udziału plików za pośrednictwem witryny Azure Portal
1. **Przejdź do bloku Konto usługi Storage w witrynie Azure Portal**:    
    ![Blok Konto magazynu](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kliknij przycisk Dodaj udział pliku**:    
    ![Kliknij przycisk Dodaj udział pliku](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Podaj nazwę i limit przydziału. Maksymalna wartość limitu przydziału wynosi obecnie 5 TiB**:    
    ![Podaj nazwę i żądany limit przydziału dla nowego udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Wyświetl nowy udziału plików**:  ![Wyświetlanie nowego udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Przekaż plik**:  ![Przekazywanie pliku](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Przejdź do udziału plików i zarządzaj swoimi katalogami i plikami**:  ![Przeglądanie udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Tworzenie udziału plików za pośrednictwem programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby przygotować się do użycia programu Azure PowerShell, pobierz i zainstaluj polecenia cmdlet tego programu. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz  [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) .

> [!Note]  
> Zalecamy pobranie i zainstalowanie najnowszej wersji modułu Azure PowerShell (lub uaktualnienie do tej wersji).

1. **Utwórz kontekst konta magazynu i klucza** W kontekście zawarta jest nazwa konta magazynu i klucz konta. Aby uzyskać instrukcje dotyczące kopiowania klucza konta z witryny  [Azure Portal](https://portal.azure.com/), zobacz  [Storage account access keys](../common/storage-account-manage.md#access-keys) (Klucze dostępu do konta usługi Storage).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Utwórz nowy udział plików**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie  [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Nazywanie i przywoływanie udziałów, katalogów, plików i metadanych).

## <a name="create-file-share-through-command-line-interface-cli"></a>Tworzenie udziału plików za pomocą interfejsu wiersza polecenia (CLI)
1. **Aby przygotować się do użycia interfejsu wiersza polecenia (CLI), pobierz i zainstaluj interfejs wiersza polecenia platformy Azure.**  
    Zobacz artykuły  [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i [Wprowadzenie do interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Utwórz parametry połączenia z kontem magazynu, w którym chcesz utworzyć udział.**  
    W poniższym przykładzie zastąp zmienne ```<storage-account>``` oraz ```<resource_group>``` nazwą konta magazynu i grupą zasobów:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Tworzenie udziału plików**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Kolejne kroki
* [Łączenie i instalowanie udziału plików — system Windows](storage-how-to-use-files-windows.md)
* [Łączenie i instalowanie udziału plików — system Linux](../storage-how-to-use-files-linux.md)
* [Łączenie i instalowanie udziału plików — system macOS](storage-how-to-use-files-mac.md)

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Rozwiązywanie problemów w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)   
