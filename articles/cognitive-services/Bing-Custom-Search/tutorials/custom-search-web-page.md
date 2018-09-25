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
ms.openlocfilehash: 8bc1520325afc256ac62cc1f1dfaf24c53da4b83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980002"
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

1. Otwórz przeglądarkę internetową.  
  
2. Przejdź do wyszukiwania niestandardowego [portal](https://customsearch.ai).  
  
3. Zaloguj się do portalu przy użyciu konta Microsoft (MSA). Jeśli nie masz konta Microsoft, kliknij przycisk **Utwórz konto Microsoft**. Jeśli po raz pierwszy przy użyciu portalu, zostanie wyświetlony monit uprawnień dostępu do danych. Kliknij przycisk **Yes** (Tak).  
  
4. Po zarejestrowaniu się kliknij **nowego wyszukiwania niestandardowego**. W **utworzenia nowego wystąpienia wyszukiwania niestandardowego** okna, wprowadź nazwę, która ma znaczenie i opisuje typ zawartości, ponieważ funkcja wyszukiwania. Nazwę można zmienić w dowolnym momencie.  
  
  ![Zrzut ekranu przedstawiający Utwórz nowe pole wystąpienia wyszukiwania niestandardowego](../media/newCustomSrch.png)  
  
5. Kliknij przycisk OK, określ adres URL i czy mają zostać dołączone podstrony adresu URL.  
  
  ![Strona definicji zrzut ekranu z adresu URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Dodaj aktywne wpisy

Aby dołączyć wyniki z określonych witryn internetowych lub adresy URL, dodaj je do **Active** kartę.

1.  Na **konfiguracji** kliknij **Active** kartę, a następnie wprowadź adres URL jednego lub więcej witryn sieci Web, które chcesz uwzględnić w wyszukiwaniu.

    ![Zrzut ekranu przedstawiający aktywną kartę Edytor definicji](../media/customSrchEditor.png)

2.  Aby upewnić się, że wystąpienie zwraca wyniki, należy wprowadzić kwerendę w okienku podglądu, po prawej stronie. Wyszukiwarka Bing zwraca tylko wyniki dla witryn publicznych, które ma on indeksowane.

## <a name="add-blocked-entries"></a>Dodaj pozycje zablokowane

Aby wykluczyć wyniki z określonych witryn internetowych lub adresy URL, dodaj je do **zablokowane** kartę.

1. Na **konfiguracji** kliknij **zablokowane** kartę, a następnie wprowadź adres URL jednego lub więcej witryn sieci Web, które chcesz wykluczyć z wyszukiwania.

    ![Zrzut ekranu przedstawiający kartę zablokowanych Edytor definicji](../media/blockedCustomSrch.png)


2. Aby upewnić się, że wystąpienie usługi nie zwracania wyników z zablokowanych witryn sieci Web, wprowadź kwerendę w okienku podglądu, po prawej stronie. 

## <a name="add-pinned-entries"></a>Dodaj pozycje przypięte

Aby przypiąć konkretnej strony sieci Web na początku wyników wyszukiwania, należy dodać termin strony sieci Web i zapytania, aby **Pinned** kartę. **Pinned** karta zawiera listę par termin do strony sieci Web i zapytania, określających strony sieci Web, który jest wyświetlany jako najlepszy wynik dla określonej kwerendy. Strony sieci Web jest przypięty, tylko wtedy, gdy ciąg zapytania użytkownika pasuje do ciągu zapytania numeru pin, na podstawie warunku dopasowania numeru pin. [Dowiedz się więcej](../define-your-custom-view.md#pin-to-top).

1. Na **konfiguracji** kliknij **Pinned** kartę, a następnie wprowadź termin strony sieci Web i zapytania, strony sieci Web, które mają być zwracane w wyniku najważniejsze.  
  
2. Domyślnie ciąg zapytania użytkownika musi dokładnie odpowiadać ciągu zapytania numeru pin, Bing powrócić na stronę sieci Web jako najlepszy wynik. Aby zmienić warunek dopasowania, Edytuj numer pin (kliknij ikonę ołówka), kliknij Exact w **warunek dopasowania zapytania** kolumny, a następnie wybierz warunek dopasowania, który jest odpowiedni dla danej aplikacji.  
  
    ![Zrzut ekranu edytora definicji przypięte karty](../media/pinnedCustomSrch.png)
  
3. Aby upewnić się, że wystąpienie zwraca określona strona sieci Web jako najlepszy wynik, wprowadź termin kwerendy, przypięte przez Ciebie w okienku podglądu, po prawej stronie.

## <a name="configure-hosted-ui"></a>Konfigurowanie hostowanej interfejsu użytkownika

Custom Search udostępnia hostowanej interfejsu użytkownika do renderowania odpowiedzi JSON wystąpienia wyszukiwania niestandardowego. Aby zdefiniować środowiska interfejsu użytkownika:

1. Kliknij przycisk **hostowanych interfejsu użytkownika** kartę.  
  
2. Wybierz układ.  
  
  ![Zrzut ekranu przedstawiający interfejs użytkownika obsługiwany wybierz krok układu](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Wybierz motyw kolorów.  
  
  ![Zrzut ekranu przedstawiający interfejs użytkownika obsługiwany wybierz motyw kolorów](./media/custom-search-hosted-ui-select-color-theme.png)  

  Jeśli musisz dostosowywać motyw kolorów, aby lepiej Integracja z aplikacją sieci web, kliknij przycisk **motyw Dostosuj**. Nie wszystkie konfiguracje kolor mają zastosowanie do wszystkich motywy układu. Aby zmienić kolor, wprowadź wartość SZESNASTKOWY RGB koloru (na przykład #366eb8) w odpowiednim polu tekstowym. Ewentualnie kliknij przycisk koloru, a następnie kliknij przycisk odcienia, który Ci odpowiada. Zawsze wziąć pod uwagę ułatwienia dostępu podczas wybierania kolorów.
  
  ![Zrzut ekranu przedstawiający interfejs użytkownika obsługiwany dostosować motyw kolorów](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Określ dodatkowe opcje konfiguracji.  
  
  ![Zrzut ekranu przedstawiający kroku dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Aby uzyskać zaawansowane konfiguracje, kliknij przycisk **Pokaż zaawansowane konfiguracje**. Spowoduje to dodanie konfiguracji takich jak *cel łącza* do opcji wyszukiwania w sieci Web, *Włącz filtry* opcje obrazów i wideo, i *symbol zastępczy tekstu pole wyszukiwania* na różne Opcje.

  ![Zrzut ekranu przedstawiający kroku zaawansowane konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Wybierz klucze subskrypcji z listy rozwijanej. Alternatywnie możesz ręcznie wprowadzić klucz subskrypcji. Aby uzyskać informacji na temat uzyskiwania kluczy, zobacz [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![Zrzut ekranu przedstawiający kroku dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

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
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Dodaj element podziału wiersza i div do działania jako kontener.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. W **hostowanych interfejsu użytkownika** strony, przewiń w dół do sekcji **korzystanie z interfejsu użytkownika**. Kliknij przycisk *punktów końcowych* do uzyskania dostępu do fragmentu kodu JavaScript. Można także uzyskać się do fragmentu kodu, klikając **produkcji** i następnie **hostowanych interfejsu użytkownika** kartę.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
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
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **wwwroot** i kliknij przycisk **Pokaż w przeglądarce**.  
  
  ![Zrzut ekranu przedstawiający Eksploratora rozwiązań z wybraniu widoku w przeglądarce z poziomu menu kontekstowego wwwroot](./media/custom-search-webapp-view-in-browser.png)  

Strony sieci web wyszukiwania niestandardowego powinien wyglądać mniej więcej tak:

![Zrzut ekranu przedstawiający stronę sieci web wyszukiwania niestandardowego](./media/custom-search-webapp-browse-index.png)

Wykonywanie wyszukiwania renderuje wyniki w następujący sposób:

![Zrzut ekranu przedstawiający wyniki wyszukiwania niestandardowego](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywołanie punktu końcowego usługi Bing Custom Search (C#)](../call-endpoint-csharp.md)