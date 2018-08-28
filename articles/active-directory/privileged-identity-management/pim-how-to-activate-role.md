---
title: Jak aktywować lub dezaktywować rolę | Dokumentacja firmy Microsoft
description: Dowiedz się, jak aktywować ról uprzywilejowanych tożsamości za pomocą aplikacji Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/21/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 54e092cb98df6c092e6cf3ed8c365f966587b0db
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091605"
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Jak aktywować lub dezaktywować ról w usłudze Azure AD Privileged Identity Management
Usługa Azure Active Directory (AD) Privileged Identity Management upraszcza sposób przedsiębiorstw Zarządzanie uprzywilejowanego dostępu do zasobów w usłudze Azure AD i innych usług online firmy Microsoft, takich jak usługi Office 365 lub Microsoft Intune.  

Jeśli użytkownik wprowadzono kwalifikuje się do roli administracyjnej, oznacza to, że możesz aktywować tej roli, gdy potrzebne do wykonania uprzywilejowanych akcji. Na przykład jeśli zarządzasz od czasu do czasu funkcje usługi Office 365, Administratorzy ról uprzywilejowanych w organizacji może nie mieć możesz stałe Administrator globalny, ponieważ ta rola ma wpływ na inne usługi, za. Zamiast tego są każą kwalifikuje się do ról usługi Azure AD, takich jak Exchange Online Administrator. Możesz poprosić o aktywowanie tej roli, gdy potrzebujesz jego uprawnienia, a następnie będziesz mieć kontrolę administracyjną w okresie wyznaczonym czasie.

Ten artykuł jest przeznaczony dla administratorów, którzy muszą aktywować swoją rolę w usłudze Azure AD Privileged Identity Management (PIM). Przeprowadza użytkownika przez kroki, aby aktywować rolę, gdy potrzebne uprawnienia i dezaktywacja roli, gdy wszystko będzie gotowe. Ponadto administratorzy ról uprzywilejowanych może wymagać zatwierdzenia do aktywowania roli (wersja zapoznawcza). Dowiedz się więcej o [przepływy pracy zatwierdzania usługi PIM](./azure-ad-pim-approval-workflow.md) tutaj.

## <a name="add-the-privileged-identity-management-application"></a>Dodawanie aplikacji Privileged Identity Management
Za pomocą aplikacji Azure AD Privileged Identity Management w [witryny Azure portal](https://portal.azure.com/) żądania uaktywnienia roli, nawet wtedy, gdy użytkownik chce działać w innym portalu lub programu PowerShell. Jeśli nie masz aplikacji usługi Azure AD Privileged Identity Management w swojej witrynie Azure portal, wykonaj następujące kroki, aby rozpocząć pracę.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz swoją nazwę użytkownika w prawym górnym rogu witryny Azure portal i wybierz katalog, w której można będzie działać.

1. Wybierz pozycję **Wszystkie usługi** i użyj pola tekstowego filtru, aby wyszukać usługę **Azure AD Privileged Identity Management**.

1. Zaznacz opcję **Przypnij do pulpitu nawigacyjnego**, a następnie kliknij pozycję **Utwórz**. Nastąpi otwarcie aplikacji Privileged Identity Management.

## <a name="activate-a-role"></a>Uaktywnij rolę
Gdy należy wykonać w roli, możesz poprosić aktywacji, wybierając **Moje role** opcji nawigacji w aplikacji Azure AD Privileged Identity Management w lewej kolumnie nawigacji.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz Kafelek usługi Azure AD Privileged Identity Management.

1. Wybierz **Moje role**. Lista przypisane role uprawnione są wyświetlane w grupowaniu w górnej części strony.

1. Wybierz rolę, aby aktywować.

1. Wybierz **aktywować**. **Żądanie uaktywnienia roli** zostanie wyświetlony blok.

1. Niektóre role wymagają uwierzytelniania Multi-Factor Authentication (MFA), zanim będzie można aktywować rolę. Musisz uwierzytelnić się jeden raz na sesję.

    ![Przed aktywację roli — zrzut ekranu sprawdzić za pomocą usługi MFA](./media/pim-how-to-activate-role/PIM_activation_MFA.png)

1. Podaj przyczynę żądania aktywacji w polu tekstowym.  Niektóre role wymagają podania numeru biletu problemy.

1. Kliknij przycisk **OK**.  Jeśli rola wymaga zatwierdzenia, jest już aktywna, a rola jest wyświetlana na liście aktywnych ról (bezpośrednio poniżej listę kwalifikujących się przypisań ról). Jeśli [rola wymaga zatwierdzenia](./azure-ad-pim-approval-workflow.md) można aktywować, wyskakujące powiadomienie krótko pojawi się w prawym górnym rogu przeglądarki, informujące o tym, żądanie oczekuje na zatwierdzenie.

    ![Żądania oczekujące powiadomienia — zrzut ekranu](./media/pim-how-to-activate-role/PIM_Request_Pending_Toast2.png)

## <a name="use-a-role-immediately-after-activation"></a>Użyj roli od razu po aktywacji

Z powodu buforowania, aktywacji nie występują bezpośrednio w witrynie Azure portal, bez jego odświeżania. Jeśli musisz ograniczyć możliwość opóźnienia po aktywowaniu rolę, możesz użyć **dostęp do aplikacji** strony w portalu. Aplikacje dostępne na tej stronie sprawdź, czy nowego przypisania roli natychmiast.

1. Otwórz program Azure AD Privileged Identity Management.

1. Kliknij przycisk **dostęp do aplikacji** strony.

    ![Dostęp do aplikacji PIM — zrzut ekranu](./media/pim-how-to-activate-role/pim-application-access.png)

1. Kliknij przycisk **usługi Azure Active Directory** ponownie otworzyć portalu na **wszyscy użytkownicy** strony.

    Po kliknięciu tego łącza, wymusić odświeżenie wyboru dla nowej usługi Azure AD przypisań ról.

## <a name="deactivate-a-role"></a>Dezaktywacja roli
Po uaktywnieniu roli automatycznie dezaktywuje to, gdy zostanie osiągnięty limit czasu (czas trwania zakwalifikowania).

Jeśli wykonasz wcześnie zadań administracyjnych, również można dezaktywować rolę ręcznie w aplikacji Azure AD Privileged Identity Management.  Wybierz **Moje role**, wybierz rolę, wszystko będzie gotowe przy użyciu z **aktywne przypisania ról** grupowania i wybierz pozycję **Dezaktywuj**.  

## <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania
W przypadku, gdy nie jest wymagane uaktywnienie roli, która wymaga zatwierdzenia, możliwe jest anulowanie oczekującego żądania w dowolnym momencie. Po prostu zaznacz **Moje role** opcji nawigacji w aplikacji Azure AD Privileged Identity Management w lewej kolumnie nawigacji.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) i wybierz Kafelek usługi Azure AD Privileged Identity Management.

1. Wybierz **Moje role**. Lista przypisane role uprawnione są wyświetlane w grupowaniu w górnej części strony.

1. Wybierz rolę.

1. Wybierz **aktywacji oczekuje na zatwierdzenie** transparent w bloku szczegółów aktywacji roli.

1. Wybierz **anulować** w górnej części **oczekujące na zatwierdzenie** bloku.

   ![Anulowanie oczekującego żądania zrzut ekranu](./media/pim-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png)

## <a name="next-steps"></a>Kolejne kroki
Jeśli chcesz dowiedzieć się więcej o usłudze Azure AD Privileged Identity Management, poniższe łącza zostały więcej informacji.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
