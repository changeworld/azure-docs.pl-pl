---
title: Lista odmów przydziałów dla zasobów platformy Azure za pomocą witryny Azure portal
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, podmiotów usługi i tożsamości zarządzanych, którym odmówiono dostępu do określonych akcji zasobów platformy Azure w określonych zakresach przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 4db76e5c6191457346ca1f95678cf73843334d3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137419"
---
# <a name="list-deny-assignments-for-azure-resources-using-the-azure-portal"></a>Lista odmów przydziałów dla zasobów platformy Azure przy użyciu witryny Azure portal

[Odmów przydziałów](deny-assignments.md) zablokować użytkownikom wykonywanie określonych akcji zasobów platformy Azure, nawet jeśli przypisanie roli udziela im dostępu. W tym artykule opisano sposób listy przypisań odmowy przy użyciu witryny Azure Portal.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań odmowy. Aby uzyskać informacje o sposobie tworzenia przypisań odmowy, zobacz [Odrzucanie przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, musisz mieć:

- `Microsoft.Authorization/denyAssignments/read`uprawnienia, które są zawarte w większości [wbudowanych ról zasobów platformy Azure.](built-in-roles.md)

## <a name="list-deny-assignments"></a>Lista przypisań odmowy

Wykonaj następujące kroki, aby wyświetlić listę odmów przypisania w zakresie subskrypcji lub grupy zarządzania.

1. W witrynie Azure portal kliknij pozycję **Wszystkie usługi,** a następnie **pozycję Grupy zarządzania** lub **Subskrypcje**.

1. Kliknij grupę zarządzania lub subskrypcję, którą chcesz wyświetlić.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Odmów przydziałów** (lub kliknij przycisk **Wyświetl** na kafelku Wyświetl przypisania odmów).

    Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

    ![Kontrola dostępu — karta Odmów przydziałów](./media/deny-assignments-portal/access-control-deny-assignments.png)

1. Aby wyświetlić dodatkowe kolumny, kliknij pozycję **Edytuj kolumny**.

    ![Odmów przydziałów — kolumny](./media/deny-assignments-portal/deny-assignments-columns.png)

    |  |  |
    | --- | --- |
    | **Nazwa** | Nazwa przypisania odmowy. |
    | **Typ nazwy głównej** | Użytkownik, grupa, grupa zdefiniowana przez system lub podmiot usługi. |
    | **Odmowa**  | Nazwa podmiotu zabezpieczeń, który znajduje się w przypisaniu odmowy. |
    | **Identyfikator** | Unikatowy identyfikator przypisania odmowy. |
    | **Wykluczone zleceniodawców** | Czy istnieją podmioty zabezpieczeń, które są wykluczone z przypisania odmowy. |
    | **Nie dotyczy dzieci** | Czy przypisanie odmowy jest dziedziczone do podzakresów. |
    | **Ochrona systemu** | Czy przypisanie odmowy jest zarządzane przez platformę Azure. Obecnie zawsze tak. |
    | **Zakres** | Grupa zarządzania, subskrypcja, grupa zasobów lub zasób. |

1. Dodaj znacznik wyboru do dowolnego z włączonych elementów, a następnie kliknij przycisk **OK,** aby wyświetlić wybrane kolumny.

## <a name="list-details-about-a-deny-assignment"></a>Wyświetlanie szczegółów dotyczących przypisania odmowy

Wykonaj następujące kroki, aby wyświetlić listę dodatkowych szczegółów dotyczących przypisania odmowy.

1. Otwórz okienko **Odmów przypisania** zgodnie z opisem w poprzedniej sekcji.

1. Kliknij nazwę przypisania odmowy, aby otworzyć blok **Użytkownicy.**

    ![Odmowa przypisania — użytkownicy](./media/deny-assignments-portal/deny-assignment-users.png)

    **Blok Użytkownicy** zawiera następujące dwie sekcje.

    |  |  |
    | --- | --- |
    | **Odmowa przypisania ma zastosowanie do**  | Podmioty zabezpieczeń, których dotyczy przypisanie odmowy. |
    | **Odmów przypisania wyklucza** | Podmioty zabezpieczeń, które są wykluczone z przypisania odmowy. |

    **Podmiot zabezpieczeń zdefiniowany przez system** reprezentuje wszystkich użytkowników, grupy, jednostki usługi i tożsamości zarządzane w katalogu usługi Azure AD.

1. Aby wyświetlić listę uprawnień, których odmówiono, kliknij pozycję **Odmowa uprawnień**.

    ![Odmowa przypisania — odmowa uprawnień](./media/deny-assignments-portal/deny-assignment-denied-permissions.png)

    | Typ akcji | Opis |
    | --- | --- |
    | **Akcje**  | Odmowa operacji zarządzania. |
    | **NotActions** | Operacje zarządzania wyłączone z operacji zarządzania odmową. |
    | **DataAkcje**  | Odmowa operacji danych. |
    | **NotDataACtions (NotDataActions)** | Operacje danych wykluczone z operacji odmowy danych. |

    W przykładzie pokazanym na poprzednim zrzucie ekranu, poniżej znajdują się skuteczne uprawnienia:

    - Wszystkie operacje magazynowania na płaszczyźnie danych są odrzucane z wyjątkiem operacji obliczeniowych.

1. Aby wyświetlić właściwości przypisania **odmowy,** kliknij przycisk Właściwości .

    ![Odmowa przypisania - Właściwości](./media/deny-assignments-portal/deny-assignment-properties.png)

    Na **właściwości bloku,** można zobaczyć nazwę przypisania odmowy, identyfikator, opis i zakres. Przełącznik **Nie dotyczy** dzieci wskazuje, czy przypisanie odmowy jest dziedziczone do podzakresów. Przełącznik **chroniony przez system** wskazuje, czy to przypisanie odmowy jest zarządzane przez platformę Azure. Obecnie jest to **tak** we wszystkich przypadkach.

## <a name="next-steps"></a>Następne kroki

* [Opis przypisań odmów dla zasobów platformy Azure](deny-assignments.md)
* [Lista odmów przydziałów dla zasobów platformy Azure przy użyciu programu Azure PowerShell](deny-assignments-powershell.md)
