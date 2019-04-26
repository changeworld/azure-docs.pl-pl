---
title: Usługa Azure encryption overview | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych opcji szyfrowania na platformie Azure
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 272cc843ab90eade06525f665d3cf2decf74a26f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444520"
---
# <a name="azure-encryption-overview"></a>Omówienie szyfrowania platformy Azure

Ten artykuł zawiera omówienie sposobu używania szyfrowania w systemie Microsoft Azure. Poruszono w nim głównych obszarów szyfrowania, w tym szyfrowanie w spoczynku, szyfrowanie w locie i zarządzania kluczami w usłudze Azure Key Vault. Każda sekcja zawiera łącza do bardziej szczegółowych informacji.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych

Dane magazynowane zawiera informacje, który znajduje się w trwałym magazynie na nośniku fizycznym, w dowolnym formacie cyfrowym. Nośnik mogą zawierać plików na nośnikach magnetycznych albo optycznych danych archiwalnych i kopie zapasowe danych. Platforma Microsoft Azure oferuje szeroką gamę rozwiązań magazynowania danych do różnych celów, w tym plików, dysków, obiektów blob i Magazyn tabel. Firma Microsoft udostępnia również szyfrowanie w celu ochrony [usługi Azure SQL Database](../sql-database/sql-database-technical-overview.md), [usługi Azure Cosmos DB](../cosmos-db/introduction.md), a usługa Azure Data Lake.

Szyfrowanie danych magazynowanych jest dostępna dla usług dla oprogramowania jako usługi (SaaS), platforma jako usługa (PaaS) i infrastruktury jako modele chmury usługi (IaaS). Ten artykuł zawiera podsumowanie i udostępnia zasoby przydatne podczas używania opcji szyfrowania platformy Azure.

Aby uzyskać bardziej szczegółowe omówienie sposobu dane magazynowane są szyfrowane na platformie Azure, zobacz [danych na platformę Azure szyfrowania podczas spoczynku](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Usługa Azure encryption modeli

Platforma Azure obsługuje różne modele szyfrowania, w tym szyfrowania po stronie serwera, który korzysta z kluczy zarządzanych przez usługę, kluczy zarządzanych przez klienta w usłudze Key Vault lub kluczy zarządzanych przez klienta na sprzęcie kontrolowane przez klienta. Za pomocą szyfrowania po stronie klienta można zarządzać i przechowywania kluczy w środowisku lokalnym lub w innym, Zabezpiecz lokalizację.

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Szyfrowanie po stronie klienta jest wykonywane poza platformą Azure. Zawiera ona:

- Dane są szyfrowane przez aplikację, która jest uruchomiona w centrum danych klienta lub aplikacji usługi.
- Dane, które jest już zaszyfrowany po odebraniu przez platformę Azure.

Za pomocą szyfrowania po stronie klienta dostawców usług w chmurze nie ma dostępu do kluczy szyfrowania i nie może odszyfrować te dane. To Ty masz pełną kontrolę nad kluczami.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Trzy modele szyfrowanie po stronie serwera mają właściwości różnych zarządzania kluczami, które można wybrać zgodnie z wymaganiami:

- **Klucze zarządzane przez usługę**: Zapewnia kombinację kontrola i wygoda niskie obciążenie.

- **Klucze zarządzane przez klienta**: Zapewnia kontrolę nad kluczami, łącznie z obsługą rozwiązania Bring Your Own kluczy (BYOK) lub umożliwia generowanie nowych.

- **Klucze zarządzane przez usługę sprzętu kontrolowane przez klienta**: Umożliwia zarządzanie kluczami w repozytorium własnościowych poza kontrolą firmy Microsoft. Cecha ta nosi nazwę hosta Your Own Key (HYOK). Jednak konfiguracja jest złożona i większość usług platformy Azure nie obsługują tego modelu.

### <a name="azure-disk-encryption"></a>Usługa Azure disk encryption

Można chronić maszyny wirtualne Windows i Linux przy użyciu [usługa Azure disk encryption](azure-security-disk-encryption.md), który korzysta [funkcji Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technologii i systemu Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) ochrony zarówno dyski systemu operacyjnego i dysków z danymi za pomocą szyfrowania pełnego woluminu.

Szyfrowanie kluczy i wpisów tajnych, które są chronione w sieci [subskrypcji usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Za pomocą usługi Azure Backup, możesz utworzyć kopię zapasową i przywracanie zaszyfrowanych maszyn wirtualnych (VM), korzystających z konfiguracji klucza szyfrowania klucza (KEK).

### <a name="azure-storage-service-encryption"></a>Szyfrowanie usługi Azure Storage

Dane magazynowane w usłudze Azure Blob storage i udziałów plików platformy Azure mogą być szyfrowane w scenariuszach zarówno po stronie serwera, jak i po stronie klienta.

[Szyfrowanie usługi Azure Storage (SSE)](../storage/common/storage-service-encryption.md) automatycznie można zaszyfrować dane przed ich przechowywania oraz automatycznie klient odszyfrowuje dane podczas pobierania go. Proces jest całkowicie niewidoczne dla użytkowników. Szyfrowanie usługi Storage korzysta z 256-bitowego [szyfrowania Advanced Encryption Standard (AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), czyli jednego z najsilniejszych szyfrów blokowych. AES obsługuje przezroczyste szyfrowanie, odszyfrowywanie i zarządzanie kluczami.

### <a name="client-side-encryption-of-azure-blobs"></a>Szyfrowanie po stronie klienta obiektów blob platformy Azure

Można wykonać po stronie klienta szyfrowania platformy Azure, obiekty BLOB na różne sposoby.

Biblioteki klienta usługi Azure Storage dla pakietu NuGet programu .NET służy do szyfrowania danych w aplikacjach klienckich przed ich przekazaniem do usługi Azure storage.

Aby dowiedzieć się więcej o i Pobierz biblioteki klienta usługi Azure Storage dla pakietu NuGet programu .NET, zobacz [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Korzystając z szyfrowania po stronie klienta za pomocą usługi Key Vault, dane są szyfrowane za pomocą jednorazowego symetrycznego zawartości szyfrowania klucza (CEK) generowany przez zestaw SDK klienta usługi Azure Storage. CEK są szyfrowane przy użyciu klucza szyfrowania klucza (KEK), który może być klucza symetrycznego lub para kluczy asymetrycznych. Można zarządzać nią lokalnie lub przechowywać je w usłudze Key Vault. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

Aby dowiedzieć się więcej o funkcji Szyfrowanie po stronie klienta za pomocą usługi Key Vault i rozpoczynanie pracy z usługą instrukcji, zobacz [samouczka: Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Azure Storage za pomocą usługi Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Ponadto umożliwia także biblioteki klienta usługi Azure Storage dla języka Java do wykonywania szyfrowania po stronie klienta, aby przekazać dane do usługi Azure Storage, a także do odszyfrowania danych po pobraniu jej do klienta. Ta biblioteka obsługuje również integrację z [usługi Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami konta magazynu.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Szyfrowanie danych magazynowanych za pomocą usługi Azure SQL Database

[Usługa Azure SQL Database](../sql-database/sql-database-technical-overview.md) jest usługą relacyjnej bazy danych ogólnego przeznaczenia na platformie Azure, która obsługuje struktury, takich jak dane relacyjne, JSON, dane przestrzenne i XML. SQL Database obsługuje szyfrowanie po stronie serwera za pomocą funkcji przezroczystego szyfrowania danych (TDE) i szyfrowanie po stronie klienta za pośrednictwem funkcji Always Encrypted.

#### <a name="transparent-data-encryption"></a>Niewidoczne szyfrowanie danych

[Funkcja TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) jest używany do szyfrowania [programu SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [usługi Azure SQL Database](../sql-database/sql-database-technical-overview.md), i [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pliki danych w czasie rzeczywistym, przy użyciu klucza szyfrowania bazy danych (DEK) , który jest przechowywany w rekordzie rozruchowym bazy danych dostępności podczas odzyskiwania.

Funkcja TDE ochronę danych i plików dziennika, przy użyciu szyfrowania AES i Triple Data Encryption Standard (3DES) algorytmy szyfrowania. Szyfrowanie pliku bazy danych jest wykonywane na poziomie strony. Strony w szyfrowanej bazy danych są szyfrowane, zanim są zapisywane na dysku i są odszyfrowywane po odczytywane do pamięci. Funkcja TDE jest teraz domyślnie włączona na nowo utworzonej bazy danych Azure SQL.

#### <a name="always-encrypted-feature"></a>Zawsze zaszyfrowane funkcji

Za pomocą [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkcji w usłudze Azure SQL można szyfrować danych w aplikacjach klienckich przed zapisanie go w usłudze Azure SQL Database. Można także włączyć delegowanie administracji bazą danych lokalnych do podmiotów trzecich i obsługa rozdzielenie tych, którzy należą do Ciebie i przeglądać dane i osoby, zarządzać nią, ale nie powinny mieć do niego dostęp.

#### <a name="cell-level-or-column-level-encryption"></a>Szyfrowanie na poziomie kolumny lub na poziomie komórki

Usługa Azure SQL Database można zastosować szyfrowania symetrycznego kolumnę danych za pomocą języka Transact-SQL. To podejście jest nazywane [poziomie komórki lub szyfrowania na poziomie kolumny (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), ponieważ służy do szyfrowania określonej kolumny lub komórki nawet określonych danych z różnymi kluczami szyfrowania. Ten sposób zapewnia bardziej szczegółową możliwości szyfrowania funkcji TDE, który szyfruje dane na stronach.

CLE posiada wbudowane funkcje, które służy do szyfrowania danych przy użyciu kluczy konfiguracji symetrycznej lub asymetrycznej, klucz publiczny certyfikatu lub hasła przy użyciu algorytmu 3DES.

### <a name="cosmos-db-database-encryption"></a>Szyfrowanie bazy danych cosmos DB

[Usługa Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) firmy Microsoft globalnie dystrybuowana, wielomodelowa baza danych. Dane użytkownika są przechowywane w usłudze Cosmos DB w pamięci trwałej (dyski półprzewodnikowe) jest domyślnie szyfrowane. Nie istnieją żadne formanty, aby go włączyć lub wyłączyć. Szyfrowanie w spoczynku jest implementowany przy użyciu wielu technologii zabezpieczeń, w tym systemów bezpiecznego magazynu kluczy, sieci szyfrowane i interfejsów API usług kryptograficznych. Klucze szyfrowania są zarządzane przez firmę Microsoft i są obracane na wewnętrzne wytyczne firmy Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Szyfrowanie w spoczynku w usłudze Data Lake

[Usługa Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) to repozytorium całego przedsiębiorstwa każdego typu danych zebranych w jednym miejscu przed wszelkim formalnym zdefiniowaniem wymagań i schematów. Data Lake Store obsługuje "na domyślnie" przezroczyste szyfrowanie danych magazynowanych, który został ustawiony podczas tworzenia konta. Domyślnie usługi Azure Data Lake Store zarządza kluczami dla Ciebie, ale istnieje możliwość zarządzać samodzielnie.

Trzy typy kluczy są używane w szyfrowania i odszyfrowywania danych: główny klucz szyfrowania (główny klucz szyfrowania), klucz szyfrowania klucza szyfrowania danych (danych) i klucza szyfrowania bloku (BEK). Główny klucz szyfrowania jest używany do szyfrowania klucza szyfrowania danych, który jest przechowywany na nośniku trwałym, a klucz szyfrowania bloków jest tworzony na podstawie klucza szyfrowania danych i bloków danych. Jeśli zarządzasz własnych kluczy można też obrócić głównego klucza szyfrowania.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

Platforma Azure oferuje wiele mechanizmów do przechowywania danych prywatnych, kiedy przesuwa się on z jednej lokalizacji do innej.

### <a name="tlsssl-encryption-in-azure"></a>Szyfrowanie TLS/SSL na platformie Azure

Firma Microsoft używa [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) protokołu (TLS) w celu ochrony danych, gdy są przesyłane między usługami w chmurze i klientami. Centra danych Microsoft negocjowania połączenia TLS w systemach klienckich, łączących się z usługami platformy Azure. TLS zapewnia silne uwierzytelnianie, poufność komunikatów i integralność (Włączanie wykrywania komunikat o naruszeniu, przejmowanie i sfałszowaniem), interoperacyjności, elastyczność algorytmów i łatwość wdrażania i używania.

[Doskonała utajnienie](https://en.wikipedia.org/wiki/Forward_secrecy) (przekazywania PFS) chroni unikatowe klucze połączenia między klienckich systemów i usług chmurowych firmy Microsoft dla klientów. Połączenia używają także długość klucza szyfrowania na podstawie RSA 2048 bitowych. Ta kombinacja utrudnia niepowołanym intercept i dostęp do danych, które są w drodze.

### <a name="azure-storage-transactions"></a>Transakcje magazynu platformy Azure

Gdy wchodzisz w interakcję z usługą Azure Storage w witrynie Azure portal, wszystkie transakcje mają miejsce w przy użyciu protokołu HTTPS. Umożliwia także interfejsu API REST magazynu przy użyciu protokołu HTTPS do interakcji z usługą Azure Storage. Podczas wywoływania interfejsów API REST na uzyskiwanie dostępu do obiektów w ramach kont magazynu przez włączenie bezpiecznego transferu, który jest wymagany dla konta magazynu, można wymusić użycie protokołu HTTPS.

Sygnatury dostępu współdzielonego ([sygnatury dostępu Współdzielonego](../storage/storage-dotnet-shared-access-signature-part-1.md)), który może służyć do Delegowanie dostępu do obiektów usługi Azure Storage, obejmują opcję, aby określić, że mogą być używane tylko z protokołu HTTPS, korzystając z sygnatury dostępu współdzielonego. Takie podejście zapewnia, że każdy użytkownik, który wysyła połączeń za pomocą tokenów sygnatur dostępu Współdzielonego używa odpowiedni protokół.

[Protokół SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), które umożliwiają dostęp do usługi Azure Files udziałów, obsługuje szyfrowanie i jest dostępna w systemie Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10. Umożliwia dostęp między regionami oraz również uzyskać dostęp do pulpitu.

Szyfrowanie po stronie klienta szyfruje dane przed wysłaniem go do swojego wystąpienia usługi Azure Storage, dzięki czemu są szyfrowane, podczas jego przesyłania w sieci.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Szyfrowanie protokołu SMB za pośrednictwem sieci wirtualnych platformy Azure 

Za pomocą [protokołu SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) na maszynach wirtualnych z systemem Windows Server 2012 lub nowszego, mogą efektywnie korzystać danych transfery zabezpieczyć dane, szyfrując przesyłane za pośrednictwem sieci wirtualnych platformy Azure. Dane, szyfrując, możesz chronić przed naruszeniem i ochronę atakach. Administratorzy mogą włączyć szyfrowanie protokołu SMB dla całego serwera lub tylko określone udziały.

Domyślnie po włączeniu szyfrowania protokołu SMB dla udziału lub serwera, tylko klientów protokołu SMB 3.0 są dozwolone dostępu do zaszyfrowanych udziałów.

## <a name="in-transit-encryption-in-vms"></a>Szyfrowanie podczas przesyłania na maszynach wirtualnych

Dane przesyłane do z i między maszynami wirtualnymi z systemem Windows są szyfrowane na wiele sposobów, w zależności od charakteru połączenia.

### <a name="rdp-sessions"></a>Sesje protokołu RDP

Umiesz nawiązywać połączenia i zaloguj się do maszyny Wirtualnej przy użyciu [protokołu RDP (Remote Desktop)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z komputera klienckiego Windows lub Mac z zainstalowanym klientem RDP. Dane przesyłane przez sieć w sesji protokołu RDP, mogą być chronione przez protokół TLS.

Umożliwia także Pulpit zdalny nawiązać połączenia z maszyny Wirtualnej z systemem Linux na platformie Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Bezpieczny dostęp do maszyn wirtualnych systemu Linux przy użyciu protokołu SSH

W przypadku zarządzania zdalnego, można użyć [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH), aby nawiązać połączenie z systemem Linux uruchomionych maszyn wirtualnych na platformie Azure. Protokół SSH jest protokołem szyfrowanego połączenia, który umożliwia bezpieczne logowania za pośrednictwem niezabezpieczonych połączeń. Jest domyślnym protokołem połączenia maszyn wirtualnych systemu Linux hostowanych na platformie Azure. Przy użyciu kluczy SSH do uwierzytelniania, można wyeliminować potrzebę hasła do logowania. Protokół SSH używa parę kluczy publiczny/prywatny (szyfrowanie asymetryczne) do uwierzytelniania.

## <a name="azure-vpn-encryption"></a>Szyfrowanie sieci VPN Azure

Na platformie Azure można połączyć za pośrednictwem wirtualnej sieci prywatnej, która tworzy bezpieczny tunel, aby zapewnić ochronę prywatności danych przesyłanych przez sieć.

### <a name="azure-vpn-gateways"></a>Bramy sieci VPN platformy Azure

Możesz użyć [bramy Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) wysyłać zaszyfrowany ruch sieciowy między siecią wirtualną i Twojej lokalizacji lokalnej przez połączenie publiczne lub wysyłać ruch sieciowy między sieciami wirtualnymi.

Użycie sieci VPN typu lokacja lokacja [IPsec](https://en.wikipedia.org/wiki/IPsec) szyfrowanie transportu. Bramy sieci VPN platformy Azure korzystają z zestawu domyślnego propozycji. Można skonfigurować bramy sieci VPN platformy Azure, niestandardowe zasady protokołu IPsec/IKE za pomocą algorytmy kryptograficzne i siły klucza, a nie ustawia zasad domyślnych.

### <a name="point-to-site-vpns"></a>Sieci VPN typu punkt lokacja

Sieci VPN typu punkt lokacja pozwalają poszczególnych klientów komputerom dostęp do sieci wirtualnej platformy Azure. [Tunelowanie protokołu SSTP (Secure Socket)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) służy do tworzenia tunelu VPN. Ona przechodzić przez zapory (tunel zostanie wyświetlony jako połączenie HTTPS). Można użyć własnych wewnętrznej infrastruktury kluczy publicznych (PKI) główny urząd certyfikacji (CA) dla połączenia punkt lokacja.

Można skonfigurować połączenie VPN punkt lokacja z siecią wirtualną przy użyciu witryny Azure portal przy użyciu uwierzytelniania certyfikatów lub programu PowerShell.

Aby dowiedzieć się więcej na temat połączeń sieci VPN punkt lokacja z siecią wirtualną platformy Azure, zobacz:

[Konfigurowanie połączenia punkt lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikacji: Witryna Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Konfigurowanie połączenia punkt lokacja z siecią wirtualną przy użyciu uwierzytelniania certyfikatu: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sieci VPN typu lokacja lokacja 

Połączenie bramy sieci VPN typu lokacja lokacja można użyć, aby połączyć sieć lokalną z siecią wirtualną platformy Azure za pośrednictwem tunelu VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN został zewnętrznie publiczny adres IP przypisany do niego.

Można skonfigurować połączenie VPN lokacja lokacja z siecią wirtualną przy użyciu witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji, zobacz:

[Utwórz połączenie lokacja lokacja w witrynie Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Utwórz połączenie lokacja lokacja w programie PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Tworzenie sieci wirtualnej za pomocą połączenia sieci VPN typu lokacja lokacja przy użyciu interfejsu wiersza polecenia](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Szyfrowanie podczas przesyłania w usłudze Data Lake

Dane przesyłane (inaczej dane w ruchu) również są zawsze szyfrowane w usłudze Data Lake Store. Oprócz szyfruje dane przed przekazaniem jej nośniku trwałym, dane są również zawsze zabezpieczane podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST usługi Data Lake Store.

Aby dowiedzieć się więcej o funkcji Szyfrowanie danych przesyłanych w usłudze Data Lake, zobacz [szyfrowanie danych w Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Zarządzanie kluczami za pomocą usługi Key Vault

Bez prawidłowego ochrony i zarządzanie kluczami szyfrowanie staje się bezużyteczny. Usługa Key Vault to rozwiązanie zalecanymi przez firmę Microsoft do zarządzania i kontrolowania dostępu do kluczy szyfrowania używanych przez usługi w chmurze. Do usług lub użytkownikom za pośrednictwem portalu konta usługi Azure Active Directory, można przypisać uprawnienia do dostępu do kluczy.

Usługa Key Vault zwalnia organizacjach konieczność konfigurowania, poprawiania i obsługi sprzętowych modułów zabezpieczeń (HSM) oraz oprogramowania do zarządzania kluczami. Korzystając z usługi Key Vault to Ty masz kontrolę. Microsoft nigdy nie widzi Twoich kluczy, a aplikacje nie mają bezpośredniego dostępu do nich. Można również importować lub generować klucze w sprzętowych modułach zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd zabezpieczeń platformy Azure](security-get-started-overview.md)
- [Omówienie zabezpieczeń sieci platformy Azure](security-network-overview.md)
- [Omówienie zabezpieczeń usługi Azure database](azure-database-security-overview.md)
- [Omówienie zabezpieczeń usługi Azure virtual machines](security-virtual-machines-overview.md)
- [Szyfrowanie danych w spoczynku](azure-security-encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](azure-security-data-encryption-best-practices.md)
