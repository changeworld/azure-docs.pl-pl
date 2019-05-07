---
title: Dodaj indywidualnie licencjonowanych użytkowników do Licencjonowanie oparte na grupach — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak przeprowadzić migrację z licencji użytkownika do licencjonowania opartego na grupach, przy użyciu usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192454"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Jak przeprowadzić migrację użytkowników z licencjami poszczególnych grup licencjonowania

Masz istniejące licencje wdrożone dla użytkowników w organizacji za pośrednictwem "bezpośredniego przypisania"; oznacza to za pomocą skryptów programu PowerShell lub innych narzędzi, aby przypisać licencje do poszczególnych użytkowników. Przed rozpoczęciem korzystania Licencjonowanie na podstawie grupy, aby zarządzać licencjami w organizacji, można użyć ten plan migracji można bezproblemowo zastąpić istniejące rozwiązania z licencjonowaniem opartym na grupie.

Ważne jest, aby pamiętać o to, że należy unikać sytuacji, w których migracja do Licencjonowanie na podstawie grupy spowoduje tymczasowo utraty ich aktualnie przypisanych licencji użytkowników. Każdy proces, który może spowodować usunięcie licencji należy unikać do usunięcia ryzyko utraty dostępu do usług i ich danych.

## <a name="recommended-migration-process"></a>Proces migracji zalecane

1. Masz istniejące usługi automation (na przykład programu PowerShell), zarządzanie przypisania licencji i usuwania dla użytkowników. Pozostaw będzie działać, ponieważ jest.

2. Utwórz nową grupę licencji (lub zdecydować, które istniejących grup do użycia) i upewnij się, że wszystkie wymagane użytkownicy są dodawani jako członkowie.

3. Przypisywanie licencji do tych grup; Celem powinno być w celu odzwierciedlenia takiego samego stanu licencjonowania, które istniejącej usługi automation (na przykład programu PowerShell) są stosowane do tych użytkowników.

4. Sprawdź, czy licencje zostały zastosowane do wszystkich użytkowników w tych grupach. Ta aplikacja może odbywać się sprawdzając stan przetwarzania dla każdej grupy i sprawdzając dzienniki inspekcji.

   - Możesz poszczególnych użytkowników w miejscu, analizując ich szczegóły licencji. Zostanie wyświetlony, mają te same licencje przypisane "bezpośrednio" i "dziedziczone" z grupy.

   - Można uruchomić skryptu programu PowerShell, aby [Sprawdź, jak są przypisywane licencje do użytkowników](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Po przypisaniu licencji produktu w tym samym użytkownikowi zarówno bezpośrednio i za pośrednictwem grupy, tylko jedna licencja jest wykorzystywane przez użytkownika. Dlatego żadne dodatkowe licencje są wymagane do przeprowadzenia migracji.

5. Upewnij się, że żadnych przypisań licencji nie powiodło się, sprawdzając w każdej grupie użytkowników w stanie błąd. Aby uzyskać więcej informacji, zobacz [rozpoznawanie i rozwiązywania problemów z licencją dla grupy](licensing-groups-resolve-problems.md).

6. Rozważ usunięcie oryginalnego przypisań bezpośrednich; Można to zrobić w stopniowo, "etapami", najpierw monitorować wyniki dla podzbioru użytkowników.

   Można pozostawić oryginalny przypisań bezpośrednich, użytkownicy, ale gdy użytkownicy opuszczają ich grup licencji nadal zachowa oryginalnej licencji, który ma prawdopodobnie nie ma.

## <a name="an-example"></a>Przykład

Organizacja ma 1000 użytkowników jednocześnie. Wszyscy użytkownicy wymagają pakietu Enterprise Mobility + Security (EMS) licencji. 200 użytkownikom w dziale finansowym i wymagają licencji usługi Office 365 Enterprise E3. Obecnie organizacja ma uruchomiony lokalnie, dodawanie i usuwanie licencji użytkowników, jak długo będą pochodzić i przejdź skrypt programu PowerShell. Jednak organizacja chce Zamień skryptu oparte na grupach licencji, licencje mogą automatycznie zarządzane przez usługę Azure AD.

Poniżej przedstawiono, jak może wyglądać procesu migracji:

1. W witrynie Azure portal, przypisać licencji pakietu EMS do **wszyscy użytkownicy** grupy w usłudze Azure AD. Przypisać licencję E3 **pracownicy działu finansowego** grupy zawierającej wszystkich wymaganych użytkowników.

2. Dla każdej grupy upewnij się, że przypisanie licencji zakończył się dla wszystkich użytkowników. Przejdź do bloku dla każdej grupy wybierz **licencji**i sprawdzić stan przetwarzania w górnej części **licencji** bloku.

   - Wyszukaj "Licencję najnowsze zmiany zostały zastosowane do wszystkich użytkowników" Aby upewnić się, przetwarzanie zostało zakończone.

   - Poszukaj powiadomień u góry dotyczących wszystkich użytkowników, dla których licencji mogą nie zostały pomyślnie przypisane. Uruchomiliśmy już licencji dla niektórych użytkowników? Niektórzy użytkownicy mają licencję powodujące konflikt jednostek SKU, które uniemożliwiają dziedziczenie grupy licencji?

3. Miejscu Sprawdź niektórych użytkowników, aby sprawdzić, czy mają one zarówno bezpośrednich i grupy licencji stosowane. Przejdź do bloku dla użytkownika, wybierz opcję **licencji**oraz zbadania stanu licencji.

   - Jest to stan oczekiwany użytkownika podczas migracji:

      ![Stan oczekiwany użytkownika podczas migracji](./media/licensing-groups-migrate-users/expected-user-state.png)

   Będzie to potwierdzenie, że użytkownik ma licencje dziedziczone i bezpośrednie. Widzimy, że oba **EMS** i **E3** są przypisane.

   - Wybierz każdej licencji, aby wyświetlić szczegółowe informacje o usługach włączone. Może to służyć do sprawdzania, jeśli bezpośrednie i grupy licencji, Włącz dokładnie tych samych planów usługi dla użytkownika.

      ![Sprawdź planów usług dla użytkownika](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Po potwierdzeniu, że zarówno bezpośrednich, jak i grupy licencji są równoważne, można uruchomić, usunięcie bezpośrednie licencji użytkowników. Można testować tę aplikację, usuwając je dla poszczególnych użytkowników w portalu, a następnie uruchom skrypty automatyzacji, aby były usuwane w trybie zbiorczym. Oto przykład tego samego użytkownika z licencjami bezpośrednie usunięte za pośrednictwem portalu. Zwróć uwagę, stan licencji pozostaje niezmieniony, że firma Microsoft nie jest już wyświetlana przypisań bezpośrednich.

   ![Upewnij się, że licencje bezpośredniego są usuwane](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak inne scenariusze dotyczące zarządzania licencjami za pomocą grup, przeczytaj

* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
* [Jak przeprowadzić migrację użytkowników między licencjami produktów za pomocą licencjonowania opartego na grupy w usłudze Azure Active Directory](licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupy w usłudze Azure Active Directory](licensing-ps-examples.md)
