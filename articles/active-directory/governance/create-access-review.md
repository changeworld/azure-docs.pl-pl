---
title: Tworzenie przeglądu dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji z usługą Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Przegląd dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji.
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
ms.date: 11/15/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 1c71333a49643647bae3730e4f14f26939388c4f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153747"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Utwórz Przegląd dostępu członków grupy lub dostęp do aplikacji z usługą Azure AD

Dostęp do grupy i aplikacje dla pracowników i gości zmienia się wraz z upływem czasu. W celu zmniejszenia ryzyka związanego z przypisania starych dostępu, Administratorzy mogą używać usługi Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostęp do aplikacji. Jeśli zachodzi potrzeba rutynowo Przegląd dostępu wszystkich użytkowników, można również utworzyć cyklicznego przeglądów dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [zarządzanie dostępem gości](manage-guest-access-with-access-reviews.md).

W tym artykule opisano sposób tworzenia nowego przeglądu dostępu dla członków grupy lub dostęp do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- [Przeglądy dostępu włączone](access-reviews-overview.md)
- Administrator globalny lub Administrator konta

## <a name="create-an-access-review"></a>Tworzenie przeglądu dostępu

1. Zaloguj się do witryny Azure portal i Otwórz [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Kliknij przycisk **formantów**.

1. Kliknij przycisk **nowy przegląd dostępu** Aby utworzyć nowy przegląd dostępu.

    ![Przegląd dostępu — formanty](./media/create-access-review/controls.png)

1. Nazwa przeglądu dostępu. Opcjonalnie można podać przeglądu opis. Nazwa i opis są wyświetlane dla recenzentów.

    ![Tworzenie przeglądu dostępu — Przegląd nazwę i opis](./media/create-access-review/name-description.png)

1. Ustaw **Data rozpoczęcia**. Domyślnie przeglądu dostępu pojawia się raz, rozpoczyna się w tym samym czasie, w którym zostały utworzone, a kończy się ona w ciągu jednego miesiąca. Można zmienić początek, a końcową dostępu Przejrzyj rozpoczęcia w przyszłości i ostatnio jednak liczbę dni ma.

    ![Tworzenie przeglądu dostępu — rozpoczęcia i zakończenia](./media/create-access-review/start-end-dates.png)

1. Aby cyklu przeglądu dostępu, należy zmienić **częstotliwość** z **jeden raz** do **tygodniowy**, **miesięczne**,  **Co kwartał** lub **rocznie**i użyj **czas trwania** suwaka lub pola tekstowego, aby określić, ile dni będą każdej recenzji serii cyklicznych, Otwórz na dane wejściowe recenzentów. Na przykład maksymalny czas trwania, którego można ustawić comiesięcznego przeglądu jest 27 dni, należy unikać nakładania się recenzji.

1. Użyj **zakończenia** ustawienie, aby określić sposób kończyć się cyklicznym dostępu Przejrzyj serii. Seria mogą kończyć się na trzy sposoby: działa w sposób ciągły można uruchomić przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Inny Administrator konta użytkownika lub inny Administrator globalny może zatrzymać serii po utworzeniu, zmieniając datę w **ustawienia**, dzięki czemu kończy się ona w tym dniu.

1. W **użytkowników** sekcji, określ dotyczą użytkowników, którzy Przegląd dostępu. Przeglądy dostępu może być członkami grupy lub osób, które zostały przypisane do aplikacji. Możesz dodatkowo ograniczyć dostęp tylko do przeglądu i przejrzyj użytkowników-gości kto są elementami członkowskimi (lub przypisane do aplikacji), zamiast przeglądanie wszystkich użytkowników, którzy są członkami lub mających dostęp do aplikacji.

    ![Tworzenie przeglądu dostępu — użytkownicy](./media/create-access-review/users.png)

1. W **recenzentów** wybierz jeden lub więcej osób, aby zapoznać się z wszystkich użytkowników w zakresie. Lub możesz wybrać członkowie dokonać przeglądu własnego dostępu. Jeśli zasób jest grupą, możesz poprosić właścicieli grupy, aby przejrzeć. Możesz również wymagać że recenzenci mogą zatwierdzać dostęp, podania przyczyny.

    ![Tworzenie przeglądu dostępu — osób dokonujących przeglądu](./media/create-access-review/reviewers.png)

1. W **programy** wybierz program, którego chcesz użyć. Można uprościć jak śledzenie i zbierać przeglądów dostępu do różnych celów, organizując je w programach. **Domyślny Program** zawsze jest obecny, lub utworzyć inny program. Na przykład możesz mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych.

    ![Tworzenie przeglądu dostępu — programy](./media/create-access-review/programs.png)

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

## <a name="start-the-access-review"></a>Rozpocznij Przegląd dostępu

Po określeniu ustawienia przeglądu dostępu, kliknij przycisk **Start**.

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów, wkrótce, po uruchomieniu przeglądu. Jeśli użytkownik chce nie ma wysyłać wiadomości e-mail z usługi Azure AD, pamiętaj poinformować osób dokonujących przeglądu, które przeglądu dostępu oczekuje na ich zakończenie. Można pokazać im instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników](perform-access-review.md). W przypadku zapoznania się z nimi dla gości, aby dokonać przeglądu własnego dostępu, wyświetlić je instrukcje dotyczące sposobu [przeglądu własnego dostępu](perform-access-review.md).

W przypadku niektórych recenzentów gości, gości są powiadamiane za pośrednictwem poczty e-mail tylko wtedy, gdy już zaakceptowane zaproszenia.

## <a name="manage-the-access-review"></a>Zarządzanie przeglądu dostępu

Recenzenci wykonać ich recenzje na pulpicie nawigacyjnym usługi Azure AD w postęp można śledzić **przeglądów dostępu** sekcji. Nie prawa dostępu zostaną zmienione w katalogu, dopóki [zakończeniu przeglądu](complete-access-review.md).

Jeśli jest to jednorazowa przeglądu, następnie po umieszczeniu okres przeglądu dostępu lub administrator zatrzymania przeglądu dostępu postępuj zgodnie z instrukcjami w [Kończenie przeglądu dostępu](complete-access-review.md) aby zobaczyć i zastosować wyniki.  

Aby zarządzać serię przeglądów dostępu, przejdź do przeglądu dostępu z **formantów**, będzie znajdowanie kolejnych wystąpień w przeglądach zaplanowane i Edytuj datę zakończenia lub Dodawanie/usuwanie recenzentów w związku z tym. 

Na podstawie dokonanego wyboru w ustawienia działań po zakończeniu, automatycznie stosowanego będzie wykonywany po Data zakończenia przeglądu lub jeśli ręcznie zatrzymaj przeglądu. Stan przeglądu ulegnie zmianie z ukończono za pośrednictwem pośrednich stanów, takich jak stosowanie a na koniec stan zastosowano. Należy się spodziewać się zablokowani użytkownicy ewentualnej usuwany z przypisania grupy członkostwa lub aplikacji w ciągu kilku minut.

## <a name="create-reviews-via-apis"></a>Utwórz przeglądy za pośrednictwem interfejsów API

Można również utworzyć przeglądów dostępu za pomocą interfejsów API. Co zrobić, aby zarządzać dostępem do przeglądów grup i użytkowników aplikacji w witrynie Azure portal można również wykonać przy użyciu interfejsów API programu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [dokumentacja przeglądów dostępu usługi Azure AD interfejsu API](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/accessreviews_root). Przykładowy kod, zobacz [przykład pobierania przeglądów dostępu usługi Azure AD za pomocą programu Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Kolejne kroki

- [Uruchamianie przeglądu dostępu za pomocą przeglądów dostępu usługi Azure AD](perform-access-review.md)
- [Kończenie przeglądu dostępu członków grupy lub dostęp użytkowników do aplikacji w usłudze Azure AD](complete-access-review.md)
