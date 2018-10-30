---
title: Samouczek — kierowanie ruchu do punktów końcowych z wagami przy użyciu usługi Azure Traffic Manager | Microsoft Docs
description: W tym artykule zawierającym samouczek opisano sposób kierowania ruchu do punktów końcowych z wagami za pomocą usługi Traffic Manager.
services: traffic-manager
author: KumudD
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: kumud
ms.openlocfilehash: 171a9cfe5b5b63efde31c37cd49ef548f1e4ebcc
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649438"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-using-traffic-manager"></a>Samouczek: sterowanie routingiem ruchu za pomocą punktów końcowych z wagami przy użyciu usługi Traffic Manager 

W tym samouczku opisano, jak sterować routingiem ruchu użytkowników między punktami końcowymi metodą routingu ważonego za pomocą usługi Traffic Manager. W przypadku tej metody routingu do każdego punktu końcowego przypisywane są wagi w konfiguracji profilu usługi Traffic Manager, a ruch użytkowników jest kierowany zgodnie z wagami przypisanymi do poszczególnych punktów końcowych. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Im większa jest wartość wagi przypisana do punktu końcowego, tym wyższy jest priorytet.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie dwóch maszyn wirtualnych z podstawową witryną internetową w usługach IIS
> * Tworzenie dwóch testowych maszyn wirtualnych, aby zobaczyć usługę Traffic Manager w działaniu
> * Konfigurowanie nazwy DNS dla maszyn wirtualnych z uruchomionymi usługami IIS
> * Tworzenie profilu usługi Traffic Manager
> * Dodawanie punktów końcowych maszyny wirtualnej do profilu usługi Traffic Manager
> * Wyświetlanie informacji o działaniu usługi Traffic Manager

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne
Aby w tym samouczku zobaczyć usługi Traffic Manager w działaniu, trzeba wdrożyć:
- dwa wystąpienia podstawowych witryn internetowych działających w różnych regionach świadczenia usługi Azure — **Wschodnie stany USA** i **Europa Zachodnia**.
- dwa testowe maszyny wirtualne do testowania usługi Traffic Manager — jedną maszynę wirtualną w regionie **Wschodnie stany USA** i drugą maszynę wirtualna w regionie **Europa Zachodnia**. Testowe maszyny wirtualne są używane w celu zilustrowania sposobu kierowania ruchu użytkowników przez usługę Traffic Manager do witryny internetowej, która ma wyższą wagę przypisaną do punktu końcowego.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure 

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

### <a name="create-websites"></a>Tworzenie witryn internetowych

W tej sekcji opisano tworzenie dwóch wystąpień witryny internetowej, które zapewniają dwa punkty końcowe usługi dla profilu usługi Traffic Manager w dwóch regionach platformy Azure. Aby utworzyć te dwie witryny internetowe, wykonaj następujące czynności:
1. Utwórz dwie maszyny wirtualne w celu uruchomienia podstawowej witryny internetowej — jedną w regionie **Wschodnie stany USA**, a drugą w regionie **Europa Zachodnia**.
2. Zainstaluj serwer IIS na każdej maszynie wirtualnej i zaktualizuj domyślną stronę witryny internetowej, aby informowała o nazwie maszyny wirtualnej, z którą łączy się użytkownik podczas odwiedzania witryny internetowej.

#### <a name="create-vms-for-running-websites"></a>Tworzenie maszyn wirtualnych do uruchamiania witryn internetowych
W tej sekcji utworzysz dwie maszyny wirtualne *myIISVMEastUS* i *myIISVMWEurope* w regionach świadczenia usługi Azure **Wschodnie stany USA** i **Europa Zachodnia**.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myIISVMEastUS|
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
    |Nazwa maszyny wirtualnej | myIISVMWEurope|
    |Sieć wirtualna | Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **nazwa** wprowadź *myVNet2*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |||
8. Proces tworzenia maszyny wirtualnej może potrwać kilka minut. Nie kontynuuj wykonywania pozostałych kroków, dopóki obie maszyny wirtualne nie zostaną utworzone.

![Tworzenie maszyny wirtualnej](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalowanie usług IIS i dostosowywanie domyślnej strony internetowej

W tej sekcji zainstaluj serwer usług IIS na dwóch maszynach wirtualnych — *myIISVMEastUS*  &  i *myIISVMWEurope*, a następnie zaktualizuj domyślną stronę witryny internetowej. Niestandardowa strona witryny internetowej przedstawia nazwę maszyny wirtualnej, z którą jest nawiązywane połączenie podczas odwiedzania witryny internetowej z przeglądarki sieci Web.

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**. 
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Menedżer serwera**.
7. Uruchom program Windows PowerShell na maszynie wirtualnej VM1 i użyj poniższych poleceń, aby zainstalować serwer usług IIS i zaktualizować domyślny plik HTM.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalowanie usług IIS i dostosowywanie strony internetowej](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Zamknij połączenie protokołu RDP z maszyną wirtualną *myIISVMEastUS*.
9. Powtórz kroki 1–8, tworząc połączenie RDP z maszyną wirtualną *myIISVMWEurope* w grupie zasobów *myResourceGroupTM2*, aby zainstalować usługi IIS i dostosować domyślną stronę internetową.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurowanie nazw DNS dla maszyn wirtualnych z uruchomionymi usługami IIS

Usługa Traffic Manager kieruje ruch użytkowników na podstawie nazwy DNS punktów końcowych usługi. W tej sekcji skonfigurujesz nazwy DNS dla serwerów usług IIS — *myIISVMEastUS* i *myIISVMWEurope*.

1. Kliknij opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów wybierz zasób *myIISVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd**, w obszarze **Nazwa DNS**, wybierz opcję **Konfiguruj**.
3. Na stronie **Konfiguracja**, w obszarze etykieta nazwy DNS, dodaj unikatową nazwę, a następnie wybierz opcję **Zapisz**.
4. Powtórz kroki 1–3 dla maszyny wirtualnej o nazwie *myIISVMWEurope* znajdującej się w grupie zasobów *myResourceGroupTM1*.

### <a name="create-a-test-vm"></a>Tworzenie testowej maszyny wirtualnej

W tej sekcji utworzysz maszynę wirtualną — *mVMEastUS*. Ta maszyna wirtualna zostanie użyta do przetestowania sposobu kierowania ruchu przez usługę Traffic Manager do punktu końcowego witryny internetowej, który ma wyższą wartość wagi.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Środowisko obliczeniowe** > **Maszyna wirtualna z systemem Windows Server 2016**.
2. Wprowadź lub wybierz następujące informacje w obszarze **Podstawy**, zaakceptuj wartości domyślne dla pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVMEastUS|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*.|
    |||

4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz następujące wartości w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:
    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna| Wybierz opcję **Sieć wirtualna** w obszarze **Tworzenie sieci wirtualnej**, następnie w polu **Nazwa** wprowadź *myVNet3*, a jako nazwę podsieci wprowadź *mySubnet*.|
    |Sieciowa grupa zabezpieczeń|Wybierz opcję **Podstawowa**, a następnie z listy rozwijanej **Dodaj publiczne porty wejściowe** wybierz opcje **HTTP** i **RDP** |
    |Diagnostyka rozruchu|Wybierz opcję **Wyłączone**.|
    |||

6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.
8. W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj wykonywania pozostałych kroków, dopóki nie zostanie utworzona maszyna wirtualna.

## <a name="create-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager
Utwórz profil usługi Traffic Manager, bazując na metodzie routingu **ważonego**.

1. W lewej górnej części ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Profil usługi Traffic Manager** > **Utwórz**.
2. W obszarze **Tworzenie profilu usługi Traffic Manager** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Name (Nazwa)                   | Ta nazwa musi być unikatowa w obrębie strefy trafficmanager.net. Na jej podstawie zostanie utworzona nazwa DNS trafficmanager.net służąca do uzyskiwania dostępu do profilu usługi Traffic Manager.                                   |
    | Metoda routingu          | Wybierz **ważoną** metodę routingu.                                       |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Istniejąca** i wybierz grupę *myResourceGroupTM1*. |
    |        |   |

    ![Tworzenie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Dodawanie punktów końcowych usługi Traffic Manager

Dodaj dwie maszyny wirtualne, na których działają serwery IIS — *myIISVMEastUS*  & *myIISVMWEurope*, aby skierować do nich ruch użytkowników.

1. Na pasku wyszukiwania portalu wyszukaj nazwę profilu usługi Traffic Manager, który został utworzony w poprzedniej sekcji, a następnie wybierz ten profil w wyświetlonych wynikach.
2. W obszarze **Profil usługi Traffic Manager** w sekcji **Ustawienia** kliknij pozycję **Punkty końcowe**, a następnie kliknij pozycję **Dodaj**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Typ                    | Punkt końcowy platformy Azure                                   |
    | Name (Nazwa)           | myEastUSEndpoint                                        |
    | Typ zasobu docelowego           | Publiczny adres IP                          |
    | Zasób docelowy          | **Wybierz publiczny adres IP**, aby wyświetlić listę zasobów z publicznymi adresami IP w ramach tej samej subskrypcji. W obszarze **Zasób** wybierz publiczny adres IP o nazwie *myIISVMEastUS-ip*. Jest to publiczny adres IP serwera usług IIS maszyny wirtualnej w regionie Wschodnie stany USA.|
    |  Waga      | Wprowadź wartość **100**.        |
    |        |           |

4. Powtórz kroki 2 i 3, aby dodać inny punkt końcowy o nazwie *myWestEuropeEndpoint* z publicznym adresem IP *myIISVMWEurope-ip*, który jest skojarzony z serwerem IIS na maszynie wirtualnej o nazwie *myIISVMWEurope*, a w polu **Waga** wprowadź wartość **25**. 
5.  Po zakończeniu dodawania obu punktów końcowych będą one wyświetlane w obszarze **Profil usługi Traffic Manager** ze stanem monitorowania **Online**.

## <a name="test-traffic-manager-profile"></a>Testowanie profilu usługi Traffic Manager
Aby zobaczyć działanie usługi Traffic Manager, wykonaj następujące czynności:
1. Określ nazwę DNS profilu usługi Traffic Manager.
2. Sprawdź działanie usługi Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Określanie nazwy DNS profilu usługi Traffic Manager
Dla uproszczenia w tym samouczku użyjesz nazwy DNS profilu usługi Traffic Manager do odwiedzania witryn internetowych. 

Nazwę DNS profilu usługi Traffic Manager można określić w następujący sposób:

1.  Na pasku wyszukiwania portalu wyszukaj nazwę **profilu usługi Traffic Manager**, który został utworzony w poprzedniej sekcji. W wyświetlonych wynikach kliknij profil usługi Traffic Manager.
1. Kliknij pozycję **Przegląd**.
2. W obszarze **Profil usługi Traffic Manager** zostanie wyświetlona nazwa DNS nowo utworzonego profilu usługi Traffic Manager. We wdrożeniach produkcyjnych można skonfigurować niestandardową nazwę domeny w celu wskazania nazwy domeny usługi Traffic Manager przy użyciu rekordu CNAME systemu DNS.

   ![Nazwa DNS usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Wyświetlanie informacji o działaniu usługi Traffic Manager
W tej sekcji zobaczysz działanie usługi Traffic Manager. 

1. Wybierz opcję **Wszystkie zasoby** w menu po lewej stronie, a następnie na liście zasobów kliknij zasób *myVMEastUS*, który znajduje się w grupie zasobów *myResourceGroupTM1*.
2. Na stronie **Przegląd** kliknij opcję **Połącz**, a następnie w polu **Połącz z maszyną wirtualną**, wybierz opcję **Pobierz plik RDP**. 
3. Otwórz pobrany plik rdp. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej. 
4. Kliknij przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie. 
6. W przeglądarce internetowej na maszynie wirtualnej *myVMEastUS* wpisz nazwę DNS profilu usługi Traffic Manager, aby wyświetlić witrynę internetową. Nastąpi przekierowanie do witryny internetowej hostowanej na serwerze IIS *myIISVMEastUS*, ponieważ ma on przypisaną wyższą wartość wagi (**100**) niż serwer IIS *myIISVMWEurope* (z przypisaną niższą wartością wagi punktu końcowego — **25**).

   ![Testowanie profilu usługi Traffic Manager](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)
   
## <a name="delete-the-traffic-manager-profile"></a>Usuwanie profilu usługi Traffic Manager
Jeśli nie będą Ci już potrzebne, usuń grupy zasobów, które zostały utworzone w ramach tego samouczka. Aby to zrobić, wybierz grupę zasobów (**ResourceGroupTM1** lub **ResourceGroupTM2**), a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kierowanie ruchu do określonych punktów końcowych na podstawie lokalizacji geograficznej użytkownika](traffic-manager-configure-geographic-routing-method.md)


