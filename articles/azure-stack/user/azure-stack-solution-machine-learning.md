---
title: Utwórz krawędzi rozwiązanie uczenia maszynowego z platformą Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć rozwiązanie learning maszyny edge, przy użyciu języka Python z platformą Azure i usługi Azure Stack.
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
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: c195f2ee24b61a57c098d5214a37f65e80845074
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410208"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Samouczek: Tworzenie przeglądarki edge rozwiązanie uczenia maszynowego z platformą Azure i usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak utworzyć krawędzi rozwiązanie uczenia maszynowego z platformą Azure i usługi Azure Stack.

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> - Utwórz konto magazynu i kontener czyste dane znajdują się.
> - Utwórz Ubuntu Data Science Virtual Machine (dsvm dystrybucji) w witrynie Azure portal.
> - Wdrażanie usług Azure Machine Learning na platformie Azure, aby skompilować i szkolenie modeli.
> - Tworzenie konta usługi Azure Machine Learning.
> - Wdrażanie i używanie usługi Azure Container Registry.
> - Wdrażanie klastra Kubernetes w usłudze Azure Stack.
> - Utwórz konto magazynu usługi Azure Stack i kolejki magazynu danych.
> - Tworzenie funkcji nowej usługi Azure Stack, aby przenieść Wyczyść dane z usługi Azure Stack na platformie Azure.

**Kiedy należy używać tego rozwiązania**

 -  Twoja organizacja używa metodyki DevOps, lub jeden zaplanowanych na najbliższą przyszłość.
 -  Chcesz zaimplementować ciągłej integracji/ciągłego wdrażania rozwiązania w zakresie wdrożenia usługi Azure Stack i w chmurze publicznej.
 -  Chcesz skonsolidować potoku ciągłej integracji/ciągłego wdrażania w środowiskach w chmurze i lokalnych.
 -  Ma zdolność do tworzenia aplikacji za pomocą usług w chmurze lub lokalnie.
 -  Chcesz wykorzystać umiejętności programowania spójne w aplikacjach w chmurze i lokalnych.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi aplikacje hybrydowe. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.

## <a name="prerequisites"></a>Wymagania wstępne

Kilka składniki są wymagane do utworzenia ten przypadek użycia i może zająć trochę czasu, aby przygotować:

 -  Partner OEM/sprzętu Azure mogą wdrożyć produkcji usługi Azure Stack i wszyscy użytkownicy mogą wdrażać ASDK

 -  Operator usługi Azure Stack musi również wdrażanie usługi App Service, tworzyć plany i oferty, Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016

 -  Sieć hybrydowa i App Service konfiguracja jest wymagana (Dowiedz się więcej o [integracji aplikacji z sieciami wirtualnymi.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Prywatna [kompilacji i wersji agenta](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) dla integracji usługi VSTS, muszą zostać skonfigurowane przed wdrożeniem (Upewnij się, że wszelkie istniejące składniki używane spełniają wymagania przed rozpoczęciem).

Znajomość platformy Azure i usługi Azure Stack jest wymagana. Aby dowiedzieć się więcej, zanim przejdziesz dalej, Rozpocznij od tych tematów:

 -  [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Kluczowe pojęcia usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Przewodnik rozwiązania ciągłej integracji/ciągłego wdrażania hybrydowego usługi Azure Stack](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Subskrypcja platformy Azure (Tworzenie [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  Nowy adres URL aplikacji sieci Web utworzone przez [aplikacji sieci Web](https://docs.microsoft.com/azure/app-service/app-service-web-overview) na platformie Azure

 -  Wdrażanie [kontenerów platformy Azure Services (ACS) Kubernetes na platformie Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Wdrożenie usługi Azure Machine Learning (wersja zapoznawcza) [część 4 samouczka](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Azure Machine Learning eksperymentowanie w usłudze [konta](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Azure Stack zintegrowany System lub wdrożenia usługi Azure Stack Development Kit.

    - Możesz znaleźć instrukcje dotyczące instalowania usługi Azure Stack w [Zainstaluj zestaw Azure Stack Development Kit](/articles/azure-stack/asdk/asdk-install).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Ta instalacja może wymagać kilka godzin.

 -  Wdrażanie [usługi App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) usługi PaaS do usługi Azure Stack

 -  A [planu/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku usługi Azure Stack

 -  A [dzierżawy subskrypcji](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku usługi Azure Stack

 -  Obrazu maszyny Wirtualnej systemu Ubuntu Server (dostępne w [usługi Azure Stack w portalu Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) ta maszyna wirtualna zostanie utworzona w ramach subskrypcji dzierżawy w usłudze Azure Stack jako agenta kompilacji prywatnych, jak i maszyny wirtualne platformy Kubernetes. Jeżeli ten obraz nie jest dostępny, Operator usługi Azure Stack mogą ułatwić rozwiązanie, aby upewnić się, że jest dodawany do środowiska. (Nie należy używać 18.04 kompilacji ubuntu, ponieważ obecnie jest nieobsługiwana.)

 -  Aplikacja sieci Web w ramach subskrypcji dzierżawy (Uwaga: nowy adres URL aplikacji sieci Web w celu późniejszego użycia.)

 -  Wdrożenia z opartą na systemie Linux w usłudze VSTS prywatnej kompilacji agenta maszyny wirtualnej, w ramach subskrypcji dzierżawy

 -  Wdrożenie [kontenerów platformy Azure Services (ACS) Kubernetes w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Narzędzia dla deweloperów**

 -  [Obszar roboczy usługi VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (proces rejestracji tworzy projekt o nazwie "MyFirstProject")

 -  [Program Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) instalacji i [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) logowania jednokrotnego

 -  [Lokalnego klona](https://www.visualstudio.com/docs/git/gitquickstart) projektu

 -  [Podsystem systemu Linux w systemie Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) instalacji (w przypadku powłoki BASH i ustawieniami SSH)

 -  [Docker for Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) instalacji

 -  [Usługa Azure Machine Learning Workbench (wersja zapoznawcza)](https://aka.ms/azureml-wb-msi) instalacji

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) środowiska instalacji

**VSTS**

 -  **Konto usługi VSTS.** Szybko skonfiguruj ciągłą integrację dla kompilacji, testowania i wdrażania. Aby uzyskać więcej informacji o kontach programu VSTS, zobacz [Utwórz konto usługi VSTS](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **Repozytorium kodu.** Korzystając z istniejących repozytoriów kod w GitHub, BitBucket, DropBox, Onedrive i TFS, platformy VSTS mogą korzystać z wielu repozytoriów kodu, aby usprawnić potoku tworzenia aplikacji. Aby uzyskać więcej informacji o kodzie Zobacz repozytoriów [Rozpoczynanie pracy z usługą Git i usługi VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) samouczka.

 -  **Połączenia z usługą.** Połączyć się z usługami zewnętrznymi i zdalnymi w celu wykonania zadań testowania kompilacji lub wdrożenia. Aby uzyskać więcej informacji o usłudze VSTS połączenia usługi, zobacz [punkty końcowe usługi kompilowanie i wydawanie](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Definicje kompilacji.** Definiowanie procesów automatycznej kompilacji i tworzą zestaw zadań za pośrednictwem katalogu zadania. Aby uzyskać więcej informacji na temat kompilacji Zobacz definicje [utworzyć definicję kompilacji](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentacji.

 -  **Definicji wydania.** Zdefiniuj proces wdrażania przez kolekcję środowiska, w których są wdrażane artefaktów aplikacji. Aby uzyskać więcej informacji o wersji Zobacz definicje [Utwórz definicję wersji](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentacji.

 -  **Pula agentów kompilacji systemu Linux hostowanych w usłudze VSTS.** Szybkie tworzenie, testowanie i wdrażanie aplikacji przy użyciu programu Microsoft i zarządzać hostowany agent. Aby uzyskać więcej informacji na temat hostowanej kompilacji usługi VSTS Zobacz agentów [hostowanych agentów](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) dokumentacji.

## <a name="step-1-create-a-storage-account"></a>Krok 1: Tworzenie konta magazynu

Utwórz konto magazynu i kontener czyste dane znajdują się.

1.  Zaloguj się do [ *witryny Azure portal*](https://portal.azure.com/).

2.  W witrynie Azure portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz **wszystkich usług**. Przewiń w dół do **magazynu** i wybierz polecenie **kont magazynu**. W ** kont magazynu ** wybierz okno **Dodaj**.

3.  Wprowadź nazwę konta magazynu.

    > [!Note]  
    > Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Nazwa konta magazynu musi być unikatowa na platformie Azure. Witryna Azure portal poinformuje, jeśli wybrana nazwa konta magazynu jest już używana.

4.  Określ model wdrożenia, który ma być używany: **usługi Resource Manager**.

5.  Wybierz typ konta magazynu: **ogólnego przeznaczenia w wersji 1**, określ warstwę wydajności: **standardowa**.

6.  Wybierz opcję replikacji dla konta magazynu: **GRS**.

7.  Wybierz nową subskrypcję konta magazynu.

8.  Określ nową grupę zasobów lub wybierz istniejącą grupę zasobów.

9.  Wybierz lokalizację geograficzną dla konta magazynu.

10. Wybierz pozycję **Utwórz**, aby utworzyć konto magazynu.

    ![Tekst alternatywny](\media\azure-stack-solution-machine-learning\image1.png)

11.  Wybierz konto magazynu, ostatnio utworzone.

12.  Wybierz **obiektów blob**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image2.png)

13.  Wybierz **+ kontener** i wybierz **kontenera**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image3.png)

14.  Nadaj nazwę kontenera **uploadeddata** i wybierz typ dostępu **kontenera**.

15.  Wybierz **tworzenie**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Krok 2. Tworzenie maszyny wirtualnej do nauki o danych

Utwórz Ubuntu Data Science Virtual Machine (dsvm dystrybucji) w witrynie Azure portal.

1.  Zaloguj się do witryny Azure portal z [https://portal.azure.com](https://portal.azure.com/)

2.  Wybierz **+ nowy** łącze i wyszukaj "Data nauki maszyny wirtualnej dla systemu Linux Ubuntu dostawcy usług Kryptograficznych

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image5.png)

1.  Wybierz **maszyna wirtualna do nauki o danych dla systemu Linux (Ubuntu)** na liście i postępuj zgodnie z wyświetlanymi na ekranie instrukcjami w celu utworzenia maszyny DSVM.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image6.png)

> ! [Ważne]  
> **Wybierz** hasło ** jako*typ uwierzytelniania*.

W tej samej grupie zasobów co konto nowo utworzonego magazynu, należy umieścić nowej maszyny wirtualnej DSVM. Wszystkie obiekty Edge ML są wdrażane na platformie Azure w tej grupie zasobów.

1.  Konfigurowanie ustawień funkcji opcjonalnych

    a.  Wybierz **konta magazynu** utworzone wcześniej.

    b.  Utwórz nową **sieci wirtualnej**, **podsieci**, i **publiczny adres IP** domyślnie należy go utworzyć nazwę na podstawie nazwy grupy zasobów.

    c.  Utwórz nową **sieciowej grupy zabezpieczeń** automatycznie należy utworzyć ten z regułami poprawne już zastosowany.

    d.  Aby uzyskać **konto magazynu diagnostyki**, wybierz utworzone wcześniej konto magazynu.

    e.  Uwaga: Przy użyciu usługi AAD, włączona i skonfigurowana dla subskrypcji platformy Azure tożsamości usługi zarządzanej można włączyć również.

2.  Kliknij przycisk **OK**.

### <a name="connect-to-the-dsvm"></a>Łączenie do maszyny DSVM

Po utworzeniu maszyny DSVM połączyć z maszyną wirtualną z podsystemu Windows dla systemu Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Typ tak, po wyświetleniu monitu o potwierdzenie połączenia maszyny Wirtualnej.

2.  Wprowadź hasło utworzone dla maszyny DSVM.

### <a name="update-the-dsvm"></a>Aktualizacja maszyny DSVM 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Krok 3: Wdrażanie usługi Azure Machine Learning Services

Wdrażanie usługi Azure Machine Learning Services na platformie Azure.

Usługi Azure Machine Learning (wersja zapoznawcza) to zintegrowane, kompleksowe rozwiązanie do nauki o danych i przeprowadzania zaawansowanych analiz. Pomaga ono profesjonalnym analitykom przygotowywać dane, opracowywać eksperymenty i wdrażać modele w skali chmury.

W tej sekcji opisano:

> [!div class="checklist"]
> - Tworzenie kont usługi dla usług Azure Machine Learning
> - Instalowanie aplikacji Azure Machine Learning Workbench i logowanie się do niej
> - Tworzenie projektu w aplikacji Workbench
> - Uruchamianie skryptu w tym projekcie
> - Uzyskiwanie dostępu do interfejsu wiersza polecenia

Będące częścią portfolio platformy Microsoft Azure usługi Azure Machine Learning wymagają subskrypcji platformy Azure. Aby uzyskać subskrypcję platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Odpowiednie uprawnienia są wymagane do utworzenia zasobów, takich jak grupy zasobów, maszyny wirtualne i inne zasoby.

Aplikacja Azure Machine Learning Workbench może być instalowane w następujących systemach operacyjnych:

 -  Windows 10 lub Windows Server 2016
 -  macOS Sierra lub High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Krok 4: Tworzenie usługi Azure Machine Learning

Tworzenie konta usługi Azure Machine Learning.

Aprowizuj konta usługi Azure Machine Learning za pomocą witryny Azure portal:

1.  Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu poświadczeń subskrypcji platformy Azure do użycia. Aby uzyskać subskrypcję platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image7.png)

1.  Wybierz przycisk **Utwórz zasób** (+) w lewym górnym rogu portalu.

    ![Tworzenie zasobu w witrynie Azure Portal](media\azure-stack-solution-machine-learning\image8.png)

1.  Wpisz ciąg **Machine Learning** na pasku wyszukiwania. Wybierz wynik wyszukiwania o nazwie **Eksperymentowanie w usłudze Machine Learning (wersja zapoznawcza)**.

    ![Wyszukiwanie w usłudze Azure Machine Learning](media\azure-stack-solution-machine-learning\image9.png)

1.  W **eksperymentowanie w usłudze Machine Learning** okienko, przewiń do dołu i wybierz pozycję **Utwórz** aby zacząć definiować konta eksperymentowania.

    ![Azure Machine Learning — tworzenie konta do eksperymentowania](media\azure-stack-solution-machine-learning\image10.png)

1.  W **eksperymentowanie** okienko Konfigurowanie konta eksperymentowania w usłudze Machine Learning.

    | Ustawienie | Sugerowane wartości dla samouczka | Opis |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nazwa konta Eksperymentowanie | Unikatowa nazwa | Wprowadź unikatową nazwę, która identyfikuje konto. Użyj nazwy działu lub projektu, która najlepiej identyfikuje eksperyment. Nazwa powinna mieć od 2 do 32 znaków. Powinna ona zawierać tylko znaki alfanumeryczne i znak łącznika (-). |
    | Subskrypcja | Subskrypcja | Wybierz subskrypcję platformy Azure na potrzeby eksperymentu. Jeśli istnieje wiele subskrypcji, wybierz odpowiednią subskrypcję, w którym zasób jest naliczana. |
    | Grupa zasobów | Grupa zasobów | Użyj istniejącej grupy zasobów w ramach subskrypcji, lub wprowadź nazwę, aby utworzyć nową grupę zasobów dla tego konta eksperymentowania. |
    | Lokalizacja | Region najbliżej użytkowników | Wybierz lokalizację znajdującą się najbliżej użytkowników i zasobów danych. |
    | Liczba stanowisk | 2 | Wprowadź liczbę stanowisk. Dowiedz się, jak [liczba stanowisk przekłada się na ceny](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>W tym przewodniku Szybki Start potrzebne są tylko dwa stanowiska. Stanowiska można dodawać i usuwać w miarę potrzeb w witrynie Azure Portal. |
    | Konto magazynu | Unikatowa nazwa | Wybierz pozycję **Utwórz nowy** i podaj nazwę, aby utworzyć [konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). Nazwa zawierać od 3 do 24 znaków oraz tylko znaki alfanumeryczne. Można także wybrać **Użyj istniejącej** i wybierz istniejące konto magazynu z listy. Konto magazynu jest wymagane i jest używane do przechowywania artefaktów projektu oraz danych historii uruchamiania. |
    | Obszar roboczy dla konta Eksperymentowanie | IrisGarden<br>(nazwa używana w samouczkach) | Podaj nazwę obszaru roboczego dla tego konta. Nazwa powinna mieć od 2 do 32 znaków. Powinna ona zawierać tylko znaki alfanumeryczne i znak łącznika (-). Ten obszar roboczy zawiera narzędzia potrzebne do tworzenia, zarządzania i publikowania eksperymentów. |
    | Przypisz właściciela obszaru roboczego | To konto | Wybierz własne konto jako właściciela obszaru roboczego. |
    | Tworzenie konta usługi Zarządzanie modelami | **zaznaczone** | Utwórz konto zarządzania modelami. To będzie służyć do wdrażania modeli i zarządzania nimi jako usługami internetowymi w czasie rzeczywistym. <br><br>Chociaż jest to opcjonalne, zaleca się utworzenie konta Zarządzanie modelami w tym samym czasie co konto usługi eksperymentowanie w usłudze. |
    | Nazwa konta | Unikatowa nazwa | Wybierz unikatową nazwę, która identyfikuje konto zarządzania modelami. Użyj dla działów lub nazwę projektu, która najlepiej identyfikuje eksperyment. Nazwa powinna mieć od 2 do 32 znaków. Powinna ona zawierać tylko znaki alfanumeryczne i znak łącznika (-). |
    | Warstwa cenowa Zarządzanie modelami | **DEVTEST** | Wybierz **nie wybrano warstwy cenowej** Aby określić warstwę cenową dla nowego konta Zarządzanie modelami. Celu uzyskania oszczędności wybierz warstwę cenową DEVTEST, jeśli jest dostępna w ramach subskrypcji (ograniczona dostępność). W przeciwnym razie wybierz warstwę cenową S1. Wybierz pozycję Wybierz, aby zapisać warstwę cenową. |
    | Przypnij do pulpitu nawigacyjnego | znacznik wyboru | Wybierz **Przypnij do pulpitu nawigacyjnego** opcję, aby umożliwić łatwe śledzenie konta eksperymentowanie w usłudze Machine Learning na pierwszej stronie pulpitu nawigacyjnego witryny Azure Portal. |

    ![Konfiguracja konta Eksperymentowania w usłudze Machine Learning](media\azure-stack-solution-machine-learning\image11.png)

1.  Wybierz pozycję **Utwórz**, aby zacząć proces tworzenia konta Eksperymentowania oraz konta Zarządzania modelami.

    ![Konfiguracja konta Eksperymentowania w usłudze Machine Learning](media\azure-stack-solution-machine-learning\image12.png)

    Może upłynąć kilka minut, aby utworzyć konto. Stan procesu wdrażania należy sprawdzić, wybierając ikonę powiadomienia (dzwonek) na pasku narzędzi portalu Azure.

    ![Powiadomienia w witrynie Azure Portal](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Instalowanie i zaloguj się do aplikacji workbench 

Aplikacja Azure Machine Learning Workbench jest dostępna dla systemów Windows i macOS. Zobacz listę [obsługiwanych platform](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

**Ostrzeżenie:** instalacja może zająć około godziny, aby zakończyć.

1.  Pobierz i uruchom najnowszego instalatora aplikacji Workbench.

    > [!Important]  
    > Pobierz instalatora w całości na dysk, a następnie uruchom go z tego miejsca. Nie uruchamiaj go bezpośrednio z widżetu pobierania w przeglądarce.<br>**Na Windows:<br>**  . Pobierz plik [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. Kliknij dwukrotnie pobrany plik instalatora w Eksploratorze plików.

1.  Postępuj zgodnie z wyświetlanymi na ekranie instrukcjami Instalatora w celu ukończenia.

    Instalacja może zająć nawet 30 minut. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    Instalator pobierze i skonfiguruj wszystkie niezbędne zależności, takie jak Python, Miniconda i inne powiązane biblioteki. Ta instalacja obejmuje również narzędzie wiersza polecenia platformy Azure dla wielu platform lub wiersza polecenia platformy Azure.

1.  Uruchom aplikację Workbench, wybierając przycisk **Uruchom aplikację Workbench** na ostatnim ekranie instalatora.

    Jeśli Instalator zostanie zamknięty, uruchamiać **Machine Learning Workbench** skrót na pulpicie.

1.  Wybierz **Zaloguj się przy użyciu Microsoft** do uwierzytelniania w usłudze Azure Machine Learning Workbench. Użyj tych samych poświadczeń, które są używane w witrynie Azure portal do tworzenia kont eksperymentowania oraz zarządzania modelami.

    Po zalogowaniu aplikacja Workbench użyje pierwszego konta eksperymentowania, znajduje się w subskrypcji platformy Azure i wyświetli wszystkie obszary robocze oraz projekty skojarzone z tym kontem.

    > [!Tip]  
    > Przełącz się do innego konta eksperymentowania, używając ikony w lewym dolnym rogu okna aplikacji Workbench.

### <a name="create-a-new-project-in-workbench"></a>Utwórz nowy projekt w aplikacji workbench

1.  Otwórz aplikację Azure Machine Learning Workbench i zaloguj się, jeśli to konieczne.

    - W Windows, uruchamiać **Machine Learning Workbench** skrót na pulpicie.

    - W systemie macOS wybierz pozycję **Azure ML Workbench** w programie Launchpad.

1.  W okienku **PROJEKTY** wybierz znak plus (+), a następnie wybierz pozycję **Nowy projekt**.

    ![Nowy obszar roboczy](media\azure-stack-solution-machine-learning\image14.png)

1.  Wypełnij pola formularza i wybierz przycisk **Utwórz**, aby utworzyć nowy projekt w aplikacji Workbench.

    | Pole | Sugerowane wartości dla samouczka | Opis |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Project name (Nazwa projektu) | myIris | Wprowadź unikatową nazwę, która identyfikuje konto. Użyj dla działów lub nazwę projektu, która najlepiej identyfikuje eksperyment. Nazwa powinna mieć od 2 do 32 znaków. Powinna ona zawierać tylko znaki alfanumeryczne i znak łącznika (-). |
    | Katalog projektu | c:\Temp\ | Określ katalog, w którym zostanie utworzony projekt. |
    | Opis projektu | Pozostaw to pole puste | Pole opcjonalne przydatne do opisywania projektów. |
    | Adres URL repozytorium usługi GIT VisualStudio.com | Pozostaw to pole puste | Pole opcjonalne. Skojarz projekt z repozytorium Git w usłudze Visual Studio Team Services do kontroli źródła i współpracy. [Dowiedz się, jak skonfigurować repozytorium](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Wybrany obszar roboczy | IrisGarden (jeśli istnieje) | Wybierz obszar roboczy utworzony dla konta eksperymentowania w witrynie Azure portal. <br>Korzystając z przewodnika Szybki Start, znajduje się obszar roboczy nazwie IrisGarden. W przeciwnym razie Użyj obszaru roboczego przy użyciu nazwy konta eksperymentowania lub nazwę preferowanego konta. |
    | Szablon projektu | Classifying Iris | Szablony zawierają skrypty i dane, pozwala zapoznać się z produktem. Ten szablon zawiera skrypty i dane wymagane przez ten przewodnik Szybki Start i samouczków w tej witrynie dokumentacji. |

    ![Nowy projekt](media\azure-stack-solution-machine-learning\image15.png)

1.  Utworzony zostanie nowy projekt i otworzy się pulpit nawigacyjny projektu z tym projektem. Poznaj stronę główną projektu, źródła danych, notesy i pliki kodu źródłowego.

    ![Otwieranie projektu](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Dołączanie maszyny wirtualnej DSVM obliczeniowego elementu docelowego

Po utworzeniu maszyny DSVM dołącza go do projektu usługi uczenie Maszynowe Azure.

1.  Z poziomu aplikacji Azure ML Workbench Uruchom wiersza polecenia platformy Azure ML Workbench, wybierając **pliku**->**Otwórz program PowerShell**

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image17.png)

1.  Po programu PowerShell, wiersz został otwarty, użyj następującego polecenia:

    ```PowerShell  
        az login
    ```

1.  Występuje następujący wiersz:

     ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image18.png)

1.  Przejdź do witryny zgodnie z opisem w wierszu polecenia i wprowadź kod, który jest dostarczany.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image19.png)

1.  Wybierz pozycję Kontynuuj, po wyświetleniu monitu, a następnie wybierz konto platformy Azure, którą skojarzona jest konto eksperymentalne uczenie Maszynowe Azure.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image20.png)

1.  Interfejsu wiersza polecenia platformy Azure ML Workbench prześle następujący wiersz:

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image21.png)

1.  Podczas logowania ML konto i obszar roboczy jest wyświetlany jako pomyślne, można dołączyć maszyny DSVM.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    Pojawi się następujące powiadomienia:

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Ten proces może potrwać pewien czas i wygeneruje znacznej ilości tekstu, zgodnie z jego ściąga różnych obrazów platformy docker, rejestruje je, a następnie stosuje potrzebne kodu i aplikacje do nich.

Teraz można uruchomić w tej maszyny wirtualnej DSVM eksperymentów.

### <a name="create-a-data-preparation-package"></a>Tworzenie pakietu przygotowywania danych

Następnie rozpocząć ich przygotowywanie w aplikacji Azure Machine Learning Workbench. Każde przekształcenie wykonywane w aplikacji Workbench jest przechowywane w formacie JSON w lokalnym pakiecie przygotowywania danych (\*.dprep pliku). Ten pakiet przygotowywania danych jest podstawowym kontenerem na potrzeby przygotowywaniem danych w aplikacji Workbench.

Ten pakiet przygotowywania danych można go przekazać później do środowiska uruchomieniowego, takich jak C lokalnego\#/CoreCLR, Scala/Spark lub Scala/HDI.

1.  Wybierz ikonę folderu, aby otworzyć widok Pliki, a następnie wybierz plik **iris.csv** w celu jego otwarcia.

    Ten plik zawiera tabelę składającą się z 5 kolumn i 50 wierszy. Cztery kolumny są kolumnami funkcji liczbowych. Piąta kolumna jest kolumną docelową ciągu. Żadna z tych kolumn nie ma nazwy nagłówka.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  W obszarze **Widok danych** wybierz znak plus (**+**), aby dodać nowe źródło danych. Zostanie otwarta strona **Dodawanie źródła danych**.

    ![Widok danych w aplikacji Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image24.png)

1.  Wybierz **pliki tekstowe (\*CSV, \*.json, \*.txt.,...)** .

    ![Źródło danych w usłudze Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image25.png)

1.  Wybierz opcję **Dalej**.

2.  Przejdź do pliku **iris.csv**i wybierz **Zakończ**. Spowoduje to użycie wartości domyślnych parametrów, takich jak separator i typy danych.

    > [!Important]  
    > Wybierz **iris.csv** plik z bieżącego katalogu projektu na potrzeby tego ćwiczenia. W przeciwnym razie kolejnych krokach może zakończyć się niepowodzeniem.

    ![Wybierz iris](media\azure-stack-solution-machine-learning\image26.png)

1.  Nowy plik o nazwie `*iris-1.dsource` zostanie utworzony. Unikatową nazwę pliku z `-1` ponieważ przykładowy projekt zawiera już nienumerowany **iris.dsource** pliku.

    Plik zostanie otwarty i widoczne będą dane. Nagłówki kolumn od **Kolumna1** do **Column5**, jest automatycznie dodawany do tego zestawu danych. Przewiń w dół i zwróć uwagę, że ostatni wiersz z zestawu danych jest pusta. Wiersz jest pusty, z powodu dodatkowego podziału wiersza w pliku CSV.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image27.png)

1.  Wybierz przycisk **Metryki**. Spowoduje to wygenerowanie i wyświetlenie histogramów.

    Przejdź z powrotem do widoku danych, wybierając **danych** przycisku.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image28.png)

1.  Przyjrzyj się histogramom. Dla każdej kolumny został obliczony kompletny zestaw statystyk.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image29.png)

1.  Rozpocznij tworzenie pakietu przygotowywania danych, naciskając przycisk **Przygotowywanie**. Zostanie otwarte okno dialogowe **Przygotowywanie**.

    Przykładowy projekt zawiera **iris.dprep** plik przygotowywania danych, która jest domyślnie zaznaczone.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image30.png)

1.  Utwórz nowy pakiet przygotowania danych, wybierając **+ nowy pakiet przygotowania danych** z menu.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image31.png)

1.  Wprowadź nową wartość nazwy pakietu (użyj nazwy **iris-1**), a następnie wybierz pozycję **OK**.

    Nowy pakiet przygotowania danych o nazwie **iris-1.dprep** zostanie utworzony i otwarty w edytorze przygotowywania danych.

    ![Widok danych irysów](media\azure-stack-solution-machine-learning\image32.png)

    Następnie przygotowywania danych jest wymagana.

1.  Zaznacz poszczególne nagłówki kolumn, aby przystąpić do ich edycji. Następnie zmień nazwę każdej kolumny w następujący sposób:

    W porządku, wprowadź **długość słupka**, **szerokość słupka**, **długość płatka**, **szerokość płatka**, i **gatunków** w pięciu kolumnach odpowiednio.

    ![Zmienianie nazw kolumn](media\azure-stack-solution-machine-learning\image33.png)

1.  Zlicz unikatowe wartości:

    1.  Wybierz kolumnę **Gatunki**.

    2.  Kliknij prawym przyciskiem myszy, aby ją zaznaczyć.

    3.  Wybierz **liczba wartości** z menu.

        Poniżej danych zostanie otwarte okienko **Inspektorzy**. Zostanie wyświetlony histogram z czterema paskami. Kolumna docelowa ma cztery różne wartości: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**, a **(null)** wartość.

    ![Wybieranie pozycji Liczba wartości](media\azure-stack-solution-machine-learning\image34.png)

    ![Histogram liczby wartości](media\azure-stack-solution-machine-learning\image35.png)

1.  Aby odfiltrować wartości null, wybierz słupek „(null)”, a następnie wybierz znak minus (**-**).

    Po chwili wiersz o wartości (null) zmieni kolor na szary w celu wskazania, że został on odfiltrowany.

    ![Filtrowanie wartości null](media\azure-stack-solution-machine-learning\image36.png)

1.  Zwróć uwagę na poszczególne etapy przygotowywania danych, które zostały szczegółowo opisane w okienku **KROKI**. Zostały zmienione kolumn i filtrowania wierszy z wartością null, każde działanie jest rejestrowane jako krok przygotowywania danych. Poszczególne kroki, aby dostosować ich ustawienia, zmienić kolejność kroków i usunąć kroki edytować.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image37.png)

1.  Zamknij Kreatora przygotowywania danych. Wybierz ikonę **x** na karcie **iris-1** z ikoną wykresu, aby zamknąć kartę. Praca zostanie automatycznie zapisana w **iris-1.dprep** plik wyświetlany w obszarze **przygotowywanie danych** nagłówka.

    ![Zamykanie](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Generowanie kodu Python w celu wywołania pakietu przygotowywania danych

Dane wyjściowe pakietu przygotowywania danych można eksplorować bezpośrednio w języku Python lub w notesie Jupyter. Pakiety mogą być wykonywane w różnych środowiskach uruchomieniowych, między innymi w lokalnych środowiskach języka Python, aparatu Spark (w tym Docker) i usługi HDInsight.

1.  Znajdź plik **iris-1.dprep** na karcie Przygotowywanie danych.

2.  Kliknij prawym przyciskiem myszy plik **iris-1.dprep**, a następnie z menu kontekstowego wybierz opcję **Generuj plik kodu dostępu do danych**.

    ![Generowanie kodu](media\azure-stack-solution-machine-learning\image39.png)

    Nowy plik o nazwie **iris-1.py** otwiera się z następującymi wierszami kodu do wywołania logiki utworzone jako pakiet przygotowywania danych:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    W zależności od kontekstu uruchamiania kodu drep reprezentuje inny rodzaj ramki DataFrame:

    -  Podczas wykonywania w środowisku uruchomieniowym języka Python jest używana ramka danych [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -  Podczas wykonywania w kontekście aparatu Spark jest używana ramka danych [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html).

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Przeglądanie pliku iris_sklearn.py i plików konfiguracji

1.  W otwartym projekcie wybierz **pliki** przycisku (ikona folderu) w okienku daleko po lewej stronie, aby otworzyć listę plików w folderze projektu.

    ![Otwieranie projektu w aplikacji Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image40.png)

1.  Wybierz plik skryptu języka Python **iris_sklearn.py**.

    ![Wybieranie skryptu](media\azure-stack-solution-machine-learning\image41.png)

    Kod zostanie otwarty w nowej karcie edytora tekstów wewnątrz aplikacji Workbench.

    > [!Note]  
    > Kod wyświetlany może nie być dokładnie takie same, jak kod powyżej, ponieważ ten przykładowy projekt jest często aktualizowany.

    ![Otwieranie pliku](media\azure-stack-solution-machine-learning\image42.png)

1.  Zbadaj kod skryptu języka Python, aby zapoznać się ze stylem kodowania.

    Skrypt **iris_sklearn.py** wykonuje następujące zadania:  

    -  Ładuje domyślny pakiet przeznaczony do przygotowania danych o nazwie **iris.dprep** w celu utworzenia struktury danych [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Dodaje losowe funkcje utrudniające rozwiązanie problemu. Losowość jest konieczna, ponieważ Iris jest małym zestawem danych, który można łatwo sklasyfikować z niemal 100-procentową dokładnością.

    -  Używa biblioteki uczenia thescikit learnmachine do zbudowania modelu regresji logistycznej. Ta biblioteka jest domyślnie dostarczana razem z aplikacją Azure Machine Learning Workbench.

    -  Serializuje model przez użycie biblioteki [pickle](https://docs.python.org/3/library/pickle.html) w pliku w folderze `outputs`.

    -  Ładuje zserializowany model i deserializuje go z powrotem do pamięci.

    -  Za pomocą modelu po deserializacji przewiduje nowy rekord.

    -  Kreśli dwa wykresy — macierz pomyłek i odbiorcę wieloklasowej krzywej cechy (ROC) działania, za pomocą [matplotlib](https://matplotlib.org/) biblioteki, a następnie zapisuje je w theoutputsfolder. Jeśli jest to jasne, nie należy zainstalować tę bibliotekę w środowisku.

    -  Drukuje automatycznie uregulowania szybkości i dokładności modelu w historii uruchamiania. Obiekt `run_logger` jest używany przez cały czas w celu rejestrowania współczynnika uregulowania i modelowania dokładności w dziennikach.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Uruchamianie skryptu iris_sklearn.py w środowisku lokalnym

1.  Uruchom interfejs wiersza polecenia usługi Azure Machine Learning:

    1.  Uruchom aplikację Azure Machine Learning Workbench.

    2.  Z menu aplikacji Workbench wybierz kolejno pozycje **Plik**> **Otwórz wiersz polecenia**.

    W oknie interfejsu wiersza polecenia (CLI) usługi Azure Machine Learning, który rozpoczyna się w folderze projektu `C:\Temp\\myIris\` na Windows. Ten projekt jest taka sama jak rejestr utworzony w części 1 tego samouczka.

    > [!Important]  
    > To okno interfejsu wiersza polecenia do wykonania kolejnych kroków.

1.  W oknie interfejsu wiersza polecenia Zainstaluj bibliotekę wykreślania języka Python **matplotlib**, jeśli nie jest jeszcze zainstalowana.

    Skrypt **iris_sklearn.py** ma zależności z dwoma pakietami języka Python: **scikit-learn** i **matplotlib**. **Scikit-informacje** pakiet jest zainstalowany przez aplikację Azure Machine Learning Workbench dla wygody. Jednak instalacja **matplotlib** mogą być wymagane.

    Jeśli kontynuowane bez konieczności instalowania **matplotlib**, kod w tym samouczku może nadal działać prawidłowo. Nie wygeneruje on jednak wykresu wyjściowej macierzy pomyłek ani wykresu wieloklasowej krzywej ROC, które przedstawiono w wizualizacjach historycznych.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    Ta instalacja trwa około minuty.

1.  Wróć do aplikacji Workbench.

2.  Znajdź kartę o nazwie **iris_sklearn.py**.

    ![Wyszukiwanie karty ze skryptem](media\azure-stack-solution-machine-learning\image43.png)

1.  Na pasku narzędzi tej karty, wybierz **lokalnego** jako środowisko wykonawcze andiris_sklearn.pyas skrypt do uruchomienia. Te elementy mogły już zostać wybrane.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image44.png)

1.  Przesuń prawą stronę paska narzędzi i enter0.01in **argumenty** pola.

    Ta wartość odpowiada współczynnikowi uregulowania modelu regresji logistycznej.

    ![Wybieranie pozycji „lokalne” i skryptu](media\azure-stack-solution-machine-learning\image45.png)

1.  Wybierz przycisk **Uruchom**. Nastąpi natychmiastowe zaplanowanie zadania. To zadanie jest widoczne w panelu **Zadania** po prawej stronie okna aplikacji Workbench.

    ![Wybieranie pozycji „lokalne” i skryptu](media\azure-stack-solution-machine-learning\image46.png)

    Po kilku chwilach stan zadania przejścia z **przesyłanie**, **systemem**, a na koniec do **Ukończono**.

1.  Wybierz wyraz **Ukończono** w tekście stanu zadania w panelu **Zadania**.

    ![Uruchamianie skryptu sklearn](media\azure-stack-solution-machine-learning\image47.png)

    Okno podręczne otwiera i wyświetla tekst wyjścia standardowego (stdout) dla uruchomienia. Aby zamknąć tekst stdout, wybierz **Zamknij** (**x**) przycisk w prawym górnym rogu okna podręcznego.

    ![Wyjście standardowe](media\azure-stack-solution-machine-learning\image48.png)

1.  W tym samym stanie zadania w **zadania** okienku zaznacz **iris_sklearn.py niebieski tekst \[n\] **(* n * to numer uruchomienia) tuż nad  **Ukończono** stanu i czasem uruchomienia. Zostanie otwarte okno **Właściwości uruchamiania**, które zawiera następujące informacje dotyczące konkretnego uruchomienia:

    -  Informacje w oknie **Właściwości uruchomienia**

    -  **Dane wyjściowe**

    -  **Metryki**

    -  **Wizualizacje**, jeśli są dostępne

    -  **Dzienniki**

    Gdy uruchomienie zostanie zakończone, pojawi się okno podręczne z następującymi wynikami:

    > [!Note]  
    > Do samouczka wprowadziliśmy Generowanie losowe do zestawu szkoleniowego, uzyskane wyniki mogą się nieznacznie różnić od widocznych tutaj.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Zamknij kartę **Właściwości uruchomienia**, a następnie wróć do karty **iris_sklearn.py**.

1.  Powtórz te czynności dla dodatkowych uruchomień.

W polu **Argumenty** wprowadź serię wartości z zakresu od `0.001` do `10`. Wybierz przycisk **Uruchom**, aby wykonać ten kod jeszcze kilka razy. Wartość argumentu zmieniana jest podawany do modelu regresji logistycznej w kodzie, przez co różne każdorazowo.

### <a name="review-the-run-history-in-detail"></a>Przeglądanie szczegółów historii uruchamiania

W aplikacji Azure Machine Learning Workbench Każde wykonanie skryptu jest przechwytywane jako rekord historii uruchamiania. Wyświetl historię uruchamiania konkretnego skryptu, otwierając **przebiegów** widoku.

1.  Aby otworzyć listę **Uruchomienia**, wybierz przycisk **Uruchomienia** (ikona zegara) na pasku narzędzi po lewej stronie. Następnie wybierz pozycję **iris_sklearn.py** pokazanie **pulpit nawigacyjny uruchomień** ofiris_sklearn.py.

    ![Widok uruchomienia](media\azure-stack-solution-machine-learning\image49.png)

1.  Zostanie otwarta karta **Pulpit nawigacyjny uruchomień**.

    Przejrzyj statystyki przechwycone z wielu uruchomień. Wykresy są renderowane w górnej części karty. Każde uruchomienie ma kolejny numer, a szczegóły dotyczące uruchomienia są wymienione w tabeli u dołu ekranu.

    ![Pulpit nawigacyjny uruchomień](media\azure-stack-solution-machine-learning\image50.png)

1.  Filtruj tabelę i wybieraj dowolne wykresy, aby wyświetlić stan, czas trwania, dokładność i współczynnik uregulowania każdego uruchomienia.

2.  Zaznacz pola wyboru obok co najmniej dwóch uruchomień w tabeli **Uruchomienia**. Wybierz przycisk **Porównaj**, aby otworzyć okienko ze szczegółowym porównaniem. Porównaj pozycje zestawione obok siebie.

3.  Aby wrócić do **pulpitu nawigacyjnego uruchomień**, wybierz przycisk Wstecz **listy Uruchomienia** w lewym górnym rogu okienka **Porównanie**.

    ![Powrót do listy uruchomień](media\azure-stack-solution-machine-learning\image51.png)

1.  Wybierz pojedyncze uruchomienie, aby wyświetlić widok szczegółów uruchomienia. Zwróć uwagę na to, że statystyki dotyczące wybranego uruchomienia będą widoczne w sekcji **Właściwości uruchomienia**. Pliki zapisane w folderze wyjściowym są wymienione w **dane wyjściowe** sekcji, a następnie pobrać pliki z tego miejsca.

    ![Szczegóły uruchomienia](media\azure-stack-solution-machine-learning\image52.png)

Dwa wykresy — macierz pomyłek i wieloklasowa krzywa ROC — zostaną zrenderowane w sekcji **Wizualizacje**. Wszystkie pliki dziennika można znaleźć w sekcji **Dzienniki**.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Uruchamianie skryptów w oknie interfejsu wiersza polecenia Azure Machine Learning (ML) aplikacji Workbench

1.  Uruchom interfejs wiersza polecenia usługi Azure Machine Learning:

    1.  Uruchom aplikację Azure Machine Learning Workbench.

    2.  Z menu aplikacji Workbench wybierz kolejno pozycje **Plik** > **Otwórz wiersz polecenia**.

    Zostanie otwarte okno interfejsu wiersza polecenia w folderze projektu `C:\\Temp\\myIris` na Windows. Jest to projekt utworzony w części 1 tego samouczka.

    > [!Important]  
    > To okno interfejsu wiersza polecenia do wykonania kolejnych kroków.

1.  W oknie interfejsu wiersza polecenia zaloguj się do platformy Azure. [Dowiedz się więcej o poleceniu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Pomiń ten krok, jeśli jest zalogowany.

1.  W wierszu polecenia wprowadź:

    ```CLI
        az login
    ```

    To polecenie zwraca kod do użycia w przeglądarce pod adresem [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Przejdź do [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) w przeglądarce. Wprowadź kod uzyskany z odebrany w interfejsie wiersza polecenia.

    Aplikacja Workbench i interfejs wiersza polecenia Użyj pamięci podręcznych poświadczeń podczas uwierzytelniania w zasobach platformy Azure. Uwierzytelnianie nie jest wymagane ponownie do czasu wygaśnięcia tokenu z pamięci podręcznej.

1.  Jeśli organizacja ma wiele subskrypcji platformy Azure (środowisko przedsiębiorstwa), należy ustawić subskrypcję do użycia. Znalezienia subskrypcji, ustaw ją za pomocą Identyfikatora subskrypcji i przetestuj.

1.  Wyświetl każdy dostęp do subskrypcji platformy Azure za pomocą tego polecenia:

    ```CLI
        az account list -o table 
    ```

    **Listy kont az** polecenia zwraca listę subskrypcji dostępnych do logowania. Jeśli istnieje więcej niż jeden, określ wartość Identyfikatora subskrypcji używanej w subskrypcji.

1.  Ustaw subskrypcję platformy Azure używane jako konto domyślne:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Gdzie < identyfikator subskrypcji > jest wartość Identyfikatora subskrypcji używany. Nie uwzględniaj nawiasów.

1.  Potwierdź nowe ustawienie subskrypcji, żądając szczegółów bieżącej subskrypcji.

    ```CLI
        az account show
    ```

1.  W oknie interfejsu wiersza polecenia prześlij skrypt **iris_sklearn.py** jako eksperyment.

    Wykonywanie skryptu iris_sklearn.py jest uruchamiane w lokalnym kontekście przetwarzania.

1.  W systemie Windows:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  W systemie macOS:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    Dane wyjściowe powinny wyglądać podobnie do: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Przejrzyj dane wyjściowe. Należy wszystkie te same dane wyjściowe i wyniki w postaci uruchomienie skryptu aplikacji Workbench.

7.  Wróć do aplikacji Workbench, a następnie:

    Wybierz ikonę folderu w okienku po lewej stronie, aby wyświetlić listę plików projektu.  Otwórz skrypt języka Python o nazwie **run.py**. Ten skrypt jest przydatny do zapętlania różnych współczynników uregulowania. 

    ![Powrót do listy uruchomień](media\azure-stack-solution-machine-learning\image53.png)

1.  Uruchom eksperyment wiele razy z tymi współczynnikami.

    Ten skrypt uruchamia` aniris_sklearn.pyjob` z o współczynnika uregulowania `10.0` (bardzo duża liczba). Skrypt następnie skraca współczynnik o połowę w kolejnym uruchomieniu i tak dalej, aż współczynnik nie będzie mniejszy niż `0.005`. Skrypt zawiera następujący kod:

    ![Powrót do listy uruchomień](media\azure-stack-solution-machine-learning\image54.png)

1.  Uruchom skrypt **run.py** z poziomu wiersza polecenia w następujący sposób:

    ```CLI
        python run.py
    ```
To polecenie przesyła `iris_sklearn.py` wiele razy z różnymi współczynnikami uregulowania

Gdy `run.py` zostanie zakończone, wykres różnych metryk są wyświetlane w widoku listy historii uruchamiania w aplikacji workbench.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Uruchamianie skryptów w oparta na systemie Ubuntu Data Science Virtual Machine (dsvm dystrybucji) na platformie Azure

Można wykonać skryptu w kontenerze platformy Docker na komputerze zdalnym z systemem Linux, dostęp protokołu SSH (nazwa użytkownika i hasło) jest wymagane do uruchomienia na komputerze zdalnym. Ponadto na maszynie należy zainstalować i uruchomić aparat platformy Docker.

1.  Edytuj wygenerowany<your DSVM Name>.runconfigfile underaml_configand Zmień framework z valuePySparktoPython domyślne:

    ```yaml  
    Framework: Python
    ```
1.  Wydaj to samo polecenie jak poprzednio w oknie interfejsu wiersza polecenia przy użyciu docelowej*<DSVM>* tym razem w celu uruchomienia skryptu iris_sklearn.py w zdalnym kontenerze platformy Docker: (Zastąp <DSVM> o nazwie maszyny Wirtualnej do nauki o danych, bez nawiasów).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

Polecenie zostanie wykonane tak, jakby w adocker pythonenvironment, z wyjątkiem jest wykonywany na zdalnej maszynie Wirtualnej z systemem Linux. W oknie interfejsu wiersza polecenia pojawią się takie same informacje wyjściowe.

### <a name="download-the-model-pickle-file"></a>Pobieranie pliku modelu utworzonego w pakiecie pickle

W poprzedniej części samouczka skrypt **iris_sklearn.py** został uruchomiony lokalnie w programie Machine Learning Workbench. Ta akcja spowodowała serializowanie modelu regresji logistycznej przy użyciu popularnego pakietu [pickle](https://docs.python.org/3/library/pickle.html) do serializacji obiektów w języku Python.

1.  Otwórz aplikację Machine Learning Workbench. Następnie otwórz **myIris** projektu utworzonego w poprzedniej części tej serii samouczka.

2.  Po otwarciu projektu wybierz **pliki** przycisku (ikona folderu) w okienku po lewej stronie, aby otworzyć listę plików w folderze projektu.

3.  Wybierz plik **iris_sklearn.py**. Kod Python zostanie otwarty w nowej karcie edytora tekstów wewnątrz aplikacji Workbench.

4.  Przejrzyj plik **iris_sklearn.py**, aby dowiedzieć się, gdzie został wygenerowany plik z pakietu pickle. Użyj kombinacji klawiszy Ctrl+F, aby otworzyć okno dialogowe **znajdowania**, a następnie znajdź słowo **pickle** w kodzie języka Python.

Następujący fragment kodu przedstawia, jak został wygenerowany plik wyjściowy z pakietu pickle. Plik wyjściowy z pakietu pickle ma na dysku nazwę **model.pkl**.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Odszukaj plik modelu z pakietu pickle w plikach wyjściowych z poprzedniego przebiegu.

    Gdy **iris_sklearn.py** skrypt jest uruchamiany, model jest zapisany plik **generuje** folder o nazwie **model.pkl**. Ten folder znajduje się w środowisku wykonawczym wybrana do uruchomienia skryptu, a nie w lokalnym folderze projektu. 

    1. Aby zlokalizować plik, wybierz pozycję **przebiegów** przycisku (ikona zegara) w okienku po lewej stronie, aby otworzyć listę **wszystkie przebiegi**.  

    2. Zostanie otwarta karta **Wszystkie przebiegi**. W tabeli przebiegów wybierz jeden z ostatnich przebiegów, w której element docelowy został **lokalnego** a skrypt miał nazwę **iris_sklearn.py**.  

    3. Zostanie otwarte okienko **Właściwości przebiegu**. W prawej górnej części okienka Zwróć uwagę, **dane wyjściowe** sekcji. d\. Aby pobrać plik z pakietu pickle, zaznacz pole wyboru obok pozycji **model.pkl** pliku, a następnie wybierz **Pobierz**. Zapisz plik w katalogu głównym folderu projektu. Plik jest potrzebny w kolejnych krokach.  

    ![Pobieranie pliku z pakietu pickle](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Pobierz oceniania skryptów i plików schematu

Aby wdrożyć usługę sieci web z plikiem modelu, oceniania skryptu jest wymagana. Opcjonalnie schemat dla danych wejściowych usługi internetowej jest wymagany. Skrypt oceniania ładuje plik **model.pkl** z bieżącego folderu i używa go w celu tworzenia nowych przewidywań.

1.  Otwórz aplikację Machine Learning Workbench. Następnie otwórz **myIris** projektu utworzonego w poprzedniej części tej serii samouczka.

2.  Po otwarciu projektu wybierz **pliki** przycisku (ikona folderu) w okienku po lewej stronie, aby otworzyć listę plików w folderze projektu.

3.  Wybierz plik **score_iris.py**. Zostanie otwarty skrypt w języku Python. Ten plik jest używany jako plik oceniania.

    ![Plik oceniania](media\azure-stack-solution-machine-learning\image56.png)

1.  Aby uzyskać plik schematu, uruchom skrypt. Na pasku poleceń wybierz środowisko **local** i skrypt **score_iris.py**, a następnie wybierz pozycję **Przebieg**.

    Ten skrypt umożliwia utworzenie pliku JSON w **dane wyjściowe** sekcji, który będzie przechwytywał wejściowy schemat danych wymagany przez model.

1.  Zwróć uwagę na okienko **Zadania** po prawej stronie okienka **Pulpit nawigacyjny projektu**. Poczekaj, aż zadanie score_iris.py** najnowsze ** na zielony wskaźnik **Ukończono** stanu. Następnie wybierz hiperlink **score_iris.py** dla najnowszego uruchomienia zadania, aby wyświetlić szczegóły dotyczące uruchomienia.

2.  W okienku **Właściwości przebiegu** w sekcji **Dane wyjściowe** wybierz nowo utworzony plik **service_schema.json**. Zaznacz pole wyboru obok nazwy pliku, a następnie wybierz polecenie **Pobierz**. Zapisz plik w folderze głównym projektu.

3.  Wróć do poprzedniej karty, a **score_iris.py** skryptu. Korzystając z funkcji zbierania danych, danych wejściowych i modelu za pomocą usługi sieci web mogą być przechwytywane. Poniższe kroki mają znaczenie w odniesieniu do zbierania danych.

4.  Przejrzyj kod u góry pliku, który importuje klasę **ModelDataCollector**. Zawiera funkcję zbierania danych modelu:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Przejrzyj poniższe wiersze kodu, w których funkcja **init()** tworzy wystąpienia klasy **ModelDataCollector**:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Przejrzyj następujące wiersze kodu, w których funkcja **run(input_df)** zbiera dane wejściowe i dane prognozowania:

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

Teraz Przygotuj środowisko do obsługi operacji modelu.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Krok 5: Wdrażanie i używanie usługi Azure Container Registry

Wdrażanie i używanie usługi Azure Container Registry.

Utwórz rejestr usługi Azure Container za pomocą polecenia **az acr create**. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Grupa zasobów jest taka sama.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Logowanie do rejestru Container Registry

Użyj polecenia **az acr login**, aby zalogować się do wystąpienia rejestru ACR. Podaj unikatową nazwę nadaną rejestrowi kontenerów podczas jego tworzenia.

    ```CLI
        az acr login --name <acrName>
    ```

Polecenie zwraca "Logowanie pomyślne komunikat po ukończeniu.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Przygotowywanie do obsługi operacji lokalnie na potrzeby opracowywania i testowania usługi

Użyj *trybu lokalnego* uruchamiania w kontenerach platformy Docker na komputerze lokalnym, a także do tworzenia i testowania wdrożenia.

Aparat platformy Docker musi być uruchomiony lokalnie, aby możliwe było wykonanie poniższych kroków w celu rozpoczęcia obsługi operacji modelu. Użyj `-h` flagi na końcu każdego polecenia w celu wyświetlenia odpowiedniego komunikatu pomocy.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Otwórz interfejs wiersza polecenia. W aplikacji Machine Learning Workbench w menu **Plik** wybierz polecenie **Otwórz wiersz polecenia**.

    Wiersz polecenia otworzy się w bieżącej lokalizacji folderu projektu **c:\\temp\\myIris**.

1.  Upewnij się, że dostawca zasobów platformy Azure **Microsoft.ContainerRegistry** jest zarejestrowany w ramach subskrypcji. Zarejestruj tego dostawcę zasobów przed utworzeniem środowisko w kroku 3. Sprawdź, czy został on już zarejestrowany przy użyciu następującego polecenia:

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Wyświetl te dane wyjściowe:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Jeśli **Microsoft.ContainerRegistry** jest niezarejestrowany Użyj następującego polecenia:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    Rejestracja może potrwać kilka minut. Sprawdzanie stanu rejestracji za pomocą poprzedniej **az provider list** polecenia lub następującego polecenia:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    W trzecim wierszu danych wyjściowych znajduje się następująca informacja: **„registrationState”: „Rejestrowanie”**. Poczekaj chwilę i powtarzaj **Pokaż** polecenia do momentu, gdy dane wyjściowe **"registrationState": "zarejestrowano.**

1.  Utwórz środowisko. Uruchom ten krok, raz dla każdego środowiska.

    Poniższe polecenie Instalatora wymaga prawa dostępu współautora do subskrypcji. Wymagany jest dostęp współautora do grupy zasobów, wdrażania. Określ nazwę grupy zasobów jako część polecenia Instalatora przy użyciu gflag.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Postępuj zgodnie z wyświetlanymi instrukcjami, aby aprowizować konto magazynu do przechowywania obrazów platformy Docker, rejestr Azure Container Registry, który wyświetla listę obrazów platformy Docker, oraz konto usługi Azure Application Insights, które zbiera dane telemetryczne. **Jeśli używasz cswitch, polecenie dodatkowo spowoduje utworzenie klastra usługi Container Service**.

    Nazwa klastra jest sposób identyfikowania środowiska. Lokalizacja powinna być taka sama jak lokalizacja konta zarządzania modelami, utworzone w witrynie Azure portal.

    Aby upewnić się, że środowisko zostało poprawie skonfigurowane, sprawdź stan za pomocą następującego polecenia:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Upewnij się, czy "Stan aprowizacji" ma wartość "Powodzenie", jak pokazano przed rozpoczęciem konfigurowania środowiska w kroku 5:

    ![Stan aprowizacji](media\azure-stack-solution-machine-learning\image57.png)

1.  Ustaw środowisko.

    Po zakończeniu konfiguracji użyj następującego polecenia, aby ustawić zmienne środowiskowe wymagane do obsługi operacji środowiska. Użyj nazwy środowiska użytych wcześniej w kroku 3. Użyj tej samej nazwy grupy zasobów, która została wyświetlona w danych wyjściowych w oknie wiersza polecenia po ukończeniu procesu instalacji.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Aby sprawdzić właściwa konfiguracja środowisko obsługi operacji dla lokalnego wdrożenia usługi internetowej, wprowadź następujące polecenie:

    ```CLI
    az ml env show
    ```

    Teraz Utwórz usługę internetową czasu rzeczywistego.

    > [!Note]  
    > Ponownie użyć konta zarządzania modelami i środowiska dla kolejnych wdrożeń usług internetowych. Nie ma potrzeby zostać utworzone dla poszczególnych usług sieci web. Z kontem lub środowiskiem można skojarzyć wiele usług internetowych.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Tworzenie usługi internetowej czasu rzeczywistego przy użyciu oddzielnych poleceń

Jako alternatywę dla **az ml service tworzenie w czasie rzeczywistym** przedstawionego wcześniej, te kroki można wykonać także oddzielnie polecenia.

Najpierw należy zarejestrować model. Następnie należy wygenerować manifest, utworzyć obraz platformy Docker i utworzyć usługę internetową. Takie szczegółowe podejście zapewnia większą elastyczność na każdym kroku. Ponadto jednostki generowane w poprzednich krokach mogą być ponownie używane.

1. Zarejestruj model, podając nazwę pliku z pakietu pickle.

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    To polecenie spowoduje wygenerowanie identyfikatora modelu.

2.  Utwórz manifest.

    Aby utworzyć manifest, użyj następującego polecenia i podaj wyjściowy identyfikator modelu z poprzedniego kroku: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    To polecenie spowoduje wygenerowanie identyfikatora manifestu.

3.  Utwórz obraz platformy Docker.

    Aby utworzyć obraz platformy Docker, użyj następującego polecenia i podaj wyjściowy Identyfikator manifestu z poprzedniego kroku. Zależności conda może być również za pośrednictwem `-c` przełącznika. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    To polecenie spowoduje wygenerowanie identyfikatora obrazu platformy Docker.

2.  Utwórz usługę.

    Aby utworzyć usługę, użyj następującego polecenia i podaj wyjściowy identyfikator obrazu z poprzedniego kroku: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    To polecenie spowoduje wygenerowanie identyfikatora usługi internetowej.
    
    Następnie uruchom usługę sieci web.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Krok 6: Wdrażanie klastra Kubernetes w usłudze Azure Stack

Wdrażanie klastra Kubernetes w usłudze Azure Stack.

Kubernetes może zostać zainstalowany za pomocą szablonów usługi Azure Resource Manager, generowane przez aparat usługi kontenerów Azure (ACS) w usłudze Azure Stack. [*Kubernetes* ](https://kubernetes.io/) to system typu open source do automatyzowania wdrażania, skalowania i zarządzania nimi aplikacji w kontenerach. A [ *kontenera* ](https://www.docker.com/what-container) jest zawarty w obrazie, podobne do maszyny Wirtualnej. W przeciwieństwie do maszyn wirtualnych obraz kontenera zawiera zasoby potrzebne do uruchomienia aplikacji, takich jak kod, środowisko uruchomieniowe do wykonania kodu, określonych bibliotek i ustawień.

Używaj programu Kubernetes, aby:

 -  Twórz wysoce skalowalne, można uaktualnić, aplikacje, które można wdrożyć w kilka sekund.

 -  Uproszczenie projektowania aplikacji i zwiększyć jej niezawodność przez różne aplikacje narzędzia Helm. [*Polecenie Helm* ](https://github.com/kubernetes/helm) to narzędzie open source pakietu, które pomaga zainstalować i zarządzanie cyklem życia aplikacji platformy Kubernetes.

 -  Łatwe monitorowanie i diagnozowanie kondycji aplikacji w skali i uaktualniania funkcji.

> [!Note]  
> Instalacja klastra potrwa około godzinę plan odpowiednio.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Wymagania wstępne dotyczące wdrażania klastra Kubernetes

Aby rozpocząć pracę, upewnij się, uprawnienia i gotowości usługi Azure Stack:

1.  Sprawdzić, czy **Tworzenie klastra Kubernetes (wersja zapoznawcza)** element jest dostępny w witrynie Azure Marketplace stosu. Jeśli brakuje ten element roboczy z operatorem usługi Azure Stack można dodać tego elementu do środowiska Azure Stack.

2.  Sprawdź możliwość tworzenia aplikacji w dzierżawie usługi Azure Active Directory (Azure AD). Uprawnienia są wymagane do wdrożenia rozwiązania Kubernetes.

    Aby uzyskać instrukcje dotyczące sprawdzania występowania uprawnień, zobacz [ *uprawnień Sprawdź usługi Azure Active Directory*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Generowanie publicznego i prywatnego pary kluczy SSH do logowania do maszyny Wirtualnej systemu Linux w usłudze Azure Stack. Klucz publiczny jest potrzebny podczas tworzenia klastra. Aby uzyskać instrukcje zobacz [wygenerować klucz uwierzytelniania dla protokołu SSH](#generate-an-authenticatio-key-for-ssh).

4.  Sprawdź, czy subskrypcja w portalu dzierżawcy usługi Azure Stack jest prawidłowa i ma wystarczającej liczby publiczny adres IP adresów dostępnych do dodania nowych aplikacji.

    Nie można wdrożyć klaster do usługi Azure Stack **administratora** subskrypcji. Użyj **użytkownika** subskrypcji.

###  <a name="generate-an-authentication-key-for-ssh"></a>Generowanie klucza uwierzytelniania w przypadku protokołu SSH

Z podsystemu Windows dla systemu Linux sesji Użyj następujących poleceń, aby wygenerować klucz uwierzytelniania: 

1. Wpisz:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Wybierz `id_rsa` po wyświetleniu monitu. 
3. Utwórz hasło po wyświetleniu monitu. Koniecznie Zapisz hasło do późniejszego użycia. 
    Dane wyjściowe będą wyglądać podobnie jak na poniższej ilustracji: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image58.png)

4. Po wygenerowaniu klucza, Wklej informacje o kluczu, używając następujących poleceń: 
    ```Bash
    cat id_rsa.pub
    ```
    Dane wyjściowe będą wyglądać podobnie jak na poniższej ilustracji: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Skopiuj wygenerowany klucz do protokołu SSH publiczne pole klucza.

### <a name="create-a-service-principal-in-azure-ad"></a>Tworzenie jednostki usługi w usłudze Azure AD

1.  Zaloguj się do globalnej [ *witryny Azure portal*](http://www.poartal.azure.com/).

2.  Zaloguj się przy użyciu dzierżawy usługi Azure AD skojarzonego z wystąpieniem usługi Azure Stack.

3.  Utwórz aplikację usługi Azure AD.

    1. Wybierz **usługi Azure Active Directory** > **+ rejestracje aplikacji**> **rejestrowanie nowej aplikacji**.
    2. Wprowadź **nazwa** aplikacji. 
    3. Wybierz **aplikacji sieci Web / interfejs API**. 
    4. Wprowadź `http://localhost` dla **adres URL logowania**. 
    5. Wybierz pozycję **Utwórz**.

1.  Zanotuj wartość **Identyfikatora aplikacji**. Ten identyfikator jest wymagana podczas tworzenia klastra i jest przywoływany jako **identyfikator klienta jednostki usługi**.

2.  Wybierz **ustawienia** > **klucze**.

    1. Wprowadź **opis**. 
    2. Wybierz **nigdy nie wygasa** dla **Expires**. 
    3. Wybierz pozycję **Zapisz**. Upewnij się, należy pamiętać, ciąg klucza. Ciąg klucza jest wymagana podczas tworzenia klastra i jest przywoływany jako **klucz tajny klienta jednostki usługi**.

### <a name="give-the-service-principal-access"></a>Nadaj dostęp do nazwy głównej usługi

Zapewniają jednostce usługi dostępu do subskrypcji, dzięki czemu mogą one stanowić zasobów.

1.  Zaloguj się do [portalu administracyjnego](https://adminportal.local.azurestack.external/).

2.  Wybierz **więcej usług** > ** subskrypcji użytkownika ** > **+ Dodaj**.

3.  Wybierz subskrypcję, utworzony.

4.  Wybierz **kontrola dostępu (IAM)** > Wybierz **+ Dodaj**.

5.  Wybierz **właściciela** roli.

6.  Wybierz nazwę aplikacji utworzone przez usługę podmiotu zabezpieczeń. Wpisz nazwę w polu wyszukiwania, jeśli to konieczne.

7.  Wybierz pozycję **Zapisz**.

8.  Otwórz [portalu Azure Stack](https://portal.local.azurestack.external).

9.  Wybierz **+ nowe** > **obliczenia** > **klastra Kubernetes**. Wybierz pozycję **Utwórz**.

    ![Wdrażanie szablonu rozwiązań](media\azure-stack-solution-machine-learning\image59.png)

10\. Wybierz **podstawy** w tworzenie klastra Kubernetes.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Wprowadź **nazwa użytkownika administratora maszyny Wirtualnej systemu Linux**. Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.

12. Wprowadź **klucz publiczny SSH** używane na potrzeby autoryzacji do wszystkich utworzonych jako część klastra Kubernetes i Menedżer DVM maszyn z systemem Linux.

13. Wprowadź **prefiks DNS profilu wzorzec** jest unikatowa w regionie. Musi to być region unikatowej nazwy, takie `ask8s-12345`. Spróbuj wybrać ją nazwę takie same jak grupy zasobów jako najlepsze rozwiązanie.

    > [!Note]  
    > Dla każdego klastra użyj prefiks DNS nowych i unikatowych głównym profilu.

14. Wprowadź **liczba profil puli agentów**. Liczba zawiera liczbę agentów w klastrze. Może być z zakresu od 1 do 4.

15. Wprowadź **ClientId nazwy głównej usługi** jest on używany przez dostawcę chmury Kubernetes Azure.

16. Wprowadź **klucz tajny klienta jednostki usługi** tworzone podczas tworzenia aplikacji nazwy głównej usługi.

17. Wprowadź **wersja dostawcy chmury Kubernetes Azure**. To jest wersja dostawcy usługi Kubernetes Azure. Usługa Azure Stack zwalnia niestandardowej kompilacji rozwiązania Kubernetes dla każdej wersji usługi Azure Stack.

18. Wybierz **subskrypcji** identyfikatora.

19. Wprowadź nazwę nowej grupy zasobów lub wybierz istniejącą grupę zasobów. Nazwa zasobu musi być alfanumeryczne i małe litery.

20. Wybierz **lokalizacji** grupy zasobów. Jest to region, w wybranym dla instalacji usługi Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Określanie ustawień usługi Azure Stack

1.  Wybierz **ustawienia sygnatury usługi Azure Stack**.

    ![Wdrażanie szablonu rozwiązań](media\azure-stack-solution-machine-learning\image61.png)

2.  Wprowadź **dzierżawy punkt końcowy usługi Azure Resource Manager**. To jest punkt końcowy usługi Azure Resource Manager, aby Połącz, aby utworzyć grupę zasobów dla klastra Kubernetes. Punkt końcowy z operatora infrastruktury Azure Stack jest wymagany przez system zintegrowany. Dla usługi Azure Stack Development Kit (ASDK), użyj `https://management.local.azurestack.external`.

3.  Wprowadź **identyfikator dzierżawy** dla dzierżawy. Zobacz [ *uzyskanie Identyfikatora dzierżawy* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) można znaleźć wartość Identyfikatora dzierżawy.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Instalowanie narzędzia kubectl w środowisku programu Windows PowerShell

W środowisku WSL uruchom następujące polecenia, aby zainstalować narzędzie kubectl w środowisku WSL.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Instalowanie narzędzia kubectl w podsystemie Windows w środowisku systemu Linux

W środowisku WSL, uruchom następujące polecenia, aby zainstalować narzędzie kubectl w środowisku WSL.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Konfigurowanie narzędzia kubectl

Aby narzędzie kubectl w celu znalezienia i uzyskiwania dostępu do klastra Kubernetes*pliku plik kubeconfig* jest wymagana. Jest tworzony automatycznie podczas tworzenia klastra przy użyciu rozwiązania kubernetes up.sh lub wdrażania Minikube klastra. Zobacz [ *wprowadzenie przewodniki* ](https://kubernetes.io/docs/setup/) więcej informacji na temat tworzenia klastrów. Aby uzyskać dostęp do klastra utworzone przez innego użytkownika, zobacz [ *dokumentu udostępnianie klastrowi dostęp*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Domyślnie narzędzie kubectl w konfiguracji znajduje się w **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Sprawdź konfigurację narzędzia kubectl

Sprawdź, czy ten kubectl jest poprawnie skonfigurowany przez pobranie stanu klastra:

```Bash  
kubectl cluster-info
```

Narzędzie kubectl jest poprawnie skonfigurowany dostęp do klastra, gdy zostanie wyświetlony adres url odpowiedzi.

narzędzia kubectl nie jest poprawnie skonfigurowana lub nie można połączyć się z klastrem Kubernetes, jeśli zostanie wyświetlony następujący komunikat:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Na przykład, uruchamiając na laptopie lokalnym klastrem Kubernetes, narzędzia może być wymagana (minikube lub podobny) aby ponownie uruchomić polecenia podanych powyżej.

Jeśli narzędzie kubectl w klastrze info zwraca odpowiedź adres url, ale klaster nadal nie jest dostępny, sprawdź odpowiedniej konfiguracji za pomocą:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Włączanie automatycznego uzupełniania powłoki

narzędzia kubectl obejmuje obsługę automatycznego uzupełniania, co shell umożliwia szybkie i łatwe.

Sam skrypt zakończenia jest generowany przez narzędzie kubectl i jest dostępny z profilu.

### <a name="connect-to-the-kubernetes-cluster"></a>Nawiąż połączenie z klastrem Kubernetes

Aby połączyć się z klastrem, czyli klienta wiersza polecenia usługi Kubernetes (**kubectl**) jest wymagana. Instrukcje dotyczące nawiązywania połączenia z klastra i zarządzania nim znajdują się w [dokumentacja usług Azure Container Service.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Dowolny klient SSH może służyć do łączenia z klastrem Kubernetes. Podsystem Windows dla systemu Linux (WSL) jest zalecane. Maszyny łączenia się z klastrem Kubernetes będą znajdować się w grupie zasobów tworzona dla klastra i rozpoczyna się od prefiksu vmd edge-ml — stos.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Po nawiązaniu połączenia z maszyną Kubernetes, uruchom następujące maszyny, aby pobrać plik konfiguracji Kubernetes.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

Dane wyjściowe będą wygląda następująco:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Skopiuj te informacje o ścieżce plików, a następnie uruchom następujące polecenie, wklejając w ścieżce pliku plik kubeconfig skopiowane z powyższych:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Dane wyjściowe będą dużych bloków tekstu, który jest nieprzetworzonej zawartości JSON. Skopiuj ten tekst wyjściowy, a następnie wklej ten kod do pliku programu Visual Studio, zapisywanie jako plik w formacie JSON. Skutkuje to plik kubeconfig.json przechowywane lokalnie. (Zapisz, abywolumin/mnt/c/users/ <current user> /dokumentów lub katalogu rozwiązania Kubernetes jako kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Konfigurowanie klastra usługi Kubernetes

Po uzyskaniu lokalny plik JSON w nowej sesji WSL, użyj następujących poleceń, aby skonfigurować klaster.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Ustawienia konfiguracji Kubernetes zostaną określone (zobacz dane wyjściowe poniżej).

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image62.png)

Uruchom usługę lokalnego serwera proxy:

```Bash  
kubectl proxy
```

Przejdź do klastra kubernetes interfejsu użytkownika, pod następującym adresem: `https://localhost:8001`.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image63.png)

Masz teraz miejsce do wdrażania kontenerów i kontener, który znajduje się w chmurze, które widać w środowisku lokalnym.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image64.png)

Dostosowywanie **iris_deployment.yaml** pliku (znajdujący się w /*użytkowników/mnt/c/<current user>/dokumentów lub bieżącym katalogu rozwiązania Kubernetes*) tak **webservicename** i kontenery  **Obraz** i **nazwa** dopasowania wdrożenie, za pomocą dowolnego edytora kodu w wybranym.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image65.png)

Ustaw port kontenera **5001.**

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image66.png)

A następnie utwórz **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Utwórz klucz tajny w klastrze, który zawiera token autoryzacji

Klaster usługi Kubernetes używa klucza tajnego **rejestru platformy docker** typu do uwierzytelniania za pomocą rejestru kontenerów, aby ściągnąć obrazu prywatnego.

Utwórz ten wpis tajny nadawania mu nazwy **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Znajdź:

- **<-registry serwera >** jest usługa Azure Container Registry **Login Server**.
- **< Twój name >** jest usługa Azure Container Registry **Username**.
- **< your pword — >** jest usługa Azure Container Registry **hasło**. Upewnij się, że hasło w cudzysłowie.
- **< Twój adres e-mail >** jest użytkownik, który ma dostęp do odczytu i zapisu, do kontenera.
- Te informacje można znaleźć na **kontenera platformy Azure** **rejestru** w obszarze **klucze dostępu**.
- Poświadczenia platformy docker są teraz ustawione w klastrze, nazywane wpisu tajnego **azuremlcr**.

Zapisz **iris_deployment.yaml** pliku (znajdujący się w /*użytkowników/c/mnt/<current user>/dokumentów lub bieżącym katalogu rozwiązania Kubernetes*).

### <a name="create-the-kubernetes-container"></a>Utwórz kontener Kubernetes

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Sprawdź stan wdrożenia:

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

Wdrożenie może potrwać pewien czas.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Konfigurowanie programu Visual Studio Team Services, aby wdrożyć automatycznie

#### <a name="create-a-team-project"></a>Utwórz projekt zespołowy

1.  Upewnij się, [członkostwa grupy administratorów kolekcji projektów.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Do utworzenia zespołu projektowego **tworzyć nowe projekty** uprawnień musi być równa **Zezwalaj**.

2.  Na stronie projektów wybierz **nowy projekt**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image69.png)

1.  Nadaj projektowi nazwę **HybridMLIris**.

2.  Wybierz jego typ kontroli źródła początkowego jako **Git**

3.  Wybierz proces i wybierz **Utwórz**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importowanie kodu tworzenia repozytorium

Potrzebny jest repozytorium Git na potrzeby kodu YAML.

#### <a name="add-user-to-the-git-repo"></a>Dodawanie użytkownika do repozytorium GIT

1.  Z domyślnego pulpitu nawigacyjnego Projekt wybierz Git Generowanie poświadczenia.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image71.png)

1.  Wprowadź hasło, gdzie wymagane, a następnie Zapisz poświadczenia narzędzia Git.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image72.png)

1.  Inicjowanie repozytorium, wybierając **zainicjować** przycisk i tworzenie **README** pliku.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Klonowanie repozytorium Git lokalnie i przekazać kod. 

1.  Utwórz katalog na maszynie w `c:\\users\\<User>\\source\\repos\\hybridMLIris`i sklonuj repozytorium

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image74.png)

1.  Przejdź do nowo sklonowanego repozytorium:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image75.png)

1.  Kopiuj **iris_deployment.yaml** pliku do repozytorium.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Zatwierdź zmiany w usłudze GIT

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Przygotowanie prywatnej kompilacji i wersji agenta do integracji usługi VSTS

#### <a name="prerequisites"></a>Wymagania wstępne

Usługa VSTS jest uwierzytelniany względem usługi Azure Resource Manager przy użyciu jednostki usługi. Usługi VSTS można było wykonać aprowizację zasobów w subskrypcji usługi Azure Stack wymaga stanu współautora. \ **poniżej przedstawiono ogólne kroki, które muszą być skonfigurowane do włączenia uwierzytelniania takie:**

1.  Powinna zostać utworzona nazwa główna usługi lub istniejącą mogą być używane.

2.  Klucze uwierzytelniania muszą być utworzone dla nazwy głównej usługi.

3.  Subskrypcji platformy Azure Stack musi zostać zweryfikowany za pomocą opartej na rolach kontroli dostępu, aby zezwolić na główną nazwę usługi, być częścią roli współautorzy.

4.  Nowa definicja usługi w usłudze VSTS musi zostać utworzona za pomocą punktów końcowych usługi Azure Stack, a także informacje nazwę SPN.

#### <a name="service-principal-creation"></a>Tworzenie nazwy głównej usługi

Zapoznaj się [instrukcje dotyczące tworzenia jednostki usługi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) do utworzenia jednostki usługi, a następnie wybierz aplikację sieci Web/interfejsu API dla typu aplikacji.

**Tworzenie klucza dostępu**

Nazwy głównej usługi wymaga klucza uwierzytelniania, należy wykonać czynności opisane w tej sekcji, aby wygenerować klucz.

1.  Z **rejestracje aplikacji** w usłudze Azure Active Directory wybierz aplikację.

    ![Wybieranie aplikacji](media\azure-stack-solution-machine-learning\image77.png)

1.  Zanotuj wartość **identyfikator aplikacji. Wartość jest używana podczas konfigurowania punktu końcowego usługi w usłudze VSTS.**

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image78.png)

1.  Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Ustawienia**.

    ![Wybieranie pozycji Ustawienia](media\azure-stack-solution-machine-learning\image79.png)

1.  Wybierz pozycję **Klucze**.

    ![Wybieranie pozycji Klucze](media\azure-stack-solution-machine-learning\image80.png)

1.  Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

    ![Zapisywanie klucza](media\azure-stack-solution-machine-learning\image81.png)

Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość, ponieważ jest ona potrzebna później. **Wartość klucza** z aplikacją, aby zalogować się jako aplikację, wymagany jest identyfikator. Store wartości klucza, gdzie aplikacja będzie mogła ją pobrać.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Pobieranie Identyfikatora dzierżawy

W ramach konfiguracji punktu końcowego usługi, wymaga usługi VSTS **identyfikator dzierżawy** odnosi się do katalogu usługi AAD została wdrożona sygnatury usługi Azure Stack. Postępuj zgodnie z instrukcjami w tej sekcji, aby zebrać identyfikatora dzierżawy.

1.  Wybierz pozycję **Azure Active Directory**.

    ![Wybieranie pozycji Azure Active Directory](media\azure-stack-solution-machine-learning\image83.png)

1.  Aby uzyskać identyfikator dzierżawy, wybierz **właściwości** dla dzierżawy usługi Azure AD.

    ![Wybieranie pozycji Właściwości usługi Azure AD](media\azure-stack-solution-machine-learning\image84.png)

1.  Skopiuj **identyfikator katalogu**. Ta wartość jest identyfikator dzierżawy.

    ![Identyfikator dzierżawy](media\azure-stack-solution-machine-learning\image85.png)

Przyznanie praw jednostki usługi do wdrażania zasobów w subskrypcji platformy Azure Stack

Dostęp do zasobów w ramach subskrypcji, przypisywanie aplikacji do roli. Zdecyduj, rolę, która reprezentuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: Built in Roles](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Ustaw zakres na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów umożliwia mu do odczytu grupę zasobów i wszystkie zasoby, które zawiera.

1.  Przejdź do żądanego poziomu zakresu, aby przypisać tę aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Wybierz **subskrypcji** (grupy zasobów lub zasobu), aby przypisać tę aplikację.

    ![Wybierz subskrypcję do przypisania](media\azure-stack-solution-machine-learning\image87.png)

1.  Wybierz **kontrola dostępu (IAM)**.

    ![Wybierz dostępu](media\azure-stack-solution-machine-learning\image88.png)

1.  Wybierz pozycję **Dodaj**.

    ![Wybieranie opcji dodawania](media\azure-stack-solution-machine-learning\image89.png)

1.  Wybierz rolę, aby przypisać tę aplikację. Na poniższej ilustracji przedstawiono **właściciela** roli.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image90.png)

1.  Domyślnie aplikacje usługi Azure Active Directory nie są wyświetlane w dostępnych opcjach. Aby znaleźć tę aplikację **Podaj nazwę** w wyszukiwaniu pola, a następnie wybierz ją.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image91.png)

1.  Wybierz **Zapisz** zakończenie przypisanie roli. Aplikacja jest wyświetlana na liście Użytkownicy przypisani do roli dla tego zakresu.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Usługa Azure opartej na rolach kontrola dostępu (RBAC) umożliwia precyzyjne zarządzanie dostępem dla platformy Azure i usługi Azure Stack. Korzystając z modelu RBAC, można udzielić tylko takiego dostępu, których użytkownicy potrzebują do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat kontroli dostępu opartej na rolach, zobacz [zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**Pule agentów usługi VSTS**

Zamiast na zarządzaniu indywidualnie każdego agenta, agenci są zorganizowane w **pul agentów**. Pula agentów definiuje granicy udostępniania dla wszystkich agentów w puli. W usłudze VSTS pule agentów są ograniczone do konta usługi VSTS, dzięki czemu mogą być współużytkowane przez projektów zespołowych. Więcej informacji i zapoznać się z samouczkiem na temat tworzenia usługi VSTS pul agentów, zobacz [Tworzenie pul agentów i kolejki.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Dodaj aPersonal tokenu dostępu (PAT) dla usługi Azure Stack**

 -  Zaloguj się na konto usługi VSTS i wyboru na **profilu konta** nazwy.

 -  Wybierz **zarządzania zabezpieczeniami** do strony tworzenia tokenu dostępu.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image92.png)

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image93.jpeg)

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Uzyskiwanie informacji o tokenie. Go nie pojawią się ponownie, po opuszczeniu ten ekran.

1.  Kopiuj **tokenu**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Zainstaluj agenta kompilacji usługi VSTS w usłudze Azure Stack hostowany serwer kompilacji

1.  Połączyć się z **Build Server** wdrożone na hoście usługi Azure Stack.

    > [!Note]  
    > Upewnij się, że usługi Azure Stack hostowany serwer kompilacji jest dostępny w publicznym Internecie. W przeciwnym razie pracy za pomocą operatora usługi Azure Stack przy uzyskiwaniu dostępu do.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Uaktualnienie do najnowszej wersji (18.04) przez serwer kompilacji Ubuntu:

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Ta operacja zajmie trochę czasu.

2.  Instalowanie aplikacji wstępnych na serwerze kompilacji. Z powłoki bash powłoki systemu Ubuntu na podstawie serwer kompilacji, uruchom następujące polecenia:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Pobieranie i wdrażanie agenta kompilacji, jako usługi przy użyciu **osobisty token dostępu (PAT)** i uruchamianie jako konto administratora maszyny Wirtualnej.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Przejdź do pozycji wyodrębniony folder agenta kompilacji. Uruchom poniższy kod.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image97.png)

2.  Po **./config.sh**zakończone, uruchom następujący kod, aby włączyć usługę podczas rozruchu serwera i uruchomienia usługi:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

Agent jest teraz widoczne w folderze usługi VSTS.

#### <a name="endpoint-creation-permissions"></a>Uprawnienia do tworzenia punktu końcowego

Użytkownicy mogą tworzyć punkty końcowe, więc VSTO kompilacji można wdrożyć usługi Azure App Service na stos. Usługa VSTS umożliwia nawiązywanie agent kompilacji, który następnie łączy się z usługą Azure Stack.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image98.png)

1.  Na **ustawienia** menu, wybierz opcję **zabezpieczeń**.

2.  W **grupy usługi VSTS** liście po lewej stronie, wybierz opcję **punktu końcowego dla twórców**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image99.png)

3.  Na **karta Członkowie** wybierz **+ Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image100.png)

1.  Typ **username** i wybierz nazwę użytkownika z listy.

2.  Wybierz **Zapisz zmiany**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image101.png)

3.  W **grupy usługi VSTS** liście po lewej stronie, wybierz opcję **Administratorzy punktu końcowego**.

4.  Na **karta Członkowie** wybierz **+ Dodaj**.

5.  Wpisz **nazwa_użytkownika** i wybierz użytkownika z listy.

6.  Wybierz **zapisać zmiany.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    Agent kompilacji w usłudze Azure Stack uzyskuje instrukcje z usługi VSTS, która następnie umożliwia przekazywanie informacji o punkcie końcowym komunikacji z usługą Azure Stack.

    Połączenie usługi Azure Stack przez usługę VSTS jest teraz gotowy.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>Konfigurowanie definicji kompilowania i wydawania

Teraz, gdy nawiązywane są połączenia, zostaną ręcznie mapy utworzony punkt końcowy platformy Azure, usługi AKS i Azure Container Registry do kompilacji i definicji wydania.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Utwórz definicję kompilacji kodu YAML

1.  Wybierz sekcję kompilacji w ramach Centrum kompilacji i wydania i Utwórz nową definicję.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image104.png)

1.  Wybieranie pozycji VSTS Git, a następnie wybierz repozytorium utworzone wcześniej.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image105.png)

1.  Wybierz pustym potoku jako szablon

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image106.png)

1.  Nazwa kompilacji **artefaktu kopiowania** i wybierz pozycję Azure Stack Build Server dla kolejki agenta.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image107.png)

1.  Wybierz fazy 1 procesy, a następnie zmień jej nazwę na **artefaktu kopiowania**, następnie **Dodaj zadanie** do fazy:

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image108.png)

1.  Wybierz **publikowanie artefaktów kompilacji** z **narzędzie** listy i wybierz **Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image109.png)

1.  Wybierz **ścieżka do publikowania** i wybierz **iris_deployment.yaml** pliku.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image110.png)

1.  Nazwa artefaktu **iris_deployment** i wybrać lokalizację publikacji na **Visual Studio Team Services/TFS**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image111.png)

1.  Wybierz **Zapisz k & Olejką**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image112.png)

1.  Sprawdź stan kompilacji, wybierając identyfikatora kompilacji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image113.png)

Powodzenie będzie wyglądać podobnie do poniższego:

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Utwórz definicję wydania dla kodu YAML

1.  Wybierz sekcję wydań w ramach Centrum kompilacji i wydania, Nowa definicja

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image115.png)

1.  Wybierz pustym potoku jako szablon.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image106.png)

1.  Nazwa środowiska Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image116.png)

1.  Dodaj nowe artefaktu, wybierając **artefaktów** i **+ Dodaj**

2.  Wybieranie kompilacji jako typ źródłowy i **HybridMLIris** jako projekt.

3.  Następnie wybierz definicję kompilacji, utworzony wcześniej dla źródła.

4.  Następnie wybierz pozycję **Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image117.png)

1.  Wybierz usługę Azure Stack ze środowisk, a następnie dodaj nowe zadanie do usługi Azure Stack

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image118.png)


1.  Na faza agenta należy ustawić kolejki agenta na serwerze usługi Azure Stack hostowanych kompilacji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image119.png)

1.  Dodaj nowe zadanie na tym etapie, wybierz Wdróż na zadanie usługi Kubernetes, w ramach wdrażania, a następnie wybierz Dodaj.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image120.png)


1.  Nadaj mu nazwę **zastosować Kubectl** (domyślna nazwa) i wybierz polecenie Zastosuj.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image121.png)

    Teraz należy utworzyć nowe połączenie usługi Kubernetes.

#### <a name="create-kubernetes-service-endpoint"></a>Tworzenie punktu końcowego usługi Kubernetes

1.  W obszarze połączenia z usługą Kubernetes, wybierz **+ nowy** przycisk, a następnie wybierz pozycję**Kubernetes**z listy. Ten punkt końcowy umożliwia łączenie**VSTS**i**Azure Container Service (AKS)**.

2.  **Nazwa połączenia**: Podaj nazwę połączenia.

3.  **Adres URL serwera**: Podaj adres usługi kontenera, w formathttp: / / {adres serwera interfejsu API}

4.  **Plik Kubeconfig**: Aby uzyskać wartość plik Kubeconfig, uruchom następujące polecenia platformy Azure, w wierszu polecenia uruchamiane z uprawnieniami administratora.

    > [!Important]  
    > To okno interfejsu wiersza polecenia do wykonania kolejnych kroków.

6.  W oknie interfejsu wiersza polecenia zaloguj się do platformy Azure. [Dowiedz się więcej o poleceniu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  W wierszu polecenia wprowadź:

    ```CLI
        az login
    ```

10. To polecenie zwraca kod do użycia w przeglądarce pod adresem <https://aka.ms/devicelogin>.

11. Przejdź do <https://aka.ms/devicelogin> w przeglądarce. Po wyświetleniu monitu wprowadź kod odebrany w interfejsie wiersza polecenia, w przeglądarce.

    ![Punkt końcowy usługi Kubernetes](media\azure-stack-solution-machine-learning\image122.png)

1.  Wpisz następujące polecenie w wierszu polecenia, aby uzyskać poświadczenia dostępu do klastra Kubernetes.

### <a name="azure-ml-workbench-cli"></a>Interfejs wiersza polecenia platformy Azure ML Workbench

AZ aks get-credentials grupa zasobów <yourResourceGroup> nazwy <yourazurecontainerservice>

![Punkt końcowy usługi Kubernetes](media\azure-stack-solution-machine-learning\image123.png)

1.  Przejdź do **.kube**folder w katalogu głównym (na przykład: C:\\użytkowników\\<user>\\dokumenty\\klastra kubernetes w usłudze)

2.  Skopiuj zawartość**config**plików i wklej go w oknie połączenia Kubernetes. Wybierz**OK**przycisku.

    ![Punkt końcowy usługi Kubernetes](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Po utworzeniu punktu końcowego usługi Kubernetes i wybrana, wybierz pliki konfiguracji użycia, zaznacz pole wyboru, aby dodać plik konfiguracji. Następnie przejdź do pliku iris_deployment.yaml w połączonych artefaktów.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image125.png)

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image126.png)

4.  Zapisz definicję wydania.

#### <a name="check-the-status-of-the-release-definition"></a>Sprawdź stan definicji wydania. 

Po zapisaniu definicji wydania usługi VSTS powinny automatycznie uruchamiać wydania.

Sprawdź stan wdrożenia, szybkie polecenia w wierszu polecenia WSL, a następnie sprawdzając interfejsu użytkownika rozwiązania Kubernetes.

```Bash  
kubectl get deployments
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, znajduje się w procesie wdrażania.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Gdy rozwiązania kubernetes jest uruchomiony interfejs użytkownika, przejść do wdrożenia w [ **https://localhost:8001/** ](https://localhost:8001/) a następnie przejdź do **obciążeń -> zestawów replik**.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>Wdrażanie usługi YAML

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Przekaż iris_service.yaml do repozytorium i synchronizowanie zmian

1.  Przejdź do nowo sklonowanego repozytorium:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image75.png)

1.  Kopiuj **iris_service.yaml** pliku do repozytorium.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Zatwierdź zmiany w usłudze GIT

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Zaktualizuj definicję kompilacji kodu YAML

1.  Wybierz sekcję kompilacji w ramach Centrum kompilacji i wydania i wybierz definicję utworzone wcześniej.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image130.png)

2.  Wybierz przycisk Edytuj, aby edytować definicję.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image131.png)

3.  **Dodaj zadanie** do fazy. Wybierz **publikowanie artefaktów kompilacji** z **narzędzie** listy i wybierz **Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image108.png)

4.  Nadaj mu nazwę **zastosować Kubectl** (domyślna nazwa) i wybierz polecenie Zastosuj.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Zaktualizuj definicję wydania dla kodu YAML

1.  Wybierz sekcję theReleases w Centrum kompilacji i wydania i wybierz definicję wydania utworzonego wcześniej. Następnie wybierz Link edycji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image132.png)

1.  Wybierz środowisko **usługi Azure Stack** następnie dodać nowe zadanie do usługi Azure Stack.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image133.png)

1.  Dodaj **nowe zadanie** na tym etapie należy wybrać **wdrażanie w usłudze Kubernetes** zadań w ramach **Wdróż** i wybierz **Dodaj**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image134.png)

1.  Nadaj mu nazwę **zastosować Kubectl** (domyślna nazwa) i wybierz polecenie Zastosuj.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image109.png)

1.  Ustawienie połączenia z usługą Kubernates połączenie stosu platformy Azure, które są utworzone wcześniej, a następnie wybierz **używać plików konfiguracji** pole wyboru, aby dodać plik konfiguracji. Przejdź do pliku iris_service.yaml w połączonych artefaktów.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image135.png)


    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image136.png)

1.  Zapisz definicję wydania.

#### <a name="check-the-status-of-the-release-definition"></a>Sprawdź stan definicji wydania

Po zapisaniu definicji wydania usługi VSTS powinny automatycznie uruchamiać wydania.

Sprawdź stan wdrożenia, szybkie polecenia w wierszu polecenia WSL, a następnie sprawdzając interfejsu użytkownika rozwiązania Kubernetes.

```Bash  
kubectl get deployments
```

Dane wyjściowe powinny wyglądać podobnie do poniższego, znajduje się w procesie wdrażania.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Gdy rozwiązania kubernetes jest uruchomiony interfejs użytkownika, przejść do wdrożenia w [ **https://localhost:8001/** ](https://localhost:8001/) a następnie przejdź do **obciążeń -> zestawów replik**.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes, oceniania i sprawdzania poprawności

Uruchom interfejs użytkownika rozwiązania Kubernetes

```Bash  
kubectl proxy
```

Przejdź do interfejsu użytkownika rozwiązania Kubernetes, następnie przejdź do **wdrożeń** -> **wdrożenia Iris** -> **nowego zestawu replik**  ->  **Iris-wdrożenie xxxxxxxxx** (xs, których identyfikator wdrożenia).

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image138.png)

Następnie przejdź do **usług** i wybierz **zewnętrzny punkt końcowy** usługi do sprawdzania poprawności będzie działać.

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image139.png)

Powinien zostać wyświetlony komunikat sprawdzania poprawności, podobny do przedstawionego poniżej:

![Tekst alternatywny](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Tworzenie usługi Azure Stack oceniania aplikacji funkcji w portalu usługi Azure Stack

Aplikacja funkcji jest wymagany do obsługi wykonywania poszczególnych funkcji. Aplikacja funkcji umożliwia korzystanie z funkcji grupowania jako jednostki logicznej, ułatwić zarządzanie, wdrażanie i udostępnianie zasobów.

1.  Za pomocą portalu użytkownika usługi Azure Stack, wybierz **+ nowy** znajdujący się w lewym górnym rogu, następnie wybierz pozycję**sieci Web i mobilność** >**aplikacji funkcji**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image141.png)

1.  Nadaj funkcji nazwę **funkcji danych** i umieść go w tej samej grupie zasobów przy użyciu uczenia maszynowego w pozostałej zawartości. Pozwól, narzędzie automatycznie Utwórz nowy plan usługi app service do użycia i użyć konta magazynu utworzonego wcześniej, do przechowywania aplikacji.

    ![Definiowanie nowych ustawień aplikacji funkcji](media\azure-stack-solution-machine-learning\image142.png)

1.  Wybierz**Utwórz**aprowizacji i wdrażania aplikacji funkcji.

2.  Wybierz ikonę powiadomienia w prawym górnym rogu portalu i poszukaj**wdrażanie zakończyło się pomyślnie** wiadomości.

    ![Definiowanie nowych ustawień aplikacji funkcji](media\azure-stack-solution-machine-learning\image143.png)

1.  Wybierz**przejdź do zasobu** Aby wyświetlić nową aplikację funkcji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image144.png)

1.  Utwórz nową funkcję, wybierając **funkcje**, a następnie **+ nowa funkcja** przycisku.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image145.png)

1.  Wybierz wyzwalacz HTTP

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image146.png)

1.  Wybierz **C\#**  jako język i nazwa funkcji: **clean-score-data**, a także Ustaw poziom autoryzacji **anonimowe**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image147.png)

1.  Kopiowanie i wklejanie zawartości przykładu kodu dla clean-score-data do funkcji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Sprawdź poprawność funkcji przy użyciu narzędzia Postman

Aby upewnić się, że po skonfigurowaniu funkcji i Kbernetes poprawnie służy bezpłatną aplikację Postman testować i weryfikować schematy i funkcje. Aby rozpocząć ten proces, najpierw musisz zebrać pewne informacje z wystąpieniem usługi Kubernetes.

1.  Przejdź do interfejsu użytkownika rozwiązania Kubernetes, następnie przejdź do **wdrożeń** -> **wdrożenia Iris** -> **nowego zestawu replik**  ->  **Iris-wdrożenie xxxxxxxxx** (xs, których identyfikator wdrożenia)

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image138.png)

1.  Następnie przejdź do **usług** i skopiuj **zewnętrzny punkt końcowy**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image149.png)

1.  Pobierz i zainstaluj aplikację Postman [tutaj](https://www.getpostman.com/apps) w razie potrzeby.

2.  Zaloguj się w aplikacji Postman, a następnie zamknij okno dialogowe Nowy plik.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image150.png)

1.  Z poziomu aplikacji postman, wybierz WPIS...

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image151.png)

1.  Wklej **zewnętrzny punkt końcowy** adresu URL w aplikacji postman, w obszarze **adresu URL żądania** Dodawanie  **\\wynik** na końcu adresu URL, jak pokazano poniżej.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image152.png)

1.  Wybierz **treści** kartę, a następnie je wpisać jako **pierwotne**, następnie **JSON**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image153.png)

1.  W przeglądarce internetowej przejdź do **zewnętrzny punkt końcowy**. Dodanie następującego elementu do adresu URL **/swagger.json** prowadzi to do pliku struktury Swagger usługi używane do testowania instalacji.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image154.png)

1.  Skopiuj przykład na liście **Swagger.JSON** pliku.

2.  W aplikacji Postman, Wklej przykład treść wpisu, a następnie wybierz pozycję **wysyłania**. Wartość powinna zwrócić podobny do przedstawionego poniżej.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Krok 7. Tworzenie usługi Azure Stack konta usługi storage i Storage Queue

Utwórz konto magazynu usługi Azure Stack i kolejki magazynu danych.

1.  Zaloguj się do portalu użytkowników usługi Azure Stack. (Każdej usługi Azure Stack ma unikatowy portal adres URL)

2.  W portalu użytkownika usługi Azure Stack, rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz **wszystkich usług**. Przewiń w dół do **magazynu** i wybierz polecenie **kont magazynu**. W **kont magazynu** wybierz okno **Dodaj**.

3.  Wprowadź nazwę konta magazynu.

4.  Wybierz opcję replikacji dla konta magazynu: **LRS**.

5.  Określ nową grupę zasobów lub wybierz istniejącą grupę zasobów.

6.  Wybierz **lokalnego** dla lokalizacji dla konta magazynu.

7.  Wybierz**Utwórz**można utworzyć konta magazynu.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image156.png)

1.  Wybierz konto magazynu, ostatnio utworzone.

2.  Wybierz**kolejek**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image157.png)

1.  Wybierz **+ kolejka** i nadaj nazwę kolejki, a następnie wybierz **OK.**

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image158.png)

1.  Pobierz **parametry połączenia** kolejki magazynu i skopiuj go.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image159.png)

1.  Przejdź do aplikacji funkcji platformy Azure, a następnie wybierz **ustawienia aplikacji**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image160.png)

1.  W w ustawieniach aplikacji w aplikacji funkcji, przewiń w dół do ustawień aplikacji i wybierz pozycję **+ Dodaj nowe ustawienie**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image161.png)

1.  Wprowadź nazwę konta magazynu w **nazwa** pól, dodając na końcu; _magazynów

Umożliwia to aplikacji dowiedzieć się, że jest to punktu końcowego konta magazynu.

1.  Następnie wklej parametry połączenia do **wartość** pola.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image162.png)

1.  Przewiń do góry, ustawień aplikacji i wybierz **Zapisz**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Aktualizacja funkcji oceniania, należy użyć kolejki magazynu

1.  Wybierz **integracja** dla funkcji i usuń zaznaczenie pola wyboru **UZYSKAĆ** opcji.

2.  Wybierz pozycję **Zapisz**.

3.  Następnie wybierz pozycję **+ nowe dane wyjściowe** z danych wyjściowych.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image164.png)

1.  Następnie wybierz pozycję **Azure Queue Storage** i wybierz polecenie **wybierz**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image165.png)

1.  Aktualizacja **Nazwa kolejki** do kolejki usługi Storage utworzonego wcześniej, a następnie ustaw **połączenie konta magazynu** na połączenie konta magazynu, które są utworzone wcześniej i wybierz **Zapisz.**

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Krok 8: Tworzenie funkcji do obsługi Wyczyść dane

Tworzenie nowej funkcji usługi Azure Stack, aby przenieść Wyczyść dane z usługi Azure Stack na platformie Azure.

1.  Utwórz nową funkcję, wybierając **funkcje**, a następnie **+ nowa funkcja** przycisku.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image167.png)

1.  Wybierz **wyzwalacza czasomierza**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image168.png)

1.  Wybierz **C\#**  jako język i nazwa funkcji: **przekazywania na platformie azure** i ustaw harmonogram **0 0 \*/1 \* \* \***  który CRON jest raz na godzinę.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Pobierz parametry połączenia konta magazynu platformy Azure hostowanej

1.  Przejdź do <https://portal.azure.com> , a następnie wybierz **konta usługi Azure Storage** utworzone wcześniej.

2.  Wybierz **klucze dostępu**, a następnie skopiuj **parametry połączenia** dla konta magazynu.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Aktualizuj przekazywania na platformę azure funkcję, aby używać usługi Azure Storage hostowanych

1.  Przejdź do aplikacji funkcji platformy Azure, a następnie wybierz **ustawienia aplikacji**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image171.png)

1.  W w ustawieniach aplikacji w aplikacji funkcji, przewiń w dół do ustawień aplikacji i wybierz pozycję **+ Dodaj nowe ustawienie**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image172.png)

1.  Wprowadź nazwę konta magazynu w **nazwa** pól, dodając na końcu; _magazynów

Umożliwia to aplikacji dowiedzieć się, że jest to punktu końcowego konta magazynu.

1.  Następnie wklej Azure hostowane parametry połączenia konta magazynu do **wartość** pola.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image173.png)

1.  Przewiń do góry, ustawień aplikacji i wybierz **Zapisz**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image174.png)

1.  Przejdź z powrotem do **przekazywania na platformie azure** funkcji.

2.  Wybierz **integracja** dla funkcji.

3.  Następnie wybierz pozycję **+ nowe dane wyjściowe** z danych wyjściowych.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image175.png)

1.  Następnie wybierz pozycję **usługi Azure Blob Storage** i wybierz polecenie **wybierz**.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image176.png)

1.  Aktualizacja **ścieżki** do kontenera magazynu utworzone wcześniej w następującym formacie: **uploadeddata / .txt {identyfikator guid rand}**, a następnie ustaw **połączenie konta magazynu** do Połączenie konta magazynu na platformie Azure utworzony wcześniej i wybierz **Zapisz.**

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image177.png)

1.  Kopiowanie i wklejanie zawartości przykładu kodu dla **przekazywania na platformie azure** do funkcji.

2.  Zmodyfikuj odpowiednio do potrzeb, aby wskazywał parametry połączenia konta magazynu.

3.  Zapisz i uruchom kod.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image178.png)

1.  Sprawdź konto magazyn hostowany w usłudze Azure, aby wyświetlić dane przeanalizowaniu zapasowych w chmurze na platformie Azure: powodzenie będzie wyglądać podobnie do poniższych.

    ![Tekst alternatywny](media\azure-stack-solution-machine-learning\image179.png)

Dane został oczyszczony z danych poufnych przez usługi Azure Stack hostowanej platformy Kubernetes usługi Machine Learning i przekazany do chmury publicznej platformy Azure z lokalnej usługi Azure Stack, za pomocą stosu hostowanej aplikacji funkcji platformy Azure i można umieścić dane dla operacji przekazywania w edge/odłączony w przypadku scenariusza.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).