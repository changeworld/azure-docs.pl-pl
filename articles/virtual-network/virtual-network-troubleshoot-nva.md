---
title: Rozwiązywanie problemów urządzenie wirtualne sieci na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wirtualnego urządzenia sieciowego na platformie Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 00393395745ca96ae14269ae80e4f3d25673fbfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723010"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemy z urządzenia wirtualnego sieci na platformie Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz napotkać maszyny Wirtualnej lub problemy z połączeniem sieci VPN i błędy podczas korzystania z innego podmiotu z wirtualnego urządzenia sieciowego (WUS) w systemie Microsoft Azure. Ten artykuł zawiera podstawowe kroki, aby pomóc podczas weryfikowania podstawowych wymagań platformy Azure w przypadku konfiguracji urządzenia WUS.

Pomoc techniczna dla urządzeń WUS innych firm i ich integracji z platformą Azure jest świadczona przez producenta urządzenia WUS.

> [!NOTE]
> Jeśli masz połączenie lub problemu z routingiem, która obejmuje NVA należy [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) bezpośrednio.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Lista kontrolna dotycząca rozwiązywania problemów z dostawcą urządzenia WUS

- Aktualizacje oprogramowania dla oprogramowania maszyna wirtualna urządzenia WUS
- Konfiguracja konta usługi i funkcje
- Trasy zdefiniowane przez użytkownika (Udr) w podsieciach sieci wirtualnej, które kierować ruch do wirtualnego urządzenia Sieciowego
- Tras zdefiniowanych przez użytkownika w podsieci sieci wirtualnej, które kierować ruch z urządzenia WUS
- Routing tabel i reguły urządzenia WUS (na przykład z NIC1 do NIC2)
- Śledzenie na kartach interfejsu sieciowego urządzenia WUS, aby sprawdzić, odbierania i wysyłania ruchu sieciowego
- Korzystając z standardowej jednostki SKU i publicznych adresów IP, musi istnieć sieciowa grupa zabezpieczeń utworzona i jawne reguły zezwalające na ruch można kierować do urządzenia NVA.

## <a name="basic-troubleshooting-steps"></a>Podstawowe kroki rozwiązywania problemów

- Sprawdzanie podstawowej konfiguracji
- Sprawdź wydajność urządzenia WUS
- Rozwiązywanie problemów z siecią zaawansowane

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Sprawdzanie wymagań dotyczących minimalnej konfiguracji dla urządzeń WUS na platformie Azure

Każde urządzenie WUS ma wymagania konfiguracji podstawowej do poprawnego działania na platformie Azure. W poniższej sekcji przedstawiono kroki, aby sprawdzić te konfiguracje podstawowe. Aby uzyskać więcej informacji [skontaktuj się z dostawcą urządzenia WUS](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Sprawdź, czy przesyłanie dalej IP jest włączone na urządzeniu WUS**

Korzystanie z witryny Azure Portal

1. Znajdź zasób urządzenia WUS w [witryny Azure portal](https://portal.azure.com)wybierz sieć, a następnie wybierz interfejs sieciowy.
2. Na stronie interfejsu sieciowego wybierz konfigurację adresu IP.
3. Upewnij się, że jest włączone przekazywanie adresów IP.

Korzystanie z programu PowerShell

1. Otwórz program PowerShell, a następnie zaloguj się do konta platformy Azure.
2. Uruchom następujące polecenie (Zastąp wartości w nawiasach kwadratowych z informacjami o):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Sprawdź **EnableIPForwarding** właściwości.
4. Jeśli nie jest włączone przekazywanie adresów IP, uruchom następujące polecenia, aby ją włączyć:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Sprawdź dla sieciowej grupy zabezpieczeń, korzystając z IP Pubilc w warstwie standardowa jednostka SKU** przy użyciu standardowej jednostki SKU i publicznych adresów IP, musi istnieć sieciowa grupa zabezpieczeń utworzona i jawne reguły, aby zezwolić na ruch do urządzenia WUS.

**Sprawdź, czy można kierować ruch do urządzenia NVA**

1. Na [witryny Azure portal](https://portal.azure.com), otwórz **usługi Network Watcher**, wybierz opcję **następnego skoku**.
2. Określ maszynę Wirtualną, która jest skonfigurowana, aby przekierować ruch do urządzenia WUS i docelowy adres IP, na którym chcesz wyświetlić następny przeskok. 
3. Jeśli urządzenie WUS nie jest wymieniony jako **miejsca docelowego następnego skoku**, sprawdź, jak i aktualizacja tabel tras platformy Azure.

**Sprawdź, czy ruch może uzyskać dostęp do urządzenia NVA**

1. W [witryny Azure portal](https://portal.azure.com), otwórz **usługi Network Watcher**, a następnie wybierz pozycję **zweryfikować przepływ IP**. 
2. Określ maszynę Wirtualną i adres IP urządzenia NVA, a następnie sprawdź, czy ruch jest blokowany przez wszystkie grupy zabezpieczeń sieci (NSG).
3. W przypadku regułę sieciowej grupy zabezpieczeń, która blokuje ruch zlokalizować sieciowej grupy zabezpieczeń w **efektywnym elementem systemu zabezpieczeń** reguły, a następnie zaktualizuj go, aby zezwolić na ruch do przekazania. Następnie uruchom **zweryfikować przepływ IP** ponownie i użyj **Rozwiązywanie problemów z połączeniami** do testowania łączności TCP z maszyny Wirtualnej na wewnętrzny lub zewnętrzny adres IP.

**Sprawdź, czy urządzenie WUS i maszyny wirtualne nasłuchują oczekiwanego natężenia ruchu**

1. Łączenie z urządzenia WUS za pomocą protokołu RDP lub SSH, a następnie uruchom następujące polecenie:

    W przypadku systemu Windows:

        netstat -an

    Dla systemu Linux:

        netstat -an | grep -i listen
2. Jeśli nie widzisz port TCP używany przez urządzenie WUS oprogramowania, który znajduje się w wynikach należy skonfigurować aplikację na urządzenie WUS i maszynę Wirtualną, aby nasłuchiwać i odpowiadać na ruch przychodzący, która będzie działać na te porty. [Skontaktuj się z dostawcą urządzenia WUS w celu uzyskania pomocy zgodnie z potrzebami](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Sprawdź wydajność urządzenia WUS

### <a name="validate-vm-cpu"></a>Sprawdź poprawność Procesora maszyny Wirtualnej

Jeśli użycie procesora CPU pobiera bliskie 100 procent, mogą wystąpić problemy, które mają wpływ na sieć pakietów docelowych. Raporty maszyny Wirtualnej średni procesora CPU dla przedział czasu określonego w witrynie Azure portal. Podczas wzrost użycia Procesora Zbadaj, który proces na gościa maszyny Wirtualnej powoduje wysokie użycie procesora CPU i rozwiązać go, jeśli jest to możliwe. Należy również zmienić rozmiar maszyny Wirtualnej na większy rozmiar jednostki SKU lub, w przypadku zestawu skalowania maszyn wirtualnych, zwiększ liczbę wystąpień lub zestawu skalowania automatycznego, użycie procesora CPU. Dla jednej z tych problemów [skontaktuj się z dostawcą urządzenia WUS w celu uzyskania pomocy](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), zgodnie z potrzebami.

### <a name="validate-vm-network-statistics"></a>Sprawdź poprawność statystykę sieci maszyny Wirtualnej

Jeśli używasz sieci maszyny Wirtualnej gwałtowne wzrosty lub pokazuje okresami wysokiego użycia, również może być konieczne zwiększenie rozmiaru jednostki SKU maszyny Wirtualnej w celu uzyskania wyższej przepływności możliwości. Można także wdrożyć ponownie maszyny Wirtualnej, dzięki przyspieszonej sieci włączona. Aby sprawdzić, czy urządzenie WUS obsługuje funkcję przyspieszonej sieci, [skontaktuj się z dostawcą urządzenia WUS w celu uzyskania pomocy](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), zgodnie z potrzebami.

## <a name="advanced-network-administrator-troubleshooting"></a>Administrator sieci Zaawansowane rozwiązywanie problemów z

### <a name="capture-network-trace"></a>Przechwytywania śledzenia sieci
Przechwytywanie, Śledzenie sieci na źródłowej maszynie Wirtualnej urządzenie WUS i docelowej maszyny Wirtualnej podczas uruchamiania **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** lub **Nmap**, a następnie Zatrzymaj śledzenie.

1. Do przechwytywania śladu sieci, uruchom następujące polecenie:

   **Dla Windows**

   netsh trace start capture = yes tracefile=c:\adres_ip_serwera.etl scenario = netconnection

   **For Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Użyj **PsPing** lub **Nmap** ze źródłowej maszyny Wirtualnej do lokalizacji docelowej maszyny Wirtualnej (na przykład: `PsPing 10.0.0.4:80` lub `Nmap -p 80 10.0.0.4`).
3. Otwórz dane śledzenia sieci z docelowej maszyny Wirtualnej przy użyciu [Network Monitor](https://www.microsoft.com/download/details.aspx?id=4865) lub tcpdump. Zastosuj filtr wyświetlania dla adresu IP maszyny wirtualnej źródłowego uruchomiono **PsPing** lub **Nmap** , takich jak `IPv4.address==10.0.0.4 (Windows netmon)` lub `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analizowanie danych śledzenia

Jeśli pakiety przychodzące do danych śledzenia maszyny Wirtualnej zaplecza nie jest widoczny, prawdopodobnie jest sieciowa grupa zabezpieczeń lub trasa zdefiniowana przez użytkownika zakłóca lub tabele routingu urządzenia WUS są niepoprawne.

Jeśli pakiety przychodzące są widoczne, ale brak jest odpowiedzi, być może musisz rozwiązać problem z zaporą lub aplikacją maszyny wirtualnej. Dla jednej z tych problemów [skontaktuj się z dostawcą urządzenia WUS w celu uzyskania pomocy zgodnie z potrzebami](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).