---
title: Macierz obsługi dla Microsoft Azure Backup Server i System Center DPM
description: Ten artykuł zawiera podsumowanie Azure Backup pomocy technicznej w przypadku używania serwera Microsoft Azure Backup lub programu System Center DPM do tworzenia kopii zapasowych zasobów lokalnych i maszyn wirtualnych platformy Azure.
author: dcurwin
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: d14cd996fe0e5a67cc3b554fe1e12146cd6b8e1c
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981082"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Macierz obsługi dla tworzenia kopii zapasowych za pomocą serwera Microsoft Azure Backup lub programu System Center DPM

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące tworzenia kopii zapasowych maszyn przy użyciu Microsoft Azure Backup Server (serwera usługi MAB) lub System Center Data Protection Manager (DPM) i Azure Backup.

## <a name="about-dpmmabs"></a>Informacje o programie DPM/serwera usługi MAB

[Program System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) to rozwiązanie korporacyjne służące do konfigurowania i wykonywania kopii zapasowych i odzyskiwania maszyn i danych przedsiębiorstwa oraz zarządzania nimi. Jest częścią pakietu produktów [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) .

SERWERA usługi MAB to produkt serwerowy, który może służyć do tworzenia kopii zapasowych lokalnych serwerów fizycznych, maszyn wirtualnych i działających na nich aplikacji.

SERWERA usługi MAB jest oparta na programie System Center DPM i oferuje podobną funkcjonalność z kilkoma różnicami:

- Do uruchomienia serwera usługi MAB nie jest wymagana żadna licencja programu System Center.
- W przypadku programów serwera usługi MAB i DPM platforma Azure zapewnia długoterminowe przechowywanie kopii zapasowych. Ponadto program DPM umożliwia tworzenie kopii zapasowych danych w celu przechowywania długoterminowego na taśmie. SERWERA usługi MAB nie zapewnia tej funkcji.
- Można utworzyć kopię zapasową podstawowego serwera DPM przy użyciu pomocniczego serwera programu DPM. Serwer pomocniczy będzie chronić bazę danych serwera podstawowego i repliki źródła danych przechowywane na serwerze podstawowym. Jeśli serwer podstawowy ulegnie awarii, serwer pomocniczy może nadal chronić obciążenia chronione przez serwer podstawowy, dopóki serwer podstawowy nie będzie ponownie dostępny.  SERWERA usługi MAB nie zapewnia tej funkcji.

Pobierasz serwera usługi MAB z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=57520). Może być uruchamiany lokalnie lub na maszynie wirtualnej platformy Azure.

Program DPM i serwera usługi MAB obsługują tworzenie kopii zapasowych różnorodnych aplikacji oraz serwerów i systemów operacyjnych klienta. Zapewniają one wiele scenariuszy tworzenia kopii zapasowych:

- Można utworzyć kopię zapasową na poziomie komputera przy użyciu kopii zapasowej stanu systemu lub bezsystemowego.
- Można tworzyć kopie zapasowe określonych woluminów, udziałów, folderów i plików.
- Można tworzyć kopie zapasowe określonych aplikacji przy użyciu zoptymalizowanych ustawień aplikacji.

## <a name="dpmmabs-backup"></a>Kopia zapasowa programu DPM/serwera usługi MAB

Tworzenie kopii zapasowej przy użyciu programu DPM/serwera usługi MAB i Azure Backup działa następująco:

1. Na każdym komputerze, na którym zostanie utworzona kopia zapasowa, jest zainstalowany agent ochrony programu DPM/serwera usługi MAB.
1. Kopie zapasowe maszyn i aplikacji są przechowywane w magazynie lokalnym w programie DPM/serwera usługi MAB.
1. Agent Microsoft Azure Recovery Services (MARS) jest zainstalowany na serwerze DPM/serwera usługi MAB.
1. Agent MARS tworzy kopię zapasową dysków DPM/serwera usługi MAB w magazynie kopii zapasowych Recovery Services na platformie Azure przy użyciu Azure Backup.

Informacje dodatkowe:

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Sprawdź, co jest obsługiwane](backup-support-matrix-mars-agent.md) przez agenta Mars.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Odczynnik** | **Lokalizacja**
--- | --- | ---
**Tworzenie kopii zapasowych maszyn/obciążeń lokalnych** | Na maszynach, dla których chcesz utworzyć kopię zapasową, działa Agent ochrony programu DPM/serwera usługi MAB.<br/><br/> Agent MARS na serwerze DPM/serwera usługi MAB.<br/> Minimalna wersja agenta Microsoft Azure Recovery Services lub agenta Azure Backup, wymagana do włączenia tej funkcji, to 2.0.8719.0.  | Program DPM/serwera usługi MAB musi działać lokalnie.

## <a name="supported-deployments"></a>Obsługiwane wdrożenia

Program DPM/serwera usługi MAB można wdrożyć zgodnie z podsumowaniem w poniższej tabeli.

**Wdrażanie** | **Pomocy** | **Uzyskać**
--- | --- | ---
**Wdrożone lokalnie** | Serwer fizyczny<br/><br/>Maszyna wirtualna funkcji Hyper-V<br/><br/> Maszyna wirtualna VMware | Jeśli program DPM/serwera usługi MAB jest instalowany jako maszyna wirtualna VMware, tworzy on tylko kopie zapasowe maszyn wirtualnych VMware i obciążeń uruchomionych na tych maszynach wirtualnych.
**Wdrożono jako maszynę wirtualną Azure Stack** | Tylko serwera usługi MAB | Programu DPM nie można używać do tworzenia kopii zapasowych maszyn wirtualnych Azure Stack.
**Wdrożono jako maszynę wirtualną platformy Azure** | Ochrona maszyn wirtualnych platformy Azure i obciążeń uruchomionych na tych maszynach wirtualnych | W programie DPM/serwera usługi MAB działającym na platformie Azure nie można tworzyć kopii zapasowych maszyn lokalnych.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Obsługiwane systemy operacyjne serwera usługi MAB i DPM

Azure Backup może tworzyć kopie zapasowe wystąpień programu DPM/serwera usługi MAB, które działają w dowolnym z następujących systemów operacyjnych. Systemy operacyjne powinny mieć zainstalowane najnowsze dodatki Service Pack i aktualizacje.

**Scenariusz** | **DPM/SERWERA USŁUGI MAB**
--- | ---
**SERWERA usługi MAB na maszynie wirtualnej platformy Azure** | Windows Server 2012 R2.<br/><br/> Centrum danych systemu Windows 2016.<br/><br/> Centrum danych systemu Windows 2019.<br/><br/> Zalecamy rozpoczęcie od obrazu z portalu Marketplace.<br/><br/> Minimalny standard a2 z dwoma rdzeniami i 3,5 GB pamięci RAM.
**Program DPM na maszynie wirtualnej platformy Azure** | System Center 2012 R2 z aktualizacją Update 3 lub nowszą.<br/><br/> System operacyjny Windows [wymagany przez program System Center](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).<br/><br/> Zalecamy rozpoczęcie od obrazu z portalu Marketplace.<br/><br/> Minimalny standard a2 z dwoma rdzeniami i 3,5 GB pamięci RAM.
**SERWERA usługi MAB lokalnie** | Obsługiwane 64-bitowe systemy operacyjne:<br/><br/> SERWERA usługi MAB v3 i nowsze: Windows Server 2019 (standard, Datacenter, Essentials). <br/><br/> SERWERA usługi MAB v2 i nowsze: Windows Server 2016 (standard, Datacenter, Essentials).<br/><br/> Wszystkie wersje serwera usługi MAB: Windows Server 2012 R2.<br/><br/>Wszystkie wersje serwera usługi MAB: Windows Storage Server 2012 R2.
**Lokalna DPM** | Serwer fizyczny/maszyna wirtualna funkcji Hyper-V: System Center 2012 z dodatkiem SP1 lub nowszy.<br/><br/> Maszyna wirtualna VMware: System Center 2012 R2 z aktualizacją Update 5 lub nowszą.

## <a name="management-support"></a>Obsługa zarządzania

**Wykonaj** | **Uzyskać**
--- | ---
**Instalacja** | Zainstaluj program DPM/serwera usługi MAB na maszynie z pojedynczym przeznaczeniem.<br/><br/> Nie należy instalować programu DPM/serwera usługi MAB na kontrolerze domeny na komputerze z instalacją roli serwera aplikacji, na komputerze z uruchomionym programem Microsoft Exchange Server lub System Center Operations Manager lub w węźle klastra.<br/><br/> Zapoznaj się ze [wszystkimi wymaganiami systemowymi programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server).
**Domeny** | Program DPM/serwera usługi MAB powinien być przyłączony do domeny. Najpierw zainstaluj program DPM/serwera usługi MAB do domeny. Przeniesienie programu DPM/serwera usługi MAB do nowej domeny po wdrożeniu nie jest obsługiwane.
**Chowan** | Nowoczesne magazyny kopii zapasowych (MB) jest obsługiwane przez program DPM 2016/serwera usługi MAB v2 i nowsze. Nie jest on dostępny dla serwera usługi MAB v1.
**SERWERA usługi MAB uaktualnienie** | Można bezpośrednio zainstalować serwera usługi MAB v3 lub uaktualnić do serwera usługi MAB v3 z wersji serwera usługi MAB v2. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Przesuwanie serwera usługi MAB** | Przeniesienie serwera usługi MAB na nowy serwer podczas zachowywania magazynu jest obsługiwane, jeśli używasz MB.<br/><br/> Serwer musi mieć taką samą nazwę jak oryginalna. Nie można zmienić nazwy, jeśli chcesz zachować tę samą pulę magazynów i użyć tej samej bazy danych serwera usługi MAB do przechowywania punktów odzyskiwania danych.<br/><br/> Konieczna będzie kopia zapasowa bazy danych serwera usługi MAB, ponieważ należy ją przywrócić.

## <a name="mabs-support-on-azure-stack"></a>Obsługa serwera usługi MAB w Azure Stack

Można wdrożyć serwera usługi MAB na maszynie wirtualnej Azure Stack, aby można było zarządzać tworzeniem kopii zapasowych Azure Stack maszyn wirtualnych i obciążeń z jednej lokalizacji.

**Składnika** | **Uzyskać**
--- | ---
**SERWERA usługi MAB na maszynie wirtualnej Azure Stack** | Co najmniej rozmiar a2. Zalecamy rozpoczęcie od obrazu systemu Windows Server 2012 R2 lub Windows Server 2016 z witryny Azure Marketplace.<br/><br/> Nie należy instalować żadnych innych elementów na maszynie wirtualnej serwera usługi MAB.
**Magazyn serwera usługi MAB** | Użyj oddzielnego konta magazynu dla maszyny wirtualnej serwera usługi MAB. Agent MARS uruchomiony w systemie serwera usługi MAB potrzebuje magazynu tymczasowego dla lokalizacji pamięci podręcznej oraz do przechowywania danych przywróconych z chmury.
**Pula magazynu serwera usługi MAB** | Rozmiar puli magazynów serwera usługi MAB zależy od liczby i rozmiaru dysków dołączonych do maszyny wirtualnej serwera usługi MAB. Każdy Azure Stack rozmiaru maszyny wirtualnej ma maksymalną liczbę dysków. Na przykład a2 to cztery dyski.
**Przechowywanie serwera usługi MAB** | Nie przechowuj kopii zapasowych danych na lokalnych dyskach serwera usługi MAB przez ponad pięć dni.
**Skalowanie w górę serwera usługi MAB** | Aby skalować wdrożenie w górę, można zwiększyć rozmiar maszyny wirtualnej serwera usługi MAB. Na przykład można zmienić z serii do D.<br/><br/> Możesz również upewnić się, że dane z kopii zapasowej są wykonywane regularnie na platformie Azure. W razie potrzeby można wdrożyć dodatkowe serwery serwera usługi MAB.
**.NET Framework w serwera usługi MAB** | Maszyna wirtualna serwera usługi MAB wymaga .NET Framework 3,3 z dodatkiem SP1 lub nowszą.
**SERWERA usługi MAB domenę** | Maszyna wirtualna serwera usługi MAB musi być przyłączona do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować serwera usługi MAB na maszynie wirtualnej.
**Azure Stack kopii zapasowej danych maszyny wirtualnej** | Można tworzyć kopie zapasowe plików, folderów i aplikacji.
**Obsługiwana kopia zapasowa** | Te systemy operacyjne są obsługiwane w przypadku maszyn wirtualnych, których kopia zapasowa ma zostać utworzona:<br/><br/> Półroczny kanał systemu Windows Server (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server obsługa Azure Stack maszyn wirtualnych** | Wykonaj kopię zapasową SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Tworzenie kopii zapasowej i odzyskiwanie bazy danych.
**Obsługa programu SharePoint na potrzeby maszyn wirtualnych Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Tworzenie kopii zapasowej i odzyskiwanie farmy, bazy danych, frontonu i serwera sieci Web.
**Wymagania sieciowe dotyczące kopii zapasowych maszyn wirtualnych** | Wszystkie maszyny wirtualne w obciążeniu Azure Stack muszą należeć do tej samej sieci wirtualnej i należeć do tej samej subskrypcji.

## <a name="dpmmabs-networking-support"></a>Obsługa sieci programu DPM/serwera usługi MAB

### <a name="url-access"></a>Dostęp do adresu URL

Serwer DPM/serwera usługi MAB potrzebuje dostępu do tych adresów URL:

- http://www.msftncsi.com/ncsi.txt
- *. Microsoft.com
- *. WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Połączenie programu DPM/serwera usługi MAB z usługą Azure Backup

Aby kopie zapasowe działały prawidłowo, wymagana jest łączność z usługą Azure Backup i subskrypcja platformy Azure powinna być aktywna. W poniższej tabeli przedstawiono zachowanie, jeśli te dwa kwestie nie wystąpią.

**SERWERA usługi MAB na platformę Azure** | **Ramach** | **Tworzenie kopii zapasowej/przywracanie**
--- | --- | ---
Łączona | Wyprzedzeni | Utwórz kopię zapasową na dysku programu DPM/serwera usługi MAB.<br/><br/> Utwórz kopię zapasową na platformie Azure.<br/><br/> Przywróć z dysku.<br/><br/> Przywróć z platformy Azure.
Łączona | Wygasłe lub anulowano obsługę administracyjną | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, możesz przywrócić ją z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można jej przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.
Brak łączności przez ponad 15 dni | Wyprzedzeni | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Można przywrócić z dysku lub platformy Azure.
Brak łączności przez ponad 15 dni | Wygasłe lub anulowano obsługę administracyjną | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, możesz przywrócić ją z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można jej przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.

## <a name="dpmmabs-storage-support"></a>Obsługa magazynu programu DPM/serwera usługi MAB

Dane, których kopię zapasową utworzono w programie DPM/serwera usługi MAB, są przechowywane na dysku lokalnym.

**Chowan** | **Uzyskać**
--- | ---
**MB** | Nowoczesne magazyny kopii zapasowych (MB) jest obsługiwane przez program DPM 2016/serwera usługi MAB v2 i nowsze. Nie jest on dostępny dla serwera usługi MAB v1.
**SERWERA usługi MAB magazynu na maszynie wirtualnej platformy Azure** | Dane są przechowywane na dyskach platformy Azure dołączonych do maszyny wirtualnej programu DPM/serwera usługi MAB, które są zarządzane w programie DPM/serwera usługi MAB. Liczba dysków, które mogą być używane dla puli magazynów programu DPM/serwera usługi MAB, jest ograniczona przez rozmiar maszyny wirtualnej.<br/><br/> Maszyna wirtualna a2:4 dyski; Maszyna wirtualna A3:8 dysków; Maszyna wirtualna A4:16 dysków o maksymalnym rozmiarze 1 TB dla każdego dysku. Ta wartość określa łączną pulę magazynów kopii zapasowych, która jest dostępna.<br/><br/> Ilość danych, dla których można utworzyć kopię zapasową, zależy od liczby i rozmiaru dołączonych dysków.
**Przechowywanie danych serwera usługi MAB na maszynie wirtualnej platformy Azure** | Zalecamy zachowywanie danych przez jeden dzień na dysku platformy Azure DPM/serwera usługi MAB i wykonywanie kopii zapasowych z poziomu programu DPM/serwera usługi MAB do magazynu w celu dłuższego przechowywania. W ten sposób można chronić większą ilość danych, przenosząc ją do Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Nowoczesny magazyn kopii zapasowych (MB)

W programie DPM 2016/serwera usługi MAB v2 (uruchomionym w systemie Windows Server 2016) lub nowszym można korzystać z nowoczesnego magazynu kopii zapasowych (MB).

- Kopie zapasowe MB są przechowywane na dysku systemu plików ReFS.
- MB używa klonowania bloków systemu plików ReFS do szybszego tworzenia kopii zapasowych i wydajniejszego wykorzystywania miejsca do magazynowania.
- Podczas dodawania woluminów do lokalnej puli magazynów programu DPM/serwera usługi MAB należy je skonfigurować przy użyciu liter dysków. Następnie można skonfigurować magazyn obciążeń na różnych woluminach.
- Podczas tworzenia grup ochrony w celu wykonywania kopii zapasowych danych w programie DPM/serwera usługi MAB wybierz dysk, którego chcesz użyć. Można na przykład przechowywać kopie zapasowe dla programu SQL lub innych obciążeń o dużej liczbie operacji we/wy na dysku o wysokiej wydajności, a także przechowywać obciążenia, których kopie zapasowe są rzadziej wykonywane na niższym dysku wydajności.

## <a name="supported-backups-to-mabs"></a>Obsługiwane kopie zapasowe do serwera usługi MAB

Poniższa tabela zawiera podsumowanie elementów, których kopia zapasowa ma zostać utworzona w celu serwera usługi MAB z maszyn lokalnych i maszyn wirtualnych platformy Azure.

**Kopii zapasowej** | **Wersje** | **SERWERA usługi MAB** | **Uzyskać** |
--- | --- | --- | --- |
**Windows 10 @ no__t-1Windows 8.1 @ no__t-2Windows 8 @ no__t-3Windows 7**<br/><br/>(32/64-bitowe) | SERWERA usługi MAB v3, wersja 2 | Lokalnie. | Wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane.<br/><br/> Woluminy muszą mieć co najmniej 1 GB i NTFS. |
**Windows Server 2016 (Datacenter, standard, nie nano)**<br/><br/> 64/32-bitowy | SERWERA usługi MAB v3, wersja 2 | Lokalna/maszyna wirtualna platformy Azure.| Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> Woluminy z deduplikacją są obsługiwane. |
**Windows Server 2012 R2 (Datacenter i standard)**<br/><br/> 64/32-bitowy | SERWERA usługi MAB v3, wersja 2 | Lokalna/maszyna wirtualna platformy Azure. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Azure VMprotection**: wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane. |
**Windows Server 2012 z dodatkiem SP1 (Datacenter i standard)**<br/><br/> 64/32-bitowy | SERWERA usługi MAB v3, wersja 2 <br/><br/> Należy zainstalować [pakiet Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) . | Lokalna/maszyna wirtualna platformy Azure. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane. |
**Windows 2008 R2 z dodatkiem SP1 (wersje Standard i Enterprise)**<br/><br/> 64/32-bitowy | Obsługiwane przez serwera usługi MAB v3, v2.<br/><br/> Należy zainstalować [pakiet Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) . | Lokalna/maszyna wirtualna platformy Azure. |   **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane. |
**Windows 2008 R2 (wersje Standard i Enterprise)**<br/><br/> 64/32-bitowy | W przypadku wersji serwera usługi MAB V3 w systemie operacyjnym musi być uruchomiony program SP1. | Lokalna/maszyna wirtualna platformy Azure. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane. |
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 64/32-bitowy | SERWERA usługi MAB v3, wersja 2 | SERWERA usługi MAB v2, wersja v3 jest obsługiwana, gdy serwera usługi MAB jest wdrożony jako maszyna wirtualna VMware.<br/><br/> Nieobsługiwane w przypadku serwera usługi MAB uruchomionego na maszynie wirtualnej platformy Azure. | Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego. |
**System Windows Storage Server 2008** | SERWERA usługi MAB v3, wersja 2 | SERWERA usługi MAB jako lokalny serwer fizyczny/maszynę wirtualną funkcji Hyper-V. <br/><br/> Nieobsługiwane w przypadku serwera usługi MAB uruchomionego na maszynie wirtualnej platformy Azure. | Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.
**SQL Server 2017** | SERWERA usługi MAB v3 | Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane. |
**SQL Server 2016/2016 z dodatkiem SP1** | SERWERA usługi MAB v3, wersja 2 | Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane. |
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SERWERA usługi MAB v3, wersja 2 | Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane. |
**Program Exchange 2016**<br/><br/> **Program Exchange 2013**<br/><br/> **Program Exchange 2010** | SERWERA usługi MAB v3, wersja 2 | Lokalnie. | Utwórz kopię zapasową autonomicznego serwera programu Exchange, bazy danych w ramach DAG.<br/><br/> Odzyskaj skrzynkę pocztową, bazę danych skrzynek pocztowych pod DAG.<br/><br/> System plików ReFS nie jest obsługiwany.<br/><br/> Tworzenie kopii zapasowych klastrów dysków nieudostępnionych.<br/><br/> Utwórz kopię zapasową programu Exchange Server skonfigurowanego do ciągłej replikacji. |
**Program SharePoint 2016**<br/><br/> **Program SharePoint 2013**<br/><br/> **Program SharePoint 2010** | SERWERA usługi MAB v3, wersja 2 | Lokalna/maszyna wirtualna platformy Azure. | Utwórz kopię zapasową farmy, serwer frontonu sieci Web.<br/><br/> Odzyskaj farmy, bazę danych, aplikację sieci Web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci Web.<br/><br/> Nie można utworzyć kopii zapasowej farmy przy użyciu SQL Server AlwaysOn dla baz danych zawartości. |
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | SERWERA usługi MAB v3, wersja 2 | Lokalnie. | **Agent serwera usługi MAB na hoście funkcji Hyper-V**: Tworzenie kopii zapasowych całych maszyn wirtualnych i plików danych hosta. Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu magazynu lokalnego, maszyn wirtualnych w klastrze z magazynem CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi MAB na maszynie wirtualnej gościa**: wykonywanie kopii zapasowych obciążeń uruchomionych na maszynie wirtualnej. CSV.<br/><br/> **Odzyskiwanie**: maszyna wirtualna, odzyskiwanie na poziomie elementu dysku VHD/woluminów/folderów/plików.<br/><br/> **Maszyny wirtualne z systemem Linux**: Tworzenie kopii zapasowej, gdy funkcja Hyper-V działa w systemie Windows Server 2012 R2 lub nowszym. Odzyskiwanie dla maszyn wirtualnych systemu Linux dotyczy całego komputera. |
**Maszyny wirtualne VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | SERWERA usługi MAB v3, wersja 2 | Lokalnie. | Tworzenie kopii zapasowych maszyn wirtualnych VMware w magazynach CSV, NFS i SAN.<br/><br/> Odzyskaj całą maszynę wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie folderów/plików na poziomie elementu tylko dla maszyn wirtualnych z systemem Windows.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie dla maszyn wirtualnych systemu Linux dotyczy całego komputera. |

## <a name="supported-backups-to-dpm"></a>Obsługiwane kopie zapasowe w programie DPM

Poniższa tabela zawiera podsumowanie elementów, których kopię zapasową można utworzyć w programie DPM z maszyn lokalnych i maszyn wirtualnych platformy Azure.

**Kopii zapasowej** | **MOŻLIWOŚCI** | **Uzyskać**
--- | --- | ---
**Windows 10 @ no__t-1Windows 8.1 @ no__t-2Windows 8 @ no__t-3Windows 7**<br/><br/>(32/64-bitowe) | Tylko lokalnie.<br/><br/> Aby utworzyć kopię zapasową systemu Windows 10 z programem DPM 2012 R2, zalecamy zainstalowanie [aktualizacji Update 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager). | Wolumin/udział/folder/plik.<br/><br/> Woluminy z deduplikacją są obsługiwane.<br/><br/> Woluminy muszą mieć co najmniej 1 GB i NTFS.
**Windows Server 2016 (Datacenter, standard, nie nano)**<br/><br/> 64/32-bitowy | Lokalna/maszyna wirtualna platformy Azure.<br/><br/> Tylko program DPM 2016.| Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> Woluminy z deduplikacją są obsługiwane.
**Windows Server 2012 R2 (Datacenter i standard)**<br/><br/> 64/32-bitowy | Lokalna/maszyna wirtualna platformy Azure. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.<br/><br/> Deduplikowane woluminy obsługiwane przez program DPM 2012 R2 i nowsze.
**Windows Server 2012 z dodatkiem SP1 (Datacenter i standard)**<br/><br/> 64/32-bitowy | Lokalna/maszyna wirtualna platformy Azure. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.<br/><br/> Deduplikowane woluminy obsługiwane przez program DPM 2012 R2 i nowsze.
**Windows 2008 R2 z dodatkiem SP1 (wersje Standard i Enterprise)**<br/><br/> 64/32-bitowy | Lokalna/maszyna wirtualna platformy Azure.<br/><br/> Należy zainstalować [pakiet Windows Management Framework 4,0](https://www.microsoft.com/download/details.aspx?id=40855) . |   **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.<br/><br/> **Ochrona maszyny wirtualnej platformy Azure**: wolumin/udział/folder/plik.
**Windows 2008 R2 (wersje Standard i Enterprise)**<br/><br/> 64/32-bitowy | Lokalnie.<br/><br/> Nie można zainstalować programu DPM jako maszyny wirtualnej VMware.<br/><br/> Program DPM działający na maszynie wirtualnej platformy Azure nie jest obsługiwany. | **Ochrona lokalna**: wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.
**Windows Server 2008 z dodatkiem SP2**<br/><br/> 64/32-bitowy | Tylko lokalnie.<br/><br/> Program DPM jest obsługiwany, gdy działa jako maszyna wirtualna VMware. Uruchamianie jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V nie jest obsługiwana. | Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.
**System Windows Storage Server 2008** | Program DPM działający lokalnie jako serwer fizyczny lub maszyna wirtualna funkcji Hyper-V. | Wolumin/udział/folder/plik; System-stan/bez systemu operacyjnego.
**SQL Server 2017** | KONSOLA SAC PROGRAMU DPM; Program DPM 2016 z uruchomionym pakietem zbiorczym aktualizacji 5 lub nowszym.<br/><br/> Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane.
**SQL Server 2016 z dodatkiem SP1** | Nieobsługiwane w przypadku programu DPM 2012 R2; Obsługiwane przez program DPM SAC, DPM 2016 z uruchomionym pakietem zbiorczym aktualizacji 4 lub nowszym.<br/><br/> Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane.
**SQL Server 2016** | Nieobsługiwane w przypadku programu DPM 2012 R2. Obsługiwane w przypadku konsoli SAC programu DPM, programu DPM 2016 z pakietu zbiorczego aktualizacji 2 i nowszych wersji.<br/><br/> Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | SQL Server 2014 z programem DPM 2012 R2 z uruchomionym pakietem zbiorczym aktualizacji 4 lub nowszym.<br/><br/> Lokalna/maszyna wirtualna platformy Azure.| Utwórz kopię zapasową bazy danych SQL Server.<br/><br/> Obsługa kopii zapasowych klastra SQL Server.<br/><br/>Bazy danych przechowywane w CSV nie są obsługiwane.
**Program Exchange 2016**<br/><br/> **Program Exchange 2013**<br/><br/> **Program Exchange 2010** | W przypadku programu Exchange 2016 program DPM 2012 R2 wymaga pakietu zbiorczego aktualizacji 9 lub nowszego.<br/><br/> Lokalna | Utwórz kopię zapasową autonomicznego serwera programu Exchange, bazy danych w ramach DAG.<br/><br/> Odzyskaj skrzynkę pocztową, bazę danych skrzynek pocztowych pod DAG.<br/><br/> System plików ReFS nie jest obsługiwany.<br/><br/> Tworzenie kopii zapasowych klastrów dysków nieudostępnionych.<br/><br/> Utwórz kopię zapasową programu Exchange Server skonfigurowanego do ciągłej replikacji.
**Program SharePoint 2016**<br/><br/> **Program SharePoint 2013**<br/><br/> **Program SharePoint 2010** | Program SharePoint 2016 w programie DPM 2016 lub nowszym.<br/><br/>Lokalna/maszyna wirtualna platformy Azure. | Utwórz kopię zapasową farmy, serwer frontonu sieci Web.<br/><br/> Odzyskaj farmy, bazę danych, aplikację sieci Web, plik lub element listy, wyszukiwanie programu SharePoint, serwer frontonu sieci Web.<br/><br/> Nie można utworzyć kopii zapasowej farmy przy użyciu SQL Server AlwaysOn dla baz danych zawartości.
**Funkcja Hyper-V w systemie Windows Server 2016**<br/><br/> **Windows Server 2012 R2/2012** (Datacenter/standard)<br/><br/> **Windows Server 2008 R2 (z dodatkiem SP1)** | Funkcja Hyper-V w systemie 2016 jest obsługiwana dla programu DPM 2016 i nowszych.<br/><br/> Lokalnie. | **Agent serwera usługi MAB na hoście funkcji Hyper-V**: Tworzenie kopii zapasowych całych maszyn wirtualnych i plików danych hosta. Tworzenie kopii zapasowych maszyn wirtualnych przy użyciu magazynu lokalnego, maszyn wirtualnych w klastrze z magazynem CSV, maszyny wirtualne z magazynem serwera plików SMB.<br/><br/> **Agent serwera usługi MAB na maszynie wirtualnej gościa**: wykonywanie kopii zapasowych obciążeń uruchomionych na maszynie wirtualnej. CSV.<br/><br/> **Odzyskiwanie**: maszyna wirtualna, odzyskiwanie na poziomie elementu dysku VHD/woluminów/folderów/plików.<br/><br/> **Maszyny wirtualne z systemem Linux**: Tworzenie kopii zapasowej, gdy funkcja Hyper-V działa w systemie Windows Server 2012 R2 lub nowszym. Odzyskiwanie dla maszyn wirtualnych systemu Linux dotyczy całego komputera.
**Maszyny wirtualne VMware: vCenter/vSphere ESXi 5.5/6.0/6.5** | SERWERA usługi MAB v3, wersja 2 <br/><br/> Program DPM 2012 R2 wymaga programu System Center Update Rollup 1) <br/><br/>Lokalnie. | Tworzenie kopii zapasowych maszyn wirtualnych VMware w magazynach CSV, NFS i SAN.<br/><br/> Odzyskaj całą maszynę wirtualną.<br/><br/> Kopia zapasowa systemu Windows/Linux.<br/><br/> Odzyskiwanie folderów/plików na poziomie elementu tylko dla maszyn wirtualnych z systemem Windows.<br/><br/> Oprogramowania vApps VMware nie są obsługiwane.<br/><br/> Odzyskiwanie dla maszyn wirtualnych systemu Linux dotyczy całego komputera.

- Klastrowane obciążenia tworzone w ramach programu DPM/serwera usługi MAB powinny znajdować się w tej samej domenie co program DPM/serwera usługi MAB lub w domenie podrzędnej/zaufanej.
- Przy użyciu uwierzytelniania NTLM/Certificate można tworzyć kopie zapasowe danych w niezaufanych domenach lub grupach roboczych.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Sprawdź](backup-support-matrix-mars-agent.md) , co jest obsługiwane przez agenta Mars.
- [Skonfiguruj](backup-azure-microsoft-azure-backup.md) serwer serwera usługi MAB.
- [Skonfiguruj program DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180).
