---
title: Utwórz grupę dynamiczną i sprawdź stan w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak utworzyć reguły członkostwa w grupie w witrynie Azure portal, sprawdź stan.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/20/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e418316a74ccf27ec730261957a8b6c64de5d063
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040605"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Utworzyć grupę dynamiczną i sprawdzić stan

W usłudze Azure Active Directory (Azure AD) aby utworzyć grupy zastosowania reguły w celu ustalenia członkostwa na podstawie właściwości użytkownika lub urządzenia. Podczas atrybuty zmiany urządzenia lub użytkownika usługi Azure AD wszystkich grup dynamicznych reguł w dzierżawie usługi Azure AD i wykonuje dowolną dodaje lub usuwa. Jeśli użytkownik lub urządzenie spełnia wymagania zasad grupy, są dodawane jako członek, a jeśli ich nie spełniają już reguły, są usuwane.

Ten artykuł szczegółowo opisuje sposób konfigurowania reguły w witrynie Azure portal, dynamicznego zarządzania członkostwem w grupach zabezpieczeń lub grupach usługi Office 365. Przykłady składni reguł i pełną listę obsługiwanych właściwości, operatory i wartości dla reguły członkostwa, zobacz [reguły członkostwa dynamicznego dla grup w usłudze Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Aby utworzyć regułę członkostwa grupy

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta należącego do roli administratora globalnego, administratora usługi Intune lub Administrator kont użytkowników w dzierżawie.
2. Wybierz **grup**.
3. Wybierz **wszystkich grup**i wybierz **nową grupę**.

   ![Dodaj nową grupę](./media/groups-create-rule/new-group-creation.png)

4. Na **grupy** bloku, wprowadź nazwę i opis dla nowej grupy. Wybierz **Typ członkostwa** albo **użytkownik dynamiczny** lub **urządzenie dynamiczne**, w zależności od tego, czy chcesz utworzyć regułę dla użytkowników lub urządzeń, a następnie wybierz **Dodaj zapytanie dynamiczne**. Można użyć konstruktora reguły do tworzenia prostej reguły lub samodzielnie zapisujesz reguły członkostwa. Ten artykuł zawiera więcej informacji na temat dostępnych atrybutów użytkowników i urządzeń, a także przykłady reguł członkostwa.

   ![Dodaj dynamiczną regułę członkostwa](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Po utworzeniu reguły, wybierz **Dodaj zapytanie** w dolnej części bloku.
6. Wybierz **Utwórz** na **grupy** bloku, aby utworzyć grupę.

> [!TIP]
> Tworzenie grupy kończy się niepowodzeniem, jeśli reguła wprowadzony został niepoprawnie sformułowany lub nieprawidłowy. W prawym górnym rogu portalu, zawierające wyjaśnienie, dlaczego nie można przetworzyć reguły zostanie wyświetlone powiadomienie. Przeczytaj uważnie, aby zrozumieć, jak musisz dostosować reguły, aby stał się nieprawidłowy.

## <a name="check-processing-status-for-a-membership-rule"></a>Sprawdź stan przetwarzania reguły członkostwa

Możesz zobaczyć członkostwa przetwarzanie stanu i Data ostatniej aktualizacji **Przegląd** stronie dla grupy.
  
  ![Wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Następujące komunikaty o stanie mogą być wyświetlane dla **przetwarzania członkostwa** stanu:

* **Ocena**: Zmiana grupy zostało odebrane i aktualizacji są oceniane.
* **Przetwarzanie**: aktualizacje są przetwarzane.
* **Ukończono aktualizację**: przetwarzanie zostało ukończone i wszystkie odpowiednie aktualizacje zostały wprowadzone.
* **Błąd przetwarzania**: Napotkano błąd podczas obliczania reguły członkostwa i nie można ukończyć przetwarzanie.
* **Aktualizowanie wstrzymane**: dynamiczną regułę członkostwa aktualizacji została wstrzymana przez administratora. MembershipRuleProcessingState jest ustawiona na "Wstrzymana".

Następujące komunikaty o stanie mogą być wyświetlane dla **członkostwa Ostatnia aktualizacja:** stanu:

* &lt;**Data i godzina**&gt;: czas ostatniego członkostwo zostało zaktualizowane.
* **Trwającą**: aktualizacje są obecnie w toku.
* **Nieznany**: nie można pobrać czas ostatniej aktualizacji. Może to być spowodowane nowo tworzonej grupy.

Jeśli wystąpi błąd podczas przetwarzania reguły członkostwa dla określonej grupy, alert jest wyświetlany w górnej części **strony Przegląd** dla grupy. Jeśli nie oczekujące członkostwo dynamiczne aktualizacje mogą być przetwarzane dla wszystkich grup w ramach dzierżawy więcej następnie 24 godziny, alert jest wyświetlany w górnej części **wszystkich grup**.

![komunikat o błędzie przetwarzania](./media/groups-create-rule/processing-error.png)

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
