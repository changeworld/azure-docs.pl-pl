---
title: Odwołanie do zestawu SDK immersyjnych czytnika
titleSuffix: Azure Cognitive Services
description: Dokumentacja dotycząca Immersyjnych czytnika zestawu SDK
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 485e8626af4266492e02d4f9fbe4af486e10c082
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718393"
---
# <a name="immersive-reader-sdk-reference"></a>Odwołanie do zestawu SDK immersyjnych czytnika

Wszechstronne SDK czytnik jest bibliotekę JavaScript, która pozwala na integrowanie czytnika realistycznych aplikacji sieci web.

## <a name="functions"></a>Funkcje

Zestaw SDK udostępnia pojedynczą funkcję `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Uruchamia Immersyjnych czytnika w ramach `iframe` w aplikacji sieci web.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametry

| Name (Nazwa) | Typ | Opis |
| ---- | ---- |------------ |
| `token` | ciąg | Token dostępu, uzyskanych z wywołania `issueToken` punktu końcowego. |
| `resourceName` | ciąg | Zastrzeżone. Musi być równa `null`. |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość ma być wyświetlany w Immersyjnych czytnika. |
| `options` | [Opcje](#options) | Opcje dotyczące konfigurowania pewnymi rodzajami zachowań tych Immersyjnych czytnika. Opcjonalny. |

#### <a name="returns"></a>Zwraca

Zwraca `Promise<HTMLDivElement>` która jest rozpoznawana jako po załadowaniu Immersyjnych czytnika. `Promise` Jest rozpoznawana jako `div` elementu, którego jedynym podrzędny jest `iframe` element, który zawiera strona Immersyjnych czytnika.

#### <a name="exceptions"></a>Wyjątki

Zwrócony `Promise` zakończy się [ `Error` ](#error) obiektu, jeśli czytnik Immersyjnych nie udało się załadować. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes).

## <a name="types"></a>Typy

### <a name="content"></a>Zawartość

Zawiera zawartość ma być wyświetlany w Immersyjnych czytnika.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Obsługiwane typy MIME

| Typ MIME | Opis |
| --------- | ----------- |
| zwykły tekst | Zwykły tekst. |
| Aplikacja/mathml + xml | Język znaczników matematyczne (MathML). [Dowiedz się więcej](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opcje

Zawiera właściwości, które skonfigurować pewnymi rodzajami zachowań tych Immersyjnych czytnika.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Błąd

Zawiera informacje o tym błędzie.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Kody błędów

| Kod | Opis |
| ---- | ----------- |
| BadArgument | Podany argument jest nieprawidłowy, zobacz `message` Aby uzyskać szczegółowe informacje. |
| limit czasu | Wszechstronne czytnik nie może załadować przed upływem określonego limitu czasu. |
| TokenExpired| Podany token wygasł. |

## <a name="launching-the-immersive-reader"></a>Uruchamianie Immersyjnych czytnika

Zestaw SDK udostępnia stylem domyślnym dla przycisku uruchamiania Immersyjnych czytnika. Użyj `immersive-reader-button` atrybut klasy, aby włączyć tego stylu.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Opcjonalne atrybuty

Aby skonfigurować wygląd i działanie przycisku, należy użyć następujących atrybutów.

| Atrybut | Opis |
| --------- | ----------- |
| `data-button-style` | Ustawia styl przycisku. Może być `icon`, `text`, lub `iconAndText`. Wartość domyślna to `icon`. |
| `data-locale` | Ustawia ustawienia regionalne, np. `en-US`, `fr-FR`. Wartość domyślna to język angielski. |
| `data-icon-px-size` | Ustawia rozmiar ikony w pikselach. Wartość domyślna to {20px. |

## <a name="browser-support"></a>Obsługa przeglądarek

Aby uzyskać najlepsze wyniki przy użyciu Immersyjnych czytnika zawartości, należy użyć najnowsze wersje następujących przeglądarek.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z [Immersyjnych czytnika zestawu SDK w witrynie GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Szybki start: Utwórz aplikację sieci web, która uruchamia Immersyjnych czytnika (C#)](./quickstart.md)