---
title: Uruchom zadanie platformy Apache Spark za pomocą usługi Azure Kubernetes Service (AKS)
description: Użyj usługi Azure Kubernetes Service (AKS), aby uruchamiać zadanie platformy Apache Spark
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104956"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Uruchamia wszystkie zadania platformy Apache Spark w usłudze AKS

[Platforma Apache Spark] [ apache-spark] to szybki aparat do przetwarzania danych na dużą skalę. Począwszy od programu [wersji platformy Spark 2.3.0][spark-latest-release], Apache Spark obsługuje natywnej integracji z klastrów Kubernetes. Usługa Azure Kubernetes Service (AKS) jest zarządzanym środowisku Kubernetes działających na platformie Azure. W tym dokumencie przedstawiono przygotowywania i uruchamiania zadań platformy Apache Spark w klastrze usługi Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebne są następujące elementy.

* Podstawową wiedzę na temat usługi Kubernetes i [platformy Apache Spark][spark-quickstart].
* [Usługi docker Hub] [ docker-hub] konta lub [usługi Azure Container Registry][acr-create].
* Interfejs wiersza polecenia Azure [zainstalowane] [ azure-cli] w systemie deweloperskim.
* [Zestaw JDK 8] [ java-install] zainstalowanych w systemie.
* SBT ([narzędzia kompilacji Scala][sbt-install]) zainstalowane w systemie.
* Narzędzia wiersza polecenia Git zainstalowana w systemie.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Spark jest używana do przetwarzania danych na dużą skalę i wymaga, że węzłach Kubernetes mają rozmiar wymaganiami zasobów platformy Spark. Zalecamy minimalny rozmiar `Standard_D3_v2` dla węzłów usługi Azure Kubernetes Service (AKS).

Jeśli potrzebujesz klastra AKS, który spełnia minimalne zalecenie, uruchom następujące polecenia.

Utwórz grupę zasobów klastra.

```azurecli
az group create --name mySparkCluster --location eastus
```

Tworzenie klastra AKS z węzłów, które są o rozmiarze `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Łączenie z klastrem usługi AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Jeśli używasz usługi Azure Container Registry (ACR) do przechowywania obrazów kontenerów, należy skonfigurować uwierzytelnianie między AKS i rejestru Azure container Registry. Zobacz [dokumentacji uwierzytelniania ACR] [ acr-aks] tych kroków.

## <a name="build-the-spark-source"></a>Tworzenie źródłowego platformy Spark

Przed uruchomieniem zadania Spark, w klastrze AKS, musisz utworzysz Spark kodu źródłowego i spakujesz ją w postaci obrazu kontenera. Źródło Spark zawiera skrypty, które mogą służyć do ukończenia tego procesu.

Sklonuj repozytorium project Spark na systemie deweloperskim.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Przejdź do katalogu sklonowanego repozytorium, a następnie zapisz ścieżki źródła platformy Spark do zmiennej.

```bash
cd spark
sparkdir=$(pwd)
```

W przypadku zainstalowania wielu wersji zestawu JDK ustaw `JAVA_HOME` używana wersja 8 dla bieżącej sesji.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Uruchom następujące polecenie, aby tworzyć Spark kodu źródłowego za pomocą Obsługa klastra Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Następujące polecenia tworzą Spark obraz kontenera i Wypchnij go do rejestru obraz kontenera. Zastąp `registry.example.com` nazwą rejestru kontenerów i `v1` ze znacznikiem wolą używać. Jeśli używasz usługi Docker Hub, ta wartość jest nazwa rejestru. Jeśli używasz usługi Azure Container Registry (ACR), ta wartość jest nazwę serwera logowania usługi ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Wypchnij obraz kontenera do rejestru obraz kontenera.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Przygotowywanie zadania Spark

Następnie przygotuj zadanie Spark. Plik jar jest używany do przechowywania zadanie platformy Apache Spark i podczas uruchamiania `spark-submit` polecenia. Plik jar mogą być dostępne za pośrednictwem publicznego adresu URL lub wstępnie spakowane w postaci obrazu kontenera. W tym przykładzie zostanie utworzona jar przykładowe Oblicz wartość liczby Pi. Ten plik jar jest następnie przekazywany do usługi Azure storage. Jeśli masz istniejący plik jar, możesz ją zastąpić

Utwórz katalog, w którym chcesz utworzyć projekt dla zadania Spark.

```bash
mkdir myprojects
cd myprojects
```

Utwórz nowy projekt Scala z szablonu.

```bash
sbt new sbt/scala-seed.g8
```

Po wyświetleniu monitu wprowadź `SparkPi` nazwę projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Przejdź do katalogu nowo utworzonego projektu.

```bash
cd sparkpi
```

Uruchom następujące polecenia, aby dodać wtyczki SBT, dzięki czemu pakowania projektu do postaci pliku jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Uruchom następujące polecenia, aby skopiować przykładowy kod do nowo utworzonego projektu i dodać wszystkie niezbędne zależności.

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

Aby spakować projektu do pliku jar, uruchom następujące polecenie.

```bash
sbt assembly
```

Po pomyślnej funkcję tworzenia pakietów, powinny być widoczne dane wyjściowe podobne do następujących.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Zadanie kopiowania do magazynu

Tworzenie konta magazynu platformy Azure i kontener do przechowywania pliku jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Przekaż plik jar do konta usługi Azure storage za pomocą następujących poleceń.

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

Zmienna `jarUrl` zawiera obecnie publicznie dostępna ścieżka do pliku jar.

## <a name="submit-a-spark-job"></a>Prześlij zadanie platformy Spark

Uruchom serwer proxy klastra kubernetes w usłudze w osobnym wiersza polecenia, używając następującego kodu.

```bash
kubectl proxy
```

Przejdź z powrotem do głównego repozytorium platformy Spark.

```bash
cd $sparkdir
```

Przesyłanie zadania za pomocą polecenia `spark-submit`.

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

Ta operacja uruchamia zadania Spark strumieni stan zadania do sesji środowiska powłoki. Gdy zadanie jest uruchomione, widać zasobnika sterownik Spark i zasobników polecenie get zasobników wykonawca przy użyciu narzędzia kubectl. Otwórz sesję terminala drugi do uruchamiania tych poleceń.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Podczas uruchamiania zadania, można również uzyskać dostęp do interfejsu użytkownika platformy Spark. W terminalu drugi sesji, użyj `kubectl port-forward` polecenia zapewniają dostęp do interfejsu użytkownika platformy Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Aby uzyskać dostęp do interfejsu użytkownika platformy Spark, otwórz adres `127.0.0.1:4040` w przeglądarce.

![Platforma Spark interfejsu użytkownika](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Pobierz wyniki zadania i dzienniki

Po zakończeniu zadania pod sterowników będzie w stanie "Completed". Pobierz nazwę zasobnik za pomocą następującego polecenia.

```bash
kubectl get pods --show-all
```

Dane wyjściowe:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Użyj `kubectl logs` polecenie, aby pobrać dzienniki z zasobników sterownik spark. Zastąp nazwę pod nazwą tym zasobniku sterownika.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

W tych dzienników możesz sprawdzić wynik zadania Spark, czyli wartość liczby Pi.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Pakiet jar przy użyciu obrazu kontenera

W powyższym przykładzie plik jar Spark został przekazany do usługi Azure storage. Innym rozwiązaniem jest plik jar tworzenia pakietów w niestandardowym obrazów platformy Docker.

Aby to zrobić, należy znaleźć `dockerfile` dla obrazu platformy Spark znajdujących się na `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` katalogu. Dodaj mnie `ADD` poufności informacji dla zadania Spark `jar` gdzieś między `WORKDIR` i `ENTRYPOINT` deklaracji.

Zaktualizuj ścieżkę pliku jar do lokalizacji `SparkPi-assembly-0.1.0-SNAPSHOT.jar` plików w systemie deweloperskim. Można również użyć własnego pliku niestandardowego pliku jar.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Skompiluj i Wypchnij obraz przy użyciu uwzględnione skryptów platformy Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Podczas uruchamiania zadania, zamiast wskazujący na adres URL zdalnego pliku jar, `local://` schemat może być używany z ścieżkę do pliku jar w obrazie platformy Docker.

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
> Z platformy Spark [dokumentacji][spark-docs]: "Harmonogram Kubernetes jest obecnie eksperymentalne. W przyszłych wersjach mogą wystąpić zachowań zmiany dotyczące konfiguracji, obrazy kontenera i punkty wejścia".

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z dokumentacją platformy Spark, aby uzyskać więcej informacji.

> [!div class="nextstepaction"]
> [Dokumentacja platformy Spark][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
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
