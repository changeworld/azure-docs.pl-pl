---
title: Dodawanie użytkowników z licencjami bezpośrednimi do licencjonowania grupowego — Azure AD | Dokumenty firmy Microsoft
description: Jak przeprowadzić migrację z licencji poszczególnych użytkowników do licencjonowania grupowego przy użyciu usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025678"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Jak migrować użytkowników z indywidualnymi licencjami do grup w celu licencjonowania

Możesz mieć istniejące licencje wdrożone dla użytkowników w organizacjach za pomocą bezpośredniego przypisania; oznacza to, że przy użyciu skryptów programu PowerShell lub innych narzędzi do przypisywania licencji poszczególnych użytkowników. Przed rozpoczęciem korzystania z licencjonowania opartego na grupach do zarządzania licencjami w organizacji można użyć tego planu migracji, aby bezproblemowo zastąpić istniejące rozwiązania licencjami opartymi na grupach.

Najważniejszą rzeczą, o której należy pamiętać, jest unikanie sytuacji, w której migracja do licencjonowania opartego na grupach spowoduje, że użytkownicy tymczasowo utracą aktualnie przypisane licencje. Należy unikać wszelkich procesów, które mogą skutkować usunięciem licencji, aby wyeliminować ryzyko utraty przez użytkowników dostępu do usług i ich danych.

## <a name="recommended-migration-process"></a>Zalecany proces migracji

1. Istnieje istniejąca automatyzacja (na przykład Program PowerShell) zarządzająca przypisywaniem i usuwaniem licencji dla użytkowników. Pozostaw go uruchomionym tak, jak jest.

1. Utwórz nową grupę licencjonowania (lub zdecyduj, które istniejące grupy mają być używane) i upewnij się, że wszyscy wymagali użytkownicy są dodawane jako członkowie.

1. Przypisywanie wymaganych licencji do tych grup; Twoim celem powinno być odzwierciedlenie tego samego stanu licencjonowania istniejącej automatyzacji (na przykład Programu PowerShell) ma zastosowanie do tych użytkowników.

1. Sprawdź, czy licencje zostały zastosowane do wszystkich użytkowników w tych grupach. Tę aplikację można wykonać, sprawdzając stan przetwarzania w każdej grupie i sprawdzając dzienniki inspekcji.

   - Możesz na miejscu sprawdzić poszczególnych użytkowników, patrząc na ich szczegóły licencji. Zobaczysz, że mają te same licencje przypisane "bezpośrednio" i "odziedziczone" z grup.

   - Można uruchomić skrypt programu PowerShell, aby [sprawdzić, w jaki sposób licencje są przypisywane do użytkowników](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Gdy ta sama licencja produktu jest przypisana do użytkownika zarówno bezpośrednio, jak i za pośrednictwem grupy, użytkownik używa tylko jednej licencji. W związku z tym do przeprowadzenia migracji nie są wymagane żadne dodatkowe licencje.

1. Sprawdź, czy żadne przypisanie licencji nie powiodło się, sprawdzając każdą grupę dla użytkowników w stanie błędu. Aby uzyskać więcej informacji, zobacz [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy](licensing-groups-resolve-problems.md).

Rozważ usunięcie oryginalnych przypisań bezpośrednich. Zaleca się, aby zrobić to stopniowo i najpierw monitorować wynik na podgrupie użytkowników. Jeśli można zostawić oryginalne przypisania bezpośrednie dla użytkowników, ale gdy użytkownicy opuszczają swoje licencjonowane grupy zachowują bezpośrednio przypisane licencje, które mogą nie być tym, co chcesz.

## <a name="an-example"></a>Przykład

Organizacja ma 1000 użytkowników. Wszyscy użytkownicy wymagają licencji usługi Office 365 Enterprise E3. Obecnie organizacja ma skrypt programu PowerShell uruchomiony lokalnie, dodając i usuwając licencje od użytkowników, gdy przychodzą i odchodzą. Jednak organizacja chce zastąpić skrypt licencjonowaniem opartym na grupach, dzięki czemu licencje mogą być zarządzane automatycznie przez usługę Azure AD.

Oto, jak może wyglądać proces migracji:

1. Korzystając z witryny Azure portal, przypisz licencję usługi Office 365 E3 do grupy **Wszyscy użytkownicy** w usłudze Azure AD.

1. Upewnij się, że przypisanie licencji zostało ukończone dla wszystkich użytkowników. Przejdź do strony przeglądu grupy, wybierz pozycję **Licencje**i sprawdź stan przetwarzania u góry bloku **Licencje.**

   - Poszukaj "Najnowsze zmiany licencji zostały zastosowane do wszystkich użytkowników", aby potwierdzić, że przetwarzanie zostało zakończone.

   - Poszukaj powiadomienia na górze o wszystkich użytkownikach, dla których licencje mogły nie zostać pomyślnie przypisane. Czy zabrakło nam licencji dla niektórych użytkowników? Czy niektórzy użytkownicy mają sprzeczne plany licencji, które uniemożliwiają im dziedziczenie licencji grupowych?

1. Sprawdź punktowe niektórych użytkowników, aby sprawdzić, czy mają zastosowane zarówno licencje bezpośrednie, jak i grupowe. Przejdź do strony profilu użytkownika, wybierz pozycję **Licencje**i sprawdź stan licencji.

   - Jest to oczekiwany stan użytkownika podczas migracji:

      ![oczekiwany stan użytkownika podczas migracji](./media/licensing-groups-migrate-users/expected-user-state.png)

     Potwierdza to, że użytkownik ma zarówno licencje bezpośrednie, jak i dziedziczone. Widzimy, że usługa Office 365 E3 jest przypisana.

   - Wybierz każdą licencję, aby zobaczyć, które usługi są włączone. Aby sprawdzić, czy licencje bezpośrednie i grupowe umożliwiają użytkownikowi dokładnie te same usługi, wybierz pozycję **Przydziały**.

1. Po potwierdzeniu, że licencje bezpośrednie i grupowe są równoważne, można rozpocząć usuwanie licencji bezpośrednich od użytkowników. Można to przetestować, usuwając je dla poszczególnych użytkowników w portalu, a następnie uruchom skrypty automatyzacji, aby je usunąć zbiorczo. Oto przykład tego samego użytkownika z bezpośrednimi licencjami usuniętymi za pośrednictwem portalu. Należy zauważyć, że stan licencji pozostaje niezmieniony, ale nie widzimy już bezpośrednich przypisań.

   ![potwierdzić, że licencje bezpośrednie są usuwane](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych scenariuszach zarządzania licencjami grupowymi:

- [Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrować użytkowników między licencjami produktów przy użyciu licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-groups-change-licenses.md)
- [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
- [Przykłady programu PowerShell dotyczące licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-ps-examples.md)
