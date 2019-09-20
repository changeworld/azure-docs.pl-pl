---
title: Zbiorcze usuwanie użytkowników (wersja zapoznawcza) w portalu Azure Active Directory | Microsoft Docs
description: Usuń użytkowników zbiorczo w centrum administracyjnym platformy Azure w Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146425"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Zbiorcze usuwanie użytkowników (wersja zapoznawcza) w Azure Active Directory

Za pomocą portalu usługi Azure Active Directory (Azure AD) można usunąć dużą liczbę elementów członkowskich do grupy przy użyciu pliku wartości rozdzielanych przecinkami (CSV) do zbiorczego usuwania użytkowników.

## <a name="to-bulk-delete-users"></a>Aby usunąć zbiorczo użytkowników

1. Zaloguj się do swojej organizacji usługi Azure AD przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **zbiorczo usuwać**.
1. Na stronie **usuwania zbiorczego użytkownika** wybierz pozycję **Pobierz** , aby otrzymać prawidłowy plik CSV właściwości użytkownika, a następnie Dodaj użytkowników, których chcesz usunąć.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do usunięcia](./media/users-bulk-delete/delete-csv-file.png)

1. Po zakończeniu edytowania pliku CSV wybierz plik w obszarze **Przekaż plik CSV** do zweryfikowania.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników do usunięcia](./media/users-bulk-delete/bulk-delete.png)

1. Po sprawdzeniu poprawności zawartości pliku należy usunąć wszelkie błędy przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby uruchomić zadanie usługi Azure Batch, które usuwa użytkowników. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Sprawdź stan przekazywania na stronie wyników operacji zbiorczych](./media/users-bulk-delete/bulk-center.png)

Następnie możesz sprawdzić, czy usunięte użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Weryfikowanie usuniętych użytkowników w Azure Portal

1. Zaloguj się do Azure Portal przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**zaznacz opcję tylko **Wszyscy użytkownicy** i sprawdź, czy usunięte użytkownicy nie są już wyświetlani.

### <a name="verify-deleted-users-with-powershell"></a>Weryfikowanie usuniętych użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Upewnij się, że usunięci użytkownicy nie są już wyświetlani.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
