---
title: Uruchamianie klastra oprogramowania Cassandra w systemie Linux na platformie Azure z poziomu środowiska Node.js
description: Jak uruchomić klaster bazy danych Cassandra w systemie Linux w usłudze Azure Virtual Machines z poziomu aplikacji Node.js
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: ''
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: 3066da9a492fc12dd8b333a089b8aabbbb647414
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50421360"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Uruchamianie klastra oprogramowania Cassandra w systemie Linux na platformie Azure przy użyciu środowiska Node.js

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrażania. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Szablony usługi Resource Manager dla [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) i [prowadzenie klastra i bazy danych Cassandra w CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Przegląd
Microsoft Azure to platforma open chmury, która działa zarówno firmy Microsoft, jak i oprogramowanie firm innych niż Microsoft, w tym systemów operacyjnych, serwerów aplikacji, oprogramowanie pośredniczące obsługi komunikatów, a także bazy danych SQL i NoSQL w z obu modeli komercyjnych typu open source. Kompilowanie odpornych usług w chmurach publicznych, takich jak Azure wymaga starannego planowania i architektury zamierzonego dla obu serwerów aplikacji jako warstwy magazynowania dobrze. Architektura rozproszona magazynu bazy danych Cassandra w naturalny sposób ułatwia budowanie systemów o wysokiej dostępności, które są odporne na uszkodzenia na wypadek awarii klastra. Bazy danych Cassandra jest skali chmury utrzymywana przez Apache Software Foundation cassandra.apache.org baza danych NoSQL. Bazy danych Cassandra został napisany w języku Java. Dlatego działa zarówno na platformach Windows i Linux.

Ten artykuł koncentruje się na Pokaż wdrożenie oprogramowania Cassandra w systemie ubuntu działającym jako klaster pojedynczych i wielu data center, korzystającą z usługi Azure Virtual Machines i sieciami wirtualnymi. Wdrożenie klastra dla obciążeń produkcyjnych, zoptymalizowane pod kątem jest poza zakres tego artykułu, ponieważ wymaga konfiguracji węzła z wieloma dyskami, projekt topologia pierścienia odpowiednie i modelowania danych, aby obsługiwać wymagane replikacji, wyjaśnienie pojęcia spójności danych, przepływności i dużej wymagania dotyczące dostępności.

Ten artykuł ma podstawowe podejścia do wyświetlenia, co jest zaangażowana w tworzenie klastra oprogramowania Cassandra platformy Docker, Chef lub Puppet, które mogą ułatwić wdrożenie infrastruktury znacznie w porównaniu.  

## <a name="the-deployment-models"></a>Modele wdrażania
Sieć Microsoft Azure umożliwia wdrożenie izolowane klastrów prywatnej dostępu, które może być ograniczone do osiągnięcia zabezpieczenia szczegółowe sieci.  Ponieważ ten artykuł dotyczy przedstawiający wdrożenie oprogramowania Cassandra na podstawowym poziomie, nie skoncentrować się na poziom spójności i projektowania magazynu optymalnej przepływności. Poniżej przedstawiono listę wymagań sieciowych dotyczących hipotetyczny klastra:

* Systemy zewnętrzne nie może uzyskać dostępu baza danych Cassandra z w obrębie lub poza systemem Azure
* Klastra oprogramowania Cassandra, musi mieć za modułem równoważenia obciążenia dla ruchu thrift
* Wdróż węzły bazy danych Cassandra w dwóch grupach w każdej centrum danych pod względem dostępności klastra rozszerzone
* Zablokowanie klastra tak, tylko do farmy serwerów aplikacji ma dostęp do bazy danych bezpośrednio
* Nie publicznych punktów końcowych sieci innych niż SSH
* Każdy węzeł bazy danych Cassandra wymaga stałej wewnętrzny adres IP

Bazy danych Cassandra można wdrożyć w pojedynczym regionie platformy Azure lub do wielu regionów, w oparciu o Rozproszony charakter obciążenia. Można użyć modelu wdrożenie w wielu regionach, ma obsługiwać użytkowników końcowych bliżej do określonej lokalizacji geograficznej przy użyciu tej samej infrastruktury bazy danych Cassandra. Cassandra dla wbudowanego węzła replikacji przyjmuje opieki synchronizacji Multi-Master zapisuje pochodzących z wielu centrach danych i przedstawia spójny widok danych do aplikacji. Zmniejszenie ryzyka szersze przerwy w działaniu usługi platformy Azure może również ułatwić wdrażanie w wielu regionach. Firmy Cassandra dostosowania spójności i topologię replikacji ułatwia spełniających różne potrzeby RPO aplikacji.

### <a name="single-region-deployment"></a>Wdrażanie jednego regionu
Teraz uruchom z wdrożeniem pojedynczego regionu i zebrać wnioski dotyczące tworzenia modelu wielu regionów. Sieci wirtualne platformy Azure umożliwia utworzyć odizolowane podsieci, mogą zostać spełnione wymagania dotyczące zabezpieczeń sieci, które zostały wymienione powyżej.  Proces opisany w tworzenie wdrożenia jednego regionu używa Ubuntu 14.04 LTS i Cassandra 2.08. Jednak ten proces można łatwo przyjąć inne wariantów systemu Linux. Poniżej przedstawiono niektóre właściwości systemowych wdrożenia w pojedynczym regionie.  

**Wysoka dostępność:** węzły bazy danych Cassandra przedstawionej na rysunku 1 są wdrażane dwa zestawy dostępności, aby węzły są rozproszone między wiele domen błędów w celu zapewnienia wysokiej dostępności. Maszyny wirtualne, oznaczony za pomocą każdego zestawu dostępności jest mapowany do 2 domen błędów. Platforma Azure używa pojęcia domeny błędów, zarządzanie nieplanowane przestoje (na przykład awarii sprzętu lub oprogramowania). Pojęcia domeny uaktualnień (na przykład host lub stosowanie poprawek systemu operacyjnego gościa bądź uaktualnień, uaktualnieniami aplikacji) jest używany do zarządzania zaplanowany czas przestoju. Zobacz [odzyskiwania po awarii i wysoka dostępność dla aplikacji platformy Azure](http://msdn.microsoft.com/library/dn251004.aspx) roli domen w domenach błędów i uaktualnień w celu osiągnięcia wysokiej dostępności.

![Wdrażanie jednego regionu](./media/cassandra-nodejs/cassandra-linux1.png)

Rysunek 1. Wdrożenie pojedynczego regionu

Należy pamiętać, w czasie pisania tego dokumentu, Azure nie dopuszcza jawnego mapowania grupy maszyn wirtualnych do określonych błędów domeny; w związku z tym nawet w przypadku modelu wdrażania, przedstawionej na rysunku 1, jest statystycznie prawdopodobne, że wszystkie maszyny wirtualne mogą być mapowane na dwie domeny błędów zamiast czterech.

**Obciążenia równoważenia ruchu Thrift:** bibliotek klienta Thrift wewnątrz serwera sieci web łączenia z klastrem przy użyciu wewnętrznego modułu równoważenia obciążenia. Wymaga to proces dodawania wewnętrznego modułu równoważenia obciążenia z podsiecią "data" (zobacz rysunek 1) w kontekście usługa w chmurze hostująca klastra oprogramowania Cassandra. Po zdefiniowaniu wewnętrznego modułu równoważenia obciążenia poszczególnych węzłów wymaga punktu końcowego o zrównoważonym obciążeniu do dodania przy użyciu adnotacji z nazwą modułu równoważenia obciążenia wcześniej zdefiniowanego zestawu z równoważeniem obciążenia. Zobacz [wewnętrzne Równoważenie obciążenia Azure ](../../../load-balancer/load-balancer-internal-overview.md)Aby uzyskać więcej informacji.

**Wartości początkowe klastra:** jest ważne wybrać Większość węzłów o wysokiej dostępności dla wartości początkowe, ponieważ nowe węzły komunikować się z węzły obiektów początkowych umożliwia odnalezienie topologii klastra. Jednego węzła z każdym zestawie dostępności nazw jest wyznaczony jako węzły obiektów początkowych w celu uniknięcia pojedynczego punktu awarii.

**Współczynnik replikacji i poziomu spójności:** bazy danych Cassandra w kompilacji w wysokiej dostępności i danych trwałości charakteryzuje się współczynnik replikacji (RF — liczba kopii każdego wiersza, przechowywane w klastrze) i poziom spójności (liczba replik być odczytanych/zapisanych przed zwróceniem wyniku do obiektu wywołującego). Współczynnik replikacji jest określone podczas tworzenia przestrzeni KLUCZY (podobnie jak w relacyjnej bazie danych), natomiast poziom spójności jest określany podczas wystawiania zapytań CRUD. Zobacz dokumentację bazy danych Cassandra w [Konfigurowanie w celu zachowania spójności](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlConfigConsistency.html) szczegóły spójności i formuła do obliczenia kworum.

Bazy danych Cassandra obsługuje dwa typy modeli integralności danych — spójność i spójność ostateczną; Współczynnik replikacji i poziomu spójności Określ Jeśli dane są spójne, gdy tylko operacja zapisu jest ukończona lub ostatecznie spójną ze sobą. Na przykład określając KWORUM, zgodnie z poziomu spójności zawsze zapewnia spójność danych, podczas dowolnego poziomu spójności mniejszej niż liczba replik w celu zapisania w celu osiągnięcia KWORUM, (na przykład jeden) powoduje danych jest ostatecznie spójny.

8 węzłów klastra, pokazana powyżej, za pomocą współczynnik replikacji równy 3 i KWORUM (2 węzły są odczytywanych lub zapisywanych w celu zachowania spójności) poziom spójności odczytu/zapisu, mogą przetrwać teoretycznych utraty maksymalnie 1 węzeł na grupę replikacji przed obserwowanie uruchamiania aplikacji Wystąpił błąd. Przyjęto założenie, że wszystko, czego klucza miejsca do magazynowania mają dobrze zrównoważone odczytu/zapisu żądania.  Poniżej przedstawiono parametry używane dla wdrożonego klastra:

Konfiguracja klastra Cassandra w jednym regionie:

| Parametr klastra | Wartość | Uwagi |
| --- | --- | --- |
| Liczba węzłów (N) |8 |Całkowita liczba węzłów w klastrze |
| Współczynnik replikacji (RF) |3 |Liczba replik w danym wierszu |
| Poziom spójności (zapis) |QUORUM[(RF/2) +1) = 2 wynik formuły jest zaokrąglona w dół |Zapisuje maksymalnie 2 repliki przed wysłaniem odpowiedzi do obiektu wywołującego 3 repliki są zapisywane w ostatecznie spójny sposób. |
| Poziom spójności (odczyt) |KWORUM [(RF/2) + 1 = 2] wynik formuły jest zaokrąglona w dół |Odczytuje 2 repliki przed wysłaniem odpowiedzi do obiektu wywołującego. |
| Strategia replikacji |Zobacz NetworkTopologyStrategy [replikacji danych](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) w dokumentacji oprogramowania Cassandra, aby uzyskać więcej informacji |Zrozumienie topologii wdrożenia, a następnie umieszcza replik w węzłach, tak, aby wszystkie repliki nie znajdą się w tej samej stojak |
| Snitch |Zobacz GossipingPropertyFileSnitch [przełączników](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) w dokumentacji oprogramowania Cassandra, aby uzyskać więcej informacji |NetworkTopologyStrategy korzysta z koncepcji snitch zrozumienie topologii. GossipingPropertyFileSnitch daje lepszą kontrolę w mapowaniu każdego węzła do centrum danych i stojaka. Klaster używa następnie plotki Propagacja te informacje. Jest to znacznie prostsze w ustawieniu dynamicznego adresu IP w względem PropertyFileSnitch |

**Azure dotyczące klastra oprogramowania Cassandra:** funkcja Microsoft Azure Virtual Machines używa usługi Azure Blob storage trwałość dysku; Usługa Azure Storage zapisuje trzech replik każdego dysku w celu zapewnienia wysokiej niezawodności. Oznacza to, że każdy wiersz danych wstawione do tabeli bazy danych Cassandra jest już przechowywany w trzech replik. Dlatego wyjaśnienie pojęcia spójności danych jest już wykonywane nawet, jeśli współczynnik replikacji (RF) ma wartość 1. Główny problem ze współczynnikiem replikacji jest 1 jest aplikacja napotyka przestojów, nawet w przypadku awarii jednego węzła bazy danych Cassandra. Jednak jeśli węzeł nie działa w przypadku problemów (na przykład sprzęt, błędy oprogramowania systemu) rozpoznawany przez kontroler sieci szkieletowej platformy Azure, obsługuje nowy węzeł, w tym miejscu przy użyciu tych samych dysków magazynu. Aprowizacja nowego węzła do zastąpi stare hasło może potrwać kilka minut.  Podobnie dla działań planowanej konserwacji, takich jak zmiany systemu operacyjnego gościa, uaktualnienia bazy danych Cassandra, i wprowadzania zmian w aplikacji Kontroler sieci szkieletowej Azure wykonuje stopniowego uaktualnienia węzłów w klastrze.  Uaktualnień stopniowych może skorzystać z kilku węzłów w dół w danym momencie, a tym samym klastrze mogą wystąpić krótki przestój w przypadku kilku partycji. Jednak dane nie jest utracone z powodu wbudowane funkcje nadmiarowości usługi Azure Storage.  

Dla systemów wdrożona na platformie Azure, która nie wymaga wysokiej dostępności (na przykład około 99,9 co odpowiada 8.76 godz. / rok; zobacz [wysokiej dostępności](http://en.wikipedia.org/wiki/High_availability) Aby uzyskać szczegółowe informacje) można uruchomić z RF = 1 i poziom spójności = jeden.  W przypadku aplikacji z wymaganiami wysokiej dostępności, RF = 3 i poziomu spójności = KWORUM zaakceptować czas przestoju jednego z węzłów z repliki. RF = 1 w przypadku tradycyjnych wdrożeń (na przykład w środowisku lokalnym) nie można używać z powodu możliwej utracie danych wynikające z problemów, takich jak awarii dysku.   

## <a name="multi-region-deployment"></a>Wdrożenie w wielu regionach
Bazy danych Cassandra w danych center-aware replikacji i spójność modelu opisanych powyżej ułatwia wdrożenie w wielu regionach bez potrzeby dowolnych narzędzi zewnętrznych. Jest to różni się od tradycyjnych relacyjnych baz danych, której Instalator obsługę dublowania bazy danych do wielu wzorców zapisu może być złożonym procesem. Bazy danych Cassandra w wielu regionach konfiguracji może ułatwić scenariusze użycia scenariuszy, w tym:

**Odległości między elementami na podstawie wdrożenia:** wielodostępnych aplikacji za pomocą zwykłego mapowania użytkowników dzierżawy-do-regionu, można korzystali przez klaster obejmujący wiele regionów niskimi opóźnieniami. Na przykład learning systemy zarządzania dla instytucji edukacyjnych można wdrożyć w klastrze rozproszonym w regionach wschodnie stany USA i zachodnie stany USA, aby obsługiwać odpowiednich kampusy dla transakcji, a także analizy. Dane można lokalnego spójne w czasie odczytu i zapisu i może być ostatecznie spójną w obu regionach. Inne przykłady takich jak dystrybucji multimediów, handlu elektronicznego i nic i wszystko, czego służy geograficznie skoncentrowane użytkownika podstawowego jest przypadek użycia dobre dla tego modelu wdrażania.

**Wysoka dostępność:** nadmiarowość jest kluczowym czynnikiem w celu osiągnięcia wysokiej dostępności sprzętu i oprogramowania; Zobacz tworzenie niezawodnych systemów w chmurze Microsoft Azure, aby uzyskać szczegółowe informacje. Microsoft Azure tylko niezawodnie osiągnięcia true nadmiarowość jest, wdrażając klaster obejmujący wiele regionów. Aplikacje można wdrożyć w trybie aktywny aktywny lub aktywny / pasywny, a jeśli jeden z regionów działa, usługa Azure Traffic Manager może przekierowywanie ruchu do aktywnym regionem.  Przy użyciu wdrażania jednego regionu, w przypadku dostępności 99,9, wdrożenia w dwóch regionach może osiągnąć dostępności pracy 99,9999 obliczane według wzoru: (1-(1-0.999) * (1-0.999)) * 100); zobacz dokument powyżej, aby uzyskać szczegółowe informacje.

**Odzyskiwanie po awarii:** klastra oprogramowania Cassandra w wielu regionach, jeśli jest odpowiednio zaprojektowane wytrzymać awarii centrum danych w wyniku katastrofy. Jeśli jeden region jest wyłączony, można uruchomić aplikacji wdrożonej w innych regionach, obsługująca użytkowników końcowych. Jak żadnych innych firm ciągłości implementacji aplikacji musi być odporny na błędy dla utratę danych, wynikających z danych w potoku asynchronicznego. Jednak bazy danych Cassandra sprawia, że odzyskiwanie znacznie usprawnić niż czas trwania procesów odzyskiwania tradycyjnych baz danych. Rysunek 2 przedstawia model typowe wdrożenie w wielu regionach, z ośmiu węzłów w każdym regionie. Oba regiony są lustrzanych odbiciach na siebie tego samego symetrii; projekty rzeczywistych zależą od typu obciążenia (na przykład transakcyjne i analityczne), cel punktu odzyskiwania, cel czasu odzyskiwania, spójności danych i wymagań dotyczących dostępności.

![Wdrożenie w wielu regionach](./media/cassandra-nodejs/cassandra-linux2.png)

Rysunek 2. Wdrożenie bazy danych Cassandra w wielu regionach

### <a name="network-integration"></a>Integracja sieci
Ustawia maszyny wirtualne wdrożone w sieciach prywatnych, znajduje się w dwóch regionach komunikuje się ze sobą przy użyciu tunelu VPN. Tunel VPN łączy się z dwóch bram oprogramowania aprowizowane w procesie wdrażania sieci. Oba regiony mają podobną architekturę sieci pod kątem podsieci "web" i "dane"; Sieci platformy Azure umożliwia tworzenie podsieci tyle zgodnie z potrzebami i stosowanie list ACL, odpowiednio do potrzeb zabezpieczeń sieci. Podczas projektowania topologii klastra, między opóźnienie komunikacji centrum danych i ekonomicznego wpływu na potrzeby ruchu sieciowego wziąć pod uwagę.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Wyjaśnienie pojęcia spójności danych dla wdrożenia centrum danych z wielu
Rozproszone wdrożeń muszą mieć świadomość wpływu na Topologia klastra, przepływności i wysokiej dostępności. RF i poziomu spójności należy wybrać w taki sposób, że kworum nie są zależne od dostępności wszystkich centrów danych.
W przypadku systemu wymaga wysoką spójność LOCAL_QUORUM poziomu spójności (w przypadku operacji odczytu i zapisu) sprawia, że się upewnić, że lokalne odczyty i zapisy są spełnione z węzłów w lokalnych podczas, gdy dane są replikowane asynchronicznie w centrach danych zdalnych.  Tabela 2 zawiera podsumowanie szczegóły konfiguracji klaster obejmujący wiele regionów opisanych w dalszej części zapisu w górę.

**Konfiguracja klastra Cassandra w dwóch regionach**

| Parametr klastra | Wartość | Uwagi |
| --- | --- | --- |
| Liczba węzłów (N) |8 + 8 |Całkowita liczba węzłów w klastrze |
| Współczynnik replikacji (RF) |3 |Liczba replik w danym wierszu |
| Poziom spójności (zapis) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] wynik formuły jest zaokrąglona w dół |2 węzły są zapisywane do pierwszego centrum danych synchronicznie; dodatkowe węzły 2 służące do kworum jest zapisywany asynchronicznie 2nd centrum danych. |
| Poziom spójności (odczyt) |LOCAL_QUORUM ((RF/2) + 1) = 2, wynik formuły jest zaokrąglona w dół |Żądania odczytu są spełnione przy użyciu tylko jednego regionu. 2 węzły są odczytywane przed odpowiedź jest wysyłana do klienta. |
| Strategia replikacji |Zobacz NetworkTopologyStrategy [replikacji danych](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) w dokumentacji oprogramowania Cassandra, aby uzyskać więcej informacji |Zrozumienie topologii wdrożenia, a następnie umieszcza replik w węzłach, tak, aby wszystkie repliki nie znajdą się w tej samej stojak |
| Snitch |Zobacz GossipingPropertyFileSnitch [Snitches](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) w dokumentacji oprogramowania Cassandra, aby uzyskać więcej informacji |NetworkTopologyStrategy korzysta z koncepcji snitch zrozumienie topologii. GossipingPropertyFileSnitch daje lepszą kontrolę w mapowaniu każdego węzła do centrum danych i stojaka. Klaster używa następnie plotki Propagacja te informacje. Jest to znacznie prostsze w ustawieniu dynamicznego adresu IP w względem PropertyFileSnitch |

## <a name="the-software-configuration"></a>KONFIGURACJA OPROGRAMOWANIA
Następujące wersje oprogramowania są używane podczas wdrażania:

<table>
<tr><th>Oprogramowanie</th><th>Element źródłowy</th><th>Wersja</th></tr>
<tr><td>JRE    </td><td>[JRE 8](https://aka.ms/azure-jdks) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Bazy danych Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Aby uprościć wdrożenie, Pobierz wymagane oprogramowanie na pulpicie. Następnie przekaż go do obrazu systemu Ubuntu szablonu do utworzenia jako wstępnym i niezbędnym warunkiem do wdrożenia klastra.

Pobierz wymienionego powyżej oprogramowania w katalogu pobierania dobrze znanych (na przykład %TEMP%/downloads na Windows lub ~/Downloads na większości dystrybucje systemu Linux lub Mac) na komputerze lokalnym.

### <a name="create-ubuntu-vm"></a>TWORZENIE MASZYNY WIRTUALNEJ Z SYSTEMEM UBUNTU
W tym kroku procesu tworzenia obrazu systemu Ubuntu przy użyciu wstępnie wymaganego oprogramowania tak, aby obraz mogą być ponownie używane do inicjowania obsługi kilka węzłów bazy danych Cassandra.  

#### <a name="step-1-generate-ssh-key-pair"></a>Krok 1: Generowania pary kluczy SSH
Platforma Azure wymaga X509 kluczem publicznym znajdującym się PEM lub DER zakodowane w czasie inicjowania obsługi administracyjnej. Wygeneruj parę kluczy publiczny/prywatny, korzystając z instrukcji znajdujących się w sposób korzystanie z protokołu SSH z systemem Linux na platformie Azure. Jeśli planujesz użyć putty.exe jako klienta SSH w Windows lub Linux, musisz przekonwertować zakodowany w formacie PEM prywatnego klucza RSA format PPK przy użyciu puttygen.exe. Odpowiednie instrukcje znajdują się w powyższej strony sieci web.

#### <a name="step-2-create-ubuntu-template-vm"></a>Krok 2: Utwórz maszynę Wirtualną z szablonem Ubuntu
Aby utworzyć szablon maszyny Wirtualnej, zaloguj się do witryny Azure portal i wykonać poniższą procedurę: kliknij pozycję Nowy, OBLICZENIOWYCH, maszyna wirtualna, GALERII FROM, UBUNTU, Ubuntu Server 14.04 LTS, a następnie kliknij strzałkę w prawo. Samouczek, który opisuje sposób tworzenia maszyny Wirtualnej z systemem Linux, zobacz Tworzenie maszyny wirtualnej Linux uruchomiony.

Na ekranie "konfiguracja maszyny wirtualnej" #1, wprowadź następujące informacje:

<table>
<tr><th>NAZWA POLA              </td><td>       WARTOŚĆ POLA               </td><td>         REMARKS                </td><tr>
<tr><td>DATA WYDANIA WERSJI    </td><td> Wybierz datę z listy rozwijanej</td><td></td><tr>
<tr><td>NAZWA MASZYNY WIRTUALNEJ    </td><td> szablon zabezpieczeń dostępu kodu                   </td><td> Jest to nazwa hosta maszyny wirtualnej </td><tr>
<tr><td>TIER                     </td><td> STANDARDOWA                           </td><td> Pozostaw wartość domyślną              </td><tr>
<tr><td>ROZMIAR                     </td><td> A1                              </td><td>Wybierz maszynę Wirtualną, w zależności od potrzeb operacji We/Wy; w tym celu pozostaw wartość domyślną </td><tr>
<tr><td> NOWĄ NAZWĘ UŻYTKOWNIKA             </td><td> localadmin                       </td><td> "admin" jest nazwą użytkownika zarezerwowane w xx 12 Ubuntu i po</td><tr>
<tr><td> UWIERZYTELNIANIE         </td><td> Kliknij pole wyboru                 </td><td>Sprawdź, czy chcesz zabezpieczyć za pomocą klucza SSH </td><tr>
<tr><td> CERTYFIKAT             </td><td> Nazwa pliku certyfikatu klucza publicznego </td><td> Użyj wcześniej wygenerowany klucz publiczny</td><tr>
<tr><td> Nowe hasło    </td><td> Silne hasło </td><td> </td><tr>
<tr><td> Potwierdź hasło    </td><td> Silne hasło </td><td></td><tr>
</table>

Na ekranie "konfiguracja maszyny wirtualnej" #2, wprowadź następujące informacje:

<table>
<tr><th>NAZWA POLA             </th><th> WARTOŚĆ POLA                       </th><th> REMARKS                                 </th></tr>
<tr><td> USŁUGA W CHMURZE    </td><td> Utwórz nową usługę w chmurze    </td><td>Usługa w chmurze jest kontener zasoby obliczeniowe, takie jak maszyny wirtualne</td></tr>
<tr><td> NAZWA DNS USŁUGI W CHMURZE    </td><td>ubuntu-template.cloudapp.net    </td><td>Nadaj nazwę modułu równoważenia obciążenia niezależny od maszyny</td></tr>
<tr><td> REGION/GRUPA KOLIGACJI/SIECI WIRTUALNEJ </td><td>    Zachodnie stany USA    </td><td> Wybierz region, z którego aplikacje sieci web dostęp do klastra oprogramowania Cassandra</td></tr>
<tr><td>KONTO MAGAZYNU </td><td>    Używanie wartości domyślnej    </td><td>Użyj domyślnego konta magazynu lub wstępnie utworzone konto magazynu w danym regionie</td></tr>
<tr><td>ZESTAW DOSTĘPNOŚCI </td><td>    Brak </td><td>    Pozostaw to pole puste</td></tr>
<tr><td>PUNKTY KOŃCOWE    </td><td>Używanie wartości domyślnej </td><td>    Użyj domyślnej konfiguracji SSH </td></tr>
</table>

Kliknij strzałkę w prawo, pozostaw wartości domyślne na ekranie #3. Kliknij przycisk "Sprawdź", aby ukończyć proces inicjowania obsługi administracyjnej maszyny Wirtualnej. Po kilku minutach maszyny Wirtualnej przy użyciu nazwy "ubuntu-template" powinien być w stanie "uruchomiona".

### <a name="install-the-necessary-software"></a>ZAINSTALUJ WYMAGANE OPROGRAMOWANIE
#### <a name="step-1-upload-tarballs"></a>Krok 1: Przekaż tarballs
Przy użyciu protokołu scp lub narzędzia pscp, skopiuj pobrane wcześniej oprogramowanie do katalogu ~/downloads przy użyciu następującego formatu polecenia:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>narzędzia pscp server-środowiska jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Powtórz powyższe polecenie dla środowiska JRE również jak w przypadku bazy danych Cassandra usługi bits.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>Krok 2: Przygotowanie strukturę katalogów i Wyodrębnij archiwum
Zaloguj się do maszyny Wirtualnej i Utwórz strukturę katalogów i Wyodrębnij oprogramowania jako superużytkownika, za pomocą poniższego skryptu powłoki bash:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Po wklejeniu tego skryptu do okna vim, upewnij się, że usunięcie znaku powrotu karetki ("\r") przy użyciu następującego polecenia:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Krok 3: Edytuj profil, itp.
Dołącz następujący na końcu:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Krok 4: Instalacja JNA dla systemów produkcyjnych.
Użyj poniższej sekwencji poleceń: poniższe polecenie instaluje jna 3.2.7.jar i jna platformy 3.2.7.jar /usr/share.java katalogu "sudo" apt-Get zainstalować libjna java

Tworzenie łączy symbolicznych w katalogu CASS_HOME/lib $, tak aby skrypt uruchamiania oprogramowania Cassandra można znaleźć w tych plikach JAR:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Krok 5: Konfiguracja cassandra.yaml
Edytuj cassandra.yaml na każdej maszynie Wirtualnej, aby odpowiadały konfiguracji wymagane przez wszystkie maszyny wirtualne [dostosowanie tej konfiguracji podczas aprowizowania rzeczywiste]:

<table>
<tr><th>Nazwa pola   </th><th> Wartość  </th><th>    Uwagi </th></tr>
<tr><td>cluster_name </td><td>    "CustomerService"    </td><td> Użyj nazwy, która odzwierciedla wdrożenia</td></tr>
<tr><td>listen_address    </td><td>[puste je]    </td><td> Usuń "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[puste je]    </td><td> Usuń "localhost" </td></tr>
<tr><td>wartości początkowe    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Lista wszystkich adresów IP, które zostały oznaczone jako wartości początkowe.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Jest on używany przez NetworkTopologyStrateg wnioskowania centrum danych i stojak maszyny Wirtualnej</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Krok 6: Przechwytywanie obrazu maszyny Wirtualnej
Zaloguj się do maszyny wirtualnej przy użyciu nazwy hosta (hk CA template.cloudapp.net) i klucz prywatny SSH utworzone wcześniej. Zobacz jak używanie protokołu SSH z systemem Linux na platformie Azure, aby uzyskać szczegółowe informacje na temat zalogować się przy użyciu polecenia ssh lub putty.exe.

Wykonaj następującą sekwencję czynności, aby przechwycić obraz:

##### <a name="1-deprovision"></a>1. Anulowanie aprowizacji
Użyj polecenia "waagent" sudo "— anulowania aprowizacji + user" Aby usunąć określone informacje o wystąpieniu maszyny wirtualnej. Zobacz [jak przechwycić maszynę wirtualną z systemem Linux](capture-image-classic.md) do użycia jako szablon więcej szczegółów na temat procesu przechwytywania obrazu.

##### <a name="2-shut-down-the-vm"></a>2: Zamknij maszynę Wirtualną
Upewnij się, że maszyna wirtualna zostanie wyróżniona, a następnie kliknij łącze zamykania na dolnym pasku poleceń.

##### <a name="3-capture-the-image"></a>3: przechwycenie obrazu
Upewnij się, że maszyna wirtualna zostanie wyróżniona, a następnie kliknij łącze PRZECHWYTYWANIA na dolnym pasku poleceń. W następnej ekranu, zapewniają nazwa obrazu (na przykład hk-cas-2-08-ub-14-04-2014071), odpowiedni opis obrazu i kliknij przycisk "Sprawdź" Oznacz na zakończenie procesu PRZECHWYTYWANIA.

Ten proces trwa kilka sekund, a obraz powinien być dostępny w sekcji Moje obrazy z galerii obrazów. Źródłowa maszyna wirtualna jest automatycznie usuwany po pomyślnym przechwyceniem obrazu. 

## <a name="single-region-deployment-process"></a>Proces wdrażania w jednym regionie
**Krok 1: Utwórz sieć wirtualną** Zaloguj się do witryny Azure portal i tworzenie sieci wirtualnej (klasycznej) z atrybutami pokazano w poniższej tabeli. Zobacz [sieć wirtualna (klasyczna) przy użyciu witryny Azure portal utworzyć](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) szczegółowy opis kroków procesu.      

<table>
<tr><th>Nazwa atrybutu maszyny Wirtualnej</th><th>Wartość</th><th>Uwagi</th></tr>
<tr><td>Name (Nazwa)</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>Region</td><td>Zachodnie stany USA</td><td></td></tr>
<tr><td>Serwery DNS</td><td>Brak</td><td>Zignoruj ten komunikat, ponieważ nie używamy serwera DNS</td></tr>
<tr><td>Przestrzeń adresowa</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Począwszy od adresu IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Dodaj następujące podsieci:

<table>
<tr><th>Name (Nazwa)</th><th>Począwszy od adresu IP</th><th>CIDR</th><th>Uwagi</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Podsieć dla kolektywu serwerów sieci web</td></tr>
<tr><td>dane</td><td>10.1.2.0</td><td>/24 (251)</td><td>Podsieć dla węzłów bazy danych</td></tr>
</table>

Dane i podsieci w sieci Web mogą być chronione za pomocą sieciowych grup zabezpieczeń, zakres wykracza poza zakres, w tym artykule.  

**Krok 2: Aprowizacja maszyn wirtualnych** przy użyciu utworzonego wcześniej obrazu, utworzysz następujące maszyny wirtualne na serwerze usługi chmury "hk-c-svc Zachód" i wiązania ich z tych podsieci, jak pokazano poniżej:

<table>
<tr><th>Nazwa komputera    </th><th>Podsieć    </th><th>Adres IP    </th><th>Zestaw dostępności</th><th>DC/stojak</th><th>Inicjator?</th></tr>
<tr><td>hk-c1-west-us    </td><td>dane    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa1 </td><td>Yes</td></tr>
<tr><td>hk-c2-west-us    </td><td>dane    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa1    </td><td>Nie </td></tr>
<tr><td>hk-c3-west-us    </td><td>dane    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa2    </td><td>Yes</td></tr>
<tr><td>hk-c4-west-us    </td><td>dane    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa2    </td><td>Nie </td></tr>
<tr><td>hk-c5-west-us    </td><td>dane    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = szafa3    </td><td>Yes</td></tr>
<tr><td>hk-c6-west-us    </td><td>dane    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = szafa3    </td><td>Nie </td></tr>
<tr><td>hk-c7-west-us    </td><td>dane    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = rack4    </td><td>Yes</td></tr>
<tr><td>hk-c8-west-us    </td><td>dane    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = rack4    </td><td>Nie </td></tr>
<tr><td>HK-w1 — zachodnio USA    </td><td>web    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>ND</td></tr>
<tr><td>hk-w2-west-us    </td><td>web    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>ND</td></tr>
</table>

Tworzenie powyższej listy maszyn wirtualnych wymaga następującego procesu:

1. Tworzenie usługi w chmurze pusty w danym regionie
2. Tworzenie maszyny Wirtualnej na podstawie wcześniej przechwycony obraz i dołączyć go do sieci wirtualnej utworzonej wcześniej; Powtórz tę czynność dla wszystkich maszyn wirtualnych
3. Dodawanie wewnętrznego modułu równoważenia obciążenia do usługi w chmurze i dołączyć je do podsieci "dane"
4. Dla każdej maszyny Wirtualnej utworzoną wcześniej Dodaj punkt końcowy o zrównoważonym obciążeniu dla thrift ruchu w ramach zestawu z równoważeniem obciążenia podłączone do utworzonej wcześniej wewnętrznego modułu równoważenia obciążenia

Proces powyżej mogą być wykonywane przy użyciu portalu Azure; Użyj maszyny Windows (Użyj maszyny wirtualnej na platformie Azure, jeśli nie masz dostępu do komputera Windows), umożliwia poniższy skrypt programu PowerShell automatycznie aprowizować wszystkich 8 maszyn wirtualnych.

**Lista 1: Skrypt programu PowerShell do obsługi administracyjnej maszyn wirtualnych**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. create a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Krok 3: Konfiguracja bazy danych Cassandra na każdej maszynie Wirtualnej**

Zaloguj się do maszyny Wirtualnej i wykonaj następujące czynności:

* Edytuj $CASS_HOME/conf/cassandra-rackdc.properties do określania właściwości Centrum i stojaka danych:
  
       dc =EASTUS, rack =rack1
* Edytuj cassandra.yaml, aby skonfigurować węzły obiektów początkowych, tak jak pokazano poniżej:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Krok 4: Uruchom maszyn wirtualnych i przetestowanie klastra**

Zaloguj się do jednego z węzłów, (na przykład hk — c1 — zachodnio us) i uruchom następujące polecenie, aby wyświetlić stan klastra:

       nodetool –h 10.1.2.4 –p 7199 status

Powinien zostać wyświetlony ekran podobny do przedstawionego poniżej 8 węzłów klastra:

<table>
<tr><th>Stan</th><th>Adres    </th><th>Ładowanie    </th><th>Tokeny    </th><th>Jest właścicielem </th><th>Identyfikator hosta:    </th><th>Stojak</th></tr>
<tr><th>NZ    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa1</td></tr>
<tr><th>NZ    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa1</td></tr>
<tr><th>NZ    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa2</td></tr>
<tr><th>NZ    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa2</td></tr>
<tr><th>NZ    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa3</td></tr>
<tr><th>NZ    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>szafa3</td></tr>
<tr><th>NZ    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>rack4</td></tr>
<tr><th>NZ    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięty)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Przetestowanie klastra pojedynczy Region
Aby przetestować klastra, wykonaj następujące kroki:

1. Za pomocą polecenia cmdlet Get-azureinternalloadbalancer Zapisz polecenia programu Powershell, Uzyskaj adres IP wewnętrznego modułu równoważenia obciążenia (na przykład 10.1.2.101). Poniżej przedstawiono składnię polecenia: Get-AzureLoadbalancer — ServiceName "hk-c-svc zachód nam" [Wyświetla szczegóły wewnętrznego modułu równoważenia obciążenia wraz z jego adres IP]
2. Zaloguj się do maszyny Wirtualnej (na przykład hk-w1 — zachodnio us) kolektywu serwerów sieci web przy użyciu programu Putty lub protokołu ssh
3. Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Aby sprawdzić, czy klaster działa, należy użyć następujących poleceń języka CQL:
   
     TWORZENIE przestrzeni KLUCZY customers_ks z REPLIKACJĄ = {"class": "SimpleStrategy", "replication_factor": 3};   UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wyrażenie INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "Kowalski");
   
     Wybierz * z klientów.

Powinien zostać wyświetlony podobny następujące wyniki:

<table>
  <tr><th> customer_id </th><th> Imię </th><th> nazwisko </th></tr>
  <tr><td> 1 </td><td> Jan </td><td> Nowak </td></tr>
  <tr><td> 2 </td><td> Magdalena </td><td> Nowak </td></tr>
</table>

Przestrzeń kluczy utworzony w kroku 4 używa SimpleStrategy replication_factor 3. SimpleStrategy jest zalecane w przypadku danych jednego wdrożenia Centrum natomiast NetworkTopologyStrategy danych wielu centrów. Replication_factor 3 zapewnia tolerancję na uszkodzenia węzła.

## <a id="tworegion"> </a>Proces wdrażania w wielu regionach
Wykorzystaj ukończenie wdrażania jednego regionu i powtórz ten sam proces instalacji drugiego regionu. Główną różnicą między wdrażanie jednego lub wielu regionach jest konfiguracja tunelu sieci VPN do komunikacji między regionami; Uruchom za pomocą instalacji sieciowej, aprowizowanie maszyn wirtualnych i Konfigurowanie bazy danych Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Krok 1: Utwórz sieć wirtualną w regionie 2
Zaloguj się do witryny Azure portal i Utwórz sieć wirtualną z programem atrybutów w tabeli. Zobacz [Konfigurowanie sieci wirtualnej Cloud-Only w witrynie Azure portal](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) szczegółowy opis kroków procesu.      

<table>
<tr><th>Nazwa atrybutu    </th><th>Wartość    </th><th>Uwagi</th></tr>
<tr><td>Name (Nazwa)    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>Region    </td><td>Wschodnie stany USA</td><td></td></tr>
<tr><td>Serwery DNS        </td><td></td><td>Zignoruj ten komunikat, ponieważ nie używamy serwera DNS</td></tr>
<tr><td>Konfigurowanie sieci VPN typu punkt lokacja</td><td></td><td>        Zignoruj ten komunikat</td></tr>
<tr><td>Konfiguracja tunelu sieci VPN łączącego lokacje</td><td></td><td>        Zignoruj ten komunikat</td></tr>
<tr><td>Przestrzeń adresowa    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Począwszy od adresu IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Dodaj następujące podsieci:

<table>
<tr><th>Name (Nazwa)    </th><th>Począwszy od adresu IP    </th><th>CIDR    </th><th>Uwagi</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Podsieć dla kolektywu serwerów sieci web</td></tr>
<tr><td>dane    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Podsieć dla węzłów bazy danych</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Krok 2: Tworzenie sieci lokalne
Sieć lokalna w sieci wirtualnej platformy Azure jest przestrzeni adresowej serwera proxy, który jest mapowany do lokacji zdalnej, w tym chmury prywatnej lub innego regionu platformy Azure. Ta przestrzeń adresowa serwera proxy jest powiązany z bramy zdalnego routingu sieci do prawego sieci miejsc docelowych. Zobacz [Konfigurowanie sieci wirtualnej połączenie między sieciami wirtualnymi](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) uzyskania instrukcji na temat nawiązywania połączenia z SIECIAMI wirtualnymi.

Utwórz dwie sieci lokalne na następujące informacje:

| Nazwa sieci | Adres bramy sieci VPN | Przestrzeń adresowa | Uwagi |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Podczas tworzenia sieci lokalnej należy nadać symbol zastępczy adres bramy. Adres bramy rzeczywistych jest wypełniana po utworzeniu bramy. Upewnij się, że przestrzeń adresowa dokładnie pokrywa się z odpowiednim zdalna sieć wirtualna; w tym przypadku sieć wirtualna jest tworzona w regionie wschodnie stany USA. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Podczas tworzenia sieci lokalnej należy nadać symbol zastępczy adres bramy. Adres bramy rzeczywistych jest wypełniana po utworzeniu bramy. Upewnij się, że przestrzeń adresowa dokładnie pokrywa się z odpowiednim zdalna sieć wirtualna; w tym przypadku sieć wirtualna jest tworzona w regionie zachodnie stany USA. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Krok 3: Mapy "Local" sieci do odpowiednich sieci wirtualnych
W witrynie Azure portal wybierz każdej sieci wirtualnej, kliknij przycisk "Konfiguruj", sprawdź "Połącz z sieci lokalnej" i wybierz sieci lokalnych na następujące informacje:

| Virtual Network | Sieć lokalna |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Krok 4: Tworzenie bramy sieci VNET1 i VNET2
Na pulpicie nawigacyjnym sieci wirtualnej kliknij przycisk Utwórz BRAMĘ, aby wyzwolić proces tworzenia bramy sieci VPN. Po kilku minutach adres bramy rzeczywiste powinien być wyświetlany pulpit nawigacyjny w każdej sieci wirtualnej.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Krok 5: Aktualizacja sieci "Local" z adresami odpowiednich "brama"
Edytowanie obu lokalnej sieci Zamień symbol zastępczy adres IP bramy rzeczywistego adresu IP po prostu elastycznie bram. Użyj następującego mapowania:

<table>
<tr><th>Sieć lokalna    </th><th>Brama sieci wirtualnej</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Brama hk — sieć wirtualna — zachodnio USA</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Hk — sieć wirtualna wschód nam bramy</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Krok 6. Zaktualizuj klucza wstępnego
Użyj następującego skryptu programu Powershell, aby zaktualizować klucza protokołu IPSec dla każdej usługi VPN gateway [użycia klucza sake obu bram]: Set AzureVNetGatewayKey - VNetName hk — sieć wirtualna wschód nam - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Zestaw AzureVNetGatewayKey - VNetName hk — sieć wirtualna — zachodnio USA - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Krok 7: Ustanowienia połączenia sieć wirtualna-sieć wirtualna
W witrynie Azure portal należy użyć menu "Pulpit NAWIGACYJNY" sieci wirtualnej nawiązanie połączenia brama brama. Użyj elementów menu "POŁĄCZ" w dolnego paska narzędzi. Po kilku minutach pulpit nawigacyjny powinien być wyświetlany szczegóły połączenia graficznie.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Krok 8: Tworzenie maszyn wirtualnych w regionie #2
Utwórz obraz systemu Ubuntu, zgodnie z opisem w regionie #1 wdrożenia, wykonując kroki tej samej lub kopii pliku wirtualnego dysku twardego obrazu do konta usługi Azure storage znajduje się w regionie #2 a obrazu. Utwórz następującą listę maszyn wirtualnych do nowej usługi w chmurze, hk-c-svc wschód nam i używać tego obrazu:

| Nazwa komputera | Podsieć | Adres IP | Zestaw dostępności | DC/stojak | Inicjator? |
| --- | --- | --- | --- | --- | --- |
| HK — c1-wschód USA |dane |10.2.2.4 |hk-c-aset-1 |DC = stojak EASTUS = szafa1 |Yes |
| HK — c2-wschód USA |dane |10.2.2.5 |hk-c-aset-1 |DC = stojak EASTUS = szafa1 |Nie |
| HK — c3-wschód USA |dane |10.2.2.6 |hk-c-aset-1 |DC = stojak EASTUS = szafa2 |Yes |
| HK — c5-wschód USA |dane |10.2.2.8 |hk-c-aset-2 |DC = stojak EASTUS = szafa3 |Yes |
| HK — c6-wschód USA |dane |10.2.2.9 |hk-c-aset-2 |DC = stojak EASTUS = szafa3 |Nie |
| HK — c7-wschód USA |dane |10.2.2.10 |hk-c-aset-2 |DC = stojak EASTUS = rack4 |Yes |
| HK — c8-wschód USA |dane |10.2.2.11 |hk-c-aset-2 |DC = stojak EASTUS = rack4 |Nie |
| HK-w1 — wschód USA |web |10.2.1.4 |hk-w-aset-1 |ND |ND |
| HK-w2 — wschód USA |web |10.2.1.5 |hk-w-aset-1 |ND |ND |

Postępuj zgodnie z instrukcjami tego samego jako region #1, ale korzystanie z 10.2.xxx.xxx przestrzeni adresowej.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Krok 9. Konfigurowanie rozwiązania Cassandra na każdej maszynie Wirtualnej
Zaloguj się do maszyny Wirtualnej i wykonaj następujące czynności:

1. Edytuj $CASS_HOME/conf/cassandra-rackdc.properties do określania właściwości Centrum i stojaka dane w formacie: dc = stojak EASTUS = szafa1
2. Edytuj cassandra.yaml, aby skonfigurować węzły obiektów początkowych: wartości początkowe: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>Krok 10. uruchamianie rozwiązania Cassandra
Zaloguj się do każdej maszyny Wirtualnej i uruchom bazy danych Cassandra w tle, uruchamiając następujące polecenie: $CASS_HOME/bin/bazy danych cassandra

## <a name="test-the-multi-region-cluster"></a>Testowanie klaster obejmujący wiele regionów
Już Cassandra została wdrożona do 16 węzłów z 8 węzłów w każdym regionie platformy Azure. Te węzły znajdują się w tym samym klastrze, na podstawie wspólnej nazwy klastra i konfiguracja węzła inicjatora. Poniższa procedura umożliwia przetestowanie klastra:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Krok 1: Uzyskiwanie adresu IP modułu równoważenia obciążenia wewnętrznego dla obu regionów przy użyciu programu PowerShell
* Get-azureinternalloadbalancer Zapisz - ServiceName "hk-c-svc zachód nam"
* Get-azureinternalloadbalancer Zapisz - ServiceName "hk-c-svc wschód USA"  
  
    Zanotuj ich adresy IP (przykład zachodnie - 10.1.2.101, wschód - 10.2.2.101) wyświetlane.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Krok 2: Wykonaj następujące czynności w regionie zachodnie stany po zalogowaniu się do hk-w1 — zachodnio USA
1. Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Wykonaj następujące polecenia języka CQL:
   
     TWORZENIE przestrzeni KLUCZY customers_ks z REPLIKACJĄ = {"class": 'NetworkToplogyStrategy', 'WESTUS': 3, "EASTUS": 3};   UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wyrażenie INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "Kowalski");   Wybierz * z klientów.

Powinien zostać wyświetlony ekran, tak jak poniżej:

| customer_id | Imię | nazwisko |
| --- | --- | --- |
| 1 |Jan |Nowak |
| 2 |Magdalena |Nowak |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Krok 3: Wykonaj następujące czynności w regionie wschodnim, po zalogowaniu się do hk-w1 — wschód USA:
1. Execute $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Wykonaj następujące polecenia języka CQL:
   
     UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wyrażenie INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "Kowalski");   Wybierz * z klientów.

Powinny zostać wyświetlone wyświetlania tych samych, jak pokazano w regionie zachodnie stany:

| customer_id | Imię | nazwisko |
| --- | --- | --- |
| 1 |Jan |Nowak |
| 2 |Magdalena |Nowak |

Wykonaj kilka więcej operacji wstawienia i zobacz te są replikowane zachód-us, które są częścią klastra.

## <a name="test-cassandra-cluster-from-nodejs"></a>Klastra oprogramowania Cassandra testu z poziomu środowiska Node.js
Przy użyciu jednej z maszyn wirtualnych systemu Linux, wcześniej utworzone w warstwy "Internet", wykonaj prosty skrypt środowiska Node.js do odczytywania danych wcześniej wstawionych

**Krok 1: Zainstaluj środowisko Node.js i klienta bazy danych Cassandra**

1. Zainstalować środowisko Node.js i npm
2. Zainstaluj węzeł pakietu "cassandra-client" za pomocą Menedżera npm
3. Uruchom następujący skrypt w wierszu polecenia powłoki, który zawiera ciąg json pobranych danych:
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }
   
        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }
   
        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);
   
           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }
   
        //update also inserts the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>Podsumowanie
Platforma Microsoft Azure to elastyczna platforma, która umożliwia uruchamianie zarówno firmy Microsoft, jak i oprogramowanie typu open source, jak pokazano w tym ćwiczeniu. Klastrów o wysokiej dostępności bazy danych Cassandra można wdrożyć w jednym centrum danych za pośrednictwem rozmieszczenie węzłów klastra w wielu domenach błędów. Można także wdrożyć klastry bazy danych Cassandra w wielu regionach platformy Azure odległymi geograficznie punktami dowód systemów po awarii. Azure i Cassandra razem umożliwia konstrukcja o wysokim stopniu skalowalności, dostępności i usług w chmurze możliwe do odzyskania po awarii potrzebne przez internet współczesnych usług.  

## <a name="references"></a>Dokumentacja
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

