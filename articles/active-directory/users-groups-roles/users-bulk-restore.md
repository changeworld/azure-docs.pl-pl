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
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901622"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Przywrócenie zbiorcze usuniętych użytkowników (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zaproszenia zbiorcze dla Gości i obsługują pobieranie list użytkowników, grup i członków grupy.

## <a name="to-bulk-restore-users"></a>Do przywracania zbiorczego użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **usunięci**.
1. Na stronie **usunięci użytkownicy** wybierz pozycję **przywracanie zbiorcze** , aby przekazać prawidłowy plik CSV właściwości użytkowników do przywrócenia.

   ![Wybierz polecenie Zbiorcza przywracanie na stronie usunięci użytkownicy](./media/users-bulk-restore/bulk-restore.png)

1. Po zakończeniu edytowania pliku CSV lub jeśli masz jeden z elementów gotowych do przekazania, wybierz plik w obszarze **Przekaż plik CSV** do zweryfikowania.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać](./media/users-bulk-restore/upload-button.png)

1. Po sprawdzeniu poprawności zawartości pliku napraw informacje o pliku i ponownie Prześlij plik, jeśli występują błędy. Przesłanie prawidłowego pliku powoduje automatyczne uruchomienie zadania przekazywania danych.
1. Po zakończeniu walidacji pliku CSV wybierz pozycję **Prześlij** , aby uruchomić zadanie usługi Azure Batch, które przywraca użytkowników. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

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
