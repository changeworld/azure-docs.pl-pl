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
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422352"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Aktualizowanie grupy dynamicznej w celu zarządzania członkostwem w Azure Active Directory

W Azure Active Directory (Azure AD) można używać reguł do określania członkostwa w grupach na podstawie właściwości użytkownika lub urządzenia. W tym artykule opisano sposób konfigurowania reguły dla grupy dynamicznej w Azure Portal.
Dynamiczne członkostwo jest obsługiwane dla grup zabezpieczeń lub grup pakietu Office 365. Po zastosowaniu reguły członkostwa w grupie atrybuty użytkownika i urządzenia są oceniane pod kątem zgodności z regułą członkostwa. Gdy atrybut zostanie zmieniony dla użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmiany członkostwa. Użytkownicy i urządzenia są dodawani lub usuwani, jeśli spełnią warunki dla grupy.

## <a name="rule-builder-in-the-azure-portal"></a>Konstruktor reguł w Azure Portal

Usługa Azure AD udostępnia Konstruktor reguł, który umożliwia szybsze tworzenie i aktualizowanie ważnych reguł. Konstruktor reguł obsługuje konstruowanie do pięciu wyrażeń. Konstruktor reguł ułatwia tworzenie reguły z kilkoma prostymi wyrażeniami, ale nie można ich użyć do odtworzenia każdej reguły. Jeśli Konstruktor reguł nie obsługuje reguły, którą chcesz utworzyć, możesz użyć pola tekstowego.

Poniżej przedstawiono kilka przykładów zaawansowanych reguł lub składni, dla których zalecamy utworzenie przy użyciu pola tekstowego:

- Reguła z więcej niż pięcioma wyrażeniami
- Reguła bezpośrednich podwładnych
- Ustawianie [pierwszeństwa operatorów](groups-dynamic-membership.md#operator-precedence)
- [Reguły z wyrażeniami złożonymi](groups-dynamic-membership.md#rules-with-complex-expressions); na przykład `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Konstruktor reguł może nie być w stanie wyświetlić niektórych reguł skonstruowanych w polu tekstowym. Gdy Konstruktor reguł nie może wyświetlić reguły, może zostać wyświetlony komunikat. Konstruktor reguł nie zmienia obsługiwanej składni, walidacji lub przetwarzania reguł grupy dynamicznej w dowolny sposób.

![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-update-rule/update-dynamic-group-rule.png)

Przykłady składni, obsługiwane właściwości, operatory i wartości dla reguły członkostwa można znaleźć w temacie [dynamiczne reguły członkostwa dla grup w Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Aby zaktualizować regułę członkostwa w grupie

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli Administrator globalny, administrator grupy, administrator usługi Intune lub administrator użytkowników w dzierżawie.
1. Wybierz **grupy** > **wszystkie grupy**.
1. Wybierz grupę, aby otworzyć jej profil.
1. Na stronie profil grupy wybierz pozycję **dynamiczne reguły członkostwa**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać więcej niż pięć wyrażeń, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla reguły członkostwa:
   1. Wybierz pozycję **Pobierz niestandardowe właściwości rozszerzenia**
   1. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**.
1. Po zaktualizowaniu reguły wybierz pozycję **Zapisz**.

Jeśli wprowadzona reguła jest nieprawidłowa, wyjaśnienie, dlaczego nie można przetworzyć reguły, jest wyświetlana w powiadomieniach platformy Azure w portalu. Przeczytaj uważnie, aby zrozumieć, jak naprawić regułę.

## <a name="check-processing-status-for-a-rule"></a>Sprawdzanie stanu przetwarzania dla reguły

Na stronie **Przegląd** dla grupy można zobaczyć stan przetwarzania członkostwa i datę ostatniej aktualizacji.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Na potrzeby stanu **przetwarzania członkostwa** można wyświetlić następujące komunikaty o stanie:

- **Ocenianie**: Odebrano zmianę grupy, a aktualizacje są oceniane.
- **Przetwarzanie**: aktualizacje są przetwarzane.
- **Aktualizacja zakończona**: przetwarzanie zostało zakończone i wszystkie odpowiednie aktualizacje zostały wykonane.
- **Błąd przetwarzania**: nie można ukończyć przetwarzania z powodu błędu podczas oceniania reguły członkostwa.
- **Wstrzymano aktualizację**: aktualizacje reguł członkostwa dynamicznego zostały wstrzymane przez administratora. MembershipRuleProcessingState jest ustawiona na "wstrzymany".

W przypadku **ostatniej zaktualizowanego stanu członkostwa** można wyświetlić następujące komunikaty o stanie:

- **Data i godzina**: godzina ostatniej aktualizacji członkostwa.
- **W toku**: aktualizacje są obecnie w toku.
- **Nieznane**: nie można pobrać czasu ostatniego aktualizowania. Grupa może być nowa.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, w górnej części **strony Przegląd** dla grupy zostanie wyświetlony alert. Jeśli nie można przetworzyć oczekujących aktualizacji dynamicznego członkostwa dla wszystkich grup w ramach dzierżawy przez więcej niż 24 godziny, zostanie wyświetlony alert w górnej części **wszystkich grup**.

![przetwarzanie alertów komunikatów o błędach](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje na temat pracy z grupami dynamicznymi w usłudze Azure AD.

- Aby uzyskać pełne odwołanie do struktury reguły dynamicznej, zobacz [składnia reguły członkostwa dynamicznego](groups-dynamic-membership.md).
- [Utwórz statyczną grupę członkostwa i Dodaj członków](../fundamentals/active-directory-groups-create-azure-portal.md).
