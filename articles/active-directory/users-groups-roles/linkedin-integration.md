---
title: Włączanie integracji z usługą LinkedIn w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak włączyć lub wyłączyć integracji z usługą LinkedIn dla aplikacji firmy Microsoft w usłudze Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/22/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 44bd03a8f710cf4b61e063688c9532dc6cce6480
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465602"
---
# <a name="linkedin-integration"></a>Integracja usługi LinkedIn

W tym artykule możesz dowiedzieć się, jak włączyć lub wyłączyć integracji z usługą LinkedIn dla dzierżawy w Centrum administracyjnym usługi Azure Active Directory (Azure AD).

> [!IMPORTANT]
> Ustawienia integracji usługi LinkedIn jest obecnie udostępniana dzierżaw usługi Azure AD. Gdy jest ona udostępniona do swojej dzierżawy, jego jest domyślnie włączona.
> 
> Wyjątki:
> * To ustawienie nie jest dostępna dla klientów korzystających z Microsoft Cloud dla instytucji rządowych USA, Microsoft Cloud w Niemczech, lub platformy Azure i usługi Office 365, obsługiwana przez firmę 21Vianet w Chinach.
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw aprowizowane w Niemczech. Należy pamiętać, że ustawienie nie jest dostępna dla klientów korzystających z programu Microsoft Cloud (Niemcy).
> * To ustawienie jest wyłączone domyślnie w przypadku dzierżaw zainicjowano obsługę administracyjną we Francji.

> Integracja działa tylko wtedy, gdy jest włączone *i* Jeśli zezwolisz użytkownikom na wyrażanie zgody na dostęp do danych firmy w ich imieniu aplikacji. Aby uzyskać informacji o ustawieniach zgody, zobacz [jak usunąć dostęp użytkownika do aplikacji](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>Włączanie lub wyłączanie integracji z usługą LinkedIn dla użytkowników w witrynie Azure portal

Można włączyć lub wyłączyć integracji z usługą LinkedIn dla całej dzierżawie lub tylko wybranych użytkowników w dzierżawie.

1. Zaloguj się do [Centrum administracyjne usługi Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego dla dzierżawy usługi Azure AD.
2. Wybierz **użytkowników**.
3. Na **użytkowników** bloku wybierz **ustawienia użytkownika**.
4. W obszarze **integracji z usługą LinkedIn**:
  * Wybierz **tak** można włączyć integracji usługi LinkedIn dla wszystkich użytkowników w dzierżawie
  * Wybierz **wybrane** można włączyć integracji usługi LinkedIn dla tylko użytkownicy w wybranej dzierżawy
  * Wybierz **nie** można wyłączyć integracji usługi LinkedIn dla wszystkich użytkowników ![integracji z usługą LinkedIn włączenie](./media/linkedin-integration/linkedin-integration.png)
5. Zapisz ustawienia w przypadku, gdy wszystko będzie gotowe, wybierając **Zapisz**.

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>Włączanie lub wyłączanie integracji z usługą LinkedIn dla użytkowników w zasadach grupy

1. Pobierz [plików pakietu Office 2016 administracyjne szablon (ADMX/ŚILS)](https://www.microsoft.com/download/details.aspx?id=49030)
2. Wyodrębnij **ADMX** pliki i skopiować je do centralnego magazynu.
3. Zarządzanie zasadami grupy Otwórz.
4. Utwórz obiekt zasad grupy z następującymi ustawieniami: **Konfiguracja użytkownika** > **Szablony administracyjne** > **Microsoft Office 2016** > **różne**  >  **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office**.
5. Wybierz **włączone** lub **wyłączone**.
  
 Stan | Efekt
------ | ------
**Enabled (Włączone)** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest włączone. Użytkownicy w organizacji mogą używać funkcje usługi LinkedIn w swoich aplikacjach pakietu Office.
 **Disabled (Wyłączone)** | **Pokaż funkcje usługi LinkedIn w aplikacjach pakietu Office** ustawienie w opcji programu Office 2016 jest wyłączona, a użytkownicy końcowi nie można zmienić tego ustawienia. Użytkownicy w organizacji nie można używać funkcji usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

Te zasady grupy dotyczy tylko aplikacji pakietu Office 2016 na komputerze lokalnym. Użytkownicy mogą zobaczyć funkcje usługi LinkedIn w karty profilu w całej usłudze Office 365, nawet jeśli wyłączenie usługi LinkedIn w swoich aplikacjach pakietu Office 2016.

## <a name="learn-more"></a>Dowiedz się więcej

* [Integracja usługi LinkedIn w Twojej organizacji](linkedin-user-consent.md)

* [Informacje usługi LinkedIn i funkcje w aplikacjach firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=850740)

* [Centrum pomocy usługi LinkedIn](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>Kolejne kroki

Użyj następującego linku, aby wyświetlić bieżące ustawienia integracji usługi LinkedIn w witrynie Azure portal:

[Wyświetl bieżące ustawienia integracji usługi LinkedIn w witrynie Azure portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)