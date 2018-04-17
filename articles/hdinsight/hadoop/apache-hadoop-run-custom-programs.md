---
title: Uruchom niestandardowe programy MapReduce - Azure HDInsight | Dokumentacja firmy Microsoft
description: Kiedy i jak uruchomić niestandardowe programy MapReduce w usłudze HDInsight.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: 94d199642b409a2fd087ec1543651031a907d09f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-custom-mapreduce-programs"></a>Uruchamianie niestandardowych programów MapReduce

Systemy oparte na platformie Hadoop danych big data takie jak HDInsight włączyć przetwarzania danych przy użyciu szeroką gamę narzędzi i technologii. W poniższej tabeli opisano główne zalety i zagadnienia dotyczące każdej z nich.

| Mechanizm zapytań | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- |
| **Gałąź rejestru przy użyciu HiveQL** | <ul><li>Idealnym rozwiązaniem służącym do przetwarzania wsadowego i analizy dużych ilości danych niezmienne dla podsumowania danych i na żądanie zapytań. Używa zwykłego składni przypominającego SQL.</li><li>Może służyć do tworzenia tabel trwałych danych, które można łatwo podzielona na partycje i indeksowane.</li><li>Wiele tabel zewnętrznych i widoki mogą być tworzone za pośrednictwem tych samych danych.</li><li>Obsługuje ona implementację magazynu proste danych, która oferuje duże możliwości skalowalność i odporność na uszkodzenia dla magazynu danych i przetwarzania.</li></ul> | <ul><li>Wymaga to źródło danych ma co najmniej niektórych do zidentyfikowania struktury.</li><li>Nie jest odpowiedni dla zapytań w czasie rzeczywistym i aktualizacje poziomu wiersza. Najlepiej służy do zadań wsadowych za pośrednictwem dużych zestawów danych.</li><li>Może nie być możliwe przeprowadzenie niektórych typów złożonych zadań.</li></ul> |
| **Przy użyciu Pig Latin pig** | <ul><li>Idealnym rozwiązaniem manipulowanie dane jako Ustawia, scalanie i filtrowanie zestawów danych, stosowanie funkcji do rekordów lub grupy rekordów oraz restrukturyzacji danych, definiując kolumn, grupowania wartości lub konwertując kolumn do wierszy.</li><li>Metoda przepływu pracy może używać jako sekwencja operacji na danych.</li></ul> | <ul><li>Użytkowników SQL może się okazać, że Pig Latin jest mniej znanych i bardziej trudny do wykorzystania niż HiveQL.</li><li>Domyślne dane wyjściowe zazwyczaj jest to plik tekstowy i dlatego może być trudniejsze do korzystania z narzędzi wizualizacji, takich jak Excel. Zwykle będzie warstwy tabeli programu Hive za pośrednictwem danych wyjściowych.</li></ul> |
| **Niestandardowe mapy/zmniejszyć** | <ul><li>Zawiera pełną kontrolę nad mapy i zmniejszyć etapów i wykonania.</li><li>Umożliwia on kwerendy można zoptymalizować, aby osiągnąć maksymalną wydajność z klastra lub aby zminimalizować obciążenie serwerów i sieci.</li><li>Składniki mogą być napisane w zakresie dobrze znanego języków.</li></ul> | <ul><li>Jest trudniej niż przy użyciu Pig lub Hive, ponieważ musisz utworzyć własne mapy i zmniejszyć składników.</li><li>Procesy, które wymagają łączenie zestawów danych są trudne do zaimplementowania.</li><li>Mimo że platform testów są dostępne, debugowanie kodu jest bardziej złożone niż normalne aplikacji, ponieważ kod jest uruchomiony w trybie wsadowym pod kontrolą harmonogramu zadań usługi Hadoop.</li></ul> |
| **HCatalog** | <ul><li>Go abstracts szczegóły ścieżki magazynu, ułatwiając administrowanie i usuwanie użytkownicy wiedzieć, gdzie są przechowywane dane.</li><li>Dzięki temu powiadomienia o zdarzeniach, takie jak dostępność danych, umożliwiając innych narzędzi, takich jak Oozie wykrycie, kiedy wystąpiły operacje.</li><li>Przedstawia relacyjny widok danych, w tym partycjonowanie przez klucz, a ułatwia dostęp do danych.</li></ul> | <ul><li>Obsługuje ona RCFile, tekst w formacie CSV, tekst JSON, SequenceFile i ORC formatów plików domyślnie, ale może być konieczne zapisu niestandardowych SerDe dla innych formatach.</li><li>HCatalog nie jest bezpieczne wątkowo.</li><li>Istnieją pewne ograniczenia dotyczące typów danych dla kolumny, używając modułu ładującego HCatalog w skryptów usługi Pig. Aby uzyskać więcej informacji, zobacz [typy danych HCatLoader](http://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) w dokumentacji Apache HCatalog.</li></ul> |

Należy zwykle użyć, Najprostszym z tych sposobów, które można udostępnić wyników, które są wymagane. Na przykład można osiągnąć takie wyniki za pomocą tylko gałąź, ale w bardziej złożonych scenariuszach może być konieczne korzystanie z języka Pig, lub nawet napisać własny mapy i zmniejszyć składników. Może również podjąć decyzję, po eksperymentowanie z Hive i Pig, tym mapy niestandardowe i zmniejszyć składników może zapewnić lepszą wydajność dzięki możliwości dostosowania i zoptymalizować przetwarzania.

## <a name="custom-mapreduce-components"></a>Niestandardowe mapy/zmniejszyć składników

Kod mapy/zmniejszyć składa się z dwóch osobnych funkcji zaimplementowane jako **mapy** i **zmniejszyć** składników. **Mapy** składnika jest uruchamiane równolegle na wielu węzłach klastra, każdy węzeł stosowanie mapowania do tego węzła podzbiór danych. **Zmniejszyć** składnika posortuje i podsumowuje wyniki ze wszystkich funkcji mapy. Aby uzyskać więcej informacji na temat tych dwóch składników, zobacz [MapReduce używany w Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md).

W większości przypadków przetwarzania HDInsight jest prostszy i efektywniejszy do użycia na wyższym poziomie abstrakcji, takie jak Pig lub Hive. Można również utworzyć niestandardowe mapy i zmniejszyć składników do użytku w gałęzi skryptów do bardziej zaawansowanych przetwarzania.

Niestandardowe mapy/zmniejszyć składniki zwykle są napisane w języku Java. Hadoop udostępnia interfejsem przesyłania strumieniowego, który umożliwia także składniki do użycia, które są tworzone w innych językach, takich jak C#, F #, Visual Basic, Python i JavaScript.

* Aby uzyskać wskazówki na temat tworzenia niestandardowych programów Java MapReduce, zobacz [programy opracowanie MapReduce Java dla platformy Hadoop w usłudze HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).
* Aby zapoznać się przykładem przy użyciu języka Python, zobacz [opracowanie Python dla usługi HDInsight przesyłania strumieniowego programy MapReduce](apache-hadoop-streaming-python.md).

Należy rozważyć utworzenie własnych mapy i zmniejszyć składniki następujące warunki:

* Należy do przetwarzania danych, które są całkowicie niestrukturalnych analizy danych, a następnie użyć niestandardowej logiki można uzyskać informacji z niego.
* Aby wykonać złożone zadania, które są trudne (lub niemożliwe) można wyrazić w Pig lub Hive, bez konieczności tworzenia funkcji zdefiniowanej przez użytkownika. Na przykład może być konieczne przy użyciu usługi zewnętrzne geokodowanie konwertować szerokości geograficznej i współrzędne geograficzne lub adresów IP w danych źródłowych nazwy lokalizacji geograficznej.
* Chcesz ponownie użyć istniejącego kodu platformy .NET, Python lub JavaScript w składnikach mapy/zmniejszyć za pomocą Hadoop interfejsu strumienia.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Przekazywanie i uruchomić program MapReduce niestandardowych

Najczęściej programów MapReduce są napisane w języku Java i kompilowane do pliku jar.

1. Po zostały opracowane, skompilowany i przetestowane z programem MapReduce, użyj `scp` polecenie, aby przesłać plik jar do headnode.

    ```bash
    scp mycustomprogram.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zastąp **USERNAME** przy użyciu konta użytkownika SSH dla klastra. Zastąp **CLUSTERNAME** z nazwą klastra. Aby zabezpieczyć konto SSH użyto hasła, monit o wprowadzenie hasła. Jeśli używasz certyfikatu może być konieczne użycie `-i` parametr, aby określić plik klucza prywatnego.

2. Połączyć, używając klastra [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. W sesji SSH należy wykonać program MapReduce za pośrednictwem YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    To polecenie przesyła zadanie MapReduce YARN. Plik wejściowy jest `/example/data/sample.log`, i katalog wyjściowy jest `/example/data/logoutput`. Pliku wejściowego oraz wszelkie pliki wyjściowe są zapisywane do magazynu domyślnego dla klastra.

## <a name="next-steps"></a>Kolejne kroki

* [Za pomocą języka C# MapReduce, przesyłanie strumieniowe na platformie Hadoop w usłudze HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Tworzenie programów Java MapReduce dla platformy Hadoop w usłudze HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Opracowywanie Python przesyłania strumieniowego programy MapReduce dla usługi HDInsight](apache-hadoop-streaming-python.md)
* [Tworzenie aplikacji Spark dla klastra usługi HDInsight za pomocą narzędzi Azure dla programu Eclipse](../spark/apache-spark-eclipse-tool-plugin.md)
* [Funkcje (UDF) za pomocą technologii Hive i Pig zdefiniowane przez użytkownika Python użycia w usłudze HDInsight](python-udf-hdinsight.md)
