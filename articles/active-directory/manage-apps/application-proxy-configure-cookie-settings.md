---
title: Ustawienia plików cookie serwera Proxy aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Azure Active Directory (Azure AD) ma pliki cookie dostępu i sesji do uzyskiwania dostępu do lokalnych aplikacji za pośrednictwem serwera Proxy aplikacji. W tym artykule znajdziesz informacje dotyczące sposobu użycia, a następnie skonfiguruj ustawienia plików cookie.
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
ms.openlocfilehash: d2e7f1bb54ce316a10eca0d020519779b0536c9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825741"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Ustawienia plików cookie na potrzeby uzyskiwania dostępu do aplikacji lokalnych w usłudze Azure Active Directory

Azure Active Directory (Azure AD) ma pliki cookie dostępu i sesji do uzyskiwania dostępu do lokalnych aplikacji za pośrednictwem serwera Proxy aplikacji. Dowiedz się, jak używać ustawienia plików cookie serwera Proxy aplikacji. 

## <a name="what-are-the-cookie-settings"></a>Co to są ustawienia plików cookie?

[Serwer Proxy aplikacji](application-proxy.md) następujące ustawienia pliku cookie dostępu i sesji.

| Ustawienie pliku cookie | Domyślne | Opis | Zalecenia |
| -------------- | ------- | ----------- | --------------- |
| Używaj plików Cookie tylko HTTP | **Nie** | **Tak** umożliwia serwera Proxy aplikacji, które mają zostać objęte flagi HTTPOnly nagłówków odpowiedzi HTTP. Ta flaga zapewnia dodatkowe zabezpieczenia korzyści, na przykład zapobiega po stronie klienta skryptów (CSS) kopiowanie i modyfikowanie plików cookie.<br></br><br></br>Zanim obsługiwane ustawienie tylko do protokołu HTTP, serwer Proxy aplikacji zaszyfrowany i przekazywanych plików cookie za pośrednictwem bezpiecznego kanału SSL do ochrony przed zmianami. | Użyj **tak** ze względu na korzyści dodatkowe zabezpieczenia.<br></br><br></br>Użyj **nie** dla klientów lub agentów użytkownika, które wymagają dostępu do pliku cookie sesji. Na przykład użyć **nie** dla protokołu RDP lub MTSC klienta, który nawiązuje połączenie z serwerem bramy usług pulpitu zdalnego za pośrednictwem serwera Proxy aplikacji.|
| Use Secure Cookie | **Nie** | **Tak** umożliwia serwera Proxy aplikacji uwzględnić Zabezpiecz Flaga nagłówków odpowiedzi HTTP. Bezpiecznych plików cookie zwiększa bezpieczeństwo poprzez przekazanie plików cookie za pośrednictwem bezpiecznego kanału TLS, taki jak HTTPS. Zapobiega to pliki cookie z czym muszą być spełnione przez osoby nieupoważnione ze względu na przekazywanie pliku cookie w postaci zwykłego tekstu. | Użyj **tak** ze względu na korzyści dodatkowe zabezpieczenia.|
| Użyj trwały plik Cookie | **Nie** | **Tak** umożliwia serwera Proxy aplikacji do jej używania plików cookie dostępu nie wygaśnie po zamknięciu przeglądarki sieci web. Trwałość obowiązuje do czasu wygaśnięcia tokenu dostępu lub do momentu ręcznego usunięcia trwały plik cookie przez użytkownika. | Użyj **nie** ze względu na zagrożenie bezpieczeństwa związane z utrzymywaniem użytkowników uwierzytelnionych.<br></br><br></br>Zalecane jest używanie tylko **tak** dla starszych aplikacji, które nie mogą udostępniać pliki cookie między procesami. Zaleca się zaktualizować aplikację w celu obsługi udostępniania plików cookie między procesami, zamiast trwały plik cookie. Na przykład może być konieczne trwały plik cookie, aby umożliwić użytkownikowi otwieranie dokumentów pakietu Office w widoku Eksploratora z witryny programu SharePoint. Bez trwały plik cookie ta operacja może się niepowodzeniem, jeśli pliki cookie dostępu nie są współużytkowane przez przeglądarkę, proces Eksploratora i proces pakietu Office. |

## <a name="set-the-cookie-settings---azure-portal"></a>Ustaw ustawienia plików cookie — witryna Azure portal
Aby skonfigurować ustawienia plików cookie, przy użyciu witryny Azure portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Przejdź do **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **wszystkie aplikacje**.
3. Wybierz aplikację, dla której chcesz włączyć ustawienie pliku cookie.
4. Kliknij przycisk **serwera Proxy aplikacji**.
5. W obszarze **dodatkowe ustawienia**, ustawienia plików cookie **tak** lub **nie**.
6. Kliknij przycisk **Zapisz** Aby zastosować zmiany. 

## <a name="view-current-cookie-settings---powershell"></a>Wyświetl bieżące ustawienia plików cookie — PowerShell

Aby wyświetlić bieżące ustawienia plików cookie dla aplikacji, użyj tego polecenia programu PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Ustawienia plików cookie — PowerShell

W poniższych poleceniach programu PowerShell ```<ObjectId>``` jest identyfikator obiektu aplikacji. 

**Plik Cookie tylko http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Bezpieczny plik Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Trwałe pliki cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
