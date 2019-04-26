---
title: Linki na tej stronie nie działają dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy związane z zerwane linki w aplikacji serwera Proxy aplikacji, które mają zintegrowany z usługą Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d860ac77653a97855248c45e43d28ea4c694979e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442254"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Linki na tej stronie nie działają dla aplikacji serwera Proxy aplikacji

Ten artykuł pomoże Ci rozwiązać problemy związane z łączy w swojej aplikacji serwera Proxy usługi Azure Active Directory aplikacji nie będą działać poprawnie.

## <a name="overview"></a>Omówienie 
Po opublikowaniu aplikacji serwera Proxy aplikacji, tylko linki, które działają w aplikacji domyślnie podano linki do miejsc docelowych, zawarty w opublikowanej głównego adresu URL. Łącza w obrębie aplikacji nie działają, wewnętrzny adres URL aplikacji prawdopodobnie nie obejmuje wszystkich miejsc docelowych łączy w obrębie aplikacji.

**Dlaczego tak się dzieje?** Po kliknięciu linku w aplikacji, serwer Proxy aplikacji próbuje rozpoznać adres URL, albo wewnętrzny adres URL w tej samej aplikacji lub jako adres URL dostępny zewnętrznie. Jeśli łącze wskazuje wewnętrzny adres URL, który nie znajduje się w tej samej aplikacji, nie należy do jednej z tych zasobników i powoduje błąd, który nie został odnaleziony.

## <a name="ways-you-can-resolve-broken-links"></a>Sposoby, które można rozwiązać przerwane łącza

Istnieją trzy sposoby, aby rozwiązać ten problem. W opcji poniżej są wymienione w zwiększania złożoności.

1.  Upewnij się, że wewnętrzny adres URL katalogu głównego, która zawiera odpowiednie linki w aplikacji. Dzięki temu wszystkie łącza jest rozpoznawana jako zawartości publikowanej w tej samej aplikacji.

    Jeśli zmiana wewnętrznego adresu URL, ale nie chcesz zmienić stroną docelową dla użytkowników, należy zmienić adres URL strony głównej do poprzednio opublikowanych wewnętrzny adres URL. Można to zrobić, przechodząc do "Azure Active Directory" -&gt; rejestracje aplikacji —&gt; wybierz aplikację, -&gt; właściwości. Na tej karcie właściwości widzisz pole "adres URL strony głównej", który można dopasować do strony docelowej żądaną.

2.  Jeśli aplikacje korzystają z w pełni kwalifikowanych nazw domen (FQDN), użyj [domen niestandardowych](application-proxy-configure-custom-domain.md) do publikowania aplikacji. Ta funkcja umożliwia ten sam adres URL ma być używany zarówno wewnętrznie i zewnętrznie.

    Ta opcja zapewnia, że linki w aplikacji są dostępne z zewnątrz za pośrednictwem serwera Proxy aplikacji, ponieważ linki w aplikacji do adresów URL, wewnętrznej również są rozpoznawane zewnętrznie. Wszystkie łącza nadal muszą należeć do opublikowanej aplikacji. Jednak przy użyciu tej opcji łącza nie muszą należeć do tej samej aplikacji oraz mogą należeć do wielu aplikacji.

3.  Jeśli żadna z tych opcji nie jest to możliwe, istnieje wiele opcji umożliwiających wbudowany link tłumaczenia. Te opcje obejmują przy użyciu usługi Intune Managed Browser, rozszerzenie Moje aplikacje lub ustawienia tłumaczenia łącze w swojej aplikacji. Aby dowiedzieć się więcej na temat każdego z tych opcji oraz jak je włączyć, zobacz [przekierowania zapisane na stałe linki do aplikacji opublikowanych przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Kolejne kroki
[Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)

