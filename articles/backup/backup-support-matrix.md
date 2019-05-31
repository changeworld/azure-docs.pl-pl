---
title: Tabela obsługi usługi Azure Backup
description: Zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących usługi Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400173"
---
# <a name="azure-backup-support-matrix"></a>Tabela obsługi usługi Azure Backup

Możesz użyć [kopia zapasowa Azure](backup-overview.md) do tworzenia kopii zapasowych danych z platformy Microsoft Azure w chmurze. Ten artykuł zawiera podsumowanie ustawień ogólną pomoc techniczną i ograniczenia dotyczące usługi Azure Backup scenariuszy i wdrożenia.

Dostępne są inne macierze pomocy technicznej:

- Macierz obsługi dla [kopii zapasowych maszyn wirtualnych (VM)](backup-support-matrix-iaas.md)
- Macierz obsługi kopii zapasowej za pomocą [System Center Data Protection Manager (DPM) / Microsoft Azure Backup serwera (MABS)](backup-support-matrix-mabs-dpm.md)
- Macierz obsługi kopii zapasowej za pomocą [agenta usługi Microsoft Azure Recovery Services (MARS)](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>Obsługa magazynu

Usługa Azure Backup używa magazynów usługi Recovery Services umożliwia organizowanie i zarządzanie kopiami zapasowymi. Używa również magazynów do przechowywania danych kopii zapasowej.

W poniższej tabeli opisano funkcje magazynów usługi Recovery Services:

**Funkcja** | **Szczegóły**
--- | ---
**Magazynów w subskrypcji** | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
**Maszyny w magazynie** | Maksymalnie 1000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> Maksymalnie 50 MABS można zarejestrować serwery w jednym magazynie.
**Źródła danych w magazynie magazynu** | Maksymalna 54,400 GB. Nie ma limitu kopii zapasowych maszyn wirtualnych platformy Azure.
**Tworzenie kopii zapasowych w magazynie** | **Maszyny wirtualne platformy Azure:** Raz dziennie.<br/><br/>**Komputery chronione przez program DPM/serwera usługi Mab:** Dwa razy dziennie.<br/><br/> **Kopię zapasową bezpośrednio za pomocą agenta usług MARS maszyny:** Trzy razy dziennie.
**Tworzenie kopii zapasowych między magazynów** | Kopia zapasowa jest w obrębie regionu.<br/><br/> Potrzebujesz magazynu w każdym regionie platformy Azure zawierającej maszyny wirtualne, aby utworzyć kopię zapasową. Nie można utworzyć kopii w innym regionie.
**Przeniesienie magazynów** | Możesz [Przenieś magazynów](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) między subskrypcjami lub między grupami zasobów w tej samej subskrypcji.
**Przenoszenie danych między magazynami** | Przenoszenie danych kopii zapasowej między magazynów nie jest obsługiwane.
**Modyfikowanie typu magazynu magazynu** | Typ replikacji magazynu (magazyn geograficznie nadmiarowy lub magazyn lokalnie nadmiarowy) dla magazynu można zmodyfikować przed kopie zapasowe są przechowywane. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.

## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych

Oto, co jest obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn lokalnych:

**Maszyna** | **Co to jest wykonywana kopia zapasowa** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Bezpośrednie tworzenie kopii zapasowych maszyny Windows za pomocą agenta usług MARS** | Pliki, foldery, stan systemu | Utwórz kopię zapasową w magazynie usługi Recovery Services. | Tworzenie kopii zapasowej trzy razy dziennie<br/><br/> Kopia zapasowa nie obsługujących aplikacji<br/><br/> Przywracanie plików, folderów, woluminów
**Bezpośrednie tworzenie kopii zapasowych z maszyny z systemem Linux za pomocą agenta usług MARS** | Kopia zapasowa nie jest obsługiwane
**Tworzenie kopii zapasowej programu DPM** | Plików, folderów, woluminów, stanu systemu, dane aplikacji | Utwórz kopię zapasową do lokalnego magazynu programu DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Poziom szczegółowości pełnej kopii zapasowych i odzyskiwania<br/><br/> Linux obsługiwane w przypadku maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwane
**Tworzenie kopii zapasowej serwera usługi Mab** | Plików, folderów, woluminów, stanu systemu, dane aplikacji | Tworzenie kopii zapasowej do magazynu lokalnego serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Poziom szczegółowości pełnej kopii zapasowych i odzyskiwania<br/><br/> Linux obsługiwane w przypadku maszyn wirtualnych (Hyper-V/VMware)<br/><br/> Oracle nie jest obsługiwane

## <a name="azure-vm-backup-support"></a>Maszyna wirtualna kopii zapasowej pomocy technicznej platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
**Dyski z danymi maszyny Wirtualnej platformy Azure** | Limit wynoszący 16
**Rozmiar dysku danych w usłudze Azure VM** | Poszczególnych dysków może być maksymalnie 4095 GB.

### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

Oto, co jest obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure:

**Maszyna** | **Co to jest wykonywana kopia zapasowa** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Kopia zapasowa maszyny Wirtualnej platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej** | Całą maszynę Wirtualną | Utwórz kopię zapasową w magazynie. | Rozszerzenie zainstalować po włączeniu kopii zapasowej dla maszyny Wirtualnej.<br/><br/> Tworzenie kopii zapasowych raz dziennie.<br/><br/> Obsługujących aplikacji kopii zapasowej dla maszyn wirtualnych Windows; spójna na poziomie plików kopia zapasowa maszyn wirtualnych systemu Linux. Spójności dla maszyn z systemem Linux na poziomie aplikacji można skonfigurować za pomocą skryptów niestandardowych.<br/><br/> Przywracanie maszyny Wirtualnej lub dysków.<br/><br/> Nie można utworzyć kopii maszyn wirtualnych platformy Azure do lokalizacji lokalnej.
**Kopia zapasowa maszyny Wirtualnej platformy Azure za pomocą agenta usług MARS** | Pliki, foldery, stan systemu | Utwórz kopię zapasową w magazynie. | Utwórz kopię zapasową trzy razy dziennie.<br/><br/> Jeśli chcesz utworzyć kopię zapasową określonych plików lub folderów, a nie całą maszynę Wirtualną, uruchomić agenta usług MARS wraz z rozszerzenia maszyny Wirtualnej.
**Maszyna wirtualna platformy Azure z programem DPM** | Plików, folderów, woluminów, stanu systemu, dane aplikacji | Utwórz kopię zapasową do lokalnego magazynu maszyny Wirtualnej platformy Azure, w którym działa program DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Plików, folderów, woluminów, stanu systemu, dane aplikacji | Utwórz kopię zapasową do lokalnego magazynu maszyny Wirtualnej platformy Azure, w którym działa serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji.<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.

## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

Oto, co jest obsługiwane, jeśli chcesz utworzyć kopię zapasową maszyn z systemem Linux:

**Typ kopii zapasowej** | **Linux (zatwierdzony przez Azure)**
--- | ---
**Bezpośrednie tworzenie kopii zapasowych maszyny w środowisku lokalnym, który jest uruchomiony system Linux** | Nieobsługiwane. Agenta usług MARS można zainstalować tylko na maszynach Windows.
**Aby utworzyć kopię zapasową maszyny Wirtualnej platformy Azure z systemem Linux przy użyciu rozszerzenia agenta** | Kopia zapasowa spójna z aplikacji przy użyciu [niestandardowe skrypty](backup-azure-linux-app-consistent.md).<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Aby utworzyć kopię zapasową w środowisku lokalnym lub maszynie Wirtualnej platformy Azure z systemem Linux przy użyciu programu DPM** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa systemu Linux na funkcji Hyper-V i VMWare.<br/><br/> Przywracanie maszyn wirtualnych funkcji Hyper-V i maszyn wirtualnych gości Linux VMWare.<br/><br/> Spójna na poziomie plików kopia zapasowa nie jest dostępna dla maszyny Wirtualnej platformy Azure.
**Za pomocą serwera usługi Mab do tworzenia kopii zapasowych na komputerze lokalnym lub maszynie Wirtualnej platformy Azure z systemem Linux** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa systemu Linux na funkcji Hyper-V i VMWare.<br/><br/> Przywrócenie maszyny Wirtualnej funkcji Hyper-V i maszyn wirtualnych programu VMWare z systemem Linux gościa.<br/><br/> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure.

## <a name="daylight-saving-time-support"></a>Obsługa czasu letniego

Usługa Azure Backup nie obsługuje zegara automatyczne dostosowanie czasu tworzenia kopii zapasowych maszyn wirtualnych platformy Azure. Modyfikowanie zasad tworzenia kopii zapasowej ręcznego zgodnie z potrzebami.

## <a name="disk-deduplication-support"></a>Obsługa deduplikacji dysku

Obsługa deduplikacji dysku jest następująca:

- Deduplikacja dysku jest obsługiwane w środowisku lokalnym, korzystając z programu DPM lub serwera usługi Mab do tworzenia kopii zapasowych maszyn wirtualnych funkcji Hyper-V z uruchomionym systemem Windows. System Windows Server wykonuje deduplikację danych (na poziomie hosta) na wirtualnych dyskach twardych (VHD), które są dołączone do maszyny Wirtualnej jako magazyn kopii zapasowych.
- Funkcja deduplikacji nie jest obsługiwana na platformie Azure dla żadnego składnika usługi Backup. Gdy program DPM i serwera usługi Mab są wdrażane na platformie Azure, dyski magazynowania dołączone do maszyny Wirtualnej nie może być deduplikowane.

## <a name="security-and-encryption-support"></a>Obsługa zabezpieczeń i szyfrowania

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych w spoczynku.

### <a name="network-traffic-to-azure"></a>Ruch sieciowy na platformie Azure

- Kopii zapasowej ruch z serwerów w magazynie usługi Recovery Services jest szyfrowany przy użyciu algorytmu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- Dane kopii zapasowych są przechowywane w magazynie usługi Recovery Services w taki sposób, w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.

    > [!WARNING]
    > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.

### <a name="data-security"></a>Bezpieczeństwo danych

- Jeśli wykonujesz kopie zapasowe maszyn wirtualnych platformy Azure, musisz skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Usługa Azure Backup obsługuje usługę Azure Disk Encryption, która używa funkcji BitLocker na maszynach wirtualnych z systemem Windows, a programu **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zapleczu, usługa Azure Backup używa [szyfrowanie usługi Azure Storage](../storage/common/storage-service-encryption.md), która chroni dane magazynowane.

**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
**Lokalne maszyny Windows bez programu DPM/serwera usługi Mab** | ![Tak][green] | ![Yes][green]
**Maszyny wirtualne platformy Azure** | ![Tak][green] | ![Yes][green]
**Lokalnych maszyn Windows lub maszyny wirtualne platformy Azure przy użyciu programu DPM** | ![Yes][green] | ![Tak][green]
**Lokalnych maszyn Windows lub maszyny wirtualne platformy Azure przy użyciu serwera usługi Mab** | ![Tak][green] | ![Yes][green]

## <a name="compression-support"></a>Obsługa kompresji

Backup obsługuje kompresję kopii zapasowej ruchu, zgodnie z opisem w poniższej tabeli.

- Maszyn wirtualnych platformy Azure rozszerzenia maszyny Wirtualnej odczytuje dane bezpośrednio z konta usługi Azure storage za pośrednictwem sieci magazynowania, dzięki czemu nie trzeba Kompresja tego ruchu.
- Jeśli używasz programu DPM lub MABS, możesz oszczędzić przepustowość, poprzez kompresowanie danych przed kopii zapasowej.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresuj do magazynu (HTTPS)**
--- | --- | ---
**Bezpośrednie tworzenie kopii zapasowych maszyn Windows w środowisku lokalnym** | Nie dotyczy | ![Yes][green]
**Kopia zapasowa maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej** | Nie dotyczy | Nie dotyczy
**Tworzenie kopii zapasowych na maszynach środowiska lokalne/platformę Azure za pomocą serwera usługi Mab/DPM** | ![Tak][green] | ![Yes][green]

## <a name="retention-limits"></a>Limity przechowywania

**Ustawienie** | **Limity**
--- | ---
**Maksymalna liczba punktów odzyskiwania dla chronionego wystąpienia (maszyny lub obciążenia)** | 9,999
**Maksymalny czas wygaśnięcia punktu odzyskiwania** | Bez ograniczeń
**Maksymalna częstotliwość wykonywania kopii zapasowych do DPM/serwera usługi Mab** | Co 15 minut dla programu SQL Server<br/><br/> Co godzinę dla innych obciążeń
**Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie** | **Lokalne maszyny Windows lub systemem MARS maszyn wirtualnych platformy Azure:** Trzy na dzień<br/><br/> **PROGRAM DPM/MABS:** dwie dziennie<br/><br/> **Kopia zapasowa maszyny Wirtualnej platformy Azure:** Jeden raz dziennie
**Czas przechowywania punktu odzyskiwania** | Codziennie, co tydzień, co miesiąc, co rok
**Maksymalny okres przechowywania** | Zależnie od częstotliwości wykonywania kopii zapasowych
**Punkty odzyskiwania na dysku programu DPM/serwera usługi Mab** | 64 dla serwerów plików; 448 dla serwerów aplikacji <br/><br/>Nieograniczona liczba taśm punktów odzyskiwania programu DPM w środowisku lokalnym

## <a name="next-steps"></a>Kolejne kroki

- [Macierz obsługi przeglądu](backup-support-matrix-iaas.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
