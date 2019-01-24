---
title: Jeden element członkowski Hyperledger Fabric konsorcjum
description: Szablon rozwiązania Hyperledger Fabric konsorcjum umożliwia wdrażanie i konfigurowanie sieci jeden element członkowski
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/29/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: a6aa6d925d47ad9d24de68342f4a6e76a1d9d81f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828234"
---
# <a name="hyperledger-fabric-single-member-network"></a>Hyperledger Fabric jeden element członkowski sieci

Za pomocą szablonu rozwiązania Hyperledger Fabric konsorcjum można wdrożyć i skonfigurować sieć Hyperledger Fabric jeden element członkowski (z wieloma węzłami).

Zapoznanie się z tym artykułem umożliwi:

- Uzyskaj praktyczną wiedzę na temat łańcucha bloków, Hyperledger Fabric i bardziej skomplikowanych konsorcjum architektury sieci
- Dowiedz się, jak wdrożyć i skonfigurować jednym członkiem Hyperledger Fabric konsorcjum sieci, w witrynie Azure Portal

## <a name="about-blockchain"></a>Temat łańcucha bloków

Jeśli jesteś nowym użytkownikiem społecznością łańcucha bloków, ten szablon rozwiązania to znakomita szansa, aby dowiedzieć się więcej o technologii w sposób łatwy i można je konfigurować na platformie Azure. Łańcuch bloków to podstawowy technologia Bitcoin; jednak jest znacznie więcej niż tylko włącznik wirtualnego waluty. Jest złożone istniejącą bazę danych, systemami rozproszonymi i technologii szyfrowania, umożliwiająca bezpieczne wieloosobowa obliczeń z gwarancje w zakresie niezmienności, możliwość weryfikacji, sprawdzalność i odporności na ataki. Różnych protokołów używają różnych mechanizmów zapewnienie tych atrybutów. [Hyperledger Fabric](https://github.com/hyperledger/fabric) jest jeden taki protokół.

## <a name="consortium-architecture-on-azure"></a>Architektura konsorcjum na platformie Azure

Ten szablon umożliwia wdrożenie w topologii w celu testowania i symulacjach dla użytkowników w ramach jednej organizacji (jeden element członkowski). To wdrożenie składa się z wieloma węzłami sieci w jednym regionie, wkrótce, aby rozszerzyć do wielu regionów.

Sieć składa się z trzech typów węzłów:

1. **Węzeł składowej**: Węzła z systemem usługi członkostwo w sieci szkieletowej, która rejestruje i zarządza członków sieci. Ten węzeł może być klastrowane w celu zapewnienia skalowalności i wysokiej dostępności. Jednak w tym środowisku laboratoryjnym węzła jeden element członkowski będzie używany.
2. **Węzły osoby zamawiającej**: Węzeł, implementowanie gwarancji dostarczania, takich jak łączna liczba kolejności odpowiedniej do emisji lub transakcje niepodzielne usługą komunikacji.
3. **Komunikacja równorzędna węzłów**: Węzeł, zatwierdzeń transakcji, która przechowuje stan i kopię rejestru rozproszonego.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, potrzebujesz subskrypcji platformy Azure, który może obsługiwać wdrażanie kilka maszyn wirtualnych i kont magazynu w warstwie standardowa. Jeśli nie masz subskrypcji platformy Azure, możesz to zrobić [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

Domyślnie większość typów subskrypcji obsługuje topologii, małe wdrożenie bez konieczności zwiększyć limit przydziału. Najmniejsza możliwe wdrożenie dla jednego elementu członkowskiego będą potrzebne:

- 5 maszyn wirtualnych (5 rdzeni)
- 1 VNet
- Moduł równoważenia obciążenia 1
- 1 publiczny adres IP

Po utworzeniu subskrypcji, przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **+**, wybierz opcję **łańcucha bloków**i wybierz **łańcucha bloków elementu członkowskiego pojedynczego Hyperledger Fabric**.

![Szablon Hyperledger Fabric jednego elementu członkowskiego łańcucha bloków w witrynie Marketplace](./media/hyperledger-fabric-single-member-blockchain/marketplace-template.png)

## <a name="deployment"></a>Wdrożenie

Aby rozpocząć, wybierz opcję **łańcucha bloków elementu członkowskiego pojedynczego Hyperledger Fabric** i kliknij przycisk **Utwórz** otworzyć **podstawy** bloku, w kreatorze.

Wdrożenie szablonu przeprowadzi Konfigurowanie sieci z wieloma węzłami. Przepływ wdrożenia jest podzielony na trzy kroki: Podstawowe informacje o konfiguracji sieci i konfiguracji sieci szkieletowej.

### <a name="basics"></a>Podstawy

W **podstawy** bloku określ wartości dla parametrów standardowego dla każdego wdrożenia. Takie jak subskrypcji, grupy zasobów i podstawowe wirtualne maszyny właściwości.

![Podstawy](./media/hyperledger-fabric-single-member-blockchain/basics.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Prefiks zasobów**| Ciąg używany jako podstawa nazewnictwa wdrożonych zasobów.|6 znaków lub mniej|Nie dotyczy
**Nazwa użytkownika maszyny Wirtualnej**| Nazwa użytkownika administratora dla poszczególnych maszyn wirtualnych wdrożonych dla tego elementu członkowskiego.|1 - 64 znaki|użytkownik_azure
**Typ uwierzytelniania**| Metoda uwierzytelniania do maszyny wirtualnej.|Klucz publiczny hasła lub protokołu SSH|Hasło
**Hasło (typ uwierzytelniania = hasło)**|Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Hasło musi zawierać trzy z następujących typów znaków: 1 Wielka litera, 1 mała litera, 1 cyfra i 1 znak specjalny.<br /><br />Gdy wszystkie maszyny wirtualne początkowo miały to samo hasło, możesz zmienić hasło po zainicjowaniu obsługi administracyjnej.|12 - 72 znaków|Nie dotyczy
**Klucz SSH (typ uwierzytelniania = klucz publiczny)**|Klucz protokołu secure shell, używany do logowania zdalnego.||Nie dotyczy
**Ograniczanie dostępu przy użyciu adresu IP**|Ustawienie, aby określić typ, czy dostęp do punktu końcowego klienta jest ograniczony, czy nie.|Tak/Nie| Nie
**Dozwolony adres IP lub podsieć (ograniczanie dostępu przy użyciu adresu IP = Yes)**|Adres IP lub zbiór adresów IP, która mogła uzyskać dostęp do punktu końcowego klienta, gdy kontrola dostępu jest włączona.||Nie dotyczy
**Subskrypcja** |Subskrypcja, dla której chcesz wdrożyć.
**Grupa zasobów** |Grupa zasobów, dla której chcesz wdrożyć sieci konsorcjum.||Nie dotyczy
**Lokalizacja** |Region platformy Azure, dla której chcesz wdrożyć pierwszego elementu członkowskiego ** s sieciowych zużycia.

### <a name="network-size-and-performance"></a>Network Activity block size i wydajności

Następnie w **rozmiaru i wydajność sieci** Określ dane wejściowe dla rozmiaru sieci konsorcjum. Takie jak liczba członkostwa, osoby zamawiającej i węzły równorzędne. Wybierz opcje infrastruktury i rozmiar maszyny wirtualnej.

![Network Activity block size i wydajności](./media/hyperledger-fabric-single-member-blockchain/network-size-performance.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Liczba węzłów członkostwa**|Liczba węzłów, które Uruchom usługę członkostwa. Więcej informacji na temat usługi członkostwa, Przyjrzyj się zabezpieczeń i członkostwa usługi na podstawie Hyperledger [dokumentacji](https://media.readthedocs.org/pdf/hyperledger-fabric/latest/hyperledger-fabric.pdf).<br /><br />Ta wartość jest obecnie ograniczone do 1 węzła, ale planujemy do obsługi skalowania w poziomie Dzięki klastrom w następnej wersji.|1| 1
**Liczba węzłów osoby zamawiającej** |Liczba węzłów, których kolejność (organizowanie) transakcji w bloku.--> to instrukcja jest oddechu i pozostał przejrzysty. Więcej informacji na temat usługi zamawiania, odwiedź stronę Hyperledger [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/release-1.1/ordering-service-faq.html).<br /><br />Ta wartość jest obecnie ograniczone do 1 węzła. |1 |1
**Liczba węzłów równorzędnych**| Węzły, które są własnością członkowie konsorcjum, wykonywać transakcje, które Obsługa stanu i kopię rejestru.<br /><br />Więcej informacji na temat usługi zamawiania, odwiedź stronę Hyperledger [dokumentacji](https://hyperledger-fabric.readthedocs.io/en/latest/glossary.html).|3| 3 - 9
**Wydajność magazynu**|Typ magazynu kopii każdego wdrożonego węzła. Aby dowiedzieć się więcej na temat magazynu, odwiedź stronę [wprowadzenie do usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) i [usługi Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage).|Standardowa lub Premium|Standardowa (Standard)
**Rozmiar maszyny wirtualnej** |Rozmiar maszyny wirtualnej, używany dla wszystkich węzłów w sieci|Standardowa A<br />Standard D<br />Standardowa D-v2<br />Standardowa serii F<br />Standard DS<br />i FS standardowe|Standardowa D1_v2

### <a name="fabric-specific-settings"></a>Ustawienia specyficzne dla sieci szkieletowej

Na koniec w obszarze **ustawień sieci szkieletowej**, określ ustawienia konfiguracji odnoszące się do sieci szkieletowej.

![Ustawienia sieci szkieletowej](./media/hyperledger-fabric-single-member-blockchain/fabric-settings.png)

Nazwa parametru| Opis| Dozwolone wartości|Wartość domyślna
---|---|---|---
**Nazwa użytkownika bootstrap**| Początkowy uprawnień użytkownika, który ma zostać zarejestrowany z usługami elementu członkowskiego w wdrożone sieci.|więcej niż 9 znaków|admin
**Hasło użytkownika ładowania początkowego dla urzędu certyfikacji w sieci szkieletowej**|Hasło administratora, używany do zabezpieczania konta urzędu certyfikacji w sieci szkieletowej zaimportować do węzła członkostwa.<br /><br />Hasło musi zawierać jedną wielką literę, jedną małą literę i jedną cyfrę.|co najmniej 12 znaków.|Nie dotyczy

### <a name="deploy"></a>Wdrażanie

W **Podsumowanie**, przejrzyj dane wejściowe określone i aby uruchomić podstawową walidację przed wdrożeniem.

![Podsumowanie](./media/hyperledger-fabric-single-member-blockchain/summary.png)

Przejrzyj postanowienia prawne i ochrona prywatności, a następnie kliknij przycisk **zakupu** do wdrożenia. W zależności od liczby maszyn wirtualnych aprowizowane czas wdrażania może się różnić od kilku minut do dziesiątek minut.

### <a name="accessing-nodes"></a>Uzyskiwanie dostępu do węzłów

Po zakończeniu wdrożenia **Przegląd** jest wyświetlana.

![Wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployments.png)

Jeśli ekran nie pojawia się automatycznie (może być po przeniesieniu wokół portalu zarządzania podczas wdrażania była uruchomiona), zawsze można je znaleźć na karcie grupy zasobów, na pasku nawigacyjnym po lewej stronie. Kliknij nazwę grupy zasobów zostały wprowadzone w kroku 1, aby przejść do **Przegląd** strony.

Opis zawiera listę wszystkich zasobów, które zostały wdrożone za pomocą szablonu rozwiązania. Możesz je eksplorować w będą jednak na tym ekranie możesz także uzyskać dostęp _parametrów wyjściowych_ generowane przez szablon. Te parametry wyjściowe zapewni Ci informacje przydatne podczas nawiązywania połączenia z siecią Hyperledger Fabric.

Aby uzyskać dostęp do parametrów wyjściowych, najpierw kliknij **wdrożeń** karty w bloku grupy zasobów. Zostanie wyświetlona historia wdrażania.

![Historia wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployment-history.png)

Z historii wdrożenia kliknij przy pierwszym wdrożeniu na liście, aby wyświetlić szczegóły.

![Szczegóły wdrożenia](./media/hyperledger-fabric-single-member-blockchain/deployment-details.png)

Ekran szczegółów Pokaż podsumowanie wdrożenia następują trzy parametry przydatne dane wyjściowe:

- _Punkt końcowy interfejsu API_ mogą być używane raz wdrażania aplikacji w sieci.
- _PREFIKS_ , nazywane również _prefiks wdrożenia_ , unikatowo identyfikuje zasobów oraz informacje o wdrożeniu. Będzie używany, korzystając z narzędzi wiersza polecenia.
- _SSH do pierwszej VM_ zapewnia możesz wstępnie zmontowanych polecenie ssh przy użyciu odpowiednie parametry wymagane do połączenia z pierwszej maszyny Wirtualnej w sieci. Dla Hyperledger Fabric będzie ona węzeł urzędu certyfikacji sieci szkieletowej.

Możesz zdalnie nawiązać maszyn wirtualnych, dla każdego węzła za pośrednictwem protokołu SSH kluczem administrator podanej nazwy użytkownika i hasła/SSH. Ponieważ maszyn wirtualnych węzła nie mają własnych publicznych adresów IP, należy przejść przez moduł równoważenia obciążenia i określ numer portu. Polecenie SSH, aby otworzyć pierwszy węzeł transakcji jest trzeci wynik szablonu *SSH do pierwszej VM* (Aby uzyskać przykładowe wdrożenie: `sh -p 3000 azureuser@hlf2racpt.northeurope.cloudapp.azure.com`). Aby przejść do transakcji dodatkowe węzły, należy zwiększyć numer portu za pomocą jednej (na przykład, pierwszy węzeł transakcji jest port 3000, drugi na 3001, trzeci na 3002, itp.).

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz przystąpić do skupić się na aplikacji i rozwój chaincode względem sieć Hyperledger konsorcjum łańcucha bloków.
