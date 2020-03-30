---
title: Zbiorcze przywracanie usuniętych użytkowników (wersja zapoznawcza) w portalu usługi Azure Active Directory | Dokumenty firmy Microsoft
description: Zbiorcze przywracanie usuniętych użytkowników w centrum administracyjnym usługi Azure AD w usłudze Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174254"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Zbiorcze przywracanie usuniętych użytkowników (wersja zapoznawcza) w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zbiorcze zapraszanie gości oraz obsługuje pobieranie list użytkowników, grup i członków grupy.

## <a name="to-bulk-restore-users"></a>Aby zbiorczo przywracać użytkowników

1. [Zaloguj się do organizacji usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem użytkownika w organizacji usługi Azure AD.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **usunięci**.
1. Na stronie **Usunięci użytkownicy** wybierz **opcję Przywracanie zbiorcze,** aby przekazać prawidłowy plik CSV właściwości użytkowników do przywrócenia.

   ![Wybieranie polecenia przywracania zbiorczego na stronie Użytkownicy usunięci](./media/users-bulk-restore/bulk-restore.png)

1. Otwórz plik CSV i dodaj wiersz dla każdego użytkownika, którego chcesz przywrócić. Jedyną wymaganą wartością jest **ObjectID**. Następnie zapisz plik.

   ![Wybierz lokalny plik CSV, w którym wyświetlisz listę użytkowników, których chcesz dodać](./media/users-bulk-restore/upload-button.png)

1. Na stronie **Przywracanie zbiorcze (Podgląd)** w obszarze **Przekazywanie pliku csv**przejdź do pliku. Po wybraniu pliku i kliknięciu **przycisku Prześlij**rozpocznie się sprawdzanie poprawności pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku zostanie **wyświetlony folder Plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
1. Gdy plik przechodzi weryfikacji, wybierz **prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która przywraca użytkowników.
1. Po zakończeniu operacji przywracania zostanie wyświetlone powiadomienie, że operacja zbiorcza powiodła się.

Jeśli występują błędy, można pobrać i wyświetlić plik wyników na stronie **Wyniki operacji zbiorczej.** Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej (wersja zapoznawcza).**

   ![Sprawdź stan przekazywania na stronie Wyniki operacji zbiorczych](./media/users-bulk-restore/bulk-center.png)

Następnie można sprawdzić, czy użytkownicy, których przywrócono istnieją w organizacji usługi Azure AD albo w witrynie Azure portal lub przy użyciu programu PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Wyświetlanie przywróconych użytkowników w witrynie Azure portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz pozycję **Wszyscy użytkownicy** i sprawdź, czy przywróceni użytkownicy są wymienieni.

### <a name="view-users-with-powershell"></a>Wyświetlanie użytkowników za pomocą programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinieneś zobaczyć, że użytkownicy, których przywrócono, są na liście.

## <a name="next-steps"></a>Następne kroki

- [Użytkownicy importu zbiorczego](users-bulk-add.md)
- [Zbiorcze usuwanie użytkowników](users-bulk-delete.md)
- [Lista użytkowników do pobrania](users-bulk-download.md)
