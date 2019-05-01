---
title: Uruchamianie niestandardowych programów MapReduce — Azure HDInsight
description: Kiedy i jak uruchamianie niestandardowych programów MapReduce w HDInsight.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 5ed82fc21aedc9af394922059859f81cfba1867e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713090"
---
# <a name="run-custom-mapreduce-programs"></a>Uruchamianie niestandardowych programów MapReduce

Apache systemów opartych na usłudze Hadoop dane big data, takich jak HDInsight umożliwiają przetwarzanie danych przy użyciu szerokiej gamy narzędzi i technologii. W poniższej tabeli opisano główne zalety i wady każdego z nich.

| Mechanizm zapytań | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- |
| **Apache Hive przy użyciu HiveQL** | <ul><li>Idealne rozwiązanie do przetwarzania wsadowego i analizy dużych ilości danych niezmienne dla podsumowania danych i na żądanie zapytań. Używa dobrze znanej składni podobnego do SQL.</li><li>Może służyć do tworzenia tabel trwałych danych, które można łatwo podzielona na partycje i indeksowane.</li><li>Wiele tabel zewnętrznych i widoki mogą być tworzone za pośrednictwem tych samych danych.</li><li>Obsługuje ona implementację magazynu proste dane, która zapewnia ogromną możliwości skalowania w poziomie i odporności na uszkodzenia przechowywanie i przetwarzanie danych.</li></ul> | <ul><li>Wymaga to źródło danych ma co najmniej niektórych elementów struktury do zidentyfikowania.</li><li>Nie jest odpowiednia dla zapytań w czasie rzeczywistym i aktualizacje poziomu wierszy. Go najlepiej nadaje się do zadania usługi batch w dużych zestawach danych.</li><li>Nie może być możliwe przeprowadzenie niektórych typów zadań przetwarzania złożonych.</li></ul> |
| **Apache Pig, za pomocą Pig Latin** | <ul><li>Doskonałe rozwiązanie dla manipulowanie danymi, ponieważ Ustawia, scalania i zestawy danych, stosowania funkcji do rekordów lub grup rekordów, filtrowanie i restrukturyzacja danych przez definiowanie kolumn, według wartości grupowania lub za pomocą konwersji kolumn do wierszy.</li><li>Może używać podejścia opartego o przepływ pracy za pomocą sekwencji operacji na danych.</li></ul> | <ul><li>Użytkowników SQL może się okazać, że język Pig Latin jest mniej znanych i trudniejsze niż HiveQL korzystanie z.</li><li>Domyślne dane wyjściowe to zazwyczaj plik tekstowy i dlatego może być trudniejsze do korzystania z narzędzi do wizualizacji takich jak program Excel. Zwykle będzie warstwy tabeli programu Hive za pośrednictwem danych wyjściowych.</li></ul> |
| **Niestandardowe/mapreduce** | <ul><li>Zapewnia pełną kontrolę nad mapy i zmniejszyć etapów i wykonania.</li><li>Umożliwia ona kwerendy można zoptymalizować w celu uzyskania maksymalnej wydajności z klastra lub aby zminimalizować obciążenie na serwerach i w sieci.</li><li>Składniki mogą być napisane w szeregu dobrze znanych języków.</li></ul> | <ul><li>Jest trudniejsze niż korzystanie z języka Pig i Hive, ponieważ musi utworzyć własną mapę i zmniejszyć składników.</li><li>Procesy, które wymagają łączenie zestawów danych są trudniejsze do zaimplementowania.</li><li>Mimo, że brak dostępnych środowisk testowych, debugowanie kodu jest bardziej skomplikowane niż normalny aplikacji, ponieważ kod jest uruchamiany w trybie wsadowym pod kontrolą harmonogram zadań usługi Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Jego abstrakcję szczegółów ścieżki magazynu, co ułatwia administrowanie i usuwanie użytkownicy nie wiedzieć, gdzie dane są przechowywane.</li><li>Dzięki temu powiadomień o zdarzeniach, takich jak dostępność danych, dzięki czemu innych narzędzi, takich jak Oozie w celu wykrywania wystąpił błąd operacji.</li><li>On udostępnia relacyjny widok danych, w tym podział na partycje według klucza i ułatwia dostęp do danych.</li></ul> | <ul><li>Obsługuje ona RCFile i tekst w formacie CSV, tekst JSON, SequenceFile i ORC formaty plików domyślnie, ale konieczne może być napisać niestandardowego elementu SerDe, w przypadku innych formatów.</li><li>HCatalog nie jest metodą o bezpiecznych wątkach.</li><li>Istnieją pewne ograniczenia dotyczące typów danych dla kolumn podczas korzystania z modułu ładującego HCatalog w skryptów usługi Pig. Aby uzyskać więcej informacji, zobacz [typy danych HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) w dokumentacji programu Apache HCatalog.</li></ul> |

Zazwyczaj można użyć Najprostsza z tych metod, które mogą dostarczać wyniki, które są wymagane. Na przykład może być możliwe uzyskanie takich wyników przy użyciu tylko programu Hive, ale w przypadku bardziej złożonych scenariuszy może być konieczne korzystanie z języka Pig, lub nawet napisać własne mapy i zmniejszyć składników. Może również podjąć decyzję, po eksperymentowanie z programu Hive i Pig, niestandardowych mapowane i zmniejszyć składników może zapewnić lepszą wydajność, umożliwiając dostosowywanie i optymalizowanie przetwarzania.

## <a name="custom-mapreduce-components"></a>Składniki niestandardowe/mapreduce

Kod/mapreduce składa się z dwóch osobnych funkcji zaimplementowane jako **mapy** i **zmniejszyć** składników. **Mapy** składnik to równolegle w wielu węzłach klastra, każdy węzeł stosowanie mapowania do tego węzła podzbiór danych. **Zmniejszyć** składnika porównuje i podsumowuje wyniki z wszystkich funkcji mapy. Aby uzyskać więcej informacji na temat tych dwóch składników, zobacz [używanie MapReduce na platformie Hadoop w HDInsight](hdinsight-use-mapreduce.md).

W większości przypadków HDInsight przetwarzania jest prostszy i bardziej wydajne, aby użyć wyższego poziomu abstrakcji takich jak Pig i Hive. Może również utworzyć niestandardowe mapy i ograniczyć składniki do użycia w skrypty Hive w celu wykonywania przetwarzania bardziej zaawansowanych.

Składniki niestandardowe/mapreduce zwykle są napisane w języku Java. Hadoop dostarcza interfejs przesyłania strumieniowego, który umożliwia także składniki do użycia, które są opracowywane w innych językach, takich jak C#, F#, Visual Basic, Python i JavaScript.

* Aby uzyskać wskazówki na temat tworzenia niestandardowych programów MapReduce w języku Java, zobacz [programów opracowywanie MapReduce w języku Java dla usługi Hadoop w HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Należy rozważyć utworzenie własnego mapy i zmniejszyć składniki dla następujących warunków:

* Należy do przetwarzania danych, które są całkowicie bez struktury, analizy danych i korzystać z niestandardowej logiki w celu uzyskiwania ustrukturyzowanych informacji z niego.
* Chcesz wykonać złożone zadania, które są trudne (lub niemożliwe) do wyrażenia w Pig i Hive, bez konieczności uciekania się do tworzenia funkcji zdefiniowanej przez użytkownika. Może na przykład może być konieczne użycie zewnętrznej usługi geokodowania konwertować szerokości i współrzędne geograficzne lub adresów IP w danych źródłowych nazwy lokalizacji geograficznej.
* Chcesz ponownie użyć istniejącego kodu .NET, Python lub języka JavaScript/mapreduce składniki za pomocą usługi Hadoop, przesyłanie strumieniowe interfejsu.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Przekazywanie i uruchamianie niestandardowych programu MapReduce

Najbardziej typowe programów MapReduce są napisane w języku Java i kompilowane do pliku jar.

1. Po zostały opracowane, skompilowane i przetestowane programu MapReduce, użyj `scp` polecenie, aby przekazać plik jar z węzłem głównym.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zastąp **USERNAME** przy użyciu konta użytkownika SSH dla klastra. Zastąp **CLUSTERNAME** nazwą klastra. Aby zabezpieczyć konto SSH użyto hasła, monit o wprowadzenie hasła. Jeśli używany jest certyfikat może być konieczne użycie `-i` parametru, aby określić plik klucza prywatnego.

2. Nawiązać połączenie z klastrem przy użyciu [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. W sesji SSH należy wykonać programu MapReduce za pomocą usługi YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    To polecenie przesyła zadanie MapReduce usługi YARN. Plik wejściowy jest `/example/data/sample.log`, a katalog wyjściowy jest `/example/data/logoutput`. Plik wejściowy i wszystkie pliki wyjściowe są przechowywane do domyślnego magazynu klastra.

## <a name="next-steps"></a>Kolejne kroki

* [Użyj C# o MapReduce, przesyłanie strumieniowe na technologii Apache Hadoop w HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Używanie zestawu narzędzi platformy Azure dla środowiska Eclipse do tworzenia aplikacji platformy Apache Spark dla klastra usługi HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Funkcje (UDF) zdefiniowane przez użytkownika języka Python korzystanie z programu Apache Hive i Apache Pig w HDInsight](python-udf-hdinsight.md)
