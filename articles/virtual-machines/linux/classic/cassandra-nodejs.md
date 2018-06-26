---
title: Uruchom klaster Cassandra w systemie Linux na platformie Azure w oprogramowaniu Node.js
description: Jak uruchomić Cassandra klastra w systemie Linux w maszynach wirtualnych platformy Azure z poziomu aplikacji Node.js
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
ms.openlocfilehash: 93cd4b6c4264c5905746b85f9fa46ce31ebd9e9f
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937673"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Uruchom klaster Cassandra w systemie Linux na platformie Azure za pomocą języka Node.js

> [!IMPORTANT] 
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Zobacz szablony Menedżera zasobów dla [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) i [Spark klastra i Cassandra na CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Przegląd
Microsoft Azure to platforma chmury Otwórz uruchomioną zarówno firmy Microsoft oraz oprogramowania firmy Microsoft, w tym systemów operacyjnych, serwerów aplikacji, oprogramowanie pośredniczące obsługi komunikatów, a także baz danych SQL i NoSQL z obu modeli źródła handlowych i Otwórz. Tworzenie usług odporne na chmur publicznych, w tym na platformie Azure wymaga staranne planowanie i architektura zamierzonego dla obu serwerów aplikacji jako warstwy dobrze magazynu. Naturalnie Cassandra w magazynie rozproszonym architektura ułatwia tworzenie wysokiej dostępności systemów, które są odporne na uszkodzenia w przypadku awarii klastra. Cassandra jest skali chmury utrzymywana przez Foundation oprogramowania Apache cassandra.apache.org bazy danych NoSQL. Cassandra jest napisany w języku Java. Dlatego działa zarówno na platformach systemu Windows i Linux.

Ten artykuł koncentruje się pokazanie Cassandra wdrożenia na Ubuntu jako klaster pojedynczych i wielu danych Centrum korzystającego z maszyn wirtualnych platformy Azure i sieci wirtualnych. Wdrażanie klastra, w przypadku obciążeń produkcyjnych zoptymalizowanych pod kątem jest poza zakres tego artykułu, ponieważ wymaga ona konfiguracji węzła wielu dysków, projekt topologia pierścienia odpowiednie i danych modelowania do obsługi replikacji potrzebne, spójność danych, przepływności i wysoki wymagania dotyczące dostępności.

Ten artykuł ma podstawowych podejście do wyświetlenia, co jest zaangażowane w tworzenie klastra Cassandra porównywany Docker, Chef lub Puppet, które mogą ułatwić wdrożenie infrastruktury partii.  

## <a name="the-deployment-models"></a>Modele wdrażania
Sieci Microsoft Azure umożliwia wdrożenie izolowanego prywatnej klastrów, dostępu do których może być ograniczone do osiągnięcia zabezpieczenia sieci szczegółowe.  Ponieważ ten artykuł dotyczy przedstawiający wdrażanie Cassandra na poziomie podstawowym, nie skupić się na poziomie spójności i projektu pamięci masowej optymalnej przepływności. Poniżej przedstawiono listę wymagań sieciowych dotyczących hipotetyczny klastra:

* Systemy zewnętrzne nie może uzyskać dostępu Cassandra bazy danych z lub poza Azure
* Cassandra klastra musi być za modułem równoważenia obciążenia dla ruchu thrift
* Wdrażanie węzłów Cassandra na dwie grupy w każdej centrum danych dostępności klastra rozszerzone
* Zablokowanie klastra tak że tylko do farmy serwerów aplikacji ma dostęp do bazy danych bezpośrednio
* Brak publicznego punktów końcowych sieci innych niż SSH
* Każdy węzeł Cassandra musi stałym wewnętrznego adresu IP

Cassandra można wdrożyć jeden region platformy Azure lub wielu regionach, oparte na Rozproszony charakter obciążenia. Model wdrożenia w przypadku służy do obsługi użytkowników końcowych bliżej do określonej lokalizacji geograficznej, za pomocą tej samej infrastrukturze Cassandra. Cassandra dla wbudowanego węzła replikacji ma opieki synchronizacji wielu wzorca zapisuje pochodzących z wielu centrów danych i przedstawia widok spójności danych aplikacji. W przypadku wdrożenia może również pomóc z umożliwiają ograniczenie ryzyka związanego z szerszych awarie usługi Azure. W Cassandra dostosowywalne spójności i topologię replikacji ułatwia spotkania zróżnicowane potrzeby RPO aplikacji.

### <a name="single-region-deployment"></a>Wdrażanie jednego regionu
Załóżmy zacząć wdrożenia jednego regionu i podłączono learnings modelu w przypadku tworzenia. Sieci wirtualne Azure służy do utworzyć izolowane podsieci, dzięki czemu można spełnić wymagania dotyczące zabezpieczeń sieci, które zostały wymienione powyżej.  Proces opisany w tworzeniu wdrożenia jednego regionu używa Ubuntu 14.04 LTS i Cassandra 2.08. Jednak proces można łatwo przyjąć innych wariantów systemu Linux. Poniżej przedstawiono niektóre właściwości systemowych wdrożenia w pojedynczym regionie.  

**Wysoka dostępność:** węzłów Cassandra pokazany na rysunku 1 są wdrażane dwa zestawy dostępności, aby węzły są rozkładane między wiele domen błędów w celu zapewnienia wysokiej dostępności. Maszyny wirtualne adnotowany przy każdym zestawie dostępności jest mapowana na 2 domen błędów. Azure do zarządzania nieplanowane przestoje (na przykład awarii sprzętu lub oprogramowania) są używane pojęcia domeny błędów. Koncepcji uaktualniania domeny (na przykład host lub system operacyjny gościa stosowanie poprawek/uaktualnień, uaktualnień aplikacji) służy do zarządzania zaplanowany czas przestoju. Zobacz [odzyskiwania po awarii i wysoką dostępność aplikacji Azure](http://msdn.microsoft.com/library/dn251004.aspx) roli domenach awarii i uaktualniania w celu osiągnięcia wysokiej dostępności.

![Wdrażanie jednego regionu](./media/cassandra-nodejs/cassandra-linux1.png)

Rysunek 1: Wdrożenie pojedynczy region

Zauważ, że w momencie pisania tego Azure nie zezwalają jawne mapowanie grupy maszyn wirtualnych do domeny określonych błędów; w związku z tym nawet w przypadku modelu wdrażania pokazany na rysunku 1, jest statystycznie prawdopodobne, że wszystkie maszyny wirtualne mogą być mapowane na dwóch domen błędów zamiast cztery.

**Ruch Thrift równoważenia obciążenia:** bibliotek klienckich Thrift wewnątrz serwera sieci web Połącz się z klastrem za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Wymaga to proces dodawania wewnętrznego modułu równoważenia obciążenia z podsiecią "data" (zobacz rysunek 1) w kontekście klastra Cassandra hosting usługi w chmurze. Po zdefiniowaniu jest wewnętrzny moduł równoważenia obciążenia, każdy węzeł wymaga punktu końcowego równoważenia obciążenia ma zostać dodana z adnotacji o nazwie usługi równoważenia obciążenia wcześniej zdefiniowanego zestawu o zrównoważonym obciążeniu. Zobacz [wewnętrzny równoważenia obciążenia Azure ](../../../load-balancer/load-balancer-internal-overview.md)więcej szczegółów.

**Ziarna klastra:** ważne jest, aby wybrać większości węzłów wysokiej dostępności DS, jak nowe węzły komunikować się z węzłami inicjatora umożliwia odnalezienie topologii klastra. Jeden węzeł z każdym zestawie dostępności jest wyznaczony jako węzłów inicjatora, aby uniknąć pojedynczego punktu awarii.

**Współczynnik replikacji i poziomu spójności:** Cassandra w kompilacji w wysokiej dostępności i danych trwałości charakteryzuje się współczynnik replikacji (RF - liczby kopii każdego wiersza przechowywane w klastrze) i poziomu spójności (liczba replik być odczytana/zapisana przed zwróceniem wyniku do obiektu wywołującego). Współczynnik replikacji jest określone podczas tworzenia przestrzeni KLUCZY (podobnie jak relacyjna baza danych), określić poziom spójności podczas CRUD zapytania. W dokumentacji Cassandra [Konfigurowanie spójności](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) szczegóły spójności i formułę obliczenia kworum.

Cassandra obsługuje dwa typy modeli integralności danych — spójność i spójność ostateczna; Współczynnik replikacji i poziomu spójności wspólnie określają, jeśli dane są spójne, jak operacja zapisu jest ukończona lub ostatecznie spójne. Na przykład określenie KWORUM, zgodnie z poziomu spójności zawsze gwarantuje spójności danych dowolnego poziomu spójności poniżej liczba replik do zapisania w celu osiągnięcia KWORUM, (na przykład jeden) powoduje ostatecznie spójności danych.

Powyższy współczynnik replikacji 3 i KWORUM klastra 8 węzłów (2 węzły są odczytywane lub zapisywane spójności) poziomu spójności odczytu/zapisu, przełączniki teoretycznego utraty najwyżej 1 węzła dla grupy replikacji przed powiadamiania uruchamiania aplikacji Wystąpił błąd. Przy założeniu, że wszystkie spacje klucza również mieć równoważony odczytu/zapisu żądania.  Parametry używane dla klastra wdrożone są następujące:

Konfiguracja klastra Cassandra jednego regionu:

| Parametr klastra | Wartość | Uwagi |
| --- | --- | --- |
| Liczba węzłów (N) |8 |Całkowita liczba węzłów w klastrze |
| Współczynnik replikacji (RF) |3 |Liczba replik danego wiersza |
| Poziom zgodności (Zapisz) |QUORUM[(RF/2) +1) = 2 wynik formuły zostać zaokrąglona w dół |Zapisuje najwyżej 2 replik przed wysłaniem odpowiedzi do wywołującego 3 repliki są zapisywane w sposób spójny po pewnym czasie. |
| Poziom zgodności (odczyt) |KWORUM [(RF/2) + 1 = 2] jest zaokrąglana wynik formuły |Odczytuje 2 replik przed wysłaniem odpowiedzi do obiektu wywołującego. |
| Strategii replikacji |Zobacz NetworkTopologyStrategy [replikacji danych](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) w Cassandra dokumentacji, aby uzyskać więcej informacji |Rozumie topologii wdrożenia i umieszcza replik w węzłach, tak, aby wszystkie repliki nie trafiają do tej samej stojak |
| Snitch |Zobacz GossipingPropertyFileSnitch [przełączniki](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) w Cassandra dokumentacji, aby uzyskać więcej informacji |NetworkTopologyStrategy używa pojęcie snitch zrozumienie topologii. GossipingPropertyFileSnitch zapewnia lepszą kontrolę w mapowaniu każdego węzła do centrum danych i stojaku. Klaster używa następnie plotki propagację te informacje. To jest znacznie prostsza w dynamicznej ustawienie adresu IP względem PropertyFileSnitch |

**Azure zagadnienia dotyczące klastra Cassandra:** możliwości maszyny wirtualne Microsoft Azure korzysta z magazynu obiektów Blob platformy Azure dysku trwałości; Usługa Azure Storage zapisuje trzy repliki każdego dysku, aby uzyskać wysoką trwałością. Oznacza to, że każdy wiersz danych wstawione do tabeli Cassandra jest już zapisana w trzech replik. Dlatego spójność danych jest już wykonane obsługę nawet, jeśli współczynnik replikacji (RF) wynosi 1. Główny problem z współczynnik replikacji jest 1 jest aplikacja napotyka przestojów, nawet w przypadku awarii jednego węzła Cassandra. Jednak jeśli węzeł nie działa dla problemów (na przykład sprzętu, błędów oprogramowania systemu) rozpoznawany przez kontroler sieci szkieletowej Azure, inicjuje nowego węzła w tym miejscu przy użyciu tego samego dysków. Inicjowanie obsługi nowego węzła do zastąpi stare hasło może potrwać kilka minut.  Podobnie czynności zaplanowanej konserwacji, jak zmiany systemu operacyjnego gościa, uaktualnia Cassandra, i zmian w aplikacji Kontroler sieci szkieletowej Azure wykonuje wprowadzanie uaktualnień węzłów w klastrze.  Również uaktualnień stopniowych może potrwać kilka węzłów w dół w czasie, a tym samym klastrze może wystąpić krótki przestój w przypadku kilku partycji. Jednak dane nie utracone z powodu wbudowane funkcje nadmiarowości usługi Azure Storage.  

Dla systemów wdrożeniu na platformie Azure, która nie wymaga wysokiej dostępności (na przykład około 99,9 który jest odpowiednikiem 8.76 godzin na rok, zobacz [wysokiej dostępności](http://en.wikipedia.org/wiki/High_availability) szczegółowe informacje) można uruchamiać z RF = 1 i poziom spójności równej ONE.  W przypadku aplikacji o wysokiej dostępności wymagania, RF = 3 i poziomu spójności = KWORUM zaakceptować czas przestoju jednego z węzłów z replik. RF = 1 w przypadku tradycyjnych wdrożeń (na przykład lokalnego) nie można używać z powodu możliwej utracie danych wynikające z problemów, takich jak awarii dysku.   

## <a name="multi-region-deployment"></a>W przypadku wdrożenia
W Cassandra data center rozpoznają replikacji i spójność model opisane powyżej pomaga przy użyciu wdrażania w przypadku bez konieczności stosowania żadnych narzędzi zewnętrznych. Różni się od tradycyjnych relacyjnych baz danych gdzie instalację na potrzeby dublowania bazy danych dla wielu wzorców operacji zapisu może być skomplikowane. Cassandra w konfiguracji w przypadku może ułatwić scenariusze użycia, w tym w scenariuszach:

**Wdrożenia na podstawie bliskości:** aplikacje wielodostępne, wyczyść mapowania użytkowników dzierżawy-do-region, można korzystali przez klaster w przypadku małych opóźnień. Na przykład learning systemy zarządzania dla instytucji edukacyjnych można wdrożyć klaster rozproszonych w regionach wschodnie stany USA i zachodnie stany USA do obsługi odpowiednich szkoły wyższe dla transakcyjnego, a także analizy. Dane mogą być lokalnie spójne w czasie odczyty i zapisy i może być ostatecznie spójne w obu regionach. Istnieją inne przykłady takich jak dystrybucji nośnika, handlu elektronicznego i niczego i wszystko, co służy geograficznie skoncentrowanego użytkownika podstawowego jest przypadek użycia dobrej dla tego modelu wdrażania.

**Wysoka dostępność:** nadmiarowość jest kluczowym czynnikiem w celu osiągnięcia wysokiej dostępności sprzętu i oprogramowania; więcej szczegółów, zobacz Tworzenie niezawodnej systemy chmur w systemie Microsoft Azure. W systemie Microsoft Azure tylko niezawodny sposób realizacji nadmiarowość wartość true, jest wdrażanie klastra w przypadku. Aplikacje można wdrażać w trybie aktywny aktywny lub aktywny / pasywny, a jeśli jeden z regionów działa, usługi Azure Traffic Manager można przekierowywania ruchu sieciowego na aktywnym regionem.  Przy użyciu wdrażania jednego regionu, w przypadku dostępności 99,9, region dwa wdrożenia może osiągnąć dostępności 99.9999 obliczone przez formułę: (1-(1-0.999) * (1-0.999)) * 100); zobacz dokument powyżej, aby uzyskać szczegółowe informacje.

**Odzyskiwanie po awarii:** Cassandra w przypadku klastra, jeśli przeznaczony prawidłowo, może wytrzymać przestojami centrum danych w wyniku katastrofy. Jeśli działa jeden region, można uruchomić aplikacji wdrożone do innych regionów obsługująca użytkowników końcowych. Podobnie jak wszelkie innych firm ciągłości implementacji aplikacja musi być odporny na błędy na utratę danych, na podstawie danych w potoku asynchroniczne. Jednak Cassandra sprawia, że odzyskiwania znacznie usprawnić niż czas trwania procesów odzyskiwania tradycyjne bazy danych. Na rysunku 2 przedstawiono typowe wdrożenie w przypadku modelu o ośmiu węzłów w każdym regionie. Zarówno regiony są dublowane obrazy wzajemnie dla tego samego symetrycznego; projekty rzeczywistych zależy od tego, typu obciążenia (na przykład transakcyjnej lub analitycznych), RPO, RTO, spójności danych oraz wymagań dotyczących dostępności.

![Obsługa wielu region wdrożenia](./media/cassandra-nodejs/cassandra-linux2.png)

Rysunek 2: W przypadku Cassandra wdrożenia

### <a name="network-integration"></a>Integracja sieci
Ustawia maszyn wirtualnych wdrożonych na sieciach prywatnych, znajduje się na dwóch regionach komunikuje się ze sobą przy użyciu tunelu VPN. Tunel VPN łączy dwie bramy oprogramowanie udostępniane w trakcie procesu wdrażania sieci. Zarówno regiony mają podobną architekturę sieci pod względem podsieci "web" i "dane"; Sieć platformy Azure umożliwia tworzenie tyle podsieci zgodnie z potrzebami i stosować listy ACL, odpowiednio do potrzeb zabezpieczeń sieci. Podczas projektowania topologii klastra, między opóźnienie komunikacji centrum danych i ekonomiczne wpływu na potrzeby ruchu sieciowego wziąć pod uwagę.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Spójność danych wdrożenia Centrum usługi danych
Rozproszone wdrożeń muszą być świadome wpływu Topologia klastra na przepływność i wysokiej dostępności. Należy wybrać w taki sposób, że kworum nie zależy od dostępności wszystkich centrów danych, należy RF i poziomu spójności.
W przypadku systemu wymagające wysokiej spójności LOCAL_QUORUM spójności poziomu (odczyty i zapisy) upewnia się, że lokalnego odczyty i zapisy są spełnione z lokalnych węzłów podczas, gdy dane są replikowane asynchronicznie w centrach danych zdalnych.  Tabela 2 zawiera podsumowanie szczegółów konfiguracji klastra w przypadku opisane w dalszej części zapisu w górę.

**Konfiguracja klastra Cassandra dwa regionu**

| Parametr klastra | Wartość | Uwagi |
| --- | --- | --- |
| Liczba węzłów (N) |8 + 8 |Całkowita liczba węzłów w klastrze |
| Współczynnik replikacji (RF) |3 |Liczba replik danego wiersza |
| Poziom zgodności (Zapisz) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] wynik formuły zostać zaokrąglona w dół |2 węzły są zapisywane w centrum danych pierwszego synchronicznie; dodatkowe węzły 2 potrzebne do kworum jest zapisywany asynchronicznie 2 centrum danych. |
| Poziom zgodności (odczyt) |LOCAL_QUORUM ((RF/2) + 1) = 2, wynik formuły zostać zaokrąglona w dół |Żądań odczytu są spełnione przy tylko jeden region; 2 węzły są odczytywane przed wysłaniem odpowiedzi do klienta. |
| Strategii replikacji |Zobacz NetworkTopologyStrategy [replikacji danych](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) w Cassandra dokumentacji, aby uzyskać więcej informacji |Rozumie topologii wdrożenia i umieszcza replik w węzłach, tak, aby wszystkie repliki nie trafiają do tej samej stojak |
| Snitch |Zobacz GossipingPropertyFileSnitch [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) w Cassandra dokumentacji, aby uzyskać więcej informacji |NetworkTopologyStrategy używa pojęcie snitch zrozumienie topologii. GossipingPropertyFileSnitch zapewnia lepszą kontrolę w mapowaniu każdego węzła do centrum danych i stojaku. Klaster używa następnie plotki propagację te informacje. To jest znacznie prostsza w dynamicznej ustawienie adresu IP względem PropertyFileSnitch |

## <a name="the-software-configuration"></a>KONFIGURACJA OPROGRAMOWANIA
Następujące wersje oprogramowania są używane podczas wdrażania:

<table>
<tr><th>Oprogramowanie</th><th>Element źródłowy</th><th>Wersja</th></tr>
<tr><td>JRE    </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Po pobraniu środowiska JRE, musisz ręcznie zaakceptować licencji programu Oracle. Tak aby uprościć wdrażanie, Pobierz wymaganego oprogramowania na pulpicie. Następnie przekaż go do Ubuntu obrazu szablonu do utworzenia jako macierzystych do wdrożenia klastra.

Pobierz powyższego oprogramowania w katalogu pobierania dobrze znanego (na przykład %TEMP%/downloads w systemie Windows lub ~/Downloads na większości dystrybucje systemu Linux lub Mac.) na komputerze lokalnym.

### <a name="create-ubuntu-vm"></a>TWORZENIE MASZYNY WIRTUALNEJ SYSTEMU UBUNTU
W tym kroku procesu tworzenia obrazu Ubuntu z wstępnie wymagane oprogramowanie, aby obraz mogą być ponownie używane do inicjowania obsługi kilka węzłów Cassandra.  

#### <a name="step-1-generate-ssh-key-pair"></a>Krok 1: Generuje parę kluczy SSH
Platforma Azure wymaga X509 klucza publicznego, który jest PEM lub DER zakodowane w czasie inicjowania obsługi administracyjnej. Generowanie pary kluczy publiczny/prywatny, korzystając z instrukcji znajdujący się w sposób używanie SSH z systemem Linux na platformie Azure. Jeśli planujesz użyć putty.exe jako klienta SSH w systemie Windows lub Linux, należy przekonwertować PEM zakodowane RSA klucz prywatny do formatu PPK, używając puttygen.exe. Odpowiednie instrukcje znajdują się w powyższym strony sieci web.

#### <a name="step-2-create-ubuntu-template-vm"></a>Krok 2: Tworzenie Ubuntu szablonu maszyny Wirtualnej
Aby utworzyć szablon maszyny Wirtualnej, zaloguj się do portalu Azure i użyj następującej procedury: kliknij przycisk Nowy, obliczeń, maszyny WIRTUALNEJ, z GALERII, UBUNTU, Ubuntu Server 14.04 LTS, a następnie kliknij strzałkę w prawo. Samouczek, który opisuje metodę utworzyć Maszynę wirtualną systemu Linux, zobacz Tworzenie maszyny wirtualnej systemem Linux.

Na ekranie "konfiguracja maszyny wirtualnej" #1, wprowadź następujące informacje:

<table>
<tr><th>NAZWA POLA              </td><td>       WARTOŚĆ POLA               </td><td>         REMARKS                </td><tr>
<tr><td>DATA WYDANIA WERSJI    </td><td> Wybierz datę z listy rozwijanej</td><td></td><tr>
<tr><td>NAZWA MASZYNY WIRTUALNEJ    </td><td> Szablon przypadku                   </td><td> Jest to nazwa hosta maszyny wirtualnej </td><tr>
<tr><td>TIER                     </td><td> STANDARDOWA                           </td><td> Pozostaw wartość domyślną              </td><tr>
<tr><td>ROZMIAR                     </td><td> A1                              </td><td>Wybierz maszynę Wirtualną, w zależności od potrzeb operacji We/Wy; w tym celu pozostaw wartość domyślną </td><tr>
<tr><td> NOWĄ NAZWĘ UŻYTKOWNIKA             </td><td> localadmin                       </td><td> "Administrator" jest zastrzeżoną nazwą użytkownika w xx 12 Ubuntu i po</td><tr>
<tr><td> UWIERZYTELNIANIE         </td><td> Kliknij pole wyboru                 </td><td>Sprawdź, czy chcesz zabezpieczyć za pomocą klucza SSH </td><tr>
<tr><td> CERTYFIKAT             </td><td> Nazwa pliku certyfikatu klucza publicznego </td><td> Użyj klucza publicznego, wcześniej wygenerowany</td><tr>
<tr><td> Nowe hasło    </td><td> Silne hasło </td><td> </td><tr>
<tr><td> Potwierdź hasło    </td><td> Silne hasło </td><td></td><tr>
</table>

Na ekranie "konfiguracja maszyny wirtualnej" #2, wprowadź następujące informacje:

<table>
<tr><th>NAZWA POLA             </th><th> WARTOŚĆ POLA                       </th><th> REMARKS                                 </th></tr>
<tr><td> USŁUGI W CHMURZE    </td><td> Utwórz nową usługę w chmurze    </td><td>Usługa w chmurze jest zasoby obliczeniowe kontenera, takich jak maszyny wirtualne</td></tr>
<tr><td> NAZWA DNS USŁUGI W CHMURZE    </td><td>ubuntu-template.cloudapp.net    </td><td>Nadaj nazwę modułu równoważenia obciążenia o niesprecyzowanym maszyny</td></tr>
<tr><td> REGION/GRUPY KOLIGACJI/SIECI WIRTUALNEJ </td><td>    Zachodnie stany USA    </td><td> Wybierz region, z której uzyskują dostęp do klastra Cassandra aplikacji sieci web</td></tr>
<tr><td>KONTO MAGAZYNU </td><td>    Używanie wartości domyślnej    </td><td>Użyj domyślnego konta magazynu lub wstępnie utworzone konto magazynu w określonym regionie</td></tr>
<tr><td>ZESTAW DOSTĘPNOŚCI </td><td>    Brak </td><td>    Pozostaw to pole puste</td></tr>
<tr><td>PUNKTY KOŃCOWE    </td><td>Używanie wartości domyślnej </td><td>    Użyj domyślnej konfiguracji SSH </td></tr>
</table>

Kliknij strzałkę w prawo, pozostaw wartości domyślne na ekranie #3. Kliknij przycisk "Sprawdź", aby ukończyć proces inicjowania obsługi administracyjnej maszyny Wirtualnej. Po kilku minutach maszyny Wirtualnej o nazwie "ubuntu-template" powinna być w stanie "uruchomiona".

### <a name="install-the-necessary-software"></a>ZAINSTALUJ OPROGRAMOWANIE NIEZBĘDNE
#### <a name="step-1-upload-tarballs"></a>Krok 1: Przekazywanie tarballs
Przy użyciu protokołu scp lub pscp, skopiuj pobrane wcześniej oprogramowanie do katalogu ~/downloads w następującym formacie polecenia:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-środowiska jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Powtórz powyższe polecenie dla środowiska JRE również podobnie jak w przypadku Cassandra usługi bits.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>Krok 2: Przygotowanie strukturę katalogów i wyodrębniać archiwum
Zaloguj się do maszyny Wirtualnej i Utwórz strukturę katalogów i Wyodrębnij oprogramowania jako superużytkownika przy użyciu poniższego skryptu bash:

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


Po wklejeniu tego skryptu do okna vim upewnij się usunąć powrotu karetki ("\r") przy użyciu następującego polecenia:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Krok 3: Edytowanie profilu itp.
Dołącz na końcu następujących:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Krok 4: Instalacja JNA dla systemów produkcyjnych.
Użyj poniższej sekwencji poleceń: polecenie instaluje jna 3.2.7.jar i jna platformy 3.2.7.jar /usr/share.java katalogu sudo stanie-Get zainstalować libjna java

Utwórz łącza symbolicznego w katalogu CASS_HOME/lib $ tak, aby Cassandra uruchamiania skryptu można znaleźć te słoików:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Krok 5: Konfigurowanie cassandra.yaml
Edytuj cassandra.yaml na każdej maszynie Wirtualnej w celu uwzględnienia konfiguracji wymagane przez wszystkich maszyn wirtualnych [możesz dostosować tę konfigurację, podczas udostępniania rzeczywistego]:

<table>
<tr><th>Nazwa pola   </th><th> Wartość  </th><th>    Uwagi </th></tr>
<tr><td>cluster_name </td><td>    "CustomerService"    </td><td> Użyj nazwy, która odzwierciedla wdrożenia</td></tr>
<tr><td>listen_address    </td><td>[pozostaw to pole puste]    </td><td> Usuń "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[pozostaw to pole puste]    </td><td> Usuń "localhost" </td></tr>
<tr><td>ziarna    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Lista wszystkich adresów IP, które są oznaczone jako dane.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> To jest używany przez NetworkTopologyStrateg dla wnioskowanie centrum danych i stojaku maszyny Wirtualnej</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Krok 6: Przechwytywanie obrazu maszyny Wirtualnej
Zaloguj się do maszyny wirtualnej przy użyciu nazwy hosta (hk-urzędów certyfikacji template.cloudapp.net) i klucz prywatny SSH wcześniej utworzony. Zobacz temat jak używanie SSH z systemem Linux na platformie Azure, aby uzyskać więcej informacji na temat sposobu Zaloguj się za pomocą polecenia ssh lub putty.exe.

Wykonania następującej akcji w celu przechwycenia obrazu:

##### <a name="1-deprovision"></a>1. Deprovision
Użyj polecenia "sudo agenta waagent — deprovision + użytkownika" Aby usunąć określone informacje o wystąpieniu maszyny wirtualnej. Zobacz dla [Przechwytywanie maszyny wirtualnej systemu Linux](capture-image-classic.md) do użycia jako szablon więcej szczegółów na temat procesu przechwytywania obrazu.

##### <a name="2-shut-down-the-vm"></a>2: zamykania maszyny Wirtualnej
Upewnij się, że maszyna wirtualna zostanie wyróżniona, a następnie kliknij łącze zamknięcia z dolnym pasku poleceń.

##### <a name="3-capture-the-image"></a>3: Przechwytywanie obrazu
Upewnij się, że maszyna wirtualna zostanie wyróżniona, a następnie kliknij łącze PRZECHWYTYWANIA z dolnym pasku poleceń. W następnej ekranu, należy podać nazwę obrazu (na przykład hk-cas-2-08-ub-14-04-2014071), odpowiednie opis obrazu i kliknij znacznik "wyboru" na zakończenie procesu PRZECHWYTYWANIA.

Ten proces trwa kilka sekund i obraz powinien być dostępny w sekcji Moje obrazy Galeria obrazów. Źródłowej maszyny Wirtualnej jest automatycznie usuwana pomyślnie przechwycony obraz. 

## <a name="single-region-deployment-process"></a>Proces wdrażania pojedynczy Region
**Krok 1: Utwórz sieć wirtualną** Zaloguj się do portalu Azure i utworzyć sieć wirtualną (klasyczne) z atrybutami pokazano w poniższej tabeli. Zobacz [Utwórz sieć wirtualną (klasyczne) przy użyciu portalu Azure](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) szczegółowy opis kroków procesu.      

<table>
<tr><th>Nazwa atrybutu maszyny Wirtualnej</th><th>Wartość</th><th>Uwagi</th></tr>
<tr><td>Name (Nazwa)</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>Region</td><td>Zachodnie stany USA</td><td></td></tr>
<tr><td>Serwery DNS</td><td>Brak</td><td>Zignoruj ten komunikat, ponieważ nie używamy serwera DNS</td></tr>
<tr><td>Przestrzeń adresowa</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Uruchamianie adresu IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Dodaj następujące podsieci:

<table>
<tr><th>Name (Nazwa)</th><th>Uruchamianie adresu IP</th><th>CIDR</th><th>Uwagi</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Podsieć dla kolektywu serwerów sieci web</td></tr>
<tr><td>dane</td><td>10.1.2.0</td><td>/24 (251)</td><td>Podsieć dla węzłów bazy danych</td></tr>
</table>

Dane i podsieci w sieci Web mogą być chronione przy użyciu grup zabezpieczeń sieci zakres wykracza poza zakres tego artykułu.  

**Krok 2: Maszyny wirtualne należy** przy użyciu obrazu utworzonego wcześniej, należy utworzyć następujące maszyny wirtualne na serwerze chmury "hk-c-svc Zachód", jak i powiązać je do odpowiednich podsieci, jak pokazano poniżej:

<table>
<tr><th>Nazwa komputera    </th><th>Podsieć    </th><th>Adres IP    </th><th>Zestaw dostępności</th><th>DC/Rack</th><th>Inicjatora?</th></tr>
<tr><td>hk-c1-west-us    </td><td>dane    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa1 </td><td>Yes</td></tr>
<tr><td>hk-c2-west-us    </td><td>dane    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa1    </td><td>Nie </td></tr>
<tr><td>hk-c3-west-us    </td><td>dane    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa2    </td><td>Yes</td></tr>
<tr><td>hk-c4-west-us    </td><td>dane    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>DC = stojak WESTUS = szafa2    </td><td>Nie </td></tr>
<tr><td>hk-c5-west-us    </td><td>dane    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = szafa3    </td><td>Yes</td></tr>
<tr><td>hk-c6-west-us    </td><td>dane    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = szafa3    </td><td>Nie </td></tr>
<tr><td>hk-c7-west-us    </td><td>dane    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = rack4    </td><td>Yes</td></tr>
<tr><td>hk-c8-west-us    </td><td>dane    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>DC = stojak WESTUS = rack4    </td><td>Nie </td></tr>
<tr><td>HK-w1 — zachód us    </td><td>web    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>ND</td></tr>
<tr><td>hk-w2-west-us    </td><td>web    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>ND</td></tr>
</table>

Utworzenie powyższej listy maszyn wirtualnych wymaga następujący proces:

1. Tworzenie usługi w chmurze pusta w określonym regionie
2. Utwórz maszynę Wirtualną z wcześniej przechwycony obraz i dołączenie go do sieci wirtualnej utworzone wcześniej; Powtórz tę czynność dla wszystkich maszyn wirtualnych
3. Dodawanie wewnętrznego modułu równoważenia obciążenia do usługi w chmurze i dołącz je do podsieci "dane"
4. Dla każdej maszyny Wirtualnej utworzone wcześniej Dodaj punkt końcowy równoważenia obciążenia dla ruchu thrift za pomocą zestawu o zrównoważonym obciążeniu podłączone do utworzonej wcześniej wewnętrznego modułu równoważenia obciążenia

Proces powyżej mogą być wykonywane przy użyciu portalu Azure; Użyj maszynę z systemem Windows (Użyj maszyny Wirtualnej na platformie Azure, jeśli nie masz dostępu do komputera z systemem Windows), umożliwia automatycznie udostępnić wszystkich maszyn wirtualnych 8 następujący skrypt programu PowerShell.

**Lista 1: Skrypt programu PowerShell do obsługi maszyn wirtualnych**

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
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Krok 3: Konfigurowanie Cassandra na każdej maszynie Wirtualnej**

Zaloguj się do maszyny Wirtualnej i wykonaj następujące czynności:

* Edytuj $CASS_HOME/conf/cassandra-rackdc.properties do określania właściwości stojaku i centrum danych:
  
       dc =EASTUS, rack =rack1
* Edytuj cassandra.yaml do konfiguracji węzłów inicjatora, jak pokazano poniżej:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Krok 4: Uruchom maszyn wirtualnych i przetestowanie klastra**

Zaloguj się do jednego z węzłów (na przykład hk-c1-zachód us) i uruchom następujące polecenie, aby wyświetlić stan klastra:

       nodetool –h 10.1.2.4 –p 7199 status

Powinien zostać wyświetlony ekran podobny do przedstawionego poniżej 8 węzłów klastra:

<table>
<tr><th>Stan</th><th>Adres    </th><th>Ładowanie    </th><th>Tokeny    </th><th>Właścicielem </th><th>Identyfikator hosta    </th><th>Stojak</th></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa1</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa1</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa2</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa2</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa3</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>szafa3</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>rack4</td></tr>
<tr><th>WYREJESTRUJ    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Identyfikator GUID (usunięte)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Przetestowanie klastra pojedynczy Region
Aby przetestować klastra, wykonaj następujące kroki:

1. Za pomocą polecenia Get-AzureInternalLoadbalancer polecenia programu Powershell, Uzyskaj adres IP wewnętrznego modułu równoważenia obciążenia (na przykład 10.1.2.101). Poniżej przedstawiono składnię polecenia: Get-AzureLoadbalancer — ServiceName "hk — c-svc zachód us" [Wyświetla szczegóły wewnętrznego modułu równoważenia obciążenia oraz adres IP]
2. Zaloguj się do maszyny Wirtualnej (na przykład hk-w1 — zachód us) kolektywu serwerów sieci web przy użyciu programu Putty lub ssh
3. Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Aby sprawdzić, czy klaster działa, należy użyć następujących poleceń CQL:
   
     TWORZENIE przestrzeni KLUCZY customers_ks z REPLIKACJĄ = {"class": "SimpleStrategy", "replication_factor": 3};   UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wstaw do Customers(customer_id, firstname, lastname) wartości 2, 'Magdalena', 'Nowak';
   
     Wybierz * od klientów.

Powinien zostać wyświetlony ekran podobny do następujące wyniki:

<table>
  <tr><th> customer_id </th><th> Imię </th><th> nazwisko </th></tr>
  <tr><td> 1 </td><td> Jan </td><td> Nowak </td></tr>
  <tr><td> 2 </td><td> Magdalena </td><td> Nowak </td></tr>
</table>

Przestrzeni kluczy, utworzony w kroku 4 używa SimpleStrategy z replication_factor 3. SimpleStrategy jest zalecane dla pojedynczego wdrożenia Centrum natomiast NetworkTopologyStrategy danych z wielu centrów. Replication_factor 3 zapewnia tolerancji na wypadek awarii węzła.

## <a id="tworegion"> </a>Proces wdrażania wielu regionu
Wykorzystanie zakończono wdrażanie jednego regionu i powtórz ten sam proces instalacji drugiego regionu. Klucza różnica między wdrażania jednego lub wielu region jest instalację tunelu sieci VPN na potrzeby komunikacji między regionu; Rozpocznij instalację sieciową, obsługi administracyjnej maszyn wirtualnych i skonfigurować Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Krok 1: Utwórz sieć wirtualną w regionie 2
Zaloguj się do portalu Azure i utworzyć sieć wirtualną z programem atrybutów w tabeli. Zobacz [skonfigurować Cloud-Only sieci wirtualnej w portalu Azure](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) szczegółowy opis kroków procesu.      

<table>
<tr><th>Nazwa atrybutu    </th><th>Wartość    </th><th>Uwagi</th></tr>
<tr><td>Name (Nazwa)    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>Region    </td><td>Wschodnie stany USA</td><td></td></tr>
<tr><td>Serwery DNS        </td><td></td><td>Zignoruj ten komunikat, ponieważ nie używamy serwera DNS</td></tr>
<tr><td>Konfigurowanie sieci VPN punkt lokacja</td><td></td><td>        Zignoruj ten komunikat</td></tr>
<tr><td>Konfiguracja tunelu sieci VPN łączącego lokacje</td><td></td><td>        Zignoruj ten komunikat</td></tr>
<tr><td>Przestrzeń adresowa    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Uruchamianie adresu IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Dodaj następujące podsieci:

<table>
<tr><th>Name (Nazwa)    </th><th>Uruchamianie adresu IP    </th><th>CIDR    </th><th>Uwagi</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Podsieć dla kolektywu serwerów sieci web</td></tr>
<tr><td>dane    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Podsieć dla węzłów bazy danych</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Krok 2: Tworzenie sieci lokalne
Sieci lokalnej w sieci wirtualnej platformy Azure jest przestrzeń adresów serwera proxy, która mapuje z lokacją zdalną, w tym chmurę prywatną lub inny region platformy Azure. Ta przestrzeń adresowa serwera proxy jest powiązany z bramy zdalnego routingu sieci do prawej sieci miejsc docelowych. Zobacz [Konfigurowanie sieci wirtualnej do sieci wirtualnej połączenia](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) instrukcje dotyczące nawiązywania połączenia do Wirtualnymi.

Utwórz dwie sieci lokalne na następujące informacje:

| Nazwa sieci | Adres bramy sieci VPN | Przestrzeń adresowa | Uwagi |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Podczas tworzenia sieci lokalnej do symbolu zastępczego adres bramy. Adres bramy rzeczywistych jest wypełniony, po utworzeniu bramy. Upewnij się, że przestrzeń adresowa dokładnie odpowiada odpowiednich zdalna sieć wirtualna; w takim przypadku w regionie wschodnie stany USA utworzona sieć wirtualna. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Podczas tworzenia sieci lokalnej do symbolu zastępczego adres bramy. Adres bramy rzeczywistych jest wypełniony, po utworzeniu bramy. Upewnij się, że przestrzeń adresowa dokładnie odpowiada odpowiednich zdalna sieć wirtualna; w takim przypadku utworzona sieć wirtualna regionu zachodnie stany USA. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Krok 3: Sieć "Lokalnie" mapy do odpowiednich sieci wirtualnych
W portalu Azure wybierz każdej sieci wirtualnej, kliknij przycisk "Konfiguruj" Sprawdź "Połącz z sieci lokalnej" i wybierz sieci lokalnej na następujące szczegóły:

| Virtual Network | Sieci lokalnej |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Krok 4: Tworzenie bramy na VNET1 i VNET2
Z poziomu pulpitu nawigacyjnego sieci wirtualnych kliknij przycisk Utwórz BRAMĘ, aby wyzwolić bramy sieci VPN, w procesie inicjowania obsługi. Po kilku minutach adres bramy rzeczywiste powinien być wyświetlany pulpit nawigacyjny w każdej sieci wirtualnej.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Krok 5: Aktualizacji "Lokalnie" sieci z adresami odpowiednich "brama"
Edytuj zarówno lokalnej sieci należy zastąpić adres IP bramy symbol zastępczy adres IP rzeczywista właśnie elastycznie bramy. Użyj następującego mapowania:

<table>
<tr><th>Sieci lokalnej    </th><th>Brama sieci wirtualnej</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Brama hk — sieci wirtualnej zachód us</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Brama hk — sieci wirtualnej wschód us</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Krok 6: Zaktualizuj klucza wspólnego
Użyj następującego skryptu programu Powershell można zaktualizować klucza IPSec każdej bramy sieci VPN [Użyj klucza sake dla obu bram]: Set AzureVNetGatewayKey - VNetName hk — sieci wirtualnej wschód us - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Zestaw AzureVNetGatewayKey - VNetName hk — sieci wirtualnej zachód us - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Krok 7: Nawiązania połączenia do Wirtualnymi
W portalu Azure użyj menu "Pulpitu NAWIGACYJNEGO" sieciami wirtualnymi Nawiązanie połączenia usługi brama brama. Używanie elementów menu "POŁĄCZ" w dolnym pasku narzędzi. Po kilku minutach pulpitu nawigacyjnego powinien być wyświetlany szczegóły połączenia graficznie.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Krok 8: Tworzenie maszyn wirtualnych w regionie #2
Utwórz obraz Ubuntu zgodnie z opisem w regionie #1 wdrożenia, wykonując kroki tego samego lub kopiowania pliku obrazu dysku VHD do konta magazynu Azure znajduje się w regionie #2 a obrazu. Skorzystaj z tego obrazu i Utwórz następującą listę maszyn wirtualnych do nowej usługi w chmurze hk-c-svc wschód nam:

| Nazwa maszyny | Podsieć | Adres IP | Zestaw dostępności | DC/Rack | Inicjatora? |
| --- | --- | --- | --- | --- | --- |
| HK-c1-wschód us |dane |10.2.2.4 |hk-c-aset-1 |DC = stojak EASTUS = szafa1 |Yes |
| HK-c2-wschód us |dane |10.2.2.5 |hk-c-aset-1 |DC = stojak EASTUS = szafa1 |Nie |
| HK-c3-wschód us |dane |10.2.2.6 |hk-c-aset-1 |DC = stojak EASTUS = szafa2 |Yes |
| HK-c5-wschód us |dane |10.2.2.8 |hk-c-aset-2 |DC = stojak EASTUS = szafa3 |Yes |
| HK-c6-wschód us |dane |10.2.2.9 |hk-c-aset-2 |DC = stojak EASTUS = szafa3 |Nie |
| HK-c7-wschód us |dane |10.2.2.10 |hk-c-aset-2 |DC = stojak EASTUS = rack4 |Yes |
| HK-c8-wschód us |dane |10.2.2.11 |hk-c-aset-2 |DC = stojak EASTUS = rack4 |Nie |
| HK-w1 — wschód us |web |10.2.1.4 |hk-w-aset-1 |ND |ND |
| HK-w2 — wschód us |web |10.2.1.5 |hk-w-aset-1 |ND |ND |

Postępuj zgodnie z instrukcjami region #1, ale użyj 10.2.xxx.xxx przestrzeni adresowej.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Krok 9: Konfigurowanie Cassandra na każdej maszynie Wirtualnej
Zaloguj się do maszyny Wirtualnej i wykonaj następujące czynności:

1. Edytuj $CASS_HOME/conf/cassandra-rackdc.properties do określania właściwości stojaku i centrum danych w formacie: dc = stojak EASTUS = szafa1
2. Edytuj cassandra.yaml do konfigurowania węzłów inicjatora: ziarna: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>Krok 10: Uruchom Cassandra
Zaloguj się do każdej maszyny Wirtualnej i uruchom Cassandra w tle, uruchamiając następujące polecenie: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Przetestowanie klastra w przypadku
Już Cassandra została wdrożona do 16 węzłów o 8 węzłów w każdym regionie Azure. Te węzły znajdują się w tym samym klastrze z wspólnej nazwy klastra i konfiguracja węzła inicjatora. Aby przetestować klastra, należy wykonać poniższe czynności:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Krok 1: Uzyskiwanie adresu IP usługi równoważenia obciążenia wewnętrznego dla regionów przy użyciu programu PowerShell
* Get-AzureInternalLoadbalancer - ServiceName "hk — c-svc zachód us"
* Get-AzureInternalLoadbalancer - ServiceName "hk — c-svc wschód us"  
  
    Należy zwrócić uwagę na adresy IP (na przykład zachodnie - 10.1.2.101, wschód - 10.2.2.101) wyświetlane.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Krok 2: Wykonaj następujące czynności w regionie Zachód po zalogowaniu w hk-w1 — zachód us
1. Execute $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Uruchom następujące polecenia CQL:
   
     TWORZENIE przestrzeni KLUCZY customers_ks z REPLIKACJĄ = {"class": "NetworkToplogyStrategy", "WESTUS": 3, EASTUS: 3};   UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wstaw do Customers(customer_id, firstname, lastname) wartości 2, 'Magdalena', 'Nowak';   Wybierz * od klientów.

Powinien zostać wyświetlony ekran, tak jak poniżej:

| customer_id | Imię | nazwisko |
| --- | --- | --- |
| 1 |Jan |Nowak |
| 2 |Magdalena |Nowak |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Krok 3: Wykonaj następujące czynności w regionie wschodnie po zalogowaniu w hk-w1 — wschód us:
1. Execute $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Uruchom następujące polecenia CQL:
   
     UŻYJ customers_ks;   Tworzenie tabeli Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Wstaw do Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Wstaw do Customers(customer_id, firstname, lastname) wartości 2, 'Magdalena', 'Nowak';   Wybierz * od klientów.

Wyświetlanie tego samego powinny być widoczne, widziany dla regionu zachodnie:

| customer_id | Imię | nazwisko |
| --- | --- | --- |
| 1 |Jan |Nowak |
| 2 |Magdalena |Nowak |

Wykonaj kilka więcej operacji wstawienia i zobacz, czy te replikowane Zachodnie-us, które są częścią klastra.

## <a name="test-cassandra-cluster-from-nodejs"></a>Testowanie Cassandra klastra w oprogramowaniu Node.js
Przy użyciu jednej z maszyn wirtualnych systemu Linux utworzone wcześniej w warstwie "web", wykonaj proste skryptu Node.js do odczytywania danych wcześniej wstawionych

**Krok 1: Instalacja klienta Cassandra i Node.js**

1. Instalowanie środowiska Node.js i npm
2. Instalowanie pakietu "cassandra klienta w węźle" za pomocą programu npm
3. Uruchom następujący skrypt w wierszu polecenia powłoki, który zawiera ciąg json pobrane dane:
   
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
Microsoft Azure to elastyczna platforma, która umożliwia uruchamianie zarówno firmy Microsoft, jak i oprogramowanie typu open source, jak zostało to pokazane w tym ćwiczeniu. Klastry Cassandra wysokiej dostępności można wdrożyć na jednego centrum danych przez rozłożenie węzły klastra w wielu domenach awarii. Klastry Cassandra można także wdrożyć w różnych regionach platformy Azure odległymi geograficznie systemów potwierdzającego po awarii. Azure i Cassandra razem umożliwia konstruowanie wysoce skalowalną, wysokiej dostępności i usługi w chmurze możliwych do odzyskania po awarii wymagane przez internet współczesnych skalowania usług.  

## <a name="references"></a>Dokumentacja
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

