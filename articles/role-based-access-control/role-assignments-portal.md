---
title: Dodawanie lub usuwanie przypisań ról za pomocą funkcji RBAC i witryny Azure Portal
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure użytkownikom, grupom, jednostkom usług lub tożsamościom zarządzanym przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i witryny Azure portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246359"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i witryny Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu witryny Azure portal.

Jeśli chcesz przypisać role administratora w usłudze Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Kontrola dostępu (IAM)

**Kontrola dostępu (IAM)** to blok, którego używasz do przypisywania ról w celu przyznania dostępu do zasobów platformy Azure. Jest również znany jako zarządzanie tożsamością i dostępem i pojawia się w kilku lokalizacjach w witrynie Azure portal. Poniżej przedstawiono przykład bloku kontroli dostępu (IAM) dla subskrypcji.

![Blok kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

Aby być najbardziej skuteczne z kontroli dostępu (IAM) bloku, to pomaga, jeśli można odpowiedzieć na następujące trzy pytania podczas próby przypisania roli:

1. **Kto potrzebuje dostępu?**

    Kto odnosi się do użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej. Jest to również nazywany *podmiotem zabezpieczeń*.

1. **Jakiej roli potrzebują?**

    Uprawnienia są pogrupowane w role. Można wybrać z listy kilku [wbudowanych ról](built-in-roles.md) lub można użyć własnych ról niestandardowych.

1. **Gdzie potrzebują dostępu?**

    Gdzie odnosi się do zestawu zasobów, które ma zastosowanie do dostępu. Gdzie może być grupa zarządzania, subskrypcja, grupa zasobów lub pojedynczy zasób, taki jak konto magazynu. Nazywa się to *zakresem*.

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W usłudze Azure RBAC, aby udzielić dostępu do zasobu platformy Azure, należy dodać przypisanie roli. Wykonaj następujące kroki, aby przypisać rolę.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz zakres, do którego chcesz udzielić dostępu. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób dla tego zakresu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról,** aby wyświetlić przypisania ról w tym zakresie.

    ![Karta Kontrola dostępu (IAM) i Przypisania ról](./media/role-assignments-portal/role-assignments.png)

1. Kliknij **pozycję Dodaj** > **przypisanie roli Dodaj**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Na liście **Wybierz** wybierz użytkownika, grupę, jednostkę usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń otrzymuje przypisaną rolę w wybranym zakresie.

    ![Dozowanie zapisanego przypisania roli](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz mu rolę [Właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Rola Właściciel daje użytkownikowi pełny dostęp do wszystkich zasobów w ramach subskrypcji, w tym uprawnienia do udzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, do której chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról,** aby wyświetlić przypisania ról dla tej subskrypcji.

    ![Karta Kontrola dostępu (IAM) i Przypisania ról](./media/role-assignments-portal/role-assignments.png)

1. Kliknij **pozycję Dodaj** > **przypisanie roli Dodaj**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W usłudze Azure RBAC, aby usunąć dostęp z zasobu platformy Azure, należy usunąć przypisanie roli. Wykonaj następujące kroki, aby usunąć przypisanie roli.

1. **Open Access control (IAM)** w zakresie, takim jak grupa zarządzania, subskrypcja, grupa zasobów lub zasób, do którego chcesz usunąć dostęp.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kliknij pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Jeśli zostanie wyświetlony komunikat, że nie można usunąć dziedziczonych przypisań ról, próbujesz usunąć przypisanie roli w zakresie podrzędnym. Należy otworzyć formant dostępu (IAM) w zakresie, w którym rola została przypisana i spróbuj ponownie. Szybkim sposobem otwarcia kontroli dostępu (IAM) w prawidłowym zakresie jest zajrzenie do kolumny **Zakres** i kliknięcie łącza obok **(Dziedziczone)**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Następne kroki

- [Lista przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal](role-assignments-list-portal.md)
- [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu funkcji RBAC i witryny Azure portal](quickstart-assign-role-user-portal.md)
- [Rozwiązywanie problemów z materiałami RBAC dla platformy Azure](troubleshooting.md)
- [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](../governance/management-groups/overview.md)
