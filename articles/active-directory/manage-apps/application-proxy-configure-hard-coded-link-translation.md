---
title: Tłumaczenie łączy i adresów URL usługi Azure AD App Proxy | Dokumenty firmy Microsoft
description: Obejmuje podstawowe informacje o łącznikach serwera proxy aplikacji usługi Azure AD.
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
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533700"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Przekierowywanie łączy zakodowanych na stałe dla aplikacji opublikowanych za pomocą serwera proxy aplikacji usługi Azure AD

Usługa Azure AD Application Proxy udostępnia aplikacje lokalne użytkownikom zdalnym lub na własnych urządzeniach. Niektóre aplikacje zostały jednak opracowane z lokalnymi linkami osadzonymi w kodzie HTML. Te łącza nie działają poprawnie, gdy aplikacja jest używana zdalnie. Jeśli masz kilka aplikacji lokalnych wskazują na siebie, użytkownicy oczekują, że łącza będą nadal działać, gdy nie są w biurze. 

Najlepszym sposobem upewnienia się, że łącza działają tak samo zarówno w sieci firmowej, jak i poza nią, jest skonfigurowanie zewnętrznych adresów URL aplikacji tak, aby były takie same jak ich wewnętrzne adresy URL. Użyj [domen niestandardowych,](application-proxy-configure-custom-domain.md) aby skonfigurować zewnętrzne adresy URL, aby mieć nazwę domeny firmowej zamiast domyślnej domeny serwera proxy aplikacji.


Jeśli nie można używać domen niestandardowych w dzierżawie, istnieje kilka innych opcji dostarczania tej funkcji. Wszystkie te są również zgodne z domenami niestandardowymi i sobą nawzajem, dzięki czemu można skonfigurować domeny niestandardowe i inne rozwiązania w razie potrzeby.

> [!NOTE]
> Tłumaczenie linków nie jest obsługiwane dla zakodowanych wewnętrznych adresów URL generowanych za pomocą języka JavaScript.

**Opcja 1: Użyj zarządzanej przeglądarki lub przeglądarki Microsoft Edge** — to rozwiązanie ma zastosowanie tylko wtedy, gdy użytkownik zamierza zalecić lub zażądać, aby użytkownicy uzyskili dostęp do aplikacji za pośrednictwem zarządzanej przeglądarki usługi Intune lub przeglądarki Microsoft Edge. Będzie obsługiwać wszystkie opublikowane adresy URL. 

**Opcja 2: Użyj rozszerzenia MyApps** — to rozwiązanie wymaga od użytkowników zainstalowania rozszerzenia przeglądarki po stronie klienta, ale będzie obsługiwać wszystkie opublikowane adresy URL i współpracuje z najpopularniejszymi przeglądarkami. 

**Opcja 3: Użyj ustawienia tłumaczenia łącza** — jest to ustawienie po stronie administratora, które jest niewidoczne dla użytkowników. Jednak będzie obsługiwać tylko adresy URL w HTML i CSS.   

Te trzy funkcje utrzymują działanie linków bez względu na to, gdzie znajdują się użytkownicy. Jeśli masz aplikacje, które wskazują bezpośrednio na wewnętrzne punkty końcowe lub porty, możesz mapować te wewnętrzne adresy URL na opublikowane zewnętrzne adresy URL serwera proxy aplikacji. 

 
> [!NOTE]
> Ostatnia opcja jest dostępna tylko dla dzierżawców, którzy z jakiegokolwiek powodu nie mogą używać domen niestandardowych do posiadania tych samych wewnętrznych i zewnętrznych adresów URL dla swoich aplikacji. Przed włączeniem tej funkcji sprawdź, czy [domeny niestandardowe w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md) mogą działać. 
> 
> Lub jeśli aplikacją, którą musisz skonfigurować za pomocą tłumaczenia łączy, jest sharepoint, zobacz [Konfigurowanie mapowań dostępu alternatywnego dla programu SharePoint 2013,](https://technet.microsoft.com/library/cc263208.aspx) aby uzyskać inne podejście do łączy mapowania. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Opcja 1: Zarządzana przeglądarka usługi Intune i integracja z przeglądarką Microsoft Edge 

Za pomocą zarządzanej przeglądarki usługi Intune lub przeglądarki Microsoft Edge można dalej chronić aplikację i zawartość. Aby użyć tego rozwiązania, należy wymagać/zalecać użytkownikom dostęp do aplikacji za pośrednictwem zarządzanej przeglądarki usługi Intune. Wszystkie wewnętrzne adresy URL opublikowane za pomocą serwera proxy aplikacji zostaną rozpoznane przez zarządzaną przeglądarkę i przekierowane do odpowiedniego zewnętrznego adresu URL. Gwarantuje to, że wszystkie zakodowane wewnętrzne adresy URL działają, a jeśli użytkownik przejdzie do przeglądarki i bezpośrednio wpisuje wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby dowiedzieć się więcej, w tym jak skonfigurować tę opcję, zapoznaj się z dokumentacją [przeglądarki zarządzanej.](https://docs.microsoft.com/intune/app-configuration-managed-browser)  

### <a name="option-2-myapps-browser-extension"></a>Opcja 2: Rozszerzenie przeglądarki MyApps 

Dzięki rozszerzeniu przeglądarki MyApps wszystkie wewnętrzne adresy URL opublikowane za pomocą serwera proxy aplikacji są rozpoznawane przez rozszerzenie i przekierowywane do odpowiedniego zewnętrznego adresu URL. Dzięki temu wszystkie zakodowane wewnętrzne adresy URL działają, a jeśli użytkownik przejdzie do paska adresu przeglądarki i bezpośrednio wpisuje wewnętrzny adres URL, działa nawet wtedy, gdy użytkownik jest zdalny.  

Aby korzystać z tej funkcji, użytkownik musi pobrać rozszerzenie i być zalogowany. Nie ma innej konfiguracji potrzebnej administratorom ani użytkownikom. 

Aby dowiedzieć się więcej, w tym jak skonfigurować tę opcję, zapoznaj się z dokumentacją [rozszerzenia przeglądarki MyApps.](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension)

### <a name="option-3-link-translation-setting"></a>Opcja 3: Ustawienie tłumaczenia łączy 

Gdy tłumaczenie łączy jest włączone, usługa Proxy aplikacji przeszukuje kod HTML i CSS w poszukiwaniu opublikowanych łączy wewnętrznych i tłumaczy je, aby użytkownicy mogli uzyskać nieprzerwane działanie. Korzystanie z rozszerzenia przeglądarki MyApps jest preferowane do ustawienia tłumaczenia linków, ponieważ daje użytkownikom bardziej wydajne wrażenia.

> [!NOTE]
> Jeśli używasz opcji 2 lub 3, tylko jeden z nich powinien być włączony w czasie.

## <a name="how-link-translation-works"></a>Jak działa tłumaczenie linków

Po uwierzytelnieniu serwer proxy przekazuje dane aplikacji użytkownikowi, serwer proxy aplikacji skanuje aplikację w poszukiwaniu linków zakodowanych na stałe i zastępuje je odpowiednimi, opublikowanymi zewnętrznymi adresami URL.

Serwer proxy aplikacji zakłada, że aplikacje są zakodowane w UTF-8. Jeśli tak nie jest, określ typ kodowania w nagłówku odpowiedzi http, na przykład `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Których linków dotyczy problem?

Funkcja tłumaczenia łączy wyszukuje tylko łącza, które znajdują się w tagach kodu w treści aplikacji. Serwer proxy aplikacji ma osobną funkcję tłumaczenia plików cookie lub adresów URL w nagłówkach. 

Istnieją dwa typowe typy łączy wewnętrznych w aplikacjach lokalnych:

- **Względne łącza wewnętrzne** wskazujące zasób udostępniony `/claims/claims.html`w lokalnej strukturze plików, takie jak . Te łącza automatycznie działają w aplikacjach, które są publikowane za pośrednictwem serwera proxy aplikacji i nadal działają z tłumaczeniem łącza lub bez niego. 
- **Zakodowane łącza wewnętrzne** z kodami `http://expenses` twardymi do `http://expenses/logo.jpg`innych aplikacji lokalnych, takich jak lub opublikowane pliki, takie jak . Funkcja tłumaczenia łączy działa na twardych łączach wewnętrznych i zmienia je w taki sposób, aby wskazywały zewnętrzne adresy URL, przez które muszą przechodzić użytkownicy zdalni.

Pełna lista tagów kodu HTML, które serwer proxy aplikacji obsługuje tłumaczenie linków, obejmują:
* a
* dźwięk
* base
* button
* div
* Osadź
* Formularza
* ramowe
* Głowy
* html
* iframe
* img
* wejście
* połącz
* Menuitem
* Meta
* obiekt
* skrypt
* source
* śledź
* wideo

Ponadto w CSS atrybut URL jest również tłumaczony.

### <a name="how-do-apps-link-to-each-other"></a>W jaki sposób aplikacje łączą się ze sobą?

Tłumaczenie łącza jest włączone dla każdej aplikacji, dzięki czemu masz kontrolę nad środowiska użytkownika na poziomie poszczególnych aplikacji. Włącz tłumaczenie linków dla aplikacji, jeśli chcesz, aby łącza *z* tej aplikacji zostały przetłumaczone, a nie łącza *do* tej aplikacji. 

Załóżmy na przykład, że masz trzy aplikacje opublikowane za pośrednictwem serwera proxy aplikacji, które wszystkie łączą się ze sobą: korzyści, wydatki i podróże. Istnieje czwarta aplikacja, Opinie, która nie jest publikowana za pośrednictwem serwera proxy aplikacji.

Po włączeniu tłumaczenia łączy dla aplikacji Korzyści łącza do wydatków i podróży są przekierowywane do zewnętrznych adresów URL tych aplikacji, ale łącze do opinii nie jest przekierowywane, ponieważ nie ma zewnętrznego adresu URL. Linki z wydatków i podróży z powrotem do korzyści nie działają, ponieważ tłumaczenie linku nie zostało włączone dla tych dwóch aplikacji.

![Linki z korzyści do innych aplikacji, gdy tłumaczenie linku jest włączone](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Które linki nie są tłumaczone?

Aby zwiększyć wydajność i bezpieczeństwo, niektóre łącza nie są tłumaczone:

- Linki nie wewnątrz tagów kodu. 
- Linki nie w HTML lub CSS. 
- Linki w formacie zakodowanym w adresie URL.
- Linki wewnętrzne otwarte z innych programów. Linki wysyłane za pośrednictwem poczty e-mail lub wiadomości błyskawicznej lub zawarte w innych dokumentach nie będą tłumaczone. Użytkownicy muszą wiedzieć, aby przejść do zewnętrznego adresu URL.

Jeśli chcesz obsługiwać jeden z tych dwóch scenariuszy, użyj tych samych wewnętrznych i zewnętrznych adresów URL zamiast tłumaczenia łącza.  

## <a name="enable-link-translation"></a>Włączanie tłumaczenia łączy

Rozpoczęcie tłumaczenia linków jest tak proste, jak kliknięcie przycisku:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **Enterprise applications** > **Wszystkie aplikacje** > wybrać aplikację, którą chcesz zarządzać > **proxy aplikacji**.
3. **Obróć translate adresy URL w treści aplikacji** do **Tak**.

   ![Wybierz tak, aby przetłumaczyć adresy URL w treści aplikacji](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Aby zastosować zmiany, wybierz pozycję **Zapisz**.

Teraz, gdy użytkownicy uzyskują dostęp do tej aplikacji, serwer proxy automatycznie skanuje w poszukiwaniu wewnętrznych adresów URL, które zostały opublikowane za pośrednictwem serwera proxy aplikacji w dzierżawie.

## <a name="send-feedback"></a>Przesyłanie opinii

Chcemy, aby Twoja pomoc działała dla wszystkich Twoich aplikacji. Wyszukujemy ponad 30 tagów w HTML i CSS. Jeśli masz przykład wygenerowanych łączy, które nie są tłumaczone, wyślij fragment kodu do [opinii serwera proxy aplikacji](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Następne kroki
[Używanie domen niestandardowych z serwerem proxy aplikacji usługi Azure AD,](application-proxy-configure-custom-domain.md) aby mieć ten sam wewnętrzny i zewnętrzny adres URL

[Konfigurowanie mapowania dostępu alternatywnego dla programu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
