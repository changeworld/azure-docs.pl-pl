---
title: Usuwanie grupy — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Instrukcje dotyczące usuwania grupy za pomocą usługi Azure Active Directory.
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
ms.custom: it-pro, seodec18
ms.openlocfilehash: 405e86506f13472886878eb18557121c752e9464
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062302"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Usuń grupę przy użyciu usługi Azure Active Directory
Możesz usunąć grupę usługi Azure Active Directory (Azure AD) na dowolną liczbę powodów, ale zazwyczaj będzie się, ponieważ możesz:

- Niepoprawnie ustawione **typ grupy** nieprawidłową opcję.

- Utworzona przez pomyłkę nieprawidłowa lub grupy. 

- Nie są już potrzebne grupy.

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
