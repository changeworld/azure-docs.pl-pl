---
title: Strona aplikacji nie jest wyświetlana poprawnie dla aplikacji Proxy aplikacji | Dokumenty firmy Microsoft
description: Wskazówki, gdy strona nie jest wyświetlana poprawnie w aplikacji proxy aplikacji, która została zintegrowana z usługą Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13e73f0ed56648ce162f00d6df5e7b86a922ca01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381416"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie jest wyświetlana poprawnie dla aplikacji proxy aplikacji

Ten artykuł ułatwia rozwiązywanie problemów z aplikacjami serwera proxy aplikacji usługi Azure Active Directory podczas przechodzenia do strony, ale coś na stronie nie wygląda poprawnie.

## <a name="overview"></a>Omówienie
Podczas publikowania aplikacji proxy aplikacji tylko strony w katalogu głównym są dostępne podczas uzyskiwania dostępu do aplikacji. Jeśli strona nie jest wyświetlana poprawnie, w głównym wewnętrznym adresie URL używanym dla aplikacji może brakować niektórych zasobów strony. Aby rozwiązać, upewnij się, że zostały opublikowane *wszystkie* zasoby dla strony jako część aplikacji.

Możesz sprawdzić, czy brakujące zasoby to problem, otwierając moduł śledzenia sieci (na przykład narzędzia Fiddler lub F12 w programie Internet Explorer/Microsoft Edge), ładując stronę i wyszukując błędy 404. Oznacza to, że strony obecnie nie można znaleźć i że trzeba je opublikować.

Na przykład w tym przypadku załóżmy, że opublikowano `http://myapps/expenses`aplikację wydatków przy użyciu `http://myapps/style.css`wewnętrznego adresu URL, ale aplikacja używa arkusza stylów . W takim przypadku arkusz stylów nie jest publikowany w aplikacji, więc ładowanie aplikacji wydatków zgłosić błąd 404 podczas próby załadowania style.css. W tym przykładzie problem został rozwiązany przez opublikowanie `http://myapp/`aplikacji przy wewnętrznym adresie URL .

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jedna aplikacja

Jeśli nie jest możliwe opublikowanie wszystkich zasobów w ramach tej samej aplikacji, należy opublikować wiele aplikacji i włączyć łącza między nimi.

Aby to zrobić, zalecamy użycie rozwiązania [domen niestandardowych.](application-proxy-configure-custom-domain.md) Jednak to rozwiązanie wymaga posiadania certyfikatu dla domeny, a aplikacje używają w pełni kwalifikowanych nazw domen (FQDN). Aby uzyskać inne opcje, zobacz rozwiązywanie problemów z [dokumentacją przerwanych łączy](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Następne kroki
[Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
