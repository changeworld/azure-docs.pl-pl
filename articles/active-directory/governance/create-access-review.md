---
title: Tworzenie przeglądu dostępu grup lub aplikacji — Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Przegląd dostępu członków grupy lub dostęp do aplikacji w przeglądów dostępu w usłudze Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bafa4614e40bb1796ec90e07ecf5b9286a8acb9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113476"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu grup lub przeglądów dostępu dla aplikacji w usłudze Azure AD

Dostęp do grupy i aplikacje dla pracowników i gości zmienia się wraz z upływem czasu. W celu zmniejszenia ryzyka związanego z przypisania starych dostępu, Administratorzy mogą używać usługi Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostęp do aplikacji. Jeśli zachodzi potrzeba rutynowo Przegląd dostępu wszystkich użytkowników, można również utworzyć cyklicznego przeglądów dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [zarządzanie dostępem gości](manage-guest-access-with-access-reviews.md).

W tym artykule opisano sposób tworzenia wyświetlanie przeglądów dostępu dla członków grupy lub dostęp do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2
- [Przeglądy dostępu włączone](access-reviews-overview.md)
- Administrator globalny lub administrator użytkowników

Aby uzyskać więcej informacji, zobacz [użytkowników, którzy muszą mieć licencje?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Utwórz co najmniej jeden przeglądów dostępu

1. Zaloguj się w witrynie Azure portal i Otwórz [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij **przeglądów dostępu**.

1. Kliknij przycisk **nowy przegląd dostępu** Aby utworzyć nowy przegląd dostępu.

    ![Przegląd dostępu — formanty](./media/create-access-review/access-reviews.png)

1. Nazwa przeglądu dostępu. Opcjonalnie można podać przeglądu opis. Nazwa i opis są wyświetlane dla recenzentów.

    ![Tworzenie przeglądu dostępu — Przegląd nazwę i opis](./media/create-access-review/name-description.png)

1. Ustaw **Data rozpoczęcia**. Domyślnie przeglądu dostępu pojawia się raz, rozpoczyna się w tym samym czasie, w którym zostały utworzone, a kończy się ona w ciągu jednego miesiąca. Można zmienić początek, a końcową dostępu Przejrzyj rozpoczęcia w przyszłości i ostatnio jednak liczbę dni ma.

    ![Tworzenie przeglądu dostępu — rozpoczęcia i zakończenia](./media/create-access-review/start-end-dates.png)

1. Aby cyklu przeglądu dostępu, należy zmienić **częstotliwość** z **jeden raz** do **tygodniowy**, **miesięczne**,  **Co kwartał** lub **rocznie**. Użyj **czas trwania** suwaka lub pola tekstowego, aby określić, ile dni będą każdej recenzji serii cyklicznych, Otwórz na dane wejściowe recenzentów. Na przykład maksymalny czas trwania, którego można ustawić comiesięcznego przeglądu jest 27 dni, należy unikać nakładania się recenzji.

1. Użyj **zakończenia** ustawienie, aby określić sposób kończyć się cyklicznym dostępu Przejrzyj serii. Seria mogą kończyć się na trzy sposoby: działa w sposób ciągły można uruchomić przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Inny administrator użytkownika lub inny administrator globalny może zatrzymać serii po utworzeniu, zmieniając datę w **ustawienia**, dzięki czemu kończy się ona w tym dniu.

1. W **użytkowników** sekcji, określ użytkowników, których dotyczy przegląd dostępu. Przeglądy dostępu może być członkami grupy lub osób, które zostały przypisane do aplikacji. Możesz dodatkowo ograniczyć dostęp tylko do przeglądu i przejrzyj użytkowników-gości kto są elementami członkowskimi (lub przypisane do aplikacji), zamiast przeglądanie wszystkich użytkowników, którzy są członkami lub mających dostęp do aplikacji.

    ![Tworzenie przeglądu dostępu — użytkownicy](./media/create-access-review/users.png)

1. W **grupy** wybierz co najmniej jednej grupy, które chcesz przejrzeć członkostwo.

    > [!NOTE]
    > Wybranie więcej niż jednej grupy spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięć grup utworzy pięciu przeglądach uprawnień dostępu.
    
    ![Tworzenie przeglądu dostępu — grupy](./media/create-access-review/select-group.png)

1. W **aplikacje** sekcji (w przypadku wybrania **przypisanych do aplikacji** w kroku 8), wybierz aplikacje, które chcesz przeprowadzić przegląd dostępu do.

    > [!NOTE]
    > Wybranie więcej niż jednej aplikacji spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu aplikacji utworzy pięciu przeglądach uprawnień dostępu.
    
    ![Tworzenie przeglądu dostępu — wybór aplikacji](./media/create-access-review/select-application.png)

1. W **recenzentów** wybierz jeden lub więcej osób, aby zapoznać się z wszystkich użytkowników w zakresie. Lub możesz wybrać członkowie dokonać przeglądu własnego dostępu. Jeśli zasób jest grupą, możesz poprosić właścicieli grupy, aby przejrzeć. Możesz również wymagać że recenzenci mogą zatwierdzać dostęp, podania przyczyny.

    ![Tworzenie przeglądu dostępu — osób dokonujących przeglądu](./media/create-access-review/reviewers.png)

1. W **programy** wybierz program, którego chcesz użyć. **Domyślny Program** zawsze jest obecny.

    ![Tworzenie przeglądu dostępu — programy](./media/create-access-review/programs.png)

    Można uprościć jak śledzenie i zbierać przeglądów dostępu do różnych celów, organizując je w programach. Każdy przeglądu dostępu mogą być połączone z programem. Następnie podczas przygotowania raporty Audytor możesz skupić się na przeglądów dostępu w zakresie określonym inicjatywy. Programy i wyników przeglądu dostępu są widoczne dla użytkowników w administrator globalny, administrator użytkownika, administratora zabezpieczeń lub roli czytelnika zabezpieczeń.

    Aby wyświetlić listę programów, przejdź do pozycji dostępu przegląda strony i wybierz pozycję **programy**. Jeśli jesteś administratorem globalnym lub rola administratora, możesz utworzyć dodatkowe programy. Na przykład możesz mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych. Jeśli nie potrzebujesz już program i nie ma żadnych formantów z nim połączone, możesz go usunąć.

### <a name="upon-completion-settings"></a>Ustawienia działań po zakończeniu

1. Aby określić, co się dzieje po zakończeniu przeglądu, rozwiń **ustawienia działań po zakończeniu** sekcji.

    ![Ustawienia działań po zakończeniu](./media/create-access-review/upon-completion-settings.png)

1. Jeśli chcesz automatycznie Usuń dostęp dla użytkowników, które zostały odrzucone, ustaw **automatycznie Zastosuj wyniki do zasobu** do **Włącz**. Jeśli chcesz ręcznie zastosować wyników, po zakończeniu przeglądu, ustaw przełącznik **wyłączyć**.

1. Użyj **powinien recenzenta odpowiada** listy, aby określić, co się stanie, dla użytkowników, którzy nie są przeglądane przez recenzenta przed upływem przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostało przejrzane przez recenzenta ręcznie. Jeśli decyzja końcowego recenzent jest Odmów, dostęp użytkownika zostaną usunięte.

    - **Nie wprowadzono zmian** — pozostaw dostępu użytkownika bez zmian
    - **Usuń dostęp** -spowodować usunięcie dostępu użytkownika
    - **Zatwierdź dostęp** — zatwierdzanie dostępu użytkownika
    - **Uwzględnij rekomendacje** — wykonaj zalecenia systemu odmowy lub zatwierdzenie użytkownika przez stały dostęp

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń **Zaawansowane ustawienia** sekcji.

    ![Ustawienia zaawansowane](./media/create-access-review/advanced-settings.png)

1. Ustaw **Pokaż zalecenia** do **Włącz** pokazują stan systemu recenzentów zalecenia na podstawie informacji dotyczących dostępu użytkownika.

1. Ustaw **wymagana Przyczyna przy zatwierdzaniu** do **Włącz** do Wymagaj od recenzenta podania przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztowe** do **Włącz** mieć usługi Azure AD, wysyłania powiadomień e-mail do recenzentów podczas uruchamiania przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** do **Włącz** aby platforma Azure AD wysyłała przypomnienia o przeglądach dostępu w toku dla recenzentów, którzy nie ukończyli swojego przeglądu.

    Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

## <a name="start-the-access-review"></a>Rozpocznij Przegląd dostępu

Po określeniu ustawienia przeglądu dostępu, kliknij przycisk **Start**. Przegląd dostępu będą wyświetlane na liście ze wskaźnikiem jego stan.

![Lista przeglądów dostępu](./media/create-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów, wkrótce, po uruchomieniu przeglądu. Jeśli użytkownik chce nie ma wysyłać wiadomości e-mail z usługi Azure AD, pamiętaj poinformować osób dokonujących przeglądu, które przeglądu dostępu oczekuje na ich zakończenie. Można pokazać im instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników do grup ani aplikacji](perform-access-review.md). W przypadku zapoznania się z nimi dla gości, aby dokonać przeglądu własnego dostępu, wyświetlić je instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacji](review-your-access.md).

Jeśli przypisano gości recenzentów, a nie zaakceptowali zaproszenia, nie otrzymają wiadomość e-mail z przeglądów dostępu ponieważ one musisz najpierw zaakceptować zaproszenie przed przeglądania.

## <a name="create-reviews-via-apis"></a>Utwórz przeglądy za pośrednictwem interfejsów API

Można również utworzyć przeglądów dostępu za pomocą interfejsów API. Co zrobić, aby zarządzać dostępem do przeglądów grup i użytkowników aplikacji w witrynie Azure portal można również wykonać przy użyciu interfejsów API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [przeglądy dostępu w usłudze Azure AD, dokumentacja interfejsu API](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Przykładowy kod, zobacz [przykład pobierania dostępu usługi Azure AD, przeglądy za pomocą programu Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd dostępu wszystkich użytkowników do grupy lub aplikacji](perform-access-review.md)
- [Przegląd dostępu wszystkich użytkowników dla siebie do grupy lub aplikacji](review-your-access.md)
- [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)
