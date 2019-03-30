---
title: Wtyczka usługi Azure Service Fabric dla środowiska Eclipse | Microsoft Docs
description: Rozpocznij pracę z wtyczką usługi Service Fabric dla środowiska Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: c33ecce5610dbef0dce13aa95f04ae4f0620603b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666940"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Wtyczka usługi Service Fabric na potrzeby tworzenia aplikacji Java w środowisku Eclipse
Eclipse jest jednym z najczęściej używanych zintegrowanych środowisk projektowych (IDE, integrated development environment) przez programistów języka Java. W tym artykule omówiono konfigurowanie środowiska projektowego Eclipse do pracy z usługą Azure Service Fabric. Dowiedz się, jak zainstalować wtyczkę usługi Service Fabric oraz utworzyć aplikację usługi Service Fabric i wdrożyć ją w lokalnym lub zdalnym klastrze usługi Service Fabric w środowisku Eclipse. 

> [!NOTE]
> Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalowanie lub aktualizowanie wtyczki usługi Service Fabric w środowisku Eclipse
W środowisku Eclipse można zainstalować wtyczkę usługi Service Fabric. Wtyczka może pomóc w uproszczeniu procesu kompilowania i wdrażania usług Java.

> [!IMPORTANT]
> Wtyczka usługi Service Fabric wymaga środowiska Eclipse Neon lub nowszej wersji. Aby sprawdzić wersję środowiska Eclipse, zobacz poniższe instrukcje. Jeśli masz zainstalowaną starszą wersję środowiska Eclipse, możesz pobrać nowsze wersje z [witryny środowiska Eclipse](https://www.eclipse.org). Nie zaleca się instalowania nowej wersji na istniejącej instalacji środowiska Eclipse. Można usunąć starszą wersję przed uruchomieniem instalatora lub zainstalować nowszą wersję w innym katalogu. 
> 
> W systemie Ubuntu zaleca się instalowanie bezpośrednio z witryny środowiska Eclipse, a nie za pomocą instalatora pakietu (`apt` lub `apt-get`). Gwarantuje to uzyskanie najnowszej wersji środowiska Eclipse. 

Zainstaluj program Eclipse Neon lub nowszy z [witryny programu Eclipse](https://www.eclipse.org).  Zainstaluj także wersję 2.2.1 lub nowszą wtyczki Buildship (wtyczka usługi Service Fabric nie jest zgodna ze starszymi wersjami wtyczki Buildship):
-   Aby sprawdzić wersje zainstalowanych składników, w środowisku Eclipse przejdź do pozycji **Help** > **About Eclipse** > **Installation Details** (Pomoc > Informacje o środowisku Eclipse > Szczegóły instalacji).
-   Aby zaktualizować zestaw Buildship, zobacz [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update].
-   Aby sprawdzić i zainstalować aktualizacje środowiska Eclipse, wybierz opcję **Help** > **Check for Updates** (Pomoc — Sprawdź dostępność aktualizacji).

Zainstaluj wtyczkę usługi Service Fabric: w środowisku Eclipse przejdź do pozycji **Help** > **Install New Software** (Pomoc > Instaluj nowe oprogramowanie).
1. W **pracować** wprowadź https:\//dl.microsoft.com/eclipse.
2. Kliknij pozycję **Add** (Dodaj).

   ![Wtyczka usługi Service Fabric dla środowiska Eclipse][sf-eclipse-plugin-install]
3. Wybierz wtyczkę usługi Service Fabric, a następnie kliknij przycisk **Next** (Dalej).
4. Wykonaj kroki instalacji, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
  
Jeśli wtyczka usługi Service Fabric jest już zainstalowana, zainstaluj najnowszą wersję. 
1. Aby sprawdzić, czy są dostępne aktualizacje, przejdź do pozycji **Help** > **About Eclipse** > **Installation Details** (Pomoc > Informacje o środowisku Eclipse > Szczegóły instalacji). 
2. Na liście zainstalowanych wtyczek wybierz usługę Service Fabric, a następnie kliknij pozycję **Update** (Aktualizuj). Dostępne aktualizacje zostaną zainstalowane.
3. Po zaktualizowaniu wtyczki usługi Service Fabric odśwież także projekt narzędzia Gradle.  Kliknij prawym przyciskiem myszy pozycję **build.gradle**, a następnie wybierz polecenie **Odśwież**.

> [!NOTE]
> Jeśli instalowanie lub aktualizowanie wtyczki usługi Service Fabric przebiega wolno, przyczyną może być ustawienie środowiska Eclipse. Środowisko Eclipse zbiera metadane dotyczące wszystkich zmian w witrynach aktualizowania, które są zarejestrowane w wystąpieniu środowiska Eclipse. Aby przyspieszyć proces sprawdzania i instalowania aktualizacji wtyczki usługi Service Fabric, wybierz pozycję **Available Software Sites** (Dostępne witryny z oprogramowaniem). Usuń zaznaczenie pól wyboru dla wszystkich witryn poza witryną wskazującą lokalizację wtyczki usługi Service Fabric (https:\//dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Jeśli środowisko Eclipse nie działa zgodnie z oczekiwaniami na komputerze Mac (lub wymagane są uprawnienia administratora), przejdź do folderu **ECLIPSE_INSTALLATION_PATH**, a następnie do podfolderu **Eclipse.app/Contents/MacOS**. Uruchom środowisko Eclipse, uruchamiając element `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Tworzenie aplikacji usługi Service Fabric w środowisku Eclipse

1.  W środowisku Eclipse wybierz opcję **File** > **New** > **Other** (Plik — Nowy — Inne). Wybierz pozycję **Service Fabric Project** (Projekt usługi Service Fabric), a następnie kliknij przycisk **Next** (Dalej).

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

## <a name="build-a-service-fabric-application-in-eclipse"></a>Tworzenie aplikacji usługi Service Fabric w środowisku Eclipse

1.  Kliknij prawym przyciskiem myszy nową aplikację usługi Service Fabric, a następnie wybierz polecenie **Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

2. W menu kontekstowym wybierz jedną z następujących opcji:
    -   Aby skompilować aplikację bez czyszczenia, kliknij opcję **Build Application** (Kompiluj aplikację).
    -   Aby skompilować aplikację z wcześniejszym czyszczeniem, kliknij opcję **Rebuild Application** (Kompiluj ponownie aplikację).
    -   Aby wyczyścić aplikację z artefaktów kompilacji, kliknij opcję **Clean Application** (Wyczyść aplikację).
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Wdrażanie aplikacji usługi Service Fabric w klastrze lokalnym za pomocą środowiska Eclipse

Po skonstruowaniu aplikacji usługi Service Fabric, wykonaj następujące kroki, aby wdrożyć go na lokalny klaster.

1. Jeśli jeszcze nie rozpoczęto klaster lokalny, postępuj zgodnie z instrukcjami [Konfigurowanie lokalnego klastra](./service-fabric-get-started-linux.md#set-up-a-local-cluster) Uruchom klaster lokalny i upewnij się, że jest on uruchomiony.
2. Kliknij prawym przyciskiem myszy aplikację usługi Service Fabric, a następnie wybierz **usługi Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

3.  W menu kontekstowym kliknij **wdrażanie aplikacji**.
4.  Możesz śledzić postęp operacji wdrażania, w oknie konsoli.
5.  Aby sprawdzić, czy aplikacja jest uruchomiona, Otwórz narzędzie Service Fabric Explorer w klastrze lokalnym w oknie przeglądarki [ http://localhost:19080/Explorer ](http://localhost:19080/Explorer). Rozwiń **aplikacje** węzła i upewnij się, że aplikacja jest uruchomiona. 

Informacje na temat debugowania aplikacji w środowisku Eclipse, przy użyciu klastra lokalnego, zobacz [debugowanie usługi Java w środowisku Eclipse](./service-fabric-debugging-your-application-java.md).

Można także wdrożyć aplikację w klastrze lokalnym za pomocą **Publikuj aplikację** polecenia:

1. Kliknij prawym przyciskiem myszy aplikację usługi Service Fabric, a następnie wybierz **usługi Service Fabric**.
2. W menu kontekstowym kliknij **Publikuj aplikację...** .
3. W **Publikuj aplikację** oknie Wybierz **PublishProfiles/Local.json** jako profil docelowy i kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — lokalnie](./media/service-fabric-get-started-eclipse/localjson.png)

    Domyślnie Local.json publikowania profilu skonfigurowano do publikowania w klastrze lokalnym. Aby uzyskać więcej informacji o parametrach połączenia i punktu końcowego, które muszą być obecne w profilach publikowania zobacz następną sekcję.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publikowanie aplikacji usługi Service Fabric na platformie Azure przy użyciu środowiska Eclipse

Aby opublikować aplikację w chmurze, wykonaj następujące kroki:

1. Aby opublikować aplikację w usłudze bezpiecznego klastra w chmurze, wymagany jest certyfikat X.509 można używać do komunikowania się z klastrem. W środowisku projektowania i testowania certyfikat używany jest często certyfikatu klastra. W środowiskach produkcyjnych certyfikat powinien być certyfikat klienta, który różni się od certyfikatu klastra. Potrzebujesz certyfikatu i klucza prywatnego. Plik certyfikatu (i klucza) musi być w formacie PEM. Można utworzyć pliku PEM, który zawiera certyfikat i klucz prywatny z pliku PFX za pomocą następującego polecenia openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Jeśli plik PFX nie jest chroniony hasłem, użyj `--passin pass:` ostatniego parametru.

2. Otwórz **Cloud.json** plik **PublishProfiles** katalogu. Musisz skonfigurować poświadczenia punktu końcowego i zabezpieczenia klastra, odpowiednio dla klastra.

   - `ConnectionIPOrURL` Pole zawiera adres IP lub adres URL klastra. Należy pamiętać, że wartość nie zawiera schemat adresu URL (`https://`).
   - Domyślnie `ConnectionPort` pole powinno być `19080`, chyba że jawnie zmienisz tego portu dla klastra.
   - `ClientKey` Pól powinien wskazywać w formacie PEM PEM lub Key pliku na komputerze lokalnym, który zawiera klucz prywatny dla certyfikatu klienta lub klastra.
   - `ClientCert` Pól powinien wskazywać w formacie PEM PEM lub CRT pliku na komputerze lokalnym, zawierający dane certyfikatu dla klienta lub klastra. certyfikat. 

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

2. Kliknij prawym przyciskiem myszy aplikację usługi Service Fabric, a następnie wybierz **usługi Service Fabric**.
3. W menu kontekstowym kliknij **Publikuj aplikację...** .
3. W **Publikuj aplikację** oknie Wybierz **PublishProfiles/Cloud.json** jako profil docelowy i kliknij przycisk **Publikuj**.

    ![Okno dialogowe publikowania — chmura](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Możesz śledzić postęp operacji publikowania w oknie konsoli.
5. Aby sprawdzić, czy aplikacja jest uruchomiona, Otwórz narzędzie Service Fabric Explorer w klastrze usługi Azure, w oknie przeglądarki. W powyższym przykładzie będzie to: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Rozwiń **aplikacje** węzła i upewnij się, że aplikacja jest uruchomiona. 


W klastrach systemu Linux bezpieczne Jeśli Twoja aplikacja usług Reliable Services, należy również skonfigurować certyfikat, który umożliwia wywołanie środowiska uruchomieniowego usługi Service Fabric interfejsów API usługi. Aby dowiedzieć się więcej, zobacz [skonfigurować aplikację usług Reliable Services, aby działały w klastrach systemu Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Aby uzyskać szybkie omówienie sposobu wdrażania aplikacji usług Reliable Services usługi Service Fabric, napisane w języku Java do zabezpieczonego klastra systemu Linux, zobacz [Szybki Start: Wdrażanie aplikacji Java usług Reliable Services](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Wdrażanie aplikacji usługi Service Fabric za pomocą konfiguracji uruchamiania środowiska Eclipse

Alternatywnym sposobem wdrażania aplikacji usługi Service Fabric jest użycie konfiguracji uruchamiania środowiska Eclipse.

1. W środowisku Eclipse przejdź do **Uruchom** > **konfiguracje uruchomieniowe**.
2. W obszarze **Gradle Project** (Projekt narzędzia Gradle) wybierz konfigurację uruchamiania **ServiceFabricDeployer**.
3. W okienku po prawej stronie na **argumenty** i upewnij się, że **ip**, **portu**, **clientCert**, i **clientKey**parametry są odpowiednio ustawione dla danego wdrożenia. Parametry są domyślnie do wdrożenia w klastrze lokalnym, tak jak w poniższym zrzucie ekranu. Aby opublikować aplikację na platformie Azure można zmodyfikować parametry, które zawierają szczegóły punktu końcowego i poświadczenia zabezpieczeń dla klastra usługi Azure. Aby uzyskać więcej informacji, zobacz poprzednią sekcję [publikowanie aplikacji usługi Service Fabric na platformie Azure przy użyciu środowiska Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Uruchom lokalny okna dialogowego konfiguracji](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Upewnij się, że **katalog roboczy** wskazuje aplikację, którą chcesz wdrożyć. Aby zmienić aplikację, kliknij przycisk **Workspace** (Obszar roboczy) i wybierz odpowiednią aplikację.
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

Na potrzeby scenariusza uaktualniania przyjmijmy, że utworzono projekt **App1** za pomocą wtyczki usługi Service Fabric w środowisku Eclipse. Wdrożono go za pomocą wtyczki w celu utworzenia aplikacji o nazwie **fabric:/App1Application**. Typ aplikacji jest **App1ApplicationType**, a jej wersja to 1.0. Teraz chcesz uaktualnić aplikację bez przerw w dostępności.

Najpierw wprowadź zmiany w aplikacji i ponownie skompiluj zmodyfikowaną usługę. Zaktualizuj plik manifestu zmodyfikowanej usługi (ServiceManifest.xml) za pomocą zaktualizowanych wersji usługi (i kodu, konfiguracji lub danych — odpowiednio do potrzeb). Zmodyfikuj także manifest aplikacji (ApplicationManifest.xml), określając zaktualizowany numer wersji dla aplikacji i zmodyfikowaną usługę.  

Aby uaktualnić aplikację przy użyciu środowiska Eclipse, możesz utworzyć zduplikowany profil konfiguracji uruchamiania. Następnie użyj go do uaktualnienia aplikacji zgodnie z potrzebami.

1.  Wybierz opcję **Run** > **Run Configurations** (Uruchom — Konfiguracje uruchamiania). W lewym okienku kliknij małą strzałkę po lewej stronie pozycji **Gradle Project** (Projekt narzędzia Gradle).
2.  Kliknij prawym przyciskiem myszy pozycję **ServiceFabricDeployer** i wybierz polecenie **Duplicate** (Duplikuj). Wprowadź nową nazwę dla tej konfiguracji, na przykład **ServiceFabricUpgrader**.
3.  W prawym panelu na karcie **Arguments** (Argumenty) zmień wartość **-Pconfig='deploy'** na wartość **-Pconfig='upgrade'** i kliknij przycisk **Apply** (Zastosuj).

Ten proces umożliwia utworzenie i zapisanie profilu konfiguracji uruchamiania, za pomocą którego można w dowolnym momencie uaktualnić aplikację. W jego ramach pobierana jest również najnowsza zaktualizowana wersja typu aplikacji z pliku manifestu aplikacji.

Uaktualnienie aplikacji zajmuje kilka minut. Narzędzie Service Fabric Explorer umożliwia monitorowanie uaktualniania aplikacji.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrowanie starych aplikacji Java usługi Service Fabric na potrzeby użycia z narzędziem Maven
Ostatnio przenieśliśmy biblioteki Java usługi Service Fabric z zestawu SDK Java usługi Service Fabric do repozytorium narzędzia Maven. Nowe aplikacje generowane za pomocą środowiska Eclipse będą generować najnowsze, zaktualizowane projekty (mogące działać z narzędziem Maven), ale możesz zaktualizować swoje istniejące bezstanowe aplikacje Java lub aplikacje Java aktora usługi Service Fabric, które wcześniej korzystały z zestawu SDK Java usługi Service Fabric, aby używały zależności języka Java usługi Service Fabric z narzędzia Maven. Wykonaj kroki wymienione [tutaj](service-fabric-migrate-old-javaapp-to-use-maven.md), aby zapewnić działanie Twojej starszej aplikacji z narzędziem Maven.

## <a name="next-steps"></a>Kolejne kroki

- Szybkie kroki tworzenia niezawodnych Java usługi aplikacji i wdrażania go lokalnie, jak i na platformie Azure, zobacz [Szybki Start: Wdrażanie aplikacji Java usług Reliable Services](./service-fabric-quickstart-java-reliable-services.md).
- Aby dowiedzieć się, jak debugowanie aplikacji Java w klastrze lokalnym, zobacz [debugowanie usługi Java w środowisku Eclipse](./service-fabric-debugging-your-application-java.md).
- Aby dowiedzieć się, jak monitorować i diagnozować aplikacje usługi Service Fabric, zobacz [monitorowanie i diagnozowanie usług w lokalnym komputerze deweloperskim](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

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
