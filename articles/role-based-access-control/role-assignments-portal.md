---
title: Zarządzanie dostępem do resoruces platformy Azure przy użyciu RBAC i witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać dostępem do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług i zarządzanych tożsamości przy użyciu kontroli dostępu opartej na rolach (RBAC) i witryny Azure portal. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e3057108ef179af2f4692c061091fbdf59f0af2
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342341"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal

[Kontrola dostępu oparta na rolach (RBAC)](overview.md) sposób zarządzania dostępem do zasobów platformy Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup, nazw głównych usług i zarządzanych tożsamości przy użyciu RBAC i witryny Azure portal.

## <a name="open-access-control-iam"></a>Otwórz z kontroli dostępu (IAM)

**Kontrola dostępu (IAM)** bloku, Zarządzanie tożsamościami i dostępem, nazywana również pojawia się w portalu. Aby wyświetlić lub zarządzanie dostępem w portalu, pierwszą rzeczą, jaką zwykle są to, otwórz blok sterowania (IAM) dostępu w zakresie, w którym chcesz wyświetlić, lub wprowadzić zmiany.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług** a następnie wybierz zakresu lub zasobu, aby wyświetlić lub zarządzać. Na przykład, możesz wybrać **grup zarządzania**, **subskrypcje**, **grup zasobów**, lub zasobu.

1. Kliknij konkretny rodzaj zasobu, który chcesz wyświetlić lub zarządzania nimi.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

    Poniżej przedstawiono przykład bloku sterowania (IAM) dostępu do subskrypcji.

    ![W bloku kontrola (IAM) dostępu do subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Wyświetlanie ról i uprawnień

Definicja roli jest kolekcją uprawnień służącą do przypisywania ról. Platforma Azure oferuje ponad 70 [wbudowane role zasobów platformy Azure](built-in-roles.md). Wykonaj następujące kroki, aby wyświetlić role i uprawnienia, które mogą być wykonywane w klastrach zarządzania i płaszczyzny danych.

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, które chcesz wyświetlać role i uprawnienia.

1. Kliknij przycisk **role** kartę, aby wyświetlić listę wszystkich ról wbudowanych i niestandardowych.

   Można wyświetlić liczbę użytkowników i grup, które są przypisane do każdej roli, w tym zakresie.

   ![Lista ról](./media/role-assignments-portal/roles-list.png)

1. Kliknij poszczególnych ról, aby zobaczyć, który został przypisany tej roli, a także wyświetlić uprawnienia roli.

   ![Przypisania ról](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Zarządzanie dostępem obejmuje ustalanie, kto ma dostęp, jakie ma uprawnienia i na jakim poziomie. Lista dostępu dla użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną Wyświetlanie przypisań ról.

### <a name="view-role-assignments-for-a-single-user"></a>Wyświetl przypisania roli dla jednego użytkownika

Wykonaj następujące kroki, aby wyświetlić dostęp dla pojedynczego użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną w określonym zakresie.

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, które chcesz wyświetlić dostępu.

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/role-assignments-portal/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/role-assignments-portal/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/role-assignments-portal/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

### <a name="view-all-role-assignments-at-a-scope"></a>Wyświetlanie wszystkich przypisań ról w zakresie

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, które chcesz wyświetlić dostępu.

1. Kliknij przycisk **przypisań ról** karty (lub kliknij przycisk **widoku** przycisku na kafelku przypisania roli w widoku) aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania roli](./media/role-assignments-portal/access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisywany specjalnie do tego zasobu albo dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="add-a-role-assignment"></a>Dodawanie przypisania roli

W funkcji RBAC Aby udzielić dostępu, możesz przypisać rolę do użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną. Wykonaj następujące kroki, aby udzielić dostępu w różnych zakresach.

### <a name="assign-a-role-at-a-scope"></a>Przypisz rolę w zakresie

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, którym chcesz udzielić dostępu.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij przycisk **Dodaj przypisanie roli** aby otworzyć okienko Dodaj przypisanie roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. W **wybierz** wybierz użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz** Aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń jest przypisana rola w wybranym zakresie.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik jest administratorem subskrypcji platformy Azure, należy przypisać je [właściciela](built-in-roles.md#owner) rolę w zakresie subskrypcji. Rola właściciela zapewnia pełny dostęp użytkownika do wszystkich zasobów w ramach subskrypcji, łącznie z prawej strony można delegować dostępu do innych osób. Te kroki są takie same jak inne przypisania roli.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, w którym chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania roli dla tej subskrypcji.

1. Kliknij przycisk **Dodaj przypisanie roli** aby otworzyć okienko Dodaj przypisanie roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. W **roli** listy rozwijanej wybierz **właściciela** roli.

1. W **wybierz** , wybierz użytkownika na liście. Jeśli nie widzisz użytkownika na liście, należy wpisać w **wybierz** pole, aby przeszukać katalog nazw wyświetlanych i adresów e-mail.

1. Kliknij przycisk **Zapisz** Aby przypisać rolę.

   Po kilku chwilach użytkownik przypisany do roli właściciela w zakresie subskrypcji.

## <a name="remove-role-assignments"></a>Usuwanie przypisań ról

Aby usunąć dostęp za pomocą kontroli dostępu opartej na rolach, usuwa się przypisanie roli. Wykonaj następujące kroki, aby usunąć dostęp.

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, które chcesz usunąć dostęp.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania roli dla tej subskrypcji.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kliknij przycisk **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. Usuń komunikat przypisania roli, wyświetlany, kliknij przycisk **tak**.

    Dziedziczonych przypisań ról nie można usunąć. Aby usunąć odziedziczone przypisanie roli, należy to zrobić w zakresie, w którym je utworzono. W **zakres** kolumny, obok **(dziedziczony)** znajduje się link umożliwiający przejście do zakresu, w którym ta rola została przypisana. Przejdź do zakresu wskazanego w tym miejscu, aby usunąć przypisanie roli.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Daj użytkownikowi dostęp do zasobów platformy Azure przy użyciu RBAC i witryny Azure portal](quickstart-assign-role-user-portal.md)
* [Samouczek: Daj użytkownikowi dostęp do zasobów platformy Azure przy użyciu RBAC i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Rozwiązywanie problemów z funkcji RBAC dla zasobów platformy Azure](troubleshooting.md)
* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/index.md)
