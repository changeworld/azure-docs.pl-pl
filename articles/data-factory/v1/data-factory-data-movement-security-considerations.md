---
title: Zagadnienia dotyczące zabezpieczeń w przypadku przenoszenia danych w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zabezpieczania przenoszenie danych w usłudze Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 083770c24a6c8939f8d1ff9f0efd5d18aff9dcb0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60487082"
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Usługa Azure Data Factory — zagadnienia dotyczące zabezpieczeń w przypadku przenoszenia danych

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [zagadnienia dotyczące zabezpieczeń przenoszenia danych dla usługi Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Wprowadzenie
W tym artykule opisano podstawowe zabezpieczeń infrastruktury usługi przenoszenia danych w usłudze Azure Data Factory umożliwia Zabezpieczanie danych. Zasoby zarządzania usługi Azure Data Factory są oparte na infrastrukturze zabezpieczeń platformy Azure i używać wszystkich możliwych zabezpieczenia oferowanych na platformie Azure.

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](data-factory-create-pipelines.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Te potoki znajdują się w regionie, w której został utworzony z fabryką danych. 

Mimo że usługi Data Factory jest dostępna tylko w **zachodnie stany USA**, **wschodnie stany USA**, i **Europa Północna** regionach usługi data movement service jest dostępna [globalnie w kilku regionach](data-factory-data-movement-activities.md#global). Usługa Data Factory zapewnia, że dane nie opuści obszar geograficzny / region, chyba że jawnie poinstruować usługi w celu używania alternatywnym regionie, jeśli usługi data movement service nie zostało jeszcze wdrożone do tego regionu. 

Usługa Azure Data Factory, sam nie przechowuje żadnych danych, z wyjątkiem poświadczeń połączonej usługi dla magazynami danych w chmurze, które są szyfrowane za pomocą certyfikatów. Usługa umożliwia tworzenie opartych na danych przepływów pracy do aranżowania przenoszenia danych między [obsługiwanymi magazynami danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) oraz przetwarzania danych przy użyciu [usług obliczeniowych](data-factory-compute-linked-services.md) w innych regionach lub w środowisku lokalnym. Umożliwia także [monitorowanie przepływów pracy i zarządzanie nimi](data-factory-monitor-manage-pipelines.md) przy użyciu zarówno mechanizmów programowych, jaki i interfejsu użytkownika.

Przenoszenie danych za pomocą usługi Azure Data Factory został **certyfikowane** dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Jeśli interesuje Cię zgodności platformy Azure i jak Azure zabezpiecza własną infrastrukturę, odwiedź stronę [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx). 

W tym artykule omówimy zagadnienia dotyczące zabezpieczeń w następujących scenariuszach przenoszenia danych w dwóch: 

- **Scenariusz chmury**— w tym scenariuszu źródłowe i docelowe są publicznie dostępne za pośrednictwem Internetu. Obejmują one usług magazynu zarządzana usługa w chmurze, takich jak usługi Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, usług SaaS, takich jak Salesforce i protokołów sieci web, takich jak FTP i OData. Pełną listę obsługiwanych źródeł danych można znaleźć [tutaj](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Scenariusza hybrydowego**— w tym scenariuszu źródłowych i docelowych jest za zaporą lub wewnątrz sieci firmowej w środowisku lokalnym lub dane magazynu jest w sieci prywatnej / wirtualnych sieci (w większości przypadków źródła) i nie jest dostępny publicznie. Serwery bazy danych hostowanych na maszynach wirtualnych również wchodzą w zakres tego scenariusza.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="cloud-scenarios"></a>Scenariusze chmury
### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych
Usługa Azure Data Factory chroni swoje poświadczenia magazynu danych przez **szyfrowania** je za pomocą **certyfikaty zarządzany przez firmę Microsoft**. Te certyfikaty są obracane co **dwa lata** (w tym odnawiania certyfikatu i migracji poświadczeń). Te zaszyfrowane poświadczenia są bezpiecznie przechowywane w **usługi Azure Storage zarządzanych przez usługi zarządzania usługi Azure Data Factory**. Aby uzyskać więcej informacji na temat zabezpieczeń usługi Azure Storage można znaleźć [Omówienie zabezpieczeń usługi Azure Storage](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
Jeśli magazynu danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie dane transferu między usługi przenoszenia danych w usłudze Data Factory się z magazynem danych w chmurze za pośrednictwem bezpiecznego kanału protokołu HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia **usługi Azure SQL Database** i **Azure SQL Data Warehouse** zawsze należy wymagać szyfrowania (SSL/TLS) podczas gdy dane są przesyłane do i z bazy danych. Podczas tworzenia potoku za pomocą edytora JSON, Dodaj **szyfrowania** właściwości i ustaw ją na **true** w **parametry połączenia**. Kiedy używasz [kreatora kopiowania](data-factory-azure-copy-wizard.md), Kreator ustawia tę właściwość, domyślnie. Aby uzyskać **usługi Azure Storage**, możesz użyć **HTTPS** w parametrach połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre dane są przechowywane Obsługa szyfrowania nieużywanych danych. Zalecamy, aby włączyć mechanizm szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczystego szyfrowania danych (TDE) w usłudze Azure SQL Data Warehouse pomaga chronić przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych magazynowanych. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Usługa Azure SQL Database obsługuje również przezroczyste szyfrowanie danych (TDE), która pomaga w ochronie przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie danych bez konieczności wprowadzania zmian do aplikacji. To zachowanie jest niewidoczne dla klienta. Aby uzyskać więcej informacji, zobacz [funkcji Transparent Data Encryption z usługą Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Usługa Azure Data Lake store umożliwia także szyfrowanie danych przechowywanych na koncie. Po włączeniu usługa Data Lake store automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobierania, ustawiania przezroczystości klientowi dostęp do danych. Aby uzyskać więcej informacji, zobacz [zabezpieczeń w usłudze Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Usługa Azure Blob Storage i Azure Table Storage
Usługa Azure Blob Storage i Azure Table storage obsługuje szyfrowanie usługi Storage (SSE), który automatycznie szyfruje dane przed utrwaleniem ich w magazynie i odszyfrowuje przed pobierania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje klienta i serwera szyfrowanie danych magazynowanych. Aby uzyskać więcej informacji, zobacz [ochrony danych przy użyciu szyfrowania](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Obecnie fabryki danych nie obsługuje Amazon S3 w wirtualnej chmurze prywatnej (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Usługi Amazon Redshift obsługuje klaster szyfrowanie danych magazynowanych. Aby uzyskać więcej informacji, zobacz [szyfrowania bazy danych usługi Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Obecnie fabryki danych nie obsługuje usługi Amazon Redshift wewnątrz VPC. 

#### <a name="salesforce"></a>Salesforce
SalesForce obsługuje szyfrowanie platformy Shield, który umożliwia szyfrowanie wszystkich plików, załączniki, pól niestandardowych. Aby uzyskać więcej informacji, zobacz [zrozumienie przepływu uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Scenariusze hybrydowe (przy użyciu bramy zarządzania danymi)
Hybrydowe scenariusze wymagają bramy zarządzania danymi, można zainstalować w sieci lokalnej lub wewnątrz sieci wirtualnej (Azure) lub wirtualnej chmury prywatnej (Amazon). Brama musi być w stanie uzyskiwać dostępu do magazynów danych lokalnych. Aby uzyskać więcej informacji na temat bramy, zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md). 

![Kanały zarządzania bramy danych](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

**Kanał polecenia** umożliwia komunikację między usługi przenoszenia danych w usłudze Data Factory i brama zarządzania danymi. Komunikat zawiera informacje dotyczące działania. Kanał danych jest używana do przesyłania danych między magazynami danych w środowisku lokalnym i magazynami danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Poświadczenia magazynu danych w środowisku lokalnym
Poświadczenia dla swoich lokalnych magazynów danych są przechowywane lokalnie (nie w chmurze). Mogą one można ustawić na trzy różne sposoby. 

- Za pomocą **zwykłego tekstu** (mniej bezpieczna opcja) za pośrednictwem protokołu HTTPS w witrynie Azure Portal / Kreator kopiowania. Poświadczenia są przekazywane w postaci zwykłego tekstu do bramy w środowisku lokalnym.
- Za pomocą **biblioteki kryptografii JavaScript za pomocą Kreatora kopiowania**.
- Za pomocą **kliknij — po na podstawie aplikacji Menedżer poświadczeń**. Kliknięcie — gdy aplikacja wykonuje na maszynie lokalnej, która ma dostęp do bramy i ustawia poświadczenia dla magazynu danych. Tę opcję, a kolejny są najbardziej bezpieczne opcje. Aplikacji Menedżer poświadczeń, domyślnie używa portu 8050 na komputerze przy użyciu bramy na potrzeby bezpiecznej komunikacji.  
- Użyj [New AzDataFactoryEncryptValue](/powershell/module/az.datafactory/New-azDataFactoryEncryptValue) polecenia cmdlet programu PowerShell, aby zaszyfrować poświadczenia. Polecenie cmdlet używa certyfikatu że brama jest skonfigurowana na potrzeby szyfrowania poświadczeń. Można użyć zaszyfrowane poświadczenia, które są zwracane przez to polecenie cmdlet i dodaj go do **EncryptedCredential** elementu **connectionString** w pliku JSON, który jest używany z [ Nowe AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) polecenia cmdlet lub fragment kodu JSON w edytorze fabryki danych w portalu. Tę opcję, a następnie kliknij — po najbezpieczniejszych dostępnych aplikacji. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Szyfrowanie oparte na bibliotece kryptografii JavaScript
Umożliwia ona szyfrowanie poświadczeń magazynu danych przy użyciu [biblioteki JavaScript kryptografii](https://www.microsoft.com/download/details.aspx?id=52439) z [kreatora kopiowania](data-factory-copy-wizard.md). Po wybraniu tej opcji, Kreator kopiowania pobiera klucz publiczny, bramy i używa go do zaszyfrowania poświadczeń magazynu danych. Poświadczenia są odszyfrowywane przez maszynie bramy i chronione przez Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Obsługiwane przeglądarki:** IE8 programem IE9 lub nowszym, IE10, IE11, Microsoft Edge i najnowszych Firefox, Chrome, Opera, przeglądarki Safari. 

#### <a name="click-once-credentials-manager-app"></a>Kliknij przycisk — raz aplikację Menedżer poświadczeń
Możesz uruchomić kliknięcie — po na podstawie aplikację Menedżer poświadczeń z usługi Azure portal/kopiowanie kreatora podczas tworzenia potoków. Tej aplikacji zapewnia, że poświadczenia nie są przekazywane w postaci zwykłego tekstu przez sieć. Domyślnie używa portu **8050** na komputerze przy użyciu bramy na potrzeby bezpiecznej komunikacji. Jeśli to konieczne, można zmienić ten port.  
  
![Port HTTPS dla bramy](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Obecnie brama zarządzania danymi używa pojedynczego **certyfikatu**. Ten certyfikat jest tworzony podczas instalacji bramy (mają zastosowanie do bramy zarządzania danymi utworzone po listopada 2016 r. i 2.4.xxxx.x wersji lub nowszej). Możesz zastąpić ten certyfikat za pomocą certyfikatu SSL/TLS. Ten certyfikat jest używany przez kliknięcie — raz poświadczeń aplikacji Menedżera nawiązania bezpiecznego połączenia z maszyny bramy do ustawiania poświadczeń magazynu danych. Przechowuje dane poświadczeń magazynu bezpiecznie w środowisku lokalnym za pomocą Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na maszynie bramy. 

> [!NOTE]
> Starsze bram, które zostały zainstalowane przed listopada 2016 r. lub 2.3.xxxx.x wersji w dalszym ciągu używać poświadczeń zaszyfrowane i przechowywane w chmurze. Nawet jeśli uaktualnisz bramy do najnowszej wersji, poświadczenia nie są migrowane do komputera lokalnego    
  
| Wersja bramy (tworzenia) | Przechowywane poświadczenia | Szyfrowanie poświadczeń / zabezpieczeń | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | W chmurze | Szyfrowane przy użyciu certyfikatu (innego niż ten używany przez aplikację Menedżer poświadczeń) | 
| > = 2.4.xxxx.x | W środowisku lokalnym | Zabezpieczone za pomocą interfejsu DPAPI | 
  

### <a name="encryption-in-transit"></a>Szyfrowanie podczas transferu
Wszystkie transfery danych są za pośrednictwem bezpiecznego kanału **HTTPS** i **TLS za pośrednictwem protokołu TCP** przed atakami typu man-in--middle podczas komunikowania się z usługami platformy Azure.
 
Można również użyć [protokołu IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [Express Route](../../expressroute/expressroute-introduction.md) dodatkowo zabezpieczyć kanał komunikacyjny między siecią lokalną i platformy Azure.

Sieć wirtualna jest logicznym odzwierciedleniem Twojej sieci w chmurze. Można połączyć sieć lokalną z usługą Azure virtual network (VNet), konfigurując sieci VPN IPSec (site-to-site) lub usługi Expressroute (prywatnej komunikacji równorzędnej)     

W poniższej tabeli przedstawiono zalecenia konfiguracji sieci i bramy, w zależności od różnych kombinacji lokalizację źródłową i docelową dla hybrydowe przenoszenie danych.

| source | Miejsce docelowe | Konfiguracja sieci | Instalator bramy |
| ------ | ----------- | --------------------- | ------------- | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | Sieć VPN IPSec (point-to-site lub site-to-site) | Brama może być zainstalowana lokalnie lub na usługa Azure virtual machine (VM) w sieci wirtualnej | 
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożone w sieciach wirtualnych | ExpressRoute (prywatnej komunikacji równorzędnej) | Brama może być zainstalowana lokalnie lub na Maszynie wirtualnej platformy Azure w sieci wirtualnej | 
| Lokalnie | Usługi oparte na platformie Azure, z publicznym punktem końcowym | ExpressRoute (publicznej komunikacji równorzędnej) | Brama musi być zainstalowane w środowisku lokalnym | 

Na poniższych ilustracjach przedstawiono użycie bramy zarządzania danymi do przenoszenia danych między lokalną bazą danych i usług platformy Azure przy użyciu expressroute i sieci VPN IPSec (przy użyciu sieci wirtualnej):

**Expressroute:**
 
![Expressroute za pomocą bramy](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec VPN:**

![Sieć VPN IPSec za pomocą bramy](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Konfiguracje zapory i umieszczania na białej liście adres IP bramy

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory w sieci o lokalne prywatny  
W przedsiębiorstwie **zaporą firmową** działa na routerze centralnej w organizacji. Ponadto **zapory Windows** działa jako demon na komputerze lokalnym, na którym zainstalowano bramę. 

W poniższej tabeli przedstawiono **wychodząca przez port** i wymagania dotyczące domeny dla **zaporą firmową**.

| Nazwy domen | Porty ruchu wychodzącego | Opis |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Wymagane przez bramę do połączenia z usługi przenoszenia danych w usłudze Data Factory |
| `*.core.windows.net` | 443 | Używane przez bramę, połączyć się z kontem usługi Azure Storage, korzystając z [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) funkcji. | 
| `*.frontend.clouddatahub.net` | 443 | Wymagane przez bramę usługi, aby połączyć się z usługą Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (OPCJONALNIE) wymagane w przypadku lokalizacji docelowej bazy danych Azure SQL / Azure SQL Data Warehouse. Funkcja kopiowania przejściowego służy do kopiowania danych do usługi Azure SQL Database/Azure SQL Data Warehouse bez konieczności otwierania portu 1433. | 
| `*.azuredatalakestore.net` | 443 | (OPCJONALNIE) wymagane w przypadku lokalizacji docelowej usługi Azure Data Lake store | 

> [!NOTE] 
> Może być konieczne zarządzanie portami / domen umieszczania na białej liście na zaporze firmy na poziomie zgodnie z wymaganiami przez źródła odpowiednich danych. Ta tabela używa tylko usługi Azure SQL Database, Azure SQL Data Warehouse, Azure Data Lake Store jako przykłady.   

W poniższej tabeli przedstawiono **port wejściowy** wymagania dotyczące **zapory systemu windows**.

| Porty wejściowe | Opis | 
| ------------- | ----------- | 
| 8050 (TCP) | Wymagane przez aplikację Menedżer poświadczeń można bezpiecznie ustawić poświadczenia dla lokalnych magazynów danych w bramie. | 

![Wymagania dotyczące portów bramy](media/data-factory-data-movement-security-considerations/gateway-port-requirements.png)

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Konfiguracje adresów IP / Przechowaj umieszczania na białej liście w danych
Niektóre magazyny danych w chmurze wymagają również listę dozwolonych adresu IP komputera, uzyskując dostęp do nich. Upewnij się, że adres IP na maszynie bramy jest umieszczona na białej liście / odpowiednio skonfigurowane w zaporze.

Następujące magazynami danych w chmurze wymagają umieszczania na białej liście adres IP na maszynie bramy. Niektóre z tych magazynów danych, domyślnie nie może wymagać listę dozwolonych adresów IP. 

- [Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pytanie:** Bramy można współdzielić w ramach fabryki danych?
**Odpowiedź:** Firma Microsoft nie obsługuje tej funkcji jeszcze. Aktywnie nad nią pracujemy.

**Pytanie:** Jakie są wymagania dotyczące portów, aby brama mogła działać?
**Odpowiedź:** Brama umożliwia nawiązanie połączenia oparte na protokole HTTP do otwartej sieci internet. **Wychodzącego porty 443 i 80** muszą być otwarte dla bramy to połączenie. Otwórz **8050 portu dla ruchu przychodzącego** tylko na poziomie komputera (nie na poziomie zapory firmowej) dla aplikacji Menedżer poświadczeń. Jeśli usługa Azure SQL Database lub Azure SQL Data Warehouse jest używany jako źródło / docelowym, a następnie możesz trzeba otworzyć **1433** również port. Aby uzyskać więcej informacji, zobacz [zapory, konfiguracje i listy dozwolonych adresów IP](#firewall-configurations-and-whitelisting-ip-address-of gateway) sekcji. 

**Pytanie:** Jakie są wymagania dotyczące certyfikatów dla bramy?
**Odpowiedź:** Bieżąca brama wymaga certyfikatu, który jest używany przez aplikację Menedżer poświadczeń do bezpiecznego ustawiania poświadczeń magazynu danych. Ten certyfikat jest certyfikat z podpisem własnym utworzony i skonfigurowany przez Instalatora bramy. Można użyć własnego protokołu TLS / SSL certyfikatu zamiast tego. Aby uzyskać więcej informacji, zobacz [kliknij — raz poświadczeń aplikacji Menedżera](#click-once-credentials-manager-app) sekcji. 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje dotyczące wydajności działania kopiowania, zobacz [skopiuj dostrajania przewodnik dotyczący wydajności działania i](data-factory-copy-activity-performance.md).

 
