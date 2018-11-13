---
title: Używanie usługi Azure Event Grid do automatyzowania zmiany rozmiaru przekazanych obrazów| Microsoft Docs
description: Usługa Azure Event Grid może być wyzwalana przy przekazywaniu obiektów blob do usługi Azure Storage. W ten sposób można wysyłać obrazy przekazane do usługi Azure Storage do innych usług, takich jak Azure Functions, zmieniać ich rozmiar i wprowadzać inne ulepszenia.
services: event-grid, functions
author: ggailey777
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/29/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 2a60084577255b9aa88700509129b8d917c43a79
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282513"
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid

[Azure Event Grid](overview.md) to usługa do obsługi zdarzeń dla chmury. Usługa Event Grid pozwala tworzyć subskrypcje zdarzeń zgłaszanych przez usługi platformy Azure lub zasoby innych firm.  

Ten samouczek to druga część serii samouczków na temat usługi Storage. Stanowi rozszerzenie [poprzedniego samouczka na temat usługi Storage][previous-tutorial] o dodanie bezserwerowego, automatycznego generowania miniatur za pomocą usług Azure Event Grid i Azure Functions. Dzięki usłudze Event Grid usługa [Azure Functions](..\azure-functions\functions-overview.md) może reagować na zdarzenia usługi [Azure Blob Storage](..\storage\blobs\storage-blobs-introduction.md) i generować miniatury przekazanych obrazów. Subskrypcja zdarzeń jest tworzona dla zdarzenia tworzenia usługi Blob Storage. Gdy do konkretnego kontenera usługi Blob Storage dodawany jest obiekt blob, następuje wywołanie punktu końcowego funkcji. Za pomocą danych przekazanych do powiązania funkcji z usługi Event Grid uzyskiwany jest dostęp do obiektu blob i generowana jest miniatura obrazu.

Aby dodać funkcję zmiany rozmiaru do istniejącej aplikacji do przekazywania obrazów, używane są interfejs wiersza polecenia platformy Azure i witryna Azure Portal.

![Opublikowana aplikacja internetowa w przeglądarce Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie ogólnego konta usługi Azure Storage
> * Wdrażanie bezserwerowego kodu za pomocą usługi Azure Functions
> * Tworzenie subskrypcji zdarzeń usługi Blob Storage w usłudze Event Grid

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

Musisz wcześniej ukończyć poprzedni samouczek na temat usługi Blob Storage: [Przekazywanie danych obrazu w chmurze za pomocą usługi Azure Storage][previous-tutorial].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Jeśli wcześniej dostawca zasobów usługi Event Grid nie został zarejestrowany w ramach subskrypcji, upewnij się, że teraz jest on zarejestrowany.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.EventGrid
```

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.14 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

Jeśli nie korzystasz z usługi Cloud Shell, musisz się najpierw zalogować za pomocą polecenia `az login`.

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Usługa Azure Functions wymaga konta magazynu ogólnego. Utwórz oddzielne konto magazynu ogólnego w grupie zasobów przy użyciu polecenia [az storage account create](/cli/azure/storage/account#az-storage-account-create).

Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. 

W poniższym poleceniu w miejsce symbolu zastępczego `<general_storage_account>` wstaw swoją własną unikatową w skali globalnej nazwę konta magazynu ogólnego. 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji  

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do bezserwerowego wykonywania kodu funkcji. Utwórz aplikację funkcji przy użyciu polecenia [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

W poniższym poleceniu w miejsce symbolu zastępczego `<function_app>` wstaw swoją własną unikatową w skali globalnej nazwę aplikacji funkcji. Nazwa aplikacji funkcji jest używana jako domyślna domena DNS aplikacji funkcji, więc nazwa ta musi być unikatowa wśród wszystkich aplikacji na platformie Azure. Zastąp ciąg `<general_storage_account>` nazwą utworzonego konta magazynu ogólnego.

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

Teraz musisz skonfigurować aplikację funkcji do połączenia z kontem usługi Blob Storage utworzonym w [poprzednim samouczku][previous-tutorial].

## <a name="configure-the-function-app"></a>Konfigurowanie aplikacji funkcji

Do łączenia się z kontem usługi Blob Storage funkcja wymaga parametrów połączenia. Kod funkcji wdrażanej na platformie Azure w poniższym kroku wyszukuje parametry połączenia w ustawieniu aplikacji myblobstorage_STORAGE oraz wyszukuje nazwę kontenera obrazu miniatury w ustawieniu aplikacji myContainerName. Parametry połączenia można uzyskać za pomocą polecenia [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string). Skonfiguruj ustawienia aplikacji za pomocą polecenia [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

W poniższych poleceniach interfejsu wiersza polecenia nazwą konta usługi Blob Storage utworzonego w poprzednim samouczku jest `<blob_storage_account>`.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbnails FUNCTIONS_EXTENSION_VERSION=~2
```

Ustawienie `FUNCTIONS_EXTENSION_VERSION=~2` sprawia, że aplikacja funkcji jest uruchamiana w ramach wersji 2.x środowiska uruchomieniowego usługi Azure Functions.

Teraz możesz wdrożyć projekt kodu funkcji do tej aplikacji funkcji.

## <a name="deploy-the-function-code"></a>Wdrażanie kodu funkcji 

# <a name="nettabnet"></a>[\.NET](#tab/net)

Przykładowy skrypt w języku C# (csx) funkcji zmiany rozmiaru jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/function-image-upload-resize). Wdróż ten projekt kodu funkcji do aplikacji funkcji, używając polecenia [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config). 

W poniższym poleceniu `<function_app>` to nazwa wcześniej utworzonej aplikacji funkcji.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)
Przykładowa funkcja zmiany rozmiaru w formacie Node.js jest dostępna w usłudze [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node). Wdróż ten projekt kodu funkcji do aplikacji funkcji, używając polecenia [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config).

W poniższym poleceniu `<function_app>` to nazwa wcześniej utworzonej aplikacji funkcji.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

Funkcja zmiany rozmiaru obrazu jest wyzwalana przez żądania HTTP wysyłane do niej z usługi Event Grid. Utwórz subskrypcję zdarzeń, aby określić w usłudze Event Grid, że chcesz otrzymywać te powiadomienia pod adresem URL funkcji. W tym samouczku zasubskrybowano zdarzenia tworzone przez obiekty blob.

Dane przekazane do funkcji z powiadomienia usługi Event Grid zawierają adres URL obiektu blob. Ten adres URL jest następnie przekazywany do powiązania wejściowego w celu pobrania przekazanego obrazu z magazynu Blob Storage. Funkcja generuje obraz miniatury i zapisuje wynikowy strumień do oddzielnego kontenera w usłudze Blob Storage. 

Ten projekt używa wartości `EventGridTrigger` dla typu wyzwalacza. Zamiast ogólnych wyzwalaczy HTTP zaleca się korzystanie z wyzwalacza usługi Event Grid. Usługa Event Grid automatycznie weryfikuje wyzwalacze funkcji usługi Event Grid. W przypadku ogólnych wyzwalaczy HTTP trzeba zaimplementować [odpowiedź weryfikacji](security-authentication.md#webhook-event-delivery).

Aby dowiedzieć się więcej na temat tej funkcji, zobacz [pliki function.json i run.csx](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc).
 
Kod projektu funkcji jest wdrażany bezpośrednio z publicznego przykładowego repozytorium. Aby dowiedzieć się więcej na temat opcji wdrażania dla usługi Azure Functions, zobacz [Ciągłe wdrażanie dla usługi Azure Functions](../azure-functions/functions-continuous-deployment.md).

## <a name="create-an-event-subscription"></a>Tworzenie subskrypcji zdarzeń

Subskrypcja zdarzeń wskazuje, które zdarzenia generowane przez dostawcę mają być wysyłane do określonego punktu końcowego. W tym przypadku punkt końcowy jest uwidaczniany przez Twoją funkcję. Poniżej przedstawiono procedurę tworzenia subskrypcji zdarzeń wysyłającej powiadomienia do funkcji w witrynie Azure Portal: 

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij strzałkę w lewym dolnym rogu, aby rozwinąć wszystkie usługi, wpisz *funkcje* w polu **Filtr** i wybierz pozycję **Aplikacje funkcji**. 

    ![Przechodzenie do aplikacji funkcji w witrynie Azure Portal](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. Rozwiń swoją funkcję aplikacji, wybierz funkcję **imageresizerfunc**, a następnie wybierz pozycję **Dodaj subskrypcję usługi Event Grid**.

    ![Przechodzenie do aplikacji funkcji w witrynie Azure Portal](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. Użyj ustawień subskrypcji zdarzeń w sposób określony w tabeli poniżej.
    
    ![Tworzenie subskrypcji zdarzeń z funkcji w witrynie Azure Portal](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Typ tematu** |  Konta magazynu | Wybierz dostawcę zdarzeń konta usługi Storage. | 
    | **Subskrypcja** | Twoja subskrypcja platformy Azure | Domyślnie jest wybrana Twoja bieżąca subskrypcja platformy Azure.   |
    | **Grupa zasobów** | myResourceGroup | Wybierz pozycję **Użyj istniejącej** i wybierz grupę zasobów używaną w tym samouczku.  |
    | **Zasób** |  Konto usługi Blob Storage |  Wybierz utworzone konto usługi Blob Storage. |
    | **Typy zdarzeń** | Utworzony obiekt blob | Anuluj zaznaczenie wszystkich typów innych niż **Utworzony obiekt blob**. Tylko typy zdarzeń `Microsoft.Storage.BlobCreated` są przekazywane do funkcji.| 
    | **Typ subskrybenta** |  generowany automatycznie |  Wstępnie zdefiniowany jako element webhook. |
    | **Punkt końcowy subskrybenta** | generowany automatycznie | Użyj automatycznie wygenerowanego adresu URL punktu końcowego. | 
    | **Nazwa** | imageresizersub | Nazwa identyfikująca nową subskrypcję zdarzeń. | 

4. Kliknij pozycję **Utwórz**, aby dodać subskrypcję zdarzeń. Spowoduje to utworzenie subskrypcji zdarzeń, która wyzwala funkcję `imageresizerfunc` po dodaniu obiektu blob do kontenera *images*. Funkcja zmieni rozmiar obrazów i doda je do kontenera *thumbnails*.

Ponieważ usługi zaplecza zostały już skonfigurowane, można przetestować funkcję zmieniania rozmiaru obrazów w przykładowej aplikacji internetowej. 

## <a name="test-the-sample-app"></a>Testowanie przykładowej aplikacji

Aby przetestować zmienianie rozmiaru obrazów w aplikacji internetowej, przejdź pod adres URL Twojej opublikowanej aplikacji. Domyślnym adresem URL aplikacji internetowej jest `https://<web_app>.azurewebsites.net`.

Kliknij region **Upload photos** (Przekazywanie zdjęć), aby wybrać i przekazać plik. Możesz także przeciągnąć zdjęcia do tego obszaru. 

Zwróć uwagę, że gdy przekazany obraz zniknie, jego kopia jest wyświetlana na karuzeli **Generated thumbnails** (Wygenerowane miniatury). Rozmiar obrazu został zmieniony przez funkcję, obraz został dodany do kontenera *thumbnails* i pobrany przez klienta internetowego.

![Opublikowana aplikacja internetowa w przeglądarce Microsoft Edge](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie ogólnego konta usługi Azure Storage
> * Wdrażanie bezserwerowego kodu za pomocą usługi Azure Functions
> * Tworzenie subskrypcji zdarzeń usługi Blob Storage w usłudze Event Grid

Przejdź do trzeciej części serii samouczków na temat usługi Storage, aby dowiedzieć się, jak zabezpieczyć dostęp do konta magazynu.

> [!div class="nextstepaction"]
> [Zabezpieczanie dostępu do danych aplikacji w chmurze](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Aby dowiedzieć się więcej na temat usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md). 
+ Aby wypróbować inny samouczek na temat usługi Azure Functions, zobacz [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](..\azure-functions\functions-twitter-email.md). 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
