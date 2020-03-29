---
title: Konfigurowanie zasad dostępu warunkowego w trybie tylko do raportu — usługa Azure Active Directory
description: Korzystanie z trybu tylko do raportu w dostępie warunkowym do pomocy w adopcji
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295111"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Konfigurowanie zasad dostępu warunkowego w trybie tylko do raportu (Wersja zapoznawcza)

Aby skonfigurować zasady dostępu warunkowego w trybie tylko do raportu:

> [!IMPORTANT]
> Jeśli twoja organizacja jeszcze tego nie zrobiła, [skonfiguruj integrację usługi Azure Monitor z usługą Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Proces ten musi się odbyć, zanim dane będą dostępne do przeglądu.

1. Zaloguj się do **witryny Azure portal** jako administrator dostępu warunkowego, administrator zabezpieczeń lub administrator globalny.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
1. Skonfiguruj warunki zasad i wymagane formanty dotacji w razie potrzeby.
1. W obszarze **Włącz zasady** ustaw przełącznik na tryb tylko do **raportu.**
1. Wybierz pozycję **Zapisz**.

> [!TIP]
> Można edytować stan **włącz zasadę** istniejącej zasady z **włącz** **do raportu,** ale spowoduje to wyłączenie wymuszania zasad. 

Wyświetlanie wyniku tylko do raportu w dziennikach logowania usługi Azure AD.

Aby wyświetlić wynik zasad tylko do raportu dla określonego logowania:

1. Zaloguj się do **witryny Azure portal** jako czytnik raportów, czytnik zabezpieczeń, administrator zabezpieczeń lub administrator globalny.
1. Przejdź do logowania usługi **Azure Active Directory** > **.**
1. Wybierz logowanie lub dodaj filtry, aby zawęzić wyniki.
1. W szufladzie **Szczegóły** wybierz kartę **Tylko do raportu (Wersja zapoznawcza),** aby wyświetlić zasady oceniane podczas logowania.

> [!NOTE]
> Podczas pobierania dzienników logowania wybierz format JSON, aby uwzględnić dane wyników tylko do raportu dostępu warunkowego.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Konfigurowanie integracji usługi Azure Monitor z usługą Azure AD

Aby wyświetlić zagregowany wpływ zasad dostępu warunkowego przy użyciu nowego skoroszytu usługi Warunkowy dostęp do usługi Insights, należy zintegrować usługę Azure Monitor z usługą Azure AD i wyeksportować dzienniki logowania. Istnieją dwa kroki, aby skonfigurować tę integrację: 

1. [Zarejestruj się w celu utworzenia subskrypcji usługi Azure Monitor i utwórz obszar roboczy](/azure/azure-monitor/learn/quick-create-workspace).
1. [Eksportuj dzienniki logowania z usługi Azure AD do usługi Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Więcej informacji na temat cen usługi Azure Monitor można znaleźć na [stronie cennika usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Zasoby do szacowania kosztów, ustawiania dziennego limitu lub dostosowywania okresu przechowywania danych można znaleźć w artykule [Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Wyświetlanie skoroszytu statystyk dostępu warunkowego

Po zintegrowaniu dzienników usługi Azure AD z usługą Azure Monitor można monitorować wpływ zasad dostępu warunkowego przy użyciu nowych skoroszytów wglądu w dostęp warunkowy.

1. Zaloguj się do **witryny Azure portal** jako administrator zabezpieczeń lub administrator globalny.
1. Przejdź do**skoroszytów** **usługi Azure Active Directory** > .
1. Wybierz **warunkowe statystyki dostępu (wersja zapoznawcza)**.
1. Wybierz jedną lub więcej zasad z listy rozwijanej **Zasady dostępu warunkowego.** Wszystkie włączone zasady są wybierane domyślnie.
1. Wybierz zakres czasu (jeśli zakres czasu przekracza dostępny zestaw danych, raport pokaże wszystkie dostępne dane). Po ustawieniu parametrów **zasad dostępu warunkowego** i **zakresu czasu** raport zostanie załadowany.
   1. Opcjonalnie wyszukaj poszczególnych **użytkowników** lub **aplikacje,** aby zawęzić zakres raportu.
1. Wybierz między wyświetlaniem danych w zakresie czasu według liczby użytkowników lub liczby logów.
1. W zależności od **widoku Dane** **podsumowanie wpływu** wyświetla liczbę użytkowników lub logowanie w zakresie wybranych parametrów, pogrupowanych według całkowitej liczby, **sukcesu,** **niepowodzenia,** **wymaganej akcji użytkownika**i **Niestosowano**. Wybierz kafelek, aby sprawdzić logowania określonego typu wyniku. 
   1. Jeśli parametry skoroszytu zostały zmienione, można zapisać kopię do wykorzystania w przyszłości. Wybierz ikonę zapisz u góry raportu i podaj nazwę i lokalizację do zapisania.
1. Przewiń w dół, aby wyświetlić podział logów dla każdego warunku.
1. Wyświetl **szczegóły logowania u** dołu raportu, aby zbadać poszczególne zdarzenia logowania filtrowane według powyższych wyborów.

> [!TIP] 
> Chcesz przejść do szczegółów określonej kwerendy lub wyeksportować szczegóły logowania? Wybierz przycisk po prawej stronie dowolnej kwerendy, aby otworzyć kwerendę w usłudze Log Analytics. Wybierz pozycję Eksportuj, aby wyeksportować do pliku CSV lub usługi Power BI.

## <a name="common-problems-and-solutions"></a>Typowe problemy i rozwiązania

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Dlaczego kwerendy w skoroszycie nie popełniają się niepowodzeniem?

Klienci zauważyli, że kwerendy czasami nie powiodą się, jeśli z skoroszytem jest skojarzonych nieprawidłowe lub wiele obszarów roboczych. Aby rozwiązać ten problem, kliknij pozycję **Edytuj** u góry skoroszytu, a następnie na narzędziu Ustawienia. Zaznacz, a następnie usuń obszary robocze, które nie są skojarzone ze skoroszytem. Z każdym skoroszytem powinien być skojarzony tylko jeden obszar roboczy.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Dlaczego parametr listy rozwijanej Zasady dostępu warunkowego nie zawiera moich zasad?

Listy rozwijane Zasady dostępu warunkowego są wypełniane przez wykonywanie zapytań o najnowsze logowania w okresie 4 godzin. Jeśli dzierżawca nie ma żadnych logowania w ciągu ostatnich 4 godzin, jest możliwe, że listy rozwijanej będzie pusty. Jeśli to opóźnienie jest trwały problem, takich jak w małych dzierżaw z rzadkich logowania, administratorzy mogą edytować kwerendę dla zasad dostępu warunkowego zasady rozwijane i przedłużyć czas kwerendy do czasu dłuższego niż 4 godziny.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

Aby uzyskać więcej informacji na temat skoroszytów usługi Azure AD, zobacz artykuł [Jak używać skoroszytów usługi Azure Monitor dla raportów usługi Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
