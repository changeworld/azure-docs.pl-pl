---
title: Utwórz rozwiązanie rozproszone geograficznie aplikacji przy użyciu platformy Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć rozwiązanie rozproszone geograficznie aplikacji przy użyciu platformy Azure i usługi Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2c2b0d022c81dffe5e98932c22b9707eb10b7e63
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481726"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Samouczek: Utwórz rozwiązanie rozproszone geograficznie aplikacji przy użyciu platformy Azure i usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak kierować ruch do określonych punktów końcowych, w oparciu o różne metryki za pomocą wzorca aplikacji rozproszonej geograficznie. Tworzenie usługi Traffic Manager profil przy użyciu konfiguracji routingu i punktu końcowego geograficznego na podstawie gwarantuje, że informacji jest kierowany do punktów końcowych na podstawie wymagań regionalnych, firmowych, jak i międzynarodowej rozporządzenie i potrzeb danych.

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> - Tworzenie aplikacji rozproszonej geograficznie.
> - Użyj usługi Traffic Manager pod kątem aplikacji.

## <a name="use-the-geo-distributed-apps-pattern"></a>Użyj wzorca aplikacje rozproszone geograficznie

Za pomocą wzorca rozproszone geograficznie aplikacji można zakresów regionów. Domyślne do chmury publicznej, ale niektóre z użytkownicy mogą wymagać, że ich dane pozostają w danym regionie. Możesz przekierować użytkowników do najbardziej odpowiednich chmury, w zależności od wymagań.

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia

#### <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności

Rozwiązania, które tworzysz z tego samouczka jest nie w celu uwzględnienia skalowalności. Jednak w połączeniu z inne technologie platformy Azure i w środowisku lokalnym i rozwiązań może obsłużyć wymagania w zakresie skalowalności. Aby uzyskać informacje o tworzeniu rozwiązań hybrydowych przy użyciu automatycznego skalowania za pośrednictwem ruchu menedżera, zobacz [tworzyć rozwiązania skalowania wielu chmur przy użyciu platformy Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Zagadnienia dotyczące dostępności

Podobnie jak w przypadku zagadnienia dotyczące skalowalności, to rozwiązanie nie bezpośrednio adresu dostępności. Jednak również podobnie jak nasze zagadnienia dotyczące skalowalności, Azure i w środowisku lokalnym, technologii i rozwiązań można zaimplementować w ramach tego rozwiązania, aby zapewnić wysoką dostępność, aby uzyskać wszystkie składniki zaangażowane.

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca

- Twoja organizacja ma międzynarodowy gałęzi wymagające niestandardowe regionalnych zabezpieczeń i zasad dystrybucji.

- Ściąga poszczególnych biur organizacje pracowników, biznesowych i danych funkcji wymagających raportowanie aktywności na lokalnych regulacji prawnych i strefy czasowej.

- Mogą być spełnione wymogi dużej skali, skalując w poziomie aplikacje z wieloma wdrożeniami aplikacji, które zostaną wprowadzone w jednym regionie, a także w regionach, aby zaspokoić wymagania ekstremalne obciążenie.

### <a name="planning-the-topology"></a>Planowanie topologii

Przed kompilacją limit rozmiaru aplikacji rozproszonej, warto dysponują wiedzą na temat do następujących:

-   **Domeny niestandardowej do aplikacji:** Jaka jest nazwa domeny niestandardowej, który użytkownicy będą używać do dostępu do aplikacji? Przykładowa aplikacja niestandardowa nazwa domeny jest *www.scalableasedemo.com.*

-   **Domena usługi Traffic Manager:** Nazwa domeny, należy wybrać podczas tworzenia [profilu usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Ta nazwa zostanie połączony z elementem *trafficmanager.net* sufiks Rejestracja zapisu domeny, który jest zarządzany przez usługę Traffic Manager. Przykładowa aplikacja jest wybrana nazwa *pokaz skalowalne środowisko ase*. W rezultacie pełną nazwa domeny, który jest zarządzany przez usługę Traffic Manager jest *demo.trafficmanager.net skalowalne środowisko ase*.

-   **Strategia skalowania zasięgu aplikacji:** Zużycie aplikacji będą rozproszone na wielu środowisk App Service Environment w jednym regionie? Wiele regionów? Kombinacja obu metod? Decyzja powinna być oparta na oczekiwania, z której pochodzą ruchu klientów, a także jak można skalować w pozostałej części aplikacji obsługi infrastruktury zaplecza. Na przykład za pomocą aplikacji bezstanowych w 100% aplikacji może być wysoce skalowana na region platformy Azure, pomnożona przez środowisk usługi App Service wdrożone w wielu regionach platformy Azure przy użyciu kombinacji wielu środowisk usługi App Service. Za pomocą 15 + globalnych regionów platformy Azure dostępne do wyboru klienci mogą tworzyć naprawdę zużycie aplikacja ogromnego na całym świecie. Przykładowa aplikacja używana w tym artykule, aby uzyskać trzy środowisk usługi App Service zostały utworzone w jednym regionie platformy Azure (południowo-środkowe stany USA).

-   **Konwencje nazewnictwa dla środowisk usługi App Service:** Każdy środowiska App Service Environment wymaga unikatowej nazwy. Po przekroczeniu jednego lub dwóch środowisk usługi App Service warto mają konwencję nazewnictwa ułatwiają identyfikację każdego środowiska App Service Environment. Dla przykładowej aplikacji użyto prostych konwencji nazewnictwa. Nazwy trzech środowisk App Service Environment są *fe1ase*, *fe2ase*, i *fe3ase*.

-   **Konwencje nazewnictwa dla aplikacji:** Ponieważ wiele wystąpień aplikacji zostanie wdrożony, nazwy jest wymagane dla poszczególnych wystąpień wdrożonej aplikacji. Przy użyciu środowisk usługi App Service można taką samą nazwę aplikacji w wielu środowiskach usługi App Service. Ponieważ każdy środowiska App Service Environment ma sufiks domeny unikatowy, deweloperzy można ponownie użyć dokładnie tej samej nazwy aplikacji w każdym środowisku. Na przykład deweloper może mieć aplikacji o nazwie w następujący sposób: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*itp. Dla aplikacji, w tym scenariuszu każde wystąpienie aplikacji ma unikatową nazwę. Nazwy wystąpienia aplikacji, które są używane są *webfrontend1*, *webfrontend2*, i *webfrontend3*.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi aplikacje hybrydowe. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.

## <a name="part-1-create-a-geo-distributed-app"></a>Część 1: Tworzenie aplikacji rozproszonej geograficznie

W tej części utworzysz aplikację sieci web.

> [!div class="checklist"]
> - Tworzenie aplikacji sieci web i publikowanie
> - Dodaj kod do repozytoriów platformy Azure
> - Polecenie kompilacji aplikacji do wielu celów w chmurze.
> - Zarządzanie i konfigurowanie proces ciągłego Dostarczania

### <a name="prerequisites"></a>Wymagania wstępne

Wymagane są subskrypcja platformy Azure i instalacja usługi Azure Stack.

### <a name="geo-distributed-app-steps"></a>Kroki rozproszone geograficznie aplikacji

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Uzyskaj domenę niestandardową i konfigurowanie systemu DNS

Zaktualizuj plik strefy DNS dla domeny. Usługa Azure AD może zweryfikować własność nazwy domeny niestandardowej. Użyj [system DNS Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure/Office 365/zewnętrzną DNS rekordów w obrębie platformy Azure, lub dodanie wpisu DNS u [różnych rejestratora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Zarejestruj domenę niestandardową publicznych rejestrze.

2. Zaloguj się do rejestratora nazw domen dla domeny. Może być konieczne administrator zatwierdzone aktualizacje DNS.

3. Zaktualizuj plik strefy DNS dla domeny, dodając wpis DNS udostępniony przez usługę Azure AD. Wpis DNS nie zmienia zachowań, takich jak routing poczty lub hosting sieci web.

### <a name="create-web-apps-and-publish"></a>Tworzenie aplikacji sieci web i publikowanie

Konfigurowanie ciągłej integracji/ciągłego wdrażania na wdrażanie aplikacji sieci Web platformy Azure i usługi Azure Stack i automatyczne wypychanie zmian do obu chmur hybrydowych.

> [!Note]  
> Usługa Azure Stack przy użyciu prawidłowego obrazów zespolone do uruchomienia (system Windows Server i SQL) i wdrożenie usługi App Service nie są wymagane. Zapoznaj się z dokumentacją usługi App Service [przed rozpoczęciem pracy z usługą App Service w usłudze Azure Stack](../azure-stack-app-service-before-you-get-started.md) sekcji dla usługi Azure Stack operatora.

#### <a name="add-code-to-azure-repos"></a>Dodaj kod do repozytoriów platformy Azure

1. Zaloguj się do programu Visual Studio z **konta mającego uprawnienia do tworzenia projektu** z repozytoriów platformy Azure.

    Hybrydowe ciągłej integracji/ciągłego dostarczania (CI/CD) można stosować do kodu aplikacji i infrastruktury kodu. Użyj [szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/) dla obu chmur prywatnych i hostowanych rozwoju.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image1.JPG)

2. **Sklonuj repozytorium** przez tworzenie i otwieranie domyślną aplikację sieci web.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Tworzenie wdrożenia aplikacji sieci web w obu chmurach

1.  Edytuj **WebApplication.csproj** pliku: Wybierz **Runtimeidentifier** i Dodaj **win10 x64**. (Zobacz [wdrożenia Self-contained](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentacji.)

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image3.png)

1.  **Ewidencjonują kod do repozytoriów Azure** za pomocą programu Team Explorer.

2.  Upewnij się, że **kod aplikacji** została sprawdzona pod repozytoriów platformy Azure.

### <a name="create-the-build-definition"></a>Utwórz definicję kompilacji

1. **Zaloguj się do potoków Azure** o potwierdzenie zdolność do tworzenia definicji kompilacji.

2. Dodaj **- r dla systemu win10-x64** kodu. Jest to konieczne wyzwolić wdrożenie niezależna z platformą .NET Core.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image4.png)

3. **Uruchom kompilację**. [Kompilacji wdrożenia niezależna](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces będzie publikować artefaktów, które można uruchamiać na platformie Azure i usługi Azure Stack.

**Przy użyciu platformy Azure hostowanej agenta**

Za pomocą agentów hostowanych w potokach Azure to wygodny sposób do tworzenia i wdrażania aplikacji sieci web. Konserwacja i aktualizacje automatyczne są wykonywane przez Microsoft Azure, umożliwiając ciągłe, nieprzerwany tworzenia, testowania i wdrażania.

### <a name="manage-and-configure-the-cd-process"></a>Zarządzanie i konfigurowanie proces ciągłego Dostarczania

Usługi Azure DevOps i serwer usługi Azure DevOps oferują wysoce konfigurowalne i łatwe w zarządzaniu potoku dla wersji w wielu środowiskach, takich jak rozwój, przejściowe, odpowiedzi na pytania i środowisk produkcyjnych; w tym wymagające zatwierdzenia określonych etapów.

#### <a name="create-release-definition"></a>Tworzenie definicji wydania


![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image5.png)

1. Wybierz **oraz** przycisk, aby dodać nowe wydanie w ramach **karcie wersje** na stronie kompilacji i wydania programu Visual Studio Online (VSO).

   ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image6.png)

2. Zastosuj **wdrożenie usługi aplikacji Azure** szablonu.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image7.png)

3. W menu rozwijanym artefaktu Dodaj **Dodawanie artefaktu** kompilacji aplikacji w chmurze platformy Azure.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image8.png)

4. Na karcie potoku wybierz **fazy, zadanie** połączyć środowiska i ustaw wartości środowiska chmury platformy Azure.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image9.png)

5. Ustaw **Nazwa środowiska** i wybierz pozycję Azure **subskrypcji** dla punktu końcowego w chmurze platformy Azure.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image10.png)

6. W obszarze Nazwa środowiska, ustaw wymagane **nazwa usługi Azure app service**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image11.png)

7. Wprowadź **hostowany program VS2017** w ramach kolejki agentów dla środowiska hostowane w chmurze platformy Azure.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image12.png)

8. Wdrażanie usługi Azure App Service, wybierz menu prawidłowe **pakietu lub folderu** dla środowiska. Wybierz przycisk OK, aby **lokalizacji folderu**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image13.png)

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image14.png)

9. Zapisz wszystkie zmiany i wróć do **potoku tworzenia wersji**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image15.png)

10. Dodaj **nowych artefaktów** Wybieranie kompilacji dla aplikacji usługi Azure Stack.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image16.png)

11. Dodaj jeden więcej stosowanie środowiska **wdrożenie usługi aplikacji Azure.**

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image17.png)

12. Nowemu środowisku nazwę **usługi Azure Stack.**

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image18.png)

13. Znajdź środowiska usługi Azure Stack w obszarze **zadań** kartę.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image19.png)

14. Wybierz **subskrypcji** dla punktu końcowego usługi Azure Stack.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image20.png)

15. Ustaw nazwę aplikacji sieci web usługi Azure Stack jako **nazwa usługi App service**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image21.png)

16. Wybierz **agenta usługi Azure Stack**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image22.png)

17. W ramach wdrożenia usługi Azure App Service wybierz prawidłowe sekcji **pakietu lub folderu** dla środowiska. Wybierz przycisk OK, aby **lokalizacji folderu**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image23.png)

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image24.png)

18. W obszarze **zmiennej** karta Dodaj zmienną o nazwie `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, ustaw dla niego wartość jako `true`i ograniczyć zakres do `Azure Stack`.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image25.png)

19. Wybierz **ciągłe** ikona wyzwalacza wdrożenia zarówno w przypadku artefaktów, jak i Włącz **kontynuuje** wyzwalacza wdrażania.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image26.png)

20. Wybierz **przed wdrożeniem** ikonę warunków w środowisku usługi Azure Stack i ustawić wyzwalacz na **po wydaniu.**

21. Zapisz wszystkie zmiany.

> [!Note]  
>  Niektóre ustawienia dla zadania mogły być automatycznie zdefiniowane jako [zmienne środowiskowe](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) podczas tworzenia definicji wydania z szablonu. Nie można modyfikować tych ustawień w ustawieniach zadań; Zamiast tego należy wybrać nadrzędny element środowiska, aby edytować te ustawienia.

## <a name="part-2-update-web-app-options"></a>Część 2: Zaktualizuj Opcje aplikacji sieci web

Usługa [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. 

![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure
> - Użyj ** rejestratora CNAME **rekord** Aby zamapować niestandardową nazwę DNS w usłudze App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mapowanie istniejącej niestandardowej nazwy DNS na aplikacje internetowe platformy Azure

> [!Note]  
>  Użycie rekordu CNAME dla wszystkich niestandardowych nazw DNS z wyjątkiem domeny katalogu głównego (na potrzeby example,northwind.com).

Aby przeprowadzić migrację aktywnej witryny oraz jej nazwy domeny DNS do usługi App Service, zobacz [Migrate an active DNS name to Azure App Service](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain) (Migrowanie aktywnej nazwy DNS do usługi Azure App Service).

### <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

-   [Utwórz aplikację usługi App Service](https://docs.microsoft.com/azure/app-service/), lub użyj aplikacja utworzona w innym samouczku.

-   Kup nazwę domeny i zapewnić dostęp do rejestru systemu DNS dostawcy domeny.

Zaktualizuj plik strefy DNS dla domeny. Usługa Azure AD zweryfikuje własności nazwy domeny niestandardowej. Użyj [system DNS Azure](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure/Office 365/zewnętrzną DNS rekordów w obrębie platformy Azure, lub dodanie wpisu DNS u [różnych rejestratora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Zarejestruj domenę niestandardową publicznych rejestrze.

-   Zaloguj się do rejestratora nazw domen dla domeny. (Administrator zatwierdzonych może być konieczne upewnić aktualizacji DNS.)

-   Zaktualizuj plik strefy DNS dla domeny, dodając wpis DNS udostępniony przez usługę Azure AD.

Na przykład dodać wpisy DNS dla northwindcloud.com i www.northwindcloud.com, należy skonfigurować ustawienia DNS dla domeny katalogu głównego northwindcloud.com.

> [!Note]  
>  Nazwa domeny można kupować w ramach [witryny Azure portal](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain).  
> Aby zamapować niestandardową nazwę DNS na aplikację internetową, dla tej aplikacji internetowej musisz mieć płatną warstwę [planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) (**Współdzielona**, **Podstawowa**, **Standardowa** lub  **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Tworzy i mapuje CNAME i

#### <a name="access-dns-records-with-domain-provider"></a>Uzyskiwanie dostępu do rekordów DNS u dostawcy domen

> [!Note]  
>  Konfigurowanie niestandardowej nazwy DNS dla usługi Azure Web Apps przy użyciu usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

1.  Zaloguj się do witryny sieci Web dostawcy głównego.

2.  Znajdź stronę służącą do zarządzania rekordami DNS. Każdy dostawca domeny ma własny interfejs rekordów DNS. Poszukaj obszarów witryny z etykietą **Nazwa domeny**, **DNS** lub **Zarządzanie serwerami nazw**.

Strona rekordów DNS mogą być wyświetlane w **Moje domen**. Znajdź łącze o nazwie **plik strefy**, **rekordy DNS**, lub **zaawansowanej konfiguracji**.

Poniższy zrzut ekranu przedstawia przykład strony rekordów DNS:

![Przykładowa strona rekordów DNS](media/azure-stack-solution-geo-distributed/image28.png)

1. W rejestrze nazw domen, wybierz **apletu Dodaj lub Utwórz** do utworzenia rekordu. Niektórzy dostawcy udostępniają różne linki na potrzeby dodawania różnych typów rekordów. Zapoznaj się z dokumentacją dostawcy.

2. Dodaj rekord CNAME, aby zmapować poddomenę na domyślną hosta aplikacji.

   Na przykład domena www.northwindcloud.com, Dodaj rekord CNAME, który mapuje nazwę na < aplikacji\_name >. azurewebsites.net.

Po dodaniu tego rekordu CNAME, strona rekordów DNS wygląda podobnie jak w poniższym przykładzie:

![Nawigacja w portalu do aplikacji platformy Azure](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Włączanie mapowania rekordów CNAME na platformie Azure

1. W nowej karcie Zaloguj się do witryny Azure portal

2. Przejdź do usługi App Services.

3. Wybierz aplikację sieci web.

4. W lewym obszarze nawigacji na stronie aplikacji w witrynie Azure Portal wybierz pozycję **Domeny niestandardowe**.

5. Wybierz ikonę **+** obok pozycji **Dodaj nazwę hosta**.

1. Wpisz w pełni kwalifikowaną nazwę domeny, takich jak `www.northwindcloud.com`.

2. Wybierz przycisk **Weryfikuj**.

3. Jeśli zaznaczone, Dodaj dodatkowe rekordy innych typów (`A` lub `TXT`) do rekordów DNS rejestratorów nazw domen. Azure zapewni wartości i typy tych rekordów:

   a.  Rekord **A** do zmapowania adresu IP aplikacji.

   b.  A **TXT** rekordu do mapowania na aplikacja domyślnej nazwy hosta < nazwa_aplikacji >. azurewebsites.net. Usługa App Service używa tego rekordu tylko podczas konfiguracji, aby zweryfikować własność domeny niestandardowej. Po zakończeniu weryfikacji usuwanie rekordu TXT.

4. Wykonania tego zadania na karcie rejestratora domen i przechowywać aż **Dodaj nazwę hosta** zostanie aktywowany przycisk.

5. Upewnij się, że ** typ rekordu nazwy hosta jest ustawiona na **CNAME (www.example.com lub dowolna poddomena)**.

6. Wybierz przycisk **Dodaj nazwę hosta**.

7. Wpisz w pełni kwalifikowaną nazwę domeny, takich jak `northwindcloud.com`.

8. Wybierz przycisk **Weryfikuj**.

9. **Dodaj** została aktywowana.

10. Upewnij się, że ** typ rekordu nazwy hosta jest ustawiona na **rekord (example.com)**.

11. **Dodaj nazwę hosta**.

    Może upłynąć trochę czasu, nowe nazwy hostów zostaną odzwierciedlone w aplikacji **domen niestandardowych** strony. Spróbuj odświeżyć przeglądarkę, aby zaktualizować dane.
  
    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image31.png) 
  
    W przypadku błędu w dolnej części strony pojawi się powiadomienie o błędzie weryfikacji. ![Błąd weryfikacji](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  Może być powtarzany powyższe kroki do mapowania domeny z symbolami wieloznacznymi (\*. northwindcloud.com)... Umożliwia to dodanie wszelkich dodatkowych poddomen do tej usługi app service bez konieczności tworzenia oddzielny rekord CNAME dla każdej z nich. Postępuj zgodnie z instrukcjami rejestratora, do skonfigurowania tego ustawienia.

#### <a name="test-in-a-browser"></a>Testowanie w przeglądarce

Przejdź do nazwy DNS skonfigurowane wcześniej (na przykład `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>Część 3: Powiązania niestandardowego certyfikatu SSL

W tej części:

> [!div class="checklist"]
> - Wiązanie niestandardowego certyfikatu SSL w usłudze App Service
> - Wymuszanie protokołu HTTPS dla aplikacji
> - Automatyzacja wiązania certyfikatu protokołu SSL za pomocą skryptów

> [!Note]  
> Jeśli to konieczne, uzyskać klienta certyfikatu SSL w witrynie Azure portal i powiązać go z aplikacją internetową. Postępuj zgodnie z [samouczkiem Certyfikaty usługi App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

-   [Utwórz aplikację usługi App Service](https://docs.microsoft.com/azure/app-service/)
-   [Mapuj niestandardową nazwę DNS na swoją aplikację internetową](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Uzyskaj certyfikat protokołu SSL z zaufanego urzędu certyfikacji i używać klucza do podpisania żądania

### <a name="requirements-for-your-ssl-certificate"></a>Wymagania dotyczące certyfikatu protokołu SSL

Aby używać certyfikatu w usłudze App Service, musi on spełniać wszystkie następujące wymagania:

-   Podpisany przez zaufany urząd certyfikacji

-   Wyeksportowany jako chroniony hasłem plik PFX

-   Zawiera klucz prywatny o długości co najmniej 2048 bitów

-   Zawiera wszystkie certyfikaty pośrednie w łańcuchu certyfikatów

> [!Note]  
>  **Elliptic certyfikaty kryptografii krzywych (ECC)** działają z usługą App Service, ale nie są uwzględnione w tym przewodniku. Aby uzyskać pomoc w tworzeniu certyfikatów ECC zapoznaj się z urzędu certyfikacji. 

#### <a name="prepare-the-web-app"></a>Przygotowywanie aplikacji sieci web

Aby powiązać niestandardowy certyfikat protokołu SSL do aplikacji sieci web [planu usługi App Service](https://azure.microsoft.com/pricing/details/app-service/) musi znajdować się w **podstawowe**, **standardowa**, lub **Premium** warstwy.

#### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1.  Otwórz [witryny Azure portal](https://portal.azure.com/) i przejdź do aplikacji sieci web.

2.  Z menu po lewej stronie wybierz **App Services**, a następnie wybierz nazwę aplikacji sieci web.

![Wybieranie aplikacji internetowej](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>Sprawdzanie warstwy cenowej

1.  W lewym obszarze nawigacji na stronie aplikacji sieci web, przewiń do **ustawienia** i wybierz pozycję **skalowanie w górę (plan usługi App Service)**.

    ![Menu skalowania w górę](media/azure-stack-solution-geo-distributed/image34.png)

1.  Upewnij się, aplikacja internetowa nie znajduje się w **bezpłatna** lub **Shared** warstwy. Bieżąca warstwa aplikacji internetowej jest wyróżniona w ciemnoniebieskim polem.

    ![Sprawdzanie warstwy cenowej](media/azure-stack-solution-geo-distributed/image35.png)

Niestandardowy protokół SSL nie jest obsługiwany w warstwie **Bezpłatna** ani **Współdzielona**. Aby zmniejszać, postępuj zgodnie z instrukcjami w następnej sekcji lub **Wybieranie warstwy cenowej** stronie, a następnie przejdź do [przekazany i powiązany certyfikat protokołu SSL](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Skalowanie w górę planu usługi App Service

1.  Wybierz jedną z następujących warstw: **Podstawowa**, **Standardowa** lub **Premium**.

2.  Wybierz przycisk **Wybierz**.

![Wybieranie warstwy cenowej](media/azure-stack-solution-geo-distributed/image36.png)

Operacja skalowania została zakończona, gdy zostanie wyświetlone powiadomienie.

![Powiadomienie o skalowaniu w górę](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Wiązanie certyfikatu protokołu SSL i scalanie certyfikatów pośrednich

Scal wiele certyfikatów w łańcuchu.

1. **Otwórz każdy certyfikat** odebrane w edytorze tekstów.

2. Utwórz plik scalonego certyfikatu o nazwie *mergedcertificate.crt*. W edytorze tekstów skopiuj zawartość każdego certyfikatu do tego pliku. Kolejność certyfikatów powinna być zgodna z kolejnością w łańcuchu certyfikatów, poczynając od Twojego certyfikatu i kończąc na certyfikacie głównym. Wygląda to następująco:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Eksportowanie certyfikatu do pliku PFX

Wyeksportuj scalony certyfikat protokołu SSL z kluczem prywatnym, generowane przez certyfikat.

Plik klucza prywatnego jest tworzony przy użyciu biblioteki OpenSSL. Aby wyeksportować certyfikat do pliku PFX, uruchom następujące polecenie, zastępując symbole zastępcze *< prywatny key-file >* i *< scalone certificate-file >* ścieżkami klucza prywatnego i scalone plik certyfikatu.

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Po wyświetleniu monitu określ hasło eksportu później przekazywania certyfikatu protokołu SSL w usłudze App Service.

Gdy usługi IIS lub **Certreq.exe** są używane do wygenerowania żądania certyfikatu, zainstaluj certyfikat na komputerze lokalnym i następnie [wyeksportować certyfikat do pliku PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Przekaż certyfikat protokołu SSL

1. Wybierz **ustawienia protokołu SSL** w lewym obszarze nawigacji aplikacji internetowej.

2. Wybierz **Przekaż certyfikat**.

3. W **plik certyfikatu PFX**, wybierz plik PFX.

4. 1. W **hasło certyfikatu**, wpisz hasło utworzone podczas eksportowania pliku PFX.

5. Wybierz pozycję **Przekaż**.

![Przekazywanie certyfikatu](media/azure-stack-solution-geo-distributed/image38.png)

Po zakończeniu przekazywania certyfikatu usługi App Service pojawia się w **ustawienia protokołu SSL** strony.

![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>Wiązanie certyfikatu protokołu SSL

1.  W **powiązania SSL** zaznacz **Dodaj powiązanie**.

    > [!Note]  
    >  Jeśli certyfikat został przekazany, ale nie ma nazwy domeny w **Hostname** listy rozwijanej, spróbuj odświeżyć stronę przeglądarki.

1.  W **Dodawanie powiązania SSL** strony, należy użyć listy rozwijane, aby wybrać nazwę domeny do zabezpieczenia i certyfikat do użycia.

2.  W **typ SSL**, wybierz, czy chcesz użyć [ **oznaczaniem nazwy serwera (SNI)**](https://en.wikipedia.org/wiki/Server_Name_Indication)lub SSL opartego na protokole IP.

-   **Oparte na SNI SSL**-powiązania SSL oparte na SNI wiele, mogą zostać dodane. Ta opcja umożliwia zabezpieczenie wielu domen na tym samym adresie IP za pomocą wielu certyfikatów protokołu SSL. Większość nowoczesnych przeglądarek (w tym programy Internet Explorer, Chrome, Firefox i Opera) obsługuje funkcję SNI. Bardziej szczegółowe informacje dotyczące obsługi przeglądarek możesz znaleźć w artykule [Server Name Indication (Oznaczanie nazwy serwera)](https://wikipedia.org/wiki/Server_Name_Indication).

-   **Połączenie IP SSL**— mogą być dodawane tylko jednego powiązanie SSL oparte na adresie IP. Ta opcja umożliwia zabezpieczenie dedykowanego publicznego adresu IP za pomocą tylko jednego certyfikatu protokołu SSL. Aby zabezpieczyć wiele domen, je zabezpieczyć za pomocą tego samego certyfikatu SSL. Jest to tradycyjna opcja dla powiązania protokołu SSL.

    1.  Wybierz **Dodaj powiązanie**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image40.png)

Po zakończeniu przekazywania certyfikatu usługi App Service pojawia się w **powiązania SSL** sekcje.

![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Ponowne mapowanie rekordu A dla protokołu IP SSL

Jeśli połączenie IP SSL nie jest używany w aplikacji sieci web, przejść do [testowanie protokołu HTTPS dla domeny niestandardowej](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Domyślnie aplikacja internetowa używa udostępnionego publicznego adresu IP. Gdy certyfikat jest powiązana z opartych na protokole SSL, usługi App Service tworzy nowy i dedykowany adres IP dla aplikacji sieci web.

Gdy rekord A jest mapowany do aplikacji sieci web, należy zaktualizować rejestr domeny za pomocą dedykowanego adresu IP.

**Domena niestandardowa** strona zostanie zaktualizowana z nowego, dedykowanego adresu IP. Skopiuj to [adresu IP](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), następnie ponownie zamapować [rekord](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) ten nowy adres IP.

#### <a name="test-https"></a>Testowanie protokołu HTTPS

W różnych przeglądarkach przejdź do https://<your.custom.domain>to upewnij się, że aplikacja sieci web jest obsługiwana.

![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> Jeśli wystąpią błędy sprawdzania poprawności certyfikatów, przyczyną może być certyfikat z podpisem własnym lub certyfikaty pośrednie może mieć zostały przerwane podczas eksportowania do pliku PFX.

#### <a name="enforce-https"></a>Wymuszanie protokołu HTTPS

Domyślnie każdy użytkownik może uzyskać dostęp do aplikacji sieci web przy użyciu protokołu HTTP. może zostać przekierowany wszystkie żądania HTTP do portu HTTPS.

Na stronie aplikacji sieci web, wybierz **ustawienia SL**. Następnie w pozycji **Tylko HTTPS** wybierz opcję **Włączone**.

![Wymuszanie protokołu HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

Po zakończeniu operacji, przejdź do dowolnego adresu URL HTTP, do aplikacji. Na przykład:

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Wymuszanie protokołu TLS 1.1/1.2

Umożliwia aplikacji [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 domyślnie, co jest już uznawany za bezpieczny przez standardy branżowe, takie jak [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Aby wymusić nowsze wersje protokołu TLS, wykonaj następujące kroki:

1.  Na stronie aplikacji sieci web, w obszarze nawigacji po lewej stronie, wybierz **ustawienia protokołu SSL**.

2.  W **wersję protokołu TLS**, wybierz minimalną wersję protokołu TLS.

![Wymuszanie protokołu TLS 1.1 lub 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1.  Wybierz **Utwórz zasób** > **sieć** > **profilu usługi Traffic Manager** > **Utwórz**.

2.  W obszarze **Tworzenie profilu usługi Traffic Manager** podaj następujące informacje:

    1.  W **nazwa**, podaj nazwę dla profilu. Ta nazwa musi być unikatowa w obrębie strefy manager.net ruch i powoduje nazwę DNS, manager.net ruchu, który jest używany do uzyskania dostępu do profilu usługi Traffic Manager.

    2.  W **Metoda routingu**, wybierz opcję **metoda Geographicrouting**.

    3.  W **subskrypcji**, wybierz subskrypcję, w którym ma zostać utworzony ten profil.

    4.  W obszarze **Grupy zasobów** utwórz nową grupę zasobów, w której zostanie umieszczony ten profil.

    5.  W obszarze **Lokalizacja grupy zasobów** wybierz lokalizację grupy zasobów. To ustawienie dotyczy lokalizacji grupy zasobów i nie ma wpływu na profil usługi Traffic Manager, który będzie wdrażany globalnie.

    6.  Wybierz pozycję **Utwórz**.

    7.  Po zakończeniu globalnego wdrażania profilu usługi Traffic Manager jest wyświetlany w odpowiedniej grupie zasobów jako jeden z zasobów.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

1. Na pasku wyszukiwania portali Wyszukaj ** profilu usługi Traffic Manager ** nazwą utworzoną w poprzedniej sekcji i wybierz profil usługi traffic manager w wynikach, wyświetlana.

2. W **profilu usługi Traffic Manager**w **ustawienia** zaznacz **punktów końcowych**.

3. Wybierz pozycję **Dodaj**.

4. Dodawanie punktu końcowego usługi Azure Stack.

5. Aby uzyskać **typu**, wybierz opcję **zewnętrzny punkt końcowy**.

6. Podaj **nazwa** dla tego punktu końcowego, najlepiej nazwa usługi Azure Stack.

7. Dla w pełni kwalifikowaną nazwę domeny (**FQDN**), Użyj zewnętrznego adresu URL dla aplikacji sieci Web usługi Azure Stack.

8. W obszarze map geograficznych, wybierz region/kontynent, gdzie znajduje się zasób, na przykład **Europa.**

9. W obszarze Kraj/Region listy rozwijanej wyświetlonej, wybierz kraj, które zostaną zastosowane do tego punktu końcowego, na przykład **Niemcy**.

10. Pozycję **Dodaj jako wyłączone** pozostaw niezaznaczoną.

11. Kliknij przycisk **OK**.

12. Dodawanie punktu końcowego platformy Azure:

    1.  Aby uzyskać **typu**, wybierz opcję **punkt końcowy platformy Azure**.

    2.  Podaj **nazwa** dla tego punktu końcowego.

    3.  Aby uzyskać **typ zasobu docelowego**, wybierz opcję **usługi App Service**.

    4.  Aby uzyskać **zasób docelowy**, wybierz opcję **wybierz usługę aplikacji** Aby wyświetlić listę aplikacji sieci Web w ramach tej samej subskrypcji. W **zasobów**, wybierz użycia usług aplikacji w pierwszym punktem końcowym.

13. Wybierz region/kontynent, gdzie znajduje się zasób, na przykład map geograficznych **Ameryka Ameryka Północna/środkowa/Karaiby.**

14. W obszarze Kraj/Region listy rozwijanej wyświetlonej pozostaw to pole puste, aby zaznaczyć wszystkie powyższe grupowanie regionalne.

15. Pozycję **Dodaj jako wyłączone** pozostaw niezaznaczoną.

16. Kliknij przycisk **OK**

    > [!Note]  
    >  Utwórz co najmniej jeden punkt końcowy z geograficzny zakres wszystkich (świat) jako domyślny punkt końcowy dla zasobu.

1. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

    ![Tekst alternatywny](media/azure-stack-solution-geo-distributed/image46.png)

**Globalne przedsiębiorstw opiera się na możliwości dystrybucja geograficzna platformy Azure**

Kierowanie ruchu danych za pomocą usługi Azure Traffic Manager i punktów końcowych w określonej lokalizacji geograficznej umożliwia przedsiębiorstwom globalnego stosować regionalnych przepisy i danych zgodności i bezpieczne zasadnicze znaczenie dla powodzenia firmy lokalnych i w lokalizacjach zdalnych.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
