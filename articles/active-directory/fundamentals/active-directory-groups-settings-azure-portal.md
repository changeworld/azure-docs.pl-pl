---
title: Jak edytować informacje o grupie za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak edytować informacje o grupie za pomocą usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a02987fdce3a15cd5d416234e3717df6d33622ec
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731346"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>Porady: edytowanie informacji o grupie za pomocą usługi Azure Active Directory

Za pomocą usługi Azure Active Directory, można edytować grupę ustawień, takich jak aktualizowanie jego nazwę, opis lub typ członkostwa.

## <a name="to-edit-your-group-settings"></a>Aby edytować ustawienia grupy
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

    **Grupy — wszystkie grupy** zostanie wyświetlona strona pokazująca wszystkie aktywne grup.

3. Z **grupy — wszystkie grupy** wpisz, jaka nazwa grupy, jak to możliwe do **wyszukiwania** pole. Na potrzeby tego artykułu, firma Microsoft poszukiwaną **zasad zarządzania urządzeniami Przenośnymi - Zachodnia** grupy.

    Wyniki wyszukiwania są wyświetlane w obszarze **wyszukiwania** polu aktualizowanie pisania większej liczby znaków.

    ![Wszystkie grupy zawierającej wyszukiwany tekst w polu wyszukiwania](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Wybierz grupę **zasady zarządzania urządzeniami Przenośnymi — zachód**, a następnie wybierz pozycję **właściwości** z **Zarządzaj** obszaru.

    ![Strona przeglądu grupy za pomocą numeru i elementy członkowskie i wyróżnioną opcją elementu członkowskiego](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Aktualizacja **ustawienia ogólne** informacje zgodnie z potrzebami, w tym:

    ![Ustawienia właściwości dla grupy](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nazwa grupy.** Edytuj nazwę istniejącej grupy.
    
    - **Opis grupy.** Edytuj istniejący opis grupy.

    - **Typ grupy.** Typ grupy nie można zmienić po jej utworzeniu. Aby zmienić **typ grupy**, należy usunąć grupę i Utwórz nową.
    
    - **Typ członkostwa.** Zmień typ członkostwa. Aby uzyskać więcej informacji na temat różnych typów dostępne członkostwa, zobacz [porady: utworzenie podstawowej grupy i dodawać członków przy użyciu portalu Azure Active Directory](active-directory-groups-create-azure-portal.md)
    
    - **Identyfikator obiektu.** Nie można zmienić Identyfikatora obiektu, ale możesz skopiować go do użycia w poleceniach programu PowerShell dla grupy. Aby uzyskać więcej informacji o używaniu poleceń cmdlet programu PowerShell, zobacz [poleceń cmdlet usługi Azure Active Directory, do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Kolejne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md)

- [Jak dodać lub usunąć członków z grupy](active-directory-groups-members-azure-portal.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Skojarzyć lub dodać subskrypcję platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
