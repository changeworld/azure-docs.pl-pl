---
title: Zarządzanie serwer przetwarzania na platformie Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie serwera przetwarzania, ustaw dla maszyny Wirtualnej VMware i replikacji serwera fizycznego w usłudze Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/21/2018
ms.author: ramamill
ms.openlocfilehash: f8368aab1bc979492143d77a191a31afef754c4c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213014"
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
  ```
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


