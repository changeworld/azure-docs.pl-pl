---
title: Tworzenie połączenia sieci VPN typu lokacja lokacja między dwiema sieciami wirtualnymi w różnych środowiskach Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Procedura krok po kroku, który administrator chmury nie używa w celu utworzenia połączenia VPN lokacja lokacja między dwa środowiska Azure Stack Development Kit w pojedynczym węzłem.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 432231d7b571cfda9ec4d71d24aa8bd05346c6b2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803752"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Tworzenie połączenia sieci VPN typu lokacja lokacja między dwiema sieciami wirtualnymi w różnych środowiskach Azure Stack Development Kit

## <a name="overview"></a>Przegląd

W tym artykule opisano sposób tworzenia połączenia sieci VPN typu lokacja lokacja między dwiema sieciami wirtualnymi w dwóch oddzielnych środowiskach Azure Stack Development Kit (ASDK). Podczas konfigurowania połączeń dowiesz się, jak działają bramy sieci VPN w usłudze Azure Stack.

### <a name="connection"></a>Połączenie

Na poniższej ilustracji przedstawiono konfigurację połączenia jak powinien wyglądać po wykonaniu tych czynności.

![Konfiguracja połączenia sieci VPN typu lokacja lokacja](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć konfigurację połączenia, upewnij się, że masz następujące elementy, przed rozpoczęciem:

* Dwa serwery i inne wymagania wstępne, które spełniają wymagania sprzętowe ASDK, zgodnie z opisem w [Szybki Start: Ocena usługi Azure Stack Development Kit](azure-stack-deploy-overview.md).
* [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) pakietu wdrożeniowego.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Wdrażanie środowisk Azure Stack Development Kit

Aby ukończyć konfigurację połączenia, należy wdrożyć dwóch środowisk ASDK.

> [!NOTE]
> Dla każdego ASDK, który można wdrożyć, postępuj zgodnie z [instrukcje dotyczące wdrażania](azure-stack-run-powershell-script.md). W tym artykule są wywoływane w środowiskach ASDK **POC1** i **POC2**.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Przygotowanie ofertę w środowisku POC1 i środowisku POC2

W środowisku POC1 i POC2 przygotować ofertę, aby użytkownik mógł subskrybowaniu oferty i wdrażanie maszyn wirtualnych. Aby uzyskać informacje o tym, jak utworzyć ofertę, zobacz [udostępnić maszyn wirtualnych dla użytkowników usługi Azure Stack](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Przejrzyj i ukończ tabeli konfiguracji sieci.

W poniższej tabeli podsumowano w obu środowiskach ASDK konfiguracji sieci. Użyj procedury, która pojawia się pod tabelą, aby dodać adres BGPNAT zewnętrznych, które są specyficzne dla sieci.

### <a name="network-configuration-table"></a>Tabela konfiguracji sieci

|   |POC1|POC2|
|---------|---------|---------|
|Nazwa sieci wirtualnej     |VNET-01|VNET-02 |
|Przestrzeń adresowa sieci wirtualnej |10.0.10.0/23|10.0.20.0/23|
|Nazwa podsieci     |Subnet-01|Subnet-02|
|Zakres adresów podsieci|10.0.10.0/24 |10.0.20.0/24 |
|Podsieć bramy     |10.0.11.0/24|10.0.21.0/24|
|Zewnętrzny adres BGPNAT     |         |         |

> [!NOTE]
> Zewnętrzne adresy BGPNAT IP w środowisku przykładowym są 10.16.167.195 dla środowiska POC1 i 10.16.169.131 dla środowiska POC2. Użyj poniższej procedury, aby określić zewnętrzne adresy BGPNAT IP ASDK hostów, a następnie dodać je do poprzedniej tabeli konfiguracji sieci.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Uzyskiwanie adresu IP zewnętrznej karty sieciowej maszyny wirtualnej translatora adresów sieciowych

1. Zaloguj się do maszyny fizycznej usługi Azure Stack dla środowiska POC1.
2. Edytuj następujący kod programu PowerShell w celu zastąpienia hasła administratora, a następnie uruchom kod, na hoście POC:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. Dodaj adres IP do tabeli konfiguracji sieci, który pojawia się w poprzedniej sekcji.

4. Powtórz tę procedurę w środowisku POC2.

## <a name="create-the-network-resources-in-poc1"></a>Tworzenie zasobów sieciowych w środowisku POC1

Teraz możesz utworzyć zasoby sieciowe POC1 należy ustawić się bram. Poniższe instrukcje przedstawiają sposób tworzenia zasobów za pomocą aplikacji portal użytkowników usługi Azure Stack. Kod programu PowerShell można również użyć do utworzenia zasobów.

![Przepływ pracy, który jest używany do tworzenia zasobów](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Zaloguj się jako dzierżawca

Administrator usługi można zalogować się jako dzierżawca Aby przetestować plany, oferty i subskrypcje, które mogą korzystać dzierżawcy. Jeśli nie masz jeszcze jeden, [utworzenie konta dzierżawy](azure-stack-add-new-user-aad.md) przed zalogowaniem.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej

1. Użyj konta dzierżawy do logowania do portalu użytkowników.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Wybierz pozycję **Sieć wirtualna**.
5. Dla **nazwa**, **przestrzeń adresowa**, **Nazwa podsieci**, i **zakres adresów podsieci**, użyj wartości, które są wyświetlane we wcześniejszej części sieci Tabela konfiguracji.
6. W **subskrypcji**, pojawi się w subskrypcji, która została utworzona wcześniej.
7. Aby uzyskać **grupy zasobów**, możesz utworzyć grupę zasobów lub jeśli już istnieje, wybrać **Użyj istniejącej**.
8. Sprawdź lokalizację domyślną.
9. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
10. Wybierz pozycję **Utwórz**.

### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. Na pulpicie nawigacyjnym otwórz zasób sieci wirtualnej VNET-01, który został utworzony wcześniej.
2. W bloku **Ustawienia** wybierz pozycję **Podsieci**.
3. Aby dodać podsieć bramy do sieci wirtualnej, wybierz **podsieci bramy**.

    ![Dodaj podsieć bramy](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Domyślnie nazwa podsieci jest równa **GatewaySubnet**. Podsieć bramy to specjalne. Aby działać poprawnie, muszą oni korzystać z **GatewaySubnet** nazwy.
5. W **zakres adresów**, sprawdź, czy adres jest **10.0.11.0/24**.
6. Wybierz **OK** utworzyć podsieć bramy.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Wybierz z listy zasobów sieciowych, **bramy sieci wirtualnej**.
4. W **nazwa**, wprowadź **GW1**.
5. Wybierz **sieć wirtualna** element, aby wybrać sieć wirtualną. Wybierz **VNET-01** z listy.
6. Wybierz **publiczny adres IP** elementu menu. Gdy **wybierz publiczny adres IP** zostanie wyświetlone okno dialogowe, wybierz opcję **Utwórz nową**.
7. W **nazwa**, wprowadź **GW1-PiP**, a następnie wybierz pozycję **OK**.
8. Domyślnie dla **typu sieci VPN**, **oparte na trasach** jest zaznaczone. Zachowaj **oparte na trasach** typu sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Możesz przypiąć zasób do pulpitu nawigacyjnego. Wybierz pozycję **Utwórz**.

### <a name="create-the-local-network-gateway"></a>Tworzenie bramy sieci lokalnej

Implementacja *bramy sieci lokalnej* w tym wdrożeniu na potrzeby oceny usługi Azure Stack jest nieco inna niż w rzeczywistym wdrożeniu platformy Azure.

We wdrożeniu platformy Azure Brama sieci lokalnej reprezentuje lokalne (w dzierżawie) urządzenie fizyczne, używanej do połączenia z bramą sieci wirtualnej, na platformie Azure. W tym wdrożeniu na potrzeby oceny usługi Azure Stack obu końcach połączenia są bramy sieci wirtualnej.

Sposób myślenia o tym bardziej ogólnie jest zasobu Brama sieci lokalnej będzie zawsze informuje bramę zdalną po drugiej stronie połączenia. Ze względu na sposób, w jaki zaprojektowano tak, Azure Stack Development Kit musisz podać adres IP zewnętrznej karty sieciowej na translatora adresów sieciowych (NAT) maszyny Wirtualnej o innym ASDK jako publiczny adres IP bramy sieci lokalnej. Następnie utworzysz mapowania translatora adresów Sieciowych na maszynie Wirtualnej translatora adresów Sieciowych, aby upewnić się, że oba środowiska zostaną prawidłowo połączone.

### <a name="create-the-local-network-gateway-resource"></a>Tworzenie zasobu Brama sieci lokalnej

1. Zaloguj się do maszyny fizycznej usługi Azure Stack dla środowiska POC1.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Z listy zasobów wybierz **bramy sieci lokalnej**.
5. W **nazwa**, wprowadź **POC2-GW**.
6. W **adres IP**, wprowadź adres zewnętrzny BGPNAT dla środowiska POC2. Ten adres będzie widoczny we wcześniejszej części tabeli konfiguracji sieci.
7. W **przestrzeni adresowej**, przestrzeni adresowej sieci Wirtualnej w środowisku POC2, która utworzysz w dalszej części, wprowadź **10.0.20.0/23**.
8. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** wartości są poprawne, a następnie wybierz **Utwórz**.

### <a name="create-the-connection"></a>Tworzenie połączenia

1. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **połączenia**.
4. Na **podstawy** bloku ustawienia dla **typu połączenia**, wybierz opcję **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
6. Na **ustawienia** bloku wybierz **bramy sieci wirtualnej**, a następnie wybierz pozycję **GW1**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **POC2-GW**.
8. W **nazwa połączenia**, wprowadź **POC1-POC2**.
9. W **klucz współużytkowany (PSK)**, wprowadź **12345**, a następnie wybierz pozycję **OK**.
10. Na **Podsumowanie** bloku wybierz **OK**.

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Aby sprawdzić poprawność danych, przybliżone ilości tych danych za pośrednictwem połączenia sieci VPN, należy maszyny wirtualne do wysyłania i odbierania danych w każdej usłudze Azure Stack Development Kit. Teraz Utwórz maszynę wirtualną w środowisku POC1, a następnie w Twojej sieci wirtualnej, umieść je w podsieci maszyny Wirtualnej.

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **obliczenia**.
3. Z listy obrazów maszyn wirtualnych wybierz **systemu Windows Server 2016 Datacenter — wersja próbna** obrazu.
4. Na **podstawy** bloku, w **nazwa**, wprowadź **VM01**.
5. Wprowadź prawidłową nazwę użytkownika i hasło. Użyjesz tego konta do logowania się do maszyny Wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
7. Na **rozmiar** blok dla tego wystąpienia, wybierz rozmiar maszyny wirtualnej, a następnie wybierz **wybierz**.
8. Na **ustawienia** bloku, zaakceptuj wartości domyślne. Upewnij się, że **VNET-01** została wybrana sieć wirtualnych. Sprawdź, czy podsieci jest równa **10.0.10.0/24**. Następnie wybierz przycisk **OK**.
9. Na **Podsumowanie** bloku, przejrzyj ustawienia, a następnie wybierz **OK**.

## <a name="create-the-network-resources-in-poc2"></a>Tworzenie zasobów sieciowych w środowisku POC2

Następnym krokiem jest tworzenie zasobów sieciowych dla środowiska POC2. Poniższe instrukcje przedstawiają sposób tworzenia zasobów za pomocą aplikacji portal użytkowników.

### <a name="sign-in-as-a-tenant-again"></a>Zaloguj się ponownie jako dzierżawca

Administrator usługi można zalogować się jako dzierżawca Aby przetestować plany, oferty i subskrypcje, które mogą korzystać dzierżawcy. Jeśli nie masz jeszcze jeden, [utworzenie konta dzierżawy](azure-stack-add-new-user-aad.md) przed zalogowaniem.

### <a name="create-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny Wirtualnej

1. Zaloguj się przy użyciu konta dzierżawy.
2. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
3. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
4. Wybierz pozycję **Sieć wirtualna**.
5. Użyj informacji znajdujących się wcześniej w tabeli konfiguracji sieci, aby zidentyfikować wartości dla środowiska POC2 **nazwa**, **przestrzeń adresowa**, **Nazwa podsieci**i **Zakres adresów podsieci**.
6. W **subskrypcji**, pojawi się w subskrypcji, która została utworzona wcześniej.
7. Aby uzyskać **grupy zasobów**, Utwórz nową grupę zasobów, lub jeśli już masz, wybierz **Użyj istniejącej**.
8. Zweryfikowanie domyślnego **lokalizacji**.
9. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
10. Wybierz pozycję **Utwórz**.

### <a name="create-gateway-subnet"></a>Tworzenie podsieci bramy

1. Otwórz właśnie utworzony zasób sieci wirtualnej (**VNET-02**) z poziomu pulpitu nawigacyjnego.
2. W bloku **Ustawienia** wybierz pozycję **Podsieci**.
3. Wybierz **podsieci bramy** dodać podsieć bramy do sieci wirtualnej.
4. Domyślna nazwa podsieci to **GatewaySubnet**. Podsieci bramy to specjalne podsieci i muszą mieć dokładnie tę nazwę, aby działać prawidłowo.
5. W **zakres adresów** pola, sprawdź adres jest **10.0.21.0/24**.
6. Wybierz **OK** utworzyć podsieć bramy.

### <a name="create-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.  
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Wybierz z listy zasobów sieciowych, **bramy sieci wirtualnej**.
4. W **nazwa**, wprowadź **GW2**.
5. Aby wybrać sieć wirtualną, wybrać **sieć wirtualna**. Następnie wybierz pozycję **VNET-02** z listy.
6. Wybierz pozycję **Publiczny adres IP**. Gdy **wybierz publiczny adres IP** zostanie otwarty blok wybierz **Utwórz nową**.
7. W **nazwa**, wprowadź **GW2-PiP**, a następnie wybierz pozycję **OK**.
8. Domyślnie dla **typu sieci VPN**, **oparte na trasach** jest zaznaczone. Zachowaj **oparte na trasach** typu sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Możesz przypiąć zasób do pulpitu nawigacyjnego. Wybierz pozycję **Utwórz**.

### <a name="create-local-network-gateway-resource"></a>Tworzenie zasobu Brama sieci lokalnej

1. W środowisku POC2 portalu użytkowników, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **bramy sieci lokalnej**.
4. W **nazwa**, wprowadź **POC1-GW**.
5. W **adres IP**, wprowadź adres zewnętrzny BGPNAT dla środowiska POC1, który znajduje się wcześniej w tabeli konfiguracji sieci.
6. W **przestrzeni adresowej**, ze środowiska POC1, wprowadź **10.0.10.0/23** przestrzeń adresowa **VNET-01**.
7. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne, a następnie wybierz **Utwórz**.

## <a name="create-connection"></a>Tworzenie połączenia

1. W aplikacji portal użytkowników, wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **sieć**.
3. Z listy zasobów wybierz **połączenia**.
4. Na **podstawowe** bloku ustawienia dla **typu połączenia**, wybierz **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
6. Na **ustawienia** bloku wybierz **bramy sieci wirtualnej**, a następnie wybierz pozycję **GW2**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **POC1-GW**.
8. W **nazwa połączenia**, wprowadź **POC2-POC1**.
9. W **klucz współużytkowany (PSK)**, wprowadź **12345**. Jeśli wybierzesz inną wartość, należy pamiętać, że musi ona odpowiadać wartości dla klucza współużytkowanego, który został utworzony w środowisku POC1. Kliknij przycisk **OK**.
10. Przegląd **Podsumowanie** bloku, a następnie wybierz **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Teraz Utwórz maszynę wirtualną w środowisku POC2 i umieść je w podsieci maszyny Wirtualnej w sieci wirtualnej:

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Przejdź do **Marketplace**, a następnie wybierz pozycję **obliczenia**.
3. Z listy obrazów maszyn wirtualnych wybierz **systemu Windows Server 2016 Datacenter — wersja próbna** obrazu.
4. Na **podstawy** bloku dla **nazwa**, wprowadź **VM02**.
5. Wprowadź prawidłową nazwę użytkownika i hasło. Użyjesz tego konta do logowania się do maszyny wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz pozycję **OK**.
7. Na **rozmiar** bloku wybierz maszynę wirtualną, rozmiar dla tego wystąpienia, a następnie wybierz **wybierz**.
8. Na **ustawienia** bloku możesz zaakceptować wartości domyślne. Upewnij się, że **VNET-02** sieci wirtualnej jest zaznaczone, a następnie sprawdź, czy podsieci jest równa **10.0.20.0/24**. Kliknij przycisk **OK**.
9. Sprawdź ustawienia na **Podsumowanie** bloku, a następnie wybierz **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Konfigurowanie maszyny wirtualnej translatora adresów Sieciowych na każdym Azure Stack Development Kit dla przechodzenia przez bramy

Ponieważ ASDK autonomiczne i odizolowane od sieci, na którym jest wdrożony host fizyczny, *zewnętrznych* bramy są podłączone do sieci wirtualne adresy IP nie jest w rzeczywistości zewnętrzna. Zamiast tego sieć adresów VIP jest ona ukryta za routerem, który wykonuje translatora adresów sieciowych.

Router jest maszynę wirtualną systemu Windows Server o nazwie **AzS-bgpnat01**, które jest uruchamiane w roli usługi Routing i usługi Dostęp zdalny (RRAS) w infrastrukturze ASDK. Należy skonfigurować translatora adresów Sieciowych na maszynie wirtualnej AzS-bgpnat01, aby umożliwić nawiązanie połączenia na obu końcach połączenia sieci VPN lokacja lokacja.

Aby skonfigurować połączenie sieci VPN, należy utworzyć trasę statyczną mapy translatora adresów Sieciowych, która mapuje interfejs zewnętrzny na maszynie wirtualnej BGPNAT na adres VIP puli bramy granicznej. Trasy statycznej mapy translatora adresów Sieciowych jest wymaganych dla poszczególnych portów w przypadku połączenia sieci VPN.

> [!NOTE]
> Ta konfiguracja jest wymagana tylko w przypadku usługi Azure Stack Development Kit środowisk.
>
>

### <a name="configure-the-nat"></a>Konfigurowanie translatora adresów Sieciowych

> [!IMPORTANT]
> Wykonaj tę procedurę dla obu środowiskach ASDK.

1. Określić **wewnętrzny adres IP** do użycia w poniższy skrypt programu PowerShell. Otwórz bramy sieci wirtualnej (GW1 i GW2), a następnie na **Przegląd** bloku Zapisz wartość **publiczny adres IP** do późniejszego użycia.

   ![Wewnętrzny adres IP](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Zaloguj się do maszyny fizycznej usługi Azure Stack dla środowiska POC1.
3. Skopiuj i Edytuj następujący skrypt programu PowerShell. Aby skonfigurować translatora adresów Sieciowych w każdym Azure Stack Development Kit, uruchom skrypt w podwyższonym środowiska Windows PowerShell ISE. W skrypcie, Dodaj wartości w `External BGPNAT address` i `Internal IP address` symbole zastępcze:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Powtórz tę procedurę w środowisku POC2.

## <a name="test-the-connection"></a>Testowanie połączenia

Skoro nawiązaniu połączenia lokacja lokacja należy zweryfikować, czy można uzyskać przez niego przesyłać ruch. Aby sprawdzić, zaloguj się do jednej z maszyn wirtualnych, które zostały utworzone w dowolnym środowisku ASDK. Następnie należy wysłać polecenie ping maszyny wirtualnej, który został utworzony w innym środowisku.

Upewnij się, aby upewnić się, że wysyłać ruch przez połączenie lokacja lokacja, polecenie ping do adresu bezpośredni adres IP (DIP), maszyny wirtualnej w podsieci zdalnej, a nie adres VIP. Aby to zrobić, należy znaleźć adres DIP na drugiej stronie połączenia. Zapisz adres w celu późniejszego użycia.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Zaloguj się do maszyny Wirtualnej dzierżawy w środowisku POC1

1. Zaloguj się do maszyny fizycznej usługi Azure Stack dla środowiska POC1, a następnie użyj konta dzierżawy, aby zalogować się do portalu użytkowników.
2. Na pasku nawigacyjnym po lewej stronie wybierz **obliczenia**.
3. Na liście maszyn wirtualnych Znajdź **VM01** , który został wcześniej utworzony, a następnie wybierz ją.
4. W bloku maszyny wirtualnej, kliknij **Connect**, a następnie otwórz plik VM01.rdp.

     ![Przycisk Połącz](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Zaloguj się przy użyciu konta, które zostały skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otworzyć z podwyższonym poziomem uprawnień **programu Windows PowerShell** okna.
7. Wprowadź **ipconfig/all**.
8. W danych wyjściowych, Znajdź **adres IPv4**, a następnie Zapisz adres w celu późniejszego użycia. Jest to adres, który wyślesz polecenie ping ze środowiska POC2. W środowisku przykładowym ten adres to **10.0.10.4**, ale w Twoim środowisku może on być inny. Należy włączyć w **10.0.10.0/24** podsieci, który został utworzony wcześniej.
9. Aby utworzyć regułę zapory, która umożliwia maszynę wirtualną, aby odpowiadać na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Zaloguj się do maszyny Wirtualnej dzierżawy w środowisku POC2

1. Zaloguj się do maszyny fizycznej usługi Azure Stack dla środowiska POC2, a następnie użyj konta dzierżawy, aby zalogować się do portalu użytkowników.
2. Na pasku nawigacyjnym po lewej stronie kliknij **obliczenia**.
3. Z listy maszyn wirtualnych, należy znaleźć **VM02** , który został wcześniej utworzony, a następnie wybierz ją.
4. W bloku maszyny wirtualnej kliknij pozycję **Połącz**.
5. Zaloguj się przy użyciu konta, które zostały skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otworzyć z podwyższonym poziomem uprawnień **programu Windows PowerShell** okna.
7. Wprowadź **ipconfig/all**.
8. Adres IPv4 jest wyświetlana, który mieści się w **10.0.20.0/24**. W środowisku przykładowym jest adres **10.0.20.4**, ale Twój adres mogą się różnić.
9. Aby utworzyć regułę zapory, która umożliwia maszynę wirtualną, aby odpowiadać na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z maszyny wirtualnej w środowisku POC2 Wyślij polecenie ping z maszyny wirtualnej w środowisku POC1, za pośrednictwem tunelu. Aby to zrobić, należy wysłać polecenie ping na bezpośredni adres IP zarejestrowany na maszynie VM01. W środowisku przykładowym jest to **10.0.10.4**, ale pamiętaj wysłać komunikat ping adres, możesz zauważyć, że w środowisku laboratoryjnym. Powinien zostać wyświetlony wynik, który wygląda następująco:

    ![Żądanie ping zakończone powodzeniem](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Odpowiedź ze zdalnej maszyny wirtualnej wskazuje pomyślnego testowego. Możesz zamknąć okno maszyny wirtualnej. Aby przetestować połączenie, możesz wypróbować inne rodzaje transferów danych, takich jak kopiowanie plików.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Wyświetlanie statystyk transferu danych za pośrednictwem połączenia bramy

Jeśli chcesz dowiedzieć się, jak dużo danych przechodzą przez połączenie lokacja lokacja, te informacje są dostępne na **połączenia** bloku. Ten test jest również innym sposobem, aby sprawdzić, czy wysłane polecenie ping rzeczywiście przesłane przez połączenie VPN.

1. Zalogowano się do maszyny wirtualnej dzierżawy w środowisku POC2, użyć danego konta dzierżawy do logowania do portalu użytkowników.
2. Przejdź do **wszystkie zasoby**, a następnie wybierz pozycję **POC2-POC1** połączenia. **Połączenia** pojawia się.
3. W **połączenia** okna, statystyki dotyczące **dane w** i **dane wyjściowe** są wyświetlane. Poniższy zrzut ekranu dużych liczb są przypisane do transfer dodatkowych plików. Powinien zostać wyświetlony niektóre wartości wartość różną od zera.

    ![Dane wejściowe i wyjściowe](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
