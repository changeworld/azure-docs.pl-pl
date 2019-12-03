---
title: Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu kontroli dostępu opartej na rolach (RBAC) na platformie Azure i Azure Portal.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707863"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] w tym artykule opisano sposób przypisywania ról przy użyciu Azure Portal.

Jeśli musisz przypisać role administratorów w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- uprawnienia `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete`, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Przegląd kontroli dostępu (IAM)

**Kontrola dostępu (IAM)** to blok, który służy do przypisywania ról. Jest on również znany jako Zarządzanie tożsamościami i dostępem i pojawia się w kilku lokalizacjach w Azure Portal. Poniżej przedstawiono przykład bloku kontroli dostępu (IAM) dla subskrypcji.

![Blok kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

Aby najbardziej efektywnie korzystać z bloku kontroli dostępu (IAM), można uzyskać odpowiedzi na następujące trzy pytania podczas próby przypisania roli:

1. **Kto musi mieć dostęp?**

    Kto odwołuje się do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Jest to również nazywane *podmiotem zabezpieczeń*.

1. **Jaką rolę potrzebują?**

    Uprawnienia są pogrupowane w role. Możesz wybrać jedną z listy [wbudowanych ról](built-in-roles.md) lub użyć własnych ról niestandardowych.

1. **Gdzie są potrzebne dostęp?**

    Gdzie odwołuje się do zestawu zasobów, do którego odnosi się ten dostęp. Gdzie może być grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem, takim jak konto magazynu. Jest to nazywane *zakresem*.

## <a name="add-a-role-assignment"></a>Dodawanie przypisania roli

Wykonaj następujące kroki, aby przypisać rolę w różnych zakresach.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w wybranym zakresie.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz im rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełny dostęp do wszystkich zasobów w ramach subskrypcji, w tym uprawnienia do udzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, w której chcesz dodać przypisanie roli.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Wykonaj następujące kroki, aby usunąć przypisanie roli.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w której chcesz usunąć dostęp.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kliknij pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Dziedziczonych przypisań ról nie można usunąć. Aby usunąć odziedziczone przypisanie roli, należy to zrobić w zakresie, w którym je utworzono. W kolumnie **zakres** obok **(Odziedziczone)** znajduje się łącze umożliwiające przejście do zakresu, w którym ta rola została przypisana. Przejdź do zakresu wskazanego w tym miejscu, aby usunąć przypisanie roli.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](role-assignments-list-portal.md)
- [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu RBAC i Azure Portal](quickstart-assign-role-user-portal.md)
- [Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure](troubleshooting.md)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
