---
title: Utwórz ruchu sieciowego trasy do trasy zdefiniowane przez użytkownika za pośrednictwem sieci urządzenie wirtualne - portalu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzona jest trasa zdefiniowana przez użytkownika, aby zastąpić domyślny Azure routingu przez routingu ruchu sieciowego przez urządzenie wirtualne sieci.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2018
---
# <a name="create-a-user-defined-route---azure-portal"></a>Tworzenie trasy zdefiniowane przez użytkownika - portalu Azure

W tym samouczku, Dowiedz się, jak utworzyć trasy zdefiniowane przez użytkownika, aby kierować ruchem między dwiema [sieci wirtualnej](virtual-networks-overview.md) podsieci przez urządzenie wirtualne sieci. Urządzenie wirtualne sieci jest maszyny wirtualnej, która uruchamia aplikację sieciową, takie jak zapory. Aby dowiedzieć się więcej na temat wstępnie skonfigurowanej sieci wirtualnych urządzeń, które można wdrożyć w sieci wirtualnej platformy Azure, zobacz [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Po utworzeniu podsieci w sieci wirtualnej Azure tworzy domyślne [tras systemowych](virtual-networks-udr-overview.md#system-routes) umożliwiających zasoby we wszystkich podsieciach do komunikowania się ze sobą, jak pokazano na poniższej ilustracji:

![Trasy domyślne](./media/create-user-defined-route/default-routes.png)

W tym samouczku utworzysz sieć wirtualną z publiczne, prywatne i sieć obwodową podsieci, jak pokazano na rysunku poniżej. Zwykle serwery sieci web mógł zostać wdrożony do publicznego podsieci i aplikacji lub serwer bazy danych może być wdrożona w sieci prywatnej. Utwórz maszynę wirtualną na działanie jako urządzenie wirtualne sieci w podsieci obwodowej i opcjonalnie, utwórz maszynę wirtualną w każdej podsieci, które komunikują się za pośrednictwem sieci urządzenia wirtualnego. Cały ruch między podsieciami publiczne i prywatne jest kierowany przez urządzenia, jak pokazano na poniższej ilustracji:

![Trasy definiowane przez użytkownika](./media/create-user-defined-route/user-defined-routes.png)

Ten artykuł zawiera kroki umożliwiające utworzenie trasy zdefiniowanej przez użytkownika za pośrednictwem modelu wdrażania usługi Resource Manager, który jest modelem wdrożenia, które firma Microsoft zaleca używanie podczas tworzenia tras zdefiniowanych przez użytkownika. Jeśli konieczne jest utworzenie trasy zdefiniowanej przez użytkownika (klasyczne), zobacz [utworzenie trasy zdefiniowanej przez użytkownika (klasyczne)](virtual-network-create-udr-classic-ps.md). Jeśli nie masz doświadczenia w obsłudze modele wdrażania platformy Azure, zobacz [modele wdrażania zrozumieć Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [User-defined routes overview](virtual-networks-udr-overview.md#user-defined) (Omówienie tras definiowanych przez użytkownika).

## <a name="create-routes-and-network-virtual-appliance"></a>Tworzenie trasy i urządzenie wirtualne sieci

1. **Wymagań wstępnych**: tworzenie sieci wirtualnej z dwoma podsieciami, wykonując kroki opisane w [utworzyć sieć wirtualną](#create-a-virtual-network).
2. Po utworzeniu sieci wirtualnej, a w przeglądarce sieci Web, przejdź do [portalu Azure](https://portal.azure.com). Zaloguj się za pomocą programu [konta Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Na **wyszukiwania zasobów** polu w górnej części portalu, wprowadź *myResourceGroup*. Kliknij przycisk **myResourceGroup** po wyświetleniu w wynikach wyszukiwania. Grupa zasobów została utworzona w ramach wymagań wstępnych.
4. Kliknij przycisk **myVnet**, jak pokazano na poniższej ilustracji:

    ![Ustawienia interfejsu sieciowego](./media/create-user-defined-route/virtual-network.png)

5. Utwórz podsieć dla urządzenia wirtualnego sieci:
 
    - W obszarze **myVnet**, kliknij przycisk **podsieci** w obszarze **ustawienia** po lewej stronie.
    - Kliknij przycisk **+ podsieci**, jak pokazano na poniższej ilustracji:

        ![Podsieci](./media/create-user-defined-route/subnets.png) 
    - Wprowadź następujące wartości w obszarze **Dodaj podsieć**, następnie kliknij przycisk **OK**:

        |Ustawienie|Wartość|
        |-----|-----|
        |Name (Nazwa)|DMZ|
        |Zakres adresów (blok CIDR)|10.0.2.0/24|

6. Utwórz maszynę wirtualną urządzenie wirtualne sieci:

    - Po lewej stronie portalu, kliknij przycisk **+ nowy**, następnie kliknij przycisk **obliczeniowe**, a następnie kliknij przycisk **systemu Windows Server Datacenter 2016** lub **Ubuntu Server 16.04 LTS**.
    - Wprowadź następujące wartości **podstawy** bloku, zostanie wyświetlone, następnie kliknij przycisk **OK**.

        |Ustawienie|Wartość|
        |---|---|
        |Name (Nazwa)|myVm-Nva|
        |Nazwa użytkownika|użytkownik_azure|
        |Hasło i Potwierdź hasło|Hasło wybrane|
        |Subskrypcja|Wybierz subskrypcję|
        |Grupa zasobów|Kliknij przycisk **Użyj istniejącego**, a następnie wybierz pozycję **myResourceGroup**|
        |Lokalizacja|Wschodnie stany USA|
    - Na **wybierz rozmiar** bloku, który jest wyświetlany, kliknij przycisk **DS1_V2 standardowe**, następnie kliknij przycisk **wybierz**.
    - Na **ustawienia** bloku, który jest wyświetlany, kliknij przycisk **sieci wirtualnej**. Kliknij przycisk **myVnet** w **sieci wirtualnej wybierz** wyświetlonym bloku.
    - Na **ustawienia** bloku, kliknij przycisk **podsieci**. Kliknij przycisk **DMZ** na **Wybierz podsieć** wyświetlonym bloku. 
    - Pozostaw wartości domyślne dla pozostałych ustawień i kliknij przycisk **OK**.
    - Kliknij przycisk **Utwórz** na **Utwórz** wyświetlonym bloku. Wdrożenie maszyny wirtualnej trwa kilka minut.

    > [!NOTE]
    > Oprócz tworzenia maszyny wirtualnej, portalu Azure tworzy publiczny adres IP i przypisuje go do maszyny wirtualnej, domyślnie. Jeśli zostały wdrażanie maszyny wirtualnej w środowisku produkcyjnym, możesz nie można przypisać do publicznego adresu IP do maszyny wirtualnej. Urządzenie wirtualne sieci może dostęp z innych maszyn wirtualnych w sieci wirtualnej. Aby dowiedzieć się więcej na temat publiczne adresy IP, zobacz [Zarządzanie publicznego adresu IP](virtual-network-public-ip-address.md).

7. Przypisz statycznego prywatnego adresu IP, a następnie włączyć funkcji przekazywania dla interfejsu sieciowego portalu, który został utworzony w poprzednim kroku adresu IP. 
    - Na **wyszukiwania zasobów** polu w górnej części strony, wprowadź *myVm Nva*.
    - Kliknij przycisk **myVm Nva** po wyświetleniu w wynikach wyszukiwania.
    - Kliknij przycisk **sieci** w obszarze **ustawienia** po lewej stronie.
    - Kliknij nazwę interfejsu sieciowego w obszarze **interfejsy sieciowe myVm Nva -**. Nazwa jest **myvm nva *** X*, gdzie *X* to numer przypisany przez portal.
    - Kliknij przycisk **konfiguracje adresów IP** w obszarze **ustawienia** dla interfejsu sieciowego, jak pokazano na poniższej ilustracji:

        ![Ustawienia interfejsu sieciowego](./media/create-user-defined-route/network-interface-settings.png)
        
    - Kliknij przycisk **włączone** dla **przesyłanie dalej IP** ustawienie, następnie kliknij przycisk **zapisać**. Przesyłanie dalej IP musi być włączony dla każdego interfejsu sieciowego, który odbiera ruch nie jest skierowana do adresu IP przypisane do niej. Włączenie przesyłania dalej IP wyłączenie wyboru Lokalizacja źródłowa/docelowa platformy Azure dla interfejsu sieciowego.
    - Kliknij przycisk **ipconfig1** na liście konfiguracje adresów IP.
    - Kliknij przycisk **statycznych** dla **przypisania** prywatnego adresu IP w obszarze **ipconfig1**, jak pokazano na poniższej ilustracji:

        ![Konfiguracja adresu IP](./media/create-user-defined-route/ip-configuration.png)
    - Jak pokazano na rysunku powyżej, wprowadź *10.0.2.4* w obszarze **adres IP** w **ustawienia prywatnego adresu IP**. Przypisanie statycznego adresu IP do interfejsu sieciowego zapewnia, że adres nie ulega zmianie przez cały okres istnienia interfejs sieciowy jest dołączony do maszyny wirtualnej. Wprowadzony adres nie jest aktualnie przypisany do innego zasobu w podsieci sieci obwodowej, która jest interfejs sieciowy. 
    - Aby zapisać konfigurację, kliknij przycisk **zapisać** w obszarze **ipconfig1**. Pozostaw pole ipconfig1, dopóki nie zostanie wyświetlone powiadomienie w portalu interfejs sieciowy jest zapisywany.
 
8. Utwórz dwie tabele tras. Tabele tras zawierać zero lub więcej trasy:

    - Po lewej stronie portalu, kliknij przycisk **+ nowy** > **sieci** > **tabeli tras**.
    - Wprowadź następujące wartości w obszarze **utworzyć tabelę tras**, a następnie kliknij przycisk **Utwórz**:

        |Ustawienie|Wartość|
        |---|---|
        |Name (Nazwa)|myRouteTable-Public|
        |Subskrypcja|Wybierz subskrypcję|
        |Grupa zasobów|Wybierz **Użyj istniejącego**, a następnie wybierz pozycję **myResourceGroup**|
        |Lokalizacja|Wschodnie stany USA|
    
    - Zakończenie poprzedniego podetapów w kroku 8 ponownie, ale nazwa tabeli tras *myRouteTable prywatnego*.
9. Dodawanie tras do *publicznego myRouteTable* tabeli tras i skojarz tabeli tras do *publicznego* podsieci:

    - Na **wyszukiwania zasobów** polu w górnej części portalu, wprowadź *myRouteTable publicznego*. Kliknij przycisk **myRouteTable publicznego** po wyświetleniu w wynikach wyszukiwania.
    - W obszarze **publicznego myRouteTable**, kliknij przycisk **tras** na liście **ustawienia**.
    - Kliknij przycisk **+ Dodaj** w obszarze **publicznego myRouteTable - tras**.
    -  Domyślnie program Azure tras ruchu między wszystkich podsieci w sieci wirtualnej. Utwórz trasę, aby zmienić domyślne Azure routingu, który ruch z *publicznego* podsieci jest kierowany przez NVA, zamiast bezpośrednio do *prywatnej* podsieci. Wprowadź następujące wartości **Dodaj trasę** bloku, który zostanie wyświetlony, a następnie kliknij przycisk **OK**:

        |Ustawienie|Wartość|Wyjaśnienie|
        |---|---|---|
        |Nazwa trasy|ToPrivateSubnet|Ta trasa kieruje ruch do podsieci prywatnej przez urządzenie wirtualne sieci.|
        |Prefiks adresu|10.0.1.0/24| Cały ruch wysyłany do wszystkich adresów w obrębie tego prefiksu adresu (10.0.1.0 - 10.0.1.254) są wysyłane do urządzenia wirtualnego sieci.|
        |Typ następnego skoku| Wybierz **urządzenie wirtualne**||
        |Adres następnego skoku|10.0.2.4| Statyczne prywatny adres IP interfejsu sieciowego, dołączone do urządzenia wirtualnego sieci. Jest to jedyny typ przeskoku można określić adresu **urządzenie wirtualne**.|

    - W obszarze **publicznego myRouteTable**, kliknij przycisk **podsieci** w obszarze **ustawienia**. 
    - Kliknij przycisk **+ Skojarz** w obszarze **publicznego myRouteTable - podsieci**.
    - Kliknij przycisk **sieci wirtualnej** w obszarze **skojarzyć podsieci**, następnie kliknij przycisk **myVnet**.
    - Kliknij przycisk **podsieci** w obszarze **skojarzyć podsieci**, następnie kliknij przycisk **publicznego** w obszarze **Wybierz podsieć**. 
    - Aby zapisać konfigurację, kliknij przycisk **OK** w obszarze **skojarzyć podsieci**. Podsieć może mieć zero lub jedną tabelę tras skojarzony.
10. Wykonaj krok 9 ponownie, wyszukiwanie **prywatnego myRouteTable**, Tworzenie trasy przy użyciu następujących ustawień, a następnie kojarzenie tabeli tras do **prywatnej** podsieć **myVnet** sieci wirtualnej:

    |Ustawienie|Wartość|Wyjaśnienie|
    |---|---|---|
    |Nazwa trasy|ToPublicSubnet|Tej trasy kieruje ruch do publicznego podsieci za pośrednictwem sieci urządzenia wirtualnego.|
    |Prefiks adresu|10.0.0.0/24| Cały ruch wysyłany do wszystkich adresów w obrębie tego prefiksu adresu (10.0.0.0 - 10.0.1.254) są wysyłane do urządzenia wirtualnego sieci.|
    |Typ następnego skoku| Wybierz **urządzenie wirtualne**||
    |Adres następnego skoku|10.0.2.4||

    Ruch sieciowy między żadnych zasobów w podsieci prywatne i publiczne przechodzi przez urządzenie wirtualne sieci. 
11. **Opcjonalnie:** Utwórz maszynę wirtualną w podsieciach publicznej i prywatnej, a następnie sprawdź, czy komunikacji między maszynami wirtualnymi jest kierowany przez urządzenie wirtualne sieci, wykonując kroki opisane w [zweryfikowaćroutingu](#validate-routing). 
12. **Opcjonalne**: usunięcie zasobów, utworzonych w tym samouczku, wykonując kroki opisane w [zasoby zostaną usunięte](#delete-resources).

## <a name="validate-routing"></a>Sprawdź poprawność routingu

1. Jeśli nie jest jeszcze, wykonaj kroki [tworzenia tras i urządzenie wirtualne sieci](#create-routes-and-network-virtual-appliance).
2. Kliknij przycisk **wypróbuj** przycisk w polu poniżej, które otwiera powłokę chmury Azure. W przypadku wyświetlenia monitu zaloguj się do platformy Azure przy użyciu Twojej [konta Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p). Powłoka chmury Azure jest powłoki bash wolnego z interfejsu wiersza polecenia platformy Azure preinstalowany. 

    Następujące skrypty utworzyć dwie maszyny wirtualne w *publicznego* podsieci i jeden w *prywatnej* podsieci. Skrypty również włączyć funkcję przekazywania pakietów IP dla interfejsu sieciowego w ramach systemu operacyjnego NVA, aby włączyć system operacyjny do routowania ruchu za pośrednictwem interfejsu sieciowego. Zwykle produkcji NVA sprawdza ruch przed przesłaniem go, ale w tym samouczku proste NVA tylko kieruje ruch na bez sprawdzania go. 

    Kliknij przycisk **kopiowania** przycisk **Linux** lub **Windows** skrypty, które należy wykonać i Wklej zawartość skryptu w edytorze tekstów. Zmień hasło *adminPassword* zmiennej, a następnie wklej skrypt powłokę chmury Azure. Uruchom skrypt dla systemu operacyjnego wybranego podczas tworzenia urządzenia wirtualnego sieci w kroku 6 [tworzenia tras i urządzenie wirtualne sieci](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Sprawdź poprawność komunikacji między maszynami wirtualnymi w podsieci publicznej i prywatnej. 

    - Otwórz [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) lub [pulpitu zdalnego](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) połączenia (system Windows) do publicznego adresu IP *myVm publicznego* maszyny wirtualnej.
    - W wierszu polecenia na *publicznego myVm* maszyny wirtualnej, wprowadź `ping myVm-Private`. Odpowiedzi jest wyświetlany, ponieważ analizę NVA kieruje ruchem i zapobieganie publicznemu dostępowi do podsieci prywatnego.
    - Z *myVm publicznego* maszyny wirtualnej, uruchom śledzenie trasy między maszynami wirtualnymi w podsieci publiczne i prywatne. Wprowadź odpowiednie polecenie poniżej, w zależności od systemu operacyjnego zainstalowanych na maszynach wirtualnych w podsieciach publicznej i prywatnej:
        - **Windows**: W wierszu polecenia Uruchom `tracert myvm-private` polecenia.
        - **Ubuntu**: Uruchom `tracepath myvm-private` polecenia.
      Ruch przechodzi przez 10.0.2.4 (NVA) przed osiągnięciem 10.0.1.4 (maszynę wirtualną w podsieci prywatne). 
    - Wykonać poprzednie kroki, łącząc się z *prywatnego myVm* maszyny wirtualnej i polecenie ping *myVm publicznego* maszyny wirtualnej. Śledzenie trasy zawiera komunikacji określanego 10.0.2.4 przed osiągnięciem 10.0.0.4 (maszynę wirtualną w publicznych podsieci).

      > [!NOTE]
      > Poprzednie kroki umożliwiają potwierdzenie routingu między Azure prywatnych adresów IP. Jeśli chcesz przekazać lub serwer proxy, ruch do publicznego adresu IP adresów przez urządzenie wirtualne sieci:
      > - Urządzenie musi zapewnić translatora adresów sieciowych lub możliwości serwera proxy. Jeśli translatora adresów sieciowych, urządzenie musi tłumaczenie źródła adresów IP, adres do jego własnej i przesyłania dalej tego żądania do publicznego adresu IP. Czy urządzenie ma adres sieciowy translacji adresu źródłowego lub używany jako serwer proxy, Azure tłumaczy urządzenie wirtualne sieci prywatnego adresu IP do publicznego adresu IP. Aby uzyskać więcej informacji o różnych metodach Azure używa do przekształcania prywatnych adresów IP na publiczne adresy IP, zobacz [opis połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - Dodatkowe trasy w tabeli tras, takie jak prefiks: wartość 0.0.0.0/0, typ następnego przeskoku VirtualAppliance i następnego przeskoku adresów IP 10.0.2.4 (w poprzednim przykładowy skrypt).
      >
    - **Opcjonalnie**: Aby zweryfikować następnego przeskoku między dwiema maszynami wirtualnymi w obrębie platformy Azure, użyj następnego przeskoku możliwości obserwatora sieciowego Azure. Przed użyciem obserwatora sieciowego, należy najpierw [utworzenia wystąpienia obserwatora sieciowego Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dla regionu, którego chcesz użyć w. W tym samouczku jest używany nam wschodnie regionu. Po aktywowaniu wystąpienia obserwatora sieciowego, dla regionu, wprowadź następujące polecenie, aby zobaczyć informacje o następnego przeskoku między maszynami wirtualnymi w podsieci publicznej i prywatnej:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Zwraca dane wyjściowe *10.0.2.4* jako **adres IP następnego przeskoku** i *VirtualAppliance* jako **Typ następnego przeskoku**. 

> [!NOTE]
> W celu zilustrowania koncepcji, w tym samouczku, publicznych adresów IP przypisanych do maszyn wirtualnych w publicznej i prywatnej podsieci i dostępu do portu całej sieci jest włączona w systemie Azure dla obu maszyn wirtualnych. Podczas tworzenia maszyn wirtualnych w środowisku produkcyjnym, publicznych adresów IP nie może przypisać do nich i mogą filtrować ruch sieciowy do podsieci prywatne, wdrażając urządzenie wirtualne sieci przed nim lub przypisywanie sieciowej grupy zabezpieczeń do podsieci, Interfejs sieciowy, lub obie. Aby dowiedzieć się więcej na temat grup zabezpieczeń sieci, zobacz [sieciowej grupy zabezpieczeń](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Ten samouczek wymaga istniejącej sieci wirtualnej z dwoma podsieciami. Kliknij przycisk **wypróbuj** przycisk w polu poniżej, aby szybko utworzyć sieć wirtualną. Kliknięcie przycisku **wypróbuj** przycisk otwiera [powłoki chmury Azure](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Chociaż powłoki chmury programu PowerShell lub powłoki Bash, jest uruchamiany w tej sekcji, powłoki Bash służy do tworzenia sieci wirtualnej. Powłoki Bash ma interfejsu wiersza polecenia platformy Azure zainstalowany. W przypadku wyświetlenia monitu przez powłokę chmury, zaloguj się do platformy Azure przy użyciu Twojej [konta Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Jeśli nie masz konta platformy Azure, możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/offers/ms-azr-0044p). Aby utworzyć sieć wirtualna używana w tym samouczku, kliknij przycisk **kopiowania** przycisku w polu poniżej, a następnie wkleić skrypt powłoki chmury Azure:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Aby dowiedzieć się więcej na temat sposobu korzystania z portalu, programu PowerShell lub szablonu usługi Azure Resource Manager można utworzyć sieci wirtualnej, zobacz [utworzyć sieć wirtualną](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Usuwanie zasobów

Po zakończeniu tego samouczka można usunąć zasoby, które zostały utworzone, tak aby nie wiąże się z opłatami użycia. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów, które znajdują się w grupie zasobów. Po otwarciu portalu wykonaj następujące czynności:

1. W polu wyszukiwania portalu wprowadź **myResourceGroup**. W wynikach wyszukiwania kliknij **myResourceGroup**.
2. Na **myResourceGroup** bloku, kliknij przycisk **usunąć** ikony.
3. Aby potwierdzić decyzję, w **typu nazwa grupy zasobów** wprowadź **myResourceGroup**, a następnie kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

- Utwórz [urządzenie wirtualne wysokiej dostępności sieci](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Urządzenie wirtualne sieci często mają wiele interfejsów sieciowych i adresów IP przypisanych do nich. Dowiedz się, jak [dodać interfejsów sieciowych do istniejącej maszyny wirtualnej](virtual-network-network-interface-vm.md#vm-add-nic) i [dodać adresy IP do istniejącego interfejsu sieciowego](virtual-network-network-interface-addresses.md#add-ip-addresses). Chociaż wszystkich rozmiarów maszyn wirtualnych może mieć co najmniej dwa interfejsy sieci dołączone, każdego rozmiaru maszyny wirtualnej obsługuje maksymalna liczba interfejsów sieciowych. Aby dowiedzieć się, jak wiele interfejsów sieciowych każdego wirtualnego komputera obsługuje rozmiaru, zobacz [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) i [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych. 
- Utworzenie trasy zdefiniowanej przez użytkownika do wymuszanie tunelowania ruchu lokalnej za pośrednictwem [połączenia sieci VPN lokacja lokacja](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
