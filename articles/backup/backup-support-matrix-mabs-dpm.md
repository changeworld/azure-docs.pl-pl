---
title: Macierz obsługi dla Microsoft Azure Backup server i programu System Center DPM
description: Ten artykuł zawiera podsumowanie obsługi usługi Azure Backup, korzystając z serwera usługi Microsoft Azure Backup lub programu System Center DPM do tworzenia kopii zapasowych lokalnie i zasobów maszyny Wirtualnej platformy Azure.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430850"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Macierz obsługi kopii zapasowej za pomocą pakietu Microsoft Azure Backup Server/System Center DPM

Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych maszyn lokalnych i obciążeń oraz maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące tworzenia kopii zapasowych maszyn za pomocą programu Microsoft Azure Backup Server (MABS) lub System Center Data Protection Manager (DPM) oraz usługi Azure Backup.

## <a name="about-mabsdpm"></a>Temat serwera usługi Mab/programu DPM

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) to rozwiązanie enterprise, które konfiguruje, ułatwia i zarządza nimi kopii zapasowych i odzyskiwania maszyn enterprise i danych. Tego część [programu System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) pakiet produktów.


Microsoft Azure Backup Server (MABS) jest produktem serwera, który może służyć do tworzenia kopii zapasowej lokalnych serwerów fizycznych, maszyn wirtualnych (VM) i aplikacje działające na ich i maszyn wirtualnych platformy Azure.

- Serwera usługi Mab opiera się na System Center Data Protection Manager (DPM) i zapewnia podobne funkcje z kilkoma różnicami:
    - Licencja programu System Center nie jest wymagane do uruchamiania serwera usługi MAB.
    - Zarówno dla serwera usługi Mab i DPM na platformie Azure zapewnia długoterminowego przechowywania kopii zapasowych. Ponadto program DPM umożliwia tworzenie kopii zapasowych danych w celu przechowywania długoterminowego na taśmie. Serwera usługi Mab nie zapewnia tę funkcję.
- Pobieranie serwera usługi Mab serwera z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Mogą być uruchamiane lokalnie lub na Maszynie wirtualnej platformy Azure na platformie Azure.

Program DPM i serwera usługi Mab obsługuje tworzenie kopii zapasowej szerokiej gamy aplikacji i serwera oraz klienckie systemy operacyjne. Zapewniają one wiele scenariuszy tworzenia kopii zapasowej:
    - Kopię zapasową można wykonywać na poziomie komputera przy użyciu kopii zapasowej stanu systemu lub bez systemu operacyjnego.
    - Można utworzyć kopię zapasową określonych woluminów, udziałów, folderów i plików.
    - Kopię zapasową można wykonywać określone aplikacje przy użyciu zoptymalizowanego obsługujących aplikacji ustawienia.

## <a name="mabsdpm-backup"></a>Kopia zapasowa serwera usługi Mab/programu DPM

Kopię zapasową przy użyciu serwera usługi Mab/programu DPM i usługi Azure Backup działa w następujący sposób:

1. Agent ochrony programu DPM/serwera usługi Mab, zainstalowani na każdym komputerze, na którym będzie wykonywana kopia zapasowa. 
2. Aplikacje/maszyn kopię zapasową w magazynie lokalnym w programie DPM/serwera usługi MAB.
3. Agent usługi Microsoft Azure Recovery Services (MARS) jest zainstalowany na program DPM server/serwera usługi MAB.
4. Agenta usług MARS tworzy kopie zapasowe dysków DPM/serwera usługi Mab do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure za pomocą usługi Azure Backup.

Więcej informacji:
- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Przegląd](backup-support-matrix-mars-agent.md) co to jest obsługiwana w przypadku agenta usług MARS.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze 

**Scenariusz** | **Agent** | **Lokalizacja**
--- | --- | ---
**Tworzenie kopii zapasowych obciążeń maszyn lokalnych** | Agent ochrony programu DPM/serwera usługi Mab jest uruchamiane na maszynach, które chcesz utworzyć kopię zapasową<br/><br/> Agenta usług MARS na serwerze DPM/serwera usługi Mab | Serwera usługi Mab/Program DPM musi działać w środowisku lokalnym
**Tworzenie kopii zapasowych maszyn wirtualnych/obciążeń platformy Azure** | Agent ochrony programu DPM/serwera usługi Mab na komputerze chronionym<br/><br/> Agenta usług MARS na serwera usługi Mab/DPM | Program DPM/serwera usługi Mab musi działać na Maszynie wirtualnej platformy Azure.

## <a name="supported-deployments"></a>Obsługiwane wdrożenia

Program DPM/MABS można wdrożyć zgodnie z opisem w tabeli.

**Wdrożenie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
**Wdrożone w środowisku lokalnym** | Serwer fizyczny<br/><br/>Maszyna wirtualna funkcji Hyper-V<br/><br/> Maszyna wirtualna VMware | Jeśli program DPM/serwera usługi Mab jest instalowany jako maszyna wirtualna VMware tylko tworzy kopie zapasowe maszyn wirtualnych VMware i obciążeń uruchomionych na tych maszynach wirtualnych.
**Wdrożony jako usługa Azure Stack maszyny Wirtualnej** | Tylko serwera usługi Mab | Program DPM nie może służyć do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure Stack.
**Wdrożony jako maszyna wirtualna platformy Azure** | Chroni maszyny wirtualne platformy Azure i obciążeń uruchomionych na tych maszynach wirtualnych. | Serwera usługi Mab/Program DPM działający na platformie Azure nie można utworzyć kopii maszyn lokalnych.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Obsługiwane systemy operacyjne serwera usługi Mab i program DPM

Usługa Azure Backup można utworzyć kopię zapasową serwera usługi Mab/w programie DPM uruchomionym dowolnym z następujących systemów operacyjnych. Systemy operacyjne powinien być uruchomiony z najnowszymi dodatkami service pack i aktualizacjami.

**Scenariusz** | **MABS/DPM** 
--- | --- 
**Serwera usługi Mab na Maszynie wirtualnej platformy Azure** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> Zaleca się, że uruchomieniu za pomocą obrazu z witryny marketplace.<br/><br/> Minimalna A2 warstwie podstawowa przy użyciu dwóch rdzeni oraz 3,5 GB pamięci RAM. 
**Program DPM na maszynie Wirtualnej platformy Azure** | System Center 2012 R2 z aktualizacją Update 3 lub nowszej.<br/><br/> System operacyjny Windows jako [wymagane przez program System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Zaleca się, że uruchomieniu za pomocą obrazu z witryny marketplace.<br/><br/> Minimalna A2 warstwie podstawowa przy użyciu dwóch rdzeni oraz 3,5 GB pamięci RAM. 
**Serwera usługi Mab w środowisku lokalnym** | Obsługiwane 64-bitowych systemach operacyjnych:<br/><br/> -Serwera usługi Mab w wersji 3 lub nowszym: System Windows Server 2019 r (wersje Standard i Datacenter, podstawowe elementy) <br/><br/> Serwera usługi Mab w wersji 2 lub nowszy: System Windows Server 2016 (wersje Standard i Datacenter, podstawowe elementy)<br/><br/> Wszystkie wersje serwera usługi Mab: Windows Server 2012 R2/2012 (wersje Standard i Datacenter, Foundation)<br/><br/>Wszystkie wersje serwera usługi Mab: Windows Storage Server 2012 R2/2012 (Standardowa/grupa robocza)
**Program DPM w środowisku lokalnym** | Fizyczny serwer/funkcji Hyper-V maszyny Wirtualnej: System Center 2012 SP1 lub nowszym.<br/><br/> Maszyna wirtualna VMware: System Center 2012 R2 z aktualizacją Update 5 lub nowszym. 



## <a name="management-support"></a>Obsługa zarządzania
**Problem** | **Szczegóły**
--- | ---
**Instalacja** | Program DPM/serwera usługi Mab, należy zainstalować na maszynie jednozadaniowe.<br/><br/> Nie należy instalować programu DPM/serwera usługi Mab, na kontrolerze domeny, na komputerze z instalacją roli serwera aplikacji, na komputerze z programem Exchange Server lub programu System Center Operations Manager lub w węźle klastra.<br/><br/> [Przegląd](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) wszystkie wymagania systemowe programu DPM.
**Domeny** | Program DPM server/MABS powinien należeć do domeny. Najpierw zainstaluj, a następnie dołącz do programu DPM/serwera usługi Mab do domeny. Przenoszenie programu DPM/serwera usługi Mab do nowej domeny po wdrożeniu nie jest obsługiwane.
**Storage** | Nowoczesny magazyn kopii zapasowych (MB) jest obsługiwane od wersji DPM 2016/serwera usługi Mab v2 lub nowszy. Nie jest dostępny dla serwera usługi Mab w wersji 1.
**Uaktualnianie serwera usługi Mab** | Można bezpośrednio zainstalować serwera usługi Mab w wersji 3 lub uaktualnienie do serwera usługi Mab w wersji 3 z serwera usługi Mab w wersji 2. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Przenoszenie serwera usługi Mab** | Przenoszenie serwera usługi Mab do nowego serwera przy zachowaniu magazynu jest obsługiwane, jeśli używasz MB.<br/><br/> Serwer musi mieć taką samą nazwę jak oryginał. Nie można zmienić nazwy, jeśli chcesz zachować tej samej puli magazynu, a następnie użyć tej samej bazy danych serwera usługi Mab do przechowywania punktów odzyskiwania danych.<br/><br/> Ponieważ musisz przywrócić ją, należy kopię zapasową bazy danych serwera usługi MAB.




## <a name="mabs-support-on-azure-stack"></a>Obsługa serwera usługi Mab w usłudze Azure Stack

Można wdrożyć serwera usługi Mab na Maszynie wirtualnej platformy Azure Stack, tak, aby można było zarządzać tworzeniem kopii zapasowych maszyn wirtualnych platformy Azure Stack i obciążeń z jednej lokalizacji.

**Składnik** | **Szczegóły**
--- | --- 
**Serwera usługi Mab w usłudze Azure Stack maszyny Wirtualnej** | Co najmniej rozmiaru A2. Zaleca się uruchamiania przy użyciu obrazu systemu Windows Server 2012 R2 lub Windows Server 2016 z witryny Azure marketplace.<br/><br/> Nie należy instalować czymkolwiek, na maszynie Wirtualnej z serwera usługi MAB.
**Magazyn serwera usługi Mab** | Używaj oddzielnego konta magazynu dla maszyny Wirtualnej usługi MAB. Agenta usług MARS systemem serwera usługi Mab wymaga tymczasowego magazynu dla lokalizacji pamięci podręcznej, a także do przechowywania przywracanie danych w chmurze.
**Pula magazynów serwera usługi Mab** | Rozmiar puli magazynów serwera usługi Mab zależy od liczby i rozmiaru dysków dołączonych do maszyny Wirtualnej serwera usługi MAB. Każdego rozmiaru maszyny Wirtualnej platformy Azure Stack ma maksymalną liczbę dysków. Na przykład A2 to cztery dyski.
**Przechowywanie serwera usługi Mab** | Do więcej niż pięć dni nie należy przechowywać kopie zapasowe danych na dyskach lokalnych serwera usługi MAB.
**Skalowanie serwera usługi Mab** | Aby skalować wdrożenie w górę, można zwiększyć rozmiar maszyny Wirtualnej serwera usługi MAB. Na przykład od A do serii D.<br/><br/> Można także zapewnić danych przy użyciu kopii zapasowej na platformie Azure jest odciążanie regularnie, a jeśli to konieczne, można wdrożyć dodatkowe serwery serwera usługi MAB.
.NET framework na serwera usługi Mab | Maszyna wirtualna serwera usługi Mab musi .NET Framework w wersji 3.3 z dodatkiem SP1 lub nowszego na nim zainstalowany.
**Domeny serwera usługi Mab** | Maszyna wirtualna serwera usługi Mab muszą być przyłączone do domeny. Użytkownik domeny z uprawnieniami administratora, należy zainstalować serwera usługi Mab na maszynie Wirtualnej.
**Usługa Azure backup dane maszyny Wirtualnej stosu** | Można utworzyć kopię zapasową plików, folderów i aplikacji.
**Obsługiwane kopii zapasowej** | Te systemy operacyjne są obsługiwane dla maszyn wirtualnych, aby utworzyć kopię zapasową:<br/><br/> -Windows Server półroczny kanał (Datacenter/Enterprise/Standard)<br/><br/> System Windows Server 2016/2012 R2/2012/2008 R2.
**Obsługa programu SQL dla maszyn wirtualnych platformy Azure Stack** | Utwórz kopię zapasową programu SQL Server 2016/2014/2012 z dodatkiem SP1.<br/><br/> Tworzenie kopii zapasowych i odzyskiwanie bazy danych.
**Obsługa programu SharePoint dla maszyn wirtualnych platformy Azure Stack** | Program SharePoint 2016/2013/2010.<br/><br/> Tworzenie kopii zapasowych i odzyskiwania farmy, bazy danych, serwera sieci Web, serwer sieci web.
**Wymagania dotyczące sieci dla kopii zapasowych maszyn wirtualnych** | Wszystkie maszyny wirtualne w obciążeniu usługi Azure Stack musi być w tej samej sieci wirtualnej i należą do tej samej subskrypcji.


## <a name="dpmmabs-networking-support"></a>Program DPM/serwera usługi Mab obsługi sieci

### <a name="url-access"></a>Dostęp do adresu URL

Program DPM server/serwera usługi Mab musi mieć dostęp do tych adresów URL:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Program DPM/serwera usługi Mab łączności usługi Azure Backup

Połączenia z usługą Azure Backup jest wymagany do tworzenia kopii zapasowych do poprawnego działania i subskrypcji platformy Azure powinien być aktywny. W poniższej tabeli przedstawiono zachowania, jeśli nie występują te dwie rzeczy.

**Serwera usługi Mab do platformy Azure** | **Subskrypcja** | **/ Przywracania kopii zapasowej** 
--- | --- | --- 
Połączono | Aktywne | Tworzenie kopii zapasowej na dysku programu DPM/serwera usługi Mab<br/><br/> Kopia zapasowa na platformie Azure<br/><br/> Przywracanie z dysku<br/><br/> Przywracanie z platformy Azure
Połączono | Wygasłe/anulowanie aprowizacji | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja utraciła ważność można przywrócić z dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja jest zlikwidowana nie można przywrócić z dysku lub na platformie Azure. Punkty odzyskiwania platformy Azure zostaną usunięte.
Brak łączności przez więcej niż 15 dni | Aktywne | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Można przywrócić z dysku lub na platformie Azure.
Brak łączności przez więcej niż 15 dni | Wygasłe/anulowanie aprowizacji | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja utraciła ważność można przywrócić z dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja jest zlikwidowana nie można przywrócić z dysku lub na platformie Azure. Punkty odzyskiwania platformy Azure zostaną usunięte.



## <a name="dpmmabs-storage-support"></a>Program DPM/serwera usługi Mab storage — pomoc techniczna

Program DPM/serwera usługi Mab kopie zapasowe danych są przechowywane na magazyn na dysku lokalnym. 

**Storage** | **Szczegóły**
--- | ---
**MB** | Nowoczesny magazyn kopii zapasowych (MB) jest obsługiwane od wersji DPM 2016/serwera usługi Mab v2 lub nowszy. Nie jest dostępny dla serwera usługi Mab w wersji 1.
**Magazyn serwera usługi Mab na maszynie Wirtualnej platformy Azure** | Dane są przechowywane na dyskach platformy Azure dołączonych do maszyny Wirtualnej programu DPM/serwera usługi Mab i zarządzane w programie DPM/serwera usługi MAB. Liczba dysków, które mogą służyć do puli magazynów programu DPM/serwera usługi Mab jest ograniczona przez rozmiar maszyny Wirtualnej.<br/><br/> A2 MASZYNY WIRTUALNEJ: 4 dyski; A3 MASZYNY WIRTUALNEJ: 8 dysków; A4 MASZYNY WIRTUALNEJ: 16 dysków o maksymalnym rozmiarze 1 TB dla każdego dysku. Określa, łączna liczba kopii zapasowych dostępna pojemność puli magazynów.<br/><br/> Ilość danych, które można utworzyć kopię zapasową, zależy od liczby i rozmiaru dysków dołączonych.
**Przechowywanie danych serwera usługi Mab na maszynie Wirtualnej platformy Azure** | Firma Microsoft zaleca zachowywanie danych przez jeden dzień na dysku platformy Azure programu DPM/serwera usługi Mab i tworzenie kopii zapasowej z programu DPM/serwera usługi Mab do magazynu dla dłuższy okres przechowywania. Ten sposób można chronić większej ilości danych dzięki przeniesieniu ich do usługi Azure Backup.


### <a name="modern-backup-storage-mbs"></a>Nowoczesny magazyn kopii zapasowych (MB)
Od wersji DPM 2016/serwera usługi Mab w wersji 2 (uruchomiony w systemie Windows Server 2016) lub nowszy, możesz korzystać z zalet nowoczesnego magazynu kopii zapasowych (MB).

- MB kopie zapasowe są przechowywane na dysku systemu plików (ReFS).
- Zastosowań MB System plików ReFS blokują klonowania i szybszego tworzenia kopii zapasowych i bardziej efektywne wykorzystanie miejsca do magazynowania.
- Po dodaniu woluminy lokalne puli magazynów programu DPM/serwera usługi Mab, zostały skonfigurowane za pomocą litery dysku. Następnie należy skonfigurować Magazyn obciążenia na różnych woluminach.
- Podczas tworzenia grup ochrony w celu tworzenia kopii zapasowych danych programu DPM/serwera usługi Mab, należy wybrać dysk którego chcesz użyć. Na przykład można przechowywać kopie zapasowe dla bazy danych SQL lub inne wysokiego obciążenia operacji We/Wy dysków o wysokiej wydajności i obciążenia, które są rzadziej kopie na niższym dysku wydajności.


## <a name="supported-backups-to-mabs"></a>Obsługiwane tworzenie kopii zapasowej serwera usługi Mab

W poniższej tabeli podsumowano, co może można utworzyć kopie zapasowe serwera usługi Mab maszyn lokalnych i maszyn wirtualnych platformy Azure.


**Tworzenie kopii zapasowych** | **Wersje** | **SERWERA USŁUGI MAB** | **Szczegóły**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32 64-bitowy) | MABS v3, v2, V1 | Lokalnie | Wolumin/udział/folder/pliku<br/><br/> Woluminy z deduplikacją obsługiwane<br/><br/> Woluminy muszą mieć co najmniej 1 GB i systemu plików NTFS.
**System Windows Server 2016 (Datacenter, Standard i nie Nano)**<br/><br/> 32-64-bitowych | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2012 R2 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | MABS v3, v2, v1 | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Usługa Azure VMprotection**: Wolumin/udział/folder/pliku<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2012 z dodatkiem SP1 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2012 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | MABS v1 | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows 2008 R2 z dodatkiem SP1 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | Obsługiwane przez serwera usługi Mab w wersji 3, wersja 2, wersja 1.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. | Maszyna wirtualna środowiska lokalne/platformę Azure. |   **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows 2008 R2 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | MABS v1. Dla serwera usługi Mab w wersji 2 lub 3 systemu operacyjnego musi działać z dodatkiem SP1. | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 32-64-bitowych | MABS v1, v2, v3 | Tylko w wersji 1 jest obsługiwane w przypadku wdrożenia serwera usługi Mab jako maszynę fizyczną w środowisku lokalnym lub maszynie Wirtualnej funkcji Hyper-V.<br/><br/> Serwera usługi Mab w wersji 1, 2, 3 jest obsługiwana w przypadku wdrożenia serwera usługi Mab jako Maszynę wirtualną VMware.<br/><br/> Nieobsługiwane w przypadku serwera usługi Mab uruchomionych na maszynie Wirtualnej platformy Azure | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**Windows Storage Server 2008** | MABS v1, v2, v3 | Serwera usługi Mab jako fizyczny serwer/funkcji Hyper-V VM lokalnych | Nie jest obsługiwane dla serwera usługi Mab uruchomionych na maszynie Wirtualnej platformy Azure. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**SQL Server 2017** | Serwera usługi Mab w wersji 3 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2016/2016 z dodatkiem SP1** | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | W środowisku lokalnym. | Tworzenie kopii zapasowych autonomicznego serwera programu Exchange, bazy danych w grupie DAG<br/><br/> Odzyskaj Skrzynka pocztowa, bazy danych skrzynek pocztowych w grupie DAG.<br/><br/> System plików reFS nie jest obsługiwane.<br/><br/> Utwórz kopię zapasową nieudostępnione klastrów.<br/><br/> Utwórz kopię zapasową program Exchange Server skonfigurowany do ciągłej replikacji.
**SharePoint 2016**<br/><br/> **Program SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | Maszyna wirtualna na lokalne/platformy Azure | Tworzenie kopii zapasowych farmy, serwer frontonu sieci web.<br/><br/> Odzyskać farmy, bazy danych, aplikacji sieci web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci web.<br/><br/> Nie można utworzyć kopii zapasowej farmy, za pomocą funkcji AlwaysOn dla bazy danych zawartości.
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | MABS v3, v2, v1 | Lokalnie | **Agent serwera usługi Mab na hoście funkcji Hyper-V**: Utworzyć kopię zapasową całej maszyny wirtualne i pliki danych hosta. Utwórz kopię zapasową maszyn wirtualnych za pomocą magazynu lokalnego, maszyny wirtualne w klastrze za pomocą magazynu CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi Mab na maszynie Wirtualnej gościa**: Utwórz kopię zapasową obciążeń działających na maszynie Wirtualnej. CSVs.<br/><br/> **Odzyskiwanie**: Maszyna wirtualna, odzyskiwanie na poziomie elementu/woluminów/folderów/plików VHD.<br/><br/> **Maszyny wirtualne z systemem Linux**: Utwórz kopię zapasową, gdy funkcja Hyper-V jest uruchomiony w systemie Windows Server 2012 R2 lub nowszy. Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.
**Maszyny wirtualne programu VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> V1 serwera usługi Mab musi pakiet zbiorczy aktualizacji 1) | Lokalnie | Utwórz kopię zapasową maszyn wirtualnych programu VMware w magazynie woluminów CSV, systemu plików NFS i siecią SAN.<br/><br/> Odzyskaj całą maszynę Wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie na poziomie elementu folderów/plików dla maszyn wirtualnych Windows tylko.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.



## <a name="supported-backups-to-dpm"></a>Obsługiwane tworzenie kopii zapasowej programu DPM

W poniższej tabeli podsumowano, co może być kopii zapasowej program DPM z maszyn lokalnych i maszyn wirtualnych platformy Azure.



**Tworzenie kopii zapasowych** | **DPM** | **Szczegóły**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32 64-bitowy) | Tylko lokalnie.<br/><br/> Tworzenie kopii zapasowych systemu Windows 10 przy użyciu programu DPM 2012 R2 zaleca się zainstalowanie [aktualizacji 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Wolumin/udział/folder/pliku<br/><br/> Woluminy z deduplikacją obsługiwane<br/><br/> Woluminy muszą mieć co najmniej 1 GB i systemu plików NTFS.
**System Windows Server 2016 (Datacenter, Standard i nie Nano)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure.<br/><br/> Tylko program DPM 2016.| Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2012 R2 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | Maszyna wirtualna na lokalne/platformy Azure | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Woluminy z deduplikacją, obsługiwana przy użyciu programu DPM 2012 R2 lub nowszy.
**Windows Server 2012 z dodatkiem SP1 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Woluminy z deduplikacją, obsługiwana przy użyciu programu DPM 2012 R2 lub nowszy.
**Windows Server 2012 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych |  Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku<br/><br/> Woluminy z deduplikacją, obsługiwana przy użyciu programu DPM 2012 R2 lub nowszy.
**Windows 2008 R2 z dodatkiem SP1 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | Maszyna wirtualna na lokalne/platformy Azure <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. |   **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.
**Windows 2008 R2 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | W środowisku lokalnym.<br/><br/> Nie można zainstalować programu DPM jako Maszynę wirtualną VMware.<br/><br/> Program DPM działający na Maszynie wirtualnej platformy Azure nie jest obsługiwane. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 32-64-bitowych | Tylko lokalnie.<br/><br/> Program DPM jest obsługiwane podczas uruchamiania jako Maszynę wirtualną VMware. Działa jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V nie jest obsługiwane. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**Windows Storage Server 2008** | Program DPM w środowisku lokalnym systemem jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**SQL Server 2017** | KONSOLA SAC PROGRAMU DPM; Program DPM 2016 z pakietem zbiorczym aktualizacji 5 lub nowszy<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2016 z dodatkiem SP1** | Nieobsługiwane w przypadku programu DPM 2012 R2; Obsługiwane w przypadku SAC programu DPM, program DPM 2016 uruchomiony pakiet zbiorczy aktualizacji 4 lub nowszej.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2016** | Nie jest obsługiwane dla programu DPM 2012 R2. Obsługiwane w przypadku SAC programu DPM, program DPM 2016 z pakietem zbiorczym aktualizacji 2 lub nowszy.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | Program SQL Server 2014 przy użyciu programu DPM 2012 R2 jest uruchomiony pakiet zbiorczy aktualizacji 4 lub nowszy.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Dla programów Exchange 2016 Program DPM 2012 R2 wymaga pakietu zbiorczego aktualizacji 9 lub nowszy.<br/><br/> Lokalnie | Tworzenie kopii zapasowych autonomicznego serwera programu Exchange, bazy danych w grupie DAG<br/><br/> Odzyskaj Skrzynka pocztowa, bazy danych skrzynek pocztowych w grupie DAG.<br/><br/> System plików reFS nie jest obsługiwane.<br/><br/> Utwórz kopię zapasową nieudostępnione klastrów.<br/><br/> Utwórz kopię zapasową program Exchange Server skonfigurowany do ciągłej replikacji.
**SharePoint 2016**<br/><br/> **Program SharePoint 2013**<br/><br/> **SharePoint 2010** | Program SharePoint 2016 w wersji DPM 2016 i nowszych wersjach.<br/><br/>Maszyna wirtualna na lokalne/platformy Azure | Tworzenie kopii zapasowych farmy, serwer frontonu sieci web.<br/><br/> Odzyskać farmy, bazy danych, aplikacji sieci web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci web.<br/><br/> Nie można utworzyć kopii zapasowej farmy, za pomocą funkcji AlwaysOn dla bazy danych zawartości.
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | Funkcji Hyper-V na 2016 obsługiwane w przypadku programu DPM 2016 i nowszych wersjach.<br/><br/> Lokalnie | **Agent serwera usługi Mab na hoście funkcji Hyper-V**: Utworzyć kopię zapasową całej maszyny wirtualne i pliki danych hosta. Utwórz kopię zapasową maszyn wirtualnych za pomocą magazynu lokalnego, maszyny wirtualne w klastrze za pomocą magazynu CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi Mab na maszynie Wirtualnej gościa**: Utwórz kopię zapasową obciążeń działających na maszynie Wirtualnej. CSVs.<br/><br/> **Odzyskiwanie**: Maszyna wirtualna, odzyskiwanie na poziomie elementu/woluminów/folderów/plików VHD.<br/><br/> **Maszyny wirtualne z systemem Linux**: Utwórz kopię zapasową, gdy funkcja Hyper-V jest uruchomiony w systemie Windows Server 2012 R2 lub nowszy. Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.
**Maszyny wirtualne programu VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> Program DPM 2012 R2 wymaga pakietu zbiorczego aktualizacji 1 dla System Center) | Lokalnie | Utwórz kopię zapasową maszyn wirtualnych programu VMware w magazynie woluminów CSV, systemu plików NFS i siecią SAN.<br/><br/> Odzyskaj całą maszynę Wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie na poziomie elementu folderów/plików dla maszyn wirtualnych Windows tylko.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.


- Należy pamiętać, że klastrowane obciążenia wspierana przez program DPM/serwera usługi Mab powinien w tej samej domenie co program DPM/serwera usługi Mab, lub w podrzędnej/zaufanej domeny. 
- Aby utworzyć kopię zapasową danych w niezaufanych domenach lub grupach roboczych, można użyć uwierzytelniania NTLM/certyfikat.



## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Przegląd](backup-support-matrix-mars-agent.md) co to jest obsługiwana w przypadku agenta usług MARS.
- [Konfigurowanie](backup-azure-microsoft-azure-backup.md) server serwera usługi MAB.
- [Konfigurowanie] (https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
