---
title: Utworzyć grupę dynamiczną i sprawdzić stan — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak utworzyć reguły członkostwa grupy w witrynie Azure portal, sprawdź stan.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472174"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Utworzyć grupę dynamiczną i sprawdzić stan

W usłudze Azure Active Directory (Azure AD) można użyć reguły do określenia członkostwa grup na podstawie właściwości użytkownika lub urządzenia. Ten artykuł zawiera informacje dotyczące konfiguracji reguły dla grupy dynamicznej w witrynie Azure portal.
Dynamiczne członkostwo jest obsługiwane dla grup zabezpieczeń lub grup usługi Office 365. Po zastosowaniu zasad członkostwa w grupie atrybutów użytkowników i urządzeń są oceniane pod kątem dopasowań za pomocą reguł członkostwa. Gdy atrybut dla użytkowników lub urządzeń, wszystkie reguły dynamicznego w grupach w organizacji są przetwarzane dla zmiany członkostwa. Użytkownicy i urządzenia są dodawane lub usuwane, jeśli spełniają warunki dla grupy.

Przykłady składni, obsługiwanych właściwości, operatory i wartości dla reguły członkostwa, zobacz [reguły członkostwa dynamicznego dla grup w usłudze Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Aby utworzyć regułę członkostwa grupy

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta administratora globalnego, administratora usługi Intune lub roli użytkownika administrator dzierżawy.
2. Wybierz **grup**.
3. Wybierz **wszystkich grup**i wybierz **nową grupę**.

   ![Wybierz polecenie, aby dodać nową grupę](./media/groups-create-rule/new-group-creation.png)

4. Na **grupy** wpisz nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** dla użytkowników lub urządzeń i następnie wybierz pozycję **Dodaj zapytanie dynamiczne**. Można używać konstruktora reguły do tworzenia Prosta reguła lub [samodzielnie zapisujesz zastosowania reguły członkowskiej](groups-dynamic-membership.md).

   ![Dodaj regułę członkostwa dla grupy dynamicznej](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Aby wyświetlić dostępne dla zapytania o członkostwo właściwości niestandardowego rozszerzenia
   1. Wybierz **pobieranie właściwości niestandardowego rozszerzenia**
   2. Wprowadź identyfikator aplikacji, a następnie wybierz pozycję **Odśwież właściwości**. 
6. Po utworzeniu reguły, wybierz **Dodaj zapytanie** w dolnej części bloku.
7. Wybierz **Utwórz** na **grupy** bloku, aby utworzyć grupę.

Jeśli wprowadzony reguły jest nieprawidłowy, wyjaśnienie, dlaczego nie można przetworzyć reguły jest wyświetlany w prawym górnym rogu portalu. Przeczytaj dokładnie, aby dowiedzieć się, jak rozwiązać reguły.

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej wiadomości e-mail

Podczas tworzenia nowej grupy usługi Office 365 powitalnej powiadomienie jest wysyłane użytkowników, którzy są dodawane do grupy. Później Jeśli zmienisz jakiekolwiek atrybuty użytkownika lub urządzenia, wszystkie reguły dynamicznego w grupach w organizacji są przetwarzane dla zmiany członkostwa. Użytkownicy, którzy są dodawani następnie otrzymają powiadomienie powitalnej. Możesz wyłączyć to zachowanie w [PowerShell programu Exchange](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Sprawdź stan przetwarzania reguły

Możesz zobaczyć członkostwa przetwarzanie stanu i Data ostatniej aktualizacji **Przegląd** stronie dla grupy.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Następujące komunikaty o stanie mogą być wyświetlane dla **przetwarzania członkostwa** stanu:

* **Ocena**:  Zmiana grupy zostało odebrane i aktualizacji są oceniane.
* **Przetwarzanie**: Aktualizacje są przetwarzane.
* **Ukończono aktualizację**: Przetwarzanie zostało ukończone, a wszystkie odpowiednie aktualizacje zostały wprowadzone.
* **Błąd przetwarzania**:  Nie można ukończyć przetwarzanie ze względu na błąd podczas oceny reguły członkostwa.
* **Aktualizowanie wstrzymane**: Wstrzymano aktualizacje reguły członkostwa dynamicznego przez administratora. MembershipRuleProcessingState jest ustawiona na "Wstrzymana".

Następujące komunikaty o stanie mogą być wyświetlane dla **członkostwa Ostatnia aktualizacja:** stanu:

* &lt;**Data i godzina**&gt;: Czas ostatniej aktualizacji członkostwa.
* **Trwającą**: Aktualizacje są obecnie w toku.
* **Nieznany**: Nie można pobrać czas ostatniej aktualizacji. Grupa może być nowe.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, alert jest wyświetlany w górnej części **strony Przegląd** dla grupy. Jeśli nie oczekujące członkostwo dynamiczne aktualizacje mogą być przetwarzane dla wszystkich grup w ramach dzierżawy więcej następnie 24 godziny, alert jest wyświetlany w górnej części **wszystkich grup**.

![przetwarzanie alertów komunikatów o błędach](./media/groups-create-rule/processing-error.png)

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
