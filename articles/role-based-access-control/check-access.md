---
title: Szybki start — wyświetlanie informacji o dostępie użytkownika do zasobów platformy Azure | Microsoft Docs
description: Dowiedz się, jak przy użyciu kontroli dostępu opartej na rolach (RBAC) i witryny Azure Portal wyświetlić informacje o dostępie użytkownika lub innego podmiotu zabezpieczeń do zasobów platformy Azure.
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
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782353"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>Szybki start: Wyświetlanie informacji o dostępie użytkownika do zasobów platformy Azure

Informacje o dostępie użytkownika lub innego podmiotu zabezpieczeń do zasobów platformy Azure można wyświetlić w bloku **Kontrola dostępu (IAM)** w obszarze [Kontrola dostępu oparta na rolach (RBAC)](overview.md). Czasami jednak wystarczy szybki przegląd informacji o dostępie pojedynczego użytkownika lub innego podmiotu zabezpieczeń. Najłatwiej można to zrobić przy użyciu funkcji **Sprawdź dostęp** w witrynie Azure Portal.

## <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

 Aby sprawdzić informacje o dostępie użytkownika, należy wyświetlić jego przypisania ról. Wykonaj następujące kroki, aby wyświetlić przypisania ról dla jednego użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej w zakresie subskrypcji.

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](quickstart-assign-role-user-portal.md)
