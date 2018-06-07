---
title: Konfigurowanie połączenia hybrydowe chmury Azure i stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować połączenia hybrydowe chmury Azure i stosu Azure.
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
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 72c5c4b0f0ab752bb02e6bee7cd038afca44ee1b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605199"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Samouczek: Konfigurowanie połączenia hybrydowe chmury Azure i stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można uzyskiwać dostęp do zasobów z zabezpieczeniami na platformie Azure globalnych i stosu Azure przy użyciu wzorca połączenie hybrydowe.

W tym samouczku utworzysz środowisku próbki do:

> [!div class="checklist"]
> - Zachowaj dane lokalne na spełnia prywatności lub przepisami dotyczącymi działalności, ale mają dostęp do globalnego zasobów platformy Azure.
> - Obsługa starszych systemu podczas używania wdrażania aplikacji skalowana w chmurze i zasobów na platformie Azure globalne.

## <a name="prerequisites"></a>Wymagania wstępne

Kilka składników są wymagane do utworzenia łączności hybrydowej. Niektóre z tych składników czasu przygotowania, dlatego należy odpowiednio zaplanować.

**Azure Stack**

Partner OEM/sprzętu Azure można wdrożyć produkcyjnych Azure stosu, a wszyscy użytkownicy, można wdrożyć Azure stosu Development Kit (ASDK).

**Azure składniki stosu**

Operator stosu Azure musi wdrożyć usługi App Service, tworzenie planów i ofert Utwórz subskrypcję dzierżawy i dodać obraz systemu Windows Server 2016. Jeśli masz już niektóre z tych składników, upewnij się, że spełniają one wymagania, przed rozpoczęciem tego samouczka.

W tym samouczku założono, że niektóre podstawową wiedzę na temat platformy Azure i stosu Azure. Aby dowiedzieć się więcej, przed rozpoczęciem tego samouczka, przeczytaj następujące artykuły:

 - [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Kluczowe założenia Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
 - Utwórz [sieci Web aplikacji](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) na platformie Azure. Zanotuj adres URL aplikacji sieci Web, ponieważ będzie on potrzebny w samouczku.

### <a name="azure-stack"></a>Azure Stack

 - Użyj produkcyjnego stosu Azure lub wdrożyć Azure stosu Development Kit z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Wdrażanie ASDK może potrwać do 7 godzin, więc odpowiednio zaplanować.

 - Wdrażanie [usługi aplikacji](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS usług Azure stosu.
 - [Tworzenie planów i oferty](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku Azure stosu.
 - [Utwórz subskrypcję dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku Azure stosu.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, spełniają następujące kryteria przed rozpoczęciem konfigurowania połączenia hybrydowe chmury:

 - Należy zewnętrznie połączonej publiczny adres IPv4 urządzenia sieci VPN. Ten adres IP nie może znajdować się za urządzeniem NAT
 - Wszystkie zasoby są wdrażane w tej samej regionu/lokalizacji.

#### <a name="tutorial-example-values"></a>Samouczek przykładowe wartości

Przykłady w tym samouczku, użyj następujących wartości. Te wartości można użyć do tworzenia środowiska testowego lub odwołać się do nich w celu lepszego zrozumienia przykłady. Aby uzyskać więcej informacji o typach bram sieci VPN, zobacz [About VPN Gateway configuration settings (Informacje o ustawieniach konfiguracji bramy sieci VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specyfikacje połączenia:

 - **Typ sieci VPN**: opartej na trasach
 - **Typ połączenia**: lokacja lokacja (IPsec)
 - **Typ bramy**: sieci VPN
 - **Nazwa połączenia Azure**: Azure-brama-AzureStack-S2SGateway (portalu spowoduje automatyczne wypełnianie tej wartości)
 - **Nazwa połączenia w usłudze Azure stosu**: AzureStack-brama-Azure-S2SGateway (portalu spowoduje automatyczne wypełnianie tej wartości)
 - **Klucz współużytkowany**: wszystkie zgodne ze sprzętem sieci VPN, z takimi samymi wartościami po obu stronach połączenia
 - **Subskrypcja**: dowolne preferowane subskrypcji
 - **Grupa zasobów**: Test Infra

Sieci i podsieci adresów IP:

| Połączenie Azure/Azure stosu | Name (Nazwa) | Podsieć | Adres IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Sieć wirtualna platformy Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure stos sieci wirtualnej | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Brama sieci wirtualnej platformy Azure | Azure bramy |  |  |
| Brama sieci wirtualnej platformy Azure stosu | AzureStack bramy |  |  |
| Azure publicznego adresu IP | Azure GatewayPublicIP |  | Określone podczas tworzenia |
| Publiczny adres IP Azure stosu | AzureStack GatewayPublicIP |  | Określone podczas tworzenia |
| Brama sieci lokalnej platformy Azure | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Wartość publicznego adresu IP Azure stosu |
| Brama sieci lokalnej platformy Azure stosu | Azure S2SGateway<br>10.100.102.0/23 |  | Wartość Azure publicznego adresu IP |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Tworzenie sieci wirtualnej w stosie Azure i Azure globalne

Poniższe kroki umożliwiają utworzenie sieci wirtualnej przy użyciu portalu. Umożliwiają one [przykładowe wartości](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) Jeśli używasz tego artykułu jako samouczka. Jednak jeśli w tym artykule używany do konfigurowania środowiska produkcyjnego, Zastąp ustawienia przykład własne wartości.

> [!IMPORTANT]
> Należy się upewnić, nie nakładają się na siebie adresów IP w przestrzeni adresów sieci wirtualnej platformy Azure lub stos Azure.

Tworzenie sieci wirtualnej platformy Azure:

1. Aby nawiązać połączenie za pomocą przeglądarki [portalu Azure](http://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **Utwórz zasób**. W **wyszukiwania portalu marketplace** wprowadź `virtual network`". Znajdź **sieci wirtualnej** w lista wyników, a następnie wybierz **sieci wirtualnej**.
3. Z **wybierz model wdrożenia** wybierz **Resource Manager**, a następnie wybierz **Utwórz**.
4. Na **Utwórz sieć wirtualną**, skonfigurować ustawienia sieci wirtualnej. Nazwy pola wymagane mają przedrostek czerwona gwiazdka.  Wprowadź prawidłową wartość, gwiazdka zmienia się zielony znacznik wyboru.

Tworzenie sieci wirtualnej Azure stosu:

* Powtórz poprzednie kroki (1-4) przy użyciu stosu Azure **portalu dzierżawcy**.

## <a name="add-a-gateway-subnet"></a>Dodawanie podsieci bramy

Przed połączeniem bramy sieci wirtualnej, należy utworzyć podsieci bramy sieci wirtualnej, który chcesz nawiązać połączenie. Usługi bramy używają adresów IP, które określisz w podsieci bramy.

W [portalu Azure](http://portal.azure.com/), przejdź do Menedżera zasobów sieci wirtualnej, której chcesz utworzyć bramę sieci wirtualnej.

1. Wybierz sieć wirtualną, aby otworzyć **sieci wirtualnej** strony.
2. W **ustawienia**, wybierz pozycję **podsieci**.
3. Na **podsieci** wybierz pozycję **+ podsieci bramy** otworzyć **Dodaj podsieć** strony.

    ![Dodaj podsieć bramy](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **Nazwa** dla podsieci jest automatycznie wprowadzana wartość "GatewaySubnet". Ta wartość jest wymagana dla platformy Azure do podsieci jest rozpoznawana jako podsieć bramy.
5. Zmień **zakres adresów** wartości, które są dostarczane do zgodne z wymaganiami konfiguracji, a następnie wybierz **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Tworzenie bramy sieci wirtualnej platformy Azure i Azure stosu

Poniższe kroki umożliwiają utworzenie bramy sieci wirtualnej na platformie Azure.

1. W lewej części strony portalu, wybierz **+** i w polu wyszukiwania wprowadź "Brama sieci wirtualnej".
2. W **wyniki**, wybierz pozycję **Brama sieci wirtualnej**.
3. W **Brama sieci wirtualnej**, wybierz pozycję **Utwórz** otworzyć **Utwórz bramę sieci wirtualnej** strony.
4. Na **Utwórz bramę sieci wirtualnej**, określ wartości dla bramy sieci, jak pokazano w **samouczka przykładowe wartości**i następujące wartości dodatkowe:

    - **Jednostka SKU**: podstawowe
    - **Sieć wirtualna**: Wybierz wcześniej utworzoną sieć wirtualną. Podsieć bramy, który został utworzony automatycznie jest zaznaczone.
    - **Pierwszy konfiguracji IP**: jest to publiczny adres IP bramy.
        - Wybierz **konfiguracji IP bramy Utwórz**, która umożliwia przejście do **wybierz publiczny adres IP** strony.
        - Wybierz **+ Utwórz nowy** otworzyć **tworzenie publicznego adresu IP** strony.
        - Wprowadź **nazwa** dla publicznego adresu IP. Pozostaw SKU jako **podstawowe**, a następnie wybierz **OK** Aby zapisać zmiany.

       > [!Note]
       > Obecnie Brama sieci VPN obsługuje tylko Alokacja adresów dynamicznych publicznego adresu IP. Jednak nie oznacza to zmiany adresu IP po jest przypisany do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Zmiana rozmiaru, resetowania lub innych wewnętrzny konserwacji/uaktualnienia bramy sieci VPN, nie zmieniaj adresu IP.

4. Sprawdź ustawienia bramy.
5. Wybierz **Utwórz** Utwórz bramę sieci VPN. Ustawienia bramy są weryfikowane i Kafelek "Wdrażanie bramy sieci wirtualnej" jest wyświetlany na pulpicie nawigacyjnym.

   >[!Note]
   >Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

    Po utworzeniu bramy można wyświetlić adres IP przypisany przez wyszukiwanie w sieci wirtualnej w portalu. Brama jest widoczna jako urządzenie podłączone. Aby uzyskać więcej informacji dotyczących bramy, wybierz urządzenie.

6. Powtórz poprzednie kroki (1-5) na wdrożenie usługi Azure stosu.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Utwórz bramę sieci lokalnej w stosie Azure i Azure

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. Lokacji podać nazwę, która Azure lub stos Azure można odwoływać się do, a następnie określ:

- Adres IP lokalnego urządzenia sieci VPN, które tworzysz połączenia.
- Prefiksy adresów IP, które zostaną przesłane za pośrednictwem bramy sieci VPN na urządzeniu sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej.

  >[!Note]
  >Jeśli zmiany sieci lokalnej lub musisz zmienić publicznego adresu IP urządzenia sieci VPN, można łatwo aktualizować te wartości później.

1. W portalu, wybierz **+ Utwórz zasób**.
2. W polu wyszukiwania wprowadź **bramy sieci lokalnej**, a następnie wybierz pozycję **Enter** do wyszukiwania. Spowoduje to zwrócenie listy wyników.
3. Wybierz **bramy sieci lokalnej**, a następnie wybierz pozycję **Utwórz** otworzyć **Utwórz bramę sieci lokalnej** strony.
4. Na **Utwórz bramę sieci lokalnej**, określ wartości dla bramy sieci lokalnej, za pomocą naszych **samouczka przykładowe wartości**. Uwzględnij następujące dodatkowe wartości.

    - **Adres IP**: jest to publiczny adres IP urządzenia sieci VPN, które chcesz Azure lub stos Azure, aby nawiązać połączenie. Określ prawidłowy publiczny adres IP, nie za translatorem adresów Sieciowych, więc Azure może osiągnąć ten adres. Jeśli nie masz teraz adres IP, można użyć wartości z przykładu jako symbol zastępczy, ale należy wrócić do poprzedniej strony i Zastąp symbol zastępczy publiczny adres IP urządzenia sieci VPN. Azure nie może połączyć się z urządzeniem, dopóki Podaj prawidłowy adres.
    - **Przestrzeń adresowa**: jest to zakres adresów dla sieci, która reprezentuje tej sieci lokalnej. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że nie nakłada się na zakresy, które określisz z zakresami pozostałych sieci, które chcesz nawiązać połączenie. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. Jeśli chcesz nawiązać połączenia z witryną lokalnej nie Przykładowa wartość, należy użyć własne wartości.
    - **Skonfiguruj ustawienia protokołu BGP**: Użyj tylko podczas konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
    - **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
    - **Grupa zasobów**: Wybierz grupę zasobów, do którego chcesz używać. Można utworzyć nową grupę zasobów lub wybierz już utworzony.
    - **Lokalizacja**: Wybierz ten obiekt zostanie utworzony w lokalizacji. Możesz wybrać tej samej sieci wirtualnej znajdującej się w lokalizacji, ale nie musisz to zrobić.
5. Po zakończeniu określania wymagane wartości, wybierz **Utwórz** Utwórz bramę sieci lokalnej.
6. Powtórz te kroki wdrażania stosu Azure (1-5).

## <a name="configure-your-connection"></a>Skonfiguruj połączenie

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. Urządzenia sieci VPN, które można skonfigurować jest określana jako połączenie. Aby skonfigurować połączenie, potrzebne są:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć adres publiczny adres IP bramy sieci VPN przy użyciu portalu Azure, przejdź do bramy sieci wirtualnej, a następnie wybierz nazwę bramy.

Poniższe kroki umożliwiają utworzenie połączenia sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej i lokalnego urządzenia sieci VPN.

1. W portalu Azure wybierz **+ Utwórz zasób**.
2. Wyszukaj **połączenia**.
3. W **wyniki**, wybierz pozycję **połączenia**.
4. Na **połączenia**, wybierz pozycję **Utwórz**.
5. Na **Utwórz połączenie**, skonfiguruj następujące ustawienia:

    - **Typ połączenia**: Wybierz lokacja lokacja (IPSec).
    - **Grupa zasobów**: Wybierz grupę zasobów testową.
    - **Brama sieci wirtualnej**: Wybierz bramę sieci wirtualnej został utworzony.
    - **Bramy sieci lokalnej**: wybierz utworzony bramy sieci lokalnej.
    - **Nazwa połączenia**: to jest automatycznie wypełniane przy użyciu wartości z dwóch bram.
    - **Wspólny klucz**: Ta wartość musi odpowiadać wartości używasz lokalnych lokalnego urządzenia sieci VPN. Samouczek przykładzie użyto "abc123", ale można i powinien używasz bardziej złożonych. Ważne jest, że ta wartość musi być taka sama jak wartość należy określić podczas konfigurowania urządzenia sieci VPN.
    - Wartości **subskrypcji**, **grupy zasobów**, i **lokalizacji** zostały usunięte.

6. Wybierz **OK** można utworzyć połączenia.

Widać połączenia w **połączeń** strony bramy sieci wirtualnej. Stan zmieni się z *nieznany* do *łączenie*, a następnie *zakończyło się pomyślnie*.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
