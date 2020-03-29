---
title: 'Połączenie usługi Azure Active Directory: rozwiązywanie problemów z logowaniem jednokrotnym | Dokumenty firmy Microsoft'
description: W tym temacie opisano, jak rozwiązywać problemy z bezproblemowym logowaniem jednokrotnym usługi Azure Active Directory
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049488"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Rozwiązywanie problemów z bezproblemowym logowaniem jednokrotnym usługi Azure Active Directory

Ten artykuł ułatwia znajdowanie informacji dotyczących rozwiązywania typowych problemów dotyczących bezproblemowego logowania jednokrotnego usługi Azure Active Directory (Azure AD) bez szwu.This article helps you find troubleshooting information about common problems regarding Azure Active Directory (Azure AD) Seamless Single Sign-On (Seamless Single Sign-On).

## <a name="known-issues"></a>Znane problemy

- W kilku przypadkach włączenie bezproblemowego logowania jednokrotnego może potrwać do 30 minut.
- Jeśli wyłączysz i ponownie włączysz bezproblemowe logowanie jednokrotne w dzierżawie, użytkownicy nie otrzymają środowiska logowania jednokrotnego, dopóki ich buforowane bilety Protokołu Kerberos, zwykle ważne przez 10 godzin, nie wygasną.
- Jeśli bezproblemowe logowanie jednokrotne zakończy się pomyślnie, użytkownik nie ma możliwości wybrania **opcji Zachowaj mnie zalogowany**. Z powodu tego zachowania [scenariusze mapowania programu SharePoint i OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nie działają.
- Klienci usługi Office 365 Win32 (Outlook, Word, Excel i inni) w wersji 16.0.8730.xxxx i nowszej są obsługiwani przy użyciu przepływu nieinteraktywnego. Inne wersje nie są obsługiwane; w tych wersjach użytkownicy będą wprowadzać swoje nazwy użytkowników, ale nie hasła, aby się zalogować. W usłudze OneDrive należy aktywować [funkcję cichej konfiguracji usługi OneDrive,](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) aby uzyskać funkcję cichego logowania.
- Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w Firefoksie.
- Bezproblemowe logowanie jednokrotne nie działa w programie Internet Explorer po włączeniu trybu rozszerzonej ochrony.
- Bezproblemowe logowanie jednokrotne nie działa w przeglądarkach mobilnych na iOS i Android.
- Jeśli użytkownik jest częścią zbyt wielu grup w usłudze Active Directory, bilet protokołu Kerberos użytkownika będzie prawdopodobnie zbyt duży, aby przetworzyć, a to spowoduje bezproblemowe logowanie jednokrotne. Żądania https usługi Azure AD może mieć nagłówki o maksymalnym rozmiarze 50 KB; Bilety protokołu Kerberos muszą być mniejsze niż ten limit, aby uwzględnić inne artefakty usługi Azure AD (zazwyczaj 2 - 5 KB), takie jak pliki cookie. Naszym zaleceniem jest zmniejszenie liczby członków grupy użytkowników i ponowne spróbowanie.
- Jeśli synchronizujesz 30 lub więcej lasów usługi Active Directory, nie możesz włączyć bezproblemowego logowania jednokrotnego za pośrednictwem usługi Azure AD Connect. Aby obejść ten problem, można [ręcznie włączyć](#manual-reset-of-the-feature) funkcję w dzierżawie.
- Dodanie adresu URL usługi`https://autologon.microsoftazuread-sso.com`Azure AD ( ) do strefy Zaufane witryny zamiast strefy Lokalny intranet *uniemożliwia użytkownikom logowanie*się .
- Bezproblemowe logowanie jednokrotne obsługuje AES256_HMAC_SHA1, AES128_HMAC_SHA1 i RC4_HMAC_MD5 typy szyfrowania protokołu Kerberos. Zaleca się, że typ szyfrowania dla konta AzureADSSOAcc$ jest ustawiony na AES256_HMAC_SHA1 lub jeden z typów AES vs RC4 dla dodatkowych zabezpieczeń. Typ szyfrowania jest przechowywany w atrybucie msDS-SupportedEncryptionTypes konta w usłudze Active Directory.  Jeśli typ szyfrowania konta AzureADSSOAcc$ jest ustawiony na RC4_HMAC_MD5 i chcesz zmienić go na jeden z typów szyfrowania AES, upewnij się, że najpierw przerzucasz klucz odszyfrowywania protokołu Kerberos konta AzureADSSOAcc$, jak wyjaśniono w [dokumencie często zadawanych pytań](how-to-connect-sso-faq.md) w odpowiednim pytaniu, w przeciwnym razie bezproblemowe logowanie jednokrotne nie nastąpi.

## <a name="check-status-of-feature"></a>Sprawdź stan funkcji

Upewnij się, że funkcja bezproblemowego logowania jednokrotnego jest nadal **włączona** w dzierżawie. Stan można sprawdzić, przechodząc do okienka **Usługi Azure AD Connect** w centrum [administracyjnym usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory: okienko Usługi Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Kliknij, aby wyświetlić wszystkie lasy usługi AD, które zostały włączone dla bezproblemowego logowania jednokrotnego.

![Centrum administracyjne usługi Azure Active Directory: bezproblemowe okienko logowania jednokrotnego](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Przyczyny niepowodzenia logowania w centrum administracyjnym usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa ma skojarzoną z nią licencję usługi Azure AD Premium, możesz również sprawdzić [raport aktywności logowania w](../reports-monitoring/concept-sign-ins.md) centrum [administracyjnym usługi Azure Active Directory.](https://aad.portal.azure.com/)

![Centrum administracyjne usługi Azure Active Directory: raport logowania](./media/tshoot-connect-sso/sso9.png)

Przejdź do logowania usługi **Azure Active Directory** > **w** [centrum administracyjnym usługi Azure Active Directory,](https://aad.portal.azure.com/)a następnie wybierz działanie logowania określonego użytkownika. Poszukaj pola **KOD BŁĘDU LOGOWANIA.** Zamapuj wartość tego pola na przyczynę awarii i rozwiązanie problemu, korzystając z poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 81001 | Bilet Kerberos użytkownika jest zbyt duży. | Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.
| 81002 | Nie można zweryfikować biletu protokołu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81003 | Nie można zweryfikować biletu protokołu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81004 | Próba uwierzytelniania Kerberos nie powiodła się. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81008 | Nie można zweryfikować biletu protokołu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81009 | Nie można zweryfikować biletu protokołu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81010 | Bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ ważność biletu Kerberos użytkownika wygasła lub bilet jest nieprawidłowy. | Użytkownik musi zalogować się z urządzenia przyłączanego do domeny w sieci firmowej.
| 81011 | Nie można odnaleźć obiektu użytkownika na podstawie informacji zawartych w bilecie protokołu Kerberos użytkownika. | Użyj usługi Azure AD Connect, aby zsynchronizować informacje użytkownika z usługą Azure AD.
| 81012 | Użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika, który jest zalogowany do urządzenia. | Użytkownik musi zalogować się z innego urządzenia.
| 81013 | Nie można odnaleźć obiektu użytkownika na podstawie informacji zawartych w bilecie protokołu Kerberos użytkownika. |Użyj usługi Azure AD Connect, aby zsynchronizować informacje użytkownika z usługą Azure AD. 

## <a name="troubleshooting-checklist"></a>Troubleshooting checklist (Lista kontrolna rozwiązywania problemów)

Aby rozwiązać problemy z bezproblemowym logowaniem jednokrotnym, użyj następującej listy kontrolnej:

- Upewnij się, że funkcja bezproblemowego logowania jednokrotnego jest włączona w usłudze Azure AD Connect. Jeśli nie możesz włączyć tej funkcji (na przykład z powodu zablokowanego portu), upewnij się, że masz wszystkie [wymagania wstępne](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) w miejscu.
- Jeśli włączono zarówno [przyłączenie usługi Azure AD,](../active-directory-azureadjoin-overview.md) jak i bezproblemowe logowanie jednokrotne w dzierżawie, upewnij się, że problem nie dotyczy usługi Azure AD Join. Logowanie jednokrotne z usługi Azure AD Join ma pierwszeństwo przed bezproblemowym logowaniem jednokrotnym, jeśli urządzenie jest zarejestrowane w usłudze Azure AD i przyłączone do domeny. Dzięki logującemu logowanie z usługi Azure AD Dołącz użytkownik widzi kafelek logowania z napisem "Połączony z systemem Windows".
- Upewnij się, że`https://autologon.microsoftazuread-sso.com`adres URL usługi Azure AD ( ) jest częścią ustawień strefy intranetu użytkownika.
- Upewnij się, że urządzenie firmowe jest przyłączone do domeny usługi Active Directory. Urządzenie _nie_ musi być [przyłączone](../active-directory-azureadjoin-overview.md) do usługi Azure AD, aby bezproblemowe logowanie jednokrotne działało.
- Upewnij się, że użytkownik jest zalogowany do urządzenia za pośrednictwem konta domeny usługi Active Directory.
- Upewnij się, że konto użytkownika pochodzi z lasu usługi Active Directory, w którym skonfigurowano bezproblemowe logowanie jednokrotne.
- Upewnij się, że urządzenie jest podłączone do sieci firmowej.
- Upewnij się, że czas urządzenia jest zsynchronizowany z czasem zarówno w usłudze Active Directory, jak i na kontrolerach domeny i że znajdują się one w odległości pięciu minut od siebie.
- Upewnij się, że konto `AZUREADSSOACC` komputera jest obecne i włączone w każdym lesie usługi AD, w który chcesz włączyć bezproblemowe logowanie jednokrotne. Jeśli konto komputera zostało usunięte lub brakuje go, można użyć [poleceń cmdlet programu PowerShell,](#manual-reset-of-the-feature) aby je odtworzyć.
- Wyświetl listę istniejących biletów protokołu Kerberos `klist` na urządzeniu za pomocą polecenia z wiersza polecenia. Upewnij się, że bilety `AZUREADSSOACC` wystawione dla konta komputera są obecne. Bilety Kerberos użytkowników są zazwyczaj ważne przez 10 godzin. W usłudze Active Directory mogą być dostępne inne ustawienia.
- Jeśli wyłączysz i ponownie włączono bezproblemowe logowanie jednokrotne w dzierżawie, użytkownicy nie otrzymają logowania jednokrotnego, dopóki ich buforowane bilety Protokołu Kerberos nie wygasną.
- Przeczyść istniejące bilety protokołu Kerberos `klist purge` z urządzenia za pomocą polecenia i spróbuj ponownie.
- Aby ustalić, czy występują problemy związane z JavaScript, przejrzyj dzienniki konsoli przeglądarki (w obszarze **Narzędzia programistyczne).**
- Przejrzyj [dzienniki kontrolera domeny](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Jeśli włączysz inspekcję sukcesu na kontrolerze domeny, za każdym razem, gdy użytkownik loguje się za pośrednictwem bezproblemowego logowania jednokrotnego, wpis zabezpieczeń jest rejestrowany w dzienniku zdarzeń. Te zdarzenia zabezpieczeń można znaleźć za pomocą następującej kwerendy. (Poszukaj zdarzenia **4769** skojarzonego z kontem komputera **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Ręczne resetowanie funkcji

Jeśli rozwiązywanie problemów nie pomogło, można ręcznie zresetować funkcję w dzierżawie. Wykonaj następujące kroki na serwerze lokalnym, na którym jest uruchomiona usługa Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1: Importowanie modułu Seamless SSO PowerShell

1. Najpierw pobierz i zainstaluj [program Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł Seamless SSO PowerShell za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Pobierz listę lasów usługi Active Directory, na których włączono bezproblemowe logowanie jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Zadzwoń `Get-AzureADSSOStatus`. To polecenie zawiera listę lasów usługi Active Directory (patrz lista "Domeny"), na której ta funkcja została włączona.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: Wyłącz bezproblemowe logowanie jednokrotne dla każdego lasu usługi Active Directory, w którym została skonfigurowana funkcja

1. Zadzwoń `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

   > [!NOTE]
   >Nazwa użytkownika poświadczeń administratora domeny musi być wprowadzona w formacie nazwy konta SAM (contoso\johndoe lub contoso.com\johndoe). Używamy części domeny nazwy użytkownika, aby zlokalizować kontroler domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Używane konto administratora domeny nie może być członkiem grupy Użytkownicy chronieni. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Zadzwoń `Disable-AzureADSSOForest -OnPremCredentials $creds`. To polecenie usuwa `AZUREADSSOACC` konto komputera z lokalnego kontrolera domeny dla tego konkretnego lasu usługi Active Directory.
3. Powtórz poprzednie kroki dla każdego lasu usługi Active Directory, w którym została skonfigurowana funkcja.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Włącz bezproblemowe logowanie jednokrotne dla każdego lasu usługi Active Directory

1. Zadzwoń `Enable-AzureADSSOForest`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

   > [!NOTE]
   >Nazwa użytkownika poświadczeń administratora domeny musi być wprowadzona w formacie nazwy konta SAM (contoso\johndoe lub contoso.com\johndoe). Używamy części domeny nazwy użytkownika, aby zlokalizować kontroler domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Używane konto administratora domeny nie może być członkiem grupy Użytkownicy chronieni. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Powtórz poprzedni krok dla każdego lasu usługi Active Directory, w którym chcesz skonfigurować operację.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włącz funkcję w dzierżawie

Aby włączyć tę funkcję w `Enable-AzureADSSO -Enable $true`dzierżawie, zadzwoń do .
