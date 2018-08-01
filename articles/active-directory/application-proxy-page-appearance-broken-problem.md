---
title: Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Wskazówki, gdy strona nie jest prawidłowo wyświetlane w aplikacji serwera Proxy aplikacji jest zintegrowana z usługą Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 118d5780145d0421160c70546f01dc930190185e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365213"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji

Ten artykuł ułatwia rozwiązywanie problemów z aplikacji serwera Proxy usługi Azure Active Directory aplikacji, gdy przejdź do strony, ale coś, co na stronie wygląda na poprawny.

## <a name="overview"></a>Przegląd
Podczas publikowania aplikacji serwera Proxy aplikacji, tylko strony w obszarze główny są dostępne podczas uzyskiwania dostępu do aplikacji. Jeśli strona nie jest wyświetlane prawidłowo, główny wewnętrzny adres URL używany dla aplikacji może brakować niektórych zasobów strony. Aby rozwiązać problem, upewnij się, zostały opublikowane *wszystkich* zasobów dla strony w ramach Twojej aplikacji.

Możesz sprawdzić, jeśli brakujących zasobów jest problem, otwierając usługi śledzenia sieci (takich jak narzędzia Fiddler lub F12 narzędzia w Internet Explorer/Microsoft Edge), podczas ładowania strony i wyszukiwania 404 błędów. Wskazująca obecnie nie można odnaleźć strony i należy je opublikować.

Jako przykład takim założono publikujesz aplikację koszty przy użyciu wewnętrznego adresu URL http://myapps/expenses, ale aplikacja korzysta z arkusza stylów http://myapps/style.css. W tym przypadku arkusza stylów nie został opublikowany w Twojej aplikacji, dzięki czemu podczas ładowania aplikacji wydatki zgłosić błąd 404 podczas próby załadowania style.css. W tym przykładzie problem został rozwiązany przez opublikowanie aplikacji przy użyciu wewnętrznego adresu URL http://myapp/.

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jedna aplikacja

Jeśli nie jest możliwe do publikowania wszystkich zasobów w ramach tej samej aplikacji, musisz publikowanie wielu aplikacji i włączyć łącza między nimi.

Aby to zrobić, zaleca się używanie [domen niestandardowych](manage-apps/application-proxy-configure-custom-domain.md) rozwiązania. Jednak to rozwiązanie wymaga własny certyfikat dla domeny i aplikacji, użyj w pełni kwalifikowanych nazw domen (FQDN). Innych opcjach, zobacz temat [Rozwiązywanie problemów z dokumentacji uszkodzone linki](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
