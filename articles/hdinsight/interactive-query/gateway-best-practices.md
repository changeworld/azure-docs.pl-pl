---
title: Głębokie nurkowanie bramy i najlepsze rozwiązania dotyczące gałęzi Apache w usłudze Azure HDInsight
description: Dowiedz się, jak poruszać się po najlepszych rozwiązaniach dotyczących uruchamiania zapytań hive za pomocą bramy usługi Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586979"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Głębokie nurkowanie bramy i najlepsze rozwiązania dotyczące gałęzi Apache w usłudze Azure HDInsight

Brama usługi Azure HDInsight (brama) jest frontendem HTTPS dla klastrów HDInsight. Brama jest odpowiedzialna za: uwierzytelnianie, rozpoznawanie hostów, odnajdowanie usług i inne przydatne funkcje niezbędne dla nowoczesnego systemu rozproszonego. Funkcje dostarczone przez bramę spowodować pewne obciążenie, dla których ten dokument opisze najlepsze rozwiązania do nawigacji. Omówione są również techniki rozwiązywania problemów z bramą.

## <a name="the-hdinsight-gateway"></a>Brama HDInsight

Brama HDInsight jest jedyną częścią klastra HDInsight, która jest publicznie dostępna przez Internet. Dostęp do usługi Gateway można uzyskać bez przechodzenia `clustername-int.azurehdinsight.net` przez publiczny Internet przy użyciu punktu końcowego bramy wewnętrznej. Wewnętrzny punkt końcowy bramy umożliwia nawiązywanie połączeń z usługą bramy bez zamykania sieci wirtualnej klastra. Brama obsługuje wszystkie żądania, które są wysyłane do klastra i przekazuje takie żądania do odpowiednich składników i hostów klastra.

Poniższy diagram zawiera przybliżone ilustracji, jak brama zapewnia abstrakcję przed wszystkich różnych możliwości rozpoznawania hosta w ramach usługi HDInsight.

![Diagram rozpoznawania hostów](./media/gateway-best-practices/host-resolution-diagram.png "Diagram rozpoznawania hostów")

## <a name="motivation"></a>Motywacja

Motywacją do umieszczania bramy przed klastrami HDInsight jest zapewnienie interfejsu do odnajdowania usług i uwierzytelniania użytkowników. Mechanizmy uwierzytelniania dostarczane przez bramę są szczególnie istotne dla klastrów obsługujących protokół ESP.

W przypadku odnajdowania usług zaletą bramy jest to, że każdy składnik w `clustername.azurehdinsight.net/hive2`klastrze jest dostępny jako `host:port` inny punkt końcowy w witrynie sieci Web bramy ( ), w przeciwieństwie do wielu par.

W przypadku uwierzytelniania brama umożliwia `username:password` użytkownikom uwierzytelnianie przy użyciu pary poświadczeń. W przypadku klastrów obsługujących protokół ESP to poświadczenie będzie nazwą użytkownika i hasłem domeny. Uwierzytelnianie klastrów HDInsight za pośrednictwem bramy nie wymaga od klienta uzyskania biletu kerberos. Ponieważ brama `username:password` akceptuje poświadczenia i uzyskuje bilet protokołu Kerberos użytkownika w imieniu użytkownika, można nawiązać bezpieczne połączenia z bramą z dowolnego hosta klienta, w tym klientów przyłączonych do różnych domen AA-DDS niż klaster (ESP).

## <a name="best-practices"></a>Najlepsze rozwiązania

Brama to pojedyncza usługa (równoważona obciążeniem między dwoma hostami) odpowiedzialna za przekazywanie żądań i uwierzytelnianie. Brama może stać się wąskie gardło przepływności dla zapytań hive przekraczających określony rozmiar. Spadek wydajności kwerendy mogą być obserwowane, gdy bardzo duże kwerendy **SELECT** są wykonywane na bramie za pośrednictwem ODBC lub JDBC. "Bardzo duże" oznacza kwerendy, które tworzą więcej niż 5 GB danych w wierszach lub kolumnach. Ta kwerenda może zawierać długą listę wierszy i lub dużą liczbę kolumn.

Spadek wydajności bramy wokół kwerend o dużym rozmiarze jest, ponieważ dane muszą być przesyłane z magazynu danych źródłowych (ADLS Gen2) do: hdinsight hive server, bramy i wreszcie za pośrednictwem sterowników JDBC lub ODBC do hosta klienta.

Na poniższym diagramie przedstawiono kroki związane z kwerendą SELECT.

![Diagram wyników](./media/gateway-best-practices/result-retrieval-diagram.png "Diagram wyników")

Apache Hive jest abstrakcją relacyjną na szczycie systemu plików zgodnego z systemem plików HDFS. Ta abstrakcja oznacza **SELECT** instrukcji w gałęzi odpowiadają **operacji ODCZYT** w systemie plików. Operacje **READ** są tłumaczone na odpowiedni schemat przed zgłoszeniem do użytkownika. Opóźnienie tego procesu zwiększa się wraz z rozmiarem danych i całkowitymi przeskokami wymaganymi do dotarcia do użytkownika końcowego.

Podobne zachowanie może wystąpić podczas wykonywania **instrukcji CREATE** lub **INSERT** dużych danych, ponieważ te polecenia będą odpowiadać operacjom **ZAPISu** w podstawowym systemie plików. Rozważ zapisanie danych, takich jak surowy ORC, do systemu plików/datalake zamiast ładować go za pomocą **INSERT** lub **LOAD**.

W klastrach z włączoną funkcją pakietu zabezpieczeń dla przedsiębiorstw wystarczająco złożone zasady Apache Ranger mogą powodować spowolnienie czasu kompilacji kwerend, co może prowadzić do przekroczeniu limitu czasu bramy. Jeśli limit czasu bramy zostanie zauważony w klastrze ESP, należy rozważyć zmniejszenie lub połączenie liczby zasad ranger.

## <a name="troubleshooting-techniques"></a>Techniki rozwiązywania problemów

Istnieje wiele miejsc do łagodzenia i zrozumienia problemów z wydajnością spełnione w ramach powyższego zachowania. Użyj następującej listy kontrolnej, gdy występuje spadek wydajności kwerendy przez bramę USŁUGI HDInsight:

* Użyj klauzuli **LIMIT** podczas wykonywania dużych kwerend **SELECT.** Klauzula **LIMIT** zmniejszy sumę wierszy zgłoszonych do hosta klienta. Klauzula **LIMIT** wpływa tylko na generowanie wyników i nie zmienia planu kwerend. Aby zastosować klauzulę **LIMIT** do planu `hive.limit.optimize.enable`kwerend, użyj konfiguracji . **LIMIT** można łączyć z przesunięciem za pomocą formularza **argumentu LIMIT x,y**.

* Nazwij interesujące kolumny podczas uruchamiania kwerend **SELECT** zamiast **wybierać \* **. Wybranie mniejszej liczby kolumn spowoduje zmniejszenie ilości odczytu danych.

* Spróbuj uruchomić kwerendę zainteresowania za pośrednictwem Apache Beeline. Jeśli pobieranie wyników za pośrednictwem Apache Beeline zajmuje dłuższy czas, należy spodziewać się opóźnień podczas pobierania tych samych wyników za pomocą narzędzi zewnętrznych.

* Przetestuj podstawowe zapytanie gałęzi, aby upewnić się, że można ustanowić połączenie z bramą HDInsight. Spróbuj uruchomić podstawowe zapytanie z dwóch lub więcej narzędzi zewnętrznych, aby upewnić się, że żadne pojedyncze narzędzie nie jest uruchomione w problemach.

* Przejrzyj alerty Apache Ambari, aby upewnić się, że usługi HDInsight są w dobrej kondycji. Alerty Ambari można zintegrować z usługą Azure Monitor do raportowania i analizy.

* Jeśli używasz zewnętrznego magazynu hive metastore, sprawdź, czy usługa Azure SQL DB DTU dla magazynu hive metastore nie osiągnęła limitu. Jeśli DTU zbliża się do limitu, należy zwiększyć rozmiar bazy danych.

* Upewnij się, że wszystkie narzędzia innych firm, takie jak PBI lub Tableau, używają podziałów na strony do wyświetlania tabel lub baz danych. Skonsultuj się z partnerami pomocy technicznej, aby uzyskać pomoc w sprawie podziałania na strony. Głównym narzędziem używanym do podziałania `fetchSize` na strony jest parametr JDBC. Mały rozmiar pobierania może spowodować obniżenie wydajności bramy, ale rozmiar pobierania zbyt duży może spowodować limit czasu bramy. Dostrajanie rozmiaru pobierania musi odbywać się na podstawie obciążenia.

* Jeśli potok danych obejmuje odczyt dużej ilości danych z magazynu źródłowego klastra HDInsight, należy rozważyć użycie narzędzia, które łączy się bezpośrednio z usługą Azure Storage, takich jak usługa Azure Data Factory

* Rozważ użycie apache hive LLAP podczas uruchamiania obciążeń interaktywnych, ponieważ LLAP może zapewnić płynniejsze środowisko szybkiego zwracania wyników kwerendy

* Należy rozważyć zwiększenie liczby wątków dostępnych dla usługi `hive.server2.thrift.max.worker.threads`Hive Metastore przy użyciu . To ustawienie jest szczególnie istotne, gdy duża liczba równoczesnych użytkowników przesyła zapytania do klastra

* Zmniejsz liczbę ponownych prób użycia do dotarcia do bramy z dowolnych narzędzi zewnętrznych. Jeśli jest używanych wiele ponownych prób, należy rozważyć następujące wykładnicze wycofywanie się z zasad ponawiania

* Rozważ włączenie funkcji Gałąź `hive.exec.compress.output` `hive.exec.compress.intermediate`kompresji przy użyciu konfiguracji i .

## <a name="next-steps"></a>Następne kroki

* [Apache Beeline na HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Kroki rozwiązywania problemów z limitem czasu bramy HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Sieci wirtualne dla HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight z trasą ekspresową](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)