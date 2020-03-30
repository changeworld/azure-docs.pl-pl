---
title: Dostosowywanie modelu marki za pomocą interfejsu API indeksatora wideo
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model marki za pomocą interfejsu API indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127990"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Dostosowywanie modelu marki za pomocą interfejsu API indeksatora wideo

Indeksator wideo obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości wideo i audio. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowane przez bazę danych marek Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli jest wyświetlana w tekście wizualnym w filmie wideo, indeksator wideo wykrywa go jako markę w zawartości. Niestandardowy model marki pozwala wykluczyć niektóre marki z wykrywania i uwzględnić marki, które powinny być częścią modelu, które mogą nie znajdować się w bazie danych marek Bing.

Aby uzyskać szczegółowe informacje, zobacz [Omówienie](customize-brands-model-overview.md).

Za pomocą interfejsów API indeksatora wideo można tworzyć, używać i edytować niestandardowe modele marek wykryte w klipie wideo, zgodnie z opisem w tym temacie. Można również korzystać z witryny video indexer, zgodnie z opisem w [modelu Dostosuj marki za pomocą witryny wideo indeksatora](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Tworzenie marki

Tworzenie interfejsu API [marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) tworzy nową markę niestandardową i dodaje ją do niestandardowego modelu marek dla określonego konta.

> [!NOTE]
> Ustawienie `enabled` (w treści) do true umieszcza markę na liście *Dołącz* dla indeksatora wideo do wykrycia. Ustawienie `enabled` false umieszcza markę na liście *Wyklucz,* więc indeksator wideo nie wykryje jej.

Niektóre inne parametry, które można ustawić w treści:

* Wartością `referenceUrl` mogą być dowolne strony referencyjne dla marki, takie jak link do jej strony w Wikipedii.
* Wartość `tags` jest listą tagów dla marki. Ten tag pojawia się w polu *Kategoria* marki w witrynie wideo indeksatora. Na przykład marki "Azure" można oznaczyć lub sklasyfikować jako "Chmura".

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera informacje na temat marki, która została właśnie utworzona zgodnie z formatem poniższego przykładu.

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

## <a name="delete-a-brand"></a>Usuwanie marki

Usuń interfejs API [marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) usuwa markę z niestandardowego modelu marek dla określonego konta. Konto jest określone `accountId` w parametrze. Po pomyślnym wywołaniu marka nie będzie już znajdować się na listach *Dołącz* lub *Wyklucz* marki.

### <a name="response"></a>Odpowiedź

Po pomyślnym usunięciu marki nie ma zwróconej zawartości.

## <a name="get-a-specific-brand"></a>Uzyskaj konkretną markę

Interfejs API [pobierz markę](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) umożliwia wyszukiwanie szczegółów marki w niestandardowym modelu marek dla określonego konta przy użyciu identyfikatora marki.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera informacje o wyszukiwanej marce (przy użyciu identyfikatora marki) zgodnie z poniższym przykładem.

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
> `enabled`jest ustawiona w taki sposób, aby oznaczała, że marka `enabled` znajduje się na liście *Dołącz* dla indeksatora wideo do wykrycia, a fałsz oznacza, że marka znajduje się `true` na liście *Wyklucz,* więc indeksator wideo nie wykryje jej.

## <a name="update-a-specific-brand"></a>Aktualizowanie określonej marki

Aktualizacja interfejsu API [marki](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) umożliwia wyszukiwanie szczegółów marki w modelu niestandardowych marek dla określonego konta przy użyciu identyfikatora marki.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera zaktualizowane informacje o marce, które zostały zaktualizowane zgodnie z formatem poniższego przykładu.

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

## <a name="get-all-of-the-brands"></a>Zdobądź wszystkie marki

Interfejs API [get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) zwraca wszystkie marki w modelu niestandardowych marek dla określonego konta, niezależnie od tego, czy marka ma znajdować się na liście *Dołącz* lub *Wyklucz* marki.

### <a name="response"></a>Odpowiedź

Odpowiedź zawiera listę wszystkich marek na koncie i każdy z ich danych zgodnie z formatem poniższego przykładu.

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
> Marka o nazwie *Przykład* znajduje *się* na liście Dołącz dla indeksatora wideo do wykrycia, a marka o nazwie *Example2* znajduje się na liście *Wyklucz,* więc indeksator wideo nie wykryje go.

## <a name="get-brands-model-settings"></a>Pobierz ustawienia modelu marki

Interfejs API [ustawień marek pobierz](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) zwraca ustawienia modelu marki na określonym koncie. Ustawienia modelu Marki reprezentują, czy wykrywanie z bazy danych marek Bing jest włączone, czy nie. Jeśli marki Bing nie są włączone, indeksator wideo wykryje tylko marki z niestandardowego modelu marek określonego konta.

### <a name="response"></a>Odpowiedź

Odpowiedź pokazuje, czy marki Bing są włączone zgodnie z formatem poniższego przykładu.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`true oznacza, że marki Bing są włączone. Jeśli `useBuiltin` jest false, marki Bing są wyłączone. Wartość `state` może być ignorowana, ponieważ została przestarzała.

## <a name="update-brands-model-settings"></a>Aktualizowanie ustawień modelu marek

Interfejs API [marek aktualizacji](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) aktualizuje ustawienia modelu marki na określonym koncie. Ustawienia modelu Marki reprezentują, czy wykrywanie z bazy danych marek Bing jest włączone, czy nie. Jeśli marki Bing nie są włączone, indeksator wideo wykryje tylko marki z niestandardowego modelu marek określonego konta.

Flaga ustawiona `useBuiltIn` na true oznacza, że marki Bing są włączone. Jeśli `useBuiltin` jest false, marki Bing są wyłączone.

### <a name="response"></a>Odpowiedź

Nie ma zwracanych treści, gdy ustawienie modelu marki jest aktualizowane pomyślnie.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek za pomocą witryny sieci Web](customize-brands-model-with-website.md)
