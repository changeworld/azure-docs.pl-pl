---
title: 'Szybki start: generowanie miniatury — REST, cURL'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu biblioteki cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af303544be7b4e7686105b7d57a71c33c0a6a314
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009062"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Szybki start: Generowanie miniatury przy użyciu interfejsu API REST i biblioteki cURL podczas przetwarzania obrazów

W tym przewodniku Szybki Start generowanie miniatur z obrazu przy użyciu interfejsu API REST przetwarzania obrazów. Należy określić żądaną wysokość i szerokość, które mogą różnić się w racja aspekt z obrazu wejściowego. Przetwarzanie obrazów używa inteligentne przycinanie, aby inteligentnie zidentyfikować obszar zainteresowania i wygenerować przycinania współrzędne wokół tego regionu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć bibliotekę [cURL](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatnej wersji próbnej klucza z [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub, postępuj zgodnie z instrukcjami w [Tworzenie konta usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) subskrybować przetwarzania obrazów, i Uzyskaj klucz.

## <a name="get-thumbnail-request"></a>Żądanie Get Thumbnail

[Metoda Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu.

Aby uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujący kod do edytora.
1. Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji.
1. Zastąp ciąg `<File>` ścieżką i nazwę pliku, aby zapisać miniaturę.
1. Zmień adres URL żądania (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`), aby użyć lokalizacji, w której uzyskano klucze subskrypcji, jeśli jest to konieczne.
1. Opcjonalnie możesz zmienić obraz (`{\"url\":\"...`) do analizy.
1. Otwórz okno polecenia na komputerze z zainstalowanym programem cURL.
1. Wklej kod w oknie, a następnie uruchom polecenie.

>[!NOTE]
>Lokalizacja użyta w wywołaniu REST i lokalizacja użyta do otrzymania kluczy subskrypcji muszą być takie same. Jeśli na przykład uzyskano klucze subskrypcji z lokalizacji westus, zastąp ciąg „westcentralus” w poniższym adresie URL ciągiem „westus”.

## <a name="create-and-run-the-sample-command"></a>Tworzenie i uruchamianie przykładowego polecenia

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp wartość `<thumbnailFile>` ścieżką i nazwą pliku, w którym chcesz zapisać miniaturę.
    1. W razie potrzeby zastąp adres URL żądania (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) adresem URL punktu końcowego dla metody [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) z regionu platformy Azure, z którego uzyskano klucze subskrypcji.
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adres URL innego obrazu do analizy, z którego chcesz wygenerować miniaturę.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

    ```console
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak. Jeśli żądanie jest prawdopodobnie została wykonana pomyślnie, ale utworzonego miniatury nie jest prawidłowym plikiem obrazu, może być kluczem subskrypcji nie jest prawidłowy.

## <a name="next-steps"></a>Kolejne kroki

Poznaj interfejs API przetwarzania obrazów jak analizowanie obrazu, wykrywanie osobistości i charakterystycznych elementów krajobrazu, tworzenie miniatur i wyodrębniania tekstu drukowanego i pisma odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
