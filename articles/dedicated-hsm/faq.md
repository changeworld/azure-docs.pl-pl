---
title: Często zadawane pytania — sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące różnych tematów dotyczących sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 12/11/2018
ms.author: barclayn
ms.openlocfilehash: 5ea98f96fe10337e905270cf5da4847d825d6eb4
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107807"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Znajdź odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure w wersji dedykowanej sprzętowego modułu zabezpieczeń.

## <a name="the-basics"></a>Podstawowe informacje

### <a name="q-what-is-a-hardware-security-module-hsm"></a>PYT.: Co to jest sprzętowego modułu zabezpieczeń (HSM)?

Sprzętowego modułu zabezpieczeń (HSM) to fizyczne urządzenie obliczeniowe używane do ochrony i zarządzanie kluczami kryptograficznymi. Kluczy przechowywanych w sprzętowych modułach zabezpieczeń może służyć do operacji kryptograficznych. Materiał klucza, bezpiecznie pozostaje odporne na uszkodzenia, manipulacji sprzętowych modułów. Dopuszcza tylko w ramach sprzętowego modułu zabezpieczeń uwierzytelnianie i autoryzowanie aplikacji do używania kluczy. Dane dotyczące klucza nigdy nie opuszczają granic zabezpieczeń modułu HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>PYT.: Co to jest oferta sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest oparte na chmurze Usługa udostępniająca sprzętowych modułów zabezpieczeń hostowana w centrach danych platformy Azure, które były bezpośrednio podłączone do sieci wirtualnej klienta. Te moduły HSM są urządzenia sieciowe dedykowane (firmy firmy Gemalto SafeNet sieciowych przez moduł HSM 7 Model A790). Są one wdrażane bezpośrednio do klientów przestrzeń prywatnych adresów IP i firmy Microsoft nie ma dostępu do usług kryptograficznych funkcji sprzętowych modułów zabezpieczeń. Tylko klient ma pełną administracyjnych i kryptograficznych kontrolę nad tymi urządzeniami. Klienci są odpowiedzialni za zarządzanie urządzeniem i będą oni mogli uzyskać Dzienniki aktywności pełnej bezpośrednio ze swoich urządzeń. Dedykowanych sprzętowych modułów zabezpieczeń pomóc klientom spełniają wymagania zgodności/prawne, takie jak FIPS 140-2 Level 3, HIPAA, PCI-DSS i eIDAS i wiele innych.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>PYT.: Jaki sprzęt jest używany dla dedykowanych przez moduł HSM?

Firma Microsoft nawiązała współpracę z firmy Gemalto do świadczenia usług platformy Azure w wersji dedykowanej w module HSM. Określone urządzenie, używany jest [SafeNet Luna sieciowych przez moduł HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). To urządzenie nie tylko zapewnia FIPS 140-2 Level 3 zweryfikowany w oprogramowania układowego, ale oferuje także o małych opóźnieniach, o wysokiej wydajności i dużej pojemności za pośrednictwem 10 partycji. 

### <a name="q-what-is-an-hsm-used-for"></a>PYT.: Do czego są używane moduły HSM?

Moduły HSM służą do przechowywania kluczy kryptograficznych używanych na potrzeby funkcji kryptograficznych, takich jak protokół Secure Socket Layer (SSL), szyfrowanie danych, infrastruktura kluczy publicznych (PKI, Public Key Infrastructure), zarządzanie prawami cyfrowymi (DRM, Digital Rights Management) i podpisywanie dokumentów.

### <a name="q-how-does-dedicated-hsm-work"></a>PYT.: W jaki sposób działa dedykowany moduł HSM?

Klienci mogą aprowizować sprzętowych modułów zabezpieczeń w określonych regionach przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Klient określa, jakie sieci wirtualnej, sprzętowe moduły zabezpieczeń spowoduje nawiązanie połączenia i po aprowizacji modułów HSM będą dostępne w wyznaczonym podsieci w przypisanych adresów IP w prywatnej przestrzeni adresowej IP klienta. Następnie klienci mogą się łączyć sprzętowych modułów zabezpieczeń do zarządzania urządzeniem przy użyciu protokołu SSH i administrowanie, konfigurowanie połączeń klienckich przez moduł HSM, zainicjować modułów HSM, tworzenie partycji, zdefiniuj i przypisz ról, takich jak urzędnika partycji, ds. usług kryptograficznych i kryptograficznych użytkownika. Następnie klient użyje firmy Gemalto udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie do wykonywania operacji kryptograficznych z poziomu ich aplikacji.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>PYT.: Jakie oprogramowanie jest dostarczana z usługi w wersji dedykowanej przez moduł HSM?

Firmy Gemalto dostarcza całe oprogramowanie dla urządzenia sprzętowego modułu zabezpieczeń po aprowizacji przez firmę Microsoft. Oprogramowanie jest dostępne pod adresem [portal obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania. Oprogramowanie klienckie obsługiwana jest wersja 7.2, która jest zgodna z trybem FIPS 140-2 Level 3 zweryfikowane-wersja oprogramowania układowego 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>PYT.: Azure w wersji dedykowanej w module HSM oferuje uwierzytelniania opartego na hasłach i na podstawie PED?

W tej chwili sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej zawiera sprzętowych modułów zabezpieczeń wyłącznie przy użyciu uwierzytelniania opartego na hasłach.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>PYT.: Sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej obsługującym Moje sprzętowych modułów zabezpieczeń dla mnie?

Firma Microsoft tylko oferuje HSM sieci firmy Gemalto SafeNet Luna, za pośrednictwem usługi przez sprzętowy moduł zabezpieczeń w wersji dedykowanej i nie może obsługiwać żadnych urządzeń dostarczone do klienta.

### <a name="q-does-azure-dedicated-hsm-support-pinetf-features-or-meet-pci-hsm-v1-or-v2-certification-requirements"></a>PYT.: Sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej obsługuje funkcje kodu PIN/ETF lub spełniać wymagania certyfikacji v1 lub v2 PCI przez moduł HSM

Usługa Azure w wersji dedykowanej w module HSM używa urządzeń SafeNet Luna sieciowych przez moduł HSM 7 (model A790). Te urządzenia, które nie obsługują funkcji określonej przez moduł HSM płatności (na przykład numer PIN lub ETF) lub certyfikatów. Jeśli potrzebujesz sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej usługi do obsługi płatności w przyszłości sprzętowych modułów zabezpieczeń, Prześlij opinię poniżej.

## <a name="interoperability"></a>Współdziałanie

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>PYT.: Jak mojej aplikacji podłączyć do modułu HSM w wersji dedykowanej?

Udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie firmy Gemalto służy do wykonywania operacji kryptograficznych z poziomu aplikacji. Oprogramowanie jest dostępne pod adresem [portal obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>PYT.: Aplikacja z można połączyć się przez sprzętowy moduł zabezpieczeń w wersji dedykowanej innej sieci Wirtualnej w lub w wielu regionach?

Tak, musisz użyć [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) w regionie w celu ustanowienia połączenia między sieciami wirtualnymi. Łączność między regionami, należy użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>PYT.: Czy można synchronizować przez sprzętowy moduł zabezpieczeń w wersji dedykowanej przy użyciu sprzętowych modułów zabezpieczeń w środowisku lokalnym

Tak, można synchronizować lokalne sprzętowych modułów zabezpieczeń z dedykowanego sprzętowego modułu zabezpieczeń. [Protokół Point-to-Point sieci VPN lub punkt lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md) łączność, może służyć do nawiązywania łączności z Twoją siecią lokalną.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>PYT.: Czy można zaszyfrować dane używane przez inne usługi platformy Azure przy użyciu kluczy przechowywanych w wersji dedykowanej module HSM?

Nie. Azure w wersji dedykowanej Moduły HSM są dostępne jedynie z wewnątrz sieci wirtualnej.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>PYT.: Można importować klucze z istniejącego lokalnego modułu HSM do dedykowanego modułu HSM?

Tak, jeśli masz lokalne sprzętowych modułów zabezpieczeń firmy Gemalto SafeNet. Istnieje wiele metod. Zapoznaj się z dokumentacją sprzętowego modułu zabezpieczeń firmy Gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>PYT.: Jakie systemy operacyjne są obsługiwane przez oprogramowanie klienckie dedykowany przez moduł HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Wirtualne: VMware, funkcji Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>PYT.: Jak skonfigurować aplikację klienta do utworzenia konfiguracji o wysokiej dostępności przy użyciu wielu partycji z wieloma sprzętowych modułów zabezpieczeń?

Aby uzyskać wysoką dostępność, należy skonfigurować konfigurację aplikacji klienta przez moduł HSM do używania partycji z każdego modułu HSM. Zapoznaj się z dokumentacją oprogramowania sprzętowego modułu zabezpieczeń firmy Gemalto klienta.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>PYT.: Jakie mechanizmy uwierzytelniania są obsługiwane przez dedykowany przez moduł HSM?

Urządzenia SafeNet sieciowych przez moduł HSM 7 (model A790) korzysta z platformy Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń i obsługują uwierzytelniania opartego na hasłach.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>PYT.: Jakie zestawy SDK, interfejsów API klienta oprogramowanie jest dostępne za pomocą dedykowanego przez moduł HSM?

PKCS nr 11, języka Java (JCA/JCE), CAPI firmy Microsoft i CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>PYT.: Czy mogę import/zmigrować klucze w sprzętowych modułach zabezpieczeń Luna 5/6 do sprzętowych modułów zabezpieczeń platformy Azure w wersji dedykowanej?

Tak. Zapoznaj się z przewodnikiem migracji firmy Gemalto. 

## <a name="using-your-hsm"></a>Za pomocą modułu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>PYT.: Jak zdecydować, czy ma być używany w usłudze Azure Key Vault lub Azure w wersji dedykowanej modułu HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest odpowiednim wyborem dla przedsiębiorstw migracji do aplikacji platformy Azure w środowisku lokalnym, które używają sprzętowych modułów zabezpieczeń. Dedykowanych sprzętowych modułów zabezpieczeń istnieje możliwość przeprowadzania migracji aplikacji przy minimalnych zmianach. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą używać dedykowanego sprzętowego modułu zabezpieczeń. Ogólnie rzecz biorąc, porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak Application gateway lub traffic manager dla bez kluczy protokołu SSL usług AD CS (usługi certyfikatów Active Directory), lub podobne narzędzia infrastruktury kluczy publicznych, narzędzia/aplikacje używane do podpisywania dokumentów, podpisywanie kodu lub SQL Server (IaaS), skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami). Usługa Azure Key Vault jest odpowiedni dla aplikacji "urodzonych w chmury" lub szyfrowania na scenariusze rest, w którym dane klientów są przetwarzane przez PaaS (platforma jako usługa) lub scenariuszy SaaS (oprogramowanie jako usługa), takie jak klucz klienta usługi Office 365, Azure Information Protection , Usługa azure Disk Encryption, szyfrowanie usługi Azure Data Lake Store przy użyciu zarządzanych przez klienta klucza szyfrowania usługi Azure Storage za pomocą klienta zarządzane klucza i SQL Azure za pomocą klienta zarządzanego klucza.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>PYT.: Jakie najlepszych scenariuszy użycia własnych platformy Azure w wersji dedykowanej w module HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest najbardziej odpowiednie dla scenariuszy migracji. Oznacza to, że w przypadku migracji aplikacji lokalnych na platformę Azure, które już używają sprzętowych modułów zabezpieczeń. Zapewnia to możliwość prostego Migrowanie na platformę Azure przy minimalnych zmianach w aplikacji. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą być używane przez sprzętowy moduł zabezpieczeń w wersji dedykowanej. Ogólnie rzecz biorąc porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak:

* Application gateway lub traffic manager dla protokołu SSL bez kluczy
* Usług AD CS (usługi certyfikatów Active Directory)
* Podobne narzędzia infrastruktury kluczy publicznych
* Narzędzia/aplikacje używane do podpisywania dokumentów
* Podpisywanie kodu
* Program SQL Server (IaaS) skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>PYT.: Czy dedykowanego modułu HSM można używać wraz z kluczem klienta usługi Office 365, usługą Azure Information Protection, usługą Azure Data Lake Store, usługą Disk Encryption, szyfrowaniem usługi Azure Storage lub szyfrowaniem TDE Azure SQL?

Nie. Dedykowany przez moduł HSM jest aprowizowany bezpośrednio w prywatnej przestrzeni adresowej IP klienta dlatego nie jest dostępny dla innych usług platformy Azure lub programu Microsoft.

## <a name="administration-access-and-control"></a>Administracja, dostępu i kontroli

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>PYT.: Klient pobieranie pełnej wyłączną kontrolę nad sprzętowych modułów zabezpieczeń z dedykowanych sprzętowych modułów zabezpieczeń?

Tak. Każdego urządzenia sprzętowego modułu zabezpieczeń jest pełni dedykowanego jednego pojedynczego klienta, i nikt inny nie ma kontroli administracyjnej po aprowizacji i zmieniono hasło administratora.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>PYT.: Poziom dostępu do firmy Microsoft ma do mojego modułu HSM?

Microsoft nie ma dowolną kontrolkę kryptograficznych lub administracyjnych w ramach sprzętowego modułu zabezpieczeń. Firma Microsoft jeszcze monitor poziom dostępu za pośrednictwem portu szeregowego połączenia, aby pobierać podstawowe dane telemetryczne, np. temperaturę, jak i składnika kondycji. Dzięki temu firma Microsoft zapewni powiadomień z wyprzedzeniem o problemów z kondycją. W razie potrzeby klient może wyłączyć to konto.

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>PYT.: Można firmy Microsoft lub uprasza klucze dostępu firmy Microsoft w mojej przez sprzętowy moduł zabezpieczeń w wersji dedykowanej?

Nie. Microsoft nie ma dostępu do kluczy przechowywanych w klienta przydzielony dedykowany przez moduł HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>PYT.: Czy mogę uaktualnić oprogramowania i oprogramowania układowego w sprzętowych modułach zabezpieczeń przydzielony do mnie

Aby uzyskać najlepszą pomoc techniczną, firma Microsoft zaleca z uaktualniania oprogramowania i oprogramowania układowego w ramach sprzętowego modułu zabezpieczeń. Jednak klient ma pełną kontrolę administracyjną tym uaktualniania oprogramowania i oprogramowania układowego w przypadku określonych funkcji w wersji oprogramowania układowego w różnych. Przed wprowadzeniem zmian należy rozumieć konsekwencje, zgodnie z tym można, na przykład efekt FIPS zweryfikować stan. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>PYT.: Jak zarządzać w wersji dedykowanej przez moduł HSM?

Możesz zarządzać dedykowanych sprzętowych modułów zabezpieczeń, uzyskując dostęp do nich przy użyciu protokołu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>PYT.: Jak zarządzać partycji w module HSM w wersji dedykowanej?

Oprogramowanie klienckie sprzętowego modułu zabezpieczeń firmy Gemalto służy do zarządzania sprzętowych modułów zabezpieczeń oraz partycji.

### <a name="q-how-do-i-monitor-my-hsm"></a>PYT.: Jak monitorować Moje przez moduł HSM?

Klient ma pełny dostęp do dzienników aktywności przez moduł HSM za pośrednictwem usługi syslog i SNMP. Klienta należy ustawić serwera syslog lub serwer SNMP, aby odbierać dzienniki lub zdarzeń z modułów HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>PYT.: Z dedykowanego modułu HSM można uzyskać dziennika pełnego dostępu do wszystkich operacji przez moduł HSM?

Tak. Możesz wysłać dzienniki z urządzenia przez moduł HSM do serwera syslog

## <a name="high-availability"></a>Wysoka dostępność

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>PYT.: Czy jest możliwe do skonfigurowania wysokiej dostępności, w tym samym regionie lub w wielu regionach?

Tak. Konfiguracja wysokiej dostępności i konfiguracji są wykonywane w oprogramowaniu klienckim przez moduł HSM, które są dostarczane przez firmy Gemalto. Sprzętowych modułów zabezpieczeń z tej samej sieci Wirtualnej lub innych sieci wirtualnych w tym samym regionie lub w wielu regionach lub w lokalnej sprzętowych modułów zabezpieczeń połączone z siecią Wirtualną przy użyciu lokacja lokacja lub sieci VPN typu punkt-punkt mogą być dodawane do tej samej konfiguracji o wysokiej dostępności.

### <a name="can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Czy mogę dodać sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej

Tak. Muszą spełniać wymagania wysokiej dostępności dla SafeNet Luna sieciowych przez moduł HSM 7.

### <a name="can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Czy mogę dodać Luna 5/6 sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej

Nie.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>PYT.: Ile sprzętowych modułów zabezpieczeń można dodać do tej samej konfiguracji o wysokiej dostępności z jednego wniosku?

16.

## <a name="support"></a>Pomoc techniczna

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>PYT.: Co to jest umowa SLA dla usługi w wersji dedykowanej przez moduł HSM?

W tej chwili nie ma umowy SLA podane dla usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń. Microsoft zapewni poziomu dostępu do sieci na urządzeniu i dlatego stosowanie standardowych umów SLA sieci platformy Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>PYT.: Jak są używane moduły HSM w platformy Azure w wersji dedykowanej chronione przez moduł HSM?

Centra danych platformy Azure są sterowane rozbudowane zabezpieczeniom fizycznym i procedurach. Oprócz tego dedykowanych sprzętowych modułów zabezpieczeń znajdują się w obszarze dalsze ograniczony dostęp do centrum danych. Te obszary mają dodatkowe fizyczne metody kontroli dostępu i kamery nadzoru w celu zwiększenia poziomu bezpieczeństwa.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>PYT.: Co się stanie w przypadku naruszenia zabezpieczeń lub sprzętu, manipulowania zdarzenia?

Dedykowana usługa sprzętowego modułu zabezpieczeń używa urządzenia SafeNet sieciowych przez moduł HSM 7. Następujące urządzenia obsługują wykrywania manipulacji fizycznymi i logicznymi. Jeśli nigdy nie jest zdarzeniem odporne Moduły HSM są automatycznie zeroized.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>PYT.: Jak zapewnić klucze w sprzętowych modułach zabezpieczeń Moje dedykowanego nie są utracone z powodu błędu lub atak złośliwe oprogramowanie

Zdecydowanie zaleca się używać urządzenia kopii zapasowej przez moduł HSM do środowiska lokalnego do wykonywania zwykłych okresowych kopii zapasowych z modułów HSM do odzyskiwania po awarii. Należy połączyć urządzenia kopii zapasowej przez sprzętowy moduł zabezpieczeń stacji roboczej w środowisku lokalnym przy użyciu połączenia sieci VPN peer-to-peer lub lokacja lokacja.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>PYT.: Jak uzyskać pomoc techniczną dotyczącą dedykowany przez moduł HSM?

Taki sam sposób możesz uzyskać pomoc techniczną dla wszystkich innych usług platformy Azure. Zespół pomocy technicznej Azure przekaże do działu pomocy technicznej firmy Gemalto zgodnie z potrzebami, w zależności od tego, w tym przypadku.

### <a name="q-how-do-i-get-access-to-dedicated-hsm-client-software-documentation-firmware-images"></a>PYT.: Jak uzyskać dostęp do oprogramowania klienckiego przez sprzętowy moduł zabezpieczeń w wersji dedykowanej, dokumentacji, obrazach oprogramowania układowego?

Klient musi skontaktować się bezpośrednio z firmy Gemalto, aby uzyskać dostęp do oprogramowania klienckiego przez moduł HSM, dokumentacji, obrazy systemu operacyjnego i oprogramowania układowego.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>PYT.: Jeśli nie znaleziono luki w zabezpieczeniach i patch został wydany przez firmy Gemalto, który jest odpowiedzialny za uaktualnianie/poprawianie systemu operacyjnego i oprogramowania układowego?

Microsoft nie ma możliwości podłączenia do sprzętowych modułów zabezpieczeń przydzielone do klientów. Klienci, należy uaktualnić i stosowanie poprawek do ich sprzętowych modułów zabezpieczeń.

## <a name="cryptography-and-standards"></a>Standardy i kryptografii

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>PYT.: Jest to bezpieczny do przechowywania kluczy szyfrowania dla najważniejszych danych w wersji dedykowanej module HSM?

Tak, przez sprzętowy moduł zabezpieczeń w wersji dedykowanej obsługuje urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>PYT.: Jakie klucze kryptograficzne i algorytmy są obsługiwane przez dedykowany przez moduł HSM?

Dedykowane urządzenia przepisami SafeNet sieciowych przez moduł HSM 7 usługi sprzętowego modułu zabezpieczeń. Obsługują szeroką gamę typy kluczy kryptograficznych i algorytmów, w tym: Pełna obsługa Suite B

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
  * Podsumowanie/HMAC wyznaczania wartości skrótu/komunikat: SHA-1, SHA-2, SM3
  * Wyprowadzania klucza: Tryb SP800 108 licznika
  * Zawijanie kluczy: SP800-38F
  * Generowanie liczby losowej: FIPS 140-2 zatwierdzone zgodnych z BSI DRG.4 DRBG (tryb kont SP 800-90.)

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>PYT.: Jest dedykowany przez moduł HSM zweryfikowane w trybie FIPS 140-2 Level 3?

Tak. Dedykowana usługa HSM Inicjuje obsługę urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>PYT.: Co należy zrobić aby upewnić się, czy działają w wersji dedykowanej przez moduł HSM w trybie FIPS 140-2 Level 3 zweryfikować tryb?

Usługę HSM w wersji dedykowanej obsługuje urządzenia SafeNet Luna sieciowych przez moduł HSM 7. Użyj tych urządzeń ze standardem FIPS 140-2 poziom 3 zweryfikować sprzętowych modułów zabezpieczeń. Konfiguracja domyślna wdrożone, systemu operacyjnego i oprogramowania układowego są również zweryfikowane w trybie FIPS. Nie trzeba podejmować żadnych działań ze standardem FIPS 140-2 Level 3 zgodności.

### <a name="how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Jak klient zabezpiecza, gdy moduł HSM jest anulowanie aprowizacji materiału klucza jest wyczyszczone?

Przed zażądaniem anulowania obsługi, klient musi zeroized modułem HSM, za pomocą dostępne narzędzia klienckie sprzętowego modułu zabezpieczeń firmy Gemalto.

## <a name="performance-and-scale"></a>Wydajność i skalowanie

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>PYT.: Ile operacje kryptograficzne są obsługiwane z dedykowanego sprzętowego modułu zabezpieczeń na sekundę?

Dedykowane urządzenia przepisami SafeNet sieciowych przez moduł HSM 7 sprzętowego modułu zabezpieczeń (model A790). Poniżej przedstawiono podsumowanie maksymalną wydajność niektórych operacji: 

* RSA-2048: 10 000 transakcji na sekundę
* ECC P256: 20 000 transakcji na sekundę
* AES-GCM: 17,000 transakcji na sekundę

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>PYT.: Ile partycje mogą być tworzone w wersji dedykowanej module HSM?

Na podstawie określonego modelu używać sprzętowego modułu zabezpieczeń, istnieją 10 partycji.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>PYT.: Ile kluczy może być obsługiwany w wersji dedykowanej module HSM?

Maksymalna liczba kluczy. Numery te są również zastosowanie do pary kluczy przy użyciu kluczy asymetrycznych.

* SZYFROWANIA RSA 2048 - 19,000
* ECC P256 - 91,000
* AES-256 - 218,000

Pojemność różnią się w zależności od określonych kluczowych atrybutów w szablonie generowania kluczy i liczby partycji.

