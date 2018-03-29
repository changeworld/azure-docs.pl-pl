---
title: Wdrażanie internetowy równoważenia obciążenia w przypadku adresu IPv6 - szablonu Azure | Dokumentacja firmy Microsoft
description: Jak wdrożyć obsługi protokołu IPv6 dla modułu równoważenia obciążenia w Azure i maszyn wirtualnych z równoważeniem obciążenia.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: Protokół IPv6, usługi równoważenia obciążenia azure, podwójnego stosu, publiczny adres ip, natywnego protokołu ipv6, mobile, iot
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 431b43979ac364d943c58c40b4199b7f30f9acf6
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Wdróż internetowy modułu równoważenia obciążenia rozwiązanie w przypadku adresu IPv6 przy użyciu szablonu

> [!div class="op_single_selector"]
> * [Program PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](load-balancer-ipv6-internet-cli.md)
> * [Szablon](load-balancer-ipv6-internet-template.md)



Usługa Azure Load Balancer to moduł równoważenia obciążenia w warstwie 4 (TCP, UDP). Moduł równoważenia obciążenia zapewnia wysoką dostępność, dystrybuując ruch przychodzący w wystąpieniach usług o dobrej kondycji w usługach w chmurze lub na maszynach wirtualnych w zestawie modułu równoważenia obciążenia. Usługa Azure Load Balancer może także prezentować te usługi na wielu portach i/lub wielu adresach IP.

## <a name="example-deployment-scenario"></a>Przykładowy scenariusz wdrażania

Na poniższym diagramie przedstawiono rozwiązania do równoważenia obciążenia jest wdrażane za pomocą szablonu przykład opisane w tym artykule.

![Scenariusz modułu równoważenia obciążenia](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

W tym scenariuszu spowoduje utworzenie następujących zasobów platformy Azure:

* Interfejs sieci wirtualnej, dla każdej maszyny Wirtualnej przy użyciu adresów IPv4 i IPv6 przypisany
* Moduł równoważenia obciążenia internetowy z protokołów IPv4 i IPv6 publicznego adresu IP
* dwie reguły równoważenia do mapowania publiczne adresy VIP prywatnej punkty końcowe obciążenia
* Zbiór dostępności, która zawiera dwie maszyny wirtualne
* dwóch maszyn wirtualnych (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Wdrażanie szablonu przy użyciu portalu Azure

W tym artykule odwołuje się do szablonu, który jest opublikowany w [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galerii. Można pobrać szablonu z galerii lub uruchomić wdrożenia na platformie Azure bezpośrednio z poziomu galerii. W tym artykule przyjęto założenie, że szablon został pobrany na komputerze lokalnym.

1. Otwórz Azure portal i zaloguj się przy użyciu konta, które ma uprawnienia do tworzenia maszyn wirtualnych i zasobów sieciowych w ramach subskrypcji platformy Azure. Ponadto jeśli korzystasz z istniejących zasobów, konto musi uprawnień do utworzenia grupy zasobów i konto magazynu.
2. Kliknij przycisk "+ New" z menu, a następnie wpisz "szablon" w polu wyszukiwania. Wybierz "Wdrożenie szablonu" w wynikach wyszukiwania.

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. W obszarze wszystko bloku, kliknij przycisk "Wdrażania szablonu."

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kliknij przycisk "Utwórz".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kliknij pozycję "Edytuj szablon". Usuń istniejącą zawartość i kopiowania/wklejania w całą zawartość pliku szablonu (Aby dołączyć rozpoczęcia i zakończenia {}), a następnie kliknij przycisk Zapisz.

    > [!NOTE]
    > Jeśli używasz programu Microsoft Internet Explorer możesz wkleić wyświetlane okno dialogowe z pytaniem o zezwolenie na dostęp do Schowka systemu Windows. Kliknij pozycję "Zezwalaj na dostęp."

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kliknij pozycję "Edytuj parametry". W bloku parametry określ wartości zgodnie ze wskazówkami w sekcji parametrów szablonu, a następnie kliknij przycisk Zapisz, aby zamknąć bloku parametrów. W bloku wdrożenie niestandardowe wybierz subskrypcję, istniejącą grupę zasobów lub utwórz je. Jeśli tworzysz grupę zasobów, następnie wybierz lokalizację dla grupy zasobów. Następnie kliknij przycisk **postanowienia prawne**, następnie kliknij przycisk **zakupu** dla postanowienia prawne. Azure rozpoczyna wdrażanie zasobów. Trwa kilka minut, aby wdrożyć wszystkie zasoby.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Aby uzyskać więcej informacji na temat tych parametrów, zobacz [szablonu parametry i zmienne](#template-parameters-and-variables) sekcji w dalszej części tego artykułu.

7. Aby wyświetlić zasoby utworzone przez szablon, kliknij przycisk Przeglądaj, przewiń w dół listy, dopóki nie można wyświetlić "Grupy zasobów", a następnie kliknij go.

    ![lb-ipv6 — portal — krok 7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. W bloku grupy zasobów kliknij nazwę grupy zasobów określonej w kroku 6. Możesz wyświetlić listę wszystkich zasobów, które zostały wdrożone. Jeśli wszystkie poszło dobrze, powinien powiedzieć "Powodzenie" w obszarze "Ostatniego wdrożenia". Jeśli nie, upewnij się, że konto, którego używasz, ma uprawnienia do tworzenia niezbędne zasoby.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Jeśli natychmiast po ukończeniu kroku 6 możesz przeglądać grup zasobów, "Ostatniego wdrożenia" będzie wyświetlany stan "Wdrażanie" podczas wdrażania zasobów.

9. Kliknij przycisk "myIPv6PublicIP" na liście zasobów. Zostanie wyświetlony, ma adres IPv6 z adresu IP i że jego nazwa DNS jest wartość, która została określona dla parametru dnsNameforIPv6LbIP w kroku 6. Ten zasób jest publiczny IPv6 adresu i nazwy hosta, który jest dostępny dla klientów internetowych.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Sprawdzanie poprawności łączności

Po pomyślnym wdrożeniu szablonu można zweryfikować łączności, wykonując następujące zadania:

1. Zaloguj się do portalu Azure i połącz się wszystkich maszynach wirtualnych utworzonych przez wdrożenie szablonu. Jeśli wdrożono Maszynę wirtualną serwera systemu Windows, uruchom polecenie ipconfig/z wiersza polecenia. Zobaczysz, że maszyny wirtualne mają adresy IPv4 i IPv6. Jeśli wdrożono maszyn wirtualnych systemu Linux, należy skonfigurować w systemie operacyjnym Linux, aby odbierać dynamiczne adresy IPv6 przy użyciu instrukcjami dla dystrybucji systemu Linux.
2. W kliencie połączony z Internetem IPv6 nawiązania połączenia na publiczny adres IPv6 modułu równoważenia obciążenia. Aby upewnić się, że moduł równoważenia obciążenia jest równoważenia między dwiema maszynami wirtualnymi, takie jak Microsoft Internet Information Services (IIS) serwera sieci web można zainstalować na wszystkich maszynach wirtualnych. Domyślna strona sieci web na każdym serwerze mogą zawierać tekst "Server0" lub "Server1" w celu jego jednoznacznej identyfikacji. Następnie otwórz przeglądarkę internetową na kliencie podłączonej do Internetu IPv6 i wskaż nazwa hosta określona dla parametru dnsNameforIPv6LbIP usługi równoważenia obciążenia, aby upewnić się na trasie łączności IPv6 z każdej maszyny Wirtualnej. Jeśli widzisz tylko strony sieci web z tylko jednego serwera, może być konieczne Wyczyść pamięć podręczną przeglądarki. Otwórz wiele prywatnych sesji przeglądania. Powinna zostać wyświetlona odpowiedź z każdego serwera.
3. W kliencie podłączonej do Internetu IPv4 inicjowania połączenia publiczny adres IPv4 usługi równoważenia obciążenia. Aby potwierdzić dwóch maszyn wirtualnych równoważenia obciążenia modułu równoważenia obciążenia, można przetestować, za pomocą usług IIS, zgodnie z opisem w kroku 2.
4. Z każdej maszyny Wirtualnej należy zainicjować wychodzące połączenie z urządzeniem IPv6 i IPv4 podłączonej do Internetu. W obu przypadkach źródłowy adres IP, które zostały odebrane przez urządzenie docelowe jest publiczny adres IPv4 lub IPv6 modułu równoważenia obciążenia.

> [!NOTE]
> Protokół ICMP dla protokołów IPv4 i IPv6 są zablokowane w sieci platformy Azure. W związku z tym ICMP narzędzia, takie jak ping zawsze zakończyć się niepowodzeniem. Aby przetestować połączenie, użyj zamiast protokołu TCP, takie jak TCPing lub polecenia cmdlet programu PowerShell Test-NetConnection. Należy pamiętać, że adresy IP wyświetlane na diagramie przedstawiono przykładowe wartości, które można napotkać. Ponieważ dynamicznie przypisywane są adresy IPv6, adresy, które otrzymujesz będą różne i zależą od regionu. Ponadto jest typowe dla publicznego adresu IPv6, można uruchomić z prefiksem innego niż prywatnych adresów IPv6 w puli zaplecza modułu równoważenia obciążenia.

## <a name="template-parameters-and-variables"></a>Parametry szablonu i zmienne

Szablon usługi Azure Resource Manager zawiera wiele zmiennych i parametrów, które można dostosować do własnych potrzeb. Zmienne są używane dla stałej wartości, które nie mają użytkownikom na zmianę. Parametry są używane dla wartości, które mają użytkownika o podanie podczas wdrażania szablonu. Przykładowy szablon jest prawidłowo skonfigurowany scenariusz opisany w tym artykule. To ustawienie można dostosować do potrzeb danego środowiska.

Szablon przykład używany w tym artykule zawiera następujące zmiennych i parametrów:

| Parametr / zmiennej | Uwagi |
| --- | --- |
| adminUsername |Określ nazwę konta administratora, używane do logowania do maszyn wirtualnych z. |
| adminPassword |Określ hasło dla konta administratora, używane do logowania do maszyn wirtualnych z. |
| dnsNameforIPv4LbIP |Określ nazwę hosta DNS, który ma zostać przypisany jako publiczna nazwa usługi równoważenia obciążenia. Ta nazwa jest rozpoznawany jako publiczny adres IPv4 modułu równoważenia obciążenia. Nazwa musi być mała i pasuje do wyrażenia regularnego: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Określ nazwę hosta DNS, który ma zostać przypisany jako publiczna nazwa usługi równoważenia obciążenia. Ta nazwa jest rozpoznawany jako publiczny adres IPv6 modułu równoważenia obciążenia. Nazwa musi być mała i pasuje do wyrażenia regularnego: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Może to być taką samą nazwę jak adres IPv4. Gdy klient wysyła zapytanie DNS dla tej nazwy zwróci Azure A i AAAA rejestruje po udostępnieniu nazwę. |
| vmNamePrefix |Określ prefiks nazwy maszyny Wirtualnej. Szablon dołącza numer (0, 1, itp.) do nazwy podczas tworzenia maszyn wirtualnych. |
| nicNamePrefix |Określ prefiks nazwy interfejsu sieciowego. Szablon dołącza numer (0, 1, itp.) do nazwy podczas tworzenia interfejsów sieciowych. |
| storageAccountName |Wprowadź nazwę istniejącego konta magazynu lub określ nazwę nową ma zostać utworzony przez szablon. |
| availabilitySetName |Następnie wprowadź nazwę zestawu ma być używany z maszyn wirtualnych dostępności |
| addressPrefix |Prefiks adresu używane do definiowania zakresu adresów sieci wirtualnej |
| subnetName |Utworzona nazwa podsieci w sieci wirtualnej |
| subnetPrefix |Prefiks adresu używane do definiowania zakresu adresów w podsieci |
| vnetName |Określ nazwę sieci wirtualnej używanych przez maszyny wirtualne. |
| ipv4PrivateIPAddressType |Metodę przydziału prywatnego adresu IP (statyczna lub dynamiczna) |
| ipv6PrivateIPAddressType |Metoda alokacji służąca do prywatnego adresu IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamicznej alokacji. |
| numberOfInstances |Liczba wystąpień równoważenia obciążenia wdrożone w szablonie |
| ipv4PublicIPAddressName |Podaj nazwę DNS, który ma być używany do komunikacji z publiczny adres IPv4, usługi równoważenia obciążenia. |
| ipv4PublicIPAddressType |Metodę alokacji dla publicznego adresu IP (statyczna lub dynamiczna) |
| Ipv6PublicIPAddressName |Podaj nazwę DNS, który ma być używany do komunikacji z publicznego adresu IPv6 modułu równoważenia obciążenia. |
| ipv6PublicIPAddressType |Metoda alokacji służąca do publicznego adresu IP (dynamiczny). Protokół IPv6 obsługuje tylko dynamicznej alokacji. |
| lbName |Określ nazwę usługi równoważenia obciążenia. Ta nazwa jest wyświetlana w portalu lub używany podczas odwoływania się do niego przy użyciu polecenia interfejsu wiersza polecenia lub środowiska PowerShell. |

Pozostałe zmienne w szablonie zawierają pochodnej wartości, które są przypisywane, gdy platforma Azure tworzy zasobów. Nie należy zmieniać tych zmiennych.
