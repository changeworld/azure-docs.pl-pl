---
title: Lista Odmów przydziały dla zasobów platformy Azure przy użyciu witryny Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, nazw głównych usług i zarządzanych tożsamości, na których nastąpiła odmowa dostępu do akcji określony zasób platformy Azure, w szczególności zakresach przy użyciu witryny Azure portal.
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
ms.date: 06/10/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 21ffb1a2539a2e724a91dd3b2818270a5e573ef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127487"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Lista Odmów przydziały dla zasobów platformy Azure przy użyciu witryny Azure portal

[Odmów przypisania](deny-assignments.md) Zablokuj użytkownikom możliwość wykonywania akcji na określony zasób platformy Azure, nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlenia listy Odmów przydziałów za pomocą witryny Azure portal.

> [!NOTE]
> Nie można bezpośrednio utworzyć własne Odmów przypisania. Aby uzyskać informacje na temat nie zezwoli na przydziały są tworzone, zobacz [Odmów przypisania](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu Odmów, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read` uprawnienie, który jest dostępny w większości [wbudowane role zasobów platformy Azure](built-in-roles.md).

## <a name="list-deny-assignments"></a>Lista Odmów przypisania

Wykonaj te kroki, aby wyświetlić listę Odmów przypisania w zakresie grupy subskrypcji lub zarządzania.

1. W witrynie Azure portal kliknij pozycję **wszystkich usług** i następnie **grup zarządzania** lub **subskrypcje**.

1. Kliknij grupę zarządzania lub subskrypcji, którą chcesz wyświetlić listę.

1. Kliknij przycisk **kontrola dostępu (IAM)** .

1. Kliknij przycisk **Odmów przypisania** karty (lub kliknij przycisk **widoku** przycisk w widoku Odmów kafelka przypisania).

    Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

    ![Kontrola dostępu — odmowa karta przypisania](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Aby wyświetlić dodatkowe kolumny, kliknij przycisk **Edytowanie kolumn**.

    ![Odmów przypisania — kolumn](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nazwa** | Nazwa przypisania Odmów. |
    | **Typ podmiotu zabezpieczeń** | Użytkownika, grupy, grupy zdefiniowane przez system lub jednostki usługi. |
    | **Odmowa dostępu**  | Nazwa podmiotu zabezpieczeń, który znajduje się w przypisaniu Odmów. |
    | **Identyfikator** | Unikatowy identyfikator przypisania Odmów. |
    | **Podmioty wykluczone** | Czy istnieją podmiotów zabezpieczeń, które są wykluczone z przypisania Odmów. |
    | **Nie ma zastosowania do elementów podrzędnych** | Czy przypisanie Odmów jest dziedziczone do subscopes. |
    | **System chronione** | Czy przydział Odmów jest zarządzane przez platformę Azure. Obecnie tak zawsze. |
    | **Zakres** | Grupa zarządzania, subskrypcji, grupy zasobów lub zasobu. |

1. Dodaj znacznik wyboru do wszystkich włączonych elementów, a następnie kliknij przycisk **OK** do wyświetlenia w zaznaczonych kolumnach.

## <a name="list-details-about-a-deny-assignment"></a>Wyświetlić szczegółowe informacje o przypisaniu Odmów

Wykonaj następujące kroki, aby wyświetlić dodatkowe szczegółowe informacje o przypisaniu Odmów.

1. Otwórz **Odmów przypisania** okienko zgodnie z opisem w poprzedniej sekcji.

1. Kliknij nazwę przypisania Odmów, aby otworzyć **użytkowników** bloku.

    ![Odrzucanie przypisanie — użytkowników](./media/deny-assignments-portal/deny-assignment-users.png)

    **Użytkowników** blok zawiera dwie sekcje.

    |  |  |
    | --- | --- |
    | **Odmów dotyczy przypisania**  | Podmioty zabezpieczeń, których dotyczy przypisania Odmów. |
    | **Odmów wyklucza przypisania** | Podmioty zabezpieczeń, które są wykluczone z przypisania Odmów. |

    **Zdefiniowane przez system jednostki** reprezentuje wszystkich użytkowników, grup, nazw głównych usług i zarządzanych tożsamości w katalogu usługi Azure AD.

1. Aby wyświetlić listę uprawnień, które zostaną odrzucone, kliknij przycisk **odmowa uprawnień**.

    ![Przypisanie — odmowa odrzucenie uprawnień przez użytkownika](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akcji | Opis |
    | --- | --- |
    | **Akcje**  | Odmowa operacje zarządzania. |
    | **notActions** | Operacje zarządzania wykluczone z operacji zarządzania odmowy. |
    | **Elementy DataActions**  | Odmowa operacje na danych. |
    | **NotDataActions** | Operacje na danych spod odmowa operacji danych. |

    W przykładzie pokazano na poprzednim zrzucie ekranu poniżej przedstawiono czynne uprawnienia:

    - Cały magazyn, który operacji na płaszczyźnie danych są odrzucane, z wyjątkiem operacji obliczeniowych.

1. Aby wyświetlić właściwości przypisania Odmów, kliknij przycisk **właściwości**.

    ![Zezwalaj na przypisanie — właściwości](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na **właściwości** bloku widać nazwa przypisania Odmów, identyfikator, opis i zakres. **Nie ma zastosowania do elementów podrzędnych** przełącznika wskazuje, czy Odmów przypisanie jest dziedziczone do subscopes. **System chronione** przełącznika wskazuje, czy odmówić to przypisanie jest zarządzana przez platformę Azure. Obecnie jest to **tak** we wszystkich przypadkach.

## <a name="next-steps"></a>Kolejne kroki

* [Zrozumienie Odmów przydziały dla zasobów platformy Azure](deny-assignments.md)
* [Lista Odmów przydziały dla zasobów platformy Azure przy użyciu programu Azure PowerShell](deny-assignments-powershell.md)
