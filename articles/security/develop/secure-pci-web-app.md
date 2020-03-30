---
title: Bezpieczna aplikacja internetowa dla PCI DSS | Dokumenty firmy Microsoft
description: Ta przykładowa aplikacja implementuje najlepsze rozwiązania w zakresie zabezpieczeń, które poprawiają stan bezpieczeństwa aplikacji i twojej organizacji podczas opracowywania na platformie Azure.
keywords: nie dotyczy
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992616"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Tworzenie bezpiecznej infrastruktury dla aplikacji PCI

## <a name="overview"></a>Omówienie

Ta bezpieczna infrastruktura dla aplikacji Dla przemysłu kart płatniczych (PCI) zawiera wskazówki dotyczące wdrażania platformy payment card industry jako usługi (PaaS) odpowiedniego do zbierania, przechowywania i pobierania danych posiadacza karty. To rozwiązanie automatyzuje wdrażanie i konfigurację zasobów platformy Azure dla wspólnej architektury referencyjnej, demonstrując sposoby, w jakie klienci mogą spełnić określone wymagania dotyczące zabezpieczeń i zgodności oraz służy jako podstawa dla klientów do tworzenia i konfigurowania własnych rozwiązań na platformie Azure. Rozwiązanie implementuje podzbiór wymagań podobnych do standardów bezpieczeństwa danych w branży kart płatniczych (PCI DSS 3.2).

Ten przykład automatycznie wdraża architekturę referencyjną aplikacji sieci Web PaaS z wstępnie skonfigurowaną kontrolą zabezpieczeń, aby pomóc klientom osiągnąć zgodność podobną do wymagań PCI DSS 3.2. Rozwiązanie składa się z szablonów usługi Azure Resource Manager i skryptów programu PowerShell, które prowadzą wdrażanie i konfigurację zasobów.

Należy wykonać kroki opisane w tym artykule sekwencyjnie, aby upewnić się, że składniki aplikacji są poprawnie skonfigurowane. Baza danych, usługa Azure App Service, wystąpienie usługi Azure Key Vault i wystąpienie bramy aplikacji platformy Azure zależą od siebie nawzajem.

Skrypty wdrażania skonfigurować infrastrukturę. Po uruchomieniu skryptów wdrażania, należy wykonać pewne ręcznej konfiguracji w witrynie Azure Portal, aby połączyć składniki i usługi razem.

Ten przykład jest skierowany do doświadczonych deweloperów na platformie Azure, którzy pracują w branży detalicznej i chcą utworzyć aplikację sieci sprzedaży z bezpieczną infrastrukturą platformy Azure.

> [!NOTE]
> Ta architektura ma służyć jako podstawa dla klientów, aby dostosować się do ich określonych wymagań i nie powinny być używane w stanie— jest w środowisku produkcyjnym.

Wdrażanie aplikacji w tym środowisku bez modyfikacji nie jest wystarczające, aby całkowicie spełnić wymagania PCI DSS 3.2. Pamiętaj o następujących kwestiach:

- Ta architektura zapewnia punkt odniesienia, aby pomóc klientom korzystać z platformy Azure w sposób zgodny ze standardem PCI DSS 3.2.
- Klienci są odpowiedzialni za przeprowadzenie odpowiedniej oceny bezpieczeństwa i zgodności każdego rozwiązania zbudowanego przy użyciu tej architektury, ponieważ wymagania mogą się różnić w zależności od specyfiki implementacji każdego klienta.

Podczas opracowywania i wdrażania tej aplikacji, dowiedzieć się, jak:

- Utwórz wystąpienie usługi Azure Key Vault i przechowuj z niego wpisy tajne.
- Wdrażanie usługi Azure Database dla usługi Azure SQL, konfigurowanie bezpiecznych danych i autoryzowanie dostępu do nich.
- Wdrażanie aplikacji sieci web platformy Azure w środowisku usługi App Service, które jest dedykowane izolowane z aEcess zapory frontonalnej.
- Tworzenie i konfigurowanie wystąpienia bramy aplikacji platformy Azure za pomocą zapory korzystającej z zestawu [reguł OWASP Top 10](https://coreruleset.org/).
- Włącz szyfrowanie danych podczas przesyłania i odpoczynku przy użyciu usług platformy Azure.
- Konfigurowanie zasad platformy Azure i niebieskich wydruków w celu oceny zgodności

Po opracowaniu i wdrożeniu tej aplikacji zostanie skonfigurowana następująca przykładowa aplikacja internetowa wraz z opisanymi środkami konfiguracji i zabezpieczeń.

## <a name="architecture-diagram-and-components"></a>Diagram architektury i składniki
Aplikacja jest typową aplikacją n-warstwową z trzema warstwami. Fronton, back end i warstwa bazy danych ze zintegrowanymi komponentami monitorowania i tajnego zarządzania są pokazane w tym miejscu:

![Architektura aplikacji](./media/secure-pci-web-app/architecture.png)

Architektura składa się z następujących składników:

- [Środowisko usługi aplikacji w wersji 2](https://docs.microsoft.com/azure/app-service/environment/intro/). Zapewnia środowisko usługi aplikacji i moduł równoważenia obciążenia dla naszej architektury aplikacji.
- [Brama aplikacji platformy Azure](https://docs.microsoft.com/azure/application-gateway/). Zapewnia bramę i zaporę dla naszej architektury aplikacji.
- [Usługa Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Zapewnia rozszerzalną usługę zarządzania wydajnością aplikacji (APM) na wielu platformach.
- [Usługa Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Przechowuje i szyfruje wpisy tajne naszej aplikacji i zarządza tworzeniem zasad dostępu wokół nich.
- [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Zapewnia chmurową usługę zarządzania tożsamościami i dostępem, logowanie i dostęp do zasobów.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Udostępnia usługę do hostowania domeny.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Skaluje aplikacje i zapewnia wysoką dostępność usług
- [Usługa Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Zapewnia rozwiązanie dla nowoczesnych scenariuszy przechowywania danych.
- [Aplikacja Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Zapewnia usługę opartą na protokoii HTTP do hostowania aplikacji sieci web.
- [Usługa Azure Database dla usługi AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Bezpiecznie przechowuje dane naszej aplikacji.
- [Plany platformy Azure](https://docs.microsoft.com/azure/governance/blueprints/overview/). Zapewnia specyfikacje i zgodność z określonymi normami i wymaganiami.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Zasady platformy Azure](https://docs.microsoft.com/azure/governance/policy/overview). Ocenia zasoby pod kątem niezgodności z przypisanymi zasadami.

## <a name="threat-model"></a>Model zagrożenia
Modelowanie zagrożeń to proces identyfikowania potencjalnych zagrożeń bezpieczeństwa dla firmy i aplikacji, a następnie zapewnienia odpowiedniego planu łagodzenia zmiany klimatu.

W tym przykładzie użyto [narzędzia Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) do zaimplementowania modelowania zagrożeń dla bezpiecznej przykładowej aplikacji. Diagramowanie składników i przepływów danych można zidentyfikować problemy i zagrożenia na wczesnym etapie procesu tworzenia. Pozwala to zaoszczędzić czas i pieniądze później.

Jest to model zagrożenia dla przykładowej aplikacji:

![Model zagrożenia](./media/secure-pci-web-app/threat-model.png)

Niektóre przykładowe zagrożenia i potencjalne luki w zabezpieczeniach generowane przez narzędzie do modelowania zagrożeń są pokazane na poniższym zrzucie ekranu. Model zagrożenia zawiera przegląd powierzchni ataku narażone i monituje deweloperów, aby zastanowić się, jak złagodzić problemy.

![Wyjście modelu zagrożenia](./media/secure-web-app/threat-model-output.png)

Na przykład iniekcji SQL w poprzednim modelu zagrożenia danych wyjściowych jest złagodzone przez odkażanie danych wejściowych użytkownika i przy użyciu przechowywanych funkcji w usłudze Azure Database dla PostgreSQL. To ograniczenie zapobiega arbitralne wykonywanie zapytań podczas odczytów i zapisów danych.

Deweloperzy poprawić ogólne bezpieczeństwo systemu poprzez łagodzenie każdego z zagrożeń w danych wyjściowych modelu zagrożenia.

## <a name="deployment"></a>wdrażania
### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć działania aplikacji, należy zainstalować następujące narzędzia:

- Edytor kodu do modyfikowania i wyświetlania kodu aplikacji. [Visual Studio Code](https://code.visualstudio.com/) jest opcją typu open source.
- [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) na komputerze deweloperskim.
- [Git](https://git-scm.com/) w systemie. Git jest używany do klonowania kodu źródłowego lokalnie.
- [jq](https://stedolan.github.io/jq/), narzędzie unix do wykonywania zapytań JSON w sposób przyjazny dla użytkownika.

Ten przykład składa się z plików konfiguracyjnych JSON i skryptów programu PowerShell obsługiwanych przez usługę interfejsu API usługi Azure Resource Manager w celu wdrożenia zasobów na platformie Azure. Szczegółowe instrukcje wdrażania są dostępne [tutaj](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Szybki start

1.  Sklonuj lub pobierz [to](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) repozytorium GitHub na lokalną stację roboczą.
2.  Przejrzyj 0-Setup-AdministrativeAccountAndPermission.md i uruchom podane polecenia.
3.  Wdrażanie rozwiązania testowego z przykładowymi danymi contoso lub pilotażowe początkowe środowisko produkcyjne.

    Ten skrypt wdraża zasoby platformy Azure do demonstracji sklepu internetowego przy użyciu przykładowych danych contoso.

W tym przykładzie uruchomiono skrypt wdrażania, który wdraża aplikację sieci web w usłudze App Service i tworzy zasoby.

Istnieje wiele sposobów wdrażania aplikacji na platformie Azure, w tym:

- Szablony usługi Azure Resource Manager
- PowerShell
- Interfejs wiersza polecenia platformy Azure
- Portal Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Wytyczne i zalecenia

### <a name="network"></a>Network (Sieć)
Architektura definiuje prywatną sieć wirtualną o przestrzeni adresowej 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci
Sieciowe grupyhttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) zabezpieczeń( zawierają listy kontroli dostępu (ACL), które zezwalają lub odmawiają ruchu w sieci wirtualnej. Sieciowe grupy zabezpieczeń mogą służyć do zabezpieczania ruchu na poziomie podsieci lub poszczególnych maszyn wirtualnych. Istnieją następujące sieciowe grupy zabezpieczeń:

- 1 sieciowa grupa zabezpieczeń bramy aplikacji
- 1 sieciowa grupa zabezpieczeń dla środowiska usługi aplikacji
- 1 sieciowa grupa zabezpieczeń dla usługi Azure SQL Database
- 1 sieciowa grupa zabezpieczeń dla hosta bastionu

Każda z grup zabezpieczeń sieci ma otwarte określone porty i protokoły, dzięki czemu rozwiązanie może działać bezpiecznie i poprawnie. Ponadto dla każdej sieciowej grupy zabezpieczeń są włączone następujące konfiguracje:

- Dzienniki diagnostycznehttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) i zdarzenia( są włączone i przechowywane na koncie magazynu
- Dzienniki usługi Azure Monitor są połączone z diagnostyką sieciowej grupy zabezpieczeń(https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG Config
Konfiguracja NSG dla środowiska usługi app service powinna być skonfigurowana w sposób pokazany na poniższej ilustracji.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Każda podsieć jest skojarzona z odpowiednią sieciową grupą zabezpieczeń.

### <a name="config"></a>Config
Podsieci są skonfigurowane w sposób pokazany na poniższej ilustracji.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>System DNS platformy Azure
System nazw domen (DNS) jest odpowiedzialny za tłumaczenie (lub rozwiązywanie) nazwy witryny sieci Web lub usługi na jej adres IP. [Usługa Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) to usługa hostingowa dla domen DNS, która zapewnia rozpoznawanie nazw przy użyciu infrastruktury platformy Azure. Hostując domeny na platformie Azure, użytkownicy mogą zarządzać rekordami DNS przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, jak inne usługi platformy Azure. Usługa Azure DNS obsługuje również prywatne domeny DNS.

### <a name="protect-data"></a>Ochrona danych
Aby pomóc w ochronie danych w chmurze, należy uwzględnić możliwe stany, w których dane mogą wystąpić i jakie formanty są dostępne dla tego stanu. Najważniejsze wskazówki dotyczące zabezpieczeń i szyfrowania danych platformy Azure odnoszą się do następujących stanów danych:

- W spoczynku: Obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które istnieją statycznie na nośniku fizycznym, czy magnetyczne lub optyczne.
- Podczas przesyłania: gdy dane są przesyłane między składnikami, lokalizacjami lub programami, są przesyłane. Przykładami są transfery za pośrednictwem sieci, przez magistralę usług (z lokalnego do chmury i odwrotnie, w tym połączenia hybrydowe, takie jak ExpressRoute) lub podczas procesu wprowadzania/przesyłania danych.

### <a name="azure-storage"></a>Azure Storage
Aby spełnić wymagania dotyczące zaszyfrowanych danych w spoczynku, usługa [Azure Storage](https://azure.microsoft.com/services/storage/) używa usługi Azure Key Vault do obsługi kontroli nad kluczami, które uzyskują dostęp do danych i szyfrują je. Pomaga to chronić i chronić dane posiadaczy kart w celu wsparcia zobowiązań w zakresie bezpieczeństwa organizacji i wymagań dotyczących zgodności określonych przez PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption
Usługa Azure Disk Encryption wykorzystuje funkcję funkcji BitLocker systemu Windows w celu zapewnienia szyfrowania woluminów dysków danych. Rozwiązanie integruje się z usługą Azure Key Vault, aby ułatwić kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi.

### <a name="azure-sql-database"></a>Azure SQL Database
Wystąpienie usługi Azure SQL Database używa następujących środków zabezpieczeń bazy danych:

- [Uwierzytelnianie i autoryzacja usługi Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) umożliwia zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft w jednej centralnej lokalizacji.
- [Inspekcja bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) śledzi zdarzenia bazy danych i zapisuje je w dzienniku inspekcji na koncie magazynu platformy Azure.
- Usługa Azure SQL Database jest skonfigurowana do [używania przezroczystego szyfrowania danych,](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)które wykonuje szyfrowanie w czasie rzeczywistym i odszyfrowywanie bazy danych, skojarzone kopie zapasowe i pliki dziennika transakcji w celu ochrony informacji w spoczynku. Przezroczyste szyfrowanie danych zapewnia pewność, że przechowywane dane nie zostały poddane nieautoryzowanemu dostępowi.
- [Reguły zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) uniemożliwiają dostęp do serwerów bazy danych do momentu przyznania odpowiednich uprawnień. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.
- [Wykrywanie zagrożeń SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) umożliwia wykrywanie i reagowanie na potencjalne zagrożenia w miarę ich występowania, zapewniając alerty zabezpieczeń dla podejrzanych działań bazy danych, potencjalnych luk w zabezpieczeniach, ataków iniekcji SQL i nietypowych wzorców dostępu do bazy danych.
- [Zaszyfrowane kolumny](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) upewnij się, że poufne dane nigdy nie są wyświetlane jako zwykły tekst wewnątrz systemu bazy danych. Po włączeniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji z dostępem do kluczy mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu.
- [Maskowanie danych dynamicznych bazy danych SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) ogranicza ekspozycję poufnych danych, maskując dane użytkownikom lub aplikacjom nieuprzywilejowanym. Dynamiczne maskowanie danych może automatycznie wykrywać potencjalnie poufne dane i sugerować odpowiednie maski, które mają zostać zastosowane. Pomaga to zidentyfikować i zmniejszyć dostęp do danych w taki sposób, że nie kończy się z bazy danych za pośrednictwem nieautoryzowanego dostępu. Klienci są odpowiedzialni za dostosowanie ustawień dynamicznego maskowania danych w celu dostosowania się do ich schematu bazy danych.

### <a name="identity-management"></a>Zarządzanie tożsamościami
Następujące technologie zapewniają możliwości zarządzania dostępem do danych posiadacza karty w środowisku platformy Azure:

- Usługa Azure Active Directory to usługa zarządzania katalogami i tożsamościami opartymi na wielu dzierżawach firmy Microsoft. Wszyscy użytkownicy tego rozwiązania są tworzone w usłudze Azure Active Directory, w tym użytkowników uzyskujących dostęp do bazy danych SQL Azure.
- Uwierzytelnianie w aplikacji jest wykonywane przy użyciu usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). Ponadto szyfrowanie kolumny bazy danych używa usługi Azure Active Directory do uwierzytelniania aplikacji w bazie danych SQL azure. Aby uzyskać więcej informacji, zobacz [jak chronić poufne dane w usłudze Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Kontrola dostępu oparta na rolach platformy Azure umożliwia administratorom definiowanie uprawnień dostępu szczegółowej, aby udzielić tylko ilości dostępu, której użytkownicy potrzebują do wykonywania swoich zadań. Zamiast udzielać każdemu użytkownikowi nieograniczonych uprawnień do zasobów platformy Azure, administratorzy mogą zezwalać tylko na niektóre akcje dostępu do danych posiadacza karty. Dostęp do subskrypcji jest ograniczony do administratora subskrypcji.
- Zarządzanie tożsamościami uprzywilejowanymi usługi Azure Active Directory umożliwia klientom zminimalizowanie liczby użytkowników, którzy mają dostęp do określonych informacji, takich jak dane posiadacza karty. Administratorzy mogą używać zarządzania tożsamościami uprzywilejowanymi usługi Azure Active Directory do odnajdywanie, ograniczanie i monitorowanie tożsamości uprzywilejowanych i ich dostępu do zasobów. Ta funkcja może również służyć do wymuszania dostępu administracyjnego na żądanie, just-in-time w razie potrzeby.
- Usługa Azure Active Directory Identity Protection wykrywa potencjalne luki w zabezpieczeniach wpływających na tożsamość organizacji, konfiguruje automatyczne odpowiedzi na wykryte podejrzane akcje związane z tożsamościami organizacji i bada podejrzane incydentów w celu podjęcia odpowiednich działań w celu ich rozwiązania.

### <a name="secrets-management"></a>Zarządzanie wpisami tajnymi
Rozwiązanie używa usługi Azure Key Vault do zarządzania kluczami i wpisami tajnymi. Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Następujące funkcje usługi Azure Key Vault pomagają klientom chronić i uzyskiwać do nich dostęp:

- Zaawansowane zasady dostępu są konfigurowane na podstawie potrzeb.
- Zasady dostępu usługi Key Vault są definiowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych.
- Wszystkie klucze i wpisy tajne w magazynie kluczy mają daty wygaśnięcia.
- Wszystkie klucze w ucho. Typ klucza to 2048-bitowy klucz RSA chroniony przez moduł HSM.
- Za pomocą usługi Key Vault można szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, . PFX i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM)
- Użyj rbac, aby przypisać uprawnienia do użytkowników, grup i aplikacji w określonym zakresie.
- Za pomocą usługi Key Vault można zarządzać certyfikatami TLS za pomocą automatycznego odnawiania.
- Dzienniki diagnostyczne dla usługi Key Vault są włączone z okresem przechowywania co najmniej 365 dni.
- Dozwolone operacje kryptograficzne kluczy są ograniczone do wymaganych operacji.

### <a name="azure-security-center"></a>Azure Security Center
Dzięki usłudze Azure Security Center klienci mogą centralnie stosować zasady zabezpieczeń i zarządzać nimi w różnych obciążeniach, ograniczać narażenie na zagrożenia oraz wykrywać ataki i reagować na nie. Ponadto usługa Azure Security Center uzyskuje dostęp do istniejących konfiguracji usług platformy Azure w celu zapewnienia zaleceń dotyczących konfiguracji i usług w celu poprawy stanu bezpieczeństwa i ochrony danych.

Usługa Azure Security Center używa różnych funkcji wykrywania, aby ostrzegać klientów o potencjalnych atakach ukierunkowanych na ich środowiska. Te alerty zawierają cenne informacje dotyczące przyczyny ich wyzwolenia, zasobów, których dotyczy atak, i źródła ataku. Usługa Azure Security Center ma zestaw wstępnie zdefiniowanych alertów zabezpieczeń, które są wyzwalane, gdy zagrożenie lub podejrzana aktywność ma miejsce. Niestandardowe reguły alertów w usłudze Azure Security Center umożliwiają klientom definiowanie nowych alertów zabezpieczeń na podstawie danych, które są już zbierane z ich środowiska.

Usługa Azure Security Center udostępnia priorytetowe alerty zabezpieczeń i zdarzenia, co ułatwia klientom wykrywanie i rozwiązywanie potencjalnych problemów z zabezpieczeniami. Raport analizy zagrożeń jest generowany dla każdego wykrytego zagrożenia, aby pomóc zespołom reagowania na incydenty w badaniu i korygowaniu zagrożeń.

### <a name="azure-application-gateway"></a>Azure Application Gateway
Architektura zmniejsza ryzyko luk w zabezpieczeniach przy użyciu bramy aplikacji platformy Azure z skonfigurowaną zaporą aplikacji sieci web i włączonym zestawem reguł OWASP. Dodatkowe możliwości obejmują:

- Kompleksowy ssl
- Wyłączanie protokołu TLS v1.0 i v1.1
- Włączanie tlsv1.2
- Zapora aplikacji sieci Web (tryb zapobiegania)
- Tryb zapobiegania z regułami OWASP 3.0
- Włączanie rejestrowania diagnostycznego
- Niestandardowe sondy kondycji
- Usługa Azure Security Center i Usługa Azure Advisor zapewniają dodatkową ochronę i powiadomienia. Usługa Azure Security Center zapewnia również system reputacji.

### <a name="logging-and-auditing"></a>Rejestrowanie i przeprowadzanie inspekcji
Usługi platformy Azure szeroko rejestrują działania systemu i użytkownika, a także kondycję systemu:

- [Dzienniki aktywności](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) zapewniają wgląd w operacje wykonywane na zasobach w ramach subskrypcji. Dzienniki aktywności mogą pomóc w określeniu inicjatora operacji, czasu wystąpienia i stanu.
- [Dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) obejmują wszystkie dzienniki emitowane przez każdy zasób. Dzienniki te obejmują dzienniki systemu zdarzeń systemu Windows, dzienniki usługi Azure Storage, dzienniki inspekcji usługi Key Vault oraz dostęp do bramy aplikacji i dzienniki zapory. Wszystkie dzienniki diagnostyczne zapisują się na scentralizowanym i zaszyfrowanym koncie magazynu platformy Azure w celu archiwizacji. Przechowywanie jest konfigurowane przez użytkownika, do 730 dni, aby spełnić wymagania dotyczące przechowywania specyficzne dla organizacji.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Te dzienniki są konsolidowane w [dziennikach usługi Azure Monitor](https://azure.microsoft.com/services/log-analytics/) do przetwarzania, przechowywania i raportowania pulpitu nawigacyjnego. Po zebraniu dane są zorganizowane w osobne tabele dla każdego typu danych w obszarach roboczych usługi Log Analytics, co umożliwia analizowanie wszystkich danych razem niezależnie od ich oryginalnego źródła. Ponadto usługa Azure Security Center integruje się z dziennikami usługi Azure Monitor, umożliwiając klientom korzystanie z zapytań Kusto w celu uzyskania dostępu do danych zdarzeń zabezpieczeń i łączenia ich z danymi z innych usług.

Następujące [rozwiązania monitorowania](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) platformy Azure są uwzględniane jako część tej architektury:

- [Ocena usługi Active Directory:](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)Rozwiązanie do sprawdzania kondycji usługi Active Directory ocenia ryzyko i kondycję środowisk serwerów w regularnych odstępach czasu i zawiera priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Ocena SQL:](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)Rozwiązanie sql health check ocenia ryzyko i kondycję środowisk serwerów w regularnych odstępach czasu i zapewnia klientom priorytetową listę zaleceń specyficznych dla wdrożonej infrastruktury serwera.
- [Kondycja agenta:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)Rozwiązanie kondycji agenta raportuje liczbę wdrożonych agentów i ich rozkład geograficzny, a także liczbę agentów, którzy nie reagują, oraz liczbę agentów przesyłających dane operacyjne.
- [Analiza dzienników aktywności:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)Rozwiązanie analizy dzienników aktywności pomaga w analizie dzienników aktywności platformy Azure we wszystkich subskrypcjach platformy Azure dla klienta.

### <a name="azure-monitor"></a>Azure Monitor
[Usługa Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) pomaga użytkownikom śledzić wydajność, utrzymywać zabezpieczenia i identyfikować trendy, umożliwiając organizacjom inspekcję, tworzenie alertów i archiwizowanie danych, w tym śledzenie wywołań interfejsu API w swoich zasobach platformy Azure.

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) to rozszerzalna usługa zarządzania wydajnością aplikacji dla deweloperów sieci web na wielu platformach. Usługa Application Insights wykrywa anomalie wydajności, a klienci mogą go używać do monitorowania aplikacji sieci web na żywo. Zawiera zaawansowane narzędzia analityczne, które pomagają klientom diagnozować problemy i zrozumieć, co użytkownicy faktycznie robią ze swoją aplikacją. Został zaprojektowany, aby pomóc klientom stale poprawiać wydajność i użyteczność.

### <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Utwórz magazyn dla organizacji, w którym mają być przechowywane klucze, i zachowaj odpowiedzialność za zadania operacyjne, takie jak poniżej:

- Dane przechowywane w magazynie kluczy obejmują:

   - Klucz wglądu w aplikacje
   - Klucz dostępu do magazynu danych
   - Parametry połączenia
   - Nazwa tabeli danych
   - Poświadczenia użytkownika

- Zaawansowane zasady dostępu są konfigurowane na podstawie potrzeb
- Zasady dostępu usługi Key Vault są definiowane z minimalnymi wymaganymi uprawnieniami do kluczy i wpisów tajnych
- Wszystkie klucze i wpisy tajne w magazynie kluczy mają daty wygaśnięcia
- Wszystkie klucze w magazynie kluczy są chronione przez moduł HSM [Typ klucza = 2048-bitowy klucz RSA chroniony hsm]
- Wszyscy użytkownicy/tożsamości otrzymują minimalne wymagane uprawnienia przy użyciu kontroli dostępu opartej na rolach (RBAC)
- Aplikacje nie udostępniają usługi Key Vault, chyba że ufają sobie nawzajem i potrzebują dostępu do tych samych wpisów tajnych w czasie wykonywania
- Dzienniki diagnostyczne dla usługi Key Vault są włączone z okresem przechowywania co najmniej 365 dni.
- Dozwolone operacje kryptograficzne dla kluczy są ograniczone do tych wymaganych

### <a name="vpn-and-expressroute"></a>SIEĆ VPN i usługa ExpressRoute
Bezpieczny tunel sieci VPN lub [usługa ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musi być skonfigurowana w taki sposób, aby bezpiecznie ustanowić połączenie z zasobami wdrożonymi w ramach tej architektury referencyjnej aplikacji sieci Web PaaS. Poprzez odpowiednie skonfigurowanie sieci VPN lub usługi ExpressRoute klienci mogą dodać warstwę ochrony danych podczas przesyłania.

Implementując bezpieczny tunel sieci VPN z platformą Azure, można utworzyć wirtualne połączenie prywatne między siecią lokalną a siecią wirtualną platformy Azure. To połączenie odbywa się przez Internet i umożliwia klientom bezpieczne "tunelowanie" informacji wewnątrz zaszyfrowanego łącza między siecią klienta a platformą Azure. Sieć VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która jest wdrażana przez przedsiębiorstwa każdej wielkości od dziesięcioleci. Tryb tunelu IPsec jest używany w tej opcji jako mechanizm szyfrowania.

Ponieważ ruch w tunelu sieci VPN przechodzi przez Internet za pomocą sieci VPN typu lokacja lokacja, firma Microsoft oferuje inną, jeszcze bezpieczniejszą opcję połączenia. Usługa Azure ExpressRoute to dedykowane łącze sieci WAN między platformą Azure a lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ połączenia usługi ExpressRoute nie przechodzą przez Internet, połączenia te oferują większą niezawodność, szybsze prędkości, mniejsze opóźnienia i wyższe zabezpieczenia niż typowe połączenia przez Internet. Ponadto, ponieważ jest to bezpośrednie połączenie dostawcy usług telekomunikacyjnych klienta, dane nie są przesyłane przez Internet, a zatem nie są na nie narażone.

[Dostępne](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)są najważniejsze wskazówki dotyczące implementowania bezpiecznej sieci hybrydowej, która rozszerza sieć lokalną na platformę Azure.

## <a name="cost-considerations"></a>Kwestie związane z kosztami
Jeśli nie masz jeszcze konta platformy Azure, możesz utworzyć bezpłatne. Przejdź do [strony bezpłatnego konta,](https://azure.microsoft.com/free/) aby rozpocząć, zobacz, co możesz zrobić z bezpłatnym kontem platformy Azure i dowiedz się, które produkty są bezpłatne przez 12 miesięcy.

Aby wdrożyć zasoby w przykładowej aplikacji z funkcjami zabezpieczeń, należy zapłacić za niektóre funkcje premium. Ponieważ aplikacja jest skalowana, a bezpłatne warstwy i wersje próbne oferowane przez platformę Azure muszą zostać uaktualnione w celu spełnienia wymagań aplikacji, koszty mogą wzrosnąć. Kalkulator [cen](https://azure.microsoft.com/pricing/calculator/) platformy Azure służy do szacowania kosztów.

## <a name="next-steps"></a>Następne kroki
Poniższe artykuły mogą pomóc w projektowaniu, opracowywaniu i wdrażaniu bezpiecznych aplikacji.

- [Projekt](secure-design.md)
- [Opracowywanie](secure-develop.md)
- [Wdróż](secure-deploy.md)
