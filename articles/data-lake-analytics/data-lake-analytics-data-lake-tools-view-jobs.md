---
title: Korzystanie z przeglądarki zadań i widoku zadań na potrzeby zadań usługi Azure Data Lake Analytics
description: W tym artykule opisano sposób korzystania z przeglądarki zadań i widoku zadań dla zadań usługi Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60616234"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Korzystanie z przeglądarki zadań i widoku zadań usługi Azure Data Lake Analytics
Usługa Azure Data Lake Analytics archiwizuje przesłane zadania w magazynie zapytań. W tym artykule dowiesz się, jak korzystać z przeglądarki zadań i widoku zadań w usłudze Azure Data Lake Tools for Visual Studio można znaleźć informacje historyczne zadania. 

Domyślnie usługi Data Lake Analytics archiwizuje zadania przez 30 dni. Okres ważności można skonfigurować w witrynie Azure portal, konfigurując zasady wygasania dostosowane. Nie można uzyskać dostęp do informacji zadania po wygaśnięciu. 

## <a name="prerequisites"></a>Wymagania wstępne
Zobacz [Data Lake Tools for Visual Studio wymagania wstępne dotyczące](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otwórz przeglądarkę zadań
Dostęp z przeglądarki zadań za pośrednictwem **Eksplorator serwera > Azure > Data Lake Analytics > zadania** w programie Visual Studio.  Za pomocą przeglądarki zadania, można uzyskać dostępu do magazynu zapytań, konta usługi Data Lake Analytics. Z przeglądarki zadań wyświetlane Query Store po lewej stronie, informacje o zadaniu podstawowe i widok zadań na prawo przedstawiający szczegółowe informacje o zadaniu.

## <a name="job-view"></a>Widok zadania
Widok zadania przedstawiono szczegółowe informacje, które zadania. Aby otworzyć zadania, kliknij dwukrotnie zadanie w przeglądarce zadania lub otworzyć z menu usługi Data Lake, klikając widok zadania. Powinieneś zobaczyć okno dialogowe wypełniony adres URL zadania.

![Data Lake Tools Visual Studio Job Browser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Widok zadania zawiera:

* Podsumowanie zadania
  
    Odśwież widok zadań, aby zobaczyć nowych informacji o uruchomionych zadań.
  
  * Stan zadania (wykres):
    
      Stan zadania zawiera opis etapów zadań:
    
      ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Przygotowanie: Przekaż skrypt do chmury, kompilowania i optymalizowanie skryptu przy użyciu usługi kompilacji.
    * W kolejce: Zadania są umieszczane w kolejce, gdy oczekujące za mało zasobów lub zadania przekracza maksymalny równoczesnych zadań na ograniczenie konta. Ustawienie priorytetu Określa sekwencję zadań w kolejce — im niższy numer, tym wyższy priorytet.
    * Uruchomione: Zadanie jest uruchomione na Twoim koncie usługi Data Lake Analytics.
    * Finalizowanie: Zadanie jest kończonych (np. Trwa kończenie pliku).
      
      Zadanie może zakończyć się niepowodzeniem na każdym etapie. Na przykład błędy kompilacji w fazie przygotowanie błędy przekroczenia limitu czasu w fazie znajdujących się kolejce, a błędy wykonania w fazie uruchamiania itp.
  * Podstawowe informacje
    
      Informacje podstawowe zadania wyświetlone w dolnej części panel Podsumowanie zadania.
    
      ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Wynik zadania: Zakończonych powodzeniem lub niepowodzeniem. Zadanie może zakończyć się niepowodzeniem na każdym etapie.
    * Łączny czas trwania: Czas zegarowy (czas trwania) między przesyłanie czas i czas zakończenia.
    * Łączny czas obliczeń: Suma co czas wykonywania wierzchołka, można rozważyć go jako czas, który zadanie jest wykonywane w tylko jeden wierzchołka. Zapoznaj się z łączna liczba wierzchołków, aby znaleźć więcej informacji na temat wierzchołka.
    * Czas przesyłania/rozpoczęcia/zakończenia: Czas, kiedy usługi Data Lake Analytics otrzyma zadanie przesyłania/zaczyna Uruchom zadanie zadanie/kończy się powodzeniem lub niepowodzeniem.
    * Kompilacja/w kolejce/uruchamiania: Czas zegarowy spędzony w fazie przygotowanie/kolejce/uruchomiona.
    * Konto: Konto usługi Data Lake Analytics, używane do uruchamiania zadania.
    * Autor: Użytkownik, który przesłał zadania, może to być konto tworzy prawdziwa osoba lub konta systemowego.
    * Priorytet: Priorytet zadania. Im mniejsza liczba, tym wyższy priorytet. Dotyczy tylko sekwencji zadań w kolejce. Ustawienie wyższy priorytet nie wywłaszczała uruchomionych zadań.
    * Równoległość: Żądana maksymalną liczbę równoczesnych Azure Lake Analytics jednostek danych (ADLAUs) alias wierzchołków. Obecnie wierzchołka jest równy jedną maszynę Wirtualną z dwiema rdzenia wirtualnego i 6 GB pamięci RAM, chociaż może to zostać uaktualniony w przyszłości usługa Data Lake Analytics aktualizacji.
    * Bajty pozostałe: Bajty, które muszą być przetworzone, dopóki zadanie zostanie ukończone.
    * Liczba bajtów odczytanych/zapisanych: Liczba bajtów, które zostały odczytanych/zapisanych od zadania został uruchomiony.
    * Całkowita liczba wierzchołków: Zadanie jest podzielony na wiele fragmentów zadań, każdy element pracy jest nazywany wierzchołka. Tę wartość w tym artykule opisano jak wiele fragmentów zadań składa się zadania. Wierzchołek można rozważyć jako jednostka podstawowy proces alias usługi Azure Data Lake Analytics jednostki (ADLAU), a wierzchołki mogą być uruchamiane w równoległości. 
    * Ukończone lub uruchomienie/nie powiodła się: Liczba wierzchołków ukończone lub uruchomienie/nie powiodła się. Wierzchołki może zakończyć się niepowodzeniem z powodu błędów kodu i systemie zarówno użytkowników, ale ponownych prób systemu nie powiodło się wierzchołki automatycznie kilka razy. Wierzchołek nadal nie powiodło się po ponowieniu próby, całą pracę zakończy się niepowodzeniem.
* Wykres zadania
  
    Skrypt U-SQL reprezentuje logiki przekształcania danych wejściowych w dane wyjściowe. Skrypt jest skompilowany i zoptymalizowane pod kątem do planu wykonania fizycznych w fazie przygotowanie. Wykres zadania jest pokazanie plan wykonania fizycznych.  Na poniższym diagramie przedstawiono proces:
  
    ![Fazy stan zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Zadania są dzielone na wiele fragmentów zadań. Każdy element pracy jest nazywany wierzchołka. Wierzchołki są zgrupowane jako Super wierzchołka (zwane również etap) i bezpośrednią wizualizację grafu zadania. Tabliczek zielony etapu grafu zadania przedstawiono etapy.
  
    Każdy wierzchołków w etapie robi tego samego rodzaju pracę przy użyciu różnych rodzajów tych samych danych. Na przykład jeśli masz plik z danymi TB, a istnieją setki wierzchołków odczytu z niego, każdy z nich jest odczytu fragment. Te wierzchołki są grupowane w tym samym etapie i to samo działają na różnych częściach tego samego pliku wejściowego.
  
  * <a name="state-information"></a>Informacje o etapach
    
      W ramach danego etapu niektóre liczby są wyświetlane na plakietce.
    
      ![Usługa Azure Data Lake Analytics zadania etapu programu graph](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Wyodrębnij SV1: Nazwa etapu, o nazwie określonej przez liczbę, a także metoda operacji.
    * 84 wierzchołków: Łączna liczba wierzchołków na tym etapie. Rysunek wskazuje, ile elementów pracy dzieli się na tym etapie.
    * 12.90 s/wierzchołka: Średni czas wykonywania wierzchołka dla tego etapu. Poniższy rysunek jest obliczana na podstawie sumy (co czas wykonywania wierzchołka) / (całkowita liczba wierzchołków). Oznacza to, jeśli można przypisać wszystkie wierzchołki, które są wykonywane w równoległości, etap całego zostało zakończone w 12.90 s. Oznacza to również, jeśli całą pracę podczas tego etapu jest wykonywane szeregowo, opłata wyniesie #vertices * Średni czas.
    * 850,895 zapisanych wierszy: Całkowita liczba wierszy zapisywane podczas tego etapu.
    * R/W: Ilość danych odczytu/Written na tym etapie w bajtach.
    * Kolory: Kolory na etapie wskazuje stan różnych wierzchołka.
      
      * Zielony oznacza, że zakończyło się pomyślnie wierzchołka.
      * Pomarańczowy oznacza, że próba jest ponawiana wierzchołka. Ponawiane wierzchołka nie powiodło się, ale próba jest ponawiana pomyślnie i automatycznie przez system i ogólną etapu zakończyło się powodzeniem. Jeśli wierzchołka ponowione, ale nadal nie powiodło się, kolor włącza czerwony i całego zadania nie powiodło się.
      * Kolor czerwony oznacza niepowodzenie, co oznacza, że niektóre wierzchołek obejmowała zostały ponowione kilka razy przez system, ale nadal nie powiodło się. W tym scenariuszu powoduje niepowodzenie całego zadania.
      * Niebieski oznacza, że niektóre wierzchołek jest przetwarzany.
      * Oficjalny wskazuje wierzchołek oczekuje. Wierzchołek może być oczekujących na zaplanowanie po udostępnieniu ADLAU lub go może być oczekiwanie na dane wejściowe, ponieważ dane wejściowe mogą nie być gotowy.
      
      Więcej szczegółów można znaleźć na etapie, umieszczając kursor myszy kursor myszy według jedną stanu:
      
      ![Szczegóły etapu grafu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Wierzchołki: W tym artykule opisano szczegóły wierzchołki, na przykład, ile wierzchołków w całości, ile wierzchołki zostały ukończone, są one nie powiodło się lub jest nadal uruchomiona/oczekiwanie itp.
  * Dane odczytane między/wewnątrz zasobnika: Pliki i dane są przechowywane w wielu zasobnikach w rozproszonym systemie plików. Wartość, w tym miejscu opisano, jak dużo danych został odczytany w pod tym samym lub między pod.
  * Łączny czas obliczeń: Suma co czas wykonywania wierzchołków w etapie, można rozważyć jej czas, jaki zajęłoby, jeśli wszystkie działa na etapie jest wykonywane w wierzchołka tylko jeden.
  * Dane i wiersze zapisywane odczytu: Wskazuje, ile danych wierszy mają zostać odczytanych/zapisanych lub do odczytania.
  * Niepowodzenia odczytu wierzchołka: W tym artykule opisano, jak wiele wierzchołki są nie powiodło się podczas odczytu danych.
  * Odrzucenia duplikatu wierzchołka: Jeśli wierzchołek działa zbyt wolno, system może zaplanować wiele wierzchołki, aby uruchomić ten sam element pracy. Wierzchołki reductant zostaną odrzucone po jednym wierzchołki ukończone pomyślnie. Odrzucenia duplikatu wierzchołka zlicza liczbę wierzchołki, które zostaną odrzucone jako duplikatów w etapie.
  * Odwołania wierzchołka: Wierzchołka zostało pomyślnie, ale uzyskać później ponownie z określonych przyczyn. Na przykład jeśli podrzędny wierzchołka utraci pośrednie dane wejściowe, zostanie wyświetlony monit nadrzędnego wierzchołek, aby ponownie uruchomić.
  * Wykonania harmonogramu wierzchołka: Całkowity czas, który zostały zaplanowane wierzchołki.
  * Odczyt danych wierzchołka minimalny/średni/maksymalny: Minimalna/średnia/maksymalna każdego wierzchołka odczytywać dane.
  * Czas trwania: Czas zegarowy etapu przyjmuje, Załaduj profil, aby zobaczyć tę wartość.
  * Odtwarzanie zadania
    
      Data Lake Analytics uruchamia zadania i archiwa wierzchołki uruchomione informacji zadania, takie jak po uruchomieniu wierzchołki, zatrzymana, nie powiodło się, jak i jak są zwalniane, itp. Wszystkie informacje są automatycznie rejestrowane w magazynie zapytań i przechowywane w jego profil zadania. Możesz pobrać profil zadania za pomocą "Załaduj profil" w widoku zadania, a odtwarzanie zadania można wyświetlić po pobraniu profil zadania.
    
      Odtwarzanie zadania jest wizualizacja epitome co wydarzyło się w klastrze. Ułatwia on obserwowanie postępu wykonywania zadania oraz wzrokowe wykrycie anomalii wydajności i wąskich gardeł w bardzo krótkim czasie (mniej niż zwykle 30 sekund).
  * Ekran mapy cieplnej zadania 
    
      Mapa cieplna zadania można wybrać za pomocą listy rozwijanej wyświetlaną w grafu zadania. 
    
      ![Ekran mapy sterty grafu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Przedstawia on Mapa cieplna operacji We/Wy, czas i przepływności, zadania, za pomocą którego można znaleźć, gdzie zadanie spędza większość czasu lub tego, czy zadanie jest zadaniem granic operacji We/Wy i tak dalej.
    
      ![Przykład mapy sterty grafu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Postęp: Wykonanie zadania postępu, zobacz informacje w nich informacje o etapach.
    * Odczytanych/zapisanych danych: Mapa cieplna łączna ilość danych odczytanych/zapisanych na każdym etapie.
    * Czas obliczeń: Mapa cieplna SUM (co czas wykonywania wierzchołka), można rozważyć to jak długo zajmie się, jeśli całą pracę na etapie jest wykonywane przy użyciu tylko 1 wierzchołka.
    * Średni czas wykonania dla węzła: Mapa cieplna sum (co czas wykonywania wierzchołka) / (liczba wierzchołków). Oznacza to, jeśli można przypisać wszystkie wierzchołki, które są wykonywane w równoległości, etap całego zostanie wykonane w tym horyzoncie czasowym.
    * Przepływność wejścia/wyjścia: Mapa cieplna przepustowość we/wy każdego etapu, można potwierdzić, czy zadania we/wy powiązać zadania za pomocą tego.
* Operacje dotyczące metadanych
  
    Można wykonać operacji na metadanych w skrypcie języka U-SQL, takich jak tworzenie bazy danych, drop table itp. Te operacje są wyświetlane w ramach operacji metadanych po kompilacji. Możesz znaleźć potwierdzenia, tworzyć jednostki, w tym miejscu upuść jednostek.
  
    ![Operacje dotyczące metadanych usługi Azure Data Lake Analytics zadania widoku](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historia stanu
  
    Historia stanu również są wizualizowane w podsumowaniu zadania, ale możesz uzyskać szczegółowe informacje w tym miejscu. Szczegółowe informacje można znaleźć, np. gdy zadanie jest gotowa, w kolejce, zakończone wprowadzenie uruchomiona. Również znaleźć tyle razy został wcześniej skompilowany zadania (CcsAttempts: 1), gdy zadanie wysłaniu do klastra faktycznie (szczegóły: Wysyłanie zadania do klastra), itp.
  
    ![Historia stanu usługi Azure Data Lake Analytics zadania widoku](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostyka
  
    Narzędzie, które automatycznie diagnozuje wykonywania zadania. Będą otrzymywać alerty, gdy istnieją pewne błędy lub problemy z wydajnością w zadaniach. Należy pamiętać, trzeba pobrać profil, aby uzyskać pełne informacje w tym miejscu. 
  
    ![Diagnostyka Azure Data Lake Analytics zadania widoku](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Ostrzeżenia: Alert pojawia się tutaj z ostrzeżeniem kompilatora. Możesz kliknąć "x problemy" łącze, aby uzyskać więcej informacji, gdy zostanie wyświetlony alert.
  * Trwają zbyt długo wierzchołka: Jeśli dowolny z wierzchołków działa Przekroczono limit czasu (np. 5 godzin), problemów będzie można znaleźć tutaj.
  * Użycie zasobów: Jeśli nie potrzebujesz została przydzielona równoległości więcej lub nie ma wystarczającej ilości, problemów będzie można znaleźć tutaj. Można również kliknąć pozycję użycia zasobów, aby zobaczyć więcej szczegółów i realizować scenariusze warunkowej, aby znaleźć lepsze alokacji zasobów (Aby uzyskać więcej informacji, zobacz ten przewodnik).
  * Sprawdzenie pamięci: Jeśli dowolny z wierzchołków korzysta z więcej niż 5 GB pamięci, problemów będzie można znaleźć tutaj. Wykonanie zadania może uzyskać zabite przez system używa więcej pamięci niż ograniczenie systemu.

## <a name="job-detail"></a>Szczegóły zadania
Szczegóły zadania przedstawiono szczegółowe informacje o zadaniu, w tym skryptu, zasobów i widoku wykonania wierzchołka.

![Szczegóły zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skrypt
  
    Skrypt U-SQL, które zadania są przechowywane w magazynie zapytań. Można wyświetlić oryginalny skrypt U-SQL i Prześlij ponownie w razie potrzeby.
* Zasoby
  
    Znajdują się dane wyjściowe kompilacji zadania, przechowywane w magazynie zapytań za pomocą zasobów. Na przykład można znaleźć "algebra.xml", który jest używany do wyświetlania wykresu zadania, zestawów, który został zarejestrowany, itp. w tym miejscu.
* Widok wykonywania wierzchołka
  
    Wierzchołki pokazuje szczegóły wykonania. Profil zadania archiwizuje każdego dziennika wykonywania wierzchołka, takich jak łączna liczba odczytanych/zapisanych danych, środowisko uruchomieniowe, stan, itp. Za pomocą tego widoku możesz uzyskać szczegółowe informacje na temat sposobu uruchomienia zadania. Aby uzyskać więcej informacji, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonania wierzchołka, zobacz [korzystanie z widoku wykonania wierzchołka w narzędziach Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

