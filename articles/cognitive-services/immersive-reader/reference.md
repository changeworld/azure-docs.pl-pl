---
title: Immersive Reader SDK Reference
titleSuffix: Azure Cognitive Services
description: Immersive Reader SDK zawiera bibliotekę JavaScript, która umożliwia integrację programu Immersive Reader z aplikacją.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156407"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Przewodnik referencyjny immersive Reader SDK

Immersive Reader SDK zawiera bibliotekę JavaScript, która umożliwia integrację programu Immersive Reader z aplikacją.

## <a name="functions"></a>Funkcje

SDK udostępnia funkcje:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Uruchamia immersyjny czytnik `iframe` w aplikacji internetowej.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametry

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `token` | ciąg | Token uwierzytelniania usługi Azure AD. |
| `subdomain` | ciąg | Niestandardowa poddomena zasobu programu Immersive Reader na platformie Azure. |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość, która ma być wyświetlana w czytniku Immersive Reader. |
| `options` | [Opcje](#options) | Opcje konfigurowania pewnych zachowań programu Immersive Reader. Element opcjonalny. |

### <a name="returns"></a>Zwraca

Zwraca `Promise<LaunchResponse>`program , który jest rozpoznawany po załadowaniu czytnika immersive. Rozpoznawanie `Promise` jest [`LaunchResponse`](#launchresponse) obiektu.

### <a name="exceptions"></a>Wyjątki

Zwrócony `Promise` zostanie odrzucony [`Error`](#error) z obiektu, jeśli immersive reader nie można załadować. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes).

## <a name="close"></a>close

Zamyka immersyjny czytnik.

Przykładem użycia tej funkcji jest to, że ```hideExitButton: true``` przycisk wyjścia jest ukryty przez ustawienie [opcji](#options). Następnie inny przycisk (na przykład strzałka wstecz nagłówka ```close``` telefonu komórkowego) może wywołać tę funkcję po kliknięciu.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Ta funkcja stylizuje i aktualizuje elementy przycisku Immersive Reader dokumentu. Jeśli ```options.elements``` jest podana, ta funkcja ```options.elements```będzie renderować przyciski w programie . W przeciwnym razie przyciski będą renderowane w elementach ```immersive-reader-button```dokumentu, które mają klasę .

Ta funkcja jest automatycznie wywoływana przez moduł SDK podczas ładowania okna.

Zobacz [Atrybuty opcjonalne,](#optional-attributes) aby uzyskać więcej opcji renderowania.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametry

| Nazwa | Typ | Opis |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOpcje](#renderbuttonsoptions) | Opcje konfigurowania niektórych zachowań renderButtons funkcji. Element opcjonalny. |

## <a name="types"></a>Types

### <a name="content"></a>Zawartość

Zawiera zawartość, która ma być wyświetlana w czytniku Immersive Reader.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Fragment

Pojedynczy fragment danych, który zostanie przekazany do zawartości czytnika Immersive.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Zawiera odpowiedź z połączenia `ImmersiveReader.launchAsync`do .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Wyliczenia cookiepolicy

Wyliczenia używane do ustawiania zasad użycia plików cookie czytnika Immersive Reader. Zobacz [opcje](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Obsługiwane typy MIME

| Typ MIME | Opis |
| --------- | ----------- |
| tekst/zwykły | Zwykły tekst. |
| text/html | Zawartość HTML. [Dowiedz się więcej](#html-support)|
| aplikacja/mathml+xml | Matematyczny język znaczników (MathML). [Dowiedz się więcej](./how-to/display-math.md).
| aplikacja/vnd.openxmlformats-officedocument.wordprocessingml.document | dokumentu w formacie Microsoft Word .docx.

### <a name="html-support"></a>Obsługa html

| HTML | Obsługiwana zawartość |
| --------- | ----------- |
| Style czcionek | Pogrubienie, kursywa, podkreślenie, kod, przekreślenie, indeks górny, indeks dolny |
| Listy nieuporządkowane | Płyta, Okrąg, Kwadrat |
| Uporządkowane listy | Dziesiętny, Górna Alfa, Dolna Alfa, Górno-Rzymski, Dolny-Rzymski |
| Hiperlinki | Wkrótce |

Nieobsługiwały tagi będą renderowane porównywalnie. Obrazy i tabele nie są obecnie obsługiwane.

### <a name="options"></a>Opcje

Zawiera właściwości, które konfigurują pewne zachowania programu Immersive Reader.

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
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOpcje

Opcje renderowania przycisków Immersive Reader.

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

| Code | Opis |
| ---- | ----------- |
| BadArgument (BadArgument) | Podany argument jest `message` nieprawidłowy, zobacz szczegóły. |
| Limit czasu | Program Immersive Reader nie może załadować w określonym czasie. |
| TokenExpired | Dostarczony token wygasł. |
| Ograniczona | Limit szybkości wywołania został przekroczony. |

## <a name="launching-the-immersive-reader"></a>Uruchomienie czytnika Immersive Reader

Zestaw SDK zapewnia domyślną stylistykę przycisku uruchamiania czytnika Immersive Reader. Użyj `immersive-reader-button` atrybutu klasy, aby włączyć tę stylizację. Zobacz [ten artykuł,](./how-to-customize-launch-button.md) aby uzyskać więcej informacji.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Atrybuty opcjonalne

Użyj następujących atrybutów, aby skonfigurować wygląd i działanie przycisku.

| Atrybut | Opis |
| --------- | ----------- |
| `data-button-style` | Ustawia styl przycisku. Może `icon`być `text`, `iconAndText`lub . Wartość domyślna to `icon`. |
| `data-locale` | Ustawia ustawienia regionalne. Na przykład: `en-US` lub `fr-FR`. Domyślnie wartość `en`angielska . |
| `data-icon-px-size` | Ustawia rozmiar ikony w pikselach. Wartość domyślna to 20px. |

## <a name="browser-support"></a>Obsługa przeglądarki

Skorzystaj z najnowszych wersji następujących przeglądarek, aby uzyskać najlepsze wrażenia z korzystania z programu Immersive Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Następne kroki

* Poznaj [immersyjny sdk czytnika w usłudze GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Szybki start: tworzenie aplikacji sieci Web, która uruchamia czytnik Immersive Reader (C#)](./quickstart.md)
