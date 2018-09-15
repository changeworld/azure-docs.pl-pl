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
ms.component: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: e52686a8cfd691a7f82c6e64968cd94634fd596e
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45607794"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Utwórz Przegląd dostępu członków grupy lub dostęp do aplikacji z usługą Azure AD

Przypisania dostępu stają się "starych", gdy użytkownicy mają dostęp, które nie potrzebują więcej. W celu zmniejszenia ryzyka związanego z przypisania starych dostępu, Administratorzy mogą używać usługi Azure Active Directory (Azure AD), tworzenie przeglądu dostępu dla członków grupy lub Użytkownicy przypisani do aplikacji. Tworzenie cyklicznego przeglądy dostępu można zaoszczędzić czas. Jeśli zachodzi potrzeba regularnie sprawdzaj użytkowników, którzy mają dostęp do aplikacji lub są członkowie danej grupy, można zdefiniować częstotliwość tych recenzji. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [zarządzanie dostępem gości](manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Utwórz przegląd dostępu

1. Jako administrator globalny lub administrator kont użytkowników, przejdź do [strony przeglądów dostępu](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)i wybierz **programy**.

2. Wybierz program, który zawiera formant przeglądu dostępu, który chcesz utworzyć. **Domyślny Program** zawsze jest obecny, lub utworzyć inny program. Na przykład możesz mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych.

3. W ramach programu, wybierz **kontrolki**, a następnie wybierz pozycję **Dodaj** dodać kontrolkę.

4. Nazwa przeglądu dostępu. Opcjonalnie można podać przeglądu opis. Nazwa i opis są wyświetlane dla recenzentów.

5. Ustaw datę rozpoczęcia. Domyślnie przeglądu dostępu pojawia się raz, rozpoczyna się w tym samym czasie, w którym zostały utworzone, a kończy się ona w ciągu jednego miesiąca. Można zmienić początek, a końcową dostępu Przejrzyj rozpoczęcia w przyszłości i ostatnio jednak liczbę dni ma.

6. Aby cyklu przeglądu dostępu, zmiana częstotliwości z jeden raz, aby co tydzień, co miesiąc, co kwartał i co roku i umożliwia zdefiniowanie liczby dni każdego przeglądu serii cyklicznych zostanie otwarta na dane wejściowe recenzentów suwaka lub pola tekstowego. Na przykład maksymalny czas trwania można ustawić dla miesięczny Przegląd jest 27 dni, należy unikać nakładania się recenzji. 

7.  Cykliczne serii przeglądu dostępu można zakończyć na 3 sposoby: działa w sposób ciągły można uruchomić przeglądy przez czas nieokreślony, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Inny administrator konta użytkownika lub inny administrator globalny może zatrzymać serii po utworzeniu, zmieniając datę w obszarze Ustawienia, aby kończy się ona w tym dniu.

8. Przeglądy dostępu może być członkami grupy lub osób, które zostały przypisane do aplikacji. Możesz dodatkowo ograniczyć dostęp tylko do przeglądu i przejrzyj użytkowników-gości kto są elementami członkowskimi (lub przypisany do aplikacji), zamiast przeglądanie wszystkich użytkowników, którzy są członkami lub mających dostęp do aplikacji.

9. Wybierz jeden lub więcej osób, aby zapoznać się z wszystkich użytkowników w zakresie. Lub możesz wybrać członkowie dokonać przeglądu własnego dostępu. Jeśli zasób jest grupą, możesz poprosić właścicieli grupy, aby przejrzeć. Możesz również wymagać że recenzenci mogą zatwierdzać dostęp, podania przyczyny.

10. Jeśli użytkownik chce ręcznie zastosować wyników, po zakończeniu przeglądu, kliknij przycisk **Start**.  W przeciwnym razie następnej sekcji opisano sposób konfigurowania przeglądu, które można automatycznie zastosować.

### <a name="configuring-an-access-review-with-auto-apply"></a>Konfigurowanie przeglądu dostępu przy użyciu auto-apply

1.  Rozwiń menu dla ustawienia działań po zakończeniu, a następnie włącz automatycznie Zastosuj wyniki do zasobu. 

2.  W przypadkach, gdzie użytkownicy zostały nie przejrzane przez recenzenta przed upływem przeglądu, może mieć przeglądu dostępu przyjmą zalecenia systemu (jeśli jest włączona) odmawianie/zatwierdzenie przedłużenia dostępu użytkownika, pozostaw ich dostęp bez zmian lub usuń ich dostęp. To nie ma wpływu na użytkowników, którzy zostało przejrzane przez recenzenta ręcznie — jeśli decyzja końcowego recenzent jest odmowa, dostęp użytkownika zostanie usunięte.

3.  Aby włączyć możliwość rekomendacje recenzenci nie powinny odpowiadać, rozwiń węzeł Zaawansowane ustawienia i włączyć zalecenia dotyczące Show.
 
4.  Na koniec kliknij **Start**.

Na podstawie dokonanego wyboru w ustawienia działań po zakończeniu, automatycznie stosowanego będzie wykonywany po Data zakończenia przeglądu lub jeśli ręcznie zatrzymaj przeglądu. Stan przeglądu ulegnie zmianie z ukończono za pośrednictwem pośrednich stanów, takich jak stosowanie a na koniec stan zastosowano. Należy się spodziewać się zablokowani użytkownicy ewentualnej usuwany z przypisania grupy aplikacji lub członkostwa w ciągu kilku minut.


## <a name="manage-the-access-review"></a>Zarządzanie przeglądu dostępu

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów, wkrótce, po uruchomieniu przeglądu. Jeśli użytkownik chce nie ma wysyłać wiadomości e-mail z usługi Azure AD, pamiętaj poinformować osób dokonujących przeglądu, które przeglądu dostępu oczekuje na ich zakończenie. Można pokazać im instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników](perform-access-review.md). W przypadku zapoznania się z nimi dla gości, aby dokonać przeglądu własnego dostępu, wyświetlić je instrukcje dotyczące sposobu [przeglądu własnego dostępu](perform-access-review.md).

W przypadku niektórych recenzentów gości, gości są powiadamiane za pośrednictwem poczty e-mail tylko wtedy, gdy już zaakceptowane zaproszenia.

Aby zarządzać serię przeglądów dostępu, przejdź do przeglądu dostępu z **formantów**, będzie znajdowanie kolejnych wystąpień w przeglądach zaplanowane i Edytuj datę zakończenia lub Dodawanie/usuwanie recenzentów w związku z tym. 

Recenzenci wykonać ich recenzje na pulpicie nawigacyjnym usługi Azure AD w postęp można śledzić **przeglądów dostępu** sekcji. Nie prawa dostępu zostaną zmienione w katalogu, dopóki [zakończeniu przeglądu](complete-access-review.md).

## <a name="next-steps"></a>Kolejne kroki

Po rozpoczęciu przeglądu dostępu usługi Azure AD automatycznie wysyła recenzentów wiadomość e-mail z monitami, aby przeprowadzić przegląd dostępu. Jeśli użytkownik nie otrzymasz wiadomość e-mail, możesz wysłać im instrukcje dotyczące sposobu [Przegląd dostępu wszystkich użytkowników](perform-access-review.md). 

Jeśli jest to jednorazowa przeglądu, następnie po umieszczeniu okres przeglądu dostępu lub administrator zatrzymania przeglądu dostępu postępuj zgodnie z instrukcjami w [Kończenie przeglądu dostępu](complete-access-review.md) aby zobaczyć i zastosować wyniki.  

Jeśli jest to serii przeglądu, a następnie przejdź do **przeglądać historię** na stronie serii przeglądu dostępu, aby wybrać przeglądu dostępu ukończone.  Nadchodzące przeglądy zostaną wyświetlone w obszarze **zaplanowany Przegląd**, gdzie możesz można edytować czasu trwania i dodawać i usuwać osób dokonujących przeglądu dla poszczególnych przeglądów.
