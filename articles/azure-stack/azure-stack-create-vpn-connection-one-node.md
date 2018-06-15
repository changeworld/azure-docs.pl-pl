---
title: Utwórz połączenie sieci VPN lokacja lokacja między dwoma sieciami wirtualnymi w różnych środowiskach Azure stosu Development Kit | Dokumentacja firmy Microsoft
description: Krok po kroku procedury, która używa administrator chmury nie można utworzyć połączenia sieci VPN lokacja lokacja między dwoma środowiskami Azure stosu Development Kit jednym węzłem.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: e6520da6b866ebddd66604dd8f27acfc5a9bcef4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159855"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Utwórz połączenie sieci VPN lokacja lokacja między dwoma sieciami wirtualnymi w różnych środowiskach Azure stosu Development Kit
## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób utworzyć połączenie VPN lokacja lokacja między dwiema sieciami wirtualnego w dwóch oddzielnych środowisk Azure stosu Development Kit. Podczas konfigurowania połączeń, dowiesz się, jak działają bramy sieci VPN w stosie Azure.

### <a name="connection-diagram"></a>Diagram połączenia
Na poniższym diagramie przedstawiono, jak powinna wyglądać konfiguracji połączenia po zakończeniu.

![Konfiguracja połączenia sieci VPN typu lokacja lokacja](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem
Aby ukończyć konfigurację połączenia, upewnij się, że masz następujące elementy, przed rozpoczęciem:

* Dwa serwery i inne wymagania wstępne, które spełniają wymagania sprzętowe Azure stosu Development Kit, zgodnie z opisem w [Szybki Start: ocena Azure stosu Development Kit](azure-stack-deploy-overview.md). 
* [Azure stosu Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) pakietu wdrożeniowego.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Wdrażanie środowiska Azure stosu Development Kit
Aby ukończyć konfigurację połączenia, należy wdrożyć dwóch środowisk Azure stosu Development Kit.
> [!NOTE] 
> W przypadku każdego Azure stosu zestaw deweloperski, który można wdrożyć, wykonaj [instrukcje dotyczące wdrażania](azure-stack-run-powershell-script.md). W tym artykule są nazywane środowisk Azure stosu Development Kit *POC1* i *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Przygotowanie oferty POC1 i POC2
Na POC1 i POC2 Przygotuj oferty, dzięki czemu użytkownik może subskrybować ofertę i wdrażania maszyn wirtualnych. Aby uzyskać informacje na temat tworzenia oferty, zobacz [udostępnić użytkownikom stosu Azure maszyny wirtualne](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Przejrzyj i ukończ tabeli konfiguracji sieci.
W poniższej tabeli przedstawiono konfigurację sieci dla obu środowiskach Azure stosu Development Kit. Użyj tej procedury, która pojawia się po tabeli, aby dodać adres BGPNAT zewnętrznego, który jest specyficzny dla danej sieci.

**Tabela konfiguracji sieci**
|   |POC1|POC2|
|---------|---------|---------|
|Nazwa sieci wirtualnej     |VNET-01|VNET-02 |
|Przestrzeń adresową sieci wirtualnej |10.0.10.0/23|10.0.20.0/23|
|Nazwa podsieci     |Subnet-01|Subnet-02|
|Zakres adresów podsieci|10.0.10.0/24 |10.0.20.0/24 |
|Podsieć bramy     |10.0.11.0/24|10.0.21.0/24|
|Zewnętrzny adres BGPNAT     |         |         |

> [!NOTE]
> Zewnętrzne adresy BGPNAT IP w środowisku przykładzie są 10.16.167.195 dla POC1 i 10.16.169.131 dla POC2. Użyj poniższej procedury, aby określić zewnętrzne adresy BGPNAT IP dla hostów Azure stosu Development Kit, a następnie dodaj je do poprzedniej tabeli konfiguracji sieci.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Uzyskiwanie adresu IP zewnętrznej karty sieciowej maszyny wirtualnej translatora adresów sieciowych
1. Zaloguj się do komputera fizycznego Azure stosu dla POC1.
2. Edytuj następujący kod programu Powershell, aby zastąpić hasło administratora, a następnie uruchom kod na hoście, aby Zapewnić:

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
3. Dodaj adres IP do tabeli konfiguracji sieci, która jest wyświetlana w poprzedniej sekcji.

4. Powtórz tę procedurę na POC2.

## <a name="create-the-network-resources-in-poc1"></a>Tworzenie zasobów sieciowych w POC1
Teraz utworzysz POC1 zasobów sieciowych, które należy ustawić się z bramy. Poniższe instrukcje pokazują, jak utworzyć zasobów przy użyciu aplikacji portal użytkowników. Umożliwia także kod programu PowerShell na tworzenie zasobów.

![Przepływ pracy, który jest używany do tworzenia zasobów](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Zaloguj się jako dzierżawca
Administrator usługi można Zaloguj się jako dzierżawcy do testowania planów, ofertami i subskrypcje, które mogą używać swoich dzierżaw. Jeśli nie masz już konto, [utworzenie konta dzierżawy](azure-stack-add-new-user-aad.md) przed zalogowaniem.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej
1. Użyj konta dzierżawy logować się do portalu użytkowników.
2. W portalu użytkowników wybierz **nowy**.

    ![Utwórz nową sieć wirtualną](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
4. Wybierz **sieci wirtualnej**.
5. Dla **nazwa**, **przestrzeni adresów**, **nazwy podsieci**, i **zakres adresów podsieci**, użyj wartości, które pojawiają się wcześniej w sieci Tabela konfiguracji.
6. W **subskrypcji**, pojawi się subskrypcji, który został utworzony wcześniej.
7. Aby uzyskać **grupy zasobów**, można utworzyć grupę zasobów lub jeśli już istnieje, wybierz **Użyj istniejącego**.
8. Sprawdź lokalizację domyślną.
9. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
10. Wybierz pozycję **Utwórz**.

### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy
1. Na pulpicie nawigacyjnym otwórz zasobów sieci wirtualnej sieci Wirtualnej-01, utworzony wcześniej.
2. W bloku **Ustawienia** wybierz pozycję **Podsieci**.
3. Aby dodać podsieci bramy sieci wirtualnej, wybierz **podsieci bramy**.
   
    ![Dodaj podsieć bramy](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Domyślnie nazwa podsieci ma ustawioną **GatewaySubnet**.
   Specjalne są podsieciami bramy. Aby działać poprawnie, musisz użyć *GatewaySubnet* nazwy.
5. W **zakres adresów**, sprawdź, czy adres jest **10.0.11.0/24**.
6. Wybierz **OK** można utworzyć podsieci bramy.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej
1. W portalu Azure wybierz **nowy**. 
2. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
3. Wybierz z listy zasobów sieciowych, **Brama sieci wirtualnej**.
4. W **nazwa**, wprowadź **GW1**.
5. Wybierz **sieci wirtualnej** element, aby wybrać sieci wirtualnej.
   Wybierz **VNET-01** z listy.
6. Wybierz **publicznego adresu IP** elementu menu. Gdy **wybierz publiczny adres IP** zostanie otwarty blok, wybierz opcję **Utwórz nowy**.
7. W **nazwa**, wprowadź **GW1 PiP**, a następnie wybierz **OK**.
8.  Domyślnie dla **typ sieci VPN**, **opartej na trasach** jest zaznaczone.
    Zachowaj **opartej na trasach** typ sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Można przypiąć do pulpitu nawigacyjnego zasobu. Wybierz pozycję **Utwórz**.

### <a name="create-the-local-network-gateway"></a>Tworzenie bramy sieci lokalnej
Implementacja *bramy sieci lokalnej* w tym wdrożeniu na potrzeby oceny usługi Azure Stack jest nieco inna niż w rzeczywistym wdrożeniu platformy Azure.

We wdrożeniu Azure bramy sieci lokalnej reprezentuje urządzenie fizyczne lokalnymi (na dzierżawcy), używanej do nawiązania połączenia bramy sieci wirtualnej na platformie Azure. W tym wdrożeniu oceny stosu Azure obu końców połączenia są bram sieci wirtualnej!

Sposób można traktować to bardziej ogólnie jest zasobu bramy sieci lokalnej będzie zawsze informuje bramy zdalnego na drugim końcu połączenia. Ze względu na sposób Development Kit stosu Azure została zaprojektowana musisz podać adres IP zewnętrznej karty sieciowej na translatora adresów sieciowych (NAT) wirtualna innych Azure stosu zestawie jako publiczny adres IP bramy sieci lokalnej. Następnie można utworzyć mapowania translatora adresów Sieciowych NAT maszynę wirtualną i upewnij się, że oba końce są prawidłowo podłączone.


### <a name="create-the-local-network-gateway-resource"></a>Utwórz zasób bramy sieci lokalnej
1. Zaloguj się do komputera fizycznego Azure stosu dla POC1.
2. W portalu użytkowników wybierz **nowy**.
3. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
4. Wybierz z listy zasobów **bramy sieci lokalnej**.
5. W **nazwa**, wprowadź **POC2-GW**.
6. W **adres IP**, wprowadź adres zewnętrzny BGPNAT POC2. Ten adres występuje wcześniej w tabeli konfiguracji sieci.
7. W **przestrzeni adresowej**, w przypadku przestrzeni adresowej sieci wirtualnej POC2 utworzonego później, wprowadź **10.0.20.0/23**.
8. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne, a następnie wybierz **Utwórz**.

### <a name="create-the-connection"></a>Tworzenie połączenia
1. W portalu użytkowników wybierz **nowy**.
2. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
3. Wybierz z listy zasobów **połączenia**.
4. Na **podstawy** bloku ustawienia dla **typ połączenia**, wybierz pozycję **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz **OK**.
6. Na **ustawienia** bloku, wybierz opcję **Brama sieci wirtualnej**, a następnie wybierz **GW1**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz **POC2-GW**.
8. W **nazwa połączenia**, wprowadź **POC1 POC2**.
9. W **klucz udostępniony (PSK)**, wprowadź **12345**, a następnie wybierz **OK**.
10. Na **Podsumowanie** bloku, wybierz opcję **OK**.

### <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
Aby sprawdzić poprawność danych, który przechodzi przez połączenie sieci VPN, należy maszyn wirtualnych, aby wysyłać i odbierać dane w każdym Azure stosu Development Kit. Utwórz maszynę wirtualną w POC1 teraz, a następnie w Twojej sieci wirtualnej, umieść ją w podsieci sieci maszyny Wirtualnej.

1. W portalu Azure wybierz **nowy**.
2. Przejdź do **Marketplace**, a następnie wybierz **obliczeniowe**.
3. Na liście obrazów maszyny wirtualnej, wybierz **Eval centrum danych systemu Windows Server 2016** obrazu.
4. Na **podstawy** bloku, w **nazwa**, wprowadź **VM01**.
5. Wprowadź prawidłową nazwę użytkownika i hasło. Służy do logowania się do maszyny Wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz **OK**.
7. Na **rozmiar** bloku dla tego wystąpienia, wybierz rozmiar maszyny wirtualnej, a następnie wybierz **wybierz**.
8. Na **ustawienia** bloku, zaakceptuj ustawienia domyślne. Upewnij się, że **VNET-01** sieci wirtualnej jest zaznaczone. Sprawdź, czy podsieci ma ustawioną **10.0.10.0/24**. Następnie wybierz przycisk **OK**.
9. Na **Podsumowanie** bloku, przejrzyj ustawienia, a następnie wybierz **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Tworzenie zasobów sieciowych w POC2

Następnym krokiem jest tworzenie zasobów sieciowych dla POC2. Poniższe instrukcje przedstawiają sposób utworzyć zasobów przy użyciu aplikacji portal użytkowników.

### <a name="sign-in-as-a-tenant"></a>Zaloguj się jako dzierżawca
Administrator usługi można Zaloguj się jako dzierżawcy do testowania planów, ofertami i subskrypcje, które mogą używać swoich dzierżaw. Jeśli nie masz już konto, [utworzenie konta dzierżawy](azure-stack-add-new-user-aad.md) przed zalogowaniem.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Tworzenie sieci wirtualnej i podsieci maszyny wirtualnej

1. Zaloguj się przy użyciu konta dzierżawcy.
2. W portalu użytkowników wybierz **nowy**.
3. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
4. Wybierz **sieci wirtualnej**.
5. Użyj informacji znajdującego się wcześniej w tabeli konfiguracji sieci, aby zidentyfikować potrzebne wartości dla POC2 **nazwa**, **przestrzeni adresów**, **nazwy podsieci**i **Zakres adresów podsieci**.
6. W **subskrypcji**, pojawi się subskrypcji, który został utworzony wcześniej.
7. Aby uzyskać **grupy zasobów**, Utwórz nową grupę zasobów lub, jeśli masz już konto, wybierz **Użyj istniejącego**.
8. Zweryfikowanie domyślnego **lokalizacji**.
9. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.
10. Wybierz pozycję **Utwórz**.

### <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy
1. Otwórz utworzony zasób sieci wirtualnej (**VNET-02**) z poziomu pulpitu nawigacyjnego.
2. W bloku **Ustawienia** wybierz pozycję **Podsieci**.
3. Wybierz **podsieci bramy** Aby dodać podsieci bramy sieci wirtualnej.
4. Domyślna nazwa podsieci to **GatewaySubnet**.
   Podsieci bramy to specjalne podsieci i muszą mieć dokładnie tę nazwę, aby działać prawidłowo.
5. W **zakres adresów** pola, sprawdź adres jest **10.0.21.0/24**.
6. Wybierz **OK** można utworzyć podsieci bramy.

### <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej
1. W portalu Azure wybierz **nowy**.  
2. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
3. Wybierz z listy zasobów sieciowych, **Brama sieci wirtualnej**.
4. W **nazwa**, wprowadź **GW2**.
5. Aby wybrać sieć wirtualną, wybierz **sieci wirtualnej**. Następnie wybierz **VNET-02** z listy.
6. Wybierz **publicznego adresu IP**. Gdy **wybierz publiczny adres IP** zostanie otwarty blok, wybierz opcję **Utwórz nowy**.
7. W **nazwa**, wprowadź **GW2 PiP**, a następnie wybierz **OK**.
8. Domyślnie dla **typ sieci VPN**, **opartej na trasach** jest zaznaczone.
    Zachowaj **opartej na trasach** typ sieci VPN.
9. Upewnij się, że wartości w polach **Subskrypcja** i **Lokalizacja** są poprawne. Można przypiąć do pulpitu nawigacyjnego zasobu. Wybierz pozycję **Utwórz**.

### <a name="create-the-local-network-gateway-resource"></a>Utwórz zasób bramy sieci lokalnej

1. W portalu użytkowników POC2 wybierz **nowy**. 
4. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
5. Wybierz z listy zasobów **bramy sieci lokalnej**.
6. W **nazwa**, wprowadź **POC1-GW**.
7. W **adres IP**, wprowadź adres zewnętrzny BGPNAT POC1, który to wymienione we wcześniejszej części tabeli konfiguracji sieci.
8. W **przestrzeni adresowej**, z POC1, wprowadź **10.0.10.0/23** przestrzeni adresowej **VNET-01**.
9. Upewnij się, że Twoje **subskrypcji**, **grupy zasobów**, i **lokalizacji** są poprawne, a następnie wybierz **Utwórz**.

## <a name="create-the-connection"></a>Tworzenie połączenia
1. W portalu użytkowników wybierz **nowy**. 
2. Przejdź do **Marketplace**, a następnie wybierz **sieci**.
3. Wybierz z listy zasobów **połączenia**.
4. Na **podstawowe** bloku ustawienia dla **typ połączenia**, wybierz **lokacja lokacja (IPSec)**.
5. Wybierz **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz **OK**.
6. Na **ustawienia** bloku, wybierz opcję **Brama sieci wirtualnej**, a następnie wybierz **GW2**.
7. Wybierz **bramy sieci lokalnej**, a następnie wybierz **POC1-GW**.
8. W **nazwa połączenia**, wprowadź **POC2 POC1**.
9. W **klucz udostępniony (PSK)**, wprowadź **12345**. Jeśli wybierzesz inną wartość, należy pamiętać, że *musi* pasuje do wartości dla klucza udostępnionego utworzoną na POC1. Kliknij przycisk **OK**.
10. Przegląd **Podsumowanie** bloku, a następnie wybierz **OK**.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Teraz Utwórz maszynę wirtualną w POC2 i umieszczenie go w podsieci maszyny Wirtualnej w Twojej sieci wirtualnej.

1. W portalu Azure wybierz **nowy**.
2. Przejdź do **Marketplace**, a następnie wybierz **obliczeniowe**.
3. Na liście obrazów maszyny wirtualnej, wybierz **Eval centrum danych systemu Windows Server 2016** obrazu.
4. Na **podstawy** bloku dla **nazwa**, wprowadź **VM02**.
5. Wprowadź prawidłową nazwę użytkownika i hasło. Służy do logowania się na maszynie wirtualnej, po jego utworzeniu.
6. Podaj **subskrypcji**, **grupy zasobów**, i **lokalizacji**, a następnie wybierz **OK**.
7. Na **rozmiar** bloku, wybierz maszynę wirtualną rozmiaru dla tego wystąpienia, a następnie wybierz **wybierz**.
8. Na **ustawienia** bloku można zaakceptować wartości domyślne. Upewnij się, że **VNET-02** sieci wirtualnej jest zaznaczone, a następnie sprawdź, czy podsieci ma ustawioną **10.0.20.0/24**. Kliknij przycisk **OK**.
9. Sprawdź ustawienia na **Podsumowanie** bloku, a następnie wybierz **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Skonfiguruj maszynę wirtualną translatora adresów Sieciowych w każdym Azure stosu Development Kit dla operacji przechodzenia bramy
Ponieważ Azure stosu Development Kit jest niezależna i odizolowane od sieci, na którym wdrożono hosta fizycznego, *zewnętrznych* bramy są podłączone do sieci wirtualne adresy IP nie jest rzeczywiście zewnętrznych. Zamiast tego sieci adresu VIP jest ukryty za router, który wykonuje translatora adresów sieciowych. 

Router jest maszynę wirtualną systemu Windows Server o nazwie *AzS bgpnat01*, która jest uruchomiona rola usługi Routing i dostęp zdalny (RRAS) w infrastrukturze Azure stosu Development Kit. Należy skonfigurować translatora adresów Sieciowych w maszynie wirtualnej AzS bgpnat01 zezwalały na połączenie VPN lokacja lokacja na obu końcach połączenie. 

Aby skonfigurować połączenie sieci VPN, należy utworzyć tras statycznych mapy translatora adresów Sieciowych, mapująca zewnętrznego interfejsu na maszynie wirtualnej BGPNAT do adresu IP z puli krawędzi bramy. Trasy statyczne mapy translatora adresów Sieciowych jest wymaganych dla poszczególnych portów w połączeniu sieci VPN.

> [!NOTE]
> Ta konfiguracja jest wymagana tylko w środowiskach Azure stosu Development Kit.
> 
> 

### <a name="configure-the-nat"></a>Konfigurowanie translatora adresów Sieciowych
> [!IMPORTANT]
> Wykonaj tę procedurę dla *zarówno* środowisk Azure stosu Development Kit.

1. Określić **wewnętrznego adresu IP** do użycia w poniższy skrypt programu PowerShell. Otwórz bramy sieci wirtualnej (GW1 i GW2), a następnie na **omówienie** bloku, Zapisz wartość **publicznego adresu IP** do późniejszego użycia.
![Wewnętrzny adres IP](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Zaloguj się do komputera fizycznego Azure stosu dla POC1.
3. Skopiuj i edytuj poniższy skrypt programu PowerShell. Aby skonfigurować translatora adresów Sieciowych w każdym Azure stosu Development Kit, uruchom skrypt w z podwyższonym poziomem uprawnień programu Windows PowerShell ISE. W skrypcie, dodawania wartości *adres zewnętrzny BGPNAT* i *wewnętrznego adresu IP* symbole zastępcze:

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

4. Powtórz tę procedurę na POC2.

## <a name="test-the-connection"></a>Testowanie połączenia
Teraz, gdy zostanie nawiązane połączenie lokacja lokacja, należy sprawdzić, czy uzyskanie przez niego przepływające ruchu. Aby sprawdzić, zaloguj się do jednej z maszyn wirtualnych, które zostały utworzone z jednego środowiska Azure stosu Development Kit. Następnie należy wysyłać polecenia ping maszyny wirtualnej, który został utworzony w innym środowisku. 

Aby upewnić się, że wysyłać ruch przez połączenie lokacja lokacja, upewnij się, polecenie ping adres bezpośredni adres IP (DIP) maszyny wirtualnej w zdalnym podsieci, a nie adres VIP. Aby to zrobić, należy znaleźć adres DIP na drugim końcu połączenia. Zapisz adres do późniejszego użycia.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Zaloguj się do maszyny Wirtualnej w ramach POC1 dzierżawcy
1. Zaloguj się do komputera fizycznego Azure stosu dla POC1, a następnie użyj konta dzierżawy logować się do portalu użytkowników.
2. Na pasku nawigacyjnym po lewej stronie wybierz **obliczeniowe**.
3. Na liście maszyn wirtualnych, należy znaleźć **VM01** utworzonego wcześniej, i wybierz go.
4. W bloku maszyny wirtualnej, kliknij **Connect**, a następnie otwórz plik VM01.rdp.
   
     ![Przycisk Połącz](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Zaloguj się przy użyciu konta, które zostało skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otwórz podwyższonym **programu Windows PowerShell** okna.
7. Wprowadź **ipconfig/all**.
8. W danych wyjściowych, Znajdź **adres IPv4**, a następnie Zapisz adres do późniejszego użycia. Jest to adres, który będzie ping z POC2. W środowisku przykładowym ten adres to **10.0.10.4**, ale w Twoim środowisku może on być inny. Należy włączyć w **10.0.10.0/24** podsieci utworzonego wcześniej.
9. Aby utworzyć regułę zapory zezwalającą maszyny wirtualnej, które odpowiadają na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Zaloguj się do maszyny Wirtualnej w ramach POC2 dzierżawcy
1. Zaloguj się do komputera fizycznego Azure stosu dla POC2, a następnie użyj konta dzierżawy logować się do portalu użytkowników.
2. Na pasku nawigacyjnym po lewej stronie kliknij **obliczeniowe**.
3. Z listy maszyn wirtualnych, należy znaleźć **VM02** utworzonego wcześniej, i wybierz go.
4. W bloku maszyny wirtualnej kliknij pozycję **Połącz**.
5. Zaloguj się przy użyciu konta, które zostało skonfigurowane podczas tworzenia maszyny wirtualnej.
6. Otwórz podwyższonym **programu Windows PowerShell** okna.
7. Wprowadź **ipconfig/all**.
8. Powinien zostać wyświetlony adres IPv4, który mieści się w **10.0.20.0/24**. W środowisku przykładzie adres jest **10.0.20.4**, ale adres mogą się różnić.
9. Aby utworzyć regułę zapory zezwalającą maszyny wirtualnej, które odpowiadają na polecenia ping, uruchom następujące polecenie programu PowerShell:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Z poziomu maszyny wirtualnej na POC2 ping maszyny wirtualnej na POC1, za pośrednictwem tunelu. Aby to zrobić, możesz wykonać polecenie ping zapisane z VM01 DIP.
   W środowisku przykładzie jest to **10.0.10.4**, ale należy wysyłać polecenia ping adres zanotowaną w laboratorium. Powinny pojawić się wynik, który wygląda następująco:
   
    ![Pomyślne polecenie ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Odpowiedź od zdalnego maszyny wirtualnej wskazuje testów powiodło się! Możesz zamknąć okno maszyny wirtualnej. Aby przetestować połączenie, możesz wypróbować inne rodzaje transferów danych, takich jak kopiowania plików.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Wyświetlanie statystyk transferu danych za pośrednictwem połączenia bramy
Jeśli chcesz wiedzieć, ile dane są przesyłane za pośrednictwem połączenia lokacja lokacja, te informacje są dostępne na **połączenia** bloku. Ten test jest również inny sposób, aby sprawdzić, czy polecenie ping, które zostały wysłane faktycznie nawiązaniem połączenia z siecią VPN.

1. Gdy użytkownik jest zalogowany do maszyny wirtualnej dzierżawcy w POC2, należy użyć konta dzierżawy logować się do portalu użytkowników.
2. Przejdź do **wszystkie zasoby**, a następnie wybierz **POC2 POC1** połączenia. **Połączenia** pojawi się.
4. Na **połączenia** bloku, statystyki dotyczące **danych w** i **dla danych wychodzących** są wyświetlane. Na poniższym zrzucie ekranu dużą liczbą są przypisane do transferu dodatkowych plików. Niektóre niezerowe wartości powinny być widoczne.
   
    ![Dane wejściowe i wyjściowe](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
