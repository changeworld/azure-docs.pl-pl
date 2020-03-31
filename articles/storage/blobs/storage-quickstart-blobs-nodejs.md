---
title: 'Szybki start: biblioteka magazynu obiektów Blob platformy Azure w wersji 12 — JavaScript'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć kontener i obiektowy magazyn za pomocą biblioteki klienta magazynu obiektów Azure Blob w wersji 12 dla języka JavaScript. Następnie dowiesz się, jak pobrać obiekt blob na komputer lokalny i jak wyświetlać listę wszystkich obiektów blob w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e68e91d90846ab77b994b53be7a84a9dd8bc5a25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241038"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Szybki start: zarządzanie obiektami blob za pomocą kodu JavaScript w wersji 12 SDK w pliku Node.js

W tym przewodniku Szybki start nauczysz się zarządzać obiektami blob za pomocą pliku Node.js. Obiekty blob to obiekty, które mogą zawierać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwum. Będziesz przesyłać, pobierać i wystawiać obiekty blob, a także tworzyć i usuwać kontenery.

[Dokumentacja](/javascript/api/@azure/storage-blob) | dokumentacji interfejsu API[Przykłady kodu źródłowego pakietu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [(Menedżera pakietów węzłów)](https://www.npmjs.com/package/@azure/storage-blob) | [Samples](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Aby rozpocząć pracę z poprzednią wersją SDK, zobacz [Szybki start: Zarządzanie obiektami blob za pomocą pliku JavaScript w wersji 10 SDK w pliku Node.js](storage-quickstart-blobs-nodejs-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Konfigurowanie

W tej sekcji przeprowadzi Cię proces przygotowania projektu do pracy z biblioteką klienta magazynu obiektów Blob platformy Azure w wersji 12 dla języka JavaScript.

### <a name="create-the-project"></a>Tworzenie projektu

Utwórz aplikację JavaScript o nazwie *blob-szybki start-v12*.

1. W oknie konsoli (takich jak cmd, PowerShell lub Bash) utwórz nowy katalog dla projektu.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Przełącz się do nowo utworzonego katalogu *blob-szybki start-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Utwórz nowy plik tekstowy o nazwie *package.json*. Ten plik definiuje projekt Node.js. Zapisz ten plik w katalogu *blob-szybki start-v12.* Oto zawartość pliku:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Jeśli chcesz, możesz umieścić `author` swoje własne imię i nazwisko w polu.
   
### <a name="install-the-package"></a>Zainstaluj pakiet

Będąc jeszcze w katalogu *blob-szybki start-v12,* zainstaluj bibliotekę klienta magazynu obiektów `npm install` Blob platformy Azure dla pakietu JavaScript za pomocą polecenia. To polecenie odczytuje plik *package.json* i instaluje bibliotekę klienta magazynu obiektów Azure Blob w wersji 12 dla pakietu JavaScript i wszystkie biblioteki, od których zależy.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Otwieranie kolejnego nowego pliku tekstowego w edytorze kodu
1. Dodawanie `require` wywołań w celu załadowania modułów platformy Azure i node.js
1. Tworzenie struktury programu, w tym podstawowej obsługi wyjątków

    Oto kod:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Zapisz nowy plik jako *blob-szybki start-v12.js* w katalogu *blob-szybki start-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Model obiektu

Usługa Azure Blob storage jest zoptymalizowana pod kątem przechowywania ogromnych ilości nieustrukturyzowanych danych. Dane bez struktury są danymi, które nie są zgodne z żadnym modelem lub definicją danych, jak na przykład dane tekstowe lub binarne. Magazyn obiektów blob oferuje trzy typy zasobów:

* Konto magazynu
* Kontener na koncie magazynu
* Obiekt blob w kontenerze

Na poniższym diagramie przedstawiono relacje między tymi zasobami.

![Diagram architektury magazynu obiektów blob](./media/storage-blobs-introduction/blob1.png)

Użyj następujących klas JavaScript do interakcji z tymi zasobami:

* [BlobServiceClient:](/javascript/api/@azure/storage-blob/blobserviceclient) `BlobServiceClient` Klasa umożliwia manipulowanie zasobami usługi Azure Storage i kontenerami obiektów blob.
* [ContainerClient:](/javascript/api/@azure/storage-blob/containerclient) `ContainerClient` Klasa umożliwia manipulowanie kontenerami usługi Azure Storage i ich obiektów blob.
* [BlobClient:](/javascript/api/@azure/storage-blob/blobclient) `BlobClient` Klasa umożliwia manipulowanie obiektów blob usługi Azure Storage.

## <a name="code-examples"></a>Przykłady kodu

Te przykładowe fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta magazynu obiektów Blob platformy Azure dla języka JavaScript:

* [Pobieranie parametrów połączenia](#get-the-connection-string)
* [Tworzenie kontenera](#create-a-container)
* [Przekazywanie obiektów blob do kontenera](#upload-blobs-to-a-container)
* [Wyświetlanie listy obiektów blob w kontenerze](#list-the-blobs-in-a-container)
* [Pobieranie obiektów blob](#download-blobs)
* [Usuwanie kontenera](#delete-a-container)

### <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Poniższy kod pobiera parametry połączenia dla konta magazynu ze zmiennej środowiskowej utworzonej w sekcji [Konfigurowanie ciągu połączenia magazynu.](#configure-your-storage-connection-string)

Dodaj ten kod `main` wewnątrz funkcji:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Tworzenie kontenera

Zdecyduj o nazwie nowego kontenera. Poniższy kod dołącza wartość UUID do nazwy kontenera, aby upewnić się, że jest unikatowa.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji o nazewnictwie kontenerów i obiektów blob, zobacz temat [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Utwórz wystąpienie klasy [BlobServiceClient,](/javascript/api/@azure/storage-blob/blobserviceclient) wywołując metodę [fromConnectionString.](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) Następnie wywołać [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metody, aby uzyskać odwołanie do kontenera. Na koniec wywołaj [utwórz,](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) aby faktycznie utworzyć kontener na koncie magazynu.

Dodaj ten kod na `main` końcu funkcji:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Przekazywanie obiektów blob do kontenera

Fragment kodu w brzmieniu:

1. Tworzy ciąg tekstowy do przekazania do obiektu blob.
1. Pobiera odwołanie do [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) obiektu wywołując [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metody na [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) z Create sekcji [kontenera.](#create-a-container)
1. Przekazuje dane ciągu tekstowego do obiektu blob, wywołując metodę [przekazywania.](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-)

Dodaj ten kod na `main` końcu funkcji:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Lista obiektów blob w kontenerze, wywołując [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) metody. W takim przypadku tylko jeden obiekt blob został dodany do kontenera, więc operacja listy zwraca tylko ten jeden obiekt blob.

Dodaj ten kod na `main` końcu funkcji:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Pobieranie obiektów blob

Pobierz poprzednio utworzony obiekt blob, wywołując metodę [pobierania.](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) Przykładowy kod zawiera funkcję `streamToString`pomocnika o nazwie , która służy do odczytu strumienia do odczytu Node.js w ciągu.

Dodaj ten kod na `main` końcu funkcji:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Dodaj tę funkcję *after* pomocnika `main` po funkcji:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Poniższy kod czyści zasoby utworzone przez aplikację, usuwając cały kontener przy użyciu metody [usuwania.](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) Można również usunąć pliki lokalne, jeśli chcesz.

Dodaj ten kod na `main` końcu funkcji:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>Uruchamianie kodu

Ta aplikacja tworzy ciąg tekstowy i przekazuje go do magazynu obiektów Blob. W przykładzie następnie wyświetla listę obiektów blob w kontenerze, pobiera obiekt blob i wyświetla pobrane dane.

Z monitu konsoli przejdź do katalogu zawierającego plik *blob-quickstart-v12.py,* a `node` następnie wykonaj następujące polecenie, aby uruchomić aplikację.

```console
node blob-quickstart-v12.js
```

Dane wyjściowe aplikacji są podobne do następującego przykładu:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Krok po kroku kodu w debugerze i sprawdź witryny [Azure portal](https://portal.azure.com) w trakcie całego procesu. Sprawdź, czy kontener jest tworzony. Można otworzyć obiekt blob wewnątrz kontenera i wyświetlić zawartość.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak przesyłać, pobierać i wystawiać obiekty blob za pomocą języka JavaScript.

Aby uzyskać samouczki, przykłady, przewodniki Szybki start i inną dokumentację, odwiedź stronę:

> [!div class="nextstepaction"]
> [Dokumentacja platformy Azure dla języka JavaScript](/azure/javascript/)

* Aby dowiedzieć się więcej, zobacz [bibliotekę klienta magazynu obiektów Blob platformy Azure dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob).
* Aby wyświetlić przykładowe aplikacje magazynu obiektów Blob, przejdź do [przykładów biblioteki klienta magazynu obiektów Blob platformy Azure w wersji 12 w języku JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
