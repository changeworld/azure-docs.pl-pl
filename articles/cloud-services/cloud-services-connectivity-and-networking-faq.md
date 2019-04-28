---
title: Problemy z łącznością i sieci w często zadawane pytania dotyczące systemu Microsoft Azure Cloud Services | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawane pytania dotyczące łączności i sieci na potrzeby usług Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a46879a6882e6d45e4a7ccce59e4a02feea9005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432963"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Łączność i sieć problemy z usług Azure Cloud Services: Często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące łączności i sieci problemy dotyczące [usług Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Aby uzyskać informacje o rozmiarze, zobacz [VM Services chmurze rozmiaru strony](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nie można zarezerwować ilości pakietu Integracyjnego w wielu VIP usługi w chmurze.
Najpierw upewnij się, że wystąpienie maszyny wirtualnej, które próbuje zarezerwować adresu IP dla jest włączona. Po drugie upewnij się, użyj zastrzeżonych adresów IP dla środowisk przejściowych i produkcyjnych. *Nie* zmienić ustawienia, gdy wdrożenie jest uaktualniany.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak używać usług pulpitu zdalnego, gdy sieciowa grupa zabezpieczeń?
Dodaj reguły do sieciowej grupy zabezpieczeń, które zezwalają na ruch na portach **3389** i **20000**. Pulpit zdalny używa portu **3389**. Wystąpienia usługi w chmurze jest równoważone, więc nie można bezpośrednio kontrolować, które wystąpienie, aby nawiązać połączenie. *RemoteForwarder* i *RemoteAccess* agentów zarządzania ruchem protokołu RDP (Remote Desktop) i umożliwia klientowi wysłać plik cookie protokołu RDP i określić poszczególnych wystąpień, aby nawiązać połączenie. *RemoteForwarder* i *RemoteAccess* agenci wymagają portu **20000** być otwarte, który może zostać zablokowany w przypadku sieciowej grupy zabezpieczeń.

## <a name="can-i-ping-a-cloud-service"></a>Czy mogę wysłać polecenie ping do usługi w chmurze?

Nie, nie za pomocą normalnego "ping" / protokołu ICMP. Protokół ICMP jest blokowany przez moduł równoważenia obciążenia platformy Azure.

Aby przetestować łączność, zaleca się wykonanie polecenia ping portu. Podczas gdy Ping.exe używa protokołu ICMP, można użyć innych narzędzi, takich jak narzędzie PSPing, Nmap i telnet, aby przetestować łączliwość do określonego portu TCP.

Aby uzyskać więcej informacji, zobacz [użyć portu polecenia ping zamiast protokołu ICMP, aby przetestować łączność maszyn wirtualnych platformy Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zapobiec odbierające tysięcy trafień z nieznanych adresów IP, które mogą wskazywać na złośliwe ataki do usługi w chmurze?
Azure implementuje sieci wielowarstwowych zabezpieczeń do ochrony swoich usług platformy, atakami rozproszonej (DDoS) typu "odmowa usługi". System defense Azure przed atakami DDoS jest częścią platformy Azure ciągłego monitorowania procesu, który jest stale udoskonalany za pomocą testów penetracyjnych. Ten system obrony przed atakami DDoS jest przeznaczony do wytrzymać nie tylko atakami z zewnątrz, ale również z innych dzierżaw usługi Azure. Aby uzyskać więcej informacji, zobacz [bezpieczeństwa sieci Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Można również tworzyć zadania uruchamiania selektywnego blokowania niektórych konkretnych adresów IP. Aby uzyskać więcej informacji, zobacz [Block określonego adresu IP](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Gdy próbuję protokołu RDP do wystąpienia usługi w chmurze, jest wyświetlany komunikat "wygasła konta użytkownika".
Możesz otrzymać komunikat o błędzie "to konto użytkownika utracił ważność" po obejściu datę ważności, która jest skonfigurowana w ustawieniach protokołu RDP. Data wygaśnięcia można zmienić z portalu, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), przejdź do usługi w chmurze i wybierz **pulpitu zdalnego** kartę.

2. Wybierz **produkcji** lub **przemieszczania** miejsce wdrożenia.

3. Zmiana **wygasa** daty, a następnie Zapisz konfigurację.

Teraz można nawiązać połączenie RDP na komputerze.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Dlaczego jest usługa Azure Load Balancer nie równoważy równo ruchu?
Aby uzyskać informacje na temat działania wewnętrznego modułu równoważenia obciążenia, zobacz [usługi Azure Load Balancer nowy tryb dystrybucji](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Algorytm dystrybucji jest 5-krotka (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i typ protokołu) wyznaczania wartości skrótu, aby mapować ruch do dostępnych serwerów. Zapewnia lepkości tylko w sesji transportu. Pakiety w tej samej sesji TCP lub UDP są kierowane do tego samego wystąpienia adres IP (DIP) centrum danych za punkt końcowy z równoważeniem obciążenia. Gdy klienta zostanie zamknięty i ponownie otworzy połączenie lub uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmiany i powoduje, że ruch przejść do innego punktu końcowego adresu DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak można przekierować ruch przychodzący do domyślny adres URL mojego usługi w chmurze dla niestandardowego adresu URL?

Moduł ponowne zapisywanie adresów URL usług IIS może służyć do przekierowywania ruchu sieciowego, które docierają do domyślnego adresu URL dla usługi w chmurze (na przykład \*. cloudapp.net) niektóre niestandardowe nazwa/adres URL. Ponieważ moduł ponowne zapisywanie adresów URL jest domyślnie włączona dla ról internetowych i jego reguły są konfigurowane w pliku web.config aplikacji, zawsze jest dostępne na maszynie Wirtualnej niezależnie od tego, ponownego uruchamiania/odtwarza z obrazu. Aby uzyskać więcej informacji zobacz:

- [Tworzenie reguły ponownego zapisywania na moduł ponowne zapisywanie adresów URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Usuń łącze domyślne](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak można I blok/wyłączyć ruch przychodzący do domyślnego adresu URL usługi w chmurze?

Można zapobiec ruch przychodzący do domyślnego adresu URL/nazwa usługi w chmurze (na przykład \*. cloudapp.net). Ustawionych w obszarze Konfiguracja powiązania witryny nagłówka hosta do niestandardowej nazwy DNS (na przykład www.MyCloudService.com) w pliku definicji (*.csdef) usługa cloud, jak wskazano:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Ponieważ to powiązanie nagłówka hosta jest wymuszone za pomocą pliku csdef, usługa jest dostępna tylko za pośrednictwem niestandardową nazwę "www.MyCloudService.com." Wszystkie żądania przychodzące do "*. cloudapp.net" domeny zawsze kończy się niepowodzeniem. Jeśli używasz niestandardowej sondy SLB lub wewnętrznego modułu równoważenia obciążenia w usłudze, domyślnie blokuje/nazwa adresu URL usługi mogą zakłócać badania zachowanie.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak mogę upewnij się, że nigdy się nie zmienia publicznego adresu IP usługi w chmurze?

Aby upewnić się, że publicznego adresu IP usługi w chmurze (VIP) nigdy się nie zmienia tak, aby się zwyczajowo na liście dozwolonych przez kilku określonych klientów, zaleca się, że masz zastrzeżonego adresu IP skojarzone z nią. W przeciwnym razie wirtualnego adresu IP, udostępnianych przez platformę Azure jest cofniętą alokacją ze swojej subskrypcji, w przypadku usunięcia wdrożenia. Powodzenie operacji wymiany adresu VIP należy poszczególnych zastrzeżone adresy IP dla produkcyjne oraz przejściowe miejsce. Operacja zamiany bez nich kończy się niepowodzeniem. Aby zarezerwować adres IP i skojarz ją z usługą w chmurze, zobacz następujące artykuły:

- [Zastrzec adres IP istniejącej usługi w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Skojarzenie zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji usługi](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Jeśli masz więcej niż jedno wystąpienie roli, kojarzenie RIP z usługi w chmurze nie powinny powodować żadnych przestojów. Alternatywnie możesz umieścić na liście dozwolonych zakres adresów IP centrum danych platformy Azure. Możesz znaleźć wszystkie zakresy adresów IP platformy Azure w [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Ten plik zawiera zakresy adresów IP (w tym zakresy obliczeń, SQL i magazynu) używane w centrach danych platformy Azure. Zaktualizowany plik jest publikowany co tydzień, odzwierciedlający aktualnie wdrożone zakresy i wszystkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień. Pobierz nowy plik XML, co tydzień i wykonywać niezbędne zmiany w witrynie, aby prawidłowo identyfikować usługi uruchomione na platformie Azure. Użytkownicy usługi Azure ExpressRoute zauważyć, że ten plik ma być używany do anonsowania BGP platformy Azure miejsca w pierwszym tygodniu każdego miesiąca.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak używać sieci wirtualnych usługi Azure Resource Manager z usługami w chmurze?

Usługi w chmurze nie można umieścić w sieciach wirtualnych usługi Azure Resource Manager. Sieci wirtualne usługi Resource Manager i wdrożenie klasyczne sieci wirtualne mogą być połączone za pośrednictwem komunikacji równorzędnej. Aby uzyskać więcej informacji, zobacz [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak można uzyskać listę publicznych adresów IP używane przez usługi w chmurze?

Można użyć następujących skryptu środowiska PS, aby uzyskać listę publicznych adresów IP dla usług w chmurze w ramach Twojej subskrypcji

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
