---
title: Tworzenie przeglądu dostępu grup & aplikacji — Azure AD
description: Dowiedz się, jak utworzyć przegląd dostępu członków grupy lub dostępu do aplikacji w przeglądach dostępu usługi Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b8f2cf7db474f2a67ebaed818a0a440a195326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128842"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu grup i aplikacji w przeglądach dostępu usługi Azure AD

Dostęp do grup i aplikacji dla pracowników i gości zmienia się w czasie. Aby zmniejszyć ryzyko związane ze starszymi przydziałami dostępu, administratorzy mogą używać usługi Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostępu do aplikacji. Jeśli chcesz rutynowo przeglądać dostęp, możesz również tworzyć cykliczne przeglądy dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [Zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [zarządzanie dostępem gościa](manage-guest-access-with-access-reviews.md).

W tym artykule opisano sposób tworzenia co najmniej jednej recenzje dostępu dla członków grupy lub dostępu do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2
- Administrator globalny lub administrator użytkownika

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Tworzenie co najmniej jednej recenzji dostępu

1. Zaloguj się do witryny Azure portal i otwórz [stronę zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij pozycję **Przeglądy programu Access**.

1. Kliknij **pozycję Nowa recenzja dostępu,** aby utworzyć nową recenzję dostępu.

    ![Okienko Przeglądy dostępu w zarządzania tożsamościami](./media/create-access-review/access-reviews.png)

1. Nazwij recenzję dostępu. Opcjonalnie podaj opis recenzji. Nazwa i opis są wyświetlane recenzentom.

    ![Tworzenie recenzji dostępu — recenzja nazwy i opisu](./media/create-access-review/name-description.png)

1. Ustaw **datę rozpoczęcia**. Domyślnie przegląd dostępu występuje raz, rozpoczyna się w tym samym czasie, w której został utworzony, a kończy się za miesiąc. Możesz zmienić daty rozpoczęcia i zakończenia, aby w przyszłości rozpocząć przegląd dostępu i trwać przez wiele dni.

    ![Tworzenie przeglądu dostępu — daty rozpoczęcia i zakończenia](./media/create-access-review/start-end-dates.png)

1. Aby przegląd dostępu był powtarzany, zmień ustawienie **Częstotliwość** z **Jeden raz** na **tygodniowy,** **Miesięczny,** **Kwartalny,** **Półroczny**lub **Roczny.** Użyj suwaka **czas trwania** lub pola tekstowego, aby określić, ile dni każdy przegląd serii cyklicznej będzie otwarty dla danych wejściowych od recenzentów. Na przykład maksymalny czas trwania, który można ustawić dla miesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładania się opinii.

1. Użyj **end ustawienie,** aby określić, jak zakończyć cyklicznych serii przeglądu dostępu. Seria może zakończyć się na trzy sposoby: działa w sposób ciągły, aby rozpocząć przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu określonej liczby wystąpień. Użytkownik, inny administrator użytkownika lub inny administrator globalny może zatrzymać serię po utworzeniu, zmieniając datę w **Ustawieniach,** tak aby kończyła się w tym dniu.

1. W sekcji **Użytkownicy** określ użytkowników, których dotyczy przegląd dostępu. Przeglądy dostępu mogą być dla członków grupy lub dla użytkowników, którzy zostali przypisani do aplikacji. Można dalej zakres przeglądu dostępu do przeglądu tylko użytkowników-gości, którzy są członkami (lub przypisane do aplikacji), a nie przeglądowi wszystkich użytkowników, którzy są członkami lub którzy mają dostęp do aplikacji.

    ![Tworzenie recenzji dostępu — użytkownicy](./media/create-access-review/users.png)

1. W sekcji **Grupa** wybierz jedną lub więcej grup, w których chcesz przejrzeć członkostwo.

    > [!NOTE]
    > Wybranie więcej niż jednej grupy spowoduje utworzenie wielu recenzji dostępu. Na przykład wybranie pięciu grup spowoduje utworzenie pięciu oddzielnych przeglądów dostępu.
    
    ![Tworzenie recenzji dostępu — wybierz grupę](./media/create-access-review/select-group.png)

1. W sekcji **Aplikacje** (jeśli w kroku 8 wybrano opcję **Przypisano do aplikacji)** wybierz aplikacje, do których chcesz przejrzeć dostęp.

    > [!NOTE]
    > Wybranie więcej niż jednej aplikacji spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu aplikacji spowoduje utworzenie pięciu oddzielnych przeglądów dostępu.
    
    ![Tworzenie przeglądu dostępu — wybierz aplikację](./media/create-access-review/select-application.png)

1. W **recenzenci** sekcji wybierz jedną lub więcej osób, aby przejrzeć wszystkich użytkowników w zakresie. Możesz też wybrać, aby członkowie przeglądali własny dostęp. Jeśli zasób jest grupą, możesz poprosić właścicieli grupy o sprawdzenie. Można również wymagać, aby recenzenci podać przyczynę podczas zatwierdzania dostępu.

    ![Tworzenie recenzji dostępu — recenzenci](./media/create-access-review/reviewers.png)

1. W sekcji **Programy** wybierz program, którego chcesz użyć. **Domyślny program** jest zawsze obecny.

    ![Tworzenie recenzji dostępu — programy](./media/create-access-review/programs.png)

    Można uprościć sposób śledzenia i zbierania opinii o dostępie do różnych celów, organizując je w programy. Każdy przegląd dostępu może być połączony z programem. Następnie podczas przygotowywania raportów dla audytora można skupić się na przeglądach dostępu w zakresie konkretnej inicjatywy. Programy i wyniki przeglądu dostępu są widoczne dla użytkowników w roli administratora globalnego, administratora użytkownika, administratora zabezpieczeń lub czytnika zabezpieczeń.

    Aby wyświetlić listę programów, przejdź do strony przeglądy dostępu i wybierz pozycję **Programy**. Jeśli jesteś administratorem globalnym lub administratorem użytkownika, możesz utworzyć dodatkowe programy. Na przykład można wybrać jeden program dla każdej inicjatywy zgodności lub celu biznesowego. Jeśli program nie jest już potrzebny i nie ma z nim żadnych elementów sterujących, możesz go usunąć.

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **Po zakończeniu ustawień.**

    ![Tworzenie recenzji dostępu — po zakończeniu ustawień](./media/create-access-review/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć, dostęp dla użytkowników, którym odmówiono, ustaw **automatyczne stosowanie wyników do zasobu,** aby **włączyć**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik na **Wyłącz**.

1. Użyj listy **Czy recenzent nie odpowiada,** aby określić, co się stanie dla użytkowników, którzy nie są przeglądani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali przejrzeni ręcznie przez recenzentów. Jeśli ostateczną decyzją recenzenta jest Odmów, dostęp użytkownika zostanie usunięty.

    - **Bez zmian** - Pozostaw dostęp użytkownika bez zmian
    - **Usuwanie dostępu** — usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - **Podejmij zalecenia** — przyjmuj zalecenia systemu dotyczące odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane.**

    ![Tworzenie recenzji dostępu — ustawienia zaawansowane](./media/create-access-review/advanced-settings.png)

1. Ustaw **pokaż zalecenia,** aby **włączyć,** aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **wymagaj przyczyny przy zatwierdzeniu,** aby **włączyć** wymagać od recenzenta podania przyczyny zatwierdzenia.

1. Ustaw **powiadomienia poczty,** aby **włączyć,** aby usługa Azure AD wysyłała powiadomienia e-mail do recenzentów po rozpoczęciu przeglądu dostępu i do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia,** aby **włączyć,** aby usługa Azure AD wysyłała przypomnienia o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli przeglądu.

    Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

## <a name="start-the-access-review"></a>Rozpoczynanie przeglądu dostępu

Po określeniu ustawień przeglądu dostępu kliknij przycisk **Start**. Przegląd dostępu pojawi się na liście ze wskaźnikiem jego stanu.

![Lista przeglądów dostępu i ich status](./media/create-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysyłała wiadomość e-mail, należy poinformować recenzentów, że przegląd dostępu czeka na ich ukończenie. Możesz pokazać im instrukcje dotyczące [przeglądania dostępu do grup lub aplikacji](perform-access-review.md). Jeśli twoja recenzja jest dla gości, aby przejrzeć swój własny dostęp, pokaż im instrukcje, jak [sprawdzić dostęp dla siebie do grup lub aplikacji](review-your-access.md).

Jeśli przydzielisz gości jako recenzentów i nie przyjęli zaproszenia, nie otrzymają oni wiadomości e-mail z opinii o dostępie, ponieważ muszą najpierw zaakceptować zaproszenie przed sprawdzeniem.

## <a name="access-review-status-table"></a>Tabela stanu przeglądu programu Access

| Stan | Definicja |
|--------|------------|
|NotStarted (Nierozpoczęcie) | Recenzja została utworzona, odnajdowanie użytkowników czeka na rozpoczęcie. |
|Inicjowanie   | Odnajdowanie użytkowników jest w toku, aby zidentyfikować wszystkich użytkowników, którzy są częścią przeglądu. |
|Uruchamianie | Rozpoczyna się przegląd. Jeśli powiadomienia e-mail są włączone, wiadomości e-mail są wysyłane do recenzentów. |
|InProgress (Ruch przychodzący) | Rozpoczął się przegląd. Jeśli powiadomienia e-mail są włączone, do recenzentów zostały wysłane wiadomości e-mail. Recenzenci mogą składać decyzje do terminu płatności. |
|Zakończeniu | Weryfikacja jest zakończona, a wiadomości e-mail są wysyłane do właściciela recenzji. |
|Autowyświetlanie | Recenzja jest na etapie przeglądu systemu. System rejestruje decyzje dla użytkowników, którzy nie zostali przejrzeni na podstawie zaleceń lub wstępnie skonfigurowanych decyzji. |
|Autoocesło | Decyzje zostały zarejestrowane przez system dla wszystkich użytkowników, którzy nie zostali poddani przeglądowi. Recenzja jest gotowa do przejścia do **Stosowania,** jeśli funkcja automatycznego stosowania jest włączona. |
|Stosowania | Nie będzie żadnych zmian w dostępie dla użytkowników, którzy zostali zatwierdzeni. |
|Zastosowano | Odmówiono użytkownikom, jeśli istnieją, zostały usunięte z zasobu lub katalogu. |

## <a name="create-reviews-via-apis"></a>Tworzenie recenzji za pośrednictwem interfejsów API

Można również tworzyć przeglądy dostępu za pomocą interfejsów API. Co można zrobić, aby zarządzać przeglądy dostępu grup i użytkowników aplikacji w witrynie Azure portal można również zrobić za pomocą interfejsów API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [odwołanie do interfejsu API dostępu usługi Azure AD](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Przykładowy kod można znaleźć na [przykładzie pobierania przeglądów dostępu usługi Azure AD za pośrednictwem programu Microsoft Graph.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Przeglądanie dostępu do grup lub aplikacji](review-your-access.md)
- [Ukończ przegląd dostępu grup lub aplikacji](complete-access-review.md)
