---
title: Udzielanie dostępu do zarządzania usługą PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak udzielić dostępu innym administracjom do zarządzania zarządzaniem zarządzaniem tożsamościami uprzywilejowanymi usługą Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7424e92f8520d13137b6ac8787523095058a005f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022111"
---
# <a name="grant-access-to-other-administrators-to-manage-privileged-identity-management"></a>Udzielanie dostępu innym administratorom do zarządzania zarządzaniem tożsamościami uprzywilejowanymi

Administrator globalny, który włącza zarządzanie tożsamościami uprzywilejowanymi (PIM) dla organizacji, automatycznie otrzymuje przypisania ról i dostęp do zarządzania tożsamościami uprzywilejowanymi. Nikt inny w organizacji usługi Azure Active Directory (Azure AD) nie otrzymuje dostępu do zapisu domyślnie, ale, w tym innych administratorów globalnych. Inni administratorzy globalni, administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do zarządzania tożsamościami uprzywilejowanymi. Aby udzielić dostępu do zarządzania tożsamościami uprzywilejowanymi, pierwszy użytkownik może przypisać inne osoby do roli **Administrator ról uprzywilejowanych.**

> [!NOTE]
> Zarządzanie zarządzaniem uprzywilejowanymi tożsamościami wymaga uwierzytelniania wieloskładnikowego platformy Azure. Ponieważ konta Microsoft nie mogą zarejestrować się w celu uwierzytelniania wieloskładnikowego platformy Azure, użytkownik, który loguje się za pomocą konta Microsoft, nie może uzyskać dostępu do zarządzania tożsamościami uprzywilejowanymi.

Upewnij się, że zawsze w roli administratora ról uprzywilejowanych jest co najmniej dwóch użytkowników, jeśli jeden użytkownik zostanie zablokowany lub jego konto zostanie usunięte.

## <a name="grant-access-to-manage-pim"></a>Udzielanie dostępu do zarządzania pim

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. W usłudze Azure AD otwórz **zarządzanie tożsamościami uprzywilejowanymi**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Role**.

    ![Role usługi AD uprzywilejowanego zarządzania tożsamościami — role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Wybierz rolę **Administrator roli uprzywilejowanej,** aby otworzyć stronę członków.

    ![Administrator ról uprzywilejowanych — członkowie](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Wybierz **pozycję Dodaj członka,** aby otworzyć okienko Dodaj członków zarządzanych.

1. Wybierz **pozycję Wybierz członków,** aby otworzyć okienko Wybierz elementy członkowskie.

    ![Administrator ról uprzywilejowanych — wybieranie członków](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Zaznacz element członkowski, a następnie kliknij przycisk **Zaznacz**.

1. Wybierz **przycisk OK,** aby członek kwalifikował się do roli **Administrator roli uprzywilejowanej.**

    Po przypisaniu nowej roli do innej osoby w zarządzania tożsamościami uprzywilejowanymi są one automatycznie konfigurowane jako **kwalifikujące się** do aktywowania roli.

1. Aby uczynić go stałym, wybierz użytkownika na liście elementów członkowskich Administrator roli uprzywilejowanej.

1. Wybierz **pozycję Więcej,** a następnie **pozycję Pokonuj na stałe,** aby przydział stał się trwały.

    ![Administrator ról uprzywilejowanych — na stałe](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Wyślij użytkownikowi łącze do [Przycisku Rozpocznij korzystanie z zarządzania tożsamościami uprzywilejowanymi](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Usuwanie dostępu do zarządzania pim

Przed usunięciem kogoś z roli Administrator ról uprzywilejowanych, zawsze upewnij się, że nadal będzie co najmniej dwóch użytkowników przypisanych do niego.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD**.

1. Wybierz **pozycję Role**.

1. Wybierz rolę **Administrator roli uprzywilejowanej,** aby otworzyć stronę członków.

1. Zaznacz pole wyboru obok użytkownika, którego chcesz usunąć, a następnie wybierz pozycję **Usuń członka**.

    ![Administrator ról uprzywilejowanych — usuń członka](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Gdy zostaniesz poproszony o potwierdzenie, że chcesz usunąć członka z roli, wybierz przycisk **Tak**.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie korzystania z usługi Privileged Identity Management](pim-getting-started.md)
