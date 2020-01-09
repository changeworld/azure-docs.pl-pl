---
title: Tworzenie udziału plików platformy Azure
titleSuffix: Azure Files
description: Jak utworzyć udział plików platformy Azure przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452890"
---
# <a name="create-a-file-share"></a>Tworzenie udziału plików
Udział można utworzyć w Azure Files przy użyciu [Azure Portal](https://portal.azure.com/), poleceń cmdlet programu PowerShell usługi Azure Storage, bibliotek klienckich usługi Azure Storage lub interfejsu API REST usługi Azure Storage. W tym artykule dowiesz się, jak:
- Tworzenie udziału plików platformy Azure przy użyciu Azure Portal
- Tworzenie udziału plików platformy Azure przy użyciu programu PowerShell
- Tworzenie udziału plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne
Aby utworzyć udział plików platformy Azure, możesz użyć już istniejącego konta magazynu lub [utworzyć nowe konto usługi Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Aby można było utworzyć udział plików platformy Azure przy użyciu programu PowerShell, potrzebny będzie klucz konta i nazwa konta magazynu. Jeśli planujesz korzystanie z programu PowerShell lub interfejsu wiersza polecenia, będzie potrzebny klucz konta magazynu.

> [!NOTE]
> Jeśli chcesz utworzyć udziały plików większe niż 5 TiB, zobacz [enable Large file shares](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Tworzenie udziału plików za pośrednictwem witryny Azure Portal

1. Przejdź do okienka **konto magazynu** na Azure Portal.
    ![okienku konta magazynu](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Wybierz przycisk **+ udział plików** .
    ![przycisk Dodaj udział plików](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Wprowadź informacje o **nazwie** i **przydziale**.
    ![nazwę i przydział nowego udziału plików](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Wyświetl nowy udział plików.
    ![nowy udział plików w interfejsie użytkownika](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Przekaż plik.
    ![przycisku przekazywania na pasku nawigacyjnym](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Przejdź do udziału plików, a następnie Zarządzaj katalogami i plikami.
    Widok folderu ![udziałów plików](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Tworzenie udziału plików za pomocą programu PowerShell

Pobierz i zainstaluj polecenia cmdlet Azure PowerShell. Aby uzyskać informacje o punkcie instalacji oraz instrukcje dotyczące instalacji, zobacz  [Jak zainstalować i skonfigurować program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Zalecamy pobranie i zainstalowanie lub uaktualnienie do programu z najnowszym modułem Azure PowerShell.

1. Utwórz nowe konto magazynu.
    Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure i zasoby magazynu, takie jak obiekty blob lub kolejki.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Utwórz nowy udział plików.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Nazwa udziału plików musi się składać z samych małych liter. Aby uzyskać szczegółowe informacje o nazewnictwie udziałów plików i plików, zobacz [nazewnictwo i odwoływanie się do udziałów, katalogów, plików i metadanych](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Tworzenie udziału plików za pomocą interfejsu wiersza polecenia

1. Pobierz i zainstaluj interfejs wiersza polecenia platformy Azure.
    Zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) i rozpoczynanie [pracy z interfejsem wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Utwórz parametry połączenia z kontem magazynu, na którym chcesz utworzyć udział.
    W poniższym przykładzie zastąp zmienne ```<storage-account>``` oraz ```<resource_group>``` nazwą konta magazynu i grupą zasobów:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Utwórz udział plików.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Następne kroki

* [Łączenie i Instalowanie udziału plików w systemie Windows](storage-how-to-use-files-windows.md)
* [Łączenie i Instalowanie udziału plików w systemie Linux](storage-how-to-use-files-linux.md)
* [Łączenie i Instalowanie udziału plików w systemie macOS](storage-how-to-use-files-mac.md)

Poniższe linki umożliwiają uzyskanie dodatkowych informacji na temat usługi Azure Files:

* [Pliki magazynu — często zadawane pytania](storage-files-faq.md)
* [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
