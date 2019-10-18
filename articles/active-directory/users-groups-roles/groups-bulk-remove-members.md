---
title: Zbiorcze usuwanie członków grupy przez przekazanie pliku CSV — Azure Active Directory | Microsoft Docs
description: Dodaj użytkowników zbiorczo w centrum administracyjnym platformy Azure.
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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517092"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Zbiorcze usuwanie członków grupy (wersja zapoznawcza) w Azure Active Directory

Korzystając z portalu Azure Active Directory (Azure AD), można usunąć dużą liczbę członków z grupy, używając pliku wartości rozdzielanych przecinkami (CSV) do zbiorczego usuwania elementów członkowskich grupy.

## <a name="to-bulk-remove-group-members"></a>Aby zbiorczo usuwać członków grupy

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora użytkowników w organizacji. Właściciele grupy mogą również zbiorczo usuwać członków należących do nich grup.
1. W usłudze Azure AD wybierz pozycję **grupy**  > **wszystkie grupy**.
1. Otwórz grupę, z której zostaną usunięte elementy członkowskie, a następnie wybierz pozycję **Członkowie**.
1. Na stronie **Członkowie** wybierz pozycję **Usuń członków**.
1. Na stronie **zbiorcze usuwanie członków grupy (wersja zapoznawcza)** wybierz pozycję **Pobierz** , aby pobrać szablon pliku CSV z właściwościami wymaganych elementów członkowskich grupy.

   ![Polecenie Usuń członków znajduje się na stronie profilu grupy](./media/groups-bulk-remove-members/remove-panel.png)

1. Otwórz plik CSV i Dodaj wiersz dla każdego członka grupy, który chcesz usunąć z grupy (wymagane wartości to identyfikator obiektu elementu członkowskiego lub główna nazwa użytkownika). Następnie zapisz plik.

   ![Plik CSV zawiera nazwy i identyfikatory dla elementów członkowskich do usunięcia](./media/groups-bulk-remove-members/csv-file.png)

1. Na stronie **zbiorcze usuwanie członków grupy (wersja zapoznawcza)** w obszarze **Przekaż plik CSV**przejdź do pliku. Po wybraniu pliku zostanie uruchomiony Walidacja pliku CSV.
1. Po sprawdzeniu poprawności zawartości pliku, Strona importowania zbiorczego wyświetla **pomyślnie przekazany plik**. Jeśli występują błędy, należy je usunąć przed przesłaniem zadania.
1. Gdy plik zostanie pomyślnie zweryfikowany, wybierz pozycję **Prześlij** , aby rozpocząć operację zbiorczą platformy Azure, która usuwa członków grupy z grupy.
1. Po zakończeniu operacji usuwania zobaczysz powiadomienie, że operacja zbiorcza zakończyła się pomyślnie.

## <a name="check-removal-status"></a>Sprawdź stan usuwania

Stan wszystkich oczekujących żądań zbiorczych można zobaczyć na stronie **wyniki operacji zbiorczej (wersja zapoznawcza)** .

   ![Na stronie wyniki operacji zbiorczych jest wyświetlany stan zbiorczego żądania](./media/groups-bulk-remove-members/bulk-center.png)

Aby uzyskać szczegółowe informacje na temat każdego elementu wiersza w ramach operacji zbiorczej, wybierz wartości z kolumny **# Success**, **# Failure**lub **Total Requests** . Jeśli wystąpią błędy, zostaną wyświetlone przyczyny niepowodzenia.

## <a name="bulk-removal-service-limits"></a>Limity usługi usuwania zbiorczego

Każde działanie zbiorcze służące do usuwania listy członków grupy można uruchomić przez maksymalnie jedną godzinę. Umożliwia to usunięcie listy co najmniej 40 000 członków.

## <a name="next-steps"></a>Następne kroki

- [Członkowie grupy importu zbiorczego](groups-bulk-import-members.md)
- [Pobierz członków grupy](groups-bulk-download-members.md)
- [Pobierz listę wszystkich grup](groups-bulk-download.md)
