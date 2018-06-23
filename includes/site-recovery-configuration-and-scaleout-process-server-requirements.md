---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: bc1beb53c5919914919e666ecf1836ec45d32ccf
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319730"
---
**Wymagania dotyczące procesu konfiguracji serwera**

**Składnik** | **Wymaganie** 
--- | ---
**USTAWIENIA SPRZĘTU** | 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysku systemu operacyjnego, dysku pamięci podręcznej serwera przetwarzania i przechowywania dysku powrotu po awarii 
Wolne miejsce (pamięć podręczna serwera procesu) | 600 GB
Wolnego miejsca na dysku (dysku przechowywania) | 600 GB
 | 
**USTAWIENIA OPROGRAMOWANIA** | 
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Uniemożliwić dostęp do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzia do edycji rejestru. <br> — Logika zaufania dla załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak istniejących domyślnej witryny sieci Web <br> -Brak istniejących witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 
| 
**USTAWIENIA SIECI** | 
Typ adresu IP | Statyczny 
Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (Jeśli podczas konfigurowania serwera konfiguracji) <br> - time.nist.gov <br> - time.windows.com 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty Sieciowej | VMXNET3 (Jeśli serwer konfiguracji maszyny Wirtualnej VMware)
 | 
**OPROGRAMOWANIE DO ZAINSTALOWANIA** | 
VMware vSphere PowerCLI | [PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli serwer konfiguracji jest uruchomiony na maszynie Wirtualnej VMware.
MYSQL | Należy zainstalować MySQL. Można zainstalować ręcznie lub usługi Site Recovery może go zainstalować.

**Serwer konfiguracji procesu zmiany rozmiaru wymagania**

**CPU** | **Pamięci** | **Pamięci podręcznej dysku** | **Częstotliwość zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
Vcpu 8<br/><br/> gniazda 2 * 4 rdzenie @ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | < 100 maszyn
Vcpu 12<br/><br/> 2 socks * 6 rdzeni @ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100-150 komputerów
16 Vcpu<br/><br/> 2 socks * 8 rdzeni @ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | 150 -200 maszyn

