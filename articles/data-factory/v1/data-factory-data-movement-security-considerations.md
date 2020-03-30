---
title: Zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych w usłudze Azure Data Factory
description: Dowiedz się więcej o zabezpieczaniu przenoszenia danych w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 1f19d258531e5368238cba72c986aede3f4a64ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130835"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Usługa Azure Data Factory — zagadnienia dotyczące zabezpieczeń dotyczące przenoszenia danych

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [zagadnienia dotyczące zabezpieczeń przenoszenia danych dla usługi Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano podstawową infrastrukturę zabezpieczeń, której usługi przenoszenia danych w usłudze Azure Data Factory używają do zabezpieczania danych. Zasoby zarządzania usługi Azure Data Factory są oparte na infrastrukturze zabezpieczeń platformy Azure i wykorzystują wszystkie możliwe środki bezpieczeństwa oferowane przez platformę Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](data-factory-create-pipelines.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w którym utworzono fabrykę danych. 

Mimo że usługa Data Factory jest dostępna tylko w regionach **Zachodnie stany USA,** **Wschodnie stany USA**i Europa **Północna,** usługa przenoszenia danych jest dostępna [globalnie w kilku regionach.](data-factory-data-movement-activities.md#global) Usługa Fabryka danych zapewnia, że dane nie pozostawiają obszaru geograficznego/ regionu, chyba że wyraźnie nakazujeszy usłudze użycie alternatywnego regionu, jeśli usługa przenoszenia danych nie została jeszcze wdrożona w tym regionie. 

Usługa Azure Data Factory sama nie przechowuje żadnych danych z wyjątkiem poświadczeń usługi połączonej dla magazynów danych w chmurze, które są szyfrowane przy użyciu certyfikatów. Umożliwia tworzenie przepływów pracy opartych na danych w celu organizowania przepływu danych między [obsługiwanymi magazynami danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) i przetwarzania danych przy użyciu [usług obliczeniowych](data-factory-compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Umożliwia również [monitorowanie przepływów pracy i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) przy użyciu mechanizmów programowych i interfejsu użytkownika.

Przenoszenie danych przy użyciu usługi Azure Data Factory zostało **certyfikowane** dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [GWIAZDA CSA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Jeśli interesuje Cię zgodność z usługą Azure i sposób, w jaki platforma Azure zabezpiecza własną infrastrukturę, odwiedź [Centrum zaufania firmy Microsoft.](https://microsoft.com/en-us/trustcenter/default.aspx) 

W tym artykule przejrzyjmy zagadnienia dotyczące zabezpieczeń w następujących dwóch scenariuszach przenoszenia danych: 

- **Scenariusz chmury**— w tym scenariuszu zarówno źródła, jak i miejsca docelowego są publicznie dostępne za pośrednictwem Internetu. Należą do nich zarządzane usługi magazynu w chmurze, takie jak Usługa Azure Storage, usługa Azure SQL Data Warehouse, usługa Azure SQL Database, Usługa Azure Data Lake Store, Amazon S3, Amazon Redshift, usługi SaaS, takie jak Salesforce, oraz protokoły internetowe, takie jak FTP i OData. Pełną listę obsługiwanych źródeł danych można znaleźć [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scenariusz hybrydowy**— w tym scenariuszu źródło lub miejsce docelowe znajduje się za zaporą lub wewnątrz lokalnej sieci firmowej lub magazyn danych znajduje się w sieci prywatnej/ sieci wirtualnej (najczęściej źródłowej) i nie jest publicznie dostępny. Serwery bazy danych hostowane na maszynach wirtualnych również mieszczą się w tym scenariuszu.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenariusze dotyczące chmury
### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych
Usługa Azure Data Factory chroni poświadczenia magazynu danych, **szyfrując** je przy użyciu **certyfikatów zarządzanych przez firmę Microsoft.** Certyfikaty te są zmieniane co **dwa lata** (co obejmuje odnowienie certyfikatu i migrację poświadczeń). Te zaszyfrowane poświadczenia są bezpiecznie przechowywane w **usłudze Azure Storage zarządzanej przez usługi zarządzania usługami zarządzania usługami Azure Data Factory.** Aby uzyskać więcej informacji na temat zabezpieczeń usługi Azure Storage, zapoznaj się z [omówieniem zabezpieczeń usługi Azure Storage .](../../security/fundamentals/storage-overview.md)

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania danych
Jeśli magazyn danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie transfery danych między usługami przenoszenia danych w fabryce danych a magazynem danych w chmurze są za pośrednictwem bezpiecznego kanału HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia z **usługą Azure SQL Database** i **usługą Azure SQL Data Warehouse** zawsze wymagają szyfrowania (SSL/TLS) podczas przesyłania danych do i z bazy danych. Podczas tworzenia potoku przy użyciu edytora JSON dodaj właściwość **szyfrowania** i ustaw ją na **true** w **ciągu połączenia**. Podczas korzystania z [Kreatora kopiowania](data-factory-azure-copy-wizard.md)kreator domyślnie ustawia tę właściwość. W przypadku **usługi Azure Storage**można użyć protokołu **HTTPS** w ciągu połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre magazyny danych obsługują szyfrowanie danych w spoczynku. Zalecamy włączenie mechanizmu szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczyste szyfrowanie danych (TDE) w usłudze Azure SQL Data Warehouse pomaga chronić przed zagrożeniem złośliwą aktywnością, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych w spoczynku. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w magazynie danych SQL](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Usługa Azure SQL Database obsługuje również przezroczyste szyfrowanie danych (TDE), które pomaga chronić przed zagrożeniem złośliwą aktywnością, wykonując szyfrowanie w czasie rzeczywistym i odszyfrowywanie danych bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Przezroczyste szyfrowanie danych za pomocą usługi Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Usługa Azure Data Lake store zapewnia również szyfrowanie danych przechowywanych na koncie. Po włączeniu usługa Data Lake store automatycznie szyfruje dane przed utrwalaniem i odszyfrowywaniem przed pobraniem, dzięki czemu jest ona przezroczysta dla klienta uzyskującego dostęp do danych. Aby uzyskać więcej informacji, zobacz [Zabezpieczenia w magazynie usługi Azure Data Lake](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Usługa Azure Blob Storage i usługa Azure Table Storage
Usługa Azure Blob Storage i usługa Azure Table Storage obsługuje szyfrowanie usługi storage service (SSE), które automatycznie szyfruje dane przed utrwaleniem do magazynu i odszyfrowuje przed pobraniem. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage Service dla danych w spoczynku](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje zarówno szyfrowanie danych klienta, jak i serwera w rest. Aby uzyskać więcej informacji, zobacz [Ochrona danych przy użyciu szyfrowania](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Obecnie usługa Data Factory nie obsługuje usługi Amazon S3 w wirtualnej chmurze prywatnej (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift obsługuje szyfrowanie klastrów dla danych w spoczynku. Aby uzyskać więcej informacji, zobacz [Amazon Redshift Database Encryption](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Obecnie data factory nie obsługuje Amazon Redshift wewnątrz VPC. 

#### <a name="salesforce"></a>SalesForce
Salesforce obsługuje szyfrowanie platformy Shield, które umożliwia szyfrowanie wszystkich plików, załączników, pól niestandardowych. Aby uzyskać więcej informacji, zobacz [Opis przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Scenariusze hybrydowe (przy użyciu bramy zarządzania danymi)
Scenariusze hybrydowe wymagają zainstalowania bramy zarządzania danymi w sieci lokalnej lub w sieci wirtualnej (Azure) lub wirtualnej chmurze prywatnej (Amazon). Brama musi mieć dostęp do lokalnych magazynów danych. Aby uzyskać więcej informacji na temat bramy, zobacz [Brama zarządzania danymi](data-factory-data-management-gateway.md). 

![Kanały bramy zarządzania danymi](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanał poleceń** umożliwia komunikację między usługami przenoszenia danych w fabryce danych i bramie zarządzania danymi. Komunikat zawiera informacje związane z działalnością. Kanał danych służy do przesyłania danych między lokalnymi magazynami danych a magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia lokalnego magazynu danych
Poświadczenia lokalnych magazynów danych są przechowywane lokalnie (nie w chmurze). Można je ustawić na trzy różne sposoby. 

- Przy użyciu **zwykłego tekstu** (mniej bezpieczne) za pośrednictwem protokołu HTTPS z usługi Azure Portal/ Copy Wizard. Poświadczenia są przekazywane w postaci zwykłego tekstu do bramy lokalnej.
- Korzystanie z **biblioteki kryptografii JavaScript z Kreatora kopiowania**.
- Korzystanie z **aplikacji menedżera poświadczeń opartych na kliknięciach**. Aplikacja po kliknięciu jest wykonywana na komputerze lokalnym, który ma dostęp do bramy i ustawia poświadczenia dla magazynu danych. Ta opcja i następna są najbezpieczniejszymi opcjami. Aplikacja menedżera poświadczeń domyślnie używa portu 8050 na komputerze z bramą do bezpiecznej komunikacji.  
- Polecenie cmdlet programu PowerShell należy używać polecenia cmdlet [programu New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) PowerShell do szyfrowania poświadczeń. Polecenie cmdlet używa certyfikatu, który brama jest skonfigurowana do szyfrowania poświadczeń. Można użyć zaszyfrowanych poświadczeń zwróconych przez to polecenie cmdlet i dodać je do elementu **EncryptedCredential** **połączeniaString** w pliku JSON, którego używasz z poleceniem cmdlet [New-AzDataFactaFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) lub w urywek JSON w Edytorze fabryki danych w portalu. Ta opcja i aplikacja click-once są najbardziej bezpiecznymi opcjami. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Szyfrowanie oparte na bibliotece kryptografii JavaScript
Poświadczenia magazynu danych można szyfrować za pomocą [biblioteki kryptografii JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) z [Kreatora kopiowania](data-factory-copy-wizard.md). Po wybraniu tej opcji Kreator kopiowania pobiera klucz publiczny bramy i używa go do szyfrowania poświadczeń magazynu danych. Poświadczenia są odszyfrowywane przez komputer bramy i chronione przez system Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Obsługiwane przeglądarki:** IE8, IE9, IE10, IE11, Microsoft Edge i najnowsze przeglądarki Firefox, Chrome, Opera, Safari. 

#### <a name="click-once-credentials-manager-app"></a>Aplikacja Menedżera poświadczeń typu "kliknij"
Możesz uruchomić aplikację menedżera poświadczeń opartą na kliknięciach z kreatora portalu Azure/Kreatora kopiowania podczas tworzenia potoków. Ta aplikacja zapewnia, że poświadczenia nie są przesyłane w postaci zwykłego tekstu przez przewód. Domyślnie używa portu **8050** na komputerze z bramą do bezpiecznej komunikacji. W razie potrzeby można zmienić ten port.  
  
![Port HTTPS dla bramy](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Obecnie brama zarządzania danymi używa jednego **certyfikatu**. Ten certyfikat jest tworzony podczas instalacji bramy (dotyczy bramy zarządzania danymi utworzonej po listopadzie 2016 r. i wersji 2.4.xxxx.x lub nowszej). Certyfikat ten można zastąpić własnym certyfikatem SSL/TLS. Ten certyfikat jest używany przez aplikację menedżera poświadczeń kliknięć, aby bezpiecznie połączyć się z komputerem bramy w celu ustawienia poświadczeń magazynu danych. Przechowuje poświadczenia magazynu danych bezpiecznie lokalnie przy użyciu [dpapi](https://msdn.microsoft.com/library/ms995355.aspx) systemu Windows na komputerze z bramą. 

> [!NOTE]
> Starsze bramy, które zostały zainstalowane przed listopadem 2016 r. lub w wersji 2.3.xxxx.x, nadal używają poświadczeń zaszyfrowanych i przechowywanych w chmurze. Nawet jeśli uaktualnisz bramę do najnowszej wersji, poświadczenia nie są migrowane do komputera lokalnego    
  
| Wersja bramy (podczas tworzenia) | Przechowywane poświadczenia | Szyfrowanie poświadczeń/ zabezpieczenia | 
| --------------------------------- | ------------------ | --------- |  
| < = 2,3.xxxx.x | W chmurze | Szyfrowane przy użyciu certyfikatu (innego niż używany przez aplikację Menedżera poświadczeń) | 
| > = 2,4.xxxx.x | W lokalu | Zabezpieczony przez DPAPI | 
  

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są za pośrednictwem bezpiecznego kanału **HTTPS** i **TLS przez TCP,** aby zapobiec atakom typu man-in-the-middle podczas komunikacji z usługami platformy Azure.
 
Można również użyć [protokołu IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [express route,](../../expressroute/expressroute-introduction.md) aby jeszcze bardziej zabezpieczyć kanał komunikacji między siecią lokalną a platformą Azure.

Sieć wirtualna jest logiczną reprezentacją sieci w chmurze. Sieć lokalną można połączyć z siecią wirtualną platformy Azure, konfigurując sieć VPN IPSec (lokacja lokacja) lub trasę ekspresową (prywatna komunikacja równorzędna)     

W poniższej tabeli podsumowano zalecenia dotyczące konfiguracji sieci i bramy na podstawie różnych kombinacji lokalizacji źródłowych i docelowych dla hybrydowego przenoszenia danych.

| Element źródłowy | Element docelowy | Konfiguracja sieci | Konfiguracja bramy |
| ------ | ----------- | --------------------- | ------------- | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | IPSec VPN (punkt-lokacja lub lokacja) | Bramę można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | Usługa ExpressRoute (prywatna komunikacja równorzędna) | Bramę można zainstalować lokalnie lub na maszynie wirtualnej platformy Azure w sieci Wirtualnej | 
| Lokalnie | Usługi oparte na platformie Azure, które mają publiczny punkt końcowy | Usługa ExpressRoute (komunikacja równorzędna publiczna) | Brama musi być zainstalowana lokalnie | 

Na poniższych obrazach przedstawiono użycie bramy zarządzania danymi do przenoszenia danych między lokalną bazą danych a usługami platformy Azure przy użyciu trasy Express i sieci VPN IPSec (z siecią wirtualną):

**Trasa ekspresowa:**
 
![Korzystanie z trasy ekspresowej z bramą](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**Sieć VPN IPSec:**

![Sieć VPN IPSec z bramą](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfiguracje zapory i adres IP bramy na białej liście

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory sieci lokalnej/prywatnej  
W przedsiębiorstwie **zapora firmowa** działa na centralnym routerze organizacji. Zapora **systemu Windows** działa jako demon na komputerze lokalnym, na którym zainstalowana jest brama. 

Poniższa tabela zawiera wymagania dotyczące **portu wychodzącego** i domeny **zapory firmowej**.

| Nazwy domen | Porty wychodzące | Opis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Wymagane przez bramę do łączenia się z usługami przenoszenia danych w fabryce danych |
| `*.core.windows.net` | 443 | Używane przez bramę do łączenia się z kontem usługi Azure Storage podczas korzystania z funkcji [kopiowania etapowego.](data-factory-copy-activity-performance.md#staged-copy) | 
| `*.frontend.clouddatahub.net` | 443 | Wymagane przez bramę do łączenia się z usługą Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (OPCJONALNIE) potrzebne, gdy miejsce docelowe jest Azure SQL Database/ Azure SQL Data Warehouse. Funkcja kopiowania etapowego służy do kopiowania danych do usługi Azure SQL Database/Azure SQL Data Warehouse bez otwierania portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCJONALNIE) potrzebne, gdy miejscem docelowym jest magazyn usługi Azure Data Lake | 

> [!NOTE] 
> Może być konieczne zarządzanie portami/ domenami na białej liście na poziomie zapory firmowej, zgodnie z wymaganiami odpowiednich źródeł danych. W tej tabeli jako przykłady użyto tylko usługi Azure SQL Database, usługi Azure SQL Data Warehouse, Usługi Azure Data Lake Store.   

Poniższa tabela zawiera wymagania **dotyczące portów przychodzących** dla **zapory systemu Windows**.

| Porty wejściowe | Opis | 
| ------------- | ----------- | 
| 8050 (TCP) | Wymagane przez aplikację menedżera poświadczeń do bezpiecznego ustawiania poświadczeń dla lokalnych magazynów danych w bramie. | 

![Wymagania dotyczące portu bramy](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfiguracje IP/ umieszczanie na białej liście w magazynie danych
Niektóre magazyny danych w chmurze wymagają również umieszczenia na białej liście adresu IP komputera uzyskującego do nich dostęp. Upewnij się, że adres IP komputera bramy jest odpowiednio umieszczony na białej liście/ skonfigurowany w zaporze.

Następujące magazyny danych w chmurze wymagają umieszczenia na białej liście adresu IP komputera bramy. Niektóre z tych magazynów danych domyślnie mogą nie wymagać umieszczenia na białej liście adresu IP. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Magazyn danych SQL platformy Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** Czy brama może być współużytkowana w różnych fabrykach danych?
**Odpowiedź:** Nie obsługujemy jeszcze tej funkcji. Aktywnie nad nią pracujemy.

**Pytanie:** Jakie są wymagania dotyczące portu dla bramy do pracy?
**Odpowiedź:** Gateway udostępnia połączenia oparte na protokoiści HTTP do otwierania Internetu. Aby nawiązać połączenie, należy otworzyć **porty wychodzące 443 i 80** dla bramy. Otwórz **port przychodzący 8050** tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji Menedżera poświadczeń. Jeśli usługa Azure SQL Database lub usługa Azure SQL Data Warehouse jest używana jako źródło/miejsce docelowe, należy również otworzyć port **1433.** Aby uzyskać więcej informacji, zobacz [konfiguracje zapory i sekcję adresy IP na białej liście.](#firewall-configurations-and-whitelisting-ip-address-of gateway) 

**Pytanie:** Jakie są wymagania dotyczące certyfikatów dla bramy?
**Odpowiedź:** Bieżąca brama wymaga certyfikatu, który jest używany przez aplikację menedżera poświadczeń do bezpiecznego ustawiania poświadczeń magazynu danych. Ten certyfikat jest certyfikatem z podpisem własnym utworzonym i skonfigurowanym przez instalatora bramy. Zamiast tego można użyć własnego certyfikatu TLS/SSL. Aby uzyskać więcej informacji, zobacz [sekcję aplikacji menedżera poświadczeń kliknięć.](#click-once-credentials-manager-app) 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje na temat wydajności działania kopiowania, zobacz [Kopiowanie wydajności działania i dostrajania .](data-factory-copy-activity-performance.md)

 
