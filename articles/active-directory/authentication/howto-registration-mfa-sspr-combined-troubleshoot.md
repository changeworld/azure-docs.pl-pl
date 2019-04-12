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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489197"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Rozwiązywanie problemów z połączone rejestracji informacje zabezpieczeń (wersja zapoznawcza)

Informacje przedstawione w tym artykule jest przeznaczona do przeprowadzają administratorów, którzy są rozwiązywania problemów zgłaszanych przez użytkowników proces rejestracji połączone.

|     |
| --- |
| Połączone zabezpieczeń informacji o rejestracji dla usługi Azure Multi-Factor Authentication i resetowania hasła usługi Azure Active Directory (Azure AD) to funkcja publicznej wersji zapoznawczej usługi Azure AD. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz temat [Dodatkowe warunki użytkowania dotyczące wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Dzienniki inspekcji

Zdarzenia zarejestrowane dla rejestracji połączone znajdują się w kategorii metod uwierzytelniania w usłudze Azure AD dzienniki inspekcji.

![Inspekcji platformy Azure AD rejestruje zdarzenia rejestracji przedstawiający interfejsu](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Poniższa tabela zawiera listę wszystkich zdarzeń inspekcji generowanych przez połączone rejestracji:

| Działanie | Stan | Przyczyna | Opis |
| --- | --- | --- | --- |
| Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające | Powodzenie | Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik zostanie pomyślnie zakończony rejestracji.|
| Użytkownik zarejestrowany wszystkie wymagane informacje zabezpieczające | Niepowodzenie | Użytkownik anulował zabezpieczeń informacji o rejestracji. | To zdarzenie występuje, gdy użytkownik anuluje rejestracji z trybu przerwania.|
| Użytkownik zarejestrowany informacje zabezpieczające | Powodzenie | Użytkownik zarejestrowany *metoda*. | To zdarzenie występuje, gdy użytkownik rejestruje poszczególne metody. *Metoda* może być wystawcy uwierzytelnienia aplikacji, telefonicznej, wiadomości E-mail, zabezpieczeń pytania, aplikacji hasła, alternatywny numer telefonu i tak dalej.| 
| Użytkownik przejrzeć informacje zabezpieczające | Powodzenie | Użytkownik pomyślnie przejrzeć informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik wybierze **wygląda dobrze** na stronie Przegląd informacji.|
| Użytkownik przejrzeć informacje zabezpieczające | Niepowodzenie | Użytkownik nie może przejrzeć informacje zabezpieczające. | To zdarzenie występuje, gdy użytkownik wybierze **wygląda dobrze** na informacje o zabezpieczeniach Przejrzyj strony, ale coś się nie powiedzie, do wewnętrznej bazy danych.|
| Użytkownik usunął informacje zabezpieczające | Powodzenie | Użytkownik usunął *metoda*. | To zdarzenie występuje, gdy użytkownik usuwa poszczególne metody. *Metoda* może być wystawcy uwierzytelnienia aplikacji, telefonicznej, wiadomości E-mail, zabezpieczeń pytania, aplikacji hasła, alternatywny numer telefonu i tak dalej.|
| Użytkownik usunął informacje zabezpieczające | Niepowodzenie | Nie można usunąć użytkownika *metoda*. | To zdarzenie występuje, gdy użytkownik próbuje usunąć metody, ale próba nie powiedzie się z jakiegoś powodu. *Metoda* może być wystawcy uwierzytelnienia aplikacji, telefonicznej, wiadomości E-mail, zabezpieczeń pytania, aplikacji hasła, alternatywny numer telefonu i tak dalej.|
| Informacje o zabezpieczeniach domyślne użytkownika zmieniła się | Powodzenie | Użytkownik zmienił zabezpieczające domyślne *metoda*. | To zdarzenie występuje, gdy użytkownik zmienia domyślną metodę. *Metoda* może być powiadomienie w aplikacji wystawcy uwierzytelnienia, kodu z mojej aplikacji authenticator lub tokenu, wywołanie + X XXXXXXXXXX, tekst, kod, aby + X XXXXXXXXX i tak dalej.|
| Informacje o zabezpieczeniach domyślne użytkownika zmieniła się | Niepowodzenie | Użytkownik nie może zmienić domyślną zabezpieczające dla *metoda*. | To zdarzenie występuje, gdy użytkownik próbuje zmienić domyślną metodę, ale próba nie powiedzie się z jakiegoś powodu. *Metoda* może być powiadomienie w aplikacji wystawcy uwierzytelnienia, kodu z mojej aplikacji authenticator lub tokenu, wywołanie + X XXXXXXXXXX, tekst, kod, aby + X XXXXXXXXX i tak dalej.|

## <a name="troubleshooting-interrupt-mode"></a>Rozwiązywanie problemów z trybu przerwania

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie widzę metod, oczekiwano się. | 1. Sprawdź, czy użytkownik ma rolę administratora usługi Azure AD. Jeśli tak, wyświetlanie różnic zasad administrator funkcji samoobsługowego resetowania HASEŁ. <br> 2. Ustal, czy użytkownik jest jest przerwane ze względu na wymuszanie rejestracji uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania HASEŁ rejestracji wymuszania. Zobacz [schemat blokowy](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) w obszarze "Łączony rejestracji modes" do określenia metody, które mają być wyświetlane. <br> 3. Określ, jak ostatnio zmieniono zasady uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania HASEŁ. Jeśli ostatnie zmiany, może upłynąć trochę czasu, następnie zaktualizowane zasady do propagowania.|

## <a name="troubleshooting-manage-mode"></a>Rozwiązywanie problemów z zarządzania w trybie

| Objaw | Kroki rozwiązywania problemów |
| --- | --- |
| Nie mam opcję, aby dodać określoną metodę. | 1. Ustal, czy metoda jest włączone dla uwierzytelniania wieloskładnikowego lub samoobsługowego resetowania HASEŁ. <br> 2. Jeśli metoda jest włączony, ponownie zapisać zasady, a następnie poczekaj 1 – 2 godzin przed testowaniem ponownie. <br> 3. Jeśli metoda jest włączona, upewnij się, że użytkownik nie zostało już skonfigurowane maksymalną liczbę tej metody, które mają zezwolenie na konfigurowanie.|

## <a name="disable-combined-registration"></a>Wyłącz rejestrację połączone

Kiedy użytkownik rejestruje numer telefonu i/lub aplikacji mobilnej w nowym połączeniu doświadczenia, nasze sygnatury usługi zestaw flag (StrongAuthenticationMethods) dla tych metod na tego użytkownika. Ta funkcjonalność umożliwia użytkownikowi przeprowadzenie uwierzytelniania wieloskładnikowego za pomocą tych metod, zawsze wtedy, gdy jest wymagane uwierzytelnianie wieloskładnikowe.

Jeśli administrator umożliwia korzystania z wersji zapoznawczej, użytkownikom zarejestrowanie się za pomocą nowego środowiska, a następnie Administrator powoduje wyłączenie korzystania z wersji zapoznawczej, użytkownicy mogą nieświadomie także zostać zarejestrowana uwierzytelniania Multi-Factor Authentication.

Jeśli użytkownik, który ma łączna Rejestracja zakończona przechodzi do bieżącej strony rejestracji resetowania haseł w [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), użytkownik jest monitowany o wykonywać uwierzytelnianie wieloskładnikowe, zanim uzyskają dostęp tę stronę. Ten krok jest oczekiwana z technicznego punktu widzenia, ale jest nowa dla osób, które zostały wcześniej zarejestrowane dla funkcji samoobsługowego resetowania HASEŁ tylko. Chociaż ten dodatkowy krok zwiększyć poziom bezpieczeństwa użytkownika, podając inny poziom zabezpieczeń, administratorów może być wycofać użytkownikom, aby były one nie będzie mógł wykonywać uwierzytelnianie wieloskładnikowe.  

### <a name="how-to-roll-back-users"></a>Jak przywrócić użytkowników

Chcąc, jako administrator zresetować ustawienia usługi Multi-Factor Authentication użytkownika, można użyć skryptu programu PowerShell podanego w następnej sekcji. Skrypt spowoduje wyczyszczenie właściwość StrongAuthenticationMethods dla aplikacji mobilnej użytkownika i/lub numer telefonu. Po uruchomieniu tego skryptu dla użytkowników muszą ponownie rejestrację dla usługi Multi-Factor Authentication, jeśli ich potrzebują. Firma Microsoft zaleca testowania wycofywania z jednego lub dwóch użytkowników przed wycofania wszystkich użytkowników.

Poniższe kroki pomoże wycofać użytkownika lub grupy użytkowników.

#### <a name="prerequisites"></a>Wymagania wstępne

1. Zainstaluj odpowiednie moduły programu Azure AD PowerShell. W oknie programu PowerShell uruchom następujące polecenia, aby zainstalować moduły:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Zapisz listę identyfikatory obiektów użytkownika na komputerze jako plik tekstowy o identyfikatorze jeden na wiersz. Zanotuj lokalizację pliku.
1. Zapisz poniższy skrypt na komputerze i zanotuj lokalizację skryptu:

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

W oknie programu PowerShell, uruchom następujące polecenie podając lokalizacje pliku skryptu i użytkownika. Wprowadź poświadczenia administratora globalnego, po wyświetleniu monitu. Skryptu zostanie wygenerowana wynik operacji aktualizacji każdego użytkownika.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Wyłącz środowisko wersji zapoznawczej

Aby wyłączyć środowisko wersji zapoznawczej dla użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny Azure portal jako administrator użytkowników.
2. Przejdź do **usługi Azure Active Directory** > **ustawienia użytkownika** > **Zarządzanie ustawieniami funkcji w wersji zapoznawczej panelu dostępu**.
3. W obszarze **użytkownicy mogą używać funkcji w wersji zapoznawczej do rejestrowania i zarządzania nimi informacje zabezpieczające**, równa selektor **Brak**, a następnie wybierz pozycję **Zapisz**.

Użytkownicy otrzymują monit nie będzie można zarejestrować za pomocą środowiska w wersji zapoznawczej.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat publicznej wersji zapoznawczej połączone rejestracji samoobsługowego resetowania hasła i usługi Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
