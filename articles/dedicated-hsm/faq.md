---
title: Często zadawane pytania — sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące różnych tematów dotyczących sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej
services: dedicated-hsm
author: johncdawson
manager: barbkess
tags: azure-resource-manager
ms.custom: mvc
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: barclayn
ms.openlocfilehash: d432dc25a1995a2f0348c7626a051f46ffbf418b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118982"
---
# <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Znajdź odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure w wersji dedykowanej sprzętowego modułu zabezpieczeń.

## <a name="the-basics"></a>Podstawowe informacje

### <a name="q-what-is-a-hardware-security-module-hsm"></a>Pyt.: Co to jest sprzętowego modułu zabezpieczeń (HSM)?

Sprzętowego modułu zabezpieczeń (HSM) to fizyczne urządzenie obliczeniowe używane do ochrony i zarządzanie kluczami kryptograficznymi. Kluczy przechowywanych w sprzętowych modułach zabezpieczeń może służyć do operacji kryptograficznych. Materiał klucza, bezpiecznie pozostaje odporne na uszkodzenia, manipulacji sprzętowych modułów. Dopuszcza tylko w ramach sprzętowego modułu zabezpieczeń uwierzytelnianie i autoryzowanie aplikacji do używania kluczy. Dane dotyczące klucza nigdy nie opuszczają granic zabezpieczeń modułu HSM.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>Pyt.: Co to jest oferta sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest oparte na chmurze Usługa udostępniająca sprzętowych modułów zabezpieczeń hostowana w centrach danych platformy Azure, które były bezpośrednio podłączone do sieci wirtualnej klienta. Te moduły HSM są urządzenia sieciowe dedykowane (firmy firmy Gemalto SafeNet sieciowych przez moduł HSM 7 Model A790). Są one wdrażane bezpośrednio do klientów przestrzeń prywatnych adresów IP i firmy Microsoft nie ma dostępu do usług kryptograficznych funkcji sprzętowych modułów zabezpieczeń. Tylko klient ma pełną administracyjnych i kryptograficznych kontrolę nad tymi urządzeniami. Klienci są odpowiedzialni za zarządzanie urządzeniem i będą oni mogli uzyskać Dzienniki aktywności pełnej bezpośrednio ze swoich urządzeń. Dedykowanych sprzętowych modułów zabezpieczeń pomóc klientom spełniają wymagania zgodności/prawne, takie jak FIPS 140-2 Level 3, HIPAA, PCI-DSS i eIDAS i wiele innych.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>Pyt.: Jaki sprzęt jest używany dla dedykowanych przez moduł HSM?

Firma Microsoft nawiązała współpracę z firmy Gemalto do świadczenia usług platformy Azure w wersji dedykowanej w module HSM. Określone urządzenie, używany jest [SafeNet Luna sieciowych przez moduł HSM 7 Model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). To urządzenie nie tylko zapewnia FIPS 140-2 Level 3 zweryfikowany w oprogramowania układowego, ale oferuje także o małych opóźnieniach, o wysokiej wydajności i dużej pojemności za pośrednictwem 10 partycji. 

### <a name="q-what-is-an-hsm-used-for"></a>Pyt.: Do czego są używane moduły HSM?

Moduły HSM służą do przechowywania kluczy kryptograficznych używanych na potrzeby funkcji kryptograficznych, takich jak protokół Secure Socket Layer (SSL), szyfrowanie danych, infrastruktura kluczy publicznych (PKI, Public Key Infrastructure), zarządzanie prawami cyfrowymi (DRM, Digital Rights Management) i podpisywanie dokumentów.

### <a name="q-how-does-dedicated-hsm-work"></a>Pyt.: W jaki sposób działa dedykowany moduł HSM?

Klienci mogą aprowizować sprzętowych modułów zabezpieczeń w określonych regionach przy użyciu programu PowerShell lub interfejsu wiersza polecenia. Klient określa, jakie sieci wirtualnej, sprzętowe moduły zabezpieczeń spowoduje nawiązanie połączenia i po aprowizacji modułów HSM będą dostępne w wyznaczonym podsieci w przypisanych adresów IP w prywatnej przestrzeni adresowej IP klienta. Następnie klienci mogą się łączyć sprzętowych modułów zabezpieczeń do zarządzania urządzeniem przy użyciu protokołu SSH i administrowanie, konfigurowanie połączeń klienckich przez moduł HSM, zainicjować modułów HSM, tworzenie partycji, zdefiniuj i przypisz ról, takich jak urzędnika partycji, ds. usług kryptograficznych i kryptograficznych użytkownika. Następnie klient użyje firmy Gemalto udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie do wykonywania operacji kryptograficznych z poziomu ich aplikacji.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>Pyt.: Jakie oprogramowanie jest dostarczana z usługi w wersji dedykowanej przez moduł HSM?

Firmy Gemalto dostarcza całe oprogramowanie dla urządzenia sprzętowego modułu zabezpieczeń po aprowizacji przez firmę Microsoft. Oprogramowanie jest dostępne pod adresem [portal obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania. Oprogramowanie klienckie obsługiwana jest wersja 7.2, która jest zgodna z trybem FIPS 140-2 Level 3 zweryfikowane-wersja oprogramowania układowego 7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>Pyt.: Azure w wersji dedykowanej w module HSM oferuje uwierzytelniania opartego na hasłach i na podstawie PED?

W tej chwili sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej zawiera sprzętowych modułów zabezpieczeń wyłącznie przy użyciu uwierzytelniania opartego na hasłach.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>Pyt.: Sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej obsługującym Moje sprzętowych modułów zabezpieczeń dla mnie?

Firma Microsoft tylko oferuje HSM sieci firmy Gemalto SafeNet Luna, za pośrednictwem usługi przez sprzętowy moduł zabezpieczeń w wersji dedykowanej i nie może obsługiwać żadnych urządzeń dostarczone do klienta.

### <a name="q-does-azure-dedicated-hsm-support-payment-pinetf-features"></a>Pyt.: Czy płatności pomocy technicznej platformy Azure w wersji dedykowanej w module HSM funkcji (numer PIN/ETF)?

Usługa Azure w wersji dedykowanej w module HSM używa urządzeń SafeNet Luna sieciowych przez moduł HSM 7 (model A790). Te urządzenia, które nie obsługują funkcji określonej przez moduł HSM płatności (na przykład numer PIN lub ETF) lub certyfikatów. Jeśli potrzebujesz sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej usługi do obsługi płatności w przyszłości sprzętowych modułów zabezpieczeń, Przekaż opinii na swoim przedstawicielem firmy Microsoft.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>Pyt.: Które regiony platformy Azure jest dostępne w dedykowanej przez moduł HSM?

Począwszy od późno marca 2019 r dedykowany przez moduł HSM jest dostępna w 14 regionach wymienionych poniżej. Dodatkowe regiony są planowane i można podawać za pośrednictwem swoim przedstawicielem firmy Microsoft.

* Wschodnie stany USA
* Wschodnie stany USA 2
* Zachodnie stany USA
* Środkowo-południowe stany USA
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Europa Północna
* Europa Zachodnia
* Południowe Zjednoczone Królestwo
* Zachodnie Zjednoczone Królestwo
* Kanada Środkowa
* Kanada Wschodnia
* Australia Wschodnia
* Australia Południowo-Wschodnia

## <a name="interoperability"></a>Współdziałanie

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>Pyt.: Jak mojej aplikacji podłączyć do modułu HSM w wersji dedykowanej?

Udostępniane przez moduł HSM narzędzia/zestawu SDK/oprogramowanie klienckie firmy Gemalto służy do wykonywania operacji kryptograficznych z poziomu aplikacji. Oprogramowanie jest dostępne pod adresem [portal obsługi klienta firmy Gemalto](https://supportportal.gemalto.com/csm/). Klienci korzystający z usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń są wymagane do zarejestrowania dla firmy Gemalto obsługuje i ma identyfikator klienta, umożliwiająca dostęp i pobierania odpowiedniego oprogramowania.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>Pyt.: Aplikacja z można połączyć się przez sprzętowy moduł zabezpieczeń w wersji dedykowanej innej sieci Wirtualnej w lub w wielu regionach?

Tak, musisz użyć [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md) w regionie w celu ustanowienia połączenia między sieciami wirtualnymi. Łączność między regionami, należy użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>Pyt.: Czy można synchronizować przez sprzętowy moduł zabezpieczeń w wersji dedykowanej przy użyciu sprzętowych modułów zabezpieczeń w środowisku lokalnym

Tak, można synchronizować lokalne sprzętowych modułów zabezpieczeń z dedykowanego sprzętowego modułu zabezpieczeń. [Protokół Point-to-Point sieci VPN lub punkt lokacja](../vpn-gateway/vpn-gateway-about-vpngateways.md) łączność, może służyć do nawiązywania łączności z Twoją siecią lokalną.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>Pyt.: Czy można zaszyfrować dane używane przez inne usługi platformy Azure przy użyciu kluczy przechowywanych w wersji dedykowanej module HSM?

Nie. Azure w wersji dedykowanej Moduły HSM są dostępne jedynie z wewnątrz sieci wirtualnej.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>Pyt.: Można importować klucze z istniejącego lokalnego modułu HSM do dedykowanego modułu HSM?

Tak, jeśli masz lokalne sprzętowych modułów zabezpieczeń firmy Gemalto SafeNet. Istnieje wiele metod. Zapoznaj się z dokumentacją sprzętowego modułu zabezpieczeń firmy Gemalto.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>Pyt.: Jakie systemy operacyjne są obsługiwane przez oprogramowanie klienckie dedykowany przez moduł HSM?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Wirtualne: VMware, funkcji Hyper-v, Xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>Pyt.: Jak skonfigurować aplikację klienta do utworzenia konfiguracji o wysokiej dostępności przy użyciu wielu partycji z wieloma sprzętowych modułów zabezpieczeń?

Aby uzyskać wysoką dostępność, należy skonfigurować konfigurację aplikacji klienta przez moduł HSM do używania partycji z każdego modułu HSM. Zapoznaj się z dokumentacją oprogramowania sprzętowego modułu zabezpieczeń firmy Gemalto klienta.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>Pyt.: Jakie mechanizmy uwierzytelniania są obsługiwane przez dedykowany przez moduł HSM?

Urządzenia SafeNet sieciowych przez moduł HSM 7 (model A790) korzysta z platformy Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń i obsługują uwierzytelniania opartego na hasłach.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>Pyt.: Jakie zestawy SDK, interfejsów API klienta oprogramowanie jest dostępne za pomocą dedykowanego przez moduł HSM?

PKCS nr 11, języka Java (JCA/JCE), CAPI firmy Microsoft i CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>Pyt.: Czy mogę import/zmigrować klucze w sprzętowych modułach zabezpieczeń Luna 5/6 do sprzętowych modułów zabezpieczeń platformy Azure w wersji dedykowanej?

Tak. Zapoznaj się z przewodnikiem migracji firmy Gemalto. 

## <a name="using-your-hsm"></a>Za pomocą modułu HSM

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>Pyt.: Jak zdecydować, czy ma być używany w usłudze Azure Key Vault lub Azure w wersji dedykowanej modułu HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest odpowiednim wyborem dla przedsiębiorstw migracji do aplikacji platformy Azure w środowisku lokalnym, które używają sprzętowych modułów zabezpieczeń. Dedykowanych sprzętowych modułów zabezpieczeń istnieje możliwość przeprowadzania migracji aplikacji przy minimalnych zmianach. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą używać dedykowanego sprzętowego modułu zabezpieczeń. Ogólnie rzecz biorąc, porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak Application gateway lub traffic manager dla bez kluczy protokołu SSL usług AD CS (usługi certyfikatów Active Directory), lub podobne narzędzia infrastruktury kluczy publicznych, narzędzia/aplikacje używane do podpisywania dokumentów, podpisywanie kodu lub SQL Server (IaaS), skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami). Usługa Azure Key Vault jest odpowiedni dla aplikacji "urodzonych w chmury" lub szyfrowania na scenariusze rest, w którym dane klientów są przetwarzane przez PaaS (platforma jako usługa) lub scenariuszy SaaS (oprogramowanie jako usługa), takie jak klucz klienta usługi Office 365, Azure Information Protection , Usługa azure Disk Encryption, szyfrowanie usługi Azure Data Lake Store przy użyciu zarządzanych przez klienta klucza szyfrowania usługi Azure Storage za pomocą klienta zarządzane klucza i SQL Azure za pomocą klienta zarządzanego klucza.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>Pyt.: Jakie najlepszych scenariuszy użycia własnych platformy Azure w wersji dedykowanej w module HSM?

Azure w wersji dedykowanej przez sprzętowy moduł zabezpieczeń jest najbardziej odpowiednie dla scenariuszy migracji. Oznacza to, że w przypadku migracji aplikacji lokalnych na platformę Azure, które już używają sprzętowych modułów zabezpieczeń. Zapewnia to możliwość prostego Migrowanie na platformę Azure przy minimalnych zmianach w aplikacji. Jeśli operacje kryptograficzne są wykonywane w kodzie aplikacji działających w maszynie Wirtualnej platformy Azure lub aplikacji sieci Web, mogą być używane przez sprzętowy moduł zabezpieczeń w wersji dedykowanej. Ogólnie rzecz biorąc porządnie zapakowane oprogramowania uruchamianego w modelach IaaS (infrastruktura jako usługa), obsługujących sprzętowych modułów zabezpieczeń magazynu kluczy można użyć dedykować modułu HSM, takich jak:

* Application gateway lub traffic manager dla protokołu SSL bez kluczy
* Usług AD CS (usługi certyfikatów Active Directory)
* Podobne narzędzia infrastruktury kluczy publicznych
* Narzędzia/aplikacje używane do podpisywania dokumentów
* Podpisywanie kodu
* Program SQL Server (IaaS) skonfigurowano funkcję TDE (przezroczyste szyfrowanie baz danych), za pomocą klucza głównego w module HSM za pomocą dostawcy EKM (rozszerzonego zarządzania kluczami)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>Pyt.: Czy dedykowanego modułu HSM można używać wraz z kluczem klienta usługi Office 365, usługą Azure Information Protection, usługą Azure Data Lake Store, usługą Disk Encryption, szyfrowaniem usługi Azure Storage lub szyfrowaniem TDE Azure SQL?

Nie. Dedykowany przez moduł HSM jest aprowizowany bezpośrednio w prywatnej przestrzeni adresowej IP klienta dlatego nie jest dostępny dla innych usług platformy Azure lub programu Microsoft.

## <a name="administration-access-and-control"></a>Administracja, dostępu i kontroli

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>Pyt.: Klient pobieranie pełnej wyłączną kontrolę nad sprzętowych modułów zabezpieczeń z dedykowanych sprzętowych modułów zabezpieczeń?

Tak. Każdego urządzenia sprzętowego modułu zabezpieczeń jest pełni dedykowanego jednego pojedynczego klienta, i nikt inny nie ma kontroli administracyjnej po aprowizacji i zmieniono hasło administratora.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>Pyt.: Poziom dostępu do firmy Microsoft ma do mojego modułu HSM?

Microsoft nie ma dowolną kontrolkę kryptograficznych lub administracyjnych w ramach sprzętowego modułu zabezpieczeń. Firma Microsoft jeszcze monitor poziom dostępu za pośrednictwem portu szeregowego połączenia, aby pobierać podstawowe dane telemetryczne, np. temperaturę, jak i składnika kondycji. Dzięki temu firma Microsoft zapewni powiadomień z wyprzedzeniem o problemów z kondycją. W razie potrzeby klient może wyłączyć to konto.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>Pyt.: Co to jest "tenantadmin" konto Microsoft używa, używany jest użytkownika administratora "admin", w sprzętowych modułach zabezpieczeń SafeNet?

Urządzenia HSM jest dostarczany z domyślnego użytkownika administracyjnego z jego zwykle domyślne hasło. Microsoft nie chcesz mieć domyślne hasła używane, gdy dowolne urządzenie znajduje się w puli, oczekiwanie na udostępniane przez klientów. Czy spełnia naszych wymagań w zakresie ścisłymi zasadami kontrolnymi zabezpieczeń. Z tego powodu firma Microsoft ustawić silne hasło, które zostały odrzucone na czas obsługi administracyjnej. Ponadto na czas obsługi administracyjnej możemy utworzyć nowego użytkownika w roli administratora o nazwie "tenantadmin". Ten użytkownik nie ma domyślne hasło i klienci zmienić jako pierwszą akcję podczas pierwszego logowania się do nowo aprowizowanej urządzenia. Ten proces zapewnia wysoki stopień zabezpieczeń i przechowuje naszego zobowiązania wyłączną kontrolę administracyjną dla naszych klientów. Należy zauważyć, że użytkownik "tenantadmin" może służyć do resetowania hasła użytkownika administratora, jeśli klient chce użyć danego konta. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>Pyt.: Można firmy Microsoft lub uprasza klucze dostępu firmy Microsoft w mojej przez sprzętowy moduł zabezpieczeń w wersji dedykowanej?

Nie. Microsoft nie ma dostępu do kluczy przechowywanych w klienta przydzielony dedykowany przez moduł HSM.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>Pyt.: Czy mogę uaktualnić oprogramowania i oprogramowania układowego w sprzętowych modułach zabezpieczeń przydzielony do mnie

Aby uzyskać najlepszą pomoc techniczną, firma Microsoft zaleca z uaktualniania oprogramowania i oprogramowania układowego w ramach sprzętowego modułu zabezpieczeń. Jednak klient ma pełną kontrolę administracyjną tym uaktualniania oprogramowania i oprogramowania układowego w przypadku określonych funkcji w wersji oprogramowania układowego w różnych. Przed wprowadzeniem zmian należy rozumieć konsekwencje, zgodnie z tym można, na przykład efekt FIPS zweryfikować stan. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>Pyt.: Jak zarządzać w wersji dedykowanej przez moduł HSM?

Możesz zarządzać dedykowanych sprzętowych modułów zabezpieczeń, uzyskując dostęp do nich przy użyciu protokołu SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>Pyt.: Jak zarządzać partycji w module HSM w wersji dedykowanej?

Oprogramowanie klienckie sprzętowego modułu zabezpieczeń firmy Gemalto służy do zarządzania sprzętowych modułów zabezpieczeń oraz partycji.

### <a name="q-how-do-i-monitor-my-hsm"></a>Pyt.: Jak monitorować Moje przez moduł HSM?

Klient ma pełny dostęp do dzienników aktywności przez moduł HSM za pośrednictwem usługi syslog i SNMP. Klienta należy ustawić serwera syslog lub serwer SNMP, aby odbierać dzienniki lub zdarzeń z modułów HSM.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>Pyt.: Z dedykowanego modułu HSM można uzyskać dziennika pełnego dostępu do wszystkich operacji przez moduł HSM?

Tak. Możesz wysłać dzienniki z urządzenia przez moduł HSM do serwera syslog

## <a name="high-availability"></a>Wysoka dostępność

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>Pyt.: Czy jest możliwe skonfigurować wysoką dostępność, w tym samym regionie lub w wielu regionach?

Tak. Konfiguracja wysokiej dostępności i konfiguracji są wykonywane w oprogramowaniu klienckim przez moduł HSM, które są dostarczane przez firmy Gemalto. Sprzętowych modułów zabezpieczeń z tej samej sieci Wirtualnej lub innych sieci wirtualnych w tym samym regionie lub w regionach lub w środowisku lokalnym sprzętowych modułów zabezpieczeń połączone z siecią Wirtualną przy użyciu lokacja lokacja lub sieci VPN typu punkt-punkt mogą być dodawane do tej samej konfiguracji o wysokiej dostępności. Należy zauważyć, że synchronizuje tylko materiału klucza i elementy nie konkretnej konfiguracji, takie jak role.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Pyt.: Czy mogę dodać sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej

Tak. Muszą spełniać wymagania wysokiej dostępności dla SafeNet Luna sieciowych przez moduł HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>Pyt.: Czy mogę dodać Luna 5/6 sprzętowych modułów zabezpieczeń z sieci lokalnej do grupy o wysokiej dostępności przy użyciu sprzętowego modułu zabezpieczeń platformy Azure w wersji dedykowanej

Nie.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>Pyt.: Ile sprzętowych modułów zabezpieczeń można dodać do tej samej konfiguracji o wysokiej dostępności z jednego wniosku?

16 elementów członkowskich grupy o wysokiej dostępności zawiera — stała, ograniczenie przepustowości pełnego testowania za pomocą doskonałe wyniki.

## <a name="support"></a>Pomoc techniczna

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>Pyt.: Co to jest umowa SLA dla usługi w wersji dedykowanej przez moduł HSM?

Nie ma żadnej gwarancji określonego czasu, które podano dla usługi w wersji dedykowanej sprzętowego modułu zabezpieczeń. Microsoft zapewni poziomu dostępu do sieci na urządzeniu i dlatego stosowanie standardowych umów SLA sieci platformy Azure.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>Pyt.: Jak są używane moduły HSM w platformy Azure w wersji dedykowanej chronione przez moduł HSM?

Centra danych platformy Azure są sterowane rozbudowane zabezpieczeniom fizycznym i procedurach. Oprócz tego dedykowanych sprzętowych modułów zabezpieczeń znajdują się w obszarze dalsze ograniczony dostęp do centrum danych. Te obszary mają dodatkowe fizyczne metody kontroli dostępu i kamery nadzoru w celu zwiększenia poziomu bezpieczeństwa.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>Pyt.: Co się stanie w przypadku naruszenia zabezpieczeń lub sprzętu, manipulowania zdarzenia?

Dedykowana usługa sprzętowego modułu zabezpieczeń używa urządzenia SafeNet sieciowych przez moduł HSM 7. Następujące urządzenia obsługują wykrywania manipulacji fizycznymi i logicznymi. Jeśli nigdy nie jest zdarzeniem odporne Moduły HSM są automatycznie zeroized.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>Pyt.: Jak zapewnić klucze w sprzętowych modułach zabezpieczeń Moje dedykowanego nie są utracone z powodu błędu lub atak złośliwe oprogramowanie

Zdecydowanie zaleca się używać urządzenia kopii zapasowej przez moduł HSM do środowiska lokalnego do wykonywania zwykłych okresowych kopii zapasowych z modułów HSM do odzyskiwania po awarii. Należy połączyć urządzenia kopii zapasowej przez sprzętowy moduł zabezpieczeń stacji roboczej w środowisku lokalnym przy użyciu połączenia sieci VPN peer-to-peer lub lokacja lokacja.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>Pyt.: Jak uzyskać pomoc techniczną dotyczącą dedykowany przez moduł HSM?

Pomoc techniczna jest świadczona przez firmę Microsoft, jak i firmy Gemalto.  Jeśli masz problem z dostępem do sprzętu lub w sieci, zgłosić żądanie pomocy technicznej z firmą Microsoft i, jeśli występuje problem z konfiguracją modułu HSM, oprogramowania i tworzenia aplikacji, zgłoś żądanie pomocy technicznej z firmy Gemalto. Jeśli masz nieokreślonej problemu, Prześlij żądanie pomocy technicznej firmy Microsoft, a następnie firmy Gemalto może zostać uruchomione jako wymagane. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>Pyt.: Jak uzyskać dostęp do integracji wskazówki dotyczące SafeNet Luna 7 przez moduł HSM, dokumentację i oprogramowania klienta?

Po zarejestrowaniu usługi, identyfikator klienta firmy Gemalto będzie pod warunkiem, która umożliwia rejestrację w portalu firmy Gemalto klientów pomocy technicznej. Umożliwi to dostęp do wszystkich oprogramowania i dokumentacji także włączenie żądania pomocy technicznej bezpośrednio z firmy Gemalto.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>Pyt.: Jeśli nie znaleziono luki w zabezpieczeniach i patch został wydany przez firmy Gemalto, który jest odpowiedzialny za uaktualnianie/poprawianie systemu operacyjnego i oprogramowania układowego?

Microsoft nie ma możliwości podłączenia do sprzętowych modułów zabezpieczeń przydzielone do klientów. Klienci, należy uaktualnić i stosowanie poprawek do ich sprzętowych modułów zabezpieczeń.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>Pyt.: Co zrobić, jeśli trzeba ponowne uruchomienie mojej przez moduł HSM?

 Moduł HSM ma opcję ponownego uruchamiania wiersza polecenia, a następnie należy zauważyć, że opcji "ponowny rozruch sprzętowy" należy używać. Jeśli to nie powiedzie się z jakiegokolwiek powodu, Prześlij żądanie pomocy technicznej firmy Microsoft i mamy urządzenia fizycznego ponowny rozruch. 

## <a name="cryptography-and-standards"></a>Standardy i kryptografii

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>Pyt.: Jest to bezpieczny do przechowywania kluczy szyfrowania dla najważniejszych danych w wersji dedykowanej module HSM?

Tak, przez sprzętowy moduł zabezpieczeń w wersji dedykowanej obsługuje urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>Pyt.: Jakie klucze kryptograficzne i algorytmy są obsługiwane przez dedykowany przez moduł HSM?

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

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>Pyt.: Jest dedykowany przez moduł HSM zweryfikowane w trybie FIPS 140-2 Level 3?

Tak. Dedykowana usługa HSM Inicjuje obsługę urządzenia SafeNet sieciowych przez moduł HSM 7, czy korzystanie ze standardem FIPS 140-2 poziom 3 prawidłowe sprzętowych modułów zabezpieczeń.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>Pyt.: Co należy zrobić aby upewnić się, czy działają w wersji dedykowanej przez moduł HSM w trybie FIPS 140-2 Level 3 zweryfikować tryb?

Usługę HSM w wersji dedykowanej obsługuje urządzenia SafeNet Luna sieciowych przez moduł HSM 7. Użyj tych urządzeń ze standardem FIPS 140-2 poziom 3 zweryfikować sprzętowych modułów zabezpieczeń. Konfiguracja domyślna wdrożone, systemu operacyjnego i oprogramowania układowego są również zweryfikowane w trybie FIPS. Nie trzeba podejmować żadnych działań ze standardem FIPS 140-2 Level 3 zgodności.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>Pyt.: Jak klient zabezpiecza, gdy moduł HSM jest anulowanie aprowizacji materiału klucza jest wyczyszczone?

Przed zażądaniem anulowania obsługi, klient musi zeroized modułem HSM, za pomocą dostępne narzędzia klienckie sprzętowego modułu zabezpieczeń firmy Gemalto.

## <a name="performance-and-scale"></a>Wydajność i skalowanie

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>Pyt.: Ile operacje kryptograficzne są obsługiwane z dedykowanego sprzętowego modułu zabezpieczeń na sekundę?

Dedykowane urządzenia przepisami SafeNet sieciowych przez moduł HSM 7 sprzętowego modułu zabezpieczeń (model A790). Poniżej przedstawiono podsumowanie maksymalną wydajność niektórych operacji: 

* RSA-2048: 10 000 transakcji na sekundę
* ECC P256: 20 000 transakcji na sekundę
* AES-GCM: 17,000 transakcji na sekundę

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>Pyt.: Ile partycje mogą być tworzone w wersji dedykowanej module HSM?

Model SafeNet Luna HSM 7, używane przez A790 obejmuje licencję do 10 partycji w koszt usługi. Urządzenie ma limit równy 100 partycji i dodawanie partycji do tego limitu będzie ponosić kosztów dodatkowych licencjonowania i wymagają instalacji nowego pliku licencji na urządzeniu.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>Pyt.: Ile kluczy może być obsługiwany w wersji dedykowanej module HSM?

Maksymalna liczba kluczy jest funkcją dostępnej pamięci. Model SafeNet Luna 7 A790 używana ma 32MB pamięci. Następujące numery są również zastosowanie do pary kluczy przy użyciu kluczy asymetrycznych.

* SZYFROWANIA RSA 2048 - 19,000
* ECC P256 - 91,000

Pojemność różnią się w zależności od określonych kluczowych atrybutów w szablonie generowania kluczy i liczby partycji.
