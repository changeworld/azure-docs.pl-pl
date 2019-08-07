---
title: Azure Site Recovery Rozwiązywanie problemów z łącznością i błędami sieci platformy Azure na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z błędami i problemami podczas replikowania maszyn wirtualnych platformy Azure na potrzeby odzyskiwania po awarii
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang
ms.openlocfilehash: 5ed57c93f73eb9a9e972a683f1068692a5963e54
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816967"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Rozwiązywanie problemów z łącznością sieciową na platformie Azure na platformie Azure

W tym artykule opisano typowe problemy związane z łącznością sieciową w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego. Więcej informacji o wymaganiach dotyczących sieci znajduje się w temacie [wymagania dotyczące łączności w przypadku replikacji maszyn wirtualnych platformy Azure](azure-to-azure-about-networking.md).

W przypadku replikacji usługi Site Recovery do pracy, łączność wychodząca z określonych adresów URL lub IP zakresów jest wymagane z maszyny Wirtualnej. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł Sieciowej grupy zabezpieczeń sieci do sterowania ruchem wychodzącym, może być jedną z tych problemów twarzy.

**Adres URL** | **Szczegóły**  
--- | ---
*.blob.core.windows.net | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z poziomu maszyny wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz zezwolić na listę adresów URL określonych kont magazynu (na przykład cache1.blob.core.windows.net i cache2.blob.core.windows.net) zamiast *. blob.core.windows.net
login.microsoftonline.com | Wymagany do autoryzacji i uwierzytelniania do adresów URL usługi Site Recovery.
*.hypervrecoverymanager.windowsazure.com | Wymagane, aby komunikacja z usługą Site Recovery mogła się odbywać z poziomu maszyny wirtualnej. W przypadku, gdy serwer proxy zapory obsługuje adresy IP, można użyć odpowiedniego "Site Recovery".
*.servicebus.windows.net | Wymagane, aby dane dotyczące monitorowania i diagnostyki Site Recovery mogły być zapisywane z poziomu maszyny wirtualnej. W przypadku, gdy serwer proxy zapory obsługuje adresy IP, można użyć odpowiadającego "Site Recovery monitorowanie".

# <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Połączenia ruchu wychodzącego dla zakresów adresów URL Site Recovery lub adres IP (kod błędu: 151037 lub 151072)

## <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Problem 1: Nie można zarejestrować maszyny wirtualnej platformy Azure w Site Recovery (151195) </br>
- **Możliwa przyczyna** </br>
  - Nie można nawiązać połączenia, aby Site Recovery punkty końcowe z powodu błędu rozpoznawania nazw DNS.
  - Jest to częściej występujące podczas ponownej ochrony, gdy zostały przełączone w tryb failover maszyny wirtualnej, ale serwer DNS nie jest dostępny w regionie odzyskiwania po awarii.

- **Rozdzielczość**
   - W przypadku korzystania z niestandardowej usługi DNS upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii. Aby sprawdzić, czy masz niestandardowe DNS, przejdź do maszyny Wirtualnej > sieć odzyskiwania po awarii > serwery DNS. Wypróbuj, uzyskiwanie dostępu do serwera DNS z maszyny wirtualnej. Jeśli nie jest on dostępny, udostępnij go przez przechodzenie przez serwer DNS w tryb failover lub Tworzenie linii lokacji między siecią DR i systemem DNS.

    ![Błąd modelu COM](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


## <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja Site Recovery nie powiodła się (151196)

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za standardowym wewnętrznym modułem równoważenia obciążenia, nie ma dostępu do adresów IP usługi O365 (czyli login.microsoftonline.com) domyślnie. Zmień go na **podstawowy** typ wewnętrznego modułu równoważenia obciążenia lub Utwórz dostęp wychodzący, jak wspomniano w [artykule](https://aka.ms/lboutboundrulescli).

- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z uwierzytelnianiem usługi Office 365 i punktów końcowych IP4 tożsamości.

- **Rozdzielczość**
  - Usługa Azure Site Recovery zakresy adresów IP usługi Office 365 są wymagane do uwierzytelniania.
    Jeśli używasz proxy reguły i zapory Sieciowej grupy zabezpieczeń sieci platformy Azure do kontrolowania połączenia sieciowego ruchu wychodzącego na maszynie Wirtualnej, upewnij się, że zezwalają na komunikację zakresy adresów IP usługi Office 365. Utwórz opartą na [znacznikach usługi Azure Active Directory (Azure AD)](../virtual-network/security-overview.md#service-tags) regułę sieciowej grupy zabezpieczeń na potrzeby zezwalania na dostęp do wszystkich adresów IP odpowiadających usłudze Azure AD
      - Jeśli nowe adresy są dodawane do usługi Azure AD w przyszłości, należy utworzyć nowe reguły sieciowej grupy zabezpieczeń.

### <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

Ten przykład pokazuje, jak skonfigurować reguły sieciowej grupy zabezpieczeń dla maszyny wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej grupy zabezpieczeń do kontrolowania łączności wychodzącej, Użyj reguł "Zezwalaj na ruch wychodzący protokołu HTTPS" na port: 443 dla wszystkich wymaganych zakresów adresów IP.
- Przykład zakłada, że lokalizacją źródłową maszyny wirtualnej jest "Wschodnie stany USA", a lokalizacja docelowa to "środkowe stany USA".

### <a name="nsg-rules---east-us"></a>Reguły sieciowej grupy zabezpieczeń — Wschodnie stany USA

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. Wschód" na sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![Magazyn — tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń, jak pokazano na poniższym zrzucie ekranu.

      ![AAD — tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. Utwórz reguły wychodzącego protokołu HTTPS (443) dla adresów IP Site Recovery, które odpowiadają lokalizacji docelowej:

   **Location** | **Site Recovery adres IP** |  **Site Recovery monitorowania adresu IP**
    --- | --- | ---
   Środkowe stany USA | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Reguły sieciowej grupy zabezpieczeń — środkowe stany USA

Te reguły są wymagane, aby można było włączyć replikację z regionu docelowego do obszaru źródłowego po zakończeniu pracy w trybie failover:

1. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "Storage. środkowe" w sieciowej grupy zabezpieczeń.

2. Utwórz wychodzącą regułę zabezpieczeń HTTPS (443) dla elementu "usługi azureactivedirectory" w sieciowej grupy zabezpieczeń.

3. Utwórz reguły wychodzącego protokołu HTTPS (443) dla Site Recovery adresów IP, które odpowiadają lokalizacji źródłowej:

   **Location** | **Site Recovery adres IP** |  **Site Recovery monitorowania adresu IP**
    --- | --- | ---
   Środkowe stany USA | 13.82.88.226 | 104.45.147.24
## <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja Site Recovery nie powiodła się (151197)
- **Możliwa przyczyna** </br>
  - Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

- **Rozdzielczość**
  - Usługa Azure Site Recovery wymagany dostęp do [zakresów adresów IP odzyskiwania lokacji](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) w zależności od regionu. Upewnij się, wymagane zakresy adresów ip są dostępne z maszyny wirtualnej.


## <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Replikacja — A2A nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)
- **Możliwa przyczyna** </br>
  - Niestandardowe ustawienia serwera proxy są nieprawidłowe, a Azure Site Recovery Agent usługi mobilności nie wykrył automatycznie ustawień serwera proxy z programu IE


- **Rozdzielczość**
  1. Agent usługi mobilności wykrywa ustawienia serwera proxy programu Internet Explorer na Windows i /etc/environment w systemie Linux.
  2. Jeśli wolisz ustawić serwer proxy tylko dla usługi mobilności Azure Site Recovery, możesz podać szczegóły serwera proxy w ProxyInfo. conf znajdującym się w:</br>
     - ``/usr/local/InMage/config/`` na ***systemu Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` na ***Windows***
  3. ProxyInfo.conf powinna mieć ustawienia serwera proxy w następującym formacie pliku INI.</br>
                *[proxy]*</br>
                *Adres =http://1.2.3.4*</br>
                *Port = 567*</br>
  4. Agent usługi mobilności Azure Site Recovery obsługuje tylko ***nieuwierzytelnione serwery proxy***.

### <a name="fix-the-problem"></a>Rozwiąż problem
Aby zezwolić na [wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), wykonaj kroki opisane w [dokumencie wskazówki dotyczące sieci](site-recovery-azure-to-azure-networking-guidance.md).


## <a name="next-steps"></a>Następne kroki
[Replikowanie maszyn wirtualnych platformy Azure](site-recovery-replicate-azure-to-azure.md)
