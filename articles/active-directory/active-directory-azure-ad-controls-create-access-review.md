---
title: Utwórz Przegląd dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji z usługą Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Przegląd dostępu dla członków grupy lub użytkownicy z dostępem do aplikacji.
services: active-directory
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 05/14/2018
ms.author: rolyon
ms.openlocfilehash: 6a1d4a55074630cc20c98202d2e94ec682c323f4
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234077"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Tworzenie przeglądu dostępu do członków grupy lub dostęp do aplikacji z usługą Azure AD

Przydziały dostępu nieodświeżone "", gdy użytkownicy mają dostęp, których nie potrzebują więcej. W celu zmniejszenia ryzyka związanego z przypisania starych dostępu, Administratorzy mogą używać usługi Azure Active Directory (Azure AD) do utworzenia Przegląd dostępu dla członków grupy lub użytkowników przypisanych do aplikacji. Tworzenie cyklicznych przeglądami dostępu, można zaoszczędzić czas. Jeśli potrzebujesz Okresowo sprawdzaj użytkowników, którzy mają dostęp do aplikacji lub należą do grupy, można zdefiniować częstotliwość tych przeglądy. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [zarządzanie dostępem użytkowników](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) i [zarządzanie dostępem gościa](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Utwórz przegląd dostępu

1. Jako administrator globalny, przejdź do [dostępu monitoruje strony](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)i wybierz **programy**.

2. Wybierz program, który zawiera formant przeglądu dostępu, który chcesz utworzyć. **Domyślny Program** występuje zawsze, lub można utworzyć inny program. Na przykład można mieć jeden program dla każdego inicjatywy zgodności lub celach biznesowych.

3. W programie, wybierz **formanty**, a następnie wybierz **Dodaj** można dodać formantu.

4. Nazwa przeglądu dostępu. Opcjonalnie wprowadź przeglądu opis. Nazwa i opis są wyświetlane recenzentów.

5. Ustaw datę rozpoczęcia. Domyślnie Przegląd dostępu występuje raz, rozpoczyna się w tym samym czasie, w którym zostały utworzone, a kończy w ciągu miesiąca. Zmienisz rozpoczęcia i daty zakończenia dostępu Przejrzyj start w przyszłości i ostatnio jednak ma wiele dni.

6. Aby cykliczny dostęp do przeglądu, zmiana częstotliwości z jeden raz w celu co tydzień, co miesiąc, co kwartał lub co rok i użyj suwaka lub tekst pola, aby określić, ile dni każdego przeglądu serii cyklicznych będzie otwarte dla danych wejściowych recenzentów. Na przykład maksymalny czas trwania dla można ustawić dla miesięczne przeglądu jest 27 dni, aby uniknąć nakładania się recenzji. 

7.  Cykl przeglądu dostępu może być zakończona na 3 sposoby: działa w sposób ciągły zacząć przeglądami przez czas nieokreślony, aż do określonej daty lub po ukończeniu zdefiniowanej liczby wystąpień. Użytkownik lub administrator globalny innej można zatrzymać serii po utworzeniu, zmieniając datę w obszarze Ustawienia, aby go kończy się w tym dniu.

8. Przeglądy dostępu może być członkami grupy lub użytkowników, którzy są przypisani do aplikacji. Dostęp można dodatkowo zakresu przeglądu do przejrzenia tylko użytkownicy gościa który są elementami członkowskimi (lub przypisane do aplikacji), zamiast przegląd wszystkich użytkowników, którzy są członkami lub mają dostęp do aplikacji.

9. Wybierz co najmniej jeden osoby, aby przejrzeć wszystkich użytkowników w zakresie. Lub możesz wybrać członków, Przejrzyj swoje własne dostępu. Jeśli zasób jest grupą, możesz poprosić właścicieli grupy, aby przejrzeć. Możesz również wymagać czy recenzentów udzielają dostępu, podać przyczynę.

10. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu działania przeglądu, kliknij przycisk **Start**.  W przeciwnym razie następnej sekcji opisano sposób konfigurowania przeglądu można automatycznie zastosować.

### <a name="configuring-an-access-review-with-auto-apply"></a>Konfigurowanie Przegląd dostępu przy użyciu auto-apply

1.  Rozwiń menu dla po zakończeniu ustawienia i Włącz automatyczne stosowanie wyniki do zasobu. 

2.  W przypadkach, gdy użytkownicy zostały nie przejrzane przez rewidenta w okresie przeglądu, może mieć Przegląd dostępu przybrać zalecenie systemu (jeśli jest włączona) odmawianie/zatwierdzanie nieprzerwanego dostępu użytkownika, pozostaw ich dostęp bez zmian lub usuń ich dostęp. To nie ma wpływu użytkowników, którzy zostały sprawdzone recenzentów ręcznie — Jeśli weryfikacja końcowego decyzji jest Odmów, dostęp użytkownika zostaną usunięte.

3.  Aby włączyć opcję podjęcie zalecenia dotyczące osób dokonujących przeglądu nie powinny odpowiadać, rozwiń Zaawansowane ustawienia i Włącz Pokaż zalecenia.
 
4.  Na koniec kliknij **Start**.

Na podstawie wybranych opcji w po zakończeniu ustawień, automatycznie Zastosuj będzie wykonywany po Data zakończenia przeglądu lub gdy ręcznie zatrzymaj przeglądu. Stan przeglądu ulegnie zmianie z ukończono trybami pośrednie, takie jak stosowanie i w końcu do stanu zastosowano. Można oczekiwać odmowy użytkowników, jeśli jest usuwany z przypisania grupy aplikacji lub członkostwa w ciągu kilku minut.


## <a name="manage-the-access-review"></a>Zarządzanie Przegląd dostępu

Domyślnie usługi Azure AD wysyła wiadomość e-mail do osób dokonujących przeglądu, wkrótce po uruchomieniu przeglądu. Jeśli wybierzesz nie usługi Azure AD, Wyślij wiadomość e-mail, należy poinformować recenzentów, które Przegląd dostępu oczekuje na ich zakończenie. Można je wyświetlić instrukcje dotyczące sposobu [sprawdzaj dostęp](active-directory-azure-ad-controls-perform-access-review.md). Jeśli dla gości przejrzeć ich dostęp do zapoznania się z nimi, je wyświetlić instrukcje dotyczące sposobu [Przejrzyj własne dostępu](active-directory-azure-ad-controls-perform-access-review.md).

Jeśli niektórzy z recenzentów gości, gości są powiadamiane za pośrednictwem poczty e-mail tylko wtedy, gdy już zaakceptowane ich zaproszenia.

Aby zarządzać szereg przeglądami dostęp, przejdź do Przegląd dostępu z **formanty**, i będą znaleźć nadchodzących wystąpienia w recenzji zaplanowane i zmodyfikuj datę zakończenia lub Dodawanie/usuwanie recenzentów w związku z tym. 

Postęp można śledzić w recenzentów zakończenia ich przeglądami na pulpicie nawigacyjnym usługi Azure AD w **przegląda dostępu** sekcji. Nie prawa dostępu są zmieniane w katalogu do [zakończeniu przeglądu](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Kolejne kroki

Gdy Przegląd dostępu została uruchomiona, usługi Azure AD automatycznie wysyła recenzentów wiadomości e-mail, która żąda sprawdzaj dostęp. Jeśli użytkownik nie otrzymasz wiadomość e-mail, możesz wysłać je z instrukcjami dotyczącymi sposobu [sprawdzaj dostęp](active-directory-azure-ad-controls-perform-access-review.md). 

Jeśli jest to jednorazowa przeglądu, następnie po okresu przeglądu dostępu lub administrator zatrzymuje Przegląd dostępu, postępuj zgodnie z instrukcjami [zakończenia przeglądu dostępu](active-directory-azure-ad-controls-complete-access-review.md) aby zobaczyć i zastosować wyniki.  

Jeśli jest to serii przeglądu, następnie przejdź do **Przejrzyj historię** na stronie serii dostępu do przeglądu wybierz przeglądu ukończone dostępu.  Przeglądy nadchodzących zostaną wyświetlone w obszarze **zaplanowane przeglądu**, gdzie można edytować wartość czasu i dodaniu lub usunięciu recenzentów recenzji poszczególnych.
