---
title: Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego
description: Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: d0d68263485c5ab6e57a349317b1975862470cc2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721515"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Używanie platformy Caffe na platformie Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego


## <a name="introduction"></a>Wprowadzenie

Uczenie głębokie ma wpływ na wszystkie elementy z branży opieki zdrowotnej do transportu do produkcji i nie tylko. Firmy zaczynają korzystać z rozwiązania trudnych problemów, takie jak do uczenia głębokiego [Klasyfikacja obrazów](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [rozpoznawania mowy](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html)obiektu rozpoznawanie i tłumaczenia maszynowego. 

Istnieją [wiele popularnych platform](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), w tym [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, itp. [Caffe](https://caffe.berkeleyvision.org/) jest jednym z Najpopularniejsza poznana struktur — symboliczne sieci neuronowej (imperatywne) i powszechnie używane w wielu obszarów, takich jak przetwarzanie obrazów. Ponadto [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) łączy w sobie Caffe z platformą Apache Spark, w którym to przypadku głębokiego uczenia, które mogą być łatwo używane w istniejącym klastrze usługi Hadoop. Uczenie głębokie, wraz z potoki przetwarzania ETL platformy Spark, zmniejszenie złożoności systemu i opóźnień służy do kompletnego rozwiązania uczenia.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) jest chmurze Apache Hadoop oferty, która zapewnia zoptymalizowane klastry analityczne typu open source dla platformy Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka i usługi ML. HDInsight jest wspierana przez umowę SLA 99,9%. Każdy z tych technologii danych big data i aplikacji ISV jest łatwe do wdrożenia w formie zarządzanych klastrów z bezpieczeństwem i monitorowaniem dla przedsiębiorstw.

W tym artykule przedstawiono sposób instalowania [Caffe na platformie Spark](https://github.com/yahoo/CaffeOnSpark) klastra usługi HDInsight. W tym artykule używa także wbudowane pokaz mnist ręcznie ZAPISANYCH i pokazuje, jak używać rozproszonego uczenia głębokiego na procesorach przy użyciu platformy HDInsight Spark.

Istnieją cztery kroki do wykonania zadania:

1. Zainstaluj wymagane zależności na wszystkich węzłach
2. Tworzenie platformy Caffe na platformie Spark dla HDInsight w węźle głównym
3. Dystrybucja wymaganych bibliotek do wszystkich węzłów procesu roboczego
4. Utwórz Caffe model i uruchomić go w sposób rozproszony.

Ponieważ rozwiązanie PaaS, HDInsight oferuje funkcje doskonała platforma — dzięki czemu można łatwo wykonywać niektóre zadania. Jedna z funkcji używanych w tym wpisie w blogu o nazwie [akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), za pomocą którego można wykonać polecenia powłoki, aby dostosować węzłów klastra (węzła głównego, węzła procesu roboczego lub węzłem krawędzi).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Krok 1:  Zainstaluj wymagane zależności na wszystkich węzłach

Aby rozpocząć pracę, musisz zainstalować zależności. Witryna Caffe i [witryny CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferuje niektóre przydatne witryny typu wiki w celu zainstalowania zależności dla platformy Spark na tryb usługi YARN. HDInsight używa również platformy Spark w trybie usługi YARN. Jednakże należy dodać kilka więcej zależności dla platformy HDInsight. W tym celu należy użyć akcji skryptu i uruchom go na wszystkich węzłów głównych i węzłów procesu roboczego. Tę akcję skryptu zajmuje około 20 minut, jak te zależności, także zależeć od innych pakietów. Należy go umieścić w lokalizacji dostępnej z klastrem HDInsight, takie jak lokalizacja usługi GitHub lub domyślne konto magazynu obiektów BLOB.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


Istnieją dwa kroki w akcji skryptu. Pierwszym krokiem jest instalowanie wymaganych bibliotek. Te biblioteki zawierają wymagane biblioteki dla kompilowania Caffe (na przykład gflags, glog) i systemem Caffe (na przykład numpy). używasz libatlas optymalizacji procesora CPU, ale zawsze można wykonać CaffeOnSpark witryny typu wiki na temat instalowania innych bibliotek optymalizacji, takiego jak MKL lub CUDA (dla procesora GPU).

Drugim krokiem jest pobrać, skompilować i zainstalować protobuf 2.5.0 dla Caffe w czasie wykonywania. Formatu Protobuf 2.5.0 [jest wymagana](https://github.com/yahoo/CaffeOnSpark/issues/87), ale ta wersja nie jest dostępna jako pakiet w systemie Ubuntu 16, więc należy skompilować go z kodu źródłowego. Dostępne są także kilka zasobów w Internecie na temat sposobu go skompilować. Aby uzyskać więcej informacji, zobacz [tutaj](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Aby rozpocząć pracę, możesz po prostu uruchomić tę akcję skryptu względem klastra do wszystkich węzłów procesu roboczego i węzły główne (na HDInsight 3.5). Możesz uruchomić akcji skryptów w istniejącym klastrze lub użyj akcji skryptu, podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Akcje skryptu, aby zainstalować zależności](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Krok 2: Tworzenie platformy Caffe na platformie Apache Spark dla HDInsight w węźle głównym

Drugim krokiem jest tworzenie platformy Caffe na węzła głównego, a następnie dystrybucję skompilowanych bibliotek do wszystkich węzłów procesu roboczego. W tym kroku musisz [ssh do Twojej węzeł główny](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Po tym, należy wykonać [CaffeOnSpark kompilacji mają przetwarzać](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Poniżej znajduje się skrypt, który służy do tworzenia CaffeOnSpark kilka dodatkowych czynności. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Może być konieczne zrobić więcej niż dokumentacji CaffeOnSpark mówi. Dostępne są następujące zmiany:
- Tylko zmiany do procesora CPU i użyj libatlas dla tego określonego celu.
- Umieść zestawów danych do magazynu obiektów BLOB, co prowadzi do lokalizacji udostępnionej, który jest dostępny dla wszystkich węzłów procesu roboczego w celu późniejszego użycia.
- Umieść skompilowanych bibliotek Caffe do magazynu obiektów BLOB, a później skopiuj tych bibliotek do wszystkich węzłów za pomocą akcji skryptu, aby uniknąć dodatkowych kompilacji czasu.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Rozwiązywanie problemów: Wystąpiło BuildException Ant: exec zwrócone: 2

Po pierwsze Trwa próba skompilowania CaffeOnSpark, czasami wyświetlany jest tekst

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Wyczyść repozytorium kodu przez "Przechowuj czyste", a następnie wykonywania "marka kompilacji" Aby rozwiązać ten problem, tak długo, jak długo mają prawidłowe zależności.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Rozwiązywanie problemów: Limit czasu połączenia repozytorium maven

Czasami maven zapewnia Błąd limitu czasu połączenia, podobny do następującego fragmentu kodu:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Trzeba powtórzyć po kilku minutach.


### <a name="troubleshooting-test-failure-for-caffe"></a>Rozwiązywanie problemów: Niepowodzenie testu Caffe

Podczas ustalania końcowego Wyszukaj CaffeOnSpark prawdopodobnie Zobacz niepowodzenia testu. To prawdopodobnie związane z kodowaniem UTF-8, ale nie powinny mieć wpływu na używanie platformy Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Krok 3: Dystrybucja wymaganych bibliotek do wszystkich węzłów procesu roboczego

Następnym krokiem jest dystrybucja bibliotek (zasadniczo bibliotek w CaffeOnSpark/caffe-public/dystrybucji/lib/i CaffeOnSpark/caffe dystry/dystrybucji/lib /) do wszystkich węzłów. W kroku 2 umieszczenie tych bibliotek w usłudze BLOB storage, a w tym kroku używasz akcji skryptu, aby skopiować go do wszystkich węzłów głównych i węzłów procesu roboczego.

Aby to zrobić, Uruchom akcję skryptu, jak pokazano w poniższym fragmencie kodu:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Upewnij się, że musisz punkt do odpowiedniej lokalizacji określonych klastra)

Ponieważ w kroku 2, należy umieść je w magazynie obiektów BLOB, który jest dostępny dla wszystkich węzłów, w tym kroku możesz po prostu skopiuj go do wszystkich węzłów.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Krok 4: Tworzenie modelu Caffe i uruchomienia jej w sposób Rozproszony

Caffe jest instalowany po uruchomieniu powyższych kroków. Następnym krokiem jest do zapisywania modelu Caffe. 

Caffe jest przy użyciu "ekspresyjny architektury", gdzie do redagowania modelu, po prostu musisz zdefiniować pliku konfiguracji i bez kodowania w ogóle (w większości przypadków). Dlatego Przyjrzyjmy się dostępne. 

Model, który możesz później jest przykładowy model do trenowania mnist ręcznie ZAPISANYCH. Bazy danych mnist ręcznie ZAPISANYCH cyfr pisma odręcznego ma zestaw szkolenia 60 000 przykłady i zbiór przykłady 10 000. Jest podzbiorem większego zbioru dostępnym NIST. Liczba cyfr zostały znormalizowany rozmiar i wyśrodkowany w obrazie stałym rozmiarze. CaffeOnSpark ma kilka skryptów, aby pobrać zestaw danych i je przekształcić w odpowiednim formacie.

CaffeOnSpark zawiera przykładowe topologie sieci do trenowania mnist ręcznie ZAPISANYCH. Ma ona nieuprzywilejowany projektowania podziału architektura sieci (topologia sieci) i optymalizacji. W tym przypadku istnieją dwa pliki wymagane: 

Plik "Solver" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) jest używany dla nadzorowanie optymalizacji i generowania aktualizacje parametru. Na przykład, określa ona, czy procesor CPU lub GPU jest używany, co to jest pęd, jak dużo iteracji są, itp. Definiuje również wdrożonej topologii sieci neuronu powinna być używana (czyli drugiego pliku, które są potrzebne). Aby uzyskać więcej informacji na temat Solver zobacz [dokumentacji Caffe](https://caffe.berkeleyvision.org/tutorial/solver.html).

W tym przykładzie ponieważ używasz procesora CPU, a nie procesora GPU, należy zmienić ostatni wiersz, aby:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe konfiguracji](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Można zmienić innych wierszy, zgodnie z potrzebami.

Drugi plik (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definiuje, jak sieci neuronu wygląda i odpowiednie dane wejściowe i pliku wyjściowego. należy również zaktualizować pliku, aby odzwierciedlić lokalizacja danych szkoleniowych. Zmień następujące części w lenet_memory_train_test.prototxt (należy wskazać odpowiedniej lokalizacji specyficzne dla klastra):

- Zmień "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" na "wasb: / / / projektów/machine_learning/image_dataset/mnist_train_lmdb"
- Zmień "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" na "wasb: / / / projektów/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe konfiguracji](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Aby uzyskać więcej informacji na temat sposobu definiowania sieci Sprawdź [dokumentacji platformy Caffe na zestawie danych mnist ręcznie ZAPISANYCH](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Na potrzeby tego artykułu możesz użyć w tym przykładzie mnist ręcznie ZAPISANYCH. Uruchom następujące polecenia z węzłem głównym:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Poprzednie polecenie dystrybuuje wymaganych plików (lenet_memory_solver.prototxt i lenet_memory_train_test.prototxt) do każdego kontenera YARN. Polecenie ustawia również istotne ŚCIEŻKĘ każdego sterownik Spark/wykonawca LD_LIBRARY_PATH. LD_LIBRARY_PATH jest zdefiniowany w poprzednim fragmencie kodu i punktów do lokalizacji, która ma CaffeOnSpark bibliotek. 

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

Ponieważ jest używany tryb klastra YARN, w którym to przypadku sterownik Spark nastąpi po dojściu do dowolnego kontenera (i węzłem procesu roboczego dowolnego) tylko wyświetlony w konsoli, podobny do podawania:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Jeśli chcesz wiedzieć, co się stało, należy zwykle pobieranie aparatu Spark dziennika sterownika, który zawiera więcej informacji. W takim przypadku musisz przejść w interfejsie użytkownika YARN, aby znaleźć odpowiednie dzienniki usługi YARN. Można uzyskać przez ten adres URL Interfejsie użytkownika YARN: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![INTERFEJSIE UŻYTKOWNIKA YARN](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Może potrwać wygląd na ile zasoby są przydzielane dla konkretnej aplikacji. Możesz kliknąć link "Harmonogram", a następnie pojawi się, że dla tej aplikacji są dziewięć kontenery działające. możesz zadawać pytania usługi YARN w celu zapewnienia osiem executors i innego kontenera jest sterownik procesu. 

![Harmonogram usługi YARN](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Można sprawdzić dzienniki sterowników lub dzienniki kontenerów w przypadku awarii. Dzienniki sterowników kliknij przycisk z Identyfikatorem aplikacji w interfejsie użytkownika YARN następnie kliknij przycisk "Dzienniki". Dzienniki sterowników są zapisywane do strumienia wyjściowego stderr.

![INTERFEJSIE UŻYTKOWNIKA YARN 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Na przykład można napotkać niektórych błędów poniżej z dzienników sterownik wskazujący, że przydzielenie zbyt wiele executors.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

Czasami ten problem może się zdarzyć w executors zamiast sterowników. W takim przypadku należy zapoznać się z dziennikami kontenera. Można zawsze Pobierz dzienniki kontenerów, a następnie pobrać kontenera nie powiodło się. Na przykład ten błąd może spełniać podczas uruchamiania Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

W takim przypadku musisz pobrać identyfikator kontenera nie powiodło się (w tym przypadku powyżej jest container_1485916338528_0008_05_000005). Następnie należy uruchomić 

    yarn logs -containerId container_1485916338528_0008_03_000005

z węzłem głównym. Po sprawdzeniu błąd kontenera, jest przyczyną korzystania z trybu procesora GPU (gdzie należy używać trybu procesora CPU zamiast) w lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Pobieranie wyników

Ponieważ są przydzielanie 8 executors i topologii sieci jest prosta, jej tylko zająć około 30 minut, w celu uruchomienia wyniku. W wierszu polecenia znajdują się idealny model wasb:///mnist.model i umieścić wyniki w folderze o nazwie wasb: / / / mnist_features_result.

Wyniki można uzyskać, uruchamiając

    hadoop fs -cat hdfs:///mnist_features_result/*

i wynik wygląda następująco:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID reprezentuje identyfikator w zestawie danych mnist ręcznie ZAPISANYCH i etykieta to numer, który identyfikuje modelu.


## <a name="conclusion"></a>Podsumowanie

W tej dokumentacji próbowano zainstalować CaffeOnSpark z uruchomionymi prosty przykład. HDInsight to platforma rozproszonych obliczeń pełną zarządzana usługa w chmurze, a to najlepsze miejsce do uruchamiania usługi machine learning i zaawansowanych obciążeń analizy dużych zestawów danych i rozproszone uczenia głębokiego, Caffe można użyć na platformie HDInsight Spark przeprowadzić uczenia głębokiego zadania.


## <a name="seealso"></a>Zobacz też
* [Omówienie: Platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Apache Spark w usłudze Machine Learning: Korzystanie z platformy Spark w HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)

