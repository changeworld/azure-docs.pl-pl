---
title: Rozwiązywanie problemów z łącznością z odzyskiwaniem po awarii platformy Azure z platformą Azure za pomocą usługi Azure Site Recovery
description: Rozwiązywanie problemów z łącznością w odzyskiwaniu po awarii maszyny Wirtualnej platformy Azure
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: d2cc4133e52e7cab812413d23948da6ac2660e77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884872"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Rozwiązywanie problemów z łącznością sieciową platformy Azure z platformą Azure

W tym artykule opisano typowe problemy związane z łącznością sieciową podczas replikowania i odzyskiwania maszyn wirtualnych platformy Azure (VM) z jednego regionu do innego regionu. Aby uzyskać więcej informacji na temat wymagań dotyczących sieci, zobacz [wymagania dotyczące łączności dotyczące replikowania maszyn wirtualnych platformy Azure](azure-to-azure-about-networking.md).

Aby replikacja usługi Site Recovery działała, z maszyny wirtualnej wymagana jest łączność wychodząca z określonymi adresami URL lub zakresami adresów IP. Jeśli maszyna wirtualna znajduje się za zaporą lub używa reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej, może napotkać jeden z tych problemów.

| Adres URL | Szczegóły |
|---|---|
| `*.blob.core.windows.net` | Wymagane, aby dane mogły być zapisywane na koncie magazynu pamięci podręcznej w regionie źródłowym z maszyny Wirtualnej. Jeśli znasz wszystkie konta magazynu pamięci podręcznej dla maszyn wirtualnych, możesz użyć listy dozwolonych dla określonych adresów URL kont magazynu. Na przykład `cache1.blob.core.windows.net` `cache2.blob.core.windows.net` i zamiast `*.blob.core.windows.net`. |
| `login.microsoftonline.com` | Wymagane do autoryzacji i uwierzytelniania adresów URL usługi site recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Wymagane, aby komunikacja usługi odzyskiwania witryny mogła wystąpić z maszyny Wirtualnej. Można użyć odpowiedniego _adresu IP odzyskiwania witryny,_ jeśli serwer proxy zapory obsługuje adresy IP. |
| `*.servicebus.windows.net` | Wymagane, aby dane monitorowania i diagnostyki odzyskiwania lokacji mogły być zapisywane z maszyny Wirtualnej. Jeśli serwer proxy zapory obsługuje adresy _IP,_ można użyć odpowiedniego adresu IP monitorowania odzyskiwania witryny. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Łączność wychodząca dla adresów URL lub zakresów adresów IP odzyskiwania witryny (kod błędu 151037 lub 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Problem 1: nie można zarejestrować maszyny wirtualnej platformy Azure w usłudze Site Recovery (151195)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można ustanowić połączenia z punktami końcowymi usługi Site Recovery z powodu błędu rozpoznawania systemu nazw domen (DNS). Ten problem występuje częściej podczas ponownego odzyskiwania po awarii, gdy maszyna wirtualna uległa awarii, ale serwer DNS nie jest osiągalny z regionu odzyskiwania po awarii (DR).

#### <a name="resolution"></a>Rozwiązanie

Jeśli używasz niestandardowego systemu DNS, upewnij się, że serwer DNS jest dostępny z regionu odzyskiwania po awarii.

Aby sprawdzić, czy maszyna wirtualna używa niestandardowego ustawienia DNS:

1. Otwórz **maszyny wirtualne** i wybierz maszynę wirtualną.
1. Przejdź do **ustawień** maszyn wirtualnych i wybierz pozycję **Sieć**.
1. W **obszarze Sieć wirtualna/podsieć**wybierz łącze, aby otworzyć stronę zasobów sieci wirtualnej.
1. Przejdź do **pozycji Ustawienia** i wybierz pozycję **Serwery DNS**.

Spróbuj uzyskać dostęp do serwera DNS z maszyny wirtualnej. Jeśli serwer DNS nie jest dostępny, udostępnij go przez serwer DNS w wyniku awarii lub utworzenie linii lokacji między siecią odzyskiwania po awarii a systemem DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-błąd":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Problem 2: Konfiguracja odzyskiwania lokacji nie powiodła się (151196)

> [!NOTE]
> Jeśli maszyny wirtualne znajdują się za **standardowym** wewnętrznym modułem równoważenia obciążenia, domyślnie nie `login.microsoftonline.com`będzie miała dostępu do usług Office 365 IPS, takich jak . Zmień go na **Podstawowy** typ wewnętrznego modułu równoważenia obciążenia lub utwórz dostęp wychodzący, jak wspomniano w [artykule Konfigurowanie równoważenia obciążenia i reguł wychodzących w standardowym bilansie obciążenia przy użyciu interfejsu wiersza polecenia platformy Azure.](/azure/load-balancer/configure-load-balancer-outbound-cli)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można ustanowić połączenia z punktami końcowymi uwierzytelniania usługi Office 365 i tożsamości IP4.

#### <a name="resolution"></a>Rozwiązanie

- Usługa Azure Site Recovery wymaga dostępu do zakresów adresów IP usługi Office 365 do uwierzytelniania.
- Jeśli używasz reguł/serwera proxy grupy zabezpieczeń usługi Azure Network (NSG) do kontrolowania wychodzącej łączności sieciowej na maszynie Wirtualnej, upewnij się, że zezwalasz na komunikację z zakresami adresów IP usługi Office 365. Utwórz regułę nsg opartą na [usłudze Azure Active Directory (Azure AD),](/azure/virtual-network/security-overview#service-tags) która umożliwia dostęp do wszystkich adresów IP odpowiadających usłudze Azure AD.
- Jeśli nowe adresy zostaną dodane do usługi Azure AD w przyszłości, należy utworzyć nowe reguły sieciowej sieciowej sieciowej.

### <a name="example-nsg-configuration"></a>Przykładowa konfiguracja sieciowej grupy zabezpieczeń

W tym przykładzie pokazano, jak skonfigurować reguły sieciowej grupy danych sieciowych dla maszyny Wirtualnej do replikacji.

- Jeśli używasz reguł sieciowej sieciowej do kontrolowania łączności wychodzącej, użyj opcji Zezwalaj regułom **wychodzącym HTTPS** na port 443 dla wszystkich wymaganych zakresów adresów IP.
- W przykładzie zakłada się, że lokalizacją źródłą maszyny Wirtualnej jest wschodnia lokalizacja **stany USA,** a lokalizacją docelową są **środkowe stany USA.**

#### <a name="nsg-rules---east-us"></a>Zasady sieciowej sieciowej sieciowej - Wschodnie stany USA

1. Utwórz regułę zabezpieczeń wychodzących HTTPS dla nsg, jak pokazano na poniższym zrzucie ekranu. W tym przykładzie użyto **tagu usługi docelowej:** **Zakresy portów** _Storage.EastUS_ i Destination: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="znacznik magazynu":::

1. Utwórz regułę zabezpieczeń wychodzących HTTPS dla nsg, jak pokazano na poniższym zrzucie ekranu. W tym przykładzie użyto **tagu usługi docelowej:** **Zakresy portów** _Usługi AzureActiveDirectory_ i Docelowe: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="aad-tag":::

1. Utwórz reguły wychodzące portu HTTPS 443 dla adresów IP odzyskiwania lokacji, które odpowiadają lokalizacji docelowej:

   | Lokalizacja | Adres IP odzyskiwania witryny | Adres IP monitorowania odzyskiwania witryny |
   | --- | --- | --- |
   | Środkowe stany USA | 40.69.144.231 | 52.165.34.144 |

#### <a name="nsg-rules---central-us"></a>Zasady sieciowej sieciowej sieciowej - Central USA

W tym przykładzie te reguły sieciowej grupy ndg są wymagane, aby replikacja mogła być włączona z regionu docelowego do regionu źródłowego po przełączeniu w błąd:

1. Tworzenie reguły zabezpieczeń wychodzących HTTPS dla _magazynu.CentralUS:_

   - **Tag usługi docelowej**: _Storage.CentralUS_
   - **Zakresy portów docelowych:** _443_

1. Utwórz regułę zabezpieczeń wychodzących HTTPS dla _usługi AzureActiveDirectory_.

   - **Tag usługi docelowej**: _AzureActiveDirectory_
   - **Zakresy portów docelowych:** _443_

1. Utwórz reguły wychodzące portu HTTPS 443 dla adresów IP odzyskiwania lokacji, które odpowiadają lokalizacji źródłowej:

   | Lokalizacja | Adres IP odzyskiwania witryny | Adres IP monitorowania odzyskiwania witryny |
   | --- | --- | --- |
   | Wschodnie stany USA | 13.82.88.226 | 104.45.147.24 |

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Problem 3: Konfiguracja odzyskiwania lokacji nie powiodła się (151197)

#### <a name="possible-cause"></a>Możliwa przyczyna

Nie można ustanowić połączenia z punktami końcowymi usługi Azure Site Recovery.

#### <a name="resolution"></a>Rozwiązanie

Usługa Azure Site Recovery wymaga dostępu do [zakresów adresów IP usługi Site Recovery](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) w zależności od regionu. Upewnij się, że wymagane zakresy adresów IP są dostępne z maszyny Wirtualnej.

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Problem 4: Replikacja platformy Azure do platformy Azure nie powiodła się, gdy ruch sieciowy przechodzi przez lokalny serwer proxy (151072)

#### <a name="possible-cause"></a>Możliwa przyczyna

Niestandardowe ustawienia serwera proxy są nieprawidłowe, a agent usługi Azure Site Recovery Mobility nie wyekslizuł automatycznie ustawień serwera proxy w programie Internet Explorer (IE).

#### <a name="resolution"></a>Rozwiązanie

1. Agent usługi mobilności wykrywa ustawienia serwera proxy z `/etc/environment` IE w systemie Windows i linux.
1. Jeśli wolisz ustawić serwer proxy tylko dla usługi Azure Site Recovery Mobility, możesz podać szczegóły serwera proxy w _pliku ProxyInfo.conf_ znajdujące się pod adresem:

   - **Linux**:`/usr/local/InMage/config/`
   - **Okna**:`C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _Plik ProxyInfo.conf_ powinien mieć ustawienia serwera proxy w następującym formacie _INI:_

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Agent usługi Azure Site Recovery Mobility obsługuje tylko **nieuwierzyliowane serwery proxy.**

### <a name="fix-the-problem"></a>Rozwiązywanie problemu

Aby [zezwolić na wymagane adresy URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) lub [wymagane zakresy adresów IP,](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)wykonaj czynności opisane w [dokumencie zawierającym wskazówki dotyczące sieci.](site-recovery-azure-to-azure-networking-guidance.md)

## <a name="next-steps"></a>Następne kroki

[Replikowanie maszyn wirtualnych platformy Azure do innego regionu platformy Azure](azure-to-azure-how-to-enable-replication.md)
