---
title: Omówienie usługi Azure szyfrowania | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o różnych opcji szyfrowania na platformie Azure
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 00c8b30b5351b7a6e4388b186fab70e3a3357ef4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366311"
---
# <a name="azure-encryption-overview"></a>Omówienie usługi Azure szyfrowania

Ten artykuł zawiera omówienie sposobu używania szyfrowania w Microsoft Azure. Obejmuje ona głównych obszarów szyfrowania, w tym szyfrowanie magazynowanych, szyfrowanie w locie i zarządzania kluczami z usługi Azure Key Vault. Każda sekcja zawiera łącza do bardziej szczegółowych informacji.

## <a name="encryption-of-data-at-rest"></a>Szyfrowanie nieużywanych danych

Magazynowane dane zawierają informacje znajdują się w magazynu trwałego na nośniku fizycznym, w dowolnym formacie cyfrowym. Nośnik mogą zawierać pliki na nośnikach magnetycznych albo optycznych, danych archiwalnych i kopie zapasowe danych. Microsoft Azure oferuje wiele rozwiązań magazynu danych do różnych celów, w tym plików, dysków, obiektów blob i Magazyn tabel. Firma Microsoft udostępnia również szyfrowanie w celu ochrony [bazy danych SQL Azure](../sql-database/sql-database-technical-overview.md), [bazy danych Azure rozwiązania Cosmos](../cosmos-db/introduction.md)i usługi Azure Data Lake.

Szyfrowanie danych magazynowanych jest dostępna dla usług przez oprogramowanie jako usługa (SaaS), platformy jako usługa (PaaS) i infrastruktury jako modele chmury usługi (IaaS). Ten artykuł zawiera podsumowanie i udostępnia zasoby, aby użyć opcji szyfrowania platformy Azure.

Aby uzyskać bardziej szczegółowe omówienie sposobu magazynowane dane są szyfrowane na platformie Azure, zobacz [danych Azure szyfrowania na Rest](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modele Azure szyfrowania

Azure obsługuje różne modele szyfrowania, w tym szyfrowania po stronie serwera, które używa kluczy zarządzane przez usługę, zarządzane przez klienta kluczy w magazynie kluczy lub kluczy zarządzany przez klienta na sprzęcie kontrolowane przez klienta. Za pomocą szyfrowania po stronie klienta można zarządzać i przechowywanie kluczy lokalnie lub w innym Zabezpiecz lokalizację.

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Szyfrowanie po stronie klienta jest wykonywane poza platformą Azure. Obejmuje on:

- Dane są szyfrowane przez aplikację, która jest uruchomiona w centrum danych klienta, czy przez aplikację usługi.
- Dane, które już są szyfrowane po odebraniu przez platformę Azure.

Szyfrowanie po stronie klienta dostawcom usług w chmurze nie mają dostępu do kluczy szyfrowania i nie może odszyfrować te dane. Zachować pełną kontrolę nad kluczy.

### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Trzy modele szyfrowania po stronie serwera mają właściwości różnych zarządzania kluczami, które można wybrać zgodnie z wymaganiami:

- **Zarządzane przez usługę kluczy**: udostępnia połączenie kontroli i wygody z małym obciążeniem.

- **Zarządzany przez klienta kluczy**: zapewnia kontrolę nad kluczami, włącznie z obsługą Bring Your własnych kluczy (BYOK) lub umożliwia wygenerowanie nowych.

- **Zarządzane przez usługę klucze w sprzętowych kontrolowane przez klienta**: umożliwia zarządzanie kluczami w repozytorium zastrzeżonych, poza kontrolą firmy Microsoft. Tej właściwości jest wywoływana hosta swój własny klucz (HYOK). Jednak konfiguracja jest złożona i najbardziej Azure services nie obsługują tego modelu.

### <a name="azure-disk-encryption"></a>Szyfrowanie dysków Azure

Można chronić maszyny wirtualnej systemu Windows i Linux przy użyciu [szyfrowania dysków Azure](azure-security-disk-encryption.md), który korzysta z [funkcji Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technologii i Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) zarówno ochrony dyski systemu operacyjnego i dysków z danymi z szyfrowanie całego woluminu.

Szyfrowanie kluczy i kluczy tajnych są chronione w sieci [subskrypcji usługi Azure Key Vault](../key-vault/key-vault-whatis.md). Za pomocą usługi Kopia zapasowa Azure, można utworzyć kopię zapasową i przywracanie zaszyfrowanych maszyn wirtualnych (VM), które korzysta z konfiguracji klucza szyfrowania klucza (KEK).

### <a name="azure-storage-service-encryption"></a>Szyfrowanie usługi Azure Storage

Dane przechowywane w magazynie obiektów Blob Azure i udziały plików platformy Azure mogą być szyfrowane w scenariuszach zarówno po stronie serwera i po stronie klienta.

[Szyfrowanie usługi Azure magazynu (SSE)](../storage/common/storage-service-encryption.md) automatycznie można zaszyfrować dane przed jest przechowywany, i automatycznie klient odszyfrowuje dane podczas pobierania go. Proces jest całkowicie niewidoczne dla użytkowników. Szyfrowanie usługi magazynu używa 256-bitowego [encryption Advanced Encryption (Standard AES)](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), które jest jednym z najwyższy dostępny szyfrów bloku. AES obsługuje przezroczysty szyfrowania, odszyfrowywania i zarządzania kluczami.

### <a name="client-side-encryption-of-azure-blobs"></a>Szyfrowanie po stronie klienta obiekty BLOB platformy Azure

Można wykonać po stronie klienta szyfrowania Azure obiekty BLOB na różne sposoby.

Biblioteka klienta magazynu Azure dla pakietu NuGet programu .NET umożliwia szyfrowanie danych w aplikacjach klienckich przed przesłaniem go do usługi magazynu Azure.

Aby dowiedzieć się więcej o i pobrać biblioteki klienta magazynu Azure dla pakietu NuGet programu .NET, zobacz [systemu Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Korzystając z magazynu kluczy szyfrowania po stronie klienta, dane są szyfrowane przy użyciu jednorazowego symetrycznego zawartości szyfrowania klucza (CEK) generowany przez zestaw SDK klienta usługi Azure Storage. CEK są szyfrowane przy użyciu klucza szyfrowania klucza (KEK), który może być klucza symetrycznego lub para kluczy asymetrycznych. Można nim zarządzać lokalnie lub zapisz go w magazynie kluczy. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage.

Aby dowiedzieć się więcej na temat szyfrowania po stronie klienta z magazynu kluczy i rozpoczynanie pracy z dokładne instrukcje, zobacz [samouczek: szyfrowanie i odszyfrowywanie obiektów blob w usłudze Azure Storage za pomocą usługi Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Ponadto umożliwia także biblioteki klienta usługi Azure Storage dla języka Java można wykonać szyfrowania po stronie klienta, aby przekazać dane do magazynu Azure oraz do odszyfrowania danych po pobraniu jej do klienta. Ta biblioteka obsługuje również integrację z [Key Vault](https://azure.microsoft.com/services/key-vault/) zarządzania kluczami konta magazynu.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Szyfrowanie danych przechowywanych w bazie danych SQL Azure

[Baza danych SQL Azure](../sql-database/sql-database-technical-overview.md) jest usługą platformy Azure, która obsługuje struktury, takich jak relacyjnej bazie danych, formatu JSON, przestrzennych i XML ogólnego przeznaczenia relacyjnej bazy danych. Baza danych SQL obsługuje szyfrowanie po stronie serwera za pomocą funkcji funkcji przezroczystego szyfrowania danych (TDE) i szyfrowania po stronie klienta za pomocą funkcji zawsze szyfrowane.

#### <a name="transparent-data-encryption"></a>Niewidoczne szyfrowanie danych

[Funkcji TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) jest używany do szyfrowania [programu SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [bazy danych SQL Azure](../sql-database/sql-database-technical-overview.md), i [magazyn danych SQL Azure](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pliki danych w czasie rzeczywistym, przy użyciu bazy danych klucza szyfrowania , który jest przechowywany w rekordzie rozruchowym bazy danych dostępności podczas odzyskiwania.

Funkcji TDE chroni pliki danych i dziennika, przy użyciu standardu AES i Triple Data Encryption Standard (3DES) algorytmów szyfrowania. Szyfrowanie pliku bazy danych jest wykonywane na poziomie strony. Strony w zaszyfrowanej bazy danych są szyfrowane, zanim są one zapisywane na dysku i są odszyfrowywane, jeśli są one odczytu do pamięci. Funkcji TDE jest teraz włączone domyślnie na nowo utworzone bazy danych Azure SQL.

#### <a name="always-encrypted-feature"></a>Zawsze zaszyfrowane funkcji

Z [zawsze zaszyfrowane](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkcji SQL Azure można szyfrować danych w aplikacjach klienckich przed przekazaniem jej bazy danych SQL Azure. Można także włączyć delegowania administrowania bazy danych lokalnych firmom i zachowanie separacji między tych, którzy należą i przeglądać dane i osób, które możesz nim zarządzać, ale nie powinny mieć dostępu do niego.

#### <a name="cell-level-or-column-level-encryption"></a>Szyfrowanie na poziomie kolumny lub na poziomie komórki

Z bazy danych SQL Azure można zastosować szyfrowania symetrycznego z kolumną zawierającą dane przy użyciu języka Transact-SQL. Ta metoda jest wywoływana [poziomie komórki lub szyfrowania na poziomie kolumny (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), ponieważ służy do szyfrowania określone kolumny lub komórki nawet określonych danych z różnymi kluczami szyfrowania. W ten sposób zapewnia bardziej szczegółowego możliwość szyfrowania niż funkcji TDE, który szyfruje dane na stronach.

CLE ma wbudowane funkcje, które służy do szyfrowania danych przy użyciu konfiguracji symetrycznej lub asymetrycznej klucze, klucz publiczny certyfikatu lub hasło przy użyciu algorytmu 3DES.

### <a name="cosmos-db-database-encryption"></a>Szyfrowanie bazy danych DB rozwiązania cosmos

[Azure DB rozwiązania Cosmos](../cosmos-db/database-encryption-at-rest.md) jest bazą danych globalnie rozproszone i wiele modeli firmy Microsoft. Domyślnie jest szyfrowane dane użytkownika są przechowywane w bazie danych rozwiązania Cosmos w pamięci trwałej (dysków SSD). Nie istnieją żadne formanty, aby ją włączyć lub wyłączyć. Szyfrowanie magazynowane jest implementowane za pomocą wielu technologii zabezpieczeń, takich jak systemy bezpiecznego magazynu kluczy, zaszyfrowanych sieci i interfejsów API usług kryptograficznych. Klucze szyfrowania są zarządzane przez firmę Microsoft i są obracane na wewnętrzne wytyczne firmy Microsoft.

### <a name="at-rest-encryption-in-data-lake"></a>Szyfrowanie na rest w usłudze Data Lake

[Usługa Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) repozytorium dla przedsiębiorstw każdego typu danych zebranych w jednym miejscu przed żadnych formalnych definicji schematu lub wymagania. Data Lake Store obsługuje "na domyślnie" przezroczystego szyfrowania danych magazynowanych, który jest definiowany podczas tworzenia konta. Domyślnie usługa Azure Data Lake Store zarządza kluczy, ale istnieje możliwość zarządzania nimi samodzielnie.

Trzy rodzaje klucze są używane w szyfrowania i odszyfrowywania danych: klucz szyfrowania głównego (MEK), dane klucza szyfrowania i bloku klucza szyfrowania (BEK). MEK jest używany do szyfrowania klucza szyfrowania danych, który jest przechowywany na nośniku trwałe, a BEK jest określana na podstawie klucza szyfrowania danych i blok danych. Jeśli zarządzasz własnych kluczy można obracać MEK.

## <a name="encryption-of-data-in-transit"></a>Szyfrowanie danych podczas przesyłania

System Azure oferuje wiele mechanizmów zachować poufność dane przesyłane z jednej lokalizacji do innej.

### <a name="tlsssl-encryption-in-azure"></a>Protokoły TLS/SSL szyfrowania na platformie Azure

Firma Microsoft używa [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) protokołów (TLS) do ochrony danych, gdy są przesyłane między usługami w chmurze i klientów. Centrach danych firmy Microsoft negocjowania połączenia TLS w systemach klienckich łączących się z usługami Azure. TLS zapewnia silne uwierzytelnianie, poufność komunikatów i integralność (włączenie wykrywania naruszeniu wiadomości, przechwycenie i sfałszowaniem), współdziałanie, elastyczność algorytmów i łatwość wdrażania i używania.

[Udoskonalenia utajnienia](https://en.wikipedia.org/wiki/Forward_secrecy) (przekazywania PFS) chroni unikatowy kluczy połączeń między systemy klienckie klientów i usług chmurowych firmy Microsoft. Połączenia używają również na podstawie RSA 2048 bitowego szyfrowania długości kluczy. Ta kombinacja utrudnia osobom niepowołanym do przechwycenia i dostępu do danych, które są przesyłane.

### <a name="azure-storage-transactions"></a>Transakcje magazynu Azure

Gdy interakcję z usługą Azure Storage za pomocą portalu Azure wszystkich transakcji została wykonana za pośrednictwem protokołu HTTPS. Umożliwia także interfejsu API REST magazynu za pośrednictwem protokołu HTTPS do interakcji z usługą Azure Storage. Podczas wywoływania interfejsów API REST do dostępu do obiektów na kontach magazynu przez włączenie zapewnienia bezpiecznego transferu, która jest wymagana dla konta magazynu, można wymusić użycie protokołu HTTPS.

Sygnatury dostępu współdzielonego ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), które mogą służyć do delegować dostęp do obiektów usługi Azure Storage, obejmują możliwość określenia, że tylko protokołu HTTPS można użyć używania sygnatur dostępu współdzielonego. Takie podejście zapewnia, że każdy, kto wysyła łącza o tokeny sygnatury dostępu Współdzielonego używa odpowiedni protokół.

[Protokół SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), które umożliwiają dostęp do usługi pliki Azure udziałów, obsługuje szyfrowanie i jego jest dostępna w systemie Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10. Zezwala na dostęp między region, a nawet uzyskać dostęp na pulpicie.

Szyfrowanie po stronie klienta dane są szyfrowane przed wysłaniem do Twojego wystąpienia usługi Azure Storage, dzięki czemu jest on zaszyfrowany podczas przekazywania przez sieć.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Szyfrowanie protokołu SMB za pośrednictwem sieci wirtualnych platformy Azure 

Za pomocą [protokołu SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) na maszynach wirtualnych z systemem Windows Server 2012 lub nowszym, możesz wprowadzić dane bezpiecznego transferu przez szyfrowanie danych przesyłanych za pośrednictwem sieci wirtualnych Azure. Przez szyfrowanie danych, należy zapewnić ochronę przed naruszeniem i podsłuchiwaniu ataków. Administratorzy mogą włączyć szyfrowanie protokołu SMB dla całego serwera lub tylko określone udziały.

Domyślnie po włączeniu szyfrowania protokołu SMB dla udziału lub serwera, tylko klientów protokołu SMB 3.0 mogą uzyskać dostęp do zaszyfrowanych udziałów.

## <a name="in-transit-encryption-in-vms"></a>Szyfrowanie podczas przesyłania danych na maszynach wirtualnych

Dane przesyłane do z i między maszynami wirtualnymi z systemem Windows są szyfrowane na kilka sposobów, w zależności od rodzaju połączenia.

### <a name="rdp-sessions"></a>Sesje protokołu RDP

Możesz łączyć i zaloguj się do maszyny Wirtualnej za pomocą [protokołu RDP (Remote Desktop)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) z komputerem klienckim z systemem Windows lub Mac z zainstalowanym klientem RDP. Dane przesyłane przez sieć w sesji protokołu RDP, może być chroniony przez protokół TLS.

Pulpit zdalny umożliwia również połączenie z maszyną wirtualną systemu Linux na platformie Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Bezpieczny dostęp do maszyn wirtualnych systemu Linux przy użyciu protokołu SSH

W przypadku zarządzania zdalnego, można użyć [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH), aby nawiązać połączenie maszyn wirtualnych systemu Linux działających na platformie Azure. SSH jest protokołem zaszyfrowanego połączenia, który umożliwia bezpiecznego logowania za pośrednictwem niezabezpieczonego połączenia. Jest domyślnym protokołem połączenia dla maszyn wirtualnych systemu Linux hostowanych w systemie Azure. Używanie kluczy SSH do uwierzytelniania, pozwala wyeliminować potrzebę haseł do logowania. Protokół SSH używa pary kluczy publiczny/prywatny (szyfrowanie asymetryczne) do uwierzytelniania.

## <a name="azure-vpn-encryption"></a>Szyfrowanie sieci VPN platformy Azure

Azure można nawiązać za pośrednictwem wirtualnej sieci prywatnej, która tworzy bezpieczny tunel, aby chronić prywatność danych są wysyłane przez sieć.

### <a name="azure-vpn-gateways"></a>Bramy sieci VPN platformy Azure

Można użyć [bramy sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) do przesyłania szyfrowanego ruchu sieciowego między sieci wirtualnej i Twojej lokalizacji lokalnej publicznego połączenia lub na wysyłanie ruchu między sieciami wirtualnymi.

Użycie sieci VPN typu lokacja lokacja [IPsec](https://en.wikipedia.org/wiki/IPsec) szyfrowania transportu. Bramy sieci VPN platformy Azure, użyj zestaw propozycji domyślne. Można skonfigurować bramy sieci VPN platformy Azure do niestandardowych zasad IPsec i IKE za pomocą określonych algorytmów kryptograficznych i kluczy sile, a nie ustawia zasad domyślnych Azure.

### <a name="point-to-site-vpns"></a>Sieci VPN typu punkt lokacja

Sieci VPN typu punkt lokacja Zezwalaj klienta komputerom dostęp do sieci wirtualnej platformy Azure. [SSTP Secure Socket Tunneling Protocol ()](https://technet.microsoft.com/library/2007.06.cableguy.aspx) służy do tworzenia tunelu VPN. Go przechodzenie przez zapory (tunelu jest wyświetlana jako połączenia HTTPS). Można użyć własnych wewnętrznej infrastruktury kluczy publicznych (PKI) główny urząd certyfikacji (CA), połączenie punkt lokacja z.

Połączenie VPN punkt lokacja sieci wirtualnej można skonfigurować przy użyciu portalu Azure z certyfikatu uwierzytelniania lub środowiska PowerShell.

Aby dowiedzieć się więcej na temat połączeń VPN punkt lokacja sieci wirtualnej platformy Azure, zobacz:

[Skonfiguruj połączenie punkt lokacja sieci wirtualnej przy użyciu uwierzytelniania certyfikacji: Azure portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Skonfiguruj połączenie punkt lokacja sieci wirtualnej przy użyciu uwierzytelniania certyfikatu: środowiska PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Sieci VPN typu lokacja lokacja 

Połączenie bramy sieci VPN lokacja lokacja można użyć do nawiązania połączenia z siecią lokalną sieć wirtualną platformy Azure za pośrednictwem tunelu VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN, z zewnątrz publicznego adresu IP przypisane do niej.

Połączenie sieci VPN lokacja lokacja sieci wirtualnej można skonfigurować za pomocą portalu Azure, programu PowerShell lub wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji, zobacz:

[Utwórz połączenie lokacja lokacja w portalu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Utwórz połączenie lokacja lokacja w programie PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Tworzenie sieci wirtualnej z połączenia sieci VPN typu lokacja lokacja za pomocą interfejsu wiersza polecenia](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Szyfrowanie podczas przesyłania danych w usłudze Data Lake

Dane przesyłane (inaczej dane w ruchu) również są zawsze szyfrowane w usłudze Data Lake Store. Oprócz szyfrowania danych przed magazynować je na nośniku trwałe, dane są zawsze zabezpieczone podczas przesyłania przy użyciu protokołu HTTPS. Protokół HTTPS jest jedynym protokołem obsługiwanym przez interfejsy REST usługi Data Lake Store.

Aby dowiedzieć się więcej na temat szyfrowanie danych przesyłanych w usłudze Data Lake, zobacz [szyfrowanie danych w usłudze Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Zarządzanie kluczami z magazynu kluczy

Bez prawidłowego ochrony i zarządzanie kluczami szyfrowania staje się bezużyteczny. Key Vault jest rozwiązanie zalecanymi przez firmę Microsoft do zarządzania i kontrolowanie dostępu do kluczy szyfrowania używanych przez usługi w chmurze. Można przypisać uprawnienia do klucze dostępu do usług lub użytkowników za pomocą konta usługi Azure Active Directory.

Magazyn kluczy zwalnia organizacji trzeba skonfigurować, zastosować poprawki i obsługa sprzętowych modułów zabezpieczeń (HSM) i zarządzania kluczami oprogramowania. Użycie magazynu kluczy, należy zachować kontrolę. Microsoft nigdy nie widzi kluczy, a aplikacje nie ma bezpośredniego dostępu do nich. Można również zaimportować lub wygenerować klucze w sprzętowych modułów zabezpieczeń.

## <a name="next-steps"></a>Kolejne kroki

- [Przegląd zabezpieczeń platformy Azure](security-get-started-overview.md)
- [Przegląd zabezpieczeń sieci platformy Azure](security-network-overview.md)
- [Przegląd zabezpieczeń bazy danych platformy Azure](azure-database-security-overview.md)
- [Omówienie zabezpieczeń w maszynach wirtualnych platformy Azure](security-virtual-machines-overview.md)
- [Szyfrowanie danych w spoczynku](azure-security-encryption-atrest.md)
- [Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych](azure-security-data-encryption-best-practices.md)
