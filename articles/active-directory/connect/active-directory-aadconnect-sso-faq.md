---
title: 'Azure AD Connect: Bezproblemowe logowanie jednokrotne — często zadawane pytania | Dokumentacja firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące Azure Active Directory bezproblemowe rejestracji jednokrotnej.
services: active-directory
keywords: Co to jest usługa Azure AD Connect, zainstaluj usługę Active Directory, wymaganych składników dla usługi Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: c34293796860e0ab72eecedab9cd8b023237050d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: często zadawane pytania

W tym artykule można rozwiązać często zadawane pytania o Azure Active Directory bezproblemowe rejestracji jednokrotnej (SSO bezproblemowe). Sprawdzanie wstecz dla nowej zawartości.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Jakie metody logowania SSO bezproblemowe działają z?

Bezproblemowe logowanie Jednokrotne można łączyć z jedną [synchronizacji skrótu hasła](active-directory-aadconnectsync-implement-password-synchronization.md) lub [uwierzytelniania przekazywanego](active-directory-aadconnect-pass-through-authentication.md) metody logowania. Jednak ta funkcja nie można używać z Active Directory Federation Services (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>Jest funkcją wolnego bezproblemowe logowanie Jednokrotne?

Bezproblemowe rejestracji Jednokrotnej jest funkcją wolnego i nie wymagają żadnych wersji płatnej usługi Azure AD z niego korzystać.

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Jest dostępna w bezproblemowe logowanie Jednokrotne [chmury Microsoft Azure Niemcy](http://www.microsoft.de/cloud-deutschland) i [chmury Microsoft Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)?

Nie. Bezproblemowe rejestracji Jednokrotnej jest dostępna tylko w wystąpieniu na całym świecie usługi Azure AD.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Jakie aplikacje korzystać z `domain_hint` lub `login_hint` parametru możliwości łatwego logowania jednokrotnego?

Wymienione poniżej jest niepełny listę aplikacji, które wysyłanie tych parametrów do usługi Azure AD i w związku z tym zapewnia użytkownikom dyskretnej środowisko logowania przy użyciu łatwego logowania jednokrotnego (tj. nie jest konieczne użytkownikom wprowadzanie ich nazw użytkowników):

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| Panel dostępu | myapps.microsoft.com/contoso.com |
| Program Outlook w sieci Web | outlook.office365.com/contoso.com |

Ponadto użytkownicy uzyskują dyskretnej obsługi logowania jednokrotnego, jeśli aplikacja wyśle żądań logowania do usługi Azure AD dzierżawcza punkty końcowe — oznacza to, https://login.microsoftonline.com/contoso.com/<..> lub https://login.microsoftonline.com/<tenant_ID>/<..> — zamiast usługi Azure AD wspólnego punktu końcowego — to znaczy https://login.microsoftonline.com/common/<...>. Wymienione poniżej jest niepełny listę aplikacji, które tych typów żądań logowania.

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| SharePoint Online | contoso.sharepoint.com |
| Azure Portal | portal.azure.com/contoso.com |

W tabelach powyżej Zastąp nazwę domeny, aby przejść do adresu URL prawego aplikacji dla dzierżawy "contoso.com".

Jeśli chcesz, aby inne aplikacje korzystające z naszych dyskretnej jednokrotnego, Daj nam znać w sekcji opinii.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Obsługuje rejestracji Jednokrotnej bezproblemowe `Alternate ID` jako nazwa użytkownika, a nie `userPrincipalName`?

Tak. Bezproblemowe logowanie Jednokrotne obsługuje `Alternate ID` jako nazwy użytkownika, gdy skonfigurowane w programie Azure AD Connect, jak pokazano [tutaj](active-directory-aadconnect-get-started-custom.md). Nie wszystkie aplikacje usługi Office 365 obsługują `Alternate ID`. Zajrzyj do dokumentacji aplikacji określonych w instrukcji obsługi.

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>Jaka jest różnica między jednym środowisko logowania dostępna przez [Azure AD Join](../active-directory-azureadjoin-overview.md) i bezproblemowego logowania jednokrotnego?

[Azure AD Join](../active-directory-azureadjoin-overview.md) zapewnia użytkownikom logowania jednokrotnego, jeśli ich urządzenia są zarejestrowane w usłudze Azure AD. Te urządzenia nie ma musi być przyłączony do domeny. Usługa rejestracji Jednokrotnej jest realizowane przy użyciu *tokenów odświeżania podstawowego* lub *PRTs*, a nie protokołu Kerberos. Środowisko użytkownika jest optymalny na urządzeniach z systemem Windows 10. Usługa rejestracji Jednokrotnej odbywa się automatycznie w przeglądarce Edge. Działa na Chrome przy użyciu rozszerzenia przeglądarki.

Azure AD Join i bezproblemowego logowania jednokrotnego służy w dzierżawie. Te dwie funkcje są uzupełniające. Jeśli włączone są obie funkcje, następnie rejestracji Jednokrotnej z usługi Azure AD Join mają pierwszeństwo przed bezproblemowe logowania jednokrotnego.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Chcę zarejestrować urządzenia z systemem innym niż Windows 10 z usługą Azure AD, bez korzystania z usług AD FS. Można używać logowania jednokrotnego bezproblemowe zamiast niego?

Tak, ten scenariusz wymaga wersji 2.1 lub nowszej [Dołącz do miejsca pracy klienta](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Jak można I Przerzuć klucz odszyfrowywania Kerberos `AZUREADSSOACC` konto komputera?

Ważne jest, aby często Przerzuć klucz odszyfrowywania Kerberos `AZUREADSSOACC` konta komputera, (co reprezentuje usługi Azure AD) utworzone w lokalnym lesie usługi Active Directory.

>[!IMPORTANT]
>Zdecydowanie zaleca się wdrażanie za pośrednictwem protokołu Kerberos klucz odszyfrowujący co najmniej 30 dni.

Wykonaj następujące kroki na serwerze lokalnym, na którym uruchomiony jest program Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Pobierz listę lasów usługi AD, w którym włączono bezproblemowe logowanie Jednokrotne

1. Najpierw należy pobrać i zainstalować [Microsoft Online Services Asystenta logowania](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Następnie Pobierz i zainstaluj [64-bitowy moduł usługi Azure Active Directory dla środowiska Windows PowerShell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).
3. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Zaimportuj moduł bezproblemowe PowerShell logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
5. Uruchom program PowerShell jako Administrator. W programie PowerShell, wywołaj `New-AzureADSSOAuthenticationContext`. To polecenie powinien zapewnić podręcznego o wprowadzenie poświadczeń administratora globalnego Twojej dzierżawy.
6. Wywołanie `Get-AzureADSSOStatus`. To polecenie zawiera listę lasów usługi AD (odszukaj na liście "Domeny"), na którym ta funkcja została włączona.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Krok 2. Zaktualizuj klucz odszyfrowujący protokołu Kerberos w każdym lesie usługi AD, skonfigurowanego go go na

1. Wywołanie `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla danego lasu usługi AD.
2. Wywołanie `Update-AzureADSSOForest -OnPremCredentials $creds`. To polecenie aktualizuje klucz odszyfrowujący protokołu Kerberos dla `AZUREADSSOACC` konto komputera, w tym określonym lesie usługi AD i aktualizuje go w usłudze Azure AD.
3. Powtórz te czynności dla każdego lasu usługi AD, która po skonfigurowaniu funkcji na.

>[!IMPORTANT]
>Upewnij się, że _nie_ Uruchom `Update-AzureADSSOForest` polecenia więcej niż raz. W przeciwnym razie funkcja przestaje działać do czasu biletów Kerberos użytkowników wygaśnie i są ponownie w lokalnej usługi Active Directory.

## <a name="how-can-i-disable-seamless-sso"></a>Jak wyłączyć bezproblemowe logowanie Jednokrotne

Bezproblemowe logowania jednokrotnego, można wyłączyć za pomocą usługi Azure AD Connect.

Uruchom usługi Azure AD Connect, wybierz polecenie "Zmień użytkownika strony logowania" i kliknij przycisk "Dalej". Następnie usuń zaznaczenie opcji "Włącz pojedynczy znak". Kontynuuj pracę z kreatorem. Po zakończeniu działania kreatora bezproblemowe rejestracji Jednokrotnej jest wyłączona w dzierżawie.

Jednakże zostanie wyświetlony komunikat na ekranie odczytujący w następujący sposób:

"Rejestracji jednokrotnej jest obecnie wyłączony, ale ma dodatkowych czynności ręcznie wykonać w celu ukończenia czyszczenia. Dowiedz się więcej"

Aby ukończyć proces, wykonaj następujące kroki ręcznego na serwerze lokalnym, w którym uruchomiony jest program Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Krok 1. Pobierz listę lasów usługi AD, w którym włączono bezproblemowe logowanie Jednokrotne

1. Najpierw należy pobrać i zainstalować [Microsoft Online Services Asystenta logowania](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Następnie Pobierz i zainstaluj [64-bitowy moduł usługi Azure Active Directory dla środowiska Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
4. Zaimportuj moduł bezproblemowe PowerShell logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.
5. Uruchom program PowerShell jako Administrator. W programie PowerShell, wywołaj `New-AzureADSSOAuthenticationContext`. To polecenie powinien zapewnić podręcznego o wprowadzenie poświadczeń administratora globalnego Twojej dzierżawy.
6. Wywołanie `Get-AzureADSSOStatus`. To polecenie zawiera listę lasów usługi AD (odszukaj na liście "Domeny"), na którym ta funkcja została włączona.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Krok 2. Ręcznie usuń `AZUREADSSOACCT` konta komputera w każdym lesie usługi AD, który zostanie wyświetlony na liście.

## <a name="next-steps"></a>Kolejne kroki

- [**Szybki Start** ](active-directory-aadconnect-sso-quick-start.md) — Uzyskaj i systemem Azure AD bezproblemowe Usługa rejestracji Jednokrotnej.
- [**Nowości techniczne** ](active-directory-aadconnect-sso-how-it-works.md) -zrozumieć sposób działania tej funkcji.
- [**Rozwiązywanie problemów z** ](active-directory-aadconnect-troubleshoot-sso.md) — Dowiedz się, jak rozwiązać typowe problemy z funkcją.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłoszenia żądania nowych funkcji.
