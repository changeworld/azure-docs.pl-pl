---
title: Wyświetlanie, dodawanie i usuwanie przydziałów dla pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD — usługa Azure Active Directory
description: Dowiedz się, jak wyświetlać, dodawać i usuwać przydziały dla pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262024"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Wyświetlanie, dodawanie i usuwanie przydziałów dla pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

W zarządzania uprawnieniami usługi Azure AD można zobaczyć, kto został przypisany do pakietów dostępu, ich zasad i stanu. Jeśli pakiet dostępu ma odpowiednie zasady, można również bezpośrednio przypisać użytkownika do pakietu dostępu. W tym artykule opisano sposób wyświetlania, dodawania i usuwania przypisań dla pakietów dostępu.

## <a name="view-who-has-an-assignment"></a>Wyświetlanie, kto ma przypisanie

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Przydziały,** aby wyświetlić listę aktywnych przypisań.

    ![Lista przypisań do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Kliknij określone przypisanie, aby wyświetlić dodatkowe szczegóły.

1. Aby wyświetlić listę przypisań, które nie miały poprawnie aprowizowanych wszystkich ról zasobów, kliknij stan filtru i wybierz pozycję **Dostarczanie**.

    Dodatkowe informacje na temat błędów dostarczania można wyświetlić, lokalizując odpowiednie żądanie użytkownika na stronie **Żądania.**

1. Aby wyświetlić wygasłe przydziały, kliknij stan filtru i wybierz pozycję **Wygasłe**.

1. Aby pobrać plik CSV z listy filtrowanych, kliknij przycisk **Pobierz**.

### <a name="viewing-assignments-programmatically"></a>Programowe wyświetlanie przydziałów

Przydziały można również pobierać w pakiecie dostępu za pomocą programu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienie można wywołać interfejsu API do [listy accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Bezpośrednie przypisywanie użytkownika

W niektórych przypadkach można bezpośrednio przypisać określonych użytkowników do pakietu dostępu, dzięki czemu użytkownicy nie muszą przechodzić przez proces żądania pakietu dostępu. Aby bezpośrednio przypisać użytkowników, pakiet dostępu musi mieć zasady, które umożliwiają bezpośrednie przypisania administratora.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **Przydziały**.

1. Kliknij **pozycję Nowe przypisanie,** aby otworzyć aplikuj użytkownika, aby uzyskać dostęp do pakietu.

    ![Przydziały — dodawanie użytkownika do pakietu dostępu](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kliknij **pozycję Dodaj użytkowników,** aby wybrać użytkowników, do których chcesz przypisać ten pakiet dostępu.

1. Na liście **Wybierz zasady** wybierz zasadę, zgodnie z którą przyszłe żądania i cykl życia użytkowników będą zarządzane i śledzone. Jeśli chcesz, aby wybrani użytkownicy mieli różne ustawienia zasad, możesz kliknąć przycisk **Utwórz nowe zasady,** aby dodać nową zasadę.

1. Ustaw datę i godzinę rozpoczęcia i zakończenia przypisania wybranych użytkowników. Jeśli data zakończenia nie zostanie podana, zostaną użyte ustawienia cyklu życia zasad.

1. Opcjonalnie podaj uzasadnienie bezpośredniego przypisania do przechowywania dokumentacji.

1. Kliknij **przycisk Dodaj,** aby bezpośrednio przypisać wybranych użytkowników do pakietu dostępu.

    Po kilku chwilach kliknij przycisk **Odśwież,** aby wyświetlić użytkowników na liście Przydziały.

### <a name="directly-assigning-users-programmatically"></a>Bezpośrednie przypisywanie użytkowników programowo

Można również bezpośrednio przypisać użytkownika do pakietu dostępu za pomocą programu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienia można wywołać interfejsu API, aby [utworzyć accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Usuwanie przypisania

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. W menu po lewej stronie kliknij pozycję **Przydziały**.
 
1. Kliknij pole wyboru obok użytkownika, którego przypisanie chcesz usunąć z pakietu dostępu. 

1. Kliknij przycisk **Usuń** u góry lewego okienka. 
 
    ![Przydziały — usuwanie użytkownika z pakietu dostępu](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Pojawi się powiadomienie informujące o usunięciu przypisania. 

## <a name="next-steps"></a>Następne kroki

- [Zmienianie żądania i ustawień pakietu dostępu](entitlement-management-access-package-request-policy.md)
- [Wyświetlanie raportów i dzienników](entitlement-management-reports.md)
