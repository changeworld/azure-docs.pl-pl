---
title: Ciągłe kompilacji i integracji aplikacji usługi Azure Service Fabric Linux przy użyciu Wpięć | Dokumentacja firmy Microsoft
description: Ciągłe kompilacji i integracji aplikacji usługi sieci szkieletowej w systemie Linux przy użyciu Wpięć
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 047b3d00da4f192febeeab79c9c87b67a8a0489b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207965"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Użyj Wpięć do tworzenia i wdrażania aplikacji systemu Linux
Jenkins to popularne narzędzie służące do przeprowadzania ciągłej integracji i ciągłego wdrażania aplikacji. Poniżej przedstawiono sposób kompilowania i wdrażania aplikacji usługi Azure Service Fabric przy użyciu narzędzia Jenkins.

## <a name="topic-overview"></a>Omówienie tematu
W tym artykule opisano kilka możliwości konfigurowania środowiska Wpięć, a także różne sposoby wdrażania aplikacji do klastra usługi sieć szkieletowa po został skompilowany. Wykonaj następujące ogólne kroki, aby pomyślnie skonfigurować Wpięć, kopiować zmiany z usługi GitHub skompilować aplikację i wdrożyć go do klastra:

1. Upewnij się, że instalujesz program [wymagania wstępne](#prerequisites).
2. Następnie wykonaj kroki opisane w jednym z tych sekcji, aby skonfigurować Wpięć:
   * [Konfigurowanie Wpięć wewnątrz klastra sieci szkieletowej usług](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Konfigurowanie Wpięć poza klaster sieci szkieletowej usług](#set-up-jenkins-outside-a-service-fabric-cluster), lub
   * [Instalowanie wtyczki sieci szkieletowej usług w istniejącym środowisku Wpięć](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
3. Po skonfigurowaniu Wpięć, postępuj zgodnie z instrukcjami [Utwórz i skonfiguruj zadanie Wpięć](#create-and-configure-a-jenkins-job) do konfigurowania GitHub Wpięć podczas wprowadzania zmian do aplikacji i konfigurowania Wpięć planowaną zadania za pomocą kroku kompilacji do ściągnięcia zmiany z usługi GitHub i skompilować aplikację. 
4. Na koniec skonfiguruj etap Wpięć zadania mające miejsce po kompilacji do wdrożenia aplikacji do klastra usługi sieć szkieletowa usług. Istnieją dwa sposoby konfigurowania Wpięć wdrażania aplikacji do klastra:    
   * W przypadku środowisk projektowania i testowania, użyj [skonfigurować wdrożenie przy użyciu punktu końcowego klastra zarządzania](#configure-deployment-using-cluster-management-endpoint). Jest to najprostsza metoda wdrażania do skonfigurowania.
   * W środowiskach produkcyjnych należy użyć [skonfigurować wdrożenie przy użyciu poświadczeń usługi Azure](#configure-deployment-using-azure-credentials). Firma Microsoft zaleca tej metody dla środowisk produkcyjnych, ponieważ przy użyciu poświadczeń platformy Azure można ograniczyć zadanie Wpięć ma dostęp do zasobów platformy Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że Git jest instalowany lokalnie. Można zainstalować odpowiednią wersję Git z [Git pobiera strony](https://git-scm.com/downloads) oparte na systemie operacyjnym. Jeśli jesteś nowym użytkownikiem Git, więcej informacji na temat z [dokumentacji Git](https://git-scm.com/docs).
- W tym artykule wykorzystano *pobieranie rozpoczęto próbki z sieci szkieletowej usług* w witrynie GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) do tworzenia i wdrażania aplikacji. Można rozwidlenie tego repozytorium, aby z niego skorzystać, lub, niektóre zmiany w instrukcji za pomocą projektu usługi GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalowanie wtyczki sieci szkieletowej usług w istniejącym środowisku Wpięć
Jeśli dodajesz wtyczki sieci szkieletowej usług do istniejącego środowiska Wpięć, potrzebne są następujące elementy:

- [Interfejsu wiersza polecenia usługi sieć szkieletowa](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Pamiętaj zainstalować interfejs wiersza polecenia na poziomie systemu, a nie na poziomie użytkownika, więc Wpięć można uruchomić polecenia interfejsu wiersza polecenia. 
   >

- Aby wdrożyć aplikacji Java, należy zainstalować zarówno [Gradle i otwórz 8.0 JDK](service-fabric-get-started-linux.md#set-up-java-development). 
- Aby wdrożyć aplikacje .NET Core 2.0, należy zainstalować [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po zainstalowaniu wymagań wstępnych potrzebnymi w danym środowisku, można wyszukać wtyczkę Azure Usługa sieci szkieletowej w witrynie marketplace Wpięć i go zainstalować.

Po zainstalowaniu dodatku plug-in, przejdź do [Utwórz i skonfiguruj zadanie Wpięć](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins w ramach klastra usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. Poniższe sekcje pokazują, jak je skonfigurować w klastrze podczas korzystania z konta magazynu platformy Azure można zapisać stanu wystąpienia kontenera.

### <a name="prerequisites"></a>Wymagania wstępne
- Mają klastra usługi sieć szkieletowa Linux Docker zainstalowane. Klastrów sieci szkieletowej usług działających na platformie Azure już mieć zainstalowane Docker. Jeśli korzystasz z lokalnego klastra (środowiska deweloperskiego OneBox), sprawdź, czy Docker jest zainstalowany na komputerze z `docker info` polecenia. Jeśli nie jest zainstalowany, należy go zainstalować za pomocą następujących poleceń:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Upewnij się, że 8081 port jest określony jako punktu końcowego niestandardowych w klastrze. Jeśli korzystasz z lokalnym klastrem, upewnij się, że port 8081 jest otwarty na komputerze hosta i czy jest adresem IP publicznych.
   >

### <a name="steps"></a>Kroki
1. Klonowanie aplikacji, za pomocą następujących poleceń:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. Utrwalanie stanu kontenera Wpięć w udziale plików:
   1. Tworzenie konta magazynu Azure w **tego samego regionu** co klaster o nazwie, takich jak `sfjenkinsstorage1`.
   2. Utwórz **udziału plików** w magazynie konta o nazwie takich jak `sfjenkins`.
   3. Polecenie **Connect** dla udziału plików i zanotuj wartości on wyświetla w obszarze **łączących się z systemem Linux**, wartość powinna wyglądać podobnie do poniższego:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Do instalacji cifs udziałów musisz z pakietem witryny cifs zainstalowanych w węzłach klastra.      
   >

4. Zaktualizuj symbole zastępcze w `setupentrypoint.sh` skryptu szczegóły usługi azure storage w kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Zastąp `[REMOTE_FILE_SHARE_LOCATION]` z wartością `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z danych wyjściowych Połącz w kroku 2. powyżej.
   * Zastąp `[FILE_SHARE_CONNECT_OPTIONS_STRING]` z wartością `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2. powyżej.

5. **Tylko zabezpieczone klastra:** 
   
   Umożliwia skonfigurowanie wdrażania aplikacji w klastrze bezpiecznego z Wpięć, musi być dostępny w kontenerze Wpięć certyfikatu klastra. W *ApplicationManifest.xml* pliku, w obszarze **ContainerHostPolicies** tag dodać to odwołanie certyfikatu i zaktualizuj wartość odcisku palca z z certyfikatu klastra.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Ponadto Dodaj następujące wiersze w **ApplicationManifest** tag (root) w *ApplicationManifest.xml* plików i zaktualizuj wartość odcisku palca z z certyfikatu klastra.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. Połącz się z klastrem i instalowanie aplikacji do kontenera.

   **Zabezpieczenia klastra**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Poprzednie polecenie pobiera certyfikat w formacie PEM. Jeśli certyfikat jest w formacie PFX, służy następujące polecenie do konwertowania. Jeśli plik PFX nie jest chroniony hasłem, określ **passin** parametr jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Niezabezpieczone klastra**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   To spowoduje zainstalowanie kontenera narzędzia Jenkins w klastrze i umożliwi monitorowanie za pomocą narzędzia Service Fabric Explorer.

   > [!NOTE]
   > Może upłynąć kilka minut dla obrazu Wpięć do pobrania w klastrze.
   >

7. W przeglądarce przejdź na adres `http://PublicIPorFQDN:8081`. Tam jest dostępna ścieżka do początkowego hasła administratora wymaganego do zalogowania się. 
2. Spójrz na Service Fabric Explorer ustalenie, na który węzeł kontenera Wpięć jest uruchomiony. Secure Shell (SSH), logowanie do tego węzła.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. Uzyskaj identyfikator wystąpienia kontenera za pomocą polecenia `docker ps -a`.
4. Secure Shell (SSH), logowanie do kontenera i Wklej ścieżki, które były wyświetlane w portalu Wpięć. Na przykład, jeśli w portalu zawiera ścieżkę `PATH_TO_INITIAL_ADMIN_PASSWORD`, uruchom następujące polecenia:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. Na stronie wprowadzenie Wpięć wybierz wybierz wtyczki do zainstalowania opcji, zaznacz **Brak** pole wyboru, a następnie kliknij przycisk Zainstaluj.
6. Utwórz użytkownika, lub zaznacz, aby kontynuować konta administratora.

Po skonfigurowaniu Wpięć przejść od razu do [Utwórz i skonfiguruj zadanie Wpięć](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Konfigurowanie narzędzia Jenkins poza klastrem usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. W poniższych sekcjach opisano sposób konfiguracji narzędzia poza klastrem.

### <a name="prerequisites"></a>Wymagania wstępne
- Upewnij się, że Docker jest zainstalowany na tym komputerze. Poniższe polecenia umożliwiają zainstalowanie platformy Docker z terminalu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Po uruchomieniu `docker info` w terminalu, dane wyjściowe powinny wskazywać, że działa usługa Docker.

### <a name="steps"></a>Kroki
1. Ściągnij obraz kontenera narzędzia Jenkins usługi Service Fabric: `docker pull rapatchi/jenkins:latest`. Ten obraz jest dostarczany ze wstępnie zainstalowaną wtyczką narzędzia Jenkins usługi Service Fabric.
2. Uruchom obraz kontenera: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. Uzyskaj identyfikator wystąpienia obrazu kontenera. Listę wszystkich kontenerów platformy Docker można wyświetlić za pomocą polecenia `docker ps –a`
4. Zaloguj się do portalu Wpięć następujące czynności:

   1. Zaloguj się do powłoki Wpięć z hosta. Użyj pierwsze cztery cyfry identyfikator kontenera. Na przykład, jeśli identyfikator kontenera jest `2d24a73b5964`, użyj `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. Z poziomu powłoki Wpięć uzyskać hasło administratora dla swojego wystąpienia kontenera:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. Aby zalogować się do pulpitu nawigacyjnego Wpięć, otwórz następujący adres URL w przeglądarce sieci web: `http://<HOST-IP>:8080`. Umożliwia odblokowanie Wpięć hasła z poprzedniego kroku.
   4. (opcjonalnie) Po zalogowaniu się po raz pierwszy, może utworzyć konta użytkownika i użyć jej dla następujących kroków, lub można nadal korzystać z konta administratora. Utwórz użytkownika, musisz kontynuować ten użytkownik.
5. Konfigurowanie usługi GitHub do pracy z Wpięć, wykonując kroki opisane w [generowania klucza SSH i dodanie go do agenta SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Korzystając z instrukcji udostępnionych przez usługę GitHub, wygeneruj klucz SSH i dodaj go do konta usługi GitHub, które hostuje repozytorium.
   * Uruchom polecenia wymienione pod powyższym linkiem w powłoce platformy Docker narzędzia Jenkins (a nie na hoście).
   * Aby zalogować się do powłoki narzędzia Jenkins z poziomu hosta, użyj następującego polecenia:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Upewnij się, że klastra lub komputera, gdzie znajduje się obraz kontenera Wpięć ma adres IP publicznych. Dzięki temu wystąpienie narzędzia Jenkins może otrzymywać powiadomienia z usługi GitHub.

Po skonfigurowaniu Wpięć przejść do następnej sekcji [Utwórz i skonfiguruj zadanie Wpięć](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Tworzenie i konfigurowanie zadania narzędzia Jenkins

Kroki opisane w tej sekcji opisano sposób konfiguracji zadania Wpięć odpowiadanie na zmiany w repozytorium GitHub, pobrać zmiany i ich tworzenia. Na końcu tej sekcji są kierowane do ostatnie kroki konfigurowania zadania wdrażania aplikacji w oparciu jest wdrażany w środowisku programowania i testowania lub w środowisku produkcyjnym. 

1. Na pulpicie nawigacyjnym Wpięć kliknij **nowy element**.
2. Wprowadź nazwę elementu (na przykład **MyJob**). Wybierz pozycję **free-style project** (projekt w stylu dowolnym) i kliknij przycisk **OK**.
3. Zostanie otwarta strona konfiguracji zadania. (Aby uzyskać konfigurację z Wpięć pulpitu nawigacyjnego, kliknij zadanie, a następnie kliknij **Konfiguruj**).

4. Na **ogólne** karcie, pole wyboru dla **projektu GitHub**, i określ adres URL projektu usługi GitHub. Pod tym adresem URL jest hostowana aplikacja Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

5. Na **zarządzania kodem źródłowym** wybierz opcję **Git**. Określ adres URL repozytorium hostującego aplikację Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Można również określić, która gałąź do kompilacji (na przykład `/master`).
6. Konfigurowanie sieci *GitHub* repozytorium, aby komunikował się z Wpięć:

   a. Na stronie repozytorium GitHub, przejdź do **ustawienia** > **integracji i usług**.

   b. Wybierz pozycję **Add Service** (Dodaj usługę), wpisz ciąg **Jenkins** i wybierz pozycję **Jenkins-Github plugin** (Wtyczka Jenkins-Github).

   c. Wprowadź adres URL elementu webhook narzędzia Jenkins (domyślnie adres URL powinien być następujący: `http://<PublicIPorFQDN>:8081/github-webhook/`). Kliknij pozycję **add/update service** (dodaj/aktualizuj usługę).

   d. Do wystąpienia narzędzia Jenkins zostanie wysłane zdarzenie testowe. W usłudze GitHub powinien zostać wyświetlony zielony znacznik wyboru obok elementu webhook i projekt zostanie skompilowany.

7. Na **kompilacji wyzwalaczy** w Wpięć, a następnie wybierz opcję kompilacji, które. Na przykład chcesz wyzwolić kompilację, gdy wypychania do repozytorium się stanie, dlatego wybierz **wyzwalacza haku GitHub dla sondowania GITScm**. Wcześniej ta opcja miała nazwę **Build when a change is pushed to GitHub** (Kompiluj po wypchnięciu zmiany do usługi GitHub).
8. Na **kompilacji** wykonaj jedną z następujących czynności w zależności od tego, czy tworzysz aplikację Java lub .NET Core:

   * **W przypadku aplikacji Java:** z **kroku kompilacji Dodaj** listy rozwijanej, wybierz pozycję **wywołanie skryptu narzędzia Gradle**. Kliknij przycisk **zaawansowane**. W menu Zaawansowane, określ ścieżkę do **głównego kompilacji skryptu** aplikacji. Spowoduje to wybranie elementu build.gradle z określonej ścieżki i wykonanie odpowiedniego działania. Aby uzyskać [aplikacji ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), to: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric][build-step]

   * **Aplikacji programu .NET Core:** z **kroku kompilacji Dodaj** listy rozwijanej, wybierz pozycję **wykonywania powłoki**. W wyświetlonym oknie polecenia katalogu najpierw musi zostać zmienione na ścieżkę, w którym znajduje się plik build.sh. Po zmianie katalogu build.sh skrypt może działać i zostanie utworzenie aplikacji.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Poniższy zrzut ekranu przedstawia przykład poleceń, które są używane do tworzenia [usługi licznika](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) Przykładowa nazwa zadania Wpięć CounterServiceApplication.

      ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric][build-step-dotnet]

9. Aby skonfigurować Wpięć do wdrożenia aplikacji do klastra usługi sieć szkieletowa w działaniach mające miejsce po kompilacji, należy lokalizację certyfikatu w tym klastrze w kontenerze z Wpięć. Wybierz jedną z następujących czynności w zależności od tego, czy kontener z Wpięć działa wewnątrz lub na zewnątrz klastra i zanotuj lokalizację certyfikatu klastra:

   * **Dla Wpięć uruchomionych w klastrze:** ścieżkę do certyfikatu można znaleźć, wyświetlania wartości *Certificates_JenkinsOnSF_Code_MyCert_PEM* zmiennej środowiskowej od w kontenerze.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Dla Wpięć działających poza klastrem:** wykonaj następujące kroki, aby skopiować certyfikat klastra do kontenera sieci:
      1. Certyfikat musi być w formacie PEM. Jeśli nie masz pliku PEM, można utworzyć jedną z pliku PFX certyfikatu. Jeśli plik PFX nie jest chroniony hasłem, uruchom następujące polecenie z hosta:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Jeśli plik PFX jest chroniony hasłem, zawierać hasło w `-passin` parametru. Na przykład:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. Aby uzyskać identyfikator kontenera dla kontenera z Wpięć, uruchom `docker ps` z hosta.
      3. Skopiuj plik PEM z kontenerem przy użyciu następującego polecenia Docker:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Jesteś prawie gotowe! Nie zamykaj Wpięć zadania. Pozostałe zadania jest skonfigurowanie kroki mające miejsce po kompilacji do wdrożenia aplikacji do klastra usługi sieć szkieletowa usług:

* Aby wdrożyć środowisko rozwoju lub testowania, postępuj zgodnie z instrukcjami [skonfigurować wdrożenie przy użyciu punktu końcowego klastra zarządzania](#configure-deployment-using-cluster-management-endpoint).
* Aby wdrożyć w środowisku produkcyjnym, postępuj zgodnie z instrukcjami [skonfigurować wdrożenie przy użyciu poświadczeń usługi Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurowanie wdrożenia przy użyciu punktu końcowego klastra zarządzania
Dla środowisk projektowania i testowania punkt końcowy zarządzania klastra służy do wdrażania aplikacji. Konfigurowanie akcji po kompilacji z punktem końcowym zarządzania klastra wdrażania aplikacji wymaga minimalnej liczbie konfiguracji. Jeśli jest wdrażany w środowisku produkcyjnym, przejdź do [skonfigurować wdrożenie przy użyciu poświadczeń usługi Azure](#configure-deployment-using-azure-credentials) Aby skonfigurować nazwę główną usługi Azure Active Directory do wykorzystania podczas wdrażania.    

1. W zadaniu Wpięć kliknij **działania mające miejsce po kompilacji** kartę. 
2. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
3. W obszarze **konfiguracji klastra usługi sieć szkieletowa**, wybierz pozycję **wypełnienia punkt końcowy usługi sieci szkieletowej zarządzania** przycisk radiowy.
4. Dla **hosta zarządzania**, wprowadź punktu końcowego połączenia dla sieci klastra, na przykład `{your-cluster}.eastus.cloudapp.azure.com`.
5. Aby uzyskać **klucz klienta** i **certyfikatu klienta**, wprowadź lokalizację pliku PEM w kontenerze z Wpięć; na przykład `/var/jenkins_home/clustercert.pem`. (Skopiować ostatni krok lokalizację certyfikatu [Utwórz i skonfiguruj zadanie Wpięć](#create-and-configure-a-jenkins-job).)
6. W obszarze **konfiguracji aplikacji**, skonfiguruj **Nazwa aplikacji**, **typu aplikacji**i (względnej) **ścieżkę doManifestaplikacji** pól.

   ![Usługa sieci szkieletowej Wpięć Postkompilacyjnego akcji Skonfiguruj punkt końcowy zarządzania](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. Kliknij przycisk **Sprawdź konfigurację**. Pomyślnie weryfikację, kliknij przycisk **zapisać**. Planowaną zadania Wpięć teraz jest w pełni skonfigurowane. Przejdź do [następne kroki](#next-steps) do przetestowania wdrożenia.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurowanie wdrożenia przy użyciu poświadczeń platformy Azure
W środowiskach produkcyjnych zdecydowanie zalecane jest skonfigurowanie Azure poświadczeń do wdrożenia aplikacji. W tej sekcji przedstawiono sposób konfigurowania nazwy głównej usługi Azure Active Directory na potrzeby wdrażania aplikacji w akcji po kompilacji. Nazwy główne usług można przypisać do ról w katalogu, aby ograniczyć uprawnienia Wpięć zadania. 

Dla środowisk projektowania i testowania można skonfigurować poświadczenia platformy Azure lub punkt końcowy zarządzania klastra do wdrożenia aplikacji. Aby uzyskać więcej informacji o sposobie konfigurowania punktu końcowego zarządzania klastrem, zobacz [skonfigurować wdrożenie przy użyciu punktu końcowego klastra zarządzania](#configure-deployment-using-cluster-management-endpoint).   

1. Aby zapisać nazwy głównej usługi Azure Active Directory i przypisz mu uprawnienia w Twojej subskrypcji platformy Azure, postępuj zgodnie z instrukcjami [utworzyć podmiot zabezpieczeń aplikacji i usług Azure Active Directory za pomocą portalu](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Należy zwrócić uwagę na następujące kwestie:

   * Podczas czynności opisane w temacie, upewnij się, skopiuj i Zapisz następujące wartości: *identyfikator aplikacji*, *klucz aplikacji*, *identyfikator katalogu (identyfikator dzierżawcy)* i *Identyfikator subskrypcji*. Należy je skonfigurować poświadczenia Azure w Wpięć.
   * Jeśli nie masz [wymagane uprawnienia](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) na katalogu, należy poprosić administratora o udzielenie uprawnień albo zapisać nazwy głównej usługi dla Ciebie lub musisz skonfigurować punkt końcowy zarządzania dla programu klaster w **działania mające miejsce po kompilacji** dla zadania w Wpięć.
   * W [tworzy aplikację usługi Azure Active Directory](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) sekcji, można wprowadzić dowolny poprawnie sformułowany adres URL dla **adres URL logowania**.
   * W [przypisywanie aplikacji do roli](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) sekcji, możesz przypisać aplikację *czytnika* roli w grupie zasobów klastra.

2. W zadaniu Wpięć kliknij **działania mające miejsce po kompilacji** kartę.
3. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). 
4. W obszarze **konfiguracji klastra usługi sieć szkieletowa**, wybierz pozycję **wybierz klastra sieci szkieletowej usług** przycisk radiowy. Kliknij przycisk **Dodaj** obok **poświadczenia Azure**. Kliknij przycisk **Wpięć** Wybieranie dostawcy poświadczeń Wpięć.
5. Dostawca poświadczeń Wpięć, wybierz **główną usługi Microsoft Azure** z **rodzaj** listy rozwijanej.
6. Użyj wartości zapisane przez ustawienie się z nazwy głównej usługi w kroku 1, aby ustawić następujące pola:

   * **Identyfikator klienta**: *identyfikator aplikacji*
   * **Klucz tajny klienta**: *klucz aplikacji*
   * **Identyfikator dzierżawy**: *identyfikator katalogu*
   * **Identyfikator subskrypcji**: *identyfikator subskrypcji*
6. Wpisz opisową nazwę **identyfikator** umożliwiają określanie poświadczeń w Wpięć i zwięzłe **opis**. Następnie kliknij przycisk **Sprawdź nazwy głównej usługi**. Jeśli weryfikacja zakończy się pomyślnie, kliknij przycisk **Dodaj**.

   ![Usługa sieci szkieletowej Wpięć wprowadź poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. Pod **konfiguracji klastra usługi sieć szkieletowa**, upewnij się, że Twoje nowe poświadczenia został wybrany do **poświadczenia Azure**. 
8. Z **grupy zasobów** listy rozwijanej, wybierz grupę zasobów klastra, którą chcesz wdrożyć aplikację.
9. Z **sieci szkieletowej usług** listy rozwijanej, wybierz klaster, który chcesz wdrożyć aplikację.
10. Aby uzyskać **klucz klienta** i **certyfikatu klienta**, wprowadź lokalizację pliku PEM w kontenerze z Wpięć. Na przykład: `/var/jenkins_home/clustercert.pem`. 
11. W obszarze **konfiguracji aplikacji**, skonfiguruj **Nazwa aplikacji**, **typu aplikacji**i (względnej) **ścieżkę doManifestaplikacji** pól.
    ![Usługa sieci szkieletowej Wpięć Postkompilacyjnego akcji skonfigurować poświadczenia platformy Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. Kliknij przycisk **Sprawdź konfigurację**. Pomyślnie weryfikację, kliknij przycisk **zapisać**. Planowaną zadania Wpięć teraz jest w pełni skonfigurowane. W dalszym ciągu na [następne kroki](#next-steps) do przetestowania wdrożenia.

## <a name="next-steps"></a>Kolejne kroki
Usługa GitHub i narzędzie Jenkins są teraz skonfigurowane. Należy rozważyć zmianę niektóre przykładowe zmiany w `reliable-services-actor-sample/Actors/ActorCounter` projektu w rozwidlenia repozytorium, https://github.com/Azure-Samples/service-fabric-java-getting-started. Wypchnij zmiany do zdalnego `master` gałęzi (lub który został skonfigurowany do pracy z działu). Spowoduje to wyzwolenie skonfigurowanego zadania narzędzia Jenkins `MyJob`. Pobiera zmiany z usługi GitHub, ich tworzenia i wdraża aplikację do klastra, określonej w akcji po kompilacji.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

