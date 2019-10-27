---
title: Wyświetlanie, Dodawanie i usuwanie przypisań pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak wyświetlać, dodawać i usuwać przypisania dla pakietu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a20ff526ba00f5b6521198122972071360bb58d6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934491"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Wyświetlanie, Dodawanie i usuwanie przypisań pakietu dostępu w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W usłudze Azure AD uprawnienia do zarządzania można zobaczyć, kto został przypisany do pakietów, ich zasad i stanu. Jeśli pakiet dostępu ma odpowiednie zasady, możesz również bezpośrednio przypisać użytkownika do pakietu dostępu. W tym artykule opisano sposób wyświetlania, dodawania i usuwania przypisań dla pakietów dostępu.

## <a name="view-who-has-an-assignment"></a>Wyświetl, kto ma przypisanie

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. Kliknij pozycję **przypisania** , aby wyświetlić listę aktywnych przypisań.

    ![Lista przypisań do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Aby wyświetlić listę przypisań, dla których nie zainicjowano prawidłowo wszystkich ról zasobów, kliknij stan filtru i wybierz pozycję **dostarczanie**.

    Aby wyświetlić dodatkowe szczegóły dotyczące błędów dostarczania, można zlokalizować odpowiednie żądanie użytkownika na stronie **żądania** .

1. Aby wyświetlić wygasłe przypisania, kliknij stan filtru i wybierz pozycję **wygasłe**.

1. Aby pobrać plik CSV z filtrowanej listy, kliknij przycisk **Pobierz**.

## <a name="directly-assign-a-user"></a>Bezpośrednie przypisanie użytkownika

W niektórych przypadkach może być konieczne bezpośrednie przypisanie określonych użytkowników do pakietu dostępu, dzięki czemu użytkownicy nie muszą przechodzić przez proces żądania pakietu dostępu. Aby bezpośrednio przypisywać użytkowników, pakiet dostępu musi mieć zasady umożliwiające bezpośrednie przypisania administratora.

**Rola wymagana wstępnie:** Administrator globalny, administrator użytkownika, właściciel katalogu lub Menedżer pakietów dostępu

1. W Azure Portal kliknij pozycję **Azure Active Directory** , a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **pakiety dostępu** , a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **przypisania**.

1. Kliknij pozycję **nowe przypisanie** , aby otworzyć aplet Dodaj użytkownika do pakietu.

    ![Przypisania — Dodawanie użytkownika do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kliknij przycisk **Dodaj użytkowników** , aby wybrać użytkowników, do których chcesz przypisać ten pakiet dostępu.

1. Z listy **Wybierz zasady** wybierz zasady, według których będą podlegać przyszłe żądania i cykl życia użytkowników. Jeśli chcesz, aby wybrani użytkownicy mieli inne ustawienia zasad, kliknij pozycję **Utwórz nowe zasady** , aby dodać nowe zasady.

1. Ustaw datę i godzinę, o której chcesz rozpocząć i zakończyć przypisanie wybranych użytkowników. Jeśli data zakończenia nie zostanie podana, zostaną użyte ustawienia cyklu życia zasad.

1. Opcjonalnie możesz podać uzasadnienie dla bezpośredniego przypisania do przechowywania rekordów.

1. Kliknij przycisk **Dodaj** , aby bezpośrednio przypisać wybranych użytkowników do pakietu dostępu.

    Po kilku chwilach kliknij pozycję **Odśwież** , aby wyświetlić użytkowników na liście przydziałów.

## <a name="next-steps"></a>Następne kroki

- [Żądanie zmiany i ustawienia pakietu dostępu](entitlement-management-access-package-request-policy.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)
