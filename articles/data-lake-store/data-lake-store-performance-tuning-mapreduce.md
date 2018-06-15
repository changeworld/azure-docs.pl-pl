---
title: Dostosowywanie wskazówki dotyczące wydajności MapReduce usługi Azure Data Lake Store | Dokumentacja firmy Microsoft
description: Dostosowywanie wskazówki dotyczące wydajności MapReduce usługi Azure Data Lake Store
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
ms.openlocfilehash: f5586e7706d4dad7e3c943b2a661fa296b4d30bf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198637"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Wskazówki dotyczące MapReduce na HDInsight i usługi Azure Data Lake Store dostrajania wydajności

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.
* **W usłudze HDInsight przy użyciu MapReduce**.  Aby uzyskać więcej informacji, zobacz [MapReduce używany w Hadoop w usłudze HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Wytyczne dotyczące ADLS dostrajania wydajności**.  Pojęcia dotyczące ogólnej wydajności, aby zapoznać [Data Lake magazynu dostrajanie wytyczne dotyczące wydajności](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Podczas uruchamiania zadań MapReduce, poniżej przedstawiono najważniejsze parametry, które można skonfigurować w celu zwiększenia wydajności na ADLS:

* **Mapreduce.map.Memory.MB** — ilość pamięci do przydzielenia do każdego mapowania
* **Mapreduce.job.Maps** — liczba zadań mapy na zadanie
* **Mapreduce.Reduce.Memory.MB** — ilość pamięci do przydzielenia do każdego reduktor
* **Mapreduce.job.reduces** — liczba zadań Zmniejsz na zadanie

**Mapreduce.map.Memory / Mapreduce.reduce.memory** ta liczba powinna dostosowywane w zależności od ilości pamięci jest wymagana dla mapy i/lub Zmniejsz zadań.  Wartości domyślne mapreduce.map.memory i mapreduce.reduce.memory można wyświetlić w Ambari za pomocą konfiguracji Yarn.  W Ambari przejdź do YARN, a następnie Wyświetl kartę Configs.  Pojawi się pamięci YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** określi maksymalną liczbę mapowań lub reduktory ma zostać utworzony.  Liczba podziałów określi, ile mapowań zostaną utworzone dla zadania MapReduce.  W związku z tym może pobrać mapowań, mniejsze niż wymagane, jeśli istnieją podziałów mniejszą niż liczba mapowań żądanie.       

## <a name="guidance"></a>Wskazówki

**Krok 1: Określanie liczba zadań uruchomionych** — domyślnie MapReduce użyje całego klastra dla zadania.  Przy użyciu mapowań, mniejsze niż jest dostępne kontenery, można użyć mniej klastra.  Wskazówki zawarte w tym dokumencie przyjęto założenie, że aplikacja jest tylko aplikacja była uruchomiona w klastrze.      

**Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory** — rozmiar pamięci dla mapy co pozwala zmniejszyć liczbę zadań będzie zależało od określonego zadania.  Można zmniejszyć rozmiar pamięci, aby zwiększyć współbieżność.  Liczbę jednocześnie uruchomionych zadań zależy od liczbę kontenerów.  Przez zmniejszenie ilości pamięci dla mapowania lub reduktor, można utworzyć więcej kontenerów, umożliwiające więcej mapowań lub reduktory można uruchamiać jednocześnie.  Zbyt dużo zmniejszenie ilości pamięci może spowodować niektóre procesy mało pamięci.  Jeśli zostanie wyświetlony błąd stosu podczas uruchamiania zadania, należy zwiększyć ilość pamięci na mapowania lub reduktor.  Należy rozważyć dodanie więcej kontenerów spowoduje dodanie czy bardzo ogólnych, dla każdego kontenera dodatkowe, które potencjalnie może zmniejszyć wydajność.  Alternatywą jest uzyskanie większej ilości pamięci za pomocą klastra, który ma większych ilości pamięci lub zwiększenie liczby węzłów w klastrze.  Więcej pamięci umożliwi więcej kontenerów, które mają być używane, co oznacza, że więcej współbieżności.  

**Krok 3: Określanie YARN całkowitej pamięci** — aby dostroić mapreduce.job.maps/mapreduce.job.reduces, należy rozważyć ilość całkowitej ilości pamięci YARN dostępne do użycia.  Te informacje są dostępne w narzędzia Ambari.  Przejdź do YARN, a następnie Wyświetl kartę Configs.  Pamięć YARN jest wyświetlany w tym oknie.  Należy pomnożyć pamięci YARN z liczbą węzłów w klastrze można pobrać całkowitej ilości pamięci YARN.

    Total YARN memory = nodes * YARN memory per node
Jeśli używane są puste klastra, pamięci może być pamięć YARN dla klastra.  Jeśli inne aplikacje korzystają z pamięci, można wybrać do użycia tylko część klastra pamięci dzięki zmniejszeniu liczby mapowań lub reduktory liczba kontenerów, do których chcesz użyć.  

**Krok 4: Obliczyć liczbę kontenerów YARN** — YARN kontenery dyktowania ilość współbieżności dostępne dla zadania.  Pobrać YARN pamięć i przez mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Ustawianie mapreduce.job.maps/mapreduce.job.reduces** ustawioną mapreduce.job.maps/mapreduce.job.reduces co najmniej liczba dostępnych kontenerów.  Możesz eksperymentować przez odpowiednie zwiększenie liczby mapowań i reduktory, aby zobaczyć, czy osiągnąć wyższą wydajność.  Należy pamiętać więcej mapowań ma dodatkowe obciążenie, o zbyt wiele mapowań może obniżyć wydajność.  

Planowanie procesora CPU i procesora CPU izolacji są domyślnie wyłączone, liczbę kontenerów YARN jest ograniczane przez pamięci.

## <a name="example-calculation"></a>Przykład obliczeń

Załóżmy, że masz obecnie klaster składa się z 8 węzłów D14 i chcesz uruchomić zadanie intensywne operacje We/Wy.  Poniżej przedstawiono obliczeń, które należy wykonać:

**Krok 1: Określanie liczba zadań uruchomionych** — w naszym przykładzie przyjęto założenie, że nasze zadania jest uruchomione tylko jedno.  

**Krok 2: Ustaw mapreduce.map.memory/mapreduce.reduce.memory** — w naszym przykładzie są uruchomione znacznym zadania we/wy i zdecydować, że 3 GB pamięci dla zadań mapy będą wystarczające.

    mapreduce.map.memory = 3GB
**Krok 3: Określanie YARN całkowitej pamięci**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Liczba kontenerów YARN obliczyć**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Ustawianie mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Ograniczenia

**ADLS ograniczania przepustowości**

Jako usługę wielodostępną ADLS Ustawia limity przepustowości poziomu konta.  Jeśli naciśniesz tych limitów powoduje rozpoczęcie Zobacz niepowodzeń zadań. Może to zostać zidentyfikowane na podstawie obserwowania błędy ograniczania przepustowości w dziennikach zadań.  Jeśli potrzebujesz większej przepustowości dla zadania, skontaktuj się z nami.   

Aby sprawdzić, jeśli użytkownik są pobierania ograniczane, musisz włączyć rejestrowania po stronie klienta debugowania. Oto, jak można to zrobić:

1. Umieść następującą właściwość w właściwości log4j Ambari > YARN > Config > Zaawansowane narzędzia log4j yarn: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Uruchom ponownie wszystkie węzły/usługi dla konfiguracji zaczęły obowiązywać.

3. Jeśli możesz są pobierania ograniczane, zobaczysz kod błędu HTTP 429 w pliku dziennika YARN. YARN plik dziennika znajduje się w /tmp/&lt;użytkownika&gt;/yarn.log

## <a name="examples-to-run"></a>Przykłady do uruchomienia

Aby zademonstrować, jak działa MapReduce na Azure Data Lake Store, poniżej znajdują się niektóre przykładowy kod, która została uruchomiona w klastrze z następującymi ustawieniami:

* węzeł 16 D14v2
* Uruchomiona 3,6 HDI klastra usługi Hadoop

Punkt początkowy poniżej przedstawiono niektóre przykładowe polecenia do uruchomienia MapReduce Teragen, Terasort i Teravalidate.  Można dostosować tych poleceń, w oparciu o Twoich zasobów.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
