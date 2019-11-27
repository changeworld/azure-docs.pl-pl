---
title: Wdróż szablon rozwiązania dla konsorcjum Fabric w sieci szkieletowej na platformie Azure
description: Jak wdrożyć i skonfigurować szablon rozwiązania sieciowego konsorcjum sieci szkieletowej w systemie Azure
ms.date: 05/09/2019
ms.topic: article
ms.reviewer: caleteet
ms.openlocfilehash: be35cfa26204b36ad65da91252144b9167cb9e54
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325133"
---
# <a name="hyperledger-fabric-consortium-network"></a>Sieć szkieletowa

Za pomocą szablonu rozwiązania do tworzenia i konfigurowania sieci szkieletowej w ramach usługi sieci szkieletowej w systemie Azure można wdrożyć i skonfigurować sieć konsorcjum Fabric.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę na temat łańcucha bloków, sieci szkieletowej i bardziej skomplikowanych architektur sieci konsorcjum
- Dowiedz się, jak wdrażać i konfigurować sieć konsorcjum sieci szkieletowej z poziomu Azure Portal

## <a name="about-blockchain"></a>Informacje o łańcucha bloków

Jeśli jesteś nowym członkiem społeczności łańcucha bloków, ten szablon rozwiązania jest doskonałym rozwiązaniem, aby dowiedzieć się więcej o technologii w sposób łatwy i konfigurowalny na platformie Azure. Łańcucha bloków jest podstawową technologią za Bitcoin; jest to jednak znacznie więcej niż tylko czynnik dla waluty wirtualnej. Jest to złożona usługa bazy danych, systemu rozproszonego i technologii kryptograficznych, która umożliwia bezpieczne Obliczanie wielostronne z gwarancjami dotyczącymi niezmienności, możliwości weryfikacji, kontroli i odporności na ataki. Różne protokoły korzystają z różnych mechanizmów, aby zapewnić te atrybuty. [Sieć szkieletowa z księgą](https://github.com/hyperledger/fabric) jest jednym z tych protokołów.

## <a name="consortium-architecture-on-azure"></a>Architektura konsorcjum na platformie Azure

Aby włączyć sieć szkieletową tworzenia księgi na platformie Azure, obsługiwane są dwa podstawowe typy wdrożeń. Te wdrożenia zostały zaprojektowane z uwzględnieniem różnych topologii w oparciu o żądany element docelowy.

- **Pojedyncza maszyna wirtualna, serwer dewelopera** — ten typ wdrożenia jest zaprojektowany jako środowisko programistyczne służące do kompilowania i testowania rozwiązań opartych na sieci szkieletowej.
- **Wiele maszyn wirtualnych, skalowanie w poziomie** — ten typ wdrożenia jest przeznaczony dla środowisk, które modelują konsorcjum różnych uczestników korzystających ze współużytkowanego środowiska.

W obu wdrożeniach bloki konstrukcyjne, które tworzą rdzeń sieci szkieletowej w arkuszu, są takie same.  Różnice między wdrożeniami to sposób skalowania tych składników.

- **Węzły urzędu certyfikacji**: węzeł z uruchomionym urzędem certyfikacji, który jest używany do generowania certyfikatów używanych dla tożsamości w sieci.
- **Węzły programu orderer**: węzeł z uruchomioną usługą komunikacji implementującą gwarancję dostarczania, na przykład łączną emisję lub transakcję niepodzielną.
- **Węzły równorzędne**: węzeł, który zatwierdza transakcje i utrzymuje stan i kopię księgi rozproszonej.
- **Węzły CouchDB**: węzeł, w którym można uruchomić usługę CouchDB, która może przechowywać bazę danych stanu i zapewniać zaawansowane zapytania o dane chaincode, rozszerzając z prostego klucza/wartości na magazyn typu JSON.

### <a name="single-virtual-machine-architecture"></a>Architektura pojedynczej maszyny wirtualnej

Jak wspomniano wcześniej, architektura pojedynczej maszyny wirtualnej jest tworzona dla deweloperów, którzy mają niewielki serwer, który jest używany do opracowywania aplikacji. Wszystkie wyświetlone kontenery są uruchomione na jednej maszynie wirtualnej. Usługa określania kolejności używa opcji " [Solo](https://github.com/hyperledger/fabric/tree/master/orderer) " dla tej konfiguracji. Ta konfiguracja *nie* jest usługą do odporności na uszkodzenia, ale jest przeznaczona do celów deweloperskich.

![Architektura pojedynczej maszyny wirtualnej](./media/hyperledger-fabric-consortium-blockchain/hlf-single-arch.png)

### <a name="multiple-virtual-machine-architecture"></a>Wiele architektur maszyn wirtualnych

Architektura wielu maszyn wirtualnych, skalowanie w poziomie jest zbudowana z wysoką dostępnością i skalowaniem każdego składnika na rdzeń. Ta architektura jest znacznie bardziej odpowiednia dla wdrożeń klasy produkcyjnej.

![Wiele architektur maszyn wirtualnych](./media/hyperledger-fabric-consortium-blockchain/hlf-multi-arch.png)

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, musisz mieć subskrypcję platformy Azure, która może obsługiwać wdrażanie kilku maszyn wirtualnych i kont magazynu w warstwie Standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Po utworzeniu subskrypcji przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **Utwórz zasób > łańcucha bloków >** .

![Szablon witryny Marketplace z jednym członkiem sieci szkieletowej łańcucha bloków](./media/hyperledger-fabric-consortium-blockchain/marketplace-template.png)

## <a name="deployment"></a>Wdrożenie

W szablonie **konsorcjum sieci szkieletowej w ramach księgi głównej** wybierz pozycję **Utwórz**.

Wdrożenie szablonu przeprowadzi Cię przez proces konfigurowania sieci [1,3](https://hyperledger-fabric.readthedocs.io/en/release-1.3/) wielowęzłowego. Przepływ wdrożenia jest podzielony na cztery kroki: podstawowe, ustawienia sieci konsorcjum, konfiguracja sieci szkieletowej i składniki opcjonalne.

### <a name="basics"></a>Podstawy

W obszarze **podstawowe**Określ wartości parametrów standardowych dla każdego wdrożenia. Takie jak, subskrypcja, Grupa zasobów i podstawowe właściwości maszyny wirtualnej.

![Podstawy](./media/hyperledger-fabric-consortium-blockchain/basics.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Prefiks zasobu** | Prefiks nazwy dla zasobów zainicjowanych jako część wdrożenia |co najmniej 6 znaków |
**Nazwa użytkownika** | Nazwa użytkownika administratora dla każdej z maszyn wirtualnych wdrożonych dla tego elementu członkowskiego |1-64 znaków |
**Typ uwierzytelniania** | Metoda do uwierzytelnienia na maszynie wirtualnej |Hasło lub klucz publiczny SSH|
**Hasło (typ uwierzytelniania = hasło)** |Hasło dla konta administratora dla każdej wdrożonej maszyny wirtualnej. Hasło musi zawierać trzy z następujących typów znaków: 1 wielkie litery, 1 małe litery, 1 cyfra i 1 znak specjalny<br /><br />Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, można zmienić hasło po zainicjowaniu obsługi administracyjnej|12-72 znaków|
**Klucz SSH (typ uwierzytelniania = publiczny klucz SSH)** |Bezpieczny klucz powłoki używany do zdalnego logowania ||
**Subskrypcja** |Subskrypcja do wdrożenia ||
**Grupa zasobów** |Grupa zasobów, w której ma zostać wdrożona sieć konsorcjum ||
**Lokalizacja** |Region świadczenia usługi Azure, do którego ma zostać wdrożony pierwszy element członkowski ||

Kliknij przycisk **OK**.

### <a name="consortium-network-settings"></a>Ustawienia sieci konsorcjum

W obszarze **Ustawienia sieci**Określ dane wejściowe do utworzenia lub przyłączenia istniejącej sieci konsorcjum i skonfiguruj ustawienia organizacji.

![Ustawienia sieci konsorcjum](./media/hyperledger-fabric-consortium-blockchain/network-settings.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Konfiguracja sieci** |Można utworzyć nową sieć lub dołączyć do istniejącej sieci. W przypadku wybrania opcji *Dołącz do istniejących*należy podać dodatkowe wartości. |Nowa sieć <br/> Dołącz do istniejącej |
**HLF — hasło urzędu certyfikacji** |Hasło używane dla certyfikatów generowanych przez urzędy certyfikacji, które są tworzone w ramach wdrożenia. Hasło musi zawierać trzy z następujących typów znaków: 1 wielkie litery, 1 małe litery, 1 cyfra i 1 znak specjalny.<br /><br />Mimo że wszystkie maszyny wirtualne mają początkowo to samo hasło, można zmienić hasło po zainicjowaniu obsługi administracyjnej.|1-25 znaków |
**Konfiguracja organizacji** |Można dostosować nazwę i certyfikat organizacji lub użyć wartości domyślnych.|Domyślny <br/> Zaawansowane |
**Ustawienia sieci VPN** | Inicjowanie obsługi administracyjnej bramy tunelu sieci VPN w celu uzyskania dostępu do maszyn wirtualnych | Yes <br/> Nie |

Kliknij przycisk **OK**.

### <a name="fabric-specific-settings"></a>Ustawienia specyficzne dla sieci szkieletowej

W obszarze **Konfiguracja sieci szkieletowej**można skonfigurować rozmiar i wydajność sieci oraz określić dane wejściowe dotyczące dostępności sieci. Taki jak numer programu porządkowania i węzłów równorzędnych, aparat trwałości używany przez każdy węzeł i rozmiar maszyny wirtualnej.

![Ustawienia sieci szkieletowej](./media/hyperledger-fabric-consortium-blockchain/fabric-specific-settings.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Typ skalowania** |Typ wdrożenia pojedynczej maszyny wirtualnej z wieloma kontenerami lub wieloma maszynami wirtualnymi w modelu skalowalnym w poziomie.|Pojedyncza maszyna wirtualna lub wiele maszyn wirtualnych |
**Typ dysku maszyny wirtualnej** |Typ magazynu wykonującego kopie zapasowe wszystkich wdrożonych węzłów. <br/> Aby dowiedzieć się więcej na temat dostępnych typów dysków, odwiedź stronę [Wybierz typ dysku](../../virtual-machines/windows/disks-types.md).|Standardowa, SSD <br/> Premium, SSD |

### <a name="multiple-vm-deployment-additional-settings"></a>Wdrożenie wielu maszyn wirtualnych (Ustawienia dodatkowe)

![Ustawienia sieci szkieletowej dla wielu wdrożeń maszyn wirtualnych](./media/hyperledger-fabric-consortium-blockchain/multiple-vm-deployment.png)

| Nazwa parametru | Opis | Dozwolone wartości |
|---|---|---|
**Liczba węzłów programu orderer** |Liczba węzłów, które porządkują (organizują) transakcje w bloku. <br />Dodatkowe szczegóły dotyczące usługi porządkowania można znaleźć w [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html) dotyczącej księgi |1 – 4 |
**Rozmiar maszyny wirtualnej węzła w programie orderer** |Rozmiar maszyny wirtualnej używany dla węzłów programu orderer w sieci|Saldo standardowe,<br />Standard DS,<br />Standard FS |
**Liczba węzłów równorzędnych** | Węzły należące do członków konsorcjum, które wykonują transakcje i utrzymują stan oraz kopię księgi.<br />Dodatkowe szczegóły dotyczące usługi porządkowania można znaleźć w [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html)dotyczącej księgi.|1 – 4 |
**Trwałość stanu węzła** |Aparat trwałości używany przez węzły równorzędne. Można skonfigurować ten aparat dla węzła równorzędnego. Poniżej znajdują się szczegółowe informacje dotyczące wielu węzłów równorzędnych.|CouchDB <br />LevelDB |
**Rozmiar maszyny wirtualnej węzła równorzędnego** |Rozmiar maszyny wirtualnej używany dla wszystkich węzłów w sieci|Saldo standardowe,<br />Standard DS,<br />Standard FS |

### <a name="multiple-peer-node-configuration"></a>Konfiguracja wielu węzłów równorzędnych

Ten szablon umożliwia wybranie aparatu trwałości dla węzła równorzędnego. Na przykład jeśli masz trzy węzły równorzędne, możesz użyć CouchDB na jeden i LevelDB na drugi.

![Konfiguracja wielu węzłów równorzędnych](./media/hyperledger-fabric-consortium-blockchain/multiple-peer-nodes.png)

Kliknij przycisk **OK**.

### <a name="deploy"></a>Wdrażanie

W obszarze **Podsumowanie**Przejrzyj określone dane wejściowe i uruchom podstawową weryfikację przed wdrożeniem.

![Podsumowanie](./media/hyperledger-fabric-consortium-blockchain/summary.png)

Przejrzyj warunki prawne i zachowania poufności informacji, a następnie wybierz pozycję **Kup** do wdrożenia. W zależności od liczby maszyn wirtualnych, czas wdrożenia może się różnić od kilku minut do dziesiątek minut.

## <a name="next-steps"></a>Następne kroki

Teraz można przystąpić do skoncentrowania się na programowaniu aplikacji i chaincode w sieci łańcucha bloków.
