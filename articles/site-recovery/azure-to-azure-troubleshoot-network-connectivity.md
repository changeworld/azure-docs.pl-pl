---
title: Rozwiązywanie problemów z łącznością z odzyskiwaniem po awarii platformy Azure z platformą Azure za pomocą usługi Azure Site Recovery
description: Rozwiązywanie problemów z łącznością w odzyskiwaniu po awarii maszyny Wirtualnej platformy Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 08/05/2019
ms.openlocfilehash: b082e1aca094dcb335a7268e4c116376d756fd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292025"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Rozwiązywanie problemów z łącznością sieciową platformy Azure z platformą Azure

W tym artykule opisano typowe problemy związane z łącznością sieciową podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego regionu. Aby uzyskać więcej informacji na temat wymagań dotyczących sieci, zobacz [wymagania dotyczące łączności dotyczące replikowania maszyn wirtualnych platformy Azure](azure-to-azure-about-networking.md).

Aby replikacja usługi Site Recovery działała, z maszyny wirtualnej wymagana jest łączność wychodząca z określonymi adresami URL lub zakresami adresów IP. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej, może napotkać jeden z tych problemów.

**Adres URL** | **Szczegóły**  
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z maszyny Wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz zezwolić na wyświetlanie określonych adresów URL kont magazynu (na przykład cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *.blob.core.windows.net
login.microsoftonline.com | Wymagane do autoryzacji i uwierzytelniania adresów URL usługi site recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja usługi odzyskiwania witryny mogła wystąpić z maszyny Wirtualnej. Możesz użyć odpowiedniego adresu IP odzyskiwania witryny, jeśli serwer proxy zapory obsługuje adresy IP.
*.servicebus.windows.net | Wymagane, aby dane monitorowania i diagnostyki odzyskiwania lokacji mogły być zapisywane z maszyny Wirtualnej. Możesz użyć odpowiedniego adresu IP monitorowania odzyskiwania witryny, jeśli serwer proxy zapory obsługuje adresy IP.

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Łączność wychodząca dla adresów URL lub zakresów adresów IP odzyskiwania witryny (kod błędu 151037 lub 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a><a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: nie można zarejestrować maszyny wirtualnej platformy Azure w usłudze Site Recovery (151195) </br>
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Site Recovery z powodu błędu rozpoznawania DNS.
  - Ta sytuacja występuje częściej podczas ponownego włączania ochrony, gdy maszyna wirtualna została przełączona w tryb failover, ale serwer DNS jest nieosiągalny z regionu odzyskiwania po awarii.

- **Rozdzielczość**
   - Jeśli używasz niestandardowego systemu DNS, upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii. Aby sprawdzić, czy masz niestandardowy system DNS, przejdź do sieci odzyskiwania po awarii> maszyny Wirtualnej> serwerów DNS. Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli nie jest dostępny, udostępnij ją przez niepowodzenie serwera DNS lub tworząc linię lokacji między siecią odzyskiwania po awarii a systemem DNS.

    ![com-błąd](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja odzyskiwania lokacji nie powiodła się (151196)

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za **standardowym** modułem wyważania obciążenia wewnętrznego, domyślnie nie będą miały dostępu do adresów IP usługi O365 (czyli login.microsoftonline.com). Zmień go na **Podstawowy** typ wewnętrznego modułu równoważenia obciążenia lub utwórz dostęp wychodzący, jak wspomniano w [artykule](https://aka.ms/lboutboundrulescli).

- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi uwierzytelniania usługi Office 365 i tożsamości IP4.

- **Rozdzielczość**
  - Usługa Azure Site Recovery wymagała dostępu do zakresów usług Office 365 do uwierzytelniania.
    Jeśli używasz reguł grupy zabezpieczeń usługi Azure Network (NSG)/serwera proxy zapory do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że zezwalasz na komunikację z rozsypkami IPranges usługi O365. Tworzenie [reguły nsg usługi usługi Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) umożliwiającej dostęp do wszystkich adresów IP odpowiadających usłudze Azure AD
      - Jeśli nowe adresy zostaną dodane do usługi Azure AD w przyszłości, należy utworzyć nowe reguły sieciowej sieciowej sieciowej.

### <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

W tym przykładzie pokazano, jak skonfigurować reguły sieciowej grupy danych sieciowych dla maszyny Wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej sieciowej do kontrolowania łączności wychodzącej, użyj reguł "Zezwalaj na wychodzące https" do portu:443 dla wszystkich wymaganych zakresów adresów IP.
- W przykładzie zakłada się, że lokalizacja źródło maszyny Wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "Central US".

### <a name="nsg-rules---east-us"></a>Zasady sieciowej sieciowej sieciowej - Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "Storage.EastUS" w nsg, jak pokazano na poniższym zrzucie ekranu.

      ![znacznik magazynu](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "AzureActiveDirectory" w pliku zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Tworzenie wychodzących reguł HTTPS (443) dla adresów IP odzyskiwania lokacji, które odpowiadają lokalizacji docelowej:

   **Lokalizacja** | **Adres IP odzyskiwania witryny** |  **Adres IP monitorowania odzyskiwania witryny**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Zasady sieciowej sieciowej sieciowej - Central USA

Te reguły są wymagane, aby replikacja mogła być włączona z regionu docelowego do regionu źródłowego po przełączeniu w błąd:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "Storage.CentralUS" w nsg.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla "AzureActiveDirectory" w nsg.

3. Tworzenie wychodzących reguł HTTPS (443) dla adresów IP odzyskiwania witryny, które odpowiadają lokalizacji źródłowej:

   **Lokalizacja** | **Adres IP odzyskiwania witryny** |  **Adres IP monitorowania odzyskiwania witryny**
    --- | --- | ---
   Środkowe stany USA | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja odzyskiwania lokacji nie powiodła się (151197)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

- **Rozdzielczość**
  - Usługa Azure Site Recovery wymaga dostępu do [zakresów adresów IP usługi Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-using-service-tags) w zależności od regionu. Upewnij się, że wymagane zakresy adresów IP są dostępne z maszyny wirtualnej.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Replikacja A2A nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)
- **Możliwa przyczyna** </br>
  - Niestandardowe ustawienia serwera proxy są nieprawidłowe, a agent usługi mobilności usługi Azure Site Recovery Nie wykrył automatycznie ustawień serwera proxy z programu IE


- **Rozdzielczość**
  1. Agent usługi mobilności wykrywa ustawienia serwera proxy z IE w systemie Windows i /etc/environment w systemie Linux.
  2. Jeśli wolisz ustawić serwer proxy tylko dla usługi Azure Site Recovery Mobility Service, możesz podać szczegóły serwera proxy w pliku ProxyInfo.conf znajdujące się pod adresem:</br>
     - ``/usr/local/InMage/config/``na ***Linuksie***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config``w ***systemie Windows***
  3. Plik ProxyInfo.conf powinien zawierać ustawienia serwera proxy w następującym formacie pliku INI.</br>
                *[proxy]*</br>
                *Adres=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Agent usługi Azure Site Recovery Mobility Service obsługuje tylko ***nieu uwierzytelnione serwery proxy.***

### <a name="fix-the-problem"></a>Rozwiązywanie problemu
Aby [zezwolić na wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)wykonaj czynności opisane w [dokumencie zawierającym wskazówki dotyczące sieci.](site-recovery-azure-to-azure-networking-guidance.md)


## <a name="next-steps"></a>Następne kroki
[Replikowanie maszyn wirtualnych platformy Azure](site-recovery-replicate-azure-to-azure.md)
