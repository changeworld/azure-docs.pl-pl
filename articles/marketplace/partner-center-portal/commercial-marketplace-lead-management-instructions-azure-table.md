---
title: Tabela platformy Azure | Portal Azure Marketplace
description: Skonfiguruj zarządzanie liderem w tabeli platformy Azure.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 7151be3ac9f55825fd2e9dde35c9afda6a30726a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902639"
---
# <a name="configure-lead-management-using-an-azure-table"></a>Konfigurowanie zarządzania liderem za pomocą tabeli platformy Azure

Jeśli system zarządzania relacjami z klientami (CRM) nie jest jawnie obsługiwany w centrum partnerskim na potrzeby otrzymywania potencjalnych klientów w witrynie Azure Marketplace i AppSource, możesz użyć tabeli platformy Azure, aby obsłużyć te potencjalni klienci. Następnie można wyeksportować dane i zaimportować je do systemu CRM. Instrukcje zawarte w tym artykule przeprowadzą Cię przez proces tworzenia konta usługi Azure Storage i tabeli platformy Azure w ramach tego konta. Ponadto możesz utworzyć nowy przepływ przy użyciu Microsoft Flow, aby wysłać powiadomienie e-mail, gdy oferta odbierze potencjalny klient.

## <a name="configure-azure-table"></a>Konfigurowanie tabeli platformy Azure

1. Jeśli nie masz konta platformy Azure, możesz [utworzyć bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
2. Po uaktywnieniu konta platformy Azure Zaloguj się do [Azure Portal](https://portal.azure.com).
3. W Azure Portal Utwórz konto magazynu, wykonując poniższą procedurę.  
    1. Wybierz pozycję **+ Utwórz zasób** na pasku menu po lewej stronie.  **Nowe** okienko (blok) zostanie wyświetlone po prawej stronie.
    2. Wybierz pozycję **Magazyn** w **nowym** okienku.  Lista **proponowanych** zostanie wyświetlona po prawej stronie.
    3. Wybierz pozycję **konto magazynu** , aby rozpocząć tworzenie konta.  Postępuj zgodnie z instrukcjami w artykule [Tworzenie konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

        ![Procedura tworzenia konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        Aby uzyskać więcej informacji na temat kont magazynu, wybierz pozycję [samouczek szybkiego startu](https://docs.microsoft.com/azure/storage/).  Aby uzyskać więcej informacji na temat cennika usługi Storage, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/storage/).

4. Zaczekaj na zainicjowanie obsługi konta magazynu, proces, który zwykle trwa kilka minut.  Następnie uzyskaj dostęp do konta usługi Storage ze strony **głównej** Azure Portal, wybierając pozycję **Pokaż wszystkie zasoby** lub wybierając pozycję **wszystkie zasoby** na pasku menu nawigacji po lewej stronie Azure Portal.

    ![Dostęp do konta usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. W okienku konto magazynu wybierz pozycję **klucze dostępu** i skopiuj wartość *Parametry połączenia* dla klucza. Zapisz tę wartość, ponieważ jest to wartość *parametrów połączenia konta magazynu* , która będzie potrzebna w portalu wydawców w celu otrzymywania potencjalnych klientów na potrzeby oferty z witryny Marketplace. 

    Przykładem połączenia Sting jest:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Klucz usługi Azure Storage](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. W okienku konto magazynu wybierz pozycję **tabele** i wybierz pozycję **+ tabela** , aby utworzyć tabelę. Wprowadź nazwę tabeli i wybierz **przycisk OK**. Zapisz tę wartość, ponieważ będzie ona potrzebna, jeśli chcesz skonfigurować program MS Flow do odbierania powiadomień e-mail po odebraniu potencjalnych klientów.

    ![Tabele platformy Azure](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    Aby wyświetlić dane w tabeli magazynu, można użyć [Eksploratora usługi Azure Storage](https://archive.codeplex.com/?p=azurestorageexplorer) lub innego narzędzia. Możesz również eksportować dane w tabeli platformy Azure. 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>Obowiązkowe Używanie Microsoft Flow z tabelą platformy Azure  

[Microsoft Flow](https://docs.microsoft.com/flow/) służy do automatyzowania powiadomień za każdym razem, gdy potencjalny klient zostanie dodany do tabeli platformy Azure. Jeśli nie masz konta, możesz [zarejestrować się w celu uzyskania bezpłatnego konta](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Przykład powiadomienia lidera

Ten przykład służy jako przewodnik tworzenia prostego przepływu, który automatycznie wysyła powiadomienie e-mail po dodaniu nowego potencjalnego klienta do tabeli platformy Azure. W tym przykładzie skonfigurowano cykl do wysyłania informacji o potencjalnym kliencie co godzinę w przypadku zaktualizowania magazynu tabel.

1. Zaloguj się do konta Microsoft Flow.
2. Na pasku nawigacyjnym po lewej stronie wybierz pozycję **Moje przepływy**.
3. Na górnym pasku nawigacyjnym wybierz pozycję **+ Nowy**.  
4. Na liście rozwijanej wybierz pozycję **+ zaplanowana — od zera** .

   ![Moje przepływy * * + zaplanowane — od zera * *](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  W oknie *kompilacja zaplanowanego przepływu* w obszarze *Powtarzaj każdy* wybierz opcję "1" dla interwału i "godzina", aby uzyskać częstotliwość. Ponadto Nadaj nazwę przepływowi, jeśli chcesz. Wybierz pozycję **Utwórz**.

    >[!Note]
    >Mimo że w tym przykładzie używany jest 1-godzinny interwał, można wybrać interwał i częstotliwość, które najlepiej odpowiadają potrzebom biznesowym.

    ![Kompilowanie zaplanowanego przepływu.](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. Wybierz pozycję **+ nowy krok**.
7. W oknie *Wybierz akcję* Wyszukaj ciąg "Pobierz czas ostatniej", a następnie wybierz pozycję **Pobierz czas** ostatniej akcji.

   ![Wybierz akcję.](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. W oknie **Pobierz czas** ostatniej Ustaw **Interwał** na 1. Z listy rozwijanej **jednostka czasu** wybierz pozycję **Hour (godzina**).

    >[!Important]
    >Upewnij się, że ten interwał i jednostka czasu są zgodne z interwałem i częstotliwością skonfigurowaną dla cyklu w kroku 5.

    ![Ustaw przedział czasu pobierania](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>W każdej chwili można sprawdzić, czy każdy krok jest prawidłowo skonfigurowany. Aby sprawdzić przepływ, wybierz pozycję **moduł sprawdzania przepływu** na pasku menu przepływ.

W następnym zestawie kroków zostanie nawiązane połączenie z tabelą platformy Azure i skonfigurowanie logiki przetwarzania w celu obsługi nowych potencjalnych klientów.

9. Po kroku Pobierz poprzedni czas wybierz pozycję **+ nowy krok**, a następnie wyszukaj ciąg "Pobierz jednostki" w oknie *Wybieranie akcji* .
10. W obszarze **Akcje**wybierz pozycję **Pobierz jednostki (Azure Table Storage)** .
11. W oknie **Table Storage platformy Azure** podaj informacje dotyczące następujących pól i wybierz pozycję **Utwórz**:

    * *Nazwa połączenia* — Podaj zrozumiałą nazwę połączenia, które tworzysz między tym przepływem a tabelą platformy Azure.
    * *Nazwa konta magazynu* — Podaj nazwę konta magazynu dla swojej tabeli platformy Azure. Tę wartość można znaleźć na stronie **klucze dostępu** konta magazynu.
    * *Współużytkowany klucz magazynu* — podaj wartość klucza dla konta magazynu dla swojej tabeli platformy Azure. Tę wartość można znaleźć na stronie **klucze dostępu** konta magazynu.

        ![Azure Table Storage.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    Po kliknięciu przycisku Utwórz zostanie wyświetlone okno *pobieranie jednostek* . W tym miejscu wybierz pozycję **Pokaż opcje zaawansowane** i podaj informacje dla następujących pól:

       * *Tabela* — wybierz nazwę Table Storage platformy Azure (z kroku 6 instrukcje dotyczące konfigurowania tabeli platformy Azure). Przechwytywanie następnego ekranu pokazuje monit, gdy w tym przykładzie wybrano tabelę "marketplaceleads".

            ![Jednostki pobierania tabeli platformy Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *Zapytanie filtru* — zaznacz to pole i wklej tę funkcję do pola:`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Pobieranie jednostek przez tabelę platformy Azure — filtr querry.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. Po zakończeniu konfigurowania połączenia z tabelą platformy Azure wybierz pozycję **nowy krok** , aby dodać warunek skanowania tabeli platformy Azure pod kątem nowych potencjalnych klientów. 

13. W oknie **Wybierz akcję** wybierz pozycję **Akcje**, a następnie wybierz **kontrolkę warunek**.

    ![Azure Table — wybierz akcję.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. W oknie **warunek** wybierz pole **Wybierz wartość** , a następnie w oknie podręcznym wybierz pozycję **wyrażenie** .

15. Wklej `length(body('Get_entities')?['value'])` do pola ***FX*** . Wybierz **przycisk OK** , aby dodać tę funkcję. 

16. Aby zakończyć konfigurowanie warunku:
    1. Wybierz pozycję "jest większe niż" z listy rozwijanej.
    2. Wprowadź 0 jako wartość

        ![Warunek tabeli platformy Azure.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

W następnych kilku krokach skonfigurujesz akcję do wykonania w oparciu o wynik warunku.

* Jeśli warunek jest rozpoznawany jako **nie**, nic nie rób.
* Jeśli warunek ma **wartość tak**, wyzwól akcję, która łączy konto Office 365 w celu wysłania wiadomości e-mail. 

17. Wybierz pozycję **Dodaj akcję** w obszarze **Jeśli tak**.

    ![Warunek tabeli platformy Azure, * *, jeśli tak * *.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. Wybierz pozycję **Wyślij wiadomość e-mail (Office 365 Outlook)** .

    ![Warunek tabeli platformy Azure, * *, jeśli tak * *, Wyślij wiadomość e-mail.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >Jeśli chcesz użyć innego wyszukiwania dostawcy poczty e-mail, a następnie wybierz opcję Wyślij wiadomość e-mail z powiadomieniem (pocztą) jako akcję. W tych instrukcjach pokazano, jak skonfigurować program Office 365 Outlook, ale instrukcje są podobne dla innego dostawcy poczty e-mail.

19. W oknie **Office 365 Outlook** podaj informacje dla następujących pól:

    1. Wprowadź adres e-mail dla wszystkich użytkowników, którzy otrzymają to powiadomienie.
    1. **Podmiot** — Podaj temat wiadomości e-mail. Przykład: Nowi potencjalni klienci!
    1. **Treść** — Dodaj tekst, który ma zostać uwzględniony w każdej wiadomości e-mail (opcjonalnie), a następnie wklej w `body('Get_entities')?['value']`treści.

    >[!Note]
    >Do treści tej wiadomości e-mail można wstawić dodatkowe statyczne lub dynamiczne punkty danych.

    ![Warunek tabeli platformy Azure, * *, jeśli tak * *, okno Office 365 Outlook.](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. Wybierz pozycję **Zapisz** , aby zapisać przepływ. Microsoft Flow automatycznie przetestuje przepływ pod kątem błędów. Jeśli nie ma żadnych błędów, przepływ zostanie uruchomiony po jego zapisaniu.

Na następnym ekranie przechwytywanie ekranu przedstawiono przykład sposobu, w jaki powinien wyglądać końcowy przepływ.

![Przykład przepływu końcowego.](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>Zarządzanie przepływem

Zarządzanie przepływem jest proste. Masz pełną kontrolę nad przepływem. Można na przykład ją zatrzymać, edytować, wyświetlić historię uruchamiania i uzyskać analizę. Przechwytywanie następnego ekranu pokazuje opcje, które są dostępne do zarządzania przepływem. 

 ![Zarządzanie przepływem](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

Przepływ pozostaje uruchomiony do momentu zatrzymania go przy użyciu opcji **Wyłącz przepływ** .

Jeśli nie otrzymasz żadnych powiadomień e-mail dotyczących potencjalnych klientów, oznacza to, że nowi potencjalni klienci nie zostali dodani do tabeli platformy Azure. Jeśli wystąpią jakieś błędy przepływu, otrzymasz wiadomość e-mail podobną do przykładu podczas następnego przechwytywania ekranu.

 ![Powiadomienie e-mail o niepowodzeniu przepływu](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>Konfigurowanie oferty do wysyłania potencjalnych klientów do tabeli platformy Azure

Gdy wszystko jest gotowe do skonfigurowania informacji dotyczących zarządzania potencjalnym liderem oferty w portalu wydawców, wykonaj następujące czynności:

1. Przejdź do strony **Konfiguracja oferty** dla swojej oferty.
2. Wybierz pozycję **Połącz** w sekcji Zarządzanie potencjalnymi klientami.
3. W oknie podręcznym szczegóły połączenia wybierz pozycję **tabela platformy Azure** dla **miejsca docelowego potencjalnego klienta**i wklej ciąg połączenia z konta usługi Azure Storage utworzonego w ramach wcześniejszych kroków do **parametrów połączenia konta magazynu** . polami.
4. Wybierz pozycję **Zapisz**. 

>[!Note]
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.

Gdy są generowane potencjalni klienci, firma Microsoft wysyła potencjalni klienci do tabeli platformy Azure. W przypadku skonfigurowania przepływu poczta e-mail również zostanie wysłana na skonfigurowany adres e-mail.

![Zarządzanie potencjalnymi klientami](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![Zarządzanie potencjalnymi klientami — szczegóły połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![Zarządzanie potencjalnymi klientami — konto magazynu szczegółów połączenia](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

