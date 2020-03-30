---
title: Edytowanie informacji o grupie — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące edytowania informacji o grupie przy użyciu usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561876"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Edytowanie informacji o grupie przy użyciu usługi Azure Active Directory

Korzystając z usługi Azure Active Directory (Azure AD), można edytować ustawienia grupy, w tym aktualizować jej nazwę, opis lub typ członkostwa.

## <a name="to-edit-your-group-settings"></a>Aby edytować ustawienia grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Grupy**.

    Zostanie **wyświetlona** strona Grupy — wszystkie grupy z wyświetlaniem wszystkich aktywnych grup.

3. Na stronie **Grupy — wszystkie grupy** wpisz w polu **Wyszukiwania** jak najwięcej nazwy grupy. Na potrzeby tego artykułu szukamy **zasad MDM —** grupa Zachód.

    Wyniki wyszukiwania są wyświetlane w polu **Wyszukiwania,** aktualizując się podczas wpisywania większej liczby znaków.

    ![Strona Wszystkie grupy z tekstem wyszukiwania w polu Wyszukiwania](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Wybierz **zasadę MDM**grupy - Zachód , a następnie wybierz **polecenie Właściwości** z obszaru **Zarządzanie.**

    ![Strona Przegląd grupy z wyróżnioną opcją Członka i informacjami](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. W razie potrzeby zaktualizuj informacje o **ustawieniach ogólnych,** w tym:

    ![Ustawienia właściwości grupy](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nazwa grupy.** Edytuj istniejącą nazwę grupy.
    
    - **Opis grupy.** Edytuj istniejący opis grupy.

    - **Typ grupy.** Nie można zmienić typu grupy po jej utworzeniu. Aby zmienić **typ grupy,** należy usunąć grupę i utworzyć nową.
    
    - **Typ członkostwa.** Zmień typ członkostwa. Aby uzyskać więcej informacji na temat różnych dostępnych typów członkostwa, zobacz [Jak: Tworzenie grupy podstawowej i dodawanie członków za pomocą portalu Usługi Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **Identyfikator obiektu.** Nie można zmienić identyfikatora obiektu, ale można go skopiować do użycia w poleceniach programu PowerShell dla grupy. Aby uzyskać więcej informacji na temat używania poleceń cmdlet programu PowerShell, zobacz [Polecenia cmdlet usługi Azure Active Directory dotyczące konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Jak dodać lub usunąć członków z grupy](active-directory-groups-members-azure-portal.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
