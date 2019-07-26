---
title: Dokumentacja zestawu SDK czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: Dokumentacja zestawu SDK czytnika immersyjny
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 22860e0798ec852b6d25da27e108befe8a3089a2
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488829"
---
# <a name="immersive-reader-sdk-reference"></a>Dokumentacja zestawu SDK czytnika immersyjny

Zestaw SDK czytnika immersyjny to biblioteka języka JavaScript, która umożliwia integrację czytnika immersyjny z aplikacją sieci Web.

## <a name="functions"></a>Funkcje

Zestaw SDK uwidacznia pojedynczą funkcję `ImmersiveReader.launchAsync(token, subdomain, content, options)`.

### <a name="launchasync"></a>launchAsync

Uruchamia czytnik `iframe` immersyjny w aplikacji sieci Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametry

| Name (Nazwa) | Typ | Opis |
| ---- | ---- |------------ |
| `token` | ciąg | Token uwierzytelniania usługi Azure AD. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | ciąg | Niestandardowa poddomena zasobu czytnika immersyjny na platformie Azure. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość, która ma zostać pokazana w czytniku immersyjny. |
| `options` | [Opcje](#options) | Opcje konfigurowania niektórych zachowań czytnika immersyjny. Opcjonalna. |

#### <a name="returns"></a>Zwraca

Zwraca, `Promise<HTMLDivElement>` który jest rozpoznawany podczas ładowania czytnika immersyjny. Jest rozpoznawany `div` jako element, `iframe` którego jedynym elementem podrzędnym jest element, który zawiera stronę czytnika immersyjny. `Promise`

#### <a name="exceptions"></a>Wyjątki

Zwracana `Promise` wartość zostanie odrzucona [`Error`](#error) z obiektem, jeśli czytnik immersyjny nie zostanie załadowany. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes).

## <a name="types"></a>Typy

### <a name="content"></a>Zawartość

Zawiera zawartość, która ma zostać pokazana w czytniku immersyjny.

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
| tekst/zwykły | Zwykły tekst. |
| Application/MathML + XML | Język matematycznych znaczników (MathML). [Dowiedz się więcej](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opcje

Zawiera właściwości, które konfigurują pewne zachowania czytnika immersyjny.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Błąd

Zawiera informacje o błędzie.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Kody błędów

| Kod | Opis |
| ---- | ----------- |
| BadArgument | Podany argument jest nieprawidłowy. Aby `message` uzyskać szczegółowe informacje, zobacz. |
| limit czasu | Nie można załadować czytnika immersyjny w określonym limicie czasu. |
| TokenExpired| Podany token wygasł. |

## <a name="launching-the-immersive-reader"></a>Uruchamianie czytnika immersyjny

Zestaw SDK zawiera domyślne style dla przycisku umożliwiającego uruchomienie czytnika immersyjny. Użyj atrybutu `immersive-reader-button` Class, aby włączyć ten styl.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atrybuty opcjonalne

Użyj następujących atrybutów, aby skonfigurować wygląd i działanie przycisku.

| Atrybut | Opis |
| --------- | ----------- |
| `data-button-style` | Ustawia styl przycisku. Może być `icon`, `text`lub. `iconAndText` Wartość domyślna to `icon`. |
| `data-locale` | Ustawia ustawienia regionalne, np. `en-US`. `fr-FR` Domyślnie jest używany język angielski. |
| `data-icon-px-size` | Ustawia rozmiar ikony w pikselach. Wartość domyślna to 20px. |

## <a name="browser-support"></a>Obsługa przeglądarki

Najnowsze wersje następujących przeglądarek są używane w celu uzyskania najlepszego środowiska z czytnikiem immersyjny.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Kolejne kroki

* Eksplorowanie [zestawu SDK czytnika immersyjny w witrynie GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Szybki start: Tworzenie aplikacji sieci Web, która uruchamia czytnik immersyjny (C#)](./quickstart.md)