---
title: Wprowadzenie do uwierzytelniania opartego na certyfikatach — usługi Azure Active Directory
description: Dowiedz się, jak skonfigurować uwierzytelnianie oparte na certyfikatach w danym środowisku
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f57d4615fc80df6c5df9ba295288ad71ae12fa23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359079"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Wprowadzenie do uwierzytelniania opartego na certyfikatach w usłudze Azure Active Directory

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelniane przez usługę Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu Windows, Android lub iOS podczas nawiązywania połączenia z Twojego konta usługi Exchange online do:

- Aplikacje mobilne firmy Microsoft, takich jak Microsoft Outlook i Microsoft Word
- Klienci programu Exchange ActiveSync (EAS)

Konfiguruje tę funkcję, eliminuje konieczność wprowadzania kombinację nazwy użytkownika i hasła w niektórych poczty i aplikacje Microsoft Office na urządzeniu przenośnym.

W tym temacie:

- Zawiera instrukcje konfigurowania i korzystanie z uwierzytelniania opartego na certyfikatach dla użytkowników dzierżaw w planach Office 365 Enterprise, Business, Education i dla instytucji rządowych USA. Ta funkcja jest dostępna w wersji zapoznawczej w planach (Chiny) pakietu Office 365, ochrona dla instytucji rządowych USA i nam rządu federalnego.
- Przyjęto założenie, że masz już [infrastruktury kluczy publicznych (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) i [usług AD FS](../hybrid/how-to-connect-fed-whatis.md) skonfigurowane.

## <a name="requirements"></a>Wymagania

Aby skonfigurować uwierzytelnianie oparte na certyfikatach, muszą być spełnione następujące instrukcje:

- Uwierzytelnianie oparte na certyfikatach (CBA) jest obsługiwana tylko dla środowisk Sfederowane dla aplikacji przeglądarki lub natywnych klientów korzystających z nowoczesnego uwierzytelniania (ADAL). Jedynym wyjątkiem jest programu Exchange Active Sync (EAS) dla programu Exchange Online (EKSO), którego można użyć w przypadku kont federacyjnych i zarządzanych.
- Główny urząd certyfikacji i wszelkie pośrednim urzędem certyfikacji musi być skonfigurowany w usłudze Azure Active Directory.
- Każdy urzędu certyfikacji musi mieć listę odwołania certyfikatów (CRL), która może znajdować się za pośrednictwem adresu URL dostępnego z Internetu.
- Musisz mieć uprawnienia co najmniej jeden certyfikat skonfigurowany w usłudze Azure Active Directory. Możesz znaleźć powiązanych czynności opisane w [Konfigurowanie urzędów certyfikacji](#step-2-configure-the-certificate-authorities) sekcji.
- Dla klientów programu Exchange ActiveSync certyfikat klienta musi mieć adres e-mail routingowi użytkownika w programie Exchange online w nazwie podmiotu zabezpieczeń lub wartości RFC822 nazwę w polu alternatywna nazwa podmiotu. Usługa Azure Active Directory mapuje RFC822 wartość atrybutu adres serwera Proxy w katalogu.
- Urządzenia klienta musi mieć dostęp do urzędu certyfikacji co najmniej jedną, która wystawia certyfikaty klienta.
- Certyfikat klienta na potrzeby uwierzytelniania klienta musi być wydany do klienta.

## <a name="step-1-select-your-device-platform"></a>Krok 1: Wybierz platformę urządzeń

Pierwszym krokiem dla platformy urządzenia, które Cię interesują należy przejrzeć następujące:

- Obsługa aplikacji mobilnych pakietu Office
- Wymagania dotyczące konkretnej implementacji

Istnieje odpowiednie informacje na następujących platformach urządzeń:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurowanie urzędów certyfikacji

Aby skonfigurować swoje urzędów certyfikacji w usłudze Azure Active Directory dla każdego urzędu certyfikacji, należy przekazać następujące czynności:

* Część publiczną certyfikatu w *cer* formatu
* Adresy URL dostępnego z Internetu, gdzie znajdują się list odwołania certyfikatów (CRL)

Schemat dla urzędu certyfikacji będzie wyglądać w następujący sposób:

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

W przypadku konfiguracji, można użyć [usługi Azure Active Directory PowerShell w wersji 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Uruchom program Windows PowerShell z uprawnieniami administratora.
2. Instalowanie wersji modułu usługi Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) lub nowszej.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Pierwszym krokiem konfiguracji należy ustanowić połączenie z Twojej dzierżawy. Jak najszybciej istnieje połączenie z dzierżawą, można przejrzeć, dodać, usuwanie i modyfikowanie zaufanych urzędów certyfikacji, które są zdefiniowane w katalogu.

### <a name="connect"></a>Połączenie

Aby nawiązać połączenie w swojej dzierżawie, użyj [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) polecenia cmdlet:

    Connect-AzureAD

### <a name="retrieve"></a>Pobieranie

Aby pobrać zaufanych urzędów certyfikacji, które są zdefiniowane w katalogu, użyj [Get AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Add

Aby utworzyć zaufanego urzędu certyfikacji, należy użyć [New AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet i ustaw **crlDistributionPoint** atrybutu do poprawnej wartości:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Usuń

Aby usunąć zaufany urząd certyfikacji, należy użyć [AzureADTrustedCertificateAuthority Usuń](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modyfikuj

Aby zmodyfikować zaufanego urzędu certyfikacji, należy użyć [AzureADTrustedCertificateAuthority zestaw](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) polecenia cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Krok 3: Konfigurowanie odwołania

Aby odwołać certyfikat klienta, Azure Active Directory pobiera listy odwołania certyfikatów (CRL) w przypadku adresów URL przekazany jako część informacji o certyfikacie urzędu i zapisuje go w pamięci podręcznej. Ostatni publikowania sygnatury czasowej (**Data rozpoczęcia obowiązywania** właściwości) w listy CRL jest używane do zapewnienia listy CRL jest nadal ważny. Listy CRL jest okresowo przywoływany odwołać dostęp do certyfikatów, które są częścią listy.

Jeśli bardziej natychmiastowe odwoływanie jest wymagana (na przykład, jeśli użytkownik utraci urządzenie), unieważnić tokenu autoryzacji użytkownika. Unieważnianie token autoryzacji, należy ustawić **StsRefreshTokenValidFrom** pola dla tego użytkownika za pomocą programu Windows PowerShell. Należy zaktualizować **StsRefreshTokenValidFrom** pola dla każdego użytkownika, które chcesz odwołać dostęp.

Aby upewnić się, że odwołania będzie nadal występował, należy ustawić **Data rozpoczęcia obowiązywania** listy CRL na datę wypadającą po wartość ustawioną przy użyciu **StsRefreshTokenValidFrom** i upewnij się, w danym certyfikat znajduje się w listę CRL.

Poniższe kroki wchodzą w skład procesu aktualizacji i unieważnienia token autoryzacji, ustawiając **StsRefreshTokenValidFrom** pola.

**Aby skonfigurować odwołania:**

1. Połącz przy użyciu poświadczeń administracyjnych do usługi MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Pobierz bieżącą wartość StsRefreshTokensValidFrom dla użytkownika:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Skonfiguruj nową wartość StsRefreshTokensValidFrom dla równości użytkownika na bieżącą sygnaturę czasową:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Data, gdy ustawiasz musi być w przyszłości. Jeśli nie przypada w przyszłości **StsRefreshTokensValidFrom** nie ustawiono właściwości. Jeśli przypada w przyszłości **StsRefreshTokensValidFrom** jest ustawiona na bieżącą godzinę (nie Data wskazane przez polecenia Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Testowanie konfiguracji

### <a name="testing-your-certificate"></a>Testowanie certyfikatu

Jako pierwszy test konfiguracji, należy próbować zalogować się do [programu Outlook Web Access](https://outlook.office365.com) lub [usługi SharePoint Online](https://microsoft.sharepoint.com) przy użyciu usługi **w przeglądarce na urządzeniu**.

Jeśli logowanie zakończy się pomyślnie, następnie wiesz, że:

- Certyfikat użytkownika zainicjowano na urządzeniu testowym
- Usługi AD FS jest prawidłowo skonfigurowany.

### <a name="testing-office-mobile-applications"></a>Testowanie aplikacji mobilnych pakietu Office

**Aby przetestować uwierzytelniania opartego na aplikacji mobilnych pakietu Office:**

1. Na urządzeniu testowym można zainstalować aplikacji mobilnych pakietu Office (na przykład OneDrive).
3. Uruchom aplikację.
4. Wprowadź swoją nazwę użytkownika, a następnie wybierz certyfikat użytkownika, którego chcesz użyć.

Możesz się pomyślnie zalogować.

### <a name="testing-exchange-activesync-client-applications"></a>Testowanie aplikacji klienckich programu Exchange ActiveSync

Aby uzyskać dostęp programu Exchange ActiveSync (EAS) przy użyciu uwierzytelniania opartego na certyfikatach, profilu EAS zawierający certyfikat klienta musi być dostępne dla aplikacji.

Profilu EAS musi zawierać następujące informacje:

- Certyfikat użytkownika służący do uwierzytelniania

- Punkt końcowy programu EAS (np. Exchange.office365.com)

Profil programu EAS można konfigurować i umieszczone na urządzeniu w ramach użycia zarządzania urządzeniami przenośnymi (MDM) takich jak usługi Intune lub ręcznie umieszczenie certyfikatu w profilu EAS na urządzeniu.

### <a name="testing-eas-client-applications-on-android"></a>Testowanie aplikacji klienta EAS w systemie Android

**Aby przetestować uwierzytelnianie certyfikatu:**

1. Konfigurowanie profilu EAS w aplikacji, która spełnia wymagania opisane w poprzedniej sekcji.
2. Otwórz aplikację i sprawdź, czy poczta jest synchronizowany.

## <a name="next-steps"></a>Kolejne kroki

[Dodatkowe informacje dotyczące uwierzytelniania opartego na urządzeniach z systemem Android.](active-directory-certificate-based-authentication-android.md)

[Dodatkowe informacje dotyczące uwierzytelniania opartego na urządzeniach z systemem iOS.](active-directory-certificate-based-authentication-ios.md)
