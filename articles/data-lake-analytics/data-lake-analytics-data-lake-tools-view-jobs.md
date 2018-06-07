---
title: Użyj przeglądarki zadania i widoku zadania dla zadań usługi Azure Data Lake Analytics
description: W tym artykule opisano sposób użycia przeglądarki zadania i widok zadań dla zadania usługi Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 6d7ebc8d14e82281e074ff52abd41a16871470c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623234"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Użyj przeglądarki zadania i zadania dla usługi Azure Data Lake Analytics
Usługa Azure Data Lake Analytics archiwa przesłanych zadania w [magazyn zapytań](#query-store). W tym artykule Dowiedz się jak używać przeglądarki zadania i widoku zadania w Azure Data Lake Tools dla programu Visual Studio można znaleźć informacje o zadaniu historycznych. 

Domyślnie usługi Data Lake Analytics archiwa zadania przez 30 dni. Okres wygaśnięcia można skonfigurować w portalu Azure Konfigurując zasady wygasania dostosowane. Nie można uzyskać dostępu do informacji zadania po wygaśnięciu. 

## <a name="prerequisites"></a>Wymagania wstępne
Zobacz [narzędzi Data Lake Tools dla Visual Studio wymagania wstępne](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otwórz przeglądarkę z zadania
Dostęp przeglądarki zadania za pomocą **Eksploratora serwera > Azure > usługi Data Lake Analytics > zadań** w programie Visual Studio.  Za pomocą przeglądarki zadania, można uzyskać dostępu do magazynu zapytań konta usługi Data Lake Analytics. Przeglądarka zadania są magazynu zapytań po lewej stronie, wyświetlane informacje podstawowe zadania, a widoku zadania w prawo Wyświetlanie szczegółowych informacji zadania.

## <a name="job-view"></a>Widok zadania
Zadania widoku przedstawia szczegółowe informacje o zadaniu. Aby otworzyć zadania, kliknij dwukrotnie pozycję zadania w przeglądarce zadania lub otworzyć z menu usługi Data Lake, klikając widoku zadania. Powinny zostać wyświetlone okno dialogowe wypełniane przy użyciu adresu URL zadania.

![Data Lake Tools przeglądarki zadania programu Visual Studio](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Zadania widoku zawiera:

* Podsumowanie zadania
  
    Odśwież widok zadań, aby wyświetlić nowszą informacji o uruchomionych zadań.
  
  * Stan zadania (wykres):
    
      Stan zadania zawiera opis etapów zadania.
    
      ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Przygotowanie: Prześlij skrypt do chmury, kompilowania i optymalizowanie skryptu, za pomocą usługi kompilacji.
    * W kolejce: Są kolejkowane zadania podczas oczekiwania na za mało zasobów lub zadania przekracza maksymalną zadań jednoczesnych na ograniczenie konta. Ustawienia priorytetu określa sekwencji zadań w kolejce - im niższy numer, tym wyższy priorytet.
    * Uruchomiona: Zadanie rzeczywiście jest uruchomiona na koncie usługi Data Lake Analytics.
    * Finalizowanie: Kończy zadanie (na przykład: Trwa kończenie pliku).
      
      Zadanie może zakończyć się niepowodzeniem na każdym etapie. Na przykład błędy kompilacji w fazie Preparing, błędy przekroczenia limitu czasu w fazie w kolejce i błędy wykonania w fazie uruchomione itd.
  * Informacje podstawowe
    
      Przedstawia informacje o zadaniu podstawowe w dolnej części panel Podsumowanie zadania.
    
      ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Wynik zadania: Powodzeniem lub niepowodzeniem. Zadania mogą nie działać na każdym etapie.
    * Łączny czas trwania: Ścian czasu zegara (czas trwania) między przesyłanie czas i czas zakończenia.
    * Łączny czas obliczeniowe: Suma czas wykonywania każdego wierzchołka, można rozważyć go jako czas, który zadania są wykonywane w tylko jednego wierzchołka. Zapoznaj się łącznie wierzchołków można znaleźć więcej informacji na temat wierzchołka.
    * Czas przesyłania/rozpoczęcia/zakończenia: Czas po usługa Data Lake Analytics odbiera zadania przesyłania/zaczyna Uruchom zadanie zadania/kończy się powodzeniem lub niepowodzeniem.
    * Kompilacji/w kolejce/uruchomiona: Czas zegarowy spędzony w fazie uruchamiania-Preparing/w kolejce.
    * Konto: Konto usługi Data Lake Analytics używane do uruchamiania zadania.
    * Autor: Użytkownik, który przesłał zadanie, może to być konto tworzy prawdziwa osoba lub konta system.
    * Priorytet: Priorytet zadania. Im niższy numer, tym wyższy priorytet. Wpływa tylko na sekwencji zadań w kolejce. Ustawienie wyższy priorytet nie zastępują uruchomionych zadań.
    * Równoległość: Żądany maksymalną liczbę równoczesnych Azure Lake Analytics jednostki danych (ADLAUs), alias wierzchołków. Obecnie jednego wierzchołka jest równy jedną maszynę Wirtualną z dwa podstawowe wirtualnego i 6 GB pamięci RAM, ale można jej uaktualnić w przyszłości usługi Data Lake Analytics aktualizacji.
    * Pozostało bajtów: Bajtów, które mają być przetwarzane dopiero po zakończeniu zadania.
    * Odczyt/zapisanych bajtów: bajtów, które zostały odczytana/zapisana od uruchomienia zadania.
    * Łącznie wierzchołków: zadanie jest podzielony na wiele elementów pracy, każdy element pracy jest nazywany wierzchołka. Ta wartość opisuje liczbę fragmentów zadań zadanie składa się z. Należy rozważyć wierzchołek jako jednostka podstawowy proces alias Azure Data Lake Analytics jednostki (ADLAU), i wierzchołków mogą być uruchamiane w równoległości. 
    * Ukończono/uruchomiona/nie powiodło się: Liczba wierzchołków zakończone lub uruchomiona/nie powiodła się. Wierzchołki może zakończyć się niepowodzeniem z powodu błędów kodu i systemu zarówno użytkowników, ale ponownych prób systemu nie wierzchołków automatycznie kilka razy. Jeśli wierzchołka nadal nie powiodło się po ponowieniu próby, całą pracę zakończy się niepowodzeniem.
* Graf zadania
  
    Skrypt U-SQL reprezentuje logiki przekształcania danych wejściowych w danych wyjściowych. Skrypt jest kompilowane i optymalizowane w ramach planu wykonywania fizycznych w fazie Preparing. Wykres zadania jest Pokaż plan wykonania fizycznego.  Na poniższym diagramie przedstawiono proces:
  
    ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Zadanie jest podzielony na wiele elementów pracy. Każdy element pracy jest nazywany wierzchołka. Wierzchołki są zgrupowane jako Super wierzchołków (alias etap) i wizualizowane jako wykres zadania. Tabliczek zielony etap na wykresie zadanie Pokaż etapów.
  
    Każdy wierzchołek w fazie wykonuje ten sam rodzaj korzystają z różnych części tych samych danych. Na przykład jeśli plik z jednego TB danych, a setki wierzchołków odczytywania z niego, każde z nich jest odczytu fragmentu. Te wierzchołków są grupowane w tym samym etapie i to samo działają w różnych części tego samego pliku wejściowego.
  
  * <a name="state-information"></a>Etap informacji
    
      W szczególności etapie w plakietkę przedstawiono niektóre liczby.
    
      ![Wykres etap zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Wyodrębnij: Nazwa etapu, o nazwie liczbą oraz metody operacji.
    * 84 wierzchołków: łączna liczba wierzchołki na tym etapie. Rysunek wskazuje, ile elementów pracy jest podzielona na tym etapie.
    * 12.90 s/wierzchołków: wierzchołków Średni czas wykonania dla tego etapu. Liczba ta jest obliczana na podstawie sumy (co czas wykonania wierzchołka) / (całkowita liczba wierzchołków). Co oznacza, że jeśli można przypisać wszystkich wierzchołków wykonywane w równoległości, etap całego zostało zakończone w 12.90 s. Oznacza to również w przypadku całą pracę na tym etapie jest wykonywane szeregowo, kosztów będzie #vertices * Średni czas.
    * 850,895 wierszy, zapisany: łączna liczba wierszy, zapisany na tym etapie.
    * R/W: ilość danych odczytu/Written na tym etapie w bajtach.
    * Kolory: Kolory w fazie wskazuje stan różnych wierzchołka.
      
      * Zielony oznacza, że zakończyło się pomyślnie wierzchołka.
      * Kolor pomarańczowy wskazuje, że próba zostanie ponowiona wierzchołka. Ponawiane wierzchołka nie powiodło się, ale próba zostanie ponowiona pomyślnie i automatycznie przez system, a ogólną etap zakończyło się powodzeniem. Jeśli wierzchołka ponowione, ale nadal nie powiodło się, kolor włącza czerwony i całego zadania nie powiodło się.
      * Czerwony oznacza nie powiodło się, co oznacza, że niektóre wierzchołek ma zostały ponowione kilka razy w systemie, ale nadal nie powiodło się. W tym scenariuszu powoduje niepowodzenie całego zadania.
      * Niebieski oznacza, że niektóre wierzchołek jest uruchomiona.
      * Białe wskazuje wierzchołka oczekuje. Wierzchołka może być oczekujących na zaplanowanie po udostępnieniu ADLAU lub może go oczekuje na dane wejściowe od czasu jej danych wejściowych może nie być gotowy.
      
      Więcej informacji można znaleźć w etapie, ustawiając kursor kursor myszy przez jednego stanu:
      
      ![Wykres etap szczegóły zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Wierzchołków: Opisano wierzchołków, na przykład, ile wierzchołków łączną liczbę wierzchołków zostały ukończone, są nie powiodło się lub jest nadal uruchomiona/oczekiwanie itp.
  * Dane odczytane pod między/wewnątrz: pliki i dane są przechowywane w wielu stanowiskami w rozproszonym systemie plików. W tym miejscu wartość opisano, jak dużo danych został odczytany pod tym samym lub między pod.
  * Całkowity czas obliczeniowe: Suma każdy wierzchołek czas wykonywania na etapie, można rozważyć go jako czas wymagałoby wszystkie pracuje w fazie są wykonywane w tylko jednego wierzchołka.
  * Dane i zapisywane odczytu wierszy: wskazuje, ile danych wierszy zostać odczytana/zapisana lub odczytywania.
  * Wierzchołka odczytywać błędów: w tym artykule opisano, jak wiele wierzchołków są nie powiodło się podczas odczytu danych.
  * Odrzuca wierzchołków zduplikowane: w przypadku wierzchołek działa zbyt wolno, system może planować wielu wierzchołków do uruchamiania tej samej element pracy. Wierzchołki reductant zostaną odrzucone po jednej wierzchołków pomyślnie. Odrzucenia zduplikowane wierzchołków rejestruje liczbę wierzchołków, które zostaną odrzucone jako powtórzeń na etapie.
  * Odwołania wierzchołków: wierzchołka powiodła się, ale uzyskać Uruchom ponownie później z określonych przyczyn. Na przykład jeśli podrzędne wierzchołków utraci pośrednich danych wejściowych, jest pytanie nadrzędnego wierzchołka do ponownego uruchomienia.
  * Wierzchołków harmonogram wykonaniami: całkowity czas, który zaplanowano wierzchołków.
  * Dane wierzchołków średni/min/Max odczytane: minimalna/średnia/maksymalna każdego wierzchołka odczytywać dane.
  * Czas trwania: Czas zegarowy etap przyjmuje, należy załadować profilu, aby zobaczyć tę wartość.
  * Odtwarzanie zadania
    
      Data Lake Analytics uruchamia zadania i archiwa wierzchołków uruchomione informacji zadania, takie jak przy uruchamianiu wierzchołki, zatrzymana, nie powiodło się i jak są zwalniane itp. Wszystkie informacje jest automatycznie rejestrowane w magazynie zapytań i przechowywane w profilu zadania. Można pobrać profilu zadania za pomocą "Załaduj profil" w widoku zadania, a po pobraniu profilu zadania można wyświetlić odtwarzania zadania.
    
      Odtwarzanie zadania jest wizualizację epitome co się stało w klastrze. Pomaga obserwowanie postępu wykonywania zadania oraz wzrokowe wykrycie anomalii wydajności i wąskich gardeł w krótkim czasie (mniej niż 30s zwykle).
  * Ekran mapy interakcji zadania 
    
      Mapa cieplna zadania można określić za pomocą listy rozwijanej wyświetlanej na wykresie zadania. 
    
      ![Ekran mapy sterty wykresu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Przedstawia on Mapa cieplna we/wy, czas i przepływności zadania, za pomocą których można znaleźć gdzie zadanie zużywa większość czasu oraz czy zadanie zadania granic We/Wy i tak dalej.
    
      ![Przykład mapy sterty wykresu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Postęp: Wykonanie zadania postępu, zobacz informacje w [przemieszczanie informacji](#stage-information).
    * Dane odczytana/zapisana: Mapa cieplna całkowita danych odczytana/zapisana na każdym etapie.
    * Godziny obliczeniowe: Mapa cieplna SUM (co czas wykonania wierzchołka), można rozważyć to jak długo zajmie, jeśli wszystkie prace w fazie jest wykonywane z tylko 1 wierzchołka.
    * Średni czas wykonania węzła: Mapa cieplna sum (co czas wykonania wierzchołka) / (liczba wierzchołków). Co oznacza, jeżeli można przypisać wszystkich wierzchołków wykonywane w równoległości, całe etapie zostaną wykonane w tym przedziale czasu.
    * Przepływność wejścia/wyjścia: Mapa cieplna przepustowość operacji We/Wy każdego etapu, można potwierdzić, jeśli zadanie jest powiązane zadanie we/wy przez to.
* Operacje dotyczące metadanych
  
    Można wykonać operacji na metadanych za pomocą skryptu U-SQL, takich jak tworzenie bazy danych, drop table itd. Te operacje są wyświetlane w metadanych operacji po kompilacji. Może znaleźć potwierdzeń, Utwórz jednostki, w tym miejscu upuść jednostek.
  
    ![Azure Data Lake Analytics zadania widoku operacji na metadanych](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historia stanu
  
    Historia stanu również wizualizacji w Podsumowanie zadania, ale w tym miejscu więcej informacji można uzyskać. Można znaleźć szczegółowe informacje, takie jak podczas przygotowywania zadania, w kolejce, wprowadzenie uruchomiona została zakończona. Również można znaleźć ile razy został skompilowany zadania (CcsAttempts: 1), gdy jest zadanie wysyłane do klastra faktycznie (szczegółu: podczas wysyłania zadań do klastra) itp.
  
    ![Historia stanu usługi Azure Data Lake Analytics zadania widoku](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostyka
  
    Narzędzie automatycznie diagnozuje wykonywania zadania. W przypadku błędów lub problemów z wydajnością w zadaniach zostaną wysłane alerty. Należy pamiętać, należy pobrać profilu, aby uzyskać pełne informacje w tym miejscu. 
  
    ![Diagnostyka Azure Data Lake Analytics zadania widoku](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Ostrzeżenia: Alertu zostaną wyświetlone tutaj z ostrzeżeniem kompilatora. Możesz kliknąć łącze "x problemów" ma więcej szczegółów, gdy pojawi się alert.
  * Wierzchołków Uruchom za długa: Jeśli dowolny z wierzchołków przekroczyły limit czasu (np. 5 godzin), problemy zostaną znalezione w tym miejscu.
  * Użycie zasobów: Jeżeli równoległości więcej lub za mało są przydzielone niż konieczne, problemów będzie można znaleźć tutaj. Można również kliknąć pozycję użycia zasobów, aby zobaczyć więcej szczegółów i wykonywać warunkowej scenariuszami, aby znaleźć lepsze alokacji zasobów (Aby uzyskać więcej informacji, zobacz w tym przewodniku).
  * Sprawdzanie pamięci: Jeśli dowolny z wierzchołków korzysta z więcej niż 5 GB pamięci, problemy zostaną znalezione w tym miejscu. Wykonanie zadania może pobrać przerwany przez system, gdy jest używana większa ilość pamięci niż ograniczenie systemu.

## <a name="job-detail"></a>Szczegóły zadania
Szczegóły zadania przedstawiono szczegółowe informacje o zadania, w tym skryptu, zasobów i widoku wykonania wierzchołka.

![Szczegóły zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skrypt
  
    Skrypt U-SQL zadania są przechowywane w magazynie zapytań. Można wyświetlić oryginalny skrypt U-SQL i ponownie prześlij, jeśli to konieczne.
* Zasoby
  
    Dane wyjściowe zadania kompilacji przechowywane w magazynie zapytań przy użyciu zasobów można znaleźć. Na przykład można znaleźć "algebra.xml", który służy do wyświetlenia wykresu zadania, zestawy, który został zarejestrowany, itp. w tym miejscu.
* Widoku wykonania wierzchołka
  
    Zawiera on wierzchołków szczegóły wykonywania. Profil zadania archiwa co dziennika wykonywania wierzchołków, takie jak łączna ilość danych odczytana/zapisana, runtime, stan itd. Za pomocą tego widoku można uzyskać więcej informacji na temat sposobu uruchomienia zadania. Aby uzyskać więcej informacji, zobacz [użyć widoku wykonania wierzchołka w narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonania wierzchołka, zobacz [użyć widoku wykonania wierzchołka w narzędzi Data Lake Tools dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

