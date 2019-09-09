---
title: Udzielanie dostępu innym administratorom w celu zarządzania usługą PIM Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak udzielić dostępu innym administratorom do zarządzania usługą Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3a0173108b6c884994ca25fd0495e9cb8d45186
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804351"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udzielanie dostępu innym administratorom w celu zarządzania usługą PIM

Administrator globalny, który umożliwia usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dla organizacji automatyczne pobieranie przypisań ról i dostęp do usługi PIM. Nikt inny nie uzyskuje domyślnie dostępu do zapisu, ale łącznie z innymi administratorami globalnymi. Inni administratorzy globalni, administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do programu PIM. Aby udzielić dostępu do programu PIM, pierwszy użytkownik może przypisać inne osoby do roli **administrator ról uprzywilejowanych** .

> [!NOTE]
> Zarządzanie programem PIM wymaga usługi Azure MFA. Ponieważ konta Microsoft nie mogą zarejestrować się w usłudze Azure MFA, użytkownik, który loguje się przy użyciu konto Microsoft nie może uzyskać dostępu do PIM.

Upewnij się, że w roli administratora roli uprzywilejowanej są zawsze co najmniej dwóch użytkowników, na wypadek gdy jeden użytkownik jest zablokowany lub jego konto zostanie usunięte.

## <a name="grant-access-to-manage-pim"></a>Udzielanie dostępu do zarządzania usługą PIM

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **role**.

    ![Role PIM usługi Azure AD — role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Kliknij rolę **administrator ról uprzywilejowanych** , aby otworzyć stronę członkowie.

    ![Administrator ról uprzywilejowanych — członkowie](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Kliknij przycisk **Dodaj członka** , aby otworzyć okienko Dodawanie elementów zarządzanych.

1. Kliknij pozycję **Wybierz członków** , aby otworzyć okienko wybierz członków.

    ![Administrator ról uprzywilejowanych — Wybieranie członków](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Wybierz element członkowski, a następnie kliknij przycisk **Wybierz**.

1. Kliknij przycisk **OK** , aby członek mógł kwalifikować się do roli **administrator ról uprzywilejowanych** .

    Po przypisaniu nowej roli do kogoś w usłudze PIM są one automatycznie konfigurowane jako **kwalifikujące** się do aktywowania roli.

1. Aby trwale wprowadzić element członkowski, kliknij użytkownika na liście członków administratora roli uprzywilejowanej.

1. Kliknij przycisk **więcej** , a następnie **Zmień trwały** , aby przypisywać trwało.

    ![Administrator ról uprzywilejowanych — Ustaw jako trwały](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Wyślij użytkownikowi link, aby [rozpocząć korzystanie](pim-getting-started.md)z usługi PIM.

## <a name="remove-access-to-manage-pim"></a>Usuwanie dostępu w celu zarządzania usługą PIM

Przed usunięciem kogoś z roli administrator ról uprzywilejowanych zawsze upewnij się, że nadal są przypisane co najmniej dwóch użytkowników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **role**.

1. Kliknij rolę **administrator ról uprzywilejowanych** , aby otworzyć stronę członkowie.

1. Dodaj znacznik wyboru obok użytkownika, który chcesz usunąć, a następnie kliknij przycisk **Usuń członka**.

    ![Administrator ról uprzywilejowanych — usuwanie elementu członkowskiego](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. W wyświetlonym komunikacie z pytaniem, czy chcesz usunąć członka z roli, kliknij przycisk **tak**.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczynanie korzystania z usługi PIM](pim-getting-started.md)
