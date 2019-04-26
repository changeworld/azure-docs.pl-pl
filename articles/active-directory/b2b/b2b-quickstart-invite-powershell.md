---
title: 'Przewodnik Szybki start: dodawanie użytkownika-gościa za pomocą programu PowerShell — Azure Active Directory | Microsoft Docs'
description: Z tego przewodnika Szybki start dowiesz się, jak za pomocą programu PowerShell wysłać zaproszenie do zewnętrznego użytkownika współpracy w usłudze Azure AD B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b9274652b7164a4aef71499912cb8b38ace29ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413910"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Szybki start: Dodawanie użytkownika-gościa przy użyciu programu PowerShell

Istnieje wiele sposobów zapraszania partnerów zewnętrznych do Twoich aplikacji i usług przy użyciu funkcji współpracy w usłudze Azure Active Directory B2B. W poprzednim przewodniku Szybki start zobaczyliśmy, jak dodawać użytkowników-gości bezpośrednio w portalu administracyjnym usługi Azure Active Directory. Użytkowników-gości — pojedynczo lub zbiorczo — możesz również dodawać przy użyciu programu PowerShell. W tym przewodniku Szybki start użyjesz polecenia New-AzureADMSInvitation, aby dodać jednego użytkownika-gościa do Twojej dzierżawy platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-the-latest-azureadpreview-module"></a>Zainstalowanie najnowszego modułu AzureADPreview
Upewnij się, że masz zainstalowaną najnowszą wersję modułu Azure AD PowerShell dla programu Graph (AzureADPreview). 

Najpierw sprawdź, które moduły zostały zainstalowane. Otwórz program PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchom następujące polecenie:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Jeśli moduł AzureADPreview nie wyświetla żadnego komunikatu informującego o tym, że istnieje nowsza wersja, wszystko jest gotowe. W przeciwnym razie — w zależności od rezultatu — wykonaj jedną z następujących czynności:

- Jeśli nie są zwracane żadne wyniki, uruchom następujące polecenie, aby zainstalować moduł AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureADPreview, ale pojawia się komunikat informujący o tym, że istnieje nowsza wersja, uruchom następujące polecenia, aby zaktualizować moduł: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Może zostać wyświetlony monit z informacją, że instalujesz moduł z niezaufanego repozytorium. Dzieje się tak w sytuacji, gdy repozytorium PSGallery nie zostało ustawione wcześniej jako zaufane. Naciśnij klawisz **Y**, aby zainstalować moduł.

### <a name="get-a-test-email-account"></a>Uzyskanie testowego konta e-mail

Potrzebujesz testowego konta e-mail, na które będzie można wysłać zaproszenie. Konto musi być z spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym konta społecznościowego z adresem w domenie gmail.com lub outlook.com.

## <a name="sign-in-to-your-tenant"></a>Logowanie się do dzierżawy

Uruchom następujące polecenie, aby połączyć się z domeną dzierżawy:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Na przykład `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po wyświetleniu monitu wprowadź poświadczenia.

## <a name="send-an-invitation"></a>Wysyłanie zaproszenia

1. Aby wysłać zaproszenie do swojego konta e-mail testów, uruchom następujące polecenie programu PowerShell (Zastąp **"Sanda"** i **sanda\@fabrikam.com** za pomocą testu konta nazwę i adres e-mail adres e-mail): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. Polecenie spowoduje wysłanie zaproszenia na określony adres e-mail. Sprawdź rezultat, który powinien wyglądać mniej więcej tak:

   ![Rezultat w programie PowerShell przedstawiający oczekiwanie na zaakceptowanie przez użytkownika](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Sprawdzanie, czy użytkownik istnieje w katalogu

1. Aby sprawdzić, czy zaproszony użytkownik został dodany do usługi Azure AD, uruchom następujące polecenie:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Sprawdź dane wyjściowe, aby upewnić się, użytkownika, możesz zaprosić jest wyświetlany na liście, główną nazwę użytkownika (UPN) w formacie *emailaddress*EXT #\@*domeny*. Na przykład *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacji, z którego są wysyłane zaproszeń.

   ![Rezultat w programie PowerShell przedstawiający dodanego użytkownika-gościa](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Testowe konto użytkownika możesz usunąć z katalogu, gdy przestanie być potrzebne. Uruchom następujące polecenie, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Na przykład: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Kolejne kroki
W tym przewodniku Szybki start zaprosiliśmy i dodaliśmy do katalogu pojedynczego użytkownika-gościa, używając programu PowerShell. Następnie dowiesz się, jak zapraszać użytkowników-gości zbiorczo za pomocą programu PowerShell.

> [!div class="nextstepaction"]
> [Samouczek: zbiorcze zapraszanie użytkowników współpracy B2B w usłudze Azure AD](tutorial-bulk-invite.md)
