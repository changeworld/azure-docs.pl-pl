---
title: Konfigurowanie metody routingu ruchu dla podsieci za pomocą usługi Azure Traffic Manager
description: W tym artykule opisano sposób konfigurowania usługi Traffic Manager na potrzeby kierowania ruchu z podsieci użytkownika do określonych punktów końcowych.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 16822a4928f0a68146bdb55f5bab4dd99df6236b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329561"
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
W tej sekcji utworzysz dwie maszyny wirtualne *InternalWebsite* i *ProdWebsite* w regionach **Wschodnie stany USA** i **Europa Zachodnia** platformy Azure.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|InternalWebsite|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM1*.|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet1*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–6 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Grupa zasobów | Wybierz pozycję **Nowa**, a następnie wpisz *myResourceGroupTM2*|
    |Lokalizacja|Europa Zachodnia|
    |Nazwa maszyny wirtualnej | ProdWebsite|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **nazwa** wprowadź *myVNet2*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji zainstaluj serwer usług IIS na dwóch maszynach wirtualnych — *myIISVMEastUS* &  i *myIISVMWEurope*, a następnie zaktualizuj domyślną stronę witryny internetowej. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
7. Uruchom program Windows PowerShell na maszynie wirtualnej *InternalWebsite* i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```
8. Zamknij połączenie protokołu RDP z maszyną wirtualną *InternalWebsite*.
9. Powtórz kroki 1–6, tworząc połączenie RDP z maszyną wirtualną *ProdWebsite* w grupie zasobów *myResourceGroupTM2*, aby zainstalować usługi IIS i dostosować domyślną stronę internetową.
10. Uruchom program Windows PowerShell na maszynie wirtualnej *ProdWebsite* i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS dla serwerów usług IIS — *InternalWebsite* i *ProdWebsite*.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybiera zasób *InternalWebsite*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1–3 dla maszyny wirtualnej o nazwie *ProdWebsite* znajdującej się w grupie zasobów *myResourceGroupTM2*.

### <a name="create-test-vms"></a>Tworzenie testowych maszyn wirtualnych

W tej sekcji utworzysz maszyny wirtualne (*UserVMUS* i *UserVMEurope*) w obu regionach platformy Azure (**Wschodnie stany USA** i **Europa Zachodnia**. Te maszyny wirtualne posłużą do testowania, jak usługa Traffic Manager kieruje ruch do najbliższego serwera usług IIS, kiedy użytkownik nawiguje do witryny internetowej.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Windows Server 2016 Datacenter**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|*UserVMUS*|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet3*, a jako nazwę podsieci wprowadź *mySubnet3*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

7. Wykonaj ponownie kroki 1–5 z następującymi zmianami:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa maszyny wirtualnej | *UserVMEurope*|
    |Grupa zasobów | Wybierz pozycję **Istniejąca**, a następnie wpisz *myResourceGroupTM2*|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet4*, a jako nazwę podsieci wprowadź *mySubnet4*.|
    |||

8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

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

Dodaj dwie maszyny wirtualne z działającymi serwerami usług IIS — *InternalWebsite* &  i *ProdWebsite* na potrzeby kierowania ruchu na podstawie podsieci zapytania użytkownika.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Type                    | Punkt końcowy platformy Azure                                   |
    | Name (Nazwa)           | myInternalWebSiteEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *InternalWebsite-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Ustawienia routingu dla podsieci    |   Dodaj adres IP testowej maszyny wirtualnej *UserVMUS*. Każde zapytanie użytkownika pochodzące z tej maszyny wirtualnej zostanie skierowane do punktu końcowego *InternalWebSiteEndpoint*.    |

4. Powtórz kroki 2 i 3, aby dodać inny punkt końcowy o nazwie *ProdWebsiteEndpoint* z publicznym adresem IP *ProdWebsite-ip*, który jest skojarzony z serwerem IIS na maszynie wirtualnej o nazwie *ProdWebsite*. Aby uzyskać **Ustawienia routingu dla podsieci**, dodaj adres IP testowej maszyny wirtualnej — *UserVMEurope*. Każde zapytanie użytkownika z tej testowej maszyny wirtualnej będzie kierowane do punktu końcowego *myProdWebsiteEndpoint*.
5. Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager
W tej sekcji przetestujesz kierowanie ruchu użytkowników z danej podsieci do określonego punktu końcowego przez usługę Traffic Manager. Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:
1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Wyświetl informacje o działaniu usługi Traffic Manager w następujący sposób:
    - Z testowej maszyny wirtualnej (*UserVMUS*) znajdującej się w regionie **Wschodnie stany USA** przejdź w przeglądarce sieci Web do nazwy DNS profilu usługi Traffic Manager.
    - Z testowej maszyny wirtualnej (*UserVMEurope*) znajdującej się w regionie **Europa Zachodnia** przejdź w przeglądarce sieci Web do nazwy DNS profilu usługi Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager
Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych.

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1. Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
1. Kliknij pozycję **Przegląd**.
2. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
W tej sekcji zobaczysz działanie usługi Traffic Manager.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myUserVMUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**.
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
1. W przeglądarce sieci Web na maszynie wirtualnej *UserVMUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Ponieważ adres IP maszyny wirtualnej *UserVMUS* jest skojarzony z punktem końcowym *myInternalWebsiteEndpoint*, przeglądarka internetowa uruchamia testowy serwer witryny internetowej — *InternalWebsite*.

2. Następnie połącz się z maszyną wirtualną *UserVMEurope* w regionie **Europa Zachodnia** korzystając z kroków 1–5 i przejdź do nazwy domeny profilu usługi Traffic Manager z tej maszyny Wirtualnej. Ponieważ adres IP maszyny wirtualnej *UserVMEurope* jest skojarzony z punktem końcowym *myProductionWebsiteEndpoint*, przeglądarka internetowa uruchomi testowy serwer witryny internetowej — *ProdWebsite*.

## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager
Kiedy grupy zasobów (**ResourceGroupTM1** i **ResourceGroupTM2**) przestaną być potrzebne, usuń je. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [ważonej metodzie routingu ruchu](traffic-manager-configure-weighted-routing-method.md).
- Dowiedz się więcej o [priorytetowej metodzie routingu](traffic-manager-configure-priority-routing-method.md).
- Dowiedz się więcej o [geograficznej metodzie routingu](traffic-manager-configure-geographic-routing-method.md).
