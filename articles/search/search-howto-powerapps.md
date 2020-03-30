---
title: Jak wysyłać zapytania do usługi Azure Cognitive Search z aplikacji power apps
titleSuffix: Azure Cognitive Search
description: Wskazówki krok po kroku dotyczące tworzenia łącznika niestandardowego do wyszukiwania funkcji Poznawczych i wizualizacji go za pomocą aplikacji Power App
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385116"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>Jak zbadać indeks wyszukiwania funkcji Poznawczych z aplikacji Power Apps

W tym dokumencie pokazano, jak utworzyć łącznik niestandardowy programu Power Apps, aby można było pobrać wyniki wyszukiwania z indeksu wyszukiwania. Pokazuje również, jak wystawić zapytanie wyszukiwania i wizualizować wyniki z aplikacji Power App. 

## <a name="prerequisites"></a>Wymagania wstępne
*    Dostęp do konta usługi Power Apps z możliwością tworzenia łączników niestandardowych.
*    Zakładamy, że utworzono już indeks usługi Azure Search.

## <a name="create-a-custom-connector-to-query-azure-search"></a>Tworzenie łącznika niestandardowego do wykonywania zapytań o usługę Azure Search

Istnieją dwa główne kroki do posiadania usługi PowerApp, który pokazuje wyniki usługi Azure Cognitive Search. Najpierw utwórzmy łącznik, który może wysyłać zapytania do indeksu wyszukiwania. W [następnej sekcji](#visualize-results-from-the-custom-connector) zaktualizujemy aplikację Power Apps, aby wizualizować wyniki zwracane przez łącznik.

1. Przejdź do [make.powerapps.com](http://make.powerapps.com) i **zaloguj się**.

1. Wyszukiwanie **Data** > **łączników niestandardowych** danych
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Niestandardowe menu łącznika" border="true":::

1. Kliknij **przycisk + Nowy łącznik niestandardowy,** a następnie wybierz pozycję **Utwórz z pustego**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Utwórz z pustego menu" border="true":::

1. Nadaj niestandardowemu łącznikowi nazwę. (czyli *AzureSearchQuery),* a następnie kliknij przycisk **Kontynuuj**. Spowoduje to utworzenie kreatora w celu utworzenia nowego łącznika.

1. Wprowadź informacje na stronie ogólne.

    - Kolor tła ikony (na przykład #007ee5)
    - Opis (na przykład "Łącznik do usługi Azure Cognitive Search")
    - W hostie musisz wprowadzić adres URL usługi wyszukiwania `<yourservicename>.search.windows.net`(na przykład)
    - W przypadku podstawowego adresu URL wystarczy wpisać "/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialog z informacjami ogólnymi" border="true":::

1. Na stronie Zabezpieczeń ustaw *klucz API* jako **typ uwierzytelniania,** ustaw etykietę parametru i pola nazwy parametru jako *api-key*. W polu **Lokalizacja parametru**wybierz *nagłówek,* jak pokazano poniżej.
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Opcja typu uwierzytelniania" border="true":::

1. Na stronie Definicje wybierz pozycję **+ Nowa akcja,** aby utworzyć akcję, która będzie wysyłać zapytania do indeksu. Wprowadź wartość "Zapytanie" dla podsumowania i nazwę identyfikatora operacji. Wprowadź opis w stylu *"Kwerendy indeks wyszukiwania"*.
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nowe opcje akcji" border="true":::


1. Naciśnij przycisk **+ Importuj z próbki,** aby zdefiniować parametry i nagłówki. Następnie zdefiniujesz żądanie kwerendy.  

    * Wybierz czasownik`GET`
    * Na przykład w przypadku adresu URL wprowadź przykładowe zapytanie dotyczące indeksu wyszukiwania:
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=*&api-version=2019-05-06-Preview
    

    **Power Apps** użyje składni, aby wyodrębnić parametry z kwerendy. Zwróć uwagę, że wyraźnie zdefiniowaliśmy pole wyszukiwania. 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importowanie z próbki" border="false":::

1.  Kliknij **przycisk Importuj,** aby automatycznie wstępnie wypełnić okno dialogowe Żądanie.

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importowanie z przykładowego dialogu" border="false":::


1. Pełne ustawienie metadanych parametrów przez kliknięcie **...** obok każdego z parametrów.

    - W *przypadku* `*` wyszukiwania : Ustaw jako **wartość domyślną**, ustaw **wymaganą** jako *fałsz* i ustaw **widoczność** na *brak*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadane parametrów wyszukiwania" border="true":::

    - Dla *wersji interfejsu* `2019-05-06-Preview` API : Ustaw jako **wartość domyślną**, ustaw **widoczność** jako wewnętrzną i ustaw **wymaganą** wartość *True*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadane parametrów wersji" border="true":::

    - Podobnie, dla *api-key*, ustaw go zgodnie z **wymaganiami,** z *wewnętrzną* **widocznością**. Wprowadź klucz interfejsu API usługi wyszukiwania jako **wartość domyślna**.
    
    Po dokonaniu tych zmian przełącz widok **Edytora waggerów.** W sekcji parametry powinna zostać wyświetlona następująca konfiguracja:    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. W sekcji Odpowiedź kliknij **"Dodaj odpowiedź domyślną"**. Jest to bardzo ważne, ponieważ pomoże to **aplikacji power apps** zrozumieć schemat odpowiedzi. Wklej przykładową odpowiedź.

    > [!TIP] 
    > Istnieje limit znaków do odpowiedzi JSON można wprowadzić, więc można uprościć JSON tak, aby przed wklejeniem go. Ważny schemat aspektu/format odpowiedzi. Rzeczywiste wartości w odpowiedzi próbki są mniej ważne i mogą być uproszczone, aby zmniejszyć liczbę znaków.
    

1.    Kliknij przycisk **Utwórz łącznik** w prawym górnym rogu ekranu, zanim będzie można go przetestować.

1.  Na stronie testowej kliknij przycisk **+ Nowe połączenie**i wprowadź klucz zapytania usługi wyszukiwania jako wartość dla *api-key*.

    Ten krok może zabrać Cię do wyjścia z kreatora i do strony Połączenia. Możesz wrócić do edytora połączeń niestandardowych, aby faktycznie przetestować połączenie. Przejdź do **łącznika niestandardowego** > Wybierz nowo utworzony łącznik > *...* > **Wyświetl właściwości** > **Edytuj** > **4. Przetestuj,** aby wrócić do strony testowej.

1.    Teraz kliknij **przycisk Test operacji,** aby upewnić się, że otrzymujesz wyniki z indeksu. Jeśli ci się powiedzie, powinieneś zobaczyć stan 200, a w treści odpowiedzi powinien zostać wyświetlony JSON opisujący wyniki wyszukiwania.




## <a name="visualize-results-from-the-custom-connector"></a>Wizualizuj wyniki z łącznika niestandardowego
Celem tego samouczka nie jest pokazanie, jak tworzyć fantazyjne doświadczenia użytkownika z aplikacjami zasilania, więc układ interfejsu użytkownika będzie minimalistyczny. Utwórzmy powerapp z polem wyszukiwania, przyciskiem wyszukiwania i wyświetl wyniki w formancie galerii.  Usługa PowerApp połączy się z naszym niedawno utworzonym łącznikiem niestandardowym, aby uzyskać dane z usługi Azure Search.

1. Utwórz nową aplikację Power App. Przejdź do sekcji **Aplikacje,** kliknij **+ Nowa aplikacja**i wybierz **kanwę**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Tworzenie aplikacji kanwy" border="true":::

1. Wybierz typ aplikacji, którą chcesz. W tym samouczku utwórz **pustą aplikację** z **układem telefonu**. Zostanie wyświetlone **studio Power Apps Studio.**

1. W studiu wybierz kartę **Źródła danych** i kliknij nowy łącznik, który właśnie utworzyłeś. W naszym przypadku nazywa się *AzureSearchQuery*. Kliknij **pozycję Dodaj połączenie**.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="złącze connect" border="true":::

    Teraz *AzureSearchQuery* jest źródłem danych, które jest dostępne do użycia z aplikacji.
    
1. Przejdź do **zakładki Wstaw,** abyśmy mogli dodać kilka kontrolek do naszego formularza.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Wstawianie kontrolek" border="true":::

1.  Wstawić następujące elementy:
    -   Etykieta tekstowa z wartością "Zapytanie:"
    -   Element wprowadzania tekstu (nazwij to *txtQuery*, wartość domyślna: "*")
    -   Przycisk z tekstem "Szukaj" 
    -   Galeria pionowa o nazwie (nazwijmy to *galeriaWyniki)*
    
    Formularz powinien wyglądać mniej więcej tak:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Układ formantów" border="true":::

1. Aby **przycisk Wyszukiwania** wystawił zapytanie, zaznacz przycisk i wklej następującą akcję, aby wykonać **onselect:**

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Przycisk OnSelect" border="true":::
 
    Ta akcja spowoduje, że przycisk zaktualizować nową kolekcję o nazwie *azResult* z wynikiem kwerendy wyszukiwania, przy użyciu tekstu w polu tekstowym *txtQuery* jako termin zapytania.
    
1.  W następnym kroku połączymy utworzoną przez nas galerię pionową z *kolekcją azResult.* Wybierz kontrolkę galerii i wykonaj następujące akcje w okienku właściwości.

    -  Ustaw **źródło danych** na *azResult*.
    
    -  Wybierz **układ,** który działa dla Ciebie na podstawie typu danych w indeksie. W tym przypadku użyliśmy *tytułu, napisów i* układu treści.
    
    -  **Edytuj pola**i wybierz pola, które chcesz wizualizować.

    Ponieważ podajemy przykładowy wynik podczas definiowania łącznika, aplikacja jest świadoma pól dostępnych w indeksie.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Pola galerii" border="true":::   
 
1.  Naciśnij **klawisz F5,** aby wyświetlić podgląd aplikacji.  

    Należy pamiętać, że pola można ustawić na wartości obliczeniowe.      
    Na przykład ustawienie przy użyciu układu *"Obraz, tytuł i napisy"* i określenie funkcji *Obraz* jako konkajacji ścieżki głównej `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`danych i nazwy pliku (na przykład) spowoduje uzyskanie poniższego wyniku.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Końcowa aplikacja" border="true":::        

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i szkolenia online, zobacz [Katalog uczenia aplikacji power](https://docs.microsoft.com/powerapps/learning-catalog/get-started)apps .

