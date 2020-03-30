---
title: Zbiorcze usuwanie użytkowników (wersja zapoznawcza) w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zbiorcze usuwanie użytkowników w centrum administracyjnym platformy Azure w usłudze Azure Active Directory
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
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174385"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Zbiorcze usuwanie użytkowników (wersja zapoznawcza) w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można usunąć dużą liczbę członków do grupy za pomocą pliku csv (usytuowanych przecinkami) do zbiorczego usuwania użytkowników.

## <a name="to-bulk-delete-users"></a>Aby zbiorczo usuwać użytkowników

1. [Zaloguj się do organizacji usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **usuwania zbiorczego**.
1. Na stronie **Zbiorcze usuwanie użytkownika** wybierz pozycję **Pobierz,** aby otrzymać prawidłowy plik CSV właściwości użytkownika.

   ![Wybierz lokalny plik CSV, w którym wyświetlisz listę użytkowników, których chcesz usunąć](./media/users-bulk-delete/bulk-delete.png)

1. Otwórz plik CSV i dodaj wiersz dla każdego użytkownika, którego chcesz usunąć. Jedyną wymaganą wartością jest **nazwa główna użytkownika**. Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do usunięcia](./media/users-bulk-delete/delete-csv-file.png)

1. Na stronie **Usuwanie zbiorcze (Podgląd)** w obszarze **Przekazywanie pliku csv**przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku Prześlij rozpocznie się sprawdzanie poprawności pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku zostanie **wyświetlony folder Plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
1. Gdy plik przechodzi weryfikacji, wybierz **prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która usuwa użytkowników.
1. Po zakończeniu operacji usuwania zostanie wyświetlone powiadomienie, że operacja zbiorcza powiodła się.

Jeśli występują błędy, można pobrać i wyświetlić plik wyników na stronie **Wyniki operacji zbiorczej.** Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej (wersja zapoznawcza).**

   ![Sprawdź stan przekazywania na stronie Wyniki operacji zbiorczych](./media/users-bulk-delete/bulk-center.png)

Następnie można sprawdzić, czy usunięto użytkowników istnieje w organizacji usługi Azure AD albo w witrynie Azure portal lub przy użyciu programu PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Weryfikowanie usuniętych użytkowników w portalu Azure

1. Zaloguj się do witryny Azure portal przy za pomocą konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz pozycję **Wszyscy użytkownicy** i sprawdź, czy usunięci użytkownicy nie są już na liście.

### <a name="verify-deleted-users-with-powershell"></a>Weryfikowanie usuniętych użytkowników za pomocą programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Sprawdź, czy usunięci użytkownicy nie są już na liście.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze dodawanie użytkowników](users-bulk-add.md)
- [Lista użytkowników do pobrania](users-bulk-download.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
