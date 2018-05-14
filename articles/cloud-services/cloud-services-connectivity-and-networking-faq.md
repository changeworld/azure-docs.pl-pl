---
title: Problemów dotyczących łączności i sieci dla Microsoft Azure Cloud Services często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań dotyczących łączności i sieci dla usługi w chmurze Microsoft Azure.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: ab0fa22e9ba776db3d4af301499545f6e0822478
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemów dotyczących łączności i sieci dla usług Azure Cloud Services: często zadawane pytania (FAQ)

Ten artykuł zawiera często zadawane pytania dotyczące problemów dotyczących łączności i sieci dla [usługi w chmurze Azure](https://azure.microsoft.com/services/cloud-services). Aby uzyskać informacje o rozmiarze, zobacz [maszyny Wirtualnej usługi w chmurze rozmiaru strony](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Nie można zarezerwować ilości adresu IP w usłudze chmury wielu wirtualnych adresów IP.
Najpierw upewnij się, że wystąpienie próby zarezerwowania adresu IP dla maszyny wirtualnej jest włączona. Po drugie upewnij się, użyj zastrzeżonych adresów IP w przypadku wdrożeń zarówno tymczasowych i produkcyjnych. *Nie* zmiany ustawień podczas uaktualniania jest wdrożenie.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Jak używać pulpitu zdalnego, gdy grupa NSG?
Dodaj reguły do grupy NSG, które zezwalają na ruch na portach **3389** i **20000**. Pulpit zdalny używa portu **3389**. Wystąpienia usługi chmury jest równoważone, więc nie można bezpośrednio kontrolować którego wystąpienia, aby nawiązać połączenie. *RemoteForwarder* i *RemoteAccess* agentów zarządzania ruchem protokołu RDP (Remote Desktop) i umożliwić klientom wysyłanie plików cookie protokołu RDP i określ poszczególne wystąpienia do nawiązania połączenia. *RemoteForwarder* i *RemoteAccess* agentów wymagają portu **20000** być otwarty, który może być zablokowane, jeśli masz grupy NSG.

## <a name="can-i-ping-a-cloud-service"></a>Czy można wywołać usługi w chmurze?

Nie, nie za pomocą Normalny "ping" / protokołu ICMP. Protokół ICMP nie jest dozwolone przy użyciu usługi równoważenia obciążenia Azure.

Aby przetestować połączenie, zaleca się wykonanie polecenia ping portu. Gdy Ping.exe korzysta z protokołu ICMP, można użyć innych narzędzi, takich jak narzędzia PSPing, Nmap i telnet, aby przetestować połączenie określonego portu TCP.

Aby uzyskać więcej informacji, zobacz [użyj polecenia ping portu zamiast protokołu ICMP do testowania łączności maszyny Wirtualnej Azure](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Jak zapobiec odbieranie tysięcy trafień z nieznanych adresów IP, które mogą wskazywać na złośliwe ataki do usługi w chmurze?
Azure implementuje zabezpieczeń sieci wielowarstwowych do ochrony jego usług platformy atakami rozproszone (DDoS) typu "odmowa usługi". System obrony przed atakami DDoS Azure jest częścią systemu Azure ciągłego monitorowania procesu, który stale zwiększona penetracji testy. Ten system obrony przed atakami DDoS zaprojektowano w celu wytrzymać nie tylko atakami z zewnątrz, ale także od innych dzierżawców Azure. Aby uzyskać więcej informacji, zobacz [zabezpieczenia sieci Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Można również utworzyć zadanie uruchamiania selektywnego blokowania niektórych z określonych adresów IP. Aby uzyskać więcej informacji, zobacz [zablokować określonego adresu IP](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Próba RDP do mojego wystąpienia usługi chmury, jest wyświetlany komunikat "konto użytkownika wygasło."
Po obejściu datę wygaśnięcia, który jest skonfigurowany w ustawieniach protokołu RDP można otrzymać komunikat o błędzie "upłynął to konto użytkownika". Data wygaśnięcia można zmienić z portalu, wykonaj następujące czynności:

1. Zaloguj się do [portalu Azure](https://portal.azure.com), przejdź do usługi w chmurze i wybierz **pulpitu zdalnego** kartę.

2. Wybierz **produkcji** lub **przemieszczania** miejsca wdrożenia.

3. Zmień **wygasa** daty, a następnie Zapisz konfigurację.

Teraz można dla protokołu RDP na komputerze.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Dlaczego jest modułu równoważenia obciążenia Azure nie równoważenia ruch równomiernie?
Aby uzyskać informacje dotyczące sposobu działania wewnętrznego modułu równoważenia obciążenia, zobacz [nowy tryb dystrybucji modułu równoważenia obciążenia w Azure](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Krotka 5 (źródłowy adres IP, port źródłowy, docelowy adres IP, port docelowy i typ protokołu) jest algorytm dystrybucji skrót do mapowania ruchu na dostępne serwery. Zapewnia on lepkości tylko w ramach sesji transportu. Pakiety w tej samej sesji TCP lub UDP są przekierowywane do tego samego wystąpienia adres IP (DIP) datacenter za punktem końcowym równoważeniem obciążenia. Gdy klient zamyka i ponownie otwiera połączenie lub uruchamia nową sesję z tym samym źródłowy adres IP, port źródłowy zmiany i powoduje, że ruch komunikować się z innym punktem końcowym DIP.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Jak przekierować ruch przychodzący domyślny adres URL usługi w chmurze Moje niestandardowy adres URL 

Moduł ponowne zapisywanie adresów URL usług IIS może służyć do przekierowywania ruchu sieciowego, które docierają do domyślnego adresu URL dla usługi w chmurze (na przykład \*. cloudapp.net) do niektórych niestandardowych nazwę lub adres URL. Ponieważ moduł ponowne zapisywanie adresów URL jest domyślnie włączone na role sieci web i jego reguły są konfigurowane w pliku web.config aplikacji, zawsze jest dostępne na maszynie Wirtualnej niezależnie od ponownego uruchomienia/reimages. Aby uzyskać więcej informacji, zobacz:

- [Tworzenie reguły ponownego zapisywania modułu ponowne zapisywanie adresów URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Usuń łącze domyślne](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Jak można I blok/wyłączanie ruch przychodzący do domyślnego adresu URL usługi w chmurze? 

Może zablokować ruch przychodzący do domyślnego adresu URL/nazwa usługi w chmurze (na przykład \*. cloudapp.net). Ustaw nagłówek hosta był niestandardową nazwę DNS (na przykład www.MyCloudService.com) w obszarze Konfiguracja powiązania witryny w pliku definicji (*.csdef) usługi chmury, jak wskazano: 
 

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
 
Ponieważ to powiązanie nagłówka hosta jest wymuszane za pośrednictwem pliku csdef, usługa jest dostępna tylko przy użyciu nazwy niestandardowego "www.MyCloudService.com." Wszystkie żądania przychodzące do "*. cloudapp.net" domeny zawsze się niepowodzeniem. Jeśli używasz niestandardowego sondy Programowego lub wewnętrzny moduł równoważenia obciążenia w usłudze blokuje domyślnie/nazwa adresu URL usługi może zakłócać działanie sondowania. 

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Jak I upewnij się, że nigdy nie zmiany adresu IP publicznych usługi w chmurze?

Aby upewnić się, że publicznych zmiany adresu IP (VIP) usługi chmury nigdy nie tak, aby być zwyczajowo białej przez kilka określonych klientów, zaleca się, że zastrzeżony adres IP skojarzone z nim. W przeciwnym razie wirtualny adres IP podał Azure jest alokację z subskrypcji, po usunięciu wdrożenia. Powodzenie operacji wymiany adresów VIP należy poszczególnych zastrzeżonych adresów IP dla zarówno produkcyjne i przejściowe gniazda. Operacja zamiany bez obawy, nie powiedzie się. Zastrzeżenia adresu IP i skojarzyć go z usługi w chmurze, zobacz następujące artykuły:
 
- [Rezerwacja adresu IP istniejącej usługi w chmurze](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Skojarzyć zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji usługi](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file) 

Jeśli masz więcej niż jedno wystąpienie dla poszczególnych ról kojarzenie RIP z usługi w chmurze nie powinny powodować żadnych przestojów. Można też dozwolonych zakres adresów IP centrum danych Azure. Możesz znaleźć wszystkie zakresy IP platformy Azure na [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653). 

Ten plik zawiera zakresów adresów IP (w tym zakresy obliczeniowych, SQL i magazyn) używanych w centrach danych platformy Azure. Zaktualizowany plik jest wysyłany co tydzień, odzwierciedlający aktualnie wdrożonych zakresy i nadchodzące zmiany zakresu adresów IP. Nowe zakresy, które pojawiają się w pliku nie są używane w centrach danych, przez co najmniej jeden tydzień. Pobierz nowy plik .xml co tydzień i wykonywanie niezbędne zmiany w witrynie poprawnie zidentyfikować usługi działające na platformie Azure. Azure ExpressRoute użytkowników zauważyć, że ten plik ma być używane do aktualizowania anonsu protokołu BGP platformy Azure miejsca w pierwszym tygodniu miesiąca. 

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Jak używać sieci wirtualnych Azure Resource Manager z usługami w chmurze? 

Usługi w chmurze nie można umieścić w sieciach wirtualnych Menedżera zasobów Azure. Menedżer zasobów sieci wirtualnych i wdrożenie klasyczne sieci wirtualne mogą być połączone za pomocą komunikacji równorzędnej. Aby uzyskać więcej informacji, zobacz [równorzędna sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md).
