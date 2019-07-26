---
title: Strona aplikacji nie jest poprawnie wyświetlana dla aplikacji serwera proxy aplikacji | Microsoft Docs
description: Wskazówki, gdy strona nie jest prawidłowo wyświetlane w aplikacji serwera Proxy aplikacji jest zintegrowana z usługą Azure AD
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
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381416"
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji

Ten artykuł ułatwia rozwiązywanie problemów z aplikacji serwera Proxy usługi Azure Active Directory aplikacji, gdy przejdź do strony, ale coś, co na stronie wygląda na poprawny.

## <a name="overview"></a>Przegląd
Podczas publikowania aplikacji serwera Proxy aplikacji, tylko strony w obszarze główny są dostępne podczas uzyskiwania dostępu do aplikacji. Jeśli strona nie jest wyświetlane prawidłowo, główny wewnętrzny adres URL używany dla aplikacji może brakować niektórych zasobów strony. Aby rozwiązać problem, upewnij się, zostały opublikowane *wszystkich* zasobów dla strony w ramach Twojej aplikacji.

Aby sprawdzić, czy brak zasobów jest problemem, można otworzyć narzędzie do śledzenia sieci (na przykład programu Fiddler lub F12 Tools in Internet Explorer/Microsoft Edge), załadować stronę i wyszukać błędy 404. Wskazująca obecnie nie można odnaleźć strony i należy je opublikować.

Jako przykład takim założono publikujesz aplikację koszty przy użyciu wewnętrznego adresu URL `http://myapps/expenses`, ale aplikacja korzysta z arkusza stylów `http://myapps/style.css`. W tym przypadku arkusza stylów nie został opublikowany w Twojej aplikacji, dzięki czemu podczas ładowania aplikacji wydatki zgłosić błąd 404 podczas próby załadowania style.css. W tym przykładzie problem został rozwiązany przez opublikowanie aplikacji przy użyciu wewnętrznego adresu URL `http://myapp/`.

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jedna aplikacja

Jeśli nie jest możliwe do publikowania wszystkich zasobów w ramach tej samej aplikacji, musisz publikowanie wielu aplikacji i włączyć łącza między nimi.

Aby to zrobić, zaleca się używanie [domen niestandardowych](application-proxy-configure-custom-domain.md) rozwiązania. Jednak to rozwiązanie wymaga własny certyfikat dla domeny i aplikacji, użyj w pełni kwalifikowanych nazw domen (FQDN). Innych opcjach, zobacz temat [Rozwiązywanie problemów z dokumentacji uszkodzone linki](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
