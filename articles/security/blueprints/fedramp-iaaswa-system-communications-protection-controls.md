---
title: Ochrona komunikacji i systemu plan - automatyzacji aplikacji sieci Web FedRAMP - zgodności i zabezpieczeń platformy Azure
description: Automatyzacji aplikacji sieci Web FedRAMP — systemu i ochronę komunikacji
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b96cdef1-ce3a-4f73-9a9e-f2cbd056d485
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 6749ad50cd1ea1cd4ec6ca2f86fef43a9f1515d9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="system-and-communications-protection-sc"></a>System i ochronę komunikacji (SC)

> [!NOTE]
> Formanty są definiowane przez Instytut NIST i Stanów Zjednoczonych Departament Handlu jako część poprawki NIST Special Publication 800-53 4. Zapoznaj się NIST 800 53 Rev. 4 informacji na temat testowania procedury i wskazówki dotyczące każdego formantu.

## <a name="nist-800-53-control-sc-1"></a>NIST 800 53 kontroli SC-1

#### <a name="system-and-communications-protection-policy-and-procedures"></a>Systemu i zasady ochrony komunikacji i procedur

**SC-1** organizacji rozwija, dokumentów i upowszechnia do [przypisania: techniczną zdefiniowany przez organizację lub ról] systemu i komunikacji zasady ochrony, którego dotyczy cel, zakres, ról, obowiązki, zarządzania zobowiązań, koordynacji między jednostki organizacyjnej i zgodności; i procedury w celu ułatwienia implementacji systemu i zasady ochrony komunikacji i skojarzonego z nim systemu i kontrolek ochrony komunikacji; przegląda i aktualizuje bieżące zasady ochrony systemu i komunikacji [przypisania: zdefiniowane przez organizację częstotliwość]; i procedur ochrony systemu i komunikacji [przypisania: zdefiniowane przez organizację częstotliwość].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady na poziomie przedsiębiorstwa systemu i komunikacji ochrony klienta i procedur mogą być wystarczające w celu rozwiązania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-2"></a>NIST 800 53 kontroli SC-2

#### <a name="application-partitioning"></a>Partycjonowanie aplikacji

**SC 2** systemu informacji oddziela funkcji użytkownika (w tym usług interfejsu użytkownika) od funkcji zarządzania systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan oddziela funkcji użytkownika od funkcji zarządzania systemu za pośrednictwem wymuszanie kontroli dostępu logicznych i architektura systemu. Funkcja użytkownika jest ograniczona do interfejsów aplikacji wdrożonych przez klienta sieci web. Interfejsy do obsługi funkcji zarządzania systemu są niezależne od interfejsów użytkownika. Wszystkie łączność w procesie zarządzania odbywa się za pośrednictwem bezpiecznego bastionu hosta (jumpbox) znajduje się w podsieci zarządzania z reguły grupy zabezpieczeń sieci można ograniczyć dostęp do zasobów w środowisku produkcyjnym zgodnie z potrzebami. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-3"></a>NIST 800 53 kontroli SC-3

#### <a name="security-function-isolation"></a>Izolacja funkcji zabezpieczeń

**SC-3** systemu informacji izoluje funkcji zabezpieczeń z funkcji niezwiązana z zabezpieczeniami.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows zachowuje wykonywania oddzielnych domen dla każdego procesu wykonywanego przez przypisanie prywatnego wirtualnej przestrzeni adresowej do każdego procesu. Ponadto rozwiązanie implementuje architektury i dostęp do formantów opracowana w celu odizolowania funkcje zabezpieczeń, w miarę potrzeby. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-4"></a>NIST 800 53 kontroli SC-4

#### <a name="information-in-shared-resources"></a>Informacje zawarte w udostępnionych zasobów

**SC-4** systemu informacji uniemożliwia informacji nieautoryzowanym i niezamierzone transfer za pośrednictwem udostępnionych zasobów systemowych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System operacyjny zarządza zasobów (np. pamięci, magazynu) w taki sposób, że informacje są dostępne tylko dla użytkowników i ról z odpowiednimi uprawnieniami. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-5"></a>NIST 800 53 kontroli SC-5

#### <a name="denial-of-service-protection"></a>Odmowa usługi ochrony

**SC-5** system informacji chroni przed lub ogranicza skutki następujących typów ataki: [przypisania: typy danych zdefiniowane przez organizację odmowy usługi lub odwołania do źródła tych informacji] przez wykorzystanie [przypisania: zabezpieczeń zdefiniowany przez organizację].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża bramę aplikacji obejmują zapory aplikacji sieci web i funkcji równoważenia obciążenia. Wdrożone maszyny wirtualne obsługujące warstwa sieci web, warstwy bazy danych i usługi Active Directory są wdrażane w zestawie dostępności skalowalności. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-6"></a>NIST 800 53 kontroli SC-6

#### <a name="resource-availability"></a>Dostępność zasobów

**SC 6** system informacji chroni dostępność zasobów przydzielając [przypisania: zasoby zdefiniowane przez organizację] przez [wybór (jeden lub więcej); priorytet przydziału; [Przypisania: zabezpieczeń zdefiniowany przez organizację]].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. Każdy proces systemu Windows udostępnia zasoby niezbędne do wykonania programu. Priorytet zasobów jest zarządzana przez system operacyjny. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-7a"></a>NIST 800 53 kontroli SC-7.a

#### <a name="boundary-protection"></a>Ochrona granic

**SC 7.a** systemu informacji monitorów i kontroluje komunikację na granicy zewnętrznego systemu i na klucza wewnętrzny granic w ramach systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża bramy aplikacji, usługi równoważenia obciążenia i konfiguruje reguły grupy zabezpieczeń sieci w celu kontrolowania commutations na granicach zewnętrznych i między podsieciami wewnętrznego. Brama aplikacji, usługi równoważenia obciążenia i zdarzeń grupy zabezpieczeń sieci i dzienników diagnostycznych są zbierane przez analityka dziennika umożliwia monitorowanie klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-7b"></a>Formant SC NIST 800 53-od 7.b

#### <a name="boundary-protection"></a>Ochrona granic

**Od 7.b SC** system informacji implementuje podsieci dla składników systemu publicznie, które są [zaznaczenia: fizycznie; logicznie] oddzielona od sieci organizacji wewnętrznych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania zasobów w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci. Podsieci są logicznie oddzielone reguły grupy zabezpieczeń sieci zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to niezbędne do obsługi funkcji zarządzania i systemu (np. ruch zewnętrzny nie dostęp do bazy danych, zarządzanie, lub podsieci usługi Active Directory). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-7c"></a>NIST 800 53 kontroli SC-7.c

#### <a name="boundary-protection"></a>Ochrona granic

**SC 7.c** systemu informacji łączy sieci zewnętrznych lub systemów informacyjnych tylko przez zarządzane interfejsy składające się z urządzenia ochrony granic rozmieszczone zgodnie z architektury zabezpieczeń organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża bramę aplikacji do zarządzania połączeniami zewnętrzne do aplikacji sieci web wdrożonych przez klienta. Połączenia zewnętrzne dla zarządzania dostępu są ograniczone do hosta bastionu / adresy IP autoryzowane jumpbox wdrożone w podsieci zarządzania stosowane do ograniczenia połączeń zewnętrznych z reguły zabezpieczeń sieci. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-3"></a>NIST 800 53 kontroli SC-7 (3)

#### <a name="boundary-protection--access-points"></a>Ochrona granic | Punkty dostępu

**SC-7 (3)** organizacji ogranicza liczbę połączeń sieci zewnętrznej do systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża dwóch publicznych adresów IP: jeden skojarzone z bramą aplikacji; jeden skojarzona z hostem bastionu zarządzania / jumpbox. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-4a"></a>Tj NIST 800 53 kontroli SC-7 (4).

#### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Usług telekomunikacyjnych zewnętrznych

**.A SC-7 (4)** organizacji implementuje interfejs zarządzanego dla każdej usługi telekomunikacyjnych zewnętrznych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża dwóch publicznych adresów IP: jeden skojarzone z bramą aplikacji; jeden skojarzona z hostem bastionu zarządzania / jumpbox. Zarządzanie z tych interfejsów jest włączone za pośrednictwem technologia Sdn. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-4b"></a>NIST 800 53 b kontroli SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Usług telekomunikacyjnych zewnętrznych

**B SC-7 (4)** organizacji ustanawia zasadę przepływu ruchu dla każdego zarządzanego interfejsu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdraża dwóch publicznych adresów IP: jeden skojarzone z bramą aplikacji; jeden skojarzona z hostem bastionu zarządzania / jumpbox. Zarządzanie z tych interfejsów jest włączone za pośrednictwem technologia Sdn. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-4c"></a>NIST 800 53 .c kontroli SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Usług telekomunikacyjnych zewnętrznych

**SC-7 (4) .c** organizacji zapewnia ochronę poufności i integralności informacje przesyłane między każdego interfejsu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brama aplikacji w sieci web wdrożone przez ten plan jest konfigurowana odbiornika protokołu HTTPS, następujących poufności i integralności sesje komunikacji. Połączeń pulpitu zdalnego jumpbox również są szyfrowane, zapewniając poufności i integralności. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-4d"></a>NIST 800 53 .d kontroli SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Usług telekomunikacyjnych zewnętrznych

**SC-7 (4) .d** każdego wyjątku do zasady przepływ ruchu z pomocniczych misji/potrzeb i czas trwania wymagających dokumentów organizacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klienci nie są odpowiedzialne za operacji centrum danych (w celu uwzględnienia usług telekomunikacyjnych). Wszystkie usługi telekomunikacyjnych są udostępniane i zarządzane przez Microsoft Azure. Ten formant jest dziedziczona z platformy Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-4e"></a>NIST 800 53 .e kontroli SC-7 (4)

#### <a name="boundary-protection--external-telecommunications-services"></a>Ochrona granic | Usług telekomunikacyjnych zewnętrznych

**.E SC-7 (4)** Organizacja przegląda wyjątki od zasad przepływu ruchu [przypisania: częstotliwość zdefiniowany przez organizację] i usuwa wyjątki, które nie są już obsługiwane przez jawne muszą misji/biznesowych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klienci nie są odpowiedzialne za operacji centrum danych (w celu uwzględnienia usług telekomunikacyjnych). Wszystkie usługi telekomunikacyjnych są udostępniane i zarządzane przez Microsoft Azure. Ten formant jest dziedziczona z platformy Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-5"></a>NIST 800 53 kontroli SC-7 (5)

#### <a name="boundary-protection--deny-by-default--allow-by-exception"></a>Ochrona granic | Odmów domyślnie / Zezwalaj przez wyjątek

**SC-7 (5)** systemu informacji o zarządzanych interfejsów nie zezwala na ruch sieciowy komunikacyjnych domyślnie i zezwala na ruch łączności sieciowej przez wyjątek (tj., Odmów wszystkich, przez wyjątek).

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zestawy reguł stosowane do sieciowej grupy zabezpieczeń wdrożone przez ten plan są skonfigurowane przy użyciu schematu Odmów domyślnie. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-7"></a>NIST 800 53 kontroli SC-7 (7)

#### <a name="boundary-protection--prevent-split-tunneling-for-remote-devices"></a>Ochrona granic | Zapobiegaj tunelowanie podzielone na urządzeniu zdalnym

**SC-7 (7)** system informacji, w połączeniu z urządzeniem zdalnym uniemożliwia urządzenia z jednocześnie nawiązywanie połączeń z systemem innym niż zdalnego w systemie i komunikacji za pośrednictwem niektóre inne połączenie z zasobami w sieciach zewnętrznych.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Zasady konfiguracji klienta urządzenie zdalne na poziomie przedsiębiorstwa może dotyczyć tunelowania podzielonego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-8"></a>NIST 800 53 kontroli SC-7 (8)

#### <a name="boundary-protection--route-traffic-to-authenticated-proxy-servers"></a>Ochrona granic | Kierować ruchem do serwerów uwierzytelnionego serwera Proxy

**SC-7 (8)** tras systemowych informacji [przypisania: ruchu zdefiniowany przez organizację komunikacji wewnętrznej] do [przypisania: zdefiniowane przez organizację sieci zewnętrznych] przy użyciu uwierzytelnianego serwerów proxy w zarządzanych interfejsów.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za informacje routingu zdefiniowane przez klientów za pośrednictwem uwierzytelnionego serwera proxy do sieci zewnętrznej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-10"></a>NIST 800 53 kontroli SC-7 (10)

#### <a name="boundary-protection--prevent-unauthorized-exfiltration"></a>Ochrona granic | Uniemożliwić nieautoryzowany Exfiltration

**SC-7 (10)** organizacji uniemożliwia nieautoryzowanym exfiltration informacji między zarządzane interfejsy.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za uniemożliwia nieautoryzowanym exfiltration informacji między zarządzane interfejsy. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-12"></a>NIST 800 53 kontroli SC-7 (12)

#### <a name="boundary-protection--host-based-protection"></a>Ochrona granic | Ochrona oparta na hoście

**SC-7 (12)** implementuje organizacji [przypisania: mechanizmy ochrony zdefiniowany przez organizację granic oparta na hoście] na [przypisania: składniki systemowe zdefiniowane przez organizację informacje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan skonfigurowano włączona zapora oparta na hoście. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-13"></a>NIST 800 53 kontroli SC-7 [13]

#### <a name="boundary-protection--isolation-of-security-tools--mechanisms--support-components"></a>Ochrona granic | Izolacja narzędzia zabezpieczeń / mechanizmów / obsługuje składniki

**SC-7 [13]** izoluje organizacji [przypisania: narzędzia zabezpieczeń informacje zdefiniowane przez organizację, mechanizmów i składników obsługi] z innymi składnikami systemu informacji wewnętrznych zaimplementowanie fizycznie oddzielne podsieci z zarządzane interfejsy z innymi składnikami systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania zasobów w architekturze z podsiecią oddzielnego zarządzania dla wdrożenia klienta narzędzi zabezpieczeń informacji i składników obsługi. Podsieci są logicznie oddzielone reguły grupy zabezpieczeń sieci. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-18"></a>NIST 800 53 kontroli SC-7 (18)

#### <a name="boundary-protection--fail-secure"></a>Ochrona granic | Niepowodzenie bezpiecznego

**SC-7 (18)** operacyjne awarii urządzenia ochrony granic bezpiecznie awarii systemu informacji.

**Obowiązki:** `Azure Only`

|||
|---|---|
| **Klienta** | Brak urządzeń ochrony fizycznej granicy w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Microsoft Azure wdraża geograficznie nadmiarowe i oddzielne serwery bramy i VPN protokołu SSL. W przypadku awarii systemu bramy nie jest on bezpiecznego i dostęp jest ograniczony do środowiska. Aby można było nawiązać połączenie ze środowiskiem Microsoft Azure, użytkownik musi ustanowienia połączenia oddzielne aktywnego serwera bramy zarządza Microsoft Azure. <br /> Ponadto w przypadku awarii urządzenia sieciowe Microsoft Azure (w tym routery brzegowe, dostępu do routerów, usługi równoważenia obciążenia, przełączniki agregacji i TORACH), wykorzystywanych obwodu zostanie rozłączony, co nie może bezpiecznie. Błąd urządzenia sieciowego Microsoft Azure nie może prowadzić do lub spowodować, że informacje o zewnętrznych do systemu, aby wprowadzić urządzenia ani awarii zezwolić na nieautoryzowany informacje o wersji. Wbudowane nadmiarowość umożliwia zasoby Microsoft Azure awarii bez wywierania wpływu na dostępność. |


 ### <a name="nist-800-53-control-sc-7-20"></a>NIST 800 53 kontroli SC-7 (20)

#### <a name="boundary-protection--dynamic-isolation--segregation"></a>Ochrona granic | Dynamiczne izolacji / podziału

**SC-7 (20)** system informacji zapewnia możliwość dynamicznie isolate/segregowanie [przypisania: składniki systemowe zdefiniowane przez organizację informacje] z innymi składnikami systemu.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie, że system ma możliwość dynamicznie izolowanie wdrożyć klienta zasobów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-7-21"></a>NIST 800 53 kontroli SC-7 (21)

#### <a name="boundary-protection--isolation-of-information-system-components"></a>Ochrona granic | Izolacja składników systemu informacji

**SC-7 (21)** organizacja stosuje mechanizmy ochrony granic do oddzielania [przypisania: składniki systemowe zdefiniowane przez organizację informacje] pomocniczych [przypisania: zdefiniowany przez organizację misji i/lub business funkcje].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania zasobów w architekturze podsieci WWW, podsieci bazy danych, podsieci usługi Active Directory i zarządzania podsieci. Podsieci są logicznie oddzielone zastosować do poszczególnych podsieci, aby ograniczyć ruch między podsieciami, aby tylko to konieczne do systemu oraz funkcji zarządzania reguły grupy zabezpieczeń sieci. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-8"></a>NIST 800 53 kontroli SC-8

#### <a name="transmission-confidentiality-and-integrity"></a>Poufność transmisji i integralność

**SC-8** system informacji chroni [wybór (jeden lub więcej): poufności; integralność] przekazywanych informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | SI 8 implementacji (1) spełnia tego wymagania formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-8-1"></a>NIST 800 53 kontroli SC-8 (1)

#### <a name="transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Poufność transmisji i integralności | Kryptograficznych lub alternatywnym ochrony fizycznej

**SC-8 (1)** system informacji implementuje mechanizmów kryptograficznych [wybór (jeden lub więcej): uniemożliwić nieuprawnionym ujawnieniem informacji; wykrywa zmiany danych] podczas przesyłania, chyba że w przeciwnym razie chronione przez [przypisania: zdefiniowany przez organizację alternatywnych fizycznych zabezpieczenia].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan konfiguruje zasoby do komunikowania się przy użyciu protokołów tylko na zabezpieczone. Składnik zapory aplikacji sieci Web, bramy aplikacji został skonfigurowany do akceptowania communicators z zastosowań zewnętrznych za pośrednictwem protokołu HTTPS/TLS i komunikować się z puli zaplecza tylko za pośrednictwem protokołu HTTPS/TLS. SQL Server jest skonfigurowany do komunikacji tylko za pośrednictwem protokołu HTTPS/TLS. Usługi pulpitu zdalnego są skonfigurowane do używania bezpiecznych połączeń. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-10"></a>NIST 800 53 kontroli SC-10

#### <a name="network-disconnect"></a>Odłącz sieci

**SC 10** systemu informacji kończy połączenie sieciowe skojarzone z sesji komunikacji z końcem sesji lub po [przypisania: organizacji zdefiniowane przez okres] czas braku aktywności.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Uwierzytelnianie dla sesji pulpitu zdalnego jest zarządzane przez usługę Active Directory. Po wyłączeniu dostępu dla użytkownika w usłudze Active Directory kończą się natychmiast sesje zdalne. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-12"></a>NIST 800 53 kontroli SC-12

#### <a name="cryptographic-key-establishment-and-management"></a>Utworzenie kluczy kryptograficznych i zarządzanie

**SC-12** organizacji ustanawia i zarządza nimi klucze kryptograficzne wymagane kryptografii stosowanych w ramach systemu informacji zgodnie z [przypisania: zdefiniowane przez organizację wymagania dotyczące generowania kluczy, dystrybucji, Magazyn, dostępu i zniszczenie].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Ten plan wdrażania usługi Azure Key Vault. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Usługa Azure Key Vault może wygenerować klucze za pomocą FIPS 140-2 poziom 2 możliwości sprzętowe w zabezpieczeń (HSM) moduł generowania kluczy. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-12-1"></a>NIST 800 53 kontroli SC-12 (1)

#### <a name="cryptographic-key-establishment-and-management--availability"></a>Utworzenie kluczy kryptograficznych i zarządzanie | Dostępność

**SC-12 (1)** organizacji przechowuje dostępności informacji w przypadku utraty kluczy kryptograficznych przez użytkowników.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Usługa Azure Key Vault jest używany do przechowywania kluczy kryptograficznych i kluczy tajnych używanych w tym planu. Key Vault usprawnia proces zarządzania kluczami klucze dostępu i szyfrowanie danych. Następujące wystawców uwierzytelnienia są przechowywane w magazynie kluczy: Azure hasło dla konta Wdróż, hasło administratora maszyny wirtualnej, hasło konta usługi programu SQL Server. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-12-2"></a>NIST 800 53 kontroli SC-12 (2)

#### <a name="cryptographic-key-establishment-and-management--symmetric-keys"></a>Utworzenie kluczy kryptograficznych i zarządzanie | Klucze symetryczne

**SC-12 (2)** organizacji tworzy formanty i dystrybuuje kryptograficzne klucze symetryczne, za pomocą [zaznaczenia: NIST zgodne ze standardem FIPS; Technologia zarządzania kluczami zatwierdzone NSA] i procesów.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Usługa Azure Key Vault jest używany do produkcji, kontrolowania i dystrybucji kluczy kryptograficznych. Usługa Azure Key Vault może wygenerować klucze za pomocą FIPS 140-2 poziom 2 możliwości sprzętowe w zabezpieczeń (HSM) moduł generowania kluczy. Klucze są przechowywane i zarządzane w ramach bezpiecznego zaszyfrowanego kontenery w ramach usługi Azure Key Vault. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-12-3"></a>NIST 800 53 kontroli SC-12 (3)

#### <a name="cryptographic-key-establishment-and-management--asymmetric-keys"></a>Utworzenie kluczy kryptograficznych i zarządzanie | Klucze asymetryczne

**SC-12 (3)** organizacji tworzy formanty i dystrybuuje kryptograficzne klucze asymetryczne przy użyciu [zaznaczenia: technologii zatwierdzone NSA zarządzania kluczami i procesów; zatwierdzone certyfikaty infrastruktury kluczy publicznych klasy 3 lub przewidzianego materiał; zatwierdzone certyfikaty infrastruktury kluczy publicznych klasy 3 lub 4 klasy i sprzętu tokeny zabezpieczające, które zapewniają ochronę klucza prywatnego użytkownika].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za tworzenie, kontrolowanie i dystrybucja kryptograficzne klucze asymetryczne (jeśli są one używane w ramach zasobów wdrożonych przez klienta). |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-13"></a>NIST 800 53 kontroli SC-13

#### <a name="cryptographic-protection"></a>Ochrona usług kryptograficznych

**SC-13** system informacji implementuje [przypisania: zdefiniowany organizacji używa kryptograficznych i typ kryptografii wymagane dla każdego zastosowania] zgodnie z dyrektywy obowiązujących przepisów federalne zamówień wykonawczego, zasady, przepisy, i standardów.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Uwierzytelnianie systemu Windows, pulpitu zdalnego i funkcji BitLocker są zatrudnieni przez ten plan. Te składniki można skonfigurować polegać na FIPS 140 sprawdzania poprawności modułów kryptograficznych. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-15a"></a>NIST 800 53 kontroli SC-15.a

#### <a name="collaborative-computing-devices"></a>Wspólne urządzenia komputerowe

**SC 15.a** informacji zabronione aktywacji zdalnej współpracy urządzeń komputerowych z następującymi wyjątkami: [przypisania: wyjątki zdefiniowane przez organizację zdalnej aktywacji w przypadku mogą być].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak współpracy urządzeń komputerowych wdrożenia w ramach tego planu. Uwaga: Dostępne są fizycznych urządzeń komputerowych współpracy w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-15b"></a>NIST 800 53 kontroli SC-15.b

#### <a name="collaborative-computing-devices"></a>Wspólne urządzenia komputerowe

**SC 15.b** systemu informacji udostępnia wskazanie jawne użycie fizycznej obecności na urządzeniach użytkowników.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Brak współpracy urządzeń komputerowych wdrożenia w ramach tego planu. Uwaga: Dostępne są fizycznych urządzeń komputerowych współpracy w zakresie systemów wdrożonego na platformie Azure. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-17"></a>NIST 800 53 kontroli SC-17

#### <a name="public-key-infrastructure-certificates"></a>Certyfikaty infrastruktury kluczy publicznych

**SC-17** organizacji wystawia certyfikaty klucze publiczne w obszarze [przypisania: zasady dotyczące certyfikatów zdefiniowanych przez organizację] lub uzyskuje dostęp do kluczy publicznych z dostawcą usług zatwierdzone.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na poziomie przedsiębiorstwa infrastruktury kluczy publicznych do wystawiania certyfikatów. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-18a"></a>NIST 800 53 kontroli SC-18.a

#### <a name="mobile-code"></a>Dostępu mobilnego

**SC 18.a** organizacji definiuje dopuszczalne, a nie do przyjęcia dostępu mobilnego i technologii dostępu mobilnego.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | System przedsiębiorstw klienta i procedur ochrony komunikacyjnych mogą określić dopuszczalne, a nie do przyjęcia dostępu mobilnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-18b"></a>NIST 800 53 kontroli SC-18.b

#### <a name="mobile-code"></a>Dostępu mobilnego

**SC 18.b** organizacji ustanawia ograniczenia użycia i wskazówki dotyczące implementacji dopuszczalne dostępu mobilnego i technologii dostępu mobilnego.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy przedsiębiorstw systemu i procedur ochrony komunikacji może utworzyć ograniczenia dotyczące stosowania dostępu mobilnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-18c"></a>NIST 800 53 kontroli SC-18.c

#### <a name="mobile-code"></a>Dostępu mobilnego

**SC 18.c** organizacji autoryzuje, monitoruje i steruje użyciem dostępu mobilnego w ramach systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Odbiorcy mogą polegać na poziomie przedsiębiorstwa proces autoryzacji, monitorowania i kontroli wykorzystania dostępu mobilnego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-19a"></a>NIST 800 53 kontroli SC-19.a

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC 19.a** organizacji ustanawia ograniczenia użycia i wskazówki dotyczące implementacji na głos przez Internet Protocol (VoIP) technologie oparte na może spowodować uszkodzenie systemu informacji użycie złośliwy.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych głosowych przez internet protocol technologie wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-19b"></a>NIST 800 53 kontroli SC-19.b

#### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**SC 19.b** organizacji autoryzuje, monitoruje i steruje użyciem VoIP w ramach systemu informacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Nie ma żadnych głosowych przez internet protocol technologie wdrożenia w ramach tego planu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-20a"></a>NIST 800 53 kontroli SC-20.a

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Nazwa bezpiecznego / adresu usługi rozpoznawania (wiarygodne źródło)

**SC 20.a** system informacji udostępnia dodatkowe dane ze źródła — wersja uwierzytelniania i integralności artefakty weryfikacji wraz z danymi rozpoznawania nazwy autorytatywne system zwraca w odpowiedzi na zapytania Rozpoznawanie zewnętrznych nazwę lub adres.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za bezpieczne usługi rozpoznawania nazwy i adresu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-20b"></a>NIST 800 53 kontroli SC-20.b

#### <a name="secure-name--address-resolution-service-authoritative-source"></a>Nazwa bezpiecznego / adresu usługi rozpoznawania (wiarygodne źródło)

**SC 20.b** systemu informacji w celu dostarczania środków wskazują stan zabezpieczeń stref podrzędnych i (Jeśli obiekt podrzędny obsługuje usługi rozpoznawania bezpieczny) umożliwiające weryfikacji łańcuch zaufania między domenami nadrzędnych i podrzędnych, gdy działają w ramach rozproszone, hierarchicznej przestrzeni nazw.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za bezpieczne usługi rozpoznawania nazwy i adresu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-21"></a>NIST 800 53 kontroli SC-21

#### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>Nazwa bezpiecznego / adresu usługi rozpoznawania (rekursywny lub buforowania programu rozpoznawania nazw)

**SC-21** systemu informacje żądania i przeprowadza uwierzytelnianie pochodzenia danych oraz Weryfikacja integralności danych na nazwę lub adres odpowiedzi rozwiązania system odbiera z autorytatywnych źródeł.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za konfigurowanie zasobów wdrożonych przez klienta do żądania i wykonywać uwierzytelnianie pochodzenia danych oraz Weryfikacja integralności danych na nazwę lub adres rozpoznawania odpowiedzi z autorytatywnego źródeł. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-22"></a>NIST 800 53 kontroli SC-22

#### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>Architektura i obsługa administracyjna dla nazwy / usługa rozpoznawania adresów

**SC-22** systemów informacyjnych, zbiorczo zapewniające usługi rozpoznawania nazwę lub adres organizacji są odpornej na uszkodzenia i wdrożenie separacji ról wewnętrznej/zewnętrznej.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za zapewnienie, że systemy udostępnienie adresu usługi rozpoznawania wdrożonych przez klienta zasobów są odpornej na uszkodzenia i implementować separacji ról wewnętrznej/zewnętrznej. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-23"></a>NIST 800 53 kontroli SC-23

#### <a name="session-authenticity"></a>Autentyczności sesji

**SC-23** system informacji chroni autentyczności sesje komunikacji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Dostęp zdalny do zasobów wykorzystywanych przez ten schemat, w tym portalu Azure, połączeń usług pulpitu zdalnego i brama aplikacji w sieci web, są chronione przy użyciu protokołu TLS. TLS zapewnia autentyczności komunikacji na poziomie sesji. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-23-1"></a>NIST 800 53 kontroli SC-23 (1)

#### <a name="session-authenticity--invalidate-session-identifiers-at-logout"></a>Sesja autentyczności | Unieważnienie identyfikatory sesji na wylogowania

**SC-23 (1)** systemu informacji unieważnia identyfikatory sesji wylogowania użytkownika lub zakończenia sesji.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Dostęp zdalny do zasobów wykorzystywanych przez ten schemat, w tym portalu Azure, połączeń usług pulpitu zdalnego i brama aplikacji w sieci web, są chronione przy użyciu protokołu TLS. Azure portal i sesji pulpitu zdalnego unieważnienie identyfikatory sesji podczas wylogowania. Unieważnienie sesji sieci Web jest wymuszana przez bramę aplikacji Azure — zasady zapory aplikacji sieci Web (WAF). Zapory aplikacji sieci Web stosuje koligacji pliku cookie sesji i wykonuje limit czasu sesji po 30 minutach (można skonfigurować po wdrożeniu szczegółowych zasad organizacji) aktywności klienta. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-24"></a>NIST 800 53 kontroli SC-24

#### <a name="fail-in-known-state"></a>Niepowodzenie w stanie znane

**SC-24** systemu informacji nie powiedzie się [przypisania: stan znane zdefiniowany przez organizację] dla [przypisania: typy danych zdefiniowane przez organizację niepowodzeń] zachowania [przypisania: informacje o stanie systemu zdefiniowany przez organizację] uszkodzenie.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Klient jest odpowiedzialny za egzekwowanie niepowodzenie zasobów wdrożonych przez klienta w stanie znane. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-28"></a>NIST 800 53 kontroli SC-28

#### <a name="protection-of-information-at-rest"></a>Ochrona informacji przechowywanych

**SC-28** system informacji chroni [wybór (jeden lub więcej): poufności; integralność] z [przypisania: zdefiniowane przez organizację informacje w stanie spoczynku].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | SC-28 implementacji (1) spełnia wymagania tego formantu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ### <a name="nist-800-53-control-sc-28-1"></a>NIST 800 53 kontroli SC-28 (1)

#### <a name="protection-of-information-at-rest--cryptographic-protection"></a>Ochrona informacji przechowywanych | Ochrona usług kryptograficznych

**SC-28 (1)** system informacji implementuje mechanizmów kryptograficznych, aby uniknąć nieuprawnionym ujawnieniem i modyfikacji [przypisania: zdefiniowane przez organizację informacje] na [przypisania: informacje zdefiniowane przez organizację systemu składniki].

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan wdrożenia szyfrowania dysku do ochrony poufności i integralności danych w stanie spoczynku. Szyfrowanie dysków Azure dla systemu Windows jest implementowane za pomocą funkcji BitLocker systemu Windows. Program SQL Server jest skonfigurowany do używania przezroczysty danych szyfrowania (funkcji TDE), który przeprowadza w czasie rzeczywistym szyfrowania i odszyfrowywania danych i plików dziennika do ochrony informacji w stanie spoczynku. Funkcji TDE zawiera gwarantują, że przechowywanych danych nie zostało poddane nieautoryzowanego dostępu. Klient może zdecydować się na wdrożenie dodatkowych funkcji kontroli poziomu aplikacji do ochrony integralności przechowywanych informacji. Poufność i integralność wszystkich magazynu obiektów blob wdrażane przez ten plan są chronione przy użyciu szyfrowania usługi Azure magazynu (SSE). SSE zabezpiecza dane przechowywane w ramach konta magazynu platformy Azure przy użyciu szyfrowania AES 256-bitowego. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |


 ## <a name="nist-800-53-control-sc-39"></a>NIST 800 53 kontroli SC-39

#### <a name="process-isolation"></a>Izolacja procesu

**SC 39** system informacji obsługuje domeny wykonywania osobne dla każdego procesu wykonywania.

**Obowiązki:** `Customer Only`

|||
|---|---|
| **Klienta** | Maszyn wirtualnych wdrożonych przez ten plan uruchamiania systemów operacyjnych Windows. System Windows zachowuje wykonywania oddzielnych domen dla każdego procesu wykonywanego przez przypisanie prywatnego wirtualnej przestrzeni adresowej do każdego procesu. |
| **Dostawca (Microsoft Azure)** | Nie dotyczy |
