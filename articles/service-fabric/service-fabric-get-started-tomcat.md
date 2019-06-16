---
title: Tworzenie kontenera usługi Azure Service Fabric dla oprogramowania Apache Tomcat serwera w systemie Linux | Dokumentacja firmy Microsoft
description: Tworzenie kontenera systemu Linux, aby udostępnić aplikację działającą na serwerze Apache Tomcat w usłudze Azure Service Fabric. Zbuduj obraz Docker za pomocą aplikacji i serwera Apache Tomcat, Wypchnij obraz do rejestru kontenerów, tworzenie i wdrażanie aplikacji kontenera usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/08/2018
ms.author: v-jamebr
ms.openlocfilehash: df3156688f018aee4717271557220396827dd9e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306828"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Tworzenie kontenera usługi Service Fabric z systemem serwera Apache Tomcat w systemie Linux
Apache Tomcat to popularne, typu open-source implementacja technologii Java Servlet i serwer Java. W tym artykule przedstawiono sposób tworzenia kontenera za pomocą oprogramowania Apache Tomcat i prostą aplikację sieci Web, miało miejsce wdrożenie kontenera w klastrze usługi Service Fabric z systemem Linux i połączyć się z aplikacją sieci Web.  

Aby dowiedzieć się więcej na temat oprogramowania Apache Tomcat, zobacz [strona główna oprogramowania Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Wymagania wstępne
* Komputer dewelopera z następującym oprogramowaniem:
  * [Zestaw SDK usługi Service Fabric oraz narzędzia](service-fabric-get-started-linux.md).
  * [Docker CE dla systemu Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md)

* Rejestr kontenerów w usłudze Azure Container Registry. Można utworzyć rejestr kontenerów w subskrypcji platformy Azure przy użyciu [witryny Azure portal](../container-registry/container-registry-get-started-portal.md) lub [wiersza polecenia platformy Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Zbuduj obraz Tomcat i uruchom lokalnie
Wykonaj kroki opisane w tej sekcji, aby utworzyć obraz platformy Docker na podstawie obrazu Apache Tomcat i prostą aplikację sieci Web, a następnie uruchom go w kontenerze, w systemie lokalnym. 
 
1. Klonuj [usługi Service Fabric, wprowadzenie do języka Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) repozytorium przykładów na komputerze deweloperskim.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Zmień katalogi na katalog przykładu serwera Apache Tomcat (*service-fabric-java-getting-started/container-apache-tomcat-web-server-sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Tworzenie pliku platformy Docker, w oparciu o official będzie przydatna dla [obraz Tomcat](https://hub.docker.com/_/tomcat/) znajdujących się na przykład serwer Tomcat i Docker Hub. W *service-fabric-java-getting-started/container-apache-tomcat-web-server-sample* katalogu Utwórz plik o nazwie *pliku Dockerfile* (z bez rozszerzenia). Dodaj następujący kod do pliku *Dockerfile* i zapisz zmiany:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zobacz [odwołanie do pliku Dockerfile](https://docs.docker.com/engine/reference/builder/) Aby uzyskać więcej informacji.


4. Uruchom `docker build` polecenie, aby utworzyć obraz uruchamiający aplikację sieci web:

   ```bash
   docker build . -t tomcattest
   ```

   To polecenie powoduje utworzenie nowego obrazu zgodnie z instrukcjami w pliku Dockerfile, nazewnictwa (-t znakowanie) obraz `tomcattest`. Aby utworzyć obraz kontenera, obraz podstawowy jest najpierw pobierany z usługi Docker Hub, a aplikacja zostanie dodany do niego. 

   Po zakończeniu działania polecenia budowania uruchom polecenie `docker images`, aby wyświetlić informacje o nowym obrazie:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Sprawdź, czy konteneryzowanej aplikacji jest uruchamiany lokalnie, przed wypchnięciem rejestru kontenerów:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nazwy kontenera, dzięki czemu mogą odwoływać się do niego przy użyciu przyjaznej nazwy, a nie jego identyfikator.
   * `-p` Określa mapowania portów między kontenerem a systemem operacyjnym hosta. 

   > [!Note]
   > Port, Otwórz za pomocą `-p` parametr powinien być port aplikacji Tomcat nasłuchuje żądań. W bieżącym przykładzie istnieje łącznik, który został skonfigurowany w *ApacheTomcat/conf/server.xml* pliku do nasłuchiwania na porcie 8080 dla żądań HTTP. Ten port jest mapowane na port 8080 na hoście. 

   Aby dowiedzieć się więcej na temat innych parametrów, zobacz [polecenie Docker run dokumentacji](https://docs.docker.com/engine/reference/commandline/run/).

1. Aby przetestować kontenera, otwórz przeglądarkę i wprowadź jedną z następujących adresów URL. Zostanie wyświetlony wariant "Hello World!" ekran powitalny dla każdego adresu URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Witaj świecie /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Zatrzymaj kontenera i usuń go z komputera dewelopera:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Wypchnij obraz Tomcat do rejestru kontenerów
Teraz, gdy masz pewność działania Tomcat obrazu w kontenerze na komputerze deweloperskim, Wypchnij go do repozytorium w rejestrze kontenerów. W tym artykule używa usługi Azure Container Registry do przechowywania obrazu, ale w niektórych modyfikacji kroków, możesz użyć dowolnego rejestru kontenerów, które wybierzesz. W tym artykule nazwę rejestru zakłada się, że *myregistry* , a nazwa rejestru pełną to myregistry.azurecr.io. Zmiany są odpowiednie dla danego scenariusza. 

1. Uruchom `docker login` zalogować się do rejestru kontenerów za pomocą usługi [poświadczeń rejestru](../container-registry/container-registry-authentication.md).

   Poniższy przykład przekazuje identyfikator i hasło [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Na przykład nazwę główną usługi można było przypisać do rejestru dla scenariusza automatyzacji. Alternatywnie można logowania przy użyciu nazwy i hasła użytkownika rejestru.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Następujące polecenie tworzy tag lub alias obrazu z w pełni kwalifikowaną ścieżką do Twojego rejestru. W tym przykładzie obraz jest umieszczany w przestrzeni nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Wypchnij obraz do rejestru kontenerów:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Tworzenie i wdrażanie aplikacji kontenera usługi Service Fabric
Teraz, gdy obraz Tomcat zostały wypchnięte do rejestru kontenera, można tworzyć i wdrażanie aplikacji kontenera usługi Service Fabric, która ściąga obraz Tomcat z rejestru i uruchamia go jako konteneryzowanej usługi w klastrze. 

1. Utwórz nowy katalog poza lokalnym klonie (poza *usługi fabric-java-getting-started* drzewo katalogów). Przejdź do niego, a następnie tworzenie szkieletu dla aplikacji kontenera za pomocą generatora Yeoman: 

   ```bash
   yo azuresfcontainer 
   ```
   Wprowadź następujące wartości, po wyświetleniu monitu:

   * Nadaj nazwę aplikacji: ServiceFabricTomcat
   * Nazwa usługi aplikacji: TomcatService
   * Wprowadź nazwę obrazu: Podaj adres URL obrazu kontenera w rejestru kontenerów. na przykład myregistry.azurecr.io/samples/tomcattest.
   * Polecenia: Pozostaw to pole puste. Ponieważ ten obraz ma zdefiniowany punkt wejścia obciążenia, nie musisz jawnie określić poleceń wejściowych (są to polecenia uruchamiane wewnątrz kontenera, które zapewnią działanie kontenera po uruchomieniu).
   * Liczba wystąpień aplikacji kontenera gościa: 1

   ![Generator Yeoman usługi Service Fabric dla kontenerów](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. W manifeście usługi (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*), Dodaj następujący kod XML w katalogu głównym **ServiceManfest** tag, aby otworzyć port usługi aplikacja nasłuchuje żądań. **Punktu końcowego** tag deklaruje protokół i port punktu końcowego. W tym artykule konteneryzowana usługa nasłuchuje na porcie 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. W manifeście aplikacji (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) w obszarze **ServiceManifestImport** tag, Dodaj następujący kod XML. Zastąp **AccountName** i **hasło** w **RepositoryCredentials** tagu o nazwie usługi container registry i hasła wymagane do logowania się do niego.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   **ContainerHostPolicies** tag określa zasady do aktywacji na hostach kontenerów.
    
   * **PortBinding** tag konfiguruje zasady mapowania portów kontenera typu port do hosta. **ContainerPort** atrybut jest ustawiony na 8080, ponieważ kontener uwidacznia port 8080, jak określono w pliku Dockerfile. **EndpointRef** atrybut jest ustawiony na "endpointTest" punkt końcowy zdefiniowany w manifeście usługi w poprzednim kroku. W związku z tym żądania przychodzące do usługi na porcie 8080 są mapowane na port 8080 w kontenerze. 
   * **RepositoryCredentials** tag Określa poświadczenia, które wymaga uwierzytelniania repozytorium (prywatny), gdzie ściągania obrazów z kontenera. Jeśli obraz będzie pobierany z publicznego repozytorium, nie potrzebujesz tych zasad.
    

12. W *ServiceFabricTomcat* folderze połączenia z klastrem usługi Service fabric. 

   * Aby połączyć się z lokalnym klastrem usługi Service Fabric, uruchom polecenie:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Aby nawiązać połączenie z zabezpieczonym klastrem platformy Azure, upewnij się, certyfikat klienta jest obecny jako plik PEM na *ServiceFabricTomcat* katalogu i uruchom: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     W poprzednim poleceniu zastąp `your-certificate.pem` o nazwie pliku z certyfikatem klienta. W środowiskach tworzenia i testowania certyfikat klastra jest często używany jako certyfikat klienta. Jeśli certyfikat nie jest podpisem własnym, Pomiń `-no-verify` parametru. 
       
     Certyfikatów klastra są zwykle pobierane lokalnie jako pliki PFX. Jeśli nie masz jeszcze certyfikatu w formacie PEM, możesz uruchomić następujące polecenie, aby utworzyć plik PEM z pliku PFX:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Jeśli plik PFX nie jest chroniony hasłem, użyj `-passin pass:` ostatniego parametru.


13. Uruchamianie skryptu instalacji udostępnionego w szablonie, aby wdrożyć aplikację w klastrze. Skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i tworzy wystąpienie aplikacji.

     ```bash
     ./install.sh
     ```

   Po uruchomieniu skryptu instalacji, otwórz przeglądarkę i przejdź do narzędzia Service Fabric Explorer:
    
   * W klastrze lokalnym, użyj `http://localhost:19080/Explorer` (Zastąp *localhost* prywatnym adresem IP maszyny wirtualnej w przypadku używania narzędzia Vagrant w systemie Mac OS X).
   * W zabezpieczonym klastrze platformy Azure, użyj `https://PublicIPorFQDN:19080/Explorer`. 
    
   Rozwiń **aplikacje** węzeł i zwróć uwagę, że istnieje teraz wpis dla danego typu aplikacji **ServiceFabricTomcatType**i inny wpis dla pierwszego wystąpienia tego typu. Może upłynąć kilka minut dla aplikacji do wdrożenia w pełni, więc o cierpliwość.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Dostępu do aplikacji na serwerze Tomcat, Otwórz okno przeglądarki i wprowadzić dowolne z następujących adresów URL. Jeśli wdrożono w klastrze lokalnym, użyj *localhost* dla *PublicIPorFQDN*. Zostanie wyświetlony wariant "Hello World!" ekran powitalny dla każdego adresu URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Czyszczenie
Użyj skryptu dezinstalacji udostępnionego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji.

```bash
./uninstall.sh
```

Po wypchnięciu obrazu do rejestru kontenerów można usunąć lokalny obraz z komputera dewelopera:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Kolejne kroki
* Szybkie kroki na dodatkowe funkcje kontenera systemu Linux można znaleźć [tworzenie pierwszej aplikacji kontenera usługi Service Fabric w systemie Linux](service-fabric-get-started-containers-linux.md).
* Aby uzyskać szczegółowe instrukcje dotyczące kontenerów systemu Linux, przeczytaj [tworzenie samouczek aplikacji kontenera systemu Linux](service-fabric-tutorial-create-container-images.md) samouczka.
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-containers-overview.md).


