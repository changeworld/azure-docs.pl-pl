---
title: Rozpoczynanie pracy z usługą Azure Active Directory uwierzytelniania opartego na certyfikatach
description: Dowiedz się, jak skonfigurować uwierzytelnianie oparte na certyfikatach w środowisku
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: db2c19bdc303f6f7773772dd7873878ceb892cc3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelniony przez usługę Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu z systemem Windows, Android lub iOS podczas łączenia Twoje konto programu Exchange online:

- Aplikacje mobilne Microsoft, takich jak Microsoft Outlook i Microsoft Word
- Klienci programu Exchange ActiveSync (EAS)

Konfigurowanie tej funkcji eliminuje potrzebę wprowadzić kombinacja nazwy użytkownika i hasła do niektórych poczty i aplikacje Microsoft Office na urządzeniu przenośnym.

W tym temacie:

- Zawiera instrukcje dotyczące konfigurowania i korzystania z uwierzytelniania opartego na certyfikatach dla użytkowników dzierżawców w planach Office 365 Enterprise, Business, Education i instytucji rządowych Stanów Zjednoczonych. Ta funkcja jest dostępna w wersji zapoznawczej w planach Office 365 Chin, instytucji rządowych Stanów Zjednoczonych obrony i nam rządu federalnego.
- Przyjęto założenie, że masz już [infrastruktury kluczy publicznych (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) i [usług AD FS](connect/active-directory-aadconnectfed-whatis.md) skonfigurowany.

## <a name="requirements"></a>Wymagania

Aby skonfigurować uwierzytelnianie oparte na certyfikatach, muszą być spełnione następujące instrukcje:

- Uwierzytelnianie oparte na certyfikatach (CBA) jest obsługiwana tylko dla środowisk federacyjnym dla aplikacji przeglądarki lub natywnego klientów korzystających z nowoczesnego uwierzytelniania (ADAL). Jedynym wyjątkiem jest program Exchange Active Sync (EAS) dla usługi Exchange Online (EKSO), która może służyć do federacyjnych i zarządzanych kont.
- Główny urząd certyfikacji i wszystkie urzędy certyfikacji pośrednich muszą być skonfigurowane w usłudze Azure Active Directory.
- Każdego urzędu certyfikacji musi mieć listę odwołania certyfikatów (CRL), który można odwoływać się za pośrednictwem internetowy adres URL.
- Musi mieć co najmniej jeden certyfikat urzędu skonfigurowane w usłudze Azure Active Directory. Można znaleźć powiązanych kroków [Konfigurowanie urzędów certyfikacji](#step-2-configure-the-certificate-authorities) sekcji.
- Klienci programu Exchange ActiveSync certyfikat klienta musi mieć adres e-mail z obsługą routingu użytkownika w usłudze Exchange online w główna nazwa lub wartość RFC822 nazwę pola alternatywna nazwa podmiotu. Usługa Azure Active Directory mapuje wartości RFC822 atrybut adres serwera Proxy w katalogu.
- Urządzenie klienta musi mieć dostęp do co najmniej jeden certyfikat urzędu certyfikacji, który wystawia certyfikaty klienta.
- Certyfikat klienta na potrzeby uwierzytelniania klientów musi być wydany do klienta.

## <a name="step-1-select-your-device-platform"></a>Krok 1: Wybierz danej platformy urządzenia

Pierwszym krokiem dla platformy urządzenia, które są dla Ciebie ważne należy przejrzeć następujące czynności:

- Obsługa aplikacji mobilnych pakietu Office
- Wymagania dotyczące konkretnej implementacji

Istnieje odpowiednie informacje dla następujących platform urządzeń:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurowanie urzędów certyfikacji

Aby skonfigurować z urzędów certyfikacji w usłudze Azure Active Directory, dla każdego urzędu certyfikacji, przekazać następujących elementów:

* Część publiczną certyfikatu w *.cer* formatu
* Internet skierowane adresów URL, w którym znajdują się odwołania list certyfikatów (CRL)

Schemat dla urzędu certyfikacji wygląda następująco:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

W przypadku konfiguracji, można użyć [Azure Active Directory programu PowerShell w wersji 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Uruchom program Windows PowerShell z uprawnieniami administratora.
2. Instalowanie wersji modułu Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) lub nowszej.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Pierwszym krokiem konfiguracji należy ustanowić połączenie z dzierżawą. Się, gdy istnieje połączenie z dzierżawą, można przejrzeć, dodawanie, usuwanie i modyfikowanie zaufanych urzędów certyfikacji zdefiniowanych w katalogu.

### <a name="connect"></a>Połączenie

Aby ustanowić połączenie z dzierżawy, należy użyć [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) polecenia cmdlet:

    Connect-AzureAD

### <a name="retrieve"></a>Pobieranie

Aby uzyskać dostęp do zaufanych urzędów certyfikacji zdefiniowanych w katalogu, należy użyć [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Add

Aby utworzyć zaufanego urzędu certyfikacji, należy użyć [AzureADTrustedCertificateAuthority nowy](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet i ustaw **crlDistributionPoint** atrybutu prawidłowe wartości:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remove

Aby usunąć zaufany urząd certyfikacji, należy użyć [AzureADTrustedCertificateAuthority Usuń](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modyfikuj

Aby zmodyfikować zaufanego urzędu certyfikacji, należy użyć [AzureADTrustedCertificateAuthority zestaw](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Krok 3: Konfigurowanie odwołania

Aby odwołać certyfikat klienta, Azure Active Directory pobiera listy odwołania certyfikatów (CRL) z adresów URL przekazany jako część informacji o certyfikacie urzędu i buforuje ją. Ostatni publikowania sygnatury czasowej (**daty wprowadzenia** właściwości) na liście CRL służy do zapewnienia listy CRL jest nadal ważny. Lista CRL okresowo odwołuje się do odwołania dostępu do certyfikatów, które są częścią listy.

Jeśli więcej błyskawicznych odwołania jest wymagana (na przykład, jeśli użytkownik utraci urządzenie), można unieważniona token autoryzacji użytkownika. Zanim unieważni token autoryzacji, należy ustawić **StsRefreshTokenValidFrom** dla tego użytkownika za pomocą środowiska Windows PowerShell. Należy zaktualizować **StsRefreshTokenValidFrom** pola dla każdego użytkownika, chcesz odwołać dostęp.

Aby upewnić się, że odwołania będzie się powtarzał, należy ustawić **daty wprowadzenia** listy CRL na datę przypadającą po wartości ustawionej przez **StsRefreshTokenValidFrom** i upewnij się, dany certyfikat jest na liście CRL.

Poniższe kroki wchodzą w skład procesu aktualizowania i unieważnia token autoryzacji przez ustawienie **StsRefreshTokenValidFrom** pola.

**Aby skonfigurować odwołania:**

1. Połącz z poświadczeniami administratora do usługi MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Pobierz bieżącą wartość StsRefreshTokensValidFrom dla użytkownika:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Skonfiguruj nową wartość StsRefreshTokensValidFrom dla użytkownika równe bieżącą sygnaturę czasową:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Daty, które można ustawić musi być w przyszłości. Jeśli w przyszłości, nie jest datą **StsRefreshTokensValidFrom** nie ustawiono właściwości. Jeśli w przyszłości, jest data **StsRefreshTokensValidFrom** ma ustawioną wartość bieżącej godziny (nie daty wskazanej za pomocą polecenia Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Testowanie konfiguracji

### <a name="testing-your-certificate"></a>Testowanie certyfikatu

Jako pierwszego testu konfiguracji, należy spróbować zalogować się do [programu Outlook Web Access](https://outlook.office365.com) lub [usługi SharePoint Online](https://microsoft.sharepoint.com) przy użyciu programu **przeglądarki na urządzeniu**.

Jeśli logowanie się pomyślnie, następnie należy pamiętać, że:

- Zainicjowano certyfikatu użytkownika z urządzeniem testu
- Usługi AD FS jest poprawnie skonfigurowany.

### <a name="testing-office-mobile-applications"></a>Testowanie aplikacji mobilnych pakietu Office

**Aby przetestować uwierzytelnianie oparte na aplikację mobilną Office:**

1. Na urządzeniu testu instalacji aplikacji mobilnych pakietu Office (na przykład OneDrive).
3. Uruchom aplikację.
4. Wprowadź nazwę użytkownika, a następnie wybierz certyfikat użytkownika, którego chcesz użyć.

Użytkownik powinien być pomyślnie zarejestrowany.

### <a name="testing-exchange-activesync-client-applications"></a>Testowanie aplikacji klienta programu Exchange ActiveSync

Aby uzyskać dostęp do programu Exchange ActiveSync (EAS) przy użyciu uwierzytelniania opartego na certyfikatach, profilu EAS zawierający certyfikat klienta musi być dostępne dla aplikacji.

Profilu EAS musi zawierać następujące informacje:

- Certyfikat użytkownika służący do uwierzytelniania

- Punkt końcowy EAS (na przykład outlook.office365.com)

Można konfigurować i umieszczona na urządzeniu w ramach wykorzystania zarządzania urządzeniami przenośnymi (MDM) jak usługa Intune lub umieszczając ręcznie certyfikat w profilu EAS urządzenia profilu EAS.

### <a name="testing-eas-client-applications-on-android"></a>Testowanie aplikacji klienta EAS w systemie Android

**Aby przetestować uwierzytelnianie certyfikatu:**

1. Konfigurowanie profilu EAS w aplikacji, która spełnia wymagania w poprzedniej sekcji.
2. Otwórz aplikację i sprawdź, czy jest synchronizowanie poczty.

## <a name="next-steps"></a>Kolejne kroki

[Dodatkowe informacje dotyczące uwierzytelniania opartego na certyfikatach na urządzeniach z systemem Android.](active-directory-certificate-based-authentication-android.md)

[Dodatkowe informacje dotyczące uwierzytelniania opartego na certyfikatach na urządzeniach z systemem iOS.](active-directory-certificate-based-authentication-ios.md)