---
title: Wywoływanie funkcji z usługi PowerApps | Dokumentacja firmy Microsoft
description: Tworzenie łącznika niestandardowego, a następnie wywołać funkcję za pomocą tego łącznika.
services: functions
keywords: aplikacje w chmurze, cloud services, usługi PowerApps, procesów biznesowych, aplikacji biznesowych
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 26f6502f63b39d3f1ecf8dfeb09c8df4daa63b68
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786111"
---
# <a name="call-a-function-from-powerapps"></a>Wywoływanie funkcji z usługi PowerApps
[PowerApps](https://powerapps.microsoft.com) platformy zaprojektowano pod kątem eksperci biznesowi tworzyć aplikacje bez konieczności pisania tradycyjnego kodu. Profesjonalni deweloperzy umożliwia rozszerzanie możliwości usługi PowerApps podczas osłaniania twórcy aplikacji usługi PowerApps z szczegóły techniczne usługi Azure Functions.

Utworzysz aplikację, w tym temacie, oparte na scenariuszu konserwacji dla turbiny wiatru. W tym temacie przedstawiono sposób wywołania funkcji, które zostały zdefiniowane w [Tworzenie definicji interfejsu OpenAPI dla funkcji](functions-openapi-definition.md). Funkcja określa, czy naprawa na turbiny wiatru ekonomiczne.

![Gotową aplikację w usłudze PowerApps](media/functions-powerapps-scenario/finished-app.png)

Aby uzyskać informacje na wywoływanie tej samej funkcji z Microsoft Flow, zobacz [wywoływanie funkcji z Microsoft Flow](functions-flow-scenario.md).

W tym temacie dowiesz się, jak:

> [!div class="checklist"]
> * Przygotuj przykładowych danych w programie Excel.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie z interfejsem API.
> * Utwórz aplikację, a następnie dodaj źródła danych.
> * Dodawanie formantów do wyświetlania danych w aplikacji.
> * Dodawanie formantów do wywołania funkcji i wyświetlania danych.
> * Uruchom aplikację, aby określić, czy naprawy jest opłacalna.

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

+ Aktywne [konta usługi PowerApps](https://docs.microsoft.com/powerapps/maker/signup-for-powerapps) przy użyciu tego samego konta w poświadczeniach jako konta platformy Azure. 
+ Program Excel i [Excel przykładowy plik](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) używaną jako źródło danych dla aplikacji.
+ Ukończ samouczek [Tworzenie definicji interfejsu OpenAPI dla funkcji](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Dodaj połączenie z interfejsem API
Niestandardowy interfejs API (znany także jako łącznik niestandardowy) jest dostępna w usłudze PowerApps, ale należy ustanowić połączenie z interfejsem API, zanim użyjesz go w aplikacji.

1. W [web.powerapps.com](https://web.powerapps.com), kliknij przycisk **połączeń**.

    ![Połączenia usługi PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Kliknij przycisk **nowe połączenie**, przewiń w dół do **naprawy turbiny** łącznika i kliknij ją.

    ![Nowe połączenie](media/functions-powerapps-scenario/new-connection.png)

1. Wprowadź klucz interfejsu API, a następnie kliknij przycisk **Utwórz**.

    ![Tworzenie połączenia](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Jeśli udostępniasz aplikację z innymi osobami, każdy, kto pracuje lub korzysta z aplikacji należy także podać klucz interfejsu API, aby nawiązać połączenie z interfejsu API. To zachowanie może ulec zmianie w przyszłości i będą aktualizowane w tym temacie w celu odzwierciedlenia zmiany.

## <a name="create-an-app-and-add-data-sources"></a>Utwórz aplikację, a następnie dodaj źródła danych
Teraz możesz przystąpić do tworzenia aplikacji w usłudze PowerApps, a następnie dodaj dane programu Excel i niestandardowy interfejs API jako źródła danych dla aplikacji.

1. W witrynie [web.powerapps.com](https://web.powerapps.com) wybierz pozycje **Zacznij od pustej aplikacji** > ![Ikona aplikacji na telefon](media/functions-powerapps-scenario/icon-phone-app.png) (telefon) > **Utwórz tę aplikację**.

    ![Zacznij od pustej aplikacji — aplikacji na telefon](media/functions-powerapps-scenario/create-phone-app.png)

    Aplikacja zostanie otwarta PowerApps Studio dla sieci web. Na poniższej ilustracji przedstawiono różne części PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) na lewym pasku nawigacyjnym**, w którym zobaczysz hierarchiczny widok wszystkich kontrolek na każdym ekranie

    **(B) środkowym okienku**, które pokazuje ekran pracujesz nad

    **W okienku po prawej stronie (C)**, w którym określane są opcje, takie jak układ i źródła danych

    **(D) właściwość** listy rozwijanej, wybierania właściwości, które dotyczą formuły

    **(E) pasek formuły**, w którym dodawane są formuły (jak w programie Excel) określające zachowanie aplikacji
    
    **(F) Wstążka**, służąca do dodawania kontrolek i dostosowywania elementów projektu

1. Dodaj plik programu Excel jako źródła danych.

    Dane, które zostaną zaimportowane wygląda podobnie do poniższego:

    ![Dane programu Excel do zaimportowania](media/functions-powerapps-scenario/excel-table.png)

    1. Na kanwie aplikacji wybierz pozycję **Połącz z danymi**.

    1. Na **danych** panelu, kliknij przycisk **Dodaj dane statyczne do aplikacji**.

        ![Dodawanie źródła danych](media/functions-powerapps-scenario/add-static-data.png)

        Zazwyczaj użytkownik może odczytywać i zapisywać dane z zewnętrznego źródła, ale dodajesz dane programu Excel jako statyczne dane ponieważ to jest przykładowy.

    1. Przejdź do pliku programu Excel został zapisany, wybierz **turbiny** tabeli, a następnie kliknij przycisk **Connect**.

        ![Dodawanie źródła danych](media/functions-powerapps-scenario/choose-table.png)


1. Dodaj niestandardowy interfejs API jako źródła danych.

    1. Na **danych** panelu, kliknij przycisk **Dodaj źródło danych**.

    1. Kliknij przycisk **naprawy turbiny**.

        ![Łącznik naprawy turbiny](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Dodawanie formantów do wyświetlania danych w aplikacji
Skoro źródeł danych są dostępne w aplikacji, aby można było wyświetlić dane turbiny możesz dodać ekran do aplikacji.

1. Na **Home** kliknij pozycję **nowy ekran** > **ekran listy**.

    ![Ekran listy](media/functions-powerapps-scenario/list-screen.png)

    Usługa PowerApps dodaje ekranu, który zawiera *galerii* do wyświetlania elementów i inne formanty, które umożliwiają wyszukiwanie, sortowanie i filtrowanie.

1. Zmienianie paska tytułu, aby `Turbine Repair`i zmień rozmiar galerii, więc ma miejsce dla większej liczby kontrolek na jej podstawie.

    ![Zmień tytuł i zmień rozmiar galerii](media/functions-powerapps-scenario/gallery-title.png)

1. Dzięki galerii wybrany w okienku po prawej stronie w obszarze **właściwości**, kliknij przycisk **CustomGallerySample**.

    ![Zmień źródło danych](media/functions-powerapps-scenario/change-data-source.png)

1. W **danych** panelu wybierz **turbiny** z listy.

    ![Wybierz źródło danych](media/functions-powerapps-scenario/select-data-source.png)

    Zestaw danych nie zawiera obrazu, tak aby następnie zmienisz układ, aby lepiej dopasować się dane. 

1. Nadal w **danych** panelu, zmień **układ** do **tytuł, podtytuł i treść**.

    ![Zmiana układu galerii](media/functions-powerapps-scenario/change-layout.png)

1. Jako ostatni etap **danych** panelu, zmiany pól, które są wyświetlane w galerii.

    ![Zmienianie pola galerii](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Tytuł2** = Title 

1. Przy wybranej galerii w ustaw **TemplateFill** właściwości na następującą formułę: `If(ThisItem.IsSelected, Orange, White)`.

    ![Formuła wypełnienie szablonu](media/functions-powerapps-scenario/formula-fill.png)

    Teraz łatwiej jest zobaczyć, który element galerii jest zaznaczony.

    ![Wybrany element](media/functions-powerapps-scenario/selected-item.png)

1. Nie potrzebujesz, oryginalnym ekranu w aplikacji. W okienku po lewej stronie, umieść kursor nad **Screen1**, kliknij przycisk **...** , i **Usuń**.

    ![Usuwanie ekranu](media/functions-powerapps-scenario/delete-screen.png)

1. Kliknij przycisk **pliku**i nadaj nazwę aplikacji. Kliknij przycisk **Zapisz** w menu po lewej stronie, następnie kliknij polecenie **Zapisz** w prawym dolnym rogu.

Istnieje wiele innych formatowanie, które zazwyczaj będzie można zrobić w aplikacji produkcyjnej, ale firma Microsoft będzie przejść do ważnym elementem w tym scenariuszu - wywołanie funkcji.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Dodawanie formantów do wywołania funkcji i wyświetlania danych
Masz aplikację, która wyświetla dane podsumowania każdego turbiny, więc teraz nadszedł czas, aby dodać kontrolki, które wywołanie funkcji, który został utworzony i wyświetlania danych, która jest zwracana. Możesz uzyskać dostęp do funkcji, oparte na sposób, w przypadku jej nazwy w definicji interfejsu OpenAPI; w tym przypadku ma `TurbineRepair.CalculateCosts()`.

1. Na wstążce na **Wstaw** kliknij pozycję **przycisk**. Następnie na tej samej karcie kliknij **etykiety**

    ![Wstaw przycisków i etykiet](media/functions-powerapps-scenario/insert-controls.png)

1. Przeciągnij przycisk i etykietę pod galerię, a następnie zmień rozmiar etykiet. 

1. Wybierz tekst przycisku i zmień go na `Calculate costs`. Aplikacja powinna wyglądać podobnie do następującego.

    ![Aplikacji za pomocą przycisku](media/functions-powerapps-scenario/move-button-label.png)

1. Wybierz przycisk, a następnie wprowadź następującą formułę dla przycisku **OnSelect** właściwości.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Ta formuła jest wykonywana po kliknięciu przycisku, a następnie wykonuje następujące czynności, jeśli wybranego elementu galerii ma **ServiceRequired** wartość `Yes`:

    + Czyści *kolekcji* `DetermineRepair` usunięcie danych z poprzedniego wywołania. Kolekcja jest zmiennej tabelarycznej.

    + Przypisuje do kolekcji danych zwracanych przez wywołanie funkcji `TurbineRepair.CalculateCosts()`. 
    
        Wartość przekazywana do funkcji pochodzą z **EstimatedEffort** i **MaxOutput** pola dla elementu zaznaczonego w galerii. Te pola nie są wyświetlane w galerii, ale są nadal dostępne do użycia w formułach.

1. Wybierz etykietę, a następnie wprowadź następującą formułę w etykiecie **tekstu** właściwości.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Ta formuła `First()` funkcji uzyskanie dostępu pierwszą (i jedyną) wiersza `DetermineRepair` kolekcji. Następnie wyświetla trzy wartości, które zwraca funkcja: `message`, `costToFix`, i `revenueOpportunity`. Te pola są puste, zanim aplikacja zostanie uruchomiona po raz pierwszy.

    Ukończonej aplikacji powinien wyglądać podobnie do następującego.

    ![Zakończona aplikacja przed uruchomieniem](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Uruchamianie aplikacji
Masz pełnej aplikacji. Teraz nadszedł czas, aby go uruchomić i sprawdzić, czy funkcja wywołuje w działaniu.

1. W prawym górnym rogu programu PowerApps Studio kliknij przycisk uruchamiania: ![Przycisk Uruchom aplikację](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Wybierz turbiny o wartości `Yes` dla **ServiceRequired**, następnie kliknij przycisk **obliczania kosztów** przycisku. Powinny pojawić się wyniki, takie jak na poniższej ilustracji.

    ![Gotową aplikację w usłudze PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Spróbuj innych turbiny, aby zobaczyć, co jest zwracany przez funkcję każdorazowo.

## <a name="next-steps"></a>Kolejne kroki
W tym temacie przedstawiono sposób:

> [!div class="checklist"]
> * Przygotuj przykładowych danych w programie Excel.
> * Eksportowanie definicji interfejsu API.
> * Dodaj połączenie z interfejsem API.
> * Utwórz aplikację, a następnie dodaj źródła danych.
> * Dodawanie formantów do wyświetlania danych w aplikacji.
> * Dodawanie formantów do wywołania funkcji i wyświetlania danych
> * Uruchom aplikację, aby określić, czy naprawy jest opłacalna.

Aby dowiedzieć się więcej o usłudze PowerApps, zobacz [wprowadzenie do usługi PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Aby dowiedzieć się o innych ciekawych scenariuszy, które używają usługi Azure Functions, zobacz [wywoływanie funkcji z Microsoft Flow](functions-flow-scenario.md) i [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](functions-twitter-email.md).
