---
title: Udzielanie dostępu do innych administratorów do zarządzania usługą PIM — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić dostępu do innych administracji do zarządzania usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288654"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Udzielanie dostępu do innych administratorów do zarządzania usługi PIM

Administrator globalny, który umożliwia automatyczne usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) dla organizacji, skorzystaj z przypisań ról i dostępu do usługi PIM. Nikt inny nie pobiera zapisu domyślnie, jednak tym innych administratorów globalnych. Inne Administratorzy globalni, Administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do usługi PIM. Aby udzielić dostępu do usługi PIM, pierwszego użytkownika można przypisać inne osoby **Administrator ról uprzywilejowanych** roli.

> [!NOTE]
> Zarządzanie usługą PIM wymaga usługi Azure MFA. Ponieważ kont Microsoft nie można zarejestrować usługi Azure MFA, użytkownik zaloguje się za pomocą konta Microsoft nie może uzyskać dostępu usługi PIM.

Upewnij się, są zawsze co najmniej dwóch użytkowników należących do roli Administrator ról uprzywilejowanych w przypadku, gdy jeden użytkownik jest zablokowany lub jego konto zostało usunięte.

## <a name="grant-access-to-manage-pim"></a>Przyznaj dostęp umożliwiający zarządzanie usługą PIM

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **role**.

    ![Role usługi AD PIM Azure — role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Kliknij przycisk **Administrator ról uprzywilejowanych** roli, aby otworzyć stronę elementów członkowskich.

    ![Administrator ról uprzywilejowanych — członkowie](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Kliknij przycisk **Dodawanie elementu członkowskiego** aby otworzyć okienko Dodawanie zarządzanych członków.

1. Kliknij przycisk **Wybieranie elementów członkowskich** aby otworzyć okienko wybierz elementy członkowskie.

    ![Administrator ról uprzywilejowanych — wybierz elementy członkowskie](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Wybierz składnik, a następnie kliknij przycisk **wybierz**.

1. Kliknij przycisk **OK** się kwalifikuje się do elementu członkowskiego **Administrator ról uprzywilejowanych** roli.

    W przypadku przypisania roli do osoby w usłudze PIM, są one automatycznie konfigurowane jako **kwalifikujących się** aktywowania roli.

1. Utrwalenie elementu członkowskiego, kliknij użytkownika na liście elementów członkowskich Administrator ról uprzywilejowanych.

1. Kliknij przycisk **więcej** i następnie **były trwałe** utrwalenie przypisania.

    ![Administrator ról uprzywilejowanych - były trwałe](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Wyślij łącze do użytkownika [rozpocząć korzystanie z usługi PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Usuń dostęp umożliwiający zarządzanie usługą PIM

Zanim usuniesz ktoś z roli Administrator ról uprzywilejowanych, zawsze upewnij się, nadal będzie co najmniej dwóch użytkowników przypisanych do niego.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **usługi Azure AD Privileged Identity Management**.

1. Kliknij przycisk **ról usługi Azure AD**.

1. Kliknij przycisk **role**.

1. Kliknij przycisk **Administrator ról uprzywilejowanych** roli, aby otworzyć stronę elementów członkowskich.

1. Dodaj znacznik wyboru obok użytkownika, o których chcesz usunąć, a następnie kliknij przycisk **usuwanie elementu członkowskiego**.

    ![Administrator ról uprzywilejowanych — usuwanie elementu członkowskiego](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. W komunikacie zostanie wyświetlony pytaniem, jeśli chcesz usunąć członka z roli, kliknij przycisk **tak**.

## <a name="next-steps"></a>Kolejne kroki

- [Rozpoczynanie korzystania z usługi PIM](pim-getting-started.md)
