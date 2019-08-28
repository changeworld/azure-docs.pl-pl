---
title: Wizualne monitorowanie fabryk danych platformy Azure | Microsoft Docs
description: Dowiedz się, jak wizualnie monitorować fabryki danych platformy Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: e680610263830c856a3ff902d8e55cd8b2b4a158
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114600"
---
# <a name="visually-monitor-azure-data-factories"></a>Wizualne monitorowanie fabryk danych platformy Azure
Azure Data Factory to oparta na chmurze usługa integracji danych. Za jego pomocą można tworzyć oparte na danych przepływy pracy w chmurze służące do organizowania i automatyzowania przenoszenia i przekształcania danych. Za pomocą Azure Data Factory można:

- Tworzyć oparte na danych przepływy pracy (nazywane potokami) pozyskujące dane z różnych magazynów danych i ustalać ich harmonogram.
- Przetwarzaj/Przekształcaj dane przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning.
- Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

W tym przewodniku szybki start dowiesz się, jak wizualnie monitorować potoki Data Factory bez pisania pojedynczego wiersza kodu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="monitor-data-factory-pipelines"></a>Monitorowanie potoków Data Factory

Monitoruj przebiegi potokowe i działania przy użyciu prostego interfejsu widoku listy. Wszystkie uruchomienia są wyświetlane w lokalnej strefie czasowej przeglądarki. Jeśli zmienisz strefę czasową, wszystkie pola daty i godziny są przyciągane do wybranego.  

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w tych dwóch przeglądarkach sieci Web.
2. Zaloguj się do [Azure Portal](https://portal.azure.com/).
3. Przejdź do bloku utworzonej fabryki danych w Azure Portal. Wybierz pozycję **monitoruj & Zarządzaj** kafelkiem, aby rozpocząć Data Factory środowisko monitorowania wizualnego.

## <a name="monitor-pipeline-runs"></a>Monitorowanie uruchomień potoku
Widok listy pokazuje Każde uruchomienie potoku dla potoków Data Factory. Zawiera następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku |
| Akcje | Pojedyncza Akcja dostępna do wyświetlania uruchomień działań |
| Uruchom uruchomienie | Data i godzina rozpoczęcia dla uruchomienia potoku (MM/DD/RRRR, gg: MM: SS AM/PM) |
| Duration | Czas trwania uruchomienia (HH: MM: SS) |
| Wyzwolone przez | Wyzwalacz ręczny lub zaplanowany wyzwalacz |
| State | **Zakończone niepowodzeniem**, **zakończone powodzeniem**lub **w toku** |
| Parametry | Parametry uruchomienia potoku (pary nazwa/wartość) |
| Błąd | Błąd uruchomienia potoku (jeśli istnieje) |
| Identyfikator uruchomienia | Identyfikator uruchomienia potoku |

![Widok listy na potrzeby monitorowania przebiegów potoku](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorowanie uruchomień działania
Widok listy pokazuje uruchomienia działania odpowiadające poszczególnym uruchomieniem potoku. Aby wyświetlić uruchomienia działań dla każdego uruchomienia potoku, wybierz ikonę **uruchomienia działania** w kolumnie **Akcje** . Widok listy zawiera następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania w potoku |
| Typ aktywności | Typ działania, na przykład **copy**, **HDInsightSpark**lub **HDInsightHive** |
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia działania (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Duration | Czas trwania uruchomienia (HH: MM: SS) |
| State | **Zakończone niepowodzeniem**, **zakończone powodzeniem**lub **w toku** |
| Dane wejściowe | Tablica JSON opisująca dane wejściowe działania |
| Output | Tablica JSON opisująca dane wyjściowe działania |
| Błąd | Błąd uruchamiania działania (jeśli istnieje) |

![Widok listy dla uruchomionych działań monitorowania](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Musisz wybrać przycisk **Odśwież** u góry, aby odświeżyć listę uruchomień potoków i działań. Automatyczne odświeżanie nie jest obecnie obsługiwane.

![Przycisk Odśwież](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Wybierz fabrykę danych do monitorowania
Umieść kursor nad ikoną **Data Factory** w lewym górnym rogu. Wybierz ikonę strzałki, aby wyświetlić listę subskrypcji platformy Azure i fabryk danych, które można monitorować.

![Wybieranie fabryki danych](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Skonfiguruj widok listy

### <a name="apply-rich-ordering-and-filtering"></a>Zastosuj rozbudowane porządkowanie i filtrowanie

Kolejność przebiegów potoku w OPISie/ASC według czasu rozpoczęcia przebiegu. Filtry przebiegów potoku przy użyciu następujących kolumn:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku. Opcje obejmują szybkie filtry dla **ostatnich 24 godzin**, **ubiegłych tygodni**i **ostatnich 30 dni**. Lub wybierz niestandardową datę i godzinę. |
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia potoku. |
| Stan uruchomienia | Filtry przebiega według stanu: **Zakończone powodzeniem**, **zakończone niepowodzeniem**lub **w toku**. |

![Opcje filtrowania](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Dodawanie lub usuwanie kolumn
Kliknij prawym przyciskiem myszy nagłówek widoku listy i wybierz kolumny, które mają być wyświetlane w widoku listy.

![Opcje kolumn](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Dopasuj szerokości kolumn
Zwiększ i zmniejsz szerokości kolumn w widoku listy, umieszczając kursor na nagłówku kolumny.

## <a name="promote-user-properties-to-monitor"></a>Podnieś poziom właściwości użytkownika do monitorowania

Każdą właściwość działania potoku można podwyższyć jako właściwość użytkownika, aby stała się ona jednostką, którą można monitorować. Na przykład można podwyższyć poziom właściwości źródłowej i **docelowej** działania kopiowania w potoku jako właściwości użytkownika. Możesz również wybrać pozycję **Generuj automatycznie** , aby wygenerować właściwości źródłowe i **docelowe** dla działania kopiowania.

![Tworzenie właściwości użytkownika](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Można podwyższyć do pięciu właściwości działania potoku jako właściwości użytkownika.

Po utworzeniu właściwości użytkownika można je monitorować w widokach listy monitorowania. Jeśli źródłem działania kopiowania jest nazwa tabeli, można monitorować nazwę tabeli źródłowej jako kolumnę w widoku listy dla uruchomień działania.

![Lista uruchomień działań bez właściwości użytkownika](media/monitor-visually/monitor-user-properties-image2.png)

![Dodawanie kolumn do właściwości użytkownika na liście uruchomień działania](media/monitor-visually/monitor-user-properties-image3.png)

![Lista uruchomień działania z kolumnami dla właściwości użytkownika](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Uruchom ponownie działania wewnątrz potoku

Można teraz ponownie uruchamiać działania wewnątrz potoku. Wybierz pozycję **Wyświetl uruchomienia działania**, a następnie wybierz działanie w potoku, z którego chcesz ponownie uruchomić potok.

![Wyświetlanie uruchomień działania](media/monitor-visually/rerun-activities-image1.png)

![Wybierz przebieg działania](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Wyświetl historię ponownego uruchamiania

Można wyświetlić historię ponownego uruchamiania dla wszystkich uruchomień potoku w widoku listy.

![Wyświetlanie historii](media/monitor-visually/rerun-history-image1.png)

Możesz również wyświetlić historię ponownego uruchamiania dla określonego uruchomienia potoku.

![Wyświetl historię uruchomienia potoku](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Widoki wykresu Gantta

Użyj widoków wykresu Gantta, aby szybko wizualizować potoki i uruchomienia działania. Widok wykresu Gantta można przeglądać na potok lub grupować według adnotacji/tagów utworzonych w potokach.

![Przykład wykresu Gantta](media/monitor-visually/gantt1.png)

![Adnotacje wykresu Gantta](media/monitor-visually/gantt2.png)

Długość paska informuje o czasie trwania potoku. Możesz również wybrać pasek, aby wyświetlić więcej szczegółów.

![Czas trwania wykresu Gantta](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Przewodniki
Wybierz ikonę **informacji** w lewym dolnym rogu. Następnie wybierz przewodniki instruktażowe, aby uzyskać instrukcje krok po kroku dotyczące monitorowania uruchomienia potoku i działania.

![Przewodniki](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Opinia
Wybierz ikonę **opinii** , aby przekazać nam swoją opinię na temat różnych funkcji lub wszelkich problemów, które mogą się pojawić.

![Opinia](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alerty

Alerty dla obsługiwanych metryk można zgłaszać w Data Factory. Wybierz pozycję **Monitoruj** > **alerty & metryki** na stronie monitorowanie Data Factory, aby rozpocząć pracę.

![Strona monitora fabryki danych](media/monitor-visually/alerts01.png)

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Utwórz alerty

1.  Wybierz pozycję **Nowa reguła** alertu, aby utworzyć nowy Alert.

    ![Przycisk reguły nowego alertu](media/monitor-visually/alerts02.png)

1.  Określ nazwę reguły i wybierz ważność alertu.

    ![Pola nazwy i ważności reguły](media/monitor-visually/alerts03.png)

1.  Wybierz kryteria alertu.

    ![Pole dla kryteriów docelowych](media/monitor-visually/alerts04.png)

    ![Lista kryteriów](media/monitor-visually/alerts05.png)

1.  Skonfiguruj logikę alertów. Można utworzyć alert dla wybranej metryki dla wszystkich potoków i odpowiednich działań. Możesz również wybrać konkretny typ działania, nazwę działania, nazwę potoku lub typ błędu.

    ![Opcje konfigurowania logiki alertu](media/monitor-visually/alerts06.png)

1.  Skonfiguruj wiadomości e-mail, wiadomości SMS, wypychania i powiadomień głosowych dla alertu. Utwórz grupę akcji lub wybierz istniejącą dla powiadomień o alertach.

    ![Opcje konfigurowania powiadomień](media/monitor-visually/alerts07.png)

    ![Opcje dodawania powiadomienia](media/monitor-visually/alerts08.png)

1.  Utwórz regułę alertu.

    ![Opcje tworzenia reguły alertu](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat monitorowania i zarządzania potokami, zobacz artykuł [monitorowanie i zarządzanie potokami programowo](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) .
