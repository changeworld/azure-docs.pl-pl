---
title: Jak usunąć grupę przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usunąć grupę przy użyciu usługi Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 014fe487d23a6c75e94ca2708ed15044bd6cf53b
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574335"
---
# <a name="how-to-delete-a-group-using-azure-active-directory"></a>Porady: usuwanie grupy za pomocą usługi Azure Active Directory
Możesz usunąć grupę dla dowolnej liczby powodów, ale zazwyczaj będzie się, ponieważ możesz:

- Niepoprawnie ustawione **typ grupy** do niewłaściwej opcji

- Utworzone nieprawidłowa lub zduplikowane grupy przez pomyłkę 

- Nie są już potrzebne grupy

## <a name="to-delete-a-group"></a>Aby usunąć grupę
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, a następnie wybierz pozycję **grup**.

3. Z **grupy — wszystkie grupy** strony, wyszukaj i wybierz grupę, którą chcesz usunąć. W tych krokach użyjemy **zasady zarządzania urządzeniami Przenośnymi — Wschodnia**.

    ![Strony grupy — wszystkie grupy, wyróżnioną nazwą grupy](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na **zasady zarządzania urządzeniami Przenośnymi — omówienie wschód** strony, a następnie wybierz pozycję **Usuń**.

    Grupa została usunięta z dzierżawy usługi Azure Active Directory.

    ![Zasady zarządzania urządzeniami Przenośnymi — strona omówienie wschód, opcja Usuń wyróżniony](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli usuniesz grupę przez pomyłkę, można go utworzyć ponownie. Aby uzyskać więcej informacji, zobacz [jak utworzenie podstawowej grupy i dodawać członków](active-directory-groups-create-azure-portal.md).

- Usunięcie grupy usługi Office 365 w przez pomyłkę, można go przywrócić. Aby uzyskać więcej informacji, zobacz [Przywracanie usuniętej grupy usługi Office 365](../users-groups-roles/groups-restore-deleted.md).
