---
title: Uruchamianie zadania platformy Spark apache za pomocą usługi Azure Kubernetes Service (AKS)
description: Użyj usługi Azure Kubernetes Service (AKS), aby utworzyć i uruchomić zadanie Platformy Spark Apache do przetwarzania danych na dużą skalę.
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 8ebd8990a2fdd43b243f5dd6feb632d782fdeb0b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632687"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Uruchamianie zadań Apache Spark w aks

[Apache Spark][apache-spark] to szybki silnik do przetwarzania danych na dużą skalę. Od [wersji Spark 2.3.0][spark-latest-release]platforma Apache Spark obsługuje natywną integrację z klastrami kubernetes. Usługa Azure Kubernetes Service (AKS) to zarządzane środowisko kubernetes działające na platformie Azure. Ten dokument zawiera szczegółowe informacje dotyczące przygotowywania i uruchamiania zadań platformy Apache Spark w klastrze usługi Azure Kubernetes (AKS).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, potrzebujesz następujących czynności.

* Podstawowa wiedza o Kubernetes i [Apache Spark][spark-quickstart].
* [Docker Hub][docker-hub] lub [rejestru kontenerów platformy Azure][acr-create].
* Narzędzie interfejsu [wiersza polecenia][azure-cli] platformy Azure zainstalowane w systemie dewelopera.
* [JDK 8][java-install] zainstalowany w systemie.
* SBT ([Scala Build Tool][sbt-install]) zainstalowany w systemie.
* Narzędzia wiersza polecenia Git zainstalowane w systemie.

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

Platforma Spark jest używana do przetwarzania danych na dużą skalę i wymaga, aby węzły usługi Kubernetes były dostosowane do wymagań zasobów platformy Spark. Firma Microsoft zaleca `Standard_D3_v2` minimalny rozmiar dla węzłów usługi Azure Kubernetes Service (AKS).

Jeśli potrzebujesz klastra AKS, który spełnia to minimalne zalecenie, uruchom następujące polecenia.

Utwórz grupę zasobów dla klastra.

```azurecli
az group create --name mySparkCluster --location eastus
```

Utwórz jednostkę usługi dla klastra. Po jego utworzeniu, trzeba będzie AppId jednostki usługi i hasło do następnego polecenia.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Utwórz klaster AKS z węzłami o rozmiarze `Standard_D3_v2`i wartościami identyfikatora appId i hasła przekazywane jako parametry jednostki usługi i klucza tajnego klienta.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Połącz się z klastrem AKS.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Jeśli używasz usługi Azure Container Registry (ACR) do przechowywania obrazów kontenerów, skonfiguruj uwierzytelnianie między usługami AKS i ACR. Zobacz [dokumentację uwierzytelniania ACR][acr-aks] dla tych kroków.

## <a name="build-the-spark-source"></a>Tworzenie źródła iskry

Przed uruchomieniem zadań platformy Spark w klastrze AKS należy utworzyć kod źródłowy platformy Spark i spakować go do obrazu kontenera. Źródło platformy Spark zawiera skrypty, które mogą służyć do ukończenia tego procesu.

Sklonuj repozytorium projektów Spark do systemu dewelopera.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Zmień do katalogu sklonowanego repozytorium i zapisz ścieżkę źródła Platformy Spark do zmiennej.

```bash
cd spark
sparkdir=$(pwd)
```

Jeśli masz zainstalowane wiele wersji JDK, ustaw `JAVA_HOME` użycie wersji 8 dla bieżącej sesji.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Uruchom następujące polecenie, aby utworzyć kod źródłowy platformy Spark z obsługą usługi Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Poniższe polecenia utworzyć obraz kontenera Platformy Spark i wypchnąć go do rejestru obrazów kontenera. Zastąp `registry.example.com` nazwą rejestru `v1` kontenerów i tagiem, którego wolisz używać. Jeśli używasz Centrum platformy Docker, ta wartość jest nazwą rejestru. W przypadku korzystania z usługi Azure Container Registry (ACR), ta wartość jest nazwą serwera logowania ACR.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Wypchnij obraz kontenera do rejestru obrazów kontenera.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Przygotowanie zadania platformy Spark

Następnie przygotuj zadanie platformy Spark. Plik jar jest używany do przechowywania zadania Spark `spark-submit` i jest potrzebny podczas uruchamiania polecenia. Słoik może być dostępny za pośrednictwem publicznego adresu URL lub wstępnie spakowane w obrazie kontenera. W tym przykładzie przykładowy słoik jest tworzony do obliczania wartości Pi. Ten słoik jest następnie przekazywał do magazynu platformy Azure. Jeśli masz istniejący słoik, możesz zastąpić

Utwórz katalog, w którym chcesz utworzyć projekt dla zadania platformy Spark.

```bash
mkdir myprojects
cd myprojects
```

Utwórz nowy projekt Scala na podstawie szablonu.

```bash
sbt new sbt/scala-seed.g8
```

Po wyświetleniu `SparkPi` monitu wprowadź nazwę projektu.

```bash
name [Scala Seed Project]: SparkPi
```

Przejdź do nowo utworzonego katalogu projektów.

```bash
cd sparkpi
```

Uruchom następujące polecenia, aby dodać wtyczkę SBT, która umożliwia pakowanie projektu jako pliku jar.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Uruchom te polecenia, aby skopiować przykładowy kod do nowo utworzonego projektu i dodać wszystkie niezbędne zależności.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Aby spakować projekt do słoika, uruchom następujące polecenie.

```bash
sbt assembly
```

Po pomyślnym zapakowaniu powinieneś zobaczyć dane wyjściowe podobne do następujących.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiuj zadanie do magazynu

Utwórz konto magazynu platformy Azure i kontener do przechowywania pliku jar.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Przekaż plik jar do konta magazynu platformy Azure za pomocą następujących poleceń.

```azurecli
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

Zmienna `jarUrl` zawiera teraz publicznie dostępną ścieżkę do pliku jar.

## <a name="submit-a-spark-job"></a>Prześlij zadanie platformy Spark

Uruchom kube-proxy w osobnym wierszu polecenia z następującym kodem.

```bash
kubectl proxy
```

Przejdź z powrotem do katalogu głównego repozytorium platformy Spark.

```bash
cd $sparkdir
```

Utwórz konto usługi, które ma wystarczające uprawnienia do uruchamiania zadania.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Prześlij `spark-submit`zadanie za pomocą programu .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Ta operacja uruchamia zadanie Platformy Spark, które przesyła strumieniowo stan zadania do sesji powłoki. Gdy zadanie jest uruchomione, można zobaczyć podsze sterownika platformy Spark i zasobników executor za pomocą polecenia kubectl get zasobników. Otwórz drugą sesję terminalu, aby uruchomić te polecenia.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Gdy zadanie jest uruchomione, można również uzyskać dostęp do interfejsu użytkownika platformy Spark. W drugiej sesji terminalu `kubectl port-forward` użyj polecenia zapewniają dostęp do interfejsu użytkownika platformy Spark.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Aby uzyskać dostęp do interfejsu `127.0.0.1:4040` spark, otwórz adres w przeglądarce.

![Interfejs użytkownika platformy Spark](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Uzyskaj wyniki i dzienniki zadań

Po zakończeniu zadania zasobnik sterownika będzie w stanie "Ukończone". Pobierz nazwę zasobnika za pomocą następującego polecenia.

```bash
kubectl get pods --show-all
```

Dane wyjściowe:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Użyj `kubectl logs` polecenia, aby uzyskać dzienniki z zasobnika sterownika iskrowego. Zastąp nazwę zasobnika na nazwę zasobnika kierowcy.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

W tych dziennikach można zobaczyć wynik zadania Spark, który jest wartością Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Słoik pakietu z obrazem kontenera

W powyższym przykładzie plik jar platformy Spark został przekazany do magazynu platformy Azure. Inną opcją jest spakowanie pliku jar do niestandardowych obrazów platformy Docker.

Aby to zrobić, `dockerfile` znajdź dla obrazu `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` Spark znajdującego się w katalogu. Dodaj `ADD` instrukcję am `jar` dla `WORKDIR` zadania `ENTRYPOINT` Spark gdzieś między i deklaracji.

Zaktualizuj ścieżkę `SparkPi-assembly-0.1.0-SNAPSHOT.jar` słoika do lokalizacji pliku w systemie dewelopera. Można również użyć własnego pliku niestandardowego jar.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Tworzenie i wypychanie obrazu za pomocą dołączonych skryptów Platformy Spark.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

Podczas uruchamiania zadania, zamiast wskazywać adres URL `local://` zdalnego jar, schemat może być używany ze ścieżką do pliku jar w obrazie platformy Docker.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Z [dokumentacji][spark-docs]Spark : "Harmonogram Kubernetes jest obecnie eksperymentalny. W przyszłych wersjach mogą wystąpić zmiany w zachowaniu wokół konfiguracji, obrazów kontenerów i punktów wejścia".

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją platformy Spark.

> [!div class="nextstepaction"]
> [Dokumentacja iskier][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
