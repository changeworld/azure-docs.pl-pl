---
title: 'Usługa Azure AD Connect: bezproblemowe logowanie jednokrotne — często zadawane pytania | Dokumenty firmy Microsoft'
description: Odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego usługi Azure Active Directory.
services: active-directory
keywords: co to jest usługa Azure AD Connect, instalowanie usługi Active Directory, wymagane składniki dla usługi Azure AD, logowania jednokrotnego, logowania jednokrotnego
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7241c8dfbedb24f95c29ea9e1c3f763218a5668d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025679"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Bezproblemowe logowanie usługi Azure Active Directory: często zadawane pytania

W tym artykule zajmujemy się często zadawane pytania dotyczące usługi Azure Active Directory bezproblemowe logowanie jednokrotne (bezproblemowe logowania jednokrotnego). Sprawdzaj ponownie nową zawartość.

**P: Z jakimi metodami logowania działają bezproblemowe logowanie jednokrotne**

Bezproblemowe logowanie jednokrotne można łączyć z [metodami logowania hash hasła](how-to-connect-password-hash-synchronization.md) lub [uwierzytelniania przekazywania.](how-to-connect-pta.md) Tej funkcji nie można jednak używać z usługami federacyjnymi Active Directory (ADFS).

**P: Czy bezproblemowe logowanie jednokrotne jest bezpłatną funkcją?**

Bezproblemowe logowanie jednokrotne to bezpłatna funkcja i nie potrzebujesz żadnych płatnych wersji usługi Azure AD, aby z niej korzystać.

**P: Czy bezproblemowe logowanie jednokrotne jest dostępne w [chmurze Microsoft Azure Germany](https://www.microsoft.de/cloud-deutschland) i [chmurze Microsoft Azure dla instytucji rządowych?](https://azure.microsoft.com/features/gov/)**

Nie. Bezproblemowe logowanie jednokrotne jest dostępne tylko w ogólnoświatowym wystąpieniu usługi Azure AD.

**Pyt.: Jakie `domain_hint` `login_hint` aplikacje korzystają lub parametry możliwości seamless logowania jednokrotnego?**

Poniżej znajduje się niewyczerpywawa lista aplikacji, które mogą wysyłać te parametry do usługi Azure AD i dlatego zapewnia użytkownikom ciche środowisko logowania przy użyciu bezproblemowego logowania jednokrotnego (tj. nie ma potrzeby wprowadzania przez użytkowników ich nazw użytkowników lub haseł):

| Nazwa aplikacji | Adres URL aplikacji, który ma być używany |
| -- | -- |
| Panel dostępu | https:\//myapps.microsoft.com/contoso.com |
| Aplikacja Outlook w sieci Web | https:\//outlook.office365.com/contoso.com |
| Portale usługi Office 365 | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Ponadto użytkownicy uzyskać ciche środowisko logowania, jeśli aplikacja wysyła żądania logowania do punktów końcowych usługi Azure AD skonfigurowany jako dzierżawców — oznacza to, https:\//login.microsoftonline.com/contoso.com/<..> lub https:\//login.microsoftonline.com/<tenant_ID>/<.. > — zamiast wspólnego punktu końcowego usługi Azure AD — czyli\/https: /login.microsoftonline.com/common/<...>. Poniżej znajduje się niewyczerpywna lista aplikacji, które składają tego typu żądania logowania.

| Nazwa aplikacji | Adres URL aplikacji, który ma być używany |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Portal Azure | https:\//portal.azure.com/contoso.com |

W powyższych tabelach zastąp "contoso.com" nazwą domeny, aby uzyskać odpowiednie adresy URL aplikacji dla dzierżawy.

Jeśli chcesz, aby inne aplikacje korzystały z naszego cichego logowania, poinformuj nas o tym w sekcji opinii.

**P: Czy bezproblemowe `Alternate ID` logowanie jednokrotne `userPrincipalName`obsługuje jako nazwę użytkownika, a nie?**

Tak. Bezproblemowe logowanie `Alternate ID` jednokrotne obsługuje jako nazwę użytkownika po skonfigurowaniu w usłudze Azure AD Connect, jak pokazano [poniżej.](how-to-connect-install-custom.md) Nie wszystkie aplikacje usługi `Alternate ID`Office 365 obsługują . Zapoznaj się z dokumentacją konkretnej aplikacji dla instrukcji pomocy technicznej.

**Pyt.: Jaka jest różnica między logowaniem jednokrotnym zapewniane przez [przyłącze usługi Azure AD](../active-directory-azureadjoin-overview.md) i bezproblemowe logowanie jednokrotne?**

[Usługa Azure AD Join](../active-directory-azureadjoin-overview.md) udostępnia sytuowane dane SSO użytkownikom, jeśli ich urządzenia są zarejestrowane w usłudze Azure AD. Te urządzenia nie muszą być przyłączone do domeny. SSO jest dostarczany przy użyciu *podstawowych tokenów odświeżania* lub *PRT*, a nie Kerberos. Środowisko użytkownika jest najbardziej optymalne na urządzeniach z systemem Windows 10. SSO odbywa się automatycznie w przeglądarce Microsoft Edge. Działa również na Chrome z wykorzystaniem rozszerzenia przeglądarki.

W dzierżawie można używać zarówno usługi Azure AD Join, jak i bezproblemowego logowania jednokrotnego. Te dwie funkcje są komplementarne. Jeśli obie funkcje są włączone, a następnie logowanie jednokrotne z usługi Azure AD Join ma pierwszeństwo przed bezproblemowym logowaniem jednokrotnym.

**P: Chcę zarejestrować urządzenia spoza systemu Windows 10 w usłudze Azure AD bez korzystania z usług AD FS. Czy zamiast tego mogę używać bezszwowego logowania jednokrotnego?**

Tak, ten scenariusz wymaga wersji 2.1 lub nowszej [klienta dołączania do miejsca pracy.](https://www.microsoft.com/download/details.aspx?id=53554)

**Pyt.: Jak mogę przerzucić klucz odszyfrowywania protokołu Kerberos na koncie `AZUREADSSOACC` komputera?**

Ważne jest, aby często przewracać klucz `AZUREADSSOACC` odszyfrowywania protokołu Kerberos konta komputera (który reprezentuje usługę Azure AD) utworzonego w lokalnym lesie usługi AD.

>[!IMPORTANT]
>Zdecydowanie zaleca się przewracanie klucza odszyfrowywania protokołu Kerberos co najmniej co 30 dni.

Wykonaj następujące kroki na serwerze lokalnym, na którym jest uruchomiona usługa Azure AD Connect:

   **Krok 1. Pobierz listę lasów ad, w których włączono bezproblemowe logowanie jednokrotne**

   1. Najpierw pobierz i zainstaluj [program Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj moduł Seamless SSO `Import-Module .\AzureADSSO.psd1`PowerShell za pomocą tego polecenia: .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . To polecenie powinno dać wyskakujące okienko, aby wprowadzić poświadczenia administratora globalnego dzierżawy.
   5. Zadzwoń `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie zawiera listę lasów ad (spójrz na listę "Domeny"), na której ta funkcja została włączona.

   **Krok 2. Aktualizowanie klucza odszyfrowywania protokołu Kerberos w każdym lesie usługi AD, na który został skonfigurowany**

   1. Zadzwoń `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi AD.

   > [!NOTE]
   >Nazwa użytkownika poświadczeń administratora domeny musi być wprowadzona w formacie nazwy konta SAM (contoso\johndoe lub contoso.com\johndoe). Używamy części domeny nazwy użytkownika, aby zlokalizować kontroler domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Używane konto administratora domeny nie może być członkiem grupy Użytkownicy chronieni. Jeśli tak, operacja zakończy się niepowodzeniem.

   2. Zadzwoń `Update-AzureADSSOForest -OnPremCredentials $creds`. To polecenie aktualizuje klucz odszyfrowywania protokołu Kerberos dla konta `AZUREADSSOACC` komputera w tym określonym lesie usługi AD i aktualizuje go w usłudze Azure AD.
   3. Powtórz poprzednie kroki dla każdego lasu usługi AD, na którym została skonfigurowana funkcja.

   >[!IMPORTANT]
   >Upewnij się, że polecenie `Update-AzureADSSOForest` _nie_ jest uruchamiane więcej niż jeden raz. W przeciwnym razie funkcja przestanie działać do momentu wygaśnięcia biletów protokołu Kerberos użytkowników i ponownego wysuowania ich przez lokalną usługę Active Directory.

**P: Jak mogę wyłączyć bezproblemowe logowanie jednokrotne?**

   **Krok 1. Wyłączanie funkcji w dzierżawie**

   **Opcja A: Wyłączanie korzystania z usługi Azure AD Connect**
    
   1. Uruchom usługę Azure AD Connect, wybierz pozycję **Zmień stronę logowania użytkownika** i kliknij przycisk **Dalej**.
   2. Wyeznań włącz **opcję Włączanie logowania jednokrotnego.** Kontynuuj za pośrednictwem kreatora.

   Po zakończeniu pracy kreatora bezproblemowe logowanie jednokrotne zostanie wyłączone w dzierżawie. Na ekranie pojawi się jednak komunikat o następującej treści:

   "Logowanie jednokrotne jest teraz wyłączone, ale istnieją dodatkowe kroki ręczne do wykonania w celu zakończenia oczyszczania. Dowiedz się więcej"

   Aby zakończyć proces oczyszczania, wykonaj kroki 2 i 3 na serwerze lokalnym, na którym jest uruchomiona usługa Azure AD Connect.

   **Opcja B: Wyłącz korzystanie z programu PowerShell**

   Uruchom następujące kroki na serwerze lokalnym, na którym jest uruchomiona usługa Azure AD Connect:

   1. Najpierw pobierz i zainstaluj [program Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj moduł Seamless SSO `Import-Module .\AzureADSSO.psd1`PowerShell za pomocą tego polecenia: .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . To polecenie powinno dać wyskakujące okienko, aby wprowadzić poświadczenia administratora globalnego dzierżawy.
   5. Zadzwoń `Enable-AzureADSSO -Enable $false`.

   >[!IMPORTANT]
   >Wyłączenie bezproblemowego logowania jednokrotnego przy użyciu programu PowerShell nie zmieni stanu w usłudze Azure AD Connect. Bezproblemowe logowanie jednokrotne będzie wyświetlane zgodnie z włączoną funkcją na stronie **Zmień logowanie użytkownika.**

   **Krok 2. Pobierz listę lasów ad, w których włączono bezproblemowe logowanie jednokrotne**

   Wykonaj zadania od 1 do 4 poniżej, jeśli wyłączono bezproblemowe logowanie jednokrotne przy użyciu usługi Azure AD Connect. Jeśli wyłączono bezproblemowe logowanie jednokrotne przy użyciu programu PowerShell, przejdź do zadania 5 poniżej.

   1. Najpierw pobierz i zainstaluj [program Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj moduł Seamless SSO `Import-Module .\AzureADSSO.psd1`PowerShell za pomocą tego polecenia: .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . To polecenie powinno dać wyskakujące okienko, aby wprowadzić poświadczenia administratora globalnego dzierżawy.
   5. Zadzwoń `Get-AzureADSSOStatus | ConvertFrom-Json`. To polecenie zawiera listę lasów ad (spójrz na listę "Domeny"), na której ta funkcja została włączona.

   **Krok 3. Ręcznie usuń `AZUREADSSOACCT` konto komputera z każdego lasu usługi AD, który jest widoczny na liście.**

## <a name="next-steps"></a>Następne kroki

- [**Szybki start**](how-to-connect-sso-quick-start.md) — rozpoczynanie pracy i uruchamianie bezproblemowego logowania jednokrotnego usługi Azure AD.
- [**Głębokie nurkowanie techniczne**](how-to-connect-sso-how-it-works.md) — dowiedz się, jak działa ta funkcja.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — dowiedz się, jak rozwiązać typowe problemy z tą funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — do składania nowych żądań funkcji.
