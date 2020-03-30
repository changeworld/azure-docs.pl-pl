---
title: Magazyn tabel platformy Azure | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami w usłudze Azure Table Storage.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dsindona
ms.openlocfilehash: cee28c65b97d4ac163f78b746dc7be9ee210843c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280358"
---
# <a name="lead-management-instructions-for-table-storage"></a>Instrukcje zarządzania ołowiu do przechowywania tabel

W tym artykule opisano sposób konfigurowania magazynu tabel platformy Azure do zarządzania potencjalnymi klientami sprzedaży. Magazyn tabel ułatwia przechowywanie i modyfikowanie informacji o klientach.

## <a name="configure-table-storage"></a>Konfigurowanie magazynu tabel

1. Jeśli nie masz konta platformy Azure, [utwórz bezpłatne konto próbne](https://azure.microsoft.com/pricing/free-trial/).
1. Po aktywnym koncie zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. W witrynie Azure portal wykonaj następujące kroki:  
    1. Wybierz **pozycję +Utwórz zasób** w okienku po lewej stronie. Zostanie **New** otwarte nowe okienko.
    1. W okienku **Nowy** wybierz pozycję **Magazyn**. **Po** prawej stronie zostanie otwarta wyróżniona lista.
    1. Wybierz **konto magazynu**. Następnie postępuj zgodnie z instrukcjami w [umień konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Tworzenie konta usługi Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    Aby uzyskać więcej informacji o kontach magazynu, zobacz [Samouczki szybki start](https://docs.microsoft.com/azure/storage/). Aby uzyskać informacje o cenach, zobacz [Ceny magazynu platformy Azure](https://azure.microsoft.com/pricing/details/storage/).

1. Poczekaj, aż konto magazynu zostanie aprowiedzone, co zwykle trwa kilka minut. Następnie przejdź do konta ze strony głównej witryny azure portal: Wybierz **zobacz wszystkie zasoby** lub wszystkie **zasoby** w okienku nawigacji.

    ![Uzyskiwanie dostępu do konta magazynu platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. Z okienka konta magazynu skopiuj parametry połączenia konta magazynu dla klucza. Wklej go w polu **Parametry połączenia** dla konta magazynu w portalu cloud partner.

    Przykładowy ciąg połączenia:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Klucz magazynu platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Aby wyświetlić dane w magazynie tabel, można użyć [Eksploratora usługi Azure Storage](https://azurestorageexplorer.codeplex.com/) lub podobnego narzędzia. Można również wyeksportować z niego dane.

## <a name="use-microsoft-flow-with-table-storage-optional"></a>Korzystanie z programu Microsoft Flow z magazynem tabel *(opcjonalnie)*

Za pomocą usługi [Microsoft Flow](https://docs.microsoft.com/flow/) można automatycznie wysyłać powiadomienia po dodaniu potencjalnego klienta do magazynu tabel. Jeśli nie masz konta Microsoft Flow, [zarejestruj się, aby uzyskać bezpłatne konto](https://flow.microsoft.com/).

### <a name="lead-notification-example"></a>Przykład powiadomienia potencjalnego klienta

W tym przykładzie pokazano, jak utworzyć przepływ podstawowy. Przepływ automatycznie wysyła powiadomienie e-mail co godzinę, gdy nowi potencjalni klienci są dodawane do magazynu tabel.

1. Zaloguj się do swojego konta Usługi Microsoft Flow.
1. W okienku nawigacji po lewej stronie wybierz pozycję **Moje przepływy**.
1. Na górnym pasku nawigacyjnym wybierz **+Nowy**.  
1. Z listy rozwijanej wybierz pozycję **+Utwórz z pustego**.
1. W obszarze **Tworzenie przepływu z pustego**wybierz pozycję **Utwórz z pustego**.

   ![Tworzenie nowego przepływu z pustego](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. Na stronie wyszukiwania łączników i wyzwalaczy wybierz pozycję **Wyzwalacze**.
1. W obszarze **Wyzwalacze**wybierz pozycję **Cykl**.
1. W oknie **Cykl zachowaj** domyślne ustawienie **1** dla **interval**. Z listy rozwijanej **Częstotliwość** wybierz pozycję **Godzina**.

   >[!NOTE] 
   >W tym przykładzie użyto odstępu godzinnego. Możesz jednak wybrać interwał i częstotliwość, które najlepiej pasują do Twoich potrzeb biznesowych.

   ![Ustawianie częstotliwości 1 godziny dla cyklu](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. Wybierz **+Nowy krok**.
1. Wyszukaj **pozycję Pochód przemiń**, a następnie wybierz pozycję **Uzyskaj czas przeszły** w obszarze Wybierz **akcję**.

    ![Znajdź i wybierz akcję "przejmij czas"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. W oknie **Czas przemiń** ustaw **interwał** na **1**.  Z listy rozwijanej **Jednostka czasu** wybierz pozycję **Godzina**.
    >[!IMPORTANT] 
    >Upewnij się, że jednostka **interwału** i **czasu** jest zgodna z interwałem i częstotliwością skonfigurowaną dla cyklu (krok 8).

    ![Ustawianie interwału czasu "odstęp czasu"](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Przepływ można sprawdzić w dowolnym momencie, aby sprawdzić, czy każdy krok jest poprawnie skonfigurowany: wybierz **kontroler przepływu** na pasku menu Przepływ.

W następnym zestawie kroków można połączyć się z tabeli magazynu i skonfigurować logikę przetwarzania do obsługi nowych potencjalnych klientów.

1. Po kroku **Czas przekroczania** wybierz pozycję **+Nowy krok**, a następnie wyszukaj pozycję **Pobierz encje**.
1. W obszarze **Akcje**wybierz pozycję **Pobierz encje**, a następnie wybierz pozycję **Pokaż opcje zaawansowane**.
1. W oknie **Pobierz encje** wypełnij następujące pola:

   - **Tabela**: nazwa magazynu tabel. Na poniższej ilustracji przedstawiono wpisany program "MarketPlaceLeads":

     ![Wybieranie wartości niestandardowej dla nazwy tabeli platformy Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Kwerenda filtrująca:** Po wybraniu tego pola w wyskakującym oknie zostanie wyświetlona ikona **Czas przeminięcia.** Wybierz **opcję Przemij czas,** aby użyć tej wartości jako sygnatury czasowej do filtrowania kwerendy. Można też wkleić do pola następującą funkcję:
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Konfigurowanie funkcji kwerendy filtrującej](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. Wybierz **nowy krok,** aby dodać warunek do skanowania magazynu tabel w poszukiwaniu nowych potencjalnych klientów.

   ![Użyj "Nowy krok", aby dodać warunek do skanowania magazynu tabel](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. W oknie **Wybieranie akcji** wybierz pozycję **Akcje**, a następnie wybierz pozycję **Formant kondycji**.

     ![Dodawanie kontrolki stanu](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. W oknie **Warunek** wybierz pozycję **Wybierz wartość**, a następnie wybierz pozycję **Wyrażenie** w wyskakującym oknie.
1. Wklej `length(body('Get_entities')?['value'])` w polu ***fx.*** Wybierz **przycisk OK,** aby dodać tę funkcję. 



     ![Dodawanie funkcji do warunku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. Skonfiguruj akcję do podjęcia na podstawie wyniku warunku.

    1. Wybierz **jest większy niż** z listy rozwijanej.
   1. Wprowadź **0** jako wartość.

     ![Konfigurowanie akcji na podstawie wyników warunku](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. Jeśli warunek zostanie rozpoznany jako "Jeśli nie", nie rób nic.

    Jeśli warunek zostanie rozpoznany jako "Jeśli tak", wyzwolić akcję łączącą konto usługi Office 365 w celu wysłania wiadomości e-mail:
   1. Wybierz pozycję **Dodaj akcję**.
   1. Wybierz akcję **Wyślij wiadomość e-mail**.
   1. W oknie **Wyślij wiadomość e-mail** wprowadź informacje w następujących polach:

      - **Do**: adres e-mail dla wszystkich, którzy otrzymają powiadomienie.
      - **Temat**: temat wiadomości e-mail. Na przykład: *Nowi potencjalni klienci!*
      - **Treść**: tekst, który chcesz uwzględnić w każdej wiadomości e-mail (opcjonalnie). Również wklej `body('Get_entities')?['value']` jako funkcję, aby wstawić informacje o potencjalnym klientu.

        >[!NOTE] 
        >W treści wiadomości e-mail można wstawić dodatkowe statyczne lub dynamiczne punkty danych.

      ![Konfigurowanie wiadomości e-mail do powiadamiania potencjalnego klienta](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. Aby zapisać zmiany, wybierz opcję **Zapisz**. Usługa Microsoft Flow automatycznie przetestuje go pod kątem błędów. Jeśli nie ma żadnych błędów, przepływ zaczyna działać po jego zapisaniu.

    Na poniższej ilustracji przedstawiono przykład ostatecznego przepływu.

    [![Końcowa sekwencja przepływu](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    (Wybierz*obraz, aby go powiększyć.*)

### <a name="manage-your-flow"></a>Zarządzanie przepływem

Zarządzanie przepływem jest łatwe po jego uruchomieniu. Masz pełną kontrolę nad przepływem. Na przykład możesz go zatrzymać, edytować, wyświetlić historię uruchamiania i uzyskać analizę. Na poniższej ilustracji przedstawiono opcje zarządzania przepływem.

 ![Opcje zarządzania przepływem](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Przepływ będzie działał do momentu **wybrania**opcji Wyłącz przepływ .

Jeśli nie otrzymujesz żadnych powiadomień e-mail z potencjalnymi klientami, do magazynu tabel nie dodano żadnych nowych potencjalnych klientów.
Otrzymasz wiadomość e-mail, jak w poniższym przykładzie, jeśli wystąpi błąd przepływu:

 ![Powiadomienie e-mail o awarii przepływu](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>Następne kroki

[Konfigurowanie potencjalnych klientów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
