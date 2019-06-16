---
title: Dostosuj model języka — platformy Azure przy użyciu interfejsów API indeksatora wideo
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model języka, korzystając z interfejsów API indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4ef5354a94ae707df8dd1f2767efe04dfbacd7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799591"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Dostosuj model języka, korzystając z interfejsów API indeksatora wideo

Usługa Video Indexer umożliwia tworzenie niestandardowych modeli językowych, aby dostosować rozpoznawania mowy, przekazując adaptacji tekstu, a mianowicie tekstu z domeny słownictwo, którego chcesz aparatu, aby dostosować je do. Po użytkownik nauczenia modelu, nowych słów, które pojawiają się w tekście adaptacji zostanie rozpoznana. 

Aby uzyskać szczegółowe omówienie i najlepsze rozwiązania dotyczące niestandardowych modeli językowych, zobacz [dostosować model języka, za pomocą indeksatora wideo](customize-language-model-overview.md).

Interfejsy API indeksatora wideo służy do tworzenia i edytowania niestandardowych modeli językowych ze swojego konta, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web, zgodnie z opisem w [Dostosowywanie języka modelu przy użyciu witryny internetowej Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

Następujące polecenie tworzy nowy niestandardowy model języka na określonym koncie. Możesz przekazać pliki model języka, w tym wywołaniu. Alternatywnie można utworzyć model języka w tym miejscu i przekazać pliki dla modelu później, aktualizując model języka.

> [!NOTE]
> Nadal należy nauczenia modelu, z jego plików włączone dla modelu dowiedzieć się więcej zawartości jego plików. Kierunki na szkolenie języka znajdują się w następnej sekcji.

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie POST.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|
|modelName|string|Tak|Nazwa modelu językowego|
|language|string|Yes|Język modelu językowego. <br/>**Języka** parametru musi być podany język w formacie BCP 47 "region tagu języka" (np.: "en US"). Obsługiwane języki są angielski (en US), niemiecki (de-DE), hiszpański (es-SP), arabski (ar — na przykład), francuski (fr-FR), Hindi (cześć HI), włoski (it-IT), japoński (ja-JP), portugalski (pt-BR), rosyjski (ru-RU) i chiński (zh-CN).  |

### <a name="request-body"></a>Treść żądania

Aby przekazać pliki, które mają zostać dodane do modelu języka, możesz przekazać pliki w treści przy użyciu danych formularza, oprócz wartości dla wymaganych parametrów powyżej. Istnieją dwa sposoby, w tym celu: 

1. Klucz będzie nazwa pliku, a wartość będzie pliku txt
2. Klucz będzie nazwa pliku, a wartość będzie adres URL do pliku txt

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane dotyczące nowo utworzony model języka, wraz z metadanymi na każdy plik modelu ma następujące format przykładowe dane wyjściowe JSON.

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

## <a name="train-a-language-model"></a>Uczenie modelu języka

Następujące polecenie przygotowuje niestandardowy model języka na określonym koncie z zawartością w plikach, które zostały przekazane do i włączone w modelu językowego. 

> [!NOTE]
> Najpierw należy utworzyć model języka i przekaż jej pliki. Możesz przekazać pliki, podczas tworzenia modelu językowego lub aktualizując model języka. 

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka (generowany podczas tworzenia modelu językowego)|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane na nowo uczonego modelu językowego wraz z metadanymi na każdy plik modelu ma następujące format przykładowe dane wyjściowe JSON.

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

Następnie należy użyć **identyfikator** wartość model językowy dla **linguisticModelId** parametru podczas [przekazywania filmu wideo do indeksu](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) i  **languageModelId** parametru podczas [indeksowanie wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Usuń model języka

Następujące polecenie usuwa niestandardowy model języka, z określonego konta. Wszelkie film wideo, który używał usunięto model języka spowoduje zachowanie tego samego indeksu, do momentu ponownego indeksowania wideo. Jeśli ponownie poindeksuj wideo, można przypisać nowego modelu języka filmu wideo. W przeciwnym razie usługa Video Indexer użyje jej domyślny model ponownego indeksowania filmu wideo.

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie usunięcia.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka (generowany podczas tworzenia modelu językowego)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Brak zawartości zwracane, gdy model języka został pomyślnie usunięty.

## <a name="update-a-language-model"></a>Aktualizowanie modelu języka

Następujące polecenie aktualizuje modelu niestandardowego osoby języka na określonym koncie.

> [!NOTE]
> Można już utworzyć modelu językowego. Aby włączyć lub wyłączyć wszystkie pliki w ramach modelu, zaktualizuj nazwę modelu językowego i przekazać pliki, które mają zostać dodane do modelu językowego, można użyć tego wywołania.

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka (generowany podczas tworzenia modelu językowego)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|
|modelName|string|Nie|Nowa nazwa, które można przekazać do modelu|
|Włącz|wartość logiczna|Nie|Wybierz, czy wszystkie pliki w tym modelu są włączone (PRAWDA) lub wyłączony (false)|

### <a name="request-body"></a>Treść żądania

Aby przekazać pliki, które mają zostać dodane do modelu języka, możesz przekazać pliki w treści przy użyciu danych formularza, oprócz wartości dla wymaganych parametrów powyżej. Istnieją dwa sposoby, w tym celu: 

1. Klucz będzie nazwa pliku, a wartość będzie pliku txt
2. Klucz będzie nazwa pliku, a wartość będzie adres URL do pliku txt

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane na nowo uczonego modelu językowego wraz z metadanymi na każdy plik modelu ma następujące format przykładowe dane wyjściowe JSON.

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
Możesz użyć **identyfikator** pliki zwrócony w tym miejscu można pobrać zawartość pliku.

## <a name="update-a-file-from-a-language-model"></a>Zaktualizuj plik z modelu języka

Następujące polecenie pozwala zaktualizować nazwę i **Włącz** stanu pliku w niestandardowy model języka na określonym koncie.

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie PUT.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka, który znajduje się plik (generowany podczas tworzenia modelu językowego)|
|fileId|string|Yes|Identyfikator pliku, która jest aktualizowana (generowane, gdy plik jest przekazany podczas tworzenia lub aktualizowania modelu języka)|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|
|fileName|string|Nie|Nazwę można zaktualizować nazwy pliku|
|Włącz|wartość logiczna|Nie|Zaktualizować, czy ten plik jest włączony (PRAWDA) lub wyłączony (FAŁSZ) w modelu językowego|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane pliku który zaktualizowano następujące format przykładowe dane wyjściowe JSON poniżej.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Możesz użyć **identyfikator** pliku zwracane w tym miejscu można pobrać zawartość pliku.

## <a name="get-a-specific-language-model"></a>Pobieranie określonego modelu języka

Poniższe polecenie zwraca informacje na określony model języka na określonym koncie, takie jak język i pliki, które znajdują się w modelu językowego. 

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie GET.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>Parametry żądania i treść żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Yes|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka (generowany podczas tworzenia modelu językowego)|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera metadane dotyczące określony model języka, wraz z metadanymi na każdy plik modelu ma następujące format przykładowe dane wyjściowe JSON poniżej.

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

Możesz użyć **identyfikator** pliku zwracane w tym miejscu można pobrać zawartość pliku.

## <a name="get-all-the-language-models"></a>Pobieranie wszystkich modeli językowych

Poniższe polecenie zwraca wszystkie niestandardowych modeli językowych w ramach określonego konta, na liście.

### <a name="request-url"></a>Adres URL żądania

Jest to, Uzyskaj żądania.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich modeli języków na Twoim koncie, a każdy z ich metadane i pliki po format przykładowe dane wyjściowe JSON poniżej.

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

Następujące polecenie usuwa określony plik z określonego modelu języka na określonym koncie. 

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie usunięcia.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Tak|Identyfikator modelu języka, który znajduje się plik (generowany podczas tworzenia modelu językowego)|
|fileId|string|Yes|Identyfikator pliku, która jest aktualizowana (generowane, gdy plik jest przekazany podczas tworzenia lub aktualizowania modelu języka)|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Brak zawartości zwracany, jeśli plik został pomyślnie usunięty z modelu językowego.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Pobierz metadane pliku z modelu języka

Zawartość i metadane nastąpi powrót na określony plik z wybranego języka modelu w koncie.

### <a name="request-url"></a>Adres URL żądania

Jest to żądanie GET.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Yes|Identyfikator modelu języka, który znajduje się plik (generowany podczas tworzenia modelu językowego)|
|fileId|string|Tak|Identyfikator pliku, która jest aktualizowana (generowane, gdy plik jest przekazany podczas tworzenia lub aktualizowania modelu języka)|
|accessToken|string|Tak|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera zawartość i metadane pliku w formacie JSON, podobny do następującego:

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
> Zawartość tego pliku przykładowy są słowa "hello" i world"w dwóch osobnych wierszach.

## <a name="download-a-file-from-a-language-model"></a>Pobierz plik z modelu języka

Następujące polecenie spowoduje pobranie pliku tekstowego zawierającego zawartość określonego pliku od określonego modelu języka na określonym koncie. Ten plik powinien być zgodny zawartość pliku tekstowego, który pierwotnie został przekazany.

### <a name="request-url"></a>Adres URL żądania
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Poniżej znajduje się na żądanie w Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>Parametry żądania 

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountID|string|Tak|Unikatowy identyfikator globalny dla konta|
|modelId|string|Yes|Identyfikator modelu języka, który znajduje się plik (generowany podczas tworzenia modelu językowego)|
|fileId|string|Tak|Identyfikator pliku, która jest aktualizowana (generowane, gdy plik jest przekazany podczas tworzenia lub aktualizowania modelu języka)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania 

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź będzie miała pobierania pliku tekstowego przy użyciu zawartości pliku w formacie JSON. 

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model języka, witryny sieci Web](customize-language-model-with-website.md)
