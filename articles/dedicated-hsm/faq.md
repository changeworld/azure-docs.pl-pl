---
title: Często zadawane pytania — sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące różnych tematów dotyczących sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 947dc6eb8060d2d229ee7984f719cb837e638490
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77045928"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Znajdź odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure w wersji dedykowanej sprzętowego modułu zabezpieczeń.

## <a name="the-basics"></a>Podstawowe informacje

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Pyt.: co to jest sprzętowego modułu zabezpieczeń (HSM)?

Sprzętowego modułu zabezpieczeń (HSM) to fizyczne urządzenie obliczeniowe używane do ochrony i zarządzanie kluczami kryptograficznymi. Kluczy przechowywanych w sprzętowych modułach zabezpieczeń może służyć do operacji kryptograficznych. Materiał klucza, bezpiecznie pozostaje odporne na uszkodzenia, manipulacji sprzętowych modułów. Dopuszcza tylko w ramach sprzętowego modułu zabezpieczeń uwierzytelnianie i autoryzowanie aplikacji do używania kluczy. Dane dotyczące klucza nigdy nie opuszczają granic zabezpieczeń modułu HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>P: co to jest oferta dedykowanego modułu HSM platformy Azure?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest oparte na chmurze Usługa udostępniająca sprzętowych modułów zabezpieczeń hostowana w centrach danych platformy Azure, które były bezpośrednio podłączone do sieci wirtualnej klienta. Te moduły HSM są urządzenia sieciowe dedykowane (firmy firmy Gemalto SafeNet sieciowych przez moduł HSM 7 Model A790). Są one wdrażane bezpośrednio do klientów przestrzeń prywatnych adresów IP i firmy Microsoft nie ma dostępu do usług kryptograficznych funkcji sprzętowych modułów zabezpieczeń. Tylko klient ma pełną administracyjnych i kryptograficznych kontrolę nad tymi urządzeniami. Klienci są odpowiedzialni za zarządzanie urządzeniem i będą oni mogli uzyskać Dzienniki aktywności pełnej bezpośrednio ze swoich urządzeń. Dedykowanych sprzętowych modułów zabezpieczeń pomóc klientom spełniają wymagania zgodności/prawne, takie jak FIPS 140-2 Level 3, HIPAA, PCI-DSS i eIDAS i wiele innych.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>P: jaki sprzęt jest używany do dedykowanego modułu HSM?

Firma Microsoft połączyła się z usługą firmy Gemalto w celu dostarczenia dedykowanej usługi modułu HSM platformy Azure. Określone urządzenie to [SafeNet Luna Network HSM 7 model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). To urządzenie nie tylko zapewnia FIPS 140-2 Level 3 zweryfikowany w oprogramowania układowego, ale oferuje także o małych opóźnieniach, o wysokiej wydajności i dużej pojemności za pośrednictwem 10 partycji. 

### <a name="q-what-is-an-hsm-used-for"></a>P: co to jest używany przez moduł HSM?

Moduły HSM służą do przechowywania kluczy kryptograficznych używanych na potrzeby funkcji kryptograficznych, takich jak protokół Secure Socket Layer (SSL), szyfrowanie danych, infrastruktura kluczy publicznych (PKI, Public Key Infrastructure), zarządzanie prawami cyfrowymi (DRM, Digital Rights Management) i podpisywanie dokumentów.

### <a name="q-how-does-dedicated-hsm-work"></a>P: jak działa w wersji dedykowanej przez moduł HSM?

Klienci mogą aprowizować sprzętowych modułów zabezpieczeń w określonych regionach przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Klient określa, jakie sieci wirtualnej, sprzętowe moduły zabezpieczeń spowoduje nawiązanie połączenia i po aprowizacji modułów HSM będą dostępne w wyznaczonym podsieci w przypisanych adresów IP w prywatnej przestrzeni adresowej IP klienta. Następnie klienci mogą się łączyć sprzętowych modułów zabezpieczeń do zarządzania urządzeniem przy użyciu protokołu SSH i administrowanie, konfigurowanie połączeń klienckich przez moduł HSM, zainicjować modułów HSM, tworzenie partycji, zdefiniuj i przypisz ról, takich jak urzędnika partycji, ds. usług kryptograficznych i kryptograficznych użytkownika. Następnie klient użyje firmy Gemalto udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie do wykonywania operacji kryptograficznych z poziomu ich aplikacji.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>P: jakie oprogramowanie jest dostarczane przy użyciu usługi w wersji dedykowanej przez moduł HSM?

Firmy Gemalto dostarcza całe oprogramowanie dla urządzenia sprzętowego modułu zabezpieczeń po aprowizacji przez firmę Microsoft. Oprogramowanie jest dostępne w [portalu obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania. Obsługiwane oprogramowanie klienckie jest w wersji 7,2, która jest zgodna z zweryfikowaną wersją oprogramowania układowego FIPS 140-2 Level 3 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>P: czy dedykowany moduł HSM platformy Azure oferuje uwierzytelnianie oparte na hasłach i PED?

W tej chwili dedykowany moduł HSM platformy Azure udostępnia tylko sprzętowych modułów zabezpieczeń z uwierzytelnianiem opartym na hasłach.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>P: czy usługa Azure dedykowany Host HSM sprzętowych modułów zabezpieczeń dla mnie?

Firma Microsoft oferuje firmy Gemalto SafeNet Luna Network HSM za pośrednictwem dedykowanej usługi HSM i nie może hostować żadnych urządzeń udostępnianych przez klienta.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>P: czy dedykowany moduł HSM platformy Azure obsługuje funkcje płatności (PIN/EFT)?

Dedykowana usługa HSM platformy Azure używa urządzeń SafeNet Luna Network HSM 7 (model A790). Te urządzenia nie obsługują funkcji specyficznych dla modułu HSM (np. numeru PIN lub EFT) lub certyfikatów. Jeśli chcesz, aby usługa dedykowanego modułu HSM platformy Azure obsługiwała sprzętowych modułów zabezpieczeń płatności w przyszłości, Przekaż opinię do przedstawiciela konta Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>P: które regiony platformy Azure są dostępne w ramach dedykowanego modułu HSM?

Począwszy od dnia 2019 marca, dedykowany moduł HSM jest dostępny w 14 regionach wymienionych poniżej. Dalsze regiony są planowane i można je omówić za pośrednictwem przedstawiciela konta Microsoft.

* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Południowo-środkowe stany USA
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Indie Środkowe
* Indie Południowe
* Japonia Wschodnia
* Japonia Zachodnia
* Europa Północna
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia

## <a name="interoperability"></a>Współdziałanie

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>P: jak mojej aplikacji podłączyć do modułu HSM w wersji dedykowanej?

Udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie firmy Gemalto służy do wykonywania operacji kryptograficznych z poziomu aplikacji. Oprogramowanie jest dostępne w [portalu obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>P: czy można aplikacji nawiązać połączenie w wersji dedykowanej sprzętowego modułu zabezpieczeń z innej sieci Wirtualnej w lub w wielu regionach?

Tak, aby nawiązać łączność między sieciami wirtualnymi, należy użyć [komunikacji równorzędnej](../virtual-network/virtual-network-peering-overview.md) sieci wirtualnych w obrębie regionu. W przypadku łączności między regionami należy użyć [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>P: czy przy użyciu sprzętowych modułów zabezpieczeń lokalnych można synchronizować w wersji dedykowanej przez moduł HSM?

Tak, można synchronizować lokalne sprzętowych modułów zabezpieczeń z dedykowanego sprzętowego modułu zabezpieczeń. Połączenie [sieci VPN typu punkt-punkt lub punkt-lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md) może służyć do nawiązywania połączeń z siecią lokalną.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>P: czy można zaszyfrować dane używane przez inne usługi platformy Azure przy użyciu kluczy przechowywanych w wersji dedykowanej module HSM?

Nie. Azure w wersji dedykowanej Moduły HSM są dostępne jedynie z wewnątrz sieci wirtualnej.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>P: czy można importować klucze z istniejącego lokalnego modułu HSM do dedykowanego modułu HSM?

Tak, jeśli masz lokalne sprzętowych modułów zabezpieczeń firmy Gemalto SafeNet. Istnieje wiele metod. Zapoznaj się z dokumentacją sprzętowego modułu zabezpieczeń firmy Gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>P: jakie systemy operacyjne są obsługiwane przez oprogramowanie klienckie dedykowany przez moduł HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Wirtualnej: VMware, funkcji Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Pytanie: jak skonfigurować aplikację klienta do utworzenia konfiguracji o wysokiej dostępności przy użyciu wielu partycji z wieloma sprzętowych modułów zabezpieczeń?

Aby uzyskać wysoką dostępność, należy skonfigurować konfigurację aplikacji klienta przez moduł HSM do używania partycji z każdego modułu HSM. Zapoznaj się z dokumentacją oprogramowania sprzętowego modułu zabezpieczeń firmy Gemalto klienta.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>P: jakie mechanizmy uwierzytelniania są obsługiwane przez dedykowany przez moduł HSM?

Urządzenia SafeNet sieciowych przez moduł HSM 7 (model A790) korzysta z platformy Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń i obsługują uwierzytelniania opartego na hasłach.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>P: jakie oprogramowanie klienckie zestawów SDK, interfejsów API, jest dostępna za pomocą dedykowanego przez moduł HSM?

PKCS nr 11, języka Java (JCA/JCE), CAPI firmy Microsoft i CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>P: czy można przeprowadzić importowania/migrację klucze w sprzętowych modułach zabezpieczeń Luna 5/6 do sprzętowych modułów zabezpieczeń platformy Azure w wersji dedykowanej?

Tak. Zapoznaj się z przewodnikiem migracji firmy Gemalto. 

## <a name="using-your-hsm"></a>Za pomocą modułu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>P: Jak zdecydować, czy ma być używany w usłudze Azure Key Vault lub Azure w wersji dedykowanej modułu HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest odpowiednim wyborem dla przedsiębiorstw migracji do aplikacji platformy Azure w środowisku lokalnym, które używają sprzętowych modułów zabezpieczeń. Dedykowanych sprzętowych modułów zabezpieczeń istnieje możliwość przeprowadzania migracji aplikacji przy minimalnych zmianach. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą używać dedykowanego sprzętowego modułu zabezpieczeń. Ogólnie rzecz biorąc, porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak Application gateway lub traffic manager dla bez kluczy protokołu SSL usług AD CS (usługi certyfikatów Active Directory), lub podobne narzędzia infrastruktury kluczy publicznych, narzędzia/aplikacje używane do podpisywania dokumentów, podpisywanie kodu lub SQL Server (IaaS), skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami). Usługa Azure Key Vault jest odpowiedni dla aplikacji "urodzonych w chmury" lub szyfrowania na scenariusze rest, w którym dane klientów są przetwarzane przez PaaS (platforma jako usługa) lub scenariuszy SaaS (oprogramowanie jako usługa), takie jak klucz klienta usługi Office 365, Azure Information Protection , Usługa azure Disk Encryption, szyfrowanie usługi Azure Data Lake Store przy użyciu zarządzanych przez klienta klucza szyfrowania usługi Azure Storage za pomocą klienta zarządzane klucza i SQL Azure za pomocą klienta zarządzanego klucza.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>P: jakie najlepszych scenariuszy użycia własnych platformy Azure w wersji dedykowanej w module HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest najbardziej odpowiednie dla scenariuszy migracji. Oznacza to, że w przypadku migracji aplikacji lokalnych na platformę Azure, które już używają sprzętowych modułów zabezpieczeń. Zapewnia to możliwość prostego Migrowanie na platformę Azure przy minimalnych zmianach w aplikacji. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą być używane przez sprzętowy moduł zabezpieczeń w wersji dedykowanej. Ogólnie rzecz biorąc porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak:

* Application gateway lub traffic manager dla protokołu SSL bez kluczy
* Usług AD CS (usługi certyfikatów Active Directory)
* Podobne narzędzia infrastruktury kluczy publicznych
* Narzędzia/aplikacje używane do podpisywania dokumentów
* Podpisywanie kodu
* Program SQL Server (IaaS) skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>P: czy dedykowana sprzętowego modułu zabezpieczeń można za pomocą klucz klienta usługi Office 365, Azure Information Protection, Azure Data Lake Store, szyfrowanie dysków, szyfrowanie usługi Azure Storage, Azure SQL TDE?

Nie. Dedykowany przez moduł HSM jest aprowizowany bezpośrednio w prywatnej przestrzeni adresowej IP klienta dlatego nie jest dostępny dla innych usług platformy Azure lub programu Microsoft.

## <a name="administration-access-and-control"></a>Administracja, dostępu i kontroli

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Pytanie: czy klient może uzyskać pełnej wyłączną kontrolę nad sprzętowych modułów zabezpieczeń z dedykowanych sprzętowych modułów zabezpieczeń?

Tak. Każdego urządzenia sprzętowego modułu zabezpieczeń jest pełni dedykowanego jednego pojedynczego klienta, i nikt inny nie ma kontroli administracyjnej po aprowizacji i zmieniono hasło administratora.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>P: jakie poziom dostępu firmy Microsoft ma do mojego modułu HSM?

Microsoft nie ma dowolną kontrolkę kryptograficznych lub administracyjnych w ramach sprzętowego modułu zabezpieczeń. Firma Microsoft jeszcze monitor poziom dostępu za pośrednictwem portu szeregowego połączenia, aby pobierać podstawowe dane telemetryczne, np. temperaturę, jak i składnika kondycji. Dzięki temu firma Microsoft zapewni powiadomień z wyprzedzeniem o problemów z kondycją. W razie potrzeby klient może wyłączyć to konto.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>P: co to jest konto "tenantadmin" używane przez firmę Microsoft, które jest używane przez użytkownika administratora w usłudze SafeNet sprzętowych modułów zabezpieczeń?

Urządzenie HSM jest dostarczane z domyślnym użytkownikiem administratora z normalnym hasłem domyślnym. Firma Microsoft nie chciała używać domyślnych haseł, gdy wszystkie urządzenia znajdują się w puli oczekującej na zainicjowanie obsługi przez klientów. Nie spełnia to rygorystycznych wymagań w zakresie zabezpieczeń. Z tego powodu ustawimy silne hasło, które jest odrzucane w czasie aprowizacji. Ponadto podczas aprowizacji należy utworzyć nowego użytkownika w roli administratora o nazwie "tenantadmin". Ten użytkownik ma domyślne hasło i klienci zmieniają ten sposób jako pierwszą akcję podczas pierwszego logowania na nowo zainicjowanym urządzeniu. Ten proces zapewnia wysoki poziom bezpieczeństwa i utrzymuje nasze obietnice związane z wyłączną kontrolą administracyjną dla naszych klientów. Należy zauważyć, że użytkownik "tenantadmin" może być używany do resetowania hasła użytkownika administratora, jeśli klient woli korzystać z tego konta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>P: czy można firmy Microsoft lub uprasza klucze dostępu firmy Microsoft w mojej przez sprzętowy moduł zabezpieczeń w wersji dedykowanej?

Nie. Microsoft nie ma dostępu do kluczy przechowywanych w klienta przydzielony dedykowany przez moduł HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>P: czy mogę uaktualnić oprogramowania i oprogramowania układowego w sprzętowych modułach zabezpieczeń przydzielony do mnie?

Aby uzyskać najlepszą pomoc techniczną, firma Microsoft zaleca z uaktualniania oprogramowania i oprogramowania układowego w ramach sprzętowego modułu zabezpieczeń. Jednak klient ma pełną kontrolę administracyjną tym uaktualniania oprogramowania i oprogramowania układowego w przypadku określonych funkcji w wersji oprogramowania układowego w różnych. Przed wprowadzeniem zmian należy zrozumieć konsekwencje, jak może to być na przykład wpływ na stan zweryfikowany FIPS. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>P: jak zarządzać w wersji dedykowanej przez moduł HSM?

Możesz zarządzać dedykowanych sprzętowych modułów zabezpieczeń, uzyskując dostęp do nich przy użyciu protokołu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>P: jak zarządzać partycji w module HSM w wersji dedykowanej?

Oprogramowanie klienckie sprzętowego modułu zabezpieczeń firmy Gemalto służy do zarządzania sprzętowych modułów zabezpieczeń oraz partycji.

### <a name="q-how-do-i-monitor-my-hsm"></a>P: jak monitorować Moje przez moduł HSM?

Klient ma pełny dostęp do dzienników aktywności przez moduł HSM za pośrednictwem usługi syslog i SNMP. Klienta należy ustawić serwera syslog lub serwer SNMP, aby odbierać dzienniki lub zdarzeń z modułów HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>P: czy z dedykowanego HSM można pobrać dziennika pełnego dostępu do wszystkich operacji przez moduł HSM?

Tak. Możesz wysłać dzienniki z urządzenia przez moduł HSM do serwera syslog

## <a name="high-availability"></a>Wysoka dostępność

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>P: czy jest możliwe skonfigurowanie wysokiej dostępności w tym samym regionie lub w wielu regionach?

Tak. Konfiguracja wysokiej dostępności i konfiguracji są wykonywane w oprogramowaniu klienckim przez moduł HSM, które są dostarczane przez firmy Gemalto. Sprzętowych modułów zabezpieczeń z tej samej sieci wirtualnej lub innych sieci wirtualnych w tym samym regionie lub w różnych regionach lub lokalnych sprzętowych modułów zabezpieczeń podłączonych do sieci wirtualnej przy użyciu sieci VPN typu lokacja-lokacja lub punktu typu punkt-punkt można dodać do tej samej konfiguracji wysokiej dostępności. Należy zauważyć, że to synchronizuje tylko materiał klucza i nie konkretne elementy konfiguracji, takie jak role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Czy można dodać sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu dedykowanego modułu HSM platformy Azure?

Tak. Muszą spełniać wymagania wysokiej dostępności dla SafeNet Luna sieciowych przez moduł HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>P: Czy można dodać Luna 5/6 sprzętowych modułów zabezpieczeń z sieci lokalnych do grupy o wysokiej dostępności z dedykowanym modułem HSM platformy Azure?

Nie.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>P: jak dużo sprzętowych modułów zabezpieczeń można dodać do tej samej konfiguracji o wysokiej dostępności z jednego wniosku?

16 Członkowie grupy HA mają niezrównane testy z pełną przepustowością.

## <a name="support"></a>Pomoc techniczna

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>P: jaka jest umowa SLA dla usługi w wersji dedykowanej przez moduł HSM?

Dla dedykowanej usługi HSM nie jest dostępna żadna konkretna gwarancja czasu przestoju. Microsoft zapewni poziomu dostępu do sieci na urządzeniu i dlatego stosowanie standardowych umów SLA sieci platformy Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>P: jak Moduły HSM są używane w platformie Azure w wersji dedykowanej chronione przez moduł HSM?

Centra danych platformy Azure są sterowane rozbudowane zabezpieczeniom fizycznym i procedurach. Oprócz tego dedykowanych sprzętowych modułów zabezpieczeń znajdują się w obszarze dalsze ograniczony dostęp do centrum danych. Te obszary mają dodatkowe fizyczne metody kontroli dostępu i kamery nadzoru w celu zwiększenia poziomu bezpieczeństwa.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>P: co się dzieje w przypadku naruszenia zabezpieczeń lub sprzętu, manipulowania zdarzenia?

Dedykowana usługa sprzętowego modułu zabezpieczeń używa urządzenia SafeNet sieciowych przez moduł HSM 7. Następujące urządzenia obsługują wykrywania manipulacji fizycznymi i logicznymi. Jeśli nigdy nie jest zdarzeniem odporne Moduły HSM są automatycznie zeroized.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>P: jak zapewnić klucze w sprzętowych modułach zabezpieczeń Moje dedykowanego nie są utracone z powodu błędu lub atak złośliwe oprogramowanie

Zdecydowanie zaleca się używać urządzenia kopii zapasowej przez moduł HSM do środowiska lokalnego do wykonywania zwykłych okresowych kopii zapasowych z modułów HSM do odzyskiwania po awarii. Należy połączyć urządzenia kopii zapasowej przez sprzętowy moduł zabezpieczeń stacji roboczej w środowisku lokalnym przy użyciu połączenia sieci VPN peer-to-peer lub lokacja lokacja.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>P: jak uzyskać pomoc techniczną dotyczącą dedykowany przez moduł HSM?

Pomoc techniczna jest świadczona przez firmę Microsoft i firmy Gemalto.  Jeśli masz problem z dostępem do sprzętu lub sieci, Zgłoś żądanie pomocy technicznej w firmie Microsoft i jeśli masz problem z konfiguracją modułu HSM, oprogramowaniem i programowaniem aplikacji, Zgłoś żądanie pomocy technicznej z firmy Gemalto. Jeśli masz nieokreślony problem, Zgłoś żądanie pomocy technicznej z firmą Microsoft, a następnie firmy Gemalto je w razie potrzeby. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>P: Jak mogę uzyskać oprogramowania klienckiego, dokumentacji i dostępu do wskazówek dotyczących integracji dla modułu HSM SafeNet Luna 7?

Po zarejestrowaniu się w usłudze zostanie dostarczony identyfikator firmy Gemalto klienta, który umożliwia rejestrację w portalu obsługi klienta firmy Gemalto. Umożliwi to dostęp do całego oprogramowania i dokumentacji oraz włączanie żądań pomocy technicznej bezpośrednio z firmy Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Pyt.: Jeśli nie znaleziono luki w zabezpieczeniach i patch jest wydane przez firmy Gemalto, który jest odpowiedzialny za uaktualnianie/poprawianie systemu operacyjnego i oprogramowania układowego?

Microsoft nie ma możliwości podłączenia do sprzętowych modułów zabezpieczeń przydzielone do klientów. Klienci, należy uaktualnić i stosowanie poprawek do ich sprzętowych modułów zabezpieczeń.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>P: co zrobić, jeśli chcę uruchomić ponownie moduł HSM?

Moduł HSM ma zainstalowaną opcję ponownego uruchamiania wiersza polecenia, jednak często występują problemy z ponownym uruchomieniem systemu. z tego powodu zaleca się ponowne uruchomienie, które powoduje zgłoszenie żądania pomocy technicznej z firmą Microsoft w celu fizycznego ponownego uruchomienia urządzenia. 

## <a name="cryptography-and-standards"></a>Standardy i kryptografii

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>P: czy bezpieczny do przechowywania kluczy szyfrowania dla najważniejszych danych w wersji dedykowanej module HSM?

Tak, przez sprzętowy moduł zabezpieczeń w wersji dedykowanej obsługuje urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>P: jakie klucze kryptograficzne i algorytmy są obsługiwane przez dedykowany przez moduł HSM?

Dedykowane urządzenia przepisami SafeNet sieciowych przez moduł HSM 7 usługi sprzętowego modułu zabezpieczeń. Obsługują szeroką gamę typy kluczy kryptograficznych i algorytmów, w tym: Obsługa pełnego Suite B

* Asymetryczny:
  * RSA
  * DSA
  * Grupa Diffie'ego Hellmana
  * Krzywej eliptycznej
  * Kryptografia (ECDSA ECDH, Ed25519, ECIES) za pomocą nazwanych, zdefiniowanych przez użytkownika i krzywych Brainpool, KCDSA
* Symetryczne:
  * USŁUGA GCM AES
  * Potrójny algorytm DES
  * DES
  * ARIA, INICJATORA
  * RC2
  * RC4
  * RC5
  * CAST
  * Skrót/wiadomości szyfrowanego/HMAC: Algorytm SHA-1, SHA-2, SM3
  * Wyprowadzania klucza: Tryb licznika SP800 108
  * Zawijanie kluczy: SP800-38F
  * Generowanie liczby losowej: Zatwierdzone FIPS 140-2 zgodnych z BSI DRG.4 DRBG (tryb kont SP 800-90.)

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>P: jest dedykowany przez moduł HSM FIPS który zweryfikowane 140-2 Level 3?

Tak. Dedykowana usługa HSM Inicjuje obsługę urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>P: co muszę zrobić aby upewnić się, czy działają w wersji dedykowanej przez moduł HSM w trybie FIPS 140-2 Level 3 zweryfikować tryb?

Usługę HSM w wersji dedykowanej obsługuje urządzenia SafeNet Luna sieciowych przez moduł HSM 7. Użyj tych urządzeń ze standardem FIPS 140-2 poziom 3 zweryfikować sprzętowych modułów zabezpieczeń. Konfiguracja domyślna wdrożone, systemu operacyjnego i oprogramowania układowego są również zweryfikowane w trybie FIPS. Nie trzeba podejmować żadnych działań ze standardem FIPS 140-2 Level 3 zgodności.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>P: jak klient zapewnia, że gdy moduł HSM jest niezainicjowany, cały materiał klucza jest wyczyszczony?

Przed zażądaniem anulowania obsługi, klient musi zeroized modułem HSM, za pomocą dostępne narzędzia klienckie sprzętowego modułu zabezpieczeń firmy Gemalto.

## <a name="performance-and-scale"></a>Wydajność i skalowanie

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>P: jak dużo operacje kryptograficzne są obsługiwane z dedykowanego sprzętowego modułu zabezpieczeń na sekundę?

Dedykowane urządzenia przepisami SafeNet sieciowych przez moduł HSM 7 sprzętowego modułu zabezpieczeń (model A790). Poniżej przedstawiono podsumowanie maksymalną wydajność niektórych operacji: 

* Szyfrowania RSA 2048: 10 000 transakcji na sekundę
* ECC P256: 20 000 transakcji na sekundę
* AES-GCM: 17,000 transakcji na sekundę

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>P: jak dużo partycje mogą być tworzone w wersji dedykowanej module HSM?

A790 użyty model SafeNet Luna HSM 7 obejmuje licencję na 10 partycji w kosztach usługi. Na urządzeniu obowiązuje limit 100 partycji i dodanie partycji do tego limitu spowoduje policzenie dodatkowych kosztów licencjonowania i wymaga instalacji nowego pliku licencji na urządzeniu.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>P: jak dużo klucze będą również obsługiwane w dedykowanej przez moduł HSM?

Maksymalna liczba kluczy jest funkcją dostępnej pamięci. A790 używany model SafeNet Luna 7 ma 32MB pamięci. Poniższe liczby są również stosowane do par kluczy, jeśli są używane klucze asymetryczne.

* SZYFROWANIA RSA 2048 - 19,000
* ECC P256 - 91,000

Pojemność różnią się w zależności od określonych kluczowych atrybutów w szablonie generowania kluczy i liczby partycji.
