---
title: Znane problemy w przeglądarkach (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Dowiedz się więcej o wiedzieć problemy, korzystając z Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) za pomocą przeglądarki Safari.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873888"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Znane problemy w przeglądarce Safari MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Dyskretnej odnowienia tokenu na Safari 12 i ITP w wersji 2.0

Apple iOS 12 i systemów operacyjnych z systemem MacOS 10.14 uwzględnione wersji [przeglądarki Safari 12](https://developer.apple.com/safari/whats-new/). Ze względów bezpieczeństwa i prywatności obejmują Safari 12 [inteligentne 2.0 zapobiegania śledzenia](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Powoduje to zasadniczo przeglądarkę, aby usunąć pliki cookie innych firm, zostanie ustawiony. ITP 2.0 traktuje pliki cookie, ustawiane przez dostawców tożsamości jako pliki cookie innych firm.

### <a name="impact-on-msaljs"></a>Wpływ na MSAL.js

MSAL.js używa ukryty element Iframe do wykonywania dyskretnej uzyskanie tokenu i odnawiania jako część `acquireTokenSilent` wywołania. Dyskretnej żądania tokenu zależą od elementu Iframe, mieli dostęp do sesji uwierzytelnionego użytkownika, reprezentowane przez pliki cookie, ustaw przez usługę Azure AD. 2.0 ITP uniemożliwia dostęp do tych plików cookie z MSAL.js kończy się niepowodzeniem dyskretnie nabywania i odnawiania tokenów i skutkuje `acquireTokenSilent` błędów.

W tym momencie istnieje rozwiązanie tego problemu i możemy sprawdzamy ze społecznością standardy.

### <a name="work-around"></a>Obejście

Domyślnie ustawienie ITP jest włączone w przeglądarce Safari. To ustawienie zostanie wyłączone, przechodząc do **preferencje** -> **zachowania** i usuwając zaznaczenie pola wyboru **zapobiec śledzenia między lokacjami** opcji.

![ustawienia przeglądarki Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Konieczne będzie obsługiwać `acquireTokenSilent` błędy z interaktywną uzyskania tokenu wywołania monitujący użytkownika do logowania.
Aby uniknąć wielokrotnego logowania, można zaimplementować podejście jest obsługa `acquireTokenSilent` awarii i udostępniać użytkownikom opcję, aby wyłączyć ustawienie ITP w przeglądarce Safari przed kontynuowaniem wywołań interakcyjnego. Gdy to ustawienie jest wyłączone, kolejne dyskretnej odnowienia tokenu ma być pomyślnie wykonane.
