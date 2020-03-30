---
title: Wdrażanie modułu równoważenia obciążenia z dostępem do Internetu za pomocą szablonu platformy IPv6 —
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak wdrożyć obsługę IPv6 dla modułu Równoważenia obciążenia platformy Azure i maszyn wirtualnych z równoważenia obciążenia przy użyciu szablonu platformy Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, lazurowy moduł równoważenia obciążenia, podwójny stos, publiczny adres IP, natywny ipv6, mobilny, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045452"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Wdrażanie rozwiązania z modułem równoważenia obciążenia z internetem przy użyciu iPv6 przy użyciu szablonu

> [!div class="op_single_selector"]
> * [Powershell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablonu](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>W tym artykule opisano funkcję wprowadzającą IPv6, która umożliwia podstawowe moduły równoważenia obciążenia w celu zapewnienia łączności IPv4 i IPv6. Kompleksowa łączność IPv6 jest teraz dostępna z [technologiami wirtualnymi IPv6 dla platformy Azure,](../virtual-network/ipv6-overview.md) które integrują łączność IPv6 z sieciami wirtualnymi i zawierają najważniejsze funkcje, takie jak reguły sieciowej grupy zabezpieczeń IPv6, routing zdefiniowany przez użytkownika IPv6, podstawowe i standardowe równoważenie obciążenia IPv6 i inne.  IPv6 dla platformy Azure VNETs jest zalecanym standardem dla aplikacji IPv6 na platformie Azure. Zobacz [IPv6 dla wdrażania sieci wirtualnej sieci wirtualnej platformy Azure](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie równoważenia obciążenia wdrażane przy użyciu przykładowego szablonu opisanego w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

W tym scenariuszu utworzysz następujące zasoby platformy Azure:

* interfejs sieci wirtualnej dla każdej maszyny Wirtualnej z przypisanymi adresami IPv4 i IPv6
* internetowy moduł równoważenia obciążenia z iPv4 i publicznym adresem IP IPv6
* dwie reguły równoważenia obciążenia w celu mapowania publicznych vipów na prywatne punkty końcowe
* zestaw dostępności zawierający dwie maszyny wirtualne
* dwie maszyny wirtualne (maszyny wirtualne)

## <a name="deploying-the-template-using-the-azure-portal"></a>Wdrażanie szablonu przy użyciu portalu Azure

Ten artykuł odwołuje się do szablonu opublikowanego w galerii [szablonów szybki start platformy Azure.](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) Szablon można pobrać z galerii lub uruchomić wdrożenie na platformie Azure bezpośrednio z galerii. W tym artykule przyjęto założenie, że szablon został pobrany na komputer lokalny.

1. Otwórz witrynę Azure portal i zaloguj się przy za pomocą konta, które ma uprawnienia do tworzenia maszyn wirtualnych i zasobów sieciowych w ramach subskrypcji platformy Azure. Ponadto, chyba że używasz istniejących zasobów, konto wymaga uprawnień do utworzenia grupy zasobów i konta magazynu.
2. Kliknij "+Nowy" z menu, a następnie wpisz "szablon" w polu wyszukiwania. Wybierz "Wdrożenie szablonu" z wyników wyszukiwania.

    ![lb-ipv6-portal-krok2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. W bloku Wszystko kliknij przycisk "Wdrożenie szablonu".

    ![lb-ipv6-portal-krok3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kliknij "Utwórz".

    ![lb-ipv6-portal-krok4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kliknij "Edytuj szablon". Usuń istniejącą zawartość i skopiuj/wklej całą zawartość pliku szablonu (aby uwzględnić początek i koniec { }), a następnie kliknij przycisk "Zapisz".

    > [!NOTE]
    > Jeśli używasz programu Microsoft Internet Explorer, podczas wklejenia jest odbierane okno dialogowe z prośbą o zezwolenie na dostęp do schowka systemu Windows. Kliknij "Zezwól na dostęp".

    ![lb-ipv6-portal-krok5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kliknij "Edytuj parametry". W parametry bloku, określ wartości według wskazówek w sekcji Parametry szablonu sekcji, a następnie kliknij przycisk "Zapisz", aby zamknąć parametry bloku. W bloku wdrożenia niestandardowego wybierz subskrypcję, istniejącą grupę zasobów lub utwórz jej. Jeśli tworzysz grupę zasobów, wybierz lokalizację dla grupy zasobów. Następnie kliknij pozycję **Warunki prawne**, a następnie kliknij pozycję **Kup** dla warunków prawnych. Platforma Azure rozpoczyna wdrażanie zasobów. Wdrożenie wszystkich zasobów zajmuje kilka minut.

    ![lb-ipv6-portal-krok6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Aby uzyskać więcej informacji na temat tych parametrów, zobacz [szablon parametrów i zmiennych](#template-parameters-and-variables) sekcji w dalszej części tego artykułu.

7. Aby wyświetlić zasoby utworzone przez szablon, kliknij przycisk Przeglądaj, przewiń listę w dół, aż zobaczysz "Grupy zasobów", a następnie kliknij ją.

    ![lb-ipv6-portal-krok7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. W bloku Grupy zasobów kliknij nazwę grupy zasobów określoną w kroku 6. Zobaczysz listę wszystkich zasobów, które zostały wdrożone. Jeśli wszystko poszło dobrze, należy powiedzieć "Udało się" w obszarze "Ostatnie wdrożenie". Jeśli nie, upewnij się, że konto, którego używasz, ma uprawnienia do tworzenia niezbędnych zasobów.

    ![lb-ipv6-portal-krok8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Jeśli przeglądasz grupy zasobów natychmiast po wykonaniu kroku 6, "Ostatnie wdrożenie" wyświetli stan "Wdrażanie" podczas wdrażania zasobów.

9. Kliknij "myIPv6PublicIP" na liście zasobów. Widać, że ma adres IPv6 pod adresem IP i że jego nazwa DNS jest wartością określoną dla parametru dnsNameforIPv6LbIP w kroku 6. Ten zasób jest publiczny adres IPv6 i nazwa hosta, który jest dostępny dla klientów internetowych.

    ![lb-ipv6-portal-krok9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Po pomyślnym wdrożeniu szablonu można sprawdzić poprawność łączności, wykonując następujące zadania:

1. Zaloguj się do witryny Azure portal i połącz się z każdym z maszyn wirtualnych utworzonych przez wdrożenie szablonu. Jeśli wdrożono maszynę wirtualną systemu Windows Server, uruchom ipconfig /all z wiersza polecenia. Widać, że maszyny wirtualne mają zarówno adresy IPv4, jak i IPv6. Jeśli wdrożono maszyny wirtualne z systemem Linux, musisz skonfigurować system operacyjny Linux do odbierania dynamicznych adresów IPv6 przy użyciu instrukcji podanych dla dystrybucji systemu Linux.
2. Z klienta podłączonego do Internetu IPv6 zainicjuj połączenie z publicznym adresem IPv6 modułu równoważenia obciążenia. Aby potwierdzić, że moduł równoważenia obciążenia między dwoma maszynami wirtualnymi, można zainstalować serwer sieci web, taki jak internetowe usługi informacyjne firmy Microsoft (IIS) na każdej z maszyn wirtualnych. Domyślna strona sieci web na każdym serwerze może zawierać tekst "Server0" lub "Server1", aby jednoznacznie go zidentyfikować. Następnie otwórz przeglądarkę internetową na kliencie połączonym z Internetem IPv6 i przejdź do nazwy hosta określonej dla parametru dnsNameforIPv6LbIP modułu równoważenia obciążenia, aby potwierdzić kompleksową łączność IPv6 z każdą maszyną wirtualną. Jeśli widzisz tylko stronę internetową tylko z jednego serwera, może być konieczne wyczyszczenie pamięci podręcznej przeglądarki. Otwórz wiele prywatnych sesji przeglądania. Powinna zostać wyświetlena odpowiedź z każdego serwera.
3. Z klienta podłączonego do Internetu IPv4 zainicjuj połączenie z publicznym adresem IPv4 modułu równoważenia obciążenia. Aby potwierdzić, że moduł równoważenia obciążenia jest równoważenie obciążenia dwóch maszyn wirtualnych, można przetestować przy użyciu IIS, jak wyszczególniono w kroku 2.
4. Z każdej maszyny wirtualnej zainicjuj połączenie wychodzące z urządzeniem internetowym połączonym z IPv6 lub IPv4. W obu przypadkach źródłowy adres IP widziany przez urządzenie docelowe to publiczny adres IPv4 lub IPv6 modułu równoważenia obciążenia.

> [!NOTE]
> ICMP dla IPv4 i IPv6 jest zablokowany w sieci platformy Azure. W rezultacie narzędzia ICMP, takie jak ping, zawsze zawodzą. Aby przetestować łączność, użyj alternatywy TCP, takiej jak TCPing lub polecenie cmdlet programu PowerShell Test-NetConnection. Należy zauważyć, że adresy IP wyświetlane na diagramie są przykładami wartości, które mogą być widoczne. Ponieważ adresy IPv6 są przypisywane dynamicznie, odbierane adresy będą się różnić i mogą się różnić w zależności od regionu. Ponadto jest często dla publicznego adresu IPv6 na moduł równoważenia obciążenia, aby rozpocząć z innym prefiksem niż prywatne adresy IPv6 w puli zaplecza.

## <a name="template-parameters-and-variables"></a>Parametry i zmienne szablonu

Szablon usługi Azure Resource Manager zawiera wiele zmiennych i parametrów, które można dostosować do swoich potrzeb. Zmienne są używane dla stałych wartości, które nie mają być zmieniane przez użytkownika. Parametry są używane dla wartości, które użytkownik ma podać podczas wdrażania szablonu. Przykładowy szablon jest skonfigurowany dla scenariusza opisanego w tym artykule. Można dostosować to do potrzeb środowiska.

Przykładowy szablon użyty w tym artykule zawiera następujące zmienne i parametry:

| Parametr / zmienna | Uwagi |
| --- | --- |
| adminUsername |Określ nazwę konta administratora używanego do logowania się do maszyn wirtualnych. |
| adminPassword |Określ hasło dla konta administratora używanego do logowania się do maszyn wirtualnych. |
| dnsNameforIPv4LbIP |Określ nazwę hosta DNS, którą chcesz przypisać jako publiczną nazwę modułu równoważenia obciążenia. Ta nazwa jest rozpoznawana na publiczny adres IPv4 modułu równoważenia obciążenia. Nazwa musi być mała i zgodna z wyrażeniem: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Określ nazwę hosta DNS, którą chcesz przypisać jako publiczną nazwę modułu równoważenia obciążenia. Ta nazwa jest rozpoznawana na publiczny adres IPv6 modułu równoważenia obciążenia. Nazwa musi być mała i zgodna z wyrażeniem: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$. Może to być taka sama nazwa jak adres IPv4. Gdy klient wysyła kwerendę DNS dla tej nazwy Platforma Azure zwróci rekordy A i AAAA, gdy nazwa jest udostępniana. |
| Poprawka vmNamePrefix |Określ prefiks nazwy maszyny Wirtualnej. Szablon dołącza liczbę (0, 1 itp.) do nazwy podczas tworzenia maszyn wirtualnych. |
| nicNamePrefix |Określ prefiks nazwy interfejsu sieciowego. Szablon dołącza liczbę (0, 1 itp.) do nazwy podczas tworzenia interfejsów sieciowych. |
| storageAccountName |Wprowadź nazwę istniejącego konta magazynu lub określ nazwę nowego konta, które ma zostać utworzone przez szablon. |
| dostępnośćSetName |Wprowadź następnie nazwę zestawu dostępności do użycia z maszynami wirtualnymi |
| addressPrefix |Prefiks adresu używany do definiowania zakresu adresów sieci wirtualnej |
| podszecina |Nazwa podsieci utworzonej dla sieci wirtualnej |
| subnetPrefix |Prefiks adresu używany do definiowania zakresu adresów podsieci |
| vnetName |Określ nazwę sieci wirtualnej używanej przez maszyny wirtualne. |
| ipv4PrivateIPAddressType |Metoda alokacji używana dla prywatnego adresu IP (statycznego lub dynamicznego) |
| ipv6PrivateIPAddressType |Metoda alokacji używana dla prywatnego adresu IP (Dynamic). Protokół IPv6 obsługuje tylko alokację dynamiczną. |
| liczbaInstancje |Liczba wystąpień z równoważenia obciążenia wdrożonych przez szablon |
| ipv4PublicIPAddressName |Określ nazwę DNS, której chcesz używać do komunikowania się z publicznym adresem IPv4 modułu równoważenia obciążenia. |
| ipv4PublicIPAddressType |Metoda alokacji używana dla publicznego adresu IP (statycznego lub dynamicznego) |
| Ipv6PublicIPAddressName |Określ nazwę DNS, której chcesz używać do komunikowania się z publicznym adresem IPv6 modułu równoważenia obciążenia. |
| ipv6PublicIPAddressType |Metoda alokacji używana dla publicznego adresu IP (Dynamiczny). Protokół IPv6 obsługuje tylko alokację dynamiczną. |
| lbName (Nazwa funta) |Określ nazwę modułu równoważenia obciążenia. Ta nazwa jest wyświetlana w portalu lub używana podczas odwoływania się do niej za pomocą polecenia CLI lub PowerShell. |

Pozostałe zmienne w szablonie zawierają wartości pochodne, które są przypisywane podczas tworzenia zasobów przez platformę Azure. Nie należy zmieniać tych zmiennych.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać składnię JSON i właściwości modułu równoważenia obciążenia w szablonie, zobacz [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
