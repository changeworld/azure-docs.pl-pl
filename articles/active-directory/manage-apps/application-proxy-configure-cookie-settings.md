---
title: Ustawienia plików cookie serwera proxy aplikacji — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Usługa Azure Active Directory (Azure AD) ma dostęp i sesyjne pliki cookie do uzyskiwania dostępu do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji. Z tego artykułu dowiesz się, jak używać i konfigurować ustawienia plików cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481368"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Ustawienia plików cookie dotyczące uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) ma dostęp i sesyjne pliki cookie do uzyskiwania dostępu do aplikacji lokalnych za pośrednictwem serwera proxy aplikacji. Dowiedz się, jak korzystać z ustawień plików cookie serwera proxy aplikacji. 

## <a name="what-are-the-cookie-settings"></a>Jakie są ustawienia plików cookie?

[Serwer proxy aplikacji](application-proxy.md) używa następujących ustawień dostępu i plików cookie sesji.

| Ustawienie pliku cookie | Domyślne | Opis | Zalecenia |
| -------------- | ------- | ----------- | --------------- |
| Użyj pliku cookie tylko HTTP | **Nie** | **Tak** umożliwia serwerowi proxy aplikacji dołączanie flagi HTTPOnly w nagłówkach odpowiedzi HTTP. Ta flaga zapewnia dodatkowe korzyści w zakresie zabezpieczeń, na przykład uniemożliwia kopiowanie lub modyfikowanie plików cookie skryptów po stronie klienta (CSS).<br></br><br></br>Zanim obsługujemy ustawienie tylko http, serwer proxy aplikacji szyfrował i przesyłał pliki cookie za pośrednictwem zabezpieczonego kanału TLS w celu ochrony przed modyfikacją. | Użyj **tak** ze względu na dodatkowe korzyści związane z zabezpieczeniami.<br></br><br></br>Użyj **nie** dla klientów lub agentów użytkowników, którzy wymagają dostępu do pliku cookie sesji. Na przykład użyj **nie** dla klienta RDP lub MTSC, który łączy się z serwerem bramy usług pulpitu zdalnego za pośrednictwem serwera proxy aplikacji.|
| Użyj bezpiecznego pliku cookie | **Nie** | **Tak** umożliwia serwerowi proxy aplikacji dołączanie flagi Secure w nagłówkach odpowiedzi HTTP. Bezpieczne pliki cookie zwiększają bezpieczeństwo, przesyłając pliki cookie za pośrednictwem zabezpieczonego kanału TLS, takiego jak HTTPS. Zapobiega to obserwowaniu plików cookie przez osoby nieupoważnione z powodu transmisji pliku cookie w postaci zwykłego tekstu. | Użyj **tak** ze względu na dodatkowe korzyści związane z zabezpieczeniami.|
| Używaj trwałego pliku cookie | **Nie** | **Tak** pozwala Serwerowi Proxy aplikacji ustawić pliki cookie dostępu, aby nie wygasały po zamknięciu przeglądarki internetowej. Trwałość trwa do wygaśnięcia tokenu dostępu lub do czasu, gdy użytkownik ręcznie usunie trwałe pliki cookie. | Użyj **nie** ze względu na ryzyko bezpieczeństwa związane z utrzymaniem uwierzytelnienia użytkowników.<br></br><br></br>Sugerujemy użycie tylko **tak** dla starszych aplikacji, które nie mogą udostępniać plików cookie między procesami. Lepiej zaktualizować aplikację, aby obsługiwać udostępnianie plików cookie między procesami zamiast używać trwałych plików cookie. Na przykład może być potrzebne trwałe pliki cookie, aby umożliwić użytkownikowi otwieranie dokumentów pakietu Office w widoku eksploratora z witryny programu SharePoint. Bez trwałych plików cookie ta operacja może zakończyć się niepowodzeniem, jeśli pliki cookie dostępu nie są udostępniane między przeglądarką, procesem eksploratora i procesem pakietu Office. |

## <a name="samesite-cookies"></a>Pliki cookie samesite
Począwszy od wersji Chrome 80, a ostatecznie w przeglądarkach wykorzystujących Chromium, pliki cookie, które nie określają atrybutu [SameSite,](https://web.dev/samesite-cookies-explained) będą traktowane tak, jakby były ustawione na **SameSite=Lax**. Atrybut SameSite deklaruje, w jaki sposób pliki cookie powinny być ograniczone do kontekstu tej samej witryny. Po ustawieniu na Lax, plik cookie jest tylko do wysyłania do tej samej witryny żądań lub nawigacji najwyższego poziomu. Jednak serwer proxy aplikacji wymaga, aby te pliki cookie były zachowywane w kontekście strony trzeciej, aby użytkownicy byli prawidłowo zalogowali się podczas sesji. W związku z tym wprowadzamy aktualizacje dostępu serwera proxy aplikacji i sesyjnych plików cookie, aby uniknąć negatywnego wpływu tej zmiany. Aktualizacje obejmują:

* Ustawianie atrybutu **SameSite** na **Brak**. Dzięki temu dostęp do serwera proxy aplikacji i pliki cookie sesji mają być poprawnie wysyłane w kontekście strony trzeciej.
* Ustawienie **opcji Użyj bezpiecznego pliku cookie** na domyślne użycie opcji **Tak.** Chrome wymaga również plików cookie, aby określić flagę Secure lub zostanie ona odrzucona. Ta zmiana będzie miała zastosowanie do wszystkich istniejących aplikacji opublikowanych za pośrednictwem serwera proxy aplikacji. Należy pamiętać, że pliki cookie dostępu serwera proxy aplikacji zawsze były ustawione na Bezpieczne i przesyłane tylko za pośrednictwem protokołu HTTPS. Ta zmiana będzie miała zastosowanie tylko do plików cookie sesji.

Te zmiany w plikach cookie serwera proxy aplikacji zostaną wprowadzone w ciągu najbliższych kilku tygodni przed datą wydania Chrome 80.

Ponadto jeśli aplikacja zaplecza ma pliki cookie, które muszą być dostępne w kontekście strony trzeciej, należy wyraźnie wyrazić zgodę, zmieniając aplikację, aby używać SameSite=None dla tych plików cookie. Serwer proxy aplikacji tłumaczy nagłówek Set-Cookie na jego adresy URL i będzie respektować ustawienia tych plików cookie ustawionych przez aplikację zaplecza.



## <a name="set-the-cookie-settings---azure-portal"></a>Ustawianie ustawień plików cookie — Witryna Azure portal
Aby ustawić ustawienia plików cookie za pomocą witryny Azure portal:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). 
2. Przejdź do **aplikacji** > **Azure Active Directory** >Enterprise Wszystkie **aplikacje**.
3. Wybierz aplikację, dla której chcesz włączyć ustawienie pliku cookie.
4. Kliknij **pozycję Pełnomocnik aplikacji**.
5. W obszarze **Ustawienia dodatkowe**ustaw ustawienie pliku cookie na **Tak** lub **Nie**.
6. Aby zastosować zmiany, kliknij pozycję **Zapisz**. 

## <a name="view-current-cookie-settings---powershell"></a>Wyświetlanie bieżących ustawień plików cookie — PowerShell

Aby wyświetlić bieżące ustawienia plików cookie dla aplikacji, użyj tego polecenia programu PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Ustawianie ustawień plików cookie — PowerShell

W następujących poleceniach programu ```<ObjectId>``` PowerShell jest ObjectId aplikacji. 

**Plik cookie tylko do protokołu http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Bezpieczny plik cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Trwałe pliki cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
