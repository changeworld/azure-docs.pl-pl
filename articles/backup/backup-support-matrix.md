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
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429574"
---
# <a name="azure-backup-support-matrix"></a>Tabela obsługi usługi Azure Backup

[Usługa Azure Backup](backup-overview.md) umożliwia wykonywanie kopii zapasowych danych w chmurze platformy Microsoft Azure. W tym artykule przedstawiono ustawienia ogólną pomoc techniczną i ograniczenia dotyczące usługi Azure Backup scenariuszy i wdrożenia.

Dostępne są inne macierze pomocy technicznej:

[Macierz obsługi](backup-support-matrix-iaas.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure [macierz obsługi](backup-support-matrix-mabs-dpm.md) do utworzenia kopii zapasowej przy użyciu serwera System Center DPM/kopia zapasowa Microsoft Azure (MABS) [macierz obsługi](backup-support-matrix-mars-agent.md) dla obsługi przy użyciu Microsoft Azure Agent usług Recovery Services (MARS)

## <a name="vault-support"></a>Obsługa magazynu

Kopie zapasowe platformy Azure używają magazynów usługi Recovery Services do organizowania kopii zapasowych i zarządzania nimi oraz do przechowywania kopii zapasowych danych.

**Ustawienie** | **Szczegóły**
--- | ---
**Magazynów w subskrypcji** | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
**Maszyny w magazynie** | Maksymalnie 1000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> Maksymalnie 50 MABS można zarejestrować serwery w jednym magazynie.
**Źródła danych w magazynie magazynu** | Maksymalnie 54 400 GB. Nie ma limitu kopii zapasowych maszyn wirtualnych platformy Azure.
**Tworzenie kopii zapasowych w magazynie** | Maszyny wirtualne platformy Azure: raz dziennie<br/><br/>Komputery chronione przez program DPM/serwera usługi Mab: dwa razy dziennie<br/><br/> Kopia zapasowa maszyn bezpośrednio za pomocą agenta usług MARS: trzy razy dziennie. 
**Tworzenie kopii zapasowych między magazynów** | Kopia zapasowa jest w obrębie regionu.<br/><br/> Potrzebujesz magazynu w każdym regionie platformy Azure zawierającej maszyny wirtualne, aby utworzyć kopię zapasową. Nie można utworzyć kopii w innym regionie. 
**Przenoszenie magazynu** | Możesz [Przenieś magazynów](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) między subskrypcjami lub między grupami zasobów w tej samej subskrypcji.
**Przenoszenie danych między magazynami** | Przenoszenie kopii zapasowych danych między magazynami nie jest obsługiwane.
**Modyfikowanie typu magazynu magazynu** | Możesz zmodyfikować typ replikacji magazynu (magazyn geograficznie nadmiarowy/magazyn lokalnie nadmiarowy) można zmodyfikować zanim zaczniesz przechowywać kopie zapasowe. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.



## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych

W przypadku kopii zapasowych maszyn lokalnych są obsługiwane poniższe elementy.

**Maszyna** | **Kopie zapasowe** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Bezpośrednie tworzenie kopii zapasowych maszyny Windows za pomocą agenta usług MARS** | Pliki, foldery, stan systemu | Tworzenie kopii zapasowej do magazynu usługi Recovery services | Kopia zapasowa wykonywana trzy razy dziennie.<br/><br/> Bez kopii zapasowej uwzględniającej aplikację.<br/><br/> Przywracanie pliku, folderu i woluminu.
**Bezpośrednie tworzenie kopii zapasowych z maszyny z systemem Linux za pomocą agenta usług MARS** | Brak obsługi kopii zapasowych.
**Kopii zapasowej programu DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Utwórz kopię zapasową do lokalnego magazynu programu DPM. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.
**Wróć do serwera usługi Mab** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Tworzenie kopii zapasowej do magazynu lokalnego serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.


## <a name="azure-vm-backup-support"></a>Maszyna wirtualna kopii zapasowej pomocy technicznej platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Limit równy 16.
Rozmiar dysku z danymi maszyn wirtualnych platformy Azure | Każdy dysk może mieć rozmiar do 4095 GB.


### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure są obsługiwane poniższe elementy.

**Maszyna** | **Kopie zapasowe** | **Lokalizacja** | **Funkcje**
--- | --- | --- | ---
**Usługa Azure backup maszyny Wirtualnej przy użyciu rozszerzenia maszyny Wirtualnej** | Całą maszynę Wirtualną | Kopii zapasowej do magazynu | Rozszerzenie zainstalować po włączeniu kopii zapasowej dla maszyny Wirtualnej.<br/><br/> Kopia zapasowa wykonywana raz dziennie.<br/><br/> Kopia zapasowa z uwzględnieniem aplikacji dla maszyn wirtualnych z systemem Windows, spójna na poziomie plików kopia zapasowa maszyn wirtualnych z systemem Linux. Możesz skonfigurować spójność na poziomie aplikacji dla maszyn z systemem Linux za pomocą skryptów niestandardowych.<br/><br/> Przywracanie maszyny wirtualnej/dysku.<br/><br/> Nie można wykonać kopii zapasowej maszyny wirtualnej platformy Azure w lokalizacji lokalnej.
**Usługa Azure backup maszyny Wirtualnej za pomocą agenta usług MARS** | Pliki i foldery | Kopii zapasowej do magazynu | Kopia zapasowa wykonywana trzy razy dziennie.<br/><br/> Agenta usług MARS można uruchomić równolegle z rozszerzenia maszyny Wirtualnej, jeśli chcesz utworzyć kopię zapasową określonych plików/folderów, a nie całą maszynę Wirtualną.
**Maszyna wirtualna platformy Azure z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Utwórz kopię zapasową w magazynie lokalnym z uruchomionym programem DPM maszyny Wirtualnej platformy Azure. Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Kopię zapasową do lokalnego magazynu Azure maszyny Wirtualnej z serwera usługi MAB. Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/> Oprogramowanie Oracle nie jest obsługiwane.





## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Tworzenie kopii zapasowych** | **Linux (zatwierdzony przez Azure)**
--- | ---
**Bezpośrednie tworzenie kopii zapasowych komputera lokalnego z systemem Linux**. | Nie. Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows.
**Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure z systemem Linux (przy użyciu rozszerzenia agenta)** | Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Tworzenie kopii zapasowych w środowisku lokalnym lub maszynie Wirtualnej platformy Azure z systemem Linux przy użyciu programu DPM** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare<br/><br/> Przywracanie maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare</br></br> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure
**Tworzenie kopii zapasowych maszyn lokalnych/Azure maszyny Wirtualnej z systemem Linux przy użyciu serwera usługi Mab** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare<br/><br/> Przywracanie maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare</br></br> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure.

## <a name="daylight-saving-support"></a>Uwzględniaj pomocy technicznej

Usługa Azure Backup nie obsługuje zegara automatycznego dopasowania dla-letniego zmiany dotyczące kopii zapasowych maszyn wirtualnych platformy Azure. Modyfikowanie zasad tworzenia kopii zapasowej ręcznego zgodnie z potrzebami.


## <a name="disk-deduplication-support"></a>Obsługa deduplikacji dysku

Obsługa deduplikacji dysku jest następująca:
- Deduplikacja dysku jest obsługiwana w środowisku lokalnym, gdy kopie zapasowe maszyn wirtualnych funkcji Hyper-V z systemem Windows są wykonywane za pomocą programu DPM lub usługi MABS. System Windows Server wykonuje deduplikację danych (na poziomie hosta) na wirtualnych dyskach twardych (VHD) dołączonych do maszyny wirtualnej jako magazyn kopii zapasowych.
- Funkcja deduplikacji nie jest obsługiwana na platformie Azure dla żadnego składnika usługi Backup. Gdy na platformie Azure wdrożone są programy System Center DPM i Backup Server, dyski magazynowania dołączone do maszyny wirtualnej nie mogą być deduplikowane.


## <a name="securityencryption-support"></a>Obsługa zabezpieczeń/szyfrowania

Usługa Azure Backup obsługuje szyfrowanie danych podczas przesyłania i danych magazynowanych:

Ruch sieciowy do platformy Azure:
- Ruch sieciowy z serwerów użytkownika do magazynu usługi Recovery Services związany z wykonywaniem kopii zapasowych jest szyfrowany przy użyciu algorytmu Advanced Encryption Standard 256.
- Dane kopii zapasowej są przesyłane za pośrednictwem bezpiecznego połączenia HTTPS.
- W magazynie usługi Recovery Services dane kopii zapasowych są przechowywane w postaci zaszyfrowanej.
- Tylko Ty dysponujesz hasłem do odblokowania tych danych. Firma Microsoft nie może odszyfrować danych kopii zapasowej w żadnym punkcie.
    > [!WARNING]
    > Po skonfigurowaniu magazynu tylko Ty masz dostęp do klucza szyfrowania. Firma Microsoft nigdy nie przechowuje kopii i nie ma dostępu do klucza. W przypadku utraty klucza firma Microsoft nie może odzyskać danych kopii zapasowej.
Bezpieczeństwo danych:
- W przypadku wykonywania kopii zapasowych maszyn wirtualnych platformy Azure musisz skonfigurować szyfrowanie *w ramach* maszyny wirtualnej.
- Usługa Azure Backup obsługuje usługę Azure Disk Encryption, która używa funkcji BitLocker na maszynach wirtualnych z systemem Windows, a programu **dm-crypt** na maszynach wirtualnych z systemem Linux.
- Na zapleczu usługa Azure Backup używa [szyfrowania usługi Azure Storage](../storage/common/storage-service-encryption.md), które chroni dane magazynowane.


**Maszyna** | **Przesyłanie** | **Magazynowanie**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | ![Yes][green] | ![Yes][green]
Maszyny wirtualne platformy Azure | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Yes][green] | ![Yes][green]



## <a name="compression-support"></a>Obsługa kompresji

Usługa Backup obsługuje kompresję ruchu związanego z wykonywaniem kopii zapasowych zgodnie z opisem w poniższej tabeli. 

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta magazynu na platformie Azure w sieci magazynowania, więc kompresja tego ruchu nie jest konieczna.
- Jeśli używasz programu DPM lub usługi MABS, możesz skompresować dane przed wykonaniem ich kopii zapasowej w programie DPM/usłudze MABS, aby oszczędzić przepustowość.

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresja (HTTPS) do magazynu**
--- | --- | ---
**Bezpośrednie tworzenie kopii zapasowych maszyn Windows w środowisku lokalnym** | Nie dotyczy | Yes
**Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure przy użyciu rozszerzenia maszyny Wirtualnej** | Nie dotyczy | Nie dotyczy
** Tworzenie kopii zapasowych na maszynach środowiska lokalne/platformę Azure za pomocą serwera usługi Mab/DPM | ![Yes][green] | ![Yes][green]



## <a name="retention-limits"></a>Limity przechowywania

**Ustawienie** | **Limity**
--- | ---
Maksymalna liczba punktów odzyskiwania na chronione wystąpienie (maszyna/obciążenie) | 9999
Maksymalny czas wygaśnięcia punktu odzyskiwania | Bez ograniczeń
Maksymalna częstotliwość wykonywania kopii zapasowych w programie DPM/usłudze MABS | Co 15 minut dla programu SQL Server<br/><br/> Co godzinę w przypadku innych obciążeń.
Maksymalna częstotliwość wykonywania kopii zapasowych w magazynie | Maszyny lokalne z systemem Windows/maszyny wirtualne platformy Azure z usługą MARS: trzy dziennie<br/><br/> DPM/MABS: dwie dziennie<br/><br/> Kopia zapasowa maszyny wirtualnej platformy Azure: raz dziennie
Przechowywanie punktów odzyskiwania | Codziennie, co tydzień, co miesiąc, co rok.
Maksymalny okres przechowywania | Zależnie od częstotliwości wykonywania kopii zapasowych.
Punkty odzyskiwania na dysku programu DPM/usługi MABS | 64 w przypadku serwerów plików, 448 w przypadku serwerów aplikacji.<br/><br/> Punkty odzyskiwania na taśmie nie mają ograniczeń w przypadku lokalnego programu DPM.

## <a name="next-steps"></a>Kolejne kroki

- [Macierz obsługi przeglądu](backup-support-matrix-iaas.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure.


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
