---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: W tym artykule dowiesz się, jak usługa Azure Backup kopie zapasowe maszyn wirtualnych platformy Azure i jak postępować zgodnie z najlepszymi rozwiązaniami.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f4b36f57362607a13c09896cd7109596aba0a852
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415965"
---
# <a name="an-overview-of-azure-vm-backup"></a>Omówienie kopii zapasowej maszyny Wirtualnej platformy Azure

W tym artykule opisano, jak [usługa Azure Backup](backup-introduction-to-azure-backup.md) kopie zapasowe maszyn wirtualnych platformy Azure (maszyny wirtualne).

Usługa Azure Backup udostępnia niezależne i izolowane kopie zapasowe w celu ochrony przed niezamierzone niszczenie danych na maszynach wirtualnych. Kopie zapasowe są przechowywane w magazynie usług odzyskiwania z wbudowanym zarządzaniem punktami odzyskiwania. Konfiguracja i skalowanie są proste, kopie zapasowe są zoptymalizowane i można łatwo przywrócić w razie potrzeby.

W ramach procesu tworzenia kopii zapasowej [jest pobierana migawka,](#snapshot-creation)a dane są przesyłane do magazynu usług odzyskiwania bez wpływu na obciążenia produkcyjne. Migawka zapewnia różne poziomy spójności, jak opisano [w tym miejscu.](#snapshot-consistency)

Usługa Azure Backup ma również wyspecjalizowane oferty dla obciążeń bazy danych, takich jak [SQL Server](backup-azure-sql-database.md) i [SAP HANA,](sap-hana-db-about.md) które są obsługujące obciążenie pracą, oferują 15-minutowy cel punktu odzyskiwania (cel punktu odzyskiwania) i umożliwiają tworzenie kopii zapasowych i przywracanie poszczególnych baz danych.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Poniżej opisano, jak usługa Azure Backup kończy tworzenie kopii zapasowej dla maszyn wirtualnych platformy Azure:

1. W przypadku maszyn wirtualnych platformy Azure wybranych do tworzenia kopii zapasowych usługa Azure Backup rozpoczyna zadanie tworzenia kopii zapasowej zgodnie z określonym harmonogramem tworzenia kopii zapasowych.
1. Podczas pierwszej kopii zapasowej rozszerzenie kopii zapasowej jest instalowane na maszynie Wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych z systemem Windows jest zainstalowane [rozszerzenie VMSnapshot.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows)
    - W przypadku maszyn wirtualnych z systemem Linux jest zainstalowane [rozszerzenie VMSnapshotLinux.](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-linux)
1. W przypadku uruchomionych maszyn wirtualnych z systemem Windows współrzędne kopii zapasowej z usługą windows volume copy service (VSS) w celu uzyskania spójnej dla aplikacji migawki maszyny wirtualnej.
    - Domyślnie kopia zapasowa wykonuje pełne kopie zapasowe usługi VSS.
    - Jeśli kopia zapasowa nie może wykonać migawki spójne z aplikacją, a następnie zajmuje migawkę spójne z plikiem magazynu źródłowego (ponieważ nie zapisuje aplikacji występuje, gdy maszyna wirtualna jest zatrzymana).
1. W przypadku maszyn wirtualnych z systemem Linux kopia zapasowa wykonuje kopię zapasową spójną z plikami. W przypadku migawek spójnych z aplikacją należy ręcznie dostosować skrypty przed/post.
1. Po wykonaniu migawki przez kopię zapasową przesyła dane do magazynu.
    - Kopia zapasowa jest optymalizowana przez równoległe tworzenie kopii zapasowych każdego dysku maszyny Wirtualnej.
    - Dla każdego dysku, który jest kopią zapasową, usługa Azure Backup odczytuje bloki na dysku i identyfikuje i przesyła tylko bloki danych, które uległy zmianie (delta) od czasu poprzedniej kopii zapasowej.
    - Dane migawki mogą nie zostać natychmiast skopiowane do przechowalni. Może to potrwać kilka godzin w godzinach szczytu. Całkowity czas tworzenia kopii zapasowej maszyny Wirtualnej będzie krótszy niż 24 godziny w przypadku zasad dziennego tworzenia kopii zapasowych.
1. Zmiany wprowadzone w maszynie Wirtualnej systemu Windows po włączeniu usługi Azure Backup to:
    - Program Microsoft Visual C++ 2013 Redystrybucyjny(x64) — 12.0.40660 jest zainstalowany na maszynie wirtualnej
    - Typ uruchamiania usługi kopiowania woluminów w tle (VSS) został zmieniony na automatyczny z ręcznego
    - Dodano usługę IaaSVmProvider dla systemu Windows

1. Po zakończeniu transferu danych migawka jest usuwana i tworzony jest punkt odzyskiwania.

![Architektura kopii zapasowej maszyny wirtualnej platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Szyfrowanie kopii zapasowych maszyn wirtualnych platformy Azure

Podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure za pomocą usługi Azure Backup maszyny wirtualne są szyfrowane w spoczynku przy pomocą szyfrowania usługi magazynu (SSE). Usługa Azure Backup może również wykonać kopię zapasową maszyn wirtualnych platformy Azure, które są szyfrowane przy użyciu szyfrowania dysków platformy Azure.

**Szyfrowania** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**Usługa Azure Disk Encryption** | Szyfrowanie dysków platformy Azure szyfruje zarówno dyski systemu operacyjnego, jak i dysków danych dla maszyn wirtualnych platformy Azure.<br/><br/> Usługa Azure Disk Encryption integruje się z kluczami szyfrowania funkcji BitLocker (BEK), które są chronione w magazynie kluczy jako wpisy tajne. Szyfrowanie dysków platformy Azure integruje się również z kluczami szyfrowania kluczy usługi Azure Key Vault (KEK). | Usługa Azure Backup obsługuje tworzenie kopii zapasowych zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure zaszyfrowanych tylko za pomocą bek lub bek wraz z plikami KEK.<br/><br/> Zarówno BEKs i KEKs są archiwizowane i szyfrowane.<br/><br/> Ponieważ kopie zapasowe są kopie zapasowe ket i BEK, użytkownicy z niezbędnymi uprawnieniami można przywrócić klucze i wpisy tajne z powrotem do magazynu kluczy w razie potrzeby. Ci użytkownicy mogą również odzyskać zaszyfrowaną maszynę wirtualną.<br/><br/> Zaszyfrowane klucze i wpisy tajne nie mogą być odczytywane przez nieautoryzowanych użytkowników ani przez platformę Azure.
**Sse** | Dzięki usłudze SSE usługa Azure Storage zapewnia szyfrowanie w spoczynku, automatycznie szyfrując dane przed ich przechowywaniem. Usługa Azure Storage również odszyfrowuje dane przed ich pobraniem. | Usługa Azure Backup używa usługi SSE do szyfrowania maszyn wirtualnych platformy Azure w spoczynku.

W przypadku zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure kopia zapasowa obsługuje zarówno maszyny wirtualne szyfrowane tylko za pomocą bek, jak i maszyny wirtualne zaszyfrowane za pomocą zestawów BEK wraz z plikami KEK.

Kopie zapasowe BEK (wpisy tajne) i KEKs (klucze) są szyfrowane. Mogą być odczytywane i używane tylko wtedy, gdy są przywracane do magazynu kluczy przez autoryzowanych użytkowników. Ani nieautoryzowani użytkownicy, ani platforma Azure nie mogą odczytywać ani używać kopii zapasowych kluczy lub wpisów tajnych.

Beks są również kopii zapasowej. Tak więc, jeśli beks zostaną utracone, autoryzowani użytkownicy mogą przywrócić BEK do magazynu kluczy i odzyskać zaszyfrowane maszyny wirtualne. Tylko użytkownicy z niezbędnym poziomem uprawnień mogą wywrzenia zapasowego i przywracania zaszyfrowanych maszyn wirtualnych lub kluczy i wpisów tajnych.

## <a name="snapshot-creation"></a>Tworzenie migawki

Usługa Azure Backup wykonuje migawki zgodnie z harmonogramem tworzenia kopii zapasowych.

- **Maszyny wirtualne z systemem Windows:** W przypadku maszyn wirtualnych z systemem Windows usługa kopia zapasowa współrzędne z usługą VSS w celu wykonywania spójnej z aplikacjami migawki dysków maszyn wirtualnych.  Domyślnie usługa Azure Backup wykonuje pełną kopię zapasową vss (obcina dzienniki aplikacji, takich jak SQL Server w czasie tworzenia kopii zapasowej, aby uzyskać spójną kopię zapasową na poziomie aplikacji).  Jeśli używasz bazy danych programu SQL Server w kopii zapasowej maszyny Wirtualnej platformy Azure, możesz zmodyfikować to ustawienie, aby wykonać kopię zapasową vss copy (w celu zachowania dzienników). Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#troubleshoot-vm-snapshot-issues).

- **Maszyny wirtualne z systemem Linux:** Aby zrobić spójne z aplikacjami migawki maszyn wirtualnych z systemem Linux, użyj struktury pre-script i post-script systemu Linux, aby napisać własne skrypty niestandardowe, aby zapewnić spójność.

  - Usługa Azure Backup wywołuje tylko skrypty pre/post napisane przez Ciebie.
  - Jeśli skrypty wstępne i skrypty po pomyślnie wykonać, usługa Azure Backup oznacza punkt odzyskiwania jako spójne aplikacji. Jednak gdy używasz skryptów niestandardowych, ostatecznie odpowiadasz za spójność aplikacji.
  - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o konfigurowaniu skryptów.

## <a name="snapshot-consistency"></a>Spójność migawek

W poniższej tabeli wyjaśniono różne typy spójności migawki:

**Migawka** | **Szczegóły** | **Odzyskiwanie** | **Zagadnienie**
--- | --- | --- | ---
**Spójne z aplikacjami** | Kopie zapasowe spójne z aplikacjami przechwytują zawartość pamięci i oczekujące operacje we/wy. Migawki spójne z aplikacjami używają modułu zapisującego vss (lub skryptów przed/post dla systemu Linux), aby zapewnić spójność danych aplikacji przed wystąpieniem kopii zapasowej. | Podczas odzyskiwania maszyny Wirtualnej z migawki spójne z aplikacją, maszyny Wirtualnej uruchamia się. Nie ma uszkodzenia lub utraty danych. Aplikacje uruchamiają się w spójnym stanie. | Windows: Wszystkie moduły zapisujące vss powiodło się<br/><br/> Linux: Skrypty przed/post są skonfigurowane i powiodły się
**System plików spójny** | Spójne kopie zapasowe systemu plików zapewniają spójność, robiąc migawkę wszystkich plików w tym samym czasie.<br/><br/> | Podczas odzyskiwania maszyny Wirtualnej z migawki spójne systemu plików, maszyny Wirtualnej uruchamia się. Nie ma uszkodzenia lub utraty danych. Aplikacje muszą zaimplementować własny mechanizm "fix-up", aby upewnić się, że przywrócone dane są spójne. | Windows: niektóre moduły zapisujące usługi VSS nie powiodły się <br/><br/> Linux: Domyślne (jeśli skrypty przed/post nie są skonfigurowane lub nie powiodły się)
**Ujmuje się w crash** | Migawki zgodne ze crash-consistent zazwyczaj występują, jeśli maszyna wirtualna platformy Azure zostanie zamknięta w czasie wykonywania kopii zapasowej. Tylko dane, które już istnieją na dysku w momencie tworzenia kopii zapasowej jest przechwytywany i kopii zapasowej. | Rozpoczyna się od procesu rozruchu maszyny Wirtualnej, po którym następuje sprawdzenie dysku w celu naprawienia błędów uszkodzenia. Wszelkie dane w pamięci lub operacje zapisu, które nie zostały przeniesione na dysk przed awarią zostaną utracone. Aplikacje implementują własną weryfikację danych. Na przykład aplikacja bazy danych może używać dziennika transakcji do weryfikacji. Jeśli dziennik transakcji zawiera wpisy, które nie są w bazie danych, oprogramowanie bazy danych rolkach transakcji z powrotem, dopóki dane są spójne. | Maszyna wirtualna jest w stanie zamknięcia (zatrzymanego/ cofniętego przydziału).

## <a name="backup-and-restore-considerations"></a>Zagadnienia dotyczące tworzenia kopii zapasowych i przywracania

**Zagadnienie** | **Szczegóły**
--- | ---
**Disk** | Kopia zapasowa dysków maszyn wirtualnych jest równoległa. Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa Kopia zapasowa próbuje wykonać kopię zapasową wszystkich czterech dysków równolegle. Kopia zapasowa jest przyrostowa (tylko zmienione dane).
**Planowanie** |  Aby zmniejszyć ruch kopii zapasowej, wykonaj kopię zapasową różnych maszyn wirtualnych o różnych porach dnia i upewnij się, że czasy nie nakładają się na siebie. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje korki.
**Przygotowywanie kopii zapasowych** | Należy pamiętać o czasie potrzebnym do przygotowania kopii zapasowej. Czas przygotowania obejmuje instalowanie lub aktualizowanie rozszerzenia kopii zapasowej i wyzwalanie migawki zgodnie z harmonogramem tworzenia kopii zapasowych.
**Przesyłanie danych** | Należy wziąć pod uwagę czas potrzebny do usługi Azure Backup do zidentyfikowania przyrostowych zmian z poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej usługa Azure Backup określa zmiany, obliczając sumę kontrolną bloku. Jeśli blok zostanie zmieniony, zostanie oznaczony do przeniesienia do przechowalni. Usługa analizuje zidentyfikowane bloki, aby spróbować jeszcze bardziej zminimalizować ilość danych do przesłania. Po ocenie wszystkich zmienionych bloków usługa Azure Backup przenosi zmiany do magazynu.<br/><br/> Może istnieć opóźnienie między zrobieniem migawki a skopiowaniem jej do magazynu.<br/><br/> W godzinach szczytu przetworzenie kopii zapasowych może potrwać do ośmiu godzin. Czas tworzenia kopii zapasowej maszyny Wirtualnej będzie krótszy niż 24 godziny dla codziennej kopii zapasowej.
**Początkowa kopia zapasowa** | Chociaż całkowity czas wykonywania kopii zapasowych dla przyrostowych kopii zapasowych jest krótszy niż 24 godziny, może to nie być w przypadku pierwszej kopii zapasowej. Czas potrzebny na początkową kopię zapasową zależy od rozmiaru danych i czasu przetwarzania kopii zapasowej.
**Kolejka przywracania** | Procesy usługi Azure Backup przywracają zadania z wielu kont magazynu w tym samym czasie i umieszcza żądania przywracania w kolejce.
**Przywróć kopię** | Podczas procesu przywracania dane są kopiowane z magazynu na konto magazynu.<br/><br/> Całkowity czas przywracania zależy od operacji we/wy na sekundę (IOPS) i przepływności konta magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, które nie jest ładowane z innymi zapisami i odczytami aplikacji.

### <a name="backup-performance"></a>Wydajność tworzenia kopii zapasowych

Te typowe scenariusze mogą mieć wpływ na całkowity czas tworzenia kopii zapasowej:

- **Dodawanie nowego dysku do chronionej maszyny Wirtualnej platformy Azure:** Jeśli maszyna wirtualna jest poddawana przyrostowej kopii zapasowej i dodaje się nowy dysk, czas tworzenia kopii zapasowej wzrośnie. Całkowity czas tworzenia kopii zapasowej może trwać dłużej niż 24 godziny z powodu początkowej replikacji nowego dysku wraz z replikacją różnicową istniejących dysków.
- **Pofragmentowane dyski:** Operacje tworzenia kopii zapasowych są szybsze, gdy zmiany dysku są ciągłe. Jeśli zmiany są rozłożone i pofragmentowane na dysku, kopia zapasowa będzie wolniejsza.
- **Zmiany na dysku:** Jeśli chronione dyski, które są poddawane przyrostowej kopii zapasowej mają dzienny współczynnik zmian ponad 200 GB, tworzenie kopii zapasowej może zająć dużo czasu (ponad osiem godzin).
- **Wersje kopii zapasowych:** Najnowsza wersja kopii zapasowej (znana jako wersja przywracania błyskawicznego) używa bardziej zoptymalizowanego procesu niż porównanie sum kontrolnych do identyfikowania zmian. Jeśli jednak używasz narzędzia Natychmiastowe przywracanie i usunięto migawkę kopii zapasowej, kopia zapasowa przełącza się do porównania sumy kontrolnej. W takim przypadku operacja tworzenia kopii zapasowej przekroczy 24 godziny (lub zakończy się niepowodzeniem).

## <a name="best-practices"></a>Najlepsze rozwiązania

Podczas konfigurowania kopii zapasowych maszyn wirtualnych zalecamy następujące następujące rozwiązania:

- Zmodyfikuj domyślne godziny harmonogramu ustawione w zasadach. Na przykład jeśli domyślny czas w zasadach to 12:00 AM, należy zwiększać czas o kilka minut, tak aby zasoby były optymalnie używane.
- Jeśli przywracasz maszyny wirtualne z jednego magazynu, zdecydowanie zaleca się użycie różnych [kont magazynu ogólnego przeznaczenia w wersji 2,](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) aby upewnić się, że konto magazynu docelowego nie zostanie ograniczone. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu. Na przykład jeśli 10 maszyn wirtualnych są przywracane, użyj 10 różnych kont magazynu.
- W przypadku tworzenia kopii zapasowych maszyn wirtualnych korzystających z magazynu w wersji premium za pomocą funkcji Natychmiastowe przywracanie zaleca się przydzielenie *50%* wolnego miejsca w całkowitej ilości przydzielonego miejsca do magazynowania, co jest wymagane **tylko** dla pierwszej kopii zapasowej. 50% wolnego miejsca nie jest wymagane dla kopii zapasowych po zakończeniu pierwszej kopii zapasowej
- Limit liczby dysków na konto magazynu jest w stosunku do tego, jak mocno dyski są dostępne dla aplikacji, które są uruchomione na infrastrukturze jako usługi (IaaS) VM. W praktyce, jeśli 5 do 10 dysków lub więcej są obecne na jednym koncie magazynu, zrównoważyć obciążenie, przenosząc niektóre dyski do oddzielnych kont magazynu.

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowych

Maszyny wirtualne platformy Azure, której kopię zapasową jest za pomocą usługi Azure Backup, podlegają [cennikom usługi Azure Backup.](https://azure.microsoft.com/pricing/details/backup/)

Rozliczenia nie rozpoczynają się, dopóki nie zakończy się pierwsza pomyślna kopia zapasowa. W tym momencie rozpoczyna się rozliczenia zarówno dla magazynu, jak i chronionych maszyn wirtualnych. Rozliczenia są kontynuowane tak długo, jak wszystkie dane kopii zapasowej maszyny Wirtualnej są przechowywane w magazynie. Jeśli zatrzymasz ochronę maszyny Wirtualnej, ale dane kopii zapasowej dla maszyny Wirtualnej istnieje w magazynie, rozliczeń w dalszym ciągu.

Rozliczenia dla określonej maszyny Wirtualnej zatrzymuje się tylko wtedy, gdy ochrona jest zatrzymana i wszystkie dane kopii zapasowej są usuwane. Gdy ochrona zatrzymuje się i nie ma żadnych aktywnych zadań tworzenia kopii zapasowych, rozmiar ostatniej pomyślnej kopii zapasowej maszyny Wirtualnej staje się chronionym rozmiarem wystąpienia używanym dla miesięcznego rachunku.

Obliczanie rozmiaru wystąpienia chronionego jest oparte na *rzeczywistym* rozmiarze maszyny Wirtualnej. Rozmiar maszyny Wirtualnej jest sumą wszystkich danych na maszynie Wirtualnej, z wyłączeniem magazynu tymczasowego. Cennik jest oparty na rzeczywistych danych, które są przechowywane na dyskach z danymi, a nie na maksymalny rozmiar obsługiwany dla każdego dysku danych, który jest dołączony do maszyny Wirtualnej.

Podobnie rachunek za magazyn kopii zapasowej jest oparty na ilości danych przechowywanych w usłudze Azure Backup, która jest sumą rzeczywistych danych w każdym punkcie odzyskiwania.

Na przykład weź maszynę wirtualną o standardzie A2, która ma dwa dodatkowe dyski danych o maksymalnym rozmiarze 32 TB każdy. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**Disk** | **Maksymalny rozmiar** | **Aktualne dane**
--- | --- | ---
Dysk systemu operacyjnego | 32 TB | 17 GB
Dysk lokalny/tymczasowy | 135 GB | 5 GB (nie dołączone do kopii zapasowej)
Dysk danych 1 | 32 TB| 30 GB
Dysk danych 2 | 32 TB | 0 GB

Rzeczywisty rozmiar maszyny Wirtualnej w tym przypadku wynosi 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar wystąpienia chronionego (47 GB) staje się podstawą miesięcznego rachunku. Wraz ze wzrostem ilości danych w maszynie wirtualnej rozmiar wystąpienia chronionego używane do rozliczeń zmienia się do dopasowania.

## <a name="next-steps"></a>Następne kroki

Teraz [przygotuj się do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure.](backup-azure-arm-vms-prepare.md)
