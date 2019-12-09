---
title: Korzystanie z programu Internet Explorer z MSAL. js | Azure
titleSuffix: Microsoft identity platform
description: Użyj biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) przy użyciu przeglądarki Internet Explorer.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 715e92a10ca0b4cbe38119931bc66d36c3b9259a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916285"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Korzystanie z programu Internet Explorer i przeglądarek Microsoft Edge z MSAL. js

Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) jest generowana na potrzeby [języka JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , dzięki czemu można uruchamiać ją w programie Internet Explorer. Istnieje jednak kilka rzeczy, które należy znać.

## <a name="run-an-app-in-internet-explorer"></a>Uruchamianie aplikacji w programie Internet Explorer
Jeśli zamierzasz używać MSAL. js w aplikacjach, które mogą być uruchamiane w programie Internet Explorer, musisz dodać odwołanie do wypełnienia obietnicą przed odwołaniem do skryptu MSAL. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Wynika to z faktu, że program Internet Explorer nie obsługuje natywnie niesie obietnice zwiększenia języka JavaScript.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debugowanie aplikacji działającej w programie Internet Explorer

### <a name="running-in-production"></a>Uruchamianie w środowisku produkcyjnym
Wdrażanie aplikacji w środowisku produkcyjnym (na przykład w usłudze Azure Web Apps) zwykle działa prawidłowo, pod warunkiem, że użytkownik końcowy zaakceptował okna podręczne. Przetestowano ją z programem Internet Explorer 11.

### <a name="running-locally"></a>Uruchamianie lokalne
Jeśli chcesz uruchomić i debugować lokalnie aplikację działającą w programie Internet Explorer, musisz pamiętać o następujących kwestiach (Załóżmy, że chcesz uruchomić aplikację jako *http://localhost:1234* ):

- Program Internet Explorer ma mechanizm zabezpieczeń o nazwie "tryb chroniony", który uniemożliwia poprawne działanie MSAL. js. Po zalogowaniu się do strony można przekierować do http://localhost:1234/null.

- Aby uruchomić i debugować aplikację lokalnie, należy wyłączyć ten tryb chroniony. Dla tego:

    1. Kliknij pozycję **Narzędzia** programu Internet Explorer (ikona koła zębatego).
    1. Wybierz pozycję **Opcje internetowe** , a następnie kartę **zabezpieczenia** .
    1. Kliknij strefę **Internet** , a następnie usuń zaznaczenie pola wyboru **Włącz tryb chroniony (wymaga ponownego uruchomienia programu Internet Explorer)** . Program Internet Explorer ostrzega o tym, że komputer nie jest już chroniony. Kliknij przycisk **OK**.
    1. Uruchom ponownie program Internet Explorer.
    1. Uruchamianie i debugowanie aplikacji.

Gdy wszystko będzie gotowe, Przywróć ustawienia zabezpieczeń programu Internet Explorer.  Wybierz pozycję **ustawienia** -> **Opcje internetowe** -> **zabezpieczenia** -> **Zresetuj wszystkie strefy do poziomu domyślnego**.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [znanych problemach dotyczących używania MSAL. js w programie Internet Explorer](msal-js-use-ie-browser.md).