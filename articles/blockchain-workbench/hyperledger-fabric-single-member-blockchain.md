---
title: Konsorcjum Hyperledger sieci szkieletowej
description: Szablon rozwiązania Hyperledger sieci szkieletowej konsorcjum umożliwia wdrażanie i konfigurowanie sieci pojedynczy element członkowski
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/21/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 7b60c086896506e5883607db48a64d2a2efbd967
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655452"
---
# <a name="hyperledger-fabric-single-member-network"></a>Sieć szkieletowa Hyperledger pojedynczy element członkowski sieci

Szablon rozwiązania Hyperledger sieci szkieletowej konsorcjum umożliwia wdrażanie i konfigurowanie sieci (wielowęzłowego) pojedynczy element członkowski Hyperledger sieci szkieletowej.

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę o blockchain Hyperledger sieci szkieletowej i bardziej skomplikowanych konsorcjum architektury sieci
- Dowiedz się, jak wdrożyć i skonfigurować jeden członek Hyperledger sieci szkieletowej konsorcjum sieć z portalu Azure

## <a name="about-blockchain"></a>O blockchain

Jeśli dopiero zaczynasz społeczności blockchain, jest bardzo możliwość Dowiedz się więcej o technologii w sposób łatwy i można skonfigurować na platformie Azure. Blockchain jest podstawową technologią za Bitcoin; jednak jest znacznie więcej niż tylko czynnik wirtualnego waluty. Jest złożone istniejącej bazy danych, rozproszony system i technologii szyfrowania, umożliwiający bezpieczny obliczeń wieloosobowa gwarancje wokół immutability, możliwość weryfikacji auditability i odporności na ataki. Różnych protokołów stosować różne mechanizmy zapewnienie te atrybuty. [Sieć szkieletowa Hyperledger](https://github.com/hyperledger/fabric) takie protokół.

## <a name="consortium-architecture-on-azure"></a>Architektura konsorcjum na platformie Azure

Ten szablon wdraża topologii ułatwia testowanie i symulować produkcji dla użytkowników w jednej organizacji (pojedynczy element członkowski). To wdrożenie składa się z wieloma węzłami sieci w pojedynczym regionie, wkrótce się można rozszerzyć, aby wielu regionach.

Sieć składa się z trzech typów węzłów:

1. **Węzeł elementu członkowskiego**: węzeł uruchomiona usługa członkostwa sieci szkieletowej, która rejestruje i zarządza członkami sieci. Ten węzeł po pewnym czasie mogą być klastrowane skalowalność i wysoką dostępność, jednak w tym laboratorium, będą używane węzła pojedynczy element członkowski.
2. **Węzły osoby zamawiającej**: węzeł uruchomiona usługa komunikacji Implementowanie gwarancji dostarczenia, takie jak Suma kolejność emisji lub atomic transakcji.
3. **Węzły równorzędne**: węzeł, który zatwierdza transakcji i obsługuje stanu oraz kopię księgi rozproszonych.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, należy subskrypcji Azure, która może obsługiwać kilka maszyn wirtualnych i kont magazynu w warstwie standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Domyślnie większość typów subskrypcji obsługuje topologii małe wdrożenie bez konieczności w celu zwiększenia limitu. Najmniejsza możliwa wdrożenia dla jednego elementu członkowskiego będą potrzebne:

- 5 maszyn wirtualnych (5 rdzenie)
- Sieć wirtualna 1
- Moduł równoważenia obciążenia 1
- 1 publicznego adresu IP

Po utworzeniu subskrypcji, przejdź do [portalu Azure](https://portal.azure.com). Wybierz **+**, wybierz pozycję **Blockchain**i wybierz **Hyperledger sieci szkieletowej pojedynczy element członkowski Blockchain**.

![Szablon Hyperledger sieci szkieletowej pojedynczy element członkowski Blockchain Marketplace](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Wdrożenie

Aby rozpocząć, wybierz **Hyperledger sieci szkieletowej pojedynczy element członkowski Blockchain** i kliknij przycisk **Utwórz**. Spowoduje to otwarcie **podstawy** bloku w kreatorze.

Wdrożenie szablonu przeprowadzi Cię przez konfigurację sieci z wieloma węzłami. Przepływ wdrożenia jest podzielone na trzy kroki: podstawy, konfiguracji sieci i konfiguracji sieci szkieletowej.

### <a name="basics"></a>Podstawy

W obszarze **podstawy** bloku, podaj wartości parametrów standardowe wszystkie wdrożenia, takich jak subskrypcji, grupy zasobów i właściwości podstawowej maszyny wirtualnej.

![Podstawy](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Prefiks zasobów**| Ciąg używany jako podstawa nazewnictwa wdrożonych zasobów.|6 znaków lub mniej|Nie dotyczy
**Nazwa użytkownika maszyny Wirtualnej**| Nazwa użytkownika administratora dla poszczególnych maszyn wirtualnych wdrożonych dla tego elementu członkowskiego.|1 - 64 znaków|użytkownik_azure
**Typ uwierzytelniania**| Metoda uwierzytelniania do maszyny wirtualnej.|Hasło lub SSH klucza publicznego|Hasło
**Hasło (typ uwierzytelniania = hasło)**|Hasło dla konta administratora dla każdego z wdrożonych maszyn wirtualnych. Hasło musi zawierać 3 następujących czynności: 1 wielką literą, 1 małe litery, liczby 1 i 1 znak specjalny.<br /><br />Gdy wszystkie maszyny wirtualne mają początkowo to samo hasło, należy zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|Nie dotyczy
**Klucz SSH (typ uwierzytelniania = klucz publiczny)**|Klucz bezpiecznego powłoki używane do logowania zdalnego.||Nie dotyczy
**Ograniczanie dostępu według adresu IP**|Ustawienie, aby określić typ, czy dostęp do punktu końcowego klienta jest ograniczony.|Tak/Nie| Nie
**Dozwolone adresu IP lub podsieci (ograniczanie dostępu według adresu IP = Yes)**|Adres IP lub zbiór adresów IP, który może uzyskać dostęp do punktu końcowego klienta, gdy jest włączona kontrola dostępu.||Nie dotyczy
**Subskrypcja** |Subskrypcja, dla której chcesz wdrożyć.
**Grupa zasobów** |Grupy zasobów, dla której chcesz wdrożyć konsorcjum sieci.||Nie dotyczy
**Lokalizacja** |Region platformy Azure, dla której chcesz wdrożyć pierwszego elementu członkowskiego ** s rozmiaru sieci.

### <a name="network-size-and-performance"></a>Rozmiar sieci i wydajności

Następnie w obszarze **sieci rozmiaru i wydajności,** Określ dane wejściowe dla rozmiaru konsorcjum sieci, takie jak liczba członkostwa, osoby zamawiającej i węzły równorzędne. Wybierz opcje infrastruktury i rozmiar maszyny wirtualnej.

![Rozmiar sieci i wydajności](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Liczba węzłów członkostwa**|Liczba węzłów, które uruchamiania usługi członkostwa. Dodatkowe szczegóły dotyczące usługi członkostwa, obejrzyj zabezpieczeń i usługą członkostwa w obszarze Hyperledger [dokumentacji](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Ta wartość jest obecnie ograniczone do węzła 1, ale planujemy do obsługi skalowania w poziomie Dzięki klastrom w następnej wersji.|1| 1
**Liczba węzłów osoby zamawiającej** |Liczba węzłów, które kolejność (organizowanie) transakcji w bloku.--> to instrukcja jest długi i mylące. Dodatkowe szczegóły dotyczące zamawiania usługi, odwiedź stronę Hyperledger [dokumentacji](http://hyperledger-fabric.readthedocs.io/en/latest/orderingservice.html).<br /><br />Ta wartość jest obecnie ograniczone do węzła 1. |1 |1
**Liczba węzłów równorzędnych**| Węzły, które są własnością konsorcjum elementów członkowskich, które wykonania transakcji i zachować stan i kopię księgi.<br /><br />Dodatkowe szczegóły dotyczące zamawiania usługi, odwiedź stronę Hyperledger [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**Wydajność magazynu**|Typ magazynu kopii każdego z węzłów wdrożone. Aby dowiedzieć się więcej na temat magazynu, odwiedź stronę [wprowadzenie do usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) i [magazyn w warstwie Premium](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standard lub Premium|Standardowa (Standard)
**Rozmiar maszyny wirtualnej** |Rozmiar maszyny wirtualnej, używany dla wszystkich węzłów w sieci|Standard A<br />Standardowa D<br />Standardowa D-v2<br />Standardowa serii F,<br />Standardowa DS<br />i standardowe FS|Standardowe D1_v2

### <a name="fabric-specific-settings"></a>Określone ustawienia sieci szkieletowej

Ponadto w obszarze **ustawienia sieci szkieletowej**, określ ustawienia konfiguracji odnoszące się do sieci szkieletowej.

![Ustawienia sieci szkieletowej](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Nazwa użytkownika ładowania początkowego**| Początkowy uprawnień użytkownika, który zostanie zarejestrowany usługi Członkowskie w wdrożone sieci.|9 znaków|Administrator
**Hasło użytkownika ładowania początkowego dla urzędu certyfikacji sieci szkieletowej**|Hasło administratora, używany do zabezpieczania konta urzędu certyfikacji sieci szkieletowej zaimportować do węzła członkostwa.<br /><br />Hasło musi zawierać jedną wielką literą, jeden znak małe litery i jeden numer.|12 znaków|Nie dotyczy

### <a name="deploy"></a>Wdrażanie

W **Podsumowanie**, sprawdź określone dane wejściowe i uruchomić podstawowe sprawdzanie poprawności przed wdrożeniem.

![Podsumowanie](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Przejrzyj postanowienia prawne i ochrona prywatności i kliknij przycisk **zakupu** do wdrożenia. W zależności od liczby maszyn wirtualnych obsługiwana administracyjnie czas wdrażania może się różnić od kilku minut do dziesiątki minut.

### <a name="accessing-nodes"></a>Uzyskiwanie dostępu do węzłów

Po zakończeniu wdrożenia **omówienie** jest wyświetlany.

![Wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Jeśli ekran nie zostanie wyświetlone automatycznie (może być po przeniesieniu wokół portalu zarządzania podczas wdrażania było uruchomione), zawsze można go znaleźć na karcie grupy zasobów na pasku nawigacyjnym po lewej stronie. Kliknij nazwę grupy zasobów, wprowadzona w kroku 1, aby przejść do **omówienie** strony.

Opis zawiera listę wszystkich zasobów, które zostały wdrożone przez szablon rozwiązania. Można je eksplorować momencie, ale na tym ekranie można także przejść do _parametry wyjściowe_ generowane przez szablon. Te parametry wyjściowe zapewni informacje przydatne podczas nawiązywania połączenia z siecią szkieletową Hyperledger.

Aby uzyskać dostęp do parametrów wyjściowych, najpierw kliknij **wdrożeń** kartę w bloku grupy zasobów. Historia wdrażania jest wyświetlany.

![Historia wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Historia wdrożenia kliknij przy pierwszym wdrożeniu na liście, aby zobaczyć szczegóły.

![Szczegóły wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Ekran szczegółów wyświetli podsumowanie wdrożenia następują trzy parametry przydatne dane wyjściowe:

- _Punkt końcowy interfejsu API_ służy po wdrażanie aplikacji w sieci.
- _PREFIKS_ , nazywany również _prefiks wdrożenia_ , unikatowo identyfikuje zasobów i wdrożenia. Będzie używany, korzystając z narzędzi wiersza polecenia.
- _SSH do pierwszej wirtualna_ zapewnia można wstępnie złożony polecenie ssh z prawej parametry wymagane do nawiązania pierwszego maszyny Wirtualnej w sieci; w przypadku sieci szkieletowej Hyperledger, będzie ona węzeł urzędu certyfikacji sieci szkieletowej.

Możesz zdalnie nawiązać maszyn wirtualnych dla każdego węzła za pośrednictwem protokołu SSH z klucz administratora podana nazwa użytkownika i hasło/SSH. Ponieważ węzeł maszyn wirtualnych nie ma własnych publicznych adresów IP, należy przejść przez moduł równoważenia obciążenia i określ numer portu. Polecenie SSH, aby otworzyć pierwszy węzeł transakcji jest trzeci dane wyjściowe szablonu ** SSH do pierwszej VM (dla Przykładowe wdrożenie: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Aby przejść do transakcji dodatkowe węzły, zwiększenie numeru portu przez jedną (na przykład pierwszy węzeł transakcji jest port 3000, drugi na 3001, jest trzeci na 3002, itp.).

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do aplikacji i rozwoju chaincode przed Hyperledger konsorcjum blockchain sieci.
