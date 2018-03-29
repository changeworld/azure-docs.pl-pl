---
title: Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Wskazówki, gdy strona nie jest prawidłowo wyświetlane w aplikacji serwer Proxy aplikacji jest zintegrowany z usługą Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: asteen
ms.openlocfilehash: d187b545a486be28fc80e6baf8e58079ff94ec5e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="application-page-does-not-display-correctly-for-an-application-proxy-application"></a>Strona aplikacji nie są wyświetlane poprawnie dla aplikacji serwera Proxy aplikacji

W tym artykule ułatwiają rozwiązywanie problemów z aplikacji serwera Proxy usługi Azure Active Directory aplikacji, przejdź do strony, ale coś na stronie nie poprawna.

## <a name="overview"></a>Przegląd
Podczas publikowania aplikacji serwera Proxy aplikacji tylko strony w katalogu głównym są dostępne podczas uzyskiwania dostępu do aplikacji. Jeśli strona nie jest poprawne wyświetlanie, adres URL wewnętrzny główny używany dla aplikacji może brakować niektórych zasobów strony. Aby rozwiązać, upewnij się, po opublikowaniu *wszystkie* zasobów na stronie jako część aplikacji.

Możesz sprawdzić, jest to problem, otwierając tracker Twojej sieci (takie jak Fiddler lub F12 narzędzia w Internet Explorer/krawędzi), podczas ładowania strony i wyszukiwanie błędów 404. Wskazująca stron, które aktualnie nie można odnaleźć i muszą zostać opublikowane.

Na przykład ten przypadek założono po opublikowaniu aplikacji kosztów przy użyciu wewnętrznego adresu URL z <http://myapps/expenses>, ale aplikacja korzysta z arkusza stylów <http://myapps/style.css>. W takim przypadku arkusza stylów nie jest opublikowana w aplikacji, dlatego podczas ładowania aplikacji kosztów zgłosić błąd 404 podczas próby załadowania style.css. W tym przykładzie będzie można rozwiązać problemu, przez opublikowaniem aplikacji z wewnętrzny adres URL <http://myapp/> zamiast tego.

## <a name="problems-with-publishing-as-one-application"></a>Problemy z publikowaniem jako jedną aplikację

Jeśli nie jest możliwe do publikowania wszystkich zasobów w tej samej aplikacji, należy opublikować wiele aplikacji i włączyć łącza między nimi.

Aby to zrobić, zaleca się używanie [domen niestandardowych](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) rozwiązania. Jednak to rozwiązanie wymaga własny certyfikat dla domeny i aplikacji użyj w pełni kwalifikowanych nazw domen (FQDN). Inne opcje, zobacz [Rozwiązywanie problemów z dokumentacji uszkodzonych łączy](application-proxy-page-links-broken-problem.md).

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-publish-azure-portal.md)
