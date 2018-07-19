---
title: Rozwiązywanie problemów z replikacją dla maszyny Wirtualnej VMware oraz replikacji serwera fizycznego na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera rozwiązywania problemów dla typowych problemów z replikacją, podczas replikowania maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: c2100ee2388ae09bd309167b1be77f7bdbe32f69
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126021"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją dla maszyn wirtualnych VMware i serwerów fizycznych

Otrzymasz komunikat o błędzie w przypadku ochrony sieci maszyn wirtualnych VMware lub serwerów fizycznych przy użyciu usługi Azure Site Recovery. W tym artykule opisano niektóre typowe problemy, które mogą wystąpić podczas replikowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure za pomocą [usługi Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemy dotyczące replikacji początkowej.

W wielu przypadkach błędy replikacji początkowej, które można napotkać w witrynie pomocy technicznej są spowodowane przez problemy z łącznością między serwerem proces serwera źródłowego lub proces serwera na platformę Azure. W większości przypadków te problemy można rozwiązać, wykonując kroki wymienione poniżej.

### <a name="verify-the-source-machine"></a>Sprawdź maszyna źródłowa
* Z wiersza polecenia komputera serwera źródłowego Użyj Telnet, aby wykonać polecenie ping serwera przetwarzania za pomocą portu https (domyślnie 9443), jak pokazano poniżej, aby zobaczyć, jeśli istnieją problemy z połączeniem sieciowym lub problemy z blokowaniem portów zapory.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Użyj Telnet, nie używaj polecenia PING, aby przetestować łączność.  Jeśli nie zainstalowano programu Telnet, postępuj zgodnie z listy kroków [tutaj](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Jeśli nie można połączyć, należy zezwolić na przychodzący port 9443 na serwerze przetwarzania i sprawdź, jeśli problem nadal istnieje. Było w niej niektórych przypadkach, gdy serwer przetwarzania został za siecią obwodową, która była przyczyną tego problemu.

* Sprawdź stan usługi `InMage Scout VX Agent – Sentinel/OutpostStart` Jeśli nie jest uruchomiona i sprawdź, czy Jeśli problem nadal występuje.   

## <a name="verify-the-process-server"></a>Sprawdź serwer przetwarzania

* **Sprawdź, jeśli serwer przetwarzania jest aktywnie wypychanie danych do platformy Azure**

Z maszyny serwera przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl-Shift-Esc). Przejdź na kartę wydajności, a następnie kliknij link otwórz Monitor zasobów. Z usługi Resource Manager, przejdź do karty sieciowej. Sprawdź, jeśli cbengine.exe "Procesów za pomocą działania sieci" jest aktywnie wysyłania dużej ilości danych (w MB).

![Włączanie replikacji](./media/vmware-azure-troubleshoot-replication/cbengine.png)

W przeciwnym razie wykonaj poniższe kroki:

* **Sprawdź, czy serwer przetwarzania jest możliwość łączenia z usługą Azure Blob**: Wybierz i sprawdź cbengine.exe przeglądania "Połączeń TCP", aby sprawdzić, czy jest łączność z serwerem przetwarzania adresu URL obiektu blob usługi Azure Storage.

![Włączanie replikacji](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Jeśli nie są następnie przejdź do pozycji Panel sterowania > usługi, sprawdź, czy następujące usługi pracę:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re) Uruchom wszystkie usługi, która nie jest uruchomiona i wyboru, jeśli problem nadal występuje.

* **Sprawdź, czy serwer przetwarzania jest możliwe nawiązanie połączenia z programem Azure publiczny adres IP przy użyciu portu 443**

Otwórz najnowsze CBEngineCurr.errlog z `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` i wyszukaj: 443 i połączenia próba nie powiodła się.

![Włączanie replikacji](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Jeśli występują problemy, z serwera przetwarzania wiersza polecenia, użyj telnet na polecenie ping Azure publicznego adresu IP (zamaskowana w powyżej obrazu) znalezione w CBEngineCurr.currLog przy użyciu portu 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Jeśli nie można się połączyć, sprawdź, czy problem dostępu z powodu zapory lub serwera Proxy, zgodnie z opisem w następnym kroku.


* **Sprawdź, czy zapora oparta na adres IP na serwerze przetwarzania nie blokuje dostępu**: Jeśli używane są reguły zapory oparte na adresie IP na serwerze, Pobierz pełną listę Microsoft Azure zakresów IP centrum danych z [tutaj](https://www.microsoft.com/download/details.aspx?id=41653) i dodać je do konfiguracji zapory, aby zapewnić zezwalały na komunikację do systemu Azure (i portu 443 protokołu HTTPS).  Zezwól na użycie zakresów adresów IP dla regionu platformy Azure Twojej subskrypcji oraz regionu Zachodnie stany USA (służy do kontrolowania dostępu i zarządzania tożsamościami).

* **Sprawdź, czy opartego na adresach URL zapory na serwerze przetwarzania nie blokuje dostępu**: Jeśli używasz reguł zapory opartego na adresach URL na serwerze, upewnij się, następujące adresy URL są dodawane do konfiguracji zapory.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Sprawdź, jeśli ustawienia serwera Proxy na serwerze przetwarzania nie blokują dostęp**.  Jeśli używasz serwera Proxy, upewnij się, że nazwa serwera proxy jest rozpoznawana przez serwer DNS.
Aby sprawdzić, jakie zostały podane podczas instalacji serwera konfiguracji. Przejdź do klucza rejestru

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Teraz upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do przesyłania danych.
Kopia zapasowa Microsoft Azure Search

Otwórz go i kliknij akcję > Zmień właściwości. Na karcie Konfiguracja serwera Proxy powinien zostać wyświetlony adres serwera proxy, która powinna być taka sama, jak to przedstawiono ustawienia rejestru. W przeciwnym razie zmień ją na ten sam adres.


* **Sprawdź, jeśli ograniczania przepustowości nie jest ograniczona na serwerze przetwarzania**: zwiększyć przepustowość i sprawdź, czy problem nadal istnieje.

## <a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy, a następnie publikują zapytania [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywną społeczność i jeden z naszych inżynierów będzie mógł pomóc.
