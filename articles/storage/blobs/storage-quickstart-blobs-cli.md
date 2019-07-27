---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym przewodniku Szybki start interfejs wiersza polecenia platformy Azure jest używany w ramach magazynu obiektów (blob). Następnie przy użyciu interfejsu wiersza polecenia przekażesz obiekt blob do usługi Azure Storage, pobierzesz obiekt blob i wyświetlisz obiekty blob w kontenerze.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.reviewer: seguler
ms.openlocfilehash: 6a0aef9b2fc7a99183ebd6991691245731e00200
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565951"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Szybki start: przekazywanie, pobieranie i wyświetlanie listy obiektów blob za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure to środowisko wiersza polecenia platformy Azure do zarządzania jej zasobami. Można używać go w przeglądarce za pośrednictwem usługi Azure Cloud Shell. Istnieje także możliwość zainstalowania go w systemach macOS, Linux lub Windows, a następnie uruchomienia z poziomu wiersza polecenia. Ten przewodnik Szybki start zawiera opis użycia interfejsu wiersza polecenia platformy Azure do przekazywania danych do/pobierania danych z usługi Azure Blob Storage.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik szybkiego startu będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby sprawdzić wersję. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Tworzenie kontenera

Obiekty blob są zawsze przesyłane do kontenera. Możesz organizować grupy obiektów blob w sposób podobny do organizowania plików w folderach na komputerze.

Do tworzenia kontenera do przechowywania obiektów blob służy polecenie [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Przesyłanie obiektów blob

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Większość plików przechowywanych w usłudze Blob Storage jest przechowywana jako blokowe obiekty blob. Uzupełnialne obiekty blob są używane w razie konieczności dodania danych do istniejącego obiektu blob bez modyfikowania jego zawartości, na przykład w przypadku rejestrowania. Stronicowe obiekty blob obsługują pliki VHD maszyn wirtualnych IaaS.

Najpierw utwórz plik do przekazania do obiektu blob.
Jeśli używasz usługi Azure Cloud Shell, utwórz plik za pomocą następującego polecenia: `vi helloworld` po otwarciu pliku, naciśnij klawisz **Insert**, wpisz „Hello wrold”, a następnie naciśnij klawisz **Esc**, wpisz `:x` i naciśnij klawisz **Enter**.

W tym przykładzie do kontenera utworzonego w ostatnim kroku zostanie przekazany obiekt blob za pomocą polecenia [az storage blob upload](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Jeśli w usłudze Azure Cloud Shell utworzono plik przy użyciu wcześniej opisanej metody, możesz zamiast tego użyć następującego polecenia interfejsu wiersza polecenia (zauważ, że nie trzeba było podawać ścieżki, ponieważ plik utworzono w katalogu podstawowym; zazwyczaj podanie ścieżki jest konieczne):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld \
    --file helloworld
```

Jeśli obiekt blob nie istnieje, zostanie utworzony. W przeciwnym razie zostanie zastąpiony. Przed kontynuowaniem można przesłać dowolną liczbę plików.

Aby przekazać jednocześnie wiele plików, możesz użyć polecenia [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Do wyświetlania listy obiektów blob w kontenerze służy polecenie [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Użyj polecenia [az storage blob download](/cli/azure/storage/blob), aby pobrać przesłany wcześniej obiekt blob.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) to kolejna opcja umożliwiająca wydajny transfer danych do usługi Azure Storage z użyciem skryptów. Narzędzia AzCopy można używać do transferu danych do i z magazynu obiektów blob, plików i tabel.

Oto przykładowe polecenie AzCopy umożliwiające przesłanie pliku o nazwie *myfile.txt* do kontenera *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie są już potrzebne żadne zasoby z grupy zasobów, w tym konto magazynu utworzone w ramach tego przewodnika Szybki start, usuń grupę zasobów za pomocą polecenia [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i kontenerem w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat pracy z obiektami blob w usłudze Azure Storage, przejdź do samouczka dotyczącego pracy z usługą Azure Blob Storage.

> [!div class="nextstepaction"]
> [Instrukcje: operacje przeprowadzane w usłudze Blob Storage przy użyciu interfejsu wiersza polecenia platformy Azure](storage-how-to-use-blobs-cli.md)
