---
title: Wyświetlanie raportów i dzienników w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak wyświetlić raport przypisania użytkownika i dzienniki inspekcji w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541543"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Wyświetlanie raportów i dzienniki w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Wyświetl zasoby, których użytkownik ma dostęp do

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **zarządzania tożsamościami oraz**.

1. W menu po lewej stronie kliknij **raport przypisania użytkownika**.

1. Kliknij przycisk **wybranym użytkownikom** aby otworzyć okienko wybrani użytkownicy.

1. Znajdź użytkownika, na liście, który chcesz wyświetlić zasoby, które mają dostęp.

1. Kliknij nazwę użytkownika, a następnie kliknij przycisk **wybierz**.

    Zostanie wyświetlona lista zasobów, których użytkownik ma dostęp. Obejmuje dostęp do pakietu, zasad i daty.

    ![Raport przypisania użytkownika](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Sprawdź stan żądania użytkownika

Aby uzyskać więcej szczegółów na temat jak użytkownik zażądał i uzyskania dostępu do pakietu dostępu, można użyć w dzienniku inspekcji usługi Azure AD. W szczególności można użyć rekordy dziennika w `EntitlementManagement` i `UserManagement` kategorie, aby uzyskać więcej informacji na temat kroków przetwarzania dla każdego żądania.  

1. Kliknij przycisk **usługi Azure Active Directory** a następnie kliknij przycisk **dzienniki inspekcji**.

1. U góry strony, należy zmienić **kategorii** do jednej `EntitlementManagement` lub `UserManagement`, w zależności od rekord inspekcji, czego szukasz.  

1. Kliknij przycisk **Zastosuj**.

1. Aby pobrać dzienniki, kliknij **Pobierz**.

Gdy usługi Azure AD, otrzymuje nowe żądanie, zapisuje rekord inspekcji, w którym **kategorii** jest `EntitlementManagement` i **działania** jest zazwyczaj `User requests access package assignment`.  W przypadku przypisania bezpośredniego utworzone w witrynie Azure portal **działania** pole rekordu inspekcji jest `Administrator directly assigns user to access package`, a użytkownik wykonujący przypisanie jest identyfikowane za pomocą **ActorUserPrincipalName**.

Usługi Azure AD będą zapisywane rekordów inspekcji dodatkowe, gdy żądanie jest w toku, w tym:

| Category | Działanie | Stan żądania |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Żądanie nie wymaga zatwierdzenia |
| `UserManagement` | `Create request approval` | Żądanie wymaga zatwierdzenia |
| `UserManagement` | `Add approver to request approval` | Żądanie wymaga zatwierdzenia |
| `EntitlementManagement` | `Approve access package assignment request` | Żądanie zatwierdzone |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Żądania zatwierdzenia lub nie wymaga zatwierdzenia |

Gdy użytkownik jest przypisany dostęp, usługi Azure AD zapisuje rekord inspekcji dla `EntitlementManagement` kategorii z **działania** `Fulfill access package assignment`.  Użytkownik, który otrzymał dostęp jest identyfikowany przez **ActorUserPrincipalName** pola.

Jeśli dostęp nie została przypisana, a następnie usługi Azure AD zapisuje rekord inspekcji dla `EntitlementManagement` kategorii z **działania** albo `Deny access package assignment request`, jeśli żądanie zostało odrzucone przez osobę zatwierdzającą lub `Access package assignment request timed out (no approver action taken)`, jeśli upłynął limit czasu żądania przed Osoba zatwierdzająca może zatwierdzić.

Po wygaśnięciu przypisywania pakietów dostęp użytkownika jest anulowane przez użytkownika lub usunięte przez administratora, a następnie usługi Azure AD zapisuje rekord inspekcji dla `EntitlementManagement` kategorii z **działania** z `Remove access package assignment`.

## <a name="next-steps"></a>Kolejne kroki

- [Rozwiązywanie problemów z zarządzaniem uprawnień usługi Azure AD](entitlement-management-troubleshoot.md)
- [Typowe scenariusze](entitlement-management-scenarios.md)
