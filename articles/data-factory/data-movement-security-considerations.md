---
title: Zagadnienia dotyczące zabezpieczeń w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Opisuje infrastruktury podstawowych zabezpieczeń, który usługi przenoszenia danych z fabryki danych Azure używany do zabezpieczania danych.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: abnarain
ms.openlocfilehash: 5d9061e12ac9fe0b9d858690897e582acab5169e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754583"
---
#  <a name="security-considerations-for-data-movement-in-azure-data-factory"></a>Zagadnienia dotyczące zabezpieczeń dla ruchu danych w fabryce danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-data-movement-security-considerations.md)
> * [Wersja 2 — wersja zapoznawcza](data-movement-security-considerations.md)

W tym artykule opisano infrastruktury podstawowych zabezpieczeń, który usługi przenoszenia danych z fabryki danych Azure używany do zabezpieczania danych. Zasoby dotyczące zarządzania fabryki danych są wbudowane w infrastrukturze zabezpieczeń platformy Azure i użyj wszystkich możliwych zabezpieczenia oferowanych na platformie Azure.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [zagadnienia dotyczące zabezpieczeń przepływu danych dla fabryki danych w wersji 1](v1/data-factory-data-movement-security-considerations.md).

W ramach rozwiązania fabryki danych jest tworzony co najmniej jeden [potok](concepts-pipelines-activities.md) danych. Potoki to logiczne grupy działań, które wspólnie wykonują zadanie. Potoki te znajdują się w regionie, w której został utworzony z fabryką danych. 

Mimo że fabryki danych jest dostępna tylko w regionach kilka, usługa przenoszenia danych jest [dostępne globalnie](concepts-integration-runtime.md#integration-runtime-location) w celu zapewnienia zgodności danych, wydajność i mniejsze sieci wyjściowych kosztów. 

Fabryka danych Azure nie przechowuje żadnych danych, z wyjątkiem poświadczeń połączonej usługi dla magazynów danych chmury, które są szyfrowane za pomocą certyfikatów. Przy użyciu fabryki danych tworzenia opartych na danych przepływy pracy w celu organizowania przepływu danych między [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats)i przetwarzania danych przy użyciu [obliczeniowe usług](compute-linked-services.md) w innych regionów lub w w środowisku lokalnym. Można również monitorować i zarządzać nimi przepływów pracy za pomocą monitora Azure i zestawy SDK.

Przenoszenie danych przy użyciu fabryki danych jest certyfikowany dla:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Jeśli interesuje Cię zgodności platformy Azure i jak Azure zabezpiecza własnej infrastruktury, odwiedź stronę [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/default.aspx).

W tym artykule firma Microsoft analizuje zagadnienia dotyczące zabezpieczeń w następujących scenariuszach przepływu danych dwóch: 

- **Scenariusz chmury**: W tym scenariuszu, zarówno źródłowy, jak i folderu docelowego są publicznie dostępne za pośrednictwem Internetu. Obejmują one usługi magazynu w chmurze zarządzanych, takich jak usługi Azure Storage, Magazyn danych SQL Azure, baza danych SQL Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, usługi SaaS, takie jak Salesforce i protokoły sieci web, takich jak FTP i OData. Znajdź pełną listę obsługiwanych źródeł danych w [obsługiwane formaty i magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
- **Scenariusza hybrydowego**: W tym scenariuszu źródła lub folderu docelowego jest za zaporą lub wewnątrz sieci firmowej lokalnymi. Lub magazynu danych jest w prywatnej sieci lub sieci wirtualnej (w większości przypadków źródła) i nie jest dostępny publicznie. W tym scenariuszu dzielą się również hostowanych na maszynach wirtualnych serwerów bazy danych.

## <a name="cloud-scenarios"></a>Scenariusze chmury

### <a name="securing-data-store-credentials"></a>Zabezpieczanie poświadczeń magazynu danych

- **Przechowywanie zaszyfrowane poświadczenia w magazynie zarządzanych fabryki danych Azure**. Fabryka danych chroni poświadczeń magazynu danych, szyfrując z certyfikatami zarządzany przez firmę Microsoft. Te certyfikaty są obracane (co obejmuje odnawiania certyfikatów oraz migracja poświadczenia) na dwa lata. Zaszyfrowane poświadczenia bezpiecznie są przechowywane w koncie magazynu platformy Azure zarządzanych przez usługi zarządzania fabryki danych Azure. Aby uzyskać więcej informacji o zabezpieczeniach usługi Azure Storage, zobacz [Omówienie zabezpieczeń usługi Azure Storage](../security/security-storage-overview.md).
- **Przechowywania poświadczeń w usłudze Azure Key Vault**. Można również przechowywać poświadczenia źródła danych w [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Fabryka danych pobiera poświadczeń podczas wykonywania działania. Aby uzyskać więcej informacji, zobacz [magazynu poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md).

### <a name="data-encryption-in-transit"></a>Szyfrowanie danych podczas przesyłania
W przypadku magazynu danych w chmurze obsługuje protokół HTTPS lub TLS, wszystkie dane transferu między usługi przenoszenia danych z fabryki danych i magazynu danych w chmurze są za pośrednictwem bezpiecznego kanału HTTPS lub TLS.

> [!NOTE]
> Wszystkie połączenia z bazą danych SQL Azure i usługi Azure SQL Data Warehouse wymagają szyfrowania (SSL/TLS), gdy dane są przesyłane do i z bazy danych. Podczas potoku w przypadku tworzenia za pomocą formatu JSON, Dodaj właściwość szyfrowania i wartości **true** w parametrach połączenia. W przypadku usługi Azure Storage, można użyć **HTTPS** w parametrach połączenia.

### <a name="data-encryption-at-rest"></a>Szyfrowanie danych w spoczynku
Niektóre dane są przechowywane Obsługa szyfrowania danych magazynowanych. Firma Microsoft zaleca, aby włączyć mechanizm szyfrowania danych dla tych magazynów danych. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Przezroczysty danych szyfrowania (funkcji TDE) w magazynie danych SQL Azure chroni przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowania i odszyfrowywania danych przechowywanych. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie bazy danych w usłudze SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Baza danych SQL Azure obsługuje również przezroczystego szyfrowania danych (funkcji TDE), który pomaga chronić przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowania i odszyfrowywania danych, bez konieczności wprowadzania zmian w aplikacji. To zachowanie jest niewidoczny dla klienta. Aby uzyskać więcej informacji, zobacz [przezroczystego szyfrowania danych dla bazy danych SQL i magazyn danych](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake Store zapewnia również szyfrowanie danych przechowywanych w ramach konta. Po włączeniu usługi Data Lake Store szyfruje dane przed wprowadzeniem trwałych i automatycznie odszyfrowuje przed pobierania, ustawiania przezroczystości klientowi, który uzyskuje dostęp do danych. Aby uzyskać więcej informacji, zobacz [zabezpieczeń w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Magazyn obiektów Blob Azure i magazynem tabel Azure
Magazyn obiektów Blob Azure i magazynem tabel Azure obsługuje magazynu usługi szyfrowania (SSE), który szyfruje dane przed wprowadzeniem trwałych do magazynu i automatycznie odszyfrowuje przed pobierania. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Magazyn Azure dla danych magazynowanych](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 obsługuje szyfrowanie danych przechowywanych klienta i serwera. Aby uzyskać więcej informacji, zobacz [ochrona danych przy użyciu szyfrowania](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift obsługuje szyfrowanie klastra dla przechowywanych danych. Aby uzyskać więcej informacji, zobacz [szyfrowania bazy danych Redshift Amazon](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). 

#### <a name="salesforce"></a>SalesForce
SalesForce obsługuje szyfrowanie platformy osłony, który umożliwia szyfrowanie wszystkich plików, załączników i pola niestandardowe. Aby uzyskać więcej informacji, zobacz [opis przepływ uwierzytelniania OAuth serwera sieci Web](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios"></a>Scenariuszy hybrydowych
Scenariusze hybrydowe wymaga zainstalowanego w sieci lokalnej w sieci wirtualnej (Azure) lub wewnątrz wirtualnych chmury prywatnej (Amazon) środowiska uruchomieniowego integracji siebie. Hostowanie Samoobsługowe integrację środowiska uruchomieniowego musi mieć możliwość dostępu magazynów danych lokalnych. Aby uzyskać więcej informacji dotyczących środowiska uruchomieniowego integracji hostowanie samoobsługowe, zobacz [jak utworzyć i skonfigurować hosta samodzielnego środowiska uruchomieniowego integracji](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime). 

![kanały środowiska uruchomieniowego integracji hostowanie Samoobsługowe](media/data-movement-security-considerations/data-management-gateway-channels.png)

Kanał poleceń umożliwia komunikację między usługi przenoszenia danych z fabryki danych i środowiska uruchomieniowego integracji siebie. Komunikat zawiera informacje związane z działaniem. Kanał danych jest używany do przenoszenia danych między lokalnych magazynów danych i magazyny danych w chmurze.    

### <a name="on-premises-data-store-credentials"></a>Dane lokalne przechowywanie poświadczeń
Poświadczenia dla sieci lokalnych magazynów danych są zawsze szyfrowane i przechowywane. One być albo przechowywane lokalnie na komputerze środowiska uruchomieniowego integracji siebie lub przechowywanych w magazynie usługi fabryka danych Azure zarządzanych (podobnie jak magazynu poświadczeń w chmurze). 

- **Przechowywanie poświadczeń lokalnie**. Szyfrowanie i przechowywanie poświadczeń lokalnie na siebie integrację środowiska uruchomieniowego, należy wykonać czynności opisane w [szyfrowania poświadczeń dla lokalnych magazynów danych w fabryce danych Azure](encrypt-credentials-self-hosted-integration-runtime.md). Wszystkie łączniki obsługuje tę opcję. Przy użyciu środowiska uruchomieniowego integracji siebie [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) do szyfrowania informacji poufnych danych i poświadczeń. 

   Użyj **AzureRmDataFactoryV2LinkedServiceEncryptedCredential nowy** polecenia cmdlet do szyfrowania poświadczeń połączonej usługi i poufnych informacji w połączonej usłudze. Można użyć JSON zwrócił (z **EncryptedCredential** elementu w parametrach połączenia) można utworzyć połączonej usługi za pomocą **AzureRmDataFactoryV2LinkedService zestaw** polecenia cmdlet.  

- **Magazyn w magazynie usługi fabryka danych Azure zarządzanych**. Jeśli używasz bezpośrednio **AzureRmDataFactoryV2LinkedService zestaw** polecenia cmdlet z połączeniem ciągi i poświadczeń wbudowanego w formacie JSON, połączonej usługi jest szyfrowane i przechowywane w magazynie usługi fabryka danych Azure zarządzanych. Poufne informacje nadal są szyfrowane przez certyfikat i firma Microsoft zarządza tymi certyfikatami.



#### <a name="ports-used-when-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Porty używane do szyfrowania połączonej usługi na siebie integrację środowiska uruchomieniowego
Domyślnie program PowerShell korzysta z portu 8050 na komputerze z programem obsługi integracji hostowanie Samoobsługowe dla bezpiecznej komunikacji. W razie potrzeby można zmienić tego portu.  

![Port HTTPS dla bramy](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Szyfrowanie podczas przesyłania
Wszystkie operacje transferu danych są za pośrednictwem bezpiecznego kanału HTTPS i TLS za pośrednictwem protokołu TCP, aby zapobiec atakom man-in--middle podczas komunikowania się z usługami Azure.

Można również użyć [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) lub [Azure ExpressRoute](../expressroute/expressroute-introduction.md) dodatkowo zabezpieczyć kanał komunikacyjny między siecią lokalną a Azure.

Sieć wirtualna platformy Azure jest logiczną reprezentacja sieci w chmurze. Można połączyć sieć lokalną do sieci wirtualnej przy konfigurowania sieci VPN IPSec (site-to-site) lub ExpressRoute (prywatnej komunikacji równorzędnej).    

W poniższej tabeli przedstawiono sieci i zalecenia dotyczące konfiguracji środowiska uruchomieniowego integracji siebie oparte na różnych kombinacji źródłowe i docelowe lokalizacje hybrydowego przenoszenia danych.

| Element źródłowy      | Element docelowy                              | Konfiguracja sieci                    | Instalacja środowiska Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożony w sieci wirtualnych | IPSec VPN (punkt lokacja lub lokacja lokacja) | Hostowanie Samoobsługowe integrację środowiska uruchomieniowego może być zainstalowana lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej. |
| Lokalnie | Maszyny wirtualne i usługi w chmurze wdrożony w sieci wirtualnych | ExpressRoute (prywatnej komunikacji równorzędnej)           | Hostowanie Samoobsługowe integrację środowiska uruchomieniowego może być zainstalowana lokalnie lub na maszynie wirtualnej platformy Azure w sieci wirtualnej. |
| Lokalnie | Usług Azure, których publiczny punkt końcowy | ExpressRoute (publicznej komunikacji równorzędnej)            | Hostowanie Samoobsługowe integrację środowiska uruchomieniowego musi być zainstalowany na lokalnym. |

Na poniższych ilustracjach przedstawiono użycie środowiska uruchomieniowego integracji siebie do przenoszenia danych między lokalną bazą danych i usług Azure przy użyciu usługi ExpressRoute i sieci VPN IPSec (za pomocą usługi Azure Virtual Network):

**ExpressRoute**

![Użyj bramy ExpressRoute](media/data-movement-security-considerations/express-route-for-gateway.png) 

**Protokół IPSec sieci VPN**

![Sieć VPN IPSec z bramy](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a> Konfiguracje zapór i listę dozwolonych podobnej adresów IP

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Wymagania dotyczące zapory dla sieci o lokalnym/prywatnym  
W przedsiębiorstwie firmowa zapora działa na routerze centralnej w organizacji. Zapora systemu Windows działa jako demon na komputerze lokalnym, w którym zainstalowano środowiska uruchomieniowego integracji siebie. 

W poniższej tabeli przedstawiono wychodzący port i domeny wymagania dotyczące zapór w firmach:

| Nazwy domen                  | Porty wyjściowe | Opis                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Wymagane przez siebie integrację środowiska uruchomieniowego do nawiązania połączenia usługi przenoszenia danych z fabryki danych. |
| `*.core.windows.net`          | 443            | Używane przez siebie integrację środowiska uruchomieniowego można połączyć z kontem magazynu platformy Azure, korzystając z [przemieszczane kopiowania](copy-activity-performance.md#staged-copy) funkcji. |
| `*.frontend.clouddatahub.net` | 443            | Wymagane przez siebie integrację środowiska uruchomieniowego do nawiązania połączenia usługi fabryka danych. |
| `*.database.windows.net`      | 1433           | (Opcjonalnie) Wymagany podczas kopiowania z lub do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse. Funkcja kopiowania przemieszczanego skopiować dane do bazy danych SQL Azure lub usługi Azure SQL Data Warehouse, bez konieczności otwierania portu 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | (Opcjonalnie) Wymagany podczas kopiowania z lub do usługi Azure Data Lake Store. |

> [!NOTE] 
> Może być konieczne zarządzanie portów lub listę dozwolonych podobnej domeny na poziomie firmowej zapory zgodnie z wymaganiami źródeł odpowiednich danych. Ta tabela używa tylko bazy danych SQL Azure, Magazyn danych SQL Azure i usługi Azure Data Lake Store jako przykłady.   

W poniższej tabeli przedstawiono wymagania dotyczące portów dla ruchu przychodzącego zapory systemu Windows:

| Porty wejściowe | Opis                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Wymagany przez polecenie cmdlet programu PowerShell szyfrowania zgodnie z opisem w [szyfrowania poświadczeń dla lokalnych magazynów danych w fabryce danych Azure](encrypt-credentials-self-hosted-integration-runtime.md)i przez aplikację Menedżer poświadczeń w celu bezpiecznego ustawienia poświadczeń dla lokalnych magazynów danych na siebie integrację środowiska uruchomieniowego. |

![Wymagania dotyczące portów bramy](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-and-whitelisting-in-data-stores"></a>Konfiguracje adresów IP i listę dozwolonych podobnej w magazyny danych
Niektóre sklepy danych w chmurze również wymagać tego możesz listy dozwolonych adresów IP adres IP komputera, dostępu do magazynu. Upewnij się, że adres IP maszyny środowiska uruchomieniowego integracji hostowania samoobsługowego jest białej lub odpowiednio skonfigurowane w zaporze.

Następujące magazyny danych chmury wymagają adresu IP maszyny środowiska uruchomieniowego integracji siebie tego możesz dozwolonych. Niektóre z tych magazynów danych, domyślnie nie mogą wymagać listę dozwolonych podobnej. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Hostowanie Samoobsługowe integrację środowiska uruchomieniowego można udostępniać dla fabryki danych?**

Firma Microsoft nie obsługują tej funkcji jeszcze. Aktywnie pracujemy nad go.

**Jakie są wymagania dotyczące portów dla siebie integracji środowiska uruchomieniowego do pracy?**

Hostowanie Samoobsługowe integrację środowiska uruchomieniowego umożliwia nawiązanie połączenia oparte na protokole HTTP do uzyskania dostępu do Internetu. Hostowanie Samoobsługowe integrację środowiska uruchomieniowego to połączenie musi zostać otwarte wychodzącego porty 443 i 80. Otwórz port wejściowy 8050 tylko na poziomie komputera (nie poziom firmowej zapory) dla aplikacji Menedżer poświadczeń. Jeśli baza danych SQL Azure lub usługi Azure SQL Data Warehouse jest używany jako serwer źródłowy lub docelowy, należy otworzyć również port 1433. Aby uzyskać więcej informacji, zobacz [konfiguracji i adresy IP listę dozwolonych podobnej zapory](#firewall-configurations-and-whitelisting-ip-address-of-gateway) sekcji. 


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o wydajności działania kopiowania fabryki danych Azure, zobacz [wydajności działania kopiowania i dostrajania przewodnik](copy-activity-performance.md).

 
