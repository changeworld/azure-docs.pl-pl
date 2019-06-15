---
title: Obsługa aplikacji platformy Azure opinii | Portal Azure Marketplace
description: Wyjaśnia, jak używać DevOps platformy Azure do obsługi opinii dla aplikacji platformy Azure, ofert w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: pabutler
ms.openlocfilehash: 1a45af2cb5eed8daa4b50bb6f0b504f9653c827a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068951"
---
# <a name="handling-review-feedback"></a>Obsługa opinii dla przeglądu

W tym artykule wyjaśniono, jak uzyskiwać dostęp do środowiska DevOps platformy Azure używane przez zespół usługi Microsoft Azure Marketplace przeglądu.  Jeśli krytycznych problemów znajdują się w ofercie usługi Azure application podczas **przeglądu Microsoft** krok, użytkownik może zalogować się do tego systemu Aby wyświetlić szczegółowe informacje na temat tych problemów (Przejrzyj opinię).  Po rozwiązaniu tych problemów muszą ponownie przesłać oferty w taki sposób, aby kontynuować, opublikuj go w portalu Azure Marketplace.  Na poniższym diagramie przedstawiono, jak ten proces opinii odnosi się do publikowania.

![Kroki publikowania opinii DevOps platformy Azure](./media/pub-flow-vsts-access.png)

Zwykle Przejrzyj problemy są przywoływane jako żądania ściągnięcia (PR).  Każdego żądania Ściągnięcia jest połączony z online [DevOps platformy Azure](https://azure.microsoft.com/services/devops/) (poprzednia nazwa programu Visual Studio Team Services (VSTS)) elementu, który zawiera szczegółowe informacje o problemie.  Poniższa ilustracja przedstawia przykład odwołania do przeglądu żądania Ściągnięcia.  Dla złożonych sytuacjach zespoły przeglądu i pomocy technicznej może również wiadomość e-mail. 

![Stan karty zawierające opinii](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Dostęp roli DevOps platformy Azure

Aby wyświetlić elementy żądania Ściągnięcia, do którego odwołuje się opinii, wydawcy muszą najpierw zostać przyznane odpowiednią autoryzacją.  W przeciwnym razie odbieranie nowych wydawców `401 - Not Authorized` strony odpowiedzi podczas próby wyświetlenia żądania ściągnięcia.  Aby zażądać dostępu do tego repozytorium DevOps platformy Azure, wykonaj następujące czynności:

1. Zbierz następujące informacje:
    - Twoja nazwa wydawcy i Identyfikatora
    - Oferty (aplikacje platformy Azure), oferują nazwy i Identyfikatora jednostki SKU
    - Żądanie ściągnięcia link na przykład: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`  Ten adres URL można pobrać z komunikatu powiadomienia lub adres strony odpowiedzi 401.
    - Adresy e-mail osób z organizacji publikowania, który chcesz udzielić dostępu do.  Ta lista powinna zawierać adresy właściciela, podanych podczas rejestracji jako wydawcę w portalu Cloud Partner.
2. Utwórz żądanie pomocy technicznej.  Na pasku tytułu w portalu Cloud Partner wybierz **pomocy** przycisk, a następnie wybierz **pomocy technicznej** z menu.  Domyślne internetowej przeglądarki należy uruchomić i przejdź do firmy Microsoft nowe strony pomocy technicznej zdarzenia.  (Może mieć się się zalogować).
3. Określ **typ problemu** jako **przechodzenia do portalu marketplace** i **kategorii** jako **problem z dostępem**, a następnie wybierz **Start żądanie**.

    ![Kategoria bilet pomocy technicznej](./media/support-incident1.png)

4. W **krok 1 z 2** strony, podaj swoje informacje kontaktowe i wybierz **Kontynuuj**.
5. W **krok 2 z 2** Określ tytuł zdarzenia (na przykład `Request Azure DevOps access`) i podaj informacje zebrane w pierwszym kroku (powyżej).  Przeczytaj i zaakceptuj umowę, a następnie wybierz **przesyłania**.

Jeśli tworzenie zdarzenia zakończyło się pomyślnie, zostanie wyświetlona strona potwierdzenia.  Na tej stronie, na której można się odwołać, należy zapisać informacji o potwierdzenie.  Zespół pomocy technicznej firmy Microsoft należy odpowiedzieć na Twoje żądanie dostępu w ciągu kilku dni roboczych.


## <a name="reviewing-the-pull-request"></a>Przeglądanie żądania ściągnięcia 

Użyj poniższej procedury, przejrzeć zagadnienia, opisane w żądaniu ściągnięcia.

1. W **przeglądu Microsoft** części **publikowania kroki** formularza, kliknij Link żądania Ściągnięcia, aby uruchomić przeglądarkę i przejdź do **Przegląd** strony (macierzysty) dla tego żądania ściągnięcia.  Poniższa ilustracja przedstawia przykładową stronę główną krytyczny problem dla oferty aplikacji przykładowej firmy Contoso.  Ta strona zawiera przydatne podsumowanie informacji o problemach przeglądu w aplikacji platformy Azure.  

    [![Strona główna żądania ściągnięcia](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*
    
2. (Opcjonalnie) Po prawej stronie okna, w sekcji **zasady**, kliknąć komunikat wydania (w tym przykładzie: **Nie można zweryfikować zasad**) aby zbadać szczegóły niskiego poziomu wydania, w tym skojarzonych plików dziennika.  Błędy zwykle są wyświetlane w dolnej części plików dziennika.

3. W menu po stronie lewej części strony głównej wybierz **pliki** do wyświetlania listy plików, wchodzące w skład zasoby techniczne dla tej oferty.  Recenzenci firmy Microsoft, należy dodać komentarz opisujący wykryte problemy krytyczne.  W poniższym przykładzie dwa problemy zostały odnalezione. 

    [![Strona główna żądania ściągnięcia](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Kliknij obraz, aby rozwinąć.*

4. Kliknij pozycję w każdym węźle komentarza, w drzewie po lewej stronie, aby przejść do komentarzy w kontekście otaczającym kodem.  Napraw kod źródłowy w projekcie zespołu, aby rozwiązać ten problem opisany przez komentarz.

> [!Note]
> Nie można edytować zasoby techniczne swoją ofertę w środowisku DevOps platformy Azure zespołu przeglądu.  Dla wydawców jest to środowisko tylko do odczytu dla kodu źródłowego zawarte.  Można jednak pozostawić odpowiadanie na komentarze na rzecz przeglądu zespołu firmy Microsoft.

   W poniższym przykładzie wydawcy ma przeglądowi poprawione i odpowiedź do pierwszego wydania.

   ![Najpierw usuń i komentarz odpowiedzi](./media/first-comment-reply.png)


## <a name="next-steps"></a>Kolejne kroki

Po naprawieniu krytycznych problemów, które opisano w PR(s) przeglądu musi [raz opublikuj swoją ofertę platformy Azure aplikacja](./cpp-publish-offer.md).
