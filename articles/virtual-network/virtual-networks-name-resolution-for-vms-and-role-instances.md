---
title: Rozpoznawanie nazw dla zasobów w sieci wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Scenariusze rozpoznawania nazwy IaaS platformy Azure, rozwiązania hybrydowe między usługi w chmurze różnych, usługi Active Directory i przy użyciu serwera DNS.
services: virtual-network
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: 5d73edde-979a-470a-b28c-e103fcf07e3e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: subsarma
ms.openlocfilehash: 32d4f72afb4cd18e6b66c52eb78b2fc7b6b75cbd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Rozpoznawanie nazw dla zasobów w sieci wirtualnych platformy Azure

W zależności od tego, jak używasz usługi Azure do hostowania IaaS i PaaS, hybrydowych rozwiązań konieczne może być Zezwalaj na maszynach wirtualnych (VM) i innych zasobów wdrożony w sieci wirtualnej do komunikowania się ze sobą. Mimo że można włączyć komunikację przy użyciu adresów IP, jest znacznie prostsza do użyć nazw, które można łatwo zapamiętać i nie należy zmieniać. 

Jeśli wdrożono w sieciach wirtualnych zasobów należy do rozpoznawania nazw domen do adresów IP, mogą korzystać z jednej z dwóch metod:

* [Rozpoznawanie nazw platformy Azure](#azure-provided-name-resolution)
* [Nazwa rozwiązania, który używa serwera DNS](#name-resolution-that-uses-your-own-dns-server) (która może przesłać kwerendy do serwerów DNS platformy Azure) 

Rodzaj rozpoznawania nazw, których używasz zależy od tego, jak zasoby muszą komunikować się ze sobą. W poniższej tabeli przedstawiono scenariusze i odpowiedniego rozwiązania rozpoznawania nazwy:

> [!NOTE]
> W zależności od scenariusza można użyć funkcji stref DNS prywatnego Azure, która jest obecnie w wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [przy użyciu usługi Azure DNS dla domen prywatnej](../dns/private-dns-overview.md).
>

| **Scenariusz** | **Rozwiązania** | **Suffix** |
| --- | --- | --- |
| Rozpoznawanie nazw między maszynami wirtualnymi znajduje się w tej samej sieci wirtualnej lub usługi w chmurze Azure wystąpień roli w tej samej usłudze w chmurze. | [Strefy DNS prywatnego Azure](../dns/private-dns-overview.md) lub [rozpoznawania nazw platformy Azure](#azure-provided-name-resolution) |Nazwa hosta lub nazwa FQDN |
| Rozpoznawanie nazw między maszynami wirtualnymi w różnych sieciach wirtualnych lub wystąpień roli w innej chmurze usługi. |[Strefy DNS prywatnego Azure](../dns/private-dns-overview.md) lub serwery zarządzane przez klienta DNS przesyłania zapytań między sieciami wirtualnymi rozpoznanie przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania w usłudze Azure App Service (aplikacja sieci Web, funkcji lub Bot) przy użyciu integracji sieci wirtualnej do wystąpień roli lub maszyn wirtualnych w tej samej sieci wirtualnej. |Zarządzany przez klienta DNS serwerów przekazujących zapytań między sieciami wirtualnymi rozpoznanie przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania z aplikacji sieci Web usługi aplikacji do maszyn wirtualnych w tej samej sieci wirtualnej. |Zarządzany przez klienta DNS serwerów przekazujących zapytań między sieciami wirtualnymi rozpoznanie przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Nazwa rozwiązania z aplikacji sieci Web usługi aplikacji w jednej sieci wirtualnych do maszyn wirtualnych w innej sieci wirtualnej. |Zarządzany przez klienta DNS serwerów przekazujących zapytań między sieciami wirtualnymi rozpoznanie przez platformę Azure (serwer proxy DNS). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server-for-web-apps). |Tylko nazwa FQDN |
| Rozpoznawanie nazwy komputera i usługi lokalnej z maszyn wirtualnych lub wystąpień roli w systemie Azure. |Zarządzany przez klienta z serwerów DNS (lokalnego kontrolera domeny, kontrolera domeny tylko do odczytu lokalnej lub zsynchronizowane, na przykład przy użyciu transferów stref DNS dodatkowej). Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Rozpoznawanie nazwy Azure hostów z komputerów lokalnych. |Do przodu zapytań do serwera proxy DNS zarządzany przez klienta w odpowiedniej sieci wirtualnej, serwer proxy przekazuje zapytania na platformie Azure do rozpoznania. Zobacz [rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Tylko nazwa FQDN |
| Reverse DNS wewnętrznych adresów IP. |[Rozpoznawanie nazw przy użyciu serwera DNS](#name-resolution-that-uses-your-own-dns-server). |Nie dotyczy |
| Rozpoznawanie nazw między maszynami wirtualnymi lub wystąpień roli znajduje się w różnych usług w chmurze, nie znajduje się w sieci wirtualnej. |Nie dotyczy. Łączność między maszyn wirtualnych i wystąpień roli w innej chmurze usługi nie jest obsługiwana poza siecią wirtualną. |Nie dotyczy|

## <a name="azure-provided-name-resolution"></a>Rozpoznawanie nazw platformy Azure

Wraz z rozpoznawanie publicznej nazwy DNS platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i wystąpień ról, które znajdują się w tej samej sieci wirtualnej lub usługi w chmurze. Maszyn wirtualnych i wystąpień w usłudze w chmurze udostępnić tym samym sufiksem DNS samej nazwy hosta jest wystarczająca. Ale w sieci wirtualne wdrażane za pomocą klasycznym modelu wdrażania usługi w chmurze różnych różnych sufiksów DNS. W takiej sytuacji należy FQDN do rozpoznawania nazw między usługami inną chmurę. W sieciach wirtualnych jest wdrażane za pomocą modelu wdrażania usługi Azure Resource Manager sufiks DNS jest spójność w ramach sieci wirtualnej, aby nazwa FQDN nie jest wymagana. Nazwy DNS można przypisać do maszyn wirtualnych i interfejsów sieciowych. Mimo że rozpoznawanie nazw platformy Azure nie wymaga żadnej konfiguracji, nie jest odpowiednim wyborem w przypadku wszystkich scenariuszy wdrożenia, zgodnie z opisem w poprzedniej tabeli.

> [!NOTE]
> Gdy przy użyciu chmury usług ról sieć web i proces roboczy, można także przejść wewnętrznych adresów IP przy użyciu interfejsu API REST zarządzania usługi Azure wystąpień roli. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST zarządzania usługi](https://msdn.microsoft.com/library/azure/ee460799.aspx). Adres jest oparta na rolach nazwę i numer wystąpienia. 
> 
> 

### <a name="features"></a>Funkcje

Rozpoznawanie nazw platformy Azure oferuje następujące funkcje:
* Łatwość użycia. Nie jest wymagana żadna konfiguracja.
* Wysoka dostępność. Nie potrzebujesz do tworzenia i zarządzania klastrami serwerów DNS.
* Służy usługi w połączeniu z własnych serwerów DNS, do rozpoznania zarówno lokalnie, jak i nazwy hosta Azure.
* Możesz użyć rozpoznawanie nazw między maszyn wirtualnych i wystąpień roli w ramach tej samej usługi w chmurze, bez konieczności nazwy FQDN.
* Można użyć rozpoznawania nazw między maszynami wirtualnymi w sieciach wirtualnych, które używają modelu wdrażania usługi Azure Resource Manager, bez potrzeby wprowadzania nazwy FQDN. Sieci wirtualne w klasycznym modelu wdrażania wymagają nazwy FQDN, Rozpoznając nazwy w innej chmurze usługi. 
* Można użyć nazwy hostów, które najlepiej opisują wdrożeń, a nie Praca z nazwami wygenerowany automatycznie.

### <a name="considerations"></a>Zagadnienia do rozważenia

Informacje, które należy wziąć pod uwagę w przypadku używania rozpoznawania nazw platformy Azure:
* Nie można zmodyfikować sufiks DNS utworzony Azure.
* Nie można ręcznie zarejestrować własnych.
* Usługa WINS i NetBIOS są nieobsługiwane. Użytkownik nie widzi maszyn wirtualnych w Eksploratorze Windows.
* Nazwy hostów musi być zgodny z DNS. Nazwach muszą mieć tylko 0-9, a-z i "-" i nie może zaczynać się ani kończyć "-".
* Ruch zapytanie DNS jest ograniczenie dla każdej maszyny Wirtualnej. Ograniczanie nie powinny mieć wpływ na większości aplikacji. Ograniczanie żądań zaobserwowano, upewnij się, że włączone jest buforowanie po stronie klienta. Aby uzyskać więcej informacji, zobacz [Konfiguracja klienta DNS](#dns-client-configuration).
* W pierwszej usługi w chmurze 180 tylko maszyny wirtualne są zarejestrowane dla każdej sieci wirtualnej w klasycznym modelu wdrażania. To ograniczenie nie ma zastosowania do sieci wirtualnych w usłudze Azure Resource Manager.

## <a name="dns-client-configuration"></a>Konfiguracja klienta DNS

W tej sekcji omówiono buforowanie po stronie klienta i ponownych prób po stronie klienta.

### <a name="client-side-caching"></a>Buforowanie po stronie klienta

Nie wszystkie zapytania DNS musi być przesyłane przez sieć. Buforowanie po stronie klienta pomaga zmniejszenia opóźnienia i poprawy odporności na blips sieci, Rozpoznając cyklicznego zapytania DNS z lokalnej pamięci podręcznej. Rekordy DNS zawierają mechanizm time-to-live (TTL), dzięki czemu pamięci podręcznej do przechowywania bez wpływania na rekordów świeżości rekordu tak długo, jak to możliwe. W związku z tym buforowanie po stronie klienta jest odpowiednia dla większości sytuacji.

Domyślne klienta DNS systemu Windows ma wbudowane pamięci podręcznej DNS. Niektóre dystrybucje systemu Linux nie dołączaj buforowanie domyślnie. Jeśli okaże się, że nie lokalnej pamięci podręcznej, należy dodać pamięci podręcznej DNS do każdej maszyny Wirtualnej systemu Linux.

Istnieje szereg różnych DNS buforowania pakietów (takich jak dnsmasq). Poniżej przedstawiono sposób instalowania dnsmasq na najczęściej używane dystrybucje:

* **Ubuntu (używa resolvconf)**:
  * Zainstaluj pakiet dnsmasq z `sudo apt-get install dnsmasq`.
* **SUSE (używa netconf)**:
  * Zainstaluj pakiet dnsmasq z `sudo zypper install dnsmasq`.
  * Włącz usługę dnsmasq z `systemctl enable dnsmasq.service`. 
  * Uruchom usługę dnsmasq z `systemctl start dnsmasq.service`. 
  * Edytuj **/etc/sysconfig/network/config**i zmień *NETCONFIG_DNS_FORWARDER = ""* do *dnsmasq*.
  * Zaktualizuj resolv.conf z `netconfig update`, aby ustawić jako program rozpoznawania nazw DNS lokalnej pamięci podręcznej.
* **OpenLogic (używa NetworkManager)**:
  * Zainstaluj pakiet dnsmasq z `sudo yum install dnsmasq`.
  * Włącz usługę dnsmasq z `systemctl enable dnsmasq.service`.
  * Uruchom usługę dnsmasq z `systemctl start dnsmasq.service`.
  * Dodaj *dołączy domeny nazwy serwerów 127.0.0.1;* do **/etc/dhclient-eth0.conf**.
  * Uruchom ponownie usługę sieci z `service network restart`, aby ustawić jako program rozpoznawania nazw DNS lokalnej pamięci podręcznej.

> [!NOTE]
> Pakiet dnsmasq jest tylko jeden z wielu pamięci podręcznej DNS dostępne dla systemu Linux. Przed użyciem go, sprawdź jego przydatności do określonych potrzeb i sprawdź, czy zainstalowana jest nie pamięci podręcznej.
> 
> 
    
### <a name="client-side-retries"></a>Ponownych prób po stronie klienta

Usługa DNS jest głównie protokołu UDP. Ponieważ protokół UDP nie gwarantuje dostarczanie komunikatów, obsługi logiki ponawiania próby w samego protokołu DNS. Każdy klient DNS (systemu operacyjnego) może pokazać Logika ponawiania różne, w zależności od preferencji twórcy:

* Systemów operacyjnych Windows ponów próbę po co drugi, a następnie ponownie po drugim dwóch sekund, cztery sekund i innym cztery sekundy. 
* Domyślne Linux Instalatora ponownych prób po 5 sekund. Firma Microsoft zaleca zmianę specyfikacji ponawiania pięć razy na sekundę.

Sprawdź bieżące ustawienia na Maszynę wirtualną systemu Linux z `cat /etc/resolv.conf`. Przyjrzyj się *opcje* linii, na przykład:

```bash
options timeout:1 attempts:5
```

Plik resolv.conf jest zwykle generowane automatycznie i nie można edytować. Wykonania określonych kroków w celu dodania *opcje* wiersza różnią się dystrybucji:

* **Ubuntu** (używa resolvconf):
  1. Dodaj *opcje* wiersz do **/etc/resolveconf/resolv.conf.d/head**.
  2. Uruchom `resolvconf -u` do aktualizacji.
* **SUSE** (używa netconf):
  1. Dodaj *timeout:1 prób: 5* do **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** parametru w **/etc/sysconfig/network/config**. 
  2. Uruchom `netconfig update` do aktualizacji.
* **OpenLogic** (używa NetworkManager):
  1. Dodaj *echa "Opcje timeout:1 prób: 5"* do **/etc/NetworkManager/dispatcher.d/11-dhclient**. 
  2. Aktualizacja z `service network restart`.

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Rozpoznawanie nazw, która używa serwera DNS

W tej sekcji omówiono maszyn wirtualnych, wystąpień roli i aplikacje sieci web.

### <a name="vms-and-role-instances"></a>Maszyn wirtualnych i wystąpień roli

Twoje potrzeby rozpoznawania nazw może wykracza poza funkcje oferowane przez platformę Azure. Na przykład może być konieczne do korzystania z domeny usługi Active Directory firmy Microsoft Windows Server, rozpoznawania nazw DNS między sieciami wirtualnymi. Tak, aby pokrywał tych scenariuszy, platforma Azure oferuje możliwości do użycia serwery DNS.

Serwery DNS w sieci wirtualnej można przekazywać zapytań DNS do rozpoznawania nazw cykliczne na platformie Azure. Dzięki temu można rozwiązać nazwy hostów w tej sieci wirtualnej. Na przykład kontroler domeny (DC) działają na platformie Azure można odpowiada na zapytania DNS dla swojej domeny i przekazywania inne zapytania do usługi Azure. Przekazywanie zapytań umożliwia maszyn wirtualnych zobaczyć, zarówno lokalnych zasobów (za pośrednictwem kontrolera domeny), jak i nazwy hosta dostarczone do usługi Azure (za pośrednictwem usługi przesyłania dalej). Dostęp do rozpoznawania nazw cykliczne na platformie Azure zostaną przekazane za pośrednictwem wirtualnego adresu IP 168.63.129.16.

Przesyłanie dalej DNS również umożliwia rozpoznawanie nazw DNS między sieciami wirtualnymi i umożliwia maszyny lokalne rozpoznawanie nazw hosta dostarczone do usługi Azure. Aby można było rozpoznać nazwy hosta maszyny Wirtualnej, serwer DNS musi znajdować się w tej samej sieci wirtualnej maszyny Wirtualnej i można skonfigurować na platformie Azure, zapytania dotyczące nazwy hosta do przodu. Sufiks DNS różni się w każdej sieci wirtualnej, można użyć zasady warunkowego przesyłania dalej do wysyłania zapytań DNS do odpowiedniej sieci wirtualnych do rozpoznania. Na poniższej ilustracji przedstawiono dwie sieci wirtualne i sieć lokalną podczas rozpoznawania nazw DNS między sieciami wirtualnymi, za pomocą tej metody. Usługi przesyłania dalej DNS na przykład jest dostępny w [galerię szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Wystąpienia roli może rozpoznawać nazw maszyn wirtualnych w tej samej sieci wirtualnej. Robi to przy użyciu nazwy FQDN, która składa się z nazwy hosta maszyny Wirtualnej i **internal.cloudapp.net** sufiks DNS. Jednak, w tym przypadku rozpoznawanie nazw tylko zakończy się pomyślnie, jeśli wystąpienia roli ma nazwę maszyny Wirtualnej, zdefiniowane w [roli schematu (plik .cscfg)](https://msdn.microsoft.com/library/azure/jj156212.aspx). 
>    <Role name="<role-name>" vmName="<vm-name>">
> 
> Wystąpienia roli, trzeba będzie wykonać rozpoznawanie nazw maszyn wirtualnych w innej sieci wirtualnej (nazwa FQDN przy użyciu **internal.cloudapp.net** sufiks) ma to zrobić za pomocą metody opisanej w tej sekcji (niestandardowych serwerów DNS przesyłania dalej między dwie sieci wirtualne).
>

![Diagram DNS między sieciami wirtualnymi](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Korzystając z rozpoznawania nazw platformy Azure, Azure konfiguracji protokołu DHCP (Dynamic Host) zawiera wewnętrzny sufiks DNS (**. internal.cloudapp.net**) na każdej maszynie Wirtualnej. Ten sufiks umożliwia rozpoznawania nazwy hosta, ponieważ rekordów nazwa hosta jest **internal.cloudapp.net** strefy. Korzystając z własnych rozwiązania rozpoznawania nazw, ten sufiks nie jest dostarczony na maszynach wirtualnych, ponieważ zakłócać innych architektur DNS (na przykład scenariusze przyłączonych do domeny). Zamiast tego, platforma Azure udostępnia symbol zastępczy niedziałającej (*reddog.microsoft.com*).

Jeśli to konieczne, można określić wewnętrzny sufiks DNS przy użyciu programu PowerShell lub interfejsu API:

* Dla sieci wirtualnych w modelach wdrażania usługi Azure Resource Manager, jest dostępny za pośrednictwem sufiks [interfejsu sieciowego interfejsu API REST](/rest/api/virtualnetwork/networkinterfaces/get), [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) polecenia cmdlet programu PowerShell, a [Pokaż kart sieciowych az](/cli/azure/network/nic#az-network-nic-show) polecenia wiersza polecenia platformy Azure.
* W klasycznych modeli wdrażania, jest dostępna za pośrednictwem sufiks [uzyskać interfejsu API wdrożenia](https://msdn.microsoft.com/library/azure/ee460804.aspx) wywołania lub [Get AzureVM-debugowania](/powershell/module/azure/get-azurevm) polecenia cmdlet.

Jeśli przekazywanie zapytań na platformie Azure nie własnych potrzeb, należy podać rozwiązania DNS. Rozwiązania DNS musi:

* Podaj odpowiedniego hosta rozpoznawania nazw za pomocą [DDNS](virtual-networks-name-resolution-ddns.md), np. Jeśli używasz DDNS, może być konieczne wyłączenie oczyszczanie rekordów DNS. Azure dzierżaw DHCP są długie i oczyszczania może usunąć rekordy DNS przedwcześnie. 
* Podaj odpowiednie rekursywnego rozpoznawania zezwalająca na rozpoznawanie nazw domen zewnętrznych.
* Jest dostępny (TCP i UDP na porcie 53) od klientów, które służy ona i mieć możliwość uzyskania dostępu do Internetu.
* Być zabezpieczony przed dostępu z Internetu, ograniczających zagrożenia powodowanego przez czynników zewnętrznych.

> [!NOTE]
> Aby uzyskać najlepszą wydajność gdy używasz maszynach wirtualnych platformy Azure jako serwery DNS IPv6 powinno zostać wyłączone. A [publicznego adresu IP](virtual-network-public-ip-address.md) powinny zostać przypisane do każdego serwera DNS maszyny Wirtualnej. Analiza wydajności dodatkowe i optymalizacje podczas korzystania z systemu Windows Server jako serwer DNS, można znaleźć [Nazwa rozwiązania wydajność cykliczne Windows DNS Server 2012 R2](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx).
> 
> 

### <a name="web-apps"></a>Aplikacje sieci Web
Załóżmy, że należy przeprowadzić rozpoznawanie nazw z aplikacji sieci web utworzony za pomocą usługi aplikacji, połączonych z wirtualną siecią na maszynach wirtualnych w tej samej sieci wirtualnej. Oprócz konfigurowania niestandardowe DNS serwera, który ma przesyłania dalej DNS, który przesyła dalej zapytania do platformy Azure (wirtualny adres IP, 168.63.129.16), wykonaj następujące czynności:
1. Włącz integrację sieci wirtualnej dla aplikacji sieci web, jeśli nie jeszcze zrobione, zgodnie z opisem w [integracji aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. W portalu Azure wybierz hosting aplikacji sieci web dla planu usługi aplikacji **sieci synchronizacji** w obszarze **sieci**, **integracji sieci wirtualnej**.

    ![Zrzut ekranu przedstawiający rozpoznawania nazw sieci wirtualnej](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Jeśli zachodzi potrzeba wykonania rozpoznawania nazw z aplikacji sieci web utworzony za pomocą usługi aplikacji, połączonych z wirtualną siecią na maszynach wirtualnych w innej sieci wirtualnej, należy użyć niestandardowych serwerów DNS w obu sieciach wirtualnych w następujący sposób: 
* Konfigurowanie serwera DNS w sieci wirtualnej docelowego na maszynie Wirtualnej, która również może przekazywać kwerendy do cyklicznego programu rozpoznawania nazw na platformie Azure (wirtualny adres IP 168.63.129.16). Usługi przesyłania dalej DNS na przykład jest dostępny w [galerię szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) i [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurowanie usługi przesyłania dalej DNS w sieci wirtualnej źródła na maszynie Wirtualnej. Konfigurowanie tej usługi przesyłania dalej DNS do przesyłania zapytań do serwera DNS w sieci wirtualnej docelowej.
* Skonfiguruj serwer DNS źródła w ustawieniach sieci wirtualnej źródła.
* Włącz integrację sieci wirtualnej dla aplikacji sieci web utworzyć link do źródła sieci wirtualnej, zgodnie z instrukcjami zawartymi w [integracji aplikacji z siecią wirtualną](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* W portalu Azure wybierz hosting aplikacji sieci web dla planu usługi aplikacji **sieci synchronizacji** w obszarze **sieci**, **integracji sieci wirtualnej**. 

## <a name="specify-dns-servers"></a>Określ serwer DNS
Korzystając z serwerów DNS, Azure pozwala, aby określić wiele serwerów DNS dla sieci wirtualnej. Można również określić wiele serwerów DNS dla interfejsu sieciowego (dla usługi Azure Resource Manager) lub dla usługi w chmurze (dla klasycznym modelu wdrażania). Serwery DNS określona dla usługi chmury lub interfejs sieci uzyskać pierwszeństwo za pośrednictwem serwerów DNS określonych dla sieci wirtualnej.

> [!NOTE]
> Nie należy edytować właściwości połączenia sieciowego, takich jak serwer DNS adresów IP, bezpośrednio z poziomu maszyn wirtualnych systemu Windows. Jest to spowodowane może pobrać wymazane podczas usługi poprawianego po wymianie pobiera karty sieci wirtualnej. 
> 
> 

Korzystając z modelu wdrażania usługi Azure Resource Manager, można określić serwery DNS dla sieci wirtualnej i interfejsu sieciowego. Aby uzyskać więcej informacji, zobacz [Zarządzanie sieci wirtualnej](manage-virtual-network.md) i [Zarządzanie interfejsu sieciowego](virtual-network-network-interface.md).

Korzystając z klasycznym modelu wdrażania, można określić serwery DNS dla sieci wirtualnej w portalu Azure lub [pliku konfiguracji sieci](https://msdn.microsoft.com/library/azure/jj157100). Usługi w chmurze, można określić dla serwerów DNS za pomocą [pliku konfiguracji usługi](https://msdn.microsoft.com/library/azure/ee758710) lub za pomocą programu PowerShell, [AzureVM nowy](/powershell/module/azure/new-azurevm).

> [!NOTE]
> W przypadku zmiany ustawień DNS dla sieci wirtualnej lub maszyny wirtualnej, która została już wdrożona, musisz ponownie uruchomić każdej dotyczy maszyny Wirtualnej, aby zmiany zaczęły obowiązywać.
> 
> 

## <a name="next-steps"></a>Kolejne kroki

Model wdrażania usługi Azure Resource Manager:

* [Zarządzanie sieci wirtualnej](manage-virtual-network.md)
* [Zarządzanie interfejsu sieciowego](virtual-network-network-interface.md)

Klasycznego modelu wdrażania:

* [Schemat konfiguracji usługi Azure](https://msdn.microsoft.com/library/azure/ee758710)
* [Schemat konfiguracji sieci wirtualnej](https://msdn.microsoft.com/library/azure/jj157100)
* [Skonfiguruj sieć wirtualną przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md)
