---
title: Wizualnego monitorowania fabryki danych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i wizualnego monitorowania fabryki danych platformy Azure
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: c2967de97e9cc3b6f59eb742ecbfef9acbe64d20
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019779"
---
# <a name="visually-monitor-azure-data-factories"></a>Wizualnego monitorowania fabryki danych platformy Azure
Azure Data Factory to oparta na chmurze usługa integracji danych, za pomocą której możesz tworzyć oparte na danych przepływy pracy w chmurze służące do organizowania oraz automatyzowania przenoszenia i przekształcania danych. Za pomocą usługi Azure Data Factory można tworzyć oparte na danych przepływy pracy (nazywane potokami) i ustalać ich harmonogram. Te przepływy mogą pozyskiwać dane z różnych magazynów danych, przetwarzać/przekształcać je za pomocą usług obliczeniowych, takich jak Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics i Azure Machine Learning, a następnie publikować dane wyjściowe w magazynach danych, np. Azure SQL Data Warehouse, do użycia przez aplikacje analizy biznesowej.
W tym przewodniku Szybki Start dowiesz się, jak wizualnie monitorować potoki v2 fabryki danych bez konieczności pisania nawet jednego wiersza kodu.
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="monitor-data-factory-pipelines"></a>Monitorowanie potoków usługi Data Factory

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).
3. Przejdź do bloku fabryka danych utworzonego w witrynie Azure portal i kliknij Kafelek "Monitorowanie i zarządzanie", aby uruchomić doświadczenia wizualne monitorowania usługi fabryka danych.

## <a name="list-view-monitoring"></a>Monitorowanie w widoku listy

Monitorowanie uruchomień działań i potoku przy użyciu prostego interfejsu widoku listy. Wszystkie uruchomienia są wyświetlane z uwzględnieniem lokalnej strefy czasowej przeglądarki. Można zmienić strefę czasową, a wszystkie pola daty i godziny przyciąganie do wybranej strefie czasowej.  

### <a name="monitoring-pipeline-runs"></a>Monitorowanie uruchomienia potoku
Widok listy z poszczególnymi uruchomieniami potoków fabryki danych w wersji 2. Uwzględnionych kolumn:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku. |
| Akcje | Jednej akcji wyświetlanie uruchomień działań. |
| Uruchom Start | Potok Data i godzina rozpoczęcia przebiegu (MM/DD/RRRR GG: mm: SS AM/PM) |
| Czas trwania | Czas trwania (: mm: ss) |
| Wyzwolone przez | Wyzwalacz ręczny, wyzwalacz harmonogramu |
| Stan | Nie powiodło się, zakończyło się pomyślnie w toku |
| Parametry | Parametry (nazwa, wartość pary) uruchomienia potoku |
| Błąd | Uruchomienie potoku błędu (jeśli/any) |
| Identyfikator uruchomienia | Identyfikator uruchomienia potoku |

![Monitorowanie uruchomień potoku](media/monitor-visually/pipeline-runs.png)

### <a name="monitoring-activity-runs"></a>Monitorowanie uruchomień działań
Widok listy z uruchomieniami działań odpowiadającymi poszczególnym uruchomieniom potoków. Kliknij przycisk **działanie uruchamia** ikonę w obszarze **'Actions'** kolumnę do wyświetlenia działanie jest uruchamiane dla każdego uruchomienia potoku. Uwzględnionych kolumn:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa działania | Nazwa działania w potoku. |
| Typ aktywności | Typ działania, takich jak kopiowanie, HDInsightSpark, HDInsightHive itp. |
| Uruchom Start | Uruchomienia działania start Data i godzina (MM/DD/RRRR GG: mm: SS AM/PM) |
| Czas trwania | Czas trwania (: mm: ss) |
| Stan | Nie powiodło się, zakończyło się pomyślnie w toku |
| Dane wejściowe | Tablica JSON opisujące danych wejściowych działania |
| Dane wyjściowe | Tablica JSON opisujące danych wyjściowych działania |
| Błąd | (Jeśli/any) Błąd uruchamiania działania |

![Monitorowanie uruchomień działania](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Należy kliknąć przycisk **"Odśwież"** ikonę u góry, aby odświeżyć listę uruchomień potoków i działań. Automatyczne odświeżanie nie jest obecnie obsługiwane.
>

![Odświeżanie](media/monitor-visually/refresh.png)

## <a name="monitoring-features"></a>Funkcje monitorowania

### <a name="select-a-data-factory-to-monitor"></a>Wybierz fabrykę danych do monitorowania
Umieść kursor na **usługi Data Factory** ikony w lewym górnym rogu. Kliknij ikonę "Strzałki" Aby wyświetlić listę azure subskrypcje i fabryk danych, które można monitorować.

![Wybieranie fabryki danych](media/monitor-visually/select-datafactory.png)

### <a name="rich-ordering-and-filtering"></a>Rozbudowane porządkowanie i filtrowanie

Porządkuj uruchomienia potoków rosnąco lub przez uruchomienie Start i uruchomienia potoku filtru przez następujące kolumny:

| **Nazwa kolumny** | **Opis** |
| --- | --- |
| Nazwa potoku | Nazwa potoku. Opcje obejmują szybkie filtry dla "ostatnie 24 godziny", "Ostatni tydzień", "ostatnie 30 dni" lub wybierz opcję Niestandardowa data i godzina. |
| Uruchom Start | Potok Data i godzina rozpoczęcia przebiegu |
| Stan uruchomienia | Filtr działa według stanu — zakończyło się pomyślnie, nie powiodło się, w toku |

![Filtr](media/monitor-visually/filter.png)

### <a name="addremove-columns-in-list-view"></a>Dodawanie/usuwanie kolumn w widoku listy
Kliknij prawym przyciskiem myszy nagłówek widoku listy i wybierz kolumny, które mają być wyświetlane w widoku listy

![Kolumny](media/monitor-visually/columns.png)

### <a name="reorder-column-widths-in-list-view"></a>Zmiana kolejności szerokości kolumn w widoku listy
Zwiększać i zmniejszać szerokości kolumn w widoku listy, ustawiając kursor nad nagłówek kolumny

### <a name="user-properties"></a>Właściwości użytkownika

Możesz podwyższyć poziom dowolnej właściwości działania potoku jako właściwość użytkownika, aby stał się jednostki, która umożliwia monitorowanie. Na przykład, możesz podwyższyć poziom **źródła** i **docelowy** właściwości działania kopiowania w potoku jako właściwości użytkownika. Możesz również wybrać **automatycznie Generuj** do generowania **źródła** i **docelowy** właściwości użytkownika dla działania kopiowania.

![Tworzenie właściwości użytkownika](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Maksymalnie 5 właściwości działania w potoku można podwyższyć tylko właściwości użytkownika.

Po utworzeniu właściwości użytkownika, można monitorować je w widokach monitorowania listy. Źródło dla działania kopiowania w przypadku nazwy tabeli, można monitorować nazwy tabeli źródłowej, zgodnie z kolumną w działaniu uruchamia widoku listy.

![Działanie jest uruchamiane listy bez właściwości użytkownika](media/monitor-visually/monitor-user-properties-image2.png)

![Dodawanie kolumn do właściwości użytkownika na liście uruchomień działania](media/monitor-visually/monitor-user-properties-image3.png)

![Lista uruchomień działania z kolumnami dla właściwości użytkownika](media/monitor-visually/monitor-user-properties-image4.png)

### <a name="guided-tours"></a>Samouczki krok po kroku
Kliknij ikonę"informacje" w lewym dolnym rogu, a następnie kliknij przycisk "Nauka z przewodnikami", aby uzyskać instrukcje krok po kroku na temat sposobu monitorowania przebiegów potoków i działań.

![Samouczki krok po kroku](media/monitor-visually/guided-tours.png)

### <a name="feedback"></a>Opinia
Kliknij ikonę "Opinie", aby przesłać nam swoją opinię na różnych funkcji i wszelkich problemów, które możesz doświadczyć.

![Opinia](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Alerty

Możesz umieszczać alertów dotyczących metryk obsługiwanych w usłudze Data Factory. Wybierz Monitor -> Alerty i metryki dotyczące monitorowania fabryki danych strony na rozpoczęcie pracy.

![](media/monitor-visually/alerts01.png)

### <a name="create-alerts"></a>Tworzenie alertów

1.  Kliknij przycisk **nowego alertu zasada** można utworzyć nowego alertu.

    ![](media/monitor-visually/alerts02.png)

1.  Określ nazwę reguły, a następnie wybierz alert **ważność**.

    ![](media/monitor-visually/alerts03.png)

1.  Wybierz kryteria alertu.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Konfiguruj logikę alertów. Można utworzyć alertu dla wybranej metryki dla wszystkich potoków i odpowiednie działania. Możesz także wybrać typ danego działania, nazwa działania, Nazwa potoku lub typ niepowodzenia.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurowanie **poczty E-mail/SMS/wypychania/rejestr** powiadomienia o alercie. Utwórz lub wybierz istniejącą **grupy akcji** dla powiadomień o alertach.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  Utwórz regułę alertu.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowanie potoków i zarządzanie nimi programistycznie](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) artykuł, aby dowiedzieć się więcej o monitorowaniu i zarządzaniu potoków.
