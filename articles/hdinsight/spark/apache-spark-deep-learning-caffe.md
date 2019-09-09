---
title: Użyj Caffe na Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego
description: Użyj Caffe na Apache Spark na potrzeby rozproszonej uczenia głębokiego w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: 31911c6c2456ab8b4949bab6ef8e541b91fc8a2c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814199"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Użyj Caffe na Azure HDInsight Spark na potrzeby rozproszonej uczenia głębokiego


## <a name="introduction"></a>Wprowadzenie

Uczenie głębokie ma wpływ na wszystko z opieki zdrowotnej na transport do produkcji i nie tylko. Firmy mają na celu uczenie się w celu rozwiązania problemów twardych, takich jak [Klasyfikacja obrazu](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), [rozpoznawanie mowy](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), rozpoznawanie obiektów i tłumaczenie maszynowe. 

Istnieje [wiele popularnych struktur](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), w tym [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano itd. [Caffe](https://caffe.berkeleyvision.org/) to jedna z najbardziej sławęych, niesymbolicznych (autonomicznych) platform sieci neuronowych i szeroko wykorzystywana w wielu obszarach, w tym o wizji komputerowych. Ponadto [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) łączy Caffe z Apache Spark. w takim przypadku można łatwo korzystać z uczenia głębokiego w istniejącym klastrze usługi Hadoop. Możesz użyć głębokiej uczenia się z potokami ETL usługi Spark, zmniejszając złożoność systemu i opóźnić pełną naukę rozwiązań.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) to oferta Apache Hadoop w chmurze, która zapewnia zoptymalizowane klastry analityczne typu "open source" dla Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka i usługi ml. Usługa HDInsight jest objęta umową SLA na 99,9%. Każda z tych technologii danych Big Data i aplikacji niezależnych dostawców oprogramowania jest łatwo wdrażana jako klastry zarządzane z zabezpieczeniami i monitorowaniem dla przedsiębiorstw.

W tym artykule przedstawiono sposób instalowania [Caffe na platformie Spark](https://github.com/yahoo/CaffeOnSpark) dla klastra usługi HDInsight. W tym artykule jest również używany wbudowany Demonstracja MNIST ręcznie, w którym pokazano, jak używać rozproszonej uczenia głębokiego przy użyciu usługi HDInsight Spark na procesorach.

Istnieje cztery kroki do wykonania zadania:

1. Zainstaluj wymagane zależności na wszystkich węzłach
2. Kompiluj Caffe na Spark dla usługi HDInsight w węźle głównym
3. Dystrybuuj wymagane biblioteki do wszystkich węzłów procesu roboczego
4. Utwórz model Caffe i uruchom go w sposób rozproszony.

Ponieważ Usługa HDInsight jest rozwiązaniem PaaS, oferuje doskonałe funkcje platformy, dzięki czemu można łatwo wykonywać pewne zadania. Jedną z funkcji używanych w tym wpisie w blogu jest nazywana [Akcja skryptu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), za pomocą której można wykonać polecenia powłoki w celu dostosowania węzłów klastra (węzeł główny, węzeł procesu roboczego lub węzeł brzegowy).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Krok 1:  Zainstaluj wymagane zależności na wszystkich węzłach

Aby rozpocząć, musisz zainstalować zależności. Lokacja Caffe i [Witryna CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) oferują pewną użyteczną witrynę typu wiki na potrzeby instalowania zależności dla platformy Spark w trybie przędzy. Usługa HDInsight używa również platformy Spark w trybie PRZĘDZy. Należy jednak dodać kilka zależności dla platformy usługi HDInsight. W tym celu należy użyć akcji skryptu i uruchomić ją na wszystkich węzłach głównych i węzłach procesu roboczego. Ta akcja skryptu trwa około 20 minut, ponieważ te zależności również zależą od innych pakietów. Należy ją umieścić w niektórych lokalizacjach dostępnych dla klastra usługi HDInsight, takich jak lokalizacja serwisu GitHub lub domyślne konto magazynu obiektów BLOB.

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


Akcja skryptu obejmuje dwa etapy. Pierwszym krokiem jest zainstalowanie wszystkich wymaganych bibliotek. Te biblioteki obejmują biblioteki niezbędne do obu kompilacji Caffe (na przykład GFlags, glog) i uruchomione Caffe (takie jak numpy). używasz libatlas na potrzeby optymalizacji procesora CPU, ale zawsze możesz skorzystać z witryny typu wiki CaffeOnSpark na potrzeby instalowania innych bibliotek optymalizacji, takich jak MKL lub CUDA (dla procesora GPU).

Drugim krokiem jest pobranie, skompilowanie i zainstalowanie protobuf 2.5.0 dla Caffe w czasie wykonywania. Protobuf 2.5.0 [jest wymagana](https://github.com/yahoo/CaffeOnSpark/issues/87), ale ta wersja nie jest dostępna jako pakiet w Ubuntu 16, dlatego należy skompilować ją z kodu źródłowego. W Internecie znajduje się również kilka zasobów, na których można ją skompilować. Aby uzyskać więcej informacji, zobacz [tutaj](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Aby rozpocząć, można po prostu uruchomić tę akcję skryptu względem klastra do wszystkich węzłów procesu roboczego i węzłów głównych (dla usługi HDInsight 3,5). Można uruchomić akcje skryptu w istniejącym klastrze lub użyć akcji skryptu podczas tworzenia klastra. Aby uzyskać więcej informacji na temat akcji skryptu, zobacz dokumentację w [tym miejscu](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Akcje skryptu do instalacji zależności](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Krok 2: Kompiluj Caffe na Apache Spark dla usługi HDInsight w węźle głównym

Drugim krokiem jest skompilowanie Caffe w węzła głównego, a następnie dystrybuowanie skompilowanych bibliotek do wszystkich węzłów procesu roboczego. W tym kroku trzeba będzie [SSH do węzła głównego](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Następnie należy postępować zgodnie z [procesem kompilacji CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Poniżej znajduje się skrypt, którego można użyć do kompilowania CaffeOnSpark z kilkoma dodatkowymi krokami. 

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

Może być konieczne wykonanie więcej niż dokumentacji CaffeOnSpark. Zmiany są następujące:
- Zmień tylko na procesor CPU i użyj libatlas do tego celu.
- Umieść zestawy danych w magazynie obiektów BLOB, który jest lokalizacją udostępnioną dostępną dla wszystkich węzłów procesu roboczego w celu późniejszego użycia.
- Umieść skompilowane biblioteki Caffe w usłudze BLOB Storage, a następnie skopiuj te biblioteki do wszystkich węzłów za pomocą akcji skryptu, aby uniknąć dodatkowego czasu kompilacji.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Rozwiązywanie problemów z Wystąpił ANT Buildexception: zwrócono element exec: 2

Podczas pierwszej próby kompilacji CaffeOnSpark, czasami mówi

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Wyczyść repozytorium kodu przez "Ustaw czysty", a następnie uruchom polecenie "Utwórz kompilację", aby rozwiązać ten problem, o ile są poprawne zależności.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Rozwiązywanie problemów z Limit czasu połączenia repozytorium Maven

Czasami Maven nadaje błąd limitu czasu połączenia, podobny do następującego fragmentu kodu:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Musisz ponowić próbę za kilka minut.


### <a name="troubleshooting-test-failure-for-caffe"></a>Rozwiązywanie problemów z Niepowodzenie testu dla Caffe

Prawdopodobnie zobaczysz niepowodzenie testu podczas wykonywania końcowego sprawdzenia dla CaffeOnSpark. Jest to prawdopodobnie związane z kodowaniem UTF-8, ale nie ma wpływu na użycie Caffe

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Krok 3: Dystrybuuj wymagane biblioteki do wszystkich węzłów procesu roboczego

Następnym krokiem jest dystrybuowanie bibliotek (w zasadzie biblioteki w CaffeOnSpark/Caffe-Public/distribute/lib/i CaffeOnSpark/Caffe-punktów/Dystrybuuj/lib/) do wszystkich węzłów. W kroku 2 te biblioteki są umieszczane w magazynie obiektów BLOB, a w tym kroku są używane akcje skryptu do skopiowania do wszystkich węzłów głównych i węzłów procesu roboczego.

W tym celu uruchom akcję skryptu, jak pokazano w poniższym fragmencie kodu:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Upewnij się, że potrzebujesz punktu w odpowiedniej lokalizacji specyficznej dla klastra)

Ponieważ w kroku 2, należy umieścić go w magazynie obiektów BLOB, który jest dostępny dla wszystkich węzłów, w tym kroku wystarczy skopiować go do wszystkich węzłów.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Krok 4: Twórz model Caffe i uruchamiaj go w sposób rozproszony

Caffe jest instalowany po uruchomieniu powyższych kroków. Następnym krokiem jest napisanie modelu Caffe. 

Caffe korzysta z "architektury" i ", gdzie można utworzyć model, wystarczy zdefiniować plik konfiguracji i bez konieczności kodowania (w większości przypadków). Przyjrzyjmy się. 

Model, który jest pociągiem, to przykładowy model szkolenia MNIST ręcznie. MNIST ręcznie baza danych cyfr odręcznych zawiera zestaw szkoleniowy 60 000 przykładów oraz zestaw testów 10 000 przykładów. Jest to podzestaw większego zestawu dostępnego z NIST. Liczba cyfr została znormalizowana i wyśrodkowana w obrazie o stałym rozmiarze. CaffeOnSpark zawiera pewne skrypty do pobrania zestawu danych i przekonwertowania go w prawidłowy format.

CaffeOnSpark zawiera przykład topologii sieci dla szkolenia MNIST ręcznie. Jest to świetny projekt podziału architektury sieci (topologii sieci) i optymalizacji. W takim przypadku wymagane są dwa pliki: 

plik "Solver" ($ {CAFFE_ON_SPARK}/Data/lenet_memory_solver.prototxt) służy do nadzorowania optymalizacji i generowania aktualizacji parametrów. Na przykład określa, czy używany jest procesor CPU, czy procesor GPU, jaki jest czas, ile iteracji jest itp. Definiuje również, która topologia sieci neuron powinna być używana przez program (który jest potrzebny drugi plik). Aby uzyskać więcej informacji na temat dodatku Solver, zobacz [dokumentację Caffe](https://caffe.berkeleyvision.org/tutorial/solver.html).

W tym przykładzie, ponieważ korzystasz z procesora CPU zamiast GPU, należy zmienić ostatni wiersz na:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Caffe Config1](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

W razie konieczności można zmienić inne wiersze.

Drugi plik ($ {CAFFE_ON_SPARK}/Data/lenet_memory_train_test.prototxt) definiuje, jak wygląda sieć neuron oraz odpowiedni plik wejściowy i wyjściowy. należy również zaktualizować plik, aby odzwierciedlał lokalizację danych szkoleniowych. Zmień następującą część w lenet_memory_train_test. prototxt (należy wskazać właściwą lokalizację dla danego klastra):

- Zmień wartość "File:/Users/Mridul/bigml/demodl/mnist_train_lmdb" na "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- Zmień wartość "File:/Users/Mridul/bigml/demodl/mnist_test_lmdb/" na "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Caffe Config2](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Aby uzyskać więcej informacji na temat sposobu definiowania sieci, zapoznaj się z [dokumentacją Caffe w zestawie danych mnist ręcznie](https://caffe.berkeleyvision.org/gathered/examples/mnist.html)

Na potrzeby tego artykułu użyto tego przykładu MNIST ręcznie. Uruchom następujące polecenia z węzła głównego:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Poprzednie polecenie dystrybuuje wymagane pliki (lenet_memory_solver. prototxt i lenet_memory_train_test. prototxt) do każdego kontenera PRZĘDZy. Polecenie ustawia również odpowiednią ścieżkę każdego elementu wykonawczego sterownika/programu Spark do LD_LIBRARY_PATH. LD_LIBRARY_PATH jest zdefiniowany w poprzednim fragmencie kodu i wskazuje lokalizację, w której znajdują się biblioteki CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Monitorowanie i rozwiązywanie problemów

Ponieważ jest używany tryb klastra PRZĘDZy, w tym przypadku sterownik Spark zostanie zaplanowany do dowolnego kontenera (i dowolnego węzła procesu roboczego), który powinien być widoczny tylko w konsoli, w której znajduje się następujący element:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Jeśli chcesz wiedzieć, co się stało, zazwyczaj musisz uzyskać dziennik sterownika platformy Spark, który zawiera więcej informacji. W takim przypadku należy przejść do interfejsu użytkownika PRZĘDZy, aby znaleźć odpowiednie dzienniki PRZĘDZy. Interfejs użytkownika PRZĘDZy można uzyskać według tego adresu URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![INTERFEJS UŻYTKOWNIKA PRZĘDZY](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Zapoznaj się z liczbą zasobów dla tej konkretnej aplikacji. Możesz kliknąć link "Scheduler", a następnie zobaczyć, że dla tej aplikacji będzie uruchomionych dziewięć kontenerów. należy zażądać PRZĘDZy, aby zapewnić osiem wykonawców, a inny kontener dla procesu sterownika. 

![Harmonogram PRZĘDZy](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Jeśli wystąpią błędy, warto sprawdzić dzienniki sterowników lub dzienniki kontenerów. W przypadku dzienników sterowników można kliknąć pozycję Identyfikator aplikacji w interfejsie użytkownika PRZĘDZy, a następnie kliknąć przycisk "dzienniki". Dzienniki sterowników są zapisywane w stderr.

![INTERFEJS UŻYTKOWNIKA PRZĘDZY 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Na przykład może zostać wyświetlony następujący błąd z dzienników sterowników, wskazujący, że przydzieli zbyt wiele wykonawców.

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

Czasami problem może wystąpić w modułach wykonujących zamiast sterowników. W takim przypadku należy sprawdzić dzienniki kontenerów. Zawsze możesz pobrać dzienniki kontenerów, a następnie uzyskać kontener zakończony niepowodzeniem. Można na przykład spełnić ten błąd podczas uruchamiania Caffe.

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

W takim przypadku należy uzyskać identyfikator kontenera zakończony niepowodzeniem (w powyższym przypadku jest to container_1485916338528_0008_05_000005). Następnie należy uruchomić polecenie 

    yarn logs -containerId container_1485916338528_0008_03_000005

z węzła głównego. Po sprawdzeniu awarii kontenera jest on spowodowany przez użycie trybu GPU (w przypadku którego zamiast tego należy użyć trybu procesora) w lenet_memory_solver. prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Pobieranie wyników

Ponieważ przydzielasz 8 programów wykonujących, a topologia sieci jest prosta, wykonanie wyniku powinno trwać około 30 minut. W wierszu polecenia można zobaczyć, że należy umieścić model do wasb:///mnist.model i umieścić wyniki w folderze o nazwie wasb:///mnist_features_result.

Wyniki można uzyskać, uruchamiając

    hadoop fs -cat hdfs:///mnist_features_result/*

a wynik wygląda następująco:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID reprezentuje identyfikator w zestawie danych MNIST ręcznie, a etykieta jest numerem identyfikowanym przez model.


## <a name="conclusion"></a>Wniosek

W tej dokumentacji podjęto próbę instalacji CaffeOnSpark z uruchomionym prostym przykładem. HDInsight to w pełni zarządzana platforma obliczeniowa w chmurze, która jest najlepszym miejscem do obsługi obciążeń maszynowych i zaawansowanych analiz w dużych zestawach danych, a w przypadku rozproszonej uczenia głębokiego można użyć Caffe w usłudze HDInsight Spark, aby przeprowadzić uczenie głębokie widoku.


## <a name="seealso"></a>Zobacz też
* [Podsumowanie Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do analizowania temperatury kompilacji przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark z Machine Learning: Korzystanie z platformy Spark w usłudze HDInsight do przewidywania wyników inspekcji żywności](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Zarządzanie zasobami
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)

