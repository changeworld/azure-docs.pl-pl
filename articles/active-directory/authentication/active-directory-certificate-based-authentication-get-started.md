---
title: Uwierzytelnianie oparte na certyfikatach — usługa Azure Active Directory
description: Dowiedz się, jak skonfigurować uwierzytelnianie oparte na certyfikatach w swoim środowisku
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a6c44a8253c81b44d02351b2df9c943d9f358f8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654354"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory

Uwierzytelnianie oparte na certyfikatach umożliwia uwierzytelnianie przez usługę Azure Active Directory przy użyciu certyfikatu klienta na urządzeniu z systemem Windows, Android lub iOS podczas łączenia konta online programu Exchange z:

- Aplikacje mobilne firmy Microsoft, takie jak Microsoft Outlook i Microsoft Word
- Klienci programu Exchange ActiveSync (EAS)

Skonfigurowanie tej funkcji eliminuje konieczność wprowadzania kombinacji nazwy użytkownika i hasła do niektórych aplikacji poczty i pakietu Microsoft Office na urządzeniu przenośnym.

W tym temacie:

- Zawiera kroki konfigurowania i wykorzystywania uwierzytelniania opartego na certyfikatach dla użytkowników dzierżawców w planach usługi Office 365 Enterprise, Business, Education i US Government. Ta funkcja jest dostępna w wersji zapoznawczej w planach usługi Office 365 w Chinach, administracji rządów usa i planach federalnych rządu USA.
- Przyjęto założenie, że [infrastruktura kluczy publicznych (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) i [usługi AD FS](../hybrid/how-to-connect-fed-whatis.md) są już skonfigurowane.

## <a name="requirements"></a>Wymagania

Aby skonfigurować uwierzytelnianie oparte na certyfikatach, muszą być spełnione następujące instrukcje:

- Uwierzytelnianie oparte na certyfikatach (CBA) jest obsługiwane tylko w środowiskach federacyjnych dla aplikacji przeglądarki, klientów natywnych przy użyciu nowoczesnego uwierzytelniania (ADAL) lub bibliotek MSAL. Jedynym wyjątkiem jest Exchange Active Sync (EAS) dla exchange online (EXO), który może być używany do kont federowanych i zarządzanych.
- Główny urząd certyfikacji i wszelkie pośrednie urzędy certyfikacji muszą być skonfigurowane w usłudze Azure Active Directory.
- Każdy urząd certyfikacji musi mieć listę odwołania certyfikatów (CRL), do którego można się odwoływać za pośrednictwem internetowego adresu URL.
- W usłudze Azure Active Directory musi być skonfigurowany co najmniej jeden urząd certyfikacji. Pokrewne kroki można znaleźć w sekcji [Konfigurowanie urzędów certyfikacji.](#step-2-configure-the-certificate-authorities)
- W przypadku klientów programu Exchange ActiveSync certyfikat klienta musi mieć adres e-mail użytkownika z rutowalnym w programie Exchange online w nazwie głównej lub wartości nazwa RFC822 pola Nazwa alternatywna podmiotu. Usługa Azure Active Directory mapuje wartość RFC822 na atrybut Adres serwera proxy w katalogu.
- Urządzenie klienckie musi mieć dostęp do co najmniej jednego urzędu certyfikacji, który wystawia certyfikaty klienta.
- Certyfikat klienta do uwierzytelniania klienta musi być wystawiony do klienta.

>[!IMPORTANT]
>Maksymalny rozmiar programu CRL dla usługi Azure Active Directory do pomyślnego pobrania i pamięci podręcznej wynosi 20 MB, a czas wymagany do pobrania programu CRL nie może przekraczać 10 sekund.  Jeśli usługa Azure Active Directory nie może pobrać licencji CRL, uwierzytelnianie oparte na certyfikatach przy użyciu certyfikatów wystawionych przez odpowiedni urząd certyfikacji zakończy się niepowodzeniem. Najlepsze rozwiązania, aby upewnić się, że pliki CRL są w granicach rozmiaru są do utrzymania okresów istnienia certyfikatu w rozsądnych granicach i oczyścić wygasłe certyfikaty. 

## <a name="step-1-select-your-device-platform"></a>Krok 1: Wybierz platformę urządzeń

W pierwszym kroku, dla platformy urządzenia, na której Ci zależy, musisz przejrzeć następujące elementy:

- Obsługa aplikacji mobilnych pakietu Office
- Szczególne wymogi wdrożeniowe

Powiązane informacje istnieją dla następujących platform urządzeń:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurowanie urzędów certyfikacji

Aby skonfigurować urzędy certyfikacji w usłudze Azure Active Directory, dla każdego urzędu certyfikacji przekaż następujące elementy:

* Publiczna część certyfikatu w formacie *.cer*
* Adresy URL skierowane do Internetu, w których znajdują się listy odwołania certyfikatów (CRL)

Schemat urzędu certyfikacji wygląda następująco:

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

W przypadku konfiguracji można użyć [programu Azure Active Directory PowerShell w wersji 2:](/powershell/azure/install-adv2?view=azureadps-2.0)

1. Uruchom program Windows PowerShell z uprawnieniami administratora.
2. Zainstaluj moduł usługi Azure AD w wersji [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) lub nowszej.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Jako pierwszy krok konfiguracji należy ustanowić połączenie z dzierżawą. Jak tylko istnieje połączenie z dzierżawą, można przeglądać, dodawać, usuwać i modyfikować zaufane urzędy certyfikacji, które są zdefiniowane w katalogu.

### <a name="connect"></a>Połącz

Aby nawiązać połączenie z dzierżawą, użyj polecenia cmdlet [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

    Connect-AzureAD

### <a name="retrieve"></a>Pobrać

Aby pobrać zaufane urzędy certyfikacji, które są zdefiniowane w katalogu, należy użyć polecenia cmdlet [omiodność get-AzureADTrustedCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Dodaj

Aby utworzyć zaufany urząd certyfikacji, użyj polecenia cmdlet [new-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) i ustaw atrybut **crlDistributionPoint** na poprawną wartość:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Remove

Aby usunąć zaufany urząd certyfikacji, użyj polecenia cmdlet [o autoryzacji AzureADTrustedCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modyfikowanie

Aby zmodyfikować zaufany urząd certyfikacji, należy użyć polecenia cmdlet autoryzacji autoryzacji certyfikatu Autoryzacja:To modify a trusted certificate authority, use the [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Krok 3: Konfigurowanie odwołania

Aby odwołać certyfikat klienta, usługa Azure Active Directory pobiera listę odwołania certyfikatów (CRL) z adresów URL przekazanych jako część informacji o urzędzie certyfikatu i buforuje ją. Ostatni sygnatura czasowa publikowania **(właściwość Data wejścia w życie)** w programie CRL jest używana w celu zapewnienia, że list CRL jest nadal prawidłowy. Do listy CRL okresowo odwołuje się dostęp do certyfikatów, które są częścią listy.

Jeśli wymagane jest bardziej natychmiastowe odwołanie (na przykład, jeśli użytkownik utraci urządzenie), token autoryzacji użytkownika może zostać unieważniony. Aby unieważnić token autoryzacji, ustaw pole **StsRefreshTokenValidFrom** dla tego konkretnego użytkownika przy użyciu programu Windows PowerShell. Należy zaktualizować **pole StsRefreshTokenValidFrom** dla każdego użytkownika, dla którego chcesz odwołać dostęp.

Aby upewnić się, że odwołanie będzie się powtarzać, należy ustawić **datę wejścia w życie** programu CRL na datę po wartości ustawionej przez **StsRefreshTokenValidFrom** i upewnić się, że dany certyfikat znajduje się w programie CRL.

Poniższe kroki przedstawiają proces aktualizowania i unieważniania tokenu autoryzacji przez ustawienie pola **StsRefreshTokenValidFrom.**

**Aby skonfigurować odwołanie:**

1. Połącz się z poświadczeniami administratora usługi MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Pobierz bieżącą wartość StsRefreshTokensValidFrom dla użytkownika:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Skonfiguruj nową wartość StsRefreshTokensValidFrom dla użytkownika równą bieżącej sygnatury czasowej:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Ustawiona data musi być w przyszłości. Jeśli data nie jest w przyszłości, **StsRefreshTokensValidFrom** właściwość nie jest ustawiona. Jeśli data jest w przyszłości, **StsRefreshTokensValidFrom** jest ustawiona na bieżącą godzinę (nie datę wskazaną przez polecenie Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Przetestuj swoją konfigurację

### <a name="testing-your-certificate"></a>Testowanie certyfikatu

Jako pierwszy test konfiguracji należy spróbować zalogować się do [programu Outlook Web Access](https://outlook.office365.com) lub usługi [SharePoint Online](https://microsoft.sharepoint.com) przy użyciu przeglądarki na **urządzeniu**.

Jeśli logowanie się powiedzie, to wiesz, że:

- Certyfikat użytkownika został aprowizny na urządzeniu testowym
- Usługi AD FS są poprawnie skonfigurowane

### <a name="testing-office-mobile-applications"></a>Testowanie aplikacji mobilnych pakietu Office

**Aby przetestować uwierzytelnianie oparte na certyfikatach w aplikacji mobilnej pakietu Office:**

1. Na urządzeniu testowym zainstaluj aplikację mobilną pakietu Office (na przykład OneDrive).
3. Uruchom aplikację.
4. Wprowadź nazwę użytkownika, a następnie wybierz certyfikat użytkownika, którego chcesz użyć.

Powinieneś zostać pomyślnie zalogowany.

### <a name="testing-exchange-activesync-client-applications"></a>Testowanie aplikacji klienckich programu Exchange ActiveSync

Aby uzyskać dostęp do programu Exchange ActiveSync (EAS) za pomocą uwierzytelniania opartego na certyfikatach, dla aplikacji musi być dostępny profil EAS zawierający certyfikat klienta.

Profil EAS musi zawierać następujące informacje:

- Certyfikat użytkownika używany do uwierzytelniania

- Punkt końcowy EAS (na przykład outlook.office365.com)

Profil EAS można skonfigurować i umieścić na urządzeniu za pomocą funkcji zarządzania urządzeniami przenośnymi (MDM), takich jak usługa Intune, lub ręcznie umieszczając certyfikat w profilu EAS na urządzeniu.

### <a name="testing-eas-client-applications-on-android"></a>Testowanie aplikacji klienckich EAS na Androida

**Aby przetestować uwierzytelnianie certyfikatu:**

1. Skonfiguruj profil EAS w aplikacji, który spełnia wymagania opisane w poprzedniej sekcji.
2. Otwórz aplikację i sprawdź, czy poczta jest synchronizowane.

## <a name="next-steps"></a>Następne kroki

[Dodatkowe informacje dotyczące uwierzytelniania opartego na certyfikatach na urządzeniach z systemem Android.](active-directory-certificate-based-authentication-android.md)

[Dodatkowe informacje o uwierzytelnianiu opartym na certyfikatach na urządzeniach z systemem iOS.](active-directory-certificate-based-authentication-ios.md)
