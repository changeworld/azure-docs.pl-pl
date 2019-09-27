---
title: Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup, podmiotów usługi i zarządzanych tożsamości przy użyciu kontroli dostępu opartej na rolach (RBAC) i Azure Portal. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337623"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i Azure Portal

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano sposób zarządzania dostępem przy użyciu Azure Portal. Aby zarządzać dostępem do Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w programie Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać i usunąć przypisania ról, musisz mieć:

- uprawnienia `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete`, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Przegląd kontroli dostępu (IAM)

**Kontrola dostępu (IAM)** to blok służący do zarządzania dostępem do zasobów platformy Azure. Jest on również znany jako Zarządzanie tożsamościami i dostępem i pojawia się w kilku lokalizacjach w Azure Portal. Poniżej przedstawiono przykład bloku kontroli dostępu (IAM) dla subskrypcji.

![Blok kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-numbers.png)

W poniższej tabeli opisano niektóre elementy, które są używane przez program:

| # | Element | Do czego służy |
| --- | --- | --- |
| 1 | Zasób, w którym jest otwarta kontrola dostępu (IAM) | Zidentyfikuj zakres (subskrypcja w tym przykładzie) |
| 2 | Przycisk **Dodaj** | Dodawanie przypisań ról |
| 3 | **Sprawdzanie dostępu do** karty | Wyświetlanie przypisań ról dla pojedynczego użytkownika |
| 4 | Karta **przypisania ról** | Wyświetlanie przypisań ról w bieżącym zakresie |
| 5 | Karta **role** | Wyświetl wszystkie role i uprawnienia |

Aby najbardziej efektywnie korzystać z bloku kontroli dostępu (IAM), można uzyskać odpowiedzi na następujące trzy pytania podczas próby zarządzania dostępem:

1. **Kto musi mieć dostęp?**

    Kto odwołuje się do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Jest to również nazywane *podmiotem zabezpieczeń*.

1. **Jakie uprawnienia wymagają?**

    Uprawnienia są pogrupowane w role. Możesz wybrać jedną z kilku wbudowanych ról.

1. **Gdzie są potrzebne dostęp?**

    Gdzie odwołuje się do zestawu zasobów, do którego odnosi się ten dostęp. Gdzie może być grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem, takim jak konto magazynu. Jest to nazywane *zakresem*.

## <a name="open-access-control-iam"></a>Otwórz kontrolę dostępu (IAM)

Najpierw należy zdecydować, gdzie otworzyć blok kontrola dostępu (IAM). Zależy od zasobów, dla których chcesz zarządzać dostępem. Czy chcesz zarządzać dostępem do wszystkich elementów w grupie zarządzania, wszystko w ramach subskrypcji, wszystko w grupie zasobów lub pojedynczy zasób?

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

    Poniżej przedstawiono przykład bloku kontroli dostępu (IAM) dla subskrypcji. Jeśli w tym miejscu wprowadzisz jakiekolwiek zmiany kontroli dostępu, zostaną one zastosowane do całej subskrypcji.

    ![Blok kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Wyświetlanie ról i uprawnień

Definicja roli jest kolekcją uprawnień służącą do przypisywania ról. Platforma Azure ma ponad 70 [ról wbudowanych dla zasobów platformy Azure](built-in-roles.md). Wykonaj następujące kroki, aby wyświetlić dostępne role i uprawnienia.

1. Otwórz **kontrolę dostępu (IAM)** w dowolnym zakresie.

1. Kliknij kartę **role** , aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   Możesz zobaczyć liczbę użytkowników i grup przypisanych do każdej roli w bieżącym zakresie.

   ![Lista ról](./media/role-assignments-portal/roles-list.png)

1. Kliknij pojedynczą rolę, aby zobaczyć, komu przypisano tę rolę, a także wyświetlić uprawnienia do roli.

   ![Przypisania ról](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Wyświetl przypisania ról

W przypadku zarządzania dostępem należy wiedzieć, kto ma dostęp, jakie są uprawnienia i w jakim zakresie. Aby wyświetlić listę dostępu dla użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej, można zobaczyć przypisania ról.

### <a name="view-role-assignments-for-a-single-user"></a>Wyświetlanie przypisań ról dla pojedynczego użytkownika

Wykonaj następujące kroki, aby wyświetlić dostęp dla pojedynczego użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej w określonym zakresie.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w którym chcesz wyświetlić dostęp.

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/role-assignments-portal/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/role-assignments-portal/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/role-assignments-portal/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

### <a name="view-all-role-assignments-at-a-scope"></a>Wyświetl wszystkie przypisania ról w zakresie

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w którym chcesz wyświetlić dostęp.

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania ról](./media/role-assignments-portal/access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisany do tego zasobu lub Dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="add-a-role-assignment"></a>Dodawanie przypisania roli

W celu udzielenia dostępu w ramach RBAC można przypisać rolę do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Wykonaj następujące kroki, aby udzielić dostępu w różnych zakresach.

### <a name="assign-a-role-at-a-scope"></a>Przypisywanie roli w zakresie

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w którym chcesz udzielić dostępu.

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w wybranym zakresie.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz im rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełen dostęp do wszystkich zasobów w subskrypcji, w tym prawo do przydzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, do której chcesz udzielić dostępu.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="remove-role-assignments"></a>Usuwanie przypisań ról

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Wykonaj następujące kroki, aby usunąć dostęp.

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

* [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](quickstart-assign-role-user-portal.md)
* [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure](troubleshooting.md)
* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
