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
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469686"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Zgoda na połączenia konta usługi LinkedIn dla organizacji, w usłudze Azure Active Directory

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

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Użyj witryny Azure portal, aby włączyć połączenia konta usługi LinkedIn

Można włączyć połączenia konta usługi LinkedIn dla użytkowników, aby korzystać z całej organizacji do wybranych użytkowników w Twojej organizacji.

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego w organizacji usługi Azure AD.
2. Wybierz **użytkowników**.
3. Na **użytkowników** bloku wybierz **ustawienia użytkownika**.
4. W obszarze **połączenia konta usługi LinkedIn**, Zezwalaj użytkownikom na łączenie ich kont, aby uzyskiwać dostęp do swoich połączeń usługi LinkedIn w niektórych aplikacjach firmy Microsoft. Dane nie zostaną udostępnione, dopóki użytkownicy wyrazić zgodę na łączenie ich kont.

  * Wybierz **tak** do wyrażenia zgody na usługę dla wszystkich użytkowników w organizacji
  * Wybierz **wybrane** do wyrażenia zgody przez tylko wybranych użytkowników w organizacji
  * Wybierz **nie** Aby wycofać zgodę dla użytkowników w Twojej organizacji

    ![Integracja połączenia konta usługi LinkedIn w organizacji](./media/linkedin-integration/linkedin-integration.png)

5. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** można zapisać ustawień.
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Użyj zasad grupy, aby włączyć połączenia konta usługi LinkedIn

1. Pobierz [plików pakietu Office 2016 administracyjne szablon (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Wyodrębnij **ADMX** pliki i skopiować je do centralnego magazynu.
3. Zarządzanie zasadami grupy Otwórz.
4. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016** > **różne**  >  **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office**.
5. Wybierz **włączone** lub **wyłączone**.
  
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
