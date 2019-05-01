---
title: Zarządzanie serwerem proces używany na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure za pomocą usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie serwerem proces konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925612"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

W tym artykule opisano typowe zadania zarządzania na serwerze przetwarzania Site Recovery.

Serwer przetwarzania jest używany do odbierania, optymalizowanie i wysyłają dane replikacji do platformy Azure. Wykonuje także instalację wypychaną usługi Mobility na maszynach wirtualnych VMware i serwerów fizycznych, którą chcesz replikować, i przeprowadza automatyczne odnajdywanie maszyn lokalnych. Do replikowania lokalnych maszyn wirtualnych z programu VMware lub serwery fizyczne do platformy Azure, serwer przetwarzania jest instalowany domyślnie na komputerze z serwerem konfiguracji. 

- W przypadku dużych wdrożeń możesz potrzebować dodatkowych lokalnych serwerów przetwarzania skalować wydajność.
- Do powrotu po awarii z platformy Azure do serwera lokalnego należy skonfigurować serwer tymczasowy przetwarzania na platformie Azure. Po zakończeniu powrotu po awarii, można usunąć tej maszyny Wirtualnej. 

Dowiedz się więcej na temat serwera przetwarzania.


## <a name="upgrade-a-process-server"></a>Uaktualnij serwer przetwarzania

Podczas wdrażania serwera przetwarzania w środowisku lokalnym, lub jako Maszynę wirtualną platformy Azure do powrotu po awarii, jest zainstalowana najnowsza wersja serwera przetwarzania. Usługa Site Recovery zespołów wersji poprawki i ulepszenia w regularnych odstępach czasu i zalecamy serwerów przetwarzania zapewnianie aktualności. Możesz uaktualnić serwer przetwarzania w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Przenoszenie maszyn wirtualnych w celu zrównoważenia obciążenia serwera przetwarzania

Równoważenie obciążenia przez przeniesienie maszyn wirtualnych między dwoma serwerami proces, w następujący sposób:

1. W magazynie w obszarze **Zarządzaj** kliknij **infrastruktura usługi Site Recovery**. W obszarze **oprogramowanie VMware i fizycznych komputerów**, kliknij przycisk **serwery konfiguracji**.
2. Kliknij serwer konfiguracji za pomocą którego zarejestrowano serwerów przetwarzania.
3. Kliknij na serwerze przetwarzania, dla których chcesz równoważyć obciążenie.

    ![DSM](media/vmware-azure-manage-process-server/LoadBalance.png)

4. Kliknij przycisk **Równoważenie obciążenia**, wybierz docelowy serwer przetwarzania, do którego chcesz przenieść maszyny. Następnie kliknij przycisk **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Kliknij przycisk **wybierz maszyny**i wybierz maszyny, którą chcesz przenieść z bieżącej do docelowego serwera przetwarzania. Szczegółowe informacje o zmianie uśrednianie danych są wyświetlane na każdej maszynie wirtualnej. Następnie kliknij przycisk **OK**. 
3. W magazynie, monitorować postęp zadania w obszarze **monitorowanie** > **zadania usługi Site Recovery**.

Potrwa około 15 minut, zanim zmiany zostaną odzwierciedlone w portalu. Aby szybciej efekt [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>Przełącz całe obciążenie na inny serwer przetwarzania

Przenieś całe obciążenie, które są obsługiwane przez serwer przetwarzania do innego serwera przetwarzania, w następujący sposób:

1. W magazynie w obszarze **Zarządzaj** kliknij **infrastruktura usługi Site Recovery**. W obszarze **oprogramowanie VMware i fizycznych komputerów**, kliknij przycisk **serwery konfiguracji**.
2. Kliknij serwer konfiguracji za pomocą którego zarejestrowano serwerów przetwarzania.
3. Kliknij na serwerze przetwarzania, w którym chcesz się przełączyć obciążenia.
4. Kliknij pozycję **przełącznika**, wybierz docelowy serwer przetwarzania, do którego chcesz przenieść obciążenia. Następnie kliknij przycisk **OK**

    ![Przełącznik](media/vmware-azure-manage-process-server/Switch.PNG)

5. W magazynie, monitorować postęp zadania w obszarze **monitorowanie** > **zadania usługi Site Recovery**.

Potrwa około 15 minut, zanim zmiany zostaną odzwierciedlone w portalu. Aby szybciej efekt [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).



## <a name="reregister-a-process-server"></a>Zarejestruj ponownie serwer przetwarzania

Zarejestruj ponownie serwer przetwarzania, działającego lokalnie lub na Maszynie wirtualnej platformy Azure z serwerem konfiguracji, w następujący sposób:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Po zapisaniu ustawień, wykonaj następujące czynności:

1. Na serwerze przetwarzania Otwórz wiersz polecenia administratora.
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, a następnie uruchom polecenie:

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modyfikowanie ustawień serwera proxy dla serwera przetwarzania w środowisku lokalnym

Jeśli serwer przetwarzania w środowisku lokalnym używa serwera proxy do połączenia z platformą Azure, można zmodyfikować ustawienia serwera proxy w następujący sposób:

1. Zaloguj się do komputera serwera przetwarzania. 
2. Otwórz okno poleceń programu PowerShell i uruchom następujące polecenie:
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. Przejdź do folderu **%PROGRAMDATA%\ASR\Agent**, i uruchom następujące polecenie:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>Usuń serwer przetwarzania

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>Wyklucz foldery z oprogramowania antywirusowego

Jeśli oprogramowanie antywirusowe jest uruchomiona na serwerze przetwarzania skalowalnego w poziomie (lub główny serwer docelowy), należy wyłączyć następujące foldery z oprogramowania antywirusowego operacje:


- Agent usług C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Katalog instalacji serwera przetwarzania. Na przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery