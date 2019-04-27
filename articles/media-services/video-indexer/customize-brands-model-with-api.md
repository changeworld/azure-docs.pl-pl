---
title: Umożliwia dostosowywanie modelu marek indeksatora wideo usługi Azure
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model marek za pomocą indeksatora wideo usługi Azure.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583727"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Dostosuj model marek przy użyciu interfejsu API indeksatora wideo

Usługa Video Indexer obsługuje wykrywanie marki mowy i tekstu podczas indeksowania i indeksowanie zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianek o produktach, usługach i firm zaproponowana przez Bing marek w bazie danych. Na przykład Microsoft jest wymieniony w zawartości wideo lub audio lub zostanie ona wyświetlona w tekście visual w filmach wideo, Video Indexer wykrywa ją jako marki w zawartości. Niestandardowy model marek umożliwia dołączanie i wykluczanie niektórych marek z wykrycia marki, które powinny być częścią modelu, który może nie być w usłudze Bing marek w bazie danych.

Aby uzyskać szczegółowym omówieniem, zobacz [Przegląd](customize-brands-model-overview.md).

Za pomocą interfejsów API klipów wideo indeksator do tworzenia, użycia i Edytuj niestandardowe modele marek wykryte w filmach wideo, zgodnie z opisem w tym temacie. Umożliwia także Video Indexer witryny sieci Web, zgodnie z opisem w [modelu marek dostosowywanie witryny sieci Web Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Tworzenie witryn zbiorczych

Tworzy nowy, niestandardowy marki i dodaje go do niestandardowego modelu marki dla określonego konta.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera informacje na temat nowych marki, zgodnie z formatu w poniższym przykładzie.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Ustawienie **włączone** do wartości true umieszcza marki w *Include* listy indeksatora wideo wykryć. Ustawienie **włączone** false umieszcza marki w *wykluczyć* listy, więc Video Indexer nie są wykrywane.

**ReferenceUrl** wartość może być żadnych witryn sieci Web odwołania na marki, np. łącze do strony Wikipedii.

**Tagi** wartość znajduje się lista tagów na marki. Jest to wyświetlane w marki *kategorii* w witrynie sieci Web Video Indexer. Na przykład marki "Azure" można oznakowane lub kategorii "Chmura".

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera informacje dotyczące marki, właśnie utworzony następujący format w poniższym przykładzie.

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

## <a name="delete-a-brand"></a>Usuń marki

Usuwa witryn zbiorczych z modelu marek niestandardowego dla określonego konta. To konto określono w **accountId** parametru. Po wywołaniu pomyślnie marki nie będzie już w *Include* lub *wykluczyć* brands listy.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Yes|Identyfikator marki (generowane podczas tworzenia marki)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Brak zawartości zwracane, gdy produkt został pomyślnie usunięty.

## <a name="get-a-specific-brand"></a>Pobierz konkretną markę

Dzięki temu można wyszukiwać szczegółowe informacje o marki w niestandardowy model marki dla określonego konta, przy użyciu identyfikatora marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Yes|Identyfikator marki (generowane podczas tworzenia marki)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź informacje na marki przeszukiwane (przy użyciu Identyfikatora marki) zgodnie z formatu w poniższym przykładzie.

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
> **włączone** zostanie ustawiona **true** oznacza, że marki znajduje się w *Include* listy indeksatora wideo wykryć, i **włączone** jest wartość FAŁSZ oznacza, że Marka znajduje się w *wykluczyć* listy, więc Video Indexer nie są wykrywane.

## <a name="update-a-specific-brand"></a>Aktualizacja konkretną markę

Dzięki temu można wyszukiwać szczegółowe informacje o marki w niestandardowy model marki dla określonego konta, przy użyciu identyfikatora marki.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|id|liczba całkowita|Yes|Identyfikator marki (generowane podczas tworzenia marki)|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera zaktualizowane informacje na marki, który chcesz zaktualizować następujący format w poniższym przykładzie.

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
> W tym przykładzie na marki, który został utworzony w treści żądania przykład **tworzenie witryn zbiorczych** sekcji jest aktualizowana w tym miejscu przy użyciu nowego tagu i nowy opis. **Włączone** również wartość została zmieniona na wartość false, aby umieścić ją w *wykluczyć* listy.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera zaktualizowane informacje na marki, który użytkownik zaktualizował następujące format w poniższym przykładzie.

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

## <a name="get-all-of-the-brands"></a>Wszystkie marki

Spowoduje to zwrócenie wszystkich produktów w niestandardowy model marki dla określonego konta, niezależnie od tego, czy dostępne jest przeznaczony w *Include* lub *wykluczyć* listy marek.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich marek na Twoim koncie, a każdy z ich szczegóły, zgodnie z formatu w poniższym przykładzie.

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
> Marki o nazwie *przykład* znajduje się w *Include* listy dla indeksatora wideo, aby wykryć i marki o nazwie *przykład2* znajduje się w *wykluczyć* listy , więc Video Indexer nie są wykrywane.

## <a name="get-brands-model-settings"></a>Pobieranie ustawień modelu marki

Spowoduje to zwrócenie ustawienia modelu marek na określonym koncie. Ustawienia modelu marek pokazują, czy włączono wykrywanie z bazy danych marek Bing, czy nie. Marek Bing nie są włączone, Video Indexer tylko wykryć marek z niestandardowego modelu marek określonego konta.

### <a name="request-url"></a>Adres URL żądania

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Nie ma już treść żądania wymagane dla tego wywołania.

### <a name="response"></a>Odpowiedź

Odpowiedź wskazuje, czy marek Bing są włączone następujące format w poniższym przykładzie.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** jest ustawiona na wartość true, reprezentuje tego Bing marek są włączone. Jeśli *useBuiltin* jest wartość FAŁSZ, marek Bing są wyłączone. **Stanu** wartości można zignorować, ponieważ jest przestarzała.

## <a name="update-brands-model-settings"></a>Zaktualizuj ustawienia modelu marki

Spowoduje to zaktualizowanie ustawień modelu marek na określonym koncie. Ustawienia modelu marek pokazują, czy włączono wykrywanie z bazy danych marek Bing, czy nie. Marek Bing nie są włączone, Video Indexer tylko wykryć marek z niestandardowego modelu marek określonego konta.

### <a name="request-url"></a>Adres URL żądania:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Zobacz wymagane parametry i przetestowania przy użyciu portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametry żądania

|**Nazwa**|**Typ**|**Wymagane**|**Opis**|
|---|---|---|---|
|location|string|Yes|Region platformy Azure, do którego powinny być kierowane wywołania. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure i Video Indexer](regions.md).|
|accountId|string|Yes|Unikatowy identyfikator globalny dla konta|
|accessToken|string|Yes|Token dostępu (musi być z zakresu [Token dostępu konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) do uwierzytelniania połączenia. Tokeny dostępu wygasa w ciągu 1 godziny.|

### <a name="request-body"></a>Treść żądania

Oprócz tych parametrów należy podać obiekt JSON treści żądania, który zawiera informacje na temat nowych marki, zgodnie z formatu w poniższym przykładzie.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** jest ustawiona na wartość true, reprezentuje tego Bing marek są włączone. Jeśli *useBuiltin* jest wartość FAŁSZ, marek Bing są wyłączone.

### <a name="response"></a>Odpowiedź

Brak zwrócone zawartości, gdy ustawienie modelu marek został pomyślnie zaktualizowany.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model marek witryny sieci Web](customize-brands-model-with-website.md)
