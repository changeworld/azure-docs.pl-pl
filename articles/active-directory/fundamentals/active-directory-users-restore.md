---
title: Przywracanie lub trwale usuwanie ostatnio usuniętego użytkownika — Usługa Azure AD
description: Jak wyświetlić przywracalnych użytkowników, przywrócić usuniętego użytkownika lub trwale usunąć użytkownika za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422861"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Przywracanie lub usuwanie ostatnio usuniętego użytkownika przy użyciu usługi Azure Active Directory
Po usunięciu użytkownika konto pozostaje w stanie zawieszenia przez 30 dni. W tym 30-dniowym oknie można przywrócić konto użytkownika wraz ze wszystkimi jego właściwościami. Po upływie tego 30-dniowego okna użytkownik jest automatycznie i trwale usuwany.

Możesz wyświetlić przywracalnych użytkowników, przywrócić usuniętego użytkownika lub trwale usunąć użytkownika przy użyciu usługi Azure Active Directory (Azure AD) w witrynie Azure Portal.

>[!Important]
>Ani Ty, ani dział obsługi klienta firmy Microsoft nie mogą przywrócić trwale usuniętego użytkownika.

## <a name="required-permissions"></a>Wymagane uprawnienia
Aby przywrócić i trwale usunąć użytkowników, musisz mieć jedną z następujących ról.

- Administrator globalny

- Pomoc techniczna poziomu 1 partnera

- Obsługa partnerów Tier2

- Administrator użytkownika

## <a name="view-your-restorable-users"></a>Wyświetlanie przywracalnych użytkowników
Możesz zobaczyć wszystkich użytkowników, którzy zostali usunięci mniej niż 30 dni temu. Tych użytkowników można przywrócić.

### <a name="to-view-your-restorable-users"></a>Aby wyświetlić odtwarzalnych użytkowników
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla organizacji.

2. Wybierz **pozycję Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Użytkownicy usunięci**.

    Przejrzyj listę użytkowników, którzy są dostępni do przywrócenia.

    ![Użytkownicy — strona Usunięto użytkowników, a użytkownicy nadal mogą zostać przywróceni](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Przywracanie ostatnio usuniętego użytkownika

Gdy konto użytkownika zostanie usunięte z organizacji, konto jest w stanie wstrzymania, a wszystkie powiązane informacje o organizacji są zachowywane. Po przywróceniu użytkownika te informacje o organizacji są również przywracane.

> [!Note]
> Po przywróceniu użytkownika licencje, które zostały przypisane do użytkownika w momencie usuwania, są również przywracane, nawet jeśli nie ma wolnych miejsc dla tych licencji. Jeśli następnie zużywasz więcej licencji niż zakupiono, twoja organizacja może być tymczasowo niezgodna z przepisami dotyczącymi użycia licencji.

### <a name="to-restore-a-user"></a>Aby przywrócić użytkownika
1. Na stronie **Użytkownicy — Usunięto użytkowników** wyszukaj i wybierz jednego z dostępnych użytkowników. Na przykład _Mary Parker_.

2. Wybierz **pozycję Przywróć użytkownika**.

    ![Użytkownicy — strona Usunięto użytkowników z wyróżnioną opcją Przywróć użytkownika](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trwałe usuwanie użytkownika
Możesz trwale usunąć użytkownika z organizacji bez czekania na 30 dni na automatyczne usunięcie. Nie można przywrócić trwale usuniętego użytkownika przez użytkownika, innego administratora ani przez dział obsługi klienta firmy Microsoft.

>[!Note]
>Jeśli przez pomyłkę trwale usuniesz użytkownika, musisz utworzyć nowego użytkownika i ręcznie wprowadzić wszystkie poprzednie informacje. Aby uzyskać więcej informacji na temat tworzenia nowego użytkownika, zobacz [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Aby trwale usunąć użytkownika

1. Na stronie **Użytkownicy — Usunięto użytkowników** wyszukaj i wybierz jednego z dostępnych użytkowników. Na przykład _Rae Huff_.

2. Wybierz **pozycję Usuń trwale**.

    ![Użytkownicy — strona Usunięto użytkowników z wyróżnioną opcją Przywróć użytkownika](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Następne kroki
Po przywróceniu lub usunięciu użytkowników można wykonać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodawanie lub zmienianie informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innej organizacji](../b2b/what-is-b2b.md)

Aby uzyskać więcej informacji na temat innych dostępnych zadań zarządzania użytkownikami, [dokumentację zarządzania użytkownikami usługi Azure AD](../users-groups-roles/index.yml).
