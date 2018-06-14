---
title: Rozwiązywanie problemów z błędami ochrony VMware/fizycznych do platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano typowe błędy replikacji maszyny VMware i sposoby ich rozwiązywania
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: asgang
ms.openlocfilehash: 9e0c602646009b20c8d4f8a29d55b7f44a089131
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768494"
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Rozwiązywanie problemów replikacji serwera VMware/fizyczne lokalnej
W trakcie ochronę sieci maszyn wirtualnych VMware lub serwerów fizycznych za pomocą usługi Azure Site Recovery może zostać wyświetlony komunikat o błędzie. Ten artykuł zawiera szczegóły dotyczące niektórych bardziej typowe komunikaty o błędach napotkano wraz z czynności pozwalające rozwiązać je.


## <a name="initial-replication-is-stuck-at-0"></a>Replikacja początkowa jest zablokowany na 0%
Większość błędów jest Replikacja początkowa napotykane w witrynie pomocy technicznej są spowodowane problemami z łącznością między serwerem proces serwera źródłowego lub serwera Azure procesu.
W większości przypadków można rozwiązać te problemy, wykonując kroki opisane poniżej.

###<a name="check-the-following-on-source-machine"></a>Sprawdź, czy na MASZYNIE ŹRÓDŁOWEJ
* Z wiersza polecenia komputera serwera źródłowego należy użyć Telnet na polecenie ping serwera przetwarzania z portem https (domyślnie 9443), jak pokazano poniżej, aby zobaczyć, czy istnieją problemy z połączeniem sieciowym lub problemy z blokowaniem portów zapory.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Użyj Telnet, nie za pomocą polecenia PING do testowania łączności.  Jeśli nie zainstalowano programu Telnet, wykonaj listę kroków [tutaj](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Jeśli nie można połączyć, Zezwalaj na port wejściowy 9443 na serwerze przetwarzania i sprawdź, czy problem nadal kończy działanie. Było w niej niektórych przypadkach, gdy serwer przetwarzania został za siecią obwodową, która była przyczyną tego problemu.

* Sprawdź stan usługi `InMage Scout VX Agent – Sentinel/OutpostStart` Jeśli nie jest uruchomiona i sprawdź, czy problem nadal występuje.   

###<a name="check-the-following-on-process-server"></a>Sprawdź, czy na serwerze przetwarzania

* **Sprawdź, czy serwer przetwarzania jest aktywnie przekazywanie danych do platformy Azure**

Z komputera serwer przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl-Shift-Esc). Przejdź do karty wyników, a następnie kliknij łącze otwieranie monitora zasobów. Z Menedżera zasobów, przejdź do karty sieciowej. Sprawdź, czy cbengine.exe "Procesów z działaniem sieci" aktywnie wysyła dużej ilości danych (w MB).

![Włączanie replikacji](./media/site-recovery-protection-common-errors/cbengine.png)

Jeśli nie, wykonaj następujące kroki:

* **Sprawdź, czy serwer przetwarzania jest możliwość łączenia z obiektów Blob platformy Azure**: zaznacz i sprawdź cbengine.exe przeglądania "Połączeń TCP" czy jest łączność między serwerem przetwarzania adresu URL obiektu blob magazynu Azure.

![Włączanie replikacji](./media/site-recovery-protection-common-errors/rmonitor.png)

Jeśli nie są następnie przejdź do pozycji Panel sterowania > usługi, sprawdź, czy następujące usługi są uruchomione i działają prawidłowo:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Ponownego) Uruchom dowolnej usługi, która nie jest uruchomiona i sprawdź, czy problem nadal występuje.

* **Sprawdź, czy serwer przetwarzania jest można nawiązać połączenia z programem Azure publiczny adres IP przy użyciu portu 443**

Otwórz najnowszą CBEngineCurr.errlog z `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` i wyszukaj: 443 i połączenia próba nie powiodła się.

![Włączanie replikacji](./media/site-recovery-protection-common-errors/logdetails1.png)

Jeśli występują problemy, z serwera przetwarzania wiersza polecenia, użyj telnet na polecenie ping Azure publicznego adresu IP (maskowania w powyżej obrazu) w CBEngineCurr.currLog przy użyciu portu 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Jeśli nie możesz się połączyć, sprawdź, czy problem dostępu występuje z powodu zapory lub serwera Proxy, zgodnie z opisem w następnym kroku.


* **Sprawdź, czy zapora oparta na adres IP na serwerze przetwarzania nie blokuje dostępu**: Jeśli używasz reguły zapory oparte na adresie IP na serwerze, Pobierz pełną listę Microsoft Azure zakresów IP centrum danych z [tutaj](https://www.microsoft.com/download/details.aspx?id=41653) i dodaj je do konfiguracji zapory w celu zapewnienia ich zezwalają na komunikację z platformy Azure (i portu HTTPS (port 443)).  Zezwól na użycie zakresów adresów IP dla regionu platformy Azure Twojej subskrypcji oraz regionu Zachodnie stany USA (służy do kontrolowania dostępu i zarządzania tożsamościami).

* **Sprawdź, czy zapora oparta na adres URL, na serwerze przetwarzania nie blokuje dostępu**: Jeśli używasz reguły zapory oparte na adresach URL na serwerze, upewnij się, następujące adresy URL są dodawane do konfiguracji zapory.

  `*.accesscontrol.windows.net:` służy do kontrolowania dostępu i zarządzania tożsamościami

  `*.backup.windowsazure.com:` służy do transferowania i organizowania danych replikacji

  `*.blob.core.windows.net:` Używane do uzyskiwania dostępu do konta magazynu, czy magazyny zreplikowanych danych

  `*.hypervrecoverymanager.windowsazure.com:` służy do wykonywania operacji i organizowania zarządzania replikacją

  `time.nist.gov` i `time.windows.com`: użytych do sprawdzenia synchronizacja czasu między systemem a czasem globalnym.

Adresy URL **chmury Azure dla instytucji rządowych**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Sprawdź, czy ustawienia serwera Proxy na serwerze przetwarzania są nie blokuje dostępu**.  Jeśli używasz serwera Proxy, upewnij się, że nazwa serwera proxy jest rozpoznawana przez serwer DNS.
Aby sprawdzić, jakie zostały podane w czasie instalacji serwera konfiguracji. Przejdź do klucza rejestru

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Teraz upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do przesyłania danych.
Kopia zapasowa Microsoft Azure Search

![Włączanie replikacji](./media/site-recovery-protection-common-errors/mab.png)

Otwórz go i kliknij akcję > Zmień właściwości. Na karcie Konfiguracja serwera Proxy powinien zostać wyświetlony adres serwera proxy, która powinna być taka sama, jak to przedstawiono ustawienia rejestru. Jeśli nie, zmień ją na ten sam adres.

![Włączanie replikacji](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Sprawdź, czy ograniczania przepustowości nie jest ograniczane na serwerze przetwarzania**: zwiększyć przepustowość i sprawdź, czy problem nadal występuje.

##<a name="next-steps"></a>Kolejne kroki
Jeśli potrzebujesz więcej pomocy, opublikuj wpis kwerendy do [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywną społeczność i jeden z naszych inżynierów będzie można pomóc.
