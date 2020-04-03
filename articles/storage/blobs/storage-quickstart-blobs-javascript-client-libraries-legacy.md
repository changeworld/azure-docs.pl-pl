---
title: 'Szybki start: magazyn obiektów Blob platformy Azure dla języka JavaScript w wersji 10 w przeglądarce'
description: Dowiedz się, jak przesyłać, listy i usuwać obiekty blob przy użyciu języka JavaScript v10 SDK na stronie HTML.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 5219af0ec343a6e7f87a07e4a7280ac5f4e85cd3
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619124"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Szybki start: zarządzanie obiektami blob za pomocą kodu JavaScript w wersji 10 SDK w przeglądarce

W tym przewodniku Szybki start nauczysz się zarządzać obiektami blob za pomocą kodu JavaScript działającego w całości w przeglądarce. Obiekty blob to obiekty, które mogą zawierać duże ilości danych tekstowych lub binarnych, w tym obrazy, dokumenty, multimedia strumieniowe i dane archiwum. Użyjesz wymaganych środków bezpieczeństwa, aby zapewnić chroniony dostęp do konta magazynu obiektów blob.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Konto usługi Azure Storage. [Utwórz konto magazynu](../common/storage-account-create.md).
- Lokalny serwer www. W tym artykule użyto [pliku Node.js](https://nodejs.org) do otwarcia serwera podstawowego.
- [Visual Studio Code](https://code.visualstudio.com).
- Rozszerzenie kodu VS do debugowania przeglądarki, takie jak [Debuger dla Chrome](vscode:extension/msjsdiag.debugger-for-chrome) lub [Debuger dla przeglądarki Microsoft Edge](vscode:extension/msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>Konfigurowanie reguł CORS konta magazynu

Aby aplikacja sieci web mogła uzyskać dostęp do magazynu obiektów blob z klienta, należy skonfigurować konto, aby włączyć [współużytkowanie zasobów między źródłami](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)lub cors.

Wróć do witryny Azure Portal i wybierz konto magazynu. Aby zdefiniować nową regułę CORS, przejdź do sekcji **Ustawienia** i kliknij łącze **CORS.** Następnie kliknij przycisk **Dodaj**, aby otworzyć okno **Dodawanie reguły CORS**. W tym przewodniku Szybki start utworzymy otwartą regułę CORS:

![Ustawienia mechanizmu CORS konta usługi Azure Blob Storage](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

W poniższej tabeli przedstawiono poszczególne ustawienia mechanizmu CORS i wyjaśniono, jakie wartości zostały użyte do zdefiniowania reguły.

|Ustawienie  |Wartość  | Opis |
|---------|---------|---------|
| Dozwolone źródła | * | Dopuszczalne źródła można podać w postaci listy domen rozdzielonych przecinkami. Podanie wartości `*` umożliwia dostęp do konta magazynu ze wszystkich domen. |
| Dozwolone metody     | delete, get, head, merge, post, options i put | Wyświetla listę poleceń HTTP, które można wykonać na koncie magazynu. Na potrzeby tego przewodnika Szybki start wybierz wszystkie dostępne opcje. |
| Dozwolone nagłówki | * | Definiuje listę nagłówków żądań (łącznie z nagłówkami z prefiksem) dozwolonych na koncie magazynu. Ustawienie wartości `*` zezwala na dostęp za pomocą wszystkich nagłówków. |
| Uwidocznione nagłówki | * | Wyświetla listę dozwolonych nagłówków odpowiedzi według kont. Ustawienie wartości `*` zezwala kontu na wysyłanie dowolnego nagłówka.  |
| Maksymalny wiek (sekundy) | 86400 | Maksymalny czas buforowania żądania wstępnego OPTIONS przez przeglądarkę. Podanie wartości *86400* powoduje, że buforowanie trwa całą dobę. |

> [!IMPORTANT]
> Upewnij się, że wszystkie ustawienia używane w środowisku produkcyjnym ujawniają minimalną ilość dostępu niezbędną do konta magazynu w celu utrzymania bezpiecznego dostępu. Opisane w tym przewodniku Szybki start ustawienia mechanizmu CORS definiują łagodne zasady zabezpieczeń. Te ustawienia nie są jednak zalecane w rzeczywistym kontekście.

Następnie utworzymy token zabezpieczający za pomocą usługi Azure Cloud Shell.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Tworzenie podpisu dostępu współdzielonego

Sygnatura dostępu współdzielonego (SAS, Shared Access Signature) jest używana przez kod uruchomiony w przeglądarce do autoryzowania żądań w usłudze Blob Storage. Sygnatura dostępu współdzielonego umożliwia klientowi autoryzację dostępu do zasobów magazynu bez użycia klucza dostępu konta ani parametrów połączenia. Zobacz [Using shared access signatures (SAS) (Używanie sygnatur dostępu współdzielonego)](../common/storage-sas-overview.md), aby uzyskać więcej informacji.

Sygnatury dostępu Współdzielonego można utworzyć przy użyciu interfejsu wiersza polecenia platformy Azure za pośrednictwem powłoki chmury platformy Azure lub za pomocą witryny Azure portal lub Eksploratora usługi Azure Storage. W poniższej tabeli opisano parametry, które należy podać wartości do wygenerowania sygnatury dostępu Współdzielonego z interfejsu wiersza polecenia.

| Parametr      |Opis  | Symbol zastępczy |
|----------------|-------------|-------------|
| *Upływie terminu ważności*       | Data wygaśnięcia tokenu dostępu w formacie RRRR-MM-DD. Na potrzeby tego przewodnika Szybki start wprowadź jutrzejszą datę. | *FUTURE_DATE* |
| *nazwa konta* | Nazwa konta magazynu. Użyj nazwy zanotowanej w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | Klucz konta magazynu. Użyj klucza zanotowanego w poprzednim kroku. | *YOUR_STORAGE_ACCOUNT_KEY* |

Użyj następującego polecenia interfejsu wiersza polecenia z rzeczywistymi wartościami dla każdego symbolu zastępczego, aby wygenerować sygnaturę dostępu Współdzielonego, której można użyć w kodzie JavaScript.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Serie wartości występujące po każdym parametrze mogą wydawać się niezrozumiałe. Te wartości parametrów odpowiadają pierwszym literom odpowiednich uprawnień. W poniższej tabeli wyjaśniono pochodzenie tych wartości:

| Parametr        | Wartość   | Opis  |
|------------------|---------|---------|
| *Uprawnienia*    | racwdl  | Ta sygnatura dostępu współdzielonego umożliwia używanie poleceń *read*, *append*, *create*, *write*, *delete* i *list*. |
| *resource-types* | sco     | Ta sygnatura dostępu współdzielonego dotyczy następujących zasobów: *usługa*, *kontener* i *obiekt*. |
| *Usług*       | b       | Ta sygnatura dostępu współdzielonego dotyczy usługi *obiektów blob*. |

Teraz, gdy sygnatury dostępu Współdzielonego jest generowany, skopiuj wartość zwracaną i zapisz ją gdzieś do użycia w nadchodzącym kroku. Jeśli został wygenerowany sygnatury dostępu Współdzielonego przy `?` użyciu metody innej niż interfejsu wiersza polecenia platformy Azure, należy usunąć początkowe, jeśli jest obecny. Ten znak jest separatorem adresów URL, który jest już podany w szablonie adresu URL w dalszej części tego tematu, w którym jest używany sygnatury dostępu Współdzielonego.

> [!IMPORTANT]
> W produkcji zawsze przekazuj tokeny Sygnatury dostępu Współdzielonego przy użyciu protokołu TLS. Ponadto tokeny sygnatur dostępu współdzielonego powinny być generowane na serwerze i wysyłane do strony HTML w celu przekazania ich z powrotem do usługi Azure Blob Storage. Jedna z metod polega na wygenerowaniu tokenów sygnatur dostępu współdzielonego przy użyciu funkcji bezserwerowej. Witryna Azure Portal zawiera szablony funkcji umożliwiające generowanie sygnatury dostępu współdzielonego za pomocą funkcji języka JavaScript.

## <a name="implement-the-html-page"></a>Implementowanie strony HTML

W tej sekcji utworzysz podstawową stronę sieci Web i skonfigurujesz program VS Code do uruchamiania i debugowania strony. Zanim jednak będzie można uruchomić, musisz użyć node.js, aby uruchomić lokalny serwer sieci Web i obsługiwać stronę, gdy przeglądarka tego zażąda. Następnie dodasz kod JavaScript, aby wywołać różne interfejsy API magazynu obiektów blob i wyświetlić wyniki na stronie. Można również zobaczyć wyniki tych wywołań w [witrynie Azure portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)i rozszerzenie usługi Azure [Storage](vscode:extension/ms-azuretools.vscode-azurestorage) dla kodu vs.

### <a name="set-up-the-web-application"></a>Konfigurowanie aplikacji internetowej

Najpierw utwórz nowy folder o nazwie *azure-blobs-javascript* i otwórz go w programie VS Code. Następnie utwórz nowy plik w programie VS Code, dodaj następujący kod HTML i zapisz go jako *index.html* w folderze *azure-blobs-javascript.*

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Konfigurowanie debugera

Aby skonfigurować rozszerzenie debugera w programie VS Code, wybierz **opcję Debugowanie > Dodaj konfigurację...**, a następnie wybierz **chrome** lub **edge**, w zależności od tego, które rozszerzenie zainstalowano wcześniej w sekcji Wymagania wstępne. Ta akcja tworzy plik *launch.json* i otwiera go w edytorze.

Następnie zmodyfikuj plik *launch.json* tak, aby wartość była następująca: `url` `/index.html`

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Ta konfiguracja informuje VS Code, która przeglądarka ma zostać uruchomiona i który adres URL ma zostać załadowany.

### <a name="launch-the-web-server"></a>Uruchamianie serwera sieci Web

Aby uruchomić lokalny serwer sieci Web Node.js, wybierz **pozycję Wyświetl terminal >,** aby otworzyć okno konsoli wewnątrz programu VS Code, a następnie wprowadź następujące polecenie.

```console
npx http-server
```

To polecenie zainstaluje pakiet *http-server* i uruchomi serwer, udostępniając bieżący folder za pośrednictwem domyślnych adresów URL, w tym wskazanych w poprzednim kroku.

### <a name="start-debugging"></a>Rozpocznij debugowanie

Aby uruchomić *plik index.html* w przeglądarce z dołączonym debugerem kodu VS, wybierz opcję **Debugowanie > Rozpocznij debugowanie** lub naciśnij klawisz F5 w programie VS Code.

Wyświetlany interfejs użytkownika nie robi jeszcze nic, ale dodasz kod JavaScript w poniższej sekcji, aby zaimplementować każdą wyświetloną funkcję. Następnie można ustawić punkty przerwania i interakcji z debugerem, gdy jest wstrzymany w kodzie.

Po wprowadzeniu zmian w *pliku index.html*należy ponownie załadować stronę, aby zobaczyć zmiany w przeglądarce. W programie VS Code można również wybrać **opcję Debugowanie > uruchom ponownie debugowanie** lub nacisnąć klawisze CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>Dodawanie biblioteki klienta magazynu obiektów blob

Aby włączyć wywołania interfejsu API magazynu obiektów blob, najpierw pobierz zestaw [SDK usługi Azure Storage dla javascript — biblioteka klienta obiektów Blob](https://aka.ms/downloadazurestoragejsblob), wyodrębnij zawartość kodu błyskawicznego i umieść plik *azure-storage-blob.js* w folderze *azure-blobs-javascript.*

Następnie wklej następujący kod HTML do `</body>` *index.html* po tagu zamykającym, zastępując komentarz zastępczy.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ten kod dodaje odwołanie do pliku skryptu i zapewnia miejsce dla własnego kodu JavaScript. Na potrzeby tego przewodnika Szybki start używamy pliku skryptu *azure-storage-blob.js,* dzięki czemu można go otworzyć w programie VS Code, odczytać jego zawartość i ustawić punkty przerwania. W produkcji należy użyć bardziej kompaktowego pliku *azure-storage.blob.min.js,* który jest również dostępny w pliku zip.

Więcej informacji na temat każdej funkcji magazynu obiektów blob można znaleźć w [dokumentacji referencyjnej.](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index) Należy zauważyć, że niektóre funkcje w SDK są dostępne tylko w node.js lub tylko dostępne w przeglądarce.

Kod w *usłudze azure-storage-blob.js* `azblob`eksportuje zmienną globalną o nazwie , która będzie używana w kodzie JavaScript w celu uzyskania dostępu do interfejsów API magazynu obiektów blob.

### <a name="add-the-initial-javascript-code"></a>Dodawanie początkowego kodu JavaScript

Następnie wklej następujący kod `<script>` do elementu pokazanego w poprzednim bloku kodu, zastępując komentarz zastępczy.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Ten kod tworzy pola dla każdego elementu HTML, który `reportStatus` będzie używany przez następujący kod i implementuje funkcję wyświetlania danych wyjściowych.

W poniższych sekcjach dodaj każdy nowy blok kodu JavaScript po poprzednim bloku.

### <a name="add-your-storage-account-info"></a>Dodawanie informacji o koncie magazynu

Następnie dodaj kod, aby uzyskać dostęp do konta magazynu, zastępując symbole zastępcze nazwą konta i sygnaturą dostępu Współdzielonego wygenerowaną w poprzednim kroku.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Ten kod używa informacji o koncie i sygnatury dostępu Współdzielonego do utworzenia wystąpienia [ContainerURL,](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) co jest przydatne do tworzenia i manipulowania kontenerem magazynu.

### <a name="create-and-delete-a-storage-container"></a>Tworzenie i usuwanie kontenera magazynu

Następnie dodaj kod, aby utworzyć i usunąć kontener magazynu po naciśnięciu odpowiedniego przycisku.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Ten kod wywołuje ContainerURL [tworzenie](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) i [usuwanie](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) funkcji bez użycia [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) wystąpienia. Aby zachować rzeczy proste dla tego szybkiego startu, ten kod zakłada, że konto magazynu został utworzony i jest włączony. W kodzie produkcyjnym użyj wystąpienia Aborter, aby dodać funkcję limitu czasu.

### <a name="list-blobs"></a>Wyświetlanie listy obiektów blob

Następnie dodaj kod, aby wyświetlić listę zawartości kontenera magazynu po naciśnięciu przycisku **Lista plików.**

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Ten kod wywołuje [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) funkcji w pętli, aby upewnić się, że wszystkie segmenty są pobierane. Dla każdego segmentu zapętla się nad listą elementów obiektów blob, które zawiera, i aktualizuje listę **Pliki.**

### <a name="upload-blobs"></a>Przekazywanie obiektów blob

Następnie dodaj kod, aby przekazać pliki do kontenera magazynu po naciśnięciu przycisku **Wybierz i przekaż pliki.**

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Ten kod łączy przycisk Wybierz i przekaż `file-input` **pliki** z ukrytym elementem. W ten sposób `click` zdarzenie przycisku wyzwala `click` zdarzenie wejściowe pliku i wyświetla selektor plików. Po wybraniu plików i zamknięciu `input` okna dialogowego zdarzenie występuje i `uploadFiles` wywoływana jest funkcja. Ta funkcja wywołuje tylko przeglądarkę [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) funkcji dla każdego wybranego pliku. Każde wywołanie zwraca Promise, który jest dodawany do listy, dzięki czemu wszystkie mogą być oczekiwane na raz, powodując pliki do przekazywania równolegle.

### <a name="delete-blobs"></a>Usuwanie obiektów blob

Następnie dodaj kod, aby usunąć pliki z kontenera magazynu po naciśnięciu przycisku **Usuń wybrane pliki.**

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Ten kod wywołuje funkcję [BlobURL.delete,](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) aby usunąć każdy plik wybrany na liście. Następnie wywołuje `listFiles` funkcję pokazano wcześniej, aby odświeżyć zawartość **listy Pliki.**

### <a name="run-and-test-the-web-application"></a>Uruchamianie i testowanie aplikacji sieci web

W tym momencie można uruchomić stronę i eksperymentować, aby uzyskać wpływ na sposób działania magazynu obiektów blob. Jeśli wystąpią jakiekolwiek błędy (na przykład podczas próby listy plików przed utworzeniem kontenera), w okienku **stanu** zostanie wyświetlony komunikat o błędzie. Można również ustawić punkty przerwania w kodzie JavaScript, aby sprawdzić wartości zwracane przez interfejsy API magazynu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone podczas tego przewodnika Szybki start, przejdź do [witryny Azure portal](https://portal.azure.com) i usuń grupę zasobów utworzoną w sekcji Wymagania wstępne.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono prostą witrynę sieci Web, która uzyskuje dostęp do magazynu obiektów blob z javascript opartego na przeglądarce. Aby dowiedzieć się, jak hostować samą witrynę sieci Web w magazynie obiektów blob, przejdź do następującego samouczka:

> [!div class="nextstepaction"]
> [Hostowanie statycznej witryny internetowej w usłudze Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
