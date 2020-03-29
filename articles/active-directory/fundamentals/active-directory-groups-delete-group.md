---
title: Usuwanie grupy — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Instrukcje dotyczące usuwania grupy przy użyciu usługi Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561918"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Usuwanie grupy przy użyciu usługi Azure Active Directory
Możesz usunąć grupę usługi Azure Active Directory (Azure AD) z dowolnej liczby powodów, ale zazwyczaj będzie to spowodowane tym, że:

- Niepoprawnie ustaw **typ grupy** na niewłaściwą opcję.

- Utworzono niewłaściwą lub zduplikowaną grupę przez pomyłkę. 

- Nie potrzebują już grupy.

## <a name="to-delete-a-group"></a>Aby usunąć grupę
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **pozycję Azure Active Directory**, a następnie wybierz pozycję **Grupy**.

3. Na stronie **Grupy — wszystkie grupy** wyszukaj i wybierz grupę, którą chcesz usunąć. W tych krokach użyjemy **zasad MDM - Wschód**.

    ![Grupy — strona Wszystkie grupy, wyróżniona nazwa grupy](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na stronie **zasady MDM — Przegląd wschodni,** a następnie wybierz pozycję **Usuń**.

    Grupa zostanie usunięta z dzierżawy usługi Azure Active Directory.

    ![Zasady MDM — strona Przegląd wschodni, wyróżniona opcja usuwania](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli usuniesz grupę przez pomyłkę, możesz ją utworzyć ponownie. Aby uzyskać więcej informacji, zobacz [Jak utworzyć grupę podstawową i dodać członków](active-directory-groups-create-azure-portal.md).

- Jeśli usuniesz grupę usługi Office 365 przez pomyłkę, możesz ją przywrócić. Aby uzyskać więcej informacji, zobacz [Przywracanie usuniętej grupy usługi Office 365](../users-groups-roles/groups-restore-deleted.md).
