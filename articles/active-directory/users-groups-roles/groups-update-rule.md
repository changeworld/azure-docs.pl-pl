---
title: Aktualizowanie reguły grupy dynamicznej i rozwiązywanie problemów z członkostwem Azure Active Directory | Microsoft Docs
description: Jak utworzyć regułę członkostwa w grupie w Azure Portal, sprawdź stan.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657348"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Aktualizowanie grupy dynamicznej w celu zarządzania członkostwem w Azure Active Directory

W Azure Active Directory (Azure AD) można używać reguł do określania członkostwa w grupach na podstawie właściwości użytkownika lub urządzenia. W tym artykule opisano sposób konfigurowania reguły dla grupy dynamicznej w Azure Portal.
Dynamiczne członkostwo jest obsługiwane dla grup zabezpieczeń lub grup pakietu Office 365. Po zastosowaniu reguły członkostwa w grupie atrybuty użytkownika i urządzenia są oceniane pod kątem zgodności z regułą członkostwa. Gdy atrybut zostanie zmieniony dla użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmiany członkostwa. Użytkownicy i urządzenia są dodawani lub usuwani, jeśli spełnią warunki dla grupy.

Przykłady składni, obsługiwane właściwości, operatory i wartości dla reguły członkostwa można znaleźć w temacie [dynamiczne reguły członkostwa dla grup w Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Aby zaktualizować regułę członkostwa w grupie

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli Administrator globalny, administrator usługi Intune lub administrator użytkowników w dzierżawie.
1. Wybierz kolejno pozycje **grupy** > **wszystkie grupy**.
1. Wybierz grupę, aby otworzyć jej profil.
1. Na stronie profil grupy wybierz pozycję **dynamiczne reguły członkostwa**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać szóste lub dowolne kolejne wyrażenia, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla reguły członkostwa:
   1. Wybierz pozycję **Pobierz niestandardowe właściwości rozszerzenia**
   2. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**.
1. Po zaktualizowaniu reguły wybierz pozycję **Zapisz**.

Jeśli wprowadzona reguła jest nieprawidłowa, wyjaśnienie, dlaczego nie można przetworzyć reguły, jest wyświetlana w powiadomieniach platformy Azure w portalu. Przeczytaj uważnie, aby zrozumieć, jak naprawić regułę.

## <a name="check-processing-status-for-a-rule"></a>Sprawdzanie stanu przetwarzania dla reguły

Na stronie **Przegląd** dla grupy można zobaczyć stan przetwarzania członkostwa i datę ostatniej aktualizacji.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Na potrzeby stanu **przetwarzania członkostwa** można wyświetlić następujące komunikaty o stanie:

* **Ocenianie**:  Odebrano zmianę grupy, a aktualizacje są oceniane.
* **Przetwarzanie**: Trwa przetwarzanie aktualizacji.
* **Aktualizacja zakończona**: Przetwarzanie zostało zakończone i wszystkie odpowiednie aktualizacje zostały wykonane.
* **Błąd przetwarzania**:  Nie można ukończyć przetwarzania z powodu błędu oceny reguły członkostwa.
* **Wstrzymano aktualizację**: Aktualizacje reguł członkostwa dynamicznego zostały wstrzymane przez administratora. MembershipRuleProcessingState jest ustawiona na "wstrzymany".

W przypadku **ostatniej zaktualizowanego stanu członkostwa** można wyświetlić następujące komunikaty o stanie:

* **Data i godzina**: Ostatni raz, gdy członkostwo zostało zaktualizowane.
* **W toku**: Aktualizacje są obecnie w toku.
* **Nieznany**: Nie można pobrać czasu ostatniej aktualizacji. Grupa może być nowa.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, w górnej części **strony Przegląd** dla grupy zostanie wyświetlony alert. Jeśli nie można przetworzyć oczekujących aktualizacji dynamicznego członkostwa dla wszystkich grup w ramach dzierżawy przez więcej niż 24 godziny, zostanie wyświetlony alert w górnej części **wszystkich grup**.

![przetwarzanie alertów komunikatów o błędach](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat pracy z grupami dynamicznymi w usłudze Azure AD.

* Aby uzyskać pełne odwołanie do struktury reguły dynamicznej, zobacz [składnia reguły członkostwa dynamicznego](groups-dynamic-membership.md).
* [Utwórz statyczną grupę członkostwa i Dodaj członków](../fundamentals/active-directory-groups-create-azure-portal.md).
