---
title: Ładowanie zbiorcze importowania w celu dodania członków do grupy — Azure Active Directory | Microsoft Docs
description: Dodaj zbiorczo członków grupy w centrum administracyjnym Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517129"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Członkowie grupy importu zbiorczego (wersja zapoznawcza) w Azure Active Directory

Za pomocą portalu usługi Azure Active Directory (Azure AD) można dodać dużą liczbę elementów członkowskich do grupy przy użyciu pliku wartości rozdzielanych przecinkami (CSV) w celu zbiorczego importowania elementów członkowskich grupy.

## <a name="to-bulk-import-group-members"></a>Aby zaimportować zbiorczo członków grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo importować członków należących do nich grup.
1. W usłudze Azure AD wybierz pozycję **grupy**  > **wszystkie grupy**.
1. Otwórz grupę, do której dodawane są elementy członkowskie, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Importuj członków**.
1. Na stronie **Importuj zbiorczo członków grupy (wersja zapoznawcza)** wybierz pozycję **Pobierz** , aby pobrać szablon pliku CSV z właściwościami wymaganych elementów członkowskich grupy.

    ![Polecenie Importuj elementy członkowskie znajduje się na stronie profilu grupy](./media/groups-bulk-import-members/import-panel.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego członka grupy, który ma zostać zaimportowany do grupy (wymagane wartości to **Identyfikator obiektu elementu członkowskiego** lub **główna nazwa użytkownika**). Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory dla elementów członkowskich do zaimportowania](./media/groups-bulk-import-members/csv-file.png)

1. Na stronie **Importuj zbiorczo członków grupy (wersja zapoznawcza)** w obszarze **Przekaż plik CSV**przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku, Strona importowania zbiorczego wyświetla **pomyślnie przekazany plik**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która importuje członków grupy do grupy.
1. Po zakończeniu operacji importowania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="check-import-status"></a>Sprawdź stan importowania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Na stronie wyniki operacji zbiorczych jest wyświetlany stan zbiorczego żądania](./media/groups-bulk-import-members/bulk-center.png)

Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości z kolumny **# Success**, **# Failure**lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

## <a name="bulk-import-service-limits"></a>Limity usług importowania zbiorczego

Każde działanie zbiorcze służące do importowania listy członków grupy może być uruchamiane przez maksymalnie jedną godzinę. Umożliwia to zaimportowanie listy co najmniej 40 000 elementów członkowskich.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
