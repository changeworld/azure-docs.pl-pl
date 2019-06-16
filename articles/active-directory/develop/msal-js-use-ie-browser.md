---
title: W programie Internet Explorer (Microsoft Authentication Library dla języka JavaScript) | Azure
description: Informacje o używaniu Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) za pomocą przeglądarki Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873918"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Znane problemy w przeglądarkach Internet Explorer i Microsoft Edge z MSAL.js

Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) jest generowany dla [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , aby go uruchomić w przeglądarce Internet Explorer. Istnieje jednak kilka kwestii, które znasz.

## <a name="run-an-app-in-internet-explorer"></a>Uruchamianie aplikacji w programie Internet Explorer
Jeśli zamierzasz używać MSAL.js w aplikacjach, które można uruchomić w przeglądarce Internet Explorer, należy dodać odwołanie do kód Pro promise przed odwołaniem się do skryptu MSAL.js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Jest to spowodowane programu Internet Explorer nie obsługuje natywnie obietnic języka JavaScript.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debugowanie aplikacji w programie Internet Explorer

### <a name="running-in-production"></a>Działających w środowisku produkcyjnym
Wdrażanie aplikacji do środowiska produkcyjnego (na przykład w usłudze Azure Web apps) zwykle działa prawidłowo, pod warunkiem, że użytkownik końcowy zaakceptował okna podręczne. Możemy przetestowane za pomocą programu Internet Explorer 11.

### <a name="running-locally"></a>Uruchomiony lokalnie
Jeśli chcesz uruchamiać i debugować lokalnie aplikację w programie Internet Explorer, należy pamiętać o następujących kwestiach (przyjęto założenie, chcesz uruchomić aplikację jako *http://localhost:1234* ):

- Program Internet Explorer jest mechanizm zabezpieczeń o nazwie "Tryb chroniony", co zapobiega MSAL.js działa prawidłowo. Wśród objawy, po zalogowaniu, strona może zostać przekierowany do http://localhost:1234/null.

- Do uruchamiania i debugowania aplikacji w środowisku lokalnym, należy wyłączyć to "Tryb chroniony". W tym:

    1. Program Internet Explorer kliknij **narzędzia** (ikonę koła zębatego).
    1. Wybierz **Opcje internetowe** i następnie **zabezpieczeń** kartę.
    1. Kliknij pozycję **Internet** strefy i usuń zaznaczenie pola wyboru **Włącz tryb chroniony (wymaga ponownego uruchomienia programu Internet Explorer)** . Program Internet Explorer ostrzega o tym, że komputer nie jest już chronione. Kliknij przycisk **OK**.
    1. Ponownie uruchom program Internet Explorer.
    1. Uruchamianie i debugowanie aplikacji.

Gdy wszystko będzie gotowe, przywracanie ustawień zabezpieczeń programu Internet Explorer.  Wybierz **ustawienia** -> **Opcje internetowe** -> **zabezpieczeń** -> **zresetować wszystkie strefy na domyślny poziom**.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [znane problemy w programie Internet Explorer przy użyciu MSAL.js](msal-js-use-ie-browser.md).