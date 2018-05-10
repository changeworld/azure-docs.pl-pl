---
title: Uruchom zadanie Apache Spark z usługi Kubernetes Azure (AKS)
description: Użyj usługi Kubernetes Azure (AKS), aby uruchamiać zadanie Apache Spark
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: cb23c21fd22a35a3e8a5920a94aa5a89fe966cfa
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="running-apache-spark-jobs-on-aks"></a>Uruchomionych zadań Apache Spark na AKS

[Platforma Apache Spark] [ apache-spark] to szybki aparat do przetwarzania danych na dużą skalę. Począwszy od programu [wersji Spark 2.3.0][spark-latest-release], Apache Spark obsługuje natywna Integracja z klastrami Kubernetes. Usługa Kubernetes Azure (AKS) jest zarządzanym środowisku Kubernetes działające na platformie Azure. Ten dokument zawiera szczegóły dotyczące przygotowania i uruchomionych zadań Apache Spark w klastrze usługi Kubernetes Azure (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawową wiedzę na temat Kubernetes i [Apache Spark][spark-quickstart].
* [Centrum docker] [ docker-hub] konta, lub [rejestru kontenera Azure][acr-create].
* Azure CLI [zainstalowane] [ azure-cli] w systemie deweloperskim.
* [JDK 8] [ java-install] zainstalowanych w systemie.
* SBT ([narzędzia kompilacji Scala][sbt-install]) zainstalowanych w systemie.
* Narzędzia wiersza polecenia Git zainstalowanych w systemie.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Platforma Spark służy do przetwarzania danych na dużą skalę i wymaga się, że węzły Kubernetes mają rozmiar w celu spełnienia wymagań zasobów Spark. Firma Microsoft zaleca minimalny rozmiar `Standard_D3_v2` dla usługi Kubernetes Azure (AKS) węzły.

Jeśli potrzebujesz AKS klastra, który spełnia minimalne zalecenie, uruchom następujące polecenia.

Utwórz grupę zasobów klastra.

```azurecli
az group create --name mySparkCluster --location eastus
```

Tworzenie klastra AKS z węzłów, które mają rozmiar `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Połącz się z klastrem AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Jeśli używasz rejestru kontenera platformy Azure (ACR) do przechowywania obrazów kontenera, należy skonfigurować uwierzytelnianie między AKS i ACR. Zobacz [dokumentacji uwierzytelniania ACR] [ acr-aks] dla tych kroków.

## <a name="build-the-spark-source"></a>Tworzenie źródła Spark

Przed uruchomieniem Spark zadań w klastrze AKS, należy skompilować Spark kodu źródłowego i pakiet go w obrazie kontenera. Źródło Spark zawiera skryptów, które mogą służyć do ukończenia tego procesu.

Klonowanie repozytorium Spark projektu na platformie programistycznej.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Zmień do katalogu sklonowanego repozytorium i Zapisz ścieżki źródła Spark do zmiennej.

```bash
cd spark
sparkdir=$(pwd)
```

Jeśli masz wiele zainstalowanych wersji JDK, ustaw `JAVA_HOME` do użycia w wersji 8 dla bieżącej sesji.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Uruchom następujące polecenie, aby kompilacji kodu źródłowego z obsługą Kubernetes Spark.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Następujące polecenia Utwórz obraz kontenera Spark i wypchnąć go do rejestru obrazu kontenera. Zastąp `registry.example.com` o nazwie rejestru kontenera i `v1` znacznikiem wolisz korzystać. Jeśli przy użyciu Centrum Docker, ta wartość jest nazwa rejestru. Jeśli za pomocą rejestru kontenera platformy Azure (ACR), ta wartość jest nazwa ACR logowania serwera.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Wypchnij obrazu kontenera do rejestru obrazu kontenera.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Przygotowanie zadania Spark

Następnie przygotuj zadania Spark. Plik jar służy do przechowywania zadań Spark i podczas uruchamiania `spark-submit` polecenia. Jar mogą być udostępniane za pośrednictwem publicznego adresu URL lub wstępnie opakowane w obrazie kontenera. W tym przykładzie jar próbki jest tworzony, aby obliczyć wartość liczby Pi. Ten jar jest następnie przekazać do magazynu Azure. Jeśli masz istniejące jar, możesz podstawić

Utwórz katalog, w której chcesz utworzyć projekt dla zadania Spark.

```bash
mkdir myprojects
cd myprojects
```

Utwórz nowy projekt języka Scala z szablonu.

```bash
sbt new sbt/scala-seed.g8
```

Po wyświetleniu monitu wprowadź `SparkPi` dla nazwy projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Przejdź do katalogu nowo utworzonego projektu.

```bash
cd sparkpi
```

Uruchom następujące polecenia, aby dodać wtyczkę SBT, dzięki czemu pakowania projektu jako plik jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Uruchom następujące polecenia, aby skopiować przykładowy kod do nowo utworzonego projektu i dodać wszystkie wymagane zależności.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Do tworzenia pakietów projektu w jar, uruchom następujące polecenie.

```bash
sbt assembly
```

Po pomyślnym opakowaniu powinny zostać wyświetlone dane wyjściowe podobne do następującego.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Zadanie kopiowania do magazynu

Utwórz konto magazynu Azure i kontener, aby pomieścić plik jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Przekaż plik jar do konta magazynu Azure za pomocą następujących poleceń.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Zmienna `jarUrl` zawiera teraz publicznie dostępnej ścieżki do pliku jar.

## <a name="submit-a-spark-job"></a>Prześlij zadanie Spark

Uruchom serwer proxy kube w oddzielnej wiersza polecenia z następującym kodem.

```bash
kubectl proxy
```

Przejdź z powrotem do katalogu głównego repozytorium Spark.

```bash
cd $sparkdir
```

Przedstawia zadania za pomocą polecenia `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ta operacja uruchamia zadania Spark strumieni stanu zadania na sesję powłoki. Podczas uruchamiania zadania, można sprawdzić pod sterownik Spark i stanowiskami Moduł wykonujący przy użyciu kubectl Pobierz stanowiskami polecenie. Otwórz sesję terminala drugi do uruchamiania tych poleceń.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Gdy zadanie jest uruchomione, można także przejść interfejsu użytkownika platformy Spark. W sesji terminala drugie, użyj `kubectl port-forward` polecenia zapewnianie dostępu do interfejsu użytkownika platformy Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Aby uzyskać dostęp do interfejsu użytkownika Spark, otwórz adres `127.0.0.1:4040` w przeglądarce.

![Platforma Spark interfejsu użytkownika](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Pobierz wyniki zadania i dzienniki

Po zakończeniu zadania pod sterowników będzie w stanie "Completed". Pobierz nazwę pod przy użyciu następującego polecenia.

```bash
kubectl get pods --show-all
```

Dane wyjściowe:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Użyj `kubectl logs` polecenie, aby odczytać dzienniki pod sterownik spark. Zamień nazwę pod nazwą użytkownika pod sterownika.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

W tych dziennikach można wyświetlić wyniki zadania Spark, czyli wartość liczby Pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Jar pakietu z obrazem kontenera

W powyższym przykładzie pliku jar Spark został przekazany do magazynu Azure. Inną możliwością jest wykonanie pakietów plik jar w niestandardowej obrazy usługi Docker.

Aby to zrobić, należy znaleźć `dockerfile` dla obrazu Spark pod adresem `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` katalogu. Dodaj am `ADD` instrukcji dla zadania Spark `jar` między `WORKDIR` i `ENTRYPOINT` deklaracji.

Zaktualizuj ścieżkę jar lokalizację `SparkPi-assembly-0.1.0-SNAPSHOT.jar` pliku w systemie deweloperskim. Umożliwia także pliku jar niestandardowych.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Skompiluj i push obrazu o dołączone skrypty Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Podczas wykonywania zadania, zamiast wskazujący jar zdalnego adresu URL, `local://` schematu może być używany z ścieżkę do pliku jar w obrazie Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Z Spark [dokumentacji][spark-docs]: "harmonogramu Kubernetes jest obecnie eksperymentalne. W przyszłych wersjach mogą istnieć behawioralnej zmian dotyczących konfiguracji, kontener obrazów i punkty wejścia".

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z dokumentacją Spark więcej szczegółów.

> [!div class="nextstepaction"]
> [Platforma Spark dokumentacji][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
