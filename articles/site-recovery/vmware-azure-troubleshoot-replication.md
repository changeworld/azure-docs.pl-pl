---
title: Rozwiązywanie problemów z replikacją na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dla typowych problemów z replikacją podczas odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063682"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Rozwiązywanie problemów z replikacją dla maszyn wirtualnych VMware i serwerów fizycznych

Za pomocą usługi Azure Site Recovery w przypadku ochrony sieci maszyn wirtualnych VMware lub serwerów fizycznych, może pojawić się komunikat błędu. W tym artykule opisano niektóre typowe problemy, które można napotkać podczas replikowania lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych na platformę Azure za pomocą [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemy dotyczące replikacji początkowej

Błędy replikacji początkowej jest często spowodowane przez problemy z łącznością między serwerem źródłowym i serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. W większości przypadków te problemy można rozwiązać, wykonując czynności opisane w poniższych sekcjach.

### <a name="check-the-source-machine"></a>Sprawdź maszyna źródłowa

Poniższej liście przedstawiono sposoby można sprawdzić na maszynie źródłowej:

*  W wierszu polecenia na serwerze źródłowym użyj Telnet, aby wykonać polecenie ping do serwera przetwarzania za pośrednictwem portu HTTPS (9443 jest domyślnym portem HTTPS), uruchamiając następujące polecenie. Polecenie sprawdza, czy problemy z połączeniem sieciowym i problemy z tego bloku port zapory.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Użyj Telnet, aby przetestować łączność. Nie używaj `ping`. Jeśli Telnet nie jest zainstalowany, wykonaj czynności opisane w [zainstalować klienta usługi Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Jeśli nie można nawiązać połączenia serwera przetwarzania, zezwala na przychodzący port 9443 na serwerze przetwarzania. Na przykład może być konieczne zezwolić na przychodzący port 9443 na serwerze przetwarzania, jeśli sieć ma sieć obwodową lub podsiecią ekranowaną. Następnie sprawdź, czy problem nadal występuje.

*  Sprawdź stan **InMage Scout VX Agent — Sentinel/OutpostStart** usługi. Jeśli usługa nie jest uruchomiona, uruchom usługę, a następnie sprawdź, czy problem nadal występuje.   

### <a name="check-the-process-server"></a>Sprawdź serwer przetwarzania

Poniższej liście przedstawiono sposoby można sprawdzić na serwerze przetwarzania:

*  **Sprawdź, czy serwer przetwarzania jest aktywnie wypychanie danych do platformy Azure**.

   1. Na serwerze przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl + Shift + Esc).
   2. Wybierz **wydajności** , a następnie wybierz pozycję **otwórz Monitor zasobów** łącza. 
   3. Na **Monitor zasobów** wybierz opcję **sieci** kartę. W obszarze **procesów przy użyciu działań sieciowych**, sprawdź, czy **cbengine.exe** aktywnie wysyła duże ilości danych.

        ![Zrzut ekranu pokazujący woluminy w ramach procesów przy użyciu działań sieciowych](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Jeśli cbengine.exe nie jest wysyłany dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

*  **Sprawdź, czy serwer procesu można połączyć z usługą Azure Blob storage**.

   Wybierz **cbengine.exe**. W obszarze **połączeń TCP**, sprawdź, czy jest łączność z serwerem przetwarzania na adres URL magazynu Blog dotyczący platformy Azure.

   ![Zrzut ekranu pokazujący łączność między cbengine.exe i adres URL magazynu obiektów Blob platformy Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Jeśli nie ma łączności z serwerem przetwarzania na blogu dotyczącym platformy Azure storage, adres URL w Panelu sterowania wybierz **usług**. Sprawdź, czy są uruchomione następujące usługi:

   *  cxprocessserver
   *  Agent InMage Scout VX Agent — Sentinel/Outpost
   *  Agent usług Microsoft Azure Recovery Services
   *  Usługa Microsoft Azure Site Recovery
   *  tmansvc

   Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona. Sprawdź, czy problem nadal występuje.

*  **Sprawdź, czy serwer przetwarzania może łączyć się z platformy Azure, publiczny adres IP przy użyciu portu 443**.

   W folderze %programfiles%\Microsoft Agent\Temp usług odzyskiwania Azure Otwórz najnowszy plik CBEngineCurr.errlog. W pliku, wyszukiwanie **443** lub ciągu **próba połączenia nie powiodło się**.

   ![Zrzut ekranu pokazujący błąd rejestruje się w folderze Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Jeśli problemy zostaną wyświetlone w wierszu polecenia na serwerze przetwarzania, użyj Telnet, aby wykonać polecenie ping Azure publicznego adresu IP (adres IP jest zamaskowana w powyższej ilustracji). Usługi platformy Azure, publiczny adres IP można znaleźć w pliku CBEngineCurr.currLog przy użyciu portu 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Jeśli nie możesz się połączyć, sprawdź, czy jest problem z dostępem do z powodu ustawień zapory lub serwera proxy, zgodnie z opisem w następnym kroku.

*  **Sprawdź, czy zapora oparta na adres IP na serwerze przetwarzania blokuje dostęp**.

   Jeśli używasz reguły zapory oparte na adresie IP na serwerze, Pobierz pełną listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dodaj zakresy adresów IP na konfigurację zapory, aby upewnić się, że zapory umożliwia komunikację na platformie Azure (i domyślnym portem HTTPS 443). Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji i regionu platformy Azure zachodnie stany USA (używane do kontrolowania dostępu i tożsamości zarządzania).

*  **Sprawdź, czy Zapora opartego na adresach URL na serwerze przetwarzania blokuje dostęp**.

   Jeśli używasz regułę zapory na podstawie adresu URL na serwerze, należy dodać adresów URL wymienionych w poniższej tabeli, aby konfiguracja zapory:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Sprawdź, czy ustawienia serwera proxy na serwerze przetwarzania blokowana**.

   Jeśli używasz serwera proxy, upewnij się, że nazwa serwera proxy jest rozpoznany przez serwer DNS. Aby sprawdzić wartości, podane podczas konfigurowania serwera konfiguracji, przejdź do klucza rejestru **Recovery\ProxySettings witryny HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure**.

   Następnie upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do przesyłania danych: 
      
   1. Wyszukaj **kopia zapasowa Microsoft Azure**. 
   2. Otwórz **kopia zapasowa Microsoft Azure**, a następnie wybierz pozycję **akcji** > **Zmień właściwości**. 
   3. Na **konfigurację serwera Proxy** kartę, powinien zostać wyświetlony adres serwera proxy. Adres serwera proxy powinien być taki sam jak adres serwera proxy, który jest wyświetlany w ustawieniach rejestru. W przeciwnym razie zmień go na ten sam adres.

*  **Sprawdź, czy ograniczania przepustowości jest ograniczona na serwerze przetwarzania**.

   Zwiększyć przepustowość, a następnie sprawdź, czy problem nadal występuje.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Maszyna źródłowa nie ma na liście w witrynie Azure portal

Podczas próby wybierz maszyny źródłowej, aby włączyć replikację przy użyciu usługi Site Recovery maszyny może nie być dostępne dla jednego z następujących powodów:

*  Jeśli dwie maszyny wirtualne w ramach programu vCenter to samo wystąpienie identyfikatora UUID, pierwszej maszyny wirtualnej, wykrytych przez serwer konfiguracji jest wyświetlany w witrynie Azure portal. Aby rozwiązać ten problem, upewnij się, że nie dwie maszyny wirtualne mają tego samego wystąpienia identyfikatora UUID.
*  Nie zapomnij dodać poświadczenia poprawne vCenter, po skonfigurowaniu serwera konfiguracji za pomocą szablonu pakietu OVF lub ujednoliconej konfiguracji. Aby zweryfikować poświadczenia, które dodałeś podczas instalacji, zobacz [zmodyfikowania poświadczeń do automatycznego odnajdowania](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
*  Jeśli uprawnienia podany dostępu vCenter do nie ma wymaganych uprawnień, może wystąpić błąd, aby odnaleźć maszyny wirtualne. Upewnij się, że uprawnienia opisane w [przygotowywanie konta do automatycznego odnajdowania](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) są dodawane do konta użytkownika vCenter.
*  Jeśli maszyna wirtualna jest już chroniony przez usługi Site Recovery, maszyna wirtualna nie jest dostępny dla wybranych do ochrony w portalu. Upewnij się, czy maszyny wirtualnej, którego szukasz w portalu nie jest już chroniony przez żadnego innego użytkownika lub innej subskrypcji.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>Chronione maszyny wirtualne nie są dostępne w portalu

Maszyny wirtualne, które są replikowane w ramach odzyskiwania lokacji nie są dostępne w witrynie Azure portal, jeśli istnieją zduplikowane wpisy w systemie. Aby dowiedzieć się, jak usunąć przestarzałych wpisów i rozwiązać te problemy, zobacz [usługi Azure Site Recovery VMware na platformę Azure: Jak wyczyścić zduplikowane lub nieaktualne wpisy](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Mamy aktywnej społeczności użytkowników, a jeden z naszych inżynierów może być pomocny.
