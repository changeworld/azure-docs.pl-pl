---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 05/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 2464fa41deaa1e9c43e5f53e1a900ca11b582040
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
**Wymagania dotyczące procesu konfiguracji serwera**

**Składnik** | **Wymaganie** 
--- | ---
**SPRZĘT** | 
**Rdzenie Procesora** | 8 
**RAM** | 16 GB
**Liczba dysków** | 3, w tym dysku systemu operacyjnego, dysku pamięci podręcznej serwera przetwarzania i przechowywania dysku powrotu po awarii 
**Wolne miejsce (pamięć podręczna serwera procesu)** | 600 GB
**Wolnego miejsca na dysku (dysku przechowywania)** | 600 GB
**OPROGRAMOWANIE** | 
**System operacyjny** | Windows Server 2012 R2 <br> Windows Server 2016
**Ustawienia regionalne systemu operacyjnego** | Angielski (en-us)
**Role systemu Windows Server** | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
**Zasady grupy** | Nie włączaj tych zasad grupy: <br> -Uniemożliwić dostęp do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzia do edycji rejestru. <br> — Logika zaufania dla załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
**IIS** | -Brak istniejących domyślnej witryny sieci Web <br> -Brak istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 
**SIECI** | 
**Typ adresu IP** | Statyczny 
**Dostęp do Internetu** | Serwer musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (Jeśli podczas konfigurowania serwera konfiguracji) <br> - time.nist.gov <br> - time.windows.com 
**Porty** | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
**VMWARE (podczas konfigurowania serwera konfiguracji procesu jako maszyny Wirtualnej VMware)**
**typu karta sieciowa** | VMXNET3  
**VMware vSphere PowerCLI uruchomione na maszynie Wirtualnej* | [PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")

**Serwer konfiguracji procesu zmiany rozmiaru wymagania**

**CPU** | **Pamięci** | **Pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
Vcpu 8<br/><br/> gniazda 2 * 4 rdzenie @ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | < 100 maszyn
Vcpu 12<br/><br/> 2 socks * 6 rdzeni @ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100-150 komputerów
16 Vcpu<br/><br/> 2 socks * 8 rdzeni @ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 -200 maszyn

