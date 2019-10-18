---
title: Jak utworzyć udział plików Azure | Microsoft Docs
description: Jak utworzyć udział plików na platformie Azure w usłudze Azure Files przy użyciu witryny Azure Portal, programu PowerShell i interfejsu wiersza polecenia Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3b30df7410f56c203eb600089cce130a8de23d1b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514609"
---
# <a name="create-a-file-share-in-azure-files"></a>Tworzenie udziału plików w usłudze Azure Files
Udziały plików platformy Azure można tworzyć przy użyciu witryny  [Azure Portal](https://portal.azure.com/), poleceń cmdlet programu PowerShell usługi Azure Storage, bibliotek klienckich usługi Azure Storage lub interfejsu API REST usługi Azure Storage. Z tego samouczka dowiesz się:
* Jak utworzyć udział plików platformy Azure przy użyciu witryny Azure Portal
* [Jak utworzyć udział plików platformy Azure przy użyciu programu PowerShell](#create-file-share-through-powershell)
* [Jak utworzyć udział plików platformy Azure przy użyciu interfejsu wiersza polecenia](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć udział plików Azure, można użyć istniejącego konta magazynu lub [utworzyć nowe konto usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby utworzyć udział plików Azure przy użyciu programu PowerShell, konieczny jest klucz konta i nazwa konta magazynu. Jeśli planujesz użyć programu PowerShell lub interfejsu wiersza polecenia, niezbędny będzie klucz konta usługi Storage.

> [!NOTE]
> Jeśli chcesz utworzyć udziały plików większe niż 5 TiB, zapoznaj się z artykułem [Włączanie dużych udziałów plików](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Tworzenie udziału plików za pośrednictwem witryny Azure Portal
1. **Przejdź do bloku Konto usługi Storage w witrynie Azure Portal**:    
    ![Blok Konto magazynu](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kliknij przycisk Dodaj udział pliku**:    
    ![Kliknij przycisk Dodaj udział pliku](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Podaj nazwę i limit przydziału**:    
    ![Podaj nazwę i żądany limit przydziału dla nowego udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Wyświetl nowy udział plików**: ![Wyświetl swój nowy udział plików](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Przekaż plik**: ![Przekaż plik](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Przejdź do udziału plików i zarządzaj swoimi plikami i katalogami**: ![Przeglądaj udział plików](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Tworzenie udziału plików za pośrednictwem programu PowerShell
Aby przygotować się do użycia programu Azure PowerShell, pobierz i zainstaluj polecenia cmdlet tego programu. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz  [Jak zainstalować i skonfigurować program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Zalecamy pobranie i zainstalowanie najnowszej wersji modułu Azure PowerShell (lub uaktualnienie do tej wersji).

1. **Utwórz nowe konto magazynu:** Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure oraz inne zasoby magazynu, takie jak obiekty blob lub kolejki.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

2. **Utwórz nowy udział plików**:    
    
    ```powershell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
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
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Następne kroki
* [Łączenie i instalowanie udziału plików — system Windows](storage-how-to-use-files-windows.md)
* [Łączenie i instalowanie udziału plików — system Linux](../storage-how-to-use-files-linux.md)
* [Łączenie i instalowanie udziału plików — system macOS](storage-how-to-use-files-mac.md)

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Rozwiązywanie problemów w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)   
