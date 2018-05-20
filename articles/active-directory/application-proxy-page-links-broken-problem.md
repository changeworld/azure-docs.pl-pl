---
title: Łącza na stronie nie działają dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z uszkodzonych łączy w przypadku aplikacji serwera Proxy aplikacji, który jest zintegrowany z usługą Azure AD
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
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 7c505080e6b6144ab3cf24ad89bb084efbb77c29
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Łącza na stronie nie działają dla aplikacji serwera Proxy aplikacji

Ten artykuł pomaga w rozwiązywaniu problemów, dlaczego łącza dla aplikacji serwera Proxy usługi Azure Active Directory aplikacji nie działa prawidłowo.

## <a name="overview"></a>Przegląd 
Po opublikowaniu aplikacji serwera Proxy aplikacji, tylko łącza, które działają domyślnie w aplikacji są łącza do miejsc docelowych zawartych w opublikowanej głównego adresu URL. Łączy w obrębie aplikacje nie działają, wewnętrzny adres URL dla aplikacji, prawdopodobnie nie zawiera wszystkich miejsc docelowych łączy w obrębie aplikacji.

**Dlaczego to możliwe?** Po kliknięciu linku w aplikacji, serwer Proxy aplikacji próbuje rozpoznać adres URL jako wewnętrzne URL w tej samej aplikacji lub jako adres URL dostępny zewnętrznie. Jeśli link wskazuje wewnętrzny adres URL, który nie jest w tej samej aplikacji, nie należy do żadnego z tych zasobników i powoduje błędu nie znaleziono.

## <a name="ways-you-can-resolve-broken-links"></a>Sposoby, możesz rozwiązać przerwane łącza

Istnieją trzy sposoby, aby rozwiązać ten problem. W opcji poniżej są wyświetlane w rosnącej złożoności.

1.  Upewnij się, że wewnętrzny adres URL jest głównego, który zawiera wszystkie łącza do aplikacji. Dzięki temu wszystkie linki zostać rozpoznane jako zawartość opublikowane w tej samej aplikacji.

    Jeśli zmiany wewnętrznego adresu URL, ale nie chcesz zmienić strona docelowa dla użytkowników, należy zmienić adres URL strony głównej na wydane wcześniej wewnętrznego adresu URL. Można to zrobić przy przechodzeniu "Azure Active Directory" -&gt; rejestracji aplikacji -&gt; wybierz aplikację, -&gt; właściwości. Na tej karcie właściwości jest widoczny pole "adres URL strony głównej", który można dostosować jako strona docelowa żądany.

2.  Jeśli aplikacje Użyj w pełni kwalifikowanych nazw domen (FQDN), użyj [domen niestandardowych](manage-apps/application-proxy-configure-custom-domain.md) do publikowania aplikacji. Ta funkcja umożliwia ten sam adres URL do użycia zarówno wewnętrznie i zewnętrznie.

    Tej opcji zapewnia, że łącza do aplikacji są dostępne z zewnątrz za pośrednictwem serwera Proxy aplikacji, ponieważ łączy w aplikacji do wewnętrznych adresów URL również są rozpoznawane zewnętrznie. Wszystkie linki nadal muszą należeć do opublikowanej aplikacji. Jednak ta opcja łączy nie muszą należeć do tej samej aplikacji i może należeć do wielu aplikacji.

3.  Jeśli żadna z tych opcji jest możliwe, można wyświetlić podgląd nowej funkcji, który wykonuje translację/poprawiania adresu URL. Przy użyciu tej funkcji wewnętrznych adresów URL łącza, który istnieje w treści HTML aplikacji są translacji lub "mapowane", do opublikowanej zewnętrznego serwera Proxy adresy URL aplikacji. Tłumaczenie działa tylko w łączach w HTML i CSS, a nie pomoże, czy łącze jest generowany przez JS. 

W związku z tym zdecydowanie zaleca się używanie [domen niestandardowych](manage-apps/application-proxy-configure-custom-domain.md) rozwiązanie, jeśli to możliwe. Jeśli chcesz dołączyć do wersji zapoznawczej, Wyślij wiadomość e-mail <aadapfeedback@microsoft.com> z applicationId(s).

## <a name="next-steps"></a>Kolejne kroki
[Praca z istniejącym lokalnych serwerów proxy](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

