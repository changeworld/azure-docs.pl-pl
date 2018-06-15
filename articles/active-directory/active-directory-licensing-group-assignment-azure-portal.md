---
title: Przypisywanie licencji do grupy w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak przypisać licencje do użytkowników za pomocą usługi Azure Active Directory grupy licencji
services: active-directory
keywords: Licencjonowanie usługi Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 091750d7cadd460ac46d3293951bf4423383c3c5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763037"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Przypisywanie licencji do użytkowników na podstawie członkostwa w grupie w usłudze Azure Active Directory

W tym artykule przedstawiono sposób przypisywania licencji produktu do grupy użytkowników w usłudze Azure Active Directory (Azure AD), a następnie sprawdź, czy są one prawidłowo licencjonowane.

W tym przykładzie dzierżawcy zawiera grupę zabezpieczeń o nazwie **zarządzania zasobami Ludzkimi stosują**. Do tej grupy należą wszyscy członkowie działu kadr (około 1000 użytkowników). Chcesz przypisać licencje Office 365 Enterprise E3 do całego działu. Usługi Yammer przedsiębiorstwa, która jest zawarta w produkcie musi zostać tymczasowo wyłączone, aż działu będzie gotowa rozpocząć korzystanie z niej. Chcesz również wdrożyć Enterprise Mobility + Security licencji do tej samej grupy użytkowników.

> [!NOTE]
> Pewnych usług firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Przed licencji mogą być przypisane do użytkownika, administrator musi określać właściwość Usage lokalizacji użytkownika.

> Przypisywaniu licencji grupy użytkowników bez użycia lokalizacji określonej dziedziczą lokalizację katalogu. Jeśli użytkownicy w wielu lokalizacjach, zaleca się zawsze wartość lokalizacji użytkowania jako część programu przepływu tworzenia użytkownika w usłudze Azure AD (np. za pomocą AAD Connect) -, które powodują wynik przypisania licencji zawsze jest poprawna i użytkownicy nie będą odbierać usługi w lokalizacjach, które nie są dozwolone.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Przypisywanie licencji wymagane

1. Zaloguj się do [ **portalu Azure** ](https://portal.azure.com) przy użyciu konta administratora. Zarządzanie licencjami, konto musi być administratorem konta rolę lub użytkownika administratora globalnego.

2. Wybierz **wszystkie usługi** w lewym okienku nawigacji, a następnie wybierz **usługi Azure Active Directory**. Można dodać do ulubionych w tym okienku lub przypiąć go do pulpitu nawigacyjnego portalu.

3. Na **usługi Azure Active Directory** okienku wybierz **licencji** aby otworzyć okienko, w którym możesz wyświetlić i zarządzać wszystkich produktów objętego w dzierżawie.

4. W obszarze **wszystkich produktów**, wybierz Office 365 Enterprise E3 i Enterprise Mobility + Security wybierając nazwy produktu. Aby uruchomić przypisania, zaznacz **przypisać** u góry okienka.

   ![Wszystkie produkty, przypisywanie licencji](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Na **przypisanie licencji** okienku, kliknij przycisk **użytkowników i grup** otworzyć **użytkowników i grup** okienka. Wyszukaj nazwę grupy *zarządzania zasobami Ludzkimi stosują*, wybierz grupę, a następnie upewnij się potwierdzić, klikając **wybierz** u dołu okienka.

   ![Wybierz grupę](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Na **przypisanie licencji** okienku, kliknij przycisk **opcje przydziału (opcjonalnie)**, który zawiera wszystkie plany usługi uwzględnione w dwóch produktów, które wcześniej wybrano. Znajdź **usługi Yammer Enterprise** i włączyć ją **poza** można wyłączyć tę usługę od licencji produktu. Upewnij się, klikając **OK** w dolnej części **opcje przydziału**.

   ![Opcje przypisania](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Aby dokończyć przypisanie na **przypisanie licencji** okienku, kliknij przycisk **przypisać** w dolnej części okienka.

8. W prawym górnym rogu, który pokazuje stan i wynik procesu zostanie wyświetlone powiadomienie. Jeśli przypisanie do grupy nie można ukończyć (na przykład ze względu na istniejące licencji w grupie), kliknij powiadomienie, aby wyświetlić szczegóły błędu.

Firma Microsoft już teraz określić szablon licencji dla grupy zarządzania zasobami Ludzkimi stosują. Proces w tle w usłudze Azure AD został uruchomiony przetworzyć wszystkie istniejące elementy członkowskie tej grupy. Ta początkowa operacja może potrwać pewien czas zależnie od wielkości bieżącej grupy. Następnym krokiem opisuje sposób sprawdzić, czy proces zostanie zakończony i ustalić, czy dalszej uwagi jest wymagane do rozwiązania problemów.

> [!NOTE]
> Możesz uruchomić tego samego przypisania z alternatywną lokalizację: **użytkowników i grup** w usłudze Azure AD. Przejdź do **usługi Azure Active Directory** > **użytkowników i grup** > **wszystkich grup**. Następnie znajdź grupie, zaznacz go, a następnie przejdź do **licencji** kartę. **Przypisać** przycisk u góry okienka otwieranie okienka przypisania licencji.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Sprawdź zakończenie początkowej przypisania

1. Przejdź do **usługi Azure Active Directory** > **użytkowników i grup** > **wszystkich grup**. Następnie znajdź **zarządzania zasobami Ludzkimi stosują** grupy, które zostały przypisane licencje.

2. Na **zarządzania zasobami Ludzkimi stosują** okienko grupy, wybierz opcję **licencji**. Dzięki temu można szybko upewnij się, jeśli pełni przypisane licencje do użytkowników, a jeśli wystąpiły żadne błędy, które należy zapoznać się. Dostępne są następujące informacje:

   - Lista licencje produktów, które są aktualnie przypisane do grupy. Wybierz pozycję Pokaż określonych usług, które zostały włączone i wprowadzać zmiany.

   - Stan najnowszej zmian licencji, które zostały wprowadzone w grupie (jeśli są przetwarzane zmiany lub Zakończono przetwarzanie dla wszystkich członków użytkownika).

   - Informacje o użytkownikach, którzy są w stanie błędu, ponieważ nie można przypisać licencje do nich.

   ![Opcje przypisania](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Zobacz szczegółowe informacje o licencji przetwarzania pod **usługi Azure Active Directory** > **użytkowników i grup** > *Nazwa grupy* > **dzienniki inspekcji**. Należy uwzględnić następujące działania:

   - Działanie: **rozpocząć stosowanie grupy na podstawie licencji do użytkowników**. To jest rejestrowane, gdy system przejmuje zmiany przypisania licencji w grupie i uruchamia zastosowaniu go do wszystkich członków użytkownika. Zawiera on informacje o zmianie, który został utworzony.

   - Działanie: **zastosowaniu na podstawie grupy licencji do użytkowników**. To jest rejestrowane, gdy system zakończy przetwarzanie wszystkich użytkowników w grupie. Zawiera podsumowanie pomyślnie przetworzono ilu użytkowników i ilu użytkowników nie można przypisać do grupy licencji.

   [Przeczytaj tę sekcję](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) Aby dowiedzieć się więcej o tym jak dzienniki inspekcji można używać do analizowania zmiany wprowadzone przez oparte na grupach licencji.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Sprawdź licencji problemów i ich rozwiązania

1. Przejdź do **usługi Azure Active Directory** > **użytkowników i grup** > **wszystkich grup**i Znajdź **zarządzania zasobami Ludzkimi stosują** grupy, które zostały przypisane licencje.
2. Na **zarządzania zasobami Ludzkimi stosują** okienko grupy, wybierz opcję **licencji**. Powiadomienia u góry okienka wskazuje, że 10 użytkowników, których nie można przypisać licencje do użytkowników. Kliknięcie go otwiera listę wszystkich użytkowników w stan błędu licencjonowania dla tej grupy.
3. **Nie powiodło się przypisania** kolumna zawiera informacje nam czy obu licencji produktu nie można przypisać do użytkowników. **Najważniejsze przyczyny niepowodzenia** kolumna zawiera przyczynę niepowodzenia. W takim przypadku ma **planów usługi powodujące konflikt**.

   ![Przypisania nie powiodło się](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Wybierz użytkownika, aby otworzyć **licencji** okienka. W tym okienku są wyświetlane wszystkie licencje, które są aktualnie przypisane do użytkownika. W tym przykładzie użytkownik ma licencji Office 365 Enterprise E1, która została odziedziczona z **użytkowników kiosku** grupy. Powoduje to konflikt z licencji E3, do której próbowano zastosować z systemu **zarządzania zasobami Ludzkimi stosują** grupy. W związku z tym Brak licencji z tej grupy zostanie przypisana do użytkownika.

   ![Widok licencji dla użytkownika](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Aby rozwiązać ten konflikt, Usuń użytkownika z **użytkowników kiosku** grupy. Po przetworzeniu usługi Azure AD przez zmianę, **zarządzania zasobami Ludzkimi stosują** poprawnie przypisanych licencji.

   ![Poprawnie przypisanej licencji](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zestawu do zarządzania licencji za pomocą grup funkcji, zobacz następujące artykuły:

* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identyfikowanie i rozwiązywanie problemów z licencji dla grupy w usłudze Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Jak przeprowadzić migrację poszczególnych licencjonowanych użytkowników do licencjonowania oparte na grupach w usłudze Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Usługa Azure Active Directory na podstawie grupy licencjonowania dodatkowe scenariusze](active-directory-licensing-group-advanced.md)
