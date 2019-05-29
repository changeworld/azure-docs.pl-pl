---
title: Konfigurowanie metody routingu ruchu dla podsieci za pomocą usługi Azure Traffic Manager
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager na potrzeby kierowania ruchu z podsieci użytkownika do określonych punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: allensu
ms.openlocfilehash: da2d4816f3f7a99ac2d213d72d7e801cf630e165
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304949"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Bezpośrednie kierowanie ruchu do określonych punktów końcowych na podstawie podsieci użytkownika przy użyciu usługi Traffic Manager

W tym artykule opisano, jak skonfigurować metodę routingu ruchu dla podsieci. Metoda routingu ruchu **Podsieć** pozwala na mapowanie zestawu zakresów adresów IP do określonych punktów końcowych. Po odebraniu żądania usługa Traffic Manager sprawdza źródłowy adres IP żądania i zwraca skojarzony z nim punkt końcowy.

W tym samouczku użyto routingu dla podsieci, aby w zależności od adresu IP użytkownika zapytania kierować ruch do wewnętrznej lub produkcyjnej witryny internetowej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Tworzenie profilu usługi Traffic Manager w celu kierowania ruchu na podstawie podsieci użytkownika
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:

- dwie podstawowe witryny internetowe działające w różnych regionach platformy Azure — **Wschodnie stany USA** (służącą jako wewnętrzna witryna internetowa) i **Europa Zachodnia** (służącą jako witryna internetowa w środowisku produkcyjnym).
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie **Wschodnie stany USA** i drugą maszynę wirtualna w regionie **Europa Zachodnia**.

Testowe maszyny wirtualne służą do zilustrowania sposobu, w jaki usługa Traffic Manager kieruje ruch użytkowników do wewnętrznej witryny internetowej lub produkcyjnej witryny internetowej na podstawie podsieci, z której pochodzi zapytanie użytkownika.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Tworzenie dwóch witryn internetowej obejmuje następujące kroki:

1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych

W tej sekcji utworzysz dwie maszyny wirtualne *myIISVMEastUS* i *myIISVMWestEurope* w **wschodnie stany USA** i **Europa Zachodnia** regiony platformy Azure.

1. W górnym lewym rogu witryny Azure portal, wybierz opcję **Utwórz zasób** > **obliczenia** > **systemie Windows Datacenter 2019**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja** > **Grupa zasobów**: Wybierz **Utwórz nową** , a następnie wpisz **myResourceGroupTM1**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: Type *myIISVMEastUS*.
   - **Szczegóły wystąpienia** > **Region**:  Wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora** > **Username**:  Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora** > **hasło**:  Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Reguły portów wejściowych** > **publiczne porty wejściowe**: Wybierz pozycję **Zezwalaj na wybrane porty**.
   - **Reguły portów wejściowych** > **wybierz porty dla ruchu przychodzącego**: Wybierz **RDP** i **HTTP** w ściągania w polu listy rozwijanej.

3. Wybierz **zarządzania** karty lub wybierz **dalej: Dyski**, a następnie pozycję **Dalej: Sieć**, następnie **dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przegląd + utwórz**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę Wirtualną o nazwie *myIISVMWestEurope*, za pomocą **grupy zasobów** nazwa *myResourceGroupTM2*, **lokalizacji**z *Europa Zachodnia*i inne ustawienia takie same jak *myIISVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji, zainstaluj na serwerze usług IIS na dwie maszyny wirtualne - *myIISVMEastUS* & *myIISVMWestEurope*, a następnie zaktualizuj domyślną stronę witryny sieci Web. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
7. Uruchamianie Windows PowerShell na maszynie Wirtualnej *myIISVMEastUS*i za pomocą następujących poleceń do zainstalowania serwera usług IIS i aktualizowania domyślny plik htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Zamknij połączenie RDP z *myIISVMEastUS* maszyny Wirtualnej.
9. Powtórz kroki 1 – 6 z poprzez utworzenie połączenia RDP z maszyną Wirtualną *myIISVMWestEurope* w ramach *myResourceGroupTM2* grupę zasobów, aby zainstalować usługi IIS i dostosować jego domyślnej strony sieci web.
10. Uruchamianie Windows PowerShell na *myIISVMWestEurope* maszyny Wirtualnej, a za pomocą następujących poleceń do zainstalowania serwera usług IIS i aktualizowania domyślny plik htm.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS dla serwerów usług IIS — *myIISVMEastUS* i *myIISVMWestEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1 – 3 dla maszyny Wirtualnej o nazwie *myIISVMWestEurope* znajdującą się w *myResourceGroupTM2* grupy zasobów.

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz Maszynę wirtualną (*myVMEastUS* i *myVMWestEurope*) w każdym regionie platformy Azure (**wschodnie stany USA** i **Europa Zachodnia**). Te maszyny wirtualne użyje do testowania, jak usługa Traffic Manager kieruje ruch użytkowników tak, na podstawie podsieci zapytania użytkownika.

1. W górnym lewym rogu witryny Azure portal, wybierz opcję **Utwórz zasób** > **obliczenia** > **systemie Windows Datacenter 2019**.
2. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:

   - **Subskrypcja** > **Grupa zasobów**: Wybierz pozycję **myResourceGroupTM1**.
   - **Szczegóły wystąpienia** > **Nazwa maszyny wirtualnej**: Type *myVMEastUS*.
   - **Szczegóły wystąpienia** > **Region**:  Wybierz pozycję **Wschodnie stany USA**.
   - **Konto administratora** > **Username**:  Wprowadź wybraną nazwę użytkownika.
   - **Konto administratora** > **hasło**:  Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Reguły portów wejściowych** > **publiczne porty wejściowe**: Wybierz pozycję **Zezwalaj na wybrane porty**.
   - **Reguły portów wejściowych** > **wybierz porty dla ruchu przychodzącego**: Wybierz **RDP** w ściągania w polu listy rozwijanej.

3. Wybierz **zarządzania** karty lub wybierz **dalej: Dyski**, a następnie pozycję **Dalej: Sieć**, następnie **dalej: Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**.
4. Wybierz pozycję **Przegląd + utwórz**.
5. Przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz**.  
6. Postępuj zgodnie z instrukcjami, aby utworzyć drugą maszynę Wirtualną o nazwie *myVMWestEurope*, za pomocą **grupy zasobów** nazwa *myResourceGroupTM2*, **lokalizacji** z *Europa Zachodnia*i inne ustawienia takie same jak *myVMEastUS*.
7. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

Utwórz profil usługi Traffic Manager, który umożliwia zwracanie określonych punktów końcowych w oparciu o źródłowy adres IP żądania.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Name (Nazwa)                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz metodę routingu**Podsieć**.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Istniejąca** i wprowadź *myResourceGroupTM1*. |
    | |                              |
    |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne uruchomione usługi IIS serwery — *myIISVMEastUS* & *myIISVMWestEurope* na potrzeby kierowania ruchu użytkowników na podstawie podsieci zapytania użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Type                    | Punkt końcowy platformy Azure                                   |
    | Name (Nazwa)           | myInternalWebSiteEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Ustawienia routingu dla podsieci    |   Dodaj adres IP *myVMEastUS* testowania maszyny Wirtualnej. Każde zapytanie użytkownika pochodzących z tej maszyny Wirtualnej zostanie skierowany do *myInternalWebSiteEndpoint*.    |

4. Powtórz kroki 2 i 3, aby dodać innego punktu końcowego o nazwie *myProdWebsiteEndpoint* za publiczny adres IP *myIISVMWestEurope ip* skojarzony z serwerem IIS maszyny Wirtualnej o nazwie  *myIISVMWestEurope*. Aby uzyskać **routingu ustawienia podsieci**, Dodaj adres IP testową maszynę Wirtualną — *myVMWestEurope*. Każde zapytanie użytkownika z tej testowej maszyny wirtualnej będzie kierowane do punktu końcowego *myProdWebsiteEndpoint*.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager

W tej sekcji przetestujesz kierowanie ruchu użytkowników z danej podsieci do określonego punktu końcowego przez usługę Traffic Manager. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:

1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z testową maszynę Wirtualną (*myVMEastUS*) znajdującą się w **wschodnie stany USA** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.
    - Z testową maszynę Wirtualną (*myVMWestEurope*) znajdującą się w **Europa Zachodnia** regionu, w przeglądarce internetowej przejdź do profilu usługi Traffic Manager nazwę DNS.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager

Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
2. Kliknij pozycję **Przegląd**.
3. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager

W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ maszyna wirtualna *myVMEastUS* adres IP jest skojarzony z punktem końcowym *myInternalWebsiteEndpoint*, przeglądarki sieci web uruchamia Test serwera witryny sieci Web - *myIISVMEastUS*.

7. Następnie połącz się z maszyną wirtualną *myVMWestEurope* na terenie **Europa Zachodnia** korzystając z kroków 1 – 5 i przejdź do nazwy domeny profilu usługi Traffic Manager z tej maszyny Wirtualnej. Ponieważ maszyna wirtualna *myVMWestEurope* adres IP jest skojarzony z punktem końcowym *myProductionWebsiteEndpoint*, przeglądarki sieci web uruchamia Test serwera witryny sieci Web - *myIISVMWestEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager

Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
