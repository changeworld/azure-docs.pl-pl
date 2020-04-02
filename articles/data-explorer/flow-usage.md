---
title: Przykłady użycia łącznika programu Microsoft Azure Data Explorer (Preview)
description: Poznaj kilka typowych przykładów użycia łącznika programu Microsoft Flow.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521674"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Przykłady użycia łącznika programu Microsoft Flow (Wersja zapoznawcza)

Łącznik przepływu eksploratora danych platformy Azure umożliwia eksploratorowi danych platformy Azure korzystanie z [funkcji przepływu automatyzacji systemu Microsoft Power Automate](https://flow.microsoft.com/) do automatycznego uruchamiania zapytań i poleceń Kusto w ramach zaplanowanego lub wyzwalanego zadania. Ten dokument zawiera kilka typowych przykładów użycia łącznika programu Microsoft Flow.

Aby uzyskać więcej informacji, zobacz [Łącznik usługi Microsoft Flow (Wersja zapoznawcza)](flow.md).

* [Łącznik i sql usługi Microsoft Flow](#microsoft-flow-connector-and-sql)
* [Wypychanie danych do zestawu danych usługi Power BI](#push-data-to-power-bi-dataset)
* [Zapytania warunkowe](#conditional-queries)
* [Wysyłanie poczty e-mail do wielu wykresów przepływu usługi Azure Data Explorer](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Łącznik i sql usługi Microsoft Flow

Za pomocą łącznika usługi Microsoft Flow należy wysyłać zapytania do danych i agregować je w bazie danych SQL.

> [!Note]
> Wstawianie SQL odbywa się oddzielnie dla każdego wiersza. Złącza Microsoft Flow należy używać tylko dla niewielkich ilości danych wyjściowych. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> W polu *Nazwa klastra* wprowadź adres URL klastra.

## <a name="push-data-to-power-bi-dataset"></a>Wypychanie danych do zestawu danych usługi Power BI

Łącznik Microsoft Flow może być używany razem ze złączem usługi Power BI do wypychania danych z zapytań Kusto do zestawów danych strumieniowych usługi Power BI.

1. Utwórz nową akcję Uruchom kwerendę i listę wyników.
1. Wybierz pozycję **Nowy krok**.
1. Wybierz **pozycję Dodaj akcję** i wyszukaj program Power BI.
1. Wybierz pozycję **Power BI**.
1. Wybierz **pozycję Dodaj wiersze do zestawu danych**. 

    ![Złącze Flow Power BI](./media/flow-usage/flow-powerbiconnector.png)
1. Wprowadź **obszar roboczy**, **zestaw danych**i **tabelę,** do której zostaną wypchnięte dane.
1. W oknie dialogowym zawartości dynamicznej dodaj ładunek zawierający schemat zestawu danych i odpowiednie wyniki zapytania Kusto.

    ![Pola usługi Flow Power BI](./media/flow-usage/flow-powerbifields.png)

Flow automatycznie stosuje akcję usługi Power BI dla każdego wiersza tabeli wyników kwerendy Kusto. 

![Akcja Flow Power BI dla każdego wiersza](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Zapytania warunkowe

Wyniki zapytań Kusto mogą być używane jako dane wejściowe lub warunki dla następnych akcji przepływu.

W poniższym przykładzie zapytania Kusto dla incydentów, które wystąpiły w ciągu ostatniego dnia. Dla każdego rozwiązanego zdarzenia jest publikowany komunikat zapasowy i tworzone jest powiadomienie wypychane.
Dla każdego incydentu, który jest nadal aktywny, Kusto jest pytany o więcej informacji na temat podobnych incydentów. Wysyła te informacje jako wiadomość e-mail i otwiera powiązane zadanie TFS.

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć podobny przepływ:

1. Utwórz nową akcję Uruchom kwerendę i listę wyników.
1. Wybierz pozycję **Nowy krok**.
1. Wybierz **pozycję Formant warunków**.
1. W oknie zawartości dynamicznej wybierz parametr, którego chcesz użyć jako warunku dla następnych akcji.
1. Wybierz typ *relacji* i *wartości,* aby ustawić określony warunek dla danego parametru.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Przepływ stosuje ten warunek w każdym wierszu tabeli wyników kwerendy.
1. Dodaj akcje, gdy warunek jest true i false.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

Wartości wyników z kwerendy Kusto można użyć jako danych wejściowych dla następnych akcji. Wybierz wartości wyników z dynamicznego okna zawartości.
W poniższym przykładzie slack — post message akcji i Visual Studio — tworzenie nowej akcji elementu pracy zawierającej dane z kwerendy Kusto zostały dodane.

![Slack - Akcja księgowania wiadomości](./media/flow-usage/flow-slack.png)

![Akcja programu Visual Studio](./media/flow-usage/flow-visualstudio.png)

W tym przykładzie jeśli zdarzenie jest nadal aktywne, zapytaj Kusto ponownie, aby uzyskać informacje o tym, jak zdarzenia z tego samego źródła zostały rozwiązane w przeszłości.

![Kwerenda warunku przepływu](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> W polu *Nazwa klastra* wprowadź adres URL klastra.

Wizualizuj te informacje jako wykres kołowy i wyślij go pocztą e-mail do zespołu.

![Wiadomość e-mail z warunkiem przepływu](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Wysyłanie poczty e-mail do wielu wykresów przepływu usługi Azure Data Explorer

1. Utwórz nowy przepływ za pomocą wyzwalacza cyklu i zdefiniuj interwał przepływu i częstotliwości. 
1. Dodaj nowy krok, z jednym lub więcej Kusto - Uruchom kwerendę i wizualizuj akcje wyników. 

    ![Uruchamianie kilku zapytań w przepływie](./media/flow-usage/flow-severalqueries.png)
1. Dla każdego Kusto - Uruchom kwerendę i wizualizuj wynik, zdefiniuj następujące pola:
    * Adres URL klastra
    * Nazwa bazy danych
    * Typ kwerendy i wykresu (tabela HTML, wykres kołowy, wykres czasu, wykres słupkowy lub wprowadź wartość niestandardową).

    ![Wizualizuj wyniki za pomocą wielu załączników](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. Dodaj akcję Wyślij wiadomość e-mail (w wersji 2):Add a Send an email (v2) action: 
    1. W sekcji treści wybierz ikonę widoku kodu.
    1. W polu **Treść** wstaw wymaganą karmę, aby zwizualizowany wynik kwerendy został uwzględniony w treści wiadomości e-mail.
    1. Aby dodać załącznik do wiadomości e-mail, dodaj nazwę załącznika i zawartość załącznika.
    
    ![Wysyłanie wiadomości e-mail z wieloma załącznikami](./media/flow-usage/flow-email-multiple-attachments.png)

    Aby uzyskać pełne instrukcje dotyczące tworzenia akcji e-mail, zobacz [Wyniki zapytania E-mail Kusto](flow.md#email-kusto-query-results). 

Wyniki:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łączniku aplikacji logiki programu Microsoft Azure Explorer,](https://docs.microsoft.com/azure/kusto/tools/logicapps) który jest innym sposobem automatycznego uruchamiania zapytań i poleceń Kusto w ramach zaplanowanego lub wyzwolonego zadania.
