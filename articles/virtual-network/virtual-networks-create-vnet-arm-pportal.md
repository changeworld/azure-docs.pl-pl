---
title: Tworzenie sieci wirtualnej platformy Azure z wieloma podsieciami — Portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć sieć wirtualną z wieloma podsieciami przy użyciu portalu Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 898bdef779282d7312c76696f744b97ec2dfcded
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880556"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-the-azure-portal"></a>Tworzenie sieci wirtualnej z wieloma podsieciami przy użyciu portalu Azure

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Tworzenie wielu podsieci w sieci wirtualnej umożliwia do segmentu sieci, dzięki czemu można filtrować lub sterowania przepływem ruchu między podsieciami. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie podsieci
> * Test łączności sieciowej między maszynami wirtualnymi

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się w witrynie Azure Portal pod adresem http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz **+ Utwórz zasób** w górnym lewym rogu portalu Azure.
2. Wybierz **sieci**, a następnie wybierz **sieci wirtualnej**.
3. Jak pokazano na poniższej ilustracji, wprowadź *myVirtualNetwork* dla **nazwa**, *10.0.0.0/16* dla **przestrzeni adresów**,  **myResourceGroup** dla **grupy zasobów**, *publicznego* dla podsieci **nazwa**, 10.0.0.0/24 dla podsieci **zakresadresów**, wybierz pozycję **lokalizacji** i **subskrypcji**Zaakceptuj pozostałe wartości domyślne, a następnie wybierz **Utwórz**:

    ![Tworzenie sieci wirtualnej](./media/virtual-networks-create-vnet-arm-pportal/create-virtual-network.png)

    **Przestrzeni adresów** i **zakres adresów** są określone w notacji CIDR. Określony **przestrzeni adresów** obejmuje 10.0.0.0-10.0.255.254 adresów IP. **Zakres adresów** Określona podsieć, musi znajdować się w **przestrzeni adresów** zdefiniowana dla sieci wirtualnej. Azure DHCP przypisuje adresy IP z zakresu adresów podsieci z zasobami wdrożonymi w podsieci. Azure przypisuje 10.0.0.4-10.0.0.254 adresy tylko z zasobami wdrożonymi w **publicznego** podsieci, ponieważ Azure rezerwuje pierwsze cztery adresów (10.0.0.0-10.0.0.3 podsieci, w tym przykładzie) i ostatnich adresów () 10.0.0.255 podsieci, w tym przykładzie) w każdej podsieci.

## <a name="create-a-subnet"></a>Tworzenie podsieci

1. W **wyszukiwania zasobów, usługi i dokumenty** w górnej części portalu pozycję Rozpocznij wpisywanie *myVirtualNetwork*. Gdy **myVirtualNetwork** pojawia się w wynikach wyszukiwania, wybierz go.
2. Wybierz **podsieci** , a następnie wybierz **+ podsieci**, jak pokazano na poniższej ilustracji:

     ![Dodaj podsieć](./media/virtual-networks-create-vnet-arm-pportal/add-subnet.png)
     
3. W **Dodaj podsieć** wyświetlonym, wprowadź *prywatnej* dla **nazwa**, wprowadź *10.0.1.0/24* dla **zakresadresów**, a następnie wybierz **OK**.  Zakres adresów podsieci nie może nakładać się na zakresy adresów innych podsieci w sieci wirtualnej. 

Przed wdrożeniem sieci wirtualnych platformy Azure i podsieci w środowisku produkcyjnym, zalecane jest, że należy dokładnie zapoznać się z przestrzeni adresowej [zagadnienia](manage-virtual-network.md#create-a-virtual-network) i [limity sieci wirtualnej](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Gdy zasoby są wdrażane na podsieci, niektóre sieci wirtualnej i zmiany podsieci, takie jak zmiana zakresów adresów, może wymagać ponownego wdrażania istniejących zasobów platformy Azure, wdrażana w obrębie podsieci.

## <a name="test-network-communication"></a>Test łączności sieciowej

Sieć wirtualna umożliwia kilka typów zasobów platformy Azure do komunikowania się z Internetem i prywatnie ze sobą. Jeden typ zasobów, które można wdrożyć w sieci wirtualnej jest maszyną wirtualną. Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby można było sprawdzić komunikację sieciową między nimi i Internetu w kolejnym kroku.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. Wybierz **+ Utwórz zasób** w górnym lewym rogu portalu Azure.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. Można wybrać innego systemu operacyjnego, ale pozostałe kroki zakłada się wybrano **systemu Windows Server Datacenter 2016**. 
3. Wybierz lub wprowadź następujące informacje dotyczące **podstawy**, a następnie wybierz pozycję **OK**:
    - **Nazwa**: *myVmWeb*
    - **Grupa zasobów**: Wybierz **Użyj istniejącego** , a następnie wybierz *myResourceGroup*.
    - **Lokalizacja**: Wybierz *wschodnie stany USA*.

    **Nazwy użytkownika** i **hasło** wprowadzeniu są używane w kolejnym kroku. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). **Lokalizacji** i **subskrypcji** wybrane musi być taka sama jak lokalizacja i sieć wirtualna jest w subskrypcji. Wybierz opcję tej samej grupy zasobów w sieci wirtualnej została utworzona, ale tej samej grupy zasobów jest zaznaczona w tym samouczku nie jest wymagany.
4. Wybierz rozmiar maszyny Wirtualnej, w obszarze **wybierz rozmiar**.
5. Wybierz lub wprowadź następujące informacje dotyczące **ustawienia**, a następnie wybierz pozycję **OK**:
    - **Sieć wirtualna**: Upewnij się, że **myVirtualNetwork** jest zaznaczone. Jeśli nie, wybierz **sieci wirtualnej** , a następnie wybierz **myVirtualNetwork** w obszarze **sieci wirtualnej wybierz**.
    - **Podsieci**: Upewnij się, że **publicznego** jest zaznaczone. Jeśli nie, wybierz **podsieci** , a następnie wybierz **publicznego** w obszarze **Wybierz podsieć**, jak pokazano na poniższej ilustracji:
    
        ![Ustawienia maszyny wirtualnej](./media/virtual-networks-create-vnet-arm-pportal/virtual-machine-settings.png)
 
6. W obszarze **Utwórz** w **Podsumowanie**, wybierz pozycję **Utwórz** rozpocząć wdrażanie maszyny wirtualnej.
7. Ponownie wykonaj kroki 1 – 6, ale wprowadzić *myVmMgmt* dla **nazwa** maszyny wirtualnej i wybierz **prywatnej** dla **podsieci**.

Maszyny wirtualne potrwać kilka minut, aby utworzyć. Nie Kontynuuj pozostałe kroki aż do utworzenia zarówno maszyn wirtualnych.

Maszyny wirtualne utworzone w tym artykule istnieje [interfejs sieciowy](virtual-network-network-interface.md) z jednym adresem IP, który jest przypisywany dynamicznie do interfejsu sieciowego. Po wdrożeniu maszyny Wirtualnej, można [dodać wiele prywatnych i publicznych adresów IP lub zmień metoda przypisywania adresów IP statycznej](virtual-network-network-interface-addresses.md#add-ip-addresses). Możesz [dodać interfejsów sieciowych](virtual-network-network-interface-vm.md#vm-add-nic), w granicach obsługiwane przez [rozmiar maszyny Wirtualnej](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) wybranym podczas tworzenia maszyny wirtualnej. Możesz również [Włącz wirtualizację we/wy pojedynczego elementu głównego (SR-IOV)](create-vm-accelerated-networking-powershell.md) dla maszyny Wirtualnej, ale tylko wtedy, gdy tworzenie maszyny Wirtualnej z rozmiar maszyny Wirtualnej, która obsługuje możliwości.

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>Komunikację między maszynami wirtualnymi i z Internetu

1. W *wyszukiwania* w górnej części portalu pozycję Rozpocznij wpisywanie *myVmMgmt*. Gdy **myVmMgmt** pojawia się w wynikach wyszukiwania, wybierz go.
2. Tworzenie połączenia pulpitu zdalnego z *myVmMgmt* maszynę wirtualną, wybierając **Connect**, jak pokazano na poniższej ilustracji:

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/virtual-networks-create-vnet-arm-pportal/connect-to-virtual-machine.png)  

3. Aby nawiązać połączenie z maszyną Wirtualną, Otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz **Connect**.
4. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (musisz wybrać **więcej opcji**, następnie **korzystała z innego konta**, aby określić podczas obliczania wprowadzone poświadczenia możesz utworzono maszynę wirtualną), następnie wybierz **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **tak** Aby nawiązać połączenie.
6. W kolejnym kroku ping jest używany do komunikacji z *myVmMgmt* maszynę wirtualną z *myVmWeb* maszyny wirtualnej. Polecenie ping używa protokołu ICMP, którego odmówiono przez zaporę systemu Windows, domyślnie. Włącz ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w wierszu polecenia:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Chociaż ping są używane w tym artykule, nie zaleca się stosowanie protokołu ICMP przez zaporę systemu Windows dla wdrożeń produkcyjnych.
7. Ze względów bezpieczeństwa jest często, aby ograniczyć liczbę maszyn wirtualnych, które można zdalnie łączyć się w sieci wirtualnej. W tym samouczku *myVmMgmt* maszyny wirtualnej jest używany do zarządzania *myVmWeb* maszyny wirtualnej w sieci wirtualnej. Do usług pulpitu zdalnego do *myVmWeb* maszynę wirtualną z *myVmMgmt* maszyny wirtualnej, wprowadź następujące polecenie w wierszu polecenia:

    ``` 
    mstsc /v:myVmWeb
    ```
8. Do komunikacji *myVmMgmt* maszynę wirtualną z *myVmWeb* maszyny wirtualnej, wprowadź następujące polecenie w wierszu polecenia:

    ```
    ping myvmmgmt
    ```

    Pojawi się dane wyjściowe podobne do następujących przykładowe dane wyjściowe:
    
    ```
    Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
    Ping statistics for 10.0.1.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    ```
      
    Można stwierdzić, że adres *myVmMgmt* 10.0.1.4 jest maszyny wirtualnej. 10.0.1.4 był pierwszy dostępny adres IP z zakresu adresów *prywatnej* podsieci, która została wdrożona *myVmMgmt* maszynę wirtualną w poprzednim kroku.  Zobaczysz, że w pełni kwalifikowaną nazwę maszyny wirtualnej jest *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*. Chociaż *dar5p44cif3ulfq00wxznl3i3f* część nazwy domeny różni się dla maszyny wirtualnej, pozostałej części nazwy domeny są takie same. 

    Domyślnie wszystkie maszyny wirtualne Azure używają domyślna usługa Azure DNS. Wszystkie maszyny wirtualne sieci wirtualnej można rozpoznawania nazw innych maszyn wirtualnych w tej samej sieci wirtualnej przy użyciu usługi DNS domyślne platformy Azure. Zamiast przy użyciu usługi DNS domyślne platformy Azure, możesz użyć serwera DNS lub możliwości prywatnej domeny usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) lub [przy użyciu usługi Azure DNS dla domen prywatnej](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

9. Aby zainstalować usługi Internet Information Services (IIS) dla systemu Windows Server na *myVmWeb* maszyny wirtualnej, wprowadź następujące polecenie w sesji programu PowerShell:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

10. Po ukończeniu instalacji usług IIS, odłącz *myVmWeb* sesji usług pulpitu zdalnego, co spowoduje pozostawienie w *myVmMgmt* sesji usług pulpitu zdalnego. Otwórz przeglądarkę sieci web i przejdź do http://myvmweb. Strona powitalna usług IIS zostanie wyświetlony.
11. Odłącz *myVmMgmt* sesji usług pulpitu zdalnego.
12. Znajdź publicznego adresu IP *myVmWeb* maszyny wirtualnej. Podczas tworzenia Azure *myVmWeb* maszynę wirtualną, publiczny zasób adres IP o nazwie *myVmWeb* została także utworzona i przypisana do maszyny wirtualnej. Widać, że 52.170.5.92 została przypisana do **publicznego adresu IP** do *myVmMgmt* maszyny wirtualnej na obrazie w kroku 2. Aby znaleźć publiczny adres IP przypisany do *myVmWeb* maszyny wirtualnej, wyszukiwanie *myVmWeb* w polu wyszukiwania portalu, wybierz go w wynikach wyszukiwania.

    Jeśli maszyna wirtualna nie musi mieć publiczny adres IP przypisane do niej, Azure przypisuje publicznego adresu IP do każdej maszyny wirtualnej, które tworzysz, domyślnie. Aby komunikować się z Internetu na maszynę wirtualną, publiczny adres IP musi można przypisać do maszyny wirtualnej. Wszystkie maszyny wirtualne mogą komunikować się wychodzące z Internetem, czy przypisano publiczny adres IP do maszyny wirtualnej. Aby dowiedzieć się więcej na temat połączenia wychodzące Internet na platformie Azure, zobacz [połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
13. Na komputerze użytkownika, przejdź do publicznego adresu IP *myVmWeb* maszyny wirtualnej. Próba wyświetlenia strona powitalna usług IIS z tego komputera nie powiedzie się. Próba nie powiedzie się, ponieważ gdy maszyny wirtualne zostały wdrożone, Azure utworzyć grupę zabezpieczeń sieci dla każdej maszyny wirtualnej domyślnie. 

     Grupa zabezpieczeń sieci zawiera zasady zabezpieczeń, które dozwolonych lub zablokowanych dla ruchu przychodzącego i wychodzącego ruchu sieciowego przez port i adres IP. Domyślne grupy zabezpieczeń sieci utworzone Azure umożliwia komunikację za pośrednictwem wszystkie porty między zasobami w tej samej sieci wirtualnej. Dla maszyn wirtualnych systemu Windows domyślną grupę zabezpieczeń sieci nie zezwala na cały ruch przychodzący z Internetu przez wszystkie porty, z wyjątkiem portu TCP 3389 (RDP). W związku z tym domyślnie można również RDP bezpośrednio do *myVmWeb* maszyny wirtualnej z Internetu, nawet jeśli nie możesz portu 3389 Otwórz na serwerze sieci web. Ponieważ przeglądanie sieci web komunikuje się za pośrednictwem portu 80, komunikacji nie z Internetu, ponieważ nie istnieje żadna reguła w domyślnej grupie zabezpieczeń sieci zezwala na ruch przez port 80.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupy zasobów i wszystkie zasoby, które zawiera: 

1. Wprowadź *myResourceGroup* w **wyszukiwania** pole w górnej części portalu. Po wyświetleniu **myResourceGroup** w wynikach wyszukiwania wybierz go.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź *myResourceGroup* dla **typu nazwa grupy zasobów:** i wybierz **usunąć**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wdrażania sieci wirtualnej z wieloma podsieciami. Również wiesz, że podczas tworzenia maszyny wirtualnej systemu Windows Azure tworzy interfejs sieciowy on dołączony do maszyny wirtualnej i tworzy sieciową grupę zabezpieczeń, który tylko zezwala na ruch przez port 3389, z Internetu. Przejście do dalej samouczkiem, aby dowiedzieć się, jak do filtrowania ruchu sieciowego do podsieci, a nie do poszczególnych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego do podsieci](./virtual-networks-create-nsg-arm-pportal.md)
