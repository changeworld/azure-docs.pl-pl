---
title: Tworzenie zbiorcze użytkowników (wersja zapoznawcza) w portalu Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym usługi Azure AD w Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c62cbe71f2e02c6f2c09620a8470a97ae57392
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146315"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Tworzenie zbiorcze użytkowników (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zaproszenia zbiorcze dla Gości i obsługują pobieranie list użytkowników, grup i członków grupy.

## <a name="bulk-import-service-limits"></a>Limity usług importowania zbiorczego

Każde działanie zbiorcze służące do tworzenia użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Dzięki temu można utworzyć zbiorczo co najmniej 50 000 użytkowników.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby można było tworzyć zbiorczo użytkowników w portalu administracyjnym, użytkownik musi być zalogowany jako Administrator globalny lub administrator użytkowników.

## <a name="to-bulk-import-users"></a>Aby zaimportować zbiorczo użytkowników

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **Użytkownicy** > **Tworzenie zbiorcze**.
1. Na stronie **Tworzenie zbiorczego użytkownika** wybierz pozycję **Pobierz** , aby otrzymać prawidłowy plik wartości rozdzielanych przecinkami (CSV) właściwości użytkownika, a następnie Dodaj nowych użytkowników.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do utworzenia](./media/users-bulk-add/add-csv-file.png)

1. Po zakończeniu edytowania pliku CSV lub jeśli masz jeden z elementów gotowych do przekazania, wybierz plik w obszarze **Przekaż plik CSV** do zweryfikowania.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać](./media/users-bulk-add/upload-button.png)

1. Po sprawdzeniu poprawności zawartości pliku należy usunąć wszelkie błędy przed rozpoczęciem zadania przekazywania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby uruchomić zadanie usługi Azure Batch, które dodaje nowe informacje o użytkowniku. W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie wyników operacji zbiorczej. Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdzanie stanu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Sprawdź stan przekazywania na stronie wyników operacji zbiorczych](./media/users-bulk-add/bulk-center.png)

Następnie możesz sprawdzić, czy utworzone przez Ciebie użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Weryfikowanie użytkowników w Azure Portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz opcję **Wszyscy użytkownicy** i sprawdź, czy utworzone przez Ciebie użytkownicy znajdują się na liście.

### <a name="verify-users-with-powershell"></a>Weryfikowanie użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinny zostać wyświetlone wszystkie utworzone przez Ciebie użytkownicy.

## <a name="next-steps"></a>Następne kroki

- [Usuwanie zbiorczych użytkowników](users-bulk-delete.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
