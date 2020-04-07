---
title: Zgoda administratora na połączenia kont LinkedIn — Usługa Azure AD | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak włączać lub wyłączać połączenia kont integracji linkedin w aplikacjach firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54e3821d269d11397ec4f9f5833e33ac6b555abc
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755123"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrowanie połączeń kont LinkedIn w usłudze Azure Active Directory

Możesz zezwolić użytkownikom w organizacji na dostęp do swoich połączeń linkedin w niektórych aplikacjach firmy Microsoft. Żadne dane nie są udostępniane, dopóki użytkownicy nie zgodzielą się na połączenie swoich kont. Możesz zintegrować swoją organizację w [centrum administracyjnym](https://aad.portal.azure.com)usługi Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Ustawienie połączenia kont LinkedIn jest obecnie wdrażane w organizacjach usługi Azure AD. Po włączeniu do organizacji jest domyślnie włączona.
>
> Wyjątki:
>
> * To ustawienie nie jest dostępne dla klientów korzystających z usługi Microsoft Cloud for US Government, Microsoft Cloud Germany lub Azure i Office 365 obsługiwanych przez firmę 21Vianet w Chinach.
> * Ustawienie jest domyślnie wyłączone dla dzierżaw aprowizowanych w Niemczech. Należy zauważyć, że ustawienie nie jest dostępne dla klientów korzystających z usługi Microsoft Cloud Germany.
> * Ustawienie jest domyślnie wyłączone dla dzierżaw aprowizowanych we Francji.
>
> Po włączeniu połączeń kont LinkedIn dla twojej organizacji połączenia kont działają po zgodę użytkowników na aplikacje uzyskujące dostęp do danych firmy w ich imieniu. Aby uzyskać informacje o ustawieniu zgody użytkownika, zobacz [Jak usunąć dostęp użytkownika do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Włączanie połączeń kont LinkedIn w witrynie Azure portal

Połączenia kont LinkedIn można włączyć tylko dla użytkowników, do których chcesz mieć dostęp, od całej organizacji do tylko wybranych użytkowników w organizacji.

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/) przy za pomocą konta, które jest administratorem globalnym dla organizacji usługi Azure AD.
1. Wybierz pozycję **Użytkownicy**.
1. Na stronie **Użytkownicy** wybierz pozycję **Ustawienia użytkownika**.
1. W obszarze **Połączenia kont LinkedIn**zezwalaj użytkownikom na łączenie kont w celu uzyskania dostępu do połączeń LinkedIn w niektórych aplikacjach firmy Microsoft. Żadne dane nie są udostępniane, dopóki użytkownicy nie zgodzielą się na połączenie swoich kont.

    * Wybierz **tak,** aby włączyć usługę dla wszystkich użytkowników w organizacji
    * Wybierz **wybraną grupę,** aby włączyć usługę tylko dla grupy wybranych użytkowników w organizacji
    * Wybierz **nie,** aby wycofać zgodę wszystkich użytkowników w organizacji

    ![Integrowanie połączeń kont LinkedIn w organizacji](./media/linkedin-integration/linkedin-integration.png)

1. Po zakończeniu wybierz pozycję **Zapisz,** aby zapisać ustawienia.

> [!Important]
> Integracja z serwisem LinkedIn nie jest w pełni włączona dla użytkowników, dopóki nie zgodzą się na połączenie swoich kont. Po włączeniu połączeń kont dla użytkowników nie są udostępniane żadne dane.

### <a name="assign-selected-users-with-a-group"></a>Przypisywanie wybranych użytkowników z grupą

Zastąpiliśmy opcję "Wybranka", która określa listę użytkowników z opcją wyboru grupy użytkowników, dzięki czemu można włączyć możliwość łączenia kont LinkedIn i Microsoft dla jednej grupy zamiast wielu indywidualnych użytkowników. Jeśli nie masz włączonych połączeń kont LinkedIn dla wybranych użytkowników indywidualnych, nie musisz nic robić. Jeśli wcześniej włączono połączenia kont LinkedIn dla wybranych użytkowników indywidualnych, należy:

1. Pobierz bieżącą listę poszczególnych użytkowników
1. Przenoszenie aktualnie włączonych użytkowników indywidualnych do grupy
1. Użyj grupy z poprzedniej jako wybranej grupy w ustawieniu połączenia kont LinkedIn w centrum administracyjnym usługi Azure AD.

> [!NOTE]
> Nawet jeśli nie przeniesiesz aktualnie wybranych użytkowników indywidualnych do grupy, nadal będą mogli wyświetlać informacje o LinkedIn w aplikacjach firmy Microsoft.

### <a name="move-currently-selected-users-to-a-group"></a>Przenoszenie aktualnie wybranych użytkowników do grupy

1. Utwórz plik CSV użytkowników wybranych dla połączeń kont LinkedIn.
1. Zaloguj się do usługi Microsoft 365 za pomocą konta administratora.
1. Uruchom program PowerShell.
1. Zainstaluj moduł usługi Azure AD, uruchamiając`Install-Module AzureAD`
1. Uruchom następujący skrypt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Aby użyć grupy z kroku drugiego jako wybranej grupy w ustawieniu połączenia kont LinkedIn w centrum administracyjnym usługi Azure AD, zobacz [Włączanie połączeń kont LinkedIn w witrynie Azure portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Włącz połączenia kont LinkedIn za pomocą zasad grupy

1. Pobieranie [plików szablonów administracyjnych pakietu Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Wyodrębnij pliki **ADMX** i skopiuj je do sklepu centralnego.
1. Otwórz przystawkę Zarządzanie zasadami grupy.
1. Utwórz obiekt zasad grupy z następującym ustawieniem:**Szablony administracyjne** >  **konfiguracji** > użytkownika**Microsoft Office 2016** > **Różne funkcje** > **programu Show LinkedIn w aplikacjach pakietu Office**.
1. Wybierz **opcję Włączone** lub **Wyłączone**.
  
   Stan | Efekt
   ------ | ------
   **Enabled (Włączony)** | Ustawienie **Pokaż linkedin w aplikacjach pakietu Office** w opcjach pakietu Office 2016 jest włączone. Użytkownicy w organizacji mogą korzystać z funkcji LinkedIn w swoich aplikacjach pakietu Office 2016.
   **Wyłączone** | Ustawienie **Pokaż funkcje LinkedIn w aplikacjach pakietu Office** w opcjach pakietu Office 2016 jest wyłączone, a użytkownicy końcowi nie mogą zmienić tego ustawienia. Użytkownicy w organizacji nie mogą korzystać z funkcji LinkedIn w swoich aplikacjach pakietu Office 2016.

Ta zasada grupy dotyczy tylko aplikacji pakietu Office 2016 dla komputera lokalnego. Jeśli użytkownicy wyłączą linkedin w swoich aplikacjach pakietu Office 2016, nadal będą mogli zobaczyć funkcje LinkedIn w usłudze Office 365.

## <a name="next-steps"></a>Następne kroki

* [Zgoda użytkownika i udostępnianie danych dla LinkedIn](linkedin-user-consent.md)

* [Informacje i funkcje linkedin w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy LinkedIn](https://www.linkedin.com/help/linkedin)

* [Wyświetlanie bieżącego ustawienia integracji linkedin w witrynie Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
