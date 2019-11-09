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
ms.openlocfilehash: ed9bd6f5932fdcb2d9124a000115a6f68cf21613
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889306"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Przewodnik referencyjny zestawu SDK czytnika immersyjny

Zestaw SDK czytnika immersyjny to biblioteka języka JavaScript, która umożliwia integrację czytnika immersyjny z aplikacją sieci Web.

## <a name="functions"></a>Funkcje

Zestaw SDK udostępnia funkcje:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Uruchamia czytnik immersyjny w ramach `iframe` w aplikacji sieci Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>Parametry

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `token` | ciąg | Token uwierzytelniania usługi Azure AD. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | ciąg | Niestandardowa poddomena zasobu czytnika immersyjny na platformie Azure. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość, która ma zostać pokazana w czytniku immersyjny. |
| `options` | [Opcje](#options) | Opcje konfigurowania niektórych zachowań czytnika immersyjny. Opcjonalny. |

### <a name="returns"></a>Typu

Zwraca `Promise<HTMLDivElement>`, który rozwiązuje, kiedy czytnik immersyjny jest ładowany. `Promise` jest rozpoznawany jako element `div`, którego tylko element podrzędny jest `iframe` elementu, który zawiera stronę czytnika immersyjny.

### <a name="exceptions"></a>Wyjątki

Zwrócony `Promise` zostanie odrzucony z obiektem [`Error`](#error) w przypadku niepowodzenia załadowania czytnika immersyjny. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes).

## <a name="close"></a>zamknij

Zamyka czytnik immersyjny.

Przykładowy przypadek użycia tej funkcji to jeśli przycisk Zakończ jest ukryty przez ustawienie ```hideExitButton: true``` w [opcjach](#options). Następnie inny przycisk (na przykład strzałka wstecz nagłówka komórkowego) może wywołać tę funkcję ```close```, gdy zostanie kliknięty.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Ta funkcja stylów i aktualizuje elementy przycisku czytnika immersyjny dokumentu. Jeśli podano ```options.elements```, ta funkcja będzie renderować przyciski w ```options.elements```. W przeciwnym razie przyciski będą renderowane w obrębie elementów dokumentu, które mają ```immersive-reader-button```klasie.

Ta funkcja jest automatycznie wywoływana przez zestaw SDK podczas ładowania okna.

Zobacz [atrybuty opcjonalne](#optional-attributes) , aby uzyskać więcej opcji renderowania.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametry

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opcje konfigurowania niektórych zachowań funkcji renderButtons. Opcjonalny. |

## <a name="types"></a>Typ

### <a name="content"></a>Zawartość

Zawiera zawartość, która ma zostać pokazana w czytniku immersyjny.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Segmentu

Pojedynczy fragment danych, który zostanie przesłany do zawartości czytnika immersyjny.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Obsługiwane typy MIME

| Typ MIME | Opis |
| --------- | ----------- |
| tekst/zwykły | Zwykły tekst. |
| tekst/HTML | Zawartość HTML. [Dowiedz się więcej](#html-support)|
| Application/MathML + XML | Język matematycznych znaczników (MathML). [Dowiedz się więcej](https://developer.mozilla.org/en-US/docs/Web/MathML).
| application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Dokument formatu programu Microsoft Word. docx.

### <a name="html-support"></a>Obsługa HTML
| HTML | Obsługiwana zawartość |
| --------- | ----------- |
| Style czcionki | Pogrubienie, kursywa, podkreślenie, kod, przekreślenie, indeks górny, dolny indeks |
| Listy nieuporządkowane | Dysk, okrąg, kwadrat |
| Uporządkowane listy | Dziesiętny, górny, Dolny, niższy niż alfa, wielkie litery, małe litery |
| Hipertekst | Wkrótce |

Nieobsługiwane Tagi będą renderowane w sposób porównywalny. Obrazy i tabele nie są obecnie obsługiwane.

### <a name="options"></a>Opcje

Zawiera właściwości, które konfigurują pewne zachowania czytnika immersyjny.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opcje renderowania przycisków czytnika szczegółowego.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
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
| BadArgument | Podany argument jest nieprawidłowy. Aby uzyskać szczegółowe informacje, zobacz `message`. |
| Limit czasu | Nie można załadować czytnika immersyjny w określonym limicie czasu. |
| TokenExpired | Podany token wygasł. |
| Ograniczone | Przekroczono limit liczby wywołań. |

## <a name="launching-the-immersive-reader"></a>Uruchamianie czytnika immersyjny

Zestaw SDK zawiera domyślne style dla przycisku umożliwiającego uruchomienie czytnika immersyjny. Użyj atrybutu klasy `immersive-reader-button`, aby włączyć ten styl.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atrybuty opcjonalne

Użyj następujących atrybutów, aby skonfigurować wygląd i działanie przycisku.

| Atrybut | Opis |
| --------- | ----------- |
| `data-button-style` | Ustawia styl przycisku. Może być `icon`, `text`lub `iconAndText`. Wartość domyślna to `icon`. |
| `data-locale` | Ustawia ustawienia regionalne. Na przykład: `en-US` lub `fr-FR`. Wartość domyślna to `en`w języku angielskim. |
| `data-icon-px-size` | Ustawia rozmiar ikony w pikselach. Wartość domyślna to 20px. |

## <a name="browser-support"></a>Obsługa przeglądarki

Najnowsze wersje następujących przeglądarek są używane w celu uzyskania najlepszego środowiska z czytnikiem immersyjny.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Następne kroki

* Eksplorowanie [zestawu SDK czytnika immersyjny w witrynie GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Szybki Start: Tworzenie aplikacji sieci Web, która uruchamia czytnik immersyjnyC#()](./quickstart.md)
