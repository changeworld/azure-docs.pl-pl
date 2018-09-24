---
title: Tworzenie rozwiązań skalowania wielu chmur przy użyciu platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć rozwiązania skalowania wielu chmur przy użyciu platformy Azure.
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
ms.openlocfilehash: 1f3dcc7da3e91d3805cae805422778978d0187c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971335"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Samouczek: Tworzenie rozwiązania skalowania wielu chmur przy użyciu platformy Azure

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak utworzyć rozwiązanie wielu chmur, aby zapewnić wyzwalanych ręcznie proces przełączenie z usługi Azure Stack hostowanych aplikacji sieci web, na platformie Azure hostowanych aplikacji sieci web przy użyciu skalowania automatycznego za pomocą usługi traffic manager, zapewniając elastyczność i skalowalność chmury narzędzia w warunkach obciążenia.

W ramach tego wzorca dzierżawy nie może być gotowa do uruchomienia aplikacji w chmurze publicznej. Jednak może nie być ekonomicznie dla firm zachować pojemność wymagana w ich w środowisku lokalnym, do obsługi skoków zapotrzebowania na aplikację. Dzierżawie może zająć dzięki elastyczności chmury publicznej z rozwiązaniem w środowisku lokalnym.

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> - Utwórz aplikację sieci web z wieloma węzłami.
> - Konfigurowanie i zarządzanie procesem ciągłe wdrażanie (CD).
> - Publikowanie aplikacji sieci web do usługi Azure Stack.
> - Utwórz wydanie.
> - Dowiedz się monitorować i śledzić wdrożenia.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi aplikacje hybrydowe. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

-   Subskrypcja platformy Azure. W razie potrzeby utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

- Azure Stack zintegrowany System lub wdrożenia usługi Azure Stack Development Kit.
    - Możesz znaleźć instrukcje dotyczące instalowania usługi Azure Stack w [Zainstaluj zestaw Azure Stack Development Kit](/articles/azure-stack/asdk/asdk-install).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Ta instalacja może wymagać kilka godzin.

-   Wdrażanie [usługi App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) usługi PaaS do usługi Azure Stack.

-   [Tworzenie planu/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku usługi Azure Stack.

-   [Utwórz subskrypcję dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku usługi Azure Stack.

-   Tworzenie aplikacji sieci Web w ramach subskrypcji dzierżawy. Zanotuj nowy adres URL aplikacji sieci Web dla później użyć.

-   Wdróż maszynę wirtualną usługi VSTS w ramach subskrypcji dzierżawy.

-   Istnieje maszyna wirtualna 2016 serwera systemu Windows a .NET 3.5 wymagany. Ta maszyna wirtualna zostanie utworzona w ramach subskrypcji dzierżawy w usłudze Azure Stack jako funkcja agentów kompilacji prywatnych.

-   [Windows Server 2016 z obrazu maszyny Wirtualnej programu SQL 2017](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) jest dostępna w witrynie Azure Marketplace stosu. Jeśli ten obraz nie jest dostępne, pracy z operatorem stosu platformy Azure, aby upewnić się, że została dodana do środowiska.

## <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia

### <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Kluczowy składnik wielu chmur, skalowanie jest możliwość tworzenia natychmiastowej, na żądanie skalowania od publicznego i w obrębie lokalnej infrastruktury chmury, potwierdzające spójne i niezawodne usługi, zgodnie z ustalonym przez żądanie.

### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Upewnij się, lokalnie wdrożonych aplikacji skonfigurowanymi pod kątem wysokiej dostępności za pośrednictwem lokalnego sprzętu konfigurację i wdrażanie oprogramowania.

### <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania

Rozwiązanie w chmurze między zapewnia płynne zarządzanie i znajomy interfejs między środowiskami. Program PowerShell jest zalecana w przypadku zarządzania dla wielu platform.

## <a name="cross-cloud-scaling"></a>Skalowanie wielu chmur

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Uzyskaj domenę niestandardową i konfigurowanie systemu DNS

Zaktualizuj plik strefy DNS dla domeny. Usługa Azure AD zweryfikuje własności nazwy domeny niestandardowej. Użyj [system DNS Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure/Office 365/zewnętrzną DNS rekordów w obrębie platformy Azure, lub dodanie wpisu DNS u [różnych rejestratora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Zarejestruj domenę niestandardową publicznych rejestrze.

2.  Zaloguj się do rejestratora nazw domen dla domeny. Może być konieczne administrator zatwierdzone aktualizacje DNS. 

3.  Zaktualizuj plik strefy DNS dla domeny, dodając wpis DNS udostępniony przez usługę Azure AD. (Wpis DNS nie wpłyną na routing poczty lub zachowania hostingu w sieci web.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Utwórz domyślną aplikację sieci web z wieloma węzłami w usłudze Azure Stack

Konfigurowanie hybrydowego ciągłej integracji i ciągłego wdrażania (CI/CD) do wdrożenia aplikacji internetowej platformy Azure i usługi Azure Stack i automatyczne Wypchnij zmiany do obu chmur.

> [!Note]  
> Usługa Azure Stack przy użyciu prawidłowego obrazów zespolone do uruchomienia (system Windows Server i SQL) i wdrożenie usługi App Service nie są wymagane. Zapoznaj się z dokumentacją usługi App Service "[przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](/articles/azure-stack/azure-stack-app-service-before-you-get-started)" sekcji dla usługi Azure Stack operatora.

### <a name="add-code-to-visual-studio-team-services-project"></a>Dodaj kod do Visual Studio Team Services — projekt

1. Zaloguj do programu Visual Studio Team Services (VSTS) przy użyciu konta które ma uprawnień do tworzenia projektu w usłudze VSTS~~.~~

    Hybrydowe, ciągłą Integrację/ciągłe dostarczanie może dotyczyć zarówno kod aplikacji, jak i kodem infrastruktury. Użyj [szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/) dla obu chmur prywatnych i hostowanych rozwoju.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image1.JPG)

2. **Sklonuj repozytorium** przez tworzenie i otwieranie domyślną aplikację sieci web.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Tworzenie wdrożenia aplikacji niezależne sieci web dla usług App Service w obu chmurach

1.  Edytuj **WebApplication.csproj** pliku. Wybierz **Runtimeidentifier** i Dodaj **win10 x64**. (Zobacz [wdrożenia Self-contained](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacji.) 

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image3.png)

2.  Zaewidencjonuj kod do usługi VSTS przy użyciu programu Team Explorer.

3.  Upewnij się, że kod aplikacji została sprawdzona w Visual Studio Team Services.

## <a name="create-the-build-definition"></a>Utwórz definicję kompilacji

1. Zaloguj się do usługi VSTS, aby upewnić się, możliwość tworzenia definicji kompilacji.

2. Dodaj **- r dla systemu win10-x64** kodu. Jest to konieczne wyzwolić wdrożenie niezależna za pomocą.Net Core.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image4.png)

3. Uruchom kompilację. [Kompilacji wdrożenia niezależna](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na platformie Azure i usługi Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Użyj platformy Azure hostowany agent

Użycie hostowany agent w usłudze VSTS jest to wygodny sposób, aby tworzyć i wdrażać aplikacje sieci web. Konserwacja i aktualizacje automatyczne są wykonywane przez Microsoft Azure, umożliwiając ciągłe, nieprzerwany tworzenia, testowania i wdrażania.

### <a name="manage-and-configure-the-cd-process"></a>Zarządzanie i konfigurowanie proces ciągłego Dostarczania

Visual Studio Team Services i Team Foundation Server (TFS) zapewniają wysoce konfigurowalne i łatwe w zarządzaniu potoku dla wersji w wielu środowiskach, takich jak rozwój, przejściowe, odpowiedzi na pytania i środowisk produkcyjnych; w tym wymagające zatwierdzenia określonych etapów.

## <a name="create-release-definition"></a>Tworzenie definicji wydania

![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image5.png)

1.  Wybierz **oraz** przycisk, aby dodać nowe wydanie w ramach **karcie wersje** na stronie kompilacji i wydania programu VSO.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image6.png)

2. Zastosuj szablon wdrażania usługi Azure App Service.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image7.png)

3. W obszarze Dodawanie artefaktu Dodaj artefakt kompilacji aplikacji w chmurze platformy Azure.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image8.png)

4. Na karcie potoku wybierz **fazy, zadanie** połączyć środowiska i ustaw wartości środowiska chmury platformy Azure.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image9.png)

5. Ustaw **Nazwa środowiska** i wybierz pozycję Azure **subskrypcji** dla punktu końcowego w chmurze platformy Azure.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image10.png)

6. W obszarze Nazwa środowiska, ustaw wymagane **nazwa usługi Azure app service**.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image11.png)

7. Wprowadź **hostowany program VS2017** w ramach kolejki agentów dla środowiska hostowane w chmurze platformy Azure.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image12.png)

8. Wdrażanie usługi Azure App Service, wybierz menu prawidłowe **pakietu lub folderu** dla środowiska. Wybierz **OK** do **lokalizacji folderu**.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image13.png)

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image14.png)

9. Zapisz wszystkie zmiany i wróć do **potoku tworzenia wersji**.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image15.png)

10. Dodaj nowe artefaktu, wybierając kompilacji dla aplikacji usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image16.png)

11. Dodanie jednego środowiska więcej stosowanie wdrożenie usługi aplikacji Azure.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image17.png)

12. Nazwa nowego środowiska usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image18.png)

13. Znajdź środowiska usługi Azure Stack w obszarze **zadań** kartę.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image19.png)

14. Wybierz subskrypcję dla punktu końcowego usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image20.png)

15. Ustaw nazwę aplikacji sieci web usługi Azure Stack jako nazwa usługi App service.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image21.png)

16. Wybierz agenta usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image22.png)

17. W ramach wdrożenia usługi Azure App Service wybierz prawidłowe sekcji **pakietu lub folderu** dla środowiska. Wybierz **OK** do lokalizacji folderu.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image23.png)

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image24.png)

18. Na karcie Zmienne Dodaj zmienną o nazwie `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, ustaw dla niego wartość jako **true**oraz zakres do usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image25.png)

19. Wybierz **ciągłe** ikona wyzwalacza wdrożenia zarówno w przypadku artefaktów, jak i Włącz **kontynuuje** wyzwalacza wdrażania.

    ![Tekst alternatywny](media\azure-stack-solution-cloud-burst\image26.png)

20. Wybierz **przed wdrożeniem** ikonę warunków w środowisku usługi Azure Stack i ustawić wyzwalacz na **po wydaniu.**

21. Zapisz wszystkie zmiany.

> [!Note]  
> Niektóre ustawienia dla zadania mogły być automatycznie zdefiniowane jako [zmienne środowiskowe](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) podczas tworzenia definicji wydania na podstawie szablonu. Nie można modyfikować tych ustawień w ustawieniach zadań; Zamiast tego należy zmienić te ustawienia należy wybrać nadrzędnego elementu środowiska

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publikowanie w usłudze Azure Stack za pośrednictwem programu Visual Studio

Tworząc punktów końcowych, kompilacja programu Visual Studio Online (Narzędzia VSTO) można wdrożyć usługi Azure App Service do usługi Azure Stack. Usługa VSTS umożliwia nawiązywanie agenta kompilacji, który jest połączony z usługą Azure Stack.

1.  Zaloguj się do narzędzi VSTO dla programów, a następnie przejdź do strony Ustawienia aplikacji.

2.  Na **ustawienia**, wybierz opcję **zabezpieczeń**.

3.  W **grupy usługi VSTS**, wybierz opcję **punktu końcowego dla twórców**.

4.  Na **członków** zaznacz **Dodaj**.

5.  W **dodanie użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.

6.  Wybierz **Zapisz zmiany**.

7.  W **grupy usługi VSTS** listy wybierz **Administratorzy punktu końcowego**.

8.  Na **członków** zaznacz **Dodaj**.

9.  W **dodanie użytkowników i grup**, wprowadź nazwę użytkownika i wybierz użytkownika z listy użytkowników.

10. Wybierz **Zapisz zmiany**.

Teraz, gdy informacje o punkcie końcowym istnieje, usługa VSTS do połączenia usługi Azure Stack jest gotowe do użycia. Agent kompilacji w usłudze Azure Stack pobiera instrukcje z usługi VSTS, a następnie agenta umożliwia przekazywanie informacji o punkcie końcowym komunikacji z usługą Azure Stack.

## <a name="develop-the-application-build"></a>Tworzenie kompilacji aplikacji

> [!Note]  
> Usługa Azure Stack przy użyciu prawidłowego obrazów zespolone do uruchomienia (system Windows Server i SQL) i wdrożenie usługi App Service nie są wymagane. Zapoznaj się z dokumentacją usługi App Service "[przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](/articles/azure-stack/azure-stack-app-service-before-you-get-started)" sekcji dla usługi Azure Stack operatora.

Użyj [szablonami usługi Azure Resource Manager, takimi jak web](https://azure.microsoft.com/resources/templates/) kodu aplikacji z usługi VSTS, aby wdrożyć w obu chmurach.

### <a name="add-code-to-a-vsts-project"></a>Dodawanie kodu do projektu usługi VSTS

1.  Zaloguj się do usługi VSTS przy użyciu konta które ma uprawnień do tworzenia projektu w usłudze Azure Stack. Następny zrzut ekranu pokazuje, jak połączyć się z projektem HybridCICD.

2.  **Sklonuj repozytorium** przez tworzenie i otwieranie domyślną aplikację sieci web.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Tworzenie wdrożenia aplikacji niezależne sieci web dla usług App Service w obu chmurach

1.  Edytuj **WebApplication.csproj** pliku: Wybierz **Runtimeidentifier** , a następnie dodaj x64 Windows 10. Aby uzyskać więcej informacji, zobacz [niezależna wdrożenia](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacji.

2.  Użyj programu Team Explorer, aby sprawdzić kod do usługi VSTS.

3.  Upewnij się, że zostało zaznaczone pole wyboru kodu aplikacji w Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Utwórz definicję kompilacji

1.  Zaloguj się do usługi VSTS przy użyciu konta, które można utworzyć definicję kompilacji.

2.  Przejdź do **tworzenie aplikacji sieci Web** strony dla projektu.

3.  W **argumenty**, Dodaj **- r dla systemu win10-x64** kodu. Jest to wymagane, aby wyzwolić wdrożenie niezależna za pomocą.Net Core.

4.  Uruchom kompilację. [Kompilacji wdrożenia niezależna](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na platformie Azure i usługi Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Użyj platformy Azure hostowanej agenta kompilacji

Przy użyciu hostowanego agenta kompilacji w usłudze VSTS jest to wygodny sposób kompilowania i wdrażania aplikacji sieci web. Konserwacja agentów i uaktualnień, automatycznie są wykonywane przez Microsoft Azure, co umożliwia cyklu rozwoju ciągłe i nieprzerwany.

### <a name="configure-the-continuous-deployment-cd-process"></a>Skonfiguruj proces ciągłego wdrażania (polecenie CD)

Visual Studio Team Services (VSTS) i Team Foundation Server (TFS) zapewniają potoku wysoce konfigurowalne i łatwe w zarządzaniu dla wersji w wielu środowiskach, takich jak rozwój, przejściowe, zapewniania jakości (QA) i produkcyjnych. Ten proces może obejmować wymagające zatwierdzenia poszczególnych etapów cyklu życia aplikacji.

#### <a name="create-release-definition"></a>Tworzenie definicji wydania

Tworzenie definicji wydania jest ostatnim krokiem w aplikacji, proces kompilacji. Ta definicja wydania służy do tworzenia wersji i wdrażania kompilacji.

1.  Zaloguj się do usługi VSTS i przejdź do **kompilowania i wydawania** dla projektu.

2.  Na **wersji** zaznacz **[+]** a następnie wybierz **Tworzenie definicji wydania**.

3.  Na **wybierz szablon**, wybierz **wdrożenie usługi aplikacji Azure**, a następnie wybierz pozycję **Zastosuj**.

4.  Na **Dodawanie artefaktu**, z ** źródło (definicja kompilacji) wybierz aplikację kompilacji w chmurze platformy Azure.

5.  Na **potoku** zaznacz **fazy 1**, **1 zadanie** połączyć **przeglądać zadania w środowisku**.

6.  Na **zadania** wprowadź platformy Azure jako **Nazwa środowiska** i wybierz EP Web handlowców AzureCloud z **subskrypcji platformy Azure** listy.

7.  Wprowadź **nazwa usługi Azure app service**, czyli `northwindtraders` na następnym zrzucie ekranu.

8.  Faza agenta, wybierz **hostowany program VS2017** z **kolejki agentów** listy.

9.  W **wdrożenia usługi Azure App Service**, wybierz prawidłowe **pakietu lub folderu** dla środowiska.

10. W **Wybieranie pliku lub folderu**, wybierz opcję **OK** do **lokalizacji**.

11. Zapisz wszystkie zmiany i wróć do **potoku**.

12. Na **potoku** zaznacz **Dodawanie artefaktu**i wybierz polecenie **NorthwindCloud Traders — statek** z ** źródło (definicja kompilacji) ** listy.

13. Na **wybierz szablon**, dodać innego środowiska. Wybierz **wdrożenie usługi aplikacji Azure** , a następnie wybierz **Zastosuj**.

14. Wprowadź `Azure Stack` jako **Nazwa środowiska**.

15. Na **zadania** karcie Znajdź i zaznacz usługi Azure Stack.

16. Z **subskrypcji platformy Azure** listy wybierz **EP statku handlowców AzureStack** dla punktu końcowego usługi Azure Stack.

17. Wprowadź nazwę aplikacji sieci web usługi Azure Stack jako **nazwa usługi App service**.

18. W obszarze **wybór agenta**, wybierz **AzureStack -b jako część Douglas** z **kolejki agentów** listy.

19. Dla **wdrożenia usługi Azure App Service**, wybierz prawidłowe **pakietu lub folderu** dla środowiska. Na **Wybieranie pliku lub folderu**, wybierz opcję **OK** folderu **lokalizacji**.

20. Na **zmiennej** kartę, Znajdź zmienną o nazwie `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Ustaw wartość zmiennej **true**i ustaw jego zakres **usługi Azure Stack**.

21. Na **potoku** zaznacz **wyzwalacz ciągłego wdrażania** ikonę artefaktu NorthwindCloud Traders — Web i zestawu **wyzwalacz ciągłego wdrażania** do **Włączone**. Tak samo jak w przypadku **NorthwindCloud Traders — statek** artefaktu.

22. Dla środowiska Azure Stack, wybierz **warunki przed wdrożeniem** ikony Ustaw wyzwalacz, **po wydaniu**.

23. Zapisz wszystkie zmiany.

> [!Note]  
> Niektóre ustawienia dla zadań podrzędnych zwolnienia są automatycznie definiowane jako [zmienne środowiskowe](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) podczas tworzenia definicji wydania na podstawie szablonu. Te ustawienia nie można zmodyfikować w ustawieniach zadania, ale mogą być modyfikowane w środowisku elementów nadrzędnych.

## <a name="create-a-release"></a>Tworzenie wydania

1.  Na **potoku** otwartą kartą **wersji** i wybierz polecenie **utworzyć wersję**.

2.  Wprowadź opis wersji, sprawdź, czy poprawny artefakty są zaznaczone, a następnie wybierz **Utwórz**. Po kilku chwilach zostanie wyświetlony transparent informujący, że utworzono nową wersję i wydanie będzie wyświetlana jako link. Wybierz link, aby wyświetlić na stronie podsumowania wydania.

3.  Stronie podsumowania wydania dla przedstawia szczegółowe informacje o wersji. Na poniższym zrzucie ekranu "Release-2" **środowisk** sekcji przedstawiono **stan wdrożenia** dla platformy Azure jako "W toku", a także stan dla usługi Azure Stack to "Powodzenie". Gdy stan wdrożenia dla środowiska platformy Azure przechodzi "Powodzenie", zostanie wyświetlony transparent, co oznacza, że wydanie gotowe do zatwierdzenia. Gdy wdrożenie jest operacją oczekującą lub zakończyło się niepowodzeniem, niebieski **(i)** jest wyświetlana ikona informacji. Umieść kursor nad ikonę Aby wyświetlić okno podręczne, które zawiera przyczynę opóźnienia lub niepowodzenia.

4.  Inne widoki, takich jak lista wersji, również wyświetlana ikona wskazująca zatwierdzenie jest w stanie oczekiwania. Okno podręczne dla tej ikony zawiera nazwy środowiska i szczegółowe informacje związane z wdrażaniem. Może to być proste, można znaleźć ogólny postęp wydań i zobaczyć, które wersje oczekują na zatwierdzenie przez administratora.

## <a name="monitor-and-track-deployments"></a>Monitorowanie i śledzenie wdrożeń

1.  Na **Release 2** strony podsumowania, wybierz opcję **dzienniki**. Podczas wdrażania ta strona zawiera dziennika na żywo z agenta. W okienku po lewej stronie pokazuje stan każdej operacji w ramach wdrożenia dla każdego środowiska.

2.  Wybierz ikonę osób **akcji** kolumny o zatwierdzenie przed wdrożeniem lub po wdrożeniu, aby sprawdzić, kto zatwierdzenia (lub odrzucenia) wdrożenia i wiadomości podano ich.

3.  Po zakończeniu wdrażania cały plik dziennika jest wyświetlany w okienku po prawej stronie. Wybierz dowolne **kroku** w okienku po lewej stronie, aby wyświetlić plik dziennika dla jednego kroku, takie jak **zainicjować zadanie**. Możliwość wyświetlania poszczególne dzienniki ułatwia śledzenie i debugowanie części całe wdrożenie. **Zapisz** kroku można znaleźć w pliku dziennika lub **pobrać wszystkie dzienniki w formie pliku zip**.

4.  Otwórz **Podsumowanie** kartę, aby wyświetlić ogólne informacje o wersji. Ten widok przedstawia szczegółowe informacje o kompilacji, środowisk, którymi została wdrożona do, stan wdrożenia i inne informacje o wersji.

5.  Wybierz link środowiska (**Azure** lub **usługi Azure Stack**) aby wyświetlić informacje o istniejących i oczekujące wdrożenia w danym środowisku. Użyj tych widoków w prosty sposób, aby sprawdzić, czy tę samą kompilację została wdrożona w obu środowiskach.

6.  Otwórz **wdrożyć aplikację w środowisku produkcyjnym** w przeglądarce. Na przykład dla witryny sieci Web usługi Azure App Services, otwórz adres URL [http://[your-app-name\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net).

**Integracja platformy Azure i usługi Azure Stack zapewnia skalowalne rozwiązanie wielu chmur**

Elastyczna i niezawodna usługa wielu chmur, zapewnia bezpieczeństwo danych, wróć w górę i nadmiarowość, spójne i szybkiego dostępność, skalowalność magazynu i dystrybucji i zgodne geograficznego routingu. Ten proces wyzwalanych ręcznie zapewnia niezawodne i wydajne obciążenia przełączania się między hostowanych aplikacji sieci Web, zapewnieniu natychmiastowej dostępności ważnych danych. 

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).