---
title: Korzystanie z widoku & zadania przeglądarki zadań — usługa Azure Data Lake Analytics
description: W tym artykule opisano sposób korzystania z zadań Przeglądarki zadań i widoku zadań dla usługi Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309947"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Use Job Browser and Job View for Azure Data Lake Analytics (Korzystanie z przeglądarki zadań i widoku zadań dla usługi Azure Data Lake Analytics)
Archiwum usługi Usługi Azure Data Lake Analytics przesłane zadania w magazynie zapytań. W tym artykule dowiesz się, jak znaleźć historyczne informacje o zadaniach za pomocą przeglądarki zadań i widoku zadań w usłudze Azure Data Lake Tools for Visual Studio. 

Domyślnie usługa Data Lake Analytics archiwizuje zadania przez 30 dni. Okres wygaśnięcia można skonfigurować z witryny Azure portal, konfigurując dostosowane zasady wygasania. Nie będzie można uzyskać dostępu do informacji o pracy po wygaśnięciu. 

## <a name="prerequisites"></a>Wymagania wstępne
Zobacz [Wymagania wstępne usługi Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otwórz przeglądarkę zadań
Uzyskaj dostęp do przeglądarki zadań za pośrednictwem **Eksploratora serwera>usługi Azure>Data Lake Analytics>zadaniach** w programie Visual Studio.  Za pomocą przeglądarki zadań można uzyskać dostęp do magazynu zapytań konta usługi Data Lake Analytics. Przeglądarka zadań wyświetla magazyn zapytań po lewej stronie, z informacjami o podstawowym o pracę, a po prawej widok zadania ze szczegółowymi informacjami o pracy.

## <a name="job-view"></a>Widok zadania
Widok zadania zawiera szczegółowe informacje o zadaniu. Aby otworzyć zadanie, można dwukrotnie kliknąć zadanie w przeglądarce zadań lub otworzyć je z menu Usługi Data Lake, klikając pozycję Widok zadania. Powinno zostać wyświetlone okno dialogowe wypełnione adresem URL zadania.

![Przeglądarka zadań programu Data Lake Tools](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Widok zadania zawiera:

* Podsumowanie zadania
  
    Odśwież widok zadania, aby wyświetlić najnowsze informacje o uruchomionych zadaniach.
  
  * Stan zadania (wykres):
    
      Stan zadania określa fazy zadania:
    
      ![Stan faz zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Przygotowanie: Prześlij skrypt do chmury, kompilując i optymalizując skrypt za pomocą usługi kompilacji.
    * W kolejce: Zadania są umieszczane w kolejce, gdy oczekują na wystarczającą ilość zasobów lub zadania przekraczają maksymalną liczbę równoczesnych zadań na ograniczenie konta. Ustawienie priorytetu określa kolejność zadań w kolejce — im mniejsza liczba, tym wyższy priorytet.
    * Działa: zadanie jest faktycznie uruchomione na koncie Usługi Data Lake Analytics.
    * Finalizowanie: Zadanie jest zakończone (na przykład finalizowanie pliku).
      
      Zadanie może zakończyć się niepowodzeniem w każdej fazie. Na przykład błędy kompilacji w fazie Przygotowywanie, błędy limitu czasu w fazie kolejkowania i błędy wykonywania w fazie uruchamiania itp.
  * Podstawowe informacje
    
      Podstawowe informacje o pracy są wyświetlane w dolnej części panelu Podsumowanie zadań.
    
      ![Stan faz zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Wynik zadania: Powiodło się lub nie powiodło się. Zadanie może zakończyć się niepowodzeniem w każdej fazie.
    * Całkowity czas trwania: Czas zegara ściennego (czas trwania) między czasem przesyłania a czasem zakończenia.
    * Całkowity czas obliczeń: Suma każdego czasu wykonania wierzchołka, można uznać go za czas, że zadanie jest wykonywane tylko w jednym wierzchołku. Więcej informacji na temat wierzchołków można znaleźć w wierzchołku.
    * Czas przesyłania/rozpoczęcia/zakończenia: czas, w którym usługa Data Lake Analytics otrzymuje przesyłanie/rozpoczyna uruchamianie zadania/kończy zadanie pomyślnie lub nie.
    * Kompilacja/Kolejkowane/Uruchomione: Czas zegara ściennego spędzony podczas fazy przygotowywania/kolejkowania/uruchamiania.
    * Konto: konto Usługi Data Lake Analytics używane do uruchamiania zadania.
    * Autor: Użytkownik, który przesłał zadanie, może to być konto prawdziwej osoby lub konto systemowe.
    * Priorytet: Priorytet zadania. Im mniejsza liczba, tym wyższy priorytet. Wpływa tylko na sekwencję zadań w kolejce. Ustawienie wyższego priorytetu nie wywłaszcza uruchomionych zadań.
    * Równoległość: żądana maksymalna liczba równoczesnych jednostek analizy usługi Azure Data Lake (ADLAU), aka wierzchołków. Obecnie jeden wierzchołek jest równy jednej maszynie wirtualnej z dwoma rdzeniami wirtualnymi i sześcioma GB pamięci RAM, chociaż można go uaktualnić w przyszłych aktualizacjach usługi Data Lake Analytics.
    * Bajty w lewo: bajty, które muszą być przetwarzane do czasu zakończenia zadania.
    * Bajty odczytu/zapisu: Bajty, które zostały odczytane/zapisane od czasu rozpoczęcia pracy zadania.
    * Wierzchołki całkowite: Zadanie jest podzielone na wiele części pracy, każdy element pracy jest nazywany wierzchołkiem. Ta wartość opisuje, ile części pracy składa się z zadania. Wierzchołek można uznać za podstawową jednostkę procesu, aka Azure Data Lake Analytics Unit (ADLAU) i wierzchołki mogą być uruchamiane w równoległości. 
    * Zakończone/Uruchomione/Nie powiodło się: Liczba zakończonych/uruchomionych/nieudanych wierzchołków. Wierzchołki mogą zakończyć się niepowodzeniem z powodu błędów kodu użytkownika i systemu, ale system ponawia próby nie powiodło się vertices automatycznie kilka razy. Jeśli wierzchołek nadal nie powiedzie się po ponowieniu próby, całe zadanie zakończy się niepowodzeniem.
* Wykres zadań
  
    Skrypt U-SQL reprezentuje logikę przekształcania danych wejściowych do danych wyjściowych. Skrypt jest kompilowany i zoptymalizowany do planu wykonania fizycznego w fazie przygotowywania. Wykres zadania jest pokazanie planu wykonania fizycznego.  Poniższy diagram ilustruje proces:
  
    ![Stan faz zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Praca jest podzielona na wiele prac. Każdy element pracy jest nazywany wierzchołkiem. Wierzchołki są zgrupowane jako Super wierzchołek (aka etap) i wizualizowane jako wykres zadania. Zielone tabliczki sceniczne na wykresie zadań pokazują etapy.
  
    Każdy wierzchołek na etapie wykonuje ten sam rodzaj pracy z różnymi fragmentami tych samych danych. Na przykład jeśli masz plik z jednym tb danych i istnieją setki wierzchołków odczytu z niego, każdy z nich jest czytanie fragmentu. Wierzchołki te są zgrupowane na tym samym etapie i wykonują tę samą pracę na różnych fragmentach tego samego pliku wejściowego.
  
  * <a name="state-information"></a>Informacje o etapie
    
      Na danym etapie niektóre liczby są wyświetlane na plakacie.
    
      ![Etap wykresu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract: Nazwa etapu, nazwany przez liczbę i metodę operacji.
    * 84 wierzchołki: Całkowita liczba wierzchołków na tym etapie. Rysunek wskazuje, ile części pracy jest podzielonych na tym etapie.
    * 12,90 s/wierzchołek: Średni czas wykonywania wierzchołków dla tego etapu. Ta liczba jest obliczana przez SUMĘ (każdy czas wykonania wierzchołka) / (całkowita liczba wierzchołków). Oznacza to, że jeśli można przypisać wszystkie wierzchołki wykonywane w równoległości, cały etap jest zakończona w 12,90 s. Oznacza to również, że jeśli cała praca na tym etapie jest wykonywana szeregowo, koszt będzie #vertices * czasu AVG.
    * 850 895 wierszy zapisanych: Całkowita liczba wierszy napisana na tym etapie.
    * R/W: Ilość danych odczytanych/zapisanych na tym etapie w bajtach.
    * Kolory: Kolory są używane na stole montażowym w celu wskazania innego stanu wierzchołka.
      
      * Zielony oznacza, że wierzchołek zakończy się pomyślnie.
      * Pomarańczowy wskazuje, że wierzchołek jest ponowiony. Ponowiony wierzchołek nie powiódł się, ale został ponowiony automatycznie i pomyślnie przez system, a ogólny etap został zakończony pomyślnie. Jeśli wierzchołek został ponownie ponowiony, ale nadal nie powiódł się, kolor zmieni kolor na czerwony, a całe zadanie nie powiodło się.
      * Czerwony oznacza, że nie powiodło się, co oznacza, że pewien wierzchołek został ponowiony kilka razy przez system, ale nadal nie powiodło się. W tym scenariuszu powoduje, że całe zadanie zakończy się niepowodzeniem.
      * Niebieski oznacza, że pewien wierzchołek jest uruchomiony.
      * Biały oznacza, że wierzchołek to Oczekiwanie. Wierzchołek może czekać na zaplanowane, gdy adlau staje się dostępny lub może czekać na dane wejściowe, ponieważ jego dane wejściowe mogą nie być gotowe.
      
      Więcej szczegółów na stole montażowym można znaleźć, najeżdżając kursorem myszy o jeden stan:
      
      ![Szczegóły etapu pracy usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Wierzchołki: Opisuje szczegóły wierzchołków, na przykład, ile wierzchołków w sumie, ile wierzchołków zostały ukończone, są one nie powiodło się lub nadal uruchomione/oczekiwania, itp.
  * Dane odczytu krzyżowego / intra pod: Pliki i dane są przechowywane w wielu zasobnikach w rozproszonym systemie plików. Wartość w tym miejscu opisuje, ile danych zostało odczytywanych w tym samym zasobniku lub zasobniku krzyżowym.
  * Całkowity czas obliczeń: Suma każdego czasu wykonania wierzchołka na etapie, można uznać go za czas, jaki zajmie, jeśli wszystkie prace na etapie jest wykonywana tylko w jednym wierzchołku.
  * Dane i wiersze napisane/odczytane: Wskazuje, ile danych lub wierszy zostało odczytanych/zapisanych lub które muszą zostać odczytane.
  * Błędy odczytu wierzchołków: W tym artykule opisano, ile wierzchołków nie powiodło się podczas odczytu danych.
  * Zduplikowane wierzchołki odrzuca: Jeśli wierzchołek działa zbyt wolno, system może zaplanować wiele wierzchołków, aby uruchomić ten sam element pracy. Reducjoneracyjne wierzchołki zostaną odrzucone po pomyślnym zakończeniu jednego z wierzchołków. Zduplikowane wierzchołki odrzuca rejestruje liczbę wierzchołków, które są odrzucane jako duplikacji na stole montażowym.
  * Revocations wierzchołka: Wierzchołek został pomyślnie, ale uzyskać ponownie później z niektórych powodów. Na przykład jeśli wierzchołek niższego rzędu utraci pośrednie dane wejściowe, poprosi o ponowne uruchomienie wierzchołka nadrzędnego.
  * Wykonania harmonogramu wierzchołków: Całkowity czas, przez który zostały zaplanowane wierzchołki.
  * Odczyt danych Min/Average/Max Vertex: Minimum/średnia/maksimum każdego odczytu wierzchołków danych.
  * Czas trwania: Zegar ścienny trwa etap, musisz załadować profil, aby zobaczyć tę wartość.
  * Odtwarzanie zadania
    
      Usługa Data Lake Analytics uruchamia zadania i archiwizuje wierzchołki uruchomione informacje o zadaniach, takie jak czas uruchamiania, zatrzymywany, niewiedzy i sposób ich ponownego ponawianiu itp. Wszystkie informacje są automatycznie rejestrowane w magazynie zapytań i przechowywane w jego profilu zadania. Profil zadania można pobrać za pośrednictwem "Profilu obciążenia" w widoku zadania, a po pobraniu profilu zadania można wyświetlić odtwarzanie zadań.
    
      Odtwarzanie zadań jest uosobieniem wizualizacji tego, co wydarzyło się w klastrze. Pomaga obserwować postęp wykonywania zadań i wizualnie wykrywać anomalie wydajności i wąskie gardła w bardzo krótkim czasie (zwykle mniej niż 30s).
  * Wyświetlacz mapy ciepła zadania 
    
      Mapę cieplną zadania można wybrać za pomocą listy rozwijanej Wyświetl w wykresie zadań. 
    
      ![Wyświetlanie mapy stosu zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Pokazuje mapę cieplną we/wy, czas i przepływność zadania, dzięki której można znaleźć miejsce, w którym zadanie spędza większość czasu lub czy twoja praca jest zadaniem granicznym we/wy i tak dalej.
    
      ![Przykład mapy stosu zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Postęp: Postęp wykonywania zadania, zobacz Informacje w informacjach o etapie.
    * Odczyt/zapis danych: Mapa cieplna wszystkich danych odczytanych/zapisanych na każdym etapie.
    * Czas obliczania: Mapa cieplna SUMA (każdy czas wykonywania wierzchołków), można wziąć pod uwagę to, jak długo zajęłoby, jeśli wszystkie prace na etapie jest wykonywana tylko z 1 wierzchołkiem.
    * Średni czas wykonywania na węzeł: Mapa cieplna SUMA (każdy czas wykonywania wierzchołka) / (Liczba wierzchołków). Co oznacza, że jeśli można przypisać wszystkie wierzchołki wykonywane w równoległości, cały etap zostanie wykonany w tym przedziale czasu.
    * Przepustowość wejścia/wyjścia: Mapa cieplna przepływności wejścia/wyjścia każdego etapu, można potwierdzić, czy zadanie jest zadaniem związanym we/wy za pomocą tego.
* Operacje metadanych
  
    W skrypcie U-SQL można wykonywać niektóre operacje metadanych, takie jak tworzenie bazy danych, upuszczanie tabeli itp. Te operacje są wyświetlane w operacji metadanych po kompilacji. Można znaleźć potwierdzenia, tworzenie jednostek, upuszczanie jednostek tutaj.
  
    ![Operacje metadanych usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historia stanu
  
    Historia stanu jest również wizualizowane w podsumowaniu zadań, ale można uzyskać więcej szczegółów tutaj. Szczegółowe informacje można znaleźć, na przykład podczas przygotowywania zadania, umieszczania w kolejce, uruchamiania, zakończenia. Można również znaleźć, ile razy zadanie zostało skompilowane (CcsAttempts: 1), kiedy jest zadanie wysyłane do klastra faktycznie (Szczegóły: Wysyłanie zadania do klastra), itp.
  
    ![Historia stanu zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostyka
  
    Narzędzie automatycznie diagnozuje wykonywanie zadań. Otrzymasz alerty, gdy wystąpią pewne błędy lub problemy z wydajnością w zadaniach. Pamiętaj, że aby uzyskać pełne informacje tutaj, musisz pobrać profil. 
  
    ![Diagnostyka widoku zadań usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Ostrzeżenia: W tym miejscu pojawia się alert z ostrzeżeniem kompilatora. Możesz kliknąć łącze "x issue(s)", aby uzyskać więcej szczegółów po wyświetleniu alertu.
  * Wierzchołek działa zbyt długo: Jeśli w wierzchołku zabraknie czasu (powiedzmy 5 godzin), problemy zostaną znalezione tutaj.
  * Użycie zasobów: Jeśli przydzielono więcej lub za mało równoległości niż potrzeba, problemy zostaną znalezione tutaj. Ponadto można kliknąć użycie zasobów, aby wyświetlić więcej szczegółów i wykonać scenariusze warunkowe, aby znaleźć lepszą alokację zasobów (aby uzyskać więcej szczegółów, zobacz ten przewodnik).
  * Sprawdzanie pamięci: Jeśli dowolny wierzchołek używa więcej niż 5 GB pamięci, problemy zostaną znalezione tutaj. Wykonanie zadania może zostać zabite przez system, jeśli zużywa więcej pamięci niż ograniczenie systemu.

## <a name="job-detail"></a>Szczegóły zadania
Szczegóły zadania zawiera szczegółowe informacje o zadaniu, w tym skrypt, zasoby i widok wykonywania wierzchołka.

![Szczegóły zadania usługi Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skrypt
  
    Skrypt U-SQL zadania jest przechowywany w magazynie zapytań. Można wyświetlić oryginalny skrypt U-SQL i ponownie przesłać go w razie potrzeby.
* Resources
  
    Można znaleźć dane wyjściowe kompilacji zadań przechowywane w magazynie zapytań za pośrednictwem zasobów. Na przykład można znaleźć "algebra.xml", który jest używany do pokazu wykresu zadania, zestawy, które zarejestrowałeś, itp.
* Widok wykonania wierzchołka
  
    Pokazuje szczegóły wykonania wierzchołków. Profil zadania archiwizuje każdy dziennik wykonywania wierzchołków, taki jak całkowita liczba danych odczytu/zapisu, środowiska uruchomieniowego, stanu itp. Dzięki temu widokowi możesz uzyskać więcej szczegółów na temat sposobu, w jaki przebiegła praca. Aby uzyskać więcej informacji, zobacz [Użyj widoku wykonywania wierzchołków w narzędziach usługi Data Lake dla programu Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

## <a name="next-steps"></a>Następne kroki
* Aby rejestrować informacje diagnostyczne, zobacz [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)
* Aby uzyskać informacje na temat bardziej złożonego zapytania, zobacz temat [Analizowanie dzienników witryn sieci Web przy użyciu usługi Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Aby użyć widoku wykonywania wierzchołków, zobacz [Używanie widoku wykonywania wierzchołków w narzędziach usługi Data Lake dla programu Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

