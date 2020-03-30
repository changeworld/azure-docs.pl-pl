---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476253"
---
## <a name="create-one-or-more-access-reviews"></a>Tworzenie co najmniej jednej recenzji dostępu

1. Kliknij **przycisk Nowy,** aby utworzyć nową recenzję dostępu.

1. Nazwij recenzję dostępu. Opcjonalnie podaj opis recenzji. Nazwa i opis są wyświetlane recenzentom.

    ![Tworzenie recenzji dostępu — recenzja nazwy i opisu](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ustaw **datę rozpoczęcia**. Domyślnie przegląd dostępu występuje raz, rozpoczyna się w tym samym czasie, w której został utworzony, a kończy się za miesiąc. Możesz zmienić daty rozpoczęcia i zakończenia, aby w przyszłości rozpocząć przegląd dostępu i trwać przez wiele dni.

    ![Data rozpoczęcia, częstotliwość, czas trwania, koniec, liczba razy i data zakończenia](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Aby przegląd dostępu był powtarzany, zmień ustawienie **Częstotliwość** z **Jeden raz** na **tygodniowy,** **Miesięczny,** **Kwartalny,** **Roczny**lub **Półroczny.** Użyj suwaka **czas trwania** lub pola tekstowego, aby określić, ile dni każdy przegląd serii cyklicznej będzie otwarty dla danych wejściowych od recenzentów. Na przykład maksymalny czas trwania, który można ustawić dla miesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładania się opinii.

1. Użyj **end ustawienie,** aby określić, jak zakończyć cyklicznych serii przeglądu dostępu. Seria może zakończyć się na trzy sposoby: działa w sposób ciągły, aby rozpocząć przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu określonej liczby wystąpień. Użytkownik, inny administrator użytkownika lub inny administrator globalny może zatrzymać serię po utworzeniu, zmieniając datę w **Ustawieniach,** tak aby kończyła się w tym dniu.

1. W sekcji **Użytkownicy** wybierz jedną lub więcej ról, które chcesz przejrzeć członkostwo.

    ![Zakres użytkowników do przeglądu członkostwa roli](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Wybranie więcej niż jednej roli spowoduje utworzenie wielu recenzji dostępu. Na przykład wybranie pięciu ról spowoduje utworzenie pięciu oddzielnych przeglądów dostępu.

    Jeśli tworzysz przegląd dostępu ról usługi Azure AD, poniżej przedstawiono przykład listy członkostwa recenzja.

    ![Przeglądanie okienka członkostwa z listą ról usługi Azure AD, które możesz wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Jeśli tworzysz przegląd dostępu ról zasobów platformy Azure, poniżej przedstawiono przykład listy członkostwa Przegląd.

    ![Przeglądanie okienka członkostwa z listą ról zasobów platformy Azure, które możesz wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. W sekcji **Recenzenci** wybierz jedną lub więcej osób, które mają przejrzeć wszystkich użytkowników. Możesz też wybrać, aby członkowie przeglądali własny dostęp.

    ![Lista recenzentów wybranych użytkowników lub członków (samodzielnie)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Wybrani użytkownicy** — użyj tej opcji, gdy nie wiesz, kto potrzebuje dostępu. Za pomocą tej opcji można przypisać recenzję do właściciela zasobu lub menedżera grupy do ukończenia.
    - **Członkowie (samodzielnie)** — użyj tej opcji, aby użytkownicy przeglądali własne przypisania ról.

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **Po zakończeniu ustawień.**

    ![Po zakończeniu ustawień do automatycznego zastosowania i nie należy](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć dostęp dla użytkowników, którym odmówiono odmowy, ustaw **automatyczne stosowanie wyników do zasobu,** aby **włączyć**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik na **Wyłącz**.

1. Użyj listy **Czy recenzent nie odpowiada,** aby określić, co się stanie dla użytkowników, którzy nie są przeglądani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali przejrzeni ręcznie przez recenzentów. Jeśli ostateczną decyzją recenzenta jest Odmów, dostęp użytkownika zostanie usunięty.

    - **Bez zmian** - Pozostaw dostęp użytkownika bez zmian
    - **Usuwanie dostępu** — usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - **Podejmij zalecenia** — przyjmuj zalecenia systemu dotyczące odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane.**

    ![Zaawansowane ustawienia rekomendacji pokazywalnych, wymagają uzasadnienia przy zatwierdzaniu, powiadomieniach pocztą i przypomnieniach](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ustaw **pokaż zalecenia,** aby **włączyć,** aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **wymagaj przyczyny przy zatwierdzeniu,** aby **włączyć** wymagać od recenzenta podania przyczyny zatwierdzenia.

1. Ustaw **powiadomienia poczty,** aby **włączyć,** aby usługa Azure AD wysyłała powiadomienia e-mail do recenzentów po rozpoczęciu przeglądu dostępu i do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia,** aby **włączyć,** aby usługa Azure AD wysyłała przypomnienia o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli przeglądu.
