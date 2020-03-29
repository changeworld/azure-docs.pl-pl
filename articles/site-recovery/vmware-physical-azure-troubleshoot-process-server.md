---
title: Rozwiązywanie problemów z serwerem przetwarzania odzyskiwania witryny platformy Azure
description: W tym artykule opisano sposób rozwiązywania problemów z serwerem procesów usługi Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256863"
---
# <a name="troubleshoot-the-process-server"></a>Rozwiązywanie problemów z serwerem przetwarzania

Serwer [procesów odzyskiwania lokacji](site-recovery-overview.md) jest używany podczas konfigurowania odzyskiwania po awarii na platformie Azure dla lokalnych maszyn wirtualnych VMware i serwerów fizycznych. W tym artykule opisano sposób rozwiązywania problemów z serwerem przetwarzania, w tym problemów z replikacją i łącznością.

[Dowiedz się więcej](vmware-physical-azure-config-process-server-overview.md) o serwerze przetwarzania.

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed rozpoczęciem rozwiązywania problemów:

1. Upewnij się, że rozumiesz, jak [monitorować serwery procesów](vmware-physical-azure-monitor-process-server.md).
2. Zapoznaj się z poniższymi najlepszymi rozwiązaniami.
3. Upewnij się, że należy postępować [zgodnie z zagadnieniami dotyczącymi pojemności](site-recovery-plan-capacity-vmware.md#capacity-considerations)i korzystać ze wskazówek dotyczących rozmiaru serwera [konfiguracji](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) lub [autonomicznych serwerów procesowych](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Najważniejsze wskazówki dotyczące wdrażania serwera przetwarzania

Aby uzyskać optymalną wydajność serwerów przetwarzania, podsumowaliśmy szereg ogólnych najlepszych rozwiązań.

**Najlepsze praktyki** | **Szczegóły**
--- |---
**Użycia** | Upewnij się, że serwer konfiguracji/autonomiczny serwer procesów są używane tylko zgodnie z przeznaczeniem. Nie uruchamiaj niczego innego na komputerze.
**Adres IP** | Upewnij się, że serwer przetwarzania ma statyczny adres IPv4 i nie ma skonfigurowanego translatora adresów sieciowych.
**Sterowanie użyciem pamięci/procesora** |Utrzymuj użycie procesora i pamięci poniżej 70%.
**Zapewnij wolną przestrzeń** | Wolne miejsce odnosi się do miejsca na dysku pamięci podręcznej na serwerze przetwarzania. Dane replikacji są przechowywane w pamięci podręcznej przed przekazaniem na platformę Azure.<br/><br/> Zachowaj wolną przestrzeń powyżej 25%. Jeśli przejdzie poniżej 20%, replikacja jest ograniczana dla zreplikowanych maszyn skojarzonych z serwerem przetwarzania.

## <a name="check-process-server-health"></a>Sprawdzanie kondycji serwera procesów

Pierwszym krokiem w rozwiązywaniu problemów jest sprawdzenie kondycji i stanu serwera przetwarzania. Aby to zrobić, przejrzyj wszystkie alerty, sprawdź, czy są uruchomione wymagane usługi i sprawdź, czy istnieje puls z serwera przetwarzania. Te kroki są podsumowane w poniższej grafice, a następnie procedury ułatwiające wykonanie kroków.

![Rozwiązywanie problemów ze zdrowiem serwera procesów](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Krok 1: Rozwiązywanie problemów z alertami kondycji serwera procesu

Serwer procesów generuje szereg alertów kondycji. Te alerty i zalecane akcje są podsumowane w poniższej tabeli.

**Typ alertu** | **Błąd** | **Rozwiązywanie problemów**
--- | --- | --- 
![W dobrej kondycji][green] | Brak  | Serwer przetwarzania jest podłączony i zdrowy.
![Ostrzeżenie][yellow] | Określone usługi nie są uruchomione. | 1. Sprawdź, czy usługi są uruchomione.<br/> 2. Jeśli usługi działają zgodnie z oczekiwaniami, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją.](#check-connectivity-and-replication)
![Ostrzeżenie][yellow]  | Wykorzystanie procesora > 80% w ciągu ostatnich 15 minut. | 1. Nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych korzystających z serwera przetwarzania jest wyrównana do [zdefiniowanych limitów,](site-recovery-plan-capacity-vmware.md#capacity-considerations)i rozważ skonfigurowanie [dodatkowego serwera przetwarzania.](vmware-azure-set-up-process-server-scale.md)<br/>3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczny][red] |  Wykorzystanie procesora > 95% w ciągu ostatnich 15 minut. | 1. Nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych korzystających z serwera przetwarzania jest wyrównana do [zdefiniowanych limitów,](site-recovery-plan-capacity-vmware.md#capacity-considerations)i rozważ skonfigurowanie [dodatkowego serwera przetwarzania.](vmware-azure-set-up-process-server-scale.md)<br/>3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzał, uruchom [planisję wdrażania](https://aka.ms/asr-v2a-deployment-planner) dla replikacji serwera VMware/fizycznego.
![Ostrzeżenie][yellow] | Użycie pamięci > 80% w ciągu ostatnich 15 minut. |  1. Nie dodawaj nowych maszyn.<br/>2. Sprawdź, czy liczba maszyn wirtualnych korzystających z serwera przetwarzania jest wyrównana do [zdefiniowanych limitów,](site-recovery-plan-capacity-vmware.md#capacity-considerations)i rozważ skonfigurowanie [dodatkowego serwera przetwarzania.](vmware-azure-set-up-process-server-scale.md)<br/>3. Postępuj zgodnie z instrukcjami związanymi z ostrzeżeniem.<br/> 4. Jeśli problem będzie się powtarzał, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczny][red] | Użycie pamięci > 95% w ciągu ostatnich 15 minut. | 1. Nie dodawaj nowych maszyn i rozważasz utworzenie [dodatkowego serwera przetwarzania.](vmware-azure-set-up-process-server-scale.md)<br/> 2. Postępuj zgodnie z instrukcjami związanymi z ostrzeżeniem.<br/> 3. 4. Jeśli problem będzie się powtarzał, postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 4. Jeśli problem będzie się powtarzał, uruchom [planowanie wdrażania](https://aka.ms/asr-v2a-deployment-planner) dla problemów z replikacją serwera VMware/fizycznego serwera.
![Ostrzeżenie][yellow] | Wolne miejsce w folderze pamięci podręcznej < 30% w ciągu ostatnich 15 minut. | 1. Nie dodawaj nowych maszyn i rozważ utworzenie [dodatkowego serwera przetwarzania.](vmware-azure-set-up-process-server-scale.md)<br/>2. Sprawdź, czy liczba maszyn wirtualnych korzystających z serwera przetwarzania jest zgodna z [wytycznymi](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).
![Krytyczny][red] |  Wolne miejsce < 25% przez ostatnie 15 minut | 1. Postępuj zgodnie z instrukcjami związanymi z ostrzeżeniem o tym problemie.<br/> 2. 3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).<br/> 3. Jeśli problem będzie się powtarzał, uruchom [planisję wdrażania](https://aka.ms/asr-v2a-deployment-planner) dla replikacji serwera VMware/fizycznego.
![Krytyczny][red] | Brak pulsu z serwera przetwarzania przez 15 minut lub dłużej. Usługa tmansvs nie komunikuje się z serwerem konfiguracji. | 1) Sprawdź, czy serwer przetwarzania jest uruchomiony.<br/> 2. Sprawdź, czy tmassvc jest uruchomiony na serwerze przetwarzania.<br/> 3. Postępuj zgodnie z poniższymi instrukcjami, aby [rozwiązać problemy z łącznością i replikacją](#check-connectivity-and-replication).


![Klucz tabeli](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Krok 2: Sprawdź usługi serwera przetwarzania

Usługi, które powinny być uruchomione na serwerze przetwarzania są podsumowane w poniższej tabeli. Istnieją niewielkie różnice w usługach, w zależności od sposobu wdrażania serwera przetwarzania. 

W przypadku wszystkich usług z wyjątkiem agenta microsoft azure recovery services (obengine) sprawdź, czy typ StartType jest ustawiony na **Automatyczny** lub **Automatyczny (opóźniony start).**
 
**wdrażania** | **Uruchamianie usług**
--- | ---
**Serwer przetwarzania na serwerze konfiguracji** | Serwer przetwarzania; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload); Usługa aplikacji InMage Scout; Agent usług odzyskiwania platformy Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Posterunek (svagents); tmansvc; Usługa publikowania w sieci World Wide Web (W3SVC); mysql; Usługa odzyskiwania witryny platformy Microsoft Azure (dra)
**Przetwarzanie serwera uruchomionego jako serwer autonomiczny** | Serwer przetwarzania; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload); Usługa aplikacji InMage Scout; Agent usług odzyskiwania platformy Microsoft Azure (obengine); InMage Scout VX Agent-Sentinel/Posterunek (svagents); tmansvc.
**Serwer przetwarzania wdrożony na platformie Azure w celu powrotu po awarii** | Serwer przetwarzania; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Usługa przekazywania dziennika (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Krok 3: Sprawdź pulsu serwera procesu

Jeśli serwer przetwarzania nie ma pulsu (kod błędu 806), wykonaj następujące czynności:

1. Sprawdź, czy maszyna wirtualna serwera przetwarzania jest uruchomiona.
2. Sprawdź te dzienniki pod kątem błędów.

    C:\ProgramData\ASR\home\svsystems\eventmanager*C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Sprawdzanie łączności i replikacji

 Początkowe i trwające błędy replikacji są często spowodowane problemami z łącznością między komputerami źródłowymi a serwerem przetwarzania lub między serwerem przetwarzania a platformą Azure. Te kroki są podsumowane w poniższej grafice, a następnie procedury ułatwiające wykonanie kroków.

![Rozwiązywanie problemów z łącznością i replikacją](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Krok 4: Sprawdź synchronizację czasu na komputerze źródłowym

Upewnij się, że data/godzina systemowa replikowanego urządzenia jest zsynchronizowana. [Dowiedz się więcej](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Krok 5: Sprawdź oprogramowanie antywirusowe na komputerze źródłowym

Sprawdź, czy żadne oprogramowanie antywirusowe na zreplikowanym komputerze nie blokuje odzyskiwania witryny. Jeśli chcesz wykluczyć program Site Recovery z programów antywirusowych, zapoznaj się z [tym artykułem](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Krok 6: Sprawdź łączność z komputera źródłowego


1. Zainstaluj [klienta Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) na komputerze źródłowym, jeśli zajdzie taka potrzeba. Nie używaj pingu.
2. Z komputera źródłowego pingowanie serwera przetwarzania na porcie HTTPS za pomocą usługi Telnet. Domyślnie 9443 jest portem HTTPS dla ruchu replikacji.

    `telnet <process server IP address> <port>`

3. Sprawdź, czy połączenie zakończyło się pomyślnie.


**Łączność** | **Szczegóły** | **Akcja**
--- | --- | ---
**Pomyślne** | Telnet pokazuje pusty ekran, a serwer przetwarzania jest osiągalny. | Dalsze działania nie są wymagane.
**Nieudanych** | Nie można nawiązać połączenia | Upewnij się, że port przychodzący 9443 jest dozwolony na serwerze przetwarzania. Na przykład, jeśli masz sieć obwodową lub podsieć ekranowane. Sprawdź łączność ponownie.
**Częściowo udane** | Można nawiązać połączenie, ale komputer źródłowy zgłasza, że nie można uzyskać dostępu do serwera przetwarzania. | Przejdź do następnej procedury rozwiązywania problemów.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Krok 7: Rozwiązywanie problemów z nieosiągalnym serwerem procesów

Jeśli serwer przetwarzania nie jest osiągalny z komputera źródłowego, zostanie wyświetlony błąd 78186. Jeśli nie zostanie rozwiązany, ten problem doprowadzi do tego, że punkty odzyskiwania spójne z aplikacjami i spójne z awariami nie są generowane zgodnie z oczekiwaniami.

Rozwiązać problem, sprawdzając, czy komputer źródłowy może dotrzeć do adresu IP serwera przetwarzania i uruchomić narzędzie cxpsclient na komputerze źródłowym, aby sprawdzić połączenie end-to-end.


### <a name="check-the-ip-connection-on-the-process-server"></a>Sprawdzanie połączenia IP na serwerze przetwarzania

Jeśli telnet zakończy się pomyślnie, ale komputer źródłowy zgłasza, że nie można uzyskać dostępu do serwera przetwarzania, sprawdź, czy można uzyskać adres IP serwera przetwarzania.

1. W przeglądarce internetowej spróbuj osiągnąć adres IP https://<PS_IP>:<PS_Data_Port>/.
2. Jeśli ten czek pokazuje błąd certyfikatu HTTPS, jest to normalne. Jeśli zignorujesz błąd, powinieneś zobaczyć 400 - Bad Request. Oznacza to, że serwer nie może obsługiwać żądania przeglądarki i że standardowe połączenie HTTPS jest w porządku.
3. Jeśli to sprawdzenie nie działa, zanotuj komunikat o błędzie przeglądarki. Na przykład błąd 407 wskazuje problem z uwierzytelnianiem serwera proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Sprawdź połączenie za pomocą cxpsclient

Ponadto można uruchomić narzędzie cxpsclient, aby sprawdzić połączenie end-to-end.

1. Uruchom narzędzie w następujący sposób:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Na serwerze przetwarzania sprawdź wygenerowane dzienniki w tych folderach:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Sprawdź źródłowe dzienniki maszyn wirtualnych pod kątem błędów przekazywania (błąd 78028)

Problem z przekazywaniem danych zablokowanych z komputerów źródłowych do usługi procesu może spowodować, że punkty odzyskiwania zgodne z awariami i spójne z aplikacjami nie są generowane. 

1. Aby rozwiązać problemy z błędami przekazywania w sieci, można wyszukać błędy w tym dzienniku:

    C:\Pliki programów (x86)\Odzyskiwanie witryny microsoft azure\agent\svagents*.log 

2. Skorzystaj z pozostałych procedur w tym artykule, które pomogą Ci rozwiązać problemy z przekazywaniem danych.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Krok 8: Sprawdź, czy serwer przetwarzania wypycha dane

Sprawdź, czy serwer przetwarzania aktywnie wypycha dane na platformę Azure.

  1. Na serwerze przetwarzania otwórz Menedżera zadań (naciśnij klawisze Ctrl+Shift+Esc).
  2. Wybierz kartę **Wydajność** > **Otwórz Monitor zasobów**.
  3. Na stronie **Monitor zasobów** wybierz kartę **Sieć.** W obszarze **Procesy z aktywnością sieciową**sprawdź, czy plik cbengine.exe aktywnie wysyła dużą ilość danych.

       ![Woluminy w procesach z aktywnością sieciową](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Jeśli plik cbengine.exe nie wysyła dużej ilości danych, wykonaj kroki opisane w poniższych sekcjach.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Krok 9: Sprawdź połączenie serwera przetwarzania z magazynem obiektów blob platformy Azure

1. W obszarze Monitor zasobów wybierz opcję **cbengine.exe**.
2. W obszarze **Połączenia TCP**sprawdź, czy istnieje łączność z serwerem przetwarzania z magazynem platformy Azure.

  ![Łączność między cbengine.exe a adresem URL magazynu obiektów Blob platformy Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Sprawdź usługi

Jeśli nie ma łączności z serwera przetwarzania do adresu URL magazynu obiektów blob platformy Azure, sprawdź, czy usługi są uruchomione.

1. W Panelu sterowania wybierz pozycję **Usługi**.
2. Sprawdź, czy są uruchomione następujące usługi:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Placówka
    - Agent usług odzyskiwania platformy Microsoft Azure
    - Usługa odzyskiwania witryny platformy Microsoft Azure
    - tmansvc ( tmansvc )

3. Uruchom lub uruchom ponownie dowolną usługę, która nie jest uruchomiona.
4. Sprawdź, czy serwer przetwarzania jest połączony i osiągalny. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Krok 10: sprawdź połączenie serwera przetwarzania z publicznym adresem IP platformy Azure

1. Na serwerze przetwarzania w **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**otwórz najnowszy plik CBEngineCurr.errlog.
2. W pliku wyszukaj **443**lub próba połączenia ciągu **nie powiodła się.**

  ![Dzienniki błędów w folderze Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Jeśli widzisz problemy, znajduje się publiczny adres IP platformy Azure w pliku CBEngineCurr.currLog przy użyciu portu 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. W wierszu polecenia na serwerze przetwarzania użyj telnetu do pingowania publicznego adresu IP platformy Azure.
6. Jeśli nie możesz nawiązać połączenia, wykonaj następną procedurę.

## <a name="step-11-check-process-server-firewall-settings"></a>Krok 11: Sprawdź ustawienia zapory serwera przetwarzania. 

Sprawdź, czy zapora oparta na adresie IP na serwerze przetwarzania blokuje dostęp.

1. W przypadku reguł zapory opartej na adresie IP:

    a) Pobierz pełną listę [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Dodaj zakresy adresów IP do konfiguracji zapory, aby upewnić się, że zapora umożliwia komunikację z platformą Azure (i do domyślnego portu HTTPS, 443).

    c) Zezwalaj na zakresy adresów IP dla regionu platformy Azure subskrypcji i dla regionu Zachodnie stany USA platformy Azure (używanego do kontroli dostępu i zarządzania tożsamościami).

2. W przypadku zapór opartych na adresach URL dodaj adresy URL wymienione w poniższej tabeli do konfiguracji zapory.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Krok 12: Weryfikowanie ustawień serwera proxy serwera przetwarzania 

1. Jeśli używasz serwera proxy, upewnij się, że nazwa serwera proxy została rozpoznana przez serwer DNS. Sprawdź wartość podana podczas konfigurowania serwera konfiguracji w kluczu rejestru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Upewnij się, że te same ustawienia są używane przez agenta usługi Azure Site Recovery do wysyłania danych.

    a) Wyszukaj **kopię zapasową platformy Microsoft Azure**.

    b) Otwórz **usługę Kopia zapasowa platformy Microsoft Azure**i wybierz polecenie**Zmiany** **akcji** > .

    c) Na karcie **Konfiguracja serwera proxy** adres serwera proxy powinien być taki sam jak adres serwera proxy wyświetlany w ustawieniach rejestru. Jeśli nie, zmień go na ten sam adres.

## <a name="step-13-check-bandwidth"></a>Krok 13: Sprawdź przepustowość

Zwiększ przepustowość między serwerem przetwarzania a platformą Azure, a następnie sprawdź, czy problem nadal występuje.


## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz więcej pomocy, opublikuj swoje pytanie na [forum usługi Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
