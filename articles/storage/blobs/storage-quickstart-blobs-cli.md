---
title: Szybki start — tworzenie obiektu blob za pomocą interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Storage
description: W tym przewodniku Szybki start dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure przekazać obiekt blob do usługi Azure Storage, pobrać obiekt blob i wyświetlić listę obiektów blob w kontenerze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 2e1b1ac2ea315759b18dc882b98837bca0a84d46
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061442"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Szybki start: tworzenie, pobieranie i wystawianie obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Istnieje także możliwość zainstalowania go w systemach macOS, Linux lub Windows, a następnie uruchomienia z poziomu wiersza polecenia. Ten przewodnik Szybki start zawiera opis użycia interfejsu wiersza polecenia platformy Azure do przekazywania danych do/pobierania danych z usługi Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instalowanie interfejsu wiersza polecenia platformy Azure lokalnie

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie, ten szybki start wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Jeśli korzystasz z interfejsu wiersza polecenia platformy Azure lokalnie, musisz się zalogować i uwierzytelnić. Ten krok nie jest konieczne, jeśli używasz usługi Azure Cloud Shell. Aby zalogować się do `az login` interfejsu wiersza polecenia platformy Azure, uruchom i uwierzytelnij się w oknie przeglądarki:

```azurecli
az login
```

Aby uzyskać więcej informacji na temat uwierzytelniania za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/authenticate-azure-cli)

## <a name="authorize-access-to-blob-storage"></a>Autoryzowanie dostępu do magazynu obiektów Blob

Można autoryzować dostęp do magazynu obiektów Blob z interfejsu wiersza polecenia platformy Azure przy użyciu poświadczeń usługi Azure AD lub przy użyciu klucza dostępu do konta magazynu. Zaleca się używanie poświadczeń usługi Azure AD. W tym artykule pokazano, jak autoryzować operacje magazynu obiektów Blob przy użyciu usługi Azure AD.

Polecenia interfejsu wiersza polecenia platformy Azure `--auth-mode` dla operacji danych względem magazynu obiektów Blob obsługują parametr, który umożliwia określenie sposobu autoryzowania danej operacji. Ustaw `--auth-mode` parametr `login` do autoryzacji przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Autoryzowanie dostępu do danych obiektów blob lub kolejek za pomocą interfejsu wiersza polecenia platformy Azure](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Parametr obsługuje tylko `--auth-mode` operacje przechowywania obiektów blob. Operacje zarządzania, takie jak tworzenie grupy zasobów lub konta magazynu, automatycznie używają poświadczeń usługi Azure AD do autoryzacji.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure można utworzyć za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Do utworzenia konta magazynu (ogólnego przeznaczenia) służy polecenie [az storage account create](/cli/azure/storage/account). Konta magazynu można używać z wszystkimi czterema usługami: obiektami blob, plikami, tabelami i kolejkami.

Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Grupy obiektów blob można organizować w kontenerach, podobnie jak pliki na komputerze w folderach.

Do tworzenia kontenera do przechowywania obiektów blob służy polecenie [az storage container create](/cli/azure/storage/container). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Przesyłanie obiektów blob

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Przykłady w tym przewodniku Szybki start pokazują, jak pracować z blokowymi obiektami blob.

Najpierw utwórz plik do przekazania do bloku obiektu blob. Jeśli używasz usługi Azure Cloud Shell, użyj następującego polecenia, aby utworzyć plik:

```bash
vi helloworld
```

Po otwarciu pliku naciśnij klawisz **Wstaw .** Wpisz *Hello world*, a następnie naciśnij klawisz **Esc**. Następnie wpisz *:x*, a następnie naciśnij **klawisz Enter**.

W tym przykładzie do kontenera utworzonego w ostatnim kroku zostanie przekazany obiekt blob za pomocą polecenia [az storage blob upload](/cli/azure/storage/blob). Nie jest konieczne określenie ścieżki pliku, ponieważ plik został utworzony w katalogu głównym. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Jeśli obiekt blob nie istnieje, zostanie utworzony. W przeciwnym razie zostanie zastąpiony. Przed kontynuowaniem można przesłać dowolną liczbę plików.

Aby przekazać jednocześnie wiele plików, możesz użyć polecenia [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Do wyświetlania listy obiektów blob w kontenerze służy polecenie [az storage blob list](/cli/azure/storage/blob). Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Użyj polecenia [az storage blob download](/cli/azure/storage/blob), aby pobrać przesłany wcześniej obiekt blob. Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy oferuje wydajny, skryptowy transfer danych dla usługi Azure Storage. Za pomocą programu AzCopy można przesyłać dane do i z magazynu obiektów Blob i usługi Azure Files. Aby uzyskać więcej informacji na temat AzCopy v10, najnowszej wersji programu AzCopy, zobacz [Wprowadzenie do AzCopy](../common/storage-use-azcopy-v10.md). Aby dowiedzieć się więcej na temat korzystania z pamięci AzCopy w wersji 10 z magazynem obiektów Blob, zobacz [Przenoszenie danych za pomocą magazynu AzCopy i Blob](../common/storage-use-azcopy-blobs.md).

W poniższym przykładzie użyto AzCopy do przekazania pliku lokalnego do obiektu blob. Pamiętaj, aby zastąpić przykładowe wartości własnymi wartościami:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć zasoby utworzone w ramach tego przewodnika Szybki start, w tym konto magazynu, usuń grupę zasobów za pomocą polecenia [usuń grupę az.](/cli/azure/group) Pamiętaj, aby zastąpić wartości zastępcze w nawiasach kątowych własnymi wartościami:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób przesyłania plików między lokalnym systemem plików a kontenerem w magazynie obiektów Blob platformy Azure. Aby dowiedzieć się więcej na temat pracy z obiektami blob w usłudze Azure Storage, przejdź do samouczka dotyczącego pracy z usługą Azure Blob Storage.

> [!div class="nextstepaction"]
> [Instrukcje: operacje przeprowadzane w usłudze Blob Storage przy użyciu interfejsu wiersza polecenia platformy Azure](storage-how-to-use-blobs-cli.md)
