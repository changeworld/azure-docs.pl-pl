---
title: Dostosowywanie wskazówki dotyczące wydajności Storm Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dostosowywanie wskazówki dotyczące wydajności Storm Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 5ebca90ffd679de1c30d1bc324bf4f1c3b9f6f70
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198864"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Wskazówki dotyczące Storm w usłudze HDInsight i usługi Azure Data Lake Store dostrajania wydajności

Zrozumienie czynników, które należy uwzględnić podczas dostrajania wydajności topologii Azure Storm. Na przykład jest wziąć pod uwagę charakterystykę ilość pracy wykonanej w elementach spout i elementów bolt (czy praca jest we/wy lub pamięci). W tym artykule omówiono szereg wytycznych, w tym Rozwiązywanie typowych problemów z dostrajania wydajności.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje dotyczące jego tworzenia, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Klaster Azure HDInsight** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Uruchomiony klaster Storm w usłudze Data Lake Store**. Aby uzyskać więcej informacji, zobacz [Storm w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Wytyczne dotyczące usługi Data Lake Store dostrajania wydajności**.  Koncepcje ogólne wydajności dla [wskazówki dostrajania wydajności magazynu Lake danych](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>Dostosować równoległość topologii

Można zwiększyć wydajność, zwiększając współbieżności operacji We/Wy do i z usługi Data Lake Store. Topologii Storm ma zestaw konfiguracji, które określają równoległości:
* Liczba procesów roboczych (pracowników jest rozmieszczana równomiernie wzdłuż maszyn wirtualnych).
* Liczba wystąpień Moduł wykonujący elementy spout.
* Liczba wystąpień Moduł wykonujący bolt.
* Liczba zadań spout.
* Liczba zadań bolt.

Na przykład w klastrze z 4 maszyn wirtualnych i 4 procesów roboczych, modułów 32 spout i 32 spout zadania i modułów 256 bolt i 512 bolt zadań, należy rozważyć następujące kwestie:

Każdy przełożonego, który jest węzłem procesu roboczego, ma pojedynczego procesu roboczego procesu (JVM). maszyny wirtualnej Java. Ten proces JVM zarządza 4 wątków spout i 64 wątki bolt. W każdym wątku zadania są uruchamiane sekwencyjnie. Z tej konfiguracji każdy wątek spout ma 1 zadań, a każdy wątek bolt ma 2 zadania.

W Storm poniżej przedstawiono różne składniki zaangażowane i ich wpływ na stopień równoległości, do których masz:
* Węzła głównego (o nazwie Nimbus Storm) służy do przesyłania zadania i zarządzać nimi. Te węzły nie mają wpływu na stopień równoległości.
* Węzły przełożonego. W usłudze HDInsight odpowiada z węzłem procesu roboczego maszyny Wirtualnej platformy Azure.
* Zadania procesu roboczego są Storm procesów uruchomionych w maszynach wirtualnych. Każde zadanie procesu roboczego odpowiada wystąpienie maszyny wirtualnej Java. STORM dystrybuuje liczba procesów roboczych, określ z węzłami procesu roboczego możliwie najbardziej równomiernie.
* Spout i wystąpienia modułu wykonującego dla elementów bolt. Każde wystąpienie modułu wykonującego odpowiada wątku działających w pracowników (JVMs).
* Zadania STORM. Są to logiczne zadań, czy każdy z tych wątków wykonywania. Nie ma to wpływu stopień równoległości, dlatego należy ocenić, czy potrzebujesz wielu zadań na moduł wykonujący.

### <a name="get-the-best-performance-from-data-lake-store"></a>Uzyskać najlepszą wydajność z usługi Data Lake Store

Podczas pracy z usługą Data Lake Store, można uzyskać najlepszą wydajność, jeśli należy wykonać następujące czynności:
* Połączenie małe dołącza do większych rozmiarów (najlepiej 4 MB).
* Wykonaj dowolną liczbę jednoczesnych żądań, jak to możliwe. Ponieważ każdy wątek bolt robi blokowania odczytów, mają gdzieś w zakresie 8 12 wątków na podstawowe. Dzięki temu karta sieciowa, a także wykorzystania Procesora. Większe maszyny Wirtualnej umożliwia więcej jednoczesnych żądań.  

### <a name="example-topology"></a>Przykład topologii

Załóżmy, że masz klaster węzła procesu roboczego 8 z maszyny Wirtualnej Azure D13v2. Ta maszyna wirtualna ma 8 rdzenie, dlatego między węzłami procesu roboczego 8 należy 64 rdzenie całkowitej.

Załóżmy, że jak 8 wątków bolt na podstawowe. Podany 64 rdzenie oznacza to, że chcemy 512 całkowita bolt wystąpienia modułu wykonującego (to znaczy wątki). W takim przypadku Załóżmy, że możemy zaczynać się jeden JVM dla maszyny Wirtualnej i głównie używane do osiągnięcia współbieżności współbieżności wątku w ramach maszyny wirtualnej Java. Oznacza to, że potrzebujemy 8 zadania procesu roboczego (jeden na maszynie Wirtualnej platformy Azure), a 512 modułów bolt. W takiej konfiguracji Storm próbuje dystrybucji pracowników równomiernie między węzłami procesów roboczych (znanej także jako węzły przełożonego), zapewniając każdego węzła procesu roboczego 1 maszyny wirtualnej Java. Teraz w ramach kontrolerów, Storm próbuje dystrybucji modułów równomiernie między kontrolerów, nadanie każdej przełożonego (to znaczy JVM) 8 wątkami każdego.

## <a name="tune-additional-parameters"></a>Dostosuj dodatkowych parametrów
Po Topologia podstawowa, można rozważyć, czy chcesz dostosować wszystkie parametry:
* **Liczba JVMs na węzła procesu roboczego.** Jeśli masz struktury dużej ilości danych (na przykład tabeli odnośników) hosta w pamięci, każdej maszyny wirtualnej Java wymaga osobnej kopii. Alternatywnie można użyć struktury danych przez wiele wątków, jeśli masz mniej JVMs. Dla operacji We/Wy bolt liczba JVMs nie powoduje tyle różnica jako liczba wątków dodane w tych JVMs. Dla uproszczenia jest warto mieć jeden JVM na proces roboczy. W zależności od czynności użytkownika bolt lub aplikacji, jakie przetwarzania można wymagać, chociaż może być konieczna zmiana ta liczba.
* **Liczba modułów spout.** Ponieważ poprzedniego przykładu używa elementów bolt do zapisu do usługi Data Lake Store, liczba elementach spout nie jest bezpośrednio dotyczy wydajności bolt. Jednak w zależności od ilości przetwarzania lub operacji we/wy wykonywane w spout, jest dobrym rozwiązaniem, aby dostroić elementach spout, aby uzyskać najlepszą wydajność. Upewnij się, że masz wystarczająco dużo elementach spout, aby można było zachować zajęty elementów bolt. Dane wyjściowe stawki elementach spout powinna odpowiadać przepływność elementów bolt. Spout zależy od rzeczywistej konfiguracji.
* **Liczba zadań.** Każdy bolt działa jako pojedynczy wątek. Dodatkowe zadania na bolt nie zawierają żadnych dodatkowych współbieżności. Tylko wtedy, są one korzyści jest, jeśli procesu z potwierdzeniem spójnej kolekcji ma dużą część czasu wykonywania bolt. Jest dobrym pomysłem jest grupy się, że wiele krotek do większych Dołącz przed wysłaniem potwierdzenia z elementy bolt. Tak w większości przypadków wielu zadań stanowią żadne dodatkowe korzyści.
* **Lokalny lub losowa grupowania.** Jeśli to ustawienie jest włączone, spójne kolekcje są wysyłane do elementów bolt w obrębie tego samego procesu roboczego. Pozwala to ograniczyć wywołania między procesami komunikacji i sieci. Jest to zalecane w przypadku większości topologii.

Ten scenariusz podstawowy jest dobry punkt wyjścia. Test z użyciem własnych danych, aby dostosować poprzedniego parametrów, aby osiągnąć optymalną wydajność.

## <a name="tune-the-spout"></a>Dostosuj spout

Można zmodyfikować następujące ustawienia, aby dostroić spout.

- **Limit czasu krotki: topology.message.timeout.secs**. To ustawienie określa czas wiadomość ma zakończyć i odbieranie potwierdzenia, zanim zostanie to uznane za nie powiodło się.

- **Maksymalna liczba pamięci dla procesu roboczego: worker.childopts**. To ustawienie pozwala określić dodatkowe parametry wiersza polecenia do pracowników Java. Najczęściej używane ustawienia w tym miejscu jest XmX, określającą maksymalna ilość pamięci przydzielona do maszyny wirtualnej Java sterty.

- **Spout maksymalna liczba oczekujących: topology.max.spout.pending**. To ustawienie określa liczbę spójnych kolekcji, które mogą być w locie (nie została jeszcze potwierdzone we wszystkich węzłach w topologii) na wątek spout w dowolnym momencie.

 Dobrym obliczeń celu jest oszacowanie rozmiaru każdej z spójnych kolekcji. Następnie ustalić ma ilość pamięci spout jednego wątku. Łączna ilość pamięci przydzielona do wątku, podzielona przez wartość ta powinna zapewniają górna granica max spout oczekujących parametru.

## <a name="tune-the-bolt"></a>Dostosuj elementy bolt
Podczas pisania do usługi Data Lake Store, należy ustawić zasady synchronizacji rozmiaru (bufor po stronie klienta) do 4 MB. Opróżnianie lub hsync() następnie odbywa się tylko wtedy, gdy rozmiar buforu na tę wartość. Sterownik usługi Data Lake Store w procesie roboczym maszyny Wirtualnej automatycznie wykonuje to buforowania, chyba że jawnie przeprowadzić hsync().

Bolt Data Lake magazynu Storm domyślna ma parametr zasady synchronizacji rozmiar (fileBufferSize) używany do dopasowywania tego parametru.

W topologii/O wykonujących jest dobrym rozwiązaniem, aby każdy wątek bolt zapisu do własnego pliku, a aby ustawić zasady Obracanie pliku (fileRotationSize). Gdy plik osiągnie określony rozmiar, strumienia automatycznie jest opróżniany, i jest zapisywany nowy plik. Zalecany rozmiar obrotu jest 1 GB.

### <a name="handle-tuple-data"></a>Dojście spójnej kolekcji danych

W Storm spout utrzymuje do krotka dopóki jawnie zostaje potwierdzony przez elementy bolt. Jeśli krotka odczytane przez elementy bolt, ale nie został jeszcze potwierdzony, spout nie może mieć trwale do zaplecza usługi Data Lake Store. Po krotka zostaje potwierdzony, spout można zagwarantować trwałości przez elementy bolt i następnie można usunąć źródło danych z dowolnego źródła jest odczycie.  

Aby uzyskać najlepszą wydajność w usłudze Data Lake Store, ma elementy bolt buforu 4 MB danych spójnej kolekcji. Następnie zapisywać do usługi Data Lake Store zakończenia jako jeden zapis 4 MB. Po dane zostały pomyślnie zapisane w magazynie (przez wywołanie hflush()) elementy bolt można potwierdzić danych z powrotem na elementy spout. Jest to, czego bolt przykład podana tutaj. Jest również dopuszczalne do przechowania większej liczby krotek przed wywołanie hflush() i krotek potwierdzone. Jednak zwiększa liczbę spójnych kolekcji w locie spout potrzebuje do przechowywania, czy w związku z tym zwiększa ilość pamięci wymaganą przez poszczególne maszyny wirtualnej Java.

> [!NOTE]
W aplikacjach mogą być wymagane potwierdzić krotek częściej (w rozmiarach danych mniej niż 4 MB) innych niż — ze względu na wydajność. Jednak, który może mieć wpływ na przepustowość we/wy do magazynu wewnętrznej. Starannie porównać tej zależności względem wydajności operacji We/Wy bolt.

Jeśli szybkość odbierania krotek, nie jest duża, dlatego buforu 4 MB zajmuje dużo czasu do wypełnienia, należy rozważyć łagodzenie to przez:
* Zmniejszenie liczby elementów bolt, więc istnieje mniejszą liczbę buforów do wypełnienia.
* O zasady oparte na czasie lub na podstawie liczby, gdzie jest hflush() wyzwalane co x opróżnienia lub co milisekund y, a krotek zgromadzonych wykonanej do tej pory są potwierdzone Wstecz.

Należy zauważyć, że przepływność w tym przypadku jest niższa, ale z powolne częstość występowania zdarzeń, maksymalna przepustowość nie jest celem największych mimo to. Te czynniki pomóc zmniejszyć całkowity czas potrzebny krotka przepływ w magazynie. Może być niezależnie od tego, jeśli chcesz, aby w czasie rzeczywistym procesu, nawet w przypadku występowania zdarzeń niski. Zauważ również, czy Jeśli częstotliwość krotki przychodzących jest niska, należy zmienić parametr topology.message.timeout_secs tak krotki nie upłynął limit czasu, gdy są one pobierania buforowane lub przetworzone.

## <a name="monitor-your-topology-in-storm"></a>Monitorowanie topologii w Storm  
Topologii jest uruchomiona, można go monitorować w interfejsie użytkownika platformy Storm. Poniżej przedstawiono główne parametry przyjrzeć się:

* **Opóźnienie wykonywania procesu całkowitej.** Jest to Średni czas po jednej krotce emitowane przez elementy spout, przetwarzane przez elementy bolt i potwierdzone.

* **Bolt łączny czas oczekiwania procesu.** Jest to Średni czas spędzony przez spójna kolekcja znajdująca się na elementy bolt, dopóki nie odbierze potwierdzenia.

* **Całkowita liczba bolt wykonać opóźnienia.** Jest to Średni czas bolt w metody execute.

* **Liczba błędów.** Oznacza liczbę spójnych kolekcji, który nie może być całkowicie przetworzone przed limitu czasu.

* **Pojemność.** Jest to miara jest obciążenie systemu. Jeśli ta liczba jest 1, tak szybko, jak w pracy z elementów bolt. Jeśli jest mniejszy niż 1, należy zwiększyć równoległości. Jeśli jest większa niż 1, zmniejszenie równoległości.

## <a name="troubleshoot-common-problems"></a>Rozwiązywanie typowych problemów
Poniżej przedstawiono kilka typowych scenariuszy rozwiązywania problemów.
* **Limit czasu jest wiele spójnych kolekcji.** Spójrz na każdym węźle w topologii, aby określić, gdzie wąskie gardło. Najczęstszą przyczyną tego jest, że elementów bolt nie będą mogli przechowywać o elementach spout. Prowadzi to do krotek zapychaniem bufory wewnętrzne podczas oczekiwania na przetworzenie. Należy rozważyć, zwiększ wartość limitu czasu lub Zmniejsz max spout oczekujące.

* **Istnieje opóźnienie wykonywania wysokiej całkowita proces, ale opóźnienie procesu niski bolt.** W tym przypadku jest to możliwe, że krotki nie są przyjęte wystarczająco szybko. Sprawdź, czy istnieje wystarczająca liczba acknowledgers. Inną możliwością jest, że są one oczekujących w kolejce zbyt długo, przed ich przetwarzania elementów bolt. Zmniejszyć maksymalną spout oczekujące.

* **Brak wysokiej bolt wykonania opóźnienia.** Oznacza to, że metoda metody execute() z bolt trwa zbyt długo. Optymalizuj kod, lub sprawdź zapisu rozmiary i opróżnić zachowanie.

### <a name="data-lake-store-throttling"></a>Ograniczenia przepustowości usługi Data Lake Store
Jeśli naciśniesz limity przepustowości udostępniane przez usługi Data Lake Store, mogą występować niepowodzeń zadań. Zapoznaj się z dziennikami zadań dla ograniczania błędy. Aby zmniejszyć równoległości, należy zwiększyć rozmiar kontenera.    

Aby sprawdzić, czy możesz są pobierania ograniczane, Włącz rejestrowanie po stronie klienta debugowania:

1. W **Ambari** > **Storm** > **Config** > **zaawansowane storm-procesu roboczego log4j**, zmień **&lt;główny poziom = "info"&gt;** do  **&lt;główny poziom = "debug"&gt;**. Uruchom ponownie wszystkie węzły/usługę konfiguracji zaczęły obowiązywać.
2. Monitor topologii Storm loguje węzłów procesu roboczego (w obszarze /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;portu&gt;/worker.log) dla usługi Data Lake Store ograniczania wyjątków.

## <a name="next-steps"></a>Kolejne kroki
Dostrajanie wydajności dla Storm może być przywoływany w [ten blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Na przykład dodatkowe do uruchomienia, zobacz [pokazanego w serwisie GitHub](https://github.com/hdinsight/storm-performance-automation).
