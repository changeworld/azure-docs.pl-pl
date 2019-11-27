---
title: Wykrywanie zagrożeń warstwy usług platformy Azure — Azure Security Center
description: W tym temacie przedstawiono alerty warstwy usług platformy Azure dostępne w Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186499"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Wykrywanie zagrożeń dla warstwy usług platformy Azure w Azure Security Center

W tym temacie przedstawiono Azure Security Center alerty dostępne podczas monitorowania następujących warstw usługi platformy Azure:

* [Warstwa sieci platformy Azure](#network-layer)
* [Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)](#management-layer)
* [Usługa Azure Key Vault](#azure-keyvault)

>[!NOTE]
>Poniższe analizy dotyczą wszystkich typów zasobów. Wykorzystują dane telemetryczne, które Security Center udostępniają, wybierając wewnętrzne kanały informacyjne platformy Azure.

## Warstwa sieci platformy Azure<a name="network-layer"></a>

Security Center analiza warstwy sieciowej opiera się na przykładowych [danych IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), które są nagłówkami pakietów zebranymi przez routery podstawowe platformy Azure. W oparciu o te strumieniowe źródło danych Security Center modele uczenia maszynowego identyfikują i flagą złośliwych działań związanych z ruchem. Aby wzbogacić adresy IP, Security Center korzysta z bazy danych Microsoft Threat Intelligence.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Podejrzane działanie wychodzącej sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Remote Desktop Protocol (RDP) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego ataku zewnętrznego punktu końcowego RDP. Ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane działanie sieci wychodzącej RDP z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową wychodzącą komunikację RDP, pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego wymuszania ataku zewnętrznych punktów końcowych RDP. Ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację wychodzącą Secure Shell (SSH) pochodzącą z zasobu we wdrożeniu. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego ataku zewnętrznego punktu końcowego protokołu SSH. Ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane wychodzące działanie sieci SSH z wieloma lokalizacjami docelowymi**|Przykładowa analiza ruchu w sieci wykryła nietypową wychodzącą komunikację SSH, pochodzącą z zasobu we wdrożeniu do wielu miejsc docelowych. To działanie jest uznawane za nietypowe w przypadku tego środowiska. Może to wskazywać, że naruszono bezpieczeństwo zasobu i jest on teraz używany do pełnego wymuszania ataku zewnętrznych punktów końcowych SSH. Ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|
|**Podejrzane przychodzące działanie sieci SSH z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację SSH z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę nawiązania pełnego ataku interfejsu SSH z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci SSH**|Przykładowa analiza ruchu w sieci wykryła nietypową komunikację SSH z zasobem w danym wdrożeniu. Relatywnie duża liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania ataku na ataki w interfejsie SSH.
|**Podejrzane przychodzące działanie sieci RDP z wielu źródeł**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z wielu źródeł do zasobu we wdrożeniu. Różne unikatowe adresy IP łączące się z zasobem są uznawane za nietypowe w przypadku tego środowiska. To działanie może wskazywać na próbę rozdzielenia ataku interfejsu RDP z wielu hostów (botnet).|
|**Podejrzane przychodzące działanie sieci RDP**|Przykładowa analiza ruchu w sieci wykryła nietypową przychodzącą komunikację RDP z zasobem w danym wdrożeniu. Relatywnie duża liczba połączeń przychodzących do zasobu jest traktowana jako nietypowa dla tego środowiska. To działanie może wskazywać na próbę nawiązania ataku na ataki w interfejsie SSH.|
|**Wykryto komunikację sieciową ze złośliwym adresem**|Przykładowa analiza ruchu w sieci wykryła komunikację pochodzącą z zasobu we wdrożeniu za pomocą możliwego serwera z poleceniem i kontrolą (C & C). Ten typ działania może spowodować, że adres IP zostanie oflagowany jako złośliwy przez jednostki zewnętrzne.|

Aby zrozumieć, jak Security Center mogą używać sygnałów związanych z siecią w celu zastosowania ochrony przed zagrożeniami, zobacz [heurystyczne wykrywanie nazw DNS w Azure Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Alerty wykrywania zagrożeń warstwy sieciowej platformy Azure w Azure Security Center są generowane tylko na maszynach wirtualnych, które przypisały ten sam adres IP przez całą godzinę, podczas którego przeprowadzono podejrzaną komunikację. Dotyczy to maszyn wirtualnych, a także maszyn wirtualnych, które są tworzone w ramach subskrypcji klienta w ramach usługi zarządzanej (np. AKS, datakostki).

## Warstwa zarządzania Azure (Azure Resource Manager) (wersja zapoznawcza)<a name ="management-layer"></a>

>[!NOTE]
>Warstwa ochrony Security Center oparta na Azure Resource Manager jest obecnie dostępna w wersji zapoznawczej.

Security Center oferuje dodatkową warstwę ochrony przy użyciu zdarzeń Azure Resource Manager, które są traktowane jako płaszczyzny kontroli dla platformy Azure. Analizując Azure Resource Manager rekordy, Security Center wykrywa nietypowe lub potencjalnie szkodliwe operacje w środowisku subskrypcji platformy Azure.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Uruchomiono zestaw narzędzi mikroserii**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Mikroseria [](https://github.com/NetSPI/MicroBurst) narzędzi może być używana przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji, identyfikowania niezabezpieczonych konfiguracji i przecieku poufnych informacji.|
|**Azurite zestaw narzędzi**|W Twoim środowisku wykryto znane uruchomienie zestawu narzędzi Cloud-Environment rekonesans. Narzędzie [azurite](https://github.com/mwrlabs/Azurite) może być używane przez osobę atakującą (lub przeprowadzającą testy) do mapowania zasobów subskrypcji i identyfikowania niezabezpieczonych konfiguracji.|
|**Podejrzana sesja zarządzania przy użyciu nieaktywnego konta**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który nie jest używany przez długi czas, wykonuje teraz akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Podejrzana sesja zarządzania przy użyciu programu PowerShell**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Podmiot zabezpieczeń, który regularnie nie używa programu PowerShell do zarządzania środowiskiem subskrypcji, korzysta obecnie z programu PowerShell i wykonuje akcje, które mogą zapewnić ochronę trwałości dla osoby atakującej.|
|**Korzystanie z zaawansowanych technik trwałości platformy Azure**|Analiza dzienników aktywności subskrypcji wykryła podejrzane zachowanie. Role niestandardowe otrzymały jednostki tożsamości legitimized. Może to spowodować, że osoba atakująca uzyska utrwalenie w środowisku klienta platformy Azure.|
|**Działanie z nieczęstego kraju**|Wystąpiło działanie z lokalizacji, która nie była ostatnio lub kiedykolwiek odwiedzana przez dowolnego użytkownika w organizacji.<br/>Wykrywanie uwzględnia ostatnie lokalizacje działań do określenia lokalizacji nowych i rzadkie. Aparatu wykrywania anomalii są przechowywane informacje o powyższych lokalizacjach, które posługują się użytkownicy w organizacji. 
|**Aktywność z anonimowych adresów IP**|Wykryto aktywność użytkowników z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. <br/>Te serwery proxy są używane przez osoby, które chcą ukryć adres IP urządzenia i mogą być używane do złośliwego celu. Ta funkcja wykrywania używa algorytmu uczenia maszynowego, który zmniejsza liczbę fałszywych dodatnich, takich jak źle otagowane adresy IP, które są szeroko używane przez użytkowników w organizacji.|
|**Wykryto niemożliwa podróż**|Wystąpiły dwa działania użytkownika (w jednej lub wielu sesjach) pochodzące z lokalizacji geograficznie odległej. Dzieje się tak w przedziale czasowym krótszym niż czas, przez który użytkownik mógł podróżować z pierwszej lokalizacji do drugiej. Oznacza to, że inny użytkownik korzysta z tych samych poświadczeń. <br/>Ta funkcja wykrywania używa algorytmu uczenia maszynowego, który ignoruje oczywistych fałszywych zwracanych wyników, takich jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. Wykrycie ma początkowy okres uczenia wynoszący siedem dni, podczas którego nauczy się wzorca aktywności nowego użytkownika.|

>[!NOTE]
> Niektóre z powyższych analiz są obsługiwane przez Microsoft Cloud App Security. Aby skorzystać z tych analiz, należy aktywować licencję Cloud App Securityową. Jeśli masz licencję na Cloud App Security, te alerty są domyślnie włączone. Aby je wyłączyć:
>
> 1. W bloku **Security Center** wybierz pozycję **zasady zabezpieczeń**. W przypadku subskrypcji, którą chcesz zmienić, wybierz pozycję **Edytuj ustawienia**.
> 2. Wybierz pozycję **wykrywanie zagrożeń**.
> 3. W obszarze **Włącz integracje**wyczyść pole wyboru **Zezwalaj Microsoft Cloud App Security na dostęp do moich danych**i wybierz pozycję **Zapisz**.

>[!NOTE]
>Security Center przechowuje dane klienta związane z zabezpieczeniami w tym samym miejscu geograficznym, co jego zasób. Jeśli firma Microsoft jeszcze nie wdrożona Security Center w lokalizacji geograficznej zasobu, przechowuje dane w Stany Zjednoczone. Gdy Cloud App Security jest włączona, te informacje są przechowywane zgodnie z zasadami lokalizacji geograficznej Cloud App Security. Aby uzyskać więcej informacji, zobacz [Magazyn danych dla usług nieregionalnych](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault<a name="azure-keyvault"></a>

Azure Key Vault to usługa w chmurze, która chroni klucze szyfrowania i wpisy tajne, takie jak certyfikaty, ciągi połączeń i hasła. 

Azure Security Center obejmuje platformę Azure — natywną, zaawansowaną ochronę przed zagrożeniami dla Azure Key Vault, zapewniając dodatkową warstwę analizy zabezpieczeń. Security Center wykrywa nietypowe i potencjalnie szkodliwe próby dostępu do kont Key Vault lub korzystania z nich. Ta warstwa ochrony pozwala na rozwiązywanie zagrożeń bez konieczności postanowień eksperta w zakresie bezpieczeństwa, bez konieczności zarządzania systemami monitorowania zabezpieczeń innych firm.  

W przypadku wystąpienia nietypowych działań Security Center wyświetla alerty i opcjonalnie wysyła je za pośrednictwem poczty e-mail do administratorów subskrypcji. Te alerty obejmują szczegóły podejrzanego działania i zalecenia dotyczące sposobu badania i korygowania zagrożeń. 

> [!NOTE]
> Ta usługa nie jest obecnie dostępna w regionach platformy Azure dla instytucji rządowych i suwerennych.

> [!div class="mx-tableFixed"]

|Alerty|Opis|
|---|---|
|**Dostęp z węzła wyjścia TOR do Key Vault**|Dostęp do Key Vault został uzyskany przez kogoś przy użyciu systemu zachowywanie anonimowości IP sieci w celu ukrycia ich lokalizacji. Złośliwe osoby często próbują ukryć swoje lokalizacje podczas próby uzyskania nieautoryzowanego dostępu do zasobów połączonych z Internetem.|
|**Podejrzana zmiana zasad i tajne zapytanie w Key Vault**|Została wprowadzona zmiana zasad Key Vault, a następnie wystąpiły operacje wyświetlania i/lub pobierania wpisów tajnych. Ponadto ten wzorzec operacji nie jest zwykle wykonywany przez użytkownika w tym magazynie. Jest to wysoce wskaźnik, że zabezpieczenia Key Vault zostały naruszone i wpisy tajne w ramach programu zostały skradzione przez złośliwy aktor.|
|**Lista podejrzanych wpisów tajnych i zapytanie w Key Vault**|Po operacji na liście wpisów tajnych wykonano wiele tajnych operacji get. Ponadto ten wzorzec operacji nie jest zwykle wykonywany przez użytkownika w tym magazynie. Oznacza to, że ktoś może zatopić wpisy tajne przechowywane w Key Vault dla potencjalnie złośliwych celów.|
|**Nietypowa para aplikacji użytkownika uzyskała dostęp do Key Vault**|Dostęp do Key Vault jest uzyskiwany przez parowanie aplikacji użytkownika, które zwykle nie mają do niej dostępu. Może to być legalna próba dostępu (na przykład po aktualizacji infrastruktury lub kodu). Jest to również możliwe wskazanie naruszenia bezpieczeństwa infrastruktury i złośliwy aktor próbuje uzyskać dostęp do Key Vault.|
|**Nietypowa aplikacja uzyskała dostęp do Key Vault**|Dostęp do Key Vault jest uzyskiwany przez aplikację, która zwykle nie uzyskuje do niej dostępu. Może to być legalna próba dostępu (na przykład po aktualizacji infrastruktury lub kodu). Jest to również możliwe wskazanie naruszenia bezpieczeństwa infrastruktury i złośliwy aktor próbuje uzyskać dostęp do Key Vault.|
|**Nietypowy użytkownik uzyskał dostęp do Key Vault**|Dostęp do Key Vault został uzyskany przez użytkownika, który zwykle nie uzyskuje do niego dostępu. Może to być legalna próba dostępu (na przykład nowy użytkownik, który potrzebuje dostępu do organizacji). Jest to również możliwe wskazanie naruszenia bezpieczeństwa infrastruktury i złośliwy aktor próbuje uzyskać dostęp do Key Vault.|
|**Wzorzec nietypowej operacji w Key Vault**|Przeprowadzono nietypowy zestaw operacji Key Vault w porównaniu z danymi historycznymi. Działania Key Vault są zwykle takie same w czasie. Może to być legalna zmiana w działaniu. Alternatywnie można naruszyć bezpieczeństwo infrastruktury i zapewnić dalsze badania.|
|**Duża liczba operacji w Key Vault**|W porównaniu z danymi historycznymi przeprowadzono większą liczbę operacji Key Vault. Działania Key Vault są zwykle takie same w czasie. Może to być legalna zmiana w działaniu. Alternatywnie można naruszyć bezpieczeństwo infrastruktury i zapewnić dalsze badania.|
|**Użytkownik uzyskał wysoką ilość magazynów kluczy**|Liczba magazynów, do których dostęp użytkownik lub aplikacja została zmieniona w porównaniu z danymi historycznymi. Działania Key Vault są zwykle takie same w czasie. Może to być legalna zmiana w działaniu. Alternatywnie można naruszyć bezpieczeństwo infrastruktury i zapewnić dalsze badania.|