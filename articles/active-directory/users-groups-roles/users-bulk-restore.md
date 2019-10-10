---
title: Przywrócenie zbiorcze usuniętych użytkowników (wersja zapoznawcza) w portalu Azure Active Directory | Microsoft Docs
description: Przywróć zbiorczo usuniętych użytkowników w centrum administracyjnym usługi Azure AD w Azure Active Directory
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
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174254"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Przywrócenie zbiorcze usuniętych użytkowników (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zaproszenia zbiorcze dla Gości i obsługują pobieranie list użytkowników, grup i członków grupy.

## <a name="to-bulk-restore-users"></a>Do przywracania zbiorczego użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji usługi Azure AD.
1. W usłudze Azure AD wybierz pozycję **użytkownicy** > **usunięta**.
1. Na stronie **usunięci użytkownicy** wybierz pozycję **przywracanie zbiorcze** , aby przekazać prawidłowy plik CSV właściwości użytkowników do przywrócenia.

   ![Wybierz polecenie Zbiorcza przywracanie na stronie usunięci użytkownicy](./media/users-bulk-restore/bulk-restore.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego użytkownika, który ma zostać przywrócony. Jedyną wymaganą wartością jest **objectid**. Następnie zapisz plik.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać](./media/users-bulk-restore/upload-button.png)

1. Na stronie **przywracanie zbiorcze (wersja zapoznawcza)** w obszarze **Przekaż plik CSV**przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku **Prześlij**zostanie uruchomiony Walidacja pliku CSV.
1. Gdy zawartość pliku zostanie sprawdzona, zostanie wyświetlony **plik przekazany pomyślnie**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która przywraca użytkowników.
1. Po zakończeniu operacji przywracania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie **wyników operacji zbiorczej** . Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Sprawdź stan przekazywania na stronie wyników operacji zbiorczych](./media/users-bulk-restore/bulk-center.png)

Następnie możesz sprawdzić, czy przywróconi użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Wyświetl przywróconych użytkowników w Azure Portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz pozycję **Wszyscy użytkownicy** i sprawdź, czy na liście znajdują się użytkownicy, których przywrócono.

### <a name="view-users-with-powershell"></a>Wyświetlanie użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinna zostać wyświetlona lista użytkowników, których przywrócono.

## <a name="next-steps"></a>Następne kroki

- [Importowanie zbiorczych użytkowników](users-bulk-add.md)
- [Usuwanie zbiorczych użytkowników](users-bulk-delete.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
