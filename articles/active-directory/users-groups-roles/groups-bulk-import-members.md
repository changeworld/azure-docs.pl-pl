---
title: Zbiorcze przekazywanie importu w celu dodania członków do grupy — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Zbiorcze dodawanie członków grupy w centrum administracyjnym usługi Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517129"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Zbiorcze importowanie członków grupy (wersja zapoznawcza) w usłudze Azure Active Directory

Korzystając z portalu usługi Azure Active Directory (Azure AD), można dodać dużą liczbę członków do grupy za pomocą pliku csv (usytuowanych przecinkami) do zbiorczego importowania członków grupy.

## <a name="to-bulk-import-group-members"></a>Aby zbiorczo importować członków grupy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta administratora użytkownika w organizacji. Właściciele grup mogą również zbiorczo importować członków grup, których są właścicielami.
1. W usłudze Azure AD wybierz pozycję **Grupy** > **Wszystkie grupy**.
1. Otwórz grupę, do której dodajesz członków, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Importuj członków**.
1. Na stronie **Zbiorczo importowanie członków grupy (Wersja zapoznawcza)** wybierz pozycję **Pobierz,** aby uzyskać szablon pliku CSV z wymaganymi właściwościami członka grupy.

    ![Polecenie Importuj członków znajduje się na stronie profilu grupy](./media/groups-bulk-import-members/import-panel.png)

1. Otwórz plik CSV i dodaj wiersz dla każdego członka grupy, który chcesz zaimportować do grupy (wymagane wartości to **identyfikator obiektu członkowskiego** lub nazwa główna **użytkownika).** Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory członków do zaimportowania](./media/groups-bulk-import-members/csv-file.png)

1. Na stronie **Zbiorczo importuj członków grupy (Wersja zapoznawcza)** w obszarze **Przekazywanie pliku csv**przejdź do pliku. Po wybraniu pliku rozpocznie się sprawdzanie poprawności pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku na stronie importu zbiorczego **zostanie wyświetlony plik przekazany pomyślnie**. Jeśli występują błędy, należy je naprawić, zanim będzie można przesłać zadanie.
1. Gdy plik przechodzi weryfikacji, wybierz **prześlij,** aby rozpocząć operację zbiorczą platformy Azure, która importuje członków grupy do grupy.
1. Po zakończeniu operacji importowania zostanie wyświetlone powiadomienie, że operacja zbiorcza powiodła się.

## <a name="check-import-status"></a>Sprawdź stan importu

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **Wyniki operacji zbiorczej (wersja zapoznawcza).**

   ![Strona Wyniki operacji zbiorczych pokazuje stan żądania zbiorczego](./media/groups-bulk-import-members/bulk-center.png)

Aby uzyskać szczegółowe informacje o każdym elemencie zamówienia w ramach operacji zbiorczej, wybierz wartości w kolumnach **# Sukces**, **# Niepowodzenie**lub Całkowita **liczba żądań.** Jeśli wystąpiły błędy, zostaną wyświetlone przyczyny awarii.

## <a name="bulk-import-service-limits"></a>Limity usług importu zbiorczego

Każde działanie zbiorcze w celu zaimportowania listy członków grupy może trwać maksymalnie jedną godzinę. Umożliwia to importowanie listy co najmniej 40 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Zbiorcze usuwanie członków grupy](groups-bulk-remove-members.md)
- [Pobieranie członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
