---
title: Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure
titlesuffix: Azure Virtual Network
description: Nazwa rozwiązania scenariusze dotyczące modelu IaaS platformy Azure, rozwiązania hybrydowe między różnych usług w chmurze, usługi Active Directory i za pomocą własnego serwera DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/25/2019
ms.author: rohink
ms.openlocfilehash: 78c66ac25e9d20d9202236407d42f815879cd3f2
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426430"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure

W zależności od tego, jak używać platformy Azure do hostowania IaaS, PaaS oraz rozwiązań hybrydowych konieczne może być Zezwalaj na maszynach wirtualnych (VM) i innych zasobów wdrożonych w sieci wirtualnej do komunikowania się ze sobą. Mimo że można włączyć komunikację przy użyciu adresów IP, jest dużo łatwiejszy w obsłudze nazw, które można łatwo zapamiętać, a nie należy zmieniać. 

Kiedy zasoby wdrożone w sieciach wirtualnych muszą rozpoznać nazwy domen jako wewnętrzne adresy IP, mogą użyć jednej z dwóch metod:

* [Rozpoznawania nazw platformy Azure](#azure-provided-name-resolution)
* [Nadaj nazwę rozwiązania, który używa własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server) (która może przekazywać kwerendy do serwerów DNS platformy Azure)

Rodzaj rozpoznawania nazw, których używasz, zależy od tego, jak zasoby muszą komunikować się ze sobą. W poniższej tabeli przedstawiono scenariusze i odpowiedniego rozwiązania rozpoznawania nazwy:

> [!NOTE]
> W zależności od scenariusza możesz korzystać z funkcji usługi Azure DNS Private Zones, która jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [Using Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](../dns/private-dns-overview.md).
>

| **Scenariusz** | **Rozwiązanie** | **Sufiks** |
| --- | --- | --- |
| Rozpoznawanie nazw między maszynami wirtualnymi znajduje się w tej samej sieci wirtualnej lub usługi Azure Cloud Services wystąpień roli w tej samej usłudze w chmurze. | [Usługa Azure DNS Private Zones](../dns/private-dns-overview.md) lub [rozpoznawania nazw platformy Azure](#azure-provided-name-resolution) |Nazwa hosta lub nazwa FQDN |
| Rozpoznawanie nazw między maszynami wirtualnymi w różnych sieciach wirtualnych lub wystąpień roli w różnych usługach w chmurze. |[Usługa Azure DNS Private Zones](../dns/private-dns-overview.md) lub serwerów DNS zarządzanych przez klienta przesyłania zapytań między sieciami wirtualnymi rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania usługi Azure App Service (Web App, funkcji lub Bot) przy użyciu integracji sieci wirtualnej do maszyn wirtualnych lub wystąpień roli w tej samej sieci wirtualnej. |Zarządzane przez klienta z serwerów DNS przesyłania zapytań między sieciami wirtualnymi rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania z aplikacji sieci Web usługi App Service do maszyn wirtualnych w tej samej sieci wirtualnej. |Zarządzane przez klienta z serwerów DNS przesyłania zapytań między sieciami wirtualnymi rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania z usługi App Service Web Apps w jednej sieci wirtualnej do maszyn wirtualnych w innej sieci wirtualnej. |Zarządzane przez klienta z serwerów DNS przesyłania zapytań między sieciami wirtualnymi rozpoznawania przez platformę Azure (serwer proxy DNS). Zobacz rozpoznawanie nazw przy użyciu własnego serwera DNS. |Tylko nazwa FQDN |
| Rozpoznawanie nazw komputerów i usług lokalnych z maszyn wirtualnych lub wystąpień roli w systemie Azure. |Zarządzane przez klienta z serwerów DNS (lokalnego kontrolera domeny, kontrolera domeny tylko do odczytu lokalnej lub zsynchronizowane, na przykład przy użyciu transferu strefy DNS dodatkowej). Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazw Azure hostów z komputerów w środowisku lokalnym. |Do przodu zapytań do serwera proxy DNS zarządzanych przez klienta w odpowiedniej sieci wirtualnej, serwer proxy przekazuje zapytania na platformie Azure dla rozwiązania. Zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Odwrotne DNS dla wewnętrznych adresów IP. |[Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Nie dotyczy |
| Rozpoznawanie nazw między maszynami wirtualnymi lub wystąpień roli w różnych usługach w chmurze, nie znajduje się w sieci wirtualnej. |Nie dotyczy. Łączność między maszynami wirtualnymi i wystąpień roli w różnych usługach w chmurze nie jest obsługiwany poza siecią wirtualną. |Nie dotyczy|

## <a name="azure-provided-name-resolution"></a>Rozpoznawania nazw platformy Azure

Wraz z rozwiązania publicznej nazwy DNS platforma Azure oferuje rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i wystąpień roli, które znajdują się w tej samej sieci wirtualnej lub usługi w chmurze. Maszyny wirtualne i wystąpienia w usłudze w chmurze współużytkują sufiksu DNS tego samego, dzięki czemu samej nazwy hosta jest wystarczająca. Jednak w sieciach wirtualnych wdrożonych za pomocą klasycznego modelu wdrażania, różnych usług w chmurze mają różne sufiksy DNS. W takiej sytuacji potrzebne będą nazwy FQDN do rozpoznawania nazw między różnych usług w chmurze. W sieciach wirtualnych wdrożonych za pomocą modelu wdrażania usługi Azure Resource Manager sufiks DNS jest spójność w ramach sieci wirtualnej, aby nazwa FQDN nie jest potrzebna. Zarówno w przypadku maszyn wirtualnych, jak i interfejsów sieciowych można przypisać nazwy DNS. Rozpoznawania nazw platformy Azure nie wymaga żadnej konfiguracji, ale nie jest on odpowiednim wyborem w przypadku wszystkich scenariuszy wdrożenia, zgodnie z opisem w poprzedniej tabeli.

> [!NOTE]
> Podczas korzystania z chmury usługi ról sieć web i proces roboczy, można także przejść wewnętrznych adresów IP wystąpień roli przy użyciu interfejsu API REST zarządzania usługami platformy Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST zarządzania usługi](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adres jest oparty na roli nazwę i numer wystąpienia. 
>
>

### <a name="features"></a>Funkcje

Rozpoznawania nazw platformy Azure obejmuje następujące funkcje:
* Łatwość użycia. Nie jest wymagana żadna konfiguracja.
* Wysoka dostępność. Nie potrzebujesz do tworzenia i zarządzania klastrami serwerów DNS.
* Usługi można używać w połączeniu z własnych serwerów DNS, aby rozwiązać zarówno lokalnych, jak i nazwy hosta platformy Azure.
* Umożliwia rozpoznawanie nazw między maszynami wirtualnymi i wystąpień roli w ramach jednej usługi w chmurze, bez potrzeby nazwy FQDN.
* Umożliwia rozpoznawanie nazw między maszynami wirtualnymi w sieciach wirtualnych, korzystające z modelu wdrażania usługi Azure Resource Manager, bez potrzeby wprowadzania nazwy FQDN. Sieci wirtualne w klasycznym modelu wdrażania wymaga nazwy FQDN, Rozpoznając nazwy w różnych usługach w chmurze. 
* Można użyć nazw hostów, które najlepiej opisują wdrożenia, a nie Praca z generowanych automatycznie nazw.

### <a name="considerations"></a>Zagadnienia do rozważenia

Kwestie do rozważenia, gdy używasz rozpoznawania nazw platformy Azure:
* Nie można zmodyfikować sufiks DNS utworzone przez platformę Azure.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługa WINS i NetBIOS są nieobsługiwane. Maszyny wirtualne w Eksploratorze Windows nie są widoczne.
* Nazwy hostów musi być zgodny z DNS. Nazwy, należy użyć tylko 0-9, a – z, i "-" i nie może zaczynać ani kończyć się znakiem "-".
* Ruch DNS jest ograniczane dla poszczególnych maszyn wirtualnych. Ograniczanie nie powinny mieć wpływ na większość aplikacji. Jeśli zostanie wykryty ograniczanie żądań, upewnij się, że włączone jest buforowanie po stronie klienta. Aby uzyskać więcej informacji, zobacz [Konfiguracja klienta DNS](#dns-client-configuration).
* Tylko maszyny wirtualne pierwszej 180 w usługach w chmurze są rejestrowane dla każdej sieci wirtualnej w klasycznym modelu wdrażania. To ograniczenie nie ma zastosowania do sieci wirtualnych w usłudze Azure Resource Manager.
* Adres IP serwera DNS platformy Azure to 168.63.129.16. Jest statyczny adres IP i nie ulegnie zmianie.

## <a name="dns-client-configuration"></a>Konfiguracja klienta DNS

W tej sekcji omówiono buforowanie po stronie klienta i ponownych prób po stronie klienta.

### <a name="client-side-caching"></a>Buforowanie po stronie klienta

Nie wszystkie zapytania DNS musi być wysyłane przez sieć. Buforowanie po stronie klienta, pomaga zmniejszyć opóźnienie i poprawić odporność na blips sieci, Rozpoznając cyklicznego zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają mechanizm time to live (TTL), umożliwiająca pamięci podręcznej w celu przechowywania rekordu tak długo, jak to możliwe bez wywierania wpływu na aktualność rekordów. W związku z tym buforowanie po stronie klienta jest odpowiedni dla większości sytuacji.

Domyślny klient Windows DNS ma wbudowaną pamięć podręczną DNS. Niektórych dystrybucjach systemu Linux nie są uwzględniane w pamięci podręcznej domyślnie. Jeśli okaże się, że nie ma lokalnej pamięci podręcznej, należy dodać pamięci podręcznej DNS do każdej maszyny Wirtualnej systemu Linux.

Istnieje szereg różnych buforowanie pakietów (takich jak dnsmasq) DNS. Poniżej przedstawiono sposób instalowania dnsmasq na najbardziej typowe dystrybucji:

* **Ubuntu (używa resolvconf)**:
  * Zainstaluj pakiet dnsmasq z `sudo apt-get install dnsmasq`.
* **SUSE (używa netconf)**:
  * Zainstaluj pakiet dnsmasq z `sudo zypper install dnsmasq`.
  * Włącz usługę dnsmasq z `systemctl enable dnsmasq.service`. 
  * Uruchom usługę dnsmasq z `systemctl start dnsmasq.service`. 
  * Edytuj **/etc/sysconfig/network/config**i zmień *NETCONFIG_DNS_FORWARDER = ""* do *dnsmasq*.
  * Aktualizuj resolv.conf z `netconfig update`, aby ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS.
* **OpenLogic (używa NetworkManager)**:
  * Zainstaluj pakiet dnsmasq z `sudo yum install dnsmasq`.
  * Włącz usługę dnsmasq z `systemctl enable dnsmasq.service`.
  * Uruchom usługę dnsmasq z `systemctl start dnsmasq.service`.
  * Dodaj *dołączana serwerami nazw domen-127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Uruchom ponownie usługę sieci za pomocą `service network restart`, aby ustawić pamięci podręcznej jako lokalnego rozpoznawania nazw DNS.

> [!NOTE]
> Pakiet dnsmasq jest tylko jeden z wielu pamięci podręcznych DNS dostępnych dla systemu Linux. Przed jego użyciem, przydatności do określonych potrzeb i sprawdź, czy brak pamięci podręcznej jest zainstalowany.
>
>
    
### <a name="client-side-retries"></a>Ponownych prób po stronie klienta

Usługa DNS jest głównie protokołu UDP. Ponieważ protokół UDP nie gwarantuje dostarczanie komunikatów, Logika ponawiania jest obsługiwana w samego protokołu DNS. Logika ponawiania różne, w zależności od preferencji twórcy może cechować się każdego klienta DNS (systemu operacyjnego):

* Systemy operacyjne Windows ponów próbę po jedną sekundę, a następnie ponownie po drugim dwóch sekund czterech sekund, a inny czterech sekund. 
* Domyślne próby instalacji dla systemu Linux po 5 sekundach. Firma Microsoft zaleca zmianę specyfikacji ponownych prób na pięć razy w jednosekundowych odstępach czasu.

Sprawdź bieżące ustawienia na maszynie Wirtualnej systemu Linux przy użyciu `cat /etc/resolv.conf`. Przyjrzyj się *opcje* linii, na przykład:

```bash
options timeout:1 attempts:5
```

Plik resolv.conf jest zwykle generowane automatycznie i nie można edytować. Określone kroki, aby dodać *opcje* dystrybucji zależą od wiersza:

* **Ubuntu** (używa resolvconf):
  1. Dodaj *opcje* wiersz do **/etc/resolvconf/resolv.conf.d/tail**.
  2. Uruchom `resolvconf -u` do zaktualizowania.
* **SUSE** (używa netconf):
  1. Dodaj *timeout:1 prób: 5* do **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parametru w **/etc/sysconfig/network/config**.
  2. Uruchom `netconfig update` do zaktualizowania.
* **OpenLogic** (używa NetworkManager):
  1. Dodaj *echo "Opcje timeout:1 prób: 5"* do **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Aktualizowanie za pomocą `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozpoznawanie nazw, która używa własnego serwera DNS

W tej sekcji obejmuje maszyny wirtualne, wystąpienia ról i aplikacji sieci web.

### <a name="vms-and-role-instances"></a>Maszyny wirtualne i wystąpienia roli

Twoje potrzeby rozpoznawania nazw może wyjść poza funkcji oferowanych przez platformę Azure. Na przykład może być konieczne Microsoft Windows Server Active Directory, domen, rozpoznawania nazw DNS między sieciami wirtualnymi. Aby uwzględnić te scenariusze, platforma Azure oferuje możliwość przy użyciu serwerów DNS.

Serwery DNS w sieci wirtualnej może przekazywać zapytań DNS do rozpoznawania cyklicznego na platformie Azure. Dzięki temu można rozpoznawać nazwy hostów w ramach tej sieci wirtualnej. Na przykład kontroler domeny (DC) działające na platformie Azure można odpowiada na zapytania DNS dla swojej domeny i przesyłania dalej wszystkich innych zapytań na platformie Azure. Przekazywanie zapytań pozwala maszyn wirtualnych wyświetlić swoje zasoby lokalne (za pośrednictwem kontrolera domeny) i nazwy hostów platformy Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do rozpoznawania cyklicznego na platformie Azure jest oferowana w ramach tego wirtualnego adresu IP 168.63.129.16.

Również przesyłania dalej DNS umożliwia rozpoznawanie nazw DNS między sieciami wirtualnymi i zezwala na maszynach w środowisku lokalnym do rozpoznawania nazw hostów platformy Azure. Aby można było rozpoznać nazwy hosta maszyny Wirtualnej, maszyna wirtualna musi znajdować się w tej samej sieci wirtualnej i skonfigurować serwer DNS na kwerendy nazwy hosta do przodu na platformie Azure. Ponieważ sufiks DNS różni się w poszczególnych sieciach wirtualnych, można użyć reguły warunkowego przesyłania dalej do wysyłania zapytań DNS do odpowiedniej sieci wirtualnej rozpoznawania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną, wykonując rozpoznawania nazw DNS między sieciami wirtualnymi, za pomocą tej metody. Usługi przesyłania dalej DNS przykład jest dostępny w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Wystąpienie roli można wykonać rozpoznawanie nazw maszyn wirtualnych w ramach tej samej sieci wirtualnej. Robi to przy użyciu w pełni kwalifikowaną nazwę domeny, która składa się z nazwy hosta maszyny Wirtualnej i **internal.cloudapp.net** sufiks DNS. Jednak w takim przypadku rozpoznawania nazw jest tylko pomyślne, jeśli wystąpienie roli ma nazwę maszyny Wirtualnej, zdefiniowane w [Role — schemat (pliku cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx).
> <Role name="<role-name>" vmName="<vm-name>">
>
> Wystąpienia roli, które należy wykonać rozpoznawanie nazw maszyn wirtualnych w innej sieci wirtualnej (nazwa FQDN przy użyciu **internal.cloudapp.net** sufiks) trzeba to zrobić przy użyciu metody opisanej w tej sekcji (niestandardowe serwery DNS przekazywania między dwie sieci wirtualne).
>

![Diagram przedstawiający DNS między sieciami wirtualnymi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Korzystając z rozpoznawania nazw platformy Azure, Azure Dynamic Host Configuration Protocol (DHCP) zawiera wewnętrzny sufiks DNS (**. internal.cloudapp.net**) do każdej maszyny Wirtualnej. Ten sufiks umożliwia rozpoznawanie nazwy hosta, ponieważ są rekordy nazw hosta **internal.cloudapp.net** strefy. Korzystając z własnego rozwiązania rozpoznawania nazw, ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ zakłócać innych architektur DNS (np. scenariusze przyłączone do domeny). Zamiast tego system Azure oferuje symbolu zastępczego niedziałającej (*reddog.microsoft.com*).

Jeśli to konieczne, można określić wewnętrznego sufiks DNS przy użyciu programu PowerShell lub interfejsu API:

* Dla sieci wirtualnych w modelach wdrażania usługi Azure Resource Manager, jest dostępna za pośrednictwem sufiks [interfejsu sieciowego interfejsu API REST](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces), [Get AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) polecenia cmdlet programu PowerShell, a [ Pokaż kartę sieciową sieci az](/cli/azure/network/nic#az-network-nic-show) polecenia wiersza polecenia platformy Azure.
* W klasycznych modeli wdrażania, jest dostępna za pośrednictwem sufiks [uzyskać interfejsu API wdrożenia](https://msdn.microsoft.com/library/azure/ee460804.aspx) wywołania lub [Get-AzureVM-debugowanie](/powershell/module/servicemanagement/azure/get-azurevm) polecenia cmdlet.

Jeśli przekazywanie zapytań na platformie Azure nie spełnia Twoich potrzeb, należy podać własne rozwiązanie DNS. W rozwiązaniu DNS musi:

* Zapewniają rozpoznawanie nazw przy użyciu odpowiedniego hosta [DDNS](virtual-networks-name-resolution-ddns.md), na przykład. Jeśli używasz DDNS może być konieczne wyłączenie oczyszczania rekordu DNS. Azure dzierżaw DHCP są długie i oczyszczania może usunąć rekordy DNS przedwcześnie. 
* Podaj odpowiednie rekursywnego rozpoznawania umożliwia rozpoznawanie nazw domen zewnętrznych.
* Być dostępne (TCP i UDP na porcie 53) od klientów, które służy i mogli uzyskać dostęp do Internetu.
* Zabezpieczone przed dostępem z Internetu, aby uniknąć zagrożenia powodowane przez zewnętrzne agentów.

> [!NOTE]
> Aby uzyskać najlepszą wydajność podczas korzystania z maszyn wirtualnych platformy Azure jako serwery DNS IPv6 powinna być wyłączona. A [publiczny adres IP](virtual-network-public-ip-address.md) powinien zostać przypisany do każdego serwera DNS, który znajduje się maszyna wirtualna. Analiza wydajności i optymalizacje podczas korzystania z systemu Windows Server jako serwer DNS, zobacz [nazwę wydajność rozpoznawania cyklicznego Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Aplikacje internetowe
Załóżmy, że trzeba wykonać rozpoznawanie nazw z aplikacji sieci web utworzone przy użyciu usługi App Service, połączone z siecią wirtualną, do maszyn wirtualnych w tej samej sieci wirtualnej. Oprócz konfigurowania niestandardowe DNS serwera, który ma przesyłania dalej DNS, która przekazuje zapytania na platformie Azure (wirtualny adres IP, 168.63.129.16), wykonaj następujące czynności:
1. Włączanie integracji sieci wirtualnej dla aplikacji sieci web, jeśli nie jeszcze zrobione, zgodnie z opisem w [Integrowanie aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. W witrynie Azure portal wybierz hostowanie aplikacji sieci web dla planu usługi App Service **Synchronizuj sieć** w obszarze **sieć**, **Integracja z siecią wirtualną**.

    ![Zrzut ekranu przedstawiający rozpoznawania nazw sieci wirtualnej](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Trzeba wykonać rozpoznawanie nazw z aplikacji sieci web utworzone przy użyciu usługi App Service, połączone z siecią wirtualną, do maszyn wirtualnych w innej sieci wirtualnej, należy użyć niestandardowych serwerów DNS w obu sieciach wirtualnych w następujący sposób:

* Konfigurowanie serwera DNS w sieci wirtualnej docelowy, na maszynie Wirtualnej, która może przesyłają również zapytania cyklicznego programu rozpoznawania nazw na platformie Azure (wirtualny adres IP 168.63.129.16). Usługi przesyłania dalej DNS przykład jest dostępny w [galerii szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurowanie usługi przesyłania dalej DNS w sieci wirtualnej źródła na maszynie Wirtualnej. Konfigurowanie tej usługi przesyłania dalej DNS do przesyłania zapytań do serwera DNS w docelowej sieci wirtualnej.
* Skonfiguruj serwer DNS źródło w ustawieniach sieci wirtualnej źródła.
* Włączanie integracji sieci wirtualnej dla aplikacji sieci web utworzyć łącze do źródła sieci wirtualnej, postępując zgodnie z instrukcjami w [Integrowanie aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* W witrynie Azure portal wybierz hostowanie aplikacji sieci web dla planu usługi App Service **Synchronizuj sieć** w obszarze **sieć**, **Integracja z siecią wirtualną**.

## <a name="specify-dns-servers"></a>Określ serwer DNS
Korzystając z własnych serwerów DNS, platforma Azure oferuje możliwość określenia wielu serwerów DNS na sieć wirtualną. Można również określić wiele serwerów DNS dla każdego interfejsu sieciowego (dla usługi Azure Resource Manager) lub dla usługi w chmurze (w przypadku klasycznego modelu wdrażania). Serwery DNS określona dla sieci interfejs lub usługi w chmurze przeprowadzanie pierwszeństwo przy użyciu serwerów DNS określona dla sieci wirtualnej.

> [!NOTE]
> Nie należy edytować właściwości połączenia sieciowego, takich jak serwer DNS adresy IP, bezpośrednio z poziomu maszyn wirtualnych Windows. Jest to spowodowane może uzyskać wymazane podczas usługi poprawianie po pobiera karty sieci wirtualnej.
>
>

Korzystając z modelu wdrażania usługi Azure Resource Manager, można określić serwery DNS dla sieci wirtualnej i interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [zarządzanie siecią wirtualną](manage-virtual-network.md) i [zarządzania interfejsem sieciowym](virtual-network-network-interface.md).

> [!NOTE]
> Jeśli postanowisz dla niestandardowego serwera DNS dla sieci wirtualnej, należy określić co najmniej jeden adres IP serwera DNS; w przeciwnym razie sieci wirtualnej zignoruje konfiguracji i zamiast tego użyj DNS platformy Azure.
>
>

Korzystając z klasycznego modelu wdrażania, można określić serwery DNS dla sieci wirtualnej w witrynie Azure portal lub [plik konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100). Dla usług cloud services, należy określić serwery DNS za pośrednictwem [pliku konfiguracji usługi](https://msdn.microsoft.com/library/azure/ee758710) lub za pomocą programu PowerShell, [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Jeśli zmienisz ustawienia DNS dla sieci wirtualnej lub maszyny wirtualnej, która została już wdrożona, musisz ponownie uruchomić każdy dotyczy maszyny Wirtualnej na potrzeby zmiany zaczęły obowiązywać.
>
>

## <a name="next-steps"></a>Kolejne kroki

Model wdrażania usługi Azure Resource Manager:

* [Zarządzanie siecią wirtualną](manage-virtual-network.md)
* [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md)

Klasyczny model wdrażania:

* [Azure Service Configuration Schema](https://msdn.microsoft.com/library/azure/ee758710)
* [Schemat konfiguracji sieci wirtualnej](https://msdn.microsoft.com/library/azure/jj157100)
* [Skonfiguruj sieć wirtualną przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md)
