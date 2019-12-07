---
title: Dostosowywanie modelu marek przy użyciu usługi Azure Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak za pomocą usługi Azure Video Indexer dostosować model marek.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 804cc7b2b0b51312ed756723fff5150b02f324cc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892808"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Dostosowywanie modelu marek przy użyciu interfejsu API Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości. Niestandardowy model marek umożliwia wykluczenie niektórych marek z wykrycia i uwzględnienie marek, które powinny być częścią modelu, który może nie znajdować się w bazie danych marek usługi Bing.

Aby zapoznać się z szczegółowym omówieniem, zobacz [Omówienie](customize-brands-model-overview.md).

Za pomocą interfejsów API Video Indexer można tworzyć, używać i edytować niestandardowe modele marek wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web Video Indexer, zgodnie z opisem w temacie [Dostosowywanie modelu marek przy użyciu witryny sieci web video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Tworzenie marki

Spowoduje to utworzenie nowej marki niestandardowej i dodanie jej do niestandardowego modelu marek dla określonego konta.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera informacje o nowej marce, zgodnie z formatem poniżej.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Ustawienie " **włączone** " ma wartość "true" powoduje umieszczenie na liście *dołączanej* marki Video Indexer do wykrycia. Ustawienie **Enabled** na false powoduje umieszczenie marki na liście *wykluczeń* , więc Video Indexer nie wykryje go.

Wartość **referenceUrl** może być dowolnymi witrynami referencyjnymi dla marki, takich jak link do strony Wikipedia.

Wartość **tagów** jest listą tagów dla marki. Ta wartość jest wyświetlana w polu *Kategoria* marki w witrynie sieci Web Video Indexer. Na przykład znak "Azure" może być oznaczony jako "Chmura" lub skategoryzowany.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera informacje dotyczące marki, która została właśnie utworzona, po następującym formacie.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Usuń markę

Usuwa markę z niestandardowego modelu marek dla określonego konta. Konto jest określone w parametrze **accountId** . Po pomyślnym wywołaniu marka nie będzie już znajdować się na listach *Dołącz* lub *Wyklucz* marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Tak|Identyfikator marki (wygenerowany po utworzeniu marki)|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Po pomyślnym usunięciu marki nie jest zwracana zawartość.

## <a name="get-a-specific-brand"></a>Uzyskaj konkretną markę

Dzięki temu można wyszukiwać szczegóły marki w modelu marek niestandardowych dla określonego konta przy użyciu identyfikatora marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Tak|Identyfikator marki (wygenerowany po utworzeniu marki)|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera informacje na temat wyszukiwanego znaku towarowego (przy użyciu identyfikatora marki) zgodnie z formatem poniższego przykładu.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> ustawienie **Enabled** ma **wartość true** oznacza, że marka znajduje się na liście *dołączania* , aby Video Indexer do wykrycia, a wartość false oznacza, że marka znajduje się na liście *wykluczeń* , więc Video Indexer nie wykryje go.

## <a name="update-a-specific-brand"></a>Zaktualizuj konkretną markę

Dzięki temu można wyszukiwać szczegóły marki w modelu marek niestandardowych dla określonego konta przy użyciu identyfikatora marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Tak|Identyfikator marki (wygenerowany po utworzeniu marki)|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera zaktualizowane informacje dotyczące marki, która ma zostać zaktualizowana, w następującym formacie.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> W tym przykładzie Marka, która została utworzona w przykładowej treści żądania w sekcji **Tworzenie marki** , jest aktualizowana w tym miejscu przy użyciu nowego tagu i nowego opisu. Wartość **Enabled** została również zmieniona na false, aby umieścić ją na liście *wykluczeń* .

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera zaktualizowane informacje dotyczące marki, które zostały zaktualizowane zgodnie z poniższym formatem.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Pobierz wszystkie marki

Spowoduje to zwrócenie wszystkich marek w modelu marek niestandardowych dla określonego konta, bez względu na to, czy Marka należy do listy *Dołącz* lub *Wyklucz* marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich marek w Twoim koncie oraz wszystkie ich szczegóły zgodnie z poniższym formatem.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Marka o nazwie *przykład* znajduje się na liście *dołączania* Video Indexer do wykrycia, a marka o nazwie *example2* znajduje się na liście *wykluczeń* , więc Video Indexer nie wykryje go.

## <a name="get-brands-model-settings"></a>Pobierz ustawienia modelu marek

Spowoduje to zwrócenie ustawień modeli marek na określonym koncie. Ustawienia modelu marek przedstawiają, czy wykrywanie z bazy danych marek Bing jest włączone. Jeśli marki Bing nie są włączone, Video Indexer będzie wykrywać tylko marki z modelu niestandardowych marek określonego konta.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Dla tego wywołania nie jest wymagana żadna dodatkowa treść żądania.

### <a name="response"></a>Odpowiedź

Odpowiedź wskazuje, czy marki Bing są włączone, zgodnie z poniższym formatem przykładu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** jest ustawiona na wartość true, co oznacza, że są włączone marki Bing. Jeśli *useBuiltin* ma wartość false, marki Bing są wyłączone. Wartość **stanu** można zignorować, ponieważ została ona zaniechana.

## <a name="update-brands-model-settings"></a>Zaktualizuj ustawienia modelu marek

Spowoduje to zaktualizowanie ustawień modelu marek na określonym koncie. Ustawienia modelu marek przedstawiają, czy wykrywanie z bazy danych marek Bing jest włączone. Jeśli marki Bing nie są włączone, Video Indexer będzie wykrywać tylko marki z modelu niestandardowych marek określonego konta.

### <a name="request-url"></a>Adres URL żądania:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestuj je za pomocą portalu deweloperów Video Indexer](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Tak|Region świadczenia usługi Azure, do którego ma zostać rozesłane wywołanie. Aby uzyskać więcej informacji, zobacz [regiony i video Indexer platformy Azure](regions.md).|
|accountId|string|Tak|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Tak|Token dostępu (musi być [tokenem dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)zakresu) do uwierzytelniania w wywołaniu. Tokeny dostępu wygasną w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera informacje o nowej marce, zgodnie z formatem poniżej.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** jest ustawiona na wartość true, co oznacza, że są włączone marki Bing. Jeśli *useBuiltin* ma wartość false, marki Bing są wyłączone.

### <a name="response"></a>Odpowiedź

Nie ma żadnej zwróconej zawartości, gdy ustawienie modelu marek zostało pomyślnie zaktualizowane.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek przy użyciu witryny sieci Web](customize-brands-model-with-website.md)
