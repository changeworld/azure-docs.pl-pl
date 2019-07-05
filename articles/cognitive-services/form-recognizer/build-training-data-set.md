---
title: Jak utworzyć zestaw danych szkoleniowych modelu niestandardowego — aparat rozpoznawania formularza
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak upewnić się, zestaw danych szkoleniowych zoptymalizowana pod kątem uczenia modelu rozpoznawania formularza.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592642"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Tworzenie szkoleniowy zestaw danych dla modelu niestandardowego

Korzystając z modelu niestandardowego formularza rozpoznawania możesz podać danych szkoleniowych, aby uczyć modelu formularzy specyficznych dla branży. Możesz uczyć model z pięciu wypełnione formularze lub pusty formularz (nazwa pliku musi zawierać słowo "puste"), oraz dwie formy wypełnione. Nawet jeśli masz za mało wypełnione formularze do jego trenowanie za pomocą, dodając pusty formularz do zestawu danych szkoleniowych zwiększa dokładność modelu.

## <a name="training-data-tips"></a>Porady dotyczące danych szkoleniowych

Należy użyć zestawu danych, który jest zoptymalizowany do trenowania. Użyj następujących wskazówek, aby zagwarantować, że uzyskać najlepsze wyniki z [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operacji:

* Jeśli to możliwe zamiast oparte na tekście dokumentów PDF dokumentów na podstawie obrazu. Skanowanych dokumentów PDF są obsługiwane jako obrazy.
* Użyj jeden pusty formularz i dwie formy wypełniane, jeśli je, które są dostępne.
* W przypadku wypełnione formularze Użyj przykładów, które mają wszystkie pola wypełnione.
* Używanie formularzy z różnymi wartościami w każdym polu.
* W przypadku obrazów formularza niższej jakości, należy użyć większy zestaw danych (na przykład obrazy 10 – 15).

## <a name="general-input-requirements"></a>Ogólne wymagania wprowadzania

Upewnij się, że zestaw danych szkoleniowych następuje po danych wejściowych wymagania dla całej zawartości rozpoznawania formularza. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Przekazywanie danych szkoleniowych

Po użytkownik została utworzona, zestaw dokumentów formularz służący do celów szkoleniowych, musisz przekazać go do kontenera magazynu obiektów blob platformy Azure. Jeśli nie wiesz, jak utworzyć konto magazynu platformy Azure przy użyciu kontenera, następujące [usługi Azure Storage Przewodnik Szybki start dotyczący witryny Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizowanie danych w podfolderach (opcjonalnie)

Domyślnie [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) interfejsu API będzie używać tylko dokumenty formularza, które znajdują się w folderze głównym w kontenerze magazynu. Jednak możesz uczyć z danymi w podfolderach w przypadku określenia w wywołaniu interfejsu API. Zwykle treści [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) wywołanie ma następującą postać gdzie `<SAS URL>` jest adres URL sygnatury dostępu współdzielonego kontenera:

```json
{
  "source":"<SAS URL>"
}
```

Jeśli dodasz następującą zawartość do treści żądania interfejsu API będzie jego trenowanie za pomocą dokumenty znajdujące się w jego podfolderach. `"prefix"` Pole jest opcjonalne i ograniczy szkoleniowy zestaw danych do plików, których ścieżki zaczynają się od podanego ciągu. Dlatego wartość `"Test"`, na przykład, spowoduje, że interfejs API, aby przyjrzeć się tylko pliki lub foldery, które zaczynają się od słowa "Test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak można utworzyć zestaw danych szkoleniowych, postępuj zgodnie z przewodnika Szybki Start do trenowania modelu niestandardowego formularza rozpoznawania i rozpocząć korzystanie z niej na formularzach.

* [Szybki start: Uczenie modelu i wyodrębnić dane formularza za pomocą programu cURL](./quickstarts/curl-train-extract.md)
* [Szybki start: Uczenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST przy użyciu języka Python](./quickstarts/python-train-extract.md)

