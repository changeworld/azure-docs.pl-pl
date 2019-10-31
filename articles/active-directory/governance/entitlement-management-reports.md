---
title: Wyświetlanie raportów i dzienników w usłudze Azure AD uprawnień zarządzanie — Azure Active Directory
description: Dowiedz się, jak wyświetlić raport dotyczący przypisań użytkowników i dzienniki inspekcji w Azure Active Directory Zarządzanie uprawnieniami.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3302fc3b2513794cd66d1ebf6db2cbcdb0f713dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173886"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Wyświetlanie raportów i dzienników w usłudze Azure AD uprawnień zarządzania

Raporty zarządzania prawami usługi Azure AD i dziennik inspekcji usługi Azure AD zawierają dodatkowe informacje o zasobach, do których użytkownicy mają dostęp. Jako administrator można wyświetlić pakiety dostępu i przydziały zasobów dla użytkownika i wyświetlić dzienniki żądań na potrzeby inspekcji lub określić stan żądania użytkownika. W tym artykule opisano sposób korzystania z raportów zarządzania uprawnieniami i dzienników inspekcji usługi Azure AD.

Obejrzyj poniższe wideo, aby dowiedzieć się, jak wyświetlać zasoby, do których użytkownicy mają dostęp w usłudze zarządzania uprawnieniami:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Wyświetlanie pakietów dostępu dla użytkownika

Ten raport umożliwia wyświetlenie listy wszystkich pakietów dostępu, które użytkownik może żądać, oraz pakietów dostępu, które są aktualnie przypisane do użytkownika.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **raporty**.

1. Kliknij pozycję **dostęp do pakietów dla użytkownika**.

1. Kliknij pozycję **Wybierz użytkowników** , aby otworzyć okienko Wybieranie użytkowników.

1. Znajdź użytkownika na liście, a następnie kliknij przycisk **Wybierz**.

    Na karcie **żądanie może** zostać wyświetlona lista pakietów dostępu, które użytkownik może zażądać. Ta lista jest określana przez [zasady żądań](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) zdefiniowane dla pakietów dostępu. 

    ![Uzyskaj dostęp do pakietów dla użytkownika](./media/entitlement-management-reports/access-packages-report.png)

1. Jeśli istnieje więcej niż jedna rola zasobu lub zasady dla pakietu dostępu, kliknij wpis role lub zasady zasobów, aby wyświetlić szczegóły wyboru.

1. Kliknij **przypisaną** kartę, aby wyświetlić listę pakietów dostępu aktualnie przypisanych do użytkownika. Gdy pakiet dostępu zostanie przypisany do użytkownika, oznacza to, że użytkownik ma dostęp do wszystkich ról zasobów w pakiecie dostępu.

## <a name="view-resource-assignments-for-a-user"></a>Wyświetlanie przypisań zasobów dla użytkownika

Ten raport umożliwia wyświetlenie listy zasobów aktualnie przypisanych do użytkownika w usłudze zarządzania uprawnieniami. Należy zauważyć, że ten raport dotyczy zasobów zarządzanych za pomocą zarządzania uprawnieniami. Użytkownik może mieć dostęp do innych zasobów w katalogu poza zarządzaniem prawami.

**Rola wymagana wstępnie:** Administrator globalny lub administrator użytkowników

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **raporty**.

1. Kliknij pozycję **przypisania zasobów dla użytkownika**.

1. Kliknij pozycję **Wybierz użytkowników** , aby otworzyć okienko Wybieranie użytkowników.

1. Znajdź użytkownika na liście, a następnie kliknij przycisk **Wybierz**.

    Zostanie wyświetlona lista zasobów aktualnie przypisanych do użytkownika. Na liście przedstawiono również pakiet i zasady dostępu, od których uzyskano rolę zasobu, a także datę początkową i końcową dostępu.
    
    Jeśli użytkownik uzyskał dostęp do tego samego zasobu w co najmniej dwóch pakietach, można kliknąć strzałkę, aby zobaczyć każdy pakiet i zasady.

    ![Przypisania zasobów dla użytkownika](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Określanie stanu żądania użytkownika

Aby uzyskać dodatkowe informacje o tym, jak użytkownik zażądał i otrzymał dostęp do pakietu dostępu, możesz użyć dziennika inspekcji usługi Azure AD. W szczególności można użyć rekordów dziennika w kategorii `EntitlementManagement` i `UserManagement`, aby uzyskać dodatkowe szczegóły dotyczące kroków przetwarzania dla każdego żądania.  

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **dzienniki inspekcji**.

1. W górnej części Zmień **kategorię** na `EntitlementManagement` lub `UserManagement`, w zależności od szukanego rekordu inspekcji.  

1. Kliknij przycisk **Zastosuj**.

1. Aby pobrać dzienniki, kliknij przycisk **Pobierz**.

Gdy usługa Azure AD otrzymuje nowe żądanie, zapisuje rekord inspekcji, w którym **Kategoria** jest `EntitlementManagement` a **działanie** jest zazwyczaj `User requests access package assignment`.  W przypadku przypisania bezpośredniego utworzonego w Azure Portal pole **działania** rekordu inspekcji jest `Administrator directly assigns user to access package`, a użytkownik wykonujący przypisanie jest identyfikowany przez **ActorUserPrincipalName**.

Usługa Azure AD będzie zapisywać dodatkowe rekordy inspekcji, gdy żądanie jest w toku, w tym:

| Kategoria | Działanie | Stan żądania |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Żądanie nie wymaga zatwierdzenia |
| `UserManagement` | `Create request approval` | Żądanie wymaga zatwierdzenia |
| `UserManagement` | `Add approver to request approval` | Żądanie wymaga zatwierdzenia |
| `EntitlementManagement` | `Approve access package assignment request` | Żądanie zatwierdzone |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Żądanie zatwierdzone lub nie wymaga zatwierdzenia |

Gdy użytkownik ma przypisany dostęp, usługa Azure AD zapisuje rekord inspekcji dla kategorii `EntitlementManagement` z `Fulfill access package assignment`**działania** .  Użytkownik, który otrzymał dostęp, jest identyfikowany przez pole **ActorUserPrincipalName** .

Jeśli nie przypisano dostępu, usługa Azure AD zapisuje rekord inspekcji dla kategorii `EntitlementManagement` z **działaniem** `Deny access package assignment request`, jeśli żądanie zostało odrzucone przez osobę zatwierdzającą lub `Access package assignment request timed out (no approver action taken)`, jeśli upłynął limit czasu żądania przed zatwierdzeniem przez osobę zatwierdzającą.

Gdy przypisanie pakietu dostępu użytkownika wygaśnie, zostało anulowane przez użytkownika lub usunięte przez administratora, usługa Azure AD zapisuje rekord inspekcji dla kategorii `EntitlementManagement` z **działaniami** `Remove access package assignment`.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z zarządzaniem prawami usługi Azure AD](entitlement-management-troubleshoot.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
