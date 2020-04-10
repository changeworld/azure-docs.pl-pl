---
title: Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure
titlesuffix: Azure Virtual Network
description: Scenariusze rozpoznawania nazw dla usługi Azure IaaS, rozwiązań hybrydowych, między różnymi usługami w chmurze, usługą Active Directory i przy użyciu własnego serwera DNS.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: d7d0699718642a7eb9f85b2e8a86623092c34365
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010566"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure

W zależności od sposobu używania platformy Azure do hostowania rozwiązań IaaS, PaaS i rozwiązań hybrydowych może być konieczne zezwolenie maszynom wirtualnym i innym zasobom wdrożonym w sieci wirtualnej na komunikowanie się ze sobą. Chociaż komunikację można włączyć przy użyciu adresów IP, znacznie prostsze jest używanie nazw, które można łatwo zapamiętać i nie zmieniać. 

Gdy zasoby wdrożone w sieciach wirtualnych muszą rozpoznawać nazwy domen na wewnętrzne adresy IP, mogą użyć jednej z trzech metod:

* [Strefy prywatne usługi Azure DNS](../dns/private-dns-overview.md)
* [Rozpoznawanie nazw dostarczonych przez platformę Azure](#azure-provided-name-resolution)
* [Rozpoznawanie nazw, które używa własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server) (które może przesyłać dalej zapytania do serwerów DNS dostarczonych przez platformę Azure)

Używany typ rozpoznawania nazw zależy od tego, w jaki sposób zasoby muszą komunikować się ze sobą. W poniższej tabeli przedstawiono scenariusze i odpowiadające im rozwiązania rozpoznawania nazw:

> [!NOTE]
> Strefy prywatne usługi Azure DNS są preferowanym rozwiązaniem i zapewniają elastyczność w zarządzaniu strefami i rekordami DNS. Aby uzyskać więcej informacji, zobacz [Using Azure DNS for private domains (Korzystanie z usługi Azure DNS na potrzeby domen prywatnych)](../dns/private-dns-overview.md).

> [!NOTE]
> Jeśli używasz usługi Azure Provided DNS, odpowiedni sufiks DNS zostanie automatycznie zastosowany do maszyn wirtualnych. W przypadku wszystkich innych opcji należy użyć w pełni kwalifikowanych nazw domen (FQDN) lub ręcznie zastosować odpowiedni sufiks DNS do maszyn wirtualnych.

| **Scenariusz** | **Rozwiązanie** | **Sufiks DNS** |
| --- | --- | --- |
| Rozpoznawanie nazw między maszynami wirtualnymi znajdującymi się w tej samej sieci wirtualnej lub wystąpieniami roli usługi Azure Cloud Services w tej samej usłudze w chmurze. | [Strefy prywatne usługi Azure DNS](../dns/private-dns-overview.md) lub [rozpoznawanie nazw dostarczonych przez platformę Azure](#azure-provided-name-resolution) |Nazwa hosta lub nazwa FQDN |
| Rozpoznawanie nazw między maszynami wirtualnymi w różnych sieciach wirtualnych lub wystąpieniami ról w różnych usługach w chmurze. |[Strefy prywatne usługi Azure DNS](../dns/private-dns-overview.md) lub serwery DNS zarządzane przez klienta przesyłane dalej kwerendy między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazw z usługi Azure App Service (Web App, Function lub Bot) przy użyciu integracji sieci wirtualnej do wystąpień roli lub maszyn wirtualnych w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłające zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazw z aplikacji sieci Web usługi app service do maszyn wirtualnych w tej samej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłające zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazw z aplikacji app service web apps w jednej sieci wirtualnej do maszyn wirtualnych w innej sieci wirtualnej. |Serwery DNS zarządzane przez klienta przesyłające zapytania między sieciami wirtualnymi w celu rozwiązania przez platformę Azure (serwer proxy DNS). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazw komputerów i usług lokalnych z maszyn wirtualnych lub wystąpień roli na platformie Azure. |Serwery DNS zarządzane przez klienta (lokalny kontroler domeny, lokalny kontroler domeny tylko do odczytu lub pomocniczy dns zsynchronizowany przy użyciu transferów stref). Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Rozpoznawanie nazwy hostów platformy Azure z komputerów lokalnych. |Przesyłaj dalej zapytania do serwera proxy DNS zarządzanego przez klienta w odpowiedniej sieci wirtualnej, serwer proxy przekazuje zapytania do platformy Azure w celu rozwiązania. Zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko FQDN |
| Odwróć DNS dla wewnętrznych serwerów IP. |[Strefy prywatne usługi Azure DNS](../dns/private-dns-overview.md) lub [rozpoznawanie nazw pod warunkiem platformy Azure](#azure-provided-name-resolution) lub [rozpoznawanie nazw przy użyciu własnego serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Nie dotyczy |
| Rozpoznawanie nazw między maszynami wirtualnymi lub wystąpieniami ról znajdującymi się w różnych usługach w chmurze, a nie w sieci wirtualnej. |Nie dotyczy. Łączność między maszynami wirtualnymi i wystąpieniami ról w różnych usługach w chmurze nie jest obsługiwana poza siecią wirtualną. |Nie dotyczy|

## <a name="azure-provided-name-resolution"></a>Rozpoznawanie nazw dostarczonych przez platformę Azure

Rozpoznawanie nazw pod warunkiem platformy Azure zapewnia tylko podstawowe autorytatywne możliwości DNS. Jeśli użyjesz tej opcji, nazwy i rekordy stref DNS będą automatycznie zarządzane przez platformę Azure i nie będzie można kontrolować nazw stref DNS ani cyklu życia rekordów DNS. Jeśli potrzebujesz w pełni funkcjonego rozwiązania DNS dla sieci wirtualnych, musisz użyć [prywatnych stref dns platformy Azure](../dns/private-dns-overview.md) lub [serwerów DNS zarządzanych przez klienta.](#name-resolution-that-uses-your-own-dns-server)

Wraz z rozdzielczością publicznych nazw DNS platforma Azure zapewnia wewnętrzne rozpoznawanie nazw maszyn wirtualnych i wystąpień ról znajdujących się w tej samej sieci wirtualnej lub usłudze w chmurze. Maszyny wirtualne i wystąpienia w usłudze w chmurze współużytkuje ten sam sufiks DNS, więc sama nazwa hosta jest wystarczająca. Jednak w sieciach wirtualnych wdrożonych przy użyciu klasycznego modelu wdrażania różne usługi w chmurze mają różne sufiksy DNS. W tej sytuacji należy FQDN do rozpoznawania nazw między różnymi usługami w chmurze. W sieciach wirtualnych wdrożonych przy użyciu modelu wdrażania usługi Azure Resource Manager sufiks DNS jest spójny na wszystkich maszynach wirtualnych w sieci wirtualnej, więc nazwa FQDN nie jest potrzebna. Nazwy DNS można przypisać zarówno do maszyn wirtualnych, jak i interfejsów sieciowych. Mimo że rozpoznawanie nazw dostarczonych przez platformę Azure nie wymaga żadnej konfiguracji, nie jest to odpowiedni wybór dla wszystkich scenariuszy wdrażania, jak opisano w poprzedniej tabeli.

> [!NOTE]
> Podczas korzystania z usług w chmurze ról sieci web i procesu roboczego, można również uzyskać dostęp do wewnętrznych adresów IP wystąpień ról przy użyciu interfejsu API REST usługi Azure Service Management. Aby uzyskać więcej informacji, zobacz [odwołanie do interfejsu API REST zarządzania usługami](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adres jest oparty na nazwie roli i numerze wystąpienia. 
>

### <a name="features"></a>Funkcje

Rozpoznawanie nazw dostarczonych przez platformę Azure zawiera następujące funkcje:
* Łatwość użycia. Nie jest wymagana żadna konfiguracja.
* Wysoka dostępność. Nie trzeba tworzyć klastrów własnych serwerów DNS i zarządzać nimi.
* Usługi można używać w połączeniu z własnymi serwerami DNS, aby rozpoznać nazwy hostów lokalnych i platformy Azure.
* Rozpoznawania nazw między maszynami wirtualnymi i wystąpień roli w ramach tej samej usługi w chmurze, bez konieczności fqdn.
* Rozpoznawania nazw między maszynami wirtualnymi w sieciach wirtualnych korzystających z modelu wdrażania usługi Azure Resource Manager, bez konieczności korzystania z nazwy FQDN. Sieci wirtualne w klasycznym modelu wdrażania wymagają nazwy FQDN podczas rozpoznawania nazw w różnych usługach w chmurze. 
* Można użyć nazw hostów, które najlepiej opisują wdrożenia, zamiast pracować z automatycznie generowanymi nazwami.

### <a name="considerations"></a>Zagadnienia do rozważenia

Punkty, które należy wziąć pod uwagę podczas korzystania z rozpoznawania nazw dostarczonych przez platformę Azure:
* Nie można zmodyfikować sufiksu DNS utworzonego przez platformę Azure.
* Wyszukiwanie DNS jest ograniczone do sieci wirtualnej. Nazwy DNS utworzone dla jednej sieci wirtualnej nie mogą być rozpoznawane z innych sieci wirtualnych.
* Nie można ręcznie zarejestrować własnych rekordów.
* Usługi WINS i NetBIOS nie są obsługiwane. Maszyny wirtualne nie są widoczne w Eksploratorze Windows.
* Nazwy hostów muszą być zgodne z systemem DNS. Nazwy muszą używać tylko 0-9, a-z i '-' i nie mogą rozpoczynać się ani kończyć na '-'.
* Ruch zapytań DNS jest ograniczany dla każdej maszyny Wirtualnej. Ograniczanie przepustowości nie powinno mieć wpływu na większość aplikacji. Jeśli dławienie żądania jest przestrzegane, upewnij się, że buforowanie po stronie klienta jest włączona. Aby uzyskać więcej informacji, zobacz [Konfiguracja klienta DNS](#dns-client-configuration).
* Tylko maszyny wirtualne w pierwszych 180 usług w chmurze są rejestrowane dla każdej sieci wirtualnej w klasycznym modelu wdrażania. Ten limit nie dotyczy sieci wirtualnych w usłudze Azure Resource Manager.
* Adres IP usługi Azure DNS to 168.63.129.16. Jest to statyczny adres IP i nie ulegnie zmianie.

### <a name="reverse-dns-considerations"></a>Odwróć zagadnienia dotyczące systemu DNS
Odwrócony DNS jest obsługiwany we wszystkich sieciach wirtualnych opartych na arm. Można wystawiać kwerendy DNS (PTR) do mapowania adresów IP maszyn wirtualnych na nazwy FQDN maszyn wirtualnych.
* Wszystkie zapytania PTR dotyczące adresów IP maszyn wirtualnych \[zwracają nazwy\]FQDN o identyfikatorze vmname .internal.cloudapp.net
* Wyszukiwanie do przodu na fqdns \[formularza\]vmname .internal.cloudapp.net rozwiąże adres IP przypisany do maszyny wirtualnej.
* Jeśli sieć wirtualna jest połączona ze [strefami prywatnymi usługi Azure DNS](../dns/private-dns-overview.md) jako sieć wirtualna rejestracji, kwerendy DNS odwrotnej zwracają dwa rekordy. Jeden rekord będzie vmname \[\]formularza . [priatednszonename] i inne będą \[w formie\]vmname .internal.cloudapp.net
* Wyszukiwanie wstecznego DNS jest ograniczone do danej sieci wirtualnej, nawet jeśli jest równorzędne z innymi sieciami wirtualnymi. Wycofane kwerendy DNS (zapytania PTR) dla adresów IP maszyn wirtualnych znajdujących się w sieciach wirtualnych równorzędnych zwracają NXDOMAIN.

> [!NOTE]
> Jeśli chcesz, aby wyszukiwanie odwrotnego DNS obejmowało sieć wirtualną, możesz utworzyć strefę wyszukiwania wstecznego (in-addr.arpa) [Platformy Azure DNS](../dns/private-dns-overview.md) i łączy ją z wieloma sieciami wirtualnymi. Musisz jednak ręcznie zarządzać rekordami odwrotnego DNS dla maszyn wirtualnych.
>


## <a name="dns-client-configuration"></a>Konfiguracja klienta DNS

W tej sekcji opisano buforowanie po stronie klienta i ponownych prób po stronie klienta.

### <a name="client-side-caching"></a>Buforowanie po stronie klienta

Nie każda kwerenda DNS musi być wysyłana przez sieć. Buforowanie po stronie klienta pomaga zmniejszyć opóźnienia i zwiększyć odporność na blips sieci, rozwiązując powtarzające się zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają mechanizm czasu wygaśnięcia (TTL), który umożliwia pamięci podręcznej przechowywanie rekordu tak długo, jak to możliwe bez wpływu na świeżość rekordu. W związku z tym buforowanie po stronie klienta jest odpowiednie w większości sytuacji.

Domyślny klient DNS systemu Windows ma wbudowaną pamięć podręczną DNS. Niektóre dystrybucje systemu Linux domyślnie nie zawierają buforowania. Jeśli okaże się, że nie ma już lokalnej pamięci podręcznej, dodaj pamięć podręczną DNS do każdej maszyny Wirtualnej systemu Linux.

Dostępnych jest wiele różnych pakietów buforowania DNS (takich jak dnsmasq). Oto jak zainstalować dnsmasq na najpopularniejszych dystrybucjach:

* **Ubuntu (używa resolvconf)**:
  * Zainstaluj pakiet dnsmasq `sudo apt-get install dnsmasq`z .
* **SUSE (używa netconf)**:
  * Zainstaluj pakiet dnsmasq `sudo zypper install dnsmasq`z .
  * Włącz usługę dnsmasq `systemctl enable dnsmasq.service`za pomocą pliku . 
  * Uruchom usługę dnsmasq `systemctl start dnsmasq.service`z . 
  * Edytuj **/etc/sysconfig/network/config**i zmień *NETCONFIG_DNS_FORWARDER=""* na *dnsmasq*.
  * Zaktualizuj plik `netconfig update`resolv.conf za pomocą programu , aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.
* **CentOS (używa NetworkManager)**:
  * Zainstaluj pakiet dnsmasq `sudo yum install dnsmasq`z .
  * Włącz usługę dnsmasq `systemctl enable dnsmasq.service`za pomocą pliku .
  * Uruchom usługę dnsmasq `systemctl start dnsmasq.service`z .
  * Dodaj *prepend domain-name-servers 127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Uruchom ponownie usługę `service network restart`sieciową za pomocą programu , aby ustawić pamięć podręczną jako lokalny program rozpoznawania nazw DNS.

> [!NOTE]
> Pakiet dnsmasq jest tylko jedną z wielu pamięci podręcznych DNS dostępnych dla Linuksa. Przed użyciem należy sprawdzić jego przydatność do określonych potrzeb i sprawdzić, czy nie zainstalowano żadnej innej pamięci podręcznej.

    
### <a name="client-side-retries"></a>Ponownych prób po stronie klienta

DNS jest przede wszystkim protokołem UDP. Ponieważ protokół UDP nie gwarantuje dostarczania wiadomości, logika ponawiania jest obsługiwana w samym protokole DNS. Każdy klient DNS (system operacyjny) może wykazywać inną logikę ponawiania prób, w zależności od preferencji twórcy:

* Systemy operacyjne Windows ponowiają próbę po jednej sekundzie, a następnie ponownie po kolejnych dwóch sekundach, czterech sekundach i kolejnych czterech sekundach. 
* Domyślna konfiguracja systemu Linux ponawia ponawia ponawianie po pięciu sekundach. Zaleca się zmianę specyfikacji ponawiania próby na pięć razy, w odstępach jednosekundowych.

Sprawdź bieżące ustawienia na maszynie Wirtualnej z systemem Linux za pomocą `cat /etc/resolv.conf`pliku . Spójrz na linię *opcji,* na przykład:

```bash
options timeout:1 attempts:5
```

Plik resolv.conf jest zwykle generowany automatycznie i nie powinien być edytowany. Określone kroki dodawania linii *opcji* różnią się w zależności od rozkładu:

* **Ubuntu** (używa resolvconf):
  1. Dodaj linię *opcji* do **/etc/resolvconf/resolv.conf.d/tail**.
  2. Uruchom, `resolvconf -u` aby zaktualizować.
* **SUSE** (używa netconf):
  1. Dodaj *limit czasu:1 próby:5* do parametru **NETCONFIG_DNS_RESOLVER_OPTIONS=""** w **/etc/sysconfig/network/config**.
  2. Uruchom, `netconfig update` aby zaktualizować.
* **CentOS** (używa NetworkManager):
  1. Dodaj *echo "opcje timeout:1 attempts:5"* do **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Aktualizacja `service network restart`za pomocą pliku .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozpoznawanie nazw, które używa własnego serwera DNS

W tej sekcji omówiono maszyny wirtualne, wystąpienia ról i aplikacje sieci web.

### <a name="vms-and-role-instances"></a>Maszyny wirtualne i wystąpienia ról

Potrzeby dotyczące rozpoznawania nazw mogą wykraczać poza funkcje udostępniane przez platformę Azure. Na przykład może być konieczne użycie domen usługi Active Directory systemu Microsoft Windows Server, rozpoznawanie nazw DNS między sieciami wirtualnymi. Aby omówić te scenariusze, platforma Azure zapewnia możliwość korzystania z własnych serwerów DNS.

Serwery DNS w sieci wirtualnej mogą przesyłać dalej kwerendy DNS do cyklicznych programów rozpoznawania nazw na platformie Azure. Dzięki temu można rozpoznać nazwy hostów w tej sieci wirtualnej. Na przykład kontroler domeny (DC) działający na platformie Azure może odpowiadać na zapytania DNS dla swoich domen i przesyłać dalej wszystkie inne zapytania na platformę Azure. Przekazywanie zapytań umożliwia maszynom wirtualnym wyświetlanie zarówno zasobów lokalnych (za pośrednictwem kontrolera domeny), jak i nazw hostów dostarczonych przez platformę Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do cyklicznych programów rozpoznawania nazw na platformie Azure jest zapewniany za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przekazywanie dns umożliwia również rozpoznawanie dns między sieciami wirtualnymi i umożliwia maszynom lokalnym rozpoznawanie nazw hostów dostarczonych przez platformę Azure. Aby rozpoznać nazwę hosta maszyny Wirtualnej, maszyna wirtualna serwera DNS musi znajdować się w tej samej sieci wirtualnej i być skonfigurowana do przesyłania dalej zapytań o nazwę hosta na platformę Azure. Ponieważ sufiks DNS jest inny w każdej sieci wirtualnej, można użyć reguł przekazywania warunkowego do wysyłania zapytań DNS do właściwej sieci wirtualnej w celu rozwiązania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną wykonującą rozpoznawanie DNS między sieciami wirtualnymi przy użyciu tej metody. Przykładowa usługa przesyłania dalej DNS jest dostępna w [galerii Szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) i [usłudze GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)

> [!NOTE]
> Wystąpienie roli może wykonywać rozpoznawanie nazw maszyn wirtualnych w tej samej sieci wirtualnej. Robi to przy użyciu nazwy FQDN, która składa się z nazwy hosta maszyny Wirtualnej i **internal.cloudapp.net** sufiks DNS. Jednak w tym przypadku rozpoznawanie nazw jest skuteczne tylko wtedy, gdy wystąpienie roli ma nazwę maszyny Wirtualnej zdefiniowaną w [schemacie roli (plik cscfg).](https://msdn.microsoft.com/library/azure/jj156212.aspx)
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Wystąpienia roli, które muszą wykonywać rozpoznawanie nazw maszyn wirtualnych w innej sieci wirtualnej (FQDN przy użyciu sufiksu **internal.cloudapp.net)** muszą to zrobić przy użyciu metody opisanej w tej sekcji (niestandardowe serwery DNS przesyłane dalej między dwiema sieciami wirtualnymi).
>

![Diagram DNS między sieciami wirtualnymi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

W przypadku korzystania z rozpoznawania nazw dostarczonych przez platformę Azure protokół DHCP (Azure Dynamic Host Configuration Protocol) zapewnia wewnętrzny sufiks DNS **(internal.cloudapp.net**) każdej maszynie wirtualnej. Ten sufiks umożliwia rozpoznawanie nazw hostów, ponieważ rekordy nazw hosta znajdują się w strefie **internal.cloudapp.net.** Podczas korzystania z własnego rozwiązania rozpoznawania nazw ten sufiks nie jest dostarczany do maszyn wirtualnych, ponieważ zakłóca inne architektury DNS (takie jak scenariusze przyłączone do domeny). Zamiast tego platforma Azure udostępnia niedziałający symbol zastępczy *(reddog.microsoft.com*).

W razie potrzeby można określić wewnętrzny sufiks DNS przy użyciu programu PowerShell lub interfejsu API:

* W przypadku sieci wirtualnych w modelach wdrażania usługi Azure Resource Manager sufiks jest dostępny za pośrednictwem [interfejsu sieciowego INTERFEJSU INTERFEJSU INTERFEJSU API](https://docs.microsoft.com/rest/api/virtualnetwork/networkinterfaces)REST, polecenia cmdlet [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) PowerShell, a [sieć az kowadła polecenie](/cli/azure/network/nic#az-network-nic-show) interfejsu wiersza polecenia Platformy Azure.
* W klasycznych modelach wdrażania sufiks jest dostępny za pośrednictwem wywołania [interfejsu API wdrażania pobierz](https://msdn.microsoft.com/library/azure/ee460804.aspx) lub polecenia cmdlet [Get-AzureVM -debug.](/powershell/module/servicemanagement/azure/get-azurevm)

Jeśli przekazywanie zapytań na platformę Azure nie odpowiada Twoim potrzebom, należy podać własne rozwiązanie DNS. Rozwiązanie DNS musi:

* Podaj odpowiednią rozdzielczość nazw hosta, na przykład za pośrednictwem [DDNS.](virtual-networks-name-resolution-ddns.md) Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczania rekordów DNS. Dzierżawy usługi Azure DHCP są długie, a oczyszczanie może przedwcześnie usunąć rekordy DNS. 
* Zapewnij odpowiednią rozdzielczość cykliczną, aby umożliwić rozpoznawanie zewnętrznych nazw domen.
* Bądź dostępny (TCP i UDP na porcie 53) od obsługiwanych klientów i mieć dostęp do Internetu.
* Być zabezpieczone przed dostępem z Internetu, aby złagodzić zagrożenia stwarzane przez agentów zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność, gdy używasz maszyn wirtualnych platformy Azure jako serwerów DNS, protokół IPv6 powinien zostać wyłączony.

### <a name="web-apps"></a>Aplikacje internetowe
Załóżmy, że należy wykonać rozpoznawanie nazw z aplikacji sieci web utworzonej przy użyciu usługi App Service, połączonej z siecią wirtualną, do maszyn wirtualnych w tej samej sieci wirtualnej. Oprócz konfigurowania niestandardowego serwera DNS, który ma usługę przesyłania dalej DNS, która przekazuje zapytania do platformy Azure (wirtualny adres IP 168.63.129.16), wykonaj następujące kroki:
1. Włącz integrację sieci wirtualnej dla aplikacji sieci web, jeśli nie została już wykonana, zgodnie z opisem w [zintegruj aplikację z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. W witrynie Azure portal dla planu usługi app service obsługującej aplikację sieci web wybierz pozycję **Synchronizuj sieć** w obszarze **Sieć**, **Integracja sieci wirtualnej**.

    ![Zrzut ekranu przedstawiający rozpoznawanie nazw sieci wirtualnej](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Jeśli musisz wykonać rozpoznawanie nazw z aplikacji sieci web utworzonej przy użyciu usługi App Service, połączonej z siecią wirtualną, z maszynami wirtualnymi w innej sieci wirtualnej, musisz użyć niestandardowych serwerów DNS w obu sieciach wirtualnych w następujący sposób:

* Skonfiguruj serwer DNS w docelowej sieci wirtualnej na maszynie Wirtualnej, która może również przesyłać dalej zapytania do cyklicznego programu rozpoznawania nazw na platformie Azure (wirtualny adres IP 168.63.129.16). Przykładowa usługa przesyłania dalej DNS jest dostępna w [galerii Szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) i [usłudze GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) 
* Konfigurowanie usługi przesyłania dalej DNS w źródłowej sieci wirtualnej na maszynie wirtualnej. Skonfiguruj tę usługę przesyłania dalej DNS do przekazywania zapytań do serwera DNS w docelowej sieci wirtualnej.
* Skonfiguruj źródłowy serwer DNS w ustawieniach źródłowej sieci wirtualnej.
* Włącz integrację sieci wirtualnej dla aplikacji sieci web, aby połączyć się ze źródłową siecią wirtualną, postępując zgodnie z instrukcjami w [Integruj aplikację z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* W witrynie Azure portal dla planu usługi app service obsługującej aplikację sieci web wybierz pozycję **Synchronizuj sieć** w obszarze **Sieć**, **Integracja sieci wirtualnej**.

## <a name="specify-dns-servers"></a>Określanie serwerów DNS
Podczas korzystania z własnych serwerów DNS platforma Azure umożliwia określenie wielu serwerów DNS na sieć wirtualną. Można również określić wiele serwerów DNS dla każdego interfejsu sieciowego (w przypadku usługi Azure Resource Manager) lub dla usługi w chmurze (w przypadku klasycznego modelu wdrażania). Serwery DNS określone dla interfejsu sieciowego lub usługi w chmurze mają pierwszeństwo przed serwerami DNS określonymi dla sieci wirtualnej.

> [!NOTE]
> Właściwości połączenia sieciowego, takie jak adresy IP serwera DNS, nie powinny być edytowane bezpośrednio na maszynach wirtualnych. Dzieje się tak dlatego, że mogą zostać wymazane podczas leczenia usługi po wymianie adaptera sieci wirtualnej. Dotyczy to zarówno maszyn wirtualnych z systemem Windows, jak i Linux.

Korzystając z modelu wdrażania usługi Azure Resource Manager, można określić serwery DNS dla sieci wirtualnej i interfejsu sieciowego. Aby uzyskać szczegółowe informacje, zobacz [Zarządzanie siecią wirtualną](manage-virtual-network.md) i [zarządzanie interfejsem sieciowym](virtual-network-network-interface.md).

> [!NOTE]
> Jeśli zdecydujesz się na niestandardowy serwer DNS dla sieci wirtualnej, musisz określić co najmniej jeden adres IP serwera DNS; w przeciwnym razie sieć wirtualna zignoruje konfigurację i zamiast tego użyje systemu DNS dostarczonego przez platformę Azure.

Korzystając z klasycznego modelu wdrażania, można określić serwery DNS dla sieci wirtualnej w witrynie Azure portal lub [pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100). W przypadku usług w chmurze można określić serwery DNS za pomocą [pliku konfiguracji usługi](https://msdn.microsoft.com/library/azure/ee758710) lub za pomocą programu PowerShell za pomocą programu [New-AzureVM](/powershell/module/servicemanagement/azure/new-azurevm).

> [!NOTE]
> Jeśli zmienisz ustawienia DNS dla sieci wirtualnej lub maszyny wirtualnej, która jest już wdrożona, aby nowe ustawienia DNS zostały zastosowane, należy przeprowadzić odnowienie dzierżawy DHCP na wszystkich maszynach wirtualnych, których dotyczy problem w sieci wirtualnej. W przypadku maszyn wirtualnych z systemem operacyjnym Windows `ipconfig /renew` można to zrobić, wpisując je bezpośrednio na maszynie wirtualnej. Kroki różnią się w zależności od systemu operacyjnego. Zobacz odpowiednią dokumentację dla danego typu systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

Model wdrażania usługi Azure Resource Manager:

* [Zarządzanie siecią wirtualną](manage-virtual-network.md)
* [Zarządzanie interfejsem sieciowym](virtual-network-network-interface.md)

Klasyczny model wdrażania:

* [Schemat konfiguracji usługi Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schemat konfiguracji sieci wirtualnej](https://msdn.microsoft.com/library/azure/jj157100)
* [Konfigurowanie sieci wirtualnej przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md)
