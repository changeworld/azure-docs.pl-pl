---
title: Tłumaczenie linków i serwera Proxy aplikacji adresów URL usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2949559542759cadf90d329bc50b352998b3eb7e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262554"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Przekieruj zapisane na stałe linki do aplikacji opublikowanych przy użyciu serwera Proxy aplikacji usługi Azure AD

Serwer Proxy aplikacji usługi Azure AD udostępnia swoje aplikacje w środowisku lokalnym dla użytkowników, którzy są obiektami zdalnymi lub w ich własnych urządzeniach. Niektóre aplikacje, zostały opracowane za pomocą lokalnych łączy osadzonych w kodzie HTML. Te łącza nie będą działać poprawnie po aplikacji jest używany zdalnie. Jeśli masz kilka aplikacji lokalnych, które wskazują ze sobą, Użytkownicy oczekują łącza, aby kontynuować pracę, jeśli nie znajdują się w biurze. 

Najlepszym sposobem, aby upewnić się, że linki działać tak samo i spoza sieci firmowej jest skonfigurować zewnętrzne adresy URL aplikacji jako takie same jak ich wewnętrzne adresy URL. Użyj [domen niestandardowych](application-proxy-configure-custom-domain.md) do skonfigurowania usługi zewnętrzne adresy URL mieć nazwę domeny firmowej zamiast domyślnej domeny serwera proxy aplikacji.


W przypadku domen niestandardowych nie można używać w Twojej dzierżawie, istnieje kilka innych opcji dodaniem tej funkcji. Wszystkie są również zgodne z domenami niestandardowymi i ze sobą nawzajem tak domen niestandardowych i innych rozwiązań, w razie potrzeby można skonfigurować. 

**Opcja 1: Użyj programu Managed Browser** — to rozwiązanie dotyczy tylko jeśli planowane jest zalecane, lub wymagasz, użytkownicy mieli dostęp do aplikacji za pomocą usługi Intune Managed Browser. Będzie ona obsługiwać wszystkie opublikowane adresy URL. 

**Opcja 2: Za pomocą rozszerzenia MyApps** — to rozwiązanie wymaga od użytkowników można zainstalować rozszerzenia przeglądarki po stronie klienta, ale będą obsługiwać wszystkie opublikowane adresy URL, który działa w najbardziej popularnych przeglądarek. 

**Opcja 3: Użyj ustawienia tłumaczenia łącze** — to ustawienie po stronie administratora, który jest niewidoczny dla użytkowników. Jednak tylko będą obsługiwać adresy URL w kodzie HTML i CSS. Zakodowane wewnętrzne adresy URL wygenerowanych za pośrednictwem języka Javascript (na przykład) nie będzie działać.  

Te trzy funkcje Zachowaj łączy działa niezależnie od tego, gdzie są Twoi użytkownicy. W przypadku aplikacji, które wskazują bezpośrednio do wewnętrznych punktów końcowych lub portów można mapować tych wewnętrznych adresów URL opublikowanej zewnętrzny URL serwera Proxy aplikacji. 

 
> [!NOTE]
> Ostatnia opcja jest tylko w przypadku dzierżaw, które z jakichkolwiek przyczyn, nie mogą używać domeny niestandardowe mają ten sam wewnętrzne i zewnętrzne adresy URL dla swoich aplikacji. Przed włączeniem tej funkcji należy sprawdzić, czy [domenami niestandardowymi na serwer Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) może pracować za Ciebie. 
> 
> Lub, jeśli aplikacji, musisz skonfigurować przy użyciu linku tłumaczenia jest program SharePoint, zobacz [Konfigurowanie alternatywnych mapowań dostępu dla programu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) dla innego podejścia do mapowania łącza. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Opcja 1: Integracja z przeglądarki zarządzane przez usługę Intune 

Intune Managed Browser umożliwia dodatkową ochronę aplikacji i zawartości. Aby użyć tego rozwiązania, należy wymagać/zaleca się użytkownikom dostępu aplikacji za pomocą usługi Intune Managed Browser. Wszystkie wewnętrzne adresy URL opublikowana z użyciem serwera Proxy aplikacji będzie rozpoznawany przez Managed Browser i Przekierowanie do odpowiedniej zewnętrzny adres URL. Dzięki temu pracy wszystkich ustaloną wewnętrznych adresów URL, a jeśli użytkownik przejdzie do przeglądarki i typy bezpośrednio wewnętrzny adres URL, działa, nawet jeśli użytkownik jest zdalny.  

Aby uzyskać więcej informacji, w tym sposób konfigurowania tej opcji, zobacz [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentacji.  

### <a name="option-2-myapps-browser-extension"></a>Opcja 2: Rozszerzenie przeglądarki MyApps 

Przy użyciu rozszerzenia przeglądarki MyApps wszystkie adresy URL wewnętrznej opublikowana z użyciem serwera Proxy aplikacji są rozpoznawane przez rozszerzenie i Przekierowanie do odpowiedniej zewnętrznego adresu URL. Dzięki temu pracy wszystkich ustaloną wewnętrznych adresów URL, a jeśli użytkownik przechodzi do paska adresu w przeglądarce i bezpośrednie typy wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby użyć tej funkcji, użytkownik musi pobrać rozszerzenie i zalogować się. Brak innych konfiguracji potrzebne do administratorów lub użytkowników. 

 

### <a name="option-3-link-translation-setting"></a>Opcja 3: Ustawienie tłumaczenia łącze 

Podczas tłumaczenia łącze jest włączone, usługa serwera Proxy aplikacji wyszukuje przy użyciu języków HTML i CSS opublikowanych łączy wewnętrznych i tłumaczy je tak, aby użytkownicy pobierają nieprzerwaną środowisko. 



## <a name="how-link-translation-works"></a>Jak połączyć działa tłumaczenia

Po uwierzytelnieniu, gdy serwer proxy przekazuje dane aplikacji dla użytkownika serwera Proxy aplikacji skanuje aplikacji zapisane na stałe linki i zastępuje je ich odpowiednich opublikowane zewnętrzne adresy URL.

Serwer Proxy aplikacji przyjęto założenie, że aplikacje są zakodowane w formacie UTF-8. Jeśli nie jest to wymagane, określ typ kodowania w nagłówku odpowiedzi http, takie jak `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Łączy, których dotyczy problem?

Funkcja tłumaczenia łączenia wyszukuje tylko linki, które znajdują się w tagi kodu w treści aplikacji. Serwer Proxy aplikacji ma oddzielne funkcji tłumaczenia plików cookie lub adresy URL w nagłówkach. 

Istnieją dwie typowe rodzaje łączy wewnętrznych w aplikacjach środowiska lokalnego:

- **Względna łączy wewnętrznych** , takie jak punkt do udostępnionego zasobu w strukturze pliku lokalnego `/claims/claims.html`. Te linki automatycznie działają w aplikacji, które są publikowane za pośrednictwem serwera Proxy aplikacji usługi i kontynuować pracę z lub bez niej łącze tłumaczenia. 
- **Łączy wewnętrznych zapisane na stałe** do innych aplikacji lokalnych, takich jak `http://expenses` lub opublikowane plików, takie jak `http://expenses/logo.jpg`. Funkcja tłumaczenia łączenia działa dla łączy wewnętrznych zapisane na stałe i zmienia się je, aby wskazywał zewnętrzne adresy URL, które użytkownicy zdalni muszą przechodzić przez.

Pełną listę tagów kodu HTML, czy serwer Proxy aplikacji obsługuje tłumaczenia łącze do dołączenia:
* a
* audio
* podstawowy
* Przycisk
* div
* Osadź
* Formularz
* Ramki
* główny
* html
* element IFRAME
* img
* wejście
* link
* Element MenuItem
* meta
* obiekt
* skrypt
* source
* Śledzenie
* wideo

Ponadto w ramach CSS atrybut adresu URL jest również translacji.

### <a name="how-do-apps-link-to-each-other"></a>Jak aplikacje połączenie ze sobą?

Tłumaczenie łącze jest włączone dla każdej aplikacji tak, aby mieć kontrolę nad środowiskiem użytkownika na poziomie poszczególnych aplikacji. Włącz tłumaczenia link dla aplikacji, jeśli chcesz, aby linki *z* tej aplikacji, które ma zostać poddany translacji, nie łączy *do* tej aplikacji. 

Na przykład załóżmy, że masz trzy aplikacje opublikowane za pośrednictwem serwera Proxy aplikacji, że wszystkie łączą się wzajemnie: Korzyści, kosztów i podróży. Brak aplikacji czwarty opinię, która nie jest opublikowana przy użyciu serwera Proxy aplikacji.

Po włączeniu tłumaczenia link aplikacji korzyści, łącza do kosztów i podróże są przekierowywane do zewnętrznych adresów URL dla tych aplikacji, ale nie zostanie przekierowana łącze do informacji zwrotnych, ponieważ nie istnieje żadne zewnętrznego adresu URL. Linki z wydatków i podróży do korzyści nie działa, ponieważ łącze tłumaczenia nie został włączony dla tych dwóch aplikacji.

![Linki z korzyści do innych aplikacji, po włączeniu tłumaczenia łącza](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Łącza, które nie są tłumaczone?

Aby zwiększyć wydajność i bezpieczeństwo, nie są tłumaczone niektóre łącza:

- Łącza nie wewnątrz kodu znaczników. 
- Łącza nie jest w formacie HTML i CSS. 
- Łącza w formacie zakodowanym adresu URL.
- Wewnętrzne linki otwierane z innych programów. Nie można przetłumaczyć łącza wysyłane za pośrednictwem poczty e-mail lub wiadomości błyskawicznych lub zawartych w innych dokumentach. Użytkownicy muszą wiedzieć, aby przejść do zewnętrznego adresu URL.

Jeśli zachodzi potrzeba obsługi jednej z tych dwóch scenariuszy, należy użyć tych samych adresów URL wewnętrznych i zewnętrznych, zamiast łącza tłumaczenia.  

## <a name="enable-link-translation"></a>Włącz łącze tłumaczenia

Wprowadzenie do tłumaczenia łącze jest równie proste jak kliknięcie przycisku:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje** > Wybierz aplikację, którą chcesz zarządzać >  **Serwer proxy aplikacji**.
3. Włącz **translacji adresów URL w treści aplikacji** do **tak**.

   ![Wybierz pozycję Tak, aby translacji adresów URL w treści aplikacji](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Wybierz **Zapisz** Aby zastosować zmiany.

Teraz gdy użytkownicy uzyskują dostęp do tej aplikacji, serwer proxy będzie automatycznie Skanuj w poszukiwaniu wewnętrzne adresy URL, które zostały opublikowane za pośrednictwem serwera Proxy aplikacji w dzierżawie usługi.

## <a name="send-feedback"></a>Prześlij opinię

Chcemy Twojej pomocy, aby ta funkcja działa w przypadku wszystkich aplikacji. Przeszukiwania ponad 30 tagów w kodzie HTML i CSS. Przykład wygenerowanych łączy, które nie są podlega translacji, przesyłać fragment kodu [Opinia dotycząca aplikacji serwera Proxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Kolejne kroki
[Użyj domen niestandardowych przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md) mieć ten sam adres URL wewnętrznych i zewnętrznych

[Konfigurowanie alternatywnych mapowań dostępu dla programu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
