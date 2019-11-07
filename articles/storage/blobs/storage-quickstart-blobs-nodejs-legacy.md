---
title: Tworzenie obiektu BLOB w usłudze Azure Storage dla środowiska Node. js
description: Utwórz konto magazynu i kontener w magazynie obiektów (blob). Następnie użyj biblioteki klienta usługi Azure Storage dla środowiska Node. js v2, aby przekazać obiekt BLOB do usługi Azure Storage, pobrać obiekt BLOB i wyświetlić listę obiektów BLOB w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 8d0afc5a224e752bdd745ab4df0473a134b4180b
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609101"
---
# <a name="upload-download-and-list-blobs-using-the-client-library-for-nodejs"></a>Przekazywanie, pobieranie i wyświetlanie listy obiektów BLOB przy użyciu biblioteki klienckiej środowiska Node. js

W tym przewodniku krok po kroku dowiesz się, jak używać biblioteki klienckiej programu Node. js v2 do przekazywania, pobierania i wyświetlania listy obiektów BLOB za pomocą usługi Azure Blob Storage.

> [!TIP]
> Najnowsza wersja biblioteki klienta usługi Azure Storage dla środowiska Node. js to V10. Firma Microsoft zaleca, aby użyć najnowszej wersji biblioteki klienta, gdy jest to możliwe. Aby rozpocząć korzystanie z usługi V10, zobacz [Szybki Start: przekazywanie, pobieranie, wyświetlanie listy i usuwanie obiektów BLOB przy użyciu biblioteki klienta usługi Azure Storage dla języka JavaScript v10 (wersja zapoznawcza)](storage-quickstart-blobs-nodejs-v10.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Utwórz konto usługi Azure Storage w [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Aby uzyskać pomoc przy tworzeniu konta, zobacz [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) jest prostą aplikacją konsolową Node. js. Aby rozpocząć, sklonuj repozytorium na swoją maszynę przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Aby otworzyć tę aplikację, wyszukaj folder *storage-blobs-node-quickstart* i otwórz go w środowisku edycji kodu.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Aby uruchomić aplikację, musisz wprowadzić parametry połączenia konta magazynu. Przykładowe repozytorium zawiera plik o nazwie *.env.example*. Zmień nazwę tego pliku, usuwając rozszerzenie *.example*, aby otrzymać plik o nazwie *.env*. W pliku *.env* dodaj wartość parametrów połączenia po kluczu *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Instalowanie wymaganych pakietów

W katalogu aplikacji uruchom polecenie *npm install*, aby zainstalować pakiety wymagane przez aplikację.

```bash
npm install
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Po zainstalowaniu zależności możesz uruchomić przykład, wykonując następujące polecenie:

```bash
npm start
```

Dane wyjściowe skryptu będą mieć postać podobną do następującej:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Jeśli na potrzeby tego przykładu używasz nowego konta magazynu, nazwy kontenerów mogą nie być wyświetlane w obszarze etykieta "*kontenery*".

## <a name="understanding-the-code"></a>Omówienie kodu
Pierwsze wyrażenie jest używane do ładowania wartości do zmiennych środowiskowych.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

Moduł *dotenv* ładuje zmienne środowiskowe podczas uruchamiania aplikacji lokalnie na potrzeby debugowania. Wartości są definiowane w pliku *env* i ładowane do bieżącego kontekstu wykonania. W kontekstach produkcyjnych konfiguracja serwera udostępnia te wartości, dlatego ten kod jest uruchamiany tylko wtedy, gdy skrypt działa w kontekście innym niż „produkcja”.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Poniżej przedstawiono funkcje tych modułów: 

plik o nazwie *env* do bieżącego kontekstu wykonywania
- *path* — jest wymagany do określenia ścieżki bezwzględnej do pliku przekazywanego do magazynu obiektów blob
- *Azure-Storage* to moduł [biblioteki klienta usługi Azure Storage](https://docs.microsoft.com/javascript/api/azure-storage) dla środowiska Node. js

Następnie zmienna **blobService** jest inicjowana jako nowe wystąpienie usługi Azure Blob Service.

```javascript
const blobService = storage.createBlobService();
```

W poniższej implementacji każda funkcja *blobService* jest opakowana w obiekt *Promise*. Pozwala to korzystać z funkcji *async* i operatora *await* języka JavaScript w celu usprawnienia wywołań zwrotnych [interfejsu API usługi Azure Storage](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Jeśli poszczególne funkcje zwrócą pomyślne odpowiedzi, obiekt Promise wskazuje na właściwe dane i komunikat specyficzny dla akcji.

### <a name="list-containers"></a>Wyświetlanie listy kontenerów

Funkcja *listContainers* wywołuje element [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), który zwraca kolekcje kontenerów w grupach.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

Rozmiar grup można konfigurować za pomocą elementu [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Wywołanie metody *listContainersSegmented* zwraca metadane obiektu blob jako tablicę wystąpień klasy [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest). Wyniki są zwracane w partiach inkrementowanych o 5000 (segmenty). Jeśli kontener zawiera ponad 5000 obiektów blob, do wyników jest dołączana wartość *tokenu kontynuacji*. Aby wyświetlić listę kolejnych segmentów z kontenera obiektów blob, można przekazać token kontynuacji do metody *listContainersSegment* jako drugi argument.

### <a name="create-a-container"></a>Tworzenie kontenera

Funkcja *createContainer* wywołuje funkcję [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) i ustawia odpowiedni poziom dostępu dla obiektu blob.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

Drugi parametr — (*options*) — funkcji **createContainerIfNotExists** przyjmuje wartość [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Wartość *blob* parametru *publicAccessLevel* określa, że dane konkretnego obiektu blob są dostępne publicznie. To ustawienie jest inne niż poziom dostępu *container*, który umożliwia wyświetlanie zawartości kontenera.

Użycie funkcji **createContainerIfNotExists** pozwala aplikacji wielokrotnie uruchamiać polecenie *createContainer* bez zwracania błędów, jeśli kontener już istnieje. W środowisku produkcyjnym funkcja **createContainerIfNotExists** zwykle jest wywoływana tylko raz, ponieważ w całej aplikacji jest używany ten sam kontener. W takiej sytuacji można wcześniej utworzyć kontener za pośrednictwem portalu lub przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="upload-text"></a>Przekazywanie tekstu

Funkcja *uploadString* wywołuje element [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) w celu zapisania (lub nadpisania) dowolnego ciągu w kontenerze obiektów blob.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Przekazywanie pliku lokalnego

Za pomocą funkcji *createBlockBlobFromLocalFile* funkcja [uploadLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--) przekazuje plik z systemu plików do magazynu obiektów blob i go zapisuje lub nadpisuje. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Inne metody przekazywania zawartości do obiektów blob obejmują użycie [tekstu](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) i [strumieni](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Aby sprawdzić, czy plik został przekazany do magazynu obiektów blob, można użyć [Eksploratora usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) w celu wyświetlenia danych na koncie.

### <a name="list-the-blobs"></a>Wyświetlanie listy obiektów blob

Funkcja *listBlobs* wywołuje metodę [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) w celu zwrócenia listy metadanych obiektu blob w kontenerze. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Metoda *listBlobsSegmented* zwraca metadane obiektu blob jako tablicę wystąpień klasy [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest). Wyniki są zwracane w partiach inkrementowanych o 5000 (segmenty). Jeśli kontener zawiera ponad 5000 obiektów blob, do wyników jest dołączana wartość **tokenu kontynuacji**. Aby wyświetlić listę kolejnych segmentów z kontenera obiektów blob, można przekazać token kontynuacji do metody **listBlobsSegmented** jako drugi argument.

### <a name="download-a-blob"></a>Pobieranie obiektu blob

Za pomocą funkcji *getBlobToText* funkcja [downloadBlob](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) pobiera zawartość obiektu blob do danej ścieżki bezwzględnej do pliku.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
Przedstawiona w tym miejscu implementacja zmienia źródło i zwraca zawartość obiektu blob jako ciąg. Możesz również pobrać obiekt blob jako [strumień](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest), a także bezpośrednio do [pliku lokalnego](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Usuwanie obiektu blob

Funkcja *deleteBlob* wywołuje funkcję [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--). Jak sugeruje jej nazwa, ta funkcja nie zwraca błędu, jeśli obiekt blob został już usunięty.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Kontenery są usuwane przez wywołanie metody *deleteContainer* z usługi obiektów blob i przekazanie nazwy kontenera.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Kod wywołujący

Aby umożliwić obsługę składni *async/await* języka JavaScript, cały kod wywołujący został opakowany w funkcję o nazwie *execute*. Następnie funkcja execute jest wywoływana i obsługiwana jako obietnica.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Cały poniższy kod jest wywoływany wewnątrz funkcji execute, w której umieszczono komentarz `// commands`.

Najpierw odpowiednie zmienne są deklarowane w celu przypisania nazw, utworzenia przykładu zawartości oraz wskazania pliku lokalnego do przekazania do magazynu obiektów blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Aby wyświetlić listę kontenerów na koncie magazynu, jest wywoływana funkcja listContainers, a zwrócona lista kontenerów jest rejestrowana w oknie danych wyjściowych.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Po udostępnieniu listy kontenerów możesz użyć metody *findIndex* tablicy, aby zobaczyć, czy kontener, który chcesz utworzyć, już istnieje. Jeśli kontener nie istnieje, zostanie on utworzony.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Następnie ciąg i lokalny plik są przekazywane do magazynu obiektów blob.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Proces wyświetlania listy obiektów blob jest taki sam jak proces tworzenia listy kontenerów. Wywołanie do metody *listBlobs* zwraca tablicę obiektów blob w kontenerze. Obiekty te są rejestrowane w oknie danych wyjściowych.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Aby pobrać obiekt blob, odpowiedź jest przechwytywana i używana do uzyskiwania dostępu do wartości obiektu blob. Element readableStreamBody z odpowiedzi jest konwertowany na ciąg i rejestrowany w oknie danych wyjściowych.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Na koniec obiekt blob i kontener są usuwane z konta magazynu.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Wszystkie dane zapisane na koncie magazynu są automatycznie usuwane po zakończeniu pracy z przykładowym kodem. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Node.js z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji Node.js z magazynem obiektów blob:

### <a name="binaries-and-source-code"></a>Pliki binarne i kod źródłowy

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Node.js](https://github.com/Azure/azure-storage-node) dla usługi Azure Storage i zainstaluj go.

### <a name="client-library-reference-and-samples"></a>Dokumentacja i przykłady dotyczące biblioteka klienta

- Aby uzyskać więcej informacji na temat biblioteki klienta Node.js, zobacz [dokumentację interfejsu API platformy Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage).
- Zapoznaj się z [przykładami użycia usługi Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) napisanymi przy użyciu biblioteki klienta Node.js.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób przekazywania pliku między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu środowiska Node. js. Aby dowiedzieć się więcej na temat pracy z usługą Blob Storage, przejdź do repozytorium GitHub.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK dla środowiska Node. js i języka JavaScript dla przeglądarek](https://github.com/Azure/azure-storage-node)
