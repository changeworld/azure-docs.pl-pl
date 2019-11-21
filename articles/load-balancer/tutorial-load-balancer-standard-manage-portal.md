---
title: 'Tutorial: Load balance internet traffic to VMs - Azure portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku pokazano, jak utworzyć wystąpienie usługi Load Balancer w warstwie Standardowa przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4d4703ccb4ee96eb69a780f91eae1eb6da9e1578
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225178"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Samouczek: równoważenie obciążenia ruchem internetowym przez skierowanie go do maszyn wirtualnych przy użyciu witryny Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. W tym samouczku poznasz poszczególne składniki usługi Load Balancer w warstwie Standardowa platformy Azure, które dystrybuują ruch internetowy do maszyn wirtualnych i zapewniają wysoką dostępność. Omawiane kwestie:


> [!div class="checklist"]
> * Create an Azure Load Balancer
> * Create Load Balancer resources
> * Tworzenie maszyn wirtualnych i instalowanie serwera usług IIS
> * View Load Balancer in action
> * Add and remove VMs from a Load Balancer

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Tworzenie usługi Load Balancer w warstwie Standardowa

In this section, you create a Standard Load Balancer that helps load balance virtual machines. Usługa Load Balancer w warstwie Standardowa obsługuje tylko publiczny adres IP w warstwie Standardowa. Podczas tworzenia usługi Load Balancer w warstwie Standardowa musisz także utworzyć nowy publiczny adres IP w warstwie Standardowa, który jest skonfigurowany jako fronton (domyślnie o nazwie *LoadBalancerFrontend*) dla usługi Load Balancer w warstwie Standardowa. 

1. W lewym górnym rogu ekranu kliknij pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. Na karcie **Podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przeglądanie + tworzenie**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Select **Create new** and type *myResourceGroupSLB* in the text box.|
    | Nazwa                   | *myLoadBalancer*                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | JSZ           | Select **Standard**.                          |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. |
    | Nazwa publicznego adresu IP              | Wpisz *myPublicIP* w polu tekstowym.   |
    |Strefa dostępności| Wybierz pozycję **Strefowo nadmiarowy**.    |

3. Na karcie **Przeglądanie + tworzenie** kliknij pozycję **Utwórz**.

   ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Create Load Balancer resources

In this section, you configure Load Balancer settings for a backend address pool, a health probe, and specify a balancer rule.

### <a name="create-a-backend-address-pool"></a>Tworzenie puli adresów zaplecza

To distribute traffic to the VMs, a backend address pool contains the IP addresses of the virtual (NICs) connected to the Load Balancer. Create the backend address pool *myBackendPool* to include virtual machines for load-balancing internet traffic.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie kliknij pozycję **Dodaj**.
3. On the **Add a backend pool** page, for name, type *myBackendPool*, as the name for your backend pool, and then select **Add**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

To allow the Load Balancer to monitor the status of your app, you use a health probe. The health probe dynamically adds or removes VMs from the Load Balancer rotation based on their response to health checks. Utwórz sondę kondycji *myHealthProbe*, aby monitorować kondycję maszyn wirtualnych.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. W obszarze **Ustawienia** kliknij pozycję **Sondy kondycji**, a następnie kliknij pozycję **Dodaj**.
3. Utwórz sondę kondycji, używając następujących wartości:
     
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Enter *myHealthProbe*. |
    | Protocol (Protokół) | Select **HTTP**. |
    | Port | Enter *80*.|
    | Interval | Enter *15* for number of **Interval** in seconds between probe attempts. |
    | Unhealthy threshold | Select *2* for number of **Unhealthy threshold** or consecutive probe failures that must occur before a VM is considered unhealthy.|
    
4. Kliknij przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. Create a Load Balancer rule *myLoadBalancerRuleWeb* for listening to port 80 in the frontend *FrontendLoadBalancer* and sending load-balanced network traffic to the backend address pool *myBackEndPool* also using port 80.

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły równoważenia obciążenia**, a następnie kliknij pozycję **Dodaj**.
3. Use these values to configure the load-balancing rule:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Enter *myHTTPRule*. |
    | Protocol (Protokół) | Select **TCP**. |
    | Port | Enter *80*.|
    | Backend port | Enter *80*. |
    | Backend pool | Select *myBackendPool*.|
    | Sonda kondycji | Select *myHealthProbe*. |
    
4. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

In this section, you create a virtual network, create three virtual machines for the backend pool of the Load Balancer, and then install IIS on the virtual machines to help test the Load Balancer.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź nazwę *myVNet*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Select existing resource - *myResourceGroupSLB*. |
    | Lokalizacja | Wybierz pozycję **Europa Zachodnia**.|
    | Podsieć — nazwa | Wprowadź nazwę podsieci *myBackendSubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    
3. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Standard Load Balancer only supports VMs with Standard IP addresses in the backend pool. In this section, you will create three VMs (*myVM1*, *myVM2*, and *myVM3*) with a Standard public IP address in three different zones (*Zone 1*, *Zone 2*, and *Zone 3*) that are added to the backend pool of the Standard Load Balancer that was created earlier.

1. W lewej górnej części portalu wybierz pozycję **Utwórz zasób** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. W obszarze **Tworzenie maszyny wirtualnej** wpisz lub wybierz następujące wartości na karcie **Podstawowe**:
   - **Subscription** > **Resource Group**: Select **myResourceGroupSLB**.
   - **Instance Details** > **Virtual machine name**: Type *myVM1*.
   - **Instance Details** > **Region** > select **West Europe**.
   - **Instance Details** > **Availability Options** > Select **Availability zones**. 
   - **Instance Details** > **Availability zone** > Select **1**.
  
1. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**. 
   
   - Upewnij się, że zostały wybrane następujące opcje:
       - **Virtual network**: **myVnet**
       - **Subnet**: **myBackendSubnet**
       - **Public IP** > select **Create new**, and in the **Create public IP address** window, for **SKU**, select **Standard**, and for **Availability zone**, select **Zone-redundant**
      
   - Aby utworzyć nową sieciową grupę zabezpieczeń (NSG, network security group), czyli pewien typ zapory, w obszarze **Sieciowa grupa zabezpieczeń** wybierz pozycję **Zaawansowane**. 
       1. W polu **Konfigurowanie sieciowej grupy zabezpieczeń** wybierz pozycję **Utwórz nową**. 
       1. Type *myNetworkSecurityGroup*, and select **OK**.

   - To make the VM a part of the Load Balancer's backend pool, complete the following steps:
        - In **Load Balancing**, for **Place this virtual machine behind an existing load balancing solution?** , select **Yes**.
        - In **Load balancing settings**, for **Load balancing options**, select **Azure load balancer**.
        - For **Select a load balancer**, *myLoadBalancer*. 
1. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**. W obszarze **Monitorowanie** dla opcji **Diagnostyka rozruchu** ustaw wartość **Wyłączone**. 
1. Wybierz pozycję **Przegląd + utwórz**.   
1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.
1. Follow the steps to create two additional VMs - *myVM2* and *myVM3*, with a Standard SKU public IP address in **Availability zone** **2** and **3** respectively, and all the other settings the same as *myVM1*.  

### <a name="create-network-security-group-rule"></a>Tworzenie reguły sieciowej grupy zabezpieczeń

In this section, you create a network security group rule to allow inbound connections using HTTP.

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list click **myNetworkSecurityGroup** that is located in the **myResourceGroupSLB** resource group.
2. W obszarze **Ustawienia** kliknij pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie kliknij przycisk **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie *myHTTPRule*, aby umożliwić obsługę przychodzących połączeń HTTP przy użyciu portu 80:
    - *Tag usługi* — w polu **Źródło**.
    - *Internet* — w polu **Tag usługi źródłowej**
    - *80* — w polu **Docelowe zakresy portów**
    - *TCP* — w polu **Protokół**
    - *Zezwalaj* — w polu **Akcja**
    - *100* — w polu **Priorytet**
    - *myHTTPRule* — jako nazwę
    - *Zezwalaj na HTTP* —jako opis
4. Wybierz pozycję **Dodaj**.

### <a name="install-iis-on-vms"></a>Instalowanie usług IIS na maszynach wirtualnych

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list click **myVM1** that is located in the *myResourceGroupSLB* resource group.
2. Na stronie **Przegląd** kliknij pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. W wyskakującym okienku **Podłączanie do maszyny wirtualnej** wybierz pozycję **Pobierz plik RDP**, a następnie otwórz pobrany plik RDP.
4. W oknie **Podłączanie pulpitu zdalnego** kliknij pozycję **Połącz**.
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń podanych podczas jej tworzenia. Spowoduje to uruchomienie sesji pulpitu zdalnego z maszyną wirtualną — *myVM1*.
6. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows**>**Windows PowerShell**.
7. W oknie programu PowerShell uruchom poniższe polecenia, aby zainstalować serwer usług IIS, usunąć domyślny plik iisstart.htm i dodać nowy plik iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Zamknij sesję protokołu RDP na maszynie wirtualnej *myVM1*.
7. Powtórz kroki od 1 do 6, aby zainstalować usługi IIS i zaktualizowany plik iisstart.htm na maszynach *myVM2* i *myVM3*.

## <a name="test-the-load-balancer"></a>Test the Load Balancer
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Select **All services** in the left-hand menu, select **All resources**, and then click **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

      ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

To see the Load Balancer distribute traffic across the three VMs running your app, you can force-refresh your web browser.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Usuwanie lub dodawanie maszyn wirtualnych w puli zaplecza
Maszyny wirtualne, na których działa aplikacja, mogą wymagać przeprowadzenia konserwacji, na przykład zainstalowania aktualizacji systemu operacyjnego. Zwiększony ruch do Twojej aplikacji może wiązać się z koniecznością dodania większej liczby maszyn wirtualnych. This section shows you how to remove or add a VM (*myVM1*) from the Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Remove VM from a backend pool
To remove *myVM1* from the backend pool, complete the following steps:

1. Select **All services** in the left-hand menu, select **All resources**, and then click **myLoadBalancer** from the resources list.
2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**, a następnie na liście pul zaplecza kliknij pozycję **myBackendPool**.
3. On the **myBackendPool** page, to remove *VM1* select the delete icon at the end of the row that displays *myVM1*, and then click **Save**.

Po usunięciu maszyny *myVM1* z puli adresów zaplecza możesz wykonywać wszelkie zadania konserwacyjne na maszynie *myVM1*, takie jak instalowanie aktualizacji oprogramowania. In the absence of *VM1*, the load is now balanced across *myVM2* and *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Add VM to a backend pool
To add *myVM1* back to the backend pool, complete the following steps:

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myVM1** from the resources list.
2. In the **VM1** page, under **Settings**, select **Networking**.
3. In the **Networking** page, select the **Load balancing** tab, and then select **Add load balancing**.
4. In the **Add load balancing** page, do the following:
   1. For **Load balancing options**, select **Azure load balancer**.
   2. For **Select a load balancer**, select *myLoadBalancer*.
   3. For **Select a backend pool**, select *myBackendPool*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

When they are no longer needed, delete the resource group, Load Balancer, and all related resources. To do so, select the *myResouceGroupSLB* resource group that contains the Load Balancer, and then select  **Delete**.

## <a name="next-steps"></a>Następne kroki

In this tutorial, you created a Standard Load Balancer, attached VMs to it, configured the Load Balancer traffic rule, health probe, and then tested the Load Balancer. Ponadto usunięto maszynę wirtualną z zestawu o zrównoważonym obciążeniu i dodano ją z powrotem do puli adresów zaplecza. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
