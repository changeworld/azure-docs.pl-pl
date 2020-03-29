---
title: Lista przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal
description: Dowiedz się, jak określić, do jakich zasobów użytkownicy, grupy, jednostki usługi lub tożsamości zarządzane mają dostęp do przy użyciu kontroli dostępu opartej na rolach platformy Azure (RBAC) i witryny Azure portal.
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062238"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lista przypisań ról przy użyciu usługi Azure RBAC i witryny Azure portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]W tym artykule opisano sposób listy przypisań ról przy użyciu witryny Azure Portal.

> [!NOTE]
> Jeśli twoja organizacja zleciła funkcje zarządzania na zewnątrz dostawcy usług, który korzysta z [usługi Azure zarządzanie zasobami delegowanymi,](../lighthouse/concepts/azure-delegated-resource-management.md)przypisania ról autoryzowane przez tego dostawcę usług nie będą wyświetlane w tym miejscu.

## <a name="list-role-assignments-for-a-user-or-group"></a>Wyświetlanie przypisań ról dla użytkownika lub grupy

Najprostszym sposobem, aby wyświetlić role przypisane do użytkownika lub grupy w ramach subskrypcji jest użycie okienka **zasobów platformy Azure.**

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz pozycję **Użytkownicy** lub **grupy**.

1. Kliknij użytkownika lub grupę, dla której chcesz wyświetlić listę przypisań ról.

1. Kliknij pozycję **Zasoby platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranego użytkownika lub grupy w różnych zakresach, takich jak grupa zarządzania, subskrypcja, grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla użytkownika](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Aby zmienić subskrypcję, kliknij listę **Subskrypcje.**

## <a name="list-owners-of-a-subscription"></a>Lista właścicieli subskrypcji

Użytkownicy, którzy zostali przypisani do roli [Właściciel](built-in-roles.md#owner) subskrypcji, mogą zarządzać wszystkim w ramach subskrypcji. Wykonaj następujące kroki, aby wyświetlić listę właścicieli subskrypcji.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, którą chcesz wyświetlić na liście właścicieli.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Przewiń do sekcji **Właściciele,** aby wyświetlić wszystkich użytkowników, którzy zostali przypisani roli Właściciel dla tej subskrypcji.

   ![Kontrola dostępu do subskrypcji — karta Przypisania ról](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Wyświetlanie przypisań ról w zakresie

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz zakres. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról,** aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta Przypisania ról](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na karcie Przypisania ról można sprawdzić, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Program Access jest przypisywany specjalnie do tego zasobu lub dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Wyświetlanie przypisań ról dla użytkownika w zakresie

Aby wyświetlić listę dostępu dla użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej, należy wyświetlić listę ich przypisań ról. Wykonaj następujące kroki, aby wyświetlić listę przypisań ról dla pojedynczego użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w określonym zakresie.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie wybierz zakres. Na przykład można wybrać **grupy zarządzania,** subskrypcje, **grupy zasobów**lub **zasób.**

1. Kliknij określony zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/role-assignments-list-portal/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/role-assignments-list-portal/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Wyświetlanie przypisań ról dla tożsamości zarządzanej przypisanej do systemu

1. W witrynie Azure portal otwórz tożsamość zarządzaną przypisaną do systemu.

1. W menu po lewej stronie kliknij pozycję **Tożsamość**.

    ![Tożsamość zarządzana przypisana do systemu](./media/role-assignments-list-portal/identity-system-assigned.png)

1. W obszarze **Przypisania ról**kliknij pozycję **Pokaż role usługi Azure RBAC przypisane do tej tożsamości zarządzanej**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej do systemu w różnych zakresach, takich jak grupa zarządzania, subskrypcja, grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Wyświetlanie przypisań ról dla tożsamości zarządzanej przypisanej przez użytkownika

1. W witrynie Azure portal otwórz tożsamość zarządzaną przypisaną przez użytkownika.

1. Kliknij pozycję **Zasoby platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez użytkownika w różnych zakresach, takich jak grupa zarządzania, subskrypcja, grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **Subskrypcje.**

## <a name="list-number-of-role-assignments"></a>Wyświetlanie liczby przypisań ról

W każdej subskrypcji może być maksymalnie **2000** przypisań ról. Aby ułatwić śledzenie tego limitu, karta **Przypisania ról** zawiera wykres z listą liczby przypisań ról dla bieżącej subskrypcji.

![Kontrola dostępu — liczba przypisań ról](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Jeśli zbliżasz się do maksymalnej liczby i spróbujesz dodać więcej przypisań ról, zobaczysz ostrzeżenie w okienku **Dodawanie przypisania roli.** Aby uzyskać sposoby zmniejszenia liczby przypisań ról, zobacz [Rozwiązywanie problemów z programem Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Kontrola dostępu — ostrzeżenie o przypisywanie ról](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu usługi Azure RBAC i witryny Azure Portal](role-assignments-portal.md)
- [Rozwiązywanie problemów z materiałami RBAC dla platformy Azure](troubleshooting.md)
