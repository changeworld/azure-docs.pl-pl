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
ms.openlocfilehash: 669966ce21c5c6c2d0653eb51c81fe78aa0b3a12
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057314"
---
**Wymagania dotyczące serwera konfiguracji/przetwarzania**

**Składnik** | **Wymaganie** 
--- | ---
**USTAWIENIA SPRZĘTU** | 
Rdzenie procesora CPU | 8 
Pamięć RAM | 16 GB
Liczba dysków | 3, w tym dysku systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii 
Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
Wolnego miejsca na dysku (dysk przechowywania) | 600 GB
 | 
**USTAWIENIA OPROGRAMOWANIA** | 
System operacyjny | Windows Server 2012 R2 <br> Windows Server 2016
Ustawienia regionalne systemu operacyjnego | Angielski (en-us)
Role systemu Windows Server | Nie włączaj tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V 
Zasady grupy | Nie włączaj tych zasad grupy: <br> -Zapobiegaj dostępowi do wiersza polecenia. <br> -Uniemożliwić dostęp do narzędzi edycji rejestru. <br> — Logika zaufania dla plików załączników. <br> -Włącz wykonywanie skryptu. <br> [Dowiedz się więcej](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | -Brak przeniosła istniejące wcześniej domyślnej witryny sieci Web <br> -Brak przeniosła istniejące wcześniej witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie 
| 
**USTAWIENIA SIECI** | 
Typ adresu IP | Statyczny 
Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy) <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https://management.azure.com <br> -*. services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF również muszą mieć dostęp do następujących adresów URL <br> - https://login.microsoftonline.com <br> - https://secure.aadcdn.microsoftonline-p.com <br> - https://login.live.com  <br> - https://auth.gfx.ms <br> - https://graph.windows.net <br> - https://login.windows.net <br> - https://www.live.com <br> - https://www.microsoft.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych) 
Typ karty NIC | Innego VMXNET3 (Jeśli na serwerze konfiguracji maszyny Wirtualnej VMware)
 | 
**OPROGRAMOWANIE DO ZAINSTALOWANIA** | 
Program VMware vSphere PowerCLI | [Interfejs PowerCLI w wersji 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) powinien być zainstalowany, jeśli serwer konfiguracji jest uruchomiony na maszynie Wirtualnej VMware.
MYSQL | Powinien być zainstalowany MySQL. Można zainstalować ręcznie lub Usługa Site Recovery można go zainstalować.

**Określania rozmiaru wymagania dotyczące serwera konfiguracji/przetwarzania**

**CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU<br/><br/> gniazda 2 * 4 rdzenie \@ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | < 100 maszyn
12 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB – 1 TB | 100-150 maszyn
16 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1 – 2 TB | -150 200 maszyn

