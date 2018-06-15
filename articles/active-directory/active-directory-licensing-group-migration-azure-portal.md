---
title: Migracja użytkowników licencji użytkownika do grupy na podstawie Licencjonowanie w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak przejść z licencji użytkownika do grupy na podstawie licencji za pomocą usługi Azure Active Directory
services: active-directory
keywords: Licencjonowanie usługi Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 01/14/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: ed95ba1329ba3052fab3d81327d23475e9902d8a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762224"
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Jak dodać licencjonowanych użytkowników do grupy licencji w usłudze Azure Active Directory

Masz istniejących licencji wdrożone dla użytkowników w organizacji za pomocą "przypisania bezpośredniego" oznacza to za pomocą skryptów programu PowerShell lub innych narzędzi, aby przypisać licencje do poszczególnych użytkowników. Jeśli chcesz rozpocząć korzystanie z zarządzanie licencjami w organizacji na podstawie grupy licencji, konieczne będzie planu migracji bezproblemowo zastąpić istniejące rozwiązania oparte na grupach licencji.

Ważne jest, należy wziąć pod uwagę to, że należy unikać sytuacji, w których migracja do licencjonowania na podstawie grupy spowoduje użytkownicy tymczasowo utraty ich aktualnie przypisane licencje. Aby usunąć ryzyko utraty dostępu do usług i ich danych należy unikać każdy proces, który może spowodować usunięcie licencji.

## <a name="recommended-migration-process"></a>Proces migracji zalecane

1. Masz istniejącej automatyzacji (na przykład programu PowerShell), zarządzanie przypisania licencji i usuwania dla użytkowników. Usługa będzie działać, ponieważ jest.

2. Utwórz nową grupę licencji (lub zdecydować, które istniejących grup do używania) i upewnij się, czy wszystkie wymagane użytkownicy zostaną dodane jako członkowie.

3. Przypisywanie licencji wymagane do tych grup; Celem powinno być uwzględnienie tego samego stanu licencjonowania, które z istniejącej automatyzacji (na przykład programu PowerShell) jest stosowana do tych użytkowników.

4. Upewnij się, że licencje zostały zastosowane do wszystkich użytkowników w tych grupach. Można to zrobić, sprawdzając stan przetwarzania w każdej grupie oraz przez sprawdzanie dzienników inspekcji.

  - Patrząc na ich szczegóły licencji można kontroli poszczególnych użytkowników. Zobaczysz, że mają one taką samą licencji przypisanych "bezpośrednio" i "odziedziczony" z grupy.

  - Można uruchomić skrypt programu PowerShell w celu [Sprawdź sposób przypisywania licencji do użytkowników](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Tej samej licencji produktu jest przypisana do użytkownika zarówno bezpośrednio i za pośrednictwem grupy, tylko jedna licencja jest używane przez użytkownika. Dlatego żadne dodatkowe licencje nie są wymagane do przeprowadzenia migracji.

5. Upewnij się, że nie przypisań licencji nie sprawdzając każdej grupy użytkowników w stanie błędu. Aby uzyskać więcej informacji, zobacz [zidentyfikowanie i rozwiązywaniu problemów z licencji dla grupy](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Rozważ usunięcie oryginalnego przypisania bezpośredniego; możesz zrobić to stopniowo etapami"", najpierw monitorowania wyników na podzestaw użytkowników.

  Można pozostawić oryginalny przypisania bezpośredniego na użytkowników, ale gdy użytkownicy opuszczają ich grup licencji, które zachowują nadal oryginalna licencja jest prawdopodobnie nie ma, które chcesz.

## <a name="an-example"></a>Przykład

Mamy organizacji z 1000 użytkowników. Wszyscy użytkownicy wymagają Enterprise Mobility + Security (EMS) licencji. 200 użytkowników są pracownikiem działu finansowego i wymagają licencji Office 365 Enterprise E3. Mamy uruchomiony lokalnie, dodawanie i usuwanie licencji od użytkowników, jak długo będą pochodzić i przejdź skryptów środowiska PowerShell. Chcemy Zamień skryptu na podstawie grupy licencji, licencje są zarządzane automatycznie przez usługę Azure AD.

Oto, jak może wyglądać procesu migracji:

1. Przy użyciu portalu Azure przypisanie licencji pakietu EMS do **wszyscy użytkownicy** grupy w usłudze Azure AD. Przypisywanie licencji E3 do **działu finansowego** grupy, która zawiera wszystkie wymagane użytkowników.

2. Dla każdej grupy upewnij się, że przypisanie licencji zostało zakończone dla wszystkich użytkowników. Przejdź do bloku dla każdej grupy, wybierz opcję **licencji**i sprawdź stan przetwarzania w górnej części **licencji** bloku.

  - Wyszukaj "Licencji najnowsze zmiany zostały zastosowane do wszystkich użytkowników" Aby upewnić się, przetwarzanie zostało zakończone.

  - Poszukaj powiadomienia w górnej części o wszyscy użytkownicy, dla których licencji mogą nie zostały pomyślnie przypisane. Czy możemy zabraknie licencji dla niektórych użytkowników? W przypadku niektórych użytkowników, czy mają powodujące konflikt licencji jednostki magazynowe, które uniemożliwiają dziedziczenie grupy licencji?

3. Miejsce Sprawdź niektórych użytkowników, aby sprawdzić, czy mają one zarówno bezpośrednie i grup licencji zastosowane. Przejdź do bloku dla użytkownika, zaznacz **licencji**i sprawdź, czy stan licencji.

  - Jest to stan oczekiwany użytkownika podczas migracji:

      ![Stan użytkownika oczekiwany](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Potwierdza to, czy użytkownik ma licencje zarówno bezpośrednio, jak i dziedziczone. Firma Microsoft wyświetlana zarówno **EMS** i **E3** są przypisane.

  - Wybierz każdej licencji, aby wyświetlić szczegółowe informacje o włączone usługi. Może to służyć do Sprawdź dokładnie tego samego planów usługi dla użytkownika włączyć bezpośredniego i grup licencji.

      ![Sprawdź planów usługi](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. Po potwierdzeniu, że zarówno bezpośrednio, jak i grupy licencji są równoważne, można uruchomić, usunięcie licencji bezpośrednio od użytkowników. Można to sprawdzić, usuwając je dla poszczególnych użytkowników w portalu, a następnie uruchom skryptów automatyzacji je usunąć zbiorczo. Oto przykład tego samego użytkownika z licencjami bezpośredniego usunięte za pośrednictwem portalu. Zwróć uwagę, stan licencji nie jest zmieniany, że firma Microsoft nie jest już wyświetlana bezpośredniego przypisania.

  ![bezpośrednie licencje usunięte](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencji za pomocą grup, do odczytu

* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identyfikowanie i rozwiązywanie problemów z licencji dla grupy w usłudze Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Usługa Azure Active Directory na podstawie grupy licencjonowania dodatkowe scenariusze](active-directory-licensing-group-advanced.md)
