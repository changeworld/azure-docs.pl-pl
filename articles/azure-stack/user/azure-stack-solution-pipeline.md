---
title: Wdrażanie aplikacji na platformie Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażać aplikacje na platformę Azure i usługi Azure Stack z potokiem ciągłej integracji/ciągłego Dostarczania hybrydowego.
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: febdb2e3ae4432c36ca839f81ba7a1d333df1a2f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952005"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Samouczek: Wdrażanie aplikacji na platformie Azure i usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak wdrożyć aplikację na platformę Azure i usługi Azure Stack przy użyciu hybrydowego potoku ciągłej integracji/ciągłego dostarczania (CI/CD).

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> * Zainicjuj nowej kompilacji, w oparciu o zatwierdzenia kodu do repozytorium usługi DevOps platformy Azure.
> * Automatyczne wdrażanie aplikacji do globalnej platformy Azure dla testy odbiorcze użytkowników.
> * Po kodzie przekazuje, testowanie, można automatycznie wdrażać aplikację do usługi Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Zalety dostarczania hybrydowego tworzenie potoku

Ciągłość działania, bezpieczeństwa i niezawodności są kluczowe elementy wdrożenia aplikacji. Te elementy są istotne dla Twojej organizacji i mają kluczowe znaczenie dla zespołu deweloperów. Potok ciągłej integracji/ciągłego Dostarczania hybrydowego można konsolidować swoje potoki kompilacji w środowisku lokalnych i chmurze publicznej. Model dostarczania hybrydowego umożliwia także zmiana lokalizacji wdrożenia bez konieczności zmieniania aplikacji.

To podejście hybrydowe inne korzyści:

* Możesz zachować spójny zestaw narzędzi programistycznych w lokalnym środowisku usługi Azure Stack i chmury publicznej platformy Azure.  Ze wspólnego zestawu narzędzie ułatwia do zaimplementowania ciągłej integracji/ciągłego Dostarczania wzorców i praktyk.
* Aplikacje i usługi wdrożone na platformie Azure lub usługi Azure Stack są wymienne, a ten sam kod można uruchomić w dowolnej lokalizacji. Można korzystać w środowisku lokalnym i chmurą publiczną, funkcji i możliwości.

Aby dowiedzieć się więcej na temat ciągłej integracji i ciągłego wdrażania:

* [Co to jest ciągła Integracja?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Co to jest ciągłe dostarczanie?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi hybrydowej aplikacje. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć składniki w miejscu, aby utworzyć potok ciągłej integracji/ciągłego Dostarczania hybrydowego. Następujące składniki zajmie trochę czasu przygotowania:

* Partner OEM/sprzętowych platformy Azure można wdrażać produkcji usługi Azure Stack. Wszyscy użytkownicy, można wdrożyć usługi Azure Stack Development Kit (ASDK).
* Operator usługi Azure Stack, musi również: Wdrażanie usługi App Service, tworzyć plany i oferty, Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016.

>[!NOTE]
>Jeśli masz już niektóre z tych składników, wdrożone, upewnij się, że są spełnione wszystkie wymagania, przed rozpoczęciem tego samouczka.

W tym samouczku założono, że niektóre podstawową wiedzę na temat platformy Azure i usługi Azure Stack. Aby dowiedzieć się więcej, przed rozpoczęciem tego samouczka, przeczytaj następujące artykuły:

* [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Kluczowe pojęcia usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Wymagania systemu Azure

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tworzenie [aplikacja sieci Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) na platformie Azure. Zanotuj adres URL aplikacji sieci Web, należy go używać w tym samouczku.

### <a name="azure-stack-requirements"></a>Wymagania dotyczące usługi Azure Stack

* Użyj to system zintegrowany z usługi Azure Stack lub wdrażania usługi Azure Stack Development Kit (ASDK). Aby wdrożyć ASDK:
    * [Samouczek: Wdrażanie ASDK za pomocą Instalatora](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) zawiera instrukcje wdrożenia są szczegółowo.
    * Użyj [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) skrypt programu PowerShell do automatyzowania czynności po wdrożeniu ASDK.

    > [!Note]
    > Instalacji ASDK trwa około siedmiu godziny zakończenia, więc odpowiednio zaplanować.

 * Wdrażanie [usługi App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) usługi PaaS do usługi Azure Stack.
 * Tworzenie [planu/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w usłudze Azure Stack.
 * Tworzenie [dzierżawy subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w usłudze Azure Stack.
 * Tworzenie aplikacji sieci Web w ramach subskrypcji dzierżawy. Zanotuj nowy adres URL aplikacji sieci Web dla później użyć.
 * Wdróż maszynę wirtualną systemu Windows Server 2012 w ramach subskrypcji dzierżawy. Użyjesz tego serwera jako serwera kompilacji i uruchamiania usługi DevOps platformy Azure.
* Udostępnij obrazu systemu Windows Server 2016 platformy .NET 3.5 dla maszyny wirtualnej (VM). Ta maszyna wirtualna zostanie utworzona na usługi Azure Stack jako funkcja agentów kompilacji prywatnych.

### <a name="developer-tool-requirements"></a>Wymagania dotyczące narzędzi dla deweloperów

* Tworzenie [usługom DevOps platformy Azure w obszarze roboczym](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). Proces rejestracji tworzy projekt o nazwie **MyFirstProject**.
* [Instalowanie programu Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) i [logowania do usługi Azure DevOps Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Połącz się z projektem i [go sklonować lokalnie](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Środowisko usługi Azure Stack musi poprawne obrazów, zespolone do uruchomienia systemu Windows Server i programu SQL Server. Musi mieć również wdrożony w usłudze App Service.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Przygotowanie agenta prywatnego potoki platformy Azure do integracji z usługami DevOps platformy Azure

### <a name="prerequisites"></a>Wymagania wstępne

Usługi Azure DevOps jest uwierzytelniany względem usługi Azure Resource Manager przy użyciu jednostki usługi. Musi mieć usługi platformy Azure DevOps **Współautor** roli inicjowania obsługi administracyjnej zasobów w subskrypcji usługi Azure Stack.

W poniższych krokach opisano, co to są wymagane do skonfigurowania uwierzytelniania:

1. Tworzenie jednostki usługi, lub użyj istniejącej nazwy głównej usługi.
2. Utwórz klucze uwierzytelniania jednostki usługi.
3. Sprawdzanie poprawności subskrypcji platformy Azure Stack za pośrednictwem roli-Based Access Control, aby zezwolić na główną nazwę usługi (SPN) jako część roli "Współautor".
4. Utwórz nową definicję usługi w usłudze Azure Services DevOps za pomocą punktów końcowych usługi Azure Stack i nazwę SPN informacji.

### <a name="create-a-service-principal"></a>Tworzenie jednostki usługi

Zapoznaj się [tworzenia nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instrukcjami, aby utworzyć nazwę główną usługi. Wybierz **aplikacji sieci Web/interfejsu API** dla typu aplikacji lub [używania skryptu programu PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) zgodnie z opisem w artykule [tworzenia połączenia z usługą Azure Resource Manager za pomocą istniejącej usługi główna ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Jeśli skrypt umożliwia utworzenie punktu końcowego usługi Azure Stack usługi Azure Resource Manager, musisz przekazać **- azureStackManagementURL** parametru i **- environmentName** parametru. Na przykład:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Utwórz klucz dostępu

Jednostki usługi wymaga klucza uwierzytelniania. Wykonaj następujące kroki, aby wygenerować klucz.

1. W oknie **Rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

    ![Wybierz aplikację](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Zanotuj wartość **identyfikator aplikacji**. Podczas konfigurowania punktu końcowego usługi w usługach infrastruktury DevOps platformy Azure, użyje tej wartości.

    ![Identyfikator aplikacji](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

    ![Edytowanie ustawień aplikacji](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

    ![Konfigurowanie ustawień klucza](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Podaj opis klucza, a następnie ustaw czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

    ![Opis klucza i czas trwania](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Po zapisaniu klucza, klucz **wartość** jest wyświetlana. Skopiuj tę wartość, ponieważ ta wartość nie może pobrać później. Należy podać **wartość klucza** identyfikatorem aplikacji do logowania w aplikacji. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

    ![Klucz wartość](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Pobieranie Identyfikatora dzierżawy

W ramach konfiguracji punktu końcowego usługi, usługom DevOps platformy Azure wymaga **identyfikator dzierżawy** odnosi się do katalogu usługi AAD, który jest wdrażana sygnatury usługi Azure Stack. Wykonaj następujące kroki, aby uzyskać identyfikator dzierżawy.

1. Wybierz pozycję **Azure Active Directory**.

    ![Usługa Azure Active Directory dla dzierżawy](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Aby uzyskać identyfikator dzierżawy, wybierz pozycję **Właściwości** dla swojej dzierżawy usługi Azure AD.

    ![Wyświetl właściwości dzierżawy](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Skopiuj **identyfikator katalogu**. Ta wartość jest Twoim identyfikatorem dzierżawy.

    ![Identyfikator katalogu](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Przyznanie praw jednostki usługi do wdrażania zasobów w subskrypcji usługi Azure Stack

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która reprezentuje najlepsze uprawnień dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: Built in Roles](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów oznacza, że może odczytywać, grupy zasobów i wszystkie jej zasoby.

1. Przejdź do poziomu zakresu, którą chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**.

    ![Wybierz subskrypcje](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. W **subskrypcji**, wybierz program Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. W programie Visual Studio Enterprise, wybierz **kontrola dostępu (IAM)**.

    ![Kontrola dostępu (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Wybierz pozycję **Dodaj**.

    ![Add](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. W **Dodaj uprawnienia**, wybierz rolę, którą chcesz przypisać do aplikacji. W tym przykładzie **właściciela** roli.

    ![Rola właściciela](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcjach. Aby znaleźć aplikację, należy podać jego nazwę w **wybierz** pola za pomocą funkcji wyszukiwania. Wybierz aplikację.

    ![Wynik wyszukiwania aplikacji](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Wybierz **Zapisz** zakończenie przypisanie roli. Zostanie wyświetlona aplikacja na liście Użytkownicy przypisani do roli dla tego zakresu.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Usługa Azure opartej na rolach kontrola dostępu (RBAC) umożliwia szczegółowe zarządzanie dostępem dla platformy Azure. Za pomocą funkcji RBAC, można kontrolować poziom dostępu, których użytkownicy potrzebują do wykonywania ich zadań. Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach, zobacz [zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Pule agentów Services — Azure DevOps

Zamiast na zarządzaniu oddzielnie każdego agenta, możesz organizować agentów w pulach agentów. Pula agentów definiuje granicy udostępniania dla wszystkich agentów w puli. W usługach Azure DevOps pul agentów są ograniczone do organizacji usługom DevOps platformy Azure, co oznacza, że mogą udostępniać pulę agentów w projektach. Aby dowiedzieć się więcej o pulach agentów, zobacz [Tworzenie pul agentów i kolejki](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Dodaj osobistego tokenu dostępu (PAT) dla usługi Azure Stack

Utwórz osobisty Token dostępu na dostęp do usług infrastruktury DevOps platformy Azure.

1. Zaloguj się do Twojej organizacji usługom DevOps platformy Azure i wybierz nazwę profilu organizacji.

2. Wybierz **zarządzania zabezpieczeniami** do strony tworzenia tokenu dostępu.

    ![Logowanie użytkowników](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Wybierz projekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Dodaj osobisty token dostępu](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Utwórz token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Skopiuj token.

    > [!Note]
    > Zapisywanie informacji o tokenie. Te informacje nie są przechowywane i nie był już wyświetlany ponownie gdy pozostawisz strony sieci web.

    ![Osobisty token dostępu](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Zainstaluj agenta kompilacji usługom DevOps platformy Azure w usłudze Azure Stack hostowany serwer kompilacji

1. Połącz na serwerze kompilacji, na którym została wdrożona na hoście usługi Azure Stack.
2. Pobieranie i wdrażanie agenta kompilacji, jako usługa korzystająca z osobistej (PAT) token dostępu i uruchomiona za pomocą konta administratora maszyny Wirtualnej.

    ![Pobierz agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Przejdź do folderu dla agenta kompilacji wyodrębnione. Uruchom **config.cmd** pliku w wierszu polecenia z podwyższonym poziomem uprawnień.

    ![Agent kompilacji wyodrębnione](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Zarejestruj agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Po zakończeniu config.cmd folder agenta kompilacji jest aktualizowana dodatkowych plików. Folder z wyodrębniona zawartość powinna wyglądać następująco:

    ![Aktualizacja folder agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Możesz zobaczyć agenta w folderze usługom DevOps platformy Azure.

## <a name="endpoint-creation-permissions"></a>Uprawnienia do tworzenia punktu końcowego

Tworząc punktów końcowych, kompilacja programu Visual Studio Online (Narzędzia VSTO) można wdrożyć usługi Azure App Service do usługi Azure Stack. Usługi Azure DevOps łączy się agenta kompilacji, który jest połączony z usługą Azure Stack.

![NorthwindCloud przykładową aplikację w VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Zaloguj się do narzędzi VSTO dla programów, a następnie przejdź do strony Ustawienia aplikacji.
2. Na **ustawienia**, wybierz opcję **zabezpieczeń**.
3. W **grup usługi DevOps platformy Azure**, wybierz opcję **punktu końcowego dla twórców**.

    ![Punkt końcowy NorthwindCloud dla twórców](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Na **członków** zaznacz **Dodaj**.

    ![Dodaj członka](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. W **dodanie użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.
6. Wybierz **Zapisz zmiany**.
7. W **grup usługi DevOps platformy Azure** listy wybierz **Administratorzy punktu końcowego**.

    ![Administratorzy NorthwindCloud punktu końcowego](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Na **członków** zaznacz **Dodaj**.
9. W **dodanie użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.
10. Wybierz **Zapisz zmiany**.

Teraz, gdy informacje o punkcie końcowym istnieje, usługom DevOps platformy Azure do połączenia usługi Azure Stack jest gotowe do użycia. Agenta kompilacji w usłudze Azure Stack pobiera instrukcje z usługom DevOps platformy Azure, a następnie agenta umożliwia przekazywanie informacji o punkcie końcowym komunikacji z usługą Azure Stack.
## <a name="create-an-azure-stack-endpoint"></a>Tworzenie punktu końcowego usługi Azure Stack

Możesz wykonać instrukcje w [tworzenia połączenia z usługą Azure Resource Manager za pomocą istniejącej usługi głównej ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) artykuł, aby utworzyć połączenie usługi z istniejącej usługi głównej i użyj następującego mapowania:

- Środowisko: AzureStack
- Adres URL środowiska: Mniej więcej tak `https://management.local.azurestack.external`
- Identyfikator subskrypcji: Identyfikator subskrypcji użytkownika na podstawie usługi Azure Stack
- Nazwa subskrypcji: Nazwa subskrypcji użytkownika z usługi Azure Stack
- Identyfikator klienta nazwy głównej usługi: identyfikator podmiotu zabezpieczeń z [to](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) sekcję w tym artykule.
- Klucz jednostki usługi: klucz z tego samego artykułu (lub hasło, jeśli używany jest skrypt).
- Identyfikator dzierżawy: Identyfikator dzierżawy możesz pobrać następujące instrukcji w [uzyskanie Identyfikatora dzierżawy](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).

Teraz, gdy jest tworzony punkt końcowy, usługa VSTS do połączenia usługi Azure Stack jest gotowe do użycia. Agent kompilacji w usłudze Azure Stack pobiera instrukcje z usługi VSTS, a następnie agenta umożliwia przekazywanie informacji o punkcie końcowym komunikacji z usługą Azure Stack.

![Agenta kompilacji](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Tworzenie kompilacji aplikacji

W tej części samouczka należy:

* Dodaj kod do projektu usługom DevOps platformy Azure.
* Tworzenie wdrożenia aplikacji sieci web niezależna.
* Skonfiguruj proces ciągłego wdrażania

> [!Note]
 > Środowisko usługi Azure Stack musi poprawne obrazów, zespolone do uruchomienia systemu Windows Server i programu SQL Server. Musi mieć również wdrożony w usłudze App Service. Przejrzyj dokumentację usługi App Service sekcji "Wymagania wstępne" dla usługi Azure Stack Operator wymagań.

Hybrydowe, ciągłą Integrację/ciągłe dostarczanie może dotyczyć zarówno kod aplikacji, jak i kodem infrastruktury. Użyj [szablonami usługi Azure Resource Manager, takimi jak web ](https://azure.microsoft.com/resources/templates/) kodu aplikacji z usługom DevOps platformy Azure można wdrażać w obu chmurach.

### <a name="add-code-to-an-azure-devops-services-project"></a>Dodawanie kodu do projektu usługom DevOps platformy Azure

1. Zaloguj się do usługi Azure DevOps Services przy użyciu organizacji, która ma uprawnień do tworzenia projektu w usłudze Azure Stack. Następny zrzut ekranu pokazuje, jak połączyć się z projektem HybridCICD.

    ![Połącz z projektem](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Sklonuj repozytorium** przez tworzenie i otwieranie domyślną aplikację sieci web.

    ![Klonuj repozytorium](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Tworzenie wdrożenia aplikacji niezależne sieci web dla usług App Service w obu chmurach

1. Edytuj **WebApplication.csproj** pliku: Wybierz **Runtimeidentifier** , a następnie dodaj `win10-x64.` Aby uzyskać więcej informacji, zobacz [niezależna wdrożenia](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacja.

    ![Konfigurowanie Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Użyj programu Team Explorer, aby zaewidencjonować kod w usługom DevOps platformy Azure.

3. Upewnij się, że kod aplikacji zaewidencjonowaniu usługom DevOps platformy Azure.

### <a name="create-the-build-pipeline"></a>Tworzenie potoku kompilacji

1. Zaloguj się do usługi Azure DevOps Services przy użyciu organizacji, w której można utworzyć potoku kompilacji.

2. Przejdź do **kompilacji Web Distributed** strony dla projektu.

3. W **argumenty**, Dodaj **- r dla systemu win10-x64** kodu. Jest to wymagane, aby wyzwolić wdrożenie niezależna za pomocą.Net Core.

    ![Dodaj argument kompilacji potoku](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Uruchom kompilację. [Kompilacji wdrożenia niezależna](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na platformie Azure i usługi Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Użyj platformy Azure hostowanej agenta kompilacji

Przy użyciu hostowanego agenta kompilacji w usługom DevOps platformy Azure jest to wygodny sposób kompilowania i wdrażania aplikacji sieci web. Konserwacja agentów i uaktualnień, automatycznie są wykonywane przez Microsoft Azure, co umożliwia cyklu rozwoju ciągłe i nieprzerwany.

### <a name="configure-the-continuous-deployment-cd-process"></a>Skonfiguruj proces ciągłego wdrażania (polecenie CD)

Azure usługom DevOps i Team Foundation Server (TFS) zapewniają wysoce konfigurowalne i łatwe w zarządzaniu potoku wydań w wielu środowiskach programowania, przejściowe, zapewniania jakości (QA) i produkcja. Ten proces może obejmować wymagające zatwierdzenia poszczególnych etapów cyklu życia aplikacji.

### <a name="create-release-pipeline"></a>Tworzenie potoku tworzenia wersji

Tworzenie potoku tworzenia wersji jest procesu kompilacji w ostatnim kroku Twojej aplikacji. Ten potok wersji służy do tworzenia wersji i wdrażania kompilacji.

1. Zaloguj się do usługi DevOps platformy Azure i przejdź do **potoki Azure** dla Twojego projektu.
2. Na **wersji** zaznacz  **\[ +]** a następnie wybierz **Tworzenie definicji wydania**.

   ![Tworzenie potoku tworzenia wersji](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Na **wybierz szablon**, wybierz **wdrożenie usługi aplikacji Azure**, a następnie wybierz pozycję **Zastosuj**.

    ![Zastosuj szablon](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Na **Dodawanie artefaktu**, z **źródło (definicja kompilacji)** menu rozwijanego wybierz aplikację kompilacji w chmurze platformy Azure.

    ![Dodawanie artefaktu](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Na **potoku** zaznacz **fazy 1**, **1 zadanie** połączyć **przeglądać zadania w środowisku**.

    ![Zadania widoku potoku](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Na **zadania** wprowadź platformy Azure jako **Nazwa środowiska** i wybierz EP Web handlowców AzureCloud z **subskrypcji platformy Azure** listy rozwijanej.

    ![Ustawianie zmiennych środowiskowych](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Wprowadź **nazwa usługi Azure app service**, czyli "northwindtraders" na następnym zrzucie ekranu.

    ![Nazwa usługi App service](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Faza agenta, wybierz **hostowany program VS2017** z **kolejki agentów** listy rozwijanej.

    ![Hostowany agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. W **wdrożenia usługi Azure App Service**, wybierz prawidłowe **pakietu lub folderu** dla środowiska.

    ![Wybierz pakiet lub folderu](media\azure-stack-solution-hybrid-pipeline\108.png)

10. W **Wybieranie pliku lub folderu**, wybierz opcję **OK** do **lokalizacji**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Zapisz wszystkie zmiany i wróć do **potoku**.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\110.png)

12. Na **potoku** zaznacz **Dodawanie artefaktu**i wybierz polecenie **NorthwindCloud Traders — statek** z **źródło (definicja kompilacji)** listy rozwijanej.

    ![Dodawanie nowych artefaktu](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Na **wybierz szablon**, dodać innego środowiska. Wybierz **wdrożenie usługi aplikacji Azure** , a następnie wybierz **Zastosuj**.

    ![Wybierz szablon](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Wprowadź "Usługi Azure Stack" jako **Nazwa środowiska**.

    ![Nazwa środowiska](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Na **zadania** karcie Znajdź i zaznacz usługi Azure Stack.

    ![Środowisko platformy Azure Stack](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Z **subskrypcji platformy Azure** listy rozwijanej wybierz "AzureStack statku handlowców EP" dla punktu końcowego usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Wprowadź nazwę aplikacji sieci web usługi Azure Stack jako **nazwa usługi App service**.

    ![Nazwa usługi App service](media\azure-stack-solution-hybrid-pipeline\116.png)

18. W obszarze **wybór agenta**, masz do wyboru "AzureStack - bDouglas jako część" **kolejki agentów** listy rozwijanej.

    ![Wybierz agenta](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Dla **wdrożenia usługi Azure App Service**, wybierz prawidłowe **pakietu lub folderu** dla środowiska. Na **Wybieranie pliku lub folderu**, wybierz opcję **OK** folderu **lokalizacji**.

    ![Wybierz pakiet lub folderu](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Zatwierdź lokalizacji](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Na **zmiennej** kartę, Znajdź zmienną o nazwie **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Ustaw wartość zmiennej **true**i ustaw jego zakres **usługi Azure Stack**.

    ![Skonfigurować zmienną](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Na **potoku** zaznacz **wyzwalacz ciągłego wdrażania** ikonę artefaktu NorthwindCloud Traders — Web i zestawu **wyzwalacz ciągłego wdrażania** do **Włączone**.  Te same czynności wykonasz artefaktu "statku handlowców NorthwindCloud".

    ![Ustaw wyzwalacz ciągłego wdrażania](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Dla środowiska Azure Stack, wybierz **warunki przed wdrożeniem** ikony Ustaw wyzwalacz, **po wydaniu**.

    ![Ustaw warunki przed wdrożeniem wyzwalacza](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Zapisz wszystkie zmiany.

> [!Note]
> Niektóre ustawienia dla zadań podrzędnych zwolnienia może automatycznie zdefiniowano jako [zmienne środowiskowe](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) podczas tworzenia potoku tworzenia wersji z szablonu. Nie można zmodyfikować te ustawienia w obszarze Ustawienia zadania. Można jednak edytować te ustawienia w środowisku elementów nadrzędnych.

## <a name="create-a-release"></a>Tworzenie wydania

Teraz, że zostały wykonane modyfikacje z potokiem wersji, jest czas, aby rozpocząć wdrażanie. Aby to zrobić, należy utworzyć wersję z potoku wersji. Wydanie, które mogą zostać utworzone automatycznie. na przykład wyzwalacz ciągłego wdrażania znajduje się w potoku tworzenia wersji. Oznacza to, że modyfikowania kodu źródłowego, rozpocznie nową kompilację i przy jego użyciu, nowa wersja. Jednak w tej sekcji utworzysz nową wersję ręcznie.

1. Na **potoku** otwartą kartą **wersji** listy rozwijanej i wybierz polecenie **utworzyć wersję**.

    ![Tworzenie wydania](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Wprowadź opis wersji, sprawdź, czy poprawny artefakty są zaznaczone, a następnie wybierz **Utwórz**. Po kilku chwilach zostanie wyświetlony transparent informujący, że utworzono nową wersję i wydanie będzie wyświetlana jako link. Wybierz link, aby wyświetlić na stronie podsumowania wydania.

    ![Transparent tworzenia wersji](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Stronie podsumowania wydania dla przedstawia szczegółowe informacje o wersji. Na poniższym zrzucie ekranu "Release-2" **środowisk** sekcji przedstawiono **stan wdrożenia** dla platformy Azure jako "W toku", a także stan dla usługi Azure Stack to "Powodzenie". Gdy stan wdrożenia dla środowiska platformy Azure przechodzi "Powodzenie", zostanie wyświetlony transparent, co oznacza, że wydanie gotowe do zatwierdzenia. Gdy wdrożenie jest operacją oczekującą lub zakończyło się niepowodzeniem, niebieski **(i)** jest wyświetlana ikona informacji. Umieść kursor nad ikonę Aby wyświetlić okno podręczne, które zawiera przyczynę opóźnienia lub niepowodzenia.

    ![Stronie podsumowania wydania](media\azure-stack-solution-hybrid-pipeline\202.png)

Inne widoki, takie jak lista zwalnia, będą również wyświetlane ikony, który wskazuje, że trwa oczekiwanie na zatwierdzenie. Okno podręczne dla tej ikony zawiera nazwy środowiska i szczegółowe informacje związane z wdrażaniem. Może to być proste, można znaleźć ogólny postęp wydań i zobaczyć, które wersje oczekują na zatwierdzenie przez administratora.

### <a name="monitor-and-track-deployments"></a>Monitorowanie i śledzenie wdrożeń

W tej sekcji przedstawiono, jak monitorować i Śledź wszystkie wdrożenia. Wydania do wdrażania dwie witryny sieci Web usługi Azure App Services zapewnia dobrym przykładem.

1. Na stronie podsumowania "Release-2", wybierz **dzienniki**. Podczas wdrażania ta strona zawiera dziennika na żywo z agenta. W okienku po lewej stronie pokazuje stan każdej operacji w ramach wdrożenia dla każdego środowiska.

    Możesz wybrać ikonę osoby **akcji** kolumny o zatwierdzenie przed wdrożeniem lub po wdrożeniu, aby sprawdzić, kto zatwierdzenia (lub odrzucenia) wdrożenia i wiadomości podano ich.

2. Po zakończeniu wdrażania cały plik dziennika jest wyświetlany w okienku po prawej stronie. Możesz wybrać dowolny **kroku** w okienku po lewej stronie, aby wyświetlić plik dziennika dla jednego kroku, takie jak "Zainicjować zadanie". Możliwość wyświetlania poszczególne dzienniki ułatwia śledzenie i debugowanie części całe wdrożenie. Możesz również **Zapisz** kroku można znaleźć w pliku dziennika lub **pobrać wszystkie dzienniki w formie pliku zip**.

    ![Dziennikom wydań](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Otwórz **Podsumowanie** kartę, aby wyświetlić ogólne informacje o wersji. Ten widok przedstawia szczegółowe informacje o kompilacji, środowisk, którymi została wdrożona do, stan wdrożenia i inne informacje o wersji.

4. Wybierz link środowiska (**Azure** lub **usługi Azure Stack**) aby wyświetlić informacje o istniejących i oczekujące wdrożenia w danym środowisku. Widoki te można użyć w prosty sposób, aby sprawdzić, czy tę samą kompilację została wdrożona w obu środowiskach.

5. Otwórz **wdrożyć aplikację w środowisku produkcyjnym** w przeglądarce. Na przykład dla witryny sieci Web usługi Azure App Services, otwórz adres URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
