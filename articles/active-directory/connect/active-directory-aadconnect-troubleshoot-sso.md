---
title: 'Azure Active Directory Connect: Rozwiązywanie problemów z bezproblemowe logowanie jednokrotne | Dokumentacja firmy Microsoft'
description: W tym temacie opisano sposoby rozwiązywania problemów z usługi Azure Active Directory bezproblemowe logowanie jednokrotne
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
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: b383a081141d2fde90cfc574ec4b9ffb16940158
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Rozwiązywanie problemów z usługi Azure Active Directory bezproblemowe logowanie jednokrotne

Ten artykuł pomaga informacje o typowych problemów dotyczących usługi Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne (SSO bezproblemowe).

## <a name="known-issues"></a>Znane problemy

- W niektórych przypadkach włączenie logowania jednokrotnego bezproblemowe może potrwać do 30 minut.
- Jeśli Wyłącz i ponownie włącz bezproblemowe logowanie Jednokrotne dzierżawy, użytkownicy nie otrzymywali pojedynczego środowisko logowania, dopóki wygasły pamięci podręcznej bilet protokołu Kerberos, zwykle dotyczy 10 godzin.
- Obsługa przeglądarki Microsoft Edge nie jest dostępna.
- Jeśli bezproblemowe logowanie Jednokrotne zakończy się powodzeniem, użytkownik nie ma możliwość wybierz **wylogowuj mnie**. Ze względu na to zachowanie scenariuszy mapowania programu SharePoint i usługi OneDrive nie działają.
- Klienci Office poniżej 16.0.8730.xxxx wersji nie obsługują nieinterakcyjnym logowania z bezproblemowe logowania jednokrotnego. Na tych klientach użytkownicy muszą wprowadzić ich nazw użytkowników, ale nie hasła do logowania.
- Bezproblemowe logowanie Jednokrotne nie działa w trybie przeglądania prywatnym w programie Firefox.
- Bezproblemowe logowanie Jednokrotne nie działa w programie Internet Explorer, gdy jest włączony tryb rozszerzony chronione.
- Bezproblemowe logowanie Jednokrotne nie działa w przeglądarkach dla urządzeń przenośnych w systemach iOS i Android.
- Jeśli użytkownik wchodzi w skład zbyt wielu grup w usłudze Active Directory, biletu Kerberos użytkownika będzie prawdopodobnie zbyt duży, aby przetworzyć i spowoduje bezproblemowe logowanie Jednokrotne zakończyć się niepowodzeniem. Azure żądań AD HTTPS może mieć nagłówków o maksymalnym rozmiarze 16 KB; Bilety Kerberos muszą być znacznie mniejszy niż ten numer, aby zmieścił się w pozostałych artefaktów usługi Azure AD, takich jak pliki cookie. Nasze zalecenie jest Zmniejsz członkostwa grupy użytkownika i spróbuj ponownie.
- Jeśli synchronizacja 30 lub większą liczbą lasów usługi Active Directory, nie można włączyć bezproblemowe logowania jednokrotnego za pośrednictwem usługi Azure AD Connect. Jako rozwiązanie alternatywne można [ręcznie włączyć](#manual-reset-of-azure-ad-seamless-sso) tę funkcję na dzierżawy.
- Dodawanie adresu URL usługi Azure AD (https://autologon.microsoftazuread-sso.com) do strefy Zaufane witryny zamiast lokalnej strefy intranetowej *uniemożliwia użytkownikom logowanie*.
- Wyłączanie korzystania z **RC4_HMAC_MD5** typ szyfrowania dla protokołu Kerberos w ustawieniach usługi Active Directory spowoduje przerwanie bezproblemowe logowania jednokrotnego. W narzędziu do Edytora zarządzania zasadami grupy upewnij się, że wartość **RC4_HMAC_MD5** w obszarze **Konfiguracja komputera -> Ustawienia systemu Windows -> Ustawienia zabezpieczeń -> Zasady lokalne -> Opcje zabezpieczeń — > "Zabezpieczenia sieci: Konfigurowanie typów szyfrowania dozwolone dla protokołu Kerberos"** jest "Enabled".

## <a name="check-status-of-feature"></a>Sprawdź stan funkcji

Sprawdź, czy funkcja logowania jednokrotnego bezproblemowe jest nadal **włączone** w dzierżawie. Stan można sprawdzić, przechodząc do **Azure AD Connect** okienka w [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory: okienko Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

Kliknij, aby wyświetlić wszystkich lasów usługi AD, które zostały włączone dla bezproblemowego logowania jednokrotnego.

![Centrum administracyjne usługi Azure Active Directory: okienko bezproblemowe logowanie Jednokrotne](./media/active-directory-aadconnect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Przyczyny niepowodzenia logowania w Centrum administracyjnym usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawy ma licencji usługi Azure AD Premium skojarzonych z nim, można też przyjrzeć się [raport aktywności logowania](../active-directory-reporting-activity-sign-ins.md) w [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory: raport logowania](./media/active-directory-aadconnect-sso/sso9.png)

Przejdź do **usługi Azure Active Directory** > **logowania** w [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com/), a następnie wybierz działanie podczas logowania określonego użytkownika. Wyszukaj **kod błędu logowania w** pola. Mapowanie wartość tego pola na przyczyny błędu i rozwiązanie przy użyciu poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 81001 | Bilet Kerberos użytkownika jest zbyt duży. | Zmniejsz członkostwa w grupach użytkownika i spróbuj ponownie.
| 81002 | Nie można sprawdzić poprawności biletów Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81003 | Nie można sprawdzić poprawności biletów Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81004 | Próba uwierzytelniania Kerberos nie powiodła się. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81008 | Nie można sprawdzić poprawności biletów Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81009 | Nie można sprawdzić poprawności biletów Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81010 | Bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ ważność biletu Kerberos użytkownika wygasła lub bilet jest nieprawidłowy. | Użytkownik musi zalogować się przy użyciu urządzenia przyłączone do domeny w sieci firmowej.
| 81011 | Nie można odnaleźć obiektu użytkownika, na podstawie informacji w biletu Kerberos użytkownika. | Azure AD Connect umożliwia synchronizację informacji o użytkowniku w usłudze Azure Active Directory.
| 81012 | Użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika, który jest zalogowany do urządzenia. | Użytkownik musi się zalogować z innego urządzenia.
| 81013 | Nie można odnaleźć obiektu użytkownika, na podstawie informacji w biletu Kerberos użytkownika. |Azure AD Connect umożliwia synchronizację informacji o użytkowniku w usłudze Azure Active Directory. 

## <a name="troubleshooting-checklist"></a>Rozwiązywanie problemów z listy kontrolnej

Poniższa lista kontrolna umożliwia rozwiązywanie problemów bezproblemowe rejestracji Jednokrotnej:

- Upewnij się, że funkcja bezproblemowe logowanie Jednokrotne jest włączona w programie Azure AD Connect. Jeśli nie można włączyć funkcję (na przykład z powodu zablokowanych port), upewnij się, że masz wszystkie [wymagania wstępne](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) w miejscu.
- Jeśli włączono zarówno [Azure AD Join](../active-directory-azureadjoin-overview.md) i bezproblemowe logowanie Jednokrotne w dzierżawie, upewnij się, że problem nie jest z usługi Azure AD Join. Usługa rejestracji Jednokrotnej z usługi Azure AD Join mają pierwszeństwo przed bezproblemowe logowanie Jednokrotne Jeśli urządzenie jest zarejestrowane w usłudze Azure AD i przyłączonych do domeny. Z logowania jednokrotnego z usługi Azure AD Join użytkownik widzi kafelka logowania, stwierdzający "Podłączone do systemu Windows".
- Upewnij się, że adres URL usługi Azure AD (https://autologon.microsoftazuread-sso.com) jest częścią ustawień strefy Intranet.
- Upewnij się, że urządzeń firmowych jest przyłączony do domeny usługi Active Directory.
- Upewnij się, że użytkownik jest zalogowany na urządzeniu w ramach konta domeny usługi Active Directory.
- Upewnij się, że konto użytkownika z lasu usługi Active Directory, gdzie zostały bezproblemowe logowanie Jednokrotne skonfigurowano.
- Upewnij się, że urządzenie jest połączone z siecią firmową.
- Upewnij się, że godzina na urządzeniu jest zsynchronizowany z czasem w usłudze Active Directory i kontrolery domeny i że są one w ciągu pięciu minut od siebie.
- Listy istniejące bilety Kerberos na urządzeniu za pomocą `klist` polecenia z wiersza polecenia. Upewnij się, że bilety wystawione dla `AZUREADSSOACCT` istnieją konta komputera. Bilety Kerberos użytkowników są zazwyczaj ważne 10 godzin. Konieczne może być różne ustawienia w usłudze Active Directory.
- Jeśli wyłączona, a następnie ponownie włączona bezproblemowe logowanie Jednokrotne w swojej dzierżawy, użytkownicy nie otrzymywali pojedynczego środowisko logowania, dopóki ich pamięci podręcznej biletów Kerberos wygasły.
- Wyczyść istniejące bilety Kerberos z urządzenia przy użyciu `klist purge` polecenia, a następnie spróbuj ponownie.
- Aby ustalić, czy istnieją problemy związane ze JavaScript, przejrzyj dzienniki konsoli przeglądarki (w obszarze **Developer Tools**).
- Przegląd [dzienniki kontrolera domeny](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Po włączeniu inspekcji sukcesów na kontrolerze domeny, a następnie za każdym razem, gdy użytkownik loguje się przy użyciu łatwego logowania jednokrotnego, wpis zabezpieczeń są rejestrowane w dzienniku zdarzeń. Te zdarzenia zabezpieczeń można znaleźć przy użyciu następującej kwerendy. (Znajdź zdarzenie **4769** skojarzone z kontem komputera **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Resetowanie ręczne funkcji

Jeśli rozwiązywania problemów nie pomogły, można ręcznie zresetować tę funkcję w dzierżawie. Wykonaj następujące kroki na serwerze lokalnym, w którym pracujesz Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>: Krok 1 moduł bezproblemowe PowerShell logowania jednokrotnego

1. Pobierz i zainstaluj [Microsoft Online Services Asystenta logowania](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Pobierz i zainstaluj [64-bitowy moduł usługi Azure Active Directory dla środowiska Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Przejdź do `%programfiles%\Microsoft Azure Active Directory Connect` folderu.
4. Zaimportuj moduł bezproblemowe PowerShell logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Pobieranie listy lasów usługi Active Directory, dla których włączono bezproblemowe logowanie Jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell, wywołaj `New-AzureADSSOAuthenticationContext`. Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego Twojej dzierżawy.
2. Wywołanie `Get-AzureADSSOStatus`. To polecenie dostarcza listę lasów usługi Active Directory (odszukaj na liście "Domeny"), na którym ta funkcja została włączona.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: Wyłącz bezproblemowe logowanie Jednokrotne dla każdego lasu usługi Active Directory, w którym po skonfigurowaniu funkcji

1. Wywołanie `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla danego lasu usługi Active Directory.
2. Wywołanie `Disable-AzureADSSOForest -OnPremCredentials $creds`. To polecenie usuwa `AZUREADSSOACCT` konto komputera z lokalnego kontrolera domeny dla tego określonego lasu usługi Active Directory.
3. Powtórz te czynności dla każdego lasu usługi Active Directory, w którym po skonfigurowaniu funkcji.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Włącz bezproblemowe logowanie Jednokrotne dla każdego lasu usługi Active Directory

1. Wywołanie `Enable-AzureADSSOForest`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla danego lasu usługi Active Directory.
2. Powtórz poprzedni krok dla każdego lasu usługi Active Directory, w której chcesz skonfigurować funkcję.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włącz tę funkcję na dzierżawy

Aby włączyć tę funkcję na swojej dzierżawy, należy wywołać `Enable-AzureADSSO` , a następnie wprowadź **true** na `Enable:` wiersza.
