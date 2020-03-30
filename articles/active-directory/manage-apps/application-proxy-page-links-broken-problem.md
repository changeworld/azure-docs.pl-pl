---
title: Łącza na stronie nie działają w przypadku aplikacji proxy aplikacji
description: Jak rozwiązywać problemy z uszkodzonymi łączami w aplikacjach usługi Proxy aplikacji zintegrowanych z usługą Azure AD
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430243"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Łącza na stronie nie działają w przypadku aplikacji proxy aplikacji

Ten artykuł ułatwia rozwiązywanie problemów, dlaczego łącza w aplikacji serwera proxy aplikacji usługi Azure Active Directory nie działają poprawnie.

## <a name="overview"></a>Omówienie 
Po opublikowaniu aplikacji Proxy aplikacji jedynymi łączami, które działają domyślnie w aplikacji, są łącza do miejsc docelowych zawartych w opublikowanym głównym adresie URL. Łącza w aplikacjach nie działają, wewnętrzny adres URL aplikacji prawdopodobnie nie zawiera wszystkich miejsc docelowych łączy w aplikacji.

**Dlaczego tak się dzieje?** Po kliknięciu łącza w aplikacji serwer proxy aplikacji próbuje rozpoznać adres URL jako wewnętrzny adres URL w tej samej aplikacji lub jako zewnętrznie dostępny adres URL. Jeśli łącze wskazuje wewnętrzny adres URL, który nie znajduje się w tej samej aplikacji, nie należy do żadnego z tych zasobników i powoduje nie znaleziony błąd.

## <a name="ways-you-can-resolve-broken-links"></a>Sposoby rozwiązywania uszkodzonych łączy

Istnieją trzy sposoby rozwiązania tego problemu. Poniższe opcje są wymienione w rosnącej złożoności.

1.  Upewnij się, że wewnętrzny adres URL jest katalogiem głównym, który zawiera wszystkie odpowiednie łącza dla aplikacji. Dzięki temu wszystkie łącza mają być rozpoznawane jako zawartość opublikowana w tej samej aplikacji.

    Jeśli zmienisz wewnętrzny adres URL, ale nie chcesz zmieniać strony docelowej dla użytkowników, zmień adres URL strony głównej na wcześniej opublikowany wewnętrzny adres URL. Można to zrobić, przechodząc do "Azure&gt; Active Directory" — rejestracje aplikacji —&gt; wybierz aplikację — znakowanie.&gt; W sekcji znakowanie zobaczysz pole "Adres URL strony głównej", które możesz dostosować do żądanej strony docelowej. Jeśli nadal używasz starszych rejestracji aplikacji doświadczenie właściwości kartę będzie wyświetlać szczegóły "Adres URL strony głównej". 
    
    > [!IMPORTANT]
    > Aby wprowadzić powyższe zmiany, wymagane są prawa do modyfikowania obiektów aplikacji w usłudze Azure AD. Użytkownik musi być przypisany administrator [aplikacji](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) roli, która przyznaje prawa modyfikacji aplikacji w usłudze Azure AD do użytkownika.
    >

2.  Jeśli aplikacje używają w pełni kwalifikowanych nazw domen (FQDN), do publikowania aplikacji należy publikować [aplikacje za pomocą domen niestandardowych.](application-proxy-configure-custom-domain.md) Ta funkcja umożliwia ten sam adres URL, który ma być używany zarówno wewnętrznie, jak i zewnętrznie.

    Ta opcja gwarantuje, że łącza w aplikacji są dostępne zewnętrznie za pośrednictwem serwera proxy aplikacji, ponieważ łącza w aplikacji do wewnętrznych adresów URL są również rozpoznawane zewnętrznie. Wszystkie linki nadal muszą należeć do opublikowanej aplikacji. Jednak za pomocą tej opcji łącza nie muszą należeć do tej samej aplikacji i mogą należeć do wielu aplikacji.

3.  Jeśli żadna z tych opcji nie jest możliwa, istnieje wiele opcji włączania tłumaczenia linków wbudowanych. Opcje te obejmują korzystanie z zarządzanej przeglądarki usługi Intune, rozszerzenia Moje aplikacje lub przy użyciu ustawienia tłumaczenia łącza w aplikacji. Aby dowiedzieć się więcej o każdej z tych opcji i jak je włączyć, zobacz [Przekierowywanie linków zakodowanych na stałe dla aplikacji opublikowanych za pomocą serwera proxy aplikacji usługi Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Następne kroki
[Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)

