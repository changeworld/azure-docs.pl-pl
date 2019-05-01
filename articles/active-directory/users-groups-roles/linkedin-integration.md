---
title: Zgoda administratora dla połączenia konta usługi LinkedIn — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak włączyć lub wyłączyć połączenia konta integracji usługi LinkedIn w aplikacjach firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920262"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integracja połączenia konta usługi LinkedIn w usłudze Azure Active Directory

Możesz zezwalać użytkownikom w organizacji, aby uzyskiwać dostęp do swoich połączeń usługi LinkedIn w niektórych aplikacjach firmy Microsoft. Dane nie zostaną udostępnione, dopóki użytkownicy wyrazić zgodę na łączenie ich kont. Można zintegrować z Twojej organizacji w usłudze Azure Active Directory (Azure AD) [Centrum administracyjnego](https://aad.portal.azure.com).

> [!IMPORTANT]
> Obecnie jest wdrażana połączenia konta usługi LinkedIn, ustawiając dla organizacji usługi Azure AD. Gdy jest ona udostępniona w organizacji, jego jest domyślnie włączona.
> 
> Wyjątki:
> * To ustawienie nie jest dostępna dla klientów korzystających z Microsoft Cloud dla instytucji rządowych USA, Microsoft Cloud w Niemczech, lub platformy Azure i usługi Office 365, obsługiwana przez firmę 21Vianet w Chinach.
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw aprowizowane w Niemczech. Należy pamiętać, że ustawienie nie jest dostępna dla klientów korzystających z programu Microsoft Cloud (Niemcy).
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw zainicjowano obsługę administracyjną we Francji.
>
> Włączenie połączenia konta usługi LinkedIn dla organizacji połączenia konta będzie działać po użytkownicy wyrazić zgodę na uzyskiwanie dostępu do danych firmy w ich imieniu aplikacji. Aby uzyskać informacji o ustawieniach zgody użytkownika, zobacz [jak usunąć dostęp użytkownika do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Włącz połączenia konta usługi LinkedIn w witrynie Azure portal

Można włączyć połączenia konta usługi LinkedIn dla użytkowników, aby korzystać z całej organizacji do wybranych użytkowników w Twojej organizacji.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego w organizacji usługi Azure AD.
1. Wybierz **użytkowników**.
1. Na **użytkowników** bloku wybierz **ustawienia użytkownika**.
1. W obszarze **połączenia konta usługi LinkedIn**, Zezwalaj użytkownikom na łączenie ich kont, aby uzyskiwać dostęp do swoich połączeń usługi LinkedIn w niektórych aplikacjach firmy Microsoft. Dane nie zostaną udostępnione, dopóki użytkownicy wyrazić zgodę na łączenie ich kont.

    * Wybierz **tak** włączyć usługę dla wszystkich użytkowników w Twojej organizacji
    * Wybierz **wybrane** można włączyć tylko grupy wybranych użytkowników w Twojej organizacji
    * Wybierz **nie** Aby wycofać zgodę od wszystkich użytkowników w organizacji

    ![Integracja połączenia konta usługi LinkedIn w organizacji](./media/linkedin-integration/linkedin-integration.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** można zapisać ustawień.

> [!Important]
> Integracji z usługą LinkedIn nie włączono pełni użytkowników, dopóki nie mogą wyrazić zgodę na łączenie ich kont. Żadne dane nie jest udostępniony, po włączeniu połączenia konta użytkowników.

### <a name="assign-selected-users-with-a-group"></a>Przypisywanie wybranych użytkowników z grupy
Zastąpiliśmy opcję "Wybrane", która określa listę użytkowników z opcją do wybrania grupy użytkowników, aby włączyć możliwość łączenia z kont Microsoft i LinkedIn dla pojedynczej grupy zamiast wielu pojedynczych użytkowników. Jeśli nie masz połączenia konta usługi LinkedIn włączone dla wybranych użytkowników indywidualnych, nie trzeba podejmować żadnych działań. Jeśli zostało wcześniej włączone połączenia konta usługi LinkedIn dla wybranych użytkowników indywidualnych, należy:

1. Pobierz bieżącą listę poszczególnych użytkowników
1. Przenoszenie aktualnie włączonego poszczególnych użytkowników do grupy
1. Użyj grupy z poprzedniego jako wybraną grupę w połączenia konta usługi LinkedIn w Centrum administracyjnym usługi Azure AD.

> [!NOTE]
> Nawet wtedy, gdy zaznaczony poszczególnych użytkowników nie przenieść do innej grupy, nadal mogą zobaczyć LinkedIn informacji w aplikacjach firmy Microsoft.

### <a name="get-the-current-list-of-selected-users"></a>Pobierz bieżącą listę wybranych użytkowników

1. Zaloguj się do usługi Microsoft 365 przy użyciu konta administratora.
1. Przejdź do pozycji https://linkedinselectedusermigration.azurewebsites.net/ (Plik > Nowy > Inny). Zobaczysz listę użytkowników, którzy są wybrane do połączenia konta usługi LinkedIn.
1. Wyeksportuj listę do pliku CSV.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Przenieś zaznaczony poszczególnych użytkowników do grupy

1. Uruchamianie programu PowerShell
1. Instalowanie modułu usługi Azure AD, uruchamiając `Install-Module AzureAD`
1. Uruchom następujący skrypt:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Aby użyć grupy z kroku dwóch jako wybraną grupę w połączenia konta usługi LinkedIn w Centrum administracyjnym usługi Azure AD, zobacz [włączyć połączenia konta usługi LinkedIn w witrynie Azure portal](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Użyj zasad grupy, aby włączyć połączenia konta usługi LinkedIn

1. Pobierz [plików pakietu Office 2016 administracyjne szablon (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Wyodrębnij **ADMX** pliki i skopiować je do centralnego magazynu.
1. Zarządzanie zasadami grupy Otwórz.
1. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016** > **różne**  >  **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office**.
1. Wybierz **włączone** lub **wyłączone**.
  
   Stan | Efekt
   ------ | ------
   **Enabled (Włączone)** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest włączone. Użytkownicy w organizacji mogą używać funkcje usługi LinkedIn w swoich aplikacjach pakietu Office 2016.
   **Disabled (Wyłączone)** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest wyłączona, a użytkownicy końcowi nie można zmienić tego ustawienia. Użytkownicy w organizacji nie można używać funkcji usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

Te zasady grupy dotyczy tylko aplikacji pakietu Office 2016 na komputerze lokalnym. Jeśli użytkownicy wyłączają LinkedIn w swoich aplikacjach pakietu Office 2016, nadal mogą zobaczyć funkcje usługi LinkedIn w usłudze Office 365.

## <a name="next-steps"></a>Kolejne kroki

* [Zgoda użytkownika i danych, udostępnianie za pomocą konta LinkedIn](linkedin-user-consent.md)

* [Informacje usługi LinkedIn i funkcje w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy usługi LinkedIn](https://www.linkedin.com/help/linkedin)

* [Wyświetl bieżące ustawienia integracji usługi LinkedIn w witrynie Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
