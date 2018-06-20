---
title: Jak utworzyć udział plików Azure | Microsoft Docs
description: Jak utworzyć udział plików na platformie Azure w usłudze Azure Files przy użyciu witryny Azure Portal, programu PowerShell i interfejsu wiersza polecenia Azure.
services: storage
documentationcenter: ''
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 85e021d439698e864a26bde9515369ae801c3796
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737864"
---
# <a name="create-a-file-share-in-azure-files"></a>Tworzenie udziału plików w usłudze Azure Files
Udziały plików platformy Azure można tworzyć przy użyciu witryny [Azure Portal](https://portal.azure.com/), poleceń cmdlet programu PowerShell usługi Azure Storage, bibliotek klienckich usługi Azure Storage lub interfejsu API REST usługi Azure Storage. Z tego samouczka dowiesz się:
* [Jak utworzyć udział plików platformy Azure przy użyciu portalu Azure](#create-file-share-through-the-azure-portal)
* [Jak utworzyć udział plików platformy Azure przy użyciu programu PowerShell](#create-file-share-through-powershell)
* [Jak utworzyć udział plików platformy Azure przy użyciu interfejsu wiersza polecenia](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć udział plików Azure, można użyć istniejącego konta magazynu lub [utworzyć nowe konto usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby utworzyć udział plików Azure przy użyciu programu PowerShell, konieczny jest klucz konta i nazwa konta magazynu. Jeśli planujesz użyć programu PowerShell lub interfejsu wiersza polecenia, niezbędny będzie klucz konta magazynu.

## <a name="create-file-share-through-the-azure-portal"></a>Tworzenie udziału plików za pośrednictwem witryny Azure Portal
1. **Przejdź do bloku Konto magazynu w witrynie Azure Portal**:    
    ![Blok Konto magazynu](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Kliknij przycisk Dodaj udział pliku**:    
    ![Kliknij przycisk Dodaj udział pliku](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Podaj nazwę i limit przydziału. Obecnie maksymalna wartość limitu przydziału wynosi 5 TiB**:    
    ![Podaj nazwę i żądany limit przydziału dla nowego udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Wyświetl nowy udział plików**: ![Wyświetl swój nowy udział plików](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Przekaż plik**: ![Przekaż plik](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Przejdź do udziału plików i zarządzaj swoimi plikami i katalogami**: ![Przeglądaj udział plików](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Tworzenie udziału plików za pośrednictwem programu PowerShell
Aby przygotować się do użycia programu Azure PowerShell, pobierz i zainstaluj polecenia cmdlet tego programu. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz [How to install and configure Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) (Jak zainstalować i skonfigurować program Azure PowerShell).

> [!Note]  
> Zalecamy pobranie i zainstalowanie najnowszej wersji modułu Azure PowerShell (lub uaktualnienie do tej wersji).

1. **Utwórz kontekst konta magazynu i klucza** W kontekście zawarta jest nazwa konta magazynu i klucz konta. Aby uzyskać instrukcje dotyczące kopiowania klucza konta z witryny [Azure Portal](https://portal.azure.com/), zobacz [Wyświetlanie i kopiowanie kluczy dostępu do magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Utwórz nowy udział plików**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

## <a name="create-file-share-through-command-line-interface-cli"></a>Tworzenie udziału plików za pomocą interfejsu wiersza polecenia (CLI)
1. **Aby przygotować się do użycia interfejsu wiersza polecenia (CLI), pobierz i zainstaluj wiersz polecenia platformy Azure.**  
    Zobacz artykuły [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0) i [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) (Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure w wersji 2.0).

2. **Utwórz parametry połączenia z kontem magazynu, w którym chcesz utworzyć udział.**  
    W poniższym przykładzie zastąp zmienne ```<storage-account>``` i ```<resource_group>``` nazwą konta magazynu i grupą zasobów:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Tworzenie udziału plików**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Następne kroki
* [Łączenie i instalowanie udziału plików — system Windows](storage-how-to-use-files-windows.md)
* [Łączenie i instalowanie udziału plików — system Linux](../storage-how-to-use-files-linux.md)
* [Łączenie i instalowanie udziału plików — system macOS](storage-how-to-use-files-mac.md)

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files.

* [Często zadawane pytania](../storage-files-faq.md)
* [Rozwiązywanie problemów w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Rozwiązywanie problemów w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)   
