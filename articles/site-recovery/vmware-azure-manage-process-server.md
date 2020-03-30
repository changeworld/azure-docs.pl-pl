---
title: Zarządzanie serwerem przetwarzania maszyn wirtualnych VMware/odzyskiwanie po awarii serwera fizycznego w usłudze Azure Site Recovery
description: W tym artykule opisano zarządzanie serwerem przetwarzania do odzyskiwania po awarii maszyn wirtualnych VMware/serwerów fizycznych przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257253"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

W tym artykule opisano typowe zadania związane z zarządzaniem serwerem procesów odzyskiwania lokacji.

Serwer przetwarzania służy do odbierania, optymalizowania i wysyłania danych replikacji na platformę Azure. Wykonuje również instalację wypychaną usługi mobilności na maszynach wirtualnych VMware i serwerach fizycznych, które chcesz replikować, i wykonuje automatyczne odnajdowanie maszyn lokalnych. W przypadku replikowania lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure serwer przetwarzania jest instalowany domyślnie na komputerze serwera konfiguracji. 

- W przypadku dużych wdrożeń może być konieczne dodatkowe lokalne serwery przetwarzania do skalowania pojemności.
- W przypadku powrotu po awarii z platformy Azure do lokalnego należy skonfigurować tymczasowy serwer procesów na platformie Azure. Tę maszynę wirtualną można usunąć po zakończeniu powrotu po awarii. 

Dowiedz się więcej o serwerze przetwarzania.


## <a name="upgrade-a-process-server"></a>Uaktualnianie serwera przetwarzania

Podczas wdrażania serwera przetwarzania lokalnie lub jako maszyna wirtualna platformy Azure dla powrotu po awarii jest zainstalowana najnowsza wersja serwera przetwarzania. Zespoły odzyskiwania witryn regularnie wypuszczają poprawki i ulepszenia i zalecamy, aby serwery procesów były aktualne. Serwer przetwarzania można uaktualnić w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Przenoszenie maszyn wirtualnych w celu zrównoważenia obciążenia serwera procesu

Równoważ obciążenie, przenosząc maszyny wirtualne między dwoma serwerami procesów w następujący sposób:

1. W przechowalni w obszarze **Zarządzaj** kliknij pozycję **Infrastruktura odzyskiwania witryny**. W **obszarze Dla maszyn wirtualnych & komputerów fizycznych**kliknij pozycję **Serwery konfiguracji**.
2. Kliknij serwer konfiguracji, za pomocą którego są zarejestrowane serwery przetwarzania.
3. Kliknij serwer przetwarzania, dla którego chcesz zrównoważyć ruch.

    ![Równowaga obciążenia](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kliknij **pozycję Równoważenie obciążenia**, wybierz docelowy serwer przetwarzania, do którego chcesz przenieść maszyny. Następnie kliknij przycisk **OK**

    ![LoadPS (loadps)](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kliknij **przycisk Wybierz maszyny**i wybierz maszyny, które chcesz przenieść z bieżącego do docelowego serwera przetwarzania. Szczegóły średniej zmiany danych są wyświetlane dla każdej maszyny wirtualnej. Następnie kliknij przycisk **OK**. 
3. W przechowalni należy monitorować postęp zadania w obszarze **Monitorowanie** > **zadań odzyskiwania witryny**.

Odzwierciedlenie zmian w portalu zajmie około 15 minut. Aby uzyskać szybszy efekt, [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Przełączanie całego obciążenia na inny serwer przetwarzania

Przenoszenie całego obciążenia obsługiwanego przez serwer przetwarzania na inny serwer przetwarzania w następujący sposób:

1. W przechowalni w obszarze **Zarządzaj** kliknij pozycję **Infrastruktura odzyskiwania witryny**. W **obszarze Dla maszyn wirtualnych & komputerów fizycznych**kliknij pozycję **Serwery konfiguracji**.
2. Kliknij serwer konfiguracji, za pomocą którego są zarejestrowane serwery przetwarzania.
3. Kliknij serwer przetwarzania, z którego chcesz przełączyć obciążenie.
4. Kliknij **przełącznik**, wybierz docelowy serwer przetwarzania, do którego chcesz przenieść obciążenie. Następnie kliknij przycisk **OK**

    ![Przełącznik](media/vmware-azure-manage-process-server/Switch.PNG)

5. W przechowalni należy monitorować postęp zadania w obszarze **Monitorowanie** > **zadań odzyskiwania witryny**.

Odzwierciedlenie zmian w portalu zajmie około 15 minut. Aby uzyskać szybszy efekt, [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>Rejestrowanie głównego serwera docelowego

Główny serwer docelowy znajduje się na serwerach konfiguracji i serwerach procesów skalowanych w poziomie. Musi być zarejestrowany na serwerze konfiguracji. W przypadku awarii w tej rejestracji, może to mieć wpływ na kondycję chronionych elementów. Aby zarejestrować główny serwer docelowy z serwerem konfiguracji, zaloguj się do określonego serwera konfiguracji/serwera procesów skalowanych w poziomie, na którym wymagana jest rejestracja. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**i uruchom następujące polecenie w wierszu polecenia administratora.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>Ponowne rejestrowanie serwera przetwarzania

Ponownie zarejestrować serwer przetwarzania uruchomiony lokalnie lub na maszynie Wirtualnej platformy Azure z serwerem konfiguracji w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po zapisaniu ustawień wykonaj następujące czynności:

1. Na serwerze przetwarzania otwórz wiersz polecenia administratora.
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**i uruchom polecenie:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modyfikowanie ustawień serwera proxy lokalnego serwera przetwarzania

Jeśli lokalny serwer przetwarzania używa serwera proxy do łączenia się z platformą Azure, można zmodyfikować ustawienia serwera proxy w następujący sposób:

1. Zaloguj się na komputerze serwera przetwarzania. 
2. Otwórz okno polecenia programu Admin PowerShell i uruchom następujące polecenie:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**i uruchom to polecenie:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Usuwanie serwera przetwarzania

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Wykluczanie folderów z oprogramowania antywirusowego

Jeśli oprogramowanie antywirusowe jest uruchomione na serwerze procesów skalowalnych w poziomie (lub głównym serwerze docelowym), wyklucz następujące foldery z operacji antywirusowych:


- C:\Pliki programów\Agent usług odzyskiwania platformy Microsoft Azure
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LoguploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Katalog instalacji serwera przetwarzania. Na przykład: C:\Program Files (x86)\Microsoft Azure Site Recovery