---
title: Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal
description: Dowiedz się, jak określić, które zasoby użytkownicy, grupy, nazwy główne usług lub tożsamości zarządzane są dostępne do korzystania z kontroli dostępu opartej na rolach (RBAC) na platformie Azure i Azure Portal.
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
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c265e03cfea2ebe8bbe55a63ade04bffd06360e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462243"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Wyświetlanie listy przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] w tym artykule opisano sposób wyświetlania przypisań ról przy użyciu Azure Portal.

## <a name="list-role-assignments-for-a-user-or-group"></a>Wyświetl listę przypisań ról dla użytkownika lub grupy

Najprostszym sposobem wyświetlenia ról przypisanych do użytkownika lub grupy w ramach subskrypcji jest użycie okienka **zasoby platformy Azure** .

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz pozycję **Użytkownicy** lub **grupy**.

1. Kliknij użytkownika lub grupę, dla której chcesz wyświetlić listę przypisań ról.

1. Kliknij pozycję **zasoby platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranego użytkownika lub grupy w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla użytkownika](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Aby zmienić subskrypcję, kliknij listę **subskrypcje** .

## <a name="list-role-assignments-at-a-scope"></a>Wyświetl listę przypisań ról w zakresie

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

   ![Kontrola dostępu — karta przypisania ról](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na karcie przypisania roli można zobaczyć, kto ma dostęp w tym zakresie. Należy zauważyć, że niektóre role należą do zakresu **tego zasobu**, a inne są **dziedziczone** z innego zakresu. Dostęp jest przypisany do tego zasobu lub Dziedziczony z przypisania do zakresu nadrzędnego.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Wyświetlanie listy przypisań ról dla użytkownika w zakresie

Aby wyświetlić listę dostępu dla użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej, należy wyświetlić listę przypisań ról. Wykonaj następujące kroki, aby wyświetlić listę przypisań ról dla pojedynczego użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej w określonym zakresie.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/role-assignments-list-portal/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/role-assignments-list-portal/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Wyświetlanie listy przypisań ról dla tożsamości zarządzanej przypisanej przez system

1. W Azure Portal Otwórz przypisaną przez system tożsamość zarządzaną.

1. W menu po lewej stronie kliknij pozycję **tożsamość**.

    ![Tożsamość zarządzana przypisana przez system](./media/role-assignments-list-portal/identity-system-assigned.png)

1. W obszarze **przypisania ról**kliknij pozycję **Pokaż role RBAC platformy Azure przypisane do tej tożsamości zarządzanej**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez system w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Wyświetl listę przypisań ról dla tożsamości zarządzanej przypisanej przez użytkownika

1. W Azure Portal Otwórz przypisaną przez system tożsamość zarządzaną.

1. Kliknij pozycję **zasoby platformy Azure**.

    Zostanie wyświetlona lista ról przypisanych do wybranej tożsamości zarządzanej przypisanej przez użytkownika w różnych zakresach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów lub zasób. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcje** .

## <a name="next-steps"></a>Następne kroki

- [Dodawanie lub usuwanie przypisań ról przy użyciu funkcji RBAC platformy Azure i Azure Portal](role-assignments-portal.md)
- [Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure](troubleshooting.md)
