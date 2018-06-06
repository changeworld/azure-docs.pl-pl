---
title: Użyj usługi Azure Stream Analytics tools dla programu Visual Studio
description: W tym artykule opisano, jak rozpocząć pracę, za pomocą narzędzi usługi analiza strumienia Azure dla programu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: bcb4302c3a237ec75ff670b322c9d068d8700de9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738517"
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Użyj usługi Azure Stream Analytics tools dla programu Visual Studio
Azure Stream Analytics tools dla programu Visual Studio są teraz ogólnie dostępna. Te narzędzia Włącz rozbudowanej Stream Analytics użytkownikom Rozwiązywanie problemów, a także zapisać złożonych kwerend i nawet pisać zapytania lokalnie. Zadanie usługi Stream Analytics można również wyeksportować do projektu programu Visual Studio.

## <a name="introduction"></a>Wprowadzenie
W tym samouczku Dowiedz się jak używać usługi Stream Analytics tools dla Visual Studio do tworzenia, tworzenie, testowanie lokalnie, zarządzania i debugowania z zadania usługi analiza strumienia. Po utworzeniu zadania ciągłej integracji i wdrażania procesu na platformie Azure można skonfigurować przy użyciu pakietu Nuget CI/CD, aby dowiedzieć się więcej dotyczą [Stream Analytics VS narzędzi do konfigurowania potoku CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) artykułu.

Po ukończeniu tego samouczka, będą mieć możliwość:

* Zapoznaj się z usługi Stream Analytics tools dla programu Visual Studio.
* Konfigurowanie i wdrażanie zadanie usługi Stream Analytics.
* Przetestuj zadania lokalnie z lokalnym przykładowych danych.
* Umożliwia monitorowanie rozwiązywania problemów.
* Wyeksportuj istniejące zadania do projektów.

>[!IMPORTANT]
> Firma Microsoft zaleca uaktualnienia do usługi Azure Data Lake Tools dla programu Visual Studio w wersji 2.3.3000.4 lub nowszej. Poprzednie wersje nie są już dostępne do pobrania i obecnie są przestarzałe. 
>
>**Co muszę zrobić?**
>
>1. Sprawdź, czy używasz starszej wersji niż 2.3.3000.5 Azure Stream Analytics Tools dla programu Visual Studio. 
>   
>   ![Sprawdzanie wersji narzędzia](./media/stream-analytics-tools-for-vs/about-data-lake.png)
> 
>2. Jeśli używana wersja jest starsza wersja programu 2.3.3000.5, odwiedzając Centrum pobierania aktualizacji narzędzi Azure Data Lake Tools dla programu Visual Studio: 
>    - [Dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools)
>    - [Dla programu Visual Studio 2013 i 2015](https://www.microsoft.com/en-us/download/details.aspx?id=54630)

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebne są następujące wymagania wstępne do ukończenia tego samouczka:

* Zakończ kroki do "Utwórz zadanie usługi analiza strumienia" samouczka [tworzenia rozwiązania IoT przy użyciu usługi Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Zainstaluj program Visual Studio 2017, programu Visual Studio 2015 lub Visual Studio 2013 Update 4. Enterprise (Ultimate/Premium), Professional i społeczności wersje są obsługiwane. Express edition nie jest obsługiwane. 
* Postępuj zgodnie z [instrukcje dotyczące instalacji](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) do zainstalowania narzędzia do analizy strumienia dla programu Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Tworzenie projektu usługi analiza strumienia
W programie Visual Studio, wybierz **pliku** > **nowy projekt**. Na liście szablonów po lewej stronie wybierz **Stream Analytics**, a następnie wybierz **Azure Stream Analytics aplikacji**.
W dolnej części strony, wprowadź projektu **nazwa**, **lokalizacji**, i **Nazwa rozwiązania** podobnie jak w przypadku innych projektów.

![Tworzenie nowego projektu](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Projekt **przez** jest generowana w **Eksploratora rozwiązań**.

![Przez projekt w Eksploratorze rozwiązań](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Wybierz poprawną subskrypcję
1. Na **widoku** menu, wybierz opcję **Eksploratora serwera** w programie Visual Studio.

2. Zaloguj się przy użyciu konta platformy Azure. 

## <a name="define-input-sources"></a>Definiowanie źródeł dla wejścia
1. W **Eksploratora rozwiązań**, rozwiń węzeł **dane wejściowe** węzła i Zmień nazwę **Input.json** do **EntryStream.json**. Kliknij dwukrotnie **EntryStream.json**.

2. Aby uzyskać **Alias wejściowy**, wprowadź **EntryStream**. Należy pamiętać, że alias wejściowy jest używana w skrypcie zapytania.

3. Aby uzyskać **typ źródła**, wybierz pozycję **strumienia danych**.

4. Aby uzyskać **źródła**, wybierz pozycję **Centrum zdarzeń**.

5. Aby uzyskać **Namespace magistrali usługi**, wybierz pozycję **TollData** opcji na liście rozwijanej.

6. Aby uzyskać **nazwę Centrum zdarzeń**, wybierz pozycję **wpis**.

7. Aby uzyskać **nazwy zasad Centrum zdarzeń**, wybierz pozycję **RootManageSharedAccessKey** (wartość domyślna).

8. Dla **Format serializacji zdarzeń**, wybierz pozycję **Json**oraz **kodowanie**, wybierz pozycję **UTF8**.
   
   Ustawienia wyglądać następująco:
   
   ![Wprowadź ustawienia](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. W dolnej części strony, wybierz **zapisać** aby zakończyć pracę kreatora. Teraz możesz dodać innego źródła danych wejściowych, można utworzyć strumienia zakończenia. Kliknij prawym przyciskiem myszy **dane wejściowe** , a następnie wybierz węzeł **nowy element**.
   
   ![Nowy element](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. W oknie podręcznym wybierz **wejściowego analizy strumienia**i zmień **nazwa** do **ExitStream.json**. Wybierz pozycję **Dodaj**.
   
    ![Dodaj nowy element](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Kliknij dwukrotnie **ExitStream.json** w projekcie i wykonaj kroki takie same jako strumień wpis, aby wypełnić pola. Aby uzyskać **nazwy Centrum zdarzeń**, należy się upewnić, że **zakończyć**, jak pokazano na poniższym zrzucie ekranu:
   
    ![Ustawienia ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Teraz zdefiniowano dwóch strumieni wejściowych.
   
   ![Dwa strumienie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Następnie należy dodać odwołanie do danych wejściowych dla pliku obiektu blob, który zawiera dane rejestracji samochodów.
   
12. Kliknij prawym przyciskiem myszy **dane wejściowe** węzła w projektu, a następnie wykonać tego samego procesu dla wejścia strumienia. Dla **typ źródła**, wybierz pozycję **danych referencyjnych**oraz **Alias wejściowy**, wprowadź **rejestracji**.
   
    ![Ustawienia rejestracji](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Wybierz **magazynu** konta, które zawiera opcję z **TollData**. Nazwa kontenera jest **TollData**i **wzorzec ścieżki** jest **registration.json**. Nazwa pliku jest rozróżniana wielkość liter i powinna być małymi literami.

14. Wybierz **zapisać** aby zakończyć pracę kreatora.

Teraz wszystkie dane wejściowe są zdefiniowane.

## <a name="define-output"></a>Definiowanie danych wyjściowych
1. W **Eksploratora rozwiązań**, rozwiń węzeł **dane wejściowe** węzeł i kliknij dwukrotnie **Output.json**.

2. Aby uzyskać **Alias wyjściowy**, wprowadź **dane wyjściowe**. Aby uzyskać **Sink**, wybierz pozycję **bazy danych SQL**.

3. Aby uzyskać **bazy danych** nazw, należy wprowadzić **TollDataDB**.

4. Aby uzyskać **nazwy użytkownika**, wprowadź **tolladmin**. Aby uzyskać **hasło**, wprowadź **123toll!**. Aby uzyskać **tabeli**, wprowadź **TollDataRefJoin**.

5. Wybierz pozycję **Zapisz**.

   ![Ustawienia danych wyjściowych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Zapytanie usługi analiza strumienia
W tym samouczku próbuje odpowiedzi na kilka pytania biznesowe związane z numer danych. Firma Microsoft konstruować zapytania używane w Stream Analytics w celu zapewnienia odpowiedniego odpowiedzi. Przed rozpoczęciem pierwszego zadania Stream Analytics, Przyjrzyjmy się Prosty scenariusz i składnia zapytania.

### <a name="introduction-to-stream-analytics-query-language"></a>Wprowadzenie do języka zapytań usługi Stream Analytics
Załóżmy, że należy policzyć pojazdów, które należy wprowadzić stoisku przez. Ponieważ ten strumień zdarzeń jest ciągły, należy zdefiniować pewien czas. Umożliwia modyfikowanie zapytania jako "ilu pojazdów wprowadź stoisku przez co trzy minuty?" Ten pomiar jest często określana jako liczność wirowania.

Przyjrzyjmy się kwerendy Stream Analytics, która zawiera odpowiedzi na to pytanie:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Jak widać, Stream Analytics korzysta z językiem zapytań takiego jak SQL. Dodaje kilka rozszerzeń, aby określić czas aspektów zapytania.

Aby uzyskać więcej informacji, przeczytaj o [czas zarządzania](https://msdn.microsoft.com/library/azure/mt582045.aspx) i [okienkową](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukcji używanych w zapytaniu w witrynie MSDN.

Teraz, pierwszego zapytania Stream Analytics, musieli napisać, przetestować go przy użyciu przykładowych plików danych znajdujących się w folderze TollApp w następującej ścieżce:

**..\TollApp\TollApp\Data**

Ten folder zawiera następujące pliki:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Pytanie: Liczba pojazdów stoisku przez
W projekcie, kliknij dwukrotnie **Script.asaql** można otworzyć skryptu w edytorze. Wklej skrypt w poprzedniej sekcji do edytora. Edytor zapytań obsługuje IntelliSense, kolorowanie składni i znacznik błędu.

![Edytor zapytań](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testowanie zapytań usługi Stream Analytics lokalnie
Najpierw można kompilować zapytanie, aby sprawdzić, czy wszystkie błąd składni.

1. Aby zweryfikować tego zapytania dotyczącego przykładowych danych, użyj lokalnej przykładowych danych prawym przyciskiem myszy dane wejściowe i wybierając **dodać lokalne dane wejściowe**.
   
   ![Dodawanie lokalnych danych wejściowych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. W oknie podręcznym wybierz przykładowych danych z ścieżki lokalnej. Wybierz pozycję **Zapisz**.
   
   ![Dodawanie lokalnych danych wejściowych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Plik o nazwie **local_EntryStream.json** jest automatycznie dodawany do folderu danych wejściowych.
   
   ![Lista plików wejściowych folder lokalny](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Wybierz **uruchom lokalnie** w edytorze zapytań. Lub naciśnij klawisz F5.
   
   ![Uruchom lokalnie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Można znaleźć ścieżki wyjściowej z danych wyjściowych konsoli. Naciśnij dowolny klawisz, aby otworzyć folder wyników.
   
   ![Przebieg lokalny](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Sprawdź wyniki w folderze lokalnym.
   
   ![Lokalny folder wyników](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Przykładowe dane wejściowe
Można również przykładowe dane wejściowe z źródeł dla wejścia do pliku lokalnego. Kliknij prawym przyciskiem myszy plik wejściowy konfiguracji, a następnie wybierz **przykładowych danych**. 

![Dane przykładowe](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Należy pamiętać, można przykładowe tylko centra zdarzeń lub centra IoT w obecnie. Innych źródeł danych wejściowych nie są obsługiwane. W wyświetlonym oknie dialogowym wprowadź ścieżkę lokalną, aby zapisać przykładowe dane. Wybierz **próbki**.

![Przykładowa konfiguracja danych](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Można wyświetlić postęp w **dane wyjściowe** okna. 

![Przykładowe dane wyjściowe](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Prześlij zapytanie usługi analiza strumienia Azure
1. W **edytora zapytań**, wybierz pozycję **przesłać do platformy Azure** w Edytorze skryptów.

   ![Przesłać na platformę Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Wybierz **utworzyć nowego zadania usługi analiza strumienia Azure**. Aby uzyskać **Nazwa zadania**, wprowadź **TollApp**. Wybierz poprawny **subskrypcji** na liście rozwijanej. Wybierz **przesłać**.

   ![Prześlij zadanie](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Uruchamianie zadania
Utworzono zadanie, a w widoku zadania otwierany automatycznie. 
1. Wybierz przycisk zieloną strzałkę, aby uruchomić zadanie.

   ![Zadanie przycisk Start](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Wybierz ustawienie domyślne, a następnie wybierz **Start**.
 
   ![Uruchamianie zadania](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Można wyświetlić stan zadania został zmieniony na **systemem**, i występują zdarzenia wejścia/wyjścia.

   ![Podsumowanie zadań i metryki](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Sprawdź wyniki w programie Visual Studio
1. Otwórz Eksploratora serwera w usłudze Visual Studio, a następnie kliknij prawym przyciskiem myszy **TollDataRefJoin** tabeli.

2. Wybierz **Pokaż dane tabeli** aby zobaczyć dane wyjściowe z zadania.
   
   ![Pokaż tabelę danych](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Wyświetl zadania metryk
Niektóre zadania podstawowe statystyki są wyświetlane w **metryki zadania**. 

![Metryki zadania](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Lista zadań w Eksploratorze serwera
W **Eksploratora serwera**, wybierz pozycję **zadania usługi analiza strumienia** , a następnie wybierz **Odśwież**. Zadanie jest wyświetlany w obszarze **zadania usługi analiza strumienia**.

![Lista zadań](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Otwórz widok zadania
Rozwiń węzeł zadania, a następnie kliknij dwukrotnie **widoku zadania** węzeł, aby otworzyć widok zadania.

![Widok zadania](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Eksportuj istniejące zadanie do projektu
Istnieją dwa sposoby istniejącego zadania można wyeksportować do projektu.
* W **Eksploratora serwera**w obszarze **zadania usługi analiza strumienia** węzła, kliknij prawym przyciskiem myszy węzeł zadania. Wybierz **wyeksportować do nowego projektu usługi analiza strumienia**.
   
   ![Eksportuj do nowego projektu usługi Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Wygenerowany projekt jest wyświetlany w **Eksploratora rozwiązań**.
   
    ![Zadanie Eksploratora rozwiązań](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* W widoku zadania, wybierz **Generowanie projektu**.
   
   ![Generuj projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia
 
* Testowanie lokalnego nie działa, jeśli zapytanie ma funkcje przestrzenne geo.
* Edytor obsługi nie jest dostępna w celu dodawania lub zmieniania UDF języka JavaScript.
* Testowanie lokalnego nie obsługuje zapisywania danych wyjściowych w formacie JSON. 
* Obsługa nie jest dostępna dla danych wyjściowych usługi Power BI i ADLS danych wyjściowych.



## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy przy użyciu usługi Azure Stream Analytics](stream-analytics-get-started.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Usługa Azure Stream Analytics management dokumentacji interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)


