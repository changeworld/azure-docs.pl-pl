---
title: Tabela obsługi usługi Azure Backup
description: Zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących usługi Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b1fa723863e6485e977e075986c3779efed1e689
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360651"
---
# <a name="azure-backup-support-matrix"></a>Tabela obsługi usługi Azure Backup

[Usługa Azure Backup](backup-overview.md) umożliwia wykonywanie kopii zapasowych danych w chmurze platformy Microsoft Azure. Ten artykuł zawiera podsumowanie ustawień obsługi i ograniczeń dotyczących scenariuszy i wdrożeń usługi Azure Backup.

## <a name="vault-support"></a>Obsługa magazynu

Kopie zapasowe platformy Azure używają magazynów usługi Recovery Services do organizowania kopii zapasowych i zarządzania nimi oraz do przechowywania kopii zapasowych danych.

**Ustawienie** | **Szczegóły**
--- | ---
Liczba magazynów | Do 500 magazynów usługi Recovery Services w ramach jednej subskrypcji.
Maszyny w magazynie | Maksymalnie 1000 maszyn wirtualnych platformy Azure w jednym magazynie.<br/><br/> W jednym magazynie można zarejestrować maksymalnie 50 maszyn lokalnych z agentem usługi Azure Backup (agentem usługi Microsoft Azure Recovery Services, MABS).
Źródło danych w pamięci magazynów | Maksymalnie 54 400 GB. Nie ma limitu kopii zapasowych maszyn wirtualnych platformy Azure.
Wykonywanie kopii zapasowych w magazynie | Maszyny wirtualne platformy Azure: raz dziennie; maszyny chronione przez oprogramowanie DPM/MABS: dwa razy dziennie; maszyny, których kopie zapasowe są wykonywane bezpośrednio za pomocą agenta usługi MARS: trzy razy dziennie.  
Przenoszenie magazynu | Możesz przenosić magazyny usługi Recovery Services między subskrypcjami i grupami zasobów. [Dowiedz się więcej](backup-azure-move-recovery-services-vault.md).
Przenoszenie danych między magazynami | Przenoszenie kopii zapasowych danych między magazynami nie jest obsługiwane.
Typ replikacji magazynu | Możesz zmodyfikować typ replikacji magazynu (magazyn geograficznie nadmiarowy/magazyn lokalnie nadmiarowy) można zmodyfikować zanim zaczniesz przechowywać kopie zapasowe. Po rozpoczęciu wykonywania kopii zapasowych w magazynie nie można zmienić typu replikacji.



## <a name="on-premises-backup-support"></a>Obsługa lokalnych kopii zapasowych 

W przypadku kopii zapasowych maszyn lokalnych są obsługiwane poniższe elementy.

**Maszyna** | **Lokalizacja** | **Wykonywanie kopii zapasowej** | **Funkcje**
--- | --- | --- | ---
**Maszyna fizyczna/wirtualna z systemem Windows (bez serwera kopii zapasowych)** | Pliki, foldery, stan systemu | Wykonano kopię zapasową w magazynie usługi Recovery Services | Kopia zapasowa wykonywana trzy razy dziennie.<br/><br/> Bez kopii zapasowej uwzględniającej aplikację.<br/><br/> Przywracanie pliku, folderu i woluminu.
**Maszyna fizyczna/wirtualna z systemem Linux (bez serwera kopii zapasowych)** | Brak obsługi kopii zapasowych.
**Maszyna fizyczna/wirtualna z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Kopie zapasowe są wykonywane w programie DPM (na dysku połączonym lokalnie z serwerem programu DPM lub na taśmie).<br/><br/> Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna fizyczna/wirtualna z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Kopie zapasowe są wykonywane w usłudze MABS (na dysku połączonym lokalnie z serwerem usługi MABS). Taśma nie jest obsługiwana<br/><br/> Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.


## <a name="azure-vms"></a>Maszyny wirtualne platformy Azure

### <a name="azure-vm-limits"></a>Limity maszyn wirtualnych platformy Azure

**Limit** | **Szczegóły**
--- | ---
Dyski z danymi maszyn wirtualnych platformy Azure | Limit równy 16.
Rozmiar dysku z danymi maszyn wirtualnych platformy Azure | Każdy dysk może mieć rozmiar do 4095 GB.


### <a name="azure-vm-backup-options"></a>Opcje kopii zapasowych maszyn wirtualnych platformy Azure

W przypadku kopii zapasowych maszyn wirtualnych platformy Azure są obsługiwane poniższe elementy.

**Maszyna** | **Lokalizacja** | **Wykonywanie kopii zapasowej** | **Funkcje**
--- | --- | --- | ---
**Maszyny wirtualne platformy Azure (bez serwera kopii zapasowych)** | Pliki, foldery, stan systemu | Kopia zapasowa wykonywana w magazynie. | Kopia zapasowa wykonywana raz dziennie.<br/><br/> Kopia zapasowa z uwzględnieniem aplikacji dla maszyn wirtualnych z systemem Windows, spójna na poziomie plików kopia zapasowa maszyn wirtualnych z systemem Linux. Możesz skonfigurować spójność na poziomie aplikacji dla maszyn z systemem Linux za pomocą skryptów niestandardowych.<br/><br/> Przywracanie maszyny wirtualnej/dysku.<br/><br/> Nie można wykonać kopii zapasowej maszyny wirtualnej platformy Azure w lokalizacji lokalnej.
**Maszyna wirtualna platformy Azure z programem DPM** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Kopie zapasowe są wykonywane w programie DPM uruchomionym na platformie Azure (na dysku połączonym lokalnie z serwerem programu DPM). Taśma nie jest obsługiwana.<br/><br/> Program DPM wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.
**Maszyna wirtualna platformy Azure z usługą MABS** | Pliki, foldery, woluminy, stan systemu, dane aplikacji. | Kopie zapasowe są wykonywane w usłudze MABS uruchomionej na platformie Azure (na dysku połączonym lokalnie z serwerem usługi MABS). Taśma nie jest obsługiwana<br/><br/> Usługa MABS wykonuje kopie zapasowe w magazynie. | Migawki z uwzględnieniem aplikacji<br/><br/> Pełny stopień szczegółowości na potrzeby kopii zapasowych i odzyskiwania.<br/><br/> System Linux obsługujący maszyny wirtualne (Hyper-V/VMware).<br/><br/>. Oprogramowanie Oracle nie jest obsługiwane.





## <a name="linux-backup-support"></a>Obsługa kopii zapasowych systemu Linux

W przypadku kopii zapasowych maszyn z systemem Linux są obsługiwane poniższe elementy.

**Tworzenie kopii zapasowych** | **Linux (zatwierdzony przez Azure)**
--- | --- 
**Maszyna lokalna z systemem Linux (bez programu DPM lub usługi MABS)**. | Nie. Agenta usługi MARS można zainstalować tylko na maszynach z systemem Windows. 
**Maszyna wirtualna platformy Azure (bez programu DPM lub usługi MABS)** | Spójna na poziomie aplikacji kopia zapasowa wykonywana przy użyciu [skryptów niestandardowych](backup-azure-linux-app-consistent.md).<br/><br/> Odzyskiwanie na poziomie plików.<br/><br/> Przywracanie przez utworzenie maszyny wirtualnej z punktu odzyskiwania lub dysku.
**Maszyna lokalna/maszyna wirtualna platformy Azure z programem DPM** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare<br/><br/> Przywracanie maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare</br></br> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure
**Maszyna lokalna/maszyna wirtualna platformy Azure z usługą MABS** | Spójna na poziomie plików kopia zapasowa maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare<br/><br/> Przywracanie maszyn wirtualnych gościa z systemem Linux dla funkcji Hyper-V i programu VMWare</br></br> Spójna na poziomie plików kopia zapasowa niedostępna dla maszyn wirtualnych platformy Azure.

## <a name="disk-support"></a>Obsługa dysku

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

Usługa Backup obsługuje kompresję ruchu związanego z wykonywaniem kopii zapasowych zgodnie z opisem w poniższej tabeli. Należy pamiętać, że:

- W przypadku maszyn wirtualnych platformy Azure rozszerzenie maszyny wirtualnej odczytuje dane bezpośrednio z konta magazynu na platformie Azure w sieci magazynowania, więc kompresja tego ruchu nie jest konieczna.
- Jeśli używasz programu DPM lub usługi MABS, możesz skompresować dane przed wykonaniem ich kopii zapasowej w programie DPM/usłudze MABS, aby oszczędzić przepustowość. 

**Maszyna** | **Kompresja do usługi MABS/programu DPM (TCP)** | **Kompresja (HTTPS) do magazynu**
--- | --- | ---
Lokalne maszyny z systemem Windows bez programu DPM/usługi MABS | Nie dotyczy | Yes
Maszyny wirtualne platformy Azure | Nie dotyczy | Nie dotyczy
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z programem DPM | ![Yes][green] | ![Yes][green]
Lokalne maszyny wirtualne/maszyny wirtualne platformy Azure z usługą MABS | ![Yes][green] | ![Yes][green]



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

## <a name="next-steps"></a>Następne kroki

- [Tworzenie kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md)
- [Bezpośrednie wykonywanie kopii zapasowych maszyn z systemem Windows](tutorial-backup-windows-server-to-azure.md) bez serwera kopii zapasowych.
- [Konfigurowanie usługi MABS](backup-azure-microsoft-azure-backup.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w usłudze MABS.
- [Konfigurowanie programu DPM](backup-azure-dpm-introduction.md) na potrzeby kopii zapasowych na platformie Azure, a następnie wykonywanie kopii zapasowych obciążeń w programie DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
