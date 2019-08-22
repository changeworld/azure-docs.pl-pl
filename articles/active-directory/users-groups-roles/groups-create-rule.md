---
title: Utwórz grupę dynamiczną i sprawdź stan Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650299"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Utwórz grupę dynamiczną i sprawdź stan

W Azure Active Directory (Azure AD) można używać reguł do określania członkostwa w grupach na podstawie właściwości użytkownika lub urządzenia. W tym artykule opisano sposób konfigurowania reguły dla grupy dynamicznej w Azure Portal.
Dynamiczne członkostwo jest obsługiwane dla grup zabezpieczeń lub grup pakietu Office 365. Po zastosowaniu reguły członkostwa w grupie atrybuty użytkownika i urządzenia są oceniane pod kątem zgodności z regułą członkostwa. Gdy atrybut zostanie zmieniony dla użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmiany członkostwa. Użytkownicy i urządzenia są dodawani lub usuwani, jeśli spełnią warunki dla grupy. Grupy zabezpieczeń mogą być używane dla urządzeń lub użytkowników, ale grupy Office 365 mogą dotyczyć tylko grup użytkowników.

Przykłady składni, obsługiwane właściwości, operatory i wartości dla reguły członkostwa można znaleźć w temacie [dynamiczne reguły członkostwa dla grup w Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Aby utworzyć regułę członkostwa w grupie

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli Administrator globalny, administrator usługi Intune lub administrator użytkowników w dzierżawie.
2. Wybierz pozycję **grupy**.
3. Wybierz pozycję **wszystkie grupy**, a następnie wybierz pozycję **Nowa grupa**.

   ![Wybierz polecenie, aby dodać nową grupę](./media/groups-create-rule/new-group-creation.png)

4. Na stronie **Grupa** wprowadź nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** dla użytkowników lub urządzeń, a następnie wybierz pozycję **Dodaj zapytanie dynamiczne**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać szóste lub dowolne kolejne wyrażenia, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla kwerendy dotyczącej członkostwa
   1. Wybierz pozycję **Pobierz niestandardowe właściwości rozszerzenia**
   2. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**.
6. Po utworzeniu reguły wybierz pozycję **Zapisz**.
7. Na stronie **Nowa grupa** wybierz pozycję **Utwórz** , aby utworzyć grupę.

Jeśli wprowadzona reguła jest nieprawidłowa, wyjaśnienie, dlaczego nie można przetworzyć reguły, jest wyświetlana w powiadomieniach platformy Azure w portalu. Przeczytaj uważnie, aby zrozumieć, jak naprawić regułę.

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej poczty e-mail

Po utworzeniu nowej grupy pakietu Office 365 zostanie wysłane powiadomienie powitalnej poczty e-mail użytkowników, którzy zostaną dodani do grupy. Później, jeśli jakiekolwiek atrybuty użytkownika lub urządzenia zostaną zmienione, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmian członkostwa. Dodani użytkownicy otrzymują również powiadomienie powitalne. To zachowanie można wyłączyć w programie [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

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

* &lt;**Data i godzina**&gt;: Ostatni raz, gdy członkostwo zostało zaktualizowane.
* **W toku**: Aktualizacje są obecnie w toku.
* **Nieznany**: Nie można pobrać czasu ostatniej aktualizacji. Grupa może być nowa.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, w górnej części **strony Przegląd** dla grupy zostanie wyświetlony alert. Jeśli nie można przetworzyć oczekujących aktualizacji dynamicznego członkostwa dla wszystkich grup w ramach dzierżawy przez więcej niż 24 godziny, zostanie wyświetlony alert w górnej części **wszystkich grup**.

![przetwarzanie alertów komunikatów o błędach](./media/groups-create-rule/processing-error.png)

Te artykuły zawierają dodatkowe informacje dotyczące grup w Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
