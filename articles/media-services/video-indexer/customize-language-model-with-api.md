---
title: Dostosowywanie modelu języka za pomocą interfejsu API indeksatora wideo
titlesuffix: Azure Media Services
description: Dowiedz się, jak dostosować model języka za pomocą interfejsu API indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127976"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Dostosowywanie modelu języka za pomocą interfejsu API indeksatora wideo

Indeksator wideo umożliwia tworzenie niestandardowych modeli języka w celu dostosowania rozpoznawania mowy przez przesłanie tekstu adaptacyjnego, a mianowicie tekstu z domeny, do którego słownictwa chcesz dostosować aparat. Po przeszkoleniu modelu zostaną rozpoznane nowe słowa pojawiające się w tekście adaptacowym.

Aby uzyskać szczegółowe omówienie i najlepsze wskazówki dotyczące niestandardowych modeli języka, zobacz [Dostosowywanie modelu języka za pomocą indeksatora wideo](customize-language-model-overview.md).

Interfejsy API indeksatora wideo umożliwiają tworzenie i edytowanie niestandardowych modeli języka na koncie, zgodnie z opisem w tym temacie. Można również korzystać z witryny sieci Web, zgodnie z opisem w [modelu Dostosuj język za pomocą witryny wideo indeksatora](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

Tworzenie interfejsu API [modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) tworzy nowy niestandardowy model języka na określonym koncie. Można przekazać pliki dla modelu języka w tym wywołaniu. Alternatywnie można utworzyć model języka tutaj i przekazać pliki dla modelu później, aktualizując model języka.

> [!NOTE]
> Nadal należy trenować model z włączonymi plikami, aby model dowiedzieć się o zawartości jego plików. Wskazówki dotyczące szkolenia języka znajdują się w następnej sekcji.

Aby przekazać pliki, które mają zostać dodane do modelu języka, należy przekazać pliki w treści przy użyciu pliku FormData, oprócz dostarczania wartości dla wymaganych parametrów powyżej. Istnieją dwa sposoby wykonania tego zadania:

* Kluczem będzie nazwa pliku, a wartością będzie plik txt.
* Kluczem będzie nazwa pliku, a wartość będzie adresem URL pliku txt.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane na nowo utworzony model języka wraz z metadanymi na każdym z plików modelu po formacie tego przykładu JSON danych wyjściowych:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Trenuj model języka

Szkolenie interfejsu API [modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) trenuje niestandardowy model języka na określonym koncie z zawartością plików, które zostały przekazane do i włączone w modelu języka.

> [!NOTE]
> Najpierw należy utworzyć model języka i przekazać jego pliki. Pliki można przekazywać podczas tworzenia modelu języka lub aktualizowania modelu języka.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane na nowo przeszkolony model języka wraz z metadanymi na każdym z plików modelu po formacie tego przykładu JSON output:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Zwrócony `id` jest unikatowy identyfikator używany do rozróżniania modeli `languageModelId` języka, podczas gdy jest używany zarówno do [przekazywania wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) i [ponowneudeksyfikowanie wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) interfejsów API (znany również jako `linguisticModelId` w Video Indexer upload/reindex API).

## <a name="delete-a-language-model"></a>Usuwanie modelu języka

Usuń interfejs API [modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) usuwa niestandardowy model języka z określonego konta. Każdy film, który był przy użyciu usuniętego modelu języka będzie zachować ten sam indeks, dopóki nie ponownie zindeksuj wideo. Jeśli ponownie zeksusz film, możesz przypisać nowy model języka do filmu. W przeciwnym razie indeksator wideo użyje swojego domyślnego modelu, aby ponownie wyeksliować wideo.

### <a name="response"></a>Odpowiedź

Nie ma zwracanej zawartości, gdy model języka zostanie pomyślnie usunięty.

## <a name="update-a-language-model"></a>Aktualizowanie modelu języka

Aktualizacja interfejsu API [modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) aktualizuje niestandardowy model osoby języka na określonym koncie.

> [!NOTE]
> Musisz już utworzyć model języka. Za pomocą tego wywołania można włączyć lub wyłączyć wszystkie pliki w modelu, zaktualizować nazwę modelu języka i przekazać pliki, które mają zostać dodane do modelu języka.

Aby przekazać pliki, które mają zostać dodane do modelu języka, należy przekazać pliki w treści przy użyciu pliku FormData, oprócz dostarczania wartości dla wymaganych parametrów powyżej. Istnieją dwa sposoby wykonania tego zadania:

* Kluczem będzie nazwa pliku, a wartością będzie plik txt.
* Kluczem będzie nazwa pliku, a wartość będzie adresem URL pliku txt.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane na nowo przeszkolony model języka wraz z metadanymi na każdym z plików modelu po formacie tego przykładu JSON output:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Użyj `id` plików zwróconych w odpowiedzi, aby pobrać zawartość pliku.

## <a name="update-a-file-from-a-language-model"></a>Aktualizowanie pliku z modelu języka

[Aktualizacja pliku](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) umożliwia zaktualizowanie nazwy `enable` i stanu pliku w niestandardowym modelu języka na określonym koncie.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane w pliku, który został zaktualizowany zgodnie z formatem przykładu JSON danych wyjściowych poniżej.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Użyj `id` pliku zwróconego w odpowiedzi, aby pobrać zawartość pliku.

## <a name="get-a-specific-language-model"></a>Uzyskaj określony model języka

Interfejs API [get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) zwraca informacje o określonym modelu języka na określonym koncie, takich jak język i pliki, które znajdują się w modelu języka.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane w określonym modelu języka wraz z metadanymi na każdym z plików modelu po formacie tego przykładu JSON output:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Użyj `id` pliku zwróconego w odpowiedzi, aby pobrać zawartość pliku.

## <a name="get-all-the-language-models"></a>Pobierz wszystkie modele języka

[Pobierz wszystkie](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) interfejsy API zwraca wszystkie niestandardowe modele języka na określonym koncie na liście.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich modeli języka na koncie i każdego z ich metadanych i plików zgodnie z formatem tego przykładu wyjścia JSON:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Usuwanie pliku z modelu języka

Interfejs API [usuwania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) usuwa określony plik z określonego modelu języka na określonym koncie.

### <a name="response"></a>Odpowiedź

Nie ma zwracanej zawartości, gdy plik zostanie pomyślnie usunięty z modelu języka.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Pobierz metadane w pliku z modelu języka

[Get metadanych interfejsu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API pliku zwraca zawartość i metadane w określonym pliku z wybranego modelu języka na koncie.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera zawartość i metadane pliku w formacie JSON, podobnie jak w tym przykładzie:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Zawartość tego przykładowego pliku to słowa "hello" i world" w dwóch oddzielnych wierszach.

## <a name="download-a-file-from-a-language-model"></a>Pobieranie pliku z modelu języka

Pobierz [plik](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) interfejsu API pobiera plik tekstowy zawierający zawartość określonego pliku z określonego modelu języka na określonym koncie. Ten plik tekstowy powinien być zgodny z zawartością pliku tekstowego, który został pierwotnie przekazany.

### <a name="response"></a>Odpowiedź

Odpowiedzią będzie pobranie pliku tekstowego z zawartością pliku w formacie JSON.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu witryny sieci Web](customize-language-model-with-website.md)
