---
title: Sprawdzanie przepustowości sieci VPN z siecią wirtualną Azure firmy Microsoft | Dokumentacja firmy Microsoft
description: Ten dokument ma na celu pomóc użytkownikowi, sprawdzanie przepustowości sieci z ich zasobów lokalnych na maszynie wirtualnej platformy Azure.
services: vpn-gateway
author: cherylmc
manager: jasmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv;chadmat;genli
ms.openlocfilehash: c1117afcf6254c32ebe0a4e72ad5619606098253
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388612"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>Jak zweryfikować przepływność sieci VPN do sieci wirtualnej

Umożliwia ustanawianie bezpiecznej połączenia bramy sieci VPN, obejmujące między siecią wirtualną w ramach platformy Azure i lokalnej infrastruktury IT.

W tym artykule przedstawiono sposób sprawdzania poprawności przepływność sieci z zasobami lokalnymi, maszyną wirtualną (VM) platformy Azure. Zawiera także wskazówki dotyczące rozwiązywania problemów. 

>[!NOTE]
>Ten artykuł ma ułatwić diagnozowanie i rozwiązywanie typowych problemów dotyczących. Jeśli nie uda się rozwiązać problem, korzystając z poniższych informacji [się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
>
>

## <a name="overview"></a>Przegląd

Połączenie bramy sieci VPN obejmuje następujące składniki:

- Lokalne urządzenie sieci VPN (wyświetlanie listy [zweryfikowanych urządzeń sieci VPN)](vpn-gateway-about-vpn-devices.md#devicetable).
- Publicznej sieci Internet
- Usługa Azure VPN gateway
- Maszyna wirtualna platformy Azure

Na poniższym diagramie przedstawiono logiczne połączenie między siecią lokalną z siecią wirtualną platformy Azure za pośrednictwem sieci VPN.

![Łączność z klienta sieci logicznej sieci MSFT przy użyciu sieci VPN](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>Oblicz maksymalny oczekiwany ruchem przychodzącym/wychodzącym

1.  Ustal wymagania dotyczące przepływności linii bazowej Twojej aplikacji.
2.  Określ limitów przepływności bramy sieci VPN platformy Azure. Aby uzyskać pomoc, zobacz sekcję "Jednostki SKU bramy" [VPN Gateway — informacje](vpn-gateway-about-vpngateways.md#gwsku).
3.  Określić [wskazówki dotyczące przepustowości w maszynie Wirtualnej platformy Azure](../virtual-machines/virtual-machines-windows-sizes.md) dla rozmiaru maszyn wirtualnych.
4.  Określ przepustowość usługodawcy internetowego (ISP).
5.  Obliczanie przepływności oczekiwanego — co najmniej przepustowości (maszyna wirtualna, brama usługodawcy internetowego) * 0,8.

Jeśli obliczona przepływność nie spełnia wymagań w zakresie przepływności aplikacji linii bazowej, należy zwiększyć przepustowość zasobu, który został zidentyfikowany jako wąskie gardło. Aby zmienić rozmiar bramy Azure VPN Gateway, zobacz [Zmiana jednostki SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby zmienić rozmiar maszyny wirtualnej, zobacz [zmienić rozmiar maszyny Wirtualnej](../virtual-machines/virtual-machines-windows-resize-vm.md). Jeśli nie występują oczekiwanego przepustowością Internetu, można również skontaktować się z usługodawcą Internetowym.

## <a name="validate-network-throughput-by-using-performance-tools"></a>Sprawdzanie przepustowości sieci za pomocą narzędzia do oceny wydajności

Ta powinna być sprawdzana podczas godziny poza szczytem, jak nasycenie przepustowość tunelu sieci VPN, podczas testowania nie daje dokładne wyniki.

To narzędzie, której używamy dla tego testu jest dotyczące programu Iperf; który działa w systemach Windows i Linux oraz ma tryby klienta i serwera. Jest ograniczony do 3 GB/s dla Windows maszyn wirtualnych.

To narzędzie nie wykonuje żadnych operacji odczytu/zapisu na dysku. Tworzy wyłącznie automatycznie wygenerowany ruchu TCP od końca jednego do drugiego. Statystyka oparta na eksperymentowanie, służący do pomiaru przepustowości między klientem i serwerem węzłami on generowany. Podczas testowania między dwoma węzłami, jeden działa jako serwer, a drugi jako klienta. Po zakończeniu tego testu, firma Microsoft zaleca, aby odwrócić ról, aby przetestować zarówno przekazywanie i pobieranie przepływności na obu węzłach.

### <a name="download-iperf"></a>Pobierz dotyczące programu Iperf
Pobierz [dotyczące programu Iperf;](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). Aby uzyskać więcej informacji, zobacz [dokumentacji dotyczące programu Iperf;](https://iperf.fr/iperf-doc.php).

 >[!NOTE]
 >Produkty innych firm, w tym artykule są wytwarzane przez producentów niezależnych od firmy Microsoft. Firma Microsoft nie udziela żadnych gwarancji, dorozumianych ani żadnego innego, o wydajności ani niezawodności tych produktów.
 >
 >

### <a name="run-iperf-iperf3exe"></a>Uruchom dotyczące programu Iperf; (iperf3.exe)
1. Włącz regułę sieciowej grupy zabezpieczeń/listami ACL, zezwala na ruch (do testowania na platformie Azure maszynie Wirtualnej publiczny adres IP).

2. W obu węzłach włączenie wyjątku zapory dla portu 5001.

    **Windows:** Jako administrator, uruchom następujące polecenie:

    ```CMD
    netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
    ```

    Aby usunąć regułę, podczas testowania zakończeniu, uruchom następujące polecenie:

    ```CMD
    netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
    ```
     
    **Linux platformy Azure:**  Obrazy systemu Linux platformy Azure umieszczone są zapory ograniczające. W przypadku aplikacja nasłuchuje na porcie ruch jest dozwolony przez. Obrazy niestandardowe, które są zabezpieczone może być konieczne jawnie otwierane porty. Obejmują typowe zapory warstwy system operacyjny Linux `iptables`, `ufw`, lub `firewalld`.

3. W węźle serwera przejdź do katalogu, w której jest wyodrębniany iperf3.exe. Następnie należy uruchomić dotyczące programu Iperf; w trybie serwera i ustaw ją do nasłuchiwania na porcie 5001 jako następujących poleceń:

     ```CMD
     cd c:\iperf-3.1.2-win65

     iperf3.exe -s -p 5001
     ```

4. W węźle klienta przejdź do katalogu, w których narzędzie dotyczące programu Iperf; jest wyodrębniany i następnie uruchom następujące polecenie:

    ```CMD
    iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
    ```

    Klient jest wykonuje ruch na porcie 5001 do serwera przez 30 sekund. Flagi "-P" oznacza, że używasz 32 jednoczesnych połączeń z węzła serwera.

    Na poniższym ekranie przedstawiono dane wyjściowe z tego przykładu:

    ![Dane wyjściowe](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

5. (OPCJONALNIE) Aby zachować wyniki testów, uruchom następujące polecenie:

    ```CMD
    iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
    ```

6. Po wykonaniu poprzednich kroków, wykonaj te same kroki z rolami cofnięte, tak, aby teraz będzie węzeł serwera, klienta i na odwrót.

## <a name="address-slow-file-copy-issues"></a>Problemów z kopiowaniem plików wolnych adresów
Może wystąpić powolne pliku kopiowanie, gdy za pomocą Eksploratora Windows lub przeciąganie i upuszczanie za pomocą sesji protokołu RDP. Ten problem jest zwykle ze względu na jeden lub oba z następujących czynników:

- Podczas kopiowania plików, aplikacji kopiowania plików, takich jak Eksplorator Windows i protokołu RDP, nie należy używać wielu wątków. Lepszą wydajność, użyj aplikacji wielowątkowych plików kopii takich jak [Richcopy](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) do kopiowania plików za pomocą 16 lub 32 wątków. Aby zmienić liczbę wątków związanym z kopiowaniem plików w Richcopy, kliknij przycisk **akcji** > **opcje kopiowania** > **skopiowanie pliku**.<br><br>
![Zagadnienia dotyczące kopiowania plików powolne](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>
- Za mało szybkość odczytu/zapisu dysku maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z magazynu Azure](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Interfejs umożliwiający dostęp do Internetu zewnętrznego urządzenia w środowisku lokalnym
Jeśli lokalne urządzenie sieci VPN adresu IP dostępnego z Internetu jest uwzględniona w [sieci lokalnej](vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) definicja przestrzeni adresów na platformie Azure, możesz napotkać niemożność Przenieś w górę zamknie połączenie sieci VPN, sporadyczne lub problemów z wydajnością.

## <a name="checking-latency"></a>Sprawdzanie opóźnienia
Użyj polecenia tracert do śledzenia na urządzeniu Microsoft Azure Edge, aby ustalić, czy istnieją wszelkich opóźnień powyżej 100 ms między przeskoków.

Z sieci lokalnej, należy uruchomić *tracert* na adres VIP bramy platformy Azure lub maszynie Wirtualnej. Gdy zostanie wyświetlony tylko * zwrócone, wiesz, został osiągnięty limit usługi Azure edge. Po wyświetleniu nazwy DNS, zawierające "MSN" zwrócił wiesz, że został osiągnięty limit sieci szkieletowej firmy Microsoft.<br><br>
![Sprawdzanie opóźnienia](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać dodatkowe informacje lub pomoc zapoznaj się z następujących linków:

- [Optymalizowanie przepływności sieci maszyn wirtualnych platformy Azure](../virtual-network/virtual-network-optimize-network-bandwidth.md)
- [Pomoc techniczna firmy Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
