---
title: 'Wyszukiwanie niestandardowe Bing: Tworzenie strony internetowej wyszukiwania niestandardowego | Dokumentacja firmy Microsoft'
description: W tym artykule opisano sposób konfigurowania wystąpienia wyszukiwania niestandardowego i włączenie go do strony sieci web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 1f9b689ac6127bc2f7d1e810356ae9a23b8e0996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162397"
---
# <a name="build-a-custom-search-web-page"></a>Tworzenie strony internetowej z funkcją wyszukiwania niestandardowego
Usługa Bing Custom Search umożliwia utworzenie środowiska wyszukiwania dostosowane dla tematów, które Cię interesują. Na przykład jeśli jesteś właścicielem walki zapewniającej środowisko wyszukiwania, możesz określić, domen, Lokacje podrzędne i stron sieci Web, która wyszukuje Bing. Użytkownicy zobaczą wyniki wyszukiwania, dostosowane do zawartości, które ich interesują zamiast stronicować wyniki wyszukiwania, zawierające zawartości nie ma znaczenia. 

W tym samouczku pokazano, jak skonfigurować wystąpienia wyszukiwania niestandardowego i zintegrować ją nową stronę sieci web.

Zadania omówione są następujące:

> [!div class="checklist"]
> - Tworzenie wystąpienia wyszukiwania niestandardowego
> - Dodaj aktywne wpisy
> - Dodaj pozycje zablokowane
> - Dodaj pozycje przypięte
> - Integrowanie wyszukiwanie niestandardowe strony sieci web

## <a name="prerequisites"></a>Wymagania wstępne
- Dla interfejsu API wyszukiwania niestandardowego Bing, aby skorzystać z samouczka, potrzebujesz klucza subskrypcji.  Aby uzyskać klucz, zobacz [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**.

## <a name="create-a-custom-search-instance"></a>Tworzenie wystąpienia wyszukiwania niestandardowego
Aby utworzyć wystąpienie wyszukiwania niestandardowego Bing:

1.  Otwórz przeglądarkę internetową.
2.  Przejdź do wyszukiwania niestandardowego [portal](https://customsearch.ai).
3.  Zaloguj się do portalu przy użyciu konta Microsoft (MSA). Jeśli nie masz konta Microsoft, kliknij przycisk **Utwórz konto Microsoft**. Jeśli po raz pierwszy przy użyciu portalu, zostanie wyświetlony monit uprawnień dostępu do danych. Kliknij przycisk **Yes** (Tak).
4.  Po zarejestrowaniu się kliknij **nowego wyszukiwania niestandardowego**. W **utworzenia nowego wystąpienia wyszukiwania niestandardowego** okna, wprowadź nazwę, która ma znaczenie i opisuje typ zawartości, ponieważ funkcja wyszukiwania. Nazwę można zmienić w dowolnym momencie.
 
    ![Zrzut ekranu przedstawiający Utwórz nowe pole wystąpienia wyszukiwania niestandardowego](../media/newCustomSrch.png)

5.  Kliknij przycisk OK, określ adres URL i czy mają zostać dołączone podstrony strony podstawowej:

    ![Zrzut ekranu strony definicji adresu URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Dodaj aktywne wpisy
Aby dołączyć wyniki z określonej witryny lub adresy URL, dodaj je do **Active** kartę.

1.  W **Edytor definicji**, kliknij przycisk **Active** kartę, a następnie wprowadź adres URL co najmniej jedną lokację, do których mają zostać uwzględnione w wyszukiwaniu.

    ![Zrzut ekranu: karta aktywny edytor definicji](../media/customSrchEditor.png)

2.  Aby upewnić się, że wystąpienie zwraca wyniki, należy wprowadzić kwerendę w okienku podglądu, po prawej stronie. Wyszukiwarka Bing zwróci wyniki tylko dla publicznych witryn, które ma on indeksowane.

## <a name="add-blocked-entries"></a>Dodaj pozycje zablokowane
Aby wykluczyć wyniki z określonej witryny lub adresy URL, dodaj je do **zablokowane** kartę.

1. W **Edytor definicji**, kliknij przycisk **zablokowane** kartę, a następnie wprowadź adres URL co najmniej jedną lokację, którą chcesz wykluczyć z wyszukiwania.

    ![Zrzut ekranu: Edytor definicji zablokowane kartę](../media/blockedCustomSrch.png)


2. Aby upewnić się, że wystąpienie usługi nie zwracają wartości z zablokowanych witryn, wprowadź kwerendę w okienku podglądu, po prawej stronie. 

## <a name="add-pinned-entries"></a>Dodaj pozycje przypięte
Aby przypiąć konkretnej strony sieci Web na początku wyników wyszukiwania, należy dodać termin strony sieci Web i zapytania, aby **Pinned** kartę.  **Pinned** karta zawiera listę par termin do strony sieci Web i zapytania, określających strony sieci Web, który jest wyświetlany jako najlepszy wynik dla określonej kwerendy. Warunek kwerendy użytkownika musi dokładnie odpowiadać przypiętych wyszukiwanego terminu w witrynie internetowej przypiąć do góry.

1. W **Edytor definicji**, kliknij przycisk **Pinned** kartę, a następnie wprowadź termin strony sieci Web i zapytania, strony sieci Web, która powinna zwrócić najlepszy wynik.

    ![Zrzut ekranu: Edytor definicji przypięte karty](../media/pinnedCustomSrch.png)

2. Aby upewnić się, że wystąpienie zwraca określona strona sieci Web jako najlepszy wynik, wprowadź termin kwerendy, przypięte przez Ciebie w okienku podglądu, po prawej stronie.

## <a name="configure-hosted-ui"></a>Konfigurowanie hostowanej interfejsu użytkownika
Custom Search udostępnia hostowanej interfejsu użytkownika do renderowania odpowiedzi JSON wystąpienia wyszukiwania niestandardowego.  Aby zdefiniować środowiska interfejsu użytkownika:

1. Kliknij przycisk **hostowanych interfejsu użytkownika** kartę.
2. Wybierz układ.

    ![Zrzut ekranu przedstawiający interfejs użytkownika obsługiwany wybierz krok układu](./media/custom-search-hosted-ui-select-layout.png)

3. Wybierz motyw kolorów.

    ![Zrzut ekranu przedstawiający interfejs użytkownika obsługiwany wybierz krok układu](./media/custom-search-hosted-ui-select-color-theme.png)

4. Określ dodatkowe opcje konfiguracji.

    ![Zrzut ekranu przedstawiający kroku dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-additional-configurations.png)

5. Wklej swoje **klucz subskrypcji**. Zobacz [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Zrzut ekranu przedstawiający kroku dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Korzystanie z hostowanej interfejsu użytkownika
Istnieją dwa sposoby korzystania z obsługiwanego interfejsu użytkownika.  

- Opcja 1: Integrowanie podana fragment kodu JavaScript w aplikacji.
- Opcja 2: Używać punktu końcowego HTML, pod warunkiem.

W pozostałej części tego samouczka przedstawiono **opcja 1: fragment kodu Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Konfigurowanie rozwiązania programu Visual Studio
1. Otwórz program **Visual Studio** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W **nowy projekt** wybierz **Visual C# / Web / aplikacja sieci Web programu ASP.NET Core**, podaj nazwę projektu, a następnie kliknij przycisk **OK**.
   
    ![Zrzut ekranu przedstawiający okno nowy projekt](./media/custom-search-new-project.png)

4. W **Nowa aplikacja internetowa ASP.NET Core** wybierz **aplikacji sieci Web** i kliknij przycisk **OK**.
    
    ![Zrzut ekranu przedstawiający okno nowy projekt](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Edytuj index.cshtml
1. W **Eksploratora rozwiązań**, rozwiń węzeł **stron** i kliknij dwukrotnie **index.cshtml** można otworzyć pliku.

    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań za pomocą strony rozwinięte i index.cshtml wybrane](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. W index.cshtml Usuń wszystko, od wiersz 7, jak i poniżej.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Dodaj element podziału wiersza i div do działania jako kontener.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Z **hostowanych interfejsu użytkownika** karty, przewiń w dół do sekcji **korzystanie z interfejsu użytkownika**. Skopiuj fragment kodu JavaScript.

    ![Przycisk Zapisz zrzut ekranu przedstawiający interfejs użytkownika obsługiwany](./media/custom-search-hosted-ui-consuming-ui.png)

5. Wklej script element w kontenerze, który został dodany.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch">
        <script type="text/javascript"
                id="bcs_js_snippet"
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **wwwroot** i kliknij przycisk **Pokaż w przeglądarce**.

    ![Zrzut ekranu Eksploratora rozwiązań z wybraniu widoku w przeglądarce z poziomu menu kontekstowego wwwroot](./media/custom-search-webapp-view-in-browser.png)

Strony sieci web wyszukiwania niestandardowego powinien wyglądać mniej więcej tak:

![Zrzut ekranu strony sieci web wyszukiwania niestandardowego](./media/custom-search-webapp-browse-index.png)

Przeprowadzania wyszukiwania renderuje wyniki w następujący sposób.

![Zrzut ekranu przedstawiający wyniki wyszukiwania niestandardowego](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> - Utworzone wystąpienie wyszukiwania niestandardowego
> - Dodano aktywne wpisy
> - Dodano zablokowanych wpisów
> - Dodano przypiętych wpisów
> - Zintegrowane wyszukiwanie niestandardowe strony sieci web

Możesz teraz przejść do wywoływanie punktów końcowych usługi Bing Custom Search programowo.

> [!div class="nextstepaction"]
> [Wywołanie punktu końcowego usługi Bing Custom Search (C#)](../call-endpoint-csharp.md)