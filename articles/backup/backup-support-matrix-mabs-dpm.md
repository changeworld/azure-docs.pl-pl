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
ms.openlocfilehash: 0f99042d91738c88a8b673444bb3d4a40602b012
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204169"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Macierz obsługi dla narzędzia Kopia zapasowa Microsoft Azure Backup Server lub programu System Center DPM

Możesz użyć [usługi Azure Backup](backup-overview.md) do tworzenia kopii zapasowych maszyn lokalnych i obciążeń i Azure virtual machines (VMs). Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczenia dotyczące tworzenia kopii zapasowych maszyn za pomocą programu Microsoft Azure Backup Server (MABS) lub System Center Data Protection Manager (DPM) i usługi Azure Backup.

## <a name="about-dpmmabs"></a>Program DPM/serwera usługi Mab — informacje

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) to rozwiązanie enterprise, konfiguruje, który ułatwia tworzenie i zarządza kopii zapasowych i odzyskiwania maszyn enterprise i danych. Tego część [programu System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) pakiet produktów.

Serwera usługi Mab jest produktem serwera, który może służyć do tworzenia kopii zapasowej lokalnych serwerów fizycznych, maszyn wirtualnych i aplikacji działających na nich.

Serwera usługi Mab opiera się na System Center DPM i zapewnia podobne funkcje z kilkoma różnicami:
- Licencja programu System Center nie jest wymagane do uruchamiania serwera usługi MAB.
- Dla serwera usługi Mab i program DPM system Azure oferuje długoterminowego przechowywania kopii zapasowych. Ponadto program DPM umożliwia tworzenie kopii zapasowych danych w celu przechowywania długoterminowego na taśmie. Serwera usługi Mab nie zapewnia tę funkcję.

Pobieranie serwera usługi Mab z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Mogą być uruchamiane lokalnie lub na Maszynie wirtualnej platformy Azure.

Program DPM i serwera usługi Mab obsługuje tworzenie kopii zapasowej szerokiej gamy aplikacji i serwera oraz klienckie systemy operacyjne. Zapewniają one wiele scenariuszy tworzenia kopii zapasowej:

- Kopię zapasową można wykonywać na poziomie komputera przy użyciu kopii zapasowej stanu systemu lub bez systemu operacyjnego.
- Można utworzyć kopię zapasową określonych woluminów, udziałów, folderów i plików.
- Kopię zapasową można wykonywać określone aplikacje przy użyciu ustawień programu app-aware zoptymalizowane.

## <a name="dpmmabs-backup"></a>Kopia zapasowa programu DPM/serwera usługi Mab

Kopię zapasową przy użyciu programu DPM/serwera usługi Mab i Azure Backup działa w następujący sposób:

1. Agent ochrony programu DPM/serwera usługi Mab jest zainstalowany na każdym komputerze, na którym będzie wykonywana kopia zapasowa.
1. Komputery i aplikacje kopię zapasową w magazynie lokalnym w programie DPM/serwera usługi MAB.
1. Agent usługi Microsoft Azure Recovery Services (MARS) jest zainstalowany na program DPM server/serwera usługi MAB.
1. Agenta usług MARS tworzy kopie zapasowe dysków DPM/serwera usługi Mab do tworzenia kopii zapasowej magazynu usługi Recovery Services na platformie Azure przy użyciu usługi Azure Backup.

Informacje dodatkowe:

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Sprawdź, jakie operacje są obsługiwane](backup-support-matrix-mars-agent.md) dla agenta usług MARS.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Agent** | **Location**
--- | --- | ---
**Tworzenie kopii zapasowych obciążeń maszyn lokalnych** | Agent ochrony programu DPM/serwera usługi Mab działa na maszynach, które chcesz utworzyć kopię zapasową.<br/><br/> Agenta usług MARS na serwerze programu DPM/serwera usługi MAB. | Program DPM/serwera usługi Mab musi być uruchomiona w środowisku lokalnym.
**Tworzenie kopii zapasowych maszyn wirtualnych/obciążeń platformy Azure** | Agent ochrony programu DPM/serwera usługi Mab na komputerze chronionym.<br/><br/> Agenta usług MARS na serwerze programu DPM/serwera usługi MAB. | Program DPM/serwera usługi Mab musi działać na Maszynie wirtualnej platformy Azure.

## <a name="supported-deployments"></a>Obsługiwane wdrożenia

Program DPM/MABS można wdrożyć zgodnie z opisem w poniższej tabeli.

**Wdrożenie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
**Wdrożone w środowisku lokalnym** | Serwer fizyczny<br/><br/>Maszyna wirtualna funkcji Hyper-V<br/><br/> Maszyna wirtualna VMware | Jeśli program DPM/serwera usługi Mab jest zainstalowany jako Maszynę wirtualną VMware, tylko kopie zapasowe maszyn wirtualnych VMware i obciążeń, które są uruchomione na tych maszynach wirtualnych.
**Wdrożony jako usługa Azure Stack maszyny Wirtualnej** | Tylko serwera usługi Mab | Program DPM nie może służyć do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure Stack.
**Wdrożony jako maszyna wirtualna platformy Azure** | Chroni maszyny wirtualne platformy Azure i obciążeń, które są uruchomione na tych maszynach wirtualnych | Program DPM/serwera usługi Mab działających na platformie Azure nie można utworzyć kopii maszyn lokalnych.


## <a name="supported-mabs-and-dpm-operating-systems"></a>Obsługiwane systemy operacyjne serwera usługi Mab i program DPM

Usługa Azure Backup można utworzyć kopię zapasową wystąpienia programu DPM/serwera usługi Mab, które działają dowolne z następujących systemów operacyjnych. Systemy operacyjne powinien być uruchomiony z najnowszymi dodatkami service pack i aktualizacjami.

**Scenariusz** | **PROGRAM DPM/SERWERA USŁUGI MAB**
--- | ---
**Serwera usługi Mab na Maszynie wirtualnej platformy Azure** | Windows Server 2012 R2.<br/><br/> Windows 2016 Datacenter.<br/><br/> Windows 2019 Datacenter.<br/><br/> Zaleca się uruchamiania za pomocą obrazu z witryny marketplace.<br/><br/> Minimalna A2 warstwie podstawowa przy użyciu dwóch rdzeni oraz 3,5 GB pamięci RAM.
**Program DPM na maszynie Wirtualnej platformy Azure** | System Center 2012 R2 z aktualizacją Update 3 lub nowszej.<br/><br/> System operacyjny Windows jako [wymagane przez program System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Zaleca się uruchamiania za pomocą obrazu z witryny marketplace.<br/><br/> Minimalna A2 warstwie podstawowa przy użyciu dwóch rdzeni oraz 3,5 GB pamięci RAM.
**Serwera usługi Mab w środowisku lokalnym** | Obsługiwane 64-bitowych systemach operacyjnych:<br/><br/> Serwera usługi Mab w wersji 3 i nowszych: System Windows Server (wersje Standard i Datacenter, Essentials) 2019 r. <br/><br/> Serwera usługi Mab w wersji 2 lub nowszy: System Windows Server 2016 (wersje Standard i Datacenter, Essentials).<br/><br/> Wszystkie wersje serwera usługi Mab: Windows Server 2012 R2.<br/><br/>Wszystkie wersje serwera usługi Mab: Windows Storage Server 2012 R2.
**Program DPM w środowisku lokalnym** | Fizyczny serwer/funkcji Hyper-V maszyny Wirtualnej: System Center 2012 SP1 lub nowszym.<br/><br/> Maszyna wirtualna VMware: System Center 2012 R2 z aktualizacją Update 5 lub nowszym.



## <a name="management-support"></a>Obsługa zarządzania

**Problem** | **Szczegóły**
--- | ---
**Instalacja** | Program DPM/MABS można zainstalować na komputerze wyznaczonym do tego celu.<br/><br/> Nie należy instalować programu DPM/serwera usługi Mab, na kontrolerze domeny, na komputerze z instalacją roli serwera aplikacji, na komputerze, na którym działa program Microsoft Exchange Server lub programu System Center Operations Manager lub w węźle klastra.<br/><br/> [Przejrzyj wszystkie wymagania systemowe programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domeny** | Program DPM/serwera usługi Mab powinien należeć do domeny. Najpierw zainstaluj, a następnie dołącz do programu DPM/serwera usługi Mab do domeny. Przenoszenie programu DPM/serwera usługi Mab do nowej domeny po wdrożeniu nie jest obsługiwane.
**Storage** | Nowoczesny magazyn kopii zapasowych (MB) jest obsługiwane od wersji DPM 2016/serwera usługi Mab w wersji 2 lub nowszy. Nie jest dostępny dla serwera usługi Mab w wersji 1.
**Uaktualnianie serwera usługi Mab** | Można bezpośrednio zainstalować serwera usługi Mab w wersji 3 lub uaktualnienie do serwera usługi Mab w wersji 3 z serwera usługi Mab w wersji 2. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Przenoszenie serwera usługi Mab** | Przenoszenie serwera usługi Mab do nowego serwera przy zachowaniu magazynu jest obsługiwane, jeśli używasz MB.<br/><br/> Serwer musi mieć taką samą nazwę jak oryginał. Nie można zmienić nazwy, jeśli chcesz zachować tej samej puli magazynu, a następnie użyć tej samej bazy danych serwera usługi Mab do przechowywania punktów odzyskiwania danych.<br/><br/> Ponieważ musisz przywrócić ją, należy kopię zapasową bazy danych serwera usługi MAB.


## <a name="mabs-support-on-azure-stack"></a>Obsługa serwera usługi Mab w usłudze Azure Stack

Można wdrożyć serwera usługi Mab na Maszynie wirtualnej platformy Azure Stack, tak, aby można było zarządzać tworzeniem kopii zapasowych maszyn wirtualnych platformy Azure Stack i obciążeń z jednej lokalizacji.

**Składnik** | **Szczegóły**
--- | ---
**Serwera usługi Mab w usłudze Azure Stack maszyny Wirtualnej** | Co najmniej rozmiaru A2. Zaleca się, że uruchomieniu z obrazem systemu Windows Server 2012 R2 lub Windows Server 2016 w witrynie Azure Marketplace.<br/><br/> Nie należy instalować czymkolwiek na maszynie Wirtualnej z serwera usługi MAB.
**Magazyn serwera usługi Mab** | Używaj oddzielnego konta magazynu dla maszyny Wirtualnej z serwera usługi MAB. Agenta usług MARS systemem serwera usługi Mab wymaga tymczasowego magazynu dla lokalizacji pamięci podręcznej i do przechowywania danych przywróconych z chmury.
**Pula magazynów serwera usługi Mab** | Rozmiar puli magazynów serwera usługi Mab zależy od liczby i rozmiaru dysków dołączonych do maszyny Wirtualnej serwera usługi MAB. Każdego rozmiaru maszyny Wirtualnej platformy Azure Stack ma maksymalną liczbę dysków. Na przykład A2 to cztery dyski.
**Przechowywanie serwera usługi Mab** | Nie zawierają kopii zapasowej danych na dyskach lokalnych serwera usługi Mab przez więcej niż pięć dni.
**Skalowanie serwera usługi Mab** | Aby skalować wdrożenie w górę, można zwiększyć rozmiar maszyny Wirtualnej serwera usługi MAB. Możesz na przykład zmienić od A do serii D.<br/><br/> Może również zagwarantować, że możesz teraz Odciążanie danych przy użyciu kopii zapasowej na platformie Azure regularnie. Jeśli to konieczne, możesz wdrożyć dodatkowe serwery serwera usługi MAB.
**.NET framework na serwera usługi Mab** | Maszyna wirtualna serwera usługi Mab musi .NET Framework w wersji 3.3 z dodatkiem SP1 lub nowszy jest zainstalowany na nim.
**Domeny serwera usługi Mab** | Maszyna wirtualna serwera usługi Mab muszą być przyłączone do domeny. Użytkownik domeny z uprawnieniami administratora, należy zainstalować serwera usługi Mab na maszynie Wirtualnej.
**Usługa Azure backup dane maszyny Wirtualnej stosu** | Można utworzyć kopię zapasową plików, folderów i aplikacji.
**Obsługiwane kopii zapasowej** | Te systemy operacyjne są obsługiwane dla maszyn wirtualnych, które chcesz utworzyć kopię zapasową:<br/><br/> Windows Server półroczny kanał (Datacenter, Enterprise i Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Obsługa programu SQL Server dla maszyn wirtualnych platformy Azure Stack** | Back up SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1.<br/><br/> Tworzenie kopii zapasowych i odzyskiwanie bazy danych.
**Obsługa programu SharePoint dla maszyn wirtualnych platformy Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Tworzenie kopii zapasowych i odzyskiwania farmy, bazy danych, serwer sieci Web i serwera sieci web.
**Wymagania dotyczące sieci dla kopii zapasowych maszyn wirtualnych** | Wszystkie maszyny wirtualne w obciążeniu usługi Azure Stack musi należeć do tej samej sieci wirtualnej i należą do tej samej subskrypcji.

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
Połączono | Aktywne | Tworzenie kopii zapasowej dysku programu DPM/serwera usługi MAB.<br/><br/> Tworzenie kopii zapasowej na platformie Azure.<br/><br/> Przywracanie z dysku.<br/><br/> Przywracanie z platformy Azure.
Połączono | Wygasłe/anulowanie aprowizacji | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja utraciła ważność, można przywrócić z dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można przywrócić z dysku lub na platformie Azure. Punkty odzyskiwania platformy Azure zostaną usunięte.
Brak łączności przez więcej niż 15 dni | Aktywne | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Można przywrócić z dysku lub na platformie Azure.
Brak łączności przez więcej niż 15 dni | Wygasłe/anulowanie aprowizacji | Nie wykonywanie kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja utraciła ważność, można przywrócić z dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można przywrócić z dysku lub na platformie Azure. Punkty odzyskiwania platformy Azure zostaną usunięte.

## <a name="dpmmabs-storage-support"></a>Program DPM/serwera usługi Mab storage — pomoc techniczna

Kopia zapasowa jest tworzona DPM/serwera usługi Mab dane są przechowywane na magazyn na dysku lokalnym.

**Storage** | **Szczegóły**
--- | ---
**MB** | Nowoczesny magazyn kopii zapasowych (MB) jest obsługiwane od wersji DPM 2016/serwera usługi Mab w wersji 2 lub nowszy. Nie jest dostępny dla serwera usługi Mab w wersji 1.
**Magazyn serwera usługi Mab na maszynie Wirtualnej platformy Azure** | Dane są przechowywane na dyskach platformy Azure dołączonych do maszyny Wirtualnej programu DPM/serwera usługi Mab i które są zarządzane w programie DPM/serwera usługi MAB. Liczba dysków, które mogą służyć do puli magazynów programu DPM/serwera usługi Mab jest ograniczona przez rozmiar maszyny Wirtualnej.<br/><br/> A2 MASZYNY WIRTUALNEJ: 4 dyski; A3 MASZYNY WIRTUALNEJ: 8 dysków; A4 MASZYNY WIRTUALNEJ: 16 dysków o maksymalnym rozmiarze 1 TB dla każdego dysku. Ta opcja określa puli całkowita ilość miejsca do tworzenia kopii zapasowej, który jest dostępny.<br/><br/> Ilość danych, które można utworzyć kopię zapasową, zależy od liczby i rozmiaru dysków dołączonych.
**Przechowywanie danych serwera usługi Mab na maszynie Wirtualnej platformy Azure** | Firma Microsoft zaleca zachowywanie danych przez jeden dzień na dysku platformy Azure programu DPM/serwera usługi Mab i tworzenie kopii zapasowej z programu DPM/serwera usługi Mab do magazynu dla dłuższy okres przechowywania. Ten sposób można chronić większej ilości danych dzięki przeniesieniu ich do usługi Azure Backup.


### <a name="modern-backup-storage-mbs"></a>Nowoczesny magazyn kopii zapasowych (MB)
Od wersji DPM 2016/serwera usługi Mab w wersji 2 (uruchomiony w systemie Windows Server 2016) i nowszych możesz korzystać z zalet nowoczesnego magazynu kopii zapasowych (MB).

- MB kopie zapasowe są przechowywane na dysku systemu plików (ReFS).
- MB używa klonowania szybsze wykonywanie kopii zapasowych i bardziej efektywnego wykorzystania miejsca do magazynowania bloków w systemie plików ReFS.
- Po dodaniu woluminy lokalne puli magazynów programu DPM/serwera usługi Mab, można je skonfigurować za pomocą litery dysku. Następnie należy skonfigurować Magazyn obciążenia na różnych woluminach.
- Podczas tworzenia grup ochrony w celu tworzenia kopii zapasowych danych programu DPM/serwera usługi Mab, należy wybrać dysk którego chcesz użyć. Na przykład przechowywać kopie zapasowe bazy danych SQL lub inne wysokiej dysku obciążeń o wysokiej wydajności operacji We/Wy, a przechowywania obciążeń, które są rzadziej kopie na niższym dysku wydajności.


## <a name="supported-backups-to-mabs"></a>Obsługiwane tworzenie kopii zapasowej serwera usługi Mab

W poniższej tabeli podsumowano, co może można utworzyć kopie zapasowe serwera usługi Mab maszyn lokalnych i maszyn wirtualnych platformy Azure.


**Tworzenie kopii zapasowych** | **Wersje** | **SERWERA USŁUGI MAB** | **Szczegóły** |
--- | --- | --- | --- |
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32 64-bitowy) | MABS v3, v2 | W środowisku lokalnym. | Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane.<br/><br/> Woluminy muszą mieć co najmniej 1 GB i systemu plików NTFS. |
**System Windows Server 2016 (Datacenter, Standard i nie Nano)**<br/><br/> 32-64-bitowych | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> Aby deduplikowane woluminy są obsługiwane. |
**Windows Server 2012 R2 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Usługa Azure VMprotection**: Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane. |
**Windows Server 2012 z dodatkiem SP1 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | MABS v3, v2 <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane. |
**Windows 2008 R2 z dodatkiem SP1 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | Obsługiwane przez serwera usługi Mab w wersji 3, wersja 2.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. | Maszyna wirtualna środowiska lokalne/platformę Azure. |   **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane. |
**Windows 2008 R2 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | Dla serwera usługi Mab w wersji 2 lub 3 systemu operacyjnego musi działać z dodatkiem SP1. | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane. |
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 32-64-bitowych | MABS v2, v3 | Serwera usługi Mab v2, v3 jest obsługiwana w przypadku wdrożenia serwera usługi Mab jako Maszynę wirtualną VMware.<br/><br/> Nie jest obsługiwane dla serwera usługi Mab uruchomionych na maszynie Wirtualnej platformy Azure. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego. |
**Windows Storage Server 2008** | MABS v2, v3 | Serwera usługi Mab, jako fizyczny serwer/funkcji Hyper-V VM lokalnych. <br/><br/> Nie jest obsługiwane dla serwera usługi Mab uruchomionych na maszynie Wirtualnej platformy Azure. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**SQL Server 2017** | Serwera usługi Mab w wersji 3 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany. |
**SQL Server 2016/2016 z dodatkiem SP1** | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany. |
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2 | W środowisku lokalnym. | Utwórz kopię zapasową autonomiczny serwer programu Exchange, bazy danych w grupie DAG.<br/><br/> Odzyskaj Skrzynka pocztowa, bazy danych skrzynek pocztowych w grupie DAG.<br/><br/> System plików reFS nie jest obsługiwane.<br/><br/> Utwórz kopię zapasową nieudostępnione klastrów.<br/><br/> Utwórz kopię zapasową program Exchange Server skonfigurowany do ciągłej replikacji. |
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2 | Maszyna wirtualna środowiska lokalne/platformę Azure. | Tworzenie kopii zapasowych farmy, serwer frontonu sieci web.<br/><br/> Odzyskać farmy, bazy danych, aplikacji sieci web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci web.<br/><br/> Nie można utworzyć kopii zapasowej farmy, za pomocą programu SQL Server AlwaysOn dla bazy danych zawartości. |
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | MABS v3, v2 | W środowisku lokalnym. | **Agent serwera usługi Mab na hoście funkcji Hyper-V**: Utworzyć kopię zapasową całej maszyny wirtualne i pliki danych hosta. Utwórz kopię zapasową maszyn wirtualnych za pomocą magazynu lokalnego, maszyny wirtualne w klastrze za pomocą magazynu CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi Mab na maszynie Wirtualnej gościa**: Utwórz kopię zapasową obciążeń działających na maszynie Wirtualnej. CSVs.<br/><br/> **Odzyskiwanie**: Maszyna wirtualna, odzyskiwanie na poziomie elementu/woluminów/folderów/plików VHD.<br/><br/> **Maszyny wirtualne z systemem Linux**: Utwórz kopię zapasową, gdy funkcja Hyper-V jest uruchomiony w systemie Windows Server 2012 R2 i nowszych. Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera. |
**Maszyny wirtualne programu VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 | W środowisku lokalnym. | Utwórz kopię zapasową maszyn wirtualnych programu VMware w magazynie woluminów CSV, systemu plików NFS i siecią SAN.<br/><br/> Odzyskaj całą maszynę Wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie na poziomie elementu folderów/plików dla maszyn wirtualnych Windows tylko.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera. |



## <a name="supported-backups-to-dpm"></a>Obsługiwane tworzenie kopii zapasowej programu DPM

W poniższej tabeli podsumowano, co może być kopii zapasowej program DPM z maszyn lokalnych i maszyn wirtualnych platformy Azure.



**Tworzenie kopii zapasowych** | **DPM** | **Szczegóły**
--- | --- | ---
**Windows 10<br/>Windows 8.1<br/>Windows 8<br/>Windows 7**<br/><br/>(32 64-bitowy) | Tylko lokalnie.<br/><br/> Do utworzenia kopii zapasowej systemu Windows 10 przy użyciu programu DPM 2012 R2, zaleca się zainstalowanie [aktualizacji 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Wolumin/udział/folder/pliku.<br/><br/> Aby deduplikowane woluminy są obsługiwane.<br/><br/> Woluminy muszą mieć co najmniej 1 GB i systemu plików NTFS.
**System Windows Server 2016 (Datacenter, Standard i nie Nano)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure.<br/><br/> Tylko program DPM 2016.| Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> Aby deduplikowane woluminy są obsługiwane.
**Windows Server 2012 R2 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.<br/><br/> Woluminy z deduplikacją, obsługiwane za pomocą programu DPM 2012 R2 i nowszych.
**Windows Server 2012 z dodatkiem SP1 (wersja Datacenter i Standard)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.<br/><br/> Woluminy z deduplikacją, obsługiwane za pomocą programu DPM 2012 R2 i nowszych.
**Windows 2008 R2 z dodatkiem SP1 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | Maszyna wirtualna środowiska lokalne/platformę Azure.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) musi być zainstalowany. |   **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.<br/><br/> **Ochronę maszyny Wirtualnej platformy Azure**: Wolumin/udział/folder/pliku.
**Windows 2008 R2 (Standard i Enterprise)**<br/><br/> 32-64-bitowych | W środowisku lokalnym.<br/><br/> Nie można zainstalować programu DPM jako Maszynę wirtualną VMware.<br/><br/> Program DPM działający na Maszynie wirtualnej platformy Azure nie jest obsługiwane. | **Ochrona lokalnych**: Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 32-64-bitowych | Tylko lokalnie.<br/><br/> Program DPM jest obsługiwane podczas uruchamiania jako Maszynę wirtualną VMware. Działa jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V nie jest obsługiwane. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**Windows Storage Server 2008** | Program DPM w środowisku lokalnym systemem jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V. | Wolumin/udział/folder/pliku System — stan/bez systemu operacyjnego.
**SQL Server 2017** | KONSOLA SAC PROGRAMU DPM; Program DPM 2016 uruchomiony, pakietu zbiorczego aktualizacji w górę 5 lub nowszym.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2016 z dodatkiem SP1** | Nieobsługiwane w przypadku programu DPM 2012 R2; Obsługiwane w przypadku SAC programu DPM, program DPM 2016 uruchomiony pakiet zbiorczy aktualizacji 4 lub nowszej.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2016** | Nie jest obsługiwane dla programu DPM 2012 R2. Obsługiwane w przypadku SAC programu DPM, program DPM 2016 z pakietem zbiorczym aktualizacji 2 lub nowszy.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | Program SQL Server 2014 przy użyciu programu DPM 2012 R2 z pakietem zbiorczym aktualizacji 4 lub nowszy.<br/><br/> Maszyna wirtualna środowiska lokalne/platformę Azure.| Utwórz kopię zapasową bazy danych programu SQL Server.<br/><br/> Obsługiwane backup klastra programu SQL Server.<br/><br/>Bazy danych przechowywane na woluminach CSV jest nieobsługiwany.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Dla programów Exchange 2016 Program DPM 2012 R2 wymaga pakietu zbiorczego aktualizacji 9 lub nowszy.<br/><br/> Lokalnie | Utwórz kopię zapasową autonomiczny serwer programu Exchange, bazy danych w grupie DAG.<br/><br/> Odzyskaj Skrzynka pocztowa, bazy danych skrzynek pocztowych w grupie DAG.<br/><br/> System plików reFS nie jest obsługiwane.<br/><br/> Utwórz kopię zapasową nieudostępnione klastrów.<br/><br/> Utwórz kopię zapasową program Exchange Server skonfigurowany do ciągłej replikacji.
**SharePoint 2016**<br/><br/> **SharePoint 2013**<br/><br/> **SharePoint 2010** | Program SharePoint 2016 w wersji DPM 2016 i nowszych.<br/><br/>Maszyna wirtualna środowiska lokalne/platformę Azure. | Tworzenie kopii zapasowych farmy, serwer frontonu sieci web.<br/><br/> Odzyskać farmy, bazy danych, aplikacji sieci web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci web.<br/><br/> Nie można utworzyć kopii zapasowej farmy, za pomocą programu SQL Server AlwaysOn dla bazy danych zawartości.
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/Standard)<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | Funkcji Hyper-V w systemie 2016 obsługiwane przez program DPM 2016 lub nowszy.<br/><br/> W środowisku lokalnym. | **Agent serwera usługi Mab na hoście funkcji Hyper-V**: Utworzyć kopię zapasową całej maszyny wirtualne i pliki danych hosta. Utwórz kopię zapasową maszyn wirtualnych za pomocą magazynu lokalnego, maszyny wirtualne w klastrze za pomocą magazynu CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi Mab na maszynie Wirtualnej gościa**: Utwórz kopię zapasową obciążeń działających na maszynie Wirtualnej. CSVs.<br/><br/> **Odzyskiwanie**: Maszyna wirtualna, odzyskiwanie na poziomie elementu/woluminów/folderów/plików VHD.<br/><br/> **Maszyny wirtualne z systemem Linux**: Utwórz kopię zapasową, gdy funkcja Hyper-V jest uruchomiony w systemie Windows Server 2012 R2 i nowszych. Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.
**Maszyny wirtualne programu VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2 <br/><br/> Program DPM 2012 R2 wymaga pakietu zbiorczego aktualizacji 1 dla System Center) <br/><br/>W środowisku lokalnym. | Utwórz kopię zapasową maszyn wirtualnych programu VMware w magazynie woluminów CSV, systemu plików NFS i siecią SAN.<br/><br/> Odzyskaj całą maszynę Wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie na poziomie elementu folderów/plików dla maszyn wirtualnych Windows tylko.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie maszyn wirtualnych systemu Linux jest dla całego komputera.


- Należy pamiętać, że klastrowane obciążenia wspierana przez program DPM/serwera usługi Mab powinna być w tej samej domenie co program DPM/serwera usługi Mab lub w zaufanej/podrzędny domeny.
- Aby utworzyć kopię zapasową danych w niezaufanych domenach lub grupach roboczych, można użyć uwierzytelniania NTLM/certyfikat.



## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Przegląd](backup-support-matrix-mars-agent.md) co to jest obsługiwana w przypadku agenta usług MARS.
- [Konfigurowanie](backup-azure-microsoft-azure-backup.md) server serwera usługi MAB.
- [Konfigurowanie programu DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
