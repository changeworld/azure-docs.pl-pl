---
title: Zagadnienia dotyczące zabezpieczeń dla ruchu danych w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zabezpieczania przenoszenia danych z fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 2ca39490174047d83968561da98409ade2832253
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752619"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Fabryka danych Azure — zagadnienia dotyczące zabezpieczeń dla przepływu danych

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [zagadnienia dotyczące zabezpieczeń przepływu danych dla fabryki danych w wersji 2](../data-movement-security-considerations.md).

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano infrastrukturę podstawowych zabezpieczeń, która umożliwia Zabezpieczanie danych usługi przenoszenia danych z fabryki danych Azure. Zasoby dotyczące zarządzania fabryki danych Azure są tworzone w infrastrukturze zabezpieczeń platformy Azure i użyj wszystkich możliwych zabezpieczenia oferowanych na platformie Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](data-factory-create-pipelines.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Potoki te znajdują się w regionie, w której został utworzony z fabryką danych. 

Mimo że fabryki danych jest dostępna tylko w **zachodnie stany USA**, **wschodnie stany USA**, i **Europa Północna, Europa** regionach, usługa przenoszenia danych jest dostępna [globalnie w kilka obszarów](data-factory-data-movement-activities.md#global). Usługi fabryka danych zapewnia, że dane nie opuści obszaru geograficznego / regionu, chyba że jawnie poinstruować usługi do używania alternatywnej region, jeśli usługa przenoszenia danych nie zostało jeszcze wdrożone do tego regionu. 

Fabryka danych Azure, sam nie przechowuje żadnych danych, z wyjątkiem poświadczeń połączonej usługi dla magazynów danych chmury, które są szyfrowane za pomocą certyfikatów. Usługa umożliwia tworzenie opartych na danych przepływów pracy do aranżowania przenoszenia danych między [obsługiwanymi magazynami danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) oraz przetwarzania danych przy użyciu [usług obliczeniowych](data-factory-compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Umożliwia także [monitorowanie przepływów pracy i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) przy użyciu zarówno mechanizmów programowych, jaki i interfejsu użytkownika.

Przenoszenie danych przy użyciu fabryki danych Azure została **certyfikowane** dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Jeśli interesuje Cię zgodności platformy Azure i jak Azure zabezpiecza własnej infrastruktury, odwiedź stronę [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

W tym artykule firma Microsoft analizuje zagadnienia dotyczące zabezpieczeń w następujących scenariuszach przepływu danych dwóch: 

- **Scenariusz chmury**— w tym scenariuszu źródłowym i docelowym są publicznie dostępny za pośrednictwem Internetu. Obejmują one usługi magazynu w chmurze zarządzanych, takich jak usługi Azure Storage, Magazyn danych SQL Azure, baza danych SQL Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, usługi SaaS, takie jak Salesforce i protokoły sieci web, takich jak FTP i OData. Pełną listę obsługiwanych źródeł danych można znaleźć [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scenariusza hybrydowego**— w tym scenariuszu źródłowe lub docelowe znajduje się za zaporą lub wewnątrz sieci firmowej lokalnymi lub dane magazynu jest w sieci prywatnej / wirtualnych sieci (w większości przypadków źródła) i nie jest dostępny publicznie. W tym scenariuszu dzielą się również hostowanych na maszynach wirtualnych serwerów bazy danych.

## <a name="cloud-scenarios"></a>Scenariusze chmury
### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych
Fabryka danych Azure chroni poświadczeń magazynu danych przez **szyfrowania** je za pomocą **certyfikaty zarządzany przez firmę Microsoft**. Te certyfikaty są obracane co **dwuletni** (w tym migracja poświadczeń i odnawiania certyfikatów). Te zaszyfrowane poświadczenia są bezpiecznie przechowywane w **magazyn Azure zarządzanych przez usługi zarządzania fabryki danych Azure**. Aby uzyskać więcej informacji na temat zabezpieczeń usługi Azure Storage można znaleźć [Omówienie zabezpieczeń magazynu Azure](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
W przypadku magazynu danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie dane transferu między usługi przenoszenia danych z fabryki danych i magazynu danych w chmurze są za pośrednictwem bezpiecznego kanału HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia **bazy danych SQL Azure** i **magazyn danych SQL Azure** zawsze wymagają szyfrowania (SSL/TLS), podczas gdy dane są przesyłane, do i z bazy danych. Podczas tworzenia potoku za pomocą edytora JSON, Dodaj **szyfrowania** właściwości i wartości **true** w **ciąg połączenia**. Jeśli używasz [kreatora kopiowania](data-factory-azure-copy-wizard.md), Kreator ustawia tę właściwość domyślnie. Aby uzyskać **usługi Azure Storage**, można użyć **HTTPS** w parametrach połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre dane są przechowywane Obsługa szyfrowania danych magazynowanych. Zalecamy, aby włączyć mechanizm szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczysty danych szyfrowania (funkcji TDE) w usłudze Azure SQL Data Warehouse pomaga w ochronie przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowania i odszyfrowywania danych przechowywanych. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Baza danych SQL Azure obsługuje również przezroczystego szyfrowania danych (funkcji TDE), która pomaga w ochronie przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowania i odszyfrowywania danych bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [przezroczystego szyfrowania danych z bazy danych SQL Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Usługa Azure Data Lake store zapewnia również szyfrowanie danych przechowywanych w ramach konta. Po włączeniu usługi Data Lake store szyfruje dane przed wprowadzeniem trwałych i automatycznie odszyfrowuje przed pobierania, ustawiania przezroczystości do uzyskiwania dostępu do danych klienta. Aby uzyskać więcej informacji, zobacz [zabezpieczeń w usłudze Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Magazyn obiektów Blob platformy Azure i magazynu tabel platformy Azure
Usługa Azure storage magazynu obiektów Blob i tabel Azure obsługuje magazynu usługi szyfrowania (SSE), który szyfruje dane przed wprowadzeniem trwałych do magazynu i automatycznie odszyfrowuje przed pobierania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje szyfrowanie danych przechowywanych klienta i serwera. Aby uzyskać więcej informacji, zobacz [ochrona danych przy użyciu szyfrowania](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Fabryki danych nie obsługuje obecnie, Amazon S3 wewnątrz wirtualnych chmury prywatnej (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift obsługuje szyfrowanie klastra dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [szyfrowania bazy danych Redshift Amazon](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Fabryki danych nie obsługuje obecnie, Amazon Redshift wewnątrz VPC. 

#### <a name="salesforce"></a>SalesForce
SalesForce obsługuje szyfrowanie platformy osłony, który umożliwia szyfrowanie wszystkich plików, załączników i pola niestandardowe. Aby uzyskać więcej informacji, zobacz [opis przepływ uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Scenariuszy hybrydowych (przy użyciu bramy zarządzania danymi)
Scenariusze hybrydowe wymagają bramy zarządzania danymi do zainstalowania w sieci lokalnej lub w sieci wirtualnej (Azure) lub prywatnej chmury wirtualne (Amazon). Bramy muszą mieć możliwość dostępu w magazynie danych lokalnych. Aby uzyskać więcej informacji dotyczących bramy, zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md). 

![Kanały zarządzania bramy danych](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanał poleceń** umożliwia komunikację między usługi przenoszenia danych z fabryki danych i brama zarządzania danymi. Komunikat zawiera informacje związane z działaniem. Kanał danych jest używany do przenoszenia danych między lokalnych magazynów danych i magazyny danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Dane lokalne przechowywanie poświadczeń
Poświadczenia dla sieci lokalnych magazynów danych są przechowywane lokalnie (nie w chmurze). Można ich ustawiać na trzy różne sposoby. 

- Przy użyciu **zwykłego tekstu** (mniej bezpieczna opcja) za pośrednictwem protokołu HTTPS z portalu Azure / Kreator kopiowania. Poświadczenia są przekazywane w postaci zwykłego tekstu do bramy lokalnej.
- Przy użyciu **biblioteki JavaScript kryptografii z Kreatora kopiowania**.
- Przy użyciu **kliknij — gdy na podstawie aplikacji Menedżera poświadczeń**. Następnie kliknij pozycję-po wykonuje aplikacji na maszynie lokalnej, który ma dostęp do bramy i ustawia poświadczenia dla magazynu danych. Ta opcja i kolejnego są najbardziej bezpieczne opcje. Aplikacja Menedżera poświadczeń, domyślnie używa portu 8050 na komputerze z bramą na potrzeby bezpiecznej komunikacji.  
- Użyj [AzureRmDataFactoryEncryptValue nowy](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) polecenia cmdlet programu PowerShell do szyfrowania poświadczeń. Polecenie cmdlet używa certyfikatu, że brama jest skonfigurowana na potrzeby szyfrowania poświadczeń. Można użyć zaszyfrowane poświadczenia zwróconych przez to polecenie cmdlet i dodaj go do **EncryptedCredential** elementu **connectionString** w pliku JSON, który jest używany z [ Nowy AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) polecenia cmdlet lub we fragmencie JSON w Edytor fabryki danych w portalu. Tę opcję, a następnie kliknij — po zatwierdzeniu najbardziej bezpieczne Opcje aplikacji. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Szyfrowanie oparte na bibliotece kryptografii JavaScript
Można szyfrować poświadczeń magazynu danych przy użyciu [biblioteki JavaScript kryptografii](https://www.microsoft.com/download/details.aspx?id=52439) z [kreatora kopiowania](data-factory-copy-wizard.md). Po wybraniu tej opcji, Kreator kopiowania pobiera publiczny klucz bramy i używa go do zaszyfrowania poświadczeń magazynu danych. Poświadczenia są odszyfrowywane przez bramę i chroniony przez system Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Obsługiwane przeglądarki:** programu IE 8, IE9, IE10, IE11, Microsoft Edge i przeglądarki Firefox najnowszej, Chrome, Opera, przeglądarki Safari. 

#### <a name="click-once-credentials-manager-app"></a>Kliknij przycisk — raz aplikacji Menedżera poświadczeń
Możesz uruchomić kliknięcie — po na podstawie aplikacji Menedżera poświadczeń z portalu Azure/kopii kreatora podczas tworzenia potoków. Ta aplikacja gwarantuje, że poświadczenia nie są przekazywane w postaci zwykłego tekstu przez sieć. Domyślnie używa portu **8050** na komputerze z bramą na potrzeby bezpiecznej komunikacji. W razie potrzeby można zmienić tego portu.  
  
![Port HTTPS dla bramy](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Obecnie brama zarządzania danymi używa pojedynczego **certyfikatu**. Ten certyfikat został utworzony podczas instalacji bramy (dotyczy utworzone po listopada 2016 brama zarządzania danymi i wersji 2.4.xxxx.x lub nowszej). Ten certyfikat może zastąpić certyfikat SSL/TLS. Ten certyfikat jest używany przez kliknięcie — raz poświadczeń aplikacji Menedżera nawiązania bezpiecznego połączenia z maszyną bramy do ustawiania poświadczeń magazynu danych. Przechowuje dane poświadczeń magazynu bezpiecznie lokalnymi przy użyciu systemu Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na komputerze z bramą. 

> [!NOTE]
> Starsze bram, które zostały zainstalowane przed listopada 2016 lub 2.3.xxxx.x wersji w dalszym ciągu korzystać z poświadczeń szyfrowane i przechowywane w chmurze. Nawet w przypadku uaktualniania bramy do najnowszej wersji, poświadczenia nie są migrowane do komputera lokalnego    
  
| Wersja bramy (podczas tworzenia) | Poświadczenia zapisane | Poświadczeń szyfrowania / zabezpieczeń | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | W chmurze | Zaszyfrowane za pomocą certyfikatu (inny niż używany przez aplikację Menedżer poświadczeń) | 
| > = 2.4.xxxx.x | Lokalnie | Zabezpieczone za pomocą DPAPI | 
  

### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Są wszystkie operacje transferu danych za pośrednictwem bezpiecznego kanału **HTTPS** i **TLS za pośrednictwem protokołu TCP** przed atakami man-in--middle podczas komunikowania się z usługami Azure.
 
Można również użyć [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [Express Route](../../expressroute/expressroute-introduction.md) dodatkowo zabezpieczyć kanał komunikacyjny między siecią lokalną a Azure.

Sieć wirtualna jest logiczną reprezentacja sieci w chmurze. Można połączyć sieć lokalną do sieci wirtualnej platformy Azure (VNet) przy konfigurowania sieci VPN IPSec (site-to-site) lub usługi Express Route (prywatnej komunikacji równorzędnej)     

Poniższa tabela zawiera podsumowanie konfiguracji zalecanej sieciowe i bramy różnych kombinacji lokalizacje źródłowa i docelowa dla hybrydowych przenoszenia danych.

| Element źródłowy | Element docelowy | Konfiguracja sieci | Instalator bramy |
| ------ | ----------- | --------------------- | ------------- | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożony w sieci wirtualnych | IPSec VPN (punkt lokacja lub lokacja lokacja) | Brama może być zainstalowana lokalnie lub na platformie Azure wirtualnej maszyny (VM) w sieci wirtualnej | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożony w sieci wirtualnych | ExpressRoute (prywatnej komunikacji równorzędnej) | Brama może być zainstalowana lokalnie lub na maszynie Wirtualnej platformy Azure w sieci wirtualnej | 
| Lokalnie | Usług Azure, których publiczny punkt końcowy | ExpressRoute (publicznej komunikacji równorzędnej) | Brama musi być zainstalowany na lokalnym | 

Na poniższych ilustracjach przedstawiono użycie bramy zarządzania danymi do przenoszenia danych między lokalną bazą danych i usług Azure przy użyciu expressroute i sieci VPN IPSec (z sieci wirtualnej):

**Usługi Express Route:**
 
![Użyj bramy Expressroute](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**Protokół IPSec sieci VPN:**

![Sieć VPN IPSec z bramy](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfiguracje zapór i listę dozwolonych podobnej adres IP bramy

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory dla sieci o lokalnym/prywatnym  
W przedsiębiorstwie **firmowej zapory** działa na routerze centralnej w organizacji. A **zapory systemu Windows** działa jako demon na komputerze lokalnym, na którym zainstalowano bramę. 

W poniższej tabeli przedstawiono **port wyjściowy** i wymagania dotyczące domeny dla **firmowej zapory**.

| Nazwy domen | Porty wyjściowe | Opis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Wymagane przez bramę do nawiązania połączenia usługi przenoszenia danych z fabryki danych |
| `*.core.windows.net` | 443 | Używane przez bramę do połączenia z konta magazynu Azure, korzystając z [przemieszczane kopiowania](data-factory-copy-activity-performance.md#staged-copy) funkcji. | 
| `*.frontend.clouddatahub.net` | 443 | Wymagane przez bramę do nawiązania połączenia usługi fabryka danych Azure. | 
| `*.database.windows.net` | 1433   | (OPCJONALNIE) wymagane, jeśli folderem docelowym jest baza danych Azure SQL / Azure SQL Data Warehouse. Funkcja kopiowania przemieszczanego skopiować dane do usługi Azure SQL bazy danych/usługi Azure SQL Data Warehouse, bez konieczności otwierania portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCJONALNIE) potrzebna do folderu docelowego jest Azure Data Lake store | 

> [!NOTE] 
> Może być konieczne zarządzanie portami / listę dozwolonych podobnej domen w firmowej zapory na poziomie zgodnie z wymaganiami źródeł odpowiednich danych. Ta tabela używa tylko bazy danych SQL Azure, Magazyn danych SQL Azure, Azure Data Lake Store jako przykłady.   

W poniższej tabeli przedstawiono **port wejściowy** wymagania dotyczące **zapory systemu windows**.

| Porty wejściowe | Opis | 
| ------------- | ----------- | 
| 8050 (TCP) | Wymagane przez aplikację Menedżera poświadczeń w celu bezpiecznego ustawienia poświadczeń dla lokalnych magazynów danych w bramie. | 

![Wymagania dotyczące portów bramy](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfiguracje adresów IP / listę dozwolonych podobnej danych magazynu
Niektóre sklepy danych w chmurze również wymagać niedozwolonych adres IP komputera, dostępu do nich. Upewnij się, że adres IP maszyna bramy jest białej / odpowiednio skonfigurowane w zaporze.

Następujące magazyny danych chmury wymagają niedozwolonych adres IP komputera bramy. Niektóre z tych magazynów danych, domyślnie nie mogą wymagać niedozwolonych adresów IP. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** można udostępniać bramy dla fabryki danych?
**Odpowiedź:** nie obsługujemy tej funkcji jeszcze. Aktywnie pracujemy nad go.

**Pytanie:** jakie są wymagania dotyczące portów dla bramy do pracy?
**Odpowiedź:** bramy umożliwia nawiązanie połączenia oparte na protokole HTTP można otworzyć internet. **Wychodzącego porty 443 i 80** muszą być otwarte dla bramy to połączenie. Otwórz **ruchu przychodzącego dla portu 8050** tylko na poziomie komputera (nie na poziomie firmowej zapory) dla aplikacji Menedżer poświadczeń. Jeśli baza danych SQL Azure lub usługi Azure SQL Data Warehouse jest używany jako źródło / docelowego, następnie trzeba otworzyć **1433** również port. Aby uzyskać więcej informacji, zobacz [konfiguracji i adresy IP listę dozwolonych podobnej zapory](#firewall-configurations-and-whitelisting-ip-address-of gateway) sekcji. 

**Pytanie:** jakie są wymagania dotyczące certyfikatów dla bramy?
**Odpowiedź:** bieżącej bramy wymaga certyfikatu, który jest używany przez aplikację Menedżera poświadczeń w bezpieczny sposób ustawiania poświadczeń magazynu danych. Ten certyfikat jest certyfikatu z podpisem własnym utworzone i skonfigurowane przez Instalatora bramy. Można użyć własnych TLS / SSL zamiast tego certyfikatu. Aby uzyskać więcej informacji, zobacz [kliknij — raz poświadczeń aplikacji Menedżera](#click-once-credentials-manager-app) sekcji. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o wydajności działania kopiowania, zobacz [skopiuj wydajności działania i dostrajania przewodnik](data-factory-copy-activity-performance.md).

 
