---
title: Ciągła kompilacja i integrowanie aplikacji usługi Azure Service Fabric dla systemu Linux przy użyciu narzędzia Jenkins | Dokumentacja firmy Microsoft
description: Ciągła kompilacja i integrowanie aplikacji usługi Service Fabric systemu Linux przy użyciu narzędzia Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 3b1e6f769d5c65065d95ac96c4ab4ed10702e5cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038852"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Tworzenie i wdrażanie aplikacji systemu Linux przy użyciu narzędzia Jenkins
Jenkins to popularne narzędzie służące do przeprowadzania ciągłej integracji i ciągłego wdrażania aplikacji. Poniżej przedstawiono sposób kompilowania i wdrażania aplikacji usługi Azure Service Fabric przy użyciu narzędzia Jenkins.

## <a name="topic-overview"></a>Omówienie tematu
W tym artykule omówiono kilka możliwe sposoby konfigurowania środowiska narzędzia Jenkins, a także różne sposoby wdrażania aplikacji w klastrze usługi Service Fabric, po został skompilowany. Wykonaj następujące ogólne kroki, aby pomyślnie instalacji usługi Jenkins, ściąganie zmian z serwisu GitHub, skompilować aplikację i wdróż je do klastra:

1. Upewnij się, że instalujesz program [wymagania wstępne](#prerequisites).
1. Następnie postępuj zgodnie z instrukcjami w jednym z tych sekcji, aby skonfigurować usługi Jenkins:
   * [Konfigurowanie narzędzia Jenkins w ramach klastra usługi Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurowanie narzędzia Jenkins poza klastrem usługi Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster), lub
   * [Instalowanie wtyczki usługi Service Fabric w istniejącym środowisku usługi Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Po skonfigurowaniu serwera Jenkins, postępuj zgodnie z instrukcjami [tworzenie i konfigurowanie zadania narzędzia Jenkins](#create-and-configure-a-jenkins-job) skonfiguruj usługę GitHub do wyzwalacza usługi Jenkins podczas wprowadzania zmian do aplikacji i skonfigurować potok zadania narzędzia Jenkins przy użyciu kroku kompilacji do ściągania zmiany z repozytorium GitHub i skompilowaniu aplikacji usługi. 
1. Na koniec skonfiguruj krok po kompilacji zadania narzędzia Jenkins do wdrożenia aplikacji w klastrze usługi Service Fabric. Istnieją dwa sposoby konfigurowania serwera Jenkins, aby wdrożyć aplikację w klastrze:    
   * W przypadku środowisk deweloperskich i testowych, użyj [skonfigurować wdrożenie przy użyciu punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint). Jest to najprostsza metoda wdrażania do skonfigurowania.
   * W środowiskach produkcyjnych należy użyć [skonfigurować wdrożenie przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials). Firma Microsoft zaleca tej metody w środowiskach produkcyjnych, ponieważ przy użyciu poświadczeń platformy Azure można ograniczyć dostęp, który ma zadania narzędzia Jenkins z zasobami platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że usługi Git jest instalowana lokalnie. Można zainstalować odpowiednią wersję narzędzia Git z [strony pobierania narzędzia Git](https://git-scm.com/downloads) oparte na systemie operacyjnym. Jeśli jesteś nowym użytkownikiem narzędzia Git, Dowiedz się więcej o nim więcej z [dokumentacja usługi Git](https://git-scm.com/docs).
- W tym artykule wykorzystano *usługi Service Fabric Getting pracy — przykład* w witrynie GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) do tworzenia i wdrażania aplikacji. Można rozwidlenie tego repozytorium, aby kontynuować, lub, za pomocą wprowadzając pewne modyfikacje w instrukcji, użyj projektu usługi GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalowanie wtyczki usługi Service Fabric w istniejącym środowisku usługi Jenkins
W przypadku dodawania wtyczki usługi Service Fabric do istniejącego środowiska narzędzia Jenkins, potrzebne są następujące elementy:

- [Interfejsu wiersza polecenia usługi Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Pamiętaj zainstalować interfejs wiersza polecenia na poziomie systemu, a nie na poziomie użytkownika, więc Jenkins można uruchomić polecenia interfejsu wiersza polecenia. 
   >

- Wdrażanie aplikacji Java, należy zainstalować zarówno [Gradle i Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Wdrażanie aplikacji .NET Core 2.0, należy zainstalować [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po zainstalowaniu wymagań wstępnych w danym środowisku, można wyszukać wtyczki Azure usługi Service Fabric w witrynie marketplace usługi Jenkins i zainstaluj go.

Po zainstalowaniu dodatku plug-in, przejdź do sekcji [tworzenie i konfigurowanie zadania narzędzia Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins w ramach klastra usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. Poniższe sekcje pokazują, jak je skonfigurować wewnątrz klastra podczas korzystania z konta usługi Azure storage ma być zapisany stan wystąpienia kontenera.

### <a name="prerequisites"></a>Wymagania wstępne
- Mieć klaster usługi Service Fabric systemu Linux z zainstalowaną platformą Docker. Klastry usługi Service Fabric działających na platformie Azure już platforma Docker została zainstalowana. Jeśli korzystasz z klastra lokalnego (OneBox środowiska deweloperskiego), sprawdź, jeśli platforma Docker jest zainstalowana na komputerze przy użyciu `docker info` polecenia. Jeśli nie jest zainstalowany, należy go zainstalować przy użyciu następujących poleceń:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Upewnij się, że 8081 port jest określony jako niestandardowy punkt końcowy w klastrze. Jeśli używasz klastra lokalnego, upewnij się, że port 8081 jest otwarty na komputerze hosta oraz czy ma on publicznego adresu IP.
   >

### <a name="steps"></a>Kroki
1. Klonowanie aplikacji za pomocą następujących poleceń:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Utrwalanie stanu kontenera narzędzia Jenkins w udziale plików:
   1. Tworzenie konta usługi Azure storage w **tego samego regionu** jako klastra przy użyciu nazwy, takie jak `sfjenkinsstorage1`.
   1. Tworzenie **udziału plików** w obszarze magazynu konta o nazwie takiej jak `sfjenkins`.
   1. Kliknij pozycję **Connect** dla udziału plików i zanotuj wartości wyświetla w obszarze **nawiązywania połączenia z systemem Linux**, wartości powinny wyglądać podobnie do poniższego:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Do instalacji, cifs shares musisz mieć pakiet cifs utils instalowane w węzłach klastra.      
   >

1. Zaktualizuj wartości symboli zastępczych w `setupentrypoint.sh` skryptu ze szczegółowymi informacjami usługi azure storage w kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Zastąp `[REMOTE_FILE_SHARE_LOCATION]` wartością `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z danych wyjściowych programu connect w kroku 2 powyżej.
   * Zastąp `[FILE_SHARE_CONNECT_OPTIONS_STRING]` wartością `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2 powyżej.

1. **Zabezpieczanie klastra tylko:** 
   
   Aby skonfigurować wdrożenie aplikacji w zabezpieczonym klastrze z poziomu narzędzia Jenkins, certyfikat klastra musi być dostępny w ramach kontenera narzędzia Jenkins. W *ApplicationManifest.xml* plików w obszarze **ContainerHostPolicies** tag dodać to odwołanie certyfikatu i zaktualizuj wartość odcisku palca z tym certyfikatu klastra.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ponadto, Dodaj następujące wiersze w sekcji **ApplicationManifest** tag (root) w *ApplicationManifest.xml* pliku i zaktualizuj wartość odcisku palca przy użyciu tego certyfikatu klastra.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Łączenie z klastrem i instalowanie aplikacji kontenera.

   **Zabezpieczanie klastra**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Poprzednie polecenie pobiera certyfikat w formacie PEM. Jeśli certyfikat jest w formacie PFX, można użyć następującego polecenia, aby przekonwertować go. Jeśli Twojego pliku PFX nie jest chroniony hasłem, podaj **passin** jako parametr `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Niezabezpieczonym klastrem**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   To spowoduje zainstalowanie kontenera narzędzia Jenkins w klastrze i umożliwi monitorowanie za pomocą narzędzia Service Fabric Explorer.

   > [!NOTE]
   > Może upłynąć kilka minut, zanim obraz narzędzia Jenkins, które mają być pobrane w klastrze.
   >

1. W przeglądarce przejdź na adres `http://PublicIPorFQDN:8081`. Tam jest dostępna ścieżka do początkowego hasła administratora wymaganego do zalogowania się. 
1. Przyjrzyj się narzędzia Service Fabric Explorer, aby określić, na który węzeł kontenera narzędzia Jenkins jest uruchomiony. Secure Shell (SSH) Zaloguj się do tego węzła.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Uzyskaj identyfikator wystąpienia kontenera za pomocą polecenia `docker ps -a`.
1. Secure Shell (SSH) Zaloguj się do kontenera i wklej ścieżkę wyświetloną w portalu narzędzia Jenkins. Na przykład, jeśli w portalu wyświetlana jest ścieżka `PATH_TO_INITIAL_ADMIN_PASSWORD`, uruchom następujące polecenia:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stronie wprowadzenie do usługi Jenkins Wybierz wtyczki wybierz opcję instalacji, zaznacz **Brak** pole wyboru, a następnie kliknij przycisk Zainstaluj.
1. Utwórz użytkownika, lub zaznacz, aby kontynuować jako administrator.

Po skonfigurowaniu serwera Jenkins, przejdź do sekcji [tworzenie i konfigurowanie zadania narzędzia Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins poza klastrem usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach opisano sposób konfiguracji narzędzia poza klastrem.

### <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że platforma Docker jest zainstalowana na tym komputerze. Poniższe polecenia umożliwiają zainstalowanie platformy Docker z terminalu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Po uruchomieniu `docker info` w terminalu, dane wyjściowe powinny pokazywać, że usługa platformy Docker jest uruchomiona.

### <a name="steps"></a>Kroki
1. Ściągnij obraz kontenera narzędzia Jenkins usługi Service Fabric: `docker pull rapatchi/jenkins:latest`. Ten obraz jest dostarczany ze wstępnie zainstalowaną wtyczką narzędzia Jenkins usługi Service Fabric.
1. Uruchom obraz kontenera: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Uzyskaj identyfikator wystąpienia obrazu kontenera. Listę wszystkich kontenerów platformy Docker można wyświetlić za pomocą polecenia `docker ps –a`
1. Zaloguj się do portalu narzędzia Jenkins wykonując następujące kroki:

   1. Zaloguj się do powłoki narzędzia Jenkins z poziomu hosta. Użyj pierwsze cztery cyfry identyfikator kontenera. Na przykład, jeśli identyfikator kontenera to `2d24a73b5964`, użyj `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Z poziomu powłoki narzędzia Jenkins Pobierz hasło administratora dla wystąpienia kontenera:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Aby zalogować się do pulpitu nawigacyjnego narzędzia Jenkins, otwórz następujący adres URL w przeglądarce sieci web: `http://<HOST-IP>:8080`. Umożliwia odblokowywanie usługi Jenkins hasła z poprzedniego kroku.
   1. (opcjonalnie) Po zalogowaniu się po raz pierwszy możesz utworzyć własne konto użytkownika i używać go dla następujących kroków lub można nadal korzystać z konta administratora. Utworzenie użytkownika należy kontynuować z tego użytkownika.
1. Skonfiguruj usługę GitHub do pracy z narzędziem Jenkins wykonując kroki opisane w [Generowanie nowego klucza SSH i dodanie go do agenta SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Korzystając z instrukcji udostępnionych przez usługę GitHub, wygeneruj klucz SSH i dodaj go do konta usługi GitHub, które hostuje repozytorium.
   * Uruchom polecenia wymienione pod powyższym linkiem w powłoce platformy Docker narzędzia Jenkins (a nie na hoście).
   * Aby zalogować się do powłoki narzędzia Jenkins z poziomu hosta, użyj następującego polecenia:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Upewnij się, że klaster lub maszyna hostujące obraz kontenera narzędzia Jenkins ma publicznego adresu IP. Dzięki temu wystąpienie narzędzia Jenkins może otrzymywać powiadomienia z usługi GitHub.

Po skonfigurowaniu serwera Jenkins, przejdź do następnej sekcji [tworzenie i konfigurowanie zadania narzędzia Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Tworzenie i konfigurowanie zadania narzędzia Jenkins

Kroki opisane w tej sekcji opisano skonfigurować zadania narzędzia Jenkins, odpowiadanie na zmiany w repozytorium GitHub, pobrać zmiany i ich tworzenia. Na końcu tej sekcji są kierowane do ostatnie kroki konfigurowania zadania wdrażania aplikacji w zależności od tego, czy jest wdrażany w środowisku projektowania i testowania lub w środowisku produkcyjnym. 

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij **nowy element**.
1. Wprowadź nazwę elementu (na przykład **MyJob**). Wybierz pozycję **free-style project** (projekt w stylu dowolnym) i kliknij przycisk **OK**.
1. Zostanie otwarta strona konfiguracji zadania. (Aby uzyskać dostęp do konfiguracji z poziomu pulpitu nawigacyjnego narzędzia Jenkins, kliknij zadanie, a następnie kliknij **Konfiguruj**).

1. Na **ogólne** kartę, zaznacz pole **projektu GitHub**, a następnie określ adres URL projektu GitHub. Pod tym adresem URL jest hostowana aplikacja Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na **zarządzania kodem źródłowym** zaznacz **Git**. Określ adres URL repozytorium hostującego aplikację Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Można również określić gałąź do skompilowania (na przykład `/master`).
1. Konfigurowanie usługi *GitHub* repozytorium, aby komunikować się z narzędziem Jenkins:

   a. Na stronie repozytorium GitHub, przejdź do **ustawienia** > **integracje i usługi**.

   b. Wybierz pozycję **Add Service** (Dodaj usługę), wpisz ciąg **Jenkins** i wybierz pozycję **Jenkins-Github plugin** (Wtyczka Jenkins-Github).

   c. Wprowadź adres URL elementu webhook narzędzia Jenkins (domyślnie adres URL powinien być następujący: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kliknij pozycję **add/update service** (dodaj/aktualizuj usługę).

   d. Do wystąpienia narzędzia Jenkins zostanie wysłane zdarzenie testowe. W usłudze GitHub powinien zostać wyświetlony zielony znacznik wyboru obok elementu webhook i projekt zostanie skompilowany.

1. Na **kompilowanie wyzwalaczy** w usłudze Jenkins, a następnie wybierz odpowiednią opcję, która kompilacji. Na przykład chcesz wyzwolić kompilację, zawsze wtedy, gdy się stanie, wypychania do repozytorium, więc wybierz **wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITScm**. Wcześniej ta opcja miała nazwę **Build when a change is pushed to GitHub** (Kompiluj po wypchnięciu zmiany do usługi GitHub).
1. Na **kompilacji** karcie, wykonaj jedną z następujących czynności, w zależności od tego, czy tworzysz aplikację Java lub .NET Core:

   * **W przypadku aplikacji Java:** Z **Dodaj krok kompilacji** listę rozwijaną, wybierz opcję **wywołaj skrypt Gradle**. Kliknij przycisk **zaawansowane**. W menu Zaawansowane, określ ścieżkę do **głównego kompilacji skryptu** dla aplikacji. Spowoduje to wybranie elementu build.gradle z określonej ścieżki i wykonanie odpowiedniego działania. Aby uzyskać [aplikacji ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), to: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric][build-step]

   * **Dla aplikacji .NET Core:** Z **Dodaj krok kompilacji** listę rozwijaną, wybierz opcję **wykonaj powłokę**. W wyświetlonym oknie polecenia katalogu najpierw musi zostać zmieniony na ścieżkę, w którym znajduje się plik build.sh. Po zmianie katalogu skrypcie build.sh może zostać uruchomiona i będzie skompilować aplikację.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Poniższy zrzut ekranu przedstawia przykład poleceń, które są używane do tworzenia [usługa licznika](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) próbki o nazwie CounterServiceApplication zadania serwera Jenkins.

      ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric][build-step-dotnet]

1. Aby skonfigurować usługi Jenkins, aby wdrożyć aplikację w klastrze usługi Service Fabric w akcjach wykonywanych po kompilacji, należy lokalizację certyfikatu tego klastra w kontenerze usługi Jenkins. Wybierz jedną z następujących czynności, w zależności od tego, czy kontenera narzędzia Jenkins działa wewnątrz lub poza klastrem, a następnie zanotuj lokalizację certyfikatu klastra:

   * **Dla serwera Jenkins, działających w klastrze:** Ścieżkę do certyfikatu można znaleźć, wyświetlania wartości *Certificates_JenkinsOnSF_Code_MyCert_PEM* zmienną środowiskową z w ramach kontenera.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Dla serwera Jenkins, działającego poza klastrem:** Wykonaj następujące kroki, aby skopiować certyfikat klastra do kontenera:
     1. Twój certyfikat musi być w formacie PEM. Jeśli nie masz pliku PEM, można utworzyć jeden z pliku PFX certyfikatu. Jeśli Twojego pliku PFX nie jest chroniony hasłem, uruchom następujące polecenie z poziomu hosta:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Jeśli plik PFX jest chroniony hasłem, zawierać hasła w `-passin` parametru. Na przykład:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Aby uzyskać identyfikator kontenera kontenera narzędzia Jenkins, uruchom `docker ps` z hosta.
     1. Skopiuj plik PEM do kontenera za pomocą następującego polecenia Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Jesteś prawie gotowe! Nie zamykaj zadania narzędzia Jenkins. Pozostałe zadania jest skonfigurować kroki mające miejsce po kompilacji, aby wdrożyć aplikację w klastrze usługi Service Fabric:

* Aby wdrożyć w środowisku deweloperskim lub testowym, wykonaj kroki opisane w [skonfigurować wdrożenie przy użyciu punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint).
* Aby wdrożyć w środowisku produkcyjnym, należy wykonać czynności opisane w [skonfigurować wdrożenie przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurowanie wdrożenia przy użyciu punktu końcowego zarządzania klastrem
Dla środowisk deweloperskich i testowych punkt końcowy zarządzania klastrem służy do wdrażania aplikacji. Konfigurowanie akcji wykonywanych po kompilacji przy użyciu punktu końcowego zarządzania klastrem, aby wdrożyć aplikację wymaga minimalnej konfiguracji. Jeśli wdrażasz do środowiska produkcyjnego, przejdź do sekcji [skonfigurować wdrożenie przy użyciu poświadczeń platformy Azure](#configure-deployment-using-azure-credentials) konfigurowania jednostki usługi Azure Active Directory do wykorzystania podczas wdrażania.    

1. W ramach zadania usługi Jenkins kliknij **akcjach wykonywanych po kompilacji** kartę. 
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **konfiguracji klastra usługi Service Fabric**, wybierz opcję **wprowadź punkt końcowy usługi Service Fabric zarządzania** przycisku radiowego.
1. Aby uzyskać **hosta zarządzania**, wprowadź punkt końcowy połączenia klastra; na przykład `{your-cluster}.eastus.cloudapp.azure.com`.
1. Dla **klucz klienta** i **certyfikat klienta**, wpisz lokalizację pliku PEM w kontenerze systemu Jenkins; na przykład `/var/jenkins_home/clustercert.pem`. (Skopiowany lokalizację certyfikatu ostatni krok [tworzenie i konfigurowanie zadania narzędzia Jenkins](#create-and-configure-a-jenkins-job).)
1. W obszarze **konfiguracji aplikacji**, skonfiguruj **Nazwa aplikacji**, **typ aplikacji**oraz (względny) **ścieżkę doManifestaplikacji** pola.

   ![Akcji wykonywanych po kompilacji usługi Jenkins sieci szkieletowej usług skonfigurować punkt końcowy zarządzania](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Kliknij przycisk **Zweryfikuj konfigurację**. W pomyślnym zakończeniu weryfikacji kliknij **Zapisz**. Potok zadania serwera Jenkins jest teraz w pełni skonfigurowane. Przejdź do sekcji [następne kroki](#next-steps) do przetestowania wdrożenia.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurowanie wdrożenia przy użyciu poświadczeń platformy Azure
W środowiskach produkcyjnych zdecydowanie zalecane jest skonfigurowanie poświadczeń platformy Azure, aby wdrożyć aplikację. W tej sekcji przedstawiono sposób konfigurowania jednostki usługi Azure Active Directory do użycia, aby wdrożyć aplikację w akcji wykonywanych po kompilacji. Nazwy główne usług można przypisać do ról w katalogu, aby ograniczyć uprawnienia zadania narzędzia Jenkins. 

Dla środowisk deweloperskich i testowych można skonfigurować poświadczeń platformy Azure lub punkt końcowy zarządzania klastrem, aby wdrożyć aplikację. Aby uzyskać szczegółowe informacje o sposobie konfigurowania punktu końcowego zarządzania klastrem, zobacz [skonfigurować wdrożenie przy użyciu punktu końcowego zarządzania klastrem](#configure-deployment-using-cluster-management-endpoint).   

1. Aby utworzyć jednostkę usługi Azure Active Directory i przypisz mu uprawnienia w ramach subskrypcji platformy Azure, wykonaj kroki opisane w [korzystanie z portalu do tworzenia aplikacji i usługi nazwy głównej usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Należy zwrócić uwagę na następujące kwestie:

   * Podczas czynności opisane w temacie, upewnij się, skopiuj i Zapisz następujące wartości: *Identyfikator aplikacji*, *klucz aplikacji*, *identyfikator katalogu (identyfikator dzierżawy)* , i *identyfikator subskrypcji*. Będą one potrzebne do konfigurowania poświadczeń platformy Azure w usłudze Jenkins.
   * Jeśli nie masz [wymagane uprawnienia](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) w Twoim katalogu, musisz poprosić administratora o udzielenie uprawnień albo utworzyć jednostkę usługi lub musisz skonfigurować punkt końcowy zarządzania usługi klaster w **akcjach wykonywanych po kompilacji** dla zadania w usłudze Jenkins.
   * W [utworzyć aplikację usługi Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) sekcji, możesz wprowadzić dowolny sformułowany adres URL dla **adres URL logowania**.
   * W [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) sekcji można przypisać aplikacji *czytnika* roli w grupie zasobów klastra.

1. Zadania narzędzia Jenkins w kliknij **akcjach wykonywanych po kompilacji** kartę.
1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
1. W obszarze **konfiguracji klastra usługi Service Fabric**, wybierz opcję **wybierz klaster usługi Service Fabric** przycisku radiowego. Kliknij przycisk **Dodaj** obok **Azure Credentials**. Kliknij przycisk **Jenkins** wybranie dostawcy poświadczeń usługi Jenkins.
1. W polu Dostawca poświadczeń usługi Jenkins wybierz **Microsoft Azure Service Principal** z **rodzaj** listy rozwijanej.
1. Użyj wartości zapisane przez ustawienie zapasowej jednostki usługi w kroku 1, aby ustawić następujące pola:

   * **Identyfikator klienta**: *Identyfikator aplikacji*
   * **Klucz tajny klienta**: *Klucz aplikacji*
   * **Identyfikator dzierżawy**: *Identyfikator katalogu*
   * **Identyfikator subskrypcji**: *Subscription ID (Identyfikator subskrypcji)*
1. Wpisz opisową nazwę **identyfikator** umożliwia Wybierz poświadczenie, Jenkins i zwięzłe **opis**. Następnie kliknij przycisk **Sprawdź nazwy głównej usługi**. Jeśli weryfikacja zakończy się pomyślnie, kliknij przycisk **Dodaj**.

   ![Jenkins usługi Service Fabric, wprowadź poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Ponownie w obszarze **konfiguracji klastra usługi Service Fabric**, upewnij się, że została wybrana swoje nowe poświadczenie **Azure Credentials**. 
1. Z **grupy zasobów** listę rozwijaną, wybierz grupę zasobów klastra, którą chcesz wdrożyć aplikację.
1. Z **usługi Service Fabric** listę rozwijaną, wybierz klaster, który chcesz wdrożyć aplikację.
1. Aby uzyskać **klucz klienta** i **certyfikat klienta**, wpisz lokalizację pliku PEM w kontenerze usługi Jenkins. Na przykład: `/var/jenkins_home/clustercert.pem`. 
1. W obszarze **konfiguracji aplikacji**, skonfiguruj **Nazwa aplikacji**, **typ aplikacji**oraz (względny) **ścieżkę doManifestaplikacji** pola.
    ![Akcji wykonywanych po kompilacji usługi Jenkins sieci szkieletowej usług skonfigurować poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Kliknij przycisk **Zweryfikuj konfigurację**. W pomyślnym zakończeniu weryfikacji kliknij **Zapisz**. Potok zadania serwera Jenkins jest teraz w pełni skonfigurowane. Przejdź do [następne kroki](#next-steps) do przetestowania wdrożenia.

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Kolejne kroki
Usługa GitHub i narzędzie Jenkins są teraz skonfigurowane. Rozważ dokonanie jakiejś przykładowej zmiany w `reliable-services-actor-sample/Actors/ActorCounter` projektu w Twoim rozwidleniu repozytorium, https://github.com/Azure-Samples/service-fabric-java-getting-started. Wypchnij zmiany do zdalnego `master` gałęzi (lub dowolnej innej gałęzi, który został skonfigurowany do pracy z). Spowoduje to wyzwolenie skonfigurowanego zadania narzędzia Jenkins `MyJob`. Jej pobiera zmiany z repozytorium GitHub, skompiluje je i wdrażania aplikacji do klastra, który określiłeś w akcjach wykonywanych po kompilacji.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

