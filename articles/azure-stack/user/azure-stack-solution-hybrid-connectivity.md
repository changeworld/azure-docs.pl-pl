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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 8b5f59d3fea402efa50bdafd7fc0439a93051e69
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Samouczek: Konfigurowanie połączenia hybrydowe chmury Azure i stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można uzyskiwać dostęp do zasobów z zabezpieczeniami na platformie Azure globalnych i stosu Azure przy użyciu wzorca połączenie hybrydowe. 

W tym samouczku utworzysz środowisku próbki do:

> [!div class="checklist"]
> - Zachowaj danych w sieci lokalnej dla prywatności lub przepisami dotyczącymi działalności, ale mają dostęp do globalnego zasobów platformy Azure.
> - Obsługa starszych systemu podczas używania wdrażania aplikacji skalowana w chmurze i zasobów na platformie Azure globalne.

## <a name="prerequisites"></a>Wymagania wstępne

Kilka składników są wymagane do utworzenia hybrydowego wdrożenia łączności i może zająć trochę czasu, aby przygotować. 

Partner OEM/sprzętu Azure może wdrożyć produkcyjnych Azure stosu, a wszyscy użytkownicy mogą wdrażać ASDK. Operator stosu Azure musi również wdrożenia usługi App Service, tworzyć plany i ofert Utwórz subskrypcję dzierżawy i dodanie obrazu systemu Windows Server 2016. 

Jeśli masz już niektóre z tych składników, upewnij się, że spełniają one wymagania przed rozpoczęciem.

W tym temacie założono również, czy masz pewna znajomość Azure oraz Azure stosu. Jeśli chcesz dowiedzieć się więcej, aby kontynuować, należy koniecznie zaczynać się w tych tematach:
 - [Wprowadzenie do platformy Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Kluczowe założenia Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure
 - Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
 - Utwórz [sieci Web aplikacji](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) na platformie Azure. Zanotuj URL nowej aplikacji sieci Web, które jest używane później.

### <a name="azure-stack"></a>Azure Stack
 - Użyj produkcyjnego stosu Azure lub wdrożyć Azure stosu Development Kit linki znajdują się poniżej:
    - https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 
    - Instalacja zwykle ma kilka godzin, aby zakończyć, więc odpowiednio zaplanować.
 - Wdrażanie [usługi aplikacji](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS usług Azure stosu. 
 - [Tworzenie planu/ofert](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) w środowisku Azure stosu. 
 - [Utwórz subskrypcję dzierżawy](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) w środowisku Azure stosu. 


### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

 - Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN. Ten adres IP nie może się znajdować za translatorem adresów sieciowych.
 - Upewnij się, że wszystkie zasoby są wdrażane w tej samej regionu/lokalizacji.

#### <a name="example-values"></a>Przykładowe wartości

W przykładach w tym artykule są stosowane następujące wartości. Te wartości można użyć do tworzenia środowiska testowego lub odwołać się do nich, aby lepiej zrozumieć przykłady w tym artykule. Aby uzyskać więcej informacji o typach bram sieci VPN, zobacz [About VPN Gateway configuration settings (Informacje o ustawieniach konfiguracji bramy sieci VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specyfikacje połączenia:
 - **Typ sieci VPN**: opartej na trasach
 - **Typ połączenia**: lokacja lokacja (IPsec)
 - **Typ bramy**: sieci VPN
 - **Nazwa połączenia Azure**: Azure-brama-AzureStack-S2SGateway (portalu spowoduje automatyczne wypełnianie tej wartości)
 - **Nazwa połączenia w usłudze Azure stosu**: AzureStack-brama-Azure-S2SGateway (portalu spowoduje automatyczne wypełnianie tej wartości)
 - **Klucz współużytkowany**: wszystkie zgodne ze sprzętem sieci VPN, z takimi samymi wartościami po obu stronach połączenia
 - **Subskrypcja**: dowolne preferowane subskrypcji
 - **Grupa zasobów**: Test Infra

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

> [!note]  
> Należy upewnij się, czy jest bez nakładania się adresy IP w przestrzeni adresów sieci wirtualnej platformy Azure lub stos Azure. 

Aby utworzyć sieć wirtualną w modelu wdrażania usługi Resource Manager przy użyciu portalu Azure. Użyj [przykładowych wartości](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values), jeśli wykonujesz te kroki w ramach samouczka. Jeśli nie wykonujesz tych kroków w ramach samouczka, koniecznie zastąp te wartości własnymi. 

1. Przejdź w przeglądarce do witryny [Azure Portal](http://portal.azure.com/) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **Utwórz zasób**. W **wyszukiwania portalu marketplace** wprowadź `virtual network`". Zlokalizuj **sieci wirtualnej** na liście zwracanych i Otwórz **sieci wirtualnej** strony.
3. W dolnej części strony sieci wirtualnej z **wybierz model wdrożenia** wybierz **Resource Manager**, a następnie wybierz **Utwórz**. Spowoduje to otwarcie strony „Tworzenie sieci wirtualnej”.
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe.
5. Powtórz te kroki od **portalu dzierżawcy** stosu Azure.

## <a name="add-a-gateway-subnet"></a>Dodawanie podsieci bramy

Przed połączeniem sieci wirtualnej z bramą należy najpierw utworzyć podsieć bramy sieci wirtualnej, z którą ma zostać nawiązane połączenie. Usługi bramy korzystają z adresów IP określonych w podsieci bramy.

W [portalu](http://portal.azure.com/) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.

1. W **ustawienia** sekcji Twojej sieci wirtualnej wybierz pozycję **podsieci** rozszerzenia **podsieci** strony.
2. Na **podsieci** wybierz pozycję **+ podsieci bramy** otworzyć **Dodaj podsieć** strony.

    ![](media/azure-stack-solution-hybrid-connectivity/image4.png)

3. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana dla platformy Azure do podsieci jest rozpoznawana jako podsieć bramy. Dostosuj automatycznie wypełnianej **zakres adresów** wartości odpowiadające wymagania dotyczące konfiguracji, następnie wybierz **OK** w dolnej części strony, aby utworzyć podsieć.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Tworzenie bramy sieci wirtualnej platformy Azure i Azure stosu

1. W lewej części strony portalu, wybierz + i wprowadź "Brama sieci wirtualnej" w wyszukiwaniu. W **wyniki**Znajdź i zaznacz **Brama sieci wirtualnej**.
2. W dolnej części **Brama sieci wirtualnej** wybierz pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.
3. Na **Utwórz bramę sieci wirtualnej** określ wartości dla bramy sieci wirtualnej, zgodnie z opisem w przykładowe wartości, a także dodatkowe wartości, które zostały szczegółowo opisane.
    - **Jednostka SKU**: podstawowe
    - **Sieć wirtualna**: Wybierz wcześniej utworzoną sieć wirtualną. Automatycznie wybierze podsieć bramy, który został utworzony wcześniej. 
    - **Pierwszy konfiguracji IP**: jest to publiczny adres IP bramy. 
        - Kliknij przycisk **konfiguracji IP bramy Utwórz** i nastąpi przekierowanie do **wybierz publiczny adres IP** strony.
        - Kliknij przycisk **+ Utwórz nowy** otworzyć **tworzenie publicznego adresu IP** strony.
        - Wprowadź **nazwa** dla publicznego adresu IP. Pozostaw SKU jako **podstawowe**, a następnie wybierz pozycję **OK** u dołu tej strony, aby zapisać zmiany.

    > [!note]  
    > Brama sieci VPN aktualnie obsługuje tylko Alokacja adresów dynamicznych publicznego adresu IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

4. Sprawdź poprawność ustawień. 
5. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy sieci VPN. Zostanie sprawdzona poprawność ustawień, a na pulpicie nawigacyjnym pojawi się kafelek „Wdrażanie bramy sieci wirtualnej”. Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

    Po utworzeniu bramy sprawdź adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone. Możesz wybrać podłączonego urządzenia (bramy sieci wirtualnej), aby wyświetlić więcej informacji.
6. Powtórz te kroki dla wdrożenia usługi Azure stosu.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Utwórz bramę sieci lokalnej w stosie Azure i Azure

Brama sieci lokalnej zazwyczaj odwołuje się do lokalizacji lokalnej. Witryny nadaj nazwę za pomocą którego Azure lub stosu Azure można odwoływać się do niego, a następnie podaj adres IP lokalnego urządzenia sieci VPN, z którym będą tworzone jest połączenie. Określ również prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej. W przypadku zmiany sieci lokalnej lub jeśli trzeba zmienić publiczny adres IP urządzenia sieci VPN, można łatwo zaktualizować wartości później.

1. W portalu, wybierz **+ Utwórz zasób**.
2. W polu wyszukiwania wprowadź **bramy sieci lokalnej**, naciśnij klawisz **Enter** do wyszukiwania. Spowoduje to zwrócenie listy wyników. Kliknij przycisk **bramy sieci lokalnej**, a następnie wybierz pozycję **Utwórz** przycisk, aby otworzyć **Utwórz bramę sieci lokalnej** strony.
3. Na **tworzenia strony bramy sieci lokalnej**, określ wartości dla bramy sieci lokalnej, zgodnie z opisem w naszym przykładzie wartości, a także dodatkowe wartości, które zostały szczegółowo opisane.
    - **Adres IP**: jest to publiczny adres IP urządzenia sieci VPN, które chcesz Azure lub stos Azure, aby nawiązać połączenie. Określ prawidłowy publiczny adres IP. Adres IP nie może znajdować się za translatorem adresów sieciowych i musi być dostępny za pomocą usługi Azure. Jeśli w danej chwili nie masz adresu IP, możesz użyć wartości widocznych w przykładzie, ale konieczny będzie powrót i zamiana zastępczego adresu IP na publiczny adres IP urządzenia sieci VPN. W przeciwnym razie usługa Azure nie będzie mogła nawiązać połączenia.
    - **Przestrzeń adresowa** odwołuje się do zakresów adresów sieci, które reprezentuje sieć lokalna. Można dodać wiele zakresów przestrzeni adresów. Upewnij się, że określone w tym miejscu zakresy nie pokrywają się z zakresami innych sieci, z którymi chcesz się łączyć. Platforma Azure będzie kierować określony zakres adresów pod adres IP lokalnego urządzenia sieci VPN. Jeśli chcesz nawiązać połączenie z lokacji lokalnej nie pokazano w przykładzie wartości w tym miejscu użyć własne wartości.
    - **Skonfiguruj ustawienia protokołu BGP**: Użyj tylko podczas konfigurowania protokołu BGP. W przeciwnym razie nie wybieraj jej.
    - **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja.
    - **Grupa zasobów**: Wybierz grupę zasobów, do którego chcesz używać. Możesz utworzyć nową grupę zasobów lub wybrać już utworzoną.
    - **Lokalizacja**: Wybierz ten obiekt zostanie utworzony w lokalizacji. Można wybrać tę samą lokalizację, w której znajduje się sieć wirtualna, ale nie jest to konieczne.
4. Po wybraniu wartości wybierz **Utwórz** znajdujący się u dołu strony, aby utworzyć bramę sieci lokalnej.
5. Powtórz te kroki dla wdrożenia usługi Azure stosu.

## <a name="configure-your-connection"></a>Skonfiguruj połączenie

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku należy skonfigurować urządzenie sieci VPN, znane jako połączenie. Podczas konfigurowania połączenia, potrzebne są następujące elementy:
    - Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
    - Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć adres publiczny adres IP bramy sieci VPN przy użyciu portalu Azure, przejdź do bramy sieci wirtualnej, a następnie wybierz nazwę bramy.
Utwórz połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej a lokalnym urządzeniem sieci VPN.
1. W portalu, wybierz **+ Utwórz zasób**.
2. W polu wyszukiwania wprowadź **połączeń**, naciśnij klawisz **Enter** do wyszukiwania. Spowoduje to zwrócenie listy wyników. Kliknij przycisk **połączeń**, następnie kliknij przycisk Utwórz, aby otworzyć **tworzenie połączeń** strony.
3. Na **tworzenie połączeń** Ustaw wartości dla połączenia.
     - Podstawy:
        1. **Typ połączenia**: Wybierz lokacja lokacja (IPSec).
        2. **Grupa zasobów**: (wybierz grupę zasobów testową)
     - Ustawienia:
        1. **Brama sieci wirtualnej**: Wybierz wcześniej utworzony bramy sieci wirtualnej.
        2. **Bramy sieci lokalnej**: Wybierz wcześniej utworzony bramy sieci lokalnej. 
        3. **Nazwa połączenia**: to automatycznie wypełnić wartościami z dwóch bram. 
        4. **Wspólny klucz**: w tym miejscu wartość musi odpowiadać wartości używasz lokalnych lokalnego urządzenia sieci VPN. W przykładzie użyto wartości „abc123”, ale można (i należy) użyć bardziej złożonej wartości. Pamiętaj o tym, że wartość podana w tym miejscu musi być taka sama, jak wartość podana podczas konfigurowania urządzenia sieci VPN.
    - Pozostałe wartości w polach **Subskrypcja**, **Grupa zasobów** i **Lokalizacja** są ustalone.
4. Kliknij przycisk **OK**, aby utworzyć połączenie. Tworzenie połączenia flash na ekranie są widoczne.
5. Można wyświetlić połączenia w ** strona połączenia bramy sieci wirtualnej. Stan zmieni się z **nieznany** do **łączenie**, a następnie **zakończyło się pomyślnie**.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
