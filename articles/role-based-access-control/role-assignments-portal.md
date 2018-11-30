---
title: Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak zarządzać dostępem użytkowników, grup, nazw głównych usług i zarządzanych tożsamości, przy użyciu kontroli dostępu opartej na rolach (RBAC) i witryny Azure portal. Obejmuje to wyświetlanie dostępu, jego przyznawanie i usuwanie.
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
ms.openlocfilehash: 9f5d48a074f8069e243af5644f86ad3c3d8f559b
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634867"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Zarządzanie dostępem przy użyciu kontroli RBAC i witryny Azure Portal

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. W tym artykule opisano, jak zarządzać dostępu dla użytkowników, grup, nazw głównych usług i zarządzanych tożsamości przy użyciu RBAC i witryny Azure portal.

## <a name="open-access-control-iam"></a>Otwórz z kontroli dostępu (IAM)

**Kontrola dostępu (IAM)** bloku, Zarządzanie tożsamościami i dostępem, nazywana również pojawia się w portalu. Aby wyświetlić lub zarządzanie dostępem w portalu, pierwszą rzeczą, jaką zwykle są to, otwórz blok sterowania (IAM) dostępu w zakresie, w którym chcesz wyświetlić, lub wprowadzić zmiany.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług** a następnie wybierz zakresu lub zasobu, aby wyświetlić lub zarządzać. Na przykład, możesz wybrać **grup zarządzania**, **subskrypcje**, **grup zasobów**, lub zasobu.

1. Kliknij konkretny rodzaj zasobu, który chcesz wyświetlić lub zarządzania nimi.

1. Kliknij przycisk **kontrola dostępu (IAM)**.

    Poniżej przedstawiono przykład bloku sterowania (IAM) dostępu do subskrypcji.

    ![W bloku kontrola (IAM) dostępu do subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Wyświetlanie ról i uprawnień

Definicja roli jest kolekcją uprawnień służącą do przypisywania ról. Platforma Azure oferuje ponad 70 [wbudowane role](built-in-roles.md). Wykonaj następujące kroki, aby wyświetlić role i uprawnienia, które mogą być wykonywane w klastrach zarządzania i płaszczyzny danych.

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

1. Kliknij przycisk **Sprawdź dostęp** kartę.

    ![Kontrola dostępu — karcie dostęp do wyboru](./media/role-assignments-portal/access-control-check-access.png)

1. W **znaleźć** listy, wybierz typ podmiotu zabezpieczeń, które chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg do wyszukania katalog dla nazw wyświetlanych, adresów e-mail i identyfikatory obiektów.

    ![Sprawdź dostęp do listy wyboru](./media/role-assignments-portal/check-access-select.png)

1. Kliknij przycisk podmiotu zabezpieczeń, aby otworzyć **przypisania** okienka.

    ![okienka przypisania](./media/role-assignments-portal/check-access-assignments.png)

    W tym okienku widać role przypisane do wybranych podmiot zabezpieczeń oraz zakres. W przypadku dowolnego Odmów przypisania w tym zakresie lub dziedziczone do tego zakresu, zostaną one wyświetlone.

### <a name="view-all-role-assignments-at-a-scope"></a>Wyświetlanie wszystkich przypisań ról w zakresie

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, które chcesz wyświetlić dostępu.

1. Kliknij przycisk **przypisań ról** karty (lub kliknij przycisk **widoku** przycisku na kafelku przypisania roli w widoku) aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania roli](./media/role-assignments-portal/access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisywany specjalnie do tego zasobu albo dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W funkcji RBAC Aby udzielić dostępu, możesz przypisać rolę do użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną. Wykonaj następujące kroki, aby udzielić dostępu w różnych zakresach.

### <a name="assign-a-role-at-a-scope"></a>Przypisz rolę w zakresie

1. Otwórz **kontrola dostępu (IAM)** w zakresie, takich jak grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, którym chcesz udzielić dostępu.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Kliknij przycisk **Dodaj przypisanie roli** aby otworzyć okienko Dodaj przypisanie roli.

   Jeśli nie masz uprawnienia do przypisywania ról opcję Dodaj przypisanie roli zostanie wyłączona.

   ![Dodaj okienko przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. W **wybierz** wybierz użytkownika, grupy, jednostkę usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz** Aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń jest przypisana rola w wybranym zakresie.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik jest administratorem subskrypcji platformy Azure, należy przypisać je [właściciela](built-in-roles.md#owner) rolę w zakresie subskrypcji. Rola właściciela zapewnia pełny dostęp użytkownika do wszystkich zasobów w ramach subskrypcji, łącznie z prawej strony można delegować dostępu do innych osób. Te kroki są takie same jak inne przypisania roli.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług** i następnie **subskrypcje**.

1. Kliknij subskrypcję, w którym chcesz udzielić dostępu.

1. Kliknij przycisk **kontrola dostępu (IAM)**.

1. Kliknij przycisk **przypisań ról** kartę, aby wyświetlić wszystkie przypisania roli dla tej subskrypcji.

1. Kliknij przycisk **Dodaj przypisanie roli** aby otworzyć okienko Dodaj przypisanie roli.

   Jeśli nie masz uprawnienia do przypisywania ról opcję Dodaj przypisanie roli zostanie wyłączona.

   ![Dodaj okienko przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

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

* [Samouczek: Udzielanie dostępu dla użytkownika przy użyciu RBAC i witryny Azure portal](quickstart-assign-role-user-portal.md)
* [Samouczek: udzielanie dostępu użytkownikowi za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Role wbudowane](built-in-roles.md)
* [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../azure-resource-manager/management-groups-overview.md)
