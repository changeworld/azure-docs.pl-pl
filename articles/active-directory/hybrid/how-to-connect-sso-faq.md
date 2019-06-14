---
title: 'Program Azure AD Connect: Bezproblemowe logowanie jednokrotne — często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania o usługi Azure Active Directory bezproblemowego logowania jednokrotnego.
services: active-directory
keywords: Co to jest program Azure AD Connect, instalowanie usługi Active Directory, wymaganych składników dla usługi Azure AD, logowania jednokrotnego, logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 709fb3be37850be37d6378652921ce26f4ff15fe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60242230"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Usługa Azure Active Directory bezproblemowego logowania jednokrotnego: Często zadawane pytania

W tym artykule zajmujemy się często zadawane pytania dotyczące usługi Azure Active Directory bezproblemowe logowanie jednokrotne (bezproblemowe logowanie Jednokrotne). Zachowaj sprawdza, czy ponownie nowej zawartości.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Jakie metody logowania bezproblemowe logowanie Jednokrotne działają z?

Bezproblemowe logowanie Jednokrotne może być łączone z albo [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) lub [uwierzytelniania przekazywanego](how-to-connect-pta.md) metod logowania. Jednak nie można użyć tej funkcji za pomocą Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Jest bezproblemowe logowanie Jednokrotne bezpłatną funkcją?

Bezproblemowe logowanie Jednokrotne jest bezpłatną funkcją, i nie wymagają żadnych płatnej wersji usługi Azure AD z niego korzystać.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpswwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Jest dostępna w bezproblemowe logowanie Jednokrotne [chmury Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) i [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)?

Nie. Bezproblemowe logowanie Jednokrotne jest dostępna tylko w na całym świecie wystąpienia usługi Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Jakie aplikacje z zalet `domain_hint` lub `login_hint` parametru możliwości bezproblemowego logowania jednokrotnego?

Poniżej zamieszczono niepełna lista aplikacji, które można wysyłać te parametry do usługi Azure AD i dlatego zapewnia użytkownikom dyskretnej środowisko logowania jednokrotnego przy użyciu bezproblemowego logowania jednokrotnego (tj. nie ma potrzeby dla użytkowników do wprowadzania swoich nazw użytkowników i hasła):

| Nazwa aplikacji | Adres URL aplikacji ma być używany |
| -- | -- |
| Panel dostępu | https:\//myapps.microsoft.com/contoso.com |
| Program Outlook w sieci Web | https:\//outlook.office365.com/contoso.com |
| Portale usługi Office 365 | protokół https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Ponadto użytkownicy uzyskują dyskretnej środowisko logowania jednokrotnego, jeśli aplikacja wysyła żądania rejestrowania do punktów końcowych usługi Azure AD skonfigurowany jako dzierżawy — czyli https:\//login.microsoftonline.com/contoso.com/ <... > lub https:\//login.microsoftonline.com/ < tenant_ID > / <... > — zamiast usługi Azure AD wspólnej — oznacza to, że protokół https punktu końcowego:\//login.microsoftonline.com/common/ <... >. Poniżej zamieszczono niepełna lista aplikacji, które tego rodzaju żądań logowania.

| Nazwa aplikacji | Adres URL aplikacji ma być używany |
| -- | -- |
| SharePoint Online | protokół https:\//contoso.sharepoint.com |
| Azure Portal | protokół https:\//portal.azure.com/contoso.com |

W tabelach powyżej Zastąp "ciąg contoso.com" nazwy domeny, aby przejść do adresu URL aplikacji odpowiednie dla Twojej dzierżawy.

Jeśli chcesz, aby inne aplikacje korzystające z naszych dyskretnej logowania jednokrotnego, Daj nam znać, w sekcji opinii.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Obsługuje bezproblemowe logowanie Jednokrotne `Alternate ID` jako nazwa użytkownika, a nie `userPrincipalName`?

Tak. Bezproblemowe logowanie Jednokrotne obsługuje `Alternate ID` jako nazwa użytkownika w przypadku skonfigurowania w usłudze Azure AD Connect, jak pokazano [tutaj](how-to-connect-install-custom.md). Nie wszystkie aplikacje usługi Office 365 obsługują `Alternate ID`. Zajrzyj do dokumentacji określonej aplikacji dla instrukcji obsługi.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Różnica między logowania jednokrotnego dostarczanych przez [Azure AD Join](../active-directory-azureadjoin-overview.md) i bezproblemowe logowanie Jednokrotne?

[Funkcja Azure AD Join](../active-directory-azureadjoin-overview.md) zawiera logowania jednokrotnego dla użytkowników, jeśli ich urządzenia są zarejestrowane w usłudze Azure AD. Te urządzenia nie zawsze muszą być przyłączone do domeny. Usługa rejestracji Jednokrotnej jest realizowane przy użyciu *tokenów odświeżania podstawowego* lub *PRTs*, a nie protokołu Kerberos. Środowisko użytkownika jest optymalnie na urządzeniach z systemem Windows 10. Usługa rejestracji Jednokrotnej odbywa się automatycznie w przeglądarce Microsoft Edge. Działa w przeglądarce Chrome przy użyciu rozszerzenia przeglądarki.

Można użyć usługi Azure AD Join i bezproblemowe logowanie Jednokrotne w dzierżawie. Te dwie funkcje są wzajemnie dopełniającego się. Jeśli obie funkcje zostaną włączone, następnie logowania jednokrotnego z usługi Azure AD Join mają pierwszeństwo przed bezproblemowe logowanie Jednokrotne.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Chcę zarejestrować urządzenia do systemu Windows 10 za pomocą usługi Azure AD, bez korzystania z usług AD FS. Czy mogę użyć bezproblemowe logowanie Jednokrotne?

Tak, ten scenariusz wymaga wersji 2.1 lub nowszej [dołączenie do miejsca pracy klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Jak je czy przerzucić klucz odszyfrowywania protokołu Kerberos `AZUREADSSOACC` konto komputera?

Ważne jest, aby często przerzucić klucz odszyfrowywania protokołu Kerberos `AZUREADSSOACC` konto komputera, (który reprezentuje usługę Azure AD) utworzone w lokalnym lasem usługi AD.

>[!IMPORTANT]
>Zdecydowanie zalecamy przedłużenie klucz odszyfrowywania protokołu Kerberos co najmniej co 30 dni.

Wykonaj następujące czynności na serwerze lokalnym, w którym uruchomiony jest program Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Pobierz listę lasów usługi AD, w którym włączono bezproblemowe logowanie Jednokrotne

1. Najpierw należy pobrać i zainstalować [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł bezproblemowego logowania jednokrotnego programu PowerShell, za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako Administrator. W programie PowerShell, należy wywołać `New-AzureADSSOAuthenticationContext`. To polecenie powinien zapewnić okno podręczne o podanie poświadczeń administratora globalnego dzierżawy.
5. Wywołaj `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie zawiera listę lasów usługi AD (odszukaj pozycję na liście "Domeny"), w którym ta funkcja została włączona.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Krok 2. Zaktualizuj klucz odszyfrowywania protokołu Kerberos w każdym lesie usługi AD, skonfigurowanej go go na

1. Wywołaj `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny do zamierzonego lasu usługi AD.

   > [!NOTE]
   > Używamy nazwy użytkownika administratora domeny, podany w użytkownika głównej nazwy (UPN) (johndoe@contoso.com) format lub kwalifikowana konta sam formacie nazwy domeny (contoso\johndoe lub contoso.com\johndoe), można znaleźć zamierzony lasu usługi AD. Jeśli używasz nazwy kwalifikowanej konta sam domeny, używamy część domeny nazwa użytkownika do [zlokalizować kontrolera domeny z administratora domeny przy użyciu systemu DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Jeśli zamiast tego należy użyć nazwy UPN możemy [przekształca ją do nazwy kwalifikowanej konta sam domeny](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) przed lokalizowanie odpowiedniego kontrolera domeny.

2. Wywołaj `Update-AzureADSSOForest -OnPremCredentials $creds`. To polecenie aktualizuje klucz odszyfrowywania protokołu Kerberos dla `AZUREADSSOACC` konto komputera, w tym określonym lesie usługi AD i aktualizuje go w usłudze Azure AD.
3. Powtórz poprzednie kroki dla każdego lasu usługi AD, który po skonfigurowaniu tej funkcji na.

>[!IMPORTANT]
>Upewnij się, że _nie_ Uruchom `Update-AzureADSSOForest` polecenia więcej niż jeden raz. W przeciwnym razie funkcja przestaje działać do czasu, bilety protokołu Kerberos użytkownika wygasa i są ponownie w usłudze Active Directory w środowisku lokalnym.

## <a name="how-can-i-disable-seamless-sso"></a>Jak wyłączyć bezproblemowe logowanie Jednokrotne

### <a name="step-1-disable-the-feature-on-your-tenant"></a>Krok 1. Wyłącz funkcję w dzierżawie

#### <a name="option-a-disable-using-azure-ad-connect"></a>Option A: Wyłączyć za pomocą usługi Azure AD Connect

1. Uruchom program Azure AD Connect, wybierz polecenie **strony logowania użytkownika Zmień** i kliknij przycisk **dalej**.
2. Usuń zaznaczenie pola wyboru **Włącz logowanie jednokrotne** opcji. Kontynuuj pracę z kreatorem.

Po ukończeniu kreatora, bezproblemowe logowanie Jednokrotne zostanie wyłączona w dzierżawie. Jednakże zostanie wyświetlony komunikat na ekranie, który odczytuje w następujący sposób:

"Logowanie jednokrotne jest obecnie wyłączona, ale istnieją dodatkowe kroki ręczne do wykonania w celu ukończenia czyszczenia. Dowiedz się więcej"

Aby ukończyć proces czyszczenia, wykonaj kroki 2 i 3 na serwerze w środowisku lokalnym, w którym uruchomiony jest program Azure AD Connect.

#### <a name="option-b-disable-using-powershell"></a>Opcja B: Wyłącz przy użyciu programu PowerShell

Wykonaj następujące kroki na serwerze lokalnym, na którym uruchomiony jest program Azure AD Connect:

1. Najpierw należy pobrać i zainstalować [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł bezproblemowego logowania jednokrotnego programu PowerShell, za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako Administrator. W programie PowerShell, należy wywołać `New-AzureADSSOAuthenticationContext`. To polecenie powinien zapewnić okno podręczne o podanie poświadczeń administratora globalnego dzierżawy.
5. Wywołaj `Enable-AzureADSSO -Enable $false`.

>[!IMPORTANT]
>Wyłączanie bezproblemowego logowania jednokrotnego przy użyciu programu PowerShell nie zmieni stan w programie Azure AD Connect. Bezproblemowe logowanie Jednokrotne będzie widoczna jako włączona w **zmiana użytkownika logowania** strony.

### <a name="step-2-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 2. Pobierz listę lasów usługi AD, w którym włączono bezproblemowe logowanie Jednokrotne

Wyłączenie bezproblemowe logowanie Jednokrotne za pomocą usługi Azure AD Connect, należy wykonać zadania od 1 do 4 opisane poniżej. Wyłączenie bezproblemowe logowanie Jednokrotne przy użyciu programu PowerShell, zamiast przejść od razu do zadanie 5 poniżej.

1. Najpierw należy pobrać i zainstalować [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł bezproblemowego logowania jednokrotnego programu PowerShell, za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
4. Uruchom program PowerShell jako Administrator. W programie PowerShell, należy wywołać `New-AzureADSSOAuthenticationContext`. To polecenie powinien zapewnić okno podręczne o podanie poświadczeń administratora globalnego dzierżawy.
5. Wywołaj `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie zawiera listę lasów usługi AD (odszukaj pozycję na liście "Domeny"), w którym ta funkcja została włączona.

### <a name="step-3-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Krok 3. Ręcznie usuń `AZUREADSSOACCT` konta komputera w każdym lesie usługi AD, który zostanie wyświetlony na liście.

## <a name="next-steps"></a>Kolejne kroki

- [**Przewodnik Szybki Start** ](how-to-connect-sso-quick-start.md) — Rozpocznij pracę, a systemem Azure bezproblemowe logowanie Jednokrotne usługi AD.
- [**Rozbudowana technicznie** ](how-to-connect-sso-how-it-works.md) -zrozumienie sposobu działania tej funkcji.
- [**Rozwiązywanie problemów z** ](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania sugestie dotyczące nowych funkcji.
