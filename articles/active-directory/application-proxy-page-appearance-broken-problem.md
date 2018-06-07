---
title: Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Wskazówki, gdy strona nie jest prawidłowo wyświetlane w aplikacji serwer Proxy aplikacji jest zintegrowany z usługą Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: d30f836dd729ea5aaf9cb8e502ab65d2521cf6ab
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591538"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji

Ten artykuł ułatwia rozwiązywanie problemów z aplikacji serwera Proxy usługi Azure Active Directory aplikacji, przejdź do strony, ale coś na stronie nie poprawna.

## <a name="overview"></a>Przegląd
Podczas publikowania aplikacji serwera Proxy aplikacji tylko strony w katalogu głównym są dostępne podczas uzyskiwania dostępu do aplikacji. Jeśli strona nie jest poprawne wyświetlanie, adres URL wewnętrzny główny używany dla aplikacji może brakować niektórych zasobów strony. Aby rozwiązać, upewnij się, po opublikowaniu *wszystkie* zasobów na stronie jako część aplikacji.

Możesz sprawdzić, czy brakuje zasobów jest problem, otwierając tracker Twojej sieci (takie jak Fiddler lub F12 narzędzia w Internet Explorer/krawędzi), podczas ładowania strony i wyszukiwanie błędów 404. Wskazująca obecnie nie można odnaleźć strony i należy je opublikować.

Na przykład ten przypadek założono po opublikowaniu aplikacji kosztów przy użyciu wewnętrznego adresu URL http://myapps/expenses, ale aplikacja korzysta z arkusza stylów http://myapps/style.css. W takim przypadku arkusza stylów nie jest opublikowana w aplikacji, dlatego podczas ładowania aplikacji kosztów zgłosić błąd 404 podczas próby załadowania style.css. W tym przykładzie problem został rozwiązany przez opublikowaniem aplikacji z wewnętrznego adresu URL http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jedną aplikację

Jeśli nie jest możliwe do publikowania wszystkich zasobów w tej samej aplikacji, należy opublikować wiele aplikacji i włączyć łącza między nimi.

Aby to zrobić, zaleca się używanie [domen niestandardowych](manage-apps/application-proxy-configure-custom-domain.md) rozwiązania. Jednak to rozwiązanie wymaga własny certyfikat dla domeny i aplikacji użyj w pełni kwalifikowanych nazw domen (FQDN). Inne opcje, zobacz [Rozwiązywanie problemów z dokumentacji uszkodzonych łączy](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
