---
title: 'Dostrajanie wydajności: Storm, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty firmy Microsoft'
description: Wskazówki dotyczące dostrajania wydajności usługi Azure Data Lake Storage Gen2 Storm
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 125c583512f6bae34c2dd3c3dd76a1b96a181ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327905"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Dostrajanie wydajności: Storm, HDInsight & Azure Data Lake Storage Gen2

Zrozumienie czynników, które należy wziąć pod uwagę podczas dostrojenia wydajności topologii usługi Azure Storm. Na przykład ważne jest, aby zrozumieć cechy pracy wykonanej przez wylewki i śruby (czy praca jest we/wy lub pamięci intensywne). W tym artykule omówiono szereg wskazówek dotyczących dostrajania wydajności, w tym rozwiązywanie typowych problemów.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje dotyczące tworzenia jednego z nich, zobacz [Szybki start: Tworzenie konta magazynu dla analitycznego](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** z dostępem do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Upewnij się, że włączysz pulpit zdalny dla klastra.
* **Uruchamianie klastra Storm w pamięci masowej Data Lake Gen2**. Aby uzyskać więcej informacji, zobacz [Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Wskazówki dotyczące dostrajania wydajności w programie Data Lake Storage Gen2**.  Aby zapoznać się z ogólnymi pojęciami dotyczącymi wydajności, zobacz [Wskazówki dotyczące dostrajania wydajności magazynu danych Lake Gen2.](data-lake-storage-performance-tuning-guidance.md)   

## <a name="tune-the-parallelism-of-the-topology"></a>Dostrajanie równoległości topologii

Można poprawić wydajność, zwiększając współbieżność we/wy do i z usługi Data Lake Storage Gen2. Topologia storm ma zestaw konfiguracji, które określają równoległość:
* Liczba procesów roboczych (pracownicy są równomiernie rozłożone na maszynach wirtualnych).
* Liczba wystąpień wykonawcy wylewki.
* Liczba wystąpień wykonawcy śruby.
* Liczba zadań wylewki.
* Liczba zadań śrubowych.

Na przykład w klastrze z 4 maszynami wirtualnymi i 4 procesami roboczymi, 32 wykonawcami wylewek i 32 zadaniami wylewki oraz 256 wykonawcami śrub i 512 zadaniami śrubowymi, należy wziąć pod uwagę następujące kwestie:

Każdy nadzorca, który jest węzłem procesu procesu procesu maszyny wirtualnej języka Java (JVM) jednego procesu roboczego. Ten proces JVM zarządza 4 gwintami wylewki i 64 gwintami śrubowymi. W ramach każdego wątku zadania są uruchamiane sekwencyjnie. W poprzedniej konfiguracji każdy wątek wylewki ma 1 zadanie, a każdy gwint śruby ma 2 zadania.

W Storm, oto różne składniki zaangażowane, i jak wpływają one na poziom równoległości masz:
* Węzeł główny (o nazwie Nimbus w burzy) służy do przesyłania zadań i zarządzania nimi. Te węzły nie mają wpływu na stopień równoległości.
* Węzły nadzorcy. W hdinsight odpowiada to węzła procesu roboczego maszyny Wirtualnej platformy Azure.
* Zadania robocze są procesami Storm uruchomionymi na maszynach wirtualnych. Każde zadanie robocze odpowiada wystąpieniu JVM. Storm rozdziela liczbę procesów roboczych, które określisz do węzłów procesu roboczego tak równomiernie, jak to możliwe.
* Wystąpienia wykonawcy wylewki i śruby. Każde wystąpienie executor odpowiada wątku uruchomionego w ramach pracowników (JVM).
* Zadania burzowe. Są to zadania logiczne, które uruchamiają każdy z tych wątków. Nie zmienia to poziomu równoległości, więc należy ocenić, czy potrzebujesz wielu zadań na wykonawcę, czy nie.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Uzyskaj najlepszą wydajność dzięki data lake storage gen2

Podczas pracy z data lake storage gen2, można uzyskać najlepszą wydajność, jeśli wykonujesz następujące czynności:
* Złączaj małe dodatki na większe rozmiary.
* Wykonaj dowolną liczbę równoczesnych żądań. Ponieważ każdy wątek śruby robi odczyty blokowania, chcesz mieć gdzieś w zakresie 8-12 wątków na rdzeń. Dzięki temu karta sieciowa i procesor są dobrze wykorzystywane. Większa maszyna wirtualna umożliwia więcej równoczesnych żądań.  

### <a name="example-topology"></a>Przykładowa topologia

Załóżmy, że masz klaster węzłów procesu roboczego 8 z maszyną wirtualną platformy Azure D13v2. Ta maszyna wirtualna ma 8 rdzeni, więc wśród 8 węzłów procesu roboczego masz 64 rdzenie.

Załóżmy, że robimy 8 gwintów śrubowych na rdzeń. Biorąc pod uwagę 64 rdzenie, oznacza to, że chcemy 512 całkowitych wystąpień wykonawca śruby (czyli wątki). W takim przypadku załóżmy, że zaczynamy od jednego JVM na maszynę wirtualną i głównie używać współbieżności wątku w ramach JVM do osiągnięcia współbieżności. Oznacza to, że potrzebujemy 8 zadań procesu roboczego (po jednym na maszynę wirtualną platformy Azure) i 512 wykonawców śrub. Biorąc pod uwagę tę konfigurację, Storm próbuje rozprowadzić pracowników równomiernie między węzłami procesu roboczego (znanymi również jako węzły nadzorcy), nadając każdemu węzłowi roboczemu 1 JVM. Teraz w nadzorców, Storm próbuje rozdzielić wykonawców równomiernie między nadzorców, dając każdemu przełożonemu (czyli JVM) 8 wątków każdy.

## <a name="tune-additional-parameters"></a>Dostrajanie dodatkowych parametrów
Po dokonaniu podstawowej topologii możesz zastanowić się, czy chcesz dostosować którykolwiek z parametrów:
* **Liczba JVM na węzeł procesu roboczego.** Jeśli masz strukturę dużych danych (na przykład tabelę odnośną), która jest hostowana w pamięci, każda usługa JVM wymaga osobnej kopii. Alternatywnie można użyć struktury danych w wielu wątkach, jeśli masz mniej jednostek JVM. W przypadku we/wy śruby liczba JVM nie ma tak dużej różnicy, jak liczba wątków dodanych w tych jvmach. Dla uproszczenia warto mieć jedną dżownicę JVM na pracownika. W zależności od tego, co robi śruba lub jakie przetwarzanie aplikacji jest wymagane, jednak może być konieczna zmiana tej liczby.
* **Liczba wykonawców wylewki.** Ponieważ w poprzednim przykładzie używa śrub do zapisu do usługi Data Lake Storage Gen2, liczba wylewek nie jest bezpośrednio istotne dla wydajności śruby. Jednak w zależności od ilości przetwarzania lub we/wy dzieje się w dziobek, to dobry pomysł, aby dostroić wylewki dla najlepszej wydajności. Upewnij się, że masz wystarczająco dużo wylewek, aby móc utrzymać śruby zajęte. Szybkość wyjściowa wylewek powinna odpowiadać przepływności śrub. Rzeczywista konfiguracja zależy od dziobka.
* **Liczba zadań.** Każda śruba działa jako pojedynczy wątek. Dodatkowe zadania na śrubę nie zapewniają żadnych dodatkowych współbieżności. Tylko wtedy, gdy są one korzystne jest, jeśli proces potwierdzania krotki zajmuje dużą część czasu wykonywania śruby. Warto pogrupować wiele krotek w większe dołączanie przed wysłaniem potwierdzenia ze śruby. Tak więc w większości przypadków wiele zadań nie zapewnia żadnych dodatkowych korzyści.
* **Grupowanie lokalne lub losowe.** Gdy to ustawienie jest włączone, krotek są wysyłane do śrub w ramach tego samego procesu roboczego. Zmniejsza to komunikację między procesami i połączenia sieciowe. Jest to zalecane dla większości topologii.

Ten podstawowy scenariusz jest dobrym punktem wyjścia. Przetestuj za pomocą własnych danych, aby dostosować poprzednie parametry, aby osiągnąć optymalną wydajność.

## <a name="tune-the-spout"></a>Dostrajanie dziobka

Można zmodyfikować następujące ustawienia, aby dostroić dziobek.

- **Limit czasu krotki: topology.message.timeout.secs**. To ustawienie określa czas potrzebny do ukończenia wiadomości i odebrania potwierdzenia, zanim zostanie uznany za nieudany.

- **Maksymalna ilość pamięci na proces roboczy: worker.childopts**. To ustawienie umożliwia określenie dodatkowych parametrów wiersza polecenia dla pracowników java. Najczęściej używanym ustawieniem jest w tym miejscu XmX, który określa maksymalną pamięć przydzieloną do sterty JVM.

- **Max wylewka oczekująca: topology.max.spout.pending**. To ustawienie określa liczbę krotek, które mogą być w locie (jeszcze nie potwierdzone we wszystkich węzłach w topologii) na wątek wylewki w dowolnym momencie.

  Dobrym obliczeniem jest oszacowanie rozmiaru każdej z krotek. Następnie dowiedzieć się, ile pamięci jeden wątek dziobek ma. Całkowita pamięć przydzielona do wątku, podzielona przez tę wartość, powinna dać górną granicę dla parametru oczekujące na wylewkę max.

Domyślna śruba burzowa data lake storage Gen2 ma parametr zasad synchronizacji rozmiaru (fileBufferSize), który może służyć do strojenia tego parametru.

W topologii intensywnie we/wy, to dobry pomysł, aby każdy wątek śruby zapisu do własnego pliku i ustawić zasady rotacji plików (fileRotationSize). Gdy plik osiągnie określony rozmiar, strumień jest automatycznie opróżniany i nowy plik jest zapisywany. Zalecany rozmiar pliku dla obrotu wynosi 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitoruj topologię w storm  
Gdy topologia jest uruchomiona, można ją monitorować w interfejsie użytkownika Storm. Oto główne parametry, na które należy zwrócić uwagę:

* **Całkowite opóźnienie wykonywania procesu.** Jest to średni czas, w tym jeden krotka ma być emitowany przez wylewkę, przetwarzane przez śrubę i potwierdzone.

* **Całkowite opóźnienie procesu śruby.** Jest to średni czas spędzony przez krotek na śrubę, dopóki nie otrzyma potwierdzenia.

* **Całkowite opóźnienie wykonania śruby.** Jest to średni czas spędzony przez śrubę w metodzie wykonania.

* **Liczba awarii.** Odnosi się to do liczby krotek, które nie zostały w pełni przetworzone przed ich upłynięciem.

* **Pojemność.** Jest to miara tego, jak zajęty jest twój system. Jeśli ta liczba wynosi 1, śruby działają tak szybko, jak to tylko możliwe. Jeśli jest mniejsza niż 1, należy zwiększyć równoległość. Jeśli jest większa niż 1, zmniejsz równoległość.

## <a name="troubleshoot-common-problems"></a>Rozwiązywanie typowych problemów
Oto kilka typowych scenariuszy rozwiązywania problemów.
* **Wiele krotek są limit czasu.** Spójrz na każdy węzeł w topologii, aby określić, gdzie znajduje się wąskie gardło. Najczęstszą przyczyną tego jest to, że śruby nie są w stanie nadążyć za dziobkami. Prowadzi to do krotek zatykania buforów wewnętrznych podczas oczekiwania na przetworzenie. Należy rozważyć zwiększenie wartości limitu czasu lub zmniejszenie max wylewka oczekujące.

* **Występuje duże opóźnienie wykonania procesu, ale opóźnienie procesu o niskiej śruby.** W takim przypadku jest możliwe, że krotki nie są uznawane wystarczająco szybko. Sprawdź, czy istnieje wystarczająca liczba osób melduje się. Inną możliwością jest to, że czekają w kolejce zbyt długo, zanim śruby zaczną je przetwarzać. Zmniejsz maksymalną wylewka oczekującą.

* **Występuje duże opóźnienie wykonania śruby.** Oznacza to, że metoda execute() śruby trwa zbyt długo. Zoptymalizuj kod lub spójrz na rozmiary zapisu i zachowanie opróżnienia.

### <a name="data-lake-storage-gen2-throttling"></a>Ograniczanie pamięci masowej w jeziorze danych Gen2
Jeśli osiągniesz limity przepustowości zapewniane przez program Data Lake Storage Gen2, mogą wystąpić błędy zadań. Sprawdź dzienniki zadań pod kątem błędów ograniczania przepustowości. Równoległość można zmniejszyć, zwiększając rozmiar kontenera.    

Aby sprawdzić, czy są coraz ograniczone, włącz rejestrowanie debugowania po stronie klienta:

1. W **Ambari** > **Storm** > **Config** > Advanced**storm-worker-log4j**, zmień ** &lt;poziom root="info"&gt; ** na ** &lt;poziom root="debug".&gt;** Uruchom ponownie wszystkie węzły/usługę, aby konfiguracja została zaaucna.
2. Monitorowanie dzienników topologii storm w węzłach procesu roboczego (w obszarze&lt;/var/log/storm/worker-artifacts/ TopologyName&gt;/&lt;port&gt;/worker.log) dla wyjątków ograniczania przepustowości magazynu usługi Data Lake Storage Gen2.

## <a name="next-steps"></a>Następne kroki
W [tym blogu](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)można odwoływać się do dodatkowego dostrajania wydajności dla storm.

Aby uzyskać dodatkowy przykład do uruchomienia, zobacz [ten na GitHub](https://github.com/hdinsight/storm-performance-automation).
