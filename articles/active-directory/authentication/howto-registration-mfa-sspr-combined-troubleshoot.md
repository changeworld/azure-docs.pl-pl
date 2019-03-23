---
title: Rozwiązywanie problemów z połączonych rejestracji dla usługi Azure AD SSPR oraz uwierzytelnianie wieloskładnikowe (wersja zapoznawcza) — usługi Azure Active Directory
description: Rozwiązywanie problemów z uwierzytelniania wieloskładnikowego w usłudze Azure AD i Samoobsługowe resetowanie haseł połączone rejestracji (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370469"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Rozwiązywanie problemów z połączone rejestracji informacje zabezpieczeń (wersja zapoznawcza)

Informacje zawarte w tym artykule mogą ukierunkować administratorów, którzy są Rozwiązywanie problemów ze środowiskiem połączone rejestracji zgłoszone przez ich użytkowników końcowych.

|     |
| --- |
| Połączone zabezpieczeń informacji o rejestracji dla usługi Azure Multi-Factor Authentication i Azure AD samoobsługowego resetowania haseł to funkcja publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="audit-logs"></a>Dzienniki inspekcji

Zdarzenia zarejestrowane dla rejestracji połączone znajdują się w kategorii "Metody uwierzytelniania" w usłudze Azure AD dzienniki inspekcji.

![Inspekcji platformy Azure AD rejestruje zdarzenia rejestracji przedstawiający interfejsu](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Poniższa lista zawiera wszystkie zdarzeń inspekcji generowanych przez połączone rejestracji:

| Działanie | Stan | Przyczyna | Opis |
| --- | --- | --- | --- |
| Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające | Powodzenie | Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik zostanie pomyślnie zakończony rejestracji.|
| Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające | Niepowodzenie | Użytkownik anulował zabezpieczeń informacji o rejestracji. | To zdarzenie występuje, gdy użytkownik anuluje rejestracji z trybu przerwania.|
| Użytkownik zarejestrowany informacje zabezpieczające | Powodzenie | Użytkownik zarejestrowany "method". | To zdarzenie występuje, gdy użytkownik rejestruje poszczególne metody. "Method" może być aplikacji Authenticator z telefonu, adres E-mail, pytań zabezpieczających, hasła aplikacji, alternatywny numer telefonu itp.| 
| Użytkownik przejrzeć informacje zabezpieczające | Powodzenie | Użytkownik pomyślnie przejrzeć informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik kliknie "Wygląda dobrze" na stronie Przegląd informacji.|
| Użytkownik przejrzeć informacje zabezpieczające | Niepowodzenie | Użytkownik nie może przejrzeć informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik kliknie "Wygląda dobrze" z powodu zabezpieczeń, przejrzyj stronę, ale coś nie działa w wewnętrznej bazie danych.|
| Użytkownik usunął informacje zabezpieczające | Powodzenie | Usunięto użytkownika "method". | To zdarzenie występuje, gdy użytkownik usuwa poszczególne metody. "Method" może być aplikacji Authenticator z telefonu, adres E-mail, pytań zabezpieczających, hasła aplikacji, alternatywny numer telefonu itp.|
| Użytkownik usunął informacje zabezpieczające | Niepowodzenie | Użytkownik nie może usunąć "method". | To zdarzenie występuje, gdy użytkownik próbuje usunąć metody, ale go nie powiedzie się z jakiegoś powodu. "Method" może być aplikacji Authenticator z telefonu, adres E-mail, pytań zabezpieczających, hasła aplikacji, alternatywny numer telefonu itp.|
| Informacje o zabezpieczeniach domyślne użytkownika zmieniła się | Powodzenie | Użytkownik zmienił informacje zabezpieczające domyślną "metody". | To zdarzenie występuje, gdy użytkownik zmienia jego domyślną metodę. "Method" może być powiadomienie w aplikacji wystawcy uwierzytelnienia, kodu z mojej aplikacji authenticator lub tokenu, wywołanie + X XXXXXXXXXX, tekst kodu do + X XXXXXXXXX itp.|
| Informacje o zabezpieczeniach domyślne użytkownika zmieniła się | Niepowodzenie | Użytkownik nie może zmienić domyślne informacje zabezpieczające "method". | To zdarzenie występuje, gdy użytkownik próbuje zmienić tę metodę domyślną, ale go nie powiedzie się z jakiegoś powodu. "Method" może być powiadomienie w aplikacji wystawcy uwierzytelnienia, kodu z mojej aplikacji authenticator lub tokenu, wywołanie + X XXXXXXXXXX, tekst kodu do + X XXXXXXXXX itp.|

## <a name="troubleshooting-interrupt-mode"></a>Rozwiązywanie problemów z trybu przerwania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie widzę metod, oczekiwano się. | 1. Sprawdź, czy użytkownik ma rolę administratora usługi Azure AD. Jeśli tak, należy sprawdzić różnice zasad samoobsługowego resetowania HASŁA administratora. <br> 2. Ustal, czy użytkownik jest jest przerwane ze względu na wymuszanie rejestracji uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ rejestracji wymuszania. Przegląd schematu blokowego w trybach połączone rejestracji, aby określić metody, które mają być wyświetlane. <br> 3. Określ, jak ostatnio zmieniono zasady uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. Jeśli ostatnie zmiany, jego może potrwać trochę czasu, następnie zaktualizowane zasady do propagowania.|

## <a name="troubleshooting-manage-mode"></a>Rozwiązywanie problemów z zarządzania w trybie

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie mam opcję, aby dodać określoną metodę. | 1. Ustal, czy metoda jest włączone dla uwierzytelniania Wieloskładnikowego lub samoobsługowego resetowania HASEŁ. <br> 2. Jeśli metoda jest włączony, ponownie zapisać zasady, a następnie poczekaj 1 – 2 godzin przed testowaniem ponownie. <br> 3. Jeśli metoda jest włączona, upewnij się, że użytkownik nie zostało już skonfigurowane maksymalną liczbę tej metody, które mają zezwolenie na konfigurowanie.|

## <a name="disable-combined-registration"></a>Wyłącz rejestrację połączone

Kiedy użytkownik rejestruje numer telefonu i/lub aplikacji mobilnej w nowym połączeniu doświadczenia, nasze sygnatury usługi zestaw flag (StrongAuthenticationMethods) dla tych metod na tego użytkownika. Ta funkcjonalność umożliwia użytkownikowi przeprowadzenie usługi Azure Multi-Factor Authentication (MFA) za pomocą tych metod, zawsze wtedy, gdy usługa MFA jest wymagana.

Metody, które użytkownicy rejestrują się za pośrednictwem nowego środowiska mieć ustawioną właściwość StrongAuthenticationMethods. Jeśli administrator umożliwia korzystania z wersji zapoznawczej, użytkownikom zarejestrowanie się za pomocą nowego środowiska, a następnie Administrator powoduje wyłączenie korzystania z wersji zapoznawczej, użytkownicy mogą nieświadomie można zarejestrować usługi MFA również.

Jeśli użytkownik, który zostało ukończone w połączeniu rejestracji powoduje przejście do bieżącej strony rejestracji resetowania haseł, w [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), zostanie wyświetlony monit, aby wykonać uwierzytelnianie wieloskładnikowe, przed uzyskaniem dostępu do tej strony. Ten krok jest to oczekiwane zachowanie z technicznego punktu widzenia, ale dla użytkowników, którzy zostały wcześniej zarejestrowane dla funkcji samoobsługowego resetowania HASEŁ tylko, ten krok jest nowe zachowanie. Mimo że ten dodatkowy krok zwiększyć poziom bezpieczeństwa użytkownika dzięki zapewnieniu dodatkowego poziomu zabezpieczeń, Administratorzy mogą chcieć wycofać użytkownikom, aby były one już nie może wykonać uwierzytelnianie wieloskładnikowe.  

### <a name="how-to-roll-back-users"></a>Jak przywrócić użytkowników

Jeśli jako administrator chcesz zresetować ustawienia usługi MFA użytkownika, utworzyliśmy skrypt programu PowerShell, który spowoduje wyczyszczenie właściwość StrongAuthenticationMethods dla aplikacji mobilnej użytkownika i/lub numer telefonu. Uruchomienie tego skryptu dla użytkowników oznacza, że należy ponownie zarejestrować usługi MFA, jeśli to konieczne. Zaleca się testowanie wycofywania z jednego lub dwóch użytkowników przed wycofywanie dotkniętych użytkowników.

Poniższe kroki pomogą wycofać użytkownika lub grupy użytkowników:

#### <a name="prerequisites"></a>Wymagania wstępne

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

#### <a name="rollback"></a>Wycofywanie

W oknie programu PowerShell uruchom następujące polecenie, po zaktualizowaniu wyróżnione lokalizacje. Wprowadź poświadczenia administratora globalnego, po wyświetleniu monitu. Skryptu zostanie wygenerowana wynik operacji aktualizacji każdego użytkownika.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Wyłącz wersji zapoznawczej

Aby wyłączyć środowisko wersji zapoznawczej dla użytkowników, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal jako administrator globalny lub administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory**, **ustawienia użytkownika**, **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi zabezpieczające**, ustaw selektor **Brak** i kliknij przycisk **Zapisz**.

Użytkownicy otrzymują monit nie będzie można zarejestrować za pomocą środowiska w wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat publicznej wersji zapoznawczej połączone rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
