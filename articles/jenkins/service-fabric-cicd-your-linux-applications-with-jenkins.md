---
title: Ciągła kompilacja dla aplikacji systemu Linux przy użyciu usługi Jenkins
description: Ciągła kompilacja i integracja aplikacji sieci szkieletowej usługi Linux przy użyciu usługi Jenkins
keywords: jenkins, azure, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77675239"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Używanie usługi Jenkins do tworzenia i wdrażania aplikacji systemu Linux

W tym samouczku opisano kilka możliwych sposobów konfigurowania środowiska usługi Jenkins, a także różne sposoby wdrażania aplikacji w klastrze sieci szkieletowej usług po jej utworzeniu. Wykonaj następujące ogólne kroki, aby pomyślnie skonfigurować usługi Jenkins, wyciągnąć zmiany z usługi GitHub, utworzyć aplikację i wdrożyć ją w klastrze:

1. Upewnij się, że zainstalowano [wymagania wstępne](#prerequisites).
1. Następnie wykonaj kroki opisane w jednej z tych sekcji, aby skonfigurować usługi Jenkins:
   * [Konfigurowanie usługi Jenkins wewnątrz klastra sieci szkieletowej usług](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurowanie usługi Jenkins poza klastrem sieci szkieletowej usług](#set-up-jenkins-outside-a-service-fabric-cluster)lub
   * [Zainstaluj wtyczkę sieci szkieletowej usług w istniejącym środowisku usługi Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Po skonfigurowaniu usługi Jenkins wykonaj kroki opisane w [temacie Tworzenie i konfigurowanie zadania usługi Jenkins](#create-and-configure-a-jenkins-job) w celu skonfigurowania usługi GitHub w celu wyzwolenia usługi Jenkins po wprowadzeniu zmian w aplikacji i skonfigurowania potoku zadania usługi Jenkins za pomocą kroku kompilacji w celu wyciągnięcia zmian z usługi GitHub i utworzenia aplikacji. 
1. Na koniec należy skonfigurować krok zadania usługi Jenkins po kompilacji, aby wdrożyć aplikację w klastrze sieci szkieletowej usług. Istnieją dwa sposoby konfigurowania usługi Jenkins do wdrażania aplikacji w klastrze:    
   * W przypadku środowisk deweloperskich i testowych należy użyć [opcji Konfiguruj wdrażanie przy użyciu punktu końcowego zarządzania klastrami](#configure-deployment-using-cluster-management-endpoint). Jest to najprostsza metoda wdrażania do skonfigurowania.
   * W środowiskach produkcyjnych należy użyć [opcji Konfiguruj wdrażanie przy użyciu poświadczeń platformy Azure.](#configure-deployment-using-azure-credentials) Firma Microsoft zaleca tę metodę dla środowisk produkcyjnych, ponieważ za pomocą poświadczeń platformy Azure można ograniczyć dostęp, który ma zadanie usługi Jenkins do zasobów platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że git jest zainstalowany lokalnie. Możesz zainstalować odpowiednią wersję Git [ze strony pobierania Git](https://git-scm.com/downloads) na podstawie systemu operacyjnego. Jeśli jesteś nowy w Git, dowiedz się więcej o tym z [dokumentacji Git](https://git-scm.com/docs).
- W tym artykule użyto *przykładu wprowadzenie do sieci szkieletowej usług* w usłudze GitHub: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) dla aplikacji do tworzenia i wdrażania. Możesz rozwidlić to repozytorium, aby wykonać następujące działania lub, z pewną modyfikacją instrukcji, użyć własnego projektu GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalowanie wtyczki sieci szkieletowej usług w istniejącym środowisku usługi Jenkins

Jeśli dodajesz wtyczkę sieci szkieletowej usług do istniejącego środowiska usługi Jenkins, musisz wykonać następujące kroki:

- [Interfejsu wiersza polecenia sieci szkieletowej usług (sfctl)](../service-fabric/service-fabric-cli.md). Zainstaluj interfejs wiersza polecenia na poziomie systemu, a nie na poziomie użytkownika, dzięki czemu usługa Jenkins może uruchamiać polecenia interfejsu wiersza polecenia. 
- Aby wdrożyć aplikacje Java, zainstaluj zarówno [Gradle, jak i Open JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Aby wdrożyć aplikacje .NET Core 2.0, zainstaluj [pakiet .NET Core 2.0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po zainstalowaniu wymagań wstępnych wymaganych dla środowiska, można wyszukać wtyczkę sieci szkieletowej usługi Azure w portalu Jenkins marketplace i zainstalować go.

Po zainstalowaniu wtyczki przejdź do przodu, [aby utworzyć i skonfigurować zadanie jenkinsa](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins w ramach klastra usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach pokazano, jak skonfigurować go wewnątrz klastra podczas korzystania z konta magazynu platformy Azure, aby zapisać stan wystąpienia kontenera.

1. Upewnij się, że masz klaster Linux sieci szkieletowej usług z zainstalowanym programem Docker. Klastry sieci szkieletowej usług uruchomione na platformie Azure mają już zainstalowaną platformę Docker. Jeśli korzystasz z klastra lokalnie (środowisko deweloperów OneBox), sprawdź, czy `docker info` docker jest zainstalowany na komputerze za pomocą polecenia. Jeśli nie jest zainstalowany, zainstaluj go za pomocą następujących poleceń:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Upewnij się, że port 8081 jest określony jako niestandardowy punkt końcowy w klastrze. Jeśli używasz klastra lokalnego, upewnij się, że port 8081 jest otwarty na komputerze-hoście i że ma publiczny adres IP.
   >

1. Klonuj aplikację za pomocą następujących poleceń:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Utrwalić stan kontenera usługi Jenkins w udziale plików:
   1. Utwórz konto magazynu platformy Azure w tym samym `sfjenkinsstorage1`regionie **co** klaster o nazwie, takiej jak .
   1. Utwórz **udział plików** w obszarze konto `sfjenkins`magazynu o nazwie takiej jak .
   1. Kliknij na **Połącz** dla udziału plików i zanotuj wartości wyświetlane w obszarze **Łączenie z Linuksa**, wartość powinna wyglądać podobnie do poniższej:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Aby zainstalować udziały cifs, musisz mieć zainstalowany pakiet cifs-utils w węzłach klastra.      
   >

1. Zaktualizuj wartości `setupentrypoint.sh` zastępcze w skrypcie za pomocą szczegółów magazynu azure z kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Wymień `[REMOTE_FILE_SHARE_LOCATION]` `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` wartość z wyjścia połączenia w kroku 2 powyżej.
   * Zamień `[FILE_SHARE_CONNECT_OPTIONS_STRING]` `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` na wartość z kroku 2 powyżej.

1. **Tylko bezpieczny klaster:** 
   
   Aby skonfigurować wdrażanie aplikacji w bezpiecznym klastrze z usługi Jenkins, certyfikat klastra musi być dostępny w kontenerze usługi Jenkins. W pliku *ApplicationManifest.xml* pod tagiem **ContainerHostPolicies** dodaj to odwołanie do certyfikatu i zaktualizuj wartość odcisku palca z wartością certyfikatu klastra.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ponadto dodaj następujące wiersze w obszarze **ApplicationManifest** (główny) tag w *pliku ApplicationManifest.xml* i zaktualizować wartość odcisku palca z wartości certyfikatu klastra.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Połącz się z klastrem i zainstaluj aplikację kontenera.

   **Bezpieczny klaster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Poprzednie polecenie przyjmuje certyfikat w formacie PEM. Jeśli certyfikat jest w formacie PFX, można użyć następującego polecenia, aby go przekonwertować. Jeśli plik PFX nie jest chroniony hasłem, określ parametr **passin** jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Niezabezpieczony klaster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   To spowoduje zainstalowanie kontenera narzędzia Jenkins w klastrze i umożliwi monitorowanie za pomocą narzędzia Service Fabric Explorer.

   > [!NOTE]
   > Pobranie obrazu usługi Jenkins w klastrze może potrwać kilka minut.
   >

1. W przeglądarce przejdź na adres `http://PublicIPorFQDN:8081`. Tam jest dostępna ścieżka do początkowego hasła administratora wymaganego do zalogowania się. 
1. Spójrz na Eksploratora sieci szkieletowej usług, aby określić, w którym węźle jest uruchomiony kontener jenkinsa. Secure Shell (SSH) zaloguj się do tego węzła.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Uzyskaj identyfikator wystąpienia kontenera za pomocą polecenia `docker ps -a`.
1. Secure Shell (SSH) zaloguj się do kontenera i wklej ścieżkę, która została wyświetlona w portalu jenkins. Na przykład, jeśli w portalu `PATH_TO_INITIAL_ADMIN_PASSWORD`pokazuje ścieżkę , uruchom następujące polecenia:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stronie Wprowadzenie do usługi Jenkins wybierz opcję Wybierz wtyczki do zainstalowania, zaznacz pole wyboru **Brak** i kliknij przycisk Zainstaluj.
1. Utwórz użytkownika lub wybierz, aby kontynuować jako administrator.

Po skonfigurowaniu usługi Jenkins przejdź do przodu, [aby utworzyć i skonfigurować zadanie jenkinsa](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins poza klastrem usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach opisano sposób konfiguracji narzędzia poza klastrem.

1. Upewnij się, że docker jest `docker info` zainstalowany na komputerze, uruchamiając go w terminalu. Dane wyjściowe wskazują, czy usługa Docker jest uruchomiona.

1. Jeśli docker nie jest zainstalowany, uruchom następujące polecenia:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Ściągnij obraz kontenera narzędzia Jenkins usługi Service Fabric: `docker pull rapatchi/jenkins:latest`. Ten obraz jest dostarczany ze wstępnie zainstalowaną wtyczką narzędzia Jenkins usługi Service Fabric.
1. Uruchom obraz kontenera: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Uzyskaj identyfikator wystąpienia obrazu kontenera. Listę wszystkich kontenerów platformy Docker można wyświetlić za pomocą polecenia `docker ps –a`
1. Zaloguj się do portalu jenkinsa, wykonując następujące czynności:

   1. Zaloguj się do powłoki jenkinsa od hosta. Użyj pierwszych czterech cyfr identyfikatora kontenera. Na przykład, jeśli identyfikator `2d24a73b5964`kontenera `2d24`jest , użyj .

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. W powłoce usługi Jenkins pobierz hasło administratora dla wystąpienia kontenera:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Aby zalogować się do pulpitu nawigacyjnego `http://<HOST-IP>:8080`usługi Jenkins, otwórz następujący adres URL w przeglądarce internetowej: . Użyj hasła z poprzedniego kroku, aby odblokować usługi Jenkins.
   1. (Opcjonalnie). Po zalogowaniu się po raz pierwszy możesz utworzyć własne konto użytkownika i użyć go w następujących krokach lub nadal korzystać z konta administratora. Jeśli utworzysz użytkownika, musisz kontynuować z tym użytkownikiem.
1. Skonfiguruj gitHub do pracy z jenkinsem, wykonując czynności opisane w [2005 r. w ygenerowanie nowego klucza SSH i dodawanie go do agenta SSH.](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
   * Korzystając z instrukcji udostępnionych przez usługę GitHub, wygeneruj klucz SSH i dodaj go do konta usługi GitHub, które hostuje repozytorium.
   * Uruchom polecenia wymienione pod powyższym linkiem w powłoce platformy Docker narzędzia Jenkins (a nie na hoście).
   * Aby zalogować się do powłoki narzędzia Jenkins z poziomu hosta, użyj następującego polecenia:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Upewnij się, że klaster lub komputer, na którym znajduje się obraz kontenera usługi Jenkins, ma publiczny adres IP. Dzięki temu wystąpienie narzędzia Jenkins może otrzymywać powiadomienia z usługi GitHub.

Po skonfigurowaniu usługi Jenkins przejdź do następnej sekcji [Utwórz i skonfiguruj zadanie Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Tworzenie i konfigurowanie zadania narzędzia Jenkins

Kroki opisane w tej sekcji pokazują, jak skonfigurować zadanie usługi Jenkins, aby reagować na zmiany w repozytorium GitHub, pobierać zmiany i tworzyć je. Na końcu tej sekcji zostaniesz przekierowany do ostatnich kroków, aby skonfigurować zadanie do wdrożenia aplikacji na podstawie tego, czy wdrażasz w środowisku deweloperskim/testowym, czy w środowisku produkcyjnym. 

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij pozycję **Nowy element**.
1. Wprowadź nazwę elementu (na przykład **MyJob**). Wybierz pozycję **free-style project** (projekt w stylu dowolnym) i kliknij przycisk **OK**.
1. Zostanie otwarta strona Konfiguracja zadania. (Aby przejść do konfiguracji z pulpitu nawigacyjnego usługi Jenkins, kliknij zadanie, a następnie kliknij przycisk **Konfiguruj**).

1. Na karcie **Ogólne** zaznacz pole wyboru **dla projektu Usługi GitHub**i określ adres URL projektu Usługi GitHub. Pod tym adresem URL jest hostowana aplikacja Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na karcie **Zarządzanie kodem źródłowym** wybierz pozycję **Git**. Określ adres URL repozytorium hostującego aplikację Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Można również określić, która gałąź ma być zbudowana `/master`(na przykład ).
1. Skonfiguruj repozytorium *Usługi GitHub,* aby rozmawiać z witryną Jenkins:

   a. Na stronie repozytorium GitHub przejdź do **sekcji Integracje i** > **usługi**ustawień .

   b. Wybierz pozycję **Add Service** (Dodaj usługę), wpisz ciąg **Jenkins** i wybierz pozycję **Jenkins-Github plugin** (Wtyczka Jenkins-Github).

   d. Wprowadź adres URL elementu webhook narzędzia Jenkins (domyślnie adres URL powinien być następujący: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kliknij pozycję **add/update service** (dodaj/aktualizuj usługę).

   d. Do wystąpienia narzędzia Jenkins zostanie wysłane zdarzenie testowe. W usłudze GitHub powinien zostać wyświetlony zielony znacznik wyboru obok elementu webhook i projekt zostanie skompilowany.

1. Na karcie **Wyzwalacze kompilacji** w u jenkinsie wybierz odpowiednią opcję kompilacji. W tym przykładzie chcesz wyzwolić kompilację za każdym razem, gdy nastąpi wypychanie do repozytorium, więc wybierz **wyzwalacz haka GitHub dla sondowania GITScm**. Wcześniej ta opcja miała nazwę **Build when a change is pushed to GitHub** (Kompiluj po wypchnięciu zmiany do usługi GitHub).
1. Na karcie **Kompilacja** wykonaj jedną z następujących czynności w zależności od tego, czy tworzysz aplikację Java, czy aplikację .NET Core:

   * **Dla aplikacji Java:** Z listy rozwijanej **Dodaj krok kompilacji** wybierz pozycję **Wywołaj skrypt gradle**. Kliknij pozycję **Zaawansowane**. W menu zaawansowanym określ ścieżkę do **głównego skryptu kompilacji** dla aplikacji. Spowoduje to wybranie elementu build.gradle z określonej ścieżki i wykonanie odpowiedniego działania. Dla [aplikacji ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`jest to: .

     ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Dla aplikacji .NET Core:** Z listy rozwijanej **Dodaj krok kompilacji** wybierz pozycję **Wykonaj powłokę**. W wyświetlonym polu polecenia katalog najpierw musi zostać zmieniony na ścieżkę, w której znajduje się plik build.sh. Po zmianie katalogu można uruchomić skrypt build.sh i utworzyć aplikację.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Poniższy zrzut ekranu przedstawia przykład poleceń, które są używane do tworzenia przykładu [usługi licznika](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) o nazwie zadania usługi Jenkins CounterServiceApplication.

      ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Aby skonfigurować usługę Jenkins do wdrażania aplikacji w klastrze sieci szkieletowej usług w akcjach po kompilacji, potrzebujesz lokalizacji certyfikatu tego klastra w kontenerze usługi Jenkins. Wybierz jedną z następujących opcji w zależności od tego, czy kontener usługi Jenkins działa wewnątrz, czy na zewnątrz klastra, i zanotuj lokalizację certyfikatu klastra:

   * **W przypadku usługi Jenkins działającej wewnątrz klastra:** Ścieżkę do certyfikatu można znaleźć, powtarzając wartość *zmiennej* środowiskowej Certificates_JenkinsOnSF_Code_MyCert_PEM z poziomu kontenera.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **W przypadku usługi Jenkins działającej poza klastrem:** Wykonaj następujące kroki, aby skopiować certyfikat klastra do kontenera:
     1. Certyfikat musi być w formacie PEM. Jeśli nie masz pliku PEM, możesz go utworzyć z pliku PFX certyfikatu. Jeśli plik PFX nie jest chroniony hasłem, uruchom następujące polecenie od hosta:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Jeśli plik PFX jest chroniony hasłem, `-passin` dołącz hasło do parametru. Przykład:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Aby uzyskać identyfikator kontenera dla kontenera usługi Jenkins, uruchom `docker ps` z hosta.
     1. Skopiuj plik PEM do kontenera za pomocą następującego polecenia platformy Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Jesteś prawie gotowy! Zachowaj zadanie Jenkins otwarte. Jedynym pozostałym zadaniem jest skonfigurowanie kroków po kompilacji w celu wdrożenia aplikacji w klastrze sieci szkieletowej usług:

* Aby wdrożyć w środowisku deweloperskim lub testowym, wykonaj kroki opisane w [temacie Konfigurowanie wdrażania przy użyciu punktu końcowego zarządzania klastrami](#configure-deployment-using-cluster-management-endpoint).
* Aby wdrożyć w środowisku produkcyjnym, wykonaj kroki opisane w [temacie Konfigurowanie wdrażania przy użyciu poświadczeń platformy Azure.](#configure-deployment-using-azure-credentials)

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurowanie wdrażania przy użyciu punktu końcowego zarządzania klastrami

W środowiskach deweloperskich i testowych można użyć punktu końcowego zarządzania klastrem do wdrożenia aplikacji. Konfigurowanie akcji po kompilacji z punktem końcowym zarządzania klastrem w celu wdrożenia aplikacji wymaga najmniejszej konfiguracji. Jeśli wdrażasz w środowisku produkcyjnym, przejdź do [wyprzedzenia, aby skonfigurować wdrożenie przy użyciu poświadczeń platformy Azure,](#configure-deployment-using-azure-credentials) aby skonfigurować jednostkę usługi Azure Active Directory do użycia podczas wdrażania.    

1. W zadaniu Jenkins kliknij kartę **Akcje po kompilacji.** 
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **Konfiguracja klastra sieci szkieletowej usług**wybierz przycisk opcji Wypełnij punkt końcowy **zarządzania siecią szkieletową.**
1. W przypadku **hosta zarządzania**wprowadź punkt końcowy połączenia dla klastra; na `{your-cluster}.eastus.cloudapp.azure.com`przykład .
1. W przypadku **klucza klienta** i **certyfikatu klienta**wprowadź lokalizację pliku PEM w kontenerze usługi Jenkins; na `/var/jenkins_home/clustercert.pem`przykład . (Lokalizacja certyfikatu została skopiowana jako ostatni krok [utwórz i skonfiguruj zadanie Jenkinsa).](#create-and-configure-a-jenkins-job)
1. W obszarze **Konfiguracja aplikacji**skonfiguruj **pola Nazwa aplikacji**, Typ **aplikacji**i **(względna) Ścieżka do manifestu aplikacji.**

   ![Usługa Sieci szkieletowej Jenkins po kompilacji akcja konfigurowania punktu końcowego zarządzania](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kliknij **pozycję Weryfikuj konfigurację**. Po pomyślnej weryfikacji kliknij przycisk **Zapisz**. Potok zadań usługi Jenkins jest teraz w pełni skonfigurowany. Przejdź do [następnego kroki,](#next-steps) aby przetestować wdrożenie.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurowanie wdrażania przy użyciu poświadczeń platformy Azure

W środowiskach produkcyjnych zdecydowanie zaleca się skonfigurowanie poświadczeń platformy Azure w celu wdrożenia aplikacji. W tej sekcji pokazano, jak skonfigurować jednostkę usługi Azure Active Directory do użycia do wdrożenia aplikacji w akcji po kompilacji. Można przypisać jednostki usługi do ról w katalogu, aby ograniczyć uprawnienia zadania usługi Jenkins. 

W środowiskach deweloperskich i testowych można skonfigurować poświadczenia platformy Azure lub punkt końcowy zarządzania klastrem w celu wdrożenia aplikacji. Aby uzyskać szczegółowe informacje dotyczące konfigurowania punktu końcowego zarządzania klastrem, zobacz [Konfigurowanie wdrażania przy użyciu punktu końcowego zarządzania klastrami](#configure-deployment-using-cluster-management-endpoint).   

1. Aby utworzyć jednostkę usługi Azure Active Directory i przypisać jej uprawnienia w ramach subskrypcji platformy Azure, wykonaj kroki opisane w [obszarze Korzystanie z portalu w celu utworzenia aplikacji i jednostki usługi Azure Active Directory.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) Należy zwrócić uwagę na następujące kwestie:

   * Wykonując czynności opisane w temacie, należy skopiować i zapisać następujące wartości: *Identyfikator aplikacji,* *Klucz aplikacji,* *Identyfikator katalogu (identyfikator dzierżawy)* i *Identyfikator subskrypcji*. Potrzebne są do skonfigurowania poświadczeń platformy Azure w usłudze Jenkins.
   * Jeśli nie masz [wymaganych uprawnień](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) do katalogu, musisz poprosić administratora o przyznanie uprawnień lub utworzenie jednostki usługi dla Ciebie lub musisz skonfigurować punkt końcowy zarządzania dla klastra w **akcjach po kompilacji** dla zadania w usłudze Jenkins.
   * W sekcji [Tworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) można wprowadzić dowolny dobrze sformułowany adres URL adresu URL **logowania**.
   * W sekcji [Przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) można przypisać aplikacji rolę *czytnika* w grupie zasobów dla klastra.

1. W zadaniu jenkins kliknij kartę **Akcje po kompilacji.**
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **Konfiguracja klastra sieci szkieletowej usług**kliknij pozycję **Wybierz klaster sieci szkieletowej usług**. Kliknij **pozycję Dodaj** obok pozycji Poświadczenia platformy **Azure**. Kliknij pozycję **Jenkins,** aby wybrać dostawcę poświadczeń usługi Jenkins.
1. W dostawcy poświadczeń usługi Jenkins wybierz pozycję **Podmiotu zabezpieczeń usługi Platformy Microsoft Azure** z listy rozwijanej **Rodzaj.**
1. Użyj wartości zapisanych podczas konfigurowania jednostki usługi w kroku 1, aby ustawić następujące pola:

   * **Identyfikator klienta**: *Identyfikator aplikacji*
   * **Klucz tajny klienta:** *Klucz aplikacji*
   * **Identyfikator dzierżawy**: *Identyfikator katalogu*
   * **Identyfikator subskrypcji:** *Identyfikator subskrypcji*
1. Wprowadź opisowy **identyfikator** używany do wybierania poświadczeń w uprzeglądarce Jenkins i krótki **opis**. Następnie kliknij pozycję **Weryfikuj jednostkę usługi**. Jeśli weryfikacja zakończy się pomyślnie, kliknij przycisk **Dodaj**.

   ![Usługa Service Fabric Jenkins wprowadza poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. W obszarze **Konfiguracja klastra sieci szkieletowej usług**upewnij się, że nowe poświadczenia są wybrane dla **poświadczeń platformy Azure**. 
1. Z **listy** rozwijanej Grupa zasobów wybierz grupę zasobów klastra, w której chcesz wdrożyć aplikację.
1. Z listy rozwijanej **sieci szkieletowej usług** wybierz klaster, w który chcesz wdrożyć aplikację.
1. W polu **Klucz klienta** i **certyfikat klienta**wprowadź lokalizację pliku PEM w kontenerze usługi Jenkins. Na przykład: `/var/jenkins_home/clustercert.pem`. 
1. W obszarze **Konfiguracja aplikacji**skonfiguruj **pola Nazwa aplikacji**, Typ **aplikacji**i **(względna) Ścieżka do manifestu aplikacji.**
    ![Akcja po kompilacji usługi Jenkins — konfigurowanie poświadczeń platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kliknij **pozycję Weryfikuj konfigurację**. Po pomyślnej weryfikacji kliknij przycisk **Zapisz**. Potok zadań usługi Jenkins jest teraz w pełni skonfigurowany. Przejdź do [następnych kroków,](#next-steps) aby przetestować wdrożenie.

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli wystąpią jakiekolwiek błędy z wtyczkami jenkins, zgładź problem w [JIRA usługi Jenkins](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="ideas-to-try"></a>Pomysły do wypróbowania

Usługa GitHub i narzędzie Jenkins są teraz skonfigurowane. Rozważ wprowadzenie pewnych `reliable-services-actor-sample/Actors/ActorCounter` przykładowych zmian w projekcie w rozwidrzeć repozytorium. https://github.com/Azure-Samples/service-fabric-java-getting-started Wypchnij zmiany `master` do gałęzi zdalnej (lub dowolnej gałęzi skonfigurowaną do pracy). Spowoduje to wyzwolenie skonfigurowanego zadania narzędzia Jenkins `MyJob`. Pobiera zmiany z gitHub, tworzy je i wdraża aplikację do klastra określonego w akcjach po kompilacji.  

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Usługa Jenkins na platformie Azure](/azure/Jenkins/)