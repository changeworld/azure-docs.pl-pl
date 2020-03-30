---
title: Łączenie środowiska z usługą Power BI — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć usługę Azure Time Series Insights z usługą Power BI w celu udostępniania, tworzenia wykresów i wyświetlania danych w całej organizacji.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863846"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Wizualizuj dane z usługi Time Series Insights w usłudze Power BI

Usługa Azure Time Series Insights to platforma do przechowywania, zarządzania, wykonywania zapytań i wizualizacji danych szeregów czasowych w chmurze. [Usługa Power BI](https://powerbi.microsoft.com) to narzędzie do analizy biznesowej z bogatymi funkcjami wizualizacji, które umożliwia udostępnianie szczegółowych informacji i wyników w całej organizacji. Obie usługi można teraz zintegrować, aby uzyskać najlepsze możliwości wizualizacji zarówno usługi Time Series Insights, jak i usługi Power BI.

Omawiane tematy:

* Łączenie usługi Time Series Insights z usługą Power BI przy użyciu łącznika w chmurze
* Tworzenie wizualizacji za pomocą danych w usłudze Power BI
* Publikowanie raportu w usłudze Power BI i udostępnianie go pozostałej części organizacji

Pod koniec dowiesz się, jak wizualizować dane szeregów czasowych za pośrednictwem usługi Azure Time Series Insights i ulepszyć go dzięki silnej wizualizacji danych i łatwym możliwościom udostępniania usługi Power BI.

Jeśli jeszcze jej nie masz, zarejestruj się w [bezpłatnej subskrypcji platformy Azure.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Wymagania wstępne

* Pobieranie i instalowanie najnowszej wersji programu [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Tworzenie wystąpienia [usługi Azure Time Series Preview](time-series-insights-update-how-to-manage.md)

> [!IMPORTANT]
> Złącze usługi Power BI jest obecnie obsługiwane w środowiskach płatności zgodnie *z rzeczywistymi oczekiwaniami* usługi Time Series Insights Preview skonfigurowanych dla **magazynu Warm Store.**

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Łączenie danych z usługi Time Series Insights z programem Power BI

Aby połączyć środowisko usługi Time Series Insights z programem Power BI, wykonaj następujące czynności:

1. Otwórz Eksploratora wglądu w szeregi czasowe
1. Eksportowanie danych jako kwerendy lub jako nieprzetworzonych danych
1. Otwieranie programu Power BI Desktop
1. Ładowanie z kwerendy niestandardowej

### <a name="export-data-into-power-bi-desktop"></a>Eksportowanie danych do pulpitu usługi Power BI

Aby rozpocząć:

1. Otwórz Eksploratora podglądu aplikacji Time Series Insights i wyselekcjonuj dane.
1. Po utworzeniu widoku, z którego użytkownik jest zadowolony, przejdź do menu rozwijanego **Więcej akcji** i wybierz pozycję **Połącz z programem Power BI**.

    [![Eksport explorera usługi Time Series Insights Preview](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Ustaw parametry wewnątrz tej karty:

   1. Określ względny przedział czasu do wyświetlenia. Jeśli jesteś zadowolony z istniejącego widoku, pozostaw to jako **istniejące ramy czasowe**.
   
   1. Wybierz zdarzenia **zagregowane** i **nieprzetworzone**. 
   
       > [!NOTE]
       > Zawsze można agregować dane później w usłudze Power BI, ale nie można przywrócić nieprzetworzonych danych po agregacji. 
       
       > [!NOTE]
       > Istnieje limit liczby zdarzeń 100-K dla danych poziomu zdarzenia raw.

       [![Połącz](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Jeśli nie skonfigurowano wystąpienia usługi Time Series Insights dla **magazynu Warm Store,** zostanie wyświetlone ostrzeżenie.

       [![Ostrzeżenie o ciepłym przechowywanie](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > Można skonfigurować istniejące wystąpienie **dla Ciepłego magazynu** w witrynie Azure portal.

1. Wybierz **pozycję Kopiuj kwerendę do schowka**.
1. Teraz uruchom program Power BI Desktop.
1. W programie Power BI Desktop na karcie **Narzędzia główne** wybierz pozycję **Pobierz dane** w lewym górnym rogu, a następnie pozycję **Więcej**.

    [![Strona główna rozwijana](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Wyszukaj **wgląd w usługi Time Series Insights**, wybierz pozycję Usługa Azure Time Series Insights **(Beta),** a następnie **Connect**.

    [![Łączenie usługi Power BI z analizą szeregów czasowych](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    Możesz też przejść do karty **Azure,** wybierz pozycję **Usługa Azure Time Series Insights (Beta),** a następnie **connect**.
    
1. Zostanie wyświetlone okno dialogowe wiadomości z prośbą o pozwolenie na połączenie się z zasobami innych firm. Wybierz **przycisk Kontynuuj**.

    [![Wybierz pozycję Utwórz kwerendę niestandardową](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. W menu rozwijanym w obszarze **Źródło danych**wybierz pozycję **Utwórz kwerendę niestandardową**. Wklej ze schowka do opcjonalnego pola **Kwerenda niestandardowa (opcjonalnie)** poniżej, a następnie naciśnij **przycisk OK**.

    [![Przekaż kwerendę niestandardową i wybierz przycisk OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. Tabela danych zostanie teraz załadowana. Naciśnij **przycisk Load,** aby załadować do usługi Power BI.

    [![Przejrzyj załadowane dane w tabeli i wybierz pozycję Załaduj](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Jeśli wykonasz te kroki, przejdź do następnej sekcji.

## <a name="create-a-report-with-visuals"></a>Tworzenie raportu z wizualizacjami

Teraz, gdy dane zostały zaimportowane do usługi Power BI, nadszedł czas, aby utworzyć raport z wizualizacjami.

1. Po lewej stronie okna upewnij się, że wybrano widok **Raport.**

    [![Wybierz widok raportu](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  W kolumnie **Wizualizacje** wybierz wizualizację. Na przykład wybierz **opcję Wykres liniowy**. Spowoduje to dodanie pustego wykresu liniowego do kanwy.
 
1.  Na liście **Pola** wybierz pozycję **Sygnatura czasowa** i przeciągnij ją do pola **Osi,** aby wyświetlić elementy wzdłuż osi X.

1.  Ponownie na liście **Pola** wybierz **timeseriesId** i przeciągnij go do pola **Wartości,** aby wyświetlić elementy wzdłuż osi Y.

    [![Tworzenie wykresu liniowego](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Aby dodać kolejny wykres do kanwy, zaznacz dowolne miejsce na kanwie poza wykresem liniowym i powtórz ten proces.

    [![Tworzenie dodatkowych wykresów do udostępnienia](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Po utworzeniu raportu można go opublikować w usługach Power BI Reporting Services.

## <a name="advanced-editing"></a>Edycja zaawansowana

Jeśli zestaw danych został już załadowany w usłudze Power BI, ale chcesz zmodyfikować kwerendę (na przykład datę/godzinę lub parametry identyfikatora środowiska), można to zrobić za pomocą funkcji zaawansowanego edytora usługi Power BI. Więcej informacji można znaleźć w [dokumentacji usługi Power BI.](https://docs.microsoft.com/power-bi/desktop-query-overview)

W ramach przeglądu:

1. W programie Power BI Desktop wybierz pozycję **Edytuj kwerendy**.
1. Naciśnij **przycisk Zaawansowany Edytor**.

    [![Edytowanie zapytań w Edytorze zaawansowanym](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Zmodyfikuj ładunek JSON zgodnie z potrzebami.
1. Wybierz **pozycję Gotowe,** a następnie **zamknij & zastosuj** w oknie **Edytora dodatków .**

Interfejs będzie teraz odzwierciedlał zastosowane zmiany.  

## <a name="next-steps"></a>Następne kroki

* Przeczytaj o [pojęciach łączników usługi Power BI](https://docs.microsoft.com/power-bi/desktop-query-overview) dla usługi Azure Time Series Insights.

* Dowiedz się więcej o [programie Power BI desktop](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Przeczytaj [eksploratora ga szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) i [eksploratora analizy szeregów czasowych](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
