---
title: Problemy z programem Internet Explorer (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Użyj biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) z przeglądarką Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695861"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Znane problemy w przeglądarkach Internet Explorer i Microsoft Edge (MSAL.js)

Biblioteka uwierzytelniania Firmy Microsoft dla języka JavaScript (MSAL.js) jest generowana dla [języka JavaScript ES5,](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) dzięki czemu może działać w programie Internet Explorer. Jest jednak kilka rzeczy, o których warto wiedzieć.

## <a name="run-an-app-in-internet-explorer"></a>Uruchamianie aplikacji w programie Internet Explorer
Jeśli zamierzasz używać pliku MSAL.js w aplikacjach, które można uruchamiać w programie Internet Explorer, przed odwołaniem się do skryptu MSAL.js należy dodać odwołanie do polyfill obietnicy.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Dzieje się tak dlatego, że program Internet Explorer nie obsługuje obietnic JavaScript natywnie.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debugowanie aplikacji uruchomionej w programie Internet Explorer

### <a name="running-in-production"></a>Działa w produkcji
Wdrażanie aplikacji w środowiskach produkcyjnych (na przykład w aplikacjach sieci Web platformy Azure) zwykle działa poprawnie, pod warunkiem że użytkownik końcowy zaakceptował okna podręczne. Przetestowaliśmy go w programie Internet Explorer 11.

### <a name="running-locally"></a>Bieganie lokalnie
Jeśli chcesz uruchomić i debugować lokalnie aplikację działającą w programie Internet Explorer, musisz pamiętać o następujących *http://localhost:1234*zagadnieniach (załóżmy, że chcesz uruchomić aplikację jako):

- Program Internet Explorer ma mechanizm zabezpieczeń o nazwie "tryb chroniony", który uniemożliwia poprawne działanie pliku MSAL.js. Wśród objawów, po zalogowaniu się, strona może http://localhost:1234/nullzostać przekierowana do .

- Aby uruchomić i debugować aplikację lokalnie, należy wyłączyć ten "tryb chroniony". W tym celu:

    1. Kliknij pozycję **Narzędzia** programu Internet Explorer (ikona koła zębatego).
    1. Wybierz **pozycję Opcje internetowe,** a następnie kartę **Zabezpieczenia.**
    1. Kliknij strefę **Internetową** i wyczyść pozycję **Włącz tryb chroniony (wymaga ponownego uruchomienia programu Internet Explorer).** Program Internet Explorer ostrzega, że komputer nie jest już chroniony. Kliknij przycisk **OK**.
    1. Uruchom ponownie program Internet Explorer.
    1. Uruchamianie i debugowanie aplikacji.

Po zakończeniu przywróć ustawienia zabezpieczeń programu Internet Explorer.  Wybierz **pozycję Ustawienia** -> **Bezpieczeństwa** -> **opcji** -> internetowych**Zresetuj wszystkie strefy do poziomu domyślnego**.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [znanych problemach podczas korzystania z pliku MSAL.js w programie Internet Explorer](msal-js-use-ie-browser.md).