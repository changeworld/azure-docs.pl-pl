---
title: Zmienianie ról zasobów dla pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak zmienić role zasobów dla istniejącego pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261894"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Zmienianie ról zasobów dla pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Jako menedżer pakietów dostępu można zmienić zasoby w pakiecie dostępu w dowolnym momencie, nie martwiąc się o inicjowanie obsługi administracyjnej dostępu użytkownika do nowych zasobów lub usuwanie ich dostępu z poprzednich zasobów. W tym artykule opisano sposób zmiany ról zasobów dla istniejącego pakietu dostępu.

W tym klipie wideo przedstawiono informacje na temat zmieniania pakietu dostępu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Sprawdzanie zasobów w katalogu

Jeśli chcesz dodać zasoby do pakietu dostępu, należy sprawdzić, czy zasoby, których potrzebujesz, są dostępne w katalogu. Jeśli jesteś menedżerem pakietów dostępu, nie można dodać zasobów do katalogu, nawet jeśli są ich właścicielami. Użytkownik jest ograniczony do korzystania z zasobów dostępnych w katalogu.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Katalog,** a następnie otwórz katalog.

1. W menu po lewej stronie kliknij pozycję **Zasoby,** aby wyświetlić listę zasobów w tym katalogu.

    ![Lista zasobów w katalogu](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Jeśli jesteś menedżerem pakietów dostępu i musisz dodać zasoby do katalogu, możesz poprosić właściciela katalogu o ich dodanie.

## <a name="add-resource-roles"></a>Dodawanie ról zasobów

Rola zasobu to zbiór uprawnień skojarzonych z zasobem. Sposób udostępniania zasobów dla użytkowników do żądania jest przez dodanie ról zasobów do pakietu dostępu. Można dodawać role zasobów dla grup, zespołów, aplikacji i witryn programu SharePoint.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **Role zasobów**.

1. Kliknij **pozycję Dodaj role zasobów,** aby otworzyć stronę Dodaj role zasobów, aby uzyskać dostęp do pakietu.

    ![Pakiet dostępu — dodawanie ról zasobów](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. W zależności od tego, czy chcesz dodać grupę, zespół, aplikację lub witrynę programu SharePoint, wykonaj kroki w jednej z następujących sekcji roli zasobu.

## <a name="add-a-group-or-team-resource-role"></a>Dodawanie roli zasobu grupy lub zespołu

Zarządzanie uprawnieniami może automatycznie dodawać użytkowników do grupy lub zespołu w usłudze Microsoft Teams, gdy są oni przypisywani pakietowi dostępu. 

- Gdy grupa lub zespół jest częścią pakietu dostępu, a użytkownik jest przypisany do tego pakietu dostępu, użytkownik jest dodawany do tej grupy lub zespołu, jeśli nie jest jeszcze obecny.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika są one usuwane z grupy lub zespołu, chyba że obecnie mają przypisanie do innego pakietu dostępu, który zawiera tę samą grupę lub zespół.

Możesz wybrać dowolną [grupę zabezpieczeń usługi Azure AD lub grupę usługi Office 365](../fundamentals/active-directory-groups-create-azure-portal.md). Administratorzy mogą dodawać dowolną grupę do katalogu; właściciele katalogów mogą dodawać dowolną grupę do katalogu, jeśli są właścicielami grupy. Podczas wybierania grupy należy pamiętać o następujących ograniczeniach usługi Azure AD:

- Gdy użytkownik, w tym gość, zostanie dodany jako członek do grupy lub zespołu, może zobaczyć wszystkich innych członków tej grupy lub zespołu.
- Usługa Azure AD nie może zmienić członkostwa w grupie, która została zsynchronizowana z usługi Windows Server Active Directory przy użyciu usługi Azure AD Connect lub która została utworzona w usłudze Exchange Online jako grupa dystrybucyjna.  
- Członkostwa grup dynamicznych nie można zaktualizować przez dodanie lub usunięcie członka, więc dynamiczne członkostwa w grupach nie nadają się do użytku z zarządzaniem uprawnieniami.

Aby uzyskać więcej informacji, zobacz [Porównywanie grup](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) i [grup usługi Office 365 oraz usługi Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. Na stronie **Dodawanie ról zasobów w celu uzyskania dostępu do pakietu** kliknij pozycję Grupy i **zespoły,** aby otworzyć okienko Wybierz grupy.

1. Wybierz grupy i zespoły, które chcesz uwzględnić w pakiecie dostępu.

    ![Pakiet dostępu — dodawanie ról zasobów — wybieranie grup](./media/entitlement-management-access-package-resources/group-select.png)

1. Kliknij **pozycję Wybierz**.

    Po wybraniu grupy lub zespołu kolumna **Podtyp** zawiera jeden z następujących podtypów:

    |  |  |
    | --- | --- |
    | Zabezpieczenia | Służy do udzielania dostępu do zasobów. |
    | Dystrybucja | Służy do wysyłania powiadomień do grupy osób. |
    | O365 | Grupa usługi Office 365, która nie jest włączona w usłudze Teams. Służy do współpracy między użytkownikami, zarówno wewnątrz, jak i na zewnątrz firmy. |
    | Zespół | Grupa usługi Office 365 z obsługą teams. Służy do współpracy między użytkownikami, zarówno wewnątrz, jak i na zewnątrz firmy. |

1. Na liście **Rola** wybierz **pozycję Właściciel** lub **Członek**.

    Zazwyczaj wybierasz rolę członka. Jeśli wybierzesz rolę Właściciel, która umożliwi użytkownikom dodawanie lub usuwanie innych członków lub właścicieli.

    ![Pakiet dostępu — dodawanie roli zasobu dla grupy lub zespołu](./media/entitlement-management-access-package-resources/group-role.png)

1. Kliknij przycisk **Dodaj**.

    Każdy użytkownik z istniejącymi przypisaniami do pakietu dostępu automatycznie stanie się członkami tej grupy lub zespołu po dodaniu.

## <a name="add-an-application-resource-role"></a>Dodawanie roli zasobu aplikacji

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do aplikacji korporacyjnej usługi Azure AD, w tym zarówno aplikacji SaaS, jak i aplikacji organizacji sfederowanych z usługą Azure AD, gdy użytkownikowi przypisano pakiet dostępu. W przypadku aplikacji, które integrują się z usługą Azure AD za pośrednictwem federacyjnego logowania jednokrotnego, usługa Azure AD wystawi tokeny federacyjnye dla użytkowników przypisanych do aplikacji.

Aplikacje mogą mieć wiele ról. Podczas dodawania aplikacji do pakietu dostępu, jeśli ta aplikacja ma więcej niż jedną rolę, należy określić odpowiednią rolę dla tych użytkowników. Jeśli tworzysz aplikacje, możesz dowiedzieć się więcej o tym, jak te role są dodawane do aplikacji w [jak: Konfigurowanie oświadczenia roli wystawionego w tokenie SAML dla aplikacji dla przedsiębiorstw.](../develop/active-directory-enterprise-app-role-management.md)

Gdy rola aplikacji jest częścią pakietu dostępu:

- Gdy użytkownik jest przypisany tego pakietu dostępu, użytkownik jest dodawany do tej roli aplikacji, jeśli nie jest jeszcze obecny.
- Po wygaśnięciu przypisania pakietu dostępu użytkownika, jego dostęp zostanie usunięty z aplikacji, chyba że mają przypisanie do innego pakietu dostępu, który zawiera tę rolę aplikacji.

Oto kilka kwestii dotyczących wyboru aplikacji:

- Aplikacje mogą również mieć przypisane grupy do swoich ról.  Można dodać grupę zamiast roli aplikacji w pakiecie dostępu, jednak wtedy aplikacja nie będzie widoczna dla użytkownika jako część pakietu dostępu w portalu Mój dostęp.

1. Na stronie **Dodawanie ról zasobów w celu uzyskania dostępu do pakietu** kliknij pozycję **Aplikacje,** aby otworzyć okienko Wybierz aplikacje.

1. Wybierz aplikacje, które chcesz uwzględnić w pakiecie dostępu.

    ![Pakiet dostępu — dodawanie ról zasobów — wybieranie aplikacji](./media/entitlement-management-access-package-resources/application-select.png)

1. Kliknij **pozycję Wybierz**.

1. Na liście **Rola** wybierz rolę aplikacji.

    ![Pakiet dostępu — dodawanie roli zasobu dla aplikacji](./media/entitlement-management-access-package-resources/application-role.png)

1. Kliknij przycisk **Dodaj**.

    Każdy użytkownik z istniejącymi przypisaniami do pakietu dostępu automatycznie otrzyma dostęp do tej aplikacji po jej dodaniu.

## <a name="add-a-sharepoint-site-resource-role"></a>Dodawanie roli zasobu witryny programu SharePoint

Usługa Azure AD może automatycznie przypisywać użytkownikom dostęp do witryny usługi SharePoint Online lub zbioru witryn usługi SharePoint Online po przypisaniu pakietu dostępu.

1. Na stronie **Dodawanie ról zasobów w celu uzyskania dostępu do pakietu** kliknij pozycję **Witryny programu SharePoint,** aby otworzyć okienko Wybieranie witryn usługi SharePoint Online.

1. Wybierz witryny usługi SharePoint Online, które chcesz uwzględnić w pakiecie dostępu.

    ![Pakiet dostępu — dodawanie ról zasobów — wybieranie witryn usługi SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Kliknij **pozycję Wybierz**.

1. Na liście **Rola** wybierz rolę witryny usługi SharePoint Online.

    ![Pakiet programu Access — dodawanie roli zasobu dla witryny usługi SharePoint Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Kliknij przycisk **Dodaj**.

    Każdy użytkownik z istniejącymi przypisaniami do pakietu dostępu automatycznie otrzyma dostęp do tej witryny usługi SharePoint Online po jej dodaniu.

## <a name="remove-resource-roles"></a>Usuwanie ról zasobów

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **Role zasobów**.

1. Na liście ról zasobów znajdź rolę zasobu, którą chcesz usunąć.

1. Kliknij wielokropek (**...**), a następnie kliknij pozycję **Usuń rolę zasobu**.

    Każdy użytkownik z istniejącymi przypisaniami do pakietu dostępu automatycznie odwoła dostęp do tej roli zasobu po jej usunięciu.

## <a name="when-changes-are-applied"></a>Po zastosowaniu zmian

W zarządzaniu uprawnieniami usługa Azure AD będzie przetwarzać zbiorcze zmiany przydziału i zasobów w pakietach dostępu kilka razy dziennie. Jeśli więc dokonasz przypisania lub zmienisz role zasobów pakietu dostępu, wprowadzenie tej zmiany w usłudze Azure AD może potrwać do 24 godzin, a także czas potrzebny na rozpropagowanie tych zmian w innych usługach Microsoft Online Services lub połączonych usługach SaaS. Aplikacji. Jeśli zmiana dotyczy tylko kilku obiektów, zmiana prawdopodobnie potrwa tylko kilka minut, aby zastosować w usłudze Azure AD, po czym inne składniki usługi Azure AD wykryje tę zmianę i zaktualizuj aplikacje SaaS. Jeśli zmiana dotyczy tysięcy obiektów, zmiana potrwa dłużej. Jeśli na przykład masz pakiet dostępu z 2 aplikacjami i 100 przypisaniami użytkowników i zdecydujesz się dodać rolę witryny programu SharePoint do pakietu dostępu, może wystąpić opóźnienie, dopóki wszyscy użytkownicy nie będą częścią tej roli witryny programu SharePoint. Postęp można monitorować za pośrednictwem dziennika inspekcji usługi Azure AD, dziennika inicjowania obsługi administracyjnej usługi Azure AD i dzienników inspekcji witryny programu SharePoint.

Po usunięciu członka zespołu są one również usuwane z grupy usługi Office 365. Usunięcie z funkcji czatu zespołu może być opóźnione. Aby uzyskać więcej informacji, zobacz [Członkostwo w grupie](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Jak: Konfigurowanie oświadczenia roli wystawionego w tokenie SAML dla aplikacji korporacyjnych](../develop/active-directory-enterprise-app-role-management.md)
- [Wprowadzenie do usługi SharePoint Online](/sharepoint/introduction)