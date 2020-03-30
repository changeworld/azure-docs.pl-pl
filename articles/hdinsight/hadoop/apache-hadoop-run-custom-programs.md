---
title: Uruchamianie niestandardowych programów MapReduce — Azure HDInsight
description: Kiedy i jak uruchomić niestandardowe programy Apache MapReduce w klastrach usługi Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645025"
---
# <a name="run-custom-mapreduce-programs"></a>Uruchamianie niestandardowych programów MapReduce

Systemy dużych zbiorów danych oparte na apache Hadoop, takie jak HDInsight, umożliwiają przetwarzanie danych przy użyciu szerokiej gamy narzędzi i technologii. W poniższej tabeli opisano główne zalety i zagadnienia dla każdego z nich.

| Mechanizm kwerendy | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- |
| **Ul Apache za pomocą HiveQL** | <ul><li>Doskonałe rozwiązanie do przetwarzania wsadowego i analizy dużych ilości niezmiennych danych, podsumowania danych i zapytań na żądanie. Używa znanej składni podobnej do SQL.</li><li>Może służyć do tworzenia trwałych tabel danych, które można łatwo podzielić na partycje i zindeksować.</li><li>Za pomocą tych samych danych można utworzyć wiele tabel i widoków zewnętrznych.</li><li>Obsługuje prostą implementację magazynu danych, która zapewnia ogromne możliwości skalowania w poziomie i odporności na uszkodzenia podczas przechowywania i przetwarzania danych.</li></ul> | <ul><li>Wymaga, aby dane źródłowe miały co najmniej pewną identyfikowalną strukturę.</li><li>Nie nadaje się do zapytań w czasie rzeczywistym i aktualizacji na poziomie wiersza. Najlepiej jest używane dla zadań wsadowych przez duże zestawy danych.</li><li>Może nie być w stanie wykonać niektórych typów złożonych zadań przetwarzania.</li></ul> |
| **Apache Pig za pomocą Łaciny Wieprzowej** | <ul><li>Doskonałe rozwiązanie do manipulowania danymi jako zestawów, scalania i filtrowania zestawów danych, stosowania funkcji do rekordów lub grup rekordów oraz do restrukturyzowania danych poprzez definiowanie kolumn, grupowanie wartości lub konwertowanie kolumn na wiersze.</li><li>Można użyć podejścia opartego na przepływie pracy jako sekwencji operacji na danych.</li></ul> | <ul><li>Użytkownicy SQL mogą znaleźć Pig Latin jest mniej znane i trudniejsze w użyciu niż HiveQL.</li><li>Domyślnym wyjściem jest zwykle plik tekstowy, a więc może być trudniejszy w użyciu z narzędziami wizualizacji, takimi jak Excel. Zazwyczaj warstwa tabeli gałęzi na dane wyjściowe.</li></ul> |
| **Niestandardowa mapa/redukcja** | <ul><li>Zapewnia pełną kontrolę nad mapą i zmniejszyć fazy i wykonanie.</li><li>Umożliwia optymalizację zapytań w celu osiągnięcia maksymalnej wydajności klastra lub zminimalizowania obciążenia serwerów i sieci.</li><li>Składniki mogą być zapisywane w wielu znanych językach.</li></ul> | <ul><li>Jest to trudniejsze niż użycie świni lub ula, ponieważ musisz utworzyć własną mapę i zmniejszyć składniki.</li><li>Procesy, które wymagają łączenia zestawów danych są trudniejsze do zaimplementowania.</li><li>Mimo że dostępne są struktury testowe, kod debugowania jest bardziej złożony niż normalna aplikacja, ponieważ kod jest uruchamiany jako zadanie wsadowe pod kontrolą harmonogramu zadań Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Abstrakcje szczegółów ścieżki magazynu, dzięki czemu administracja łatwiejsze i usunięcie potrzeby użytkowników, aby wiedzieć, gdzie dane są przechowywane.</li><li>Umożliwia powiadamianie o zdarzeniach, takich jak dostępność danych, umożliwiając innym narzędziom, takim jak Oozie, wykrywanie, kiedy wystąpiły operacje.</li><li>Udostępnia relacyjne widok danych, w tym partycjonowanie według klucza i sprawia, że dane są łatwo dostępne.</li></ul> | <ul><li>Domyślnie obsługuje formaty plików RCFile, CSV, JSON, SequenceFile i ORC, ale może być konieczne napisanie niestandardowego serde dla innych formatów.</li><li>HCatalog nie jest bezpieczny dla wątków.</li><li>Istnieją pewne ograniczenia dotyczące typów danych dla kolumn podczas korzystania z modułu ładującego HCatalog w skryptach Pig. Aby uzyskać więcej informacji, zobacz [HCatLoader typy danych](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) w apache HCatalog dokumentacji.</li></ul> |

Zazwyczaj używasz najprostszych z tych metod, które mogą zapewnić wymagane wyniki. Na przykład może być w stanie osiągnąć takie wyniki przy użyciu tylko Hive, ale dla bardziej złożonych scenariuszy może być konieczne użycie Pig, lub nawet napisać własną mapę i zmniejszyć składniki. Można również zdecydować, po eksperymentowaniu z Hive lub Pig, że niestandardowa mapa i zmniejszyć składniki może zapewnić lepszą wydajność, umożliwiając dostrojenie i optymalizację przetwarzania.

## <a name="custom-mapreduce-components"></a>Niestandardowe komponenty mapy/redukcji

Map/reduce kod składa się z dwóch oddzielnych funkcji zaimplementowanych jako **map** i **zmniejszyć** składniki. Składnik **mapy** jest uruchamiany równolegle na wielu węzłach klastra, każdy węzeł stosujący mapowanie do własnego podzbioru danych węzła. **Składnik reduce** zestawia i podsumowuje wyniki ze wszystkich funkcji mapy. Aby uzyskać więcej informacji na temat tych dwóch składników, zobacz [Użyj MapReduce w Hadoop na HDInsight](hdinsight-use-mapreduce.md).

W większości scenariuszy przetwarzania HDInsight jest prostsze i bardziej wydajne użycie abstrakcji wyższego poziomu, takich jak Pig lub Hive. Można również utworzyć mapę niestandardową i zmniejszyć składniki do użycia w skryptach hive do wykonywania bardziej zaawansowanych przetwarzania.

Niestandardowe składniki mapy/reduce są zazwyczaj zapisywane w języku Java. Hadoop udostępnia interfejs przesyłania strumieniowego, który umożliwia również składniki, które są rozwijane w innych językach, takich jak C#, F#, Visual Basic, Python i JavaScript.

* Aby uzyskać wskazówki dotyczące tworzenia niestandardowych programów Java MapReduce, zobacz [Tworzenie programów Java MapReduce dla usługi Hadoop w programie HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Rozważ utworzenie własnej mapy i zmniejszenie składników w następujących warunkach:

* Należy przetwarzać dane, które są całkowicie nieustrukturyzowane przez analizowanie danych i przy użyciu logiki niestandardowej w celu uzyskania informacji strukturalnych z niego.
* Chcesz wykonać złożone zadania, które są trudne (lub niemożliwe) do wyrażenia w Pig lub Hive bez uciekania się do tworzenia UDF. Na przykład może być konieczne użycie zewnętrznej usługi geokodowania do konwersji współrzędnych szerokości i długości geograficznej lub adresów IP w danych źródłowych na nazwy lokalizacji geograficznych.
* Chcesz ponownie użyć istniejącego kodu .NET, Python lub JavaScript w map/reduce składników przy użyciu interfejsu przesyłania strumieniowego Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Prześlij i uruchom swój niestandardowy program MapReduce

Najczęstsze programy MapReduce są zapisywane w javie i kompilowane do pliku jar.

1. Po opracowaniu, skompilowaniu i przetestowaniu programu MapReduce użyj `scp` polecenia, aby przesłać plik jar do headnode.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zastąp NAZWĘ KLASTRA nazwą klastra. Jeśli użyto hasła do zabezpieczenia konta SSH, zostanie wyświetlony monit o wprowadzenie hasła. Jeśli użyto certyfikatu, może być `-i` konieczne użycie tego parametru do określenia pliku klucza prywatnego.

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z sesji SSH, wykonać program MapReduce za pośrednictwem YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    To polecenie przesyła zadanie MapReduce do YARN. Plik wejściowy `/example/data/sample.log`jest , a `/example/data/logoutput`katalog wyjściowy jest . Plik wejściowy i wszystkie pliki wyjściowe są przechowywane w domyślnym magazynie klastra.

## <a name="next-steps"></a>Następne kroki

* [Użyj języka C# z mapreduce przesyłania strumieniowego na Apache Hadoop w HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Tworzenie aplikacji Apache Spark dla klastra usługi HDInsight za pomocą zestawu narzędzi Azure toolkit for Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Korzystanie z funkcji zdefiniowanych przez użytkownika języka Python (UDF) z gałęzią Apache i świnią Apache w formacie HDInsight](python-udf-hdinsight.md)
