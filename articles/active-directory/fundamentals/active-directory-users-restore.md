---
title: Przywrócić lub usunąć trwale ostatnio usuniętego użytkownika — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak wyświetlić użytkowników z możliwością przywrócenia, przywracanie usuniętego użytkownika lub trwale usunąć użytkownika z usługą Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 066c9189e0c3cea409a99217e0ab4b46002df1f9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556785"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Przywrócić lub usunąć ostatnio usuniętego użytkownika przy użyciu usługi Azure Active Directory
Po usunięciu użytkownika konto pozostaje w stanie wstrzymania przez 30 dni. Podczas tego 30-dniowe okno konto użytkownika można przywrócić, wraz z jego właściwości. Po pomyślnej tego 30-dniowe okno, użytkownik jest automatycznie i stałe, usuwane.

Można wyświetlić z możliwością przywrócenia użytkowników, przywracanie usuniętego użytkownika lub trwałe usuwanie użytkownika przy użyciu usługi Azure Active Directory (Azure AD) w witrynie Azure portal.

>[!Important]
>Trwale usunięto użytkownika można przywrócić, nie możesz ani obsługi klienta firmy Microsoft.

## <a name="required-permissions"></a>Wymagane uprawnienia
Użytkownik musi mieć jedną z następujących ról, aby przywrócić i trwale usunąć użytkowników.

- Administrator firmy

- Pomoc techniczna dla partnerów (warstwa 1)

- Pomoc techniczna dla partnerów (warstwa 2)

- Administrator kont użytkowników

## <a name="view-your-restorable-users"></a>Wyświetlanie z możliwością przywrócenia użytkowników
Można wyświetlić wszystkich użytkowników, które zostały usunięte z mniej niż 30 dni temu. Tacy użytkownicy mogą zostać przywrócone.

### <a name="to-view-your-restorable-users"></a>Aby wyświetlić z możliwością przywrócenia użytkowników
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **usuniętych użytkowników**.

    Przejrzyj listę użytkowników, które są dostępne do przywrócenia.

    ![Użytkownicy — strona usuniętych użytkowników z użytkowników, którzy mogą zostać przywrócone](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Przywracanie ostatnio usuniętego użytkownika
Gdy konto użytkownika jest wstrzymane, wszystkich informacji katalogowych powiązane są zachowywane. Po przywróceniu użytkownika tej informacji usługi directory również zostanie przywrócony.

### <a name="to-restore-a-user"></a>Aby przywrócić użytkownika
1. Na **użytkownicy — usuniętych użytkowników** strony, wyszukaj i wybierz jedną z dostępnych użytkowników. Na przykład _Mary Parker_.

2. Wybierz **użytkownika przywracania**.

    ![Użytkownicy — stronie usuniętych użytkowników z podświetloną opcją użytkownika przywracania](media/active-directory-users-restore/users-deleted-users-restore-user.png)

>[!NOTE]
>Poprzednio po użytkownik został usunięty z zakresu synchronizacji w środowisku lokalnym i usunięte w chmurze, stan DirSyncEnabled konta błędnie została ustawiona na "False". Jeśli później, ten użytkownik został przywrócony ręcznie z Kosza usługi Azure AD, a potem nieprawidłowym stanie "chmura tylko do" konta. Teraz zostało to poprawione i wartość stanu DirSyncEnabled jest zawsze mieć "wartość True" po użytkownik zostanie usunięty z zakresu synchronizacji, następnie wszystkie usunięte nietrwale i ręcznie odtworzonych z Kosza usługi Azure AD.

## <a name="permanently-delete-a-user"></a>Trwałe usuwanie użytkownika
Możesz trwale usunąć użytkownika z katalogu, bez konieczności oczekiwania przez 30 dni dla automatycznego usuwania. Trwale usunięto użytkownika nie można przywrócić przez Ciebie innego administratora ani znakiem obsługi klienta firmy Microsoft.

>[!Note]
>Trwałe usunięcie użytkownika przez pomyłkę, należy utworzyć nowego użytkownika, a następnie ręcznie wprowadź poprzednich informacji. Aby uzyskać więcej informacji na temat tworzenia nowego użytkownika, zobacz [apletu Dodaj lub usuń użytkowników](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Aby trwale usunąć użytkownika

1. Na **użytkownicy — usuniętych użytkowników** strony, wyszukaj i wybierz jedną z dostępnych użytkowników. Na przykład _Rae Huff_.

2. Wybierz **trwałe usunięcie**.

    ![Użytkownicy — stronie usuniętych użytkowników z podświetloną opcją użytkownika przywracania](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Kolejne kroki
Po przywrócić lub usunąć użytkowników, należy wykonać następujące procesy basic:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodać lub zmienić informacje o profilu](active-directory-users-profile-azure-portal.md)

- [Dodawanie użytkowników-gości z innego katalogu](../b2b/what-is-b2b.md) 

Aby uzyskać więcej informacji na temat innych dostępnych zadań zarządzania użytkownikami [dokumentacja zarządzania użytkownika usługi Azure Active Directory](../users-groups-roles/index.yml).
