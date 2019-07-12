---
title: Opcje rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure
description: Nazwa usługi DNS dla maszyn wirtualnych systemu Linux w usłudze Azure IaaS, w tym scenariuszy, pod warunkiem rozpoznawania, hybrydowych zewnętrznego serwera DNS i przenieść swój własny DNS.
services: virtual-machines
documentationcenter: na
author: RicksterCDN
manager: gwallace
editor: tysonn
ms.assetid: 787a1e04-cebf-4122-a1b4-1fcf0a2bbf5f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: rclaus
ms.openlocfilehash: d9ff8ca8ce35c8698fc67fa8588eed4228230068
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668493"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opcje rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure
Platforma Azure udostępnia rozpoznawanie nazw DNS domyślnie dla wszystkich maszyn wirtualnych, które znajdują się w jednej sieci wirtualnej. Możesz zaimplementować własnego rozwiązania rozpoznawania nazw DNS przez skonfigurowanie usługi DNS na maszynach wirtualnych obsługujący platformy Azure. Następujące scenariusze powinny pomóc w wybierz ten, który działa w danej sytuacji.

* [Rozpoznawanie nazw, które platforma Azure udostępnia](#name-resolution-that-azure-provides)
* [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server)

Rodzaj rozpoznawania nazw, którego używasz, zależy od tego, jak maszyn wirtualnych i wystąpień roli muszą komunikować się ze sobą.

W poniższej tabeli przedstawiono scenariusze i odpowiedniego rozwiązania rozpoznawania nazwy:

| **Scenariusz** | **Rozwiązanie** | **Suffix** |
| --- | --- | --- |
| Rozpoznawanie nazw między wystąpieniami roli lub maszyn wirtualnych w tej samej sieci wirtualnej |Rozpoznawanie nazw, które platforma Azure udostępnia |Nazwa hosta lub w pełni kwalifikowana nazwa domeny (FQDN) |
| Rozpoznawanie nazw między wystąpieniami roli lub maszynami wirtualnymi w różnych sieciach wirtualnych |Zarządzane przez klienta serwerów DNS, które przesyłają dalej zapytań między sieciami wirtualnymi rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie komputerów w środowisku lokalnym i nazwy usług z wystąpień roli lub maszynach wirtualnych platformy Azure |Zarządzane przez klienta z serwerów DNS (na przykład lokalnego kontrolera domeny, kontrolera domeny tylko do odczytu lokalnej lub synchronizowane przy użyciu transferu strefy DNS dodatkowej). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Rozwiązania platformy Azure nazw hostów z komputerów w środowisku lokalnym |Przesyła zapytania do serwera proxy DNS zarządzanych przez klienta w odpowiedniej sieci wirtualnej. Serwer proxy przekazuje zapytania na platformie Azure, do rozpoznania. Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server). |Tylko nazwa FQDN |
| Odwrotne DNS dla wewnętrznych adresów IP |[Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-using-your-own-dns-server) |Nie dotyczy |

## <a name="name-resolution-that-azure-provides"></a>Rozpoznawanie nazw, które platforma Azure udostępnia
Wraz z rozwiązania publicznej nazwy DNS platforma Azure oferuje rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i wystąpień roli, które znajdują się w tej samej sieci wirtualnej. W sieciach wirtualnych, które są oparte na usłudze Resource Manager sufiks DNS jest spójny w ramach sieci wirtualnej; Nazwa FQDN nie jest wymagana. Zarówno kart interfejsu sieciowego (NIC), jak i maszyny wirtualne można przypisać nazwy DNS. Mimo że rozpoznawanie nazw, które platforma Azure udostępnia nie wymaga żadnej konfiguracji, nie jest odpowiednim wyborem na potrzeby wszystkie scenariusze wdrażania, jak pokazano w powyższej tabeli.

### <a name="features-and-considerations"></a>Funkcje i zagadnienia
**Funkcje:**

* Aby korzystanie z rozpoznawania nazw, które platforma Azure udostępnia jest wymagana żadna konfiguracja.
* Usługa rozpoznawania nazw, które platforma Azure udostępnia jest wysoce dostępna. Nie potrzebujesz do tworzenia i zarządzania klastrami serwerów DNS.
* Usługa rozpoznawania nazw, które platforma Azure udostępnia może służyć wraz z serwerów DNS rozwiązać zarówno lokalnych, jak i nazwy hostów platformy Azure.
* Rozpoznawanie nazw znajduje się między maszynami wirtualnymi w wirtualnych sieciach bez potrzeby wprowadzania nazwy FQDN.
* Możesz użyć nazw hostów, które najlepiej opisują wdrożeń zamiast Praca z nazwami wygenerowany automatycznie.

**Kwestie:**

* Nie można zmodyfikować sufiks DNS, który tworzy platformy Azure.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługa WINS i NetBIOS są nieobsługiwane.
* Nazwy hostów musi być zgodny z DNS.
    Nazwy, należy użyć tylko 0-9, a – z, i "-", i nie mogą zaczynać ani kończyć "-". Zobacz RFC 3696 sekcji 2.
* Ruch DNS jest ograniczane dla poszczególnych maszyn wirtualnych. Ograniczanie nie powinny mieć wpływ na większość aplikacji.  Jeśli zostanie wykryty ograniczanie żądań, upewnij się, że włączone jest buforowanie po stronie klienta.  Aby uzyskać więcej informacji, zobacz [wprowadzenie najefektywniejsze korzystanie z rozpoznawania nazw, które platforma Azure udostępnia](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Zapewnia pełne wykorzystanie od funkcji rozpoznawania nazw tej platformy Azure
**Buforowanie po stronie klienta:**

Niektóre zapytania DNS nie są wysyłane przez sieć. Buforowanie po stronie klienta, pomaga zmniejszyć opóźnienie i poprawić odporność na niespójności w sieci, Rozpoznając cyklicznego zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawiera Time To Live (TTL), która włącza pamięć podręczną do przechowywania rekordu tak długo, jak to możliwe bez wywierania wpływu na aktualność rekordów. W rezultacie buforowanie po stronie klienta jest odpowiedni dla większości sytuacji.

Niektórych dystrybucjach systemu Linux nie są uwzględniane w pamięci podręcznej domyślnie. Firma Microsoft zaleca, dodawanie pamięci podręcznej do każdej maszyny wirtualnej systemu Linux po sprawdzeniu, że nie ma lokalnej pamięci podręcznej.

Buforowanie pakietów, takich jak dnsmasq, na kilka różnych DNS są dostępne. Poniżej przedstawiono kroki, aby zainstalować dnsmasq na najbardziej typowe dystrybucji:

**Ubuntu (używa resolvconf)**
  * Zainstaluj pakiet dnsmasq ("" sudo "apt-get install dnsmasq").

**SUSE (używa netconf)** :
1. Zainstaluj pakiet dnsmasq ("" sudo "zypper instalacji dnsmasq").
2. Włącz usługę dnsmasq ("Włącz dnsmasq.service systemctl").
3. Uruchom usługę dnsmasq ("start systemctl dnsmasq.service").
4. Edytuj "/ etc/sysconfig/network/config" i zmień NETCONFIG_DNS_FORWARDER = "" do "dnsmasq".
5. Zaktualizuj resolv.conf ("netconfig update") można ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS.

**CentOS Rogue Wave Software (dawniej OpenLogic; używa NetworkManager)**
1. Zainstaluj pakiet dnsmasq ("" sudo "yum install dnsmasq").
2. Włącz usługę dnsmasq ("Włącz dnsmasq.service systemctl").
3. Uruchom usługę dnsmasq ("start systemctl dnsmasq.service").
4. Dodaj "dołączana serwerami nazw domen-127.0.0.1;" do "/etc/dhclient-eth0.conf".
5. Uruchom ponownie usługę sieci ("usługi sieciowej jest ponowne uruchomienie") można ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS

> [!NOTE]
> : Pakiet "dnsmasq" jest tylko jeden z wielu pamięci podręcznych DNS, które są dostępne dla systemu Linux. Zanim go użyjesz, sprawdź jego przydatność do własnych potrzeb i zainstalowanym Brak pamięci podręcznej.
>
>

**Ponownych prób po stronie klienta**

Usługa DNS jest głównie protokołu UDP. Ponieważ protokół UDP nie gwarantuje dostarczanie komunikatów, samego protokołu DNS obsługuje logikę ponawiania próby. Logika ponawiania różne w zależności od preferencji twórcy może cechować się każdego klienta DNS (systemu operacyjnego):

* Systemy operacyjne Windows ponów próbę po jedną sekundę, a następnie ponownie po drugim dwa, cztery, a inny czterech sekund.
* Domyślne próby instalacji dla systemu Linux po 5 sekundach.  Należy zmienić ten element, aby ponowić próbę pięć razy na sekundę.  

Aby sprawdzić bieżące ustawienia na maszynę wirtualną systemu Linux, "/etc/resolv.conf cat" i wyglądu w wierszu "Opcje", na przykład:

    options timeout:1 attempts:5

Plik resolv.conf został wygenerowany automatycznie i nie można edytować. Określone kroki, które należy dodać wiersz "Opcje" różnią się przez funkcję dystrybucji:

**Ubuntu** (używa resolvconf)
1. Dodaj wiersz opcje, aby "/ etc/resolveconf/resolv.conf.d/head".
2. Uruchom 'resolvconf -u' do zaktualizowania.

**SUSE** (używa netconf)
1. Dodawanie 'timeout:1 prób: 5' do NETCONFIG_DNS_RESOLVER_OPTIONS = "" parametru w "/ etc/sysconfig/network/config".
2. Uruchom usługę "netconfig update" do zaktualizowania.

**CentOS Rogue Wave Software (dawniej OpenLogic)** (używa NetworkManager)
1. Dodaj "RES_OPTIONS"timeout:1 prób: 5"=" do "/ etc/sysconfig/network".
2. Uruchom "usługi sieciowej ponowne uruchomienie" do zaktualizowania.

## <a name="name-resolution-using-your-own-dns-server"></a>Rozpoznawanie nazw przy użyciu własnego serwera DNS
Twoje potrzeby rozpoznawania nazw może wyjść poza funkcji udostępnianych przez platformę Azure. Na przykład może wymagać rozpoznawania nazw DNS między sieciami wirtualnymi. Aby uwzględnić w tym scenariuszu, można użyć własnych serwerów DNS.  

Serwery DNS w sieci wirtualnej może przekazywać zapytań DNS do rozpoznawania cyklicznego platformy Azure można rozpoznać nazwy hostów, które znajdują się w tej samej sieci wirtualnej. Na przykład serwer DNS, który działa na platformie Azure może odpowiadać na zapytania DNS dotyczące własnej plików strefy DNS i przekazywania innych zapytań do usługi Azure. Ta funkcja pozwala maszynom wirtualnym wyświetlić obie pozycje w plikach strefy i nazwy hostów, które platforma Azure udostępnia (za pośrednictwem usługi przesyłania dalej). Dostęp do rozpoznawania cyklicznego platformy Azure jest oferowana w ramach tego wirtualnego adresu IP 168.63.129.16.

Przekazywanie DNS również umożliwia rozpoznawanie nazw DNS między sieciami wirtualnymi i umożliwia maszynach w środowisku lokalnym, można rozpoznać nazwy hostów, które platforma Azure udostępnia. Można rozpoznać nazwy hosta maszyny wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i można skonfigurować do zapytań do przodu nazwy hosta na platformie Azure. Ponieważ sufiks DNS różni się w poszczególnych sieciach wirtualnych, można użyć reguły warunkowego przesyłania dalej do wysyłania zapytań DNS do odpowiedniej sieci wirtualnej rozpoznawania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną, wykonując rozpoznawania nazw DNS między sieciami wirtualnymi za pomocą tej metody:

![Rozpoznawanie nazw DNS między sieciami wirtualnymi](./media/azure-dns/inter-vnet-dns.png)

Korzystając z rozpoznawania nazw, które platforma Azure udostępnia wewnętrzny sufiks DNS znajduje się do każdej maszyny wirtualnej przy użyciu protokołu DHCP. Gdy używasz własnego rozwiązania rozpoznawania nazwy, ponieważ sufiks zakłócać innych architektur DNS ten sufiks nie podano na maszynach wirtualnych. Do odwoływania się do maszyn za pomocą nazwy FQDN lub konfigurowania sufiksu domeny na maszynach wirtualnych, aby określić sufiks można użyć programu PowerShell lub interfejsu API:

* Dla sieci wirtualnych, które są zarządzane przez usługę Azure Resource Manager, jest dostępna za pośrednictwem sufiks [karty interfejsu sieciowego](https://msdn.microsoft.com/library/azure/mt163668.aspx) zasobów. Można również uruchomić `azure network public-ip show <resource group> <pip name>` polecenie, aby wyświetlić szczegółowe informacje o adres IP, który zawiera nazwę FQDN karty sieciowej.

Jeśli przekazywanie zapytań na platformie Azure nie spełnia Twoich potrzeb, musisz podać własne rozwiązanie DNS.  W rozwiązaniu DNS musi:

* Podaj rozpoznawanie odpowiedniej nazwy hosta, na przykład za pomocą [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczania rekordu DNS. Dzierżawy DHCP systemu Azure są bardzo długie i oczyszczania może usunąć rekordy DNS przedwcześnie.
* Podaj odpowiednie rekursywnego rozpoznawania umożliwia rozpoznawanie nazw domen zewnętrznych.
* Być dostępny (TCP i UDP na porcie 53) od klientów, które służy i być w stanie uzyskać dostęp do Internetu.
* Zabezpieczone przed dostępem z Internetu, aby uniknąć zagrożenia powodowane przez zewnętrzne agentów.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, korzystając z maszyn wirtualnych na serwerach DNS platformy Azure, wyłączanie protokołu IPv6 i przypisać [publiczny adres IP na poziomie wystąpienia](../../virtual-network/virtual-networks-instance-level-public-ip.md) do każdej maszyny wirtualnej serwera DNS.  
>
>
