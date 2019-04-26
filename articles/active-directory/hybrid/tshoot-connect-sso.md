---
title: 'Azure Active Directory Connect: Rozwiązywanie problemów z bezproblemowego logowania jednokrotnego | Dokumentacja firmy Microsoft'
description: W tym temacie opisano, jak rozwiązywać problemy z usługi Azure Active Directory bezproblemowe logowanie jednokrotne
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: abfdad1db655c102dbfb300434eac952fe2154dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381894"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Rozwiązywanie problemów z usługi Azure Active Directory bezproblemowego logowania jednokrotnego

Ten artykuł zawiera informacje dotyczące typowych problemów dotyczących usługi Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne (bezproblemowe logowanie Jednokrotne).

## <a name="known-issues"></a>Znane problemy

- W niektórych przypadkach umożliwiając bezproblemowe logowanie Jednokrotne może potrwać do 30 minut.
- Jeśli wyłączyć i ponownie włącz bezproblemowe logowanie Jednokrotne na swojej dzierżawy, użytkownicy nie otrzymywali funkcji logowania jednokrotnego do momentu ich pamięci podręcznej bilety protokołu Kerberos, zwykle prawidłowe przez 10 godzin mogły wygasnąć.
- Obsługa przeglądarki Microsoft Edge nie jest dostępna.
- Jeśli bezproblemowe logowanie Jednokrotne zakończy się powodzeniem, użytkownik nie ma możliwości, aby wybrać **nie wylogowuj mnie**. Ze względu na to zachowanie [SharePoint i OneDrive scenariuszy mapowania](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nie działają.
- Klienci usługi Office 365 Win32 (Outlook, Word, Excel i innych) z wersji 16.0.8730.xxxx lub nowszym są obsługiwane przy użyciu nieinterakcyjnych usługi flow. Inne wersje są nieobsługiwane. w tych wersjach użytkownicy staną się ich nazw użytkowników, ale nie haseł do logowania. Do usługi OneDrive, musisz aktywować [OneDrive dyskretnej konfiguracji funkcji](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) dyskretnej środowisko logowania jednokrotnego.
- Bezproblemowe logowanie Jednokrotne nie działa w trybie przeglądania prywatnym w programie Firefox.
- Bezproblemowe logowanie Jednokrotne nie działa w programie Internet Explorer, gdy jest włączony tryb rozszerzony chroniony.
- Bezproblemowe logowanie Jednokrotne nie działa w przeglądarkach dla urządzeń przenośnych w systemach iOS i Android.
- Jeśli użytkownik jest częścią zbyt wielu grup w usłudze Active Directory, biletu protokołu Kerberos użytkownika będzie prawdopodobnie zbyt duży, aby przetworzyć, a spowoduje to bezproblemowe logowanie Jednokrotne nie powiedzie się. Usługa Azure AD HTTPS żądań może mieć nagłówki o maksymalnym rozmiarze 50 KB; Bilety protokołu Kerberos, konieczne może być mniejszy niż ten limit, aby pomieścić inne artefakty usługi Azure AD (zwykle 2 – 5 KB), takich jak pliki cookie. Nasze zalecenie jest zmniejszenie członkostwa grupy użytkownika i spróbuj ponownie.
- Jeśli synchronizacja co najmniej 30 lasów usługi Active Directory, nie można włączyć bezproblemowe logowanie Jednokrotne za pomocą usługi Azure AD Connect. Jako obejście tego problemu, możesz [ręcznie włączyć](#manual-reset-of-the-feature) funkcję w dzierżawie.
- Dodawanie adresu URL usługi Azure AD (https://autologon.microsoftazuread-sso.com) do strefy Zaufane witryny zamiast lokalnej strefy intranetowej *uniemożliwia użytkownikom logowanie*.
- Bezproblemowe logowanie Jednokrotne używa **RC4_HMAC_MD5** typu szyfrowania dla protokołu Kerberos. Wyłączanie korzystania z **RC4_HMAC_MD5** typ szyfrowania w ustawieniach usługi Active Directory spowoduje przerwanie bezproblemowe logowanie Jednokrotne. W narzędziu Edytor zarządzania zasadami grupy upewnij się, że wartość **RC4_HMAC_MD5** w obszarze **Konfiguracja komputera -> Ustawienia Windows -> Ustawienia zabezpieczeń -> Zasady lokalne -> Opcje zabezpieczeń — > "Zabezpieczenia sieci: Konfigurowanie typów szyfrowania dozwolonych dla protokołu Kerberos"** jest **włączone**. Ponadto bezproblemowe logowanie Jednokrotne nie może korzystać z innych typów szyfrowania, dlatego upewnij się, że są one **wyłączone**.

## <a name="check-status-of-feature"></a>Sprawdź stan funkcji

Upewnij się, że funkcja bezproblemowe logowanie Jednokrotne jest nadal **włączone** w dzierżawie. Stan możesz sprawdzić, przechodząc do **program Azure AD Connect** okienka [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory: Okienko usługi Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Kliknij, aby wyświetlić wszystkich lasów usługi AD, które zostały włączone dla bezproblemowe logowanie Jednokrotne.

![Centrum administracyjne usługi Azure Active Directory: Bezproblemowe logowanie Jednokrotne okienko](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Przyczyny niepowodzenia logowania w Centrum administracyjnym usługi Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa usługi ma skojarzoną licencję usługi Azure AD Premium, możesz także obejrzeć [raport aktywności logowania](../reports-monitoring/concept-sign-ins.md) w [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/).

![Centrum administracyjne usługi Azure Active Directory: Raport dotyczący logowań](./media/tshoot-connect-sso/sso9.png)

Przejdź do **usługi Azure Active Directory** > **logowania** w [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/), a następnie wybierz pozycję aktywności logowania dla określonego użytkownika. Wyszukaj **kod błędu logowania** pola. Mapowania wartości tego pola Przyczyna niepowodzenia i rozwiązanie, korzystając z poniższej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 81001 | Bilet Kerberos użytkownika jest zbyt duży. | Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.
| 81002 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81003 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81004 | Próba uwierzytelniania Kerberos nie powiodła się. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81008 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81009 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [Rozwiązywanie problemów z listy kontrolnej](#troubleshooting-checklist).
| 81010 | Bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ ważność biletu Kerberos użytkownika wygasła lub bilet jest nieprawidłowy. | Użytkownik musi zalogować się z poziomu urządzenia przyłączone do domeny w sieci firmowej.
| 81011 | Nie można znaleźć obiektu użytkownika, w oparciu o informacje z biletu Kerberos użytkownika. | Użyj usługi Azure AD Connect, aby zsynchronizować informacje o użytkowniku w usłudze Azure AD.
| 81012 | Użytkownik próbujący zalogować się do usługi Azure AD jest inny niż użytkownik, który jest zalogowany do urządzenia. | Użytkownik musi zalogować się z innego urządzenia.
| 81013 | Nie można znaleźć obiektu użytkownika, w oparciu o informacje z biletu Kerberos użytkownika. |Użyj usługi Azure AD Connect, aby zsynchronizować informacje o użytkowniku w usłudze Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista kontrolna rozwiązywania problemów

Poniższa lista kontrolna umożliwia rozwiązywanie problemów bezproblemowe logowanie Jednokrotne:

- Upewnij się, że włączona jest funkcja bezproblemowe logowanie Jednokrotne w programie Azure AD Connect. Jeśli nie można włączyć tę funkcję, (na przykład z powodu zablokowany port), upewnij się, że wszystkie [wymagania wstępne](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) w miejscu.
- Po włączeniu zarówno [Azure AD Join](../active-directory-azureadjoin-overview.md) i bezproblemowe logowanie Jednokrotne w swojej dzierżawie, upewnij się, że problem nie jest za pomocą usługi Azure AD Join. Usługa rejestracji Jednokrotnej z usługi Azure AD Join mają pierwszeństwo przed bezproblemowe logowanie Jednokrotne, jeśli urządzenie jest zarejestrowane w usłudze Azure AD i przyłączone do domeny. Przy użyciu logowania jednokrotnego z usługi Azure AD Join użytkownik zobaczy na kafelku logowania, który jest wyświetlany komunikat "Podłączony do Windows".
- Upewnij się, że adres URL usługi Azure AD (https://autologon.microsoftazuread-sso.com) jest częścią ustawienia strefy intranetowej dla użytkownika.
- Upewnij się, że urządzenie jest przyłączony do domeny usługi Active Directory. Urządzenie _nie_ muszą być [usługi Azure AD dołączono](../active-directory-azureadjoin-overview.md) bezproblemowego logowania jednokrotnego do pracy.
- Upewnij się, że użytkownik jest zalogowany do urządzenia za pomocą konta domeny usługi Active Directory.
- Upewnij się, że konto użytkownika zostało z lasu usługi Active Directory, w którym bezproblemowe logowanie Jednokrotne zostało skonfigurowane.
- Upewnij się, że urządzenie jest połączone z siecią firmową.
- Upewnij się, że urządzenia czas jest zsynchronizowany z czasem w usługi Active Directory i kontrolery domeny i że są one w ciągu pięciu minut od siebie.
- Upewnij się, że `AZUREADSSOACC` konto komputera jest obecny i włączona w każdym lesie usługi AD, która ma włączone bezproblemowe logowanie Jednokrotne. Jeśli konto komputera został usunięty lub nie istnieje, możesz użyć [poleceń cmdlet programu PowerShell](#manual-reset-of-the-feature) ich ponownego utworzenia.
- Listy istniejące bilety Kerberos na urządzeniu za pomocą `klist` polecenie w wierszu polecenia. Upewnij się, że bilety wystawione dla `AZUREADSSOACC` istnieją konta komputera. Bilety Kerberos konta użytkowników są zwykle prawidłowe przez 10 godzin. W usłudze Active Directory, może mieć różne ustawienia.
- Jeśli wyłączona, a następnie ponownie włączyć bezproblemowe logowanie Jednokrotne w dzierżawie, użytkownicy nie otrzymywali funkcji logowania jednokrotnego do momentu ich w pamięci podręcznej bilety protokołu Kerberos mogły wygasnąć.
- Wyczyść istniejące bilety Kerberos z urządzenia przy użyciu `klist purge` polecenia, a następnie spróbuj ponownie.
- Aby ustalić, czy istnieją problemy dotyczące języka JavaScript, przejrzyj dzienniki konsoli z przeglądarki (w obszarze **narzędzi deweloperskich**).
- Przegląd [dzienniki kontrolerów domeny](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Po włączeniu inspekcji sukcesów na kontrolerze domeny, a następnie za każdym razem, gdy użytkownik loguje się za pośrednictwem bezproblemowe logowanie Jednokrotne, wpis zabezpieczeń są rejestrowane w dzienniku zdarzeń. Za pomocą następującego zapytania, można znaleźć te zdarzenia zabezpieczeń. (Poszukaj zdarzenia **4769** skojarzone z kontem komputera **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Resetowanie ręczne funkcji

Jeśli rozwiązywania problemów nie pomogły, możesz ręcznie zresetować tę funkcję w dzierżawie. Wykonaj następujące kroki na serwerze lokalnym, w którym uruchamiasz program Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1: Zaimportuj moduł programu PowerShell usługi bezproblemowego logowania jednokrotnego

1. Najpierw należy pobrać i zainstalować [usługi Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł programu PowerShell usługi bezproblemowego logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Pobierz listę lasów usługi Active Directory, dla których włączono bezproblemowe logowanie Jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell, należy wywołać `New-AzureADSSOAuthenticationContext`. Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Wywołaj `Get-AzureADSSOStatus`. To polecenie umożliwia lista lasów usługi Active Directory (odszukaj pozycję na liście "Domeny"), na którym ta funkcja została włączona.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: Wyłącz bezproblemowe logowanie Jednokrotne dla każdego lasu usługi Active Directory, w którym po skonfigurowaniu funkcji

1. Wywołaj `$creds = Get-Credential`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

   > [!NOTE]
   > Używamy nazwy użytkownika administratora domeny, podany w użytkownika głównej nazwy (UPN) (johndoe@contoso.com) format lub kwalifikowana konta sam formacie nazwy domeny (contoso\johndoe lub contoso.com\johndoe), można znaleźć zamierzony lasu usługi AD. Jeśli używasz nazwy kwalifikowanej konta sam domeny, używamy część domeny nazwa użytkownika do [zlokalizować kontrolera domeny z administratora domeny przy użyciu systemu DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Jeśli zamiast tego należy użyć nazwy UPN możemy [przekształca ją do nazwy kwalifikowanej konta sam domeny](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) przed lokalizowanie odpowiedniego kontrolera domeny.

2. Wywołaj `Disable-AzureADSSOForest -OnPremCredentials $creds`. To polecenie usuwa `AZUREADSSOACC` konto komputera z lokalnego kontrolera domeny dla tego określonego lasu usługi Active Directory.
3. Powtórz te czynności dla każdego lasu usługi Active Directory, w którym po skonfigurowaniu funkcji.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Włącz bezproblemowe logowanie Jednokrotne dla każdego lasu usługi Active Directory

1. Wywołaj `Enable-AzureADSSOForest`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

   > [!NOTE]
   > Używamy nazwy użytkownika administratora domeny, podany w użytkownika głównej nazwy (UPN) (johndoe@contoso.com) format lub kwalifikowana konta sam formacie nazwy domeny (contoso\johndoe lub contoso.com\johndoe), można znaleźć zamierzony lasu usługi AD. Jeśli używasz nazwy kwalifikowanej konta sam domeny, używamy część domeny nazwa użytkownika do [zlokalizować kontrolera domeny z administratora domeny przy użyciu systemu DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Jeśli zamiast tego należy użyć nazwy UPN możemy [przekształca ją do nazwy kwalifikowanej konta sam domeny](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) przed lokalizowanie odpowiedniego kontrolera domeny.

2. Powtórz poprzedni krok dla każdego lasu usługi Active Directory, w której chcesz skonfigurować tę funkcję.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włącz funkcję w dzierżawie

Aby włączyć tę funkcję w dzierżawie, należy wywołać `Enable-AzureADSSO -Enable $true`.
