---
title: Wdrażanie aplikacji na platformie Azure oraz Azure stosu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacje na platformie Azure oraz Azure stosu z potokiem CI/CD hybrydowego.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604356"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Samouczek: Wdrażanie aplikacji na platformie Azure oraz Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Dowiedz się, jak wdrożyć aplikację na platformie Azure oraz stos Azure za pomocą potoku hybrydowego ciągłej integracji/ciągłego dostarczania (CI/CD).

W tym samouczku utworzysz środowisku próbki do:

> [!div class="checklist"]
> * Rozpocznij nową kompilację oparte na kodzie zatwierdzeń do repozytorium programu Visual Studio Team Services (VSTS).
> * Automatycznie Wdrażaj aplikację Azure globalne dla testów akceptacyjnych przez użytkowników.
> * Po kodzie przekazuje testowania, automatycznie Wdróż aplikację Azure stosu.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Korzyści wynikające z dostarczania hybrydowego kompilacji potoku

Ciągłość działania, bezpieczeństwa i niezawodności są kluczowe elementy wdrożenia aplikacji. Te elementy są istotne dla Twojej organizacji i krytyczne dla zespołu deweloperów. Potok CI/CD hybrydowego umożliwia konsolidowanie z potoków kompilacji w środowisku lokalnym i chmury publicznej. Model dostarczania hybrydowego umożliwia także zmiana lokalizacji wdrożenia bez zmieniania aplikacji.

Inne korzyści wynikające z używania rozwiązanie hybrydowe są:

* Możesz zachować spójny zestaw narzędzi do tworzenia między środowiska Azure stosu lokalnych i chmurze publicznej Azure.  Ze wspólnego zestawu narzędzi ułatwia wdrażanie elementu konfiguracji/CD wzorców i rozwiązań.
* Aplikacje i usługi wdrożone w Azure lub stos Azure są wymienne, i tego samego kodu można uruchomić w dowolnej lokalizacji. Można korzystać z lokalnych i chmurze publicznej, funkcje i możliwości.

Aby dowiedzieć się więcej o konfiguracji i CD:

* [Co to jest ciągłej integracji?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Co to jest ciągłego dostarczania?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć składniki w celu tworzenia potoku CI/CD hybrydowego. Następujące składniki czasu przygotowania:

* Partnera Azure OEM/sprzętu można wdrożyć produkcyjnych Azure stosu. Wszyscy użytkownicy, można wdrożyć Azure stosu Development Kit (ASDK).
* Operator stosu Azure musi również: Wdrażanie usługi App Service, tworzyć plany i ofert Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016.

>[!NOTE]
>Jeśli masz już niektóre z tych składników wdrożone, upewnij się, że spełniają wszystkie wymagania przed rozpoczęciem tego samouczka.

W tym samouczku założono, że niektóre podstawową wiedzę na temat platformy Azure i stosu Azure. Aby dowiedzieć się więcej, przed rozpoczęciem tego samouczka, przeczytaj następujące artykuły:

* [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Kluczowe założenia Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Wymagania systemu Azure

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Utwórz [sieci Web aplikacji](https://docs.microsoft.com/azure/app-service/app-service-web-overview) na platformie Azure. Należy zwrócić uwagę na adres URL aplikacji sieci Web, należy użyć go w samouczku.

### <a name="azure-stack-requirements"></a>Wymagania dotyczące usługi Azure stosu

* Użyj systemu Azure stosu zintegrowane lub wdrożyć Azure stosu Development Kit (ASDK). Aby wdrożyć ASDK:
    * [Samouczek: Wdrażanie ASDK za pomocą Instalatora](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) zawiera instrukcje wdrożenia są szczegółowo.
    * Użyj [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) skrypt programu PowerShell, aby automatyzować czynności po wdrożeniu ASDK.

    > [!Note]
    > Instalacja ASDK ma około siedmiu godzin ukończenia, więc odpowiednio zaplanować.

 * Wdrażanie [usługi aplikacji](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS usług Azure stosu.
 * Utwórz [Plan/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure stosu.
 * Utwórz [dzierżawy subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w stosie Azure.
 * Tworzenie aplikacji sieci Web w ramach subskrypcji dzierżawy. Zanotuj adres URL nowej aplikacji sieci Web dla później.
 * Wdróż maszynę wirtualną programu VSTS w ramach subskrypcji dzierżawy.
* Podaj obrazu systemu Windows Server 2016 z .NET 3.5 dla maszyny wirtualnej (VM). Ta maszyna wirtualna zostanie utworzona na stosie Azure jako agent kompilacji prywatnych.

### <a name="developer-tool-requirements"></a>Wymagania dotyczące narzędzia Projektant

* Utwórz [obszaru roboczego programu VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Proces tworzenia konta tworzy projekt o nazwie **MyFirstProject**.
* [Zainstaluj program Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) i [logowania do programu VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Połącz z projektem i [ją sklonować lokalnie](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Środowiska Azure stos musi poprawne obrazów zespoloną do uruchomienia systemu Windows Server i programu SQL Server. Musi mieć również wdrożyć usługi aplikacji.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Przygotowanie kompilację prywatną i wersji agenta do integracji programu Visual Studio Team Services

### <a name="prerequisites"></a>Wymagania wstępne

Visual Studio Team Services (VSTS) służy do uwierzytelniania względem usługi Azure Resource Manager przy użyciu nazwy głównej usługi. VSTS musi mieć **współautora** roli do udostępniania zasobów w subskrypcji platformy Azure stosu.

W poniższych krokach opisano, co jest wymagane do skonfigurowania uwierzytelniania:

1. Tworzenie nazwy głównej usługi, lub użyj istniejącej nazwy głównej usługi.
2. Tworzenie kluczy uwierzytelniania główną usługi.
3. Sprawdzanie poprawności subskrypcji stosu Azure za pomocą opartej na rolach kontrola dostępu umożliwia główna nazwa usługi (SPN) jako część roli współautora.
4. Utwórz nową definicję usługi w VSTS przy użyciu punktów końcowych stosu Azure i SPN informacji.

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Zapoznaj się [Tworzenie nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instrukcjami, aby utworzyć nazwy głównej usługi, a następnie wybierz **API aplikacji sieci Web** typu aplikacja.

### <a name="create-an-access-key"></a>Tworzenie klucza dostępu

Nazwy głównej usługi wymaga klucza dla uwierzytelniania. Wykonaj następujące kroki, aby wygenerować klucz.

1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

    ![Wybierz aplikację](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Zanotuj wartość **identyfikator aplikacji**. Podczas konfigurowania punktu końcowego usługi w VSTS użyjesz tej wartości.

    ![Identyfikator aplikacji](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

    ![Edytowanie ustawień aplikacji](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

    ![Konfigurowanie ustawień klucza](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Podaj opis dla klucza, a następnie ustaw długość klucza. Po zakończeniu wybierz pozycję **Zapisz**.

    ![Opis klucza i czas trwania](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Po zapisaniu klucz **wartość** jest wyświetlany. Ponieważ ta wartość nie może pobrać później, należy skopiować tę wartość. Należy podać **wartość klucza** wraz z Identyfikatorem aplikacji, aby zalogować się jako aplikację. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

    ![Klucz wartości](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Uzyskanie Identyfikatora dzierżawy

W ramach konfiguracji punktu końcowego usługi, wymaga programu VSTS **identyfikator dzierżawcy** odpowiadający sygnatury sieci Azure stosu jest wdrożony w katalogu usługi AAD. Wykonaj następujące kroki, aby uzyskać identyfikator dzierżawy.

1. Wybierz pozycję **Azure Active Directory**.

    ![Azure Active Directory dla dzierżawcy](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.

    ![Wyświetl właściwości dzierżawy](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Skopiuj **identyfikator katalogu**. Ta wartość jest Twoim identyfikatorem dzierżawy.

    ![Identyfikator katalogu](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Przyznanie praw główna usługi wdrażania zasobów w subskrypcji Azure stosu

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, roli reprezentuje najlepsze uprawnień dla aplikacji. Aby dowiedzieć się więcej o dostępnych ról, zobacz [RBAC: Built in Roles](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Na poziomie subskrypcji, grupy zasobów lub zasobów można ustawić zakresu. Uprawnienia są dziedziczone na niższe poziomy zakresu. Na przykład dodawanie aplikacji do roli czytnik dla grupy zasobów oznacza, że mogą odczytywać, grupy zasobów i wszystkie jego zasobów.

1. Przejdź na poziom zakresu, który chcesz przypisać aplikację do. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**.

    ![Wybierz subskrypcje](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. W **subskrypcji**, wybierz pozycję Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. W programie Visual Studio Enterprise, wybierz **kontroli dostępu (IAM)**.

    ![Kontrola dostępu (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Wybierz pozycję **Dodaj**.

    ![Add](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. W **dodać uprawnienia**, wybierz rolę, który ma zostać przypisany do aplikacji. W tym przykładzie **właściciela** roli.

    ![Rola właściciela](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcji. Aby znaleźć aplikacji, musisz podać jego nazwę **wybierz** pól do wyszukania go. Wybierz aplikację.

    ![Wynik wyszukiwania aplikacji](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Wybierz **zapisać** na zakończenie przypisanie roli. Zostanie wyświetlona aplikacja w listy użytkowników przypisanych do roli dla tego zakresu.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Azure opartej na rolach kontroli dostępu (RBAC) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Za pomocą RBAC, można kontrolować poziom dostępu, które użytkownicy muszą wykonać swoje zadania. Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach, zobacz [zarządzania dostępem do zasobów subskrypcji Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pule agenta programu VSTS

Zamiast zarządzania każdego agenta oddzielnie, można organizować agentów w pule agenta. Do puli agenta definiuje granicy udostępniania dla wszystkich agentów w tej puli. W programu VSTS pule agenta ograniczone do konta usługi VSTS, co oznacza, że można udostępnić puli agenta w projektach zespołowych. Aby dowiedzieć się więcej na temat pul agenta, zobacz [tworzenia pul agenta i kolejek](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Dodaj osobisty Token dostępu (PAWEŁ) Azure stosu

Utwórz osobisty Token dostępu do usługi VSTS.

1. Zaloguj się do konta usługi VSTS i wybierz nazwę profilu konta.
2. Wybierz **Zarządzanie zabezpieczeń** do strony tworzenia tokenu dostępu.

    ![Logowanie użytkownika](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Wybierz projekt zespołowy](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Dodaj osobisty token dostępu](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Utwórz token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Skopiuj tokenu.

    > [!Note]
    > Zapisywanie informacji o tokenie. Te informacje nie są przechowywane i nie będzie wyświetlany ponownie kiedy opuścić stronę sieci web.

    ![Osobisty token dostępu](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Zainstaluj agenta kompilacji programu VSTS na stosie Azure hostowany serwer kompilacji

1. Połącz z serwerem kompilacji wdrożone na tym hoście stosu Azure.
2. Pobierz i wdróż agenta kompilacji jako usługi przy użyciu osobistej (PAWEŁ) tokenu dostępu, a następnie uruchom jako konto administratora maszyny Wirtualnej.

    ![Pobierz agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Przejdź do folderu na agenta kompilacji wyodrębnione. Uruchom **run.cmd** pliku z wiersza polecenia o podniesionych uprawnieniach.

    ![Agent wyodrębnionego kompilacji](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Zarejestruj agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Po zakończeniu pracy run.cmd folder agenta kompilacji został zaktualizowany o dodatkowych plików. Folder z wyodrębniona zawartość powinna wyglądać następująco:

    ![Folder aktualizacji agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Widać agenta w folderze programu VSTS.

## <a name="endpoint-creation-permissions"></a>Uprawnienia do tworzenia punktu końcowego

Tworząc punkty końcowe kompilacji programu Visual Studio Online (środowisko VSTO) można wdrożyć aplikacji usługi Azure stosu Azure. VSTS łączy agenta kompilacji, która połączy się stosu Azure.

![NorthwindCloud przykładową aplikację w VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Zaloguj się do VSTO i przejdź do strony ustawień aplikacji.
2. Na **ustawienia**, wybierz pozycję **zabezpieczeń**.
3. W **grup programu VSTS**, wybierz pozycję **twórców punktu końcowego**.

    ![Twórcy NorthwindCloud punktu końcowego](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Na **członków** wybierz opcję **Dodaj**.

    ![Dodawanie elementu członkowskiego](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. W **dodać użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.
6. Wybierz **zapisać zmiany**.
7. W **grup programu VSTS** listy, wybierz **Administratorzy punktu końcowego**.

    ![Administratorzy NorthwindCloud punktu końcowego](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Na **członków** wybierz opcję **Dodaj**.
9. W **dodać użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.
10. Wybierz **zapisać zmiany**.

Teraz, gdy informacje o punkcie końcowym istnieje, VSTS stosu Azure połączenie jest gotowe do użycia. Agenta kompilacji w stosie Azure otrzymuje instrukcje od VSTS, a następnie przekazuje informacje o punkcie końcowym komunikacji z stosu Azure przez agenta.

![Agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Opracowywanie kompilację aplikacji

W tej części samouczka możesz:

* Dodawanie kodu do projektu usługi VSTS.
* Tworzenie wdrożenia aplikacji sieci web niezależne.
* Konfigurowanie ciągłego wdrażania procesu

> [!Note]
 > Środowiska Azure stos musi poprawne obrazów zespoloną do uruchomienia systemu Windows Server i programu SQL Server. Musi mieć również wdrożyć usługi aplikacji. Zapoznaj się z dokumentacją usługi aplikacji "wymagania wstępne" Azure stosu Operator wymagania.

Hybrydowe CI/CD można zastosować do kodu aplikacji i infrastruktury kodu. Użyj [szablonów usługi Azure Resource Manager, takich jak web ](https://azure.microsoft.com/resources/templates/) kodu aplikacji z programu VSTS do wdrożenia w obu chmurach.

### <a name="add-code-to-a-vsts-project"></a>Dodawanie kodu do projektu programu VSTS

1. Zaloguj się do programu VSTS przy użyciu konta z uprawnieniami Tworzenie projektu na stosie Azure. Dalej zrzucie ekranu pokazano, jak połączyć się z projektem HybridCICD.

    ![Połącz z projektem](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klonowanie repozytorium** , tworząc i otworzyć domyślnej aplikacji sieci web.

    ![Klonowanie repozytorium](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Tworzenie wdrożenia aplikacji niezależne sieci web dla usług aplikacji w obu chmurach

1. Edytuj **WebApplication.csproj** plik: Wybierz **Runtimeidentifier** , a następnie dodaj `win10-x64.` uzyskać więcej informacji, zobacz [niezależne wdrożenia](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacja.

    ![Skonfiguruj Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Użyj programu Team Explorer, aby sprawdzić kod do usługi VSTS.

3. Upewnij się, że kod aplikacji został wyewidencjonowany do programu Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Tworzenie definicji kompilacji

1. Zaloguj się do programu VSTS z kontem, które można utworzyć definicji kompilacji.
2. Przejdź do **kompilacji komputerze Web** strony dla projektu.

3. W **argumenty**, Dodaj **- r win10-x64** kodu. Jest to wymagane, aby wyzwolić wdrożenie niezależne z platformą .net Core.

    ![Dodaj definicję kompilacji argumentu](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Uruchom kompilację. [Niezależne wdrożenia kompilacji](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na stosie Azure i usługi Azure.

### <a name="use-an-azure-hosted-build-agent"></a>Użyj platformy Azure hostowanej agenta kompilacji

Używanie hostowanego agenta kompilacji w VSTS jest to wygodny sposób tworzenia i wdrażania aplikacji sieci web. Konserwacji agenta i uaktualnień automatycznie są wykonywane przez Microsoft Azure, umożliwiający cyklu programowanie ciągły i przerwana.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurowanie procesu wdrażania ciągłego (CD)

Visual Studio Team Services (VSTS) i Team Foundation Server (TFS) podaj potoku wysokiej można konfigurować i łatwą w obsłudze dla wersji w wielu środowiskach, takich jak projektowanie, przemieszczania, jakości (QA) i produkcji. Ten proces może obejmować wymagające zatwierdzenia określone etapy cyklu życia aplikacji.

### <a name="create-release-definition"></a>Utwórz definicję zlecenia

Tworzenie definicji wersji jest ostatnim krokiem w aplikacji proces kompilacji. Ta definicja wersji służy do tworzenia zlecenia i wdrożyć kompilację.

1. Zaloguj się do programu VSTS i przejdź do **kompilacji i wydania** dla projektu.
2. Na **wersje** wybierz opcję  **\[ +]** , a następnie wybierz **Tworzenie wersji definicji**.

   ![Utwórz definicję zlecenia](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Na **wybierz szablon**, wybierz **wdrażanie usługi aplikacji Azure**, a następnie wybierz **Zastosuj**.

    ![Zastosuj szablon](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Na **artefaktu Dodaj**, z **źródła (definicja kompilacji)** menu rozwijanego wybierz aplikacji w chmurze Azure kompilacji.

    ![Dodawanie artefaktu](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Na **potoku** wybierz opcję **fazy 1**, **zadań 1** połączyć **wyświetlić zadania środowiska**.

    ![Zadania widoku potoku](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Na **zadania** wprowadź Azure jako **Nazwa środowiska** i wybierz EP Traders Web AzureCloud z **subskrypcji platformy Azure** listy rozwijanej.

    ![Ustawianie zmiennych środowiskowych](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Wprowadź **nazwę usługi aplikacji Azure**, czyli "northwindtraders" dalej zrzucie ekranu.

    ![Nazwa usługi aplikacji](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Na etapie agenta, wybierz **hostowane VS2017** z **kolejki agenta** listy rozwijanej.

    ![Obsługiwany agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. W **wdrożenia usługi Azure App Service**, wybierz prawidłową **pakietu lub folderu** dla środowiska.

    ![Wybierz pakiet lub folderu](media\azure-stack-solution-hybrid-pipeline\108.png)

10. W **wybierz plik lub Folder**, wybierz pozycję **OK** do **lokalizacji**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Zapisz wszystkie zmiany i wróć do **potoku**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\110.png)

12. Na **potoku** wybierz opcję **artefaktu Dodaj**i wybierz polecenie **NorthwindCloud Traders-statku** z **źródła (Tworzenie definicji)** listy rozwijanej.

    ![Dodaj nowy artefaktów](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Na **wybierz szablon**, dodać innego środowiska. Wybierz **wdrażanie usługi aplikacji Azure** , a następnie wybierz **Zastosuj**.

    ![Wybierz szablon](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Wprowadź "Azure stosu" jako **Nazwa środowiska**.

    ![Nazwa środowiska](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Na **zadania** , Znajdź i wybierz stosu Azure.

    ![Środowiska platformy Azure stosu](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Z **subskrypcji platformy Azure** listy rozwijanej wybierz "AzureStack Traders statku EP" dla punktu końcowego stosu Azure.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Wprowadź nazwę aplikacji sieci web Azure stosu jako **nazwę usługi aplikacji**.

    ![Nazwa usługi aplikacji](media\azure-stack-solution-hybrid-pipeline\116.png)

18. W obszarze **wybór agenta**, wybierz "AzureStack - bDouglas Fir" z **kolejki agenta** listy rozwijanej.

    ![Pobierz agenta](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Dla **wdrożenia usługi Azure App Service**, wybierz prawidłową **pakietu lub folderu** dla środowiska. Na **wybierz plik lub Folder**, wybierz pozycję **OK** folderu **lokalizacji**.

    ![Wybierz pakiet lub folderu](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Zatwierdzanie lokalizacji](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Na **zmiennej** karcie, Znajdź zmiennej o nazwie **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Ustaw wartość zmiennej **true**i ustawić jej zakres **stosu Azure**.

    ![Skonfigurować zmienną](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Na **potoku** wybierz opcję **wyzwalacza ciągłe wdrażanie** ikona artefaktu NorthwindCloud Traders-Web oraz zestaw **wyzwalacza ciągłe wdrażanie** do **Włączone**.  Tak samo postąpić dla artefaktu "statku Traders NorthwindCloud".

    ![Zestaw wyzwalacza ciągłe wdrażanie](media\azure-stack-solution-hybrid-pipeline\121.png)

22. W środowisku Azure stosu wybierz **warunki przed wdrożeniem** ikona ustawić wyzwalacz **po wydaniu**.

    ![Zestaw warunków przed wdrożeniem wyzwalacza](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Zapisz wszystkie zmiany.

> [!Note]
> Niektóre ustawienia dla zadań zlecenia może automatycznie zdefiniowano jako [zmiennych środowiskowych](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) utworzenia definicji wersji z szablonu. Te ustawienia nie można zmodyfikować ustawień zadania. Można jednak edytować tych ustawień w nadrzędnych elementów środowiska.

## <a name="create-a-release"></a>Utwórz zlecenia

Po wykonaniu zmian do definicji wersji, jest godzinę, aby rozpocząć wdrażanie. Aby to zrobić, należy utworzyć wydania z definicji wersji. Zlecenia mogą być tworzone automatycznie. na przykład wyzwalacz ciągłego wdrażania jest ustawiana w definicji wersji. Oznacza to, że zmodyfikowanie kodu źródłowego rozpocznie nowej kompilacji i od tej nowej wersji. Jednak w tej sekcji utworzysz nową wersję ręcznie.

1. Na **potoku** kartę, otwórz **wersji** listy rozwijanej liście i wybierz polecenie **utworzyć wersję**.

    ![Utwórz zlecenia](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Wprowadź opis wersji, sprawdź, czy wybrano poprawną artefaktów, a następnie wybierz **Utwórz**. Po kilku chwilach transparent pojawi się wskazujący, że utworzono nową wersję, a Nazwa wersji jest wyświetlana jako łącze. Wybierz łącze, aby wyświetlić stronę podsumowania wersji.

    ![Transparent tworzenia zlecenia](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Wersja stronę podsumowania przedstawia szczegółowe informacje o wersji. W poniższych Przechwytywanie ekranu dla "Wersji 2" **środowisk** sekcji przedstawiono **stan wdrożenia** dla platformy Azure jako "W toku", a także stan stosu Azure jest "powiodło się". Po zmianie stanu wdrażania w środowisku platformy Azure do "Powodzenie", jest wyświetlany transparent wskazująca, że wersja jest gotowe do zatwierdzenia. Podczas wdrożenia jest w stanie oczekiwania lub nie powiodła się, niebieski **(i)** jest wyświetlana ikona informacji. Umieść kursor nad ikonę, aby wyświetlić okno podręczne, które zawiera przyczynę opóźnienia lub niepowodzenia.

    ![Strona podsumowania zlecenia](media\azure-stack-solution-hybrid-pipeline\202.png)

Inne widoki, takich jak lista zwalnia, będą również wyświetlane ikonę, która wskazuje, że trwa oczekiwanie na zatwierdzenie. Oknie podręcznym dla ta ikona jest wyświetlana nazwa środowiska i więcej szczegółów dotyczących wdrożenia. To proste, aby administrator Zobacz ogólny postęp wersji i zobaczyć, które wersje oczekują na zatwierdzenie.

### <a name="monitor-and-track-deployments"></a>Monitorowanie i śledzenie wdrożeń

W tej sekcji przedstawiono, jak można monitorować i śledzić wszystkich wdrożeń. Wersja do wdrażania dwie witryny sieci Web usługi aplikacji Azure oferuje dobrym przykładem.

1. Na stronie Podsumowanie "W wersji 2", wybierz **dzienniki**. Podczas wdrażania ta strona przedstawia na żywo dziennika z agenta. W okienku po lewej stronie pokazuje stan każdej operacji w ramach wdrożenia dla każdego środowiska.

    Można wybrać ikonę osoby **akcji** kolumny o zatwierdzenie przed wdrożeniem lub po wdrożeniu, który zatwierdzenia (lub odrzucenia) wdrożenia, i ich dostarczonej wiadomości.

2. Po zakończeniu wdrożenia całego pliku dziennika są wyświetlane w okienku po prawej stronie. Można wybrać dowolny **krok** w okienku po lewej stronie, aby wyświetlić plik dziennika dla jednego kroku, takie jak "Zainicjować zadanie". Widoczność poszczególne dzienniki ułatwia śledzenia i debugowania części ogólnej wdrożenia. Możesz również **zapisać** kroku, w pliku dziennika lub **Pobierz wszystkie dzienniki jako zip**.

    ![Dzienniki zlecenia](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Otwórz **Podsumowanie** kartę, aby wyświetlić ogólne informacje o wersji. Ten widok przedstawia szczegółowe informacje o kompilacji, środowiskach, które zostały wdrożone, stan wdrożenia i inne informacje o wersji.

4. Wybierz łącze środowiska (**Azure** lub **stosu Azure**) aby wyświetlić informacje o istniejących i oczekujące wdrożenia do określonego środowiska. Widoki te można użyć w prosty sposób, aby sprawdzić, czy tę samą kompilację wdrożoną w obu środowiskach.

5. Otwórz **wdrożonych aplikacji produkcyjnej** w przeglądarce. Na przykład dla witryny sieci Web usługi aplikacji Azure, otwórz adres URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
