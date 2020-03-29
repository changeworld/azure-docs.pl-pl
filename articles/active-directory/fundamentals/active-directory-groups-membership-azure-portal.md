---
title: Dodawanie lub usuwanie grupy z innej grupy — usługi Azure AD
description: Instrukcje dotyczące dodawania lub usuwania grupy z innej grupy przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423033"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Dodawanie lub usuwanie grupy z innej grupy przy użyciu usługi Azure Active Directory
Ten artykuł ułatwia dodawanie i usuwanie grupy z innej grupy przy użyciu usługi Azure Active Directory.

>[!Note]
>Jeśli próbujesz usunąć grupę nadrzędną, zobacz [Jak zaktualizować lub usunąć grupę i jej członków](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Dodawanie grupy do innej grupy
Istniejącą grupę zabezpieczeń można dodać do innej istniejącej grupy zabezpieczeń (znanej również jako grupy zagnieżdżone), tworząc grupę członkowek (podgrupę) i grupę nadrzędną. Grupa członków dziedziczy atrybuty i właściwości grupy nadrzędnej, oszczędzając czas konfiguracji.

>[!Important]
>Obecnie nie obsługujemy:<ul><li>Dodawanie grup do grupy zsynchronizowanej z lokalną usługą Active Directory.</li><li>Dodawanie grup zabezpieczeń do grup usługi Office 365.</li><li>Dodawanie grup usługi Office 365 do grup zabezpieczeń lub innych grup usługi Office 365.</li><li>Przypisywanie aplikacji do grup zagnieżdżonych.</li><li>Stosowanie licencji do grup zagnieżdżonych.</li><li>Dodawanie grup dystrybucyjnych w scenariuszach zagnieżdżania.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Aby dodać grupę jako członka innej grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Grupy**.

3. Na stronie **Grupy — wszystkie grupy** wyszukaj i wybierz grupę, która ma zostać członkiem innej grupy. W tym ćwiczeniu używamy **zasady MDM —** grupa Zachód.

    >[!Note]
    >Grupę można dodać jako członka tylko do jednej grupy naraz. Ponadto pole **Wybierz grupę** filtruje wyświetlanie na podstawie dopasowania wpisu do dowolnej części nazwy użytkownika lub urządzenia. Jednak symbole wieloznaczne nie są obsługiwane.

    ![Grupy — strona Wszystkie grupy z wybraną zasadą MDM — grupa zachodnia](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na stronie **Zasady MDM — Zachód — Członkostwo w grupach** wybierz pozycję **Członkostwo w grupach**, wybierz pozycję **Dodaj**, znajdź grupę, do której chcesz być twoją grupą, a następnie wybierz pozycję **Wybierz**. W tym ćwiczeniu używamy **zasad MDM — wszystkie** grupy organizacyjnej.

    **Zasady MDM —** grupa Zachód jest teraz członkiem zasad MDM — wszystkie grupy **organizacyjnej,** dziedzicząc wszystkie właściwości i konfigurację zasad MDM — Wszystkie grupy organizacyjnej.

    ![Tworzenie członkostwa w grupie przez dodanie grupy do innej grupy](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Przejrzyj stronę **zasady MDM — Zachodnie — członkostwo w grupach,** aby wyświetlić relację grupy i członka.

6. Aby uzyskać bardziej szczegółowy widok relacji grupy i członka, wybierz nazwę grupy **(zasady MDM — wszystkie org)** i zapoznaj się z **zasadami MDM —** szczegóły strony zachodniej.

## <a name="remove-a-group-from-another-group"></a>Usuwanie grupy z innej grupy
Istniejącą grupę zabezpieczeń można usunąć z innej grupy zabezpieczeń. Jednak usunięcie grupy usuwa również wszystkie dziedziczone atrybuty i właściwości dla jej członków.

### <a name="to-remove-a-member-group-from-another-group"></a>Aby usunąć grupę członków z innej grupy
1. Na stronie **Grupy — wszystkie grupy** wyszukaj i wybierz grupę, która ma zostać usunięta jako członek innej grupy. W tym ćwiczeniu ponownie używamy **zasady MDM —** grupa Zachód.

2. Na stronie **Omówienie zasad MDM — Przegląd zachód** wybierz pozycję **Członkostwo w grupach**.

3. Wybierz **zasady MDM — wszystkie** grupy organizacyjne z **zasady MDM — Zachód — Członkostwo w grupie,** a następnie wybierz pozycję **Usuń** z zasad MDM — szczegóły strony **zachodniej.**

    ![Strona członkostwa w grupie z informacjami o członkostwie i stronie grupy](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Dodawanie lub usuwanie członków z grupy](active-directory-groups-members-azure-portal.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Using a group to manage access to SaaS applications (Używanie grupy do zarządzania dostępem do aplikacji SaaS)](../users-groups-roles/groups-saasapps.md)

- [Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w usłudze Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
