---
title: Problemy dotyczące łączności i sieci
titleSuffix: Azure Cloud Services
description: W tym artykule wymieniono często zadawane pytania dotyczące łączności i sieci usług w chmurze platformy Microsoft Azure.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 7caeba0e88f63106eae80f7142b5d65463f8d7a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019404"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemy z łącznością i siecią usług w chmurze Azure: często zadawane pytania (często zadawane pytania)

Ten artykuł zawiera często zadawane pytania dotyczące problemów z łącznością i siecią dla [usług w chmurze Azure.](https://azure.microsoft.com/services/cloud-services) Aby uzyskać informacje o rozmiarze, zobacz [stronę rozmiar maszyny wirtualnej usług w chmurze](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nie mogę zarezerwować adresu IP w usłudze chmurowej multi-VIP.
Najpierw upewnij się, że wystąpienie maszyny wirtualnej, dla którego próbujesz zarezerwować adres IP, jest włączone. Po drugie upewnij się, że używasz zastrzeżonych serwerów IP dla wdrożeń przejściowych i produkcyjnych. *Nie* należy zmieniać ustawień podczas uaktualniania wdrożenia.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak korzystać z pulpitu zdalnego, gdy mam nsg?
Dodaj reguły do sieciowej sieciowej sieciowej, które zezwalają na ruch na portach **3389** i **20000**. Pulpit zdalny używa portu **3389**. Wystąpienia usługi w chmurze są równoważenia obciążenia, więc nie można bezpośrednio kontrolować, które wystąpienie, aby połączyć się z. Agenci *RemoteForwarder* i *RemoteAccess* zarządzają ruchem protokołu RDP (Remote Desktop Protocol) i umożliwiają klientowi wysyłanie pliku cookie RDP i określanie pojedynczego wystąpienia, z które ma się połączyć. *RemoteForwarder* i *RemoteAccess* agentów wymagają portu **20000** być otwarte, które mogą być zablokowane, jeśli masz nsg.

## <a name="can-i-ping-a-cloud-service"></a>Czy mogę pingować usługę w chmurze?

Nie, nie za pomocą normalnego protokołu "ping"/ICMP. Protokół ICMP nie jest dozwolony za pośrednictwem modułu równoważenia obciążenia platformy Azure.

Aby przetestować łączność, zaleca się wykonanie polecenia ping portu. Podczas gdy program Ping.exe używa protokołu ICMP, można użyć innych narzędzi, takich jak PSPing, Nmap i telnet, do testowania łączności z określonym portem TCP.

Aby uzyskać więcej informacji, zobacz [Używanie pingów portów zamiast ICMP do testowania łączności maszyny Wirtualnej platformy Azure.](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zapobiec odbieraniu tysięcy trafień z nieznanych adresów IP, które mogą wskazywać na złośliwy atak na usługę w chmurze?
Platforma Azure implementuje wielowarstwowe zabezpieczenia sieci, aby chronić swoje usługi platformy przed rozproszonymi atakami typu "odmowa usługi" (DDoS). System obrony usługi Azure DDoS jest częścią ciągłego monitorowania platformy Azure, który jest stale ulepszany poprzez testowanie penetracji. Ten system obrony DDoS jest przeznaczony do odporniać nie tylko ataki z zewnątrz, ale także z innych dzierżaw platformy Azure. Aby uzyskać więcej informacji, zobacz [zabezpieczenia sieci platformy Azure](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Można również utworzyć zadanie uruchamiania, aby selektywnie blokować niektóre określone adresy IP. Aby uzyskać więcej informacji, zobacz [Blokowanie określonego adresu IP](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Gdy próbuję rdp do mojego wystąpienia usługi w chmurze, otrzymuję komunikat "Konto użytkownika wygasło."
Po ominięciu daty wygaśnięcia skonfigurowane w ustawieniach protokołu RDP może zostać wyświetlony komunikat o błędzie "To konto użytkownika wygasło". Datę wygaśnięcia można zmienić w portalu, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), przejdź do usługi w chmurze i wybierz kartę **Pulpit zdalny.**

2. Wybierz miejsce wdrożenia **produkcyjnego** lub **przemieszczania.**

3. Zmień datę **Wygasa w dniu,** a następnie zapisz konfigurację.

Teraz powinieneś być w stanie RDP do komputera.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Dlaczego usługa Azure Load Balancer nie równoważy równo ruchu?
Aby uzyskać informacje o działaniu wewnętrznego modułu równoważenia obciążenia, zobacz [Nowy tryb dystrybucji modułu równoważenia obciążenia platformy Azure](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Algorytm dystrybucji używany jest 5-krotka (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i typ protokołu) do mapowania ruchu do dostępnych serwerów. Zapewnia lepkość tylko w ramach sesji transportowej. Pakiety w tej samej sesji TCP lub UDP są kierowane do tego samego wystąpienia IP (DIP) centrum danych za punktem końcowym z równoważeniem obciążenia. Gdy klient zamyka i ponownie otwiera połączenie lub uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmienia się i powoduje, że ruch przechodzi do innego punktu końcowego DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak przekierować ruch przychodzący do domyślnego adresu URL mojej usługi w chmurze do niestandardowego adresu URL?

Moduł przepisywania adresów URL usług IIS może służyć do przekierowywania ruchu \*przychodzącego do domyślnego adresu URL usługi w chmurze (na przykład .cloudapp.net) do niestandardowej nazwy/adresu URL. Ponieważ moduł przepisywania adresu URL jest domyślnie włączony w rolach sieci Web, a jego reguły są skonfigurowane w witrynie web.config aplikacji, jest on zawsze dostępny na maszynie Wirtualnej, niezależnie od ponownego uruchomienia/ponownego zaamatków. Aby uzyskać więcej informacji, zobacz:

- [Tworzenie reguł przepisywania dla modułu przepisywania adresów URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Usuwanie łącza domyślnego](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak zablokować/wyłączyć ruch przychodzący do domyślnego adresu URL mojej usługi w chmurze?

Można zapobiec przychodzącemu ruchowi do domyślnego adresu URL/nazwy usługi w chmurze (na przykład \*.cloudapp.net). Ustaw nagłówek hosta na niestandardową nazwę\.DNS (na przykład www MyCloudService.com) w ramach konfiguracji powiązania lokacji w pliku definicji usługi w chmurze (*.csdef), jak wskazano:

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

Ponieważ to powiązanie nagłówka hosta jest wymuszane za pośrednictwem pliku csdef, usługa jest dostępna tylko za pomocą niestandardowej nazwy "www.MyCloudService.com". Wszystkie przychodzące żądania do domeny "*.cloudapp.net" zawsze nie powiedzie się. Jeśli używasz niestandardowej sondy równoważenia obciążenia lub wewnętrznego modułu równoważenia obciążenia w usłudze, blokowanie domyślnego adresu URL/nazwy usługi może zakłócać zachowanie sondowania.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak upewnić się, że publiczny adres IP usługi w chmurze nigdy się nie zmienia?

Aby upewnić się, że publiczny adres IP usługi w chmurze (znany również jako adres VIP) nigdy się nie zmienia, dzięki czemu może być zwyczajowo umieszczony na białej liście przez kilku określonych klientów, zaleca się, aby mieć z nim skojarzony zastrzeżony adres IP. W przeciwnym razie wirtualny adres IP dostarczony przez platformę Azure jest cofnięty z subskrypcji po usunięciu wdrożenia. Aby wykonać pomyślnie operację wymiany vip, potrzebujesz indywidualnych zarezerwowanych adresów IP zarówno dla miejsc produkcyjnych, jak i przejściowych. Bez nich operacja wymiany kończy się niepowodzeniem. Aby zarezerwować adres IP i skojarzyć go z usługą w chmurze, zobacz następujące artykuły:

- [Rezerwuj adres IP istniejącej usługi w chmurze](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Kojarzenie zastrzeżonego adresu IP z usługą w chmurze przy użyciu pliku konfiguracji usługi](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Jeśli masz więcej niż jedno wystąpienie dla ról, kojarzenie protokołu RIP z usługą w chmurze nie powinno powodować żadnych przestojów. Alternatywnie można dodać zakres adresów IP centrum danych platformy Azure do listy dozwolonych. Wszystkie zakresy adresów IP platformy Azure można znaleźć w [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Ten plik zawiera zakresy adresów IP (w tym zakresy obliczeniowe, SQL i zakresy magazynu) używane w centrach danych platformy Azure. Zaktualizowany plik jest publikowany co tydzień, co odzwierciedla aktualnie wdrożone zakresy i wszelkie nadchodzące zmiany w zakresach adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych przez co najmniej jeden tydzień. Pobierz nowy plik xml co tydzień i wykonaj niezbędne zmiany w witrynie, aby poprawnie zidentyfikować usługi uruchomione na platformie Azure. Użytkownicy usługi Azure ExpressRoute mogą pamiętać, że ten plik używany do aktualizowania reklamy BGP miejsca na platformie Azure w pierwszym tygodniu każdego miesiąca.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak korzystać z sieci wirtualnych usługi Azure Resource Manager z usługami w chmurze?

Nie można umieszczać usług w chmurze w sieciach wirtualnych usługi Azure Resource Manager. Sieci wirtualne Menedżera zasobów i klasyczne sieci wirtualne wdrażania mogą być połączone za pośrednictwem komunikacji równorzędnej. Aby uzyskać więcej informacji, zobacz [Komunikacja równorzędna sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Jak uzyskać listę publicznych usług IP używanych przez moje usługi w chmurze?

Za pomocą następującego skryptu PS można uzyskać listę publicznych usług IP dla usług w chmurze w ramach subskrypcji

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
