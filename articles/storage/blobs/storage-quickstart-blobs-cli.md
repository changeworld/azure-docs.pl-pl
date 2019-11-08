---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: W tym przewodniku szybki start dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure przekazać obiekt BLOB do usługi Azure Storage, pobrać obiekt BLOB i wyświetlić listę obiektów BLOB w kontenerze.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747929"
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
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Przesyłanie obiektów blob

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Przykłady w tym przewodniku szybki start pokazują, jak korzystać z blokowych obiektów BLOB.

Najpierw utwórz plik do przekazania do blokowego obiektu BLOB. Jeśli używasz Azure Cloud Shell, użyj następującego polecenia, aby utworzyć plik:

```bash
vi helloworld
```

Gdy plik zostanie otwarty, naciśnij klawisz **INSERT**. Wpisz *Hello World*, a następnie naciśnij klawisz **ESC**. Następnie wpisz *: x*, a następnie naciśnij klawisz **Enter**.

W tym przykładzie do kontenera utworzonego w ostatnim kroku zostanie przekazany obiekt blob za pomocą polecenia [az storage blob upload](/cli/azure/storage/blob). Nie trzeba określać ścieżki pliku, ponieważ plik został utworzony w katalogu głównym:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Jeśli obiekt blob nie istnieje, zostanie utworzony. W przeciwnym razie zostanie zastąpiony. Przed kontynuowaniem można przesłać dowolną liczbę plików.

Aby przekazać jednocześnie wiele plików, możesz użyć polecenia [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Do wyświetlania listy obiektów blob w kontenerze służy polecenie [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Użyj polecenia [az storage blob download](/cli/azure/storage/blob), aby pobrać przesłany wcześniej obiekt blob.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transfer danych przy użyciu narzędzia AzCopy

Narzędzie [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) to kolejna opcja umożliwiająca wydajny transfer danych do usługi Azure Storage z użyciem skryptów. Narzędzia AzCopy można używać do transferu danych do i z magazynu obiektów blob, plików i tabel.

Poniższy przykład używa AzCopy do przekazania pliku o nazwie plik *. txt* do kontenera *przykładowego kontenera* . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już żadnych zasobów w grupie zasobów, w tym konta magazynu utworzonego w ramach tego przewodnika Szybki Start, Usuń grupę zasobów za pomocą polecenia [AZ Group Delete](/cli/azure/group) . Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób transferu plików między lokalnym systemem plików i kontenerem w usłudze Azure Blob Storage. Aby dowiedzieć się więcej na temat pracy z obiektami blob w usłudze Azure Storage, przejdź do samouczka dotyczącego pracy z usługą Azure Blob Storage.

> [!div class="nextstepaction"]
> [Instrukcje: operacje przeprowadzane w usłudze Blob Storage przy użyciu interfejsu wiersza polecenia platformy Azure](storage-how-to-use-blobs-cli.md)
