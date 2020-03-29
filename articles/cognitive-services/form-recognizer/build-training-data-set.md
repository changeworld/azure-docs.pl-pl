---
title: Jak utworzyć zestaw danych szkoleniowych dla modelu niestandardowego — aparat rozpoznawania formularzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak upewnić się, że zestaw danych szkoleniowych jest zoptymalizowany pod kątem szkolenia modelu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380630"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego

Korzystając z modelu niestandardowego aparatu rozpoznawania formularzy, należy podać własne dane szkoleniowe, dzięki czemu model może trenować do formularzy specyficznych dla branży. Można trenować model z pięcioma wypełnionymi formularzami lub pustym formularzem (w nazwie pliku należy uwzględnić słowo "pusty") plus dwa wypełnione formularze. Nawet jeśli masz wystarczająco dużo wypełnionych formularzy do trenowania, dodanie pustego formularza do zestawu danych szkoleniowych może poprawić dokładność modelu.

Jeśli chcesz użyć ręcznie oznaczonych danych szkoleniowych, należy zacząć od co najmniej pięciu form tego samego typu. Nadal można używać formularzy bez etykiety i pustego formularza w tym samym zestawie danych.

## <a name="training-data-tips"></a>Wskazówki dotyczące danych szkoleniowych

Ważne jest, aby używać zestawu danych, który jest zoptymalizowany pod kątem szkolenia. Skorzystaj z następujących wskazówek, aby upewnić się, że uzyskasz najlepsze wyniki operacji [modelu niestandardowego pociągu:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Jeśli to możliwe, użyj tekstowych dokumentów PDF zamiast dokumentów opartych na obrazie. Zeskanowane pliki PDF są traktowane jako obrazy.
* W przypadku formularzy wypełnionych należy użyć przykładów, w których zostały wypełnione wszystkie pola.
* Używaj formularzy z różnymi wartościami w każdym polu.
* Jeśli obrazy formularzy są niższej jakości, użyj większego zestawu danych (na przykład 10–15 obrazów).
* Całkowity rozmiar zestawu danych szkoleniowych może wynosić do 500 stron.

## <a name="general-input-requirements"></a>Ogólne wymagania wejściowe

Upewnij się, że zestaw danych szkoleniowych jest również zgodny z wymaganiami wejściowymi dla całej zawartości aparatu rozpoznawania formularzy. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Przesyłanie danych szkoleniowych

Po zebraniu zestawu dokumentów formularza, które będą używane do szkolenia, należy przekazać go do kontenera magazynu obiektów blob platformy Azure. Jeśli nie wiesz, jak utworzyć konto magazynu platformy Azure za pomocą kontenera, wykonując [przewodnik Szybki start usługi Azure Storage dla witryny Azure portal.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

### <a name="organize-your-data-in-subfolders-optional"></a>Organizowanie danych w podfolderach (opcjonalnie)

Domyślnie interfejs API [modelu niestandardowego pociągu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) będzie używać tylko dokumentów formularza, które znajdują się w katalogu głównym kontenera magazynu. Jednak można trenować z danymi w podfolderach, jeśli określisz go w wywołaniu interfejsu API. Zwykle treść wywołania [modelu niestandardowego pociągu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) ma następujący `<SAS URL>` formularz, gdzie znajduje się adres URL podpisu dostępu współdzielonego kontenera:

```json
{
  "source":"<SAS URL>"
}
```

Jeśli dodasz następującą zawartość do treści żądania, interfejs API będzie trenował z dokumentami znajdującymi się w podfolderach. Pole `"prefix"` jest opcjonalne i ograniczy zestaw danych szkoleniowych do plików, których ścieżki zaczynają się od danego ciągu. Tak więc `"Test"`wartość , na przykład, spowoduje, że INTERFEJS API będzie patrzeć tylko na pliki lub foldery, które zaczynają się od słowa "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak utworzyć zestaw danych szkoleniowych, postępuj zgodnie z przewodnikiem Szybki start, aby wyszkolić niestandardowy model rozpoznawania formularzy i rozpocząć korzystanie z niego w formularzach.

* [Szybki start: szkolenie modelu i wyodrębnianie danych formularza przy użyciu cURL](./quickstarts/curl-train-extract.md)
* [Szybki start: szkolenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST za pomocą języka Python](./quickstarts/python-train-extract.md)
* [Trenowanie z użyciem etykiet za pomocą interfejsu API REST i języka Python](./quickstarts/python-labeled-data.md)