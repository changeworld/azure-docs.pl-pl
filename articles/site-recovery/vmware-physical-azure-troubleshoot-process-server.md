---
title: Rozwiązywanie problemów z serwerem przetwarzania usługi Azure Site Recovery
description: W tym artykule opisano sposób rozwiązywania problemów z serwerem przetwarzania usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969365"
---
# <a name="troubleshoot-the-process-server"></a>Rozwiązywanie problemów z serwera przetwarzania

[Site Recovery](site-recovery-overview.md) serwer przetwarzania jest używana podczas konfigurowania odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych z programu VMware i serwerów fizycznych. W tym artykule opisano sposób rozwiązywania problemów z serwera przetwarzania, w tym problemy dotyczące replikacji i łączności.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) o serwerze przetwarzania.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem rozwiązywania problemów:

1. Upewnij się, że rozumiesz sposób [monitorowanie serwerów przetwarzania](vmware-physical-azure-monitor-process-server.md).
2. Przejrzyj poniższe najlepsze rozwiązania.
3. Upewnij się, że wykonano [zagadnienia dotyczące wydajności](site-recovery-plan-capacity-vmware.md#capacity-considerations)i użyj rozmiarów wskazówki dotyczące [serwera konfiguracji](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) lub [autonomicznych serwerów przetwarzania](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Najlepsze rozwiązania dotyczące wdrażania serwera przetwarzania

Aby uzyskać optymalną wydajność serwerów przetwarzania firma Microsoft został podsumowane szereg ogólne najlepsze rozwiązania.

**Najlepszym rozwiązaniem jest** | **Szczegóły**
--- |---
**Użycie** | Upewnij się, serwer przetwarzania serwera/autonomicznej konfiguracji są używane tylko do zamierzonego celu. Nie uruchamiaj dowolne inne na maszynie.
**Adres IP** | Upewnij się, że serwer przetwarzania ma statyczny adres IPv4 i nie ma skonfigurowany Translator adresów Sieciowych.
**Kontrola użycia pamięci/procesora CPU** |Zachowaj wykorzystanie Procesora i pamięci w obszarze 70%.
**Upewnij się, ilość wolnego miejsca** | Wolne miejsce odnosi się do miejsca na dysku pamięci podręcznej na serwerze przetwarzania. Dane replikacji są przechowywane w pamięci podręcznej, przed przesłaniem do platformy Azure.<br/><br/> Zachowaj wolne miejsce powyżej 25%. Jeśli go nie spadnie poniżej 20%, replikacja jest ograniczany replikowanych maszyn, które są skojarzone z serwerem przetwarzania.

## <a name="check-process-server-health"></a>Sprawdź kondycję serwera przetwarzania

Rozwiązywanie problemów z pierwszym krokiem jest Sprawdź kondycję i stan serwera przetwarzania. Aby to zrobić, należy przejrzeć wszystkie alerty, sprawdź, czy wymagane usługi są uruchomione i sprawdź, czy jest pulsu z serwera przetwarzania. Te kroki są podsumowywane na poniższym rysunku, następuje procedur, które ułatwiają wykonywanie czynności.

![Rozwiązywanie problemów z kondycją serwera przetwarzania](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1: Rozwiązywanie alertów dotyczących kondycji serwera przetwarzania

Serwer przetwarzania generuje liczbę alertów dotyczących kondycji. Te alerty i zalecane akcje są podsumowane w poniższej tabeli.

**Typ alertu** | **Error** | **Rozwiązywanie problemów**
--- | --- | --- 
![W dobrej kondycji][green] | Brak  | Serwer przetwarzania jest połączony i działa prawidłowo.
![Ostrzeżenie][yellow] | Określonych usług nie są uruchomione. | 1. Sprawdź, czy usługi są uruchomione.<br/> 2. Jeśli usługi są uruchomione zgodnie z oczekiwaniami, postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).
![Ostrzeżenie][yellow]  | Wykorzystanie procesora CPU > 80%, dla ostatnich 15 minut. | 1. Nie należy dodawać nowych maszyn.<br/>2. Upewnij się, że liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównywany do [określone limity](site-recovery-plan-capacity-vmware.md#capacity-considerations)i warto rozważyć skonfigurowanie [dodatkowym serwerze przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).
![Krytyczny][red] |  Wykorzystanie procesora CPU > 95%, dla ostatnich 15 minut. | 1. Nie należy dodawać nowych maszyn.<br/>2. Upewnij się, że liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównywany do [określone limity](site-recovery-plan-capacity-vmware.md#capacity-considerations)i warto rozważyć skonfigurowanie [dodatkowym serwerze przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](https://aka.ms/asr-v2a-deployment-planner) replikacji serwera fizycznego/VMware.
![Ostrzeżenie][yellow] | Użycie pamięci > 80%, dla ostatnich 15 minut. |  1. Nie należy dodawać nowych maszyn.<br/>2. Upewnij się, że liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównywany do [określone limity](site-recovery-plan-capacity-vmware.md#capacity-considerations)i warto rozważyć skonfigurowanie [dodatkowym serwerze przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>3. Postępuj zgodnie z instrukcjami skojarzone z ostrzeżeniem.<br/> 4. Jeśli problem będzie się powtarzać, postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).
![Krytyczny][red] | Użycie pamięci > 95%, dla ostatnich 15 minut. | 1. Nie należy dodawać nowych maszyn i biorąc pod uwagę konfigurowania [dodatkowym serwerze przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/> 2. Postępuj zgodnie z instrukcjami skojarzone z ostrzeżeniem.<br/> 3. 4. Jeśli problem będzie się powtarzać, postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](https://aka.ms/asr-v2a-deployment-planner) dla problemów dotyczących replikacji serwera fizycznego/VMware.
![Ostrzeżenie][yellow] | Pamięć podręczna folderu wolnego miejsca < 30% w przypadku ostatnich 15 minut. | 1. Nie Dodawanie nowych maszyn i warto rozważyć skonfigurowanie [dodatkowym serwerze przetwarzania](vmware-azure-set-up-process-server-scale.md).<br/>2. Upewnij się, że liczba maszyn wirtualnych używających serwera przetwarzania jest wyrównywany do [wytycznych](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).
![Krytyczny][red] |  Wolne miejsce < 25% w ciągu ostatnich 15 minut | 1. Wykonaj instrukcje związane z ostrzeżeniem tego problemu.<br/> 2. 3. Postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).<br/> 3. Jeśli problem będzie się powtarzać, uruchom [planista wdrażania](https://aka.ms/asr-v2a-deployment-planner) replikacji serwera fizycznego/VMware.
![Krytyczny][red] | Brak pulsu z serwera przetwarzania, przez co najmniej 15 minut. Usługa tmansvs nie łączy się z serwerem konfiguracji. | (1) upewnij się, że serwer przetwarzania jest uruchomiona.<br/> 2. Upewnij się, że tmassvc jest uruchomiona na serwerze przetwarzania.<br/> 3. Postępuj zgodnie z instrukcjami poniżej, aby [Rozwiązywanie problemów z łącznością i replikacji](#check-connectivity-and-replication).


![Klucz tabeli](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2: Sprawdź usługi serwera przetwarzania

Usługi, powinna być uruchomiona na serwerze przetwarzania, które są podsumowane w poniższej tabeli. Istnieją drobne różnice w usługach, w zależności od tego, jak serwer przetwarzania jest wdrażany. 

Dla wszystkich usług z wyjątkiem agenta usług odzyskiwania Microsoft Azure (obengine), sprawdź, czy jest równa wartości StartType **automatyczne** lub **automatycznie (opóźnione uruchomienie)** .
 
**Wdrożenie** | **Uruchamianie usług**
--- | ---
**Serwer przetwarzania na serwerze konfiguracji** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload); Usługa aplikacji InMage Scout; Agent usług odzyskiwania Microsoft Azure (obengine); Agent InMage Scout VX Agent — Sentinel/Outpost (svagents); tmansvc; World Wide Web Publishing (W3SVC); usługa MySQL; Usługa Microsoft Azure Site Recovery (dra)
**Serwer przetwarzania działający jako serwer autonomiczny** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload); Usługa aplikacji InMage Scout; Agent usług odzyskiwania Microsoft Azure (obengine); Agent InMage Scout VX Agent — Sentinel/Outpost (svagents); tmansvc.
**Serwer przetwarzania wdrożonych na platformie Azure na potrzeby powrotu po awarii** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3: Sprawdzenie pulsu serwera przetwarzania

W przypadku brak pulsu z serwera przetwarzania (kod błędu: 806), wykonaj następujące czynności:

1. Sprawdź, czy serwer przetwarzania, maszyna wirtualna jest uruchomiona.
2. Sprawdź dzienniki błędów.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Sprawdź łączność i replikacji

 Niepowodzenia replikacji początkowej i bieżących są często spowodowane przez problemy z łącznością między maszyn źródłowych i serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. Te kroki są podsumowywane na poniższym rysunku, następuje procedur, które ułatwiają wykonywanie czynności.

![Rozwiązywanie problemów z łącznością i replikacji](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4: Sprawdź synchronizację czasu na maszynie źródłowej

Upewnij się, że system daty/godziny dla replikowanej maszyny, jest synchronizowany. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5. Sprawdź oprogramowanie antywirusowe na maszynie źródłowej

Sprawdź, czy nie oprogramowanie antywirusowe na replikowanej maszynie blokuje Site Recovery. Jeśli musisz wykluczyć Site Recovery na programy antywirusowe, zapoznaj się z [w tym artykule](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6: Sprawdź łączność między komputerem źródłowym


1. Zainstaluj [klient Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na maszynie źródłowej, jeśli potrzebujesz. Nie używaj polecenia Ping.
2. Z maszyny źródłowej wysłać polecenie ping z serwera przetwarzania na porcie HTTPS przy użyciu programu Telnet. Domyślnie 9443 jest portem HTTPS dla ruchu związanego z replikacją.

    `telnet <process server IP address> <port>`

3. Sprawdź, czy połączenie zostanie nawiązane.


**Łączność** | **Szczegóły** | **Akcja**
--- | --- | ---
**Pomyślne** | Telnet pokazuje pusty ekran, a serwer przetwarzania jest dostępny. | Wymagane żadne dodatkowe działania.
**Powiodło się** | Nie możesz się połączyć | Upewnij się, że przychodzący port 9443 jest dozwolone na serwerze przetwarzania. Na przykład, jeśli masz sieci obwodowej lub podsiecią ekranowaną. Ponownie sprawdź łączność.
**Częściowo udane** | Można połączyć, ale maszyna źródłowa raporty, czy serwer przetwarzania nie można połączyć. | Przejdź do następnej procedury rozwiązywania problemów.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7: Rozwiązywanie problemów z serwerem przetwarzania jest nieosiągalny

Jeśli serwer przetwarzania nie jest dostępny z maszyny źródłowej, zostanie wyświetlony błąd 78186. Jeśli nie zostanie rozwiązany, ten problem doprowadzi do obu spójny na poziomie aplikacji i punkty odzyskiwania spójne na poziomie awarii, nie są generowane, zgodnie z oczekiwaniami.

Rozwiązywanie problemów, sprawdzając, czy maszyna źródłowa może osiągnąć adres IP serwera przetwarzania i uruchom narzędzie cxpsclient na maszynie źródłowej, aby sprawdzić połączenie end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Sprawdź połączenie pakietu Integracyjnego na serwerze przetwarzania

Jeśli telnet zakończy się pomyślnie, ale maszyna źródłowa raporty, czy serwer przetwarzania nie można połączyć, sprawdź, czy można uzyskać dostęp do adresu IP serwera przetwarzania.

1. W przeglądarce sieci web, próbują uzyskać dostęp do https://<PS_IP>:<PS_Data_Port>/ adresu IP.
2. Jeżeli to sprawdzenie pokazuje błąd certyfikatu protokołu HTTPS, który jest normalnie. Jeśli ten błąd można zignorować, powinien zostać wyświetlony 400 — Nieprawidłowe żądanie. Oznacza to, że serwer nie może obsługiwać żądania przeglądarki i wystarcza standardowego połączenia HTTPS.
3. Jeżeli to sprawdzenie nie rozwiąże problemu, należy zauważyć komunikat o błędzie przeglądarki. Na przykład błąd 407 będzie wskazywać problem z uwierzytelniania serwera proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Sprawdź połączenie z cxpsclient

Ponadto możesz uruchomić narzędzie cxpsclient, aby sprawdzić połączenie end-to-end.

1. Uruchom narzędzie w następujący sposób:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na serwerze przetwarzania Sprawdź dzienniki wygenerowane w tych folderach:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Sprawdź, czy źródło dzienników maszyny Wirtualnej dla przekazywania błędów (error 78028)

Problem z zablokowane z maszyn źródłowych do procesu usługi przekazywania może spowodować oba punkty odzyskiwania spójne na poziomie awarii i spójny na poziomie aplikacji, które nie są generowane. 

1. Aby rozwiązać błędy przekazywania sieci, można wyszukać błędy, w tym dzienniku:

    C:\Program pliki (x86) \Microsoft Azure Site Recovery\agent\svagents*.log 

2. Pozostała część procedury przedstawione w tym artykule pozwalają można rozwiązywać problemy z przekazywania danych.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8: Sprawdź, czy serwer przetwarzania jest wypychanie danych

Sprawdź, czy serwer przetwarzania jest aktywnie wypychanie danych do platformy Azure.

  1. Na serwerze przetwarzania Otwórz Menedżera zadań (naciśnij klawisze Ctrl + Shift + Esc).
  2. Wybierz **wydajności** kartę > **otwórz Monitor zasobów**.
  3. W **Monitor zasobów** wybierz opcję **sieci** kartę. W obszarze **procesów przy użyciu działań sieciowych**, sprawdź, czy cbengine.exe aktywnie wysyła duże vNotolume danych.

       ![Woluminy w ramach procesów przy użyciu działań sieciowych](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Jeśli cbengine.exe nie jest wysyłany dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9: Sprawdź połączenie z serwerem przetwarzania do magazynu obiektów blob platformy Azure

1. Monitor zasobów wybierz **cbengine.exe**.
2. W obszarze **połączeń TCP**, sprawdź, czy istnieje połączenie z serwerem przetwarzania do magazynu platformy Azure.

  ![Łączność między cbengine.exe i adres URL magazynu obiektów Blob platformy Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Sprawdź usługi

Jeśli nie ma łączności z serwerem przetwarzania na adres URL magazynu obiektów blob platformy Azure, sprawdź, czy usługi są uruchomione.

1. W Panelu sterowania wybierz **usług**.
2. Sprawdź, czy uruchomiono następujące usługi:

    - cxprocessserver
    - Agent InMage Scout VX Agent — Sentinel/Outpost
    - Agent usług Microsoft Azure Recovery Services
    - Usługa Microsoft Azure Site Recovery
    - tmansvc

3. Uruchom lub uruchom ponownie wszystkie usługi, która nie jest uruchomiona.
4. Sprawdź, czy serwer przetwarzania jest połączony i osiągalny. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10: Sprawdź połączenie z serwerem przetwarzania Azure publiczny adres IP

1. Na serwerze przetwarzania w **%programfiles%\Microsoft Agent\Temp usług odzyskiwania Azure**, otwórz najnowszy plik CBEngineCurr.errlog.
2. W pliku, wyszukiwanie **443**, lub dla ciągu **próba połączenia nie powiodło się**.

  ![Dzienniki błędów w folderze Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Jeśli widzisz problemów, na terenie Azure publicznego adresu IP pliku CBEngineCurr.currLog przy użyciu portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. W wierszu polecenia na serwerze przetwarzania Użyj Telnet, aby wykonać polecenie ping Azure publicznego adresu IP.
6. Jeśli nie możesz się połączyć, postępuj zgodnie z następnej procedury.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11: Sprawdź ustawienia zapory serwera przetwarzania. 

Sprawdź, czy Zapora oparte na adresie IP na serwerze przetwarzania blokuje dostęp.

1. Dla reguły zapory oparte na adresie IP:

    ), Pobierz pełną listę [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    (b) Dodaj zakresy adresów IP w konfiguracji zapory, aby upewnić się, że zapory umożliwia komunikację na platformie Azure (i domyślnym portem HTTPS 443).

    c) dozwolonych zakresów adresów IP dla regionu platformy Azure Twojej subskrypcji i regionu platformy Azure zachodnie stany USA (używane do kontrolowania dostępu i tożsamości zarządzania).

2. W przypadku opartego na adresach URL zapory należy dodać adresów URL wymienionych w poniższej tabeli, aby konfiguracja zapory.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12: Sprawdź ustawienia serwera proxy serwera przetwarzania 

1. Jeśli używasz serwera proxy, upewnij się, że nazwa serwera proxy jest rozpoznany przez serwer DNS. Sprawdź wartości, podane podczas konfigurowania serwera konfiguracji w kluczu rejestru **Recovery\ProxySettings witryny HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure**.
2. Upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do przesyłania danych.

    ) wyszukiwanie **kopia zapasowa Microsoft Azure**.

    (b) otwórz **kopia zapasowa Microsoft Azure**i wybierz **akcji** > **Zmień właściwości**.

    c) na **konfigurację serwera Proxy** kartę, adres serwera proxy musi być taka sama jak adres serwera proxy, który jest wyświetlany w ustawieniach rejestru. W przeciwnym razie zmień go na ten sam adres.

## <a name="step-13-check-bandwidth"></a>Krok 13 Sprawdzanie przepustowości

Zwiększyć przepustowość między serwerem przetwarzania a platformą Azure, a następnie sprawdź, czy problem nadal występuje.


## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie w [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png