---
title: Zarządzanie serwer przetwarzania na potrzeby odzyskiwania po awarii maszyn wirtualnych programu VMware i serwerów fizycznych do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie serwerem proces konfigurowania odzyskiwania po awarii maszyn wirtualnych VMware i serwera fizycznego na platformie Azure przy użyciu usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: d99b5d1fdca39466d5e09ca077329b7ffa8622bc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568856"
---
# <a name="manage-process-servers"></a>Zarządzanie serwerami przetwarzania

Domyślnie serwer przetwarzania używany w przypadku replikacji maszyn wirtualnych VMware lub serwery fizyczne do platformy Azure jest instalowany na komputerze z serwerem konfiguracji w środowisku lokalnym. Istnieje kilka wystąpień, w których należy skonfigurować oddzielnego serwera przetwarzania:

- W przypadku dużych wdrożeń możesz potrzebować dodatkowych lokalnych serwerów przetwarzania skalować wydajność.
- Powrót po awarii, należy to tymczasowy serwer przetwarzania na platformie Azure. Po zakończeniu powrotu po awarii, można usunąć tej maszyny Wirtualnej. 

Ten artykuł zawiera podsumowanie typowych zadań zarządzania dla tych dodatkowych serwerów przetwarzania.

## <a name="upgrade-a-process-server"></a>Uaktualnij serwer przetwarzania

Uaktualnij serwer przetwarzania, uruchomiony w środowisku lokalnym lub na platformie Azure (na potrzeby powrotu po awarii) w następujący sposób:

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  Zazwyczaj gdy używasz obrazu z galerii platformy Azure do utworzenia serwera przetwarzania na platformie Azure na potrzeby powrotu po awarii jest uruchomiona najnowsza dostępna wersja. Usługa Site Recovery zespołów wersji poprawki i ulepszenia w regularnych odstępach czasu i zalecamy serwerów przetwarzania zapewnianie aktualności.



## <a name="reregister-a-process-server"></a>Zarejestruj ponownie serwer przetwarzania

Jeśli chcesz ponownie zarejestrować serwer przetwarzania, działającego lokalnie lub na platformie Azure z serwerem konfiguracji, wykonaj następujące czynności:

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

Jeśli serwer przetwarzania używa serwera proxy do łączenia z usługą Site Recovery na platformie Azure, użyj tej procedury, jeśli trzeba zmodyfikować istniejących ustawień serwera proxy.

1. Zaloguj się na komputerze serwera przetwarzania. 
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

## <a name="manage-anti-virus-software-on-process-servers"></a>Zarządzanie oprogramowania antywirusowego na serwerze przetwarzania

Jeśli oprogramowanie antywirusowe jest aktywna na autonomicznym serwerze przetwarzania lub główny serwer docelowy, należy wyłączyć następujące foldery z oprogramowania antywirusowego operacje:


- Agent usług C:\Program Files\Microsoft Azure Recovery Services
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Katalog instalacji serwera przetwarzania, przykład: C:\Program Files (x86) \Microsoft Azure Site Recovery

