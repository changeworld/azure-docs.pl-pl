---
title: Przekazywanie, pobieranie, wyświetlanie i usuwanie obiektów blob za pomocą zestawu SDK usługi Azure Storage w wersji 10 dla języka JavaScript
description: Tworzenie, przekazywanie i usuwanie obiektów blob oraz kontenerów w środowisku Node.js za pomocą usługi Azure Storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/24/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: f8c7de63f2bd4b7329e8ae6a53123c9c1ea035af
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240444"
---
# <a name="quickstart-upload-download-list-and-delete-blobs-using-azure-storage-v10-sdk-for-javascript"></a>Szybki start: Przekazywanie, pobieranie, wyświetlanie i usuwanie obiektów blob za pomocą zestawu SDK usługi Azure Storage w wersji 10 dla języka JavaScript

W tym przewodniku Szybki start dowiesz się, jak za pomocą [zestawu SDK usługi Azure Storage w wersji 10 dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js) w środowisku Node.js przekazywać, pobierać, wyświetlać i usuwać obiekty blob oraz zarządzać kontenerami.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) używana w tym przewodniku Szybki start to prosta aplikacja konsolowa Node.js. Aby rozpocząć, sklonuj repozytorium na swoją maszynę przy użyciu następującego polecenia:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Następnie zmień foldery dla aplikacji:

```bash
cd azure-storage-js-v10-quickstart
```

Teraz otwórz folder w swoim ulubionym środowisku edycji kodu.

## <a name="configure-your-storage-credentials"></a>Konfigurowanie poświadczeń magazynu

Zanim uruchomisz aplikację, musisz wprowadzić poświadczenia zabezpieczeń konta magazynu. Przykładowe repozytorium zawiera plik o nazwie *.env.example*. Zmień nazwę tego pliku, usuwając rozszerzenie *example*, aby otrzymać plik o nazwie *env*. W pliku *env* dodaj nazwę swojego konta i wartości kluczy dostępu po kluczach *AZURE_STORAGE_ACCOUNT_NAME* i *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

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

Dane wyjściowe aplikacji będą mieć postać podobną do następującego przykładu:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Jeśli używasz nowego konta magazynu dla tego przewodnika Szybki Start, możesz zobaczyć tylko kontener *demonstracyjny* w obszarze etykieta "*kontenery:* ".

## <a name="understanding-the-code"></a>Omówienie kodu

Przykład zaczyna się od zaimportowania pewnej liczby klas i funkcji z przestrzeni nazw usługi Azure Blob Storage. Każdy z zaimportowanych elementów jest omawiany w kontekście, w jakim jest używany w tym przykładzie.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Poświadczenia są odczytywane ze zmiennych środowiskowych na podstawie odpowiedniego kontekstu.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

Moduł *dotenv* ładuje zmienne środowiskowe podczas uruchamiania aplikacji lokalnie na potrzeby debugowania. Wartości są definiowane w pliku *env* i ładowane do bieżącego kontekstu wykonania. W środowisku produkcyjnym konfiguracja serwera udostępnia te wartości, dlatego ten kod jest uruchamiany tylko wtedy, gdy skrypt działa w środowisku *innym* niż „produkcja”.

Kolejny blok modułów jest importowany, aby pomóc interfejsowi z systemem plików.

```javascript
const fs = require('fs');
const path = require('path');
```

Poniżej przedstawiono funkcje tych modułów: 

- Moduł *fs* to natywny moduł środowiska Node.js używany do pracy z systemem plików

- Moduł *path* jest wymagany do określenia ścieżki bezwzględnej pliku, która jest używana podczas przekazywania pliku do magazynu obiektów blob

Następnie wartości zmiennych środowiskowych są odczytywane i odkładane w stałych.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
Kolejny zestaw stałych pozwala ujawnić intencje obliczeń rozmiaru pliku podczas operacji przekazywania.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Żądania wysyłane przez interfejs API można ustawić tak, aby wygasały po upływie określonego interwału. Klasa [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-preview) jest odpowiedzialna za zarządzanie sposobem wygasania żądań, a poniższa stała służy do definiowania limitów czasu używanych w tym przykładzie.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Kod wywołujący

Aby umożliwić obsługę składni *async/await* języka JavaScript, cały kod wywołujący został opakowany w funkcję o nazwie *execute*. Następnie funkcja *execute* jest wywoływana i obsługiwana jako obietnica.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Cały poniższy kod jest wywoływany wewnątrz funkcji execute, w której umieszczono komentarz `// commands...`.

Najpierw odpowiednie zmienne są deklarowane w celu przypisania nazw, utworzenia przykładu zawartości oraz wskazania pliku lokalnego do przekazania do magazynu obiektów blob.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Poświadczenia konta służą do tworzenia potoku, który jest odpowiedzialny za zarządzanie sposobem wysyłania żądań do interfejsu API REST. Potoki są bezpieczne wątkowo i określają logikę zasad ponawiania, rejestrowania, reguł deserializacji odpowiedzi HTTP itp.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

W tym bloku kodu są używane następujące klasy:

- Klasa [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-preview) jest odpowiedzialna za opakowywanie poświadczeń konta magazynu w celu dostarczenia ich do potoku żądań.

- Klasa [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-preview) jest odpowiedzialna za tworzenie nowego potoku.

- Klasa [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-preview) modeluje adres URL używany w interfejsie API REST. Wystąpienia tej klasy umożliwiają wykonywanie akcji, takich jak wyświetlanie kontenerów i podawanie informacji o kontekście w celu generowania adresów URL kontenerów.

Wystąpienie klasy *ServiceURL* jest używane z wystąpieniami [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-preview) i [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-preview) w celu zarządzania kontenerami i obiektami blob na koncie magazynu.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

Zmienne *containerURL* i *blockBlobURL* są używane wielokrotnie w tym przykładzie na potrzeby działań związanych z kontem magazynu. 

W tym momencie na koncie magazynu nie ma kontenera. Wystąpienie zmiennej *ContainerURL* reprezentuje adres URL, na którym możesz działać. Używając tego wystąpienia, możesz utworzyć i usunąć kontener. Lokalizacja tego kontenera jest taka sama jak następująca:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

Zmienna *blockBlobURL* służy do zarządzania poszczególnymi obiektami blob, umożliwiając przekazywanie, pobieranie i usuwanie zawartości obiektów blob. Reprezentowany w tym miejscu adres URL jest podobny do tej lokalizacji:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Podobnie jak w przypadku kontenera, blokowy obiekt blob jeszcze nie istnieje. Zmienna *blockBlobURL* jest używana później do utworzenia obiektu blob przez przekazanie zawartości.

### <a name="using-the-aborter-class"></a>Używanie klasy Aborter

Żądania wysyłane przez interfejs API można ustawić tak, aby wygasały po upływie określonego interwału. Klasa *Aborter* jest odpowiedzialna za zarządzanie sposobem wygasania żądań. Poniższy kod tworzy kontekst, w którym zestaw żądań otrzymuje 30 minut na wykonanie.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Klasy Aborter zapewniają kontrolę nad żądaniami, umożliwiając:

- wyznaczanie ilości czasu dla partii żądań
- wyznaczanie czasu na wykonanie pojedynczego żądania w partii
- anulowanie żądań
- używanie elementu statycznego *Aborter.none* w celu zatrzymania upływu limitu czasu dla wszystkich żądań

### <a name="create-a-container"></a>Tworzenie kontenera

Do tworzenia kontenera służy metoda *create* klasy *ContainerURL*.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Ponieważ nazwa kontenera jest definiowana podczas wywoływania funkcji *ContainerURL.fromServiceURL (serviceURL, containerName)* , do utworzenia kontenera potrzebne jest jedynie wywołanie metody *create*.

### <a name="show-container-names"></a>Wyświetlanie nazw kontenerów

Na kontach można przechowywać ogromną liczbę kontenerów. Poniższy kod przedstawia sposób wyświetlania listy kontenerów podzielonej na segmenty, co umożliwia przeglądanie dużej liczby kontenerów. Funkcja *ShowContainerNames* przekazuje wystąpienia klas *ServiceURL* i *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

Funkcja *ShowContainerNames* używa metody *listContainersSegment*, aby zażądać partii nazw kontenerów z konta magazynu.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Po zwróceniu odpowiedzi powtarzane są elementy *containerItem* w celu zarejestrowania nazwy w konsoli. 

### <a name="upload-text"></a>Przekazywanie tekstu

Do przekazywania tekstu do obiektu blob służy metoda *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

W tym miejscu tekst i jego długość są przekazywane do metody.

### <a name="upload-a-local-file"></a>Przekazywanie pliku lokalnego

Aby przekazać plik lokalny do kontenera, potrzebujesz adresu URL kontenera i ścieżki do pliku.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

Funkcja *uploadLocalFile* wywołuje funkcję *uploadFileToBlockBlob*, która jako argumenty przyjmuje ścieżkę pliku oraz wystąpienie miejsca docelowego dla blokowego obiektu blob.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Przekazywanie strumienia

Obsługiwane jest również przekazywanie strumieni. W tym przykładzie plik lokalny jest otwierany jako strumień, który zostanie przekazany do metody upload.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

Funkcja *uploadStream* wywołuje funkcję *uploadStreamToBlockBlob* w celu przekazania strumienia do kontenera magazynu.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Podczas przekazywania funkcja *uploadStreamToBlockBlob* przydziela bufory w celu buforowania danych ze strumienia, gdy konieczne jest ponowienie. Wartość *maxBuffers* określa maksymalną liczbę używanych buforów, ponieważ każdy bufor tworzy oddzielne żądanie przekazywania. W idealnym przypadku więcej buforów oznacza większe szybkości, ale kosztem większego użycia pamięci. Szybkość przekazywania osiąga pułap możliwości, gdy liczba buforów jest na tyle duża, że wąskie gardło pojawia się w sieci lub na dysku, zamiast u klienta.

### <a name="show-blob-names"></a>Wyświetlanie nazw obiektów blob

Tak jak konta mogą zawierać wiele kontenerów, tak samo każdy kontener potencjalnie może zawierać ogromną liczbę obiektów blob. Dostęp do poszczególnych obiektów blob w kontenerze jest możliwy za pośrednictwem wystąpienia klasy *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

Funkcja *showBlobNames* wywołuje funkcję *listBlobFlatSegment*, aby zażądać partii obiektów blob z kontenera.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Pobieranie obiektu blob

Po utworzeniu obiektu blob możesz pobrać zawartość przy użyciu metody *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Odpowiedź jest zwracana jako strumień. W tym przykładzie strumień jest konwertowany na ciąg za pomocą następującej funkcji pomocnika *streamToString* .

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Usuwanie obiektu blob

Metoda *delete* z wystąpienia *BlockBlobURL* usuwa obiekt blob z kontenera.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Usuwanie kontenera

Metoda *delete* z wystąpienia *ContainerURL* usuwa kontener z konta magazynu.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Wszystkie dane zapisane na koncie magazynu są automatycznie usuwane po zakończeniu pracy z przykładowym kodem. 

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start pokazano, jak zarządzać obiektami blob i kontenerami w usłudze Azure Blob Storage przy użyciu środowiska Node.js. Aby dowiedzieć się więcej na temat pracy z tym zestawem SDK, zapoznaj się z repozytorium GitHub.

> [!div class="nextstepaction"]
> [Zestaw SDK usługi Azure Storage w wersji 10 dla repozytorium języka JavaScript](https://github.com/Azure/azure-storage-js)
> [Dokumentacja interfejsu API języka JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/storage/client)
