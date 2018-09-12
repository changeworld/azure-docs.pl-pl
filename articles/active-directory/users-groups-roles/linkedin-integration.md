---
title: Włącz połączenia integracji z usługą LinkedIn w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak włączyć lub wyłączyć połączenia konta usługi LinkedIn dla aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392270"
---
# <a name="linkedin-account-connections"></a>Połączenia konta usługi LinkedIn

W tym artykule możesz dowiedzieć się, jak włączyć lub wyłączyć połączenia konta usługi LinkedIn dla dzierżawy w Centrum administracyjnym usługi Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Połączenia konta usługi LinkedIn, ustawienie jest obecnie udostępniana dzierżaw usługi Azure AD. Gdy jest ona udostępniona do swojej dzierżawy, jego jest domyślnie włączona. 
> 
> Wyjątki:
> * To ustawienie nie jest dostępna dla klientów korzystających z Microsoft Cloud dla instytucji rządowych USA, Microsoft Cloud w Niemczech, lub platformy Azure i usługi Office 365, obsługiwana przez firmę 21Vianet w Chinach.
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw aprowizowane w Niemczech. Należy pamiętać, że ustawienie nie jest dostępna dla klientów korzystających z programu Microsoft Cloud (Niemcy).
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw zainicjowano obsługę administracyjną we Francji.

> Integracja działa tylko wtedy, gdy jest włączone *i* Jeśli zezwolisz użytkownikom na wyrażanie zgody na dostęp do danych firmy w ich imieniu aplikacji. Aby uzyskać informacji o ustawieniach zgody, zobacz [jak usunąć dostęp użytkownika do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Włącz lub wyłącz połączenia konta usługi LinkedIn dla dzierżawy w witrynie Azure portal

Można włączyć lub wyłączyć połączenia konta usługi LinkedIn dla całej dzierżawie lub tylko wybranych użytkowników w dzierżawie.

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego dla dzierżawy usługi Azure AD.
2. Wybierz **użytkowników**.
3. Na **użytkowników** bloku wybierz **ustawienia użytkownika**.
4. W obszarze **połączenia konta usługi LinkedIn**:
  * Wybierz **tak** umożliwia połączenia konta usługi LinkedIn dla wszystkich użytkowników w dzierżawie
  * Wybierz **wybrane** umożliwiające LinkedIn konto połączenia dla tylko wybranej dzierżawy użytkowników
  * Wybierz **nie** wyłączania połączenia konta usługi LinkedIn dla wszystkich użytkowników ![połączenia konta włączenie usługi LinkedIn](./media/linkedin-integration/linkedin-integration.png)
5. Zapisz ustawienia w przypadku, gdy wszystko będzie gotowe, wybierając **Zapisz**.

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>Włącz lub wyłącz połączenia konta usługi LinkedIn dla dzierżawy za pomocą zasad grupy

1. Pobierz [plików pakietu Office 2016 administracyjne szablon (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Wyodrębnij **ADMX** pliki i skopiować je do centralnego magazynu.
3. Zarządzanie zasadami grupy Otwórz.
4. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016**  >  **Różne** > **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office**.
5. Wybierz **włączone** lub **wyłączone**.
  
 Stan | Efekt
------ | ------
**Włączone** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest włączone. Użytkownicy w organizacji mogą używać funkcje usługi LinkedIn w swoich aplikacjach pakietu Office.
 **Wyłączone** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest wyłączona, a użytkownicy końcowi nie można zmienić tego ustawienia. Użytkownicy w organizacji nie można używać funkcji usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

Te zasady grupy dotyczy tylko aplikacji pakietu Office 2016 na komputerze lokalnym. Użytkownicy mogą zobaczyć funkcje usługi LinkedIn w karty profilu w całej usłudze Office 365, nawet jeśli wyłączenie usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

## <a name="learn-more"></a>Dowiedz się więcej

* [Integracja usługi LinkedIn w Twojej organizacji](linkedin-user-consent.md)

* [Informacje usługi LinkedIn i funkcje w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy usługi LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Kolejne kroki
Użyj następującego linku, aby wyświetlić bieżących połączeń konta LinkedIn ustawienia w witrynie Azure portal:

[Konfigurowanie połączenia konta usługi LinkedIn](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 