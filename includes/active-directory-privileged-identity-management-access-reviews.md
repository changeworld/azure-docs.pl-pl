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
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476253"
---
## <a name="create-one-or-more-access-reviews"></a>Utwórz co najmniej jeden przeglądów dostępu

1. Kliknij przycisk **New** Aby utworzyć nowy przegląd dostępu.

1. Nazwa przeglądu dostępu. Opcjonalnie można podać przeglądu opis. Nazwa i opis są wyświetlane dla recenzentów.

    ![Tworzenie przeglądu dostępu — Przegląd nazwę i opis](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ustaw **Data rozpoczęcia**. Domyślnie przeglądu dostępu pojawia się raz, rozpoczyna się w tym samym czasie, w którym zostały utworzone, a kończy się ona w ciągu jednego miesiąca. Można zmienić początek, a końcową dostępu Przejrzyj rozpoczęcia w przyszłości i ostatnio jednak liczbę dni ma.

    ![Data, częstotliwość, czas trwania, zakończenia, liczba, Data rozpoczęcia i zakończenia](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Aby cyklu przeglądu dostępu, należy zmienić **częstotliwość** z **jeden raz** do **tygodniowy**, **miesięczne**,  **Co kwartał**, **rocznie**, lub **częściową annually**. Użyj **czas trwania** suwaka lub pola tekstowego, aby określić, ile dni będą każdej recenzji serii cyklicznych, Otwórz na dane wejściowe recenzentów. Na przykład maksymalny czas trwania, którego można ustawić comiesięcznego przeglądu jest 27 dni, należy unikać nakładania się recenzji.

1. Użyj **zakończenia** ustawienie, aby określić sposób kończyć się cyklicznym dostępu Przejrzyj serii. Seria mogą kończyć się na trzy sposoby: działa w sposób ciągły można uruchomić przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Inny administrator użytkownika lub inny administrator globalny może zatrzymać serii po utworzeniu, zmieniając datę w **ustawienia**, dzięki czemu kończy się ona w tym dniu.

1. W **użytkowników** wybierz jedną lub więcej ról, które chcesz przejrzeć członkostwo.

    ![Zakresem użytkowników, aby zapoznać się z członkostwa w roli](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Wybranie więcej niż jednej roli spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięć ról utworzy pięciu przeglądach uprawnień dostępu.

    Jeśli tworzysz przeglądu dostępu ról usługi Azure AD, poniżej przedstawiono przykład listy członkostwa przeglądu.

    ![Przegląd członkostwa w okienku role usługi Azure AD można wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Jeśli tworzysz przeglądu dostępu dla ról zasobów platformy Azure, poniżej przedstawiono przykład listy członkostwa przeglądu.

    ![Przeglądanie członkostwa w okienku role zasobów platformy Azure można wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. W **recenzentów** wybierz co najmniej jedną osobę, aby zapoznać się z wszystkich użytkowników. Lub możesz wybrać członkowie dokonać przeglądu własnego dostępu.

    ![Lista osób dokonujących przeglądu wybrani użytkownicy lub członkowie (własny)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Wybranych użytkowników** — Użyj tej opcji, kiedy nie wiesz, który wymaga dostępu. Po wybraniu tej opcji można przypisać przeglądu właściciel zasobu lub kierownik grupy, aby zakończyć.
    - **Członkowie (własny)** — Użyj tej opcji, aby użytkownicy mogli przejrzeć swoje własne przypisań ról.

### <a name="upon-completion-settings"></a>Ustawienia działań po zakończeniu

1. Aby określić, co się dzieje po zakończeniu przeglądu, rozwiń **ustawienia działań po zakończeniu** sekcji.

    ![Po zakończeniu zastosować ustawienia automatycznego i powinien przeglądu nie odpowiada](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Jeśli chcesz automatycznie Usuń dostęp dla użytkowników, które zostały odrzucone, ustaw **automatycznie Zastosuj wyniki do zasobu** do **Włącz**. Jeśli chcesz ręcznie zastosować wyników, po zakończeniu przeglądu, ustaw przełącznik **wyłączyć**.

1. Użyj **powinien recenzenta odpowiada** listy, aby określić, co się stanie, dla użytkowników, którzy nie są przeglądane przez recenzenta przed upływem przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostało przejrzane przez recenzenta ręcznie. Jeśli decyzja końcowego recenzent jest Odmów, dostęp użytkownika zostaną usunięte.

    - **Nie wprowadzono zmian** — pozostaw dostępu użytkownika bez zmian
    - **Usuń dostęp** -spowodować usunięcie dostępu użytkownika
    - **Zatwierdź dostęp** — zatwierdzanie dostępu użytkownika
    - **Uwzględnij rekomendacje** — wykonaj zalecenia systemu odmowy lub zatwierdzenie użytkownika przez stały dostęp

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń **Zaawansowane ustawienia** sekcji.

    ![Ustawienia zaawansowane dla Pokaż zalecenia, wymagana Przyczyna przy zatwierdzenia, powiadomienia pocztowe i przypomnienia](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ustaw **Pokaż zalecenia** do **Włącz** pokazują stan systemu recenzentów zalecenia na podstawie informacji dotyczących dostępu użytkownika.

1. Ustaw **wymagana Przyczyna przy zatwierdzaniu** do **Włącz** do Wymagaj od recenzenta podania przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztowe** do **Włącz** mieć usługi Azure AD, wysyłania powiadomień e-mail do recenzentów podczas uruchamiania przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** do **Włącz** aby platforma Azure AD wysyłała przypomnienia o przeglądach dostępu w toku dla recenzentów, którzy nie ukończyli swojego przeglądu.
