---
title: Tabela platformy Azure | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami dla tabeli platformy Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288839"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurowanie zarządzania potencjalnymi klientami przy użyciu tabeli platformy Azure

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w Centrum partnerów do odbierania potencjalnych klientów w portalu Azure Marketplace i AppSource, można użyć tabeli platformy Azure do obsługi tych potencjalnych klientów. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule pokonują proces tworzenia konta usługi Azure Storage i tabeli platformy Azure w ramach tego konta. Ponadto można utworzyć nowy przepływ za pomocą usługi Microsoft Flow, aby wysłać powiadomienie e-mail, gdy oferta otrzyma potencjalnego klienta.

## <a name="configure-azure-table"></a>Konfigurowanie tabeli platformy Azure

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).
2. Po aktywnym koncie platformy Azure zaloguj się do [witryny Azure portal](https://portal.azure.com).
3. W witrynie Azure portal utwórz konto magazynu przy użyciu poniższej procedury.  
    1. Wybierz **pozycję +Utwórz zasób** na lewym pasku menu.  **Okienko Nowy** (ostrze) zostanie wyświetlone po prawej stronie.
    2. Wybierz **pozycję Magazyn** w okienku **Nowy.**  Po **Featured** prawej stronie zostanie wyświetlona lista Polecane.
    3. Wybierz **konto magazynu,** aby rozpocząć tworzenie konta.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Kroki tworzenia konta magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Aby uzyskać więcej informacji na temat kont magazynu, wybierz [samouczek Szybki start](https://docs.microsoft.com/azure/storage/).  Aby uzyskać więcej informacji na temat cen magazynowania, zobacz [ceny magazynowania](https://azure.microsoft.com/pricing/details/storage/).

4. Poczekaj, aż konto magazynu zostanie aprowizny, proces, który zwykle trwa kilka minut.  Następnie przejdź do konta magazynu ze strony **głównej** witryny Azure portal, wybierając **pozycję Zobacz wszystkie zasoby** lub wybierając wszystkie **zasoby** z lewego paska menu nawigacji w witrynie Azure portal.

    ![Uzyskiwanie dostępu do konta magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. W okienku konta magazynu wybierz pozycję **Klawisze programu Access** i skopiuj wartość *ciągu połączenia* dla klucza. Zapisz tę wartość, ponieważ jest to wartość *parametry połączenia konta magazynu,* którą należy podać w portalu publikowania, aby otrzymywać potencjalnych klientów dla oferty marketplace. 

    Przykładem użądlenia połączenia jest:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Klucz magazynu platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. W okienku konta magazynu wybierz pozycję **Tabele** i wybierz **pozycję +Tabela,** aby utworzyć tabelę. Wprowadź nazwę tabeli i wybierz **przycisk OK**. Zapisz tę wartość, ponieważ będzie ona potrzebna, jeśli chcesz skonfigurować MS Flow do odbierania powiadomień e-mail po odebraniu potencjalnych klientów.

    ![Tabele platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Explorer [magazynu platformy Azure](https://archive.codeplex.com/?p=azurestorageexplorer) lub inne narzędzie, aby wyświetlić dane w tabeli magazynu. Można również wyeksportować dane w tabeli platformy Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>(Opcjonalnie) Korzystanie z usługi Microsoft Flow z tabelą platformy Azure  

Za pomocą usługi [Microsoft Flow](https://docs.microsoft.com/flow/) można zautomatyzować powiadomienia za każdym razem, gdy potencjalny klient jest dodawany do tabeli platformy Azure. Jeśli nie masz konta, możesz [założyć bezpłatne konto.](https://flow.microsoft.com/)

### <a name="lead-notification-example"></a>Przykład powiadomienia potencjalnego klienta

Użyj tego przykładu jako przewodnika, aby utworzyć prosty przepływ, który automatycznie wysyła powiadomienie e-mail po dodaniu nowego potencjalnego klienta do tabeli platformy Azure. W tym przykładzie konfiguruje cykl wysyłania informacji o potencjalnych klientach co godzinę, jeśli magazyn tabel jest aktualizowany.

1. Zaloguj się na swoje konto Usługi Microsoft Flow.
2. Na lewym pasku nawigacyjnym wybierz pozycję **Moje przepływy**.
3. Na górnym pasku nawigacyjnym wybierz pozycję **+ Nowy**.  
4. Na liście rozwijanej wybierz **+ Zaplanowane — z pustego**

   ![Moje przepływy **+ Zaplanowane - z pustego**](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  W oknie *Kompilacja zaplanowanego przepływu* w obszarze *Powtórz co* wybierz "1" dla interwału i "godzina" dla częstotliwości. Ponadto nadaj przepływowi nazwę, jeśli chcesz. Wybierz **pozycję Utwórz**.

    >[!Note]
    >Chociaż w tym przykładzie używa interwału 1 godziny, można wybrać interwał i częstotliwość, która jest najlepsza dla potrzeb biznesowych.

    ![Tworzenie zaplanowanego przepływu.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Wybierz pozycję **+ Nowy krok**.
7. W oknie *Wybierz wyszukiwanie akcji* dla "pomiń czas", a następnie wybierz pozycję **Przejmij czas przeszły** w obszarze Akcje.

   ![Wybierz akcję.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. W oknie **Czas przemiń** ustaw **interwał** na 1. Z listy rozwijanej **Jednostka czasu** wybierz pozycję **Godzina**.

    >[!Important]
    >Upewnij się, że ta jednostka interwału i czasu jest zgodna z interwałem i częstotliwością skonfigurowaną dla cyklu w kroku 5.

    ![Ustawianie odstępu czasu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>Przepływ można sprawdzić w dowolnym momencie, aby sprawdzić, czy każdy krok jest poprawnie skonfigurowany. Aby sprawdzić przepływ, wybierz **opcję Kontroler przepływu** na pasku menu Przepływ.

W następnym zestawie kroków połączysz się z tabelą platformy Azure i skonfigurujesz logikę przetwarzania do obsługi nowych potencjalnych klientów.

9. Po kroku "Przejdź za przemijanie czasu" wybierz pozycję **+ Nowy krok**, a następnie wyszukaj hasło "Pobierz encje" w oknie Wybierz *akcję.*
10. W obszarze **Akcje**wybierz pozycję **Pobierz jednostki (Usługa Azure Table Storage).**
11. W oknie **Usługi Azure Table Storage** podaj informacje dotyczące następujących pól i wybierz pozycję **Utwórz:**

    * *Nazwa połączenia* — podaj znaczącą nazwę połączenia, które ustanawiasz między tym przepływem a tabelą platformy Azure.
    * *Nazwa konta magazynu* — podaj nazwę konta magazynu dla tabeli platformy Azure. Można to znaleźć na stronie **klucze dostępu** konta magazynu.
    * *Udostępniony klucz magazynu* — podaj wartość klucza dla konta sklepu dla tabeli platformy Azure. Można to znaleźć na stronie **klucze dostępu** konta magazynu.

        ![Magazyn tabel platformy Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Po kliknięciu przycisku Utwórz zostanie wyświetlene okno *Pobierz jednostki.* W tym miejscu wybierz pozycję **Pokaż opcje zaawansowane** i podaj informacje dotyczące następujących pól:

       * *Tabela* — wybierz nazwę usługi Azure Table Storage (z kroku 6 instrukcji dotyczących konfigurowania tabeli platformy Azure). Następne przechwytywanie ekranu pokazuje monit, gdy w tym przykładzie wybrano tabelę "marketplaceleads".

            ![Azure Table pobierz jednostki.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Kwerenda filtrująca* — zaznacz to pole i wklej tę funkcję w polu:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure Table get jednostek — filtr Querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Po zakończeniu konfigurowania połączenia z tabelą platformy Azure wybierz pozycję **Nowy krok,** aby dodać warunek do skanowania tabeli platformy Azure w poszukiwaniu nowych potencjalnych klientów. 

13. W oknie **Wybieranie akcji** wybierz pozycję **Akcje**, a następnie wybierz **kontrolka Warunek**.

    ![Tabela platformy Azure — wybierz akcję.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. W oknie **Warunek** wybierz pole **Wybierz wartość,** a następnie wybierz **pozycję Wyrażenie** w oknie podręcznym.

15. Wklej `length(body('Get_entities')?['value'])` do pola ***fx.*** Wybierz **przycisk OK,** aby dodać tę funkcję. 

16. Aby zakończyć konfigurowanie warunku:
    1. Wybierz "jest większa niż" z listy rozwijanej.
    2. Wprowadź 0 jako wartość

        ![Tabela azure — warunek.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

W następnych kilku krokach skonfigurujesz akcję do podjęcia na podstawie wyniku warunku.

* Jeśli warunek jest rozpoznawany jako **Jeśli nie,** nie rób nic.
* Jeśli warunek zostanie rozpoznany na **if yes**, wyzwolić akcję łączącą konto usługi Office 365 w celu wysłania wiadomości e-mail. 

17. Wybierz **pozycję Dodaj akcję** w obszarze Jeśli **tak**.

    ![Tabela platformy Azure — warunek **Jeśli tak**.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Wybierz **pozycję Wyślij wiadomość e-mail (Office 365 Outlook)**.

    ![Tabela platformy Azure — warunek **Jeśli tak**, wyślij wiadomość e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Jeśli chcesz użyć innego dostawcy poczty e-mail, wyszukaj i wybierz opcję Wyślij powiadomienie e-mail (Mail) jako akcję. Instrukcje pokażą, jak skonfigurować przy użyciu programu Office 365 Outlook, ale instrukcje są podobne dla innego dostawcy poczty e-mail.

19. W oknie **programu Outlook usługi Office 365** podaj informacje dotyczące następujących pól:

    1. **Do** - Wprowadź adres e-mail dla wszystkich, którzy otrzymają to powiadomienie.
    1. **Temat** — podaj temat wiadomości e-mail. Na przykład: Nowi potencjalni klienci!
    1. **Treść** — dodaj tekst, który chcesz uwzględnić w każdej wiadomości `body('Get_entities')?['value']`e-mail (opcjonalnie), a następnie wklej w treści .

    >[!Note]
    >Do treści tej wiadomości e-mail można wstawić dodatkowe statyczne lub dynamiczne punkty danych.

    ![Tabela platformy Azure — warunek **Jeśli tak**, okno programu Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Aby zapisać zmiany, wybierz opcję **Zapisz**. Usługa Microsoft Flow automatycznie przetestuje przepływ pod kątem błędów. Jeśli nie ma żadnych błędów, przepływ zaczyna działać po jego zapisaniu.

Następne przechwytywanie ekranu pokazuje przykład, jak powinien wyglądać przepływ końcowy.

![Przykład przepływu końcowego.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Zarządzanie przepływem

Zarządzanie przepływem po jego uruchomieniu jest łatwe. Masz pełną kontrolę nad przepływem. Na przykład możesz go zatrzymać, edytować, wyświetlić historię uruchamiania i uzyskać analizę. Następne przechwytywanie ekranu pokazuje opcje, które są dostępne do zarządzania przepływem. 

 ![Zarządzanie przepływem](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Przepływ działa do momentu jego zatrzymania za pomocą opcji **Wyłącz przepływ.**

Jeśli nie otrzymujesz żadnych powiadomień e-mail potencjalnych klientów, oznacza to, że nowi potencjalni klienci nie zostali dodana do tabeli platformy Azure. Jeśli wystąpią jakiekolwiek błędy przepływu, otrzymasz wiadomość e-mail, taką jak przykład w następnym przechwytywaniu ekranu.

 ![Powiadomienie e-mail o awarii przepływu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure

Gdy będziesz gotowy skonfigurować informacje o zarządzaniu potencjalnymi klientami dla oferty w portalu publikowania, wykonaj poniższe czynności:

1. Przejdź do strony **Ustawienia oferty** dla swojej oferty.
2. Wybierz **pozycję Połącz** w sekcji Zarządzanie potencjalnymi klientami.
3. W oknie podręcznym Szczegóły połączenia wybierz **pozycję Tabela platformy Azure** dla miejsca **docelowego potencjalnego klienta**i wklej ciąg połączenia z konta magazynu platformy Azure utworzonego przez wykonując wcześniejsze kroki w polu ciągu połączenia konta **magazynu.**
4. Wybierz **pozycję Zapisz**. 

>[!Note]
>Musisz zakończyć konfigurowanie pozostałej części oferty i opublikować ją, zanim będzie można odbierać potencjalnych klientów dla oferty.

Gdy potencjalni klienci są generowane, Microsoft wysyła potencjalnych klientów do tabeli platformy Azure. Jeśli skonfigurowano przepływ, na skonfigurowany adres e-mail zostanie również wysłana wiadomość e-mail.

![Zarządzanie potencjalnymi klientami](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Zarządzanie potencjalnymi klientami — szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Zarządzanie potencjalnymi klientami — konto przechowywania szczegółów połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

