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
ms.openlocfilehash: 53ead1caed47ae442670f0b6bcd54cd84956a759
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720615"
---
# <a name="visually-monitor-azure-data-factories"></a>Wizualne monitorowanie fabryk danych platformy Azure
Azure Data Factory to oparta na chmurze usługa integracji danych, za pomocą której możesz tworzyć oparte na danych przepływy pracy w chmurze służące do organizowania oraz automatyzowania przenoszenia i przekształcania danych. Za pomocą usługi Azure Data Factory można tworzyć oparte na danych przepływy pracy (nazywane potokami) i ustalać ich harmonogram. Te przepływy mogą pozyskiwać dane z różnych magazynów danych, przetwarzać/przekształcać je za pomocą usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning, a następnie publikować dane wyjściowe w magazynach danych, np. Azure SQL Data Warehouse, do użycia przez aplikacje analizy biznesowej.

W tym przewodniku szybki start dowiesz się, jak wizualnie monitorować potoki Data Factory bez pisania pojedynczego wiersza kodu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="monitor-data-factory-pipelines"></a>Monitorowanie potoków Data Factory

Monitoruj przebiegi potokowe i działania przy użyciu prostego interfejsu widoku listy. Wszystkie uruchomienia są wyświetlane w strefie czasowej przeglądarki lokalnej. Można zmienić strefę czasową i wszystkie pola daty i godziny przyciągania do wybranej strefy czasowej.  

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się do [Azure Portal](https://portal.azure.com/).
3. Przejdź do bloku utworzono fabrykę danych w Azure Portal i kliknij kafelek "Monitoruj & Zarządzaj", aby uruchomić Data Factory środowisko monitorowania wizualnego.

## <a name="monitor-pipeline-runs"></a>Monitorowanie uruchomień potoku
Widok listy z poszczególnymi uruchomieniami potoków fabryki danych w wersji 2. Uwzględnione kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku. |
| Akcje | Pojedyncza Akcja dostępna do wyświetlania uruchomień działania. |
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia potoku (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Duration | Czas trwania uruchomienia (HH: MM: SS) |
| Wyzwolone przez | Wyzwalacz ręczny, wyzwalacz harmonogramu |
| Stan | Niepowodzenie, zakończone powodzeniem, w toku |
| Parametry | Parametry uruchomienia potoku (nazwa, pary wartości) |
| Błąd | Błąd uruchomienia potoku (jeśli/jest) |
| Identyfikator uruchomienia | Identyfikator uruchomienia potoku |

![Monitorowanie uruchomień potoku](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Monitorowanie uruchomień działania
Widok listy z uruchomieniami działań odpowiadającymi poszczególnym uruchomieniom potoków. Kliknij ikonę **"uruchomienia działania"** w kolumnie **"akcje"** , aby wyświetlić uruchomienia działań dla każdego uruchomienia potoku. Uwzględnione kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania w potoku. |
| Typ działania | Typ działania, taki jak Copy, HDInsightSpark, HDInsightHive itp. |
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia działania (MM/DD/RRRR, HH: MM: SS AM/PM) |
| Duration | Czas trwania uruchomienia (HH: MM: SS) |
| Stan | Niepowodzenie, zakończone powodzeniem, w toku |
| Dane wejściowe | Tablica JSON opisująca dane wejściowe działania |
| Output | Tablica JSON opisująca dane wyjściowe działania |
| Błąd | Błąd uruchamiania działania (Jeśli/dowolna) |

![Monitorowanie uruchomień działania](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Aby odświeżyć listę uruchomień potoków i działań, należy kliknąć ikonę **"Odśwież"** w górnej części. Automatyczne odświeżanie nie jest obecnie obsługiwane.

![Odśwież](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Wybierz fabrykę danych do monitorowania
Umieść kursor na ikonie **Data Factory** w lewym górnym rogu. Kliknij ikonę "strzałka", aby wyświetlić listę subskrypcji platformy Azure i fabryk danych, które można monitorować.

![Wybieranie fabryki danych](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>Skonfiguruj widok listy

### <a name="apply-rich-ordering-and-filtering"></a>Zastosuj rozbudowane porządkowanie i filtrowanie

Kolejność przebiegów potoku w opisie/ASC przez uruchomienie przebiegu i filtrowanie potoków przez następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku. Opcje obejmują szybkie filtry dla "ostatnich 24 godzin", "ostatni tydzień", "ostatnie 30 dni" lub "niestandardowa Data Time". |
| Uruchom uruchomienie | Data i godzina rozpoczęcia uruchomienia potoku |
| Stan uruchomienia | Filtrowanie przebiega według stanu — zakończone powodzeniem, zakończone niepowodzeniem, w toku |

![Filtr](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Dodawanie lub usuwanie kolumn
Kliknij prawym przyciskiem myszy nagłówek widoku listy i wybierz kolumny, które mają być wyświetlane w widoku listy.

![Kolumny](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Dopasuj szerokości kolumn
Zwiększanie i zmniejszanie szerokości kolumn w widoku listy przez umieszczenie kursora na nagłówku kolumny

## <a name="promote-user-properties-to-monitor"></a>Podnieś poziom właściwości użytkownika do monitorowania

Każdą właściwość działania potoku można podwyższyć jako właściwość użytkownika, aby stała się ona jednostką, którą można monitorować. Na przykład można podwyższyć poziom właściwości źródłowej i **docelowej** działania kopiowania w potoku jako właściwości użytkownika. Możesz również wybrać pozycję **Generuj automatycznie** , aby wygenerować właściwości źródłowe i **docelowe** dla działania kopiowania.

![Tworzenie właściwości użytkownika](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Można podwyższyć poziom do 5 właściwości działania potoku jako właściwości użytkownika.

Po utworzeniu właściwości użytkownika można je monitorować w widokach listy monitorowania. Jeśli źródłem działania kopiowania jest nazwa tabeli, można monitorować nazwę tabeli źródłowej jako kolumnę w widoku listy uruchomień działania.

![Lista uruchomień działań bez właściwości użytkownika](media/monitor-visually/monitor-user-properties-image2.png)

![Dodawanie kolumn dla właściwości użytkownika do listy uruchomień działania](media/monitor-visually/monitor-user-properties-image3.png)

![Lista uruchomień działania z kolumnami dla właściwości użytkownika](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Uruchom ponownie działania wewnątrz potoku

Można teraz ponownie uruchamiać działania wewnątrz potoku. Kliknij pozycję **Wyświetl uruchomienia działań** i wybierz działanie w potoku, z którego punkt chcesz ponownie uruchomić potok.

![Wyświetlanie uruchomień działania](media/monitor-visually/rerun-activities-image1.png)

![Wybierz przebieg działania](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Wyświetl historię ponownego uruchamiania

Można wyświetlić historię ponownego uruchamiania dla wszystkich uruchomień potoku w widoku listy.

![Wyświetlanie historii](media/monitor-visually/rerun-history-image1.png)

Możesz również wyświetlić historię ponownego uruchamiania dla określonego uruchomienia potoku.

![Wyświetl historię uruchomienia potoku](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Widoki wykresu Gantta

Użyj widoków wykresu Gantta, aby szybko wizualizować potoki i uruchomienia działania. Widok wykresu Gantta można przeglądać na potok lub grupować według adnotacji/tagów utworzonych w potokach.

![Wykres Gantta](media/monitor-visually/gantt1.png)

![Adnotacje wykresu Gantta](media/monitor-visually/gantt2.png)

Długość paska informuje o czasie trwania potoku. Możesz również kliknąć pasek, aby wyświetlić więcej szczegółów.

![Czas trwania wykresu Gantta](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>Przewodniki
Kliknij ikonę "informacje" w lewym dolnym rogu, a następnie kliknij "przewodniki z przewodnikiem", aby uzyskać instrukcje krok po kroku dotyczące monitorowania uruchomienia potoku i działania.

![Przewodniki](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Opinia
Kliknij ikonę "opinia", aby przekazać nam swoją opinię na temat różnych funkcji lub wszelkich problemów, które mogą się pojawić.

![Opinia](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alerty

Alerty dla obsługiwanych metryk można zgłaszać w Data Factory. Wybierz pozycję **monitor-> alerty & metryki** na stronie monitorowanie Data Factory, aby rozpocząć pracę.

![](media/monitor-visually/alerts01.png)

Aby zapoznać się z wprowadzeniem do siedmiu minut i demonstracją tej funkcji, Obejrzyj następujące wideo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Tworzenie alertów

1.  Kliknij pozycję **Nowa reguła** alertu, aby utworzyć nowy Alert.

    ![](media/monitor-visually/alerts02.png)

1.  Określ nazwę reguły i wybierz **ważność**alertu.

    ![](media/monitor-visually/alerts03.png)

1.  Wybierz kryteria alertu.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Skonfiguruj logikę alertów. Można utworzyć alert dla wybranej metryki dla wszystkich potoków i odpowiednich działań. Można również wybrać konkretny typ działania, nazwę działania, nazwę potoku lub typ błędu.

    ![](media/monitor-visually/alerts06.png)

1.  Skonfiguruj **wiadomości e-mail/SMS/powiadomień wypychanych/głosowych** dla alertu. Utwórz lub wybierz istniejącą **grupę akcji** dla powiadomień o alertach.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Utwórz regułę alertu.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Następne kroki

Zobacz artykuł [monitorowanie i zarządzanie potokami programowo](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) , aby dowiedzieć się więcej na temat monitorowania i zarządzania potokami.
