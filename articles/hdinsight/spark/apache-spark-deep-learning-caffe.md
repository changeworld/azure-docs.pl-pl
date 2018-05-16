---
title: Użyj Caffe Azure HDInsight Spark dla rozproszonych głębokie learning | Dokumentacja firmy Microsoft
description: Użyj Caffe Azure HDInsight Spark dla rozproszonych learning bezpośrednich
services: hdinsight
documentationcenter: ''
author: xiaoyongzhu
manager: asadk
editor: cgronlun
tags: azure-portal
ms.assetid: 71dcd1ad-4cad-47ad-8a9d-dcb7fa3c2ff9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: xiaoyzhu
ms.openlocfilehash: 95fd9efdcd7f93d8011808af943f52d4db13de8b
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Użyj Caffe Azure HDInsight Spark dla rozproszonych learning bezpośrednich


## <a name="introduction"></a>Wprowadzenie

Głębokie learning jest wpływające na od opieki zdrowotnej do transportu do wytwarzania i inne. Firm są Włączanie celu dokładnego szkoleniowe dotyczące rozwiązywania problemów twardych, takie jak [obrazu klasyfikacji](http://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [rozpoznawania mowy](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html)obiekt rozpoznawania i komputera tłumaczenia. 

Brak [wielu popularnych środowisk](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), takie jak [kognitywnych zestaw narzędzi firmy Microsoft](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), MXNet, Theano, itp. Caffe jest jednym z Najpopularniejsza struktur-symboliczne sieci neuronowej (nadrzędnych), a powszechnie używany w wielu obszarach, w tym komputerze wizji. Ponadto [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) łączy Caffe z platformy Apache Spark w takim przypadku głębokość uczenia, które mogą być łatwo używane istniejącego klastra usługi Hadoop. Głębokie learning potoki Spark ETL, zmniejszenie złożoności systemu i opóźnienia służy do uczenia kompletnego rozwiązania.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) chmury Hadoop oferuje zapewnia zoptymalizowane klastrów analityczne open source dla platformy Spark, Hive, Hadoop, HBase, Storm, Kafka i R Server. HDInsight nie jest obsługiwana przez SLA 99,9%. Każdy z tych technologii danych big data i aplikacji niezależnego dostawcy oprogramowania jest łatwo można wdrożyć jako zarządzane klastry z zabezpieczeniami i monitorowania dla przedsiębiorstw.

W tym artykule przedstawiono sposób instalowania [Caffe na Spark](https://github.com/yahoo/CaffeOnSpark) dla klastra usługi HDInsight. W tym artykule używa również wbudowane pokaz MNIST do pokazują, jak używać rozproszonego przy użyciu HDInsight Spark na procesorach głębokie Learning.

Istnieją cztery kroki do wykonania zadania:

1. Instalowanie wymaganych zależności na wszystkich węzłach
2. Tworzenie Caffe na Spark dla usługi HDInsight w węźle głównym
3. Dystrybuuj wymaganych bibliotek do wszystkich węzłów procesu roboczego
4. Utwórz Caffe model i uruchomić go w sposób rozproszony.

Ponieważ HDInsight to rozwiązanie typu PaaS, oferuje funkcje platformy dużą — tak łatwe do wykonania niektórych zadań. Jedną z funkcji używane w tym wpisie w blogu jest wywoływana [akcji skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), z której można wykonać polecenia powłoki, aby dostosować węzły klastra (węzła głównego węzła procesu roboczego i węzłem krawędzi).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Krok 1: Instalowanie wymaganych zależności na wszystkich węzłach

Aby rozpocząć pracę, musisz zainstalować zależności. Witryna Caffe i [lokacji CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferuje niektóre przydatne wiki do instalowania zależności platformy Spark w trybie YARN. HDInsight używa również Spark w trybie YARN. Jednak należy dodać kilka zależności więcej HDInsight platformy. Aby to zrobić, należy użyć akcji skryptu i uruchom go na wszystkich węzłach głównych i węzłów procesu roboczego. Tę akcję skryptu trwa około 20 minut, jak te zależności również są zależne od innych pakietów. Należy umieścić go w określonej lokalizacji dostępnej z klastrem usługi HDInsight, takie jak lokalizacja GitHub lub domyślne konto magazynu obiektów BLOB.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
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


Istnieją dwa kroki w akcji skryptu. Pierwszym krokiem jest instalowanie wymaganych bibliotek. Te biblioteki zawierają wymagane biblioteki dla kompilacji Caffe (na przykład gflags, glog) i systemem Caffe (na przykład numpy). używasz libatlas optymalizacji Procesora, ale CaffeOnSpark wiki zawsze można wykonać na temat instalowania innych bibliotek optymalizacji, takie jak MKL lub CUDA (dla procesora GPU).

Drugim krokiem jest pobrać, kompilowania i zainstaluj protobuf 2.5.0 dla Caffe w czasie wykonywania. Protobuf 2.5.0 [wymagana jest](https://github.com/yahoo/CaffeOnSpark/issues/87), ale ta wersja nie jest dostępna jako pakiet na Ubuntu 16, dlatego należy go skompilować z kodu źródłowego. Istnieją również kilka zasobów w Internecie dotyczące go skompilować. Aby uzyskać więcej informacji, zobacz [tutaj](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Aby rozpocząć pracę, możesz po prostu uruchamiać tę akcję skryptu na klastra do wszystkich węzłów procesu roboczego i węzłów głównych (dla usługi HDInsight 3.5). Możesz uruchomić akcji skryptu istniejącego klastra lub użyć akcji skryptu podczas tworzenia klastra. Aby uzyskać więcej informacji dotyczących akcji skryptu, zobacz dokumentację [tutaj](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions).

![Akcje skryptu, aby zainstalować zależności](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-spark-for-hdinsight-on-the-head-node"></a>Krok 2: Tworzenie Caffe Spark dla usługi HDInsight w węźle głównym

Drugim krokiem jest tworzenie Caffe na headnode, a następnie dystrybucję skompilowanej biblioteki do wszystkich węzłów procesu roboczego. W tym kroku musisz [ssh do Twojej headnode](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Następnie należy wykonać [proces kompilacji CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Poniżej przedstawiono skrypt, który służy do tworzenia CaffeOnSpark kilka dodatkowych czynności. 

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
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
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

Konieczne może być więcej niż dokumentacji CaffeOnSpark mówi czy. Dostępne są następujące zmiany:
- Zmienianie tylko do procesora CPU i użyj libatlas w tym konkretnym celu.
- Umieść zestawy danych do magazynu obiektów BLOB, co prowadzi do lokalizacji udostępnionej, który jest dostępny dla wszystkich węzłów procesu roboczego do późniejszego użycia.
- Umieść skompilowanej biblioteki Caffe do magazynu obiektów BLOB, a później skopiować te biblioteki do wszystkich węzłów za pomocą akcji skryptu, aby uniknąć czas kompilacji dodatkowe.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Rozwiązywanie problemów: Wystąpił BuildException Ant: exec zwrócił: 2

Gdy najpierw w trakcie kompilacji CaffeOnSpark, czasami wyświetlany jest tekst

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Wyczyść repozytorium kodu przez "Przechowuj czystą", a następnie uruchom "Sprawdź kompilacji" Aby rozwiązać ten problem, jak długo mają prawidłowe zależności.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Rozwiązywanie problemów: Maven repozytorium limit czasu połączenia

Czasami maven zawiera błąd limitu czasu połączenia, podobny do następującego fragmentu kodu:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Trzeba powtórzyć po kilku minutach.


### <a name="troubleshooting-test-failure-for-caffe"></a>Rozwiązywanie problemów: Błąd testowego dla Caffe

W trakcie końcowego sprawdzanie CaffeOnSpark prawdopodobnie wyświetlony błąd testu. To prawdopodobnie jest powiązany z kodowanie UTF-8, ale nie powinny mieć wpływ użycie Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Krok 3: Dystrybucji wymaganych bibliotek do wszystkich węzłów procesu roboczego

Następnym krokiem jest dystrybucji bibliotek (zasadniczo bibliotek w CaffeOnSpark/caffe publicznego/dystrybucji/lib/i CaffeOnSpark/caffe dystry/dystrybucji/lib /) do wszystkich węzłów. W kroku 2 umieszcza te biblioteki magazynu obiektów BLOB, a w tym kroku używasz akcji skryptu, aby skopiować go do wszystkich węzłów głównych i węzłów procesu roboczego.

Aby to zrobić, Uruchom akcję skryptu, jak pokazano w poniższy fragment kodu:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Upewnij się, że należy punkt do odpowiedniej lokalizacji określonej do klastra)

Ponieważ w kroku 2, został on umieszczony w magazynie obiektów BLOB, który jest dostępny dla wszystkich węzłów, w tym kroku możesz po prostu skopiuj go do wszystkich węzłów.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Krok 4: Utworzenie modelu Caffe i uruchom go w sposób Rozproszony

Caffe jest instalowana po wykonaniu powyższych kroków. Następnym krokiem jest zapisany Caffe model. 

Caffe przy użyciu "obszerne architektury", gdy do tworzenia modelu, wystarczy określić plik konfiguracji i bez kodowania w ogóle (w większości przypadków). Dlatego Spójrzmy istnieje. 

Model, który możesz później jest przykładowy model MNIST szkolenia. Baza danych MNIST odręcznie cyfr ma zestaw szkolenia 60 000 przykłady i zbiór przykłady 10 000. Jest ona podzbiorem większy zestaw dostępnej w sklepie NIST. Cyfry zostały znormalizowany rozmiar i wyśrodkowany w obrazie stałym rozmiarze. CaffeOnSpark ma niektóre skrypty w celu pobrania zestawu danych i przekształcić ją w prawidłowym formacie.

CaffeOnSpark zawiera przykładowe topologie sieci MNIST szkolenia. Ma ona nieuprzywilejowany projektowania podziału architektura sieci (topologia sieci) i optymalizacji. W takim przypadku istnieją dwa pliki wymagane: 

Plik "Solver" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) służy do nadzorowaniu optymalizacji i generowania parametru aktualizacji. Na przykład, określa czy Procesora GPU są używane lub co to jest tempa są jak dużo iteracji, itp. Definiuje także które neuronu topologii sieci należy używana (czyli drugiego pliku, który należy). Aby uzyskać więcej informacji na temat Solver, zobacz [dokumentacji Caffe](http://caffe.berkeleyvision.org/tutorial/solver.html).

W tym przykładzie ponieważ używasz Procesora niż procesora GPU, należy zmienić ostatni wiersz, aby:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe konfiguracji](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

W razie potrzeby można zmienić innych wierszy.

Drugi plik (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definiuje sposób sieci neuronu wygląda i odpowiednie dane wejściowe i pliku wyjściowego. należy również zaktualizować plik, aby odzwierciedlić lokalizacji danych szkoleniowych. Zmień poniższe części w lenet_memory_train_test.prototxt (należy wskazać lokalizację prawo właściwe dla klastra):

- Zmień "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" na "wasb: / / / projektów/machine_learning/image_dataset/mnist_train_lmdb"
- Zmień "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" na "wasb: / / / projektów/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe konfiguracji](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Aby uzyskać więcej informacji na temat sposobu definiowania sieci Sprawdź [Caffe dokumentacja MNIST zestawu danych](http://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Na potrzeby tego artykułu należy użyć tego przykładu MNIST. Uruchom następujące polecenia z węzłem głównym:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Poprzednie polecenie dystrybuuje wymaganych plików (lenet_memory_solver.prototxt i lenet_memory_train_test.prototxt) do każdego kontenera YARN. Polecenie ustawia również odpowiedniego ŚCIEŻKĘ każdego Spark sterownik/Moduł wykonujący do LD_LIBRARY_PATH. LD_LIBRARY_PATH jest zdefiniowany w poprzedniej fragment kodu i punktów do lokalizacji, która ma CaffeOnSpark biblioteki. 

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

Ponieważ używasz tryb klastra YARN w takim przypadku sterownika Spark zostanie zaplanowane do dowolnego kontenera (i węzła procesu roboczego dowolnego) powinien widoczne są tylko w konsoli Generowanie wyglądać mniej więcej tak:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Jeśli chcesz dowiedzieć się, co się stało, zwykle koniecznych do uzyskania Spark dziennika sterownika, który zawiera więcej informacji. W takim przypadku musisz przejść do interfejsu użytkownika YARN, aby znaleźć odpowiednie dzienniki YARN. Można uzyskać interfejsu użytkownika YARN przez ten adres URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![YARN INTERFEJSU UŻYTKOWNIKA](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Użytkownik może Spójrz na ile zasoby są przydzielane dla konkretnej aplikacji. Można kliknąć łącze "Harmonogramu" i zostanie wyświetlona dla tej aplikacji, czy dziewięć kontenery uruchomiona. Poproś YARN zapewnienie osiem modułów, a innego kontenera jest procesu sterownika. 

![YARN harmonogramu](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Warto Sprawdź dzienniki sterownika lub dzienniki kontenera, jeśli występują błędy. W przypadku dzienników sterownika kliknij identyfikator aplikacji w interfejsie użytkownika YARN następnie kliknij przycisk "Dzienniki". Sterownik dzienniki są zapisywane do strumienia wyjściowego stderr.

![INTERFEJS UŻYTKOWNIKA YARN 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Na przykład można napotkać niektóre błąd poniżej z dzienników sterownik wskazujący, że przydzielenie zbyt wiele modułów.

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

Czasami ten problem może się zdarzyć w modułów zamiast sterowniki. W takim przypadku należy sprawdzić w dziennikach kontenera. Można zawsze uzyskać dzienniki kontenera, a następnie Pobierz kontener nie powiodło się. Na przykład ten błąd może spełniać podczas uruchamiania Caffe.

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

W takim przypadku należy uzyskać identyfikator kontenera nie powiodło się (w tym przypadku powyżej jest container_1485916338528_0008_05_000005). Następnie należy uruchomić 

    yarn logs -containerId container_1485916338528_0008_03_000005

z headnode. Po sprawdzeniu kontenera awarii, jest on spowodował przy użyciu trybu procesora GPU (gdzie należy używać trybu Procesora zamiast) w lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Uzyskiwanie wyników

Ponieważ jest alokowany 8 modułów i topologii sieci jest proste, go tylko zająć około 30 minut, w celu uruchomienia wyniku. W wierszu polecenia widoczne put modelu do wasb:///mnist.model i umieścić wyniki do folderu o nazwie wasb: / / / mnist_features_result.

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

SampleID reprezentuje identyfikator w zestawie danych MNIST, a etykieta jest liczba, która identyfikuje modelu.


## <a name="conclusion"></a>Podsumowanie

W tej dokumentacji próbowano zainstalować CaffeOnSpark z systemem prosty przykład. HDInsight to platforma obliczeń rozproszonej pełne zarządzanej chmury i jest najlepszym miejscem do uruchamiania uczenia maszynowego i obciążeń zaawansowana analityka w dużych zestawów danych i rozproszonych learning głębokie, Caffe można użyć na Spark w usłudze HDInsight do wykonywania zadań learning głębokie.


## <a name="seealso"></a>Zobacz też
* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do analizy temperatury w budynku z użyciem danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Platforma Spark i usługa Machine Learning: korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników kontroli żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)

