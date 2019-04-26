---
title: Usługa Azure Data Lake Storage Gen2 Storm wydajności, wytyczne dotyczące dostosowywania | Dokumentacja firmy Microsoft
description: Usługa Azure Data Lake Storage Gen2 Storm wydajności, wytyczne dotyczące dostosowywania
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 2401c74b55df78014a2f642b5166b4cf0017d87d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60392589"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Wskazówki dotyczące systemu Storm w HDInsight i Azure Data Lake Storage Gen2 dostrajania wydajności

Uzyskaj informacje o czynnikach, które należy uwzględnić podczas dostrajania wydajności topologii systemu Storm w usłudze Azure. Na przykład ważne jest poznanie charakterystyki pracy wykonanej przez elementy spout i bolt (czy praca jest operacji We/Wy i pamięci). W tym artykule omówiono szereg wytycznych, w tym Rozwiązywanie typowych problemów dostrajania wydajności oprogramowania.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Storage Gen2**. Aby uzyskać instrukcje na temat jej tworzenia, zobacz [Szybki Start: Tworzenie magazynu konto analityczne](data-lake-storage-quickstart-create-account.md).
* **Klaster usługi Azure HDInsight** dzięki dostępowi do konta Data Lake Storage Gen2. Zobacz [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **Uruchomiony klaster systemu Storm na Data Lake Storage Gen2**. Aby uzyskać więcej informacji, zobacz [systemu Storm w HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Wytyczne dotyczące Data Lake Storage Gen2 dostrajania wydajności**.  Pojęcia związane z ogólnej wydajności, zobacz [Data Lake Storage Gen2 dostrajania wskazówki dotyczące wydajności](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Dostosować równoległość topologii

Dzięki temu można zwiększyć wydajność, zwiększając równocześnie z operacji We/Wy do i z Data Lake Storage Gen2. Topologii Storm ma zestaw konfiguracji, które określają równoległości:
* Liczba procesów roboczych (pracownicy są dystrybuowane równomiernie na maszynach wirtualnych).
* Liczba wystąpień funkcji wykonawczej spout.
* Liczba wystąpień funkcji wykonawczej bolt.
* Liczba zadań spout.
* Liczba zadań bolt.

Na przykład w klastrze z 4 maszyn wirtualnych i 4 procesów roboczych, executors 32 spout i 32 spout zadania i 256 bolt executors i 512 bolt zadań, należy wziąć pod uwagę następujące czynności:

Każdy nadzorcy, który jest węzłem procesu roboczego, ma pojedynczego procesu roboczego Java procesu maszyny wirtualnej (JVM). Tego procesu maszyny JVM zarządza 4 wątków spout i 64 wątki bolt. W każdym wątku zadania są uruchamiane sekwencyjnie. Za pomocą tej konfiguracji każdy wątek spout zawiera 1 zadanie, a każdy wątek bolt ma 2 zadania.

W Storm poniżej przedstawiono różne składniki zaangażowane i ich wpływ na stopień równoległości, do których masz:
* Węzeł główny (o nazwie Nimbus Storm) służy do przesyłania zadań i zarządzanie nimi. Węzły te nie mają wpływu na stopień równoległości.
* Węzły nadzorcy. HDInsight to odpowiada węzłem procesu roboczego maszyn wirtualnych platformy Azure.
* Zadania procesu roboczego są Storm procesy uruchomione na maszynach wirtualnych. Każde zadanie procesu roboczego odnosi się do wystąpienia maszyny wirtualnej Java. STORM dystrybuuje liczba procesów roboczych, Ciebie z węzłami procesu roboczego możliwie najbardziej równomiernie.
* Spout i elementu bolt wystąpień funkcji wykonawczej. Każde wystąpienie funkcji wykonawczej odnosi się do wątku działającego w ramach procesów roboczych (JVMs).
* Zadania STORM. Są to zadania logiczne, że każdy z nich wątki wykonywania. Nie ma to wpływu stopień równoległości, więc należy ocenić, jeśli potrzebujesz wielu zadań na wykonawcy.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Dla uzyskania optymalnej wydajności z Data Lake Storage Gen2

Podczas pracy z Data Lake Storage Gen2, możesz uzyskać najlepszą wydajność, jeśli Wykonano następujące czynności:
* COALESCE małe dołącza do większych rozmiarów.
* Wykonaj dowolną liczbę jednoczesnych żądań, jak to możliwe. Ponieważ każdy wątek bolt robi blokowania odczytów, chcesz mieć gdzieś w zakresie 8 12 wątków na każdy rdzeń. Dzięki temu karta sieciowa i dobrze wykorzystania Procesora. Większe maszyny Wirtualnej umożliwia większą liczbę jednoczesnych żądań.  

### <a name="example-topology"></a>Przykładową topologię

Załóżmy, że masz klaster węzła procesu roboczego 8 z maszyną Wirtualną Azure wystąpienie D13v2. Ta maszyna wirtualna ma 8 rdzeni, więc między 8 węzłów procesu roboczego, musisz 64 łączna liczba rdzeni.

Załóżmy, że robimy 8 wątków bolt na każdy rdzeń. Podany 64 rdzeni oznacza to, że chcemy, aby 512 bolt łączna liczba wystąpień funkcji wykonawczej (oznacza to, wątki). W tym przypadku Załóżmy, że firma Microsoft Rozpoczęcie jednej maszyny JVM na maszynę Wirtualną, a przede wszystkim użyć współbieżności wątku w ramach maszyny JVM, aby osiągnąć współbieżności. Oznacza to, że potrzebujemy 8 zadania procesu roboczego (po jednej na maszynie Wirtualnej platformy Azure) i 512 bolt executors. W takiej konfiguracji system Storm podejmie próbę dystrybucji pracowników równomiernie między węzłami procesu roboczego (znany także jako węzły nadzorcy), dzięki czemu każdy węzeł procesu roboczego 1 maszyny JVM. Teraz w ramach opiekunów, Storm podejmie próbę dystrybucji executors równomiernie między nadzorców, dzięki czemu każdy nadzorcy (czyli JVM) 8 wątków każdego.

## <a name="tune-additional-parameters"></a>Dostosuj parametry dodatkowe
Po umieszczeniu podstawową topologię, można rozważyć, czy chcesz dostosować dowolny z parametrów:
* **Liczba JVMs na węzeł procesu roboczego.** Jeśli masz strukturę dużych ilości danych (na przykład tabela odnośników) host w pamięci, każdej maszyny wirtualnej Java wymaga oddzielna kopia. Alternatywnie można użyć struktury danych w wielu wątkach, jeśli masz mniej JVMs. Dla elementu bolt we/wy liczba JVMs nie powoduje tyle różnica jako liczba wątków dodane w tych JVMs. Dla uproszczenia to dobry pomysł, aby mieć jeden JVM na proces roboczy. Zależności od tego, co robi Twoja bolt lub aplikacji przetwarzania możesz wymagać, chociaż może być konieczna zmiana tej liczby.
* **Liczba spout executors.** Ponieważ w poprzednim przykładzie użyto elementów bolt do zapisywania Data Lake Storage Gen2, liczba elementów spout nie jest bezpośrednio dotyczą wydajności elementu bolt. Jednak w zależności od ilości przetwarzania lub operacji we/wy wykonywane w spout jest dobry pomysł, aby dostosować elementy spout, aby uzyskać najlepszą wydajność. Upewnij się, że masz wystarczająco dużo elementów spout aby można było zachować elementy bolt zajęty. Stawki danych wyjściowych elementów spout powinna odpowiadać przepływność elementów bolt. Rzeczywista konfiguracja zależy od spout.
* **Liczba zadań.** Każdy element bolt działa jako pojedynczy wątek. Dodatkowe zadania na bolt nie oferują żadnych dodatkowych współbieżności. Jedyną sytuacją, w której są korzystne jest, jeśli proces o potwierdzenie krotki ma dużą część czasu wykonywania elementu bolt. To dobry pomysł, aby grupa czy wiele krotek do większego dołączyć zanim wyślesz do potwierdzenia od elementu bolt. W większości przypadków wielu zadań zapewniają tak, żadne dodatkowe korzyści.
* **Lokalnego lub grupowanie losowa.** Jeśli to ustawienie jest włączone, spójne kolekcje są wysyłane do elementów bolt w obrębie tego samego procesu roboczego. Zmniejsza to wywołań między procesami łączności i sieci. Jest to zalecane dla większości topologii.

Ten scenariusz podstawowy jest dobry punkt wyjścia. Testowanie z użyciem własnych danych, aby dostosować parametry poprzedni, aby osiągnąć optymalną wydajność.

## <a name="tune-the-spout"></a>Dostosowywanie spout

Można zmodyfikować następujące ustawienia, aby dostroić spout.

- **Limit czasu krotki: topology.message.timeout.secs**. To ustawienie określa ilość czasu, wiadomość ma wykonać w celu odbierania potwierdzenie, jest uznawane za nie powiodło się.

- **Maksymalny rozmiar pamięci dla procesu roboczego: worker.childopts**. To ustawienie umożliwia określenie dodatkowych parametrów wiersza polecenia dla pracowników języka Java. Najczęściej używane ustawienia w tym miejscu jest XmX, który określa maksymalnej pamięci przydzielonej do maszyny JVM sterty.

- **Spout maksymalna liczba oczekujących: topology.max.spout.pending**. To ustawienie określa liczbę krotek, które mogą być w locie (nie została jeszcze potwierdzony we wszystkich węzłach w topologii) na wątek spout w dowolnym momencie.

  Dobre obliczeń celu jest oszacować rozmiar każdego z krotek. Następnie określ ilość pamięci spout jeden wątek ma. Całkowita pamięć przydzielona do wątku, podzielona przez tę wartość, powinien zapewnić górną granicę dla max spout oczekujące parametru.

Domyślny element bolt Data Lake Storage Gen2 Storm ma rozmiar synchronizacji zasad parametr (fileBufferSize) można dostrajanie tego parametru.

W topologii I/intensywnie wyjścia jest dobrze mieć każdy wątek bolt zapisu do własnego pliku i ustawiania zasad rotacji pliku (fileRotationSize). Gdy plik osiągnie określony rozmiar, strumień automatycznie jest opróżniany, i jest zapisywany nowy plik. Rozmiar plików zalecany dla obrotu jest 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Monitorowanie topologii w Storm  
Topologia jest uruchomiona, można go monitorować za pomocą interfejsu użytkownika platformy Storm. Poniżej przedstawiono główne parametry, aby przyjrzeć się:

* **Opóźnienie wykonania całkowitej procesu.** Jest to Średni czas, jaki trwa po jednej krotce wyemitowane przez spout, przetwarzane przez elementy bolt i potwierdzone.

* **Opóźnienie procesu całkowita bolt.** Jest to Średni czas spędzany przez krotki na elementy bolt, dopóki nie odbierze potwierdzenia.

* **Opóźnienie wykonania całkowitej bolt.** Jest to Średni czas spędzony przez element bolt w metodzie execute.

* **Liczba błędów.** Dotyczy to liczba krotek, którego nie można w pełni przetworzony przed ich przekroczyło limit czasu.

* **Pojemność.** Jest to miara jest obciążenie systemu. Jeśli ta liczba wynosi 1, Twoje elementy bolt działają tak szybko, jak to możliwe. Jeśli jest mniejszy niż 1, należy zwiększyć równoległości. Jeśli jest większa niż 1, zmniejszenie równoległości.

## <a name="troubleshoot-common-problems"></a>Rozwiązywanie typowych problemów
Poniżej przedstawiono kilka typowych scenariuszy rozwiązywania problemów.
* **Wiele krotek przekraczają limit.** Spójrz na każdym węźle w topologii, aby określić, gdzie jest wąskie gardło. Najbardziej typową przyczyną tego jest, że elementy bolt nie będą mogli nadążyć za elementy spout. Prowadzi to do krotki zapychaniem bufory wewnętrzne oczekujących na przetworzenie. Należy rozważyć zwiększenie wartości limitu czasu lub Zmniejsz maksymalny spout oczekujące.

* **Istnieje opóźnienie wykonywania wysokiej łączna liczba procesów, ale opóźnienie procesu niski bolt.** W tym przypadku jest to możliwe, że kolekcje nie są przyjęte wystarczająco szybko. Sprawdź, czy istnieje wystarczająca liczba acknowledgers. Inną możliwością jest, że są one oczekujących w kolejce zbyt długo, przed rozpoczęciem bolt, ich przetworzeniu. Zmniejsz maksymalny spout oczekujące.

* **Występuje opóźnienie wykonania wysokiej bolt.** Oznacza to, że metoda execute() swoje bolt trwa zbyt długo. Optymalizuj kod, lub Przyjrzyj się rozmiaru operacji zapisu i opróżniania zachowanie.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 ograniczania
Jeśli napotkasz limity przepustowości, dostarczone przez Data Lake Storage Gen2, można napotkać niepowodzeń zadań. Sprawdź dzienniki zadania, aby błędy ograniczania przepływności. Aby zmniejszyć równoległości, zwiększenie rozmiaru kontenera.    

Aby sprawdzić, czy użytkownik jest ograniczany, Włącz debugowanie rejestrowania po stronie klienta:

1. W **Ambari** > **Storm** > **Config** > **zaawansowane storm-proces roboczy — log4j**, Zmień **&lt;poziom główny = "info"&gt;** do  **&lt;poziom główny = "debug"&gt;**. Uruchom ponownie wszystkie węzły/usługę konfiguracji zostały wprowadzone.
2. Monitorowanie topologii systemu Storm dzienniki na węzłach procesu roboczego (w obszarze /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;portu&gt;/worker.log) for Data Lake Storage Gen2 ograniczania wyjątków.

## <a name="next-steps"></a>Kolejne kroki
Dodatkową wydajność dostrajania dla systemu Storm można odwoływać się do [ten blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Dodatkowe przykład do uruchomienia, zobacz [wskazanego w serwisie GitHub](https://github.com/hdinsight/storm-performance-automation).
