---
title: Wyświetlanie raportów i dzienników w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak wyświetlić raport dotyczący przypisań użytkowników i dzienniki inspekcji w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
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
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2420fc25795ec74939649fb8a17ead7c8cfdd1df
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032450"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Wyświetlanie raportów i dzienników w usłudze Azure AD uprawnia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Raport dotyczący przypisań użytkowników i dziennik inspekcji Azure Active Directory zawierają dodatkowe informacje o użytkownikach w katalogu. Jako administrator możesz wyświetlić zasoby, do których użytkownik ma dostęp, i wyświetlić dzienniki żądań na potrzeby inspekcji lub określić stan żądania użytkownika. W tym artykule opisano sposób korzystania z raportów o przypisaniach użytkowników i dziennikach inspekcji usługi Azure AD.

Obejrzyj poniższe wideo, aby dowiedzieć się, jak za pomocą zarządzania uprawnieniami zarządzać dostępem użytkowników w Azure Active Directory:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-resources-a-user-has-access-to"></a>Wyświetlanie zasobów, do których użytkownik ma dostęp

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **raport dotyczący przypisań użytkowników**.

1. Kliknij pozycję **Wybierz użytkowników** , aby otworzyć okienko Wybieranie użytkowników.

1. Znajdź użytkownika na liście, dla którego chcesz wyświetlić zasoby, do których mają dostęp.

1. Kliknij użytkownika, a następnie kliknij przycisk **Wybierz**.

    Zostanie wyświetlona lista zasobów, do których użytkownik ma dostęp. Obejmuje to pakiet dostępu, zasady i daty.

    ![Raport przypisań użytkowników](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Określanie stanu żądania użytkownika

Aby uzyskać dodatkowe informacje o tym, jak użytkownik zażądał i otrzymał dostęp do pakietu dostępu, możesz użyć dziennika inspekcji usługi Azure AD. W szczególności można użyć rekordów dziennika w `EntitlementManagement` kategorii i `UserManagement` , aby uzyskać dodatkowe szczegóły dotyczące kroków przetwarzania dla każdego żądania.  

1. Kliknij **Azure Active Directory** a następnie kliknij pozycję **dzienniki inspekcji**.

1. W górnej części Zmień **kategorię** na `EntitlementManagement` lub `UserManagement`, w zależności od szukanego rekordu inspekcji.  

1. Kliknij przycisk **zastosować**.

1. Aby pobrać dzienniki, kliknij przycisk **Pobierz**.

Gdy usługa Azure AD otrzymuje nowe żądanie, zapisuje rekord inspekcji, w którym **Kategoria** `EntitlementManagement` jest, a **działanie** jest zwykle `User requests access package assignment`.  W przypadku przypisania bezpośredniego utworzonego w Azure Portal pole **działania** rekordu inspekcji ma `Administrator directly assigns user to access package`wartość, a użytkownik wykonujący przypisanie jest identyfikowany przez **ActorUserPrincipalName**.

Usługa Azure AD będzie zapisywać dodatkowe rekordy inspekcji, gdy żądanie jest w toku, w tym:

| Kategoria | Działanie | Stan żądania |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Żądanie nie wymaga zatwierdzenia |
| `UserManagement` | `Create request approval` | Żądanie wymaga zatwierdzenia |
| `UserManagement` | `Add approver to request approval` | Żądanie wymaga zatwierdzenia |
| `EntitlementManagement` | `Approve access package assignment request` | Żądanie zatwierdzone |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Żądanie zatwierdzone lub nie wymaga zatwierdzenia |

Gdy użytkownik ma przypisany dostęp, usługa Azure AD zapisuje rekord inspekcji dla `EntitlementManagement` kategorii z **działaniem** `Fulfill access package assignment`.  Użytkownik, który otrzymał dostęp, jest identyfikowany przez pole **ActorUserPrincipalName** .

Jeśli dostęp nie został przypisany, usługa Azure AD zapisuje `EntitlementManagement` rekord inspekcji dla kategorii z **działaniem** `Deny access package assignment request`, jeśli żądanie zostało odrzucone przez osobę zatwierdzającą lub `Access package assignment request timed out (no approver action taken)`Jeśli upłynął limit czasu żądania przed zatwierdzeniem przez osobę zatwierdzającą.

Po wygaśnięciu przypisania pakietu dostępu użytkownika użytkownik zostanie anulowany przez użytkownika lub usunięty przez administratora, a następnie usługa Azure AD zapisuje rekord inspekcji dla `EntitlementManagement` kategorii z **działaniem** . `Remove access package assignment`

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z zarządzaniem prawami usługi Azure AD](entitlement-management-troubleshoot.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
