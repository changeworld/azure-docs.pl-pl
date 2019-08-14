---
title: Dokumentacja zestawu SDK systemu iOS dla czytnika immersyjny
titlesuffix: Azure Cognitive Services
description: Dokumentacja zestawu SDK programu immersyjny dla systemu iOS
services: cognitive-services
author: MeganRoach
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: t-meroa
ms.openlocfilehash: acdaaf0bf08644053e86343ae4b002002fee6a84
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966598"
---
# <a name="immersive-reader-sdk-reference"></a>Dokumentacja zestawu SDK czytnika immersyjny

Zestaw immersyjny Reader iOS SDK to SWIFT CocoaPod, który umożliwia integrację czytnika immersyjny z aplikacją systemu iOS.

## <a name="functions"></a>Funkcje

Zestaw SDK uwidacznia pojedynczą funkcję `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Uruchamia czytnik immersyjny przez uruchomienie kontrolera widoku w aplikacji systemu iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parametry

| Name (Nazwa) | Typ | Opis |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Kontroler nawigacji dla aplikacji systemu iOS, z której jest wywoływana funkcja. |
| `token` | String | Token uwierzytelniania usługi Azure AD. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | String | Niestandardowa poddomena zasobu czytnika immersyjny na platformie Azure. Zobacz [temat uwierzytelnianie w usłudze Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Zawartość](#content) | Obiekt zawierający zawartość, która ma zostać pokazana w czytniku immersyjny. |
| `options` | [Opcje](#options) | Opcje konfigurowania niektórych zachowań czytnika immersyjny. Opcjonalna. |
| `onSuccess` | () — > void | Zamknięcie, które jest wywoływane, gdy czytnik immersyjny został pomyślnie uruchomiony. |
| `onFailure` | (_ błąd: [Błąd](#error)) -> void | Zamknięcie, które jest wywoływane, gdy czytnik immersyjny nie zostanie załadowany. Ten element zamknięcia zwraca [`Error`](#error) obiekt, który reprezentuje kod błędu i komunikat o błędzie skojarzony z błędem. Aby uzyskać więcej informacji, zobacz [kody błędów](#error-codes). |

## <a name="types"></a>Typy

### <a name="content"></a>Zawartość

Zawiera zawartość, która ma zostać pokazana w czytniku immersyjny.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Obsługiwane typy MIME

| Typ MIME | Opis |
| --------- | ----------- |
| tekst/zwykły | Zwykły tekst. |
| Application/MathML + XML | Język matematycznych znaczników (MathML). [Dowiedz się więcej](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opcje

Zawiera właściwości, które konfigurują pewne zachowania czytnika immersyjny.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Błąd

Zawiera informacje o błędzie.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Kody błędów

| Kod | Opis |
| ---- | ----------- |
| BadArgument | Podany argument jest nieprawidłowy. Aby `message` uzyskać szczegółowe informacje, zobacz. |
| limit czasu | Nie można załadować czytnika immersyjny w określonym limicie czasu. |
| TokenExpired | Podany token wygasł. |
| Ograniczone | Przekroczono limit liczby wywołań. |
| InternalError | Wystąpił błąd wewnętrzny w kontrolerze widoku czytnika immersyjny. Aby `message` uzyskać szczegółowe informacje, zobacz.|

## <a name="os-version-support"></a>Obsługa wersji systemu operacyjnego

ZESTAW immersyjny Reader dla systemu iOS jest obsługiwany dla systemu iOS 9,0 lub nowszego na urządzeniach iPad i iPhone.

## <a name="next-steps"></a>Następne kroki

* Eksplorowanie [zestawu immersyjny Reader dla systemu iOS w witrynie GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Szybki start: Tworzenie aplikacji dla systemu iOS, która uruchamia czytnik immersyjny (SWIFT)](./ios-quickstart.md)