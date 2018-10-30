---
title: Przewodnik Szybki start dotyczący zarządzania udziałami plików platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki start dowiesz się, jak zarządzać usługą Azure Files za pomocą interfejsu wiersza polecenia platformy Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: aab248ac7c9adf7d996406ec35e0317594ce0b68
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945022"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Szybki start: tworzenie udziałów plików platformy Azure i zarządzanie nimi przy użyciu interfejsu wiersza polecenia platformy Azure
W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z [udziałami plików platformy Azure](storage-files-introduction.md) przy użyciu interfejsu wiersza polecenia platformy Azure. Udziały plików platformy Azure są podobne do innych udziałów plików, ale są przechowywane w chmurze i obsługiwane przez platformę Azure. Udziały plików platformy Azure obsługują standardowy w branży protokół SMB i umożliwiają udostępnianie plików między wieloma maszynami, aplikacjami i wystąpieniami. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli podejmiesz decyzję o zainstalowaniu i używaniu interfejsu wiersza polecenia platformy Azure lokalnie, to aby wykonać kroki opisane w tym artykule, musisz mieć wersję 2.0.4 lub nowszą. Uruchom polecenie **az --version**, aby określić wersję używanego interfejsu wiersza polecenia platformy Azure. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

Domyślnie polecenia interfejsu wiersza polecenia platformy Azure zwracają kod JSON (JavaScript Object Notation). Kod JSON to standardowy sposób wysyłania i odbierania komunikatów w ramach komunikacji z interfejsami API REST. W celu ułatwienia pracy z odpowiedziami JSON przykłady w tym artykule korzystają z parametru *query* w poleceniach interfejsu wiersza polecenia platformy Azure. Ten parametr używa [języka zapytań JMESPath](http://jmespath.org/) do analizowania danych JSON. Aby dowiedzieć się więcej na temat używania wyników poleceń interfejsu wiersza polecenia platformy Azure, zobacz [samouczek języka zapytań JMESPath](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
W przypadku korzystania z interfejsu wiersza polecenia platformy Azure lokalnie otwórz wiersz polecenia i zaloguj się do platformy Azure, jeśli nie zrobiono tego wcześniej.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Jeśli nie masz jeszcze grupy zasobów platformy Azure, możesz utworzyć nową grupę za pomocą polecenia [az group create](/cli/azure/group#create). 

Poniższy przykład umożliwia utworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Wschodnie stany USA*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Konto magazynu to udostępniona pula magazynu, w której można wdrażać udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob i kolejki. Konto magazynu może zawierać nieograniczoną liczbę udziałów plików. W udziale można przechowywać nieograniczoną liczbę plików, aż do osiągnięcia limitów pojemności konta magazynu.

Następujący przykład umożliwia utworzenie konta magazynu o nazwie *mystorageaccount\<losowa liczba\>* przy użyciu polecenia [az storage account create](/cli/azure/storage/account#create), a następnie umieszczenie nazwy tego konta magazynu w zmiennej `$STORAGEACCT`. Nazwy kont magazynu muszą być unikatowe. Zmienna `$RANDOM` powoduje dołączenie liczby do nazwy konta magazynu, co zapewnia jego unikatowość. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Pobieranie klucza konta magazynu
Klucze konta magazynu kontrolują dostęp do zasobów na koncie magazynu. Klucze są tworzone automatycznie podczas tworzenia konta magazynu. Klucze konta magazynu możesz pobrać przy użyciu polecenia [az storage account keys list](/cli/azure/storage/account/keys#list): 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Teraz możesz utworzyć swój pierwszy udział plików platformy Azure. Udziały plików tworzy się przy użyciu polecenia [az storage share create](/cli/azure/storage/share#create). W tym przykładzie jest tworzony udział plików platformy Azure o nazwie *myshare*: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Nazwy udziałów mogą zawierać tylko małe litery, cyfry i pojedyncze łączniki (ale nie mogą zaczynać się łącznikiem). Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and referencing shares, directories, files, and metadata (Nazywanie i przywoływanie udziałów, katalogów, plików i metadanych)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Korzystanie z udziału plików platformy Azure
Usługa Azure Files oferuje dwie metody pracy z plikami i folderami w obrębie udziału plików platformy Azure: zgodny ze standardem branżowym [protokół Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) i [protokół REST usługi Files](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Aby zainstalować udział plików za pomocą protokołu SMB, zobacz następujący dokument zgodny z używanym systemem operacyjnym:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Korzystanie z udziału plików platformy Azure za pomocą protokołu REST usługi Files 
Istnieje możliwość bezpośredniej pracy z protokołem REST usługi Files (polegającej na ręcznym tworzeniu wywołań HTTP REST), ale najczęstszym sposobem korzystania z protokołu REST usługi Files jest użycie interfejsu wiersza polecenia platformy Azure, [modułu AzureRM PowerShell](storage-how-to-use-files-powershell.md) lub zestawu Azure Storage SDK. Wszystkie te narzędzia udostępniają sprawną otokę protokołu REST usługi Files w wybranym języku skryptowym/programowania.  

Zdajemy sobie sprawę, że większość użytkowników usługi Azure Files będzie chciało pracować z udziałami plików platformy Azure za pośrednictwem protokołu SMB, ponieważ umożliwi im to skorzystanie z istniejących aplikacji i narzędzi, ale istnieje kilka powodów, dla których użycie interfejsu API REST usługi Files wydaje się korzystniejsze niż użycie protokołu SMB:

- Udziały plików przeglądasz za pomocą powłoki Azure Bash w usłudze Cloud Shell (w której nie można instalować udziałów plików za pośrednictwem protokołu SMB).
- Musisz wykonać skrypt lub uruchomić aplikację z poziomu klienta, który nie może instalować udziałów SMB. Mogą to być na przykład klienci lokalni bez odblokowanego portu 445.
- Korzystasz z zasobów bezserwerowych, takich jak [usługa Azure Functions](../../azure-functions/functions-overview.md). 

W poniższych przykładach przedstawiono, jak używać modułu AzureRM PowerShell do obsługi udziałów plików platformy Azure przy użyciu protokołu REST usługi Files. 

### <a name="create-a-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure, użyj polecenia [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create):

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Przekazywanie pliku
Aby zademonstrować sposób przekazywania pliku przy użyciu polecenia [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), najpierw utwórz plik do przekazania na dysku zapasowym usługi Cloud Shell. Poniższy przykład umożliwia utworzenie, a następnie przekazanie pliku:

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, zastąp ciąg `~/clouddrive` ścieżką, która istnieje na Twojej maszynie.

Po przekazaniu pliku możesz upewnić się, że plik został przekazany do udziału plików platformy Azure, korzystając z polecenia [`az storage file list`](/cli/azure/storage/file#az_storage_file_list):

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Pobieranie pliku
Za pomocą polecenia [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) możesz pobrać kopię pliku, który został przekazany na dysk zapasowy usługi Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopiowanie plików
Często wykonywanym zadaniem jest kopiowanie plików z jednego udziału plików do innego lub do/z kontenera usługi Azure Blob Storage. Aby zademonstrować tę funkcję, utwórz nowy udział. Skopiuj przekazany plik do nowego udziału za pomocą polecenia [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Teraz, po wyświetleniu listy plików w nowym udziale, skopiowany plik powinien być widoczny:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Mimo że polecenie `az storage file copy start` jest wygodne w przypadku przenoszenia plików między udziałami plików platformy Azure i kontenerami usługi Azure Blob Storage, to do wykonywania dużych operacji przenoszenia zalecamy używanie narzędzia AzCopy. (Dużych pod względem liczby lub rozmiaru przenoszonych plików). Dowiedz się więcej o [narzędziu AzCopy dla systemu Linux](../common/storage-use-azcopy-linux.md) i [narzędziu AzCopy dla systemu Windows](../common/storage-use-azcopy.md). Narzędzie AzCopy musi być zainstalowane lokalnie. Narzędzie AzCopy nie jest dostępne w usłudze Cloud Shell. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy skończysz, możesz usunąć grupę zasobów i wszystkie powiązane zasoby za pomocą polecenia [`az group delete`](/cli/azure/group#delete): 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Alternatywnie możesz usunąć zasoby pojedynczo.
- Aby usunąć udziały plików platformy Azure utworzone w tym artykule:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Aby usunąć samo konto magazynu. (To powoduje niejawne usunięcie utworzonych udziałów plików platformy Azure i wszystkich innych utworzonych zasobów magazynu, takich jak kontener usługi Azure Blob Storage).

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Co to jest usługa Azure Files?](storage-files-introduction.md)