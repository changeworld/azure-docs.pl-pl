---
title: Szybki start — wyświetlanie ról przypisanych do użytkownika za pomocą witryny Azure Portal | Microsoft Docs
description: Dowiedz się, jak za pomocą witryny Azure Portal wyświetlać uprawnienia kontroli dostępu opartej na rolach przypisane do użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b755dd6223c21084cafea82a1c8857f9f54b03b5
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52641870"
---
# <a name="quickstart-view-roles-assigned-to-a-user-using-the-azure-portal"></a>Szybki start: wyświetlanie ról przypisanych do użytkownika za pomocą witryny Azure Portal

Za pomocą bloku **Kontrola dostępu (IAM)** w funkcji [kontroli dostępu opartej na rolach](overview.md) można wyświetlać przypisania ról dla wielu użytkowników, grup, jednostek usługi i tożsamości zarządzanych, ale czasami konieczne jest tylko szybkie wyświetlenie przypisań ról dla jednego użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej. Najłatwiej można to zrobić przy użyciu funkcji **Sprawdź dostęp** w witrynie Azure Portal.

## <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Wykonaj następujące kroki, aby wyświetlić przypisania ról dla jednego użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w zakresie subskrypcji.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij swoją subskrypcję.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Sprawdź dostęp**.

    ![Kontrola dostępu — karta Sprawdź dostęp](./media/check-access/access-control-check-access.png)

1. Na liście **Znajdź** wybierz typ podmiotu zabezpieczeń, dla którego chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/check-access/check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    ![Okienko przypisania](./media/check-access/check-access-assignments.png)

    W tym okienku są wyświetlane role przypisane do wybranego podmiotu zabezpieczeń i zakresu. Jeśli istnieją przypisania odmowy występujące w tym zakresie lub dziedziczone przez ten zakres, zostaną wyświetlone.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie dostępu użytkownikowi za pomocą kontroli RBAC i witryny Azure Portal](quickstart-assign-role-user-portal.md)
