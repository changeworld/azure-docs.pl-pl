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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 2a51169fa74384778d3351d48055d05e387d0ea6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Samouczek: Wdrażanie aplikacji na platformie Azure oraz Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Potok hybrydowego ciągłej integracji/ciągłego dostarczania (CI/CD) umożliwia tworzenie, testowanie i wdrażanie aplikacji na wiele chmur.  W tym samouczku utworzysz środowisku próbki do:
 
> [!div class="checklist"]
> * Rozpocznij nową kompilację oparte na kodzie zatwierdzeń do repozytorium programu Visual Studio Team Services (VSTS).
> * Automatycznie Wdrażaj nowo zbudowany kodu globalnego Azure dla testów akceptacyjnych przez użytkowników.
> * Po kodzie testowania automatycznie wdraża aplikację Azure stosu.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Temat dostarczania hybrydowego kompilacji potoku

Ciągłość działania wdrożenia aplikacji, zabezpieczeń i niezawodności jest istotne dla Twojej organizacji i krytyczne dla zespołu deweloperów. Z potokiem CI/CD hybrydowe można skonsolidować z potoki w środowisku lokalnym i chmury publicznej. Możesz zmienić lokalizację bez przełączania aplikacji.

Takie podejście umożliwia również obsługa spójny zestaw narzędzi programistycznych. Narzędzia spójne w chmurze publicznej Azure oraz w lokalnym środowisku stosu Azure oznacza, że jest znacznie łatwiejsze do zaimplementowania CI/CD dev praktyki. Aplikacje i usługi wdrożone w Azure lub stos Azure są wymienne i tego samego kodu można uruchomić w dowolnej lokalizacji, korzystając z lokalnych i chmurze publicznej, funkcje i możliwości.

Dowiedz się więcej o:
 - [Co to jest ciągłej integracji?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Co to jest ciągłego dostarczania?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć kilka składników w miejscu do tworzenia potoku CI/CD hybrydowego. Potencjalnie trochę czasu, aby przygotować.
 
 - Partnera Azure OEM/sprzętu mogą wdrażać produkcji stosu Azure i wszyscy użytkownicy mogą wdrażać Azure stosu Development Kit (ASDK). 
 - Operator stosu Azure musi również wdrożenia usługi App Service, tworzyć plany i ofert Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016.

Jeśli masz już niektóre z tych składników, upewnij się, że spełniają one wymagania przed rozpoczęciem.

W tym temacie założono również, czy masz pewna znajomość Azure oraz Azure stosu. Jeśli chcesz dowiedzieć się więcej, aby kontynuować, należy koniecznie zaczynać się w tych tematach:


W tym samouczku również założono, że niektóre wiedzy Azure oraz Azure stosu. 

Jeśli chcesz dowiedzieć się więcej, przed kontynuowaniem, można uruchomić z poziomu tych tematach:
 - [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Kluczowe założenia Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

 - Utwórz [sieci Web aplikacji](https://docs.microsoft.com/azure/app-service/app-service-web-overview) na platformie Azure. Zanotuj URL nowej aplikacji sieci Web, które jest używane później.

Azure Stack
 - Użyj systemu Azure stosu zintegrowane lub wdrożyć Azure stosu Development Kit (ASDK) linki znajdują się poniżej:
    - Można znaleźć szczegółowe instrukcje dotyczące wdrażania ASDK na "[samouczek: Wdrażanie ASDK za pomocą Instalatora](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - Można zautomatyzować wiele etapów po wdrożeniu ASDK z następujący skrypt programu PowerShell, [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > Instalacja ASDK ma siedem godzin, aby zakończyć, więc odpowiednio zaplanować.

 - Wdrażanie [usługi aplikacji](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS usług Azure stosu. 
 - Utwórz [Plan/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku Azure stosu. 
 - Utwórz [dzierżawy subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku Azure stosu. 
 - Tworzenie aplikacji sieci Web w ramach subskrypcji dzierżawy. Zanotuj adres URL nowej aplikacji sieci Web dla później.
 - Wdróż maszynę wirtualną programu VSTS, nadal w ramach subskrypcji dzierżawy.
 - Windows Server 2016 maszynę Wirtualną za pomocą .NET 3.5 wymagane. Ta maszyna wirtualna zostanie utworzona na stosie Azure jako agenta kompilacji prywatnych. 

### <a name="developer-tools"></a>Narzędzia dla deweloperów

 - Utwórz [obszaru roboczego programu VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Proces tworzenia konta tworzy projekt o nazwie **MyFirstProject**.
 - [Zainstaluj program Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) i [logowania do programu VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Połącz się z projektem i [sklonować lokalnie](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > Konieczne będzie stosu Azure z obrazami prawidłowego zespoloną do uruchomienia (Windows Server i SQL), a następnie wdrożyć usługi aplikacji.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Przygotowanie kompilację prywatną i wersji agenta do integracji programu Visual Studio Team Services

### <a name="prerequisites"></a>Wymagania wstępne

Visual Studio Team Services (VSTS) służy do uwierzytelniania względem usługi Azure Resource Manager przy użyciu nazwy głównej usługi. VSTS możliwość udostępniania zasobów w subskrypcji platformy Azure stosu wymaga on współautora stanu.

Poniżej przedstawiono ogólne kroki, które trzeba skonfigurować tak, aby włączyć takie uwierzytelnianie:

1. Należy utworzyć nazwy głównej usługi lub istniejący mogą być używane.
2. Klucze uwierzytelniania trzeba utworzyć główną usługi.
3. Subskrypcji platformy Azure stos musi być sprawdzone za pomocą opartej na rolach kontroli dostępu, aby umożliwić główną nazwę usługi, część roli współautora.
4. Definicję usługi w VSTS musi zostać utworzony przy użyciu punktów końcowych stosu Azure, jak również informacje SPN.

### <a name="service-principal-creation"></a>Tworzenie głównej usługi

Zapoznaj się [Tworzenie nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instrukcje dotyczące tworzenia nazwy głównej usługi i wybierz aplikację sieci Web/interfejs API typu aplikacja.

### <a name="access-key-creation"></a>Tworzenie kluczy dostępu

Nazwy głównej usługi wymaga klucza uwierzytelniania, wykonaj czynności opisane w tej sekcji, aby wygenerować klucz.


1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Zanotuj wartość **identyfikator aplikacji**. Podczas konfigurowania punktu końcowego usługi w VSTS użyjesz tej wartości.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.
 
    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.
 
    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość, ponieważ później nie będzie można pobrać klucza. Należy podać **wartość klucza** wraz z Identyfikatorem aplikacji, aby zalogować się jako aplikację. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Pobieranie identyfikatora dzierżawy

W ramach konfiguracji punktu końcowego usługi, wymaga programu VSTS **identyfikator dzierżawcy** odpowiadający Twojej sygnatury stosu Azure została wdrożona do katalogu usługi AAD. Wykonaj kroki opisane w tej sekcji, aby zebrać identyfikator dzierżawcy.

1. Wybierz pozycję **Azure Active Directory**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Skopiuj **identyfikator katalogu**. Ta wartość jest Twoim identyfikatorem dzierżawy.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Przyznanie praw główna usługi wdrażania zasobów w subskrypcji Azure stosu 

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, które roli reprezentuje odpowiednich uprawnień dla aplikacji. Aby dowiedzieć się więcej o dostępnych ról, zobacz [RBAC: Built in Roles](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Na poziomie subskrypcji, grupy zasobów lub zasobów można ustawić zakresu. Uprawnienia są dziedziczone na niższe poziomy zakresu. Na przykład dodawanie aplikacji do roli czytnik dla grupy zasobów oznacza, że mogą odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. Przejdź na poziom zakresu, który chcesz przypisać aplikację do. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego możesz określić, grupy zasobów lub zasobu.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Wybierz **subskrypcji** (grupy zasobów lub zasobów) można przypisać do aplikacji.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Wybierz **(IAM) kontroli dostępu**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Wybierz pozycję **Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Wybierz rolę, którą chcesz przypisać do aplikacji. Poniższy obraz przedstawia **właściciela** roli.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcji. Aby znaleźć aplikacji, należy najpierw **Podaj nazwę** w polu wyszukiwania. Wybierz go.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Wybierz **zapisać** na zakończenie przypisanie roli. Zostanie wyświetlona aplikacja w listy użytkowników przypisanych do roli dla tego zakresu.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Azure opartej na rolach kontroli dostępu (RBAC) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można udzielić użytkownikom tylko takiego dostępu, jakiego potrzebują do wykonania swoich zadań. Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach, zobacz [zarządzania dostępem do zasobów subskrypcji Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Pule agenta programu VSTS

Zamiast zarządzania oddzielnie każdego agenta, możesz organizować agentów w pule agenta. Do puli agenta definiuje granicy udostępniania dla wszystkich agentów w tej puli. Pule agenta w VSTS, ograniczone do konta usługi VSTS; Dlatego możesz udostępniać puli agenta w projektach zespołowych. Więcej informacji oraz samouczek dotyczący sposobu tworzenia pul agenta programu VSTS, zobacz [tworzenia pul agenta i kolejek](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Dodaj osobisty token dostępu (PAWEŁ) Azure stosu

1. Zaloguj się do konta usługi VSTS i wybierz nazwę profilu konta.
2. Wybierz **Zarządzanie zabezpieczeń** do strony tworzenia tokenu dostępu.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Skopiuj tokenu.
    
    > [!Note]  
    > Uzyskiwanie informacji o tokenie. Nie będzie można wyświetlić ponownie, po zakończeniu tego ekranu. 
    
    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Zainstaluj agenta kompilacji programu VSTS na stosie Azure hostowany serwer kompilacji

1.  Połącz z serwerem kompilacji wdrożone na tym hoście stosu Azure.

2.  Pobierz i wdróż agenta kompilacji jako usługi przy użyciu osobistej (PAWEŁ) tokenu dostępu, a następnie uruchom jako konto administratora maszyny Wirtualnej.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Przejdź do folderu agenta kompilacji wyodrębnione. Uruchom **run.cmd** pliku z wiersza polecenia o podniesionych uprawnieniach. 

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Po zakończeniu pracy run.cmd folder z wyodrębniona zawartość powinna wyglądać następująco:

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Możesz teraz przeglądać agenta w folderze programu VSTS.

## <a name="endpoint-creation-permissions"></a>Uprawnienia do tworzenia punktu końcowego

Użytkownicy mogą tworzyć punkty końcowe, więc VSTO kompilacji można wdrażać aplikacje usługi Azure stosu. VSTS łączy agenta kompilacji, który następnie łączy się z usługą Azure stosu. 

![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Na **ustawienia** menu, wybierz opcję **zabezpieczeń**.
2. W **grup programu VSTS** liście z lewej strony, wybierz opcję **twórców punktu końcowego**. 

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Na **członków** wybierz opcję **+ Dodaj**. 

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Wpisz nazwę użytkownika i wybierz użytkownika z listy.
5. Kliknij przycisk **zapisać zmiany**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. W **grup programu VSTS** liście z lewej strony, wybierz opcję **Administratorzy punktu końcowego**.
7. Na **członków** wybierz opcję **+ Dodaj**.
8. Wpisz nazwę użytkownika i wybierz użytkownika z listy.
9. Kliknij przycisk **zapisać zmiany**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Agent kompilacji w stosie Azure uzyskuje instrukcje z programu VSTS, który następnie przekazuje informacje o punkcie końcowym komunikacji z stosu Azure. 
    
    VSTS połączenia stosu Azure jest gotowy.

## <a name="develop-your-application"></a>Tworzenie aplikacji

Konfigurowanie hybrydowego CI/CD, aby wdrożyć aplikację sieci Web na platformie Azure oraz Azure stosu i automatycznie Wypchnij zmiany do obu chmury.

> [!Note]  
> Konieczne będzie stosu Azure z obrazami prawidłowego zespoloną do uruchomienia (Windows Server i SQL), a następnie wdrożyć usługi aplikacji. Zapoznaj się z dokumentacją usługi aplikacji "wymagania wstępne" Azure stosu Operator wymagania.

### <a name="add-code-to-vsts-project"></a>Dodawanie kodu do projektu programu VSTS

1. Zaloguj się do programu Visual Studio przy użyciu konta z uprawnieniami Tworzenie projektu na stosie Azure.

    Hybrydowe CI/CD można zastosować do kodu aplikacji i infrastruktury kodu. Użyj [szablonów usługi Azure Resource Manager, takich jak web ](https://azure.microsoft.com/resources/templates/) kodu aplikacji z programu VSTS w obu chmurach.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klonowanie repozytorium** , tworząc i otworzyć domyślnej aplikacji sieci web.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Tworzenie wdrożenia aplikacji niezależne sieci web dla usług aplikacji w obu chmurach

1. Edytuj **WebApplication.csproj** plik: Wybierz **Runtimeidentifier** i Dodaj `win10-x64.` uzyskać więcej informacji, zobacz [niezależne wdrożenia](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacji .

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Sprawdź kod do programu VSTS przy użyciu programu Team Explorer.

3. Upewnij się, że został sprawdzony kod aplikacji w Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Tworzenie definicji kompilacji

1. Zaloguj się do programu VSTS, aby potwierdzić możliwość tworzenia definicji kompilacji.

2. Dodaj **- r win10-x64** kodu. Jest to konieczne wyzwolić wdrożenie niezależne z platformą .net Core. 

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Uruchom kompilację. [Niezależne wdrożenia kompilacji](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na stosie Azure i usługi Azure.

### <a name="using-an-azure-hosted-agent"></a>Przy użyciu platformy Azure hostowanej agenta

Przy użyciu agenta hostowanej w VSTS jest to wygodny sposób do tworzenia i wdrażania aplikacji sieci web. Konserwacja i uaktualnienia, automatycznie są wykonywane przez Microsoft Azure, umożliwiające programowanie ciągłych, nieprzerwaną, testowania i wdrażania.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Konfigurowanie procesu wdrażania ciągłego (CD) oraz zarządzanie nimi

Visual Studio Team Services (VSTS) i Team Foundation Server (TFS) podaj potoku wysokiej można konfigurować i łatwą w obsłudze dla wersji w wielu środowiskach, takich jak projektowanie, przemieszczania, odpowiedzi na pytania i środowisk produkcyjnych; w tym wymagające zatwierdzenia na określonych etapach.

### <a name="create-release-definition"></a>Utwórz definicję zlecenia

![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Wybierz  **\[ +]** można dodać nowej wersji w obszarze **kartę wersje** na stronie kompilacji i wydania usługi VSO.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Zastosuj **wdrażanie usługi aplikacji Azure** szablonu.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\103.png)

3. W menu rozwijanym artefaktu Dodaj **dodać artefaktu** dla aplikacji w chmurze Azure kompilacji.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Na karcie potoku, wybierz **fazy**, **zadań** połączyć środowiska i ustaw wartości środowiska chmury Azure.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Ustaw **Nazwa środowiska** i wybierz pozycję Azure **subskrypcji** dla punktu końcowego w chmurze Azure.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\106.png)

6. W obszarze Nazwa środowiska Ustaw wymaganych **nazwę usługi aplikacji Azure**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Wprowadź **hostowane VS2017** obszarze kolejki agenta dla środowiska hostowane w chmurze Azure.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Wdrażanie usługi Azure App Service, wybierz menu poprawne **pakietu lub folderu** dla środowiska. Wybierz **OK** do **lokalizacji folderu**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Zapisz wszystkie zmiany i wróć do **potoku wersji**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Dodaj **nowe artefaktu** Wybieranie kompilacji dla aplikacji Azure stosu.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Dodaj jeden więcej stosowania środowiska **wdrażanie usługi aplikacji Azure**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Nazwa nowego środowiska **stosu Azure**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Znajdź środowiska Azure stosu w obszarze **zadań** kartę.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Wybierz **subskrypcji** dla punktu końcowego stosu Azure.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Ustaw nazwę aplikacji sieci web Azure stosu jako **nazwę usługi aplikacji**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Wybierz **stosu Azure agenta**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\118.png)

17. W obszarze wdrożenia usługi Azure App Service Wybierz prawidłową sekcji **pakietu lub folderu** dla środowiska. Wybierz OK, aby **lokalizacji folderu**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Karcie zmiennej dodać zmienną o nazwie **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, ustaw jej wartość jako **true**oraz zakres do **stosu Azure**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Wybierz **ciągłe** wdrożenia wyzwolenia ikonę w obu artefakty i Włącz kontynuuje wyzwalacza wdrożenia.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Wybierz **przed wdrożeniem** warunki ikonę platformy azure stosu środowiska i ustawić wyzwalacz **po wydaniu**.

21. Zapisz wszystkie zmiany.

> [!Note]  
> Niektóre ustawienia dla tych zadań mogą automatycznie zdefiniowano jako [zmiennych środowiskowych](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) utworzenia definicji wersji z szablonu. Nie można modyfikować tych ustawień w ustawieniach zadań; Zamiast tego należy wybrać nadrzędnego elementu środowiska, aby zmienić te ustawienia.

## <a name="create-a-release"></a>Utwórz zlecenia

Po wykonaniu zmian do definicji wersji, jest godzinę, aby rozpocząć wdrażanie. Aby to zrobić, należy utworzyć wydania z definicji wersji. Zlecenia mogą być tworzone automatycznie. na przykład wyzwalacz ciągłego wdrażania jest ustawiana w definicji wersji. Oznacza to, że zmodyfikowanie kodu źródłowego rozpocznie nowej kompilacji i od tej nowej wersji. Jednak w tej sekcji utworzysz nową wersję ręcznie.

1. Otwórz **wersji** listy rozwijanej liście i wybierz polecenie **utworzyć wersję**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Wprowadź opis wersji, sprawdź poprawne artefakty są zaznaczone, a następnie wybierz **Utwórz**. Po kilku chwilach transparent pojawi się wskazujący, że utworzono nową wersję. Wybierz link (Nazwa wersji).

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. Strona podsumowania wersji otwiera ze szczegółowymi informacjami o wersji. W **środowisk** sekcji pojawi się stan wdrożenia w środowisku "QA" zmiany z "W toku" do "Powodzenie" i, w tym momencie transparent pojawia się wskazujący, że wersja obecnie oczekuje na zatwierdzenie. Podczas wdrażania w środowisku jest w stanie oczekiwania lub nie powiodła się, niebieski (i), jest wyświetlana ikona informacji. Wskazuje to, aby wyświetlić okno podręczne zawierające przyczyny.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\202.png)

Innych widoków, takich jak lista wersjach również wyświetlana ikona wskazująca zatwierdzania oczekuje. Ikona pokazuje okno podręczne zawierające nazwę środowiska i szczegółowe po wskazaniu go. Dzięki temu można łatwo dla administratora zobaczyć, których wersji oczekują na zatwierdzenie, jak również ogólny postęp wszystkich wersji.

### <a name="monitor-and-track-deployments"></a>Monitorowanie i śledzenie wdrożeń

W tej sekcji, zobaczysz, jak można monitorować i śledzić wdrożeń — w tym przykładzie do dwóch witryn sieci Web usługi aplikacji Azure — wersja utworzony w poprzedniej sekcji.

1. Na stronie Podsumowanie wersji, wybierz **dzienniki** łącza. Podczas wdrażania, ta strona przedstawia na żywo dziennika z agenta, a w okienku po lewej stronie wskazanie stan każdej operacji w procesie wdrożenia dla każdego środowiska.

    Wybierz ikonę w **akcji** kolumny o zatwierdzenie przed wdrożeniem lub po wdrożeniu wyświetlić szczegółowe informacje o tym, kto zatwierdzenia (lub odrzucenia) wdrożenia i komunikat podane przez tego użytkownika.

2. Po zakończeniu wdrażania cały plik dziennika jest wyświetlany w okienku po prawej stronie. Wybierz jedno z **przetworzyć kroki** w okienku po lewej stronie, aby wyświetlić tylko dziennika zawartość pliku dla tego kroku. Ułatwia to śledzenia i debugowania poszczególnych części ogólnej wdrożenia. Można również pobrać osobnych plikach dziennika lub zip wszystkich plików dziennika z ikon i łączy na stronie.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Otwórz **Podsumowanie** kartę, aby wyświetlić szczegóły ogólnego wydania. Zawiera on szczegóły kompilacji i środowisk, który został wdrożony — wraz z stan wdrożenia i inne informacje o wersji.

4. Zaznacz wszystkie **łącza środowiska** aby zobaczyć więcej szczegółów o istniejących i oczekujące wdrożenia do tego w danym środowisku. Te strony służy do sprawdzenia, czy tę samą kompilację wdrożoną w obu środowiskach.

5. Otwórz **wdrożonych aplikacji produkcyjnej** w Twojej przeglądania. Na przykład dla usługi aplikacji Azure witryny sieci Web, z adresu URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
