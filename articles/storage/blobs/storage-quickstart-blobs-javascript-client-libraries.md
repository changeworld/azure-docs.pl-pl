---
title: 'Szybki Start: usługa Azure Blob Storage dla języka JavaScript v2 w przeglądarce'
description: Dowiedz się, jak używać wystąpienia BlobService do przekazywania, wyświetlania i usuwania obiektów BLOB za pomocą zestawu SDK języka JavaScript V2 na stronie HTML.
keywords: magazyn, javascript, html
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/29/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 1bf71f899a38fa4ce36f8f0ffc78de0c64f6c3f3
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270035"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-upload-list-and-delete-blobs-using-javascripthtml-in-the-browser"></a>Szybki start: przekazywanie, usuwanie oraz wyświetlanie listy obiektów blob przy użyciu języka JavaScript/HTML w przeglądarce

Ten przewodnik Szybki start opisuje zarządzanie obiektami blob z poziomu kodu uruchamianego w całości w przeglądarce. Podejście użyte w tym miejscu pokazuje, jak użyć wymaganych środków zabezpieczeń do zapewnienia chronionego dostępu do konta magazynu obiektów blob. Do wykonania kroków tego przewodnika Szybki start jest potrzebna [subskrypcja platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="setting-up-storage-account-cors-rules"></a>Konfigurowanie reguł CORS konta magazynu 
Aby aplikacja internetowa mogła uzyskać dostęp do magazynu obiektów blob z klienta, na koncie musi być włączone [współużytkowanie zasobów między źródłami](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) (CORS, cross-origin resource sharing). 

Wróć do witryny Azure Portal i wybierz konto magazynu. Aby zdefiniować nową regułę CORS, wróć do sekcji **Ustawienia** i kliknij link **CORS**. Następnie kliknij przycisk **Dodaj**, aby otworzyć okno **Dodawanie reguły CORS**. W tym przewodniku Szybki start utworzymy otwartą regułę CORS:

![Ustawienia mechanizmu CORS konta usługi Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries/azure-blob-storage-cors-settings.png)

W poniższej tabeli przedstawiono poszczególne ustawienia mechanizmu CORS i wyjaśniono, jakie wartości zostały użyte do zdefiniowania reguły.

|Ustawienie  |Wartość  | Opis |
|---------|---------|---------|
| Dozwolone źródła | * | Dopuszczalne źródła można podać w postaci listy domen rozdzielonych przecinkami. Podanie wartości `*` umożliwia dostęp do konta magazynu ze wszystkich domen. |
| Dozwolone polecenia     | delete, get, head, merge, post, options i put | Wyświetla listę poleceń HTTP, które można wykonać na koncie magazynu. Na potrzeby tego przewodnika Szybki start wybierz wszystkie dostępne opcje. |
| Dozwolone nagłówki | * | Definiuje listę nagłówków żądań (łącznie z nagłówkami z prefiksem) dozwolonych na koncie magazynu. Ustawienie wartości `*` zezwala na dostęp za pomocą wszystkich nagłówków. |
| Uwidocznione nagłówki | * | Wyświetla listę dozwolonych nagłówków odpowiedzi według kont. Ustawienie wartości `*` zezwala kontu na wysyłanie dowolnego nagłówka.  |
| Maksymalny wiek (w sekundach) | 86400 | Maksymalny czas buforowania żądania wstępnego OPTIONS przez przeglądarkę. Podanie wartości *86400* powoduje, że buforowanie trwa całą dobę. |

> [!IMPORTANT]
> Aby zapewnić bezpieczny dostęp do konta magazynu, zadbaj o to, aby ustawienia używane w środowisku produkcyjnym zapewniały minimalny dostęp, umożliwiający korzystanie z tego konta. Opisane w tym przewodniku Szybki start ustawienia mechanizmu CORS definiują łagodne zasady zabezpieczeń. Te ustawienia nie są jednak zalecane w rzeczywistym kontekście.

Następnie utworzymy token zabezpieczający za pomocą usługi Azure Cloud Shell.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Tworzenie sygnatury dostępu współdzielonego
Sygnatura dostępu współdzielonego (SAS, Shared Access Signature) jest używana przez kod uruchomiony w przeglądarce do autoryzowania żądań w usłudze Blob Storage. Sygnatura dostępu współdzielonego umożliwia klientowi autoryzację dostępu do zasobów magazynu bez użycia klucza dostępu konta ani parametrów połączenia. Zobacz [Using shared access signatures (SAS) (Używanie sygnatur dostępu współdzielonego)](../common/storage-sas-overview.md), aby uzyskać więcej informacji.

Sygnaturę dostępu współdzielonego można utworzyć w usłudze Azure Cloud Shell przy użyciu interfejsu wiersza polecenia platformy Azure lub Eksploratora usługi Azure Storage. W poniższej tabeli opisano parametry, których wartości musisz podać, aby wygenerować sygnaturę dostępu współdzielonego za pomocą interfejsu wiersza polecenia.

| Parametr      |Opis  | Symbol zastępczy |
|----------------|-------------|-------------|
| *expiry*       | Data wygaśnięcia tokenu dostępu w formacie RRRR-MM-DD. Na potrzeby tego przewodnika Szybki start wprowadź jutrzejszą datę. | *FUTURE_DATE* |
| *account-name* | Nazwa konta magazynu. Użyj nazwy zanotowanej w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klucz konta magazynu. Użyj klucza zanotowanego w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

W poniższym skrypcie za pomocą interfejsu wiersza polecenia platformy Azure utworzono sygnaturę dostępu współdzielonego, którą można przekazać do usługi obiektów blob języka JavaScript.

> [!NOTE]
> Aby uzyskać najlepszy efekt, przed wklejeniem polecenia do usługi Azure Cloud Shell usuń dodatkowe spacje widoczne między parametrami.

```bash
az storage account generate-sas
                    --permissions racwdl
                    --resource-types sco
                    --services b
                    --expiry FUTURE_DATE
                    --account-name YOUR_STORAGE_ACCOUNT_NAME
                    --account-key YOUR_STORAGE_ACCOUNT_KEY
```
Serie wartości występujące po każdym parametrze mogą wydawać się niezrozumiałe. Te wartości parametrów odpowiadają pierwszym literom odpowiednich uprawnień. W poniższej tabeli wyjaśniono pochodzenie tych wartości: 

| Parametr        | Wartość   | Opis  |
|------------------|---------|---------|
| *permissions*    | racwdl  | Ta sygnatura dostępu współdzielonego umożliwia używanie poleceń *read*, *append*, *create*, *write*, *delete* i *list*. |
| *resource-types* | sco     | Ta sygnatura dostępu współdzielonego dotyczy następujących zasobów: *usługa*, *kontener* i *obiekt*. |
| *services*       | b       | Ta sygnatura dostępu współdzielonego dotyczy usługi *obiektów blob*. |

Po wygenerowaniu sygnatury dostępu współdzielonego skopiuj wartość zwróconą w konsoli do edytora tekstu. Możesz jej użyć w następnym kroku.

> [!IMPORTANT]
> W środowisku produkcyjnym zawsze przekazuj tokeny sygnatur dostępu współdzielonego przy użyciu protokołu SSL. Ponadto tokeny sygnatur dostępu współdzielonego powinny być generowane na serwerze i wysyłane do strony HTML w celu przekazania ich z powrotem do usługi Azure Blob Storage. Jedna z metod polega na wygenerowaniu tokenów sygnatur dostępu współdzielonego przy użyciu funkcji bezserwerowej. Witryna Azure Portal zawiera szablony funkcji umożliwiające generowanie sygnatury dostępu współdzielonego za pomocą funkcji języka JavaScript.

## <a name="implement-the-html-page"></a>Implementowanie strony HTML

### <a name="set-up-the-web-application"></a>Konfigurowanie aplikacji internetowej
Biblioteki klienta JavaScript usługi Azure Storage nie działają bezpośrednio z systemu plików i muszą być obsługiwane przez serwer internetowy. Poniższe kroki zawierają szczegółowe instrukcje dotyczące korzystania z prostego, lokalnego serwera internetowego za pomocą oprogramowania Node.js.

> [!NOTE]
> W tej sekcji przedstawiono tworzenie lokalnego serwera internetowego, który wymaga zainstalowania języka Node.js na komputerze. Jeśli nie chcesz instalować języka Node.js, możesz uruchomić lokalny serwer internetowy w inny sposób.

Najpierw utwórz nowy folder dla projektu i nazwij go *azure-blobs-javascript*. Następnie otwórz wiersz polecenia w folderze *azure-blobs-javascript* i przygotuj aplikację do zainstalowania modułu serwera internetowego, wprowadzając następujące polecenie:

```bash
npm init -y
```
Polecenie *init* dodaje pliki potrzebne do zainstalowania modułu serwera internetowego. Aby zainstalować moduł, wprowadź następujące polecenie:

```bash
npm i http-server
```
Następnie zmodyfikuj plik *package.json*, zastępując istniejącą definicję *scripts* poniższym fragmentem kodu:

```javascript
"scripts": {
    "start": "http-server"
}
```
Na koniec w wierszu polecenia wpisz `npm start`, aby uruchomić serwer internetowy:

```bash
npm start
```

### <a name="get-the-blob-storage-client-library"></a>Pobieranie biblioteki klienta magazynu obiektów blob
[Pobierz biblioteki klienta JavaScript](https://aka.ms/downloadazurestoragejs), a następnie wyodrębnij zawartość pliku zip i umieść pliki skryptów z folderu *bundle* w folderze *scripts*.

### <a name="add-the-client-script-reference-to-the-page"></a>Dodawanie odwołania do skryptu klienta do strony
Utwórz stronę HTML w katalogu głównym folderu *azure-blobs-javascript* i nazwij ją *index.html*. Po utworzeniu strony dodaj do niej następujące znaczniki.

```html
<!DOCTYPE html>
<html>
    <body>
        <button id="create-button">Create Container</button>

        <input type="file" id="fileinput" />
        <button id="upload-button">Upload</button>

        <button id="list-button">List</button>
        
        <button id="delete-button">Delete</button>
    </body>
    <script src="scripts/azure-storage.blob.min.js" charset="utf-8"></script>
    <script>
        // Blob-related code goes here
    </script>
</html>
```
Ten kod znaczników umożliwia dodanie do strony następujących elementów:

- odwołanie do lokalizacji *scripts/azure-storage.blob.js*;
- przyciski umożliwiające tworzenie kontenera oraz przekazywanie, usuwanie i wyświetlanie listy obiektów blob;
- element *INPUT* używany do przekazywania pliku;
- symbol zastępczy dla kodu specyficznego dla magazynu.

### <a name="create-an-instance-of-blobservice"></a>Tworzenie wystąpienia usługi BlobService 
Obiekt [BlobService](https://azure.github.io/azure-storage-node/BlobService.html) udostępnia interfejs do komunikacji z usługą Azure Blob Storage. Aby utworzyć wystąpienie tej usługi, musisz podać nazwę konta magazynu i sygnaturę dostępu współdzielonego wygenerowaną w poprzednim kroku.

```javascript
const account = {
    name: YOUR_STORAGE_ACCOUNT_NAME,
    sas:  YOUR_SAS
};

const blobUri = 'https://' + account.name + '.blob.core.windows.net';
const blobService = AzureStorage.Blob.createBlobServiceWithSas(blobUri, account.sas);
```

### <a name="create-a-blob-container"></a>Tworzenie kontenera obiektów blob
Po utworzeniu usługi obiektów blob możesz utworzyć nowy kontener do przechowywania przekazanego obiektu blob. Metoda [createContainerIfNotExists](https://azure.github.io/azure-storage-node/BlobService.html#createContainerIfNotExists__anchor) tworzy nowy kontener i nie zwraca błędu, jeśli kontener już istnieje.

```javascript
document.getElementById('create-button').addEventListener('click', () => {

    blobService.createContainerIfNotExists('mycontainer',  (error, container) => {
        if (error) {
            // Handle create container error
        } else {
            console.log(container.name);
        }
    });

});
```

### <a name="upload-a-blob"></a>Przesyłanie obiektów blob
Aby przekazać obiekt blob z formularza HTML, uzyskaj odwołanie do wybranego pliku z elementu *INPUT*. Wybrany plik jest dostępny za pośrednictwem tablicy `files` z elementem *type* ustawionym na wartość *file*.

W skrypcie możesz odwołać się do elementu HTML i przekazać wybrany plik do usługi obiektów blob.

```javascript
document.getElementById('upload-button').addEventListener('click', () => {

    const file = document.getElementById('fileinput').files[0];

    blobService.createBlockBlobFromBrowserFile('mycontainer', 
                                                file.name, 
                                                file, 
                                                (error, result) => {
                                                    if(error) {
                                                        // Handle blob error
                                                    } else {
                                                        console.log('Upload is successful');
                                                    }
                                                });

});
```

Metoda [createBlockBlobFromBrowserFile](https://azure.github.io/azure-storage-node/BlobService.html#createBlockBlobFromBrowserFile__anchor) przekazuje plik przeglądarki bezpośrednio do kontenera obiektów blob.

### <a name="list-blobs"></a>Wyświetlanie listy obiektów blob
Po przekazaniu pliku do kontenera obiektów blob możesz wyświetlić listę obiektów blob w kontenerze za pomocą metody [listBlobsSegmented](https://azure.github.io/azure-storage-node/BlobService.html#listBlobsSegmented__anchor).

```javascript
document.getElementById('list-button').addEventListener('click', () => {

    blobService.listBlobsSegmented('mycontainer', null, (error, results) => {
        if (error) {
            // Handle list blobs error
        } else {
            results.entries.forEach(blob => {
                console.log(blob.name);
            });
        }
    });
    
});
```

Metoda *listBlobsSegmented* zwraca kolekcję obiektów blob. Domyślna wielkość kolekcji to 5000 obiektów blob, ale można dostosować tę wartość do własnych potrzeb. [Kontynuacja przykładu](https://github.com/Azure/azure-storage-node/blob/master/examples/samples/continuationsample.js#L132) przedstawia sposób pracy z dużą liczbą obiektów blob oraz obsługę stronicowania w bibliotece klienta. 


### <a name="delete-blobs"></a>Usuwanie obiektów blob
Przekazany obiekt blob możesz usunąć, wywołując metodę [deleteBlobIfExists](https://azure.github.io/azure-storage-node/BlobService.html#deleteBlobIfExists__anchor).

```javascript
document.getElementById('delete-button').addEventListener('click', () => {

    var blobName = YOUR_BLOB_NAME;
    blobService.deleteBlobIfExists('mycontainer', blobName, (error, result) => {
        if (error) {
            // Handle delete blob error
        } else {
            console.log('Blob deleted successfully');
        }
    });
    
});
```
> [!WARNING]
> Aby ten przykładowy kod zadziałał, musisz podać wartość ciągu dla parametru *blobName*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby wyczyścić zasoby utworzone podczas pracy z tym przewodnikiem Szybki start, wróć do witryny [Azure Portal](https://portal.azure.com) i wybierz konto magazynu. Po wybraniu konta magazynu możesz je usunąć, wybierając pozycję **Przegląd > Usuń konto magazynu**.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z przykładami, aby dowiedzieć się, jak pobrać obiekty blob oraz wyświetlić informację o postępie przekazywania plików.

> [!div class="nextstepaction"]
> [Blob storage client libraries (Biblioteki klienta magazynu obiektów blob)](https://github.com/Azure/azure-storage-node/tree/master/browser)
