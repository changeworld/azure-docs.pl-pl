---
title: 'Wyszukiwanie niestandardowe Bing: Tworzenie strony sieci web wyszukiwania niestandardowego | Dokumentacja firmy Microsoft'
description: Opisuje sposób konfigurowania wystąpienia wyszukiwania niestandardowego i zintegrować ją z sieci Web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347881"
---
# <a name="build-a-custom-search-web-page"></a>Strona sieci web wyszukiwania niestandardowej kompilacji
Wyszukiwanie niestandardowe Bing umożliwia utworzenie środowiska wyszukiwania dopasowane do tematów, które są dla Ciebie ważne. Na przykład jeśli masz wojskowe zapewniającej środowiska wyszukiwania można określić domeny, Lokacje podrzędne i stron sieci Web, która wyszukuje Bing. Użytkownicy zobaczą wyniki wyszukiwania dostosowane do zawartości, która ich interesujących zamiast stronicowania za pośrednictwem wyniki wyszukiwania, zawierające zawartości nie ma znaczenia. 

W tym samouczku przedstawiono sposób konfigurowania wystąpienia wyszukiwania niestandardowego i włączenie go do nowej strony sieci web.

Zadania objęte są:

> [!div class="checklist"]
> - Utwórz wystąpienie wyszukiwania niestandardowego
> - Dodaj aktywne wpisy
> - Dodaj zablokowanych wpisów
> - Dodaj przypiętych wpisów
> - Integrowanie wyszukiwanie niestandardowe strony sieci web

## <a name="prerequisites"></a>Wymagania wstępne
- Aby skorzystać z tego samouczka, należy klucza subskrypcji dla interfejsu API wyszukiwania usługi Bing niestandardowe.  Aby uzyskać klucz, zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Jeśli nie masz jeszcze zainstalowanego programu Visual Studio 2017, możesz pobrać program [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) i używać go **bezpłatnie**.

## <a name="create-a-custom-search-instance"></a>Utwórz wystąpienie wyszukiwania niestandardowego
Aby utworzyć wystąpienie wyszukiwania usługi Bing niestandardowe:

1.  Otwórz przeglądarkę internetową.
2.  Przejdź do wyszukiwania niestandardowego [portal](https://customsearch.ai).
3.  Zaloguj się do portalu przy użyciu konta Microsoft (MSA). Jeśli nie masz zarządzanych kont usług, kliknij przycisk **utworzyć konto Microsoft**. Jeśli jest używany po raz pierwszy przy użyciu portalu, poprosi o uprawnienia dostępu do danych. Kliknij przycisk **Yes** (Tak).
4.  Po zalogowaniu kliknij **nowe wyszukiwanie niestandardowych**. W **utworzenia nowego wystąpienia wyszukiwania niestandardowego** okna, wprowadź nazwę, która ma znaczenie oraz opis typu zawartości, ponieważ funkcja wyszukiwania. Nazwę można zmienić w dowolnym momencie.
 
    ![Zrzut ekranu: Tworzenie nowego pola wystąpienia niestandardowego wyszukiwania](../media/newCustomSrch.png)

5.  Kliknij przycisk OK, określ adres URL i czy dołączać podstrony strony podstawowej:

    ![Zrzut ekranu strony definicji adresu URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Dodaj aktywne wpisy
Aby dołączyć wyniki z określonej witryny lub adresy URL, dodaj je do **Active** kartę.

1.  W **Edytor definicji**, kliknij przycisk **Active** i wprowadzić adres URL co najmniej jednej lokacji, które chcesz uwzględnić w wyszukiwaniu.

    ![Zrzut ekranu: karta active Edytor definicji](../media/customSrchEditor.png)

2.  Aby upewnić się, że wystąpienie zwraca wyniki, wprowadź kwerendę w okienku podglądu po prawej stronie. Bing zwraca wyniki tylko dla publicznych witryn, które ma on indeksowane.

## <a name="add-blocked-entries"></a>Dodaj zablokowanych wpisów
Aby wykluczyć wyniki z określonej witryny lub adresy URL, dodaj je do **zablokowane** kartę.

1. W **Edytor definicji**, kliknij przycisk **zablokowane** i wprowadzić adres URL co najmniej jednej lokacji, które chcesz wykluczyć z wyszukiwania.

    ![Zrzut ekranu: Edytor definicji zablokowane kartę](../media/blockedCustomSrch.png)


2. Aby potwierdzić zamiar wystąpienia nie zwraca wyników z zablokowanych witryn, wprowadź kwerendę w okienku podglądu po prawej stronie. 

## <a name="add-pinned-entries"></a>Dodaj przypiętych wpisów
Aby przypiąć określonej strony sieci Web na początku wyniki wyszukiwania, Dodaj wyrażenie strony sieci Web i zapytań, aby **Pinned** kartę.  **Pinned** karta zawiera listę pary terminu strony sieci Web i zapytania, które Określ strony sieci Web, który jest wyświetlany jako wynik top dla określonej kwerendy. Użytkownika wyszukiwanego terminu musi dokładnie odpowiadać przypiętych wyszukiwanego terminu strony sieci Web przypinać do góry.

1. W **Edytor definicji**, kliknij przycisk **Pinned** i wprowadzić termin strony sieci Web i zapytań strony sieci Web, która powinna zwrócić wyniku top.

    ![Zrzut ekranu: Edytor definicji przypięte karty](../media/pinnedCustomSrch.png)

2. Aby upewnić się, czy wystąpienia zwraca określona strona sieci Web w wyniku najwyższego, wprowadź wyszukiwanego terminu, który jest przypięte w okienku podglądu po prawej stronie.

## <a name="configure-hosted-ui"></a>Skonfiguruj hostowanej interfejsu użytkownika
Wyszukiwanie niestandardowe zapewnia hostowanej interfejsu użytkownika do renderowania odpowiedzi JSON wystąpienia wyszukiwania niestandardowego.  Aby zdefiniować środowiska interfejsu użytkownika:

1. Kliknij przycisk **hostowanej interfejsu użytkownika** kartę.
2. Wybierz układ.

    ![Zrzut ekranu interfejsu użytkownika obsługiwanych wybierz układ krok](./media/custom-search-hosted-ui-select-layout.png)

3. Wybierz motyw kolorów.

    ![Zrzut ekranu interfejsu użytkownika obsługiwanych wybierz układ krok](./media/custom-search-hosted-ui-select-color-theme.png)

4. Określ dodatkowe opcje konfiguracji.

    ![Zrzut ekranu przedstawiający krok dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-additional-configurations.png)

5. Wklej Twojej **klucza subskrypcji**. Zobacz [spróbuj kognitywnych usług](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Zrzut ekranu przedstawiający krok dodatkowe konfiguracje obsługiwane interfejsu użytkownika](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Korzystanie z hostowanej interfejsu użytkownika
Istnieją dwa sposoby korzystania z obsługiwanego interfejsu użytkownika.  

- Opcja 1: Integrować podany fragment kodu JavaScript w aplikacji.
- Opcja 2: Użyj punktu końcowego HTML pod warunkiem.

W pozostałej części tego samouczka przedstawiono **opcja 1: fragment kodu Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Konfigurowanie rozwiązania Visual Studio
1. Otwórz program **Visual Studio** na komputerze.
2. W menu **Plik** wybierz polecenie **Nowy**, a następnie wybierz pozycję **Projekt**.
3. W **nowy projekt** wybierz **Visual C# / Web / aplikacji sieci Web platformy ASP.NET Core**, nazwy projektu, a następnie kliknij przycisk **OK**.
   
    ![Zrzut ekranu okna nowy projekt](./media/custom-search-new-project.png)

4. W **nową aplikację sieci Web Core ASP.NET** wybierz **aplikacji sieci Web** i kliknij przycisk **OK**.
    
    ![Zrzut ekranu okna nowy projekt](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Edytuj index.cshtml
1. W **Eksploratora rozwiązań**, rozwiń węzeł **stron** i kliknij dwukrotnie **index.cshtml** można otworzyć pliku.

    ![Zrzut ekranu przedstawiający Eksploratora rozwiązań ze stron rozwinięty i index.cshtml wybrane](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. W index.cshtml Usuń całą sekcję od wiersza 7 i poniżej.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Dodaj element podział wiersza i div do działania jako kontener.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Z **hostowanej interfejsu użytkownika** kartę, przewiń w dół do sekcji **korzystanie z interfejsu użytkownika**. Skopiuj fragment kodu JavaScript.

    ![Zrzut ekranu interfejsu użytkownika obsługiwanych przyciskiem Zapisz](./media/custom-search-hosted-ui-consuming-ui.png)

5. Wklej element skryptu w kontenerze dodane.

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

6. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **wwwroot** i kliknij przycisk **Wyświetl w przeglądarce**.

    ![Zrzut ekranu przedstawiający wybór widoku w przeglądarce z menu kontekstowego wwwroot Eksploratora rozwiązań](./media/custom-search-webapp-view-in-browser.png)

Nowa strona sieci web wyszukiwania niestandardowego powinny wyglądać podobnie do poniższego:

![Zrzut ekranu strony sieci web wyszukiwania niestandardowego](./media/custom-search-webapp-browse-index.png)

Wykonywanie wyszukiwania renderuje wyniki w następujący sposób.

![Zrzut ekranu: wyniki wyszukiwania niestandardowego](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku wykonano następujące czynności:

> [!div class="checklist"]
> - Utworzone wystąpienie wyszukiwania niestandardowego
> - Aktywne wpisy dodany
> - Dodaje wpisy zablokowanych
> - Dodaje wpisy przypiętych
> - Zintegrowane wyszukiwanie niestandardowe strony sieci web

Możesz teraz przejść do wywoływania wyszukiwania usługi Bing niestandardowe punkty końcowe programowo.

> [!div class="nextstepaction"]
> [Wywołanie wyszukiwania usługi Bing niestandardowe punktu końcowego (C#)](../call-endpoint-csharp.md)