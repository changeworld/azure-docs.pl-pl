---
title: Przypisywanie licencji do grupy — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak przypisać licencje użytkownikom za pomocą licencjonowania grup usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253067"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Przypisywanie licencji użytkownikom według członkostwa w grupach w usłudze Azure Active Directory

W tym artykule otrzymasz od przypisywania licencji produktów do grupy użytkowników i sprawdzania, czy są one poprawnie licencjonowane w usłudze Azure Active Directory (Azure AD).

W tym przykładzie dzierżawca zawiera grupę zabezpieczeń o nazwie **Dział KADR**. Ta grupa obejmuje wszystkich członków działu zasobów ludzkich (około 1000 użytkowników). Chcesz przypisać licencje usługi Office 365 Enterprise E3 do całego działu. Usługa Yammer Enterprise, która jest zawarta w produkcie, musi być tymczasowo wyłączona, dopóki dział nie będzie gotowy do rozpoczęcia korzystania z niej. Chcesz również wdrożyć licencje Enterprise Mobility + Security dla tej samej grupy użytkowników.

> [!NOTE]
> Nie wszystkie usługi firmy Microsoft są dostępne we wszystkich lokalizacjach. Zanim licencja może być przypisana do użytkownika, administrator musi określić właściwość Lokalizacja użycia użytkownika.
>
> W przypadku przypisania licencji grupowej użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Jeśli masz użytkowników w wielu lokalizacjach, zalecamy, aby zawsze ustawiać lokalizację użycia w ramach przepływu tworzenia użytkownika w usłudze Azure AD (np. za pośrednictwem konfiguracji AAD Connect) - co zapewnia, że wynik przypisania licencji jest zawsze poprawny, a użytkownicy nie otrzymują usług w miejscach, które nie są dozwolone.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: Przypisywanie wymaganych licencji

1. Zaloguj się do [**centrum administracyjnego usługi Azure AD**](https://aad.portal.azure.com) przy za pomocą konta administratora licencji. Aby zarządzać licencjami, konto musi być administratorem licencji, administratorem użytkownika lub administratorem globalnym.

1. Wybierz **pozycję Licencje,** aby otworzyć stronę, na której można wyświetlać wszystkie produkty podlegające licencjom i zarządzać nimi w dzierżawie.

1. W obszarze **Wszystkie produkty**wybierz pozycję Office 365 Enterprise E5 i Enterprise Mobility + Security E3, wybierając nazwy produktów. Aby rozpocząć przypisanie, wybierz pozycję **Przypisz** u góry strony.

   ![Wybieranie produktów do przypisywania licencji](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na stronie **Przypisywanie licencji** wybierz **pozycję Użytkownicy i grupy,** aby otworzyć listę użytkowników i grup.

1. Wybierz użytkownika lub grupę, a następnie użyj przycisku **Wybierz** u dołu strony, aby potwierdzić wybór.

1. Na stronie **Przypisywanie licencji** kliknij pozycję **Opcje przydziału**, w którym są wyświetlane wszystkie plany usług zawarte w dwóch wybranych wcześniej produktach. Znajdź **usługę Yammer Enterprise** i wyłącz **ją,** aby wyłączyć tę usługę z licencji produktu. Potwierdź, klikając **przycisk OK** u dołu **opcji licencji**.

   ![wybieranie planów usług dla licencji](./media/licensing-groups-assign/assignment-options.png)
  
1. Aby ukończyć przypisanie, na stronie **Przypisywanie licencji** kliknij pozycję **Przypisz** u dołu strony.

1. Powiadomienie jest wyświetlane w prawym górnym rogu, który pokazuje stan i wynik procesu. Jeśli przypisanie do grupy nie może zostać ukończone (na przykład z powodu istniejących licencji w grupie), kliknij powiadomienie, aby wyświetlić szczegóły błędu.

Po przypisaniu licencji do grupy usługa Azure AD przetwarza wszystkich istniejących członków tej grupy. Ten proces może zająć trochę czasu, w zależności od rozmiaru grupy. W następnym kroku opisano, jak sprawdzić, czy proces został zakończony i określić, czy wymagana jest dalsza uwaga do rozwiązania problemów.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: Sprawdź, czy początkowe przypisanie zostało zakończone

1. Przejdź do **grupy usługi Azure Active Directory** > **.** Wybierz grupę, do których zostały przypisane licencje.

1. Na stronie grupy wybierz pozycję **Licencje**. Dzięki temu można szybko potwierdzić, czy licencje zostały w pełni przypisane do użytkowników i czy są jakieś błędy, które należy sprawdzić. Dostępne są następujące informacje:

   - Licencje na usługi, które są obecnie przypisane do grupy. Wybierz wpis, aby wyświetlić określone usługi, które zostały włączone i wprowadzić zmiany.

   - Aktualizacje stanu najnowszych zmian licencji, które są dostępne, jeśli zmiany są przetwarzane lub jeśli przetwarzanie zostało zakończone dla wszystkich członków użytkownika.

   - Informacje o przypisaniach licencji użytkowników, które są w stanie błędu.

   ![błędy licencjonowania i stan licencji](./media/licensing-groups-assign/assignment-errors.png)

1. Zobacz bardziej szczegółowe informacje o przetwarzaniu licencji w obszarze**Użytkownicy i grupy** > grup usługi Azure Active **Directory Użytkownicy** > i grupy*Dzienniki* > **inspekcji**. Sprawdź następujące działania:

   - Działanie: `Start applying group based license to users`. Jest to rejestrowane, gdy system odbiera zmianę przypisania licencji w grupie i rozpoczyna stosowanie go do wszystkich członków użytkownika. Zawiera informacje o wprowadzonej zmianie.

   - Działanie: `Finish applying group based license to users`. Jest to rejestrowane po zakończeniu przetwarzania przez system wszystkich użytkowników w grupie. Zawiera podsumowanie liczby użytkowników, którzy zostali pomyślnie przetworzoni i ilu użytkowników nie można przypisać licencji grupowych.

   [Przeczytaj tę sekcję,](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) aby dowiedzieć się więcej o tym, jak dzienniki inspekcji mogą służyć do analizowania zmian wprowadzonych przez licencjonowanie oparte na grupach.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: Sprawdź, czy nie ma problemów z licencją i rozwiąż je

1. Przejdź do **witryny Grupy usługi Azure Active Directory** > **Groups**i znajdź grupę, do których przypisano licencje.
1. Na stronie grupy wybierz pozycję **Licencje**. Powiadomienie u góry strony pokazuje, że istnieje 10 użytkowników, do których nie można przypisać licencji. Otwórz go, aby wyświetlić listę wszystkich użytkowników w stanie błędu licencjonowania dla tej grupy.
1. Kolumna **Nieudane przypisania** informuje nas, że nie można przypisać obu licencji produktu do użytkowników. **Górna przyczyna niepowodzenia** kolumna zawiera przyczynę błędu. W tym przypadku jest to **sprzeczne plany usług**.

   ![licencje, których nie można przypisać](./media/licensing-groups-assign/failed-assignments.png)

1. Wybierz użytkownika, aby otworzyć stronę **Licencje** użytkownika. Na tej stronie znajdują się wszystkie licencje, które są aktualnie przypisane do użytkownika. W tym przykładzie użytkownik ma licencję Office 365 Enterprise E1, która została odziedziczona z grupy **użytkownicy kiosku.** Jest to sprzeczne z licencją E3, którą system próbował zastosować z grupy **działu HR.** W rezultacie żadna z licencji z tej grupy nie została przypisana do użytkownika.

   ![Wyświetlanie wszystkich konfliktów licencji dla użytkownika](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Aby rozwiązać ten konflikt, usuń użytkownika z grupy **Użytkownicy kiosku.** Po przesączce usługi Azure AD, licencje **działu kadr** są poprawnie przypisane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zestawie funkcji do przypisywania licencji przy użyciu grup, zobacz następujące artykuły:

- [Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identyfikowanie i rozwiązywanie problemów z licencją dla grupy w usłudze Azure Active Directory](licensing-groups-resolve-problems.md)
- [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
- [Jak migrować użytkowników między licencjami produktów przy użyciu licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-groups-change-licenses.md)
- [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [Przykłady programu PowerShell dotyczące licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-ps-examples.md)
