---
title: Samouczek dotyczący zbiorczego zapraszania użytkowników współpracy usługi B2B — Azure Active Directory | Microsoft Docs
description: Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do zewnętrznych użytkowników współpracy w usłudze Azure AD B2B za pomocą programu PowerShell i pliku CSV.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a30281012d28489a40e3366585164628d87c220
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60355288"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Samouczek: zbiorcze zapraszanie użytkowników współpracy B2B w usłudze Azure AD

Jeśli używasz funkcji współpracy B2B w usłudze Azure Active Directory (Azure AD) do pracy z partnerami zewnętrznymi, możesz równocześnie zapraszać wielu użytkowników-gości do swojej organizacji. Z tego samouczka dowiesz się, jak wysyłać zaproszenia zbiorcze do użytkowników zewnętrznych za pomocą programu PowerShell. W szczególności należy wykonać następujące czynności:

> [!div class="checklist"]
> * Przygotowanie pliku wartości rozdzielanych przecinkami (CSV) z informacjami o użytkownikach
> * Uruchomienie skryptu programu PowerShell w celu wysłania zaproszeń
> * Sprawdzenie, czy użytkownicy zostali dodani do katalogu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="install-the-latest-azureadpreview-module"></a>Zainstalowanie najnowszego modułu AzureADPreview
Upewnij się, że masz zainstalowaną najnowszą wersję modułu Azure AD PowerShell dla programu Graph (AzureADPreview). 

Najpierw sprawdź, które moduły zostały zainstalowane. Otwórz program Windows PowerShell jako użytkownik z podwyższonym poziomem uprawnień (Uruchom jako administrator) i uruchom następujące polecenie:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

W zależności od rezultatu wykonaj jedną z następujących czynności:

- Jeśli żadne wyniki nie zostaną zwrócone, uruchom następujące polecenie, aby zainstalować moduł AzureADPreview:
  
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

### <a name="get-test-email-accounts"></a>Uzyskiwanie testowych kont e-mail

Potrzebujesz co najmniej dwóch testowych kont e-mail, na które będzie można wysłać zaproszenia. Konta muszą być spoza Twojej organizacji. Możesz użyć dowolnego typu konta, w tym kont społecznościowych z adresem w domenie gmail.com lub outlook.com.

## <a name="prepare-the-csv-file"></a>Przygotowywanie pliku CSV

W programie Microsoft Excel utwórz plik CSV zawierający listę nazw użytkownika i adresów e-mail zapraszanych osób. Upewnij się, że nagłówki kolumn **Name** i **InvitedUserEmailAddress** zostały uwzględnione. 

Na przykład utwórz arkusz w następującym formacie:


![Dane wyjściowe w programie PowerShell przedstawiające oczekiwanie na zaakceptowanie przez użytkownika](media/tutorial-bulk-invite/AddUsersExcel.png)

Zapisz plik jako **C:\BulkInvite\Invitations.csv**. 

Jeśli nie masz programu Excel, możesz utworzyć plik CSV w dowolnym edytorze tekstów, takim jak Notatnik. Oddziel poszczególne wartości przecinkami, a poszczególne wiersze znakami nowego wiersza. 

## <a name="sign-in-to-your-tenant"></a>Logowanie do dzierżawy

Uruchom następujące polecenie, aby połączyć się z domeną dzierżawy:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Na przykład `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Po wyświetleniu monitu wprowadź poświadczenia.

## <a name="send-bulk-invitations"></a>Zbiorcze wysyłanie zaproszeń

Aby wysłać zaproszenia, uruchom następujący skrypt programu PowerShell (gdzie **c:\bulkinvite\invitations.csv** to ścieżka do pliku CSV): 

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv
   
$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   
$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."
   
foreach ($email in $invitations) 
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.azure.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```
Skrypt wysyła zaproszenie na adresy e-mail podane w pliku Invitations.csv. Dla każdego użytkownika powinny zostać wyświetlone dane wyjściowe podobne do następujących:

![Dane wyjściowe w programie PowerShell przedstawiające oczekiwanie na zaakceptowanie przez użytkownika](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Sprawdzanie, czy użytkownicy istnieją w katalogu

Aby sprawdzić, czy zaproszeni użytkownicy zostali dodani do usługi Azure AD, uruchom następujące polecenie:
```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```
Powinien zostać wyświetlony, użytkownicy, którzy zaproszeni wyświetlany na liście, główną nazwę użytkownika (UPN) w formacie *emailaddress*EXT #\@*domeny*. Na przykład *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, gdzie contoso.onmicrosoft.com jest organizacji, z którego są wysyłane zaproszeń.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Testowe konta użytkowników możesz usunąć z katalogu, gdy nie będą już potrzebne. Uruchom następujące polecenie, aby usunąć konto użytkownika:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Na przykład: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wysłano zaproszenia zbiorcze do użytkowników-gości spoza organizacji. Następnie dowiesz się, jak działa proces realizacji zaproszenia.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat procesu realizacji zaproszenia do współpracy B2B w usłudze Azure AD](redemption-experience.md)

