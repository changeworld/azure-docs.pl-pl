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
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174305"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Tworzenie zbiorcze użytkowników (wersja zapoznawcza) w Azure Active Directory

Azure Active Directory (Azure AD) obsługuje operacje tworzenia i usuwania użytkowników zbiorczych, zaproszenia zbiorcze dla Gości i obsługują pobieranie list użytkowników, grup i członków grupy.

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby można było tworzyć zbiorczo użytkowników w portalu administracyjnym, użytkownik musi być zalogowany jako Administrator globalny lub administrator użytkowników.

## <a name="to-create-users-in-bulk"></a>Aby tworzyć użytkowników zbiorczo

1. [Zaloguj się do swojej organizacji usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W usłudze Azure AD wybierz pozycję **użytkownicy** > **Tworzenie zbiorcze**.
1. Na stronie **Tworzenie zbiorczego użytkownika** wybierz pozycję **Pobierz** , aby otrzymać prawidłowy plik wartości rozdzielanych przecinkami (CSV) właściwości użytkownika, a następnie Dodaj Dodaj użytkowników, których chcesz utworzyć.

   ![Wybierz lokalny plik CSV, w którym chcesz wyświetlić listę użytkowników, których chcesz dodać](./media/users-bulk-add/upload-button.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego użytkownika, który chcesz utworzyć. Jedyne wymagane wartości to **Nazwa**, **główna nazwa użytkownika**, **początkowe hasło** i **blokowanie logowania (tak/nie)** . Następnie Zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory użytkowników do utworzenia](./media/users-bulk-add/add-csv-file.png)

1. Na stronie **zbiorczego tworzenia użytkownika (wersja zapoznawcza)** w obszarze Przekaż plik CSV przejdź do pliku. Po wybraniu pliku i kliknięciu przycisku **Prześlij**zostanie uruchomiony Walidacja pliku CSV.
1. Po zweryfikowaniu zawartości pliku zostanie wyświetlony **plik pomyślnie przekazane**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która importuje nowych użytkowników.
1. Po zakończeniu operacji importowania zobaczysz powiadomienie o stanie zadania operacji zbiorczej.

W przypadku wystąpienia błędów można pobrać i wyświetlić plik wyników na stronie **wyników operacji zbiorczej** . Plik zawiera przyczynę każdego błędu.

## <a name="check-status"></a>Sprawdź stan

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Sprawdź stan przekazywania na stronie wyników operacji zbiorczych](./media/users-bulk-add/bulk-center.png)

Następnie możesz sprawdzić, czy utworzone przez Ciebie użytkownicy znajdują się w organizacji usługi Azure AD w Azure Portal lub przy użyciu programu PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Weryfikowanie użytkowników w Azure Portal

1. [Zaloguj się do centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem użytkownika w organizacji.
1. W okienku nawigacji wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzaj**wybierz pozycję **Użytkownicy**.
1. W obszarze **Pokaż**wybierz opcję **Wszyscy użytkownicy** i sprawdź, czy utworzone przez Ciebie użytkownicy znajdują się na liście.

### <a name="verify-users-with-powershell"></a>Weryfikowanie użytkowników przy użyciu programu PowerShell

Uruchom następujące polecenie:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Powinny zostać wyświetlone wszystkie utworzone przez Ciebie użytkownicy.

## <a name="bulk-import-service-limits"></a>Limity usług importowania zbiorczego

Każde działanie zbiorcze służące do tworzenia użytkowników może być uruchamiane przez maksymalnie jedną godzinę. Dzięki temu można utworzyć zbiorczo co najmniej 50 000 użytkowników.

## <a name="next-steps"></a>Następne kroki

- [Usuwanie zbiorczych użytkowników](users-bulk-delete.md)
- [Pobierz listę użytkowników](users-bulk-download.md)
- [Użytkownicy przywracania zbiorczego](users-bulk-restore.md)
