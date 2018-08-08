---
title: Wyłącz konwergentnej rejestracji dla usługi Azure AD SSPR i uwierzytelniania Wieloskładnikowego (publiczna wersja zapoznawcza)
description: Wyłącz uwierzytelnianie wieloskładnikowe w usłudze Azure AD i Samoobsługowe resetowanie haseł rejestracji (publiczna wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 3ce08f67f001a7c43602627b9eeda3ad60f867c1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623298"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Wyłącz usługi Azure AD zbieżności rejestracji (publiczna wersja zapoznawcza)

Kiedy użytkownik rejestruje numer telefonu i/lub aplikacji mobilnej w nowym zbieżność doświadczenia, nasze sygnatury usługi zestaw flag (StrongAuthenticationMethods) dla tych metod na tego użytkownika. Ta funkcjonalność umożliwia użytkownikowi przeprowadzenie usługi Azure Multi-Factor Authentication (MFA) za pomocą tych metod, zawsze wtedy, gdy usługa MFA jest wymagana.

Metody, które użytkownicy rejestrują się za pośrednictwem nowego środowiska mieć ustawioną właściwość StrongAuthenticationMethods. To zachowanie będzie również wystąpić, gdy publicznej wersji zapoznawczej będzie dostępna. Jeśli administrator umożliwia korzystania z wersji zapoznawczej, użytkownikom zarejestrowanie się za pomocą nowego środowiska, a następnie Administrator powoduje wyłączenie korzystania z wersji zapoznawczej, użytkownicy mogą nieświadomie można zarejestrować usługi MFA również.

Jeśli użytkownik, który zostało ukończone zbieżności rejestracji powoduje przejście do bieżącej strony rejestracji samoobsługowego resetowania HASEŁ w [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), zostanie wyświetlony monit, aby wykonać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do tej strony. Ten krok jest to oczekiwane zachowanie z technicznego punktu widzenia, ale dla użytkowników, którzy zostały wcześniej zarejestrowane dla funkcji samoobsługowego resetowania HASEŁ tylko, ten krok jest nowe zachowanie. Mimo że ten dodatkowy krok zwiększyć poziom bezpieczeństwa użytkownika dzięki zapewnieniu dodatkowego poziomu zabezpieczeń, Administratorzy mogą chcieć wycofać użytkownikom, aby były one już nie może wykonać uwierzytelnianie wieloskładnikowe.  

## <a name="how-to-roll-back-users"></a>Jak przywrócić użytkowników

Jeśli jako administrator chcesz zresetować ustawienia usługi MFA użytkownika, utworzyliśmy skrypt programu PowerShell, który spowoduje wyczyszczenie właściwość StrongAuthenticationMethods dla aplikacji mobilnej użytkownika i/lub numer telefonu. Uruchomienie tego skryptu dla użytkowników oznacza, że należy ponownie zarejestrować usługi MFA, jeśli to konieczne. Zaleca się testowanie wycofywania z jednego lub dwóch użytkowników przed wycofywanie dotkniętych użytkowników.

Poniższe kroki pomogą wycofać użytkownika lub grupy użytkowników:

### <a name="pre-requisites"></a>Wymagania wstępne

1. Należy zainstalować odpowiednie moduły programu Azure AD PowerShell. W oknie programu PowerShell uruchom następujące polecenia, aby zainstalować moduły:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Zapisz listę obiektów użytkownika identyfikator i identyfikatory do komputera jako plik tekstowy o identyfikatorze jeden na wiersz. Zanotuj lokalizację pliku.
1. Zapisz poniższy skrypt na komputer i zanotuj lokalizację skryptu:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Wycofywanie

W oknie programu PowerShell uruchom następujące polecenie, po zaktualizowaniu wyróżnione lokalizacje. Wprowadź poświadczenia administratora globalnego, po wyświetleniu monitu. Skryptu zostanie wygenerowana wynik operacji aktualizacji każdego użytkownika.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Wyłącz wersji zapoznawczej

Aby wyłączyć środowisko wersji zapoznawczej dla użytkowników, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory**, **ustawienia użytkownika**, **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi zabezpieczające**, ustaw selektor **Brak** i kliknij przycisk **Zapisz**.

Użytkownicy otrzymują monit nie będzie można zarejestrować za pomocą środowiska w wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat publicznej wersji zapoznawczej konwergentnej rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication](concept-registration-mfa-sspr-converged.md)