---
title: Konfigurowanie łączności chmury hybrydowej przy użyciu platformy Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować łączność w chmurze hybrydowej z platformą Azure i usługi Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 19c5e530822d1d9c2b4ec815b37b3373e8e39b02
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305982"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Samouczek: Konfigurowanie łączności chmury hybrydowej dzięki platformie Azure i usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można uzyskać dostęp do zasobów z zabezpieczeniami w globalnej platformy Azure i usługi Azure Stack przy użyciu wzorca połączenia hybrydowego.

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> - Zachowaj dane lokalnym w celu spełnienia wymagań prawnych lub ochrony prywatności, ale dostęp do globalnych zasobów platformy Azure.
> - Obsługa starszego systemu podczas korzystania z wdrożenia aplikacji skalowanych na chmurze i zasobów w globalnej platformy Azure.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi hybrydowej aplikacje. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.


## <a name="prerequisites"></a>Wymagania wstępne

Kilka składniki są wymagane do utworzenia wdrożenia połączenia hybrydowego. Niektóre z tych składników potrwa do przygotowania, dlatego należy odpowiednio zaplanować.

**Azure Stack**

Partner OEM/sprzętowych platformy Azure można wdrażać produkcji usługi Azure Stack i wszyscy użytkownicy wdrażanie usługi Azure Stack Development Kit (ASDK).

**Składniki platformy Azure Stack**

Operator usługi Azure Stack musi Wdróż usługę App Service, tworzyć plany i oferty, Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016. Jeśli masz już niektóre z tych składników, upewnij się, że spełniają one wymagania przed rozpoczęciem tego samouczka.

W tym samouczku założono, że niektóre podstawową wiedzę na temat platformy Azure i usługi Azure Stack. Aby dowiedzieć się więcej, przed rozpoczęciem tego samouczka, przeczytaj następujące artykuły:

 - [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Kluczowe pojęcia usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz  [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
 - Tworzenie [aplikacja sieci Web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) na platformie Azure. Zanotuj adres URL aplikacji sieci Web, ponieważ będzie on potrzebny w tym samouczku.

### <a name="azure-stack"></a>Azure Stack

 - Użyj usługi Azure Stack w środowisku produkcyjnym lub wdrażanie zestawu Azure Stack Development Kit z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Wdrażanie ASDK można 7 godzin, więc odpowiednio zaplanować.

 - Wdrażanie [usługi App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) usługi PaaS do usługi Azure Stack.
 - [Utwórz plany i oferty](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku usługi Azure Stack.
 - [Utwórz subskrypcję dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku usługi Azure Stack.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, spełniają następujące kryteria, przed rozpoczęciem konfigurowania łączności chmury hybrydowej:

 - Dostępny zewnętrznie publiczny adres IPv4 jest wymagany dla danego urządzenia sieci VPN. Ten adres IP nie może znajdować się za translatorem adresów sieciowych.
 - Wszystkie zasoby są wdrażane w tej samej regionu/lokalizacji.

#### <a name="tutorial-example-values"></a>Samouczek przykładowe wartości

W przykładach w tym samouczku Użyj następujących wartości. Te wartości można użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady. Aby uzyskać więcej informacji na temat bramy sieci VPN, ustawienia ogólnie rzecz biorąc, zobacz [informacje o ustawieniach bramy VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specyfikacje połączenia:

 - **Typ sieci VPN**: Oparte na trasach
 - **Typ połączenia**: Lokacja lokacja (IPsec)
 - **Typ bramy**: Sieć VPN
 - **Nazwa połączenia platformy Azure**: Azure-Gateway-AzureStack-S2SGateway (portalu zostanie automatycznie wypełniona tej wartości)
 - **Nazwa połączenia w usłudze Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (portalu zostanie automatycznie wypełniona tej wartości)
 - **Klucz wspólny**: Wszystkie zgodne ze sprzętem sieci VPN, z takimi samymi wartościami na obu stronach połączenia
 - **Subskrypcja**: Żadnych preferowanych subskrypcji
 - **Grupa zasobów**: Test-Infra

Sieci i podsieci adresów IP:

| Połączenie usługi platformy Azure/Azure Stack | Name (Nazwa) | Podsieć | Adres IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Sieci wirtualnej platformy Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Sieci wirtualnej platformy Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Brama sieci wirtualnej platformy Azure | Azure-Gateway |  |  |
| Brama sieci wirtualnej usługi Azure Stack | AzureStack-Gateway |  |  |
| Usługa Azure publicznego adresu IP | Azure-GatewayPublicIP |  | Ustalona podczas tworzenia |
| Publiczny adres IP usługi Azure Stack | AzureStack-GatewayPublicIP |  | Ustalona podczas tworzenia |
| Bramy sieci lokalnej platformy Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Wartość publiczny adres IP usługi Azure Stack |
| Brama sieci lokalnej usługi Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Wartość platformy Azure, publiczny adres IP |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Tworzenie sieci wirtualnej w globalnej platformy Azure i usługi Azure Stack

Wykonaj następujące kroki, aby utworzyć sieć wirtualną przy użyciu portalu. Można ich użyć [przykładowe wartości](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) Jeśli używasz w tym artykule w ramach samouczka. Jednak jeśli używasz w tym artykule można skonfigurować do środowiska produkcyjnego, Zamień przykładowe ustawienia własnymi wartościami.

> [!IMPORTANT]
> Należy się upewnić, że nie nakładają się adresów IP w przestrzeni adresów sieci wirtualnej platformy Azure lub usługi Azure Stack.

Tworzenie sieci wirtualnej na platformie Azure:

1. Aby nawiązać połączenie za pomocą przeglądarki [witryny Azure portal](http://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **Utwórz zasób**. W **Przeszukaj witrynę marketplace** wprowadź `virtual network`". Znajdź **sieć wirtualna** w na liście wyników, a następnie wybierz **sieci wirtualnej**.
3. Z **wybierz model wdrożenia** , wybierz na liście **usługi Resource Manager**, a następnie wybierz pozycję **Utwórz**.
4. Na **Utwórz sieć wirtualną**, skonfigurować ustawienia sieci wirtualnej. Wymagane pola nazwy są poprzedzone czerwoną gwiazdką.  Wprowadź prawidłową wartość gwiazdka zmienia się zielony znacznik wyboru.

Tworzenie sieci wirtualnej w usłudze Azure Stack:

* Powtórz poprzednie kroki (1-4) przy użyciu usługi Azure Stack **portalu dzierżawcy**.

## <a name="add-a-gateway-subnet"></a>Dodawanie podsieci bramy

Przed połączeniem sieci wirtualnej z bramą, należy utworzyć podsieć bramy dla sieci wirtualnej, którą chcesz nawiązać połączenie. Usługi bramy korzystają adresów IP, które określisz w podsieci bramy.

W [witryny Azure portal](http://portal.azure.com/), przejdź do sieci wirtualnej usługi Resource Manager, gdzie chcesz utworzyć bramę sieci wirtualnej.

1. Wybierz sieć wirtualną, aby otworzyć **sieć wirtualna** strony.
2. W **ustawienia**, wybierz opcję **podsieci**.
3. Na **podsieci** wybierz opcję **+ podsieć bramy** otworzyć **Dodaj podsieć** strony.

    ![Dodaj podsieć bramy](media/azure-stack-solution-hybrid-connectivity/image4.png)

4.  **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością "GatewaySubnet". Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy.
5. Zmiana **zakres adresów** wartości, które są dostarczane do wymagań dotyczących konfiguracji, a następnie wybierz **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Tworzenie bramy sieci wirtualnej na platformie Azure i usługi Azure Stack

Wykonaj następujące kroki, aby Tworzenie bramy sieci wirtualnej na platformie Azure.

1. W lewej części strony portalu wybierz **+**  i wpisz "Brama sieci wirtualnej" w polu wyszukiwania.
2. W **wyniki**, wybierz opcję **bramy sieci wirtualnej**.
3. W **bramy sieci wirtualnej**, wybierz opcję **Utwórz** otworzyć **Tworzenie bramy sieci wirtualnej** strony.
4. Na **Tworzenie bramy sieci wirtualnej**, określ wartości dla bramy Twojej sieci, jak pokazano na **samouczek przykładowe wartości**oraz następujące wartości dodatkowe:

    - **Jednostka SKU**: podstawowe
    - **Virtual Network**: Wybierz sieć wirtualną utworzoną wcześniej. Utworzoną podsieć bramy jest wybierana automatycznie.
    - **Konfiguracja pierwszego adresu IP**:  Jest to publiczny adres IP bramy.
        - Wybierz **Utwórz konfigurację protokołu IP bramy**, która spowoduje przejście do **wybierz publiczny adres IP** strony.
        - Wybierz **+ Utwórz nowe** otworzyć **tworzenie publicznego adresu IP** strony.
        - Wprowadź **nazwa** dla publicznego adresu IP. Pozostaw jednostkę SKU jako **podstawowe**, a następnie wybierz pozycję **OK** Aby zapisać zmiany.

       > [!Note]
       > Obecnie Usługa bramy sieci VPN obsługuje tylko dynamiczne przypisywanie publicznych adresów IP. Jednakże nie oznacza to, że adres IP zmienia się po przypisaniu do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Zmiana rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień, do bramy sieci VPN nie zmieniaj adresu IP.

4. Sprawdź ustawienia bramy.
5. Wybierz **Utwórz** do tworzenia bramy sieci VPN. Ustawienia bramy są prawidłowe, a następnie Kafelek "Wdrażanie bramy sieci wirtualnej" jest wyświetlany na pulpicie nawigacyjnym.

   >[!Note]
   >Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

    Po utworzeniu bramy można wyświetlić adres IP przypisany do niego, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone. Aby uzyskać więcej informacji na temat bramy, wybierz urządzenie.

6. Powtórz poprzednie kroki (1-5) na wdrożenia usługi Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Utwórz bramę sieci lokalnej na platformie Azure i usługi Azure Stack

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. Nadaj lokacji nazwę, która platformy Azure lub usługi Azure Stack można odwoływać się do, a następnie określ:

- Adres IP lokalnego urządzenia sieci VPN, tworzonymi połączenia.
- Prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej.

  >[!Note]
  >Jeśli zmiany sieci lokalnej lub muszą zmienić publiczny adres IP urządzenia sieci VPN, można łatwo zaktualizować wartości później.

1. W portalu, wybierz **+ Utwórz zasób**.
2. W polu wyszukiwania wprowadź **bramy sieci lokalnej**, a następnie wybierz **Enter** do wyszukania. Spowoduje to zwrócenie listy wyników.
3. Wybierz **bramy sieci lokalnej**, a następnie wybierz **Utwórz** otworzyć **Utwórz bramę sieci lokalnej** strony.
4. Na **Utwórz bramę sieci lokalnej**, określ wartości dla swojej bramy sieci lokalnej za pomocą naszych **samouczek przykładowe wartości**. Obejmują następujące dodatkowe wartości.

    - **Adres IP**: Jest to publiczny adres IP urządzenia sieci VPN, z którym chcesz, Azure lub usługi Azure Stack, aby nawiązać połączenie. Określ prawidłowy publiczny adres IP, który nie jest poza NAT, więc Azure może osiągnąć adres. Jeśli nie masz teraz adresu IP, można użyć wartości z przykładu jako symbol zastępczy, ale trzeba będzie powrót i zamiana zastępczego publiczny adres IP urządzenia sieci VPN. Azure nie może połączyć się z urządzeniem, dopóki nie podasz prawidłowy adres.
    - **Przestrzeń adresowa**: Jest to zakres adresów sieci, która reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, czy należy określić zakresy nie pokrywają się z zakresami innych sieci, które chcesz się połączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. Użyj własnych wartości, jeśli chcesz nawiązać połączenie z lokacją lokalną nie Przykładowa wartość.
    - **Skonfiguruj ustawienia protokołu BGP**: Należy używać tylko w przypadku konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
    - **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
    - **Grupa zasobów**: Wybierz grupę zasobów, do którego chcesz używać. Możesz utworzyć nową grupę zasobów lub wybierz jedną, która została już utworzona.
    - **Lokalizacja**: Wybierz lokalizację, w której ten obiekt zostanie utworzony w. Możesz wybrać tej samej lokalizacji, która sieci wirtualnej znajduje się w, ale nie, musisz to zrobić.
5. Po zakończeniu określania wymagane wartości, wybierz **Utwórz** do tworzenia bramy sieci lokalnej.
6. Powtórz te kroki (1-5) dla wdrożenia usługi Azure Stack.

## <a name="configure-your-connection"></a>Konfigurowanie połączenia

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. Urządzenie sieci VPN, które można skonfigurować jest określany jako połączenie. Aby skonfigurować połączenie, potrzebne są:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN przy użyciu witryny Azure portal, przejdź do bramy sieci wirtualnej, a następnie wybierz nazwę bramy.

Poniższe kroki umożliwiają tworzenie połączenia sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej i urządzeniem sieci VPN w środowisku lokalnym.

1. W witrynie Azure portal wybierz **+ Utwórz zasób**.
2. Wyszukaj **połączeń**.
3. W **wyniki**, wybierz opcję **połączeń**.
4. Na **połączenia**, wybierz opcję **Utwórz**.
5. Na **Utwórz połączenie**, skonfiguruj następujące ustawienia:

    - **Typ połączenia**: Wybierz pozycję lokacja lokacja (IPSec).
    - **Grupa zasobów**: Wybierz grupę zasobów testowych.
    - **Brama sieci wirtualnej**: Wybierz bramę sieci wirtualnej, który został utworzony.
    - **Brama sieci lokalnej**: Wybierz utworzoną bramę sieci lokalnej.
    - **Nazwa połączenia**: To jest automatycznie wypełniane, używając wartości z dwóch bram.
    - **Klucz wspólny**: Ta wartość musi odpowiadać wartości, którego używasz dla lokalnego urządzenia sieci VPN. W przykładzie samouczka użyto wartości "abc123", ale można i należy użyć bardziej złożonej. Ważne jest, że ta wartość musi być taka sama jak wartość można określić podczas konfigurowania urządzenia sieci VPN.
    - Wartości **subskrypcji**, **grupy zasobów**, i **lokalizacji** zostały usunięte.

6. Wybierz **OK** utworzyć połączenie.

Możesz zobaczyć połączenie w **połączeń** stronę bramy sieci wirtualnej. Stan zmieni się z *nieznany* do *łączenie*, a następnie *Powodzenie*.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
