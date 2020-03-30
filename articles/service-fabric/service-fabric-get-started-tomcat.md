---
title: Tworzenie kontenera dla Apache Tomcat w systemie Linux
description: Utwórz kontener systemu Linux, aby udostępnić aplikację działającą na serwerze Apache Tomcat w sieci szkieletowej usługi Azure. Tworzenie obrazu platformy Docker za pomocą aplikacji i serwera Apache Tomcat, wypchnij obraz do rejestru kontenerów, skompiluj i wdrażaj aplikację kontenera sieci szkieletowej usług.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614421"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Tworzenie kontenera sieci szkieletowej usług z uruchomionym serwerem Apache Tomcat w systemie Linux
Apache Tomcat jest popularną, otwartą implementacją technologii Java Servlet i Java Server. W tym artykule pokazano, jak utworzyć kontener z Apache Tomcat i prostej aplikacji sieci Web, wdrożyć kontener do klastra sieci szkieletowej usług z systemem Linux i połączyć się z aplikacją sieci Web.  

Aby dowiedzieć się więcej o Apache Tomcat, zobacz [stronę główną Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Wymagania wstępne
* Komputer dewelopera z następującym oprogramowaniem:
  * [Zestaw SDK sieci szkieletowej usług i narzędzia](service-fabric-get-started-linux.md).
  * [Docker CE dla systemu Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md)

* Rejestr kontenerów w rejestrze kontenerów platformy Azure. Rejestru kontenerów można utworzyć w ramach subskrypcji platformy Azure przy użyciu [witryny Azure portal](../container-registry/container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure.](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry) 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tworzenie obrazu Tomcat i uruchamianie go lokalnie
Wykonaj kroki opisane w tej sekcji, aby utworzyć obraz platformy Docker na podstawie obrazu Apache Tomcat i prostej aplikacji sieci Web, a następnie uruchom go w kontenerze w systemie lokalnym. 
 
1. Sklonuj [usługę Szkieletowa wprowadzenie do](https://github.com/Azure-Samples/service-fabric-java-getting-started) repozytorium próbek języka Java na komputerze deweloperskim.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Zmień katalogi do katalogu przykładowego serwera Apache Tomcat *(service-fabric-java-getting-started/container-apache-tomcat-web-server-sample):*

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Utwórz plik platformy Docker na podstawie oficjalnego [obrazu Tomcat](https://hub.docker.com/_/tomcat/) znajdującego się w centrum docker i przykładzie serwera Tomcat. W katalogu *próbkowania usługi-fabric-java-getting-started/container-apache-tomcat-web-server-sample* utwórz plik o nazwie *Dockerfile* (bez rozszerzenia pliku). Dodaj następujący kod do pliku *Dockerfile* i zapisz zmiany:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zobacz [odwołanie do pliku dockerfile, aby](https://docs.docker.com/engine/reference/builder/) uzyskać więcej informacji.


4. Uruchom `docker build` polecenie, aby utworzyć obraz, który uruchamia aplikację sieci web:

   ```bash
   docker build . -t tomcattest
   ```

   To polecenie tworzy nowy obraz przy użyciu instrukcji zawartych w pliku dockerfile, `tomcattest`nazewnictwo (-t tagowanie) obrazu . Aby utworzyć obraz kontenera, obraz podstawowy jest najpierw pobierany z usługi Docker Hub i aplikacja jest dodawana do niego. 

   Po zakończeniu działania polecenia budowania uruchom polecenie `docker images`, aby wyświetlić informacje o nowym obrazie:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Sprawdź, czy aplikacja konteneryzowana działa lokalnie przed wypchnięciem jej w rejestrze kontenerów:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name`nazywa kontener, dzięki czemu można odwoływać się do niego przy użyciu przyjaznej nazwy, a nie jego identyfikatora.
   * `-p`określa mapowanie portu między kontenerem a hostem systemu operacyjnego. 

   > [!Note]
   > Port otwarty z `-p` parametrem powinien być portem, na który aplikacja Tomcat nasłuchuje żądań. W bieżącym przykładzie istnieje łącznik skonfigurowany w pliku *ApacheTomcat/conf/server.xml* do nasłuchiwać na porcie 8080 dla żądań HTTP. Ten port jest mapowany do portu 8080 na hoście. 

   Aby dowiedzieć się więcej o innych parametrach, zobacz [dokumentację uruchamiania platformy Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Aby przetestować kontener, otwórz przeglądarkę i wprowadź jeden z następujących adresów URL. Zobaczysz wariant "Hello World!" ekran powitalny dla każdego adresu URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Witaj świat /sayhi](./media/service-fabric-get-started-tomcat/hello.png)

2. Zatrzymaj kontener i usuń go z komputera dewelopera:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Wypychanie obrazu Tomcat do rejestru kontenerów
Teraz, gdy zostały zweryfikowane, że obraz Tomcat działa w kontenerze na komputerze deweloperskim, wypchnij go do repozytorium w rejestrze kontenerów. W tym artykule używa usługi Azure Container Registry do przechowywania obrazu, ale z niektórych modyfikacji kroków, można użyć dowolnego rejestru kontenera, który wybierzesz. W tym artykule przyjmuje się, że nazwa rejestru jest *myregistry* i pełna nazwa rejestru jest myregistry.azurecr.io. Zmień je odpowiednio dla danego scenariusza. 

1. Uruchom, `docker login` aby zalogować się do rejestru kontenerów przy użyciu [poświadczeń rejestru](../container-registry/container-registry-authentication.md).

   Poniższy przykład przekazuje identyfikator i hasło [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Na przykład nazwę główną usługi można było przypisać do rejestru dla scenariusza automatyzacji. Możesz też zalogować się przy użyciu nazwy użytkownika i hasła rejestru.

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

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Tworzenie i wdrażanie aplikacji kontenera sieci szkieletowej usług
Teraz, gdy wypchnął obraz Tomcat do rejestru kontenerów, można utworzyć i wdrożyć aplikację kontenera sieci szkieletowej usług, która pobiera obraz Tomcat z rejestru i uruchamia go jako usługi konteneryzowane w klastrze. 

1. Utwórz nowy katalog poza lokalnym klonem (poza drzewem katalogów *usługi-fabric-java-getting-started).* Przełącz się do niego i użyj Yeoman, aby utworzyć rusztowanie dla aplikacji kontenera: 

   ```bash
   yo azuresfcontainer 
   ```
   Po wyświetleniu monitu wprowadź następujące wartości:

   * Nazwij aplikację: ServiceFabricTomcat
   * Nazwa usługi aplikacji: TomcatService
   * Wprowadź nazwę obrazu: Podaj adres URL obrazu kontenera w rejestrze kontenerów; na przykład myregistry.azurecr.io/samples/tomcattest.
   * Polecenia: Pozostaw to puste. Ponieważ ten obraz ma zdefiniowany punkt wejścia obciążenia, nie musisz jawnie określić poleceń wejściowych (są to polecenia uruchamiane wewnątrz kontenera, które zapewnią działanie kontenera po uruchomieniu).
   * Liczba wystąpień aplikacji kontenera gościa: 1

   ![Generator Yeoman usługi Service Fabric dla kontenerów](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. W manifeście usługi *(ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml)* dodaj następujący kod XML pod głównym tagiem **ServiceManfest,** aby otworzyć port, na który aplikacja nasłuchuje żądań. Tag **punktu końcowego** deklaruje protokół i port dla punktu końcowego. W tym artykule konteneryzowana usługa nasłuchuje na porcie 8080: 

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

11. W manifeście aplikacji (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) pod tagiem **ServiceManifestImport** dodaj następujący kod XML. Zastąp **accountName** i **Hasło** w **tagu ReppositoryCredentials** nazwą rejestru kontenerów i hasłem wymaganym do zalogowania się do niego.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   **ContainerHostPolicies** tag określa zasady aktywacji hostów kontenera.
    
   * **Tag PortBinding** konfiguruje zasady mapowania portów portu kontenera na hoście. **Atrybut ContainerPort** jest ustawiony na 8080, ponieważ kontener udostępnia port 8080, jak określono w Pliku Docker. **Atrybut EndpointRef** jest ustawiony na "endpointTest", punkt końcowy zdefiniowany w manifeście usługi w poprzednim kroku. W związku z tym przychodzące żądania do usługi na porcie 8080 są mapowane do portu 8080 w kontenerze. 
   * Tag **RepositoryCredentials** określa poświadczenia, które kontener musi uwierzytelnić za pomocą (prywatnego) repozytorium, z którego pobiera obraz. Nie potrzebujesz tej zasady, jeśli obraz zostanie pobrany z publicznego repozytorium.
    

12. W folderze *ServiceFabricTomcat* połącz się z klastrem sieci szkieletowej usług. 

   * Aby połączyć się z lokalnym klastrem sieci szkieletowej usług, uruchom:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Aby połączyć się z bezpiecznym klastrem platformy Azure, upewnij się, że certyfikat klienta jest obecny jako plik pem w katalogu *ServiceFabricTomcat* i uruchom: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     W poprzednim poleceniu `your-certificate.pem` zastąp nazwą pliku certyfikatu klienta. W środowiskach deweloperskich i testowych certyfikat klastra jest często używany jako certyfikat klienta. Jeśli certyfikat nie jest podpisany samodzielnie, `-no-verify` należy go pominąć. 
       
     Certyfikaty klastra są zazwyczaj pobierane lokalnie jako pliki .pfx. Jeśli certyfikat nie jest jeszcze w formacie PEM, możesz uruchomić następujące polecenie, aby utworzyć plik pem z pliku .pfx:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Jeśli plik .pfx nie jest chroniony `-passin pass:` hasłem, użyj ostatniego parametru.


13. Uruchom skrypt instalacji podany w szablonie, aby wdrożyć aplikację w klastrze. Skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i tworzy wystąpienie aplikacji.

     ```bash
     ./install.sh
     ```

   Po uruchomieniu skryptu instalacji otwórz przeglądarkę i przejdź do Eksploratora sieci szkieletowej usług:
    
   * W klastrze lokalnym `http://localhost:19080/Explorer` użyj (zastąp *localhost* prywatnym adresem IP maszyny Wirtualnej, jeśli używasz vagrantu w systemie Mac OS X).
   * W bezpiecznym klastrze `https://PublicIPorFQDN:19080/Explorer`platformy Azure użyj pliku . 
    
   Rozwiń **węzeł Aplikacje** i zanotuj, że istnieje teraz wpis dla typu aplikacji **ServiceFabricTomcatType**i innego dla pierwszego wystąpienia tego typu. Może upłynąć kilka minut, aby aplikacja w pełni wdrożyć, więc bądź cierpliwy.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Aby uzyskać dostęp do aplikacji na serwerze Tomcat, otwórz okno przeglądarki i wprowadź dowolny z następujących adresów URL. Jeśli wdrożono w klastrze lokalnym, użyj *localhost* dla *PublicIPorFQDN*. Zobaczysz wariant "Hello World!" ekran powitalny dla każdego adresu URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Czyszczenie
Użyj skryptu odinstalowania podanego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji.

```bash
./uninstall.sh
```

Po wypchnięciu obrazu do rejestru kontenerów można usunąć lokalny obraz z komputera dewelopera:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać szybkie kroki dotyczące dodatkowych funkcji kontenera systemu Linux, przeczytaj artykuł [Tworzenie pierwszej aplikacji kontenera sieci szkieletowej usługi w systemie Linux.](service-fabric-get-started-containers-linux.md)
* Aby uzyskać bardziej szczegółowe kroki na kontenerach systemu Linux, przeczytaj [samouczek Tworzenie aplikacji kontenera systemu Linux.](service-fabric-tutorial-create-container-images.md)
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-containers-overview.md).


