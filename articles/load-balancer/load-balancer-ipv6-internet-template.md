---
title: Wdrażanie dostępnego z Internetu modułu równoważenia obciążenia przy użyciu protokołu IPv6 — szablon platformy Azure
titlesuffix: Azure Load Balancer
description: Jak wdrożyć obsługę protokołu IPv6 dla usługi Azure Load Balancer i równoważenia obciążenia maszyn wirtualnych.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: Protokół IPv6, usługa azure load balancer, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, aplikację mobilną, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 4a8c7309a07238ef3410e42c3d631ad525f023cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216859"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Wdróż rozwiązanie do równoważenia obciążenia dostępnego z Internetu przy użyciu protokołu IPv6 przy użyciu szablonu

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)



Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązanie do równoważenia obciążenia wdrażane przy użyciu przykładowy szablon opisanych w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

W tym scenariuszu utworzysz następujących zasobów platformy Azure:

* Interfejs sieci wirtualnej, dla każdej maszyny Wirtualnej przy użyciu adresów IPv4 i IPv6 przypisany
* Moduł równoważenia obciążenia dostępnego z Internetu za pomocą protokołów IPv4 i IPv6 publiczny adres IP
* Dwa obciążenia równoważenia reguł mapowania publicznych adresów VIP do prywatnych punktów końcowych
* Zestawu dostępności zawierającego dwie maszyny wirtualne
* Dwie maszyny wirtualne (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Wdrażanie szablonu przy użyciu witryny Azure portal

W tym artykule odwołuje się do szablonu, który został opublikowany w [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galerii. Możesz pobrać szablon z galerii lub uruchamianie wdrożenia na platformie Azure bezpośrednio z galerii. W tym artykule przyjęto założenie, że szablon został pobrany na komputer lokalny.

1. Otwórz Azure portal i zaloguj się przy użyciu konta które ma uprawnienia do tworzenia maszyn wirtualnych i zasobów sieciowych w ramach subskrypcji platformy Azure. Ponadto chyba, że korzystasz z istniejących zasobów, konto wymaga uprawnień do utworzenia grupy zasobów i konto magazynu.
2. Kliknij przycisk "+ New" z menu, a następnie wpisz ciąg "szablon" w polu wyszukiwania. Wybierz pozycję "Wdrożenie szablonu" z wyników wyszukiwania.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. W obszarze wszystko bloku, kliknij przycisk "Wdrożenie szablonu".

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kliknij przycisk "Utwórz".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kliknij przycisk "Edytuj szablon". Usuń istniejącą zawartość i kopiowania/wklejania w całą zawartość pliku szablonu (Aby dołączyć rozpoczęcia i zakończenia {}), a następnie kliknij przycisk "Zapisz".

    > [!NOTE]
    > Jeśli używasz programu Microsoft Internet Explorer możesz wkleić wyświetlane okno dialogowe z pytaniem, aby zezwolić na dostęp do Schowka Windows. Kliknij opcję "Zezwalaj na dostęp".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kliknij pozycję "Edytuj parametry". W bloku parametry określ wartości zgodnie ze wskazówkami w sekcji parametrów szablonu, a następnie kliknij przycisk "Zapisz", aby zamknąć blok parametrów. W bloku wdrożenia niestandardowego wybierz swoją subskrypcję istniejącą grupę zasobów lub utworzyć nowe. Jeśli tworzysz grupę zasobów, wybierz lokalizację dla grupy zasobów. Następnie kliknij przycisk **postanowienia prawne**, następnie kliknij przycisk **zakupu** na postanowienia prawne. Azure rozpoczyna wdrażania zasobów. Trwa kilka minut, aby wdrożyć wszystkie zasoby.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Aby uzyskać więcej informacji na temat tych parametrów, zobacz [zmienne i parametry szablonu](#template-parameters-and-variables) sekcję w dalszej części tego artykułu.

7. Aby wyświetlić zasoby utworzone przez szablon, kliknij przycisk Przeglądaj, przewiń w dół listy, dopóki nie można wyświetlić "Grupy zasobów", a następnie kliknij go.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. W bloku grupy zasobów kliknij nazwę grupy zasobów, które określiłeś w kroku 6. Możesz wyświetlić listę wszystkich zasobów, które zostały wdrożone. Jeśli wszystko poszło dobrze, wyświetlony komunikat "Powodzenie" w obszarze "Ostatniego wdrożenia." Jeśli nie, upewnij się, że konto, którego używasz, ma uprawnienia do tworzenia niezbędnych zasobów.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Jeśli natychmiast po ukończeniu kroku 6 możesz przeglądać grup zasobów, "Ostatniego wdrożenia" będzie wyświetlany stan "Wdrażanie", podczas wdrażania zasobów.

9. Kliknij przycisk "myIPv6PublicIP" na liście zasobów. Zobaczysz, że ma on adres IPv6 w obszarze adres IP i nazwy DNS to wartość, która została określona dla parametru dnsNameforIPv6LbIP w kroku 6. Ten zasób to publiczny IPv6 adresów i nazwę który jest dostępny dla klientów internetowych.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Zweryfikuj łączność

Po pomyślnym wdrożeniu szablonu, można sprawdzić łączność, wykonując następujące czynności:

1. Zaloguj się do witryny Azure portal i połączyć się z poszczególnymi maszynami wirtualnymi, utworzone przez wdrożenie szablonu. Jeśli wdrożono maszynę Wirtualną serwera systemu Windows, uruchom polecenie ipconfig/wszystko w wierszu polecenia. Zobaczysz, że maszyny wirtualne mają adresy IPv4 i IPv6. Jeśli wdrożono maszyny wirtualne systemu Linux, należy skonfigurować systemu operacyjnego Linux, aby odbierać dynamicznych adresów IPv6 wykonując instrukcje podane dla Twojej dystrybucji systemu Linux.
2. Z połączonym z Internetem IPv6 klienta należy zainicjować połączenie publiczny adres IPv6 modułu równoważenia obciążenia. Aby upewnić się, że moduł równoważenia obciążenia jest równoważenie między dwiema maszynami wirtualnymi, takich jak Microsoft Internet Information Services (IIS) serwera sieci web można zainstalować na wszystkich maszynach wirtualnych. Domyślna strona internetowa na każdym serwerze może zawierać tekst "Server0" lub "Server1", umożliwiającą jej jednoznaczną identyfikację. Następnie otwórz przeglądarkę internetową na komputerze klienckim połączonym z Internetem IPv6 i wskaż nazwa hosta określona dla parametru dnsNameforIPv6LbIP modułu równoważenia obciążenia, aby potwierdzić łączność IPv6 end-to-end do każdej maszyny Wirtualnej. Jeśli widzisz tylko strony sieci web z tylko jednego serwera, może być konieczne wyczyszczenie pamięci podręcznej przeglądarki. Otwórz wiele sesji przeglądania prywatnego. Powinna pojawić się odpowiedź, z każdego serwera.
3. W kliencie podłączonej do Internetu IPv4 należy zainicjować połączenie jako publiczny adres IPv4 modułu równoważenia obciążenia. Aby potwierdzić, że moduł równoważenia obciążenia jest dwóch maszyn wirtualnych równoważenia obciążenia, można sprawdzić za pomocą usług IIS, zgodnie z opisem w kroku 2.
4. Z każdej maszyny Wirtualnej należy zainicjować połączenie wychodzące do urządzenia z systemem IPv6 i IPv4 podłączonej do Internetu. W obu przypadkach źródłowy adres IP widzianych przez urządzenie docelowe jest publiczny adres IPv4 lub IPv6 modułu równoważenia obciążenia.

> [!NOTE]
> Protokół ICMP dla IPv4 i IPv6 są zablokowane w sieci platformy Azure. W rezultacie narzędzia protokołu ICMP, takie jak ping zawsze zakończyć się niepowodzeniem. Aby przetestować łączność, należy użyć alternatywnej TCP, takie jak TCPing lub polecenia cmdlet programu PowerShell Test-NetConnection. Należy pamiętać, że adresy IP wyświetlane na diagramie przedstawiono wartości, które można napotkać. Ponieważ adresy IPv6 są przypisywane dynamicznie, adresy, które otrzymujesz różnią się i może różnić między regionami. Ponadto jest typowe dla publicznego adresu protokołu IPv6 modułu równoważenia obciążenia, można uruchomić z prefiksem innego niż prywatnymi adresami IPv6 w puli zaplecza.

## <a name="template-parameters-and-variables"></a>Zmienne i parametry szablonu

Szablon usługi Azure Resource Manager zawiera wiele zmiennych i parametrów, które można dostosować do swoich potrzeb. Zmienne są używane dla wartości stałych, które nie chcesz, aby użytkownik mógł zmienić. Parametry są używane dla wartości ma użytkownika o podanie podczas wdrażania szablonu. Przykładowy szablon jest skonfigurowany na potrzeby scenariusza opisanego w tym artykule. Można to dostosować do potrzeb danego środowiska.

Przykładowy szablon używane w tym artykule zawiera następujące zmienne i parametry:

| Parametr / zmiennej | Uwagi |
| --- | --- |
| adminUsername |Określ nazwę użyte do zalogowania się do maszyn wirtualnych przy użyciu konta administratora. |
| adminPassword |Określ hasło użyte do zalogowania się do maszyn wirtualnych przy użyciu konta administratora. |
| dnsNameforIPv4LbIP |Określ nazwę hosta DNS, który ma zostać przypisany jako publiczna nazwa modułu równoważenia obciążenia. Ta nazwa jest rozpoznawany jako publiczny adres IPv4 modułu równoważenia obciążenia. Nazwa musi zawierać tylko litery i zgodny z wyrażeniem regularnym: ^ [a-z] [-z0 - 9-]{1,61}$ [a-z0-9]. |
| dnsNameforIPv6LbIP |Określ nazwę hosta DNS, który ma zostać przypisany jako publiczna nazwa modułu równoważenia obciążenia. Ta nazwa jest rozpoznawany jako publiczny adres IPv6 modułu równoważenia obciążenia. Nazwa musi zawierać tylko litery i zgodny z wyrażeniem regularnym: ^ [a-z] [-z0 - 9-]{1,61}$ [a-z0-9]. Może to być taką samą nazwę jak adres IPv4. Gdy klient wysyła zapytanie DNS dla tej nazwy zwróci Azure A i AAAA są rejestrowane po udostępnieniu nazwę. |
| vmNamePrefix |Określ prefiks nazwy maszyny Wirtualnej. Szablon dołącza numer (0, 1, itp.) do nazwy podczas tworzenia maszyn wirtualnych. |
| nicNamePrefix |Określ prefiks nazwy interfejsu sieciowego. Szablon dołącza numer (0, 1, itp.) do nazwy podczas tworzenia interfejsów sieciowych. |
| storageAccountName |Wprowadź nazwę istniejącego konta magazynu lub określ nazwę nową ma zostać utworzony przez szablon. |
| availabilitySetName |Następnie wprowadź nazwę w zestawie dostępności, aby można używać z maszynami wirtualnymi |
| addressPrefix |Prefiks adresu, używane do definiowania zakresu adresów sieci wirtualnej |
| subnetName |Nazwa podsieci w utworzona dla sieci wirtualnej |
| subnetPrefix |Prefiks adresu, używane do definiowania zakresu adresów podsieci |
| vnetName |Określ nazwę dla sieci wirtualnej używane przez maszyny wirtualne. |
| ipv4PrivateIPAddressType |Metodę alokacji dla prywatny adres IP (statyczne lub dynamiczne) |
| ipv6PrivateIPAddressType |Metoda alokacji umożliwiający prywatny adres IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamicznej alokacji. |
| numberOfInstances |Liczba wystąpień ze zrównoważonym obciążeniem, wdrożonego w ramach szablonu |
| ipv4PublicIPAddressName |Określ nazwę DNS, który chcesz używać do komunikowania się z publicznym adresem IPv4 modułu równoważenia obciążenia. |
| ipv4PublicIPAddressType |Metodę alokacji dla publicznego adresu IP (statyczne lub dynamiczne) |
| Ipv6PublicIPAddressName |Określ nazwę DNS, który chcesz używać do komunikowania się z publicznym adresem IPv6 modułu równoważenia obciążenia. |
| ipv6PublicIPAddressType |Metoda alokacji używane dla publicznego adresu IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamicznej alokacji. |
| lbName |Określ nazwę modułu równoważenia obciążenia. Ta nazwa jest wyświetlana w portalu lub używana przy odwoływaniu się do niej przy użyciu polecenia interfejsu wiersza polecenia lub programu PowerShell. |

Pozostałe zmienne w szablonie zawierają wartości pochodnych, które są przypisane, gdy platforma Azure utworzy zasoby. Nie należy zmieniać tych zmiennych.

## <a name="next-steps"></a>Kolejne kroki

Składnię JSON i właściwości modułu równoważenia obciążenia w szablonie, zobacz [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers).
