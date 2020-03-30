---
title: Obsługa opinii o recenzjach aplikacji platformy Azure | Azure Marketplace
description: W tym artykule wyjaśniono, jak używać usługi Azure DevOps do obsługi opinii recenzji dotyczących ofert aplikacji platformy Azure dla portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285303"
---
# <a name="handling-review-feedback"></a>Obsługa opinii dla przeglądu

W tym artykule wyjaśniono, jak uzyskać dostęp do środowiska Azure DevOps używanego przez zespół przeglądu microsoft azure marketplace.  Jeśli w ofercie aplikacji platformy Azure zostaną znalezione krytyczne problemy podczas kroku **przeglądu firmy Microsoft,** można zalogować się do tego systemu, aby wyświetlić szczegółowe informacje o tych problemach (opinie dotyczące opinii).  Po rozwiązaniu wszystkich tych problemów należy ponownie przesłać ofertę, aby kontynuować publikowanie jej w portalu Azure Marketplace.  Na poniższym diagramie przedstawiono, jak ten proces sprzężenia zwrotnego odnosi się do procesu publikowania.

![Kroki publikowania za pomocą opinii o platformie Azure DevOps](./media/pub-flow-vsts-access.png)

Zazwyczaj problemy z przeglądem są odwoływane jako żądanie ściągnięcia (PR).  Każdy element pr jest połączony z elementem [online Azure DevOps](https://azure.microsoft.com/services/devops/) (wcześniej nazwanym Program visual studio team services (VSTS)), który zawiera szczegółowe informacje o problemie.  Na poniższej ilustracji przedstawiono przykład odwołania do przeglądania PR.  W złożonych sytuacjach zespoły recenzentów i pomocy technicznej mogą również wysyłać do Ciebie wiadomości e-mail. 

![Karta Stan z opiniami na temat recenzji](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Dostęp do usługi Azure DevOps

Aby wyświetlić elementy PR, do których odwołuje się opinia recenzji, wydawcy muszą najpierw uzyskać odpowiednie uprawnienia.  W przeciwnym razie nowi wydawcy otrzymują stronę `401 - Not Authorized` odpowiedzi podczas próby wyświetlenia śR.  Aby zażądać dostępu do tego repozytorium devops platformy Azure, wykonaj następujące kroki:

1. Zbierz wymienione poniżej informacje.
    - Twoja nazwa wydawcy i identyfikator
    - Typ oferty (aplikacja Platformy Azure), nazwa oferty i identyfikator jednostki SKU
    - Link żądania ściągnięcia, `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` na przykład: ten adres URL można pobrać z wiadomości powiadomienia lub adresu strony odpowiedzi 401.
    - Adres(y) e-mail osób z organizacji wydawniczej, do których chcesz uzyskać dostęp.  Ta lista powinna zawierać adresy(y) właściciela podane podczas rejestracji jako wydawca w portalu Cloud Partner Portal.
2. Utwórz zdarzenie pomocy technicznej.  Na pasku tytułu portalu cloud partnerów wybierz przycisk **Pomoc,** a następnie wybierz z menu **pozycję Pomoc.**  Domyślna przeglądarka sieci Web powinna uruchomić i przejść do nowej strony zdarzenia pomocy technicznej firmy Microsoft.  (Może być trzeba zalogować się jako pierwszy.)
3. Określ **typ problemu** jako **dołączanie do portalu marketplace,** a **kategorię** jako **problem z dostępem,** a następnie wybierz pozycję **Rozpocznij żądanie**.

    ![Kategoria biletu pomocy technicznej](./media/support-incident1.png)

4. W **kroku 1 strony 2** podaj swoje dane kontaktowe i wybierz **pozycję Kontynuuj**.
5. W **kroku 2 strony 2** określ tytuł `Request Azure DevOps access`zdarzenia (na przykład) i podaj informacje zebrane w pierwszym kroku (powyżej).  Przeczytaj i zaakceptuj umowę, a następnie wybierz pozycję **Prześlij**.

Jeśli utworzenie zdarzenia zakończyło się pomyślnie, zostanie wyświetlona strona potwierdzenia.  Zapisz informacje o potwierdzeniu na tej stronie w celach informacyjnych.  Zespół pomocy technicznej firmy Microsoft powinien odpowiedzieć na żądanie dostępu w ciągu kilku dni roboczych.


## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia 

Poniższa procedura służy do przeglądania problemów udokumentowanych w żądaniu ściągnięcia.

1. W sekcji **Microsoft review** w **formularzu Kroki publikowania** kliknij łącze PR, aby uruchomić przeglądarkę i przejdź do strony **Przegląd** (strona główna) dla tego pr.  Na poniższej ilustracji przedstawiono przykładową stronę główną problemu krytycznego dla przykładowej oferty aplikacji Contoso.  Ta strona zawiera przydatne informacje podsumowujące dotyczące problemów z przeglądem znalezionych w aplikacji platformy Azure.  

    [![Strona główna żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij na obrazek, aby rozwinąć.*
    
2. (Opcjonalnie) Po prawej stronie okna w sekcji **Zasady**kliknij komunikat o problemie (w tym przykładzie: **Sprawdzanie poprawności zasad nie powiodło się),** aby zbadać szczegóły niskiego poziomu problemu, w tym skojarzone pliki dziennika.  Błędy są zazwyczaj wyświetlane w dolnej części plików dziennika.

3. W menu po lewej stronie strony głównej wybierz pozycję **Pliki,** aby wyświetlić pliki listy, które składają się na zasoby techniczne tej oferty.  Recenzenci firmy Microsoft powinni dodać komentarze opisujące wykryte krytyczne problemy.  W poniższym przykładzie zostały wykryte dwa problemy. 

    [![Strona główna żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij na obrazek, aby rozwinąć.*

4. Kliknij na każdy węzeł komentarza w lewym drzewie, aby przejść do komentarza w kontekście otaczającego kodu.  Napraw kod źródłowy w projekcie zespołu, aby rozwiązać problem opisany przez komentarz.

> [!Note]
> Nie można edytować zasobów technicznych oferty w środowisku Azure DevOps zespołu recenzentów.  Dla wydawców jest to środowisko tylko do odczytu dla zawartego kodu źródłowego.  Można jednak pozostawić odpowiedzi na komentarze na korzyść zespołu recenzentów firmy Microsoft.

   W poniższym przykładzie wydawca przejrzał, poprawił i odpowiedział na pierwszy problem.

   ![Pierwsza poprawka i odpowiedź na komentarz](./media/first-comment-reply.png)


## <a name="next-steps"></a>Następne kroki

Po skorygowaniu krytycznych problemów udokumentowanych w pr(ach recenzji) należy [ponownie opublikować ofertę aplikacji platformy Azure](./cpp-publish-offer.md).
