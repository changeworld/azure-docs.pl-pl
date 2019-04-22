---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: afeae4af9b41bf434b26833a3bd927118a4697ae
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58887019"
---
**Wymagania dotyczące serwera konfiguracji/przetwarzania replikacji serwera fizycznego**

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
IIS | -Brak przeniosła istniejące wcześniej domyślnej witryny sieci Web <br> -Brak przeniosła istniejące wcześniej witryny sieci Web/aplikacja nasłuchuje na porcie 443 <br>-Włącz [uwierzytelnianie anonimowe](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Włącz [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ustawienie.
Typ adresu IP | Statyczny 
| 
**USTAWIENIA DOSTĘPU** | 
MYSQL | MySQL, należy zainstalować na serwerze konfiguracji. Można zainstalować ręcznie lub Usługa Site Recovery można zainstalować go podczas wdrażania. Usługi Site Recovery zainstalować, sprawdź, czy komputer może osiągnąć http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
Adresy URL | Serwer konfiguracji musi mieć dostęp do tych adresów URL (bezpośrednio lub za pośrednictwem serwera proxy):<br/><br/> Azure AD: `login.microsoftonline.com`; `login.microsoftonline.us`; `*.accesscontrol.windows.net`<br/><br/> Transfer danych replikacji: `*.backup.windowsazure.com`; `*.backup.windowsazure.us`<br/><br/> Zarządzanie replikacją: `*.hypervrecoverymanager.windowsazure.com`; `*.hypervrecoverymanager.windowsazure.us`; `https://management.azure.com`; `*.services.visualstudio.com`<br/><br/> Dostęp do magazynu: `*.blob.core.windows.net`; `*.blob.core.usgovcloudapi.net`<br/><br/> Synchronizacja czasu: `time.nist.gov`; `time.windows.com`<br/><br/> Dane telemetryczne (opcjonalnie): `dc.services.visualstudio.com`
Zapora | Reguły zapory oparte na adresie IP powinien zezwalać na komunikację z adresami URL platformy Azure. Aby uprościć i ograniczać zakresy adresów IP, zaleca się przy użyciu filtrowanie adresów URL.<br/><br/>**Komercyjne adresów IP:**<br/><br/>-Zezwalaj na [zakresów adresów IP centrum danych Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)oraz portu HTTPS (443).<br/><br/> -Zezwalaj na zakresy adresów IP dla regionu zachodnie stany USA, (używane do kontroli dostępu i zarządzania tożsamościami).<br/><br/> -Zezwalaj na zakresy adresów IP dla regionu platformy Azure Twojej subskrypcji, aby obsługiwać adresy URL służące do usługi Azure Active Directory, kopii zapasowej, replikacji i magazynu.<br/><br/> **Aby uzyskać dla instytucji publicznych adresów IP:**<br/><br/> -Zezwalaj na platformy Azure dla instytucji rządowych Datacenter zakresów adresów IP i portu HTTPS (443).<br/><br/> -Zezwalaj na zakresy adresów IP dla wszystkich US Gov regionów (Wirginia, Teksasie, Arizona i Iowa), do obsługi adresy URL służące do usługi Azure Active Directory, kopii zapasowej, replikacji i magazynu.
Porty | Zezwalaj na 443 (organizowanie kanału sterowania)<br/><br/> Zezwalaj na 9443 (transport danych) 


**Określania rozmiaru wymagania dotyczące serwera konfiguracji/przetwarzania**

**CPU** | **Pamięć** | **Dysk pamięci podręcznej** | **Współczynnik zmian danych** | **Zreplikowane maszyny**
--- | --- | --- | --- | ---
8 wirtualnych procesorów CPU<br/><br/> gniazda 2 * 4 rdzenie \@ 2,5 GHz | 16GB | 300 GB | 500 GB lub mniej | < 100 maszyn
12 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 6 rdzeni \@ 2,5 GHz | 18 GB | 600 GB | 500 GB-1 TB | 100-150 maszyn
16 procesorów wirtualnych Vcpu<br/><br/> 2 socks * 8 rdzeni \@ 2,5 GHz | 32 GB | 1 TB | 1-2 TB | -150 200 maszyn

