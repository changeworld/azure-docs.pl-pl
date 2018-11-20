---
title: 'Szybki start: tworzenie publicznego podstawowego modułu równoważenia obciążenia przy użyciu witryny Azure Portal | Microsoft Docs'
description: W tym samouczku przedstawiono sposób tworzenia publicznego podstawowego modułu równoważenia obciążenia przy użyciu witryny Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 523f5eba632b15eaaf45f24be820f7b255aae7c0
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616031"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Szybki start: tworzenie publicznego podstawowego modułu równoważenia obciążenia przy użyciu witryny Azure Portal

Równoważenie obciążenia zapewnia większą dostępność i możliwości skalowania dzięki rozdzielaniu żądań przychodzących między wiele maszyn wirtualnych. Za pomocą witryny Azure Portal można utworzyć moduł równoważenia obciążenia, który będzie równoważyć obciążenie maszyn wirtualnych. W tym przewodniku Szybki start pokazano, jak utworzyć zasoby sieciowe, serwery zaplecza oraz moduł równoważenia obciążenia w warstwie cenowej Podstawowa.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W przypadku wszystkich zadań z tego przewodnika Szybki start musisz zalogować się do witryny [Azure Portal](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Tworzenie podstawowego modułu równoważenia obciążenia

W tej sekcji utworzysz publiczny podstawowy moduł równoważenia obciążenia przy użyciu portalu. Publiczny adres IP jest automatycznie konfigurowany jako fronton modułu równoważenia obciążenia w przypadku tworzenia zasobu publicznego adresu IP i modułu równoważenia obciążenia za pomocą portalu. Nazwa frontonu to **myLoadBalancer**.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób** > **Sieć** > **Moduł równoważenia obciążenia**.
2. W okienku **Tworzenie modułu równoważenia obciążenia** wprowadź następujące wartości:
   - **myLoadBalancer** — jako nazwę modułu równoważenia obciążenia
   - **Public** — jako typ modułu równoważenia obciążenia 
   - **myPublicIP** — jako publiczny adres IP do utworzenia z jednostką **SKU** ustawioną na **Podstawowa** i pozycją **Przypisanie** ustawioną na **Dynamiczne**
   - **myResourceGroupLB** — jako nazwę nowej grupy zasobów
3. Wybierz pozycję **Utwórz**.
   
![Tworzenie modułu równoważenia obciążenia](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji utworzysz sieć wirtualną, a następnie utworzysz dwie maszyny wirtualne w puli zaplecza podstawowego modułu równoważenia obciążenia. Następnie zainstalujesz usługi Internet Information Services (IIS) na maszynach wirtualnych, aby ułatwić testowanie modułu równoważenia obciążenia.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej
1. W lewej górnej części portalu wybierz pozycję **Nowy** > **Sieć** > **Sieć wirtualna**.
2. W okienku **Tworzenie sieci wirtualnej** wprowadź poniższe wartości, a następnie wybierz pozycję **Utwórz**:
   - **myVnet** — jako nazwę sieci wirtualnej
   - **myResourceGroupLB** — jako nazwę istniejącej grupy zasobów
   - **myBackendSubnet** — jako nazwę podsieci

   ![Tworzenie sieci wirtualnej](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

1. W lewej górnej części portalu wybierz pozycje **Nowy** > **Compute** > **Windows Server 2016 Datacenter**. 
2. Wprowadź poniższe wartości dla maszyny wirtualnej, a następnie wybierz przycisk **OK**:
   - **myVM1** — jako nazwę maszyny wirtualnej.        
   - **azureuser** — jako nazwę użytkownika administratora.    
   - **myResourceGroupLB** — jako grupę zasobów. (W obszarze **Grupa zasobów** wybierz opcję **Użyj istniejącej**, a następnie wybierz wartość **myResourceGroupLB**).   
3. Wybierz **DS1_V2** jako rozmiar maszyny wirtualnej, a następnie kliknij pozycję **Wybierz**.
4. Wprowadź następujące wartości ustawień maszyny wirtualnej:
   - **myAvailabilitySet** — jako nazwę nowego zestawu dostępności, który tworzysz.
   - **myVnet** — jako nazwę sieci wirtualnej. (Upewnij się, że wybrano tę opcję).
   - **myBackendSubnet** — jako nazwę podsieci. (Upewnij się, że wybrano tę opcję).
   - **myVM1-ip** — jako publiczny adres IP.
   - **myNetworkSecurityGroup** — jako nazwę nowej sieciowej grupy zabezpieczeń (zapory), którą musisz utworzyć.
5. Kliknij pozycję **Wyłączone**, aby wyłączyć diagnostykę rozruchu.
6. Kliknij przycisk **OK**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.
7. Wykonując kroki 1–6, utwórz drugą maszynę wirtualną o nazwie **VM2** z następującymi ustawieniami:
   - **myAvailabilityset** jako zestawem dostępności.
   - **myVnet** jako siecią wirtualną.
   - **myBackendSubnet** jako podsiecią.
   - **myNetworkSecurityGroup** jako sieciową grupą zabezpieczeń. 

### <a name="create-nsg-rules"></a>Tworzenie reguł sieciowej grupy zabezpieczeń

W tej sekcji utworzysz reguły sieciowej grupy zabezpieczeń, aby zezwolić na połączenia przychodzące używające protokołów HTTP i RDP.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Na liście zasobów wybierz pozycję **myNetworkSecurityGroup** w grupie zasobów **myResourceGroupLB**.
2. W obszarze **Ustawienia** wybierz pozycję **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz pozycję **Dodaj**.
3. Wprowadź następujące wartości dla reguły zabezpieczeń dla ruchu przychodzącego o nazwie **myHTTPRule**, aby umożliwić obsługę przychodzących połączeń protokołu HTTP używających portu 80. Następnie wybierz przycisk **OK**.
   - **Tag usługi** — w polu **Źródło**
   - **Internet** — w polu **Tag usługi źródłowej**
   - **80** — w polu **Docelowe zakresy portów**
   - **TCP** — w polu **Protokół**
   - **Zezwalaj** — w polu **Akcja**
   - **100** — w polu **Priorytet**
   - **myHTTPRule** — w polu **Nazwa**
   - **Zezwalaj na HTTP** — w polu **Opis**
 
   ![Tworzenie reguły sieciowej grupy zabezpieczeń](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Powtórz kroki od 2 do 3, aby utworzyć inną regułę o nazwie **myRDPRule** zezwalającą na przychodzące połączenia protokołu RDP za pośrednictwem portu 3389. Wprowadź następujące wartości:
   - **Tag usługi** — w polu **Źródło**
   - **Internet** — w polu **Tag usługi źródłowej**
   - **3389** — w polu **Docelowe zakresy portów**
   - **TCP** — w polu **Protokół**
   - **Zezwalaj** — w polu **Akcja**
   - **200** — w polu **Priorytet**
   - **myRDPRule** — w polu **Nazwa**
   - **Zezwalaj na RDP** — w polu **Opis**

   

### <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. Z listy zasobów wybierz pozycję **myVM1** w grupie zasobów **myResourceGroupLB**.
2. Na stronie **Przegląd** wybierz pozycję **Połącz** dla protokołu RDP z maszyną wirtualną.
3. Zaloguj się do maszyny wirtualnej przy użyciu nazwy użytkownika **azureuser** i hasła **Azure123456!**.
4. Na pulpicie serwera przejdź do pozycji **Narzędzia administracyjne systemu Windows** > **Menedżer serwera**.
5. W Menedżerze serwera wybierz pozycję **Zarządzaj**, a następnie wybierz pozycję **Dodaj role i funkcje**.
   ![Dodawanie roli Menedżera serwera](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. W Kreatorze dodawania ról i funkcji użyj następujących wartości:
   - Na stronie **Wybieranie typu instalacji** wybierz pozycję **Instalacja oparta na rolach lub funkcjach**.
   - Na stronie **Wybieranie serwera docelowego** wybierz pozycję **myVM1**.
   - Na stronie **Wybieranie roli serwera** wybierz pozycję **Serwer internetowy (IIS)**.
   - Postępuj zgodnie z instrukcjami, aby ukończyć pozostałą część kreatora. 
7. Powtórz kroki od 1 do 6 dla maszyny wirtualnej **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Tworzenie zasobów na potrzeby podstawowego modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji. Określisz również reguły modułu równoważenia obciążenia i reguły translatora adresów sieciowych.


### <a name="create-a-back-end-address-pool"></a>Tworzenie puli adresów zaplecza

Na potrzeby rozdzielania ruchu między maszyny wirtualne używana jest pula adresów zaplecza, zawierająca adresy IP wirtualnych kart sieciowych połączonych z modułem równoważenia obciążenia. Utwórz pulę adresów zaplecza **myBackendPool** obejmującą maszyny wirtualne **VM1** i **VM2**.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wykonaj poniższe czynności, a następnie wybierz przycisk **OK**:
   - W polu **Nazwa** wprowadź ciąg **myBackEndPool**.
   - W polu **Skojarzone z** z menu rozwijanego wybierz pozycję **Zestaw dostępności**.
   - W polu **Zestaw dostępności** wybierz pozycję **myAvailabilitySet**.
   - Wybierz pozycję **Dodaj docelową konfigurację adresu IP sieci**, aby dodać każdą utworzoną maszynę wirtualną (**myVM1** i **myVM2**) do puli zaplecza.

   ![Dodawanie do puli adresów zaplecza](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Upewnij się, że ustawienie puli zaplecza modułu równoważenia obciążenia wyświetla obie maszyny wirtualne — **VM1** i **VM2**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Aby zezwolić podstawowemu modułowi równoważenia obciążenia na monitorowanie stanu aplikacji, należy użyć sondy kondycji. Dynamicznie dodaje lub usuwa maszyny wirtualne w rotacji modułu równoważenia obciążenia na podstawie ich odpowiedzi na kontrole kondycji. Utwórz sondę kondycji o nazwie **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
3. Użyj tych wartości, a następnie wybierz przycisk **OK**:
   - **myHealthProbe** — jako nazwy sondy kondycji
   - **HTTP** — jako typu protokołu
   - **80** — jako numeru portu
   - **Healthprobe.aspx** jako ścieżki identyfikatora URI. Możesz zastąpić tę wartość dowolnym innym identyfikatorem URI lub zachować domyślną wartość ścieżki **"\\"**, aby uzyskać domyślny identyfikator URI.
   - **15** — w polu **Interwał** jako liczby sekund między próbami sondy
   - **2** — w polu **Próg złej kondycji** jako liczby kolejnych niepowodzeń sondy, które muszą wystąpić, aby maszyna wirtualna została uznana za będącą w złej kondycji

   ![Dodawanie sondy](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia jest używana do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Zdefiniuj konfigurację adresu IP frontonu na potrzeby ruchu przychodzącego oraz pulę adresów IP zaplecza do odbierania ruchu, wraz z wymaganym portem źródłowym i docelowym. 

Utwórz regułę modułu równoważenia obciążenia o nazwie **myLoadBalancerRuleWeb** do nasłuchiwania na porcie 80 frontonu **LoadBalancerFrontEnd**. Reguła umożliwia też wysyłanie ruchu sieciowego ze zrównoważonym obciążeniem do puli adresów zaplecza **myBackEndPool**, również przy użyciu portu 80. 

1. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myLoadBalancer** na liście zasobów.
2. W obszarze **Ustawienia** wybierz pozycję **Reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
3. Użyj tych wartości, a następnie wybierz przycisk **OK**:
   - **myHTTPRule** — jako nazwy reguły równoważenia obciążenia
   - **TCP** — jako typu protokołu
   - **80** — jako numeru portu
   - **80** —jako portu zaplecza
   - **myBackendPool** — jako nazwy puli zaplecza
   - **myHealthProbe** — jako nazwy sondy kondycji
    
   ![Dodawanie reguły modułu równoważenia obciążenia](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia
1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz pozycję **Wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Grupę zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby można usunąć, gdy nie będą już potrzebne. Wybierz grupę zasobów zawierającą moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do utworzenia podstawowego modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat usługi Azure Load Balancer, przejdź do samouczków dotyczących usługi Azure Load Balancer.

> [!div class="nextstepaction"]
> [Samouczki usługi Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
