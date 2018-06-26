---
title: Sprawdź poprawność przepływność sieci VPN do sieci wirtualnej Microsoft Azure | Dokumentacja firmy Microsoft
description: Ten dokument ma na celu pomoc użytkownik zweryfikować przepustowość sieci na maszynie wirtualnej platformy Azure z zasobami lokalnymi.
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: jasmc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: 38ff1ee4c525d41e2a7446d5adc792c746504491
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754508"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Jak można sprawdzić poprawności przepływność sieci VPN do sieci wirtualnej

Umożliwia ustanowienia bezpiecznego połączenia bramy sieci VPN, między lokalizacjami łączność między sieci wirtualnej w systemie Azure i lokalnej infrastruktury IT.

W tym artykule przedstawiono sposób sprawdzania poprawności przepływność sieci z zasobów lokalnych na maszynie wirtualnej platformy Azure (VM). Umożliwia także wskazówki dotyczące rozwiązywania problemów.

>[!NOTE]
>Ten artykuł ma ułatwić diagnozowanie i rozwiązywanie typowych problemów. Jeśli nie uda się rozwiązać ten problem, korzystając z poniższych informacji [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Przegląd

Połączenie bramy sieci VPN obejmuje następujące składniki:

- Lokalnego urządzenia sieci VPN (wyświetlanie listy [zweryfikowane urządzenia sieci VPN)](vpn-gateway-about-vpn-devices.md#devicetable).
- Publicznego Internetu
- Brama sieci VPN
- Maszyna wirtualna platformy Azure

Na poniższym diagramie przedstawiono logicznej łączności z siecią lokalną sieć wirtualną Azure za pośrednictwem sieci VPN.

![Połączenie z klienta sieci logicznej MSFT siecią przy użyciu sieci VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Oblicz maksymalna oczekiwana wejście/wyjście

1.  Ustal wymagania dotyczące przepływności linii bazowej aplikacji.
2.  Określ swoje limity przepływność bramy sieci VPN platformy Azure. Aby uzyskać pomoc, zobacz sekcję "Łącznej przepływności według jednostki SKU i sieci VPN typu" [planowania i projektowania dla bramy sieci VPN](vpn-gateway-plan-design.md).
3.  Określić [maszyny Wirtualnej Azure przepływności wskazówki](../virtual-machines/virtual-machines-windows-sizes.md) dla rozmiar maszyny Wirtualnej.
4.  Określ przepustowość usługodawcy internetowego (ISP).
5.  Obliczanie przepływności oczekiwanego - minimalnej przepustowości (maszyna wirtualna, brama usługodawcy internetowego) * 0,8.

Jeśli Twoje obliczeniowej przepływności nie spełnia wymagania dotyczące przepływności linii bazowej aplikacji, należy zwiększyć przepustowość zasobu, który został zidentyfikowany jako wąskie gardło. Aby zmienić rozmiar bramy sieci VPN platformy Azure, zobacz [zmiana jednostka SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby zmienić rozmiar maszyny wirtualnej, zobacz [Zmień rozmiar maszyny Wirtualnej](../virtual-machines/virtual-machines-windows-resize-vm.md). Jeśli nie występują oczekiwanego przepustowości połączenia z Internetem, można również skontaktować się z usługodawcą Internetowym.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Sprawdź poprawność przepływność sieci przy użyciu narzędzia wydajności

Ta powinna być sprawdzana podczas godziny poza szczytem, jak nasycenie przepływności tunelu VPN podczas testowania nie daje prawidłowych wyników.

Narzędzie używanego dla tego testu jest dotyczące programu Iperf; która działa w systemach Windows i Linux i trybach klienta i serwera. Jest ograniczony do 3 GB/s dla maszyn wirtualnych systemu Windows.

To narzędzie nie wykonuje żadnych operacji odczytu/zapisu na dysku. Tworzy wyłącznie automatycznie wygenerowany ruch TCP z jednej strony do drugiej. On generowany statystyka oparta na eksperymenty, która przepustowość między węzłami klienta i serwera. Podczas testowania między dwoma węzłami, co działa jako serwer, a drugi jako klienta. Po zakończeniu tego testu, firma Microsoft zaleca, aby odwrócić ról do testowania zarówno przekazywania i pobierania przepływności na obu węzłów.

### <a name="download-iperf"></a>Pobierz dotyczące programu Iperf
Pobierz [dotyczące programu Iperf;](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Aby uzyskać więcej informacji, zobacz [dokumentacji dotyczące programu Iperf;](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Produkty innych firm omówione w tym artykule są wytwarzane przez producentów niezależnych od firmy Microsoft. Firma Microsoft nie udziela żadnych gwarancji, dorozumianych ani żadnego innego do wydajności ani niezawodności tych produktów.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Uruchom dotyczące programu Iperf; (iperf3.exe)
1. Włącz reguły NSG/list kontroli dostępu zezwala na ruch (do testowania na maszynie Wirtualnej Azure publiczny adres IP).

2. W obu węzłach Włącz wyjątek zapory dla portu 5001.

    **System Windows:** uruchom następujące polecenie jako administrator:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Aby usunąć regułę podczas testowania zakończeniu, uruchom to polecenie:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
    </br>
    **Azure Linux:** obrazów systemu Linux platformy Azure są ograniczająca zapory. Czy aplikacja nasłuchuje na porcie, ruch jest dozwolony przez. Otwarte jawnie portów może być konieczne niestandardowych obrazów, które są zabezpieczone. Typowe zapory systemu operacyjnego Linux warstwy obejmują `iptables`, `ufw`, lub `firewalld`.

3. W węźle serwera przejdź do katalogu, w której jest wyodrębniany iperf3.exe. Następnie uruchom dotyczące programu Iperf; w trybie serwera i ustaw go do nasłuchiwania na porcie 5001 jako następujące polecenia:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. W węźle klienta przejdź do katalogu zawierającego narzędzie dotyczące programu Iperf; jest wyodrębnione, a następnie uruchom następujące polecenie:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klient to powoduje ruch na porcie 5001 do serwera przez 30 sekund. Flagi "-P" wskazujące, że użyto 32 jednoczesnych połączeń z węzłem serwera.

    Poniższym ekranie przedstawiono dane wyjściowe w tym przykładzie:

    ![Dane wyjściowe](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCJONALNIE) Aby zachować wyniki testowania, uruchom następujące polecenie:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Po wykonaniu poprzednich kroków, należy wykonać te same kroki z rolami wycofać, tak, aby węzeł serwera teraz klienta i na odwrót.

## <a name="address-slow-file-copy-issues"></a>Rozwiązać problemy kopii pliku powolne
Mogą wystąpić powolne pliku kopiowanie przy użyciu Eksploratora Windows lub przeciągania i upuszczania za pomocą sesji protokołu RDP. Tego problemu jest zwykle z powodu co najmniej jednej z następujących czynników:

- Podczas kopiowania plików, aplikacji kopiowania plików, takich jak Eksplorator Windows i protokołu RDP, nie należy używać wiele wątków. W celu poprawy wydajności użyj aplikacji kopii pliku wielowątkowych takiego jak [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) do kopiowania plików za pomocą wątków 16 lub 32. Aby zmienić liczbę wątków na jedną kopię pliku Richcopy, kliknij przycisk **akcji** > **opcje kopiowania** > **skopiowanie pliku**.<br><br>
![Zagadnienia dotyczące kopiowania plików powolne](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Za mało szybkość odczytu/zapisu dysku maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z magazynu Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfejs połączonej zewnętrznego urządzenia lokalnego
Jeśli lokalnego urządzenia sieci VPN adres internetowy jest uwzględniona w [sieci lokalnej](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definicji na platformie Azure, może wystąpić niemożność Przenieś w górę zakończy połączenie sieci VPN, sporadyczne lub problemów z wydajnością.

## <a name="checking-latency"></a>Sprawdzanie opóźnienia
Tracert do śledzenia do urządzenia Microsoft Azure umożliwia określenie, czy istnieją wszelkich opóźnień powyżej 100 ms między przeskoków.

Z sieci lokalnej, uruchom *tracert* do adresu IP bramy Azure lub maszyny Wirtualnej. Gdy zostanie wyświetlony tylko * zwrócone, wiadomo, został osiągnięty Azure krawędzi. Po wyświetleniu nazwy DNS, które obejmują "MSN" zwrócił wiadomo, że został osiągnięty szkielet firmy Microsoft.<br><br>
![Sprawdzanie opóźnienia](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji lub uzyskać pomoc zapoznaj się następujących łączy:

- [Zoptymalizować przepływność sieci maszyn wirtualnych platformy Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
