---
title: Usuwanie grupy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące usuwania grupy za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561918"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Usuń grupę przy użyciu usługi Azure Active Directory
Możesz usunąć grupę usługi Azure Active Directory (Azure AD) na dowolną liczbę powodów, ale zazwyczaj będzie się, ponieważ możesz:

- Niepoprawnie Ustaw **Typ grupy** na niewłaściwą opcję.

- Wystąpił błąd podczas tworzenia nieprawidłowej lub zduplikowanej grupy. 

- Grupa nie jest już potrzebna.

## <a name="to-delete-a-group"></a>Aby usunąć grupę
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

3. Z **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, którą chcesz usunąć. W tych krokach użyjemy **zasady zarządzania urządzeniami Przenośnymi — Wschodnia**.

    ![Strony grupy — wszystkie grupy, wyróżnioną nazwą grupy](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **zasady zarządzania urządzeniami Przenośnymi — omówienie wschód** strony, a następnie wybierz pozycję **Usuń**.

    Grupa została usunięta z dzierżawy usługi Azure Active Directory.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona omówienie wschód, opcja Usuń wyróżniony](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli usuniesz grupę przez pomyłkę, można go utworzyć ponownie. Aby uzyskać więcej informacji, zobacz [jak utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md).

- Usunięcie grupy usługi Office 365 w przez pomyłkę, można go przywrócić. Aby uzyskać więcej informacji, zobacz [Przywracanie usuniętej grupy usługi Office 365](../users-groups-roles/groups-restore-deleted.md).
