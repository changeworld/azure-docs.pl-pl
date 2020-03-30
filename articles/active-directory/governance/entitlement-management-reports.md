---
title: Wyświetlanie raportów & dzienników w zarządzaniu uprawnieniami — usługa Azure AD
description: Dowiedz się, jak wyświetlić raport przydziałów użytkowników i dzienniki inspekcji w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128419"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Wyświetlanie raportów i dzienników w zarządzaniu uprawnieniami usługi Azure AD

Raporty zarządzania uprawnieniami usługi Azure AD i dziennik inspekcji usługi Azure AD zawierają dodatkowe informacje o tym, do jakich zasobów użytkownicy mają dostęp. Jako administrator można wyświetlać pakiety dostępu i przydziały zasobów dla użytkownika i wyświetlać dzienniki żądań do celów inspekcji lub w celu określenia stanu żądania użytkownika. W tym artykule opisano sposób korzystania z raportów zarządzania uprawnieniami i dzienników inspekcji usługi Azure AD.

Obejrzyj poniższy klip wideo, aby dowiedzieć się, jak wyświetlić zasoby, do których użytkownicy mają dostęp w zarządzaniu uprawnieniami:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Wyświetlanie pakietów dostępu dla użytkownika

Ten raport umożliwia wyświetlanie listy wszystkich pakietów dostępu, których użytkownik może zażądać, oraz pakietów dostępu, które są aktualnie przypisane do użytkownika.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję Zarządzanie **tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Raporty**.

1. Kliknij **pozycję Pakiety programu Access dla użytkownika**.

1. Kliknij **pozycję Wybierz użytkowników,** aby otworzyć okienko Wybierz użytkowników.

1. Znajdź użytkownika na liście, a następnie kliknij przycisk **Wybierz**.

    Karta **Może żądać** wyświetla listę pakietów dostępu, o które użytkownik może zażądać. Ta lista jest określana przez [zasady żądania](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) zdefiniowane dla pakietów dostępu. 

    ![Dostęp do pakietów dla użytkownika](./media/entitlement-management-reports/access-packages-report.png)

1. Jeśli istnieje więcej niż jedna rola zasobów lub zasady dla pakietu dostępu, kliknij wpis ról zasobów lub zasad, aby wyświetlić szczegóły wyboru.

1. Kliknij kartę **Przypisane,** aby wyświetlić listę pakietów dostępu aktualnie przypisanych do użytkownika. Gdy pakiet dostępu jest przypisany do użytkownika, oznacza to, że użytkownik ma dostęp do wszystkich ról zasobów w pakiecie dostępu.

## <a name="view-resource-assignments-for-a-user"></a>Wyświetlanie przydziałów zasobów dla użytkownika

Ten raport umożliwia wyświetlanie listy zasobów aktualnie przypisanych do użytkownika w zarządzaniu uprawnieniami. Należy zauważyć, że ten raport dotyczy zasobów zarządzanych za pomocą zarządzania uprawnieniami. Użytkownik może mieć dostęp do innych zasobów w katalogu poza zarządzaniem uprawnieniami.

**Rola wstępna:** Administrator globalny lub administrator użytkownika

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję Zarządzanie **tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Raporty**.

1. Kliknij **pozycję Przydziały zasobów dla użytkownika**.

1. Kliknij **pozycję Wybierz użytkowników,** aby otworzyć okienko Wybierz użytkowników.

1. Znajdź użytkownika na liście, a następnie kliknij przycisk **Wybierz**.

    Zostanie wyświetlona lista zasobów aktualnie przypisanych do użytkownika. Lista zawiera również pakiet dostępu i zasady, z których otrzymały rolę zasobu, wraz z datą rozpoczęcia i zakończenia dostępu.
    
    Jeśli użytkownik uzyskał dostęp do tego samego zasobu w dwóch lub więcej pakietach, możesz kliknąć strzałkę, aby wyświetlić każdy pakiet i zasady.

    ![Przydziały zasobów dla użytkownika](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Określanie stanu żądania użytkownika

Aby uzyskać dodatkowe szczegółowe informacje na temat sposobu żądania i odebranego dostępu do pakietu dostępu, można użyć dziennika inspekcji usługi Azure AD. W szczególności można użyć rekordów `EntitlementManagement` dziennika `UserManagement` w i kategorie, aby uzyskać dodatkowe informacje na temat kroków przetwarzania dla każdego żądania.  

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję **Dzienniki inspekcji**.

1. U góry zmień **kategorię** na `EntitlementManagement` jedną `UserManagement`lub , w zależności od rekordu inspekcji, którego szukasz.  

1. Kliknij przycisk **Zastosuj**.

1. Aby pobrać dzienniki, kliknij przycisk **Pobierz**.

Gdy usługa Azure AD odbiera nowe żądanie, zapisuje rekord inspekcji, w którym `User requests access package assignment` **kategoria** jest `EntitlementManagement` i **działanie** jest zazwyczaj .  W przypadku przypisania bezpośredniego utworzonego w **Activity** witrynie Azure portal `Administrator directly assigns user to access package`pole Działanie rekordu inspekcji jest , a użytkownik wykonujący przypisanie jest identyfikowany przez **ActorUserPrincipalName**.

Usługa Azure AD będzie zapisywać dodatkowe rekordy inspekcji, gdy żądanie jest w toku, w tym:

| Kategoria | Działanie | Stan żądania |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Żądanie nie wymaga zatwierdzenia |
| `UserManagement` | `Create request approval` | Wniosek wymaga zatwierdzenia |
| `UserManagement` | `Add approver to request approval` | Wniosek wymaga zatwierdzenia |
| `EntitlementManagement` | `Approve access package assignment request` | Wniosek zatwierdzony |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Wniosek zatwierdzony lub nie wymaga zatwierdzenia |

Gdy użytkownikowi jest przypisany dostęp, usługa Azure AD `EntitlementManagement` zapisuje rekord inspekcji dla kategorii z **działaniem** `Fulfill access package assignment`.  Użytkownik, który otrzymał dostęp jest identyfikowany przez **ActorUserPrincipalName** pola.

Jeśli dostęp nie został przypisany, usługa Azure AD `EntitlementManagement` zapisuje rekord `Deny access package assignment request`inspekcji dla kategorii z `Access package assignment request timed out (no approver action taken)` **działaniem** albo, jeśli żądanie zostało odrzucone przez osobę zatwierdzającą, lub , jeśli upłynie limit czasu żądania, zanim osoba zatwierdzająca może zatwierdzić.

Po wygaśnięciu przypisania pakietu dostępu użytkownika, jest anulowany przez użytkownika lub usunięte przez administratora, `EntitlementManagement` a następnie usługa `Remove access package assignment`Azure AD zapisuje rekord inspekcji dla kategorii z **działaniem** .

## <a name="next-steps"></a>Następne kroki

- [Archiwizowanie raportów i dzienników](entitlement-management-logs-and-reporting.md)
- [Rozwiązywanie problemów z zarządzaniem uprawnieniami usługi Azure AD](entitlement-management-troubleshoot.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
