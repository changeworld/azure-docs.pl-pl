---
title: Opcje rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure
description: Nazwa usługi DNS dla maszyn wirtualnych systemu Linux IaaS platformy Azure, w tym scenariuszy, pod warunkiem rozpoznawania, hybrydowego zewnętrznego serwera DNS i Przenieś swój własny DNS.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: jeconnoc
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: d899e037a144892d6d2c843fec08d63a9e3e514a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30836099"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opcje rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure
Platforma Azure udostępnia rozpoznawanie nazw DNS domyślnie dla wszystkich maszyn wirtualnych, które znajdują się w jednej sieci wirtualnej. Można zaimplementować własne rozwiązania rozpoznawania nazw DNS przez skonfigurowanie usługi DNS na maszynach wirtualnych obsługującego platformy Azure. Następujące scenariusze powinna ułatwić wybierz jedną, która działa w danej sytuacji.

* [Rozpoznawanie nazw, która udostępnia usługi Azure](#azure-provided-name-resolution)
* [Rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-using-your-own-dns-server)

Rodzaj rozpoznawania nazw, którego używasz, zależy od tego, jak sieci maszyn wirtualnych i wystąpień ról muszą komunikować się ze sobą.

W poniższej tabeli przedstawiono scenariusze i odpowiedniego rozwiązania rozpoznawania nazwy:

| **Scenariusz** | **Rozwiązania** | **Suffix** |
| --- | --- | --- |
| Rozpoznawanie nazw między wystąpień roli lub maszyn wirtualnych w tej samej sieci wirtualnej |[Rozpoznawanie nazw, która udostępnia usługi Azure](#azure-provided-name-resolution) |Nazwa hosta lub w pełni kwalifikowaną nazwę domeny (FQDN) |
| Rozpoznawanie nazw między wystąpień roli lub maszyn wirtualnych w różnych sieciach wirtualnych |Zarządzany przez klienta serwerów DNS, które przekazywania kwerend między sieciami wirtualnymi rozpoznanie przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazwy usługi z wystąpień roli lub maszynach wirtualnych platformy Azure i komputerów lokalnych |Zarządzany przez klienta z serwerów DNS (na przykład lokalnego kontrolera domeny, kontrolera domeny tylko do odczytu lokalnej lub zsynchronizowane za pomocą transferów stref DNS dodatkowej). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazwy Azure hostów z komputerów lokalnych |Przesłać zapytań do serwera proxy DNS zarządzany przez klienta w odpowiedniej sieci wirtualnej. Serwer proxy przekazuje zapytania do platformy Azure do rozpoznania. Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Reverse DNS wewnętrznych adresów IP |[Rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-using-your-own-dns-server) |Nie dotyczy |

## <a name="name-resolution-that-azure-provides"></a>Rozpoznawanie nazw, która udostępnia usługi Azure
Wraz z rozpoznawanie publicznej nazwy DNS platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i wystąpień ról, które znajdują się w tej samej sieci wirtualnej. W sieciach wirtualnych, które są oparte na usłudze Azure Resource Manager sufiks DNS jest spójne w ramach sieci wirtualnej; Nazwa FQDN nie jest wymagana. Można przypisać nazwy DNS dla maszyn wirtualnych i kart interfejsu sieciowego (NIC). Mimo że platforma Azure udostępnia rozpoznawanie nazw nie wymaga żadnej konfiguracji, nie jest odpowiednim wyborem w przypadku wszystkich scenariuszy wdrożenia w powyższej tabeli.

### <a name="features-and-considerations"></a>Funkcje i zagadnienia
**Funkcje:**

* Aby użyć rozpoznawania nazw, które platforma Azure udostępnia jest wymagana żadna konfiguracja.
* Usługa rozpoznawania nazw, które platforma Azure udostępnia ma wysoką dostępność. Nie potrzebujesz do tworzenia i zarządzania klastrami serwerów DNS.
* Usługi rozpoznawania nazw, która udostępnia usługi Azure może służyć wraz z serwerami DNS do rozpoznawania zarówno lokalnie, jak i nazwy hostów Azure.
* Rozpoznawanie nazw są udostępniane między maszynami wirtualnymi w sieci wirtualnych bez konieczności dla nazwy FQDN.
* Można użyć nazwy hostów, które najlepiej opisują wdrożeń zamiast Praca z nazwami wygenerowany automatycznie.

**Kwestie do rozważenia:**

* Nie można zmodyfikować sufiks DNS, który tworzy Azure.
* Nie można ręcznie zarejestrować własnych.
* Usługa WINS i NetBIOS są nieobsługiwane.
* Nazwy hostów musi być zgodny z DNS.
    Nazwy musi zawierać tylko 0-9, a-z, i "-", i ich nie może zaczynać się ani kończyć "-". Zobacz dokument RFC 3696 sekcji 2.
* Ruch zapytanie DNS jest ograniczenie dla każdej maszyny wirtualnej. Ograniczanie nie powinny mieć wpływ na większości aplikacji.  Ograniczanie żądań zaobserwowano, upewnij się, że włączone jest buforowanie po stronie klienta.  Aby uzyskać więcej informacji, zobacz [wprowadzenie w pełni wykorzystać możliwości rozpoznawania nazw, które platforma Azure udostępnia](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Zapewnia pełne wykorzystanie rozpoznawania nazw z tym Azure
**Buforowanie po stronie klienta:**

Niektóre zapytania DNS nie są wysyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszenia opóźnienia i poprawy odporności na niespójności w sieci przez przetłumaczenie cyklicznego zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają Time To Live (TTL), która włącza pamięć podręczną do przechowywania bez wpływania na rekordów świeżości rekordu tak długo, jak to możliwe. W związku z tym buforowanie po stronie klienta jest odpowiednia dla większości sytuacji.

Niektóre dystrybucje systemu Linux nie dołączaj buforowanie domyślnie. Firma Microsoft zaleca dodanie pamięci podręcznej do każdej maszyny wirtualnej systemu Linux po sprawdzeniu, że nie ma lokalnej pamięci podręcznej już.

Dostępnych jest kilka różnych buforowanie pakiety, takie jak dnsmasq, DNS. Poniżej przedstawiono kroki, aby zainstalować dnsmasq na najczęściej używane dystrybucje:

**Ubuntu (używa resolvconf)**
  * Zainstaluj pakiet dnsmasq ("get stanie instalacji dnsmasq sudo").

**SUSE (używa netconf)**:
1. Zainstaluj pakiet dnsmasq ("sudo zypper instalacji dnsmasq").
2. Włącz usługę dnsmasq ("Włącz dnsmasq.service systemctl").
3. Uruchom usługę dnsmasq ("systemctl start dnsmasq.service").
4. Edytuj "/ etc/sysconfig/sieci/konfiguracji" i zmień NETCONFIG_DNS_FORWARDER = "" do "dnsmasq".
5. Zaktualizuj resolv.conf ("netconfig update") można ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS.

**CentOS przez oprogramowanie Wave nieautoryzowanego (dawniej OpenLogic; używa NetworkManager)**
1. Zainstaluj pakiet dnsmasq ("sudo yum instalacji dnsmasq").
2. Włącz usługę dnsmasq ("Włącz dnsmasq.service systemctl").
3. Uruchom usługę dnsmasq ("systemctl start dnsmasq.service").
4. Dodaj "dołączy domeny nazwy serwerów 127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Uruchom ponownie usługi sieciowej ("usługi sieciowej restart"), można ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS

> [!NOTE]
> : Pakiet "dnsmasq" jest tylko jeden z wielu pamięci podręcznych DNS, które są dostępne dla systemu Linux. Przed użyciem go, sprawdź jego przydatności do potrzeb i zainstalowanym nie pamięci podręcznej.
>
>

**Ponownych prób po stronie klienta**

Usługa DNS jest głównie protokołu UDP. Ponieważ protokół UDP nie gwarantuje dostarczanie komunikatów, samego protokołu DNS obsługuje logiki ponawiania próby. Każdy klient DNS (systemu operacyjnego) może mieć Logika ponawiania różne w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponów próbę po jednej drugi, a następnie ponownie po drugim dwa, cztery i innego cztery sekundy.
* Domyślne Linux Instalatora ponownych prób po 5 sekund.  Należy zmienić, aby ponowić próbę pięć razy na sekundę.  

Aby sprawdzić bieżące ustawienia na maszynie wirtualnej systemu Linux, "/etc/resolv.conf cat" i znajduje się w wierszu "Opcje", na przykład:

    options timeout:1 attempts:5

Plik resolv.conf został wygenerowany automatycznie i nie można edytować. Wykonania określonych kroków, które Dodaj wiersz "Opcje" różnią się dystrybucji:

**Ubuntu** (używa resolvconf)
1. Dodaj linię opcji "/ etc/resolveconf/resolv.conf.d/head".
2. Uruchom 'resolvconf -u' do aktualizacji.

**SUSE** (używa netconf)
1. Dodaj do NETCONFIG_DNS_RESOLVER_OPTIONS "timeout:1 prób: 5" = "" parametru w "/ etc/sysconfig/sieci/konfiguracji".
2. Uruchom "netconfig update" do aktualizacji.

**CentOS przez nieautoryzowanego oprogramowania Wave (dawniej OpenLogic)** (używa NetworkManager)
1. Dodaj "RES_OPTIONS"timeout:1 prób: 5"=" do "/ etc/sysconfig/sieci".
2. Uruchom "sieci po ponownym uruchomieniu usługi" do aktualizacji.

## <a name="name-resolution-using-your-own-dns-server"></a>Rozpoznawanie nazw przy użyciu serwera DNS
Twoje potrzeby rozpoznawania nazw może wykracza poza platforma Azure udostępnia funkcje. Na przykład może wymagać rozpoznawania nazw DNS między sieciami wirtualnymi. Aby pokrywał się w tym scenariuszu, używając serwery DNS.  

Serwery DNS w sieci wirtualnej można przekazywać zapytań DNS do rozpoznawania nazw cykliczne platformy Azure do rozpoznania nazwy hostów, które znajdują się w tej samej sieci wirtualnej. Na przykład serwer DNS, który działa na platformie Azure można odpowiedzi na zapytania DNS dotyczące własne pliki strefy DNS i przesyła inne zapytania do usługi Azure. Ta funkcja umożliwia maszyn wirtualnych, aby wyświetlić obie pozycje w plikach strefy i nazwy hostów, które platforma Azure udostępnia (za pośrednictwem usługi przesyłania dalej). Dostęp do rozpoznawania nazw cykliczne Azure zostaną przekazane za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przesyłanie dalej DNS również umożliwia rozpoznawanie nazw DNS między sieciami wirtualnymi i umożliwia maszyny lokalnej do rozpoznania nazwy hostów, które platforma Azure udostępnia. Można rozpoznać nazwy hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i umożliwiać zapytania do przodu nazwy hosta Azure. Sufiks DNS różni się w każdej sieci wirtualnej, można użyć zasady warunkowego przesyłania dalej do wysyłania zapytań DNS do odpowiedniej sieci wirtualnych do rozpoznania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną podczas rozpoznawania nazw DNS między sieciami wirtualnymi za pomocą tej metody:

![Rozpoznawanie nazw DNS między sieciami wirtualnymi](./media/azure-dns/inter-vnet-dns.png)

Gdy używasz rozpoznawania nazw, które platforma Azure udostępnia wewnętrzny sufiks DNS podano do każdej maszyny wirtualnej przy użyciu protokołu DHCP. Korzystając z własnych rozwiązań rozpoznawania nazwy, ponieważ sufiks koliduje z innych architektur DNS ten sufiks nie podano do maszyn wirtualnych. Aby odwołać się do maszyn za pomocą nazwy FQDN lub skonfigurować sufiks na maszynach wirtualnych, umożliwia programu PowerShell lub interfejsu API określić sufiks:

* Dla sieci wirtualnych, które są zarządzane przez Menedżera zasobów Azure, jest dostępny za pośrednictwem sufiks [karty interfejsu sieciowego](https://msdn.microsoft.com/library/azure/mt163668.aspx) zasobów. Można również uruchomić `azure network public-ip show <resource group> <pip name>` polecenie, aby wyświetlić szczegóły dotyczące Twojego publicznego adresu IP, która zawiera nazwę FQDN karty sieciowej.

Jeśli przekazywanie zapytań na platformie Azure nie własnych potrzeb, musisz podać rozwiązania DNS.  Rozwiązania DNS musi:

* Podaj rozpoznawania odpowiedniej nazwy hosta, na przykład za pomocą [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczanie rekordów DNS. Dzierżawy DHCP platformy Azure są bardzo długie i oczyszczania może usunąć rekordy DNS przedwcześnie.
* Podaj odpowiednie rekursywnego rozpoznawania zezwalająca na rozpoznawanie nazw domen zewnętrznych.
* Być dostępny (TCP i UDP na porcie 53) od klientów, które służy ona i mieć możliwość uzyskania dostępu do Internetu.
* Być zabezpieczony przed dostępu z Internetu, aby zminimalizować zagrożenia powodowanego przez czynników zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, korzystając z maszyn wirtualnych na serwerach DNS platformy Azure, wyłącz protokół IPv6 i przypisz [publiczny adres IP na poziomie wystąpienia](../../virtual-network/virtual-networks-instance-level-public-ip.md) do każdej maszyny wirtualnej serwera DNS.  
>
>
