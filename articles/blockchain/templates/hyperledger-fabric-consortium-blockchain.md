---
title: Hyperledger Fabric sieci konsorcjum na platformie Azure
description: Szablon rozwiązania dotyczące wdrażania i konfigurowania sieci konsorcjum Hyperledger Fabric
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/23/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: caleteet
manager: femila
ms.openlocfilehash: ce1afbd5499e798888e77f52d7b652e7e1f548fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690661"
---
# <a name="hyperledger-fabric-consortium-network"></a>Sieci konsorcjum Hyperledger Fabric

Szablon rozwiązania konsorcjum Hyperledger Fabric umożliwia wdrażanie i konfigurowanie sieci konsorcjum Hyperledger Fabric na platformie Azure.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę na temat łańcucha bloków, Hyperledger Fabric i bardziej skomplikowanych konsorcjum architektury sieci
- Dowiedz się, jak wdrożyć i skonfigurować sieci konsorcjum Hyperledger Fabric w witrynie Azure Portal

## <a name="about-blockchain"></a>Temat łańcucha bloków

Jeśli jesteś nowym użytkownikiem społecznością łańcucha bloków, ten szablon rozwiązania to znakomita szansa, aby dowiedzieć się więcej o technologii w sposób łatwy i można je konfigurować na platformie Azure. Łańcuch bloków to podstawowy technologia Bitcoin; jednak jest znacznie więcej niż tylko włącznik wirtualnego waluty. Jest złożone istniejącą bazę danych, systemami rozproszonymi i technologii szyfrowania, umożliwiająca bezpieczne wieloosobowa obliczeń z gwarancje w zakresie niezmienności, możliwość weryfikacji, sprawdzalność i odporności na ataki. Różnych protokołów używają różnych mechanizmów zapewnienie tych atrybutów. [Hyperledger Fabric](https://github.com/hyperledger/fabric) jest jeden taki protokół.

## <a name="consortium-architecture-on-azure"></a>Architektura konsorcjum na platformie Azure

Aby włączyć Hyperledger Fabric na platformie Azure, istnieją dwa typy podstawowego wdrożenia, które są obsługiwane. Te wdrożenia są przeznaczone do obsługi różnych topologii, w oparciu o wybraną docelową.

- **Pojedyncza maszyna wirtualna, developer server** — tego typu wdrożenia został zaprojektowany jako środowiska programowania, używany do tworzenia i testowania rozwiązań skompilowanych na Hyperledger Fabric.
- **Wiele maszyn wirtualnych, skalowanie w poziomie wdrożenie** — ten typ wdrożenia jest przeznaczona dla środowisk, które modelują konsorcjum różnych uczestników, wykorzystując współużytkowanym środowisku.

W obu wdrożenia bloki konstrukcyjne wchodzące w rdzeniu Hyperledger Fabric są takie same.  Różnice we wdrożeniach są, jak te składniki są skalowane do wewnątrz.

- **Urząd certyfikacji węzłów**: Węzeł uruchomiony urząd certyfikacji, który jest używany do generowania certyfikatów, które są używane dla tożsamości w sieci.
- **Węzły osoby zamawiającej**: Węzeł, implementowanie gwarancji dostarczania, takich jak łączna liczba kolejności odpowiedniej do emisji lub transakcje niepodzielne usługą komunikacji.
- **Komunikacja równorzędna węzłów**: Węzeł, zatwierdzeń transakcji, która przechowuje stan i kopię rejestru rozproszonego.
- **Węzły CouchDB**: Węzeł, który można uruchomić usługi CouchDB, które można przechowywać stan bazy danych i zapewniają zaawansowane funkcje zapytań danych chaincode rozwijanie z prostego klucza i wartości do formatu JSON typu magazynu.

### <a name="single-virtual-machine-architecture"></a>Architektura pojedynczej maszyny wirtualnej

Jak już wspomniano pojedynczego wirtualnego architektura komputera zaprojektowano pod kątem deweloperów serwera niewielki rozmiar, który służy do tworzenia aplikacji. Wszystkie kontenery, wyświetlane są uruchomione w pojedynczej maszyny wirtualnej. Usługa szeregowania [POJEDYNCZĄ](https://github.com/hyperledger/fabric/tree/master/orderer) dla tej konfiguracji. Ta konfiguracja jest *nie* odporności na uszkodzenia porządkowanie usługi, ale została zaprojektowana jako uproszczone do celów programistycznych.

![Architektura pojedynczej maszyny wirtualnej](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Wiele Architektura maszyny wirtualnej

Wiele maszyny wirtualnej, architektura skalowalnego w poziomie jest utworzone o wysokiej dostępności i skalowania dla każdego składnika w samym sercu. Ta architektura jest znacznie bardziej odpowiednie w przypadku wdrożeń klasy korporacyjnej produkcyjnych.

![Wiele Architektura maszyny wirtualnej](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, musisz mieć subskrypcję platformy Azure, obsługująca wdrażanie kilka maszyn wirtualnych i kont magazynu w warstwie standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz to zrobić [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Po utworzeniu subskrypcji, przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **Utwórz zasób > łańcucha bloków > Hyperledger Fabric konsorcjum**.

![Szablon Hyperledger Fabric jednego elementu członkowskiego łańcucha bloków w witrynie Marketplace](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Wdrożenie

W **Hyperledger Fabric konsorcjum** szablonu, wybierz opcję **Utwórz**.

Wdrożenie szablonu przeprowadzi Cię Konfigurowanie wieloma węzłami [Hyperledger 1.3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) sieci. Przepływ wdrożenia jest podzielony na cztery kroki: Podstawowe informacje dotyczące ustawień sieci konsorcjum, konfiguracja sieci szkieletowej i opcjonalnych składników.

### <a name="basics"></a>Podstawy

W **podstawy**, określ wartości dla parametrów standardowego dla każdego wdrożenia. Takie jak subskrypcji, grupy zasobów i podstawowe wirtualne maszyny właściwości.

![Podstawy](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Prefiks zasobów** | Prefiks nazwy dla zasobów udostępnionych w ramach wdrożenia |6 znaków lub mniej |
**Nazwa użytkownika** | Nazwa użytkownika administratora dla poszczególnych maszyn wirtualnych wdrożonych dla tego elementu członkowskiego |1 - 64 znaki |
**Typ uwierzytelniania** | Metoda uwierzytelniania do maszyny wirtualnej |Klucz publiczny hasła lub protokołu SSH|
**Hasło (typ uwierzytelniania = hasło)** |Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Hasło musi zawierać trzy z następujących typów znaków: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny<br /><br />Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, można zmienić hasło po zainicjowaniu obsługi administracyjnej|12 - 72 znaków|
**Klucz SSH (typ uwierzytelniania SSH oraz publicznego klucza =)** |Klucz protokołu secure shell, używany do zdalnego logowania ||
**Subskrypcja** |Subskrypcja, dla której chcesz wdrożyć ||
**Grupa zasobów** |Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum ||
**Lokalizacja** |Region platformy Azure, dla której chcesz wdrożyć pierwszego elementu członkowskiego w ||

Kliknij przycisk **OK**.

### <a name="consortium-network-settings"></a>Ustawienia sieci konsorcjum

W **ustawienia sieciowe**, określ dane wejściowe do tworzenia lub dołączenie istniejącego konsorcjum sieci, a następnie skonfiguruj ustawienia Twojej organizacji.

![Ustawienia sieci konsorcjum](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Konfiguracja sieci** |Można utworzyć nową sieć lub przyłącz istniejącą grupę. Jeśli wybierzesz *Dołącz istniejące*, musisz podać dodatkowe wartości. |Nową sieć <br/> Dołącz istniejące |
**Hasło HLF urzędu certyfikacji** |Hasło używane do certyfikatów wygenerowanych przez urzędy certyfikacji, które są tworzone w ramach wdrożenia. Hasło musi zawierać trzy z następujących typów znaków: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny.<br /><br />Gdy wszystkie maszyny wirtualne mają początkowo tego samego hasła, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|1 - 25 znaków |
**Ustawienia organizacji** |Można dostosować nazwę organizacji i certyfikatu lub mają przypisane wartości domyślne do użycia.|Domyślne <br/> Zaawansowane |
**Ustawienia sieci VPN** | Aprowizowanie bramy tunel sieci VPN do uzyskiwania dostępu do maszyn wirtualnych | Yes <br/> Nie |

Kliknij przycisk **OK**.

### <a name="fabric-specific-settings"></a>Ustawienia specyficzne dla sieci szkieletowej

W **konfiguracja sieci szkieletowej**, skonfiguruj network Activity block size i wydajność i określ dane wejściowe dla dostępności sieci. Liczba takich jak osoby zamawiającej i węzły równorzędne, aparat trwałości używane przez każdy węzeł i rozmiar maszyny Wirtualnej.

![Ustawienia sieci szkieletowej](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Typ skalowania** |Typ wdrożenia pojedynczej maszyny wirtualnej z wieloma kontenerami lub wielu maszyn wirtualnych w modelu skalowalnego w poziomie.|Jednej maszyny Wirtualnej lub wielu maszyn wirtualnych |
**Typ dysku maszyny Wirtualnej** |Typ magazynu kopii każdego wdrożonego węzła. <br/> Aby dowiedzieć się więcej na temat typów dostępnego miejsca na dysku, odwiedź stronę [wybierz typ dysku](../../virtual-machines/windows/disks-types.md).|Standardowa, SSD <br/> Premium, SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Wdrażanie wielu maszyn wirtualnych (dodatkowe ustawienia)

![Ustawienia sieci szkieletowej podczas wielu wdrożeń maszyn wirtualnych](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Liczba węzłów osoby zamawiającej** |Liczba węzłów, których kolejność (organizowanie) transakcji w bloku. <br />Więcej informacji na temat usługi zamawiania, odwiedź stronę Hyperledger [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) |1 – 4 |
**Rozmiar maszyny wirtualnej węzła osoby zamawiającej** |Rozmiar maszyny wirtualnej, używany do osoby zamawiającej węzły w sieci|Standardowa Bs<br />Standard Ds<br />Standardowa FS |
**Liczba węzłów równorzędnych** | Węzły, które są własnością członkowie konsorcjum, wykonywać transakcje, które Obsługa stanu i kopię rejestru.<br />Więcej informacji na temat usługi zamawiania, odwiedź stronę Hyperledger [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|1 – 4 |
**Utrwalanie stanu węzła** |Aparat trwałości używana przez węzły równorzędne. Można skonfigurować ten aparat na węzeł równorzędny. Zobacz szczegóły poniżej wiele węzłów elementów równorzędnych.|CouchDB <br />LevelDB |
**Rozmiar maszyny wirtualnej węzła równorzędnego** |Rozmiar maszyny wirtualnej, używany dla wszystkich węzłów w sieci|Standardowa Bs<br />Standard Ds<br />Standardowa FS |

### <a name="multiple-peer-node-configuration"></a>Wiele konfiguracji węzła równorzędnego

Ten szablon umożliwia pobranie silnik stanów trwałych dla węzła równorzędnego. Na przykład, jeśli masz trzy węzły równorzędne służy CouchDB na jednym i LevelDB na pozostałe dwa.

![Wiele konfiguracji węzła równorzędnego](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Kliknij przycisk **OK**.

### <a name="deploy"></a>Wdrażanie

W **Podsumowanie**, przejrzyj dane wejściowe określone i aby uruchomić podstawową walidację przed wdrożeniem.

![Podsumowanie](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Przejrzyj postanowienia prawne i ochrona prywatności i wybierz **zakupu** do wdrożenia. W zależności od liczby maszyn wirtualnych aprowizowane czas wdrażania może się różnić od kilku minut do dziesiątek minut.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przystąpić do skupić się na aplikacji i rozwój chaincode względem sieć Hyperledger konsorcjum łańcucha bloków.
