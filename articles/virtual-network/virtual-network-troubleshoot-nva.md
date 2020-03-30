---
title: Rozwiązywanie problemów z sieciowym urządzeniem wirtualnym na platformie Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z sieciowym urządzeniem wirtualnym na platformie Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056833"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemy z sieciowym urządzeniem wirtualnym na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podczas korzystania z urządzenia sieciowego (NVA) na platformie Microsoft Azure mogą wystąpić problemy z łącznością maszyny wirtualnej lub sieci VPN. Ten artykuł zawiera podstawowe kroki ułatwiające sprawdzanie poprawności podstawowych wymagań platformy Azure dla konfiguracji urządzenia WUS.

Dostawca usługi WUS zapewnia pomoc techniczną dla systemów wus innych firm i ich integrację z platformą Azure.

> [!NOTE]
> Jeśli masz problem z łącznością lub routingiem, który obejmuje urządzenie WUS, należy skontaktować się bezpośrednio [z dostawcą urządzenia WUS.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista kontrolna rozwiązywania problemów z dostawcą urządzenia WUS

- Aktualizacje oprogramowania dla oprogramowania NVA VM
- Konfiguracja i funkcjonalność konta usługi
- Trasy zdefiniowane przez użytkownika (UDR) w podsieciach sieci wirtualnej, które kierują ruch do sieci WODO
- UDR w podsieciach sieci wirtualnej, które kierują ruch z sieci WUS
- Tabele i reguły routingu w ramach urządzenia WUS (na przykład od nic1 do nic2)
- Śledzenie kart sieciowych NVA w celu weryfikacji odbioru i wysyłania ruchu sieciowego
- Podczas korzystania ze standardowej jednostki SKU i publicznych usług IP musi być utworzona grupa operacyjna i jawna reguła, aby umożliwić kierowanie ruchu do urządzenia WUS.

## <a name="basic-troubleshooting-steps"></a>Podstawowe kroki rozwiązywania problemów

- Sprawdź podstawową konfigurację
- Sprawdź wydajność urządzenia NVA
- Zaawansowane rozwiązywanie problemów z siecią

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Sprawdź minimalne wymagania konfiguracyjne dla obiektów WA na platformie Azure

Każdy system WUS ma podstawowe wymagania konfiguracyjne do poprawnego działania na platformie Azure. W poniższej sekcji przedstawiono kroki, aby zweryfikować te podstawowe konfiguracje. Aby uzyskać więcej informacji, [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Sprawdzanie, czy przekazywanie adresów IP jest włączone w ucho.**

Korzystanie z witryny Azure Portal

1. Znajdź zasób urządzenia WUS w [witrynie Azure portal](https://portal.azure.com), wybierz pozycję Sieć, a następnie wybierz interfejs sieciowy.
2. Na stronie Interfejs sieci wybierz pozycję Konfiguracja IP.
3. Upewnij się, że przekazywanie adresów IP jest włączone.

Korzystanie z programu PowerShell

1. Otwórz program PowerShell, a następnie zaloguj się do konta platformy Azure.
2. Uruchom następujące polecenie (zastąp wartości w nawiasach z informacjami):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Sprawdź **EnableIPForwarding** właściwość.
4. Jeśli przekazywanie adresów IP nie jest włączone, uruchom następujące polecenia, aby ją włączyć:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Sprawdź, czy sieć sieciowa sieciowa jest obsługiwana podczas korzystania ze standardowej usługi SKU Pubilc IP** W przypadku korzystania ze standardowej jednostki SKU i publicznych usług IP musi być utworzona grupa operacyjna i jawna reguła zezwalana na ruch do urządzenia WUS.

**Sprawdź, czy ruch można kierować do urządzenia WUS**

1. W [witrynie Azure portal](https://portal.azure.com)otwórz program Kontrola **sieci**, wybierz pozycję **Następny przeskok**.
2. Określ maszynę wirtualną skonfigurowaną do przekierowywania ruchu do urządzenia WUS, oraz docelowy adres IP, pod którym ma być wyświetlony następny przeskok. 
3. Jeśli urządzenia WUS nie są wyświetlane jako **następny przeskok,** sprawdź i zaktualizuj tabele marszrut platformy Azure.

**Sprawdź, czy ruch może dotrzeć do urządzenia WUS**

1. W [witrynie Azure portal](https://portal.azure.com)otwórz program Kontrola **sieci,** a następnie wybierz pozycję **Weryfikuj przepływ adresów IP**. 
2. Określ maszynę wirtualną i adres IP urządzenia WUS, a następnie sprawdź, czy ruch jest blokowany przez grupy zabezpieczeń sieciowych(NSG).
3. Jeśli istnieje reguła sieciowej sieciowej, która blokuje ruch, zlokalizuj sieciowej sieciowej w skutecznych reguł **zabezpieczeń,** a następnie zaktualizuj ją, aby umożliwić ruch do przekazania. Następnie uruchom ponownie **weryfikację przepływu IP** i użyj **rozwiązywania problemów z połączeniem,** aby przetestować komunikację TCP z maszyny Wirtualnej na wewnętrzny lub zewnętrzny adres IP.

**Sprawdzanie, czy urządzenia WUS i maszyny wirtualne nasłuchują oczekiwanego ruchu**

1. Połącz się z urządzeniem WUS za pomocą protokołu RDP lub SSH, a następnie uruchom następujące polecenie:

    W przypadku systemu Windows:

        netstat -an

    Dla Linuksa:

        netstat -an | grep -i listen
2. Jeśli nie widzisz portu TCP używanego przez oprogramowanie NVA wymienione w wynikach, należy skonfigurować aplikację na urządzeniach WUS i maszynie wirtualnej, aby nasłuchiwać i odpowiadać na ruch docierany do tych portów. [W razie potrzeby skontaktuj się z dostawcą urządzenia WUS, aby uzyskać pomoc.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="check-nva-performance"></a>Sprawdź wydajność NVA

### <a name="validate-vm-cpu"></a>Sprawdzanie poprawności procesora maszyn wirtualnych

Jeśli użycie procesora CPU zbliży się do 100 procent, mogą wystąpić problemy, które wpływają na spadek pakietów sieciowych. Maszyna wirtualna raportuje średni procesor CPU dla określonego przedziału czasu w witrynie Azure portal. Podczas skok procesora CPU, zbadać, który proces na maszynie wirtualnej gościa powoduje wysoki procesor CPU i ograniczyć go, jeśli to możliwe. Może być również trzeba zmienić rozmiar maszyny Wirtualnej do większego rozmiaru jednostki SKU lub, w przypadku zestawu skalowania maszyny wirtualnej, zwiększyć liczbę wystąpień lub ustawić automatyczne skalowanie w użyciu procesora CPU. W przypadku jednego z tych problemów [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)w celu uzyskania pomocy w razie potrzeby.

### <a name="validate-vm-network-statistics"></a>Sprawdzanie poprawności statystyk sieci maszyn wirtualnych

Jeśli użycie w sieci maszyny Wirtualnej zwiększa lub pokazuje okresy wysokiego użycia, może być również trzeba zwiększyć rozmiar jednostki SKU maszyny Wirtualnej, aby uzyskać wyższe możliwości przepływności. Maszynę Wirtualną można również ponownie wdrożyć, włączając przyspieszoną sieć. Aby sprawdzić, czy urządzenie WUS obsługuje funkcję przyspieszonej sieci, w razie potrzeby [skontaktuj się z dostawcą urządzenia WUS,](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)aby uzyskać pomoc.

## <a name="advanced-network-administrator-troubleshooting"></a>Zaawansowane rozwiązywanie problemów z administratorem sieci

### <a name="capture-network-trace"></a>Przechwytywanie śledzenia sieci
Przechwyć jednoczesne śledzenie sieci na źródłowej maszynie Wirtualnej, wynalezienie nva i docelowej maszynie wirtualnej podczas uruchamiania **[protokołu PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** lub **Nmap,** a następnie zatrzymanie śledzenia.

1. Aby przechwycić jednoczesne śledzenie sieci, uruchom następujące polecenie:

   **W przypadku systemu Windows**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **W przypadku systemu Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Użyj **PsPing** lub **Nmap** ze źródłowej maszyny Wirtualnej `PsPing 10.0.0.4:80` do `Nmap -p 80 10.0.0.4`docelowej maszyny Wirtualnej (na przykład: lub ).
3. Otwórz śledzenie sieci z docelowej maszyny Wirtualnej przy użyciu [Monitora sieci](https://www.microsoft.com/download/details.aspx?id=4865) lub tcpdump. Zastosuj filtr wyświetlania dla adresu IP źródłowej maszyny Wirtualnej, z `IPv4.address==10.0.0.4 (Windows netmon)` `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` której uruchomiono **psping** lub **Nmap,** na przykład lub (Linux).

### <a name="analyze-traces"></a>Analizowanie śladów

Jeśli nie widzisz pakietów przychodzących do śledzenia maszyny Wirtualnej wewnętrznej bazy danych, prawdopodobnie istnieje prawdopodobieństwo, że interweniujące sieci NSG lub UDR lub tabele routingu NVA są niepoprawne.

Jeśli pakiety przychodzące są widoczne, ale brak jest odpowiedzi, być może musisz rozwiązać problem z zaporą lub aplikacją maszyny wirtualnej. W przypadku jednego z tych problemów [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)w celu uzyskania pomocy w razie potrzeby.