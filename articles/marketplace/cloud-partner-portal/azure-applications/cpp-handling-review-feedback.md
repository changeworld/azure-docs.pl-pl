---
title: Obsługa opinii na temat przeglądu aplikacji platformy Azure | Portal Azure Marketplace
description: Wyjaśnia, jak używać usługi Azure DevOps do obsługi opinii na temat ofert aplikacji platformy Azure w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: ef4aff57948034fb369bd74564306b7b8674b377
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827582"
---
# <a name="handling-review-feedback"></a>Obsługa opinii dla przeglądu

W tym artykule wyjaśniono, jak uzyskać dostęp do środowiska Azure DevOps, które jest używane przez zespół przegląd Microsoft Azure Marketplace.  Jeśli w ramach kroku **przeglądu firmy Microsoft** zostaną znalezione problemy krytyczne, możesz zalogować się do tego systemu, aby wyświetlić szczegółowe informacje o tych problemach (Przejrzyj informacje zwrotne).  Po naprawieniu wszystkich tych problemów musisz ponownie przesłać swoją ofertę, aby nadal publikować ją w portalu Azure Marketplace.  Na poniższym diagramie przedstawiono sposób, w jaki proces opinii odnosi się do procesu publikowania.

![Publikowanie kroków za pomocą usługi Azure DevOps](./media/pub-flow-vsts-access.png)

Zazwyczaj do przeglądu problemów odwołuje się żądanie ściągnięcia.  Wszystkie żądania ściągnięcia są połączone z elementem online [Azure DevOps](https://azure.microsoft.com/services/devops/) (wcześniej o Visual Studio Team Services nazwie VSTS), który zawiera szczegółowe informacje o problemie.  Na poniższej ilustracji przedstawiono przykład odwołania przeglądu żądania ściągnięcia.  W przypadku złożonych sytuacji zespoły przeglądające i pomocy technicznej mogą również wysłać wiadomość e-mail. 

![Karta stanu wyświetlająca opinię na temat przeglądu](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Dostęp do usługi Azure DevOps

Aby wyświetlić elementy żądania ściągnięcia, do których odwołuje się informacja zwrotna, należy najpierw udzielić wydawcom odpowiedniej autoryzacji.  W przeciwnym razie nowi wydawcy otrzymają stronę odpowiedzi `401 - Not Authorized` podczas próby wyświetlenia żądań ściągnięcia.  Aby zażądać dostępu do tego repozytorium usługi Azure DevOps, wykonaj następujące czynności:

1. Zbierz następujące informacje:
    - Nazwa i identyfikator wydawcy
    - Typ oferty (aplikacja platformy Azure), nazwa oferty i identyfikator jednostki SKU
    - Link żądania ściągnięcia, na przykład: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` ten adres URL można pobrać z komunikatu powiadomienia lub z adresu strony odpowiedzi 401.
    - Adresy e-mail osób z organizacji publikowania, do których chcesz uzyskać dostęp.  Ta lista powinna zawierać adresy właścicieli podane podczas rejestrowania jako Wydawca na portal Cloud Partner.
2. Utwórz zdarzenie pomocy technicznej.  Na pasku tytułu portal Cloud Partner wybierz przycisk **Pomoc** , a następnie wybierz pozycję **Obsługa** z menu.  Należy uruchomić domyślną przeglądarkę sieci Web i przejść do strony nowe zdarzenie pomocy technicznej firmy Microsoft.  (Może być konieczne zalogowanie się jako pierwsze).
3. Określ **typ problemu** jako proces dołączania do **witryny Marketplace** oraz **kategorię** jako **problem z dostępem**, a następnie wybierz pozycję **Uruchom żądanie**.

    ![Kategoria biletu pomocy technicznej](./media/support-incident1.png)

4. Na stronie **krok 1 z 2** podaj informacje kontaktowe i wybierz pozycję **Kontynuuj**.
5. Na stronie **krok 2 z 2** określ tytuł zdarzenia (na przykład `Request Azure DevOps access`) i podaj informacje zebrane w pierwszym kroku (powyżej).  Przeczytaj i zaakceptuj umowę, a następnie wybierz pozycję **Prześlij**.

Jeśli utworzenie zdarzenia zakończyło się pomyślnie, zostanie wyświetlona strona potwierdzenia.  Zapisz na tej stronie informacje o potwierdzeniu dotyczące odwołania.  Zespół pomoc techniczna firmy Microsoft powinien odpowiedzieć na żądanie dostępu w ciągu kilku dni roboczych.


## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia 

Aby przejrzeć problemy udokumentowane w żądaniu ściągnięcia, należy wykonać poniższą procedurę.

1. W sekcji **Recenzja firmy Microsoft** w obszarze **Publikowanie kroków** kliknij link żądania ściągnięcia, aby uruchomić przeglądarkę, i przejdź do strony **Przegląd** (Strona główna) dla tego żądania ściągnięcia.  Na poniższej ilustracji przedstawiono przykładową stronę główną problemu krytycznego dla oferty przykładowej aplikacji contoso.  Ta strona zawiera przydatne informacje podsumowujące dotyczące problemów z przeglądem znalezionych w aplikacji platformy Azure.  

    [![stronę główną żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*
    
2. Obowiązkowe Po prawej stronie okna, w sekcji **zasady**, kliknij komunikat problemu (w tym przykładzie: **Sprawdzanie poprawności zasad nie powiodło**się), aby zbadać szczegóły tego problemu, w tym skojarzone pliki dziennika.  Błędy są zwykle wyświetlane w dolnej części plików dziennika.

3. W menu po lewej stronie strony głównej wybierz pozycję **pliki** , aby wyświetlić listę plików wchodzących w skład zasobów technicznych dla tej oferty.  Recenzenci firmy Microsoft powinni dodać komentarze opisujące wykryte problemy krytyczne.  W poniższym przykładzie wykryto dwa problemy. 

    [![stronę główną żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*

4. Kliknij każdy węzeł komentarza w drzewie po lewej stronie, aby przejść do komentarza w kontekście otaczającego kodu.  Popraw kod źródłowy w projekcie zespołu, aby rozwiązać problem opisany przez komentarz.

> [!Note]
> Nie możesz edytować zasobów technicznych swojej oferty w środowisku usługi Azure DevOps dla zespołu.  W przypadku wydawców jest to środowisko tylko do odczytu dla zawartego kodu źródłowego.  Można jednak zostawić odpowiedzi na komentarze, aby skorzystać z zespołu ds. przeglądu firmy Microsoft.

   W poniższym przykładzie Wydawca sprawdził, poprawił i odpowiedział na pierwszy problem.

   ![Pierwsza poprawka i odpowiedź komentarza](./media/first-comment-reply.png)


## <a name="next-steps"></a>Następne kroki

Po usunięciu krytycznych problemów udokumentowanych w przeglądzie żądania ściągnięcia należy [ponownie opublikować ofertę aplikacji platformy Azure](./cpp-publish-offer.md).
