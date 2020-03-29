---
title: Znane problemy z przeglądarką Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o problemach podczas korzystania z biblioteki uwierzytelniania Firmy Microsoft dla języka JavaScript (MSAL.js) w przeglądarce Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696116"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Znane problemy w przeglądarce Safari z msal.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Ciche odnowienie tokenów w przeglądarkach Safari 12 i ITP 2.0

Systemy operacyjne Apple iOS 12 i MacOS 10.14 zawierały wersję [przeglądarki Safari 12](https://developer.apple.com/safari/whats-new/). Dla celów bezpieczeństwa i prywatności safari 12 zawiera [inteligentną ochronę przed śledzeniem 2.0.](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) Powoduje to zasadniczo, że przeglądarka do spadku plików cookie innych firm jest ustawiona. ITP 2.0 traktuje również pliki cookie ustawione przez dostawców tożsamości jako pliki cookie stron trzecich.

### <a name="impact-on-msaljs"></a>Wpływ na MSAL.js

Msal.js używa ukrytego elementu Iframe do wykonywania cichego pozyskiwania tokenów i odnawiania w `acquireTokenSilent` ramach wywołań. Żądania cichego tokenu są zależne od elementu Iframe mającego dostęp do uwierzytelnionej sesji użytkownika reprezentowanej przez pliki cookie ustawione przez usługę Azure AD. Dzięki itp 2.0 uniemożliwiając dostęp do tych plików cookie, MSAL.js nie `acquireTokenSilent` po cichu nabyć i odnowić tokeny, a to powoduje awarie.

Nie ma rozwiązania tego problemu w tym momencie i oceniamy opcje ze społecznością standardów.

### <a name="work-around"></a>Obejście

Domyślnie ustawienie ITP jest włączone w przeglądarce Safari. To ustawienie można wyłączyć, przechodząc do ustawień**Prywatność** preferencji i odznaczając opcję **Zapobiegaj śledzeniu między** **witrynami.** -> 

![ustawienie safari](./media/msal-js-known-issue-safari-browser/safari.png)

Należy obsługiwać `acquireTokenSilent` błędy z interakcyjnego wywołania tokenu nabycia, który monituje użytkownika, aby zalogować się.
Aby uniknąć powtarzających się logowań, podejście, które `acquireTokenSilent` można zaimplementować, polega na obsłudze błędu i zapewnieniu użytkownikowi opcji wyłączenia ustawienia itp. Po wyłączeniu ustawienia kolejne ciche odnawianie tokenów powinno zakończyć się pomyślnie.
