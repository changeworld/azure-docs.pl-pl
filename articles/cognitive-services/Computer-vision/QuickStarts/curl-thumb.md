---
title: 'Szybki start: generowanie miniatur — REST, cURL — przetwarzanie obrazów'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu biblioteki cURL.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.openlocfilehash: 51c6a8e5693602cdc839de80f268891c247c63a9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344068"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Szybki start: generowanie miniatury przy użyciu interfejsu API REST i biblioteki cURL podczas przetwarzania obrazów

W tym przewodniku Szybki start wygenerujesz miniaturę na podstawie obrazu, korzystając z interfejsu API REST przetwarzania obrazów. Metoda [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu. Należy określić wysokość i szerokość, które mogą mieć inny współczynnik proporcji niż obraz wejściowy. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i wygenerować współrzędne przycinania na podstawie tego obszaru.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z funkcji przetwarzania obrazów, musisz mieć klucz subskrypcji — zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Żądanie Get Thumbnail

[Metoda Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu. Należy określić wysokość i szerokość, które mogą mieć inny współczynnik proporcji niż obraz wejściowy. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i wygenerować współrzędne przycinania na podstawie tego obszaru.

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

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć bibliotekę [cURL](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Aby uzyskać klucz subskrypcji, zobacz [Obtaining Subscription Keys (Uzyskiwanie kluczy subskrypcji)](../Vision-API-How-to-Topics/HowToSubscribe.md).

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

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będziesz już używać okna wiersza polecenia i edytora tekstów, zamknij je.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsem API przetwarzania obrazów używanym do analizy obrazu, wykrywania osobistości i charakterystycznych elementów krajobrazu, tworzenia miniatur oraz wyodrębniania tekstu drukowanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
