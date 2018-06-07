---
title: Wtyczka usługi Azure Service Fabric dla środowiska Eclipse | Microsoft Docs
description: Rozpocznij pracę z wtyczką usługi Service Fabric dla środowiska Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: 2fbae584c09fd83f2233895d31c1013acd06ae3b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643002"
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
-   Aby zaktualizować zestaw Buildship, zobacz [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: wtyczki środowiska Eclipse dla narzędzia Gradle).
-   Aby sprawdzić i zainstalować aktualizacje środowiska Eclipse, wybierz opcję **Help** > **Check for Updates** (Pomoc — Sprawdź dostępność aktualizacji).

Zainstaluj wtyczkę usługi Service Fabric: w środowisku Eclipse przejdź do pozycji **Help** > **Install New Software** (Pomoc > Instaluj nowe oprogramowanie).
1. W polu **Work with** (Praca z) wpisz adres **http://dl.microsoft.com/eclipse**.
2. Kliknij pozycję **Add** (Dodaj).
    ![Wtyczka usługi Service Fabric dla środowiska Eclipse][sf-eclipse-plugin-install]
3. Wybierz wtyczkę usługi Service Fabric, a następnie kliknij przycisk **Next** (Dalej).
4. Wykonaj kroki instalacji, a następnie zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft.
  
Jeśli wtyczka usługi Service Fabric jest już zainstalowana, zainstaluj najnowszą wersję. 
1. Aby sprawdzić, czy są dostępne aktualizacje, przejdź do pozycji **Help** > **About Eclipse** > **Installation Details** (Pomoc > Informacje o środowisku Eclipse > Szczegóły instalacji). 
2. Na liście zainstalowanych wtyczek wybierz usługę Service Fabric, a następnie kliknij pozycję **Update** (Aktualizuj). Dostępne aktualizacje zostaną zainstalowane.
3. Po zaktualizowaniu wtyczki usługi Service Fabric odśwież także projekt narzędzia Gradle.  Kliknij prawym przyciskiem myszy pozycję **build.gradle**, a następnie wybierz polecenie **Odśwież**.

> [!NOTE]
> Jeśli instalowanie lub aktualizowanie wtyczki usługi Service Fabric przebiega wolno, przyczyną może być ustawienie środowiska Eclipse. Środowisko Eclipse zbiera metadane dotyczące wszystkich zmian w witrynach aktualizowania, które są zarejestrowane w wystąpieniu środowiska Eclipse. Aby przyspieszyć proces sprawdzania i instalowania aktualizacji wtyczki usługi Service Fabric, wybierz pozycję **Available Software Sites** (Dostępne witryny z oprogramowaniem). Usuń zaznaczenie pól wyboru dla wszystkich witryn poza witryną wskazującą lokalizację wtyczki usługi Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

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

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Kompilowanie i wdrażanie aplikacji usługi Service Fabric w środowisku Eclipse

1.  Kliknij prawym przyciskiem myszy nową aplikację usługi Service Fabric, a następnie wybierz polecenie **Service Fabric**.

    ![Menu prawego przycisku myszy usługi Service Fabric][publish/RightClick]

2. W podmenu wybierz odpowiednią opcję:
    -   Aby skompilować aplikację bez czyszczenia, kliknij opcję **Build Application** (Kompiluj aplikację).
    -   Aby skompilować aplikację z wcześniejszym czyszczeniem, kliknij opcję **Rebuild Application** (Kompiluj ponownie aplikację).
    -   Aby wyczyścić aplikację z artefaktów kompilacji, kliknij opcję **Clean Application** (Wyczyść aplikację).

3.  To menu umożliwia także wdrożenie, cofnięcie wdrożenia i opublikowanie aplikacji:
    -   Aby wdrożyć w klastrze lokalnym, kliknij opcję **Deploy Application** (Wdróż aplikację).
    -   W oknie dialogowym **Publish Application** (Publikowanie aplikacji) wybierz profil publikowania:
        -  **Local.json**
        -  **Cloud.json**

     Te pliki JSON (JavaScript Object Notation) służą do przechowywania informacji (takich jak punkty końcowe połączenia i informacje o zabezpieczeniach) wymaganych do nawiązania połączenia z klastrem lokalnym lub w chmurze (Azure).

  ![Menu publikowania usługi Service Fabric][publish/Publish]

Alternatywnym sposobem wdrażania aplikacji usługi Service Fabric jest użycie konfiguracji uruchamiania środowiska Eclipse.

  1.    Wybierz opcję **Run** > **Run Configurations** (Uruchom — Konfiguracje uruchamiania).
  2.    W obszarze **Gradle Project** (Projekt narzędzia Gradle) wybierz konfigurację uruchamiania **ServiceFabricDeployer**.
  3.    W okienku po prawej stronie na karcie **Arguments** (Argumenty) dla pozycji **publishProfile** wybierz opcję **local** (lokalne) lub **cloud** (w chmurze).  Wartość domyślna to **local** (lokalne). Aby wdrożyć w klastrze zdalnym lub w chmurze wybierz opcję **cloud** (w chmurze).
  4.    Aby upewnić się, że w profilach publikowania zostały podane prawidłowe informacje, edytuj plik **Local.json** lub **Cloud.json** zgodnie z potrzebami. Możesz dodać lub zaktualizować szczegóły punktu końcowego i poświadczenia zabezpieczeń.
  5.    Upewnij się, że pozycja **Working Directory** (Katalog roboczy) wskazuje aplikację do wdrożenia. Aby zmienić aplikację, kliknij przycisk **Workspace** (Obszar roboczy) i wybierz odpowiednią aplikację.
  6.    Kliknij przycisk **Apply** (Zastosuj), a następnie kliknij przycisk **Run** (Uruchom).

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

Na potrzeby scenariusza uaktualniania przyjmijmy, że utworzono projekt **App1** za pomocą wtyczki usługi Service Fabric w środowisku Eclipse. Wdrożono go za pomocą wtyczki w celu utworzenia aplikacji o nazwie **fabric:/App1Application**. Typ aplikacji to **App1ApplicationType**, a jej wersja to 1.0. Teraz chcesz uaktualnić aplikację bez przerw w dostępności.

Najpierw wprowadź zmiany w aplikacji i ponownie skompiluj zmodyfikowaną usługę. Zaktualizuj plik manifestu zmodyfikowanej usługi (ServiceManifest.xml) za pomocą zaktualizowanych wersji usługi (i kodu, konfiguracji lub danych — odpowiednio do potrzeb). Zmodyfikuj także manifest aplikacji (ApplicationManifest.xml), określając zaktualizowany numer wersji dla aplikacji i zmodyfikowaną usługę.  

Aby uaktualnić aplikację przy użyciu środowiska Eclipse, możesz utworzyć zduplikowany profil konfiguracji uruchamiania. Następnie użyj go do uaktualnienia aplikacji zgodnie z potrzebami.

1.  Wybierz opcję **Run** > **Run Configurations** (Uruchom — Konfiguracje uruchamiania). W lewym okienku kliknij małą strzałkę po lewej stronie pozycji **Gradle Project** (Projekt narzędzia Gradle).
2.  Kliknij prawym przyciskiem myszy pozycję **ServiceFabricDeployer** i wybierz polecenie **Duplicate** (Duplikuj). Wprowadź nową nazwę dla tej konfiguracji, na przykład **ServiceFabricUpgrader**.
3.  W prawym panelu na karcie **Arguments** (Argumenty) zmień wartość **-Pconfig='deploy'** na wartość **-Pconfig='upgrade'** i kliknij przycisk **Apply** (Zastosuj).

Ten proces umożliwia utworzenie i zapisanie profilu konfiguracji uruchamiania, za pomocą którego można w dowolnym momencie uaktualnić aplikację. W jego ramach pobierana jest również najnowsza zaktualizowana wersja typu aplikacji z pliku manifestu aplikacji.

Uaktualnienie aplikacji zajmuje kilka minut. Narzędzie Service Fabric Explorer umożliwia monitorowanie uaktualniania aplikacji.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrowanie starych aplikacji Java usługi Service Fabric na potrzeby użycia z narzędziem Maven
Ostatnio przenieśliśmy biblioteki Java usługi Service Fabric z zestawu SDK Java usługi Service Fabric do repozytorium narzędzia Maven. Nowe aplikacje generowane za pomocą środowiska Eclipse będą generować najnowsze, zaktualizowane projekty (mogące działać z narzędziem Maven), ale możesz zaktualizować swoje istniejące bezstanowe aplikacje Java lub aplikacje Java aktora usługi Service Fabric, które wcześniej korzystały z zestawu SDK Java usługi Service Fabric, aby używały zależności języka Java usługi Service Fabric z narzędzia Maven. Wykonaj kroki wymienione [tutaj](service-fabric-migrate-old-javaapp-to-use-maven.md), aby zapewnić działanie Twojej starszej aplikacji z narzędziem Maven.

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
