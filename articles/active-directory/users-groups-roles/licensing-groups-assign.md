---
title: Przypisywanie licencji do grupy - usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak przypisać licencje do użytkowników za pomocą licencjonowania grupy usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a54d1ad3ab809f2a2f8df6ae0e30b1b061c2be1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60471335"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Przypisywanie licencji do użytkowników, członkostwa w grupach w usłudze Azure Active Directory

W tym artykule opisano poprzez przypisywanie licencji produktu do grupy użytkowników w usłudze Azure Active Directory (Azure AD), a następnie sprawdź, czy są one poprawnie licencjonowane.

W tym przykładzie dzierżawcy zawiera grupę zabezpieczeń o nazwie **dział KADR**. Ta grupa zawiera wszystkie elementy członkowskie z działu kadr (około 1000 użytkowników). Chcesz przypisać licencje usługi Office 365 Enterprise E3 całego działu. Usługa Yammer przedsiębiorstw, która jest zawarta w produkcie musi tymczasowo wyłączone, aż dział jest gotowy rozpocząć korzystanie z niej. Chcesz również wdrożyć pakiet Enterprise Mobility + Security licencji do tej samej grupy użytkowników.

> [!NOTE]
> Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Aby można było przypisać licencję do użytkownika, administrator musi określić właściwość lokalizacja użytkowania na użytkownika.
> 
> W celu przypisania licencji grupy Wszyscy użytkownicy bez określonej lokalizacji użytkowania dziedziczą lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, zaleca się zawsze wartość lokalizacji użytkowania — część przepływu tworzenia użytkownika w usłudze Azure AD (np. przez program AAD Connect Konfiguracja) -, które gwarantuje, że wynik przypisania licencji zawsze jest poprawna, a użytkownicy nie będą odbierać usługi w lokalizacjach, które nie są dozwolone.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Przypisywanie licencji

1. Zaloguj się do [ **Centrum administracyjnego usługi Azure AD** ](https://aad.portal.azure.com) przy użyciu konta administratora licencji. Aby zarządzać licencjami, konto musi być administrator licencji, użytkownika administrator lub administrator globalny.

2. Wybierz **licencji** aby otworzyć okienko, w którym można wyświetlić i zarządzać wszystkie licencjonowane produkty firmy w dzierżawie.

4. W obszarze **wszystkie produkty**, wybierz pozycję Office 365 Enterprise E5 i rozwiązania Enterprise Mobility + Security E3, wybierając nazw produktów. Aby rozpocząć przypisywanie, wybierz opcję **przypisać** u góry okienka.

   ![Wybierz produkty, aby przypisać licencje](./media/licensing-groups-assign/all-products-assign.png)
  
5. Na **przypisywanie licencji** okienku wybierz **użytkowników i grup** aby otworzyć listę użytkowników i grup.

6. Wybierz użytkownika lub grupę, a następnie użyj **wybierz** znajdujący się u dołu okienka, aby potwierdzić wybór.

7. Na **przypisywanie licencji** okienku kliknij **opcje przydziału**, powoduje wyświetlenie wszystkich planach usługi uwzględnione w dwóch produktów, które wcześniej wybrano. Znajdź **Yammer Enterprise** i przekształcać je **poza** wyłączenie tej usługi, od licencji produktu. Potwierdź, klikając przycisk **OK** w dolnej części **opcji licencji**.

   ![Wybierz plany usługi dotyczące licencji](./media/licensing-groups-assign/assignment-options.png)
  
8. Aby zakończyć przypisywanie, w dolnej części okienka **Przypisz licencję** kliknij pozycję **Przypisz**.

9. W prawym górnym rogu, który pokazuje stan i wynik procesu zostanie wyświetlone powiadomienie. Jeśli nie można ukończyć przypisanie do grupy (na przykład z powodu istniejących licencji do grupy), kliknij powiadomienie, aby wyświetlić szczegóły dotyczące błędu.

Podczas przypisywania licencji do grupy, usługi Azure AD przetwarza istniejący członkowie tej grupy. Ten proces może potrwać pewien czas, zależne od rozmiaru grupy. Następnym krokiem w tym artykule opisano sposób sprawdzić, czy proces zostanie zakończony i określić, jeśli dalszej uwagi jest wymagane do rozwiązania problemów.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Sprawdź, czy zakończyła początkowego przydziału

1. Przejdź do **usługi Azure Active Directory** > **grup**. Wybierz grupę, do której przypisano licencje.

2. W okienku grupy wybierz **licencji**. Dzięki temu można szybko upewnij się, jeśli pełni przypisano licencje do użytkowników, a wystąpiły żadne błędy, które należy zbadać. Dostępne są następujące informacje:

   - Lista licencji produktu, które są obecnie przypisane do grupy. Wybierz wpis, aby wyświetlić konkretne usługi, które zostały włączone i wprowadzać zmiany.

   - Stan najnowsze zmiany licencji, które zostały wprowadzone do grupy (jeśli są przetwarzane zmiany lub przetwarzanie zostało zakończone dla wszystkich członków użytkownika).

   - Informacje o użytkownikach, którzy są w stanie błędu, ponieważ nie można przypisać licencji do nich.

   ![błędy licencjonowania i stan licencji](./media/licensing-groups-assign/assignment-errors.png)

3. Zobacz więcej szczegółowych informacji dotyczących przetwarzania w ramach licencji **usługi Azure Active Directory** > **użytkowników i grup** > *Nazwa grupy*  >  **Dzienniki inspekcji**. Należy zwrócić uwagę następujących działań:

   - Działania: **Rozpocznij stosowanie licencji opartej na grupie użytkowników**. To jest rejestrowany, gdy system przejmie zmianę przypisania licencji dla grupy rozpoczyna się zastosowanie go do wszystkich elementów członkowskich użytkownika. Zawiera on informacje o zmianie, który został wykonany.

   - Działania: **Kończenie stosowania licencji opartej na grupie użytkowników**. To jest rejestrowany, gdy system zakończy przetwarzanie wszystkich użytkowników w grupie. Zawiera on podsumowanie liczby użytkowników zostały pomyślnie przetworzone i ilu użytkowników nie można przypisać do grupy licencji.

   [Przeczytaj tę sekcję](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) Aby dowiedzieć się więcej na temat sposobu dzienników inspekcji może służyć do analizowania zmian wprowadzonych przez usługę licencjonowania opartego na grupach.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Sprawdź, czy problemów z licencją i ich rozwiązania

1. Przejdź do **usługi Azure Active Directory** > **grup**i Znajdź grupę, którą przypisano licencje.
2. W okienku grupy wybierz **licencji**. Powiadomienia u góry okienka pokazuje, że istnieją 10 użytkowników, których nie można przypisać licencji. Otwórz go, aby wyświetlić listę wszystkich użytkowników w stanie błąd licencjonowania dla tej grupy.
3. **Przypisań zakończonych niepowodzeniem** kolumny informuje NAS, że obie licencje produktu nie można przypisać do użytkowników. **Najważniejsze przyczyny niepowodzenia** kolumna zawiera przyczynę niepowodzenia. W tym przypadku ma **plany usług powodujące konflikt**.

   ![Nie można przypisać licencji](./media/licensing-groups-assign/failed-assignments.png)

4. Wybierz użytkownika, aby otworzyć **licencji** okienka. W tym okienku przedstawia wszystkie licencje, które są obecnie przypisane do użytkownika. W tym przykładzie użytkownik ma licencję Office 365 Enterprise E1, który został odziedziczony z **użytkowników kiosku** grupy. To jest w konflikcie z licencją E3, który próbowano zastosować z systemu **dział KADR** grupy. W rezultacie Brak licencji z tej grupy zostały przypisane do użytkownika.

   ![Wyświetl wszystkie konflikty licencji użytkownika](./media/licensing-groups-assign/user-license-view.png)

5. Aby rozwiązać ten konflikt, należy usunąć użytkownika z **użytkowników kiosku** grupy. Po przetworzeniu przez usługę Azure AD zmiany **dział KADR** poprawnie przypisanych licencji.

   ![W tym miejscu poprawnie przypisywania licencji](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat funkcji, ustaw dla zarządzania licencjami za pomocą grup, zobacz następujące artykuły:

* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak przeprowadzić migrację użytkowników między licencjami produktów za pomocą licencjonowania opartego na grupy w usłudze Azure Active Directory](licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [Przykłady programu PowerShell dla licencjonowania opartego na grupy w usłudze Azure Active Directory](licensing-ps-examples.md)
