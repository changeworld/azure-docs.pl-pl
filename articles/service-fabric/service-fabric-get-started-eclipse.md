---
title: Wtyczka sieci szkieletowej usługi Azure dla programu Eclipse
description: Dowiedz się więcej o rozpoczęciu pracy z usługą Azure Service Fabric w języku Java przy użyciu wtyczki eclipse i service fabric.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: b779873488f1fff754d4105249b28f545738c11b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258423"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Wtyczka usługi Service Fabric na potrzeby tworzenia aplikacji Java w środowisku Eclipse
Eclipse jest jednym z najczęściej używanych zintegrowanych środowisk projektowych (IDE, integrated development environment) przez programistów języka Java. W tym artykule omówiono konfigurowanie środowiska projektowego Eclipse do pracy z usługą Azure Service Fabric. Dowiedz się, jak zainstalować wtyczkę usługi Service Fabric oraz utworzyć aplikację usługi Service Fabric i wdrożyć ją w lokalnym lub zdalnym klastrze usługi Service Fabric w środowisku Eclipse. 

> [!NOTE]
> Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

> [!IMPORTANT]
> Upewnij się, że JDK 8 jest zainstalowany w systemie i wybrany w eclipse.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalowanie lub aktualizowanie wtyczki usługi Service Fabric w środowisku Eclipse
W środowisku Eclipse można zainstalować wtyczkę usługi Service Fabric. Wtyczka może pomóc w uproszczeniu procesu kompilowania i wdrażania usług Java.

> [!IMPORTANT]
> Wtyczka usługi Service Fabric wymaga środowiska Eclipse Neon lub nowszej wersji. Aby sprawdzić wersję środowiska Eclipse, zobacz poniższe instrukcje. Jeśli masz zainstalowaną starszą wersję środowiska Eclipse, możesz pobrać nowsze wersje z [witryny środowiska Eclipse](https://www.eclipse.org). Nie zaleca się instalowania nowej wersji na istniejącej instalacji środowiska Eclipse. Można usunąć starszą wersję przed uruchomieniem instalatora lub zainstalować nowszą wersję w innym katalogu. 
> 
> W systemie Ubuntu zaleca się instalowanie bezpośrednio z witryny środowiska Eclipse, a nie za pomocą instalatora pakietu (`apt` lub `apt-get`). Gwarantuje to uzyskanie najnowszej wersji środowiska Eclipse. 

Zainstaluj program Eclipse Neon lub nowszy z [witryny programu Eclipse](https://www.eclipse.org).  Zainstaluj także wersję 2.2.1 lub nowszą wtyczki Buildship (wtyczka usługi Service Fabric nie jest zgodna ze starszymi wersjami wtyczki Buildship):
-   Aby sprawdzić wersje zainstalowanych składników, w programie Eclipse przejdź do **strony Pomoc** > dotycząca**szczegółów instalacji programu****Eclipse** > .
-   Aby zaktualizować zestaw Buildship, zobacz [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: wtyczki środowiska Eclipse dla narzędzia Gradle).
-   Aby sprawdzić dostępność i zainstalować aktualizacje programu Eclipse, przejdź do **programu Pomoc** > **w sprawdzeniu dostępności aktualizacji**.

Zainstaluj wtyczkę Service Fabric w programie Eclipse przejdź do **programu Pomoc** > **w instalacji nowego oprogramowania**.
1. W polu **Praca z** wprowadź\/https: /dl.microsoft.com/eclipse.
2. Kliknij przycisk **Dodaj**.

   ![Wtyczka usługi Service Fabric dla środowiska Eclipse][sf-eclipse-plugin-install]
3. Wybierz wtyczkę usługi Service Fabric, a następnie kliknij przycisk **Next** (Dalej).
4. Wykonaj kroki instalacji, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
  
Jeśli wtyczka usługi Service Fabric jest już zainstalowana, zainstaluj najnowszą wersję. 
1. Aby sprawdzić dostępność aktualizacji, przejdź do **pomocy dotyczące** > **szczegółów instalacji programu****Eclipse** > . 
2. Na liście zainstalowanych wtyczek wybierz usługę Service Fabric, a następnie kliknij pozycję **Update** (Aktualizuj). Dostępne aktualizacje zostaną zainstalowane.
3. Po zaktualizowaniu wtyczki usługi Service Fabric odśwież także projekt narzędzia Gradle.  Kliknij prawym przyciskiem myszy pozycję **build.gradle**, a następnie wybierz polecenie **Odśwież**.

> [!NOTE]
> Jeśli instalowanie lub aktualizowanie wtyczki usługi Service Fabric przebiega wolno, przyczyną może być ustawienie środowiska Eclipse. Środowisko Eclipse zbiera metadane dotyczące wszystkich zmian w witrynach aktualizowania, które są zarejestrowane w wystąpieniu środowiska Eclipse. Aby przyspieszyć proces sprawdzania i instalowania aktualizacji wtyczki usługi Service Fabric, wybierz pozycję **Available Software Sites** (Dostępne witryny z oprogramowaniem). Wyczyść pola wyboru dla wszystkich witryn z wyjątkiem tej, która wskazuje\/lokalizację wtyczki sieci szkieletowej usług (https: /dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Jeśli środowisko Eclipse nie działa zgodnie z oczekiwaniami na komputerze Mac (lub wymagane są uprawnienia administratora), przejdź do folderu **ECLIPSE_INSTALLATION_PATH**, a następnie do podfolderu **Eclipse.app/Contents/MacOS**. Uruchom środowisko Eclipse, uruchamiając element `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Tworzenie aplikacji usługi Service Fabric w środowisku Eclipse

1.  W Eclipse, przejdź do **pliku** > **nowy** > **inny**. Wybierz pozycję **Service Fabric Project** (Projekt usługi Service Fabric), a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 1][create-application/p1]

2.  Wprowadź nazwę projektu, a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 2][create-application/p2]

3.  Na liście szablonów wybierz pozycję **Service Template** (Szablon usługi). Wybierz typ szablonu usługi — Actor (Aktor), Stateless (Bezstanowa), Container (Kontener) lub Guest Binary (Binarna gościa) — a następnie kliknij przycisk **Next** (Dalej).

    ![Nowy projekt usługi Service Fabric — strona 3][create-application/p3]

4.  Wprowadź nazwę usługi i jej szczegóły, a następnie kliknij przycisk **Finish** (Zakończ).

    ![Nowy projekt usługi Service Fabric — strona 4][create-application/p4]

5. Podczas tworzenia pierwszego projektu usługi Service Fabric w oknie dialogowym **Open Associated Perspective** (Otwieranie skojarzonej perspektywy) kliknij przycisk **Yes** (Tak).

    ![Nowy projekt usługi Service Fabric — strona 5][create-application/p5]

6.  Nowy projekt wygląda następująco:

    ![Nowy projekt usługi Service Fabric — strona 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Tworzenie aplikacji sieci szkieletowej usług w programie Eclipse

1.  Kliknij prawym przyciskiem myszy nową aplikację usługi Service Fabric, a następnie wybierz polecenie **Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

2. W menu kontekstowym wybierz jedną z następujących opcji:
    -   Aby skompilować aplikację bez czyszczenia, kliknij opcję **Build Application** (Kompiluj aplikację).
    -   Aby skompilować aplikację z wcześniejszym czyszczeniem, kliknij opcję **Rebuild Application** (Kompiluj ponownie aplikację).
    -   Aby wyczyścić aplikację z artefaktów kompilacji, kliknij opcję **Clean Application** (Wyczyść aplikację).
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Wdrażanie aplikacji sieci szkieletowej usług w klastrze lokalnym za pomocą programu Eclipse

Po ssceniu aplikacji sieci szkieletowej usług wykonaj następujące kroki, aby wdrożyć ją w klastrze lokalnym.

1. Jeśli klaster lokalny nie został uruchomiony, postępuj zgodnie z instrukcjami w [aplikacji Konfigurowanie klastra lokalnego,](./service-fabric-get-started-linux.md#set-up-a-local-cluster) aby uruchomić klaster lokalny i upewnić się, że jest uruchomiony.
2. Kliknij prawym przyciskiem myszy aplikację sieci szkieletowej usług, a następnie wybierz polecenie **Sieci szkieletowej usług**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

3.  Z menu kontekstowego kliknij polecenie **Wdrażanie aplikacji**.
4.  Postęp operacji wdrażania można śledzić w oknie konsoli.
5.  Aby sprawdzić, czy aplikacja jest uruchomiona, otwórz Eksploratora [http://localhost:19080/Explorer](http://localhost:19080/Explorer)sieci szkieletowej usług w lokalnym klastrze w oknie przeglądarki . Rozwiń węzeł **Aplikacje** i upewnij się, że aplikacja jest uruchomiona. 

Aby dowiedzieć się, jak debugować aplikację w programie Eclipse przy użyciu klastra [lokalnego, zobacz Debugowanie usługi Java w programie Eclipse](./service-fabric-debugging-your-application-java.md).

Można również wdrożyć aplikację w klastrze lokalnym za pomocą polecenia **Publikuj aplikację:**

1. Kliknij prawym przyciskiem myszy aplikację sieci szkieletowej usług, a następnie wybierz polecenie **Sieci szkieletowej usług**.
2. Z menu kontekstowego kliknij polecenie **Publikuj aplikację...**.
3. W oknie **Publikowanie aplikacji** wybierz pozycję **Publikujprofiles/Local.json** jako profil docelowy i kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — lokalnie](./media/service-fabric-get-started-eclipse/localjson.png)

    Domyślnie profil publikowania Local.json jest skonfigurowany do publikowania w klastrze lokalnym. Aby uzyskać więcej informacji na temat parametrów połączenia i punktu końcowego obecnych w profilach publikowania, zobacz następną sekcję.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publikowanie aplikacji sieci szkieletowej usług na platformie Azure za pomocą programu Eclipse

Aby opublikować aplikację w chmurze, wykonaj następujące kroki:

1. Aby opublikować aplikację w bezpiecznym klastrze w chmurze, potrzebny jest certyfikat X.509 do komunikacji z klastrem. W środowiskach testowych i deweloperskich używany certyfikat jest często certyfikat klastra. W środowiskach produkcyjnych certyfikat powinien być certyfikatem klienta, który różni się od certyfikatu klastra. Potrzebny jest zarówno certyfikat, jak i klucz prywatny. Plik certyfikatu (i klucza) musi być sformatowany w formacie PEM. Można utworzyć plik PEM zawierający certyfikat i klucz prywatny z pliku PFX za pomocą następującego polecenia openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Jeśli plik PFX nie jest chroniony `--passin pass:` hasłem, użyj ostatniego parametru.

2. Otwórz plik **Cloud.json** w katalogu **PublishProfiles.** Należy skonfigurować punkt końcowy klastra i poświadczenia zabezpieczeń odpowiednio dla klastra.

   - To `ConnectionIPOrURL` pole zawiera adres IP lub adres URL klastra. Należy zauważyć, że wartość nie`https://`zawiera schematu adresu URL ( ).
   - Domyślnie `ConnectionPort` pole powinno `19080`być , chyba że jawnie zmieniono ten port dla klastra.
   - Pole `ClientKey` powinno wskazywać plik pem w formacie PEM lub .key na komputerze lokalnym zawierający klucz prywatny certyfikatu klienta lub klastra.
   - Pole `ClientCert` powinno wskazywać plik pem w formacie PEM lub crt w formacie PEM na komputerze lokalnym, który zawiera dane certyfikatu klienta lub klastra. Certyfikat. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Kliknij prawym przyciskiem myszy aplikację sieci szkieletowej usług, a następnie wybierz polecenie **Sieci szkieletowej usług**.
3. Z menu kontekstowego kliknij polecenie **Publikuj aplikację...**.
3. W oknie **Publikowanie aplikacji** wybierz pozycję **Publikujprofiles/Cloud.json** jako profil docelowy i kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — chmura](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Postęp operacji publikowania można śledzić w oknie Konsoli.
5. Aby sprawdzić, czy aplikacja jest uruchomiona, otwórz Eksploratora sieci szkieletowej usług w klastrze platformy Azure w oknie przeglądarki. W powyższym przykładzie byłoby `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`to: . Rozwiń węzeł **Aplikacje** i upewnij się, że aplikacja jest uruchomiona. 


W przypadku bezpiecznych klastrów systemu Linux, jeśli aplikacja zawiera usługi niezawodne usługi, należy również skonfigurować certyfikat, którego usługi mogą używać do wywoływania interfejsów API środowiska uruchomieniowego sieci szkieletowej usług. Aby dowiedzieć się więcej, zobacz [Konfigurowanie aplikacji Niezawodne usługi do uruchamiania w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Aby szybko przejść przez sposób wdrażania aplikacji niezawodnych usług sieci szkieletowej usług napisanej w języku Java w bezpiecznym klastrze systemu Linux, zobacz [Szybki start: Wdrażanie aplikacji Java Reliable Services](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Wdrażanie aplikacji sieci szkieletowej usług przy użyciu konfiguracji uruchamiania programu Eclipse

Alternatywnym sposobem wdrażania aplikacji usługi Service Fabric jest użycie konfiguracji uruchamiania środowiska Eclipse.

1. W programie Eclipse przejdź do **programu Uruchom** > **konfiguracje .**
2. W obszarze **Gradle Project** (Projekt narzędzia Gradle) wybierz konfigurację uruchamiania **ServiceFabricDeployer**.
3. W prawym okienku na karcie **Argumenty** upewnij się, że parametry **ip**, **port**, **clientCert**i **clientKey** są ustawione odpowiednio dla danego wdrożenia. Domyślnie parametry są ustawione do wdrożenia w klastrze lokalnym, jak na poniższym zrzucie ekranu. Aby opublikować aplikację na platformie Azure, można zmodyfikować parametry, aby zawierały szczegóły punktu końcowego i poświadczenia zabezpieczeń dla klastra platformy Azure. Aby uzyskać więcej informacji, zobacz poprzednią [sekcję, Publikuj aplikację sieci szkieletowej usług na platformie Azure za pomocą programu Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Uruchom lokalne okno dialogowe konfiguracji](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Upewnij się, że **katalog roboczy** wskazuje na aplikację, którą chcesz wdrożyć. Aby zmienić aplikację, kliknij przycisk **Workspace** (Obszar roboczy) i wybierz odpowiednią aplikację.
6. Kliknij przycisk **Apply** (Zastosuj), a następnie kliknij przycisk **Run** (Uruchom).

Aplikacja zostanie skompilowana i wdrożona w ciągu kilku minut. Stan wdrażania możesz monitorować w narzędziu Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Dodawanie usługi Service Fabric do aplikacji usługi Service Fabric

Aby dodać usługę Service Fabric do istniejącej aplikacji usługi Service Fabric, wykonaj następujące kroki:

1.  Kliknij prawym przyciskiem myszy projekt, do którego chcesz dodać usługę, a następnie kliknij polecenie **Service Fabric**.

    ![Dodawanie usługi Service Fabric — strona 1][add-service/p1]

2.  Kliknij pozycję **Add Service Fabric Service** (Dodaj usługę Service Fabric) i wykonaj zestaw czynności, aby dodać usługę do projektu.
3.  Wybierz szablon usługi, który chcesz dodać do projektu, a następnie kliknij przycisk **Next** (Dalej).

    ![Dodawanie usługi Service Fabric — strona 2][add-service/p2]

4.  Wprowadź nazwę usługi (i inne szczegóły, zgodnie z potrzebami), a następnie kliknij przycisk **Add Service** (Dodaj usługę) przycisku.  

    ![Dodawanie usługi Service Fabric — strona 3][add-service/p3]

5.  Po dodaniu usługi ogólna struktura projektu powinna wyglądać podobnie do poniższej:

    ![Dodawanie usługi Service Fabric — strona 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Edytowanie wersji manifestu aplikacji Java usługi Service Fabric

Aby edytować wersje manifestu, kliknij prawym przyciskiem myszy projekt, przejdź do lokalizacji **Service Fabric** i wybierz z menu rozwijanego pozycję **Edytuj wersje manifestu**. W kreatorze możesz zaktualizować wersje manifestu aplikacji i manifestu usługi oraz wersje pakietów **Kod**, **Konfiguracja** i **Dane**.

Jeśli zaznaczysz opcję **Automatycznie aktualizuj wersje aplikacji i usługi**, a następnie zaktualizujesz wersję, wersje manifestu zostaną automatycznie zaktualizowane. Przykład: najpierw musisz zaznaczyć pole wyboru, a następnie zaktualizować wersję pakietu **Kod** z 0.0.0 na 0.0.1 i kliknąć przycisk **Zakończ**. Następnie wersja manifestu usługi i wersja manifestu aplikacji zostaną automatycznie zaktualizowane do wersji 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Uaktualnianie aplikacji Java usługi Service Fabric

Na potrzeby scenariusza uaktualniania przyjmijmy, że utworzono projekt **App1** za pomocą wtyczki usługi Service Fabric w środowisku Eclipse. Wdrożono go za pomocą wtyczki w celu utworzenia aplikacji o nazwie **fabric:/App1Application**. Typ aplikacji to **App1ApplicationType**, a wersja aplikacji to 1.0. Teraz chcesz uaktualnić aplikację bez przerw w dostępności.

Najpierw wprowadź zmiany w aplikacji i ponownie skompiluj zmodyfikowaną usługę. Zaktualizuj plik manifestu zmodyfikowanej usługi (ServiceManifest.xml) za pomocą zaktualizowanych wersji usługi (i kodu, konfiguracji lub danych — odpowiednio do potrzeb). Zmodyfikuj także manifest aplikacji (ApplicationManifest.xml), określając zaktualizowany numer wersji dla aplikacji i zmodyfikowaną usługę.  

Aby uaktualnić aplikację przy użyciu środowiska Eclipse, możesz utworzyć zduplikowany profil konfiguracji uruchamiania. Następnie użyj go do uaktualnienia aplikacji zgodnie z potrzebami.

1.  Przejdź do **programu Uruchom** > **konfiguracje uruchamiania**. W lewym okienku kliknij małą strzałkę po lewej stronie pozycji **Gradle Project** (Projekt narzędzia Gradle).
2.  Kliknij prawym przyciskiem myszy pozycję **ServiceFabricDeployer** i wybierz polecenie **Duplicate** (Duplikuj). Wprowadź nową nazwę dla tej konfiguracji, na przykład **ServiceFabricUpgrader**.
3.  W prawym panelu na karcie **Arguments** (Argumenty) zmień wartość **-Pconfig='deploy'** na wartość **-Pconfig='upgrade'** i kliknij przycisk **Apply** (Zastosuj).

Ten proces umożliwia utworzenie i zapisanie profilu konfiguracji uruchamiania, za pomocą którego można w dowolnym momencie uaktualnić aplikację. W jego ramach pobierana jest również najnowsza zaktualizowana wersja typu aplikacji z pliku manifestu aplikacji.

Uaktualnienie aplikacji zajmuje kilka minut. Narzędzie Service Fabric Explorer umożliwia monitorowanie uaktualniania aplikacji.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrowanie starych aplikacji Java usługi Service Fabric na potrzeby użycia z narzędziem Maven
Ostatnio przenieśliśmy biblioteki Java usługi Service Fabric z zestawu SDK Java usługi Service Fabric do repozytorium narzędzia Maven. Nowe aplikacje generowane za pomocą środowiska Eclipse będą generować najnowsze, zaktualizowane projekty (mogące działać z narzędziem Maven), ale możesz zaktualizować swoje istniejące bezstanowe aplikacje Java lub aplikacje Java aktora usługi Service Fabric, które wcześniej korzystały z zestawu SDK Java usługi Service Fabric, aby używały zależności języka Java usługi Service Fabric z narzędzia Maven. Wykonaj kroki wymienione [tutaj](service-fabric-migrate-old-javaapp-to-use-maven.md), aby zapewnić działanie Twojej starszej aplikacji z narzędziem Maven.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szybkie kroki dotyczące tworzenia aplikacji java reliable service i wdrażania jej lokalnie i na platformie Azure, zobacz [Szybki start: Wdrażanie aplikacji java reliable services](./service-fabric-quickstart-java-reliable-services.md).
- Aby dowiedzieć się, jak debugować aplikację Java w klastrze lokalnym, zobacz [Debugowanie usługi Java w programie Eclipse](./service-fabric-debugging-your-application-java.md).
- Aby dowiedzieć się, jak monitorować i diagnozować aplikacje sieci szkieletowej usług, zobacz [Monitorowanie i diagnozowanie usług w konfiguracji lokalnego tworzenia maszyn](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
