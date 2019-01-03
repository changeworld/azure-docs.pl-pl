---
title: Dotyczące funkcji Kopia zapasowa maszyny Wirtualnej platformy Azure
description: Więcej informacji na temat kopii zapasowych maszyn wirtualnych platformy Azure i zanotuj niektóre najlepsze rozwiązania.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 9a80671a72f059e24a8cebc5de803af9261ad829
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743956"
---
# <a name="about-azure-vm-backup"></a>Dotyczące funkcji Kopia zapasowa maszyny Wirtualnej platformy Azure

W tym artykule opisano sposób, w jaki [usługi Azure Backup](backup-introduction-to-azure-backup.md) tworzy kopie zapasowe maszyn wirtualnych platformy Azure.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Oto, jak usługi Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure.

1. Dla maszyn wirtualnych platformy Azure, które są wybrane do kopii zapasowej usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej zgodnie z harmonogramem tworzenia kopii zapasowej, które określisz.
2. Usługa wyzwala rozszerzenie kopii zapasowej.
    - Użyj maszyn wirtualnych Windows _VMSnapshot_ rozszerzenia.
    - Użyj maszyn wirtualnych systemu Linux _VMSnapshotLinux_ rozszerzenia.
    - To rozszerzenie jest zainstalowane podczas pierwszego tworzenia kopii zapasowej maszyny Wirtualnej.
    - Aby zainstalować rozszerzenie, musi być uruchomiona maszyna wirtualna.
    - Jeśli maszyna wirtualna nie jest uruchomiona, usługa Backup utworzy migawkę powiązanego magazynu (ponieważ gdy maszyna wirtualna jest zatrzymana, nie występują zapisy aplikacji).
4. Rozszerzenie kopii zapasowej tworzy migawkę poziom miejsca do magazynowania, spójnego na poziomie aplikacji.
5. Po migawki dane są przesyłane do magazynu. Aby zmaksymalizować wydajność, usługa identyfikuje i przesyła jedynie te bloki danych, które uległy zmianie od czasu poprzedniej kopii zapasowej (delta).
5. Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Szyfrowanie danych

Usługa Azure Backup nie szyfruje danych jako część procesu tworzenia kopii zapasowej. Usługa Azure Backup obsługuje kopii zapasowych maszyn wirtualnych platformy Azure, które są szyfrowane za pomocą usługi Azure Disk Encryption.

- Kopii zapasowych maszyn wirtualnych szyfrowane za pomocą funkcji Bitlocker szyfrowania Key(BEK) tylko, a klucz szyfrowania bloków oraz klucz szyfrowania Key(KEK) jest obsługiwany, zarządzane i niezarządzane maszyn wirtualnych platformy Azure.
- BEK(secrets) i KEK(keys) kopii zapasowej są szyfrowane, dzięki czemu mogą odczytywać i używane tylko wtedy, gdy przywrócone do magazynu kluczy przez autoryzowanych użytkowników.
- Ponieważ klucz szyfrowania bloków jest również kopię zapasową, w scenariuszach, gdzie klucz szyfrowania bloków zostało utracone lub autoryzowanych użytkowników można przywrócić klucz szyfrowania bloków do magazynu kluczy i odzyskać zaszyfrowanych maszyn wirtualnych. Kluczy i wpisów tajnych zaszyfrowanych maszyn wirtualnych kopię zapasową w postaci zaszyfrowanej, dzięki czemu może odczytywać nieautoryzowanym użytkownikom ani platformy Azure, lub użyj kopii zapasowej kluczy i wpisów tajnych. Tylko użytkownicy z odpowiedni poziom uprawnień można kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, a także kluczy i wpisów tajnych.

## <a name="snapshot-consistency"></a>Migawka spójności

Do wykonania migawek w uruchomionej aplikacji, Azure kopie zapasowe migawek spójności aplikacji.

- **Maszyny wirtualne Windows**: W przypadku maszyn wirtualnych Windows usługa Backup służy do koordynowania z woluminów w tle kopii Service (VSS) uzyskanie spójnej migawki dysków maszyny Wirtualnej.
    - Domyślnie program Azure kopie zapasowe pełnych kopii zapasowych usługi VSS. [Dowiedz się więcej](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Jeśli chcesz zmienić ustawienie kopii zapasowych Azure przyjmuje kopie kopii zapasowych usługi VSS, należy ustawić następujący klucz rejestru:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
- **Maszyny wirtualne systemu Linux**: Upewnij się, że maszyny wirtualne systemu Linux spójny na poziomie aplikacji w przypadku usługi Azure Backup tworzy migawkę, umożliwia struktury i skryptu używanego po utworzeniu systemu Linux. Można napisać własne niestandardowe skrypty do zapewnienie spójności podczas wykonywania migawki maszyny Wirtualnej.
    -  Tylko usługa Azure Backup wywoła przed i po skryptów napisanych przez użytkownika.
    - Jeśli skryptu poprzedzającego i skryptu używanego po utworzeniu wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako spójnych z aplikacją. Jednak możesz ponoszą ostateczną odpowiedzialność za spójności aplikacji podczas za pomocą skryptów niestandardowych.
    - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o konfigurowaniu skryptów.


#### <a name="consistency-types"></a>Typy spójności

W poniższej tabeli opisano różne typy spójności.

**migawki** | **Na podstawie usługi VSS** | **Szczegóły** | **Odzyskiwanie**
--- | --- | --- | ---
**Spójna na poziomie aplikacji** | Tak (tylko Windows) | Wykonywanie kopii zapasowych spójnych aplikacji przechwytywania pamięci zawartości i oczekujących operacji We/Wy. Migawki spójne z aplikacji, użyj składnika zapisywania usługi VSS (lub pre lub używanego po nim skryptu dla systemu Linux), który zapewnienia spójności danych aplikacji, zanim wystąpi kopii zapasowej. | Podczas przywracania z migawki spójności aplikacji, maszyna wirtualna jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje mają swój początek w spójnym stanie.
**Spójne na poziomie systemu plików** | Tak (tylko Windows) |  Kopie zapasowe spójne z pliku zapewnić spójne tworzenie kopii zapasowych plików na dysku, robienie wszystkie pliki w tym samym czasie.<br/><br/> Punkty odzyskiwania usługi Azure Backup są spójne dla pliku:<br/><br/> -Linux maszyn wirtualnych, kopie zapasowe, które nie mają wstępnie lub używanego po nim skrypty lub które mają skryptu, który uległ awarii.<br/><br/> — Kopie zapasowe maszyn wirtualnych Windows gdzie Usługa VSS nie powiodła się. | W przypadku odzyskiwania przy użyciu migawek spójna na poziomie plików, maszyna wirtualna jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje potrzebuje, aby wdrożyć ich własny mechanizm "poprawki", aby upewnić się, że przywróconych danych są spójne.
**Spójne na poziomie awarii** | Nie | Spójność awarii często zdarza się, wyłączaniu Maszynie wirtualnej platformy Azure w czasie wykonywania kopii zapasowej.  Tylko dane, które już istnieje na dysku w czasie wykonywania kopii zapasowej jest przechwytywane i kopii zapasowej.<br/><br/> Punktu odzyskiwania spójnego na poziomie awarii nie gwarantuje spójności danych dotyczące systemu operacyjnego lub aplikacji. | Istnieją żadnej gwarancji, ale zazwyczaj rozruchów maszyn wirtualnych i zgodny z dyskiem zaznacz, aby naprawić błędy uszkodzenia. Wszystkie dane w pamięci lub zapisu, które nie zostały przeniesione do dysku zostaną utracone. Aplikacje implementować własne Weryfikacja danych. Na przykład dla aplikacji bazy danych, jeśli dziennik transakcji zawiera wpisy, które nie znajdują się w bazie danych, oprogramowanie bazy danych przedstawia dopóki dane są spójne.


## <a name="service-and-subscription-limits"></a>Limity usług i subskrypcji

Usługa Azure Backup ma numer limitów dotyczących subskrypcji i magazynów.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Wydajność tworzenia kopii zapasowej

### <a name="disk-considerations"></a>Zagadnienia dotyczące dysków

Kopia zapasowa próbuje wykonywane tak szybko, jak to możliwe, wykorzystywanie tylu zasobów, ponieważ jest to możliwe.

- Proces tworzenia kopii zapasowej w celu zmaksymalizowania jej szybkość działania, próbuje utworzyć kopię zapasową wszystkich dysków maszyny Wirtualnej w sposób równoległy.
- Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa próbuje utworzyć kopię zapasową wszystkie cztery dyski równolegle.
- Liczba dysków, w których powstaje kopia zapasowa jest najważniejszym czynnikiem umożliwiającym określenie ruchu kopii zapasowej konta magazynu.
- Wszystkie operacje We/Wy są ograniczone dzięki *docelowa przepustowość dla pojedynczego obiektu Blob*, która ma limit równy 60 MB na sekundę.
- Dla każdego dysku, w których powstaje kopia zapasowa kopia zapasowa Azure odczytuje bloków na dysku i przechowuje tylko zmienionych danych (przyrostową kopię zapasową). Poniższe wartości średniej przepływności można użyć do szacowania czasu wymaganego do utworzenia kopii zapasowej dysku o podanej wielkości.

    **Operacja** | **Najlepszego przypadku przepływności**
    --- | ---
    Początkowa kopia zapasowa | 160 MB/s |
    Przyrostowa kopia zapasowa | 640 MB/s <br><br> Przepływność znacznie spada Jeśli dane różnicowe są rozdzielane na dysku.|



### <a name="scheduling-considerations"></a>Zagadnienia dotyczące planowania

Planowanie tworzenia kopii zapasowej ma wpływ na wydajność.

- Jeśli konfigurujesz zasady, więc wszystkie maszyny wirtualne kopie zapasowe są tworzone w tym samym czasie, zostanie zaplanowane zakleszczenie ruchu jako próbuje utworzyć kopię zapasową wszystkich dysków w sposób równoległy proces tworzenia kopii zapasowej.
- Aby zmniejszyć ruch kopii zapasowej, tworzenie kopii zapasowych różnych maszyn wirtualnych w różnych porę dnia, bez nakładania się.


### <a name="time-considerations"></a>Zagadnienia dotyczące czasu

Podczas gdy większość kopii zapasowej jest zużywany czas czytania i kopiowanie danych, inne operacje Dodaj do łącznego czasu, które są potrzebne do tworzenia kopii zapasowej maszyny Wirtualnej:

- **Zainstaluj rozszerzenie kopii zapasowej**: Czas wymagany do zainstalowania lub zaktualizowania rozszerzenia kopii zapasowej.
- **Wyzwalacz migawki**: Czas poświęcony na wyzwalanie migawki. Migawki są wyzwalane blisko według harmonogramu wykonywania kopii zapasowej.
- **Czas oczekiwania w kolejce**: Od zadania tworzenia kopii zapasowej usługi procesów z wieloma kontami magazynu klienta w tym samym czasie dane migawki, nie mogą być od razu kopiowane do magazynu usługi Recovery Services. W godzinach szczytu obciążenia może potrwać do ośmiu godzin przed kopie zapasowe są przetwarzane. Jednak całkowity czas tworzenia kopii zapasowej maszyny Wirtualnej jest krótszy niż 24 godziny dla zasad tworzenia kopii zapasowej dzienny.
- **Tworzenie początkowej kopii zapasowej**: Mimo że całkowity czas tworzenia kopii zapasowych w mniej niż 24 godziny jest prawidłowa dla przyrostowych kopii zapasowych, nie może być dla pierwszej kopii zapasowej. Czas potrzebny będzie zależeć od rozmiaru danych i tworzenia kopii zapasowej.
- **Czas przesyłania danych**: Czas potrzebny do obliczenia przyrostowych zmian od poprzedniej kopii zapasowej i przenieść te zmiany usługi kopii zapasowej do magazynu z magazynu.

### <a name="factors-affecting-backup-time"></a>Czynniki wpływające na czas wykonywania kopii zapasowych

Kopia zapasowa składa się z dwóch etapów tworzenie migawek i przekazywania migawek do magazynu. Usługa Backup optymalizuje dla magazynu.

- Podczas transferu danych migawki do magazynu, usługa tylko przesyła przyrostowe zmiany z poprzednią migawkę.
- Aby określić zmiany przyrostowe, usługa oblicza sumę kontrolną bloków.
    - Jeśli blok zostanie zmieniony, blok jest rozpoznawany jako bloku do wysłania do magazynu.
    - Dalsze awarii usługi w każdej z określonych bloków, szuka możliwości zminimalizować danych do przesłania.
    - Po dokonaniu oceny wszystkie zmienione bloki, usługa łączy zmiany i wysyła je do magazynu.

Następujące sytuacje, które mogą wpływać na czas wykonywania kopii zapasowych:


- **Tworzenie początkowej kopii zapasowej dla nowo dodany dysk do maszyny Wirtualnej już chronione**: Jeśli maszyna wirtualna jest przechodzących przyrostowej kopii zapasowej, gdy nowy dysk zostanie dodany następnie kopii zapasowej pominąć SLA jeden dzień, w zależności od rozmiaru nowego dysku.
- **Pofragmentowane aplikacji**: Jeśli aplikacja jest nieprawidłowo skonfigurowana może nie być optymalne dla magazynu:
    - Jeśli migawka zawiera wiele zapisów niewielkich, fragmentacji, usługa spędza dodatkowy czas przetwarzania danych zapisanych przez aplikacje.
    - W przypadku aplikacji uruchomionych na maszynie Wirtualnej bloku zapisy aplikacji zalecane minimalne wynosząca 8 KB. Jeśli aplikacja korzysta z blokiem mniej niż 8 KB, odbywa się wydajność tworzenia kopii zapasowej.
- **Konto magazynu przeciążone**: Można było zaplanować tworzenie kopii zapasowej, gdy aplikacja jest uruchomiona w środowisku produkcyjnym lub jeśli więcej niż pięciu do dziesięciu dyski znajdują się w tym samym koncie magazynu.
- **Tryb wyboru (DW) spójność**: Dysków większych niż 1TB, dysków kopii zapasowej może być w trybie DW z kilku powodów:
    - Przenosi dysku zarządzanego jako część ponowne uruchomienie maszyny Wirtualnej.
    - Promuje migawki do podstawowego obiektu blob.


## <a name="restore-considerations"></a>Zagadnienia dotyczące przywracania

Operacja przywracania obejmuje dwa główne zadania: kopiowanie danych z magazynu do na wybranym koncie magazynu i tworzenie maszyny wirtualnej. Czas potrzebny do skopiowania danych z magazynu zależy od tego, gdzie kopie zapasowe są przechowywane na platformie Azure i lokalizacja konta magazynu. Czas poświęcony na kopiowanie danych zależy od:

- **Czas oczekiwania w kolejce**: Ponieważ procesów informatycznych przywrócić zadania z wielu kont magazynu, w tym samym czasie, przywracanie żądania są umieszczane w kolejce.
- **Czas kopiowania danych**: Dane są kopiowane z magazynu do konta magazynu. Przywróć czas zależy od operacji We/Wy i przepływność wybranego konta magazynu, która korzysta z usługi Azure Backup. W celu skrócenia czasu kopiowania podczas procesu przywracania, wybierz konto magazynu nie załadowano z innymi aplikacji zapisu i odczytu.

## <a name="best-practices"></a>Najlepsze praktyki

Sugerujemy następujące rozwiązania w zakresie podczas konfigurowania kopii zapasowych maszyn wirtualnych:

- Nie Planowanie tworzenia kopii zapasowych dla ponad 100 maszyn wirtualnych z jednego magazynu, w tym samym czasie.
- Planowanie kopii zapasowych maszyn wirtualnych poza godzinami poza szczytem. Dzięki temu usługa Backup korzysta z operacji We/Wy do przesyłania danych z konta magazynu do magazynu.
- Jeśli wykonujesz kopie zapasowe dysków zarządzanych, usługa Azure Backup obsługuje zarządzanie magazynem. Jeśli wykonujesz kopie zapasowe dysków niezarządzanych:
    - Upewnij się, że dotyczą zasady tworzenia kopii zapasowych maszyn wirtualnych, rozkładają się na wielu kontach magazynu.
    - Nie więcej niż 20 dysków z jednego konta magazynu powinna być chroniona przez ten sam harmonogram tworzenia kopii zapasowej.
    - Jeśli masz większe niż 20 dyskami na koncie magazynu, rozkłada się na tych maszynach wirtualnych wiele zasad, aby uzyskać wymagane operacje We/Wy podczas fazy transferu procesu tworzenia kopii zapasowej.
    - Nie należy przywracać maszynę Wirtualną z systemem w magazynie premium storage do tego samego konta magazynu. Jeśli proces operacji przywracania pokrywa się z operacji tworzenia kopii zapasowej, zmniejsza dostępne operacje We/Wy do utworzenia kopii zapasowej.
    - Dla kopii zapasowej maszyny Wirtualnej usługi Premium na stos kopii zapasowej maszyny Wirtualnej w wersji 1 należy przydzielić tylko 50% całkowitego konta miejsca do magazynowania, dzięki czemu usługa Backup może kopiowania migawki do konta magazynu i transferu danych z konta magazynu do magazynu.
- Zalecane jest używanie różnych kont magazynu, zamiast przy użyciu tego samego konta magazynu w celu przywracania maszyn wirtualnych z jednym magazynie. Spowoduje to uniknąć ograniczania przepustowości i spowodować 100% przywracania zakończone sukcesem dobrą wydajność.
- Przywracanie z warstwy 1 magazynu warstwy zostanie ukończona w ciągu kilku minut przed Przywracanie magazynu warstwy 2, które zajmuje kilka godzin. Zaleca się używać [funkcji błyskawiczne RP](backup-upgrade-to-vm-backup-stack-v2.md) Aby szybciej przeprowadzać operacje przywracania. Jest to tylko odpowiednie zarządzanych maszyn wirtualnych platformy Azure.


## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowej

Maszyny wirtualne platformy Azure, kopii zapasowej w usłudze Azure Backup są podlegają [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Nie można uruchomić rozliczeń, dopiero po zakończeniu pierwszego pomyślne tworzenie kopii zapasowych. W tym momencie rozpoczyna się okres rozliczeniowy magazynu i chronionych wystąpień.
- Rozliczeń będzie kontynuowane, dopóki ma żadnych kopii zapasowej danych przechowywanych w magazynie dla maszyny wirtualnej. Jeśli użytkownik zaprzestanie ochrony na maszynie wirtualnej, ale istnieją dane kopii zapasowej maszyny wirtualnej w magazynie, nadal rozliczeń.
- Rozliczenia dla określonej maszyny Wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana, a wszystkie dane kopii zapasowej zostaną usunięte.
- Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar Ostatnia pomyślna kopia zapasowa maszyny Wirtualnej staje się rozmiar chronionego wystąpienia, umożliwiający miesięczny rachunek.
- Chronione wystąpienia jest obliczany na *rzeczywiste* rozmiar maszyny wirtualnej, która jest sumą wszystkich danych na maszynie wirtualnej, z wyłączeniem magazynu tymczasowego.
- Cennik zależy od rzeczywistych danych, które są przechowywane na dysku danych.
- Cennik tworzenia kopii zapasowych maszyn wirtualnych nie jest oparty na maksymalny obsługiwany rozmiar dla każdego dysku danych dołączonego do maszyny wirtualnej.
- Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych, który jest przechowywany w usłudze Azure Backup, który jest sumą rzeczywiste dane w poszczególnych punktach odzyskiwania.

Za przykład niech posłuży A2 standardowy rozmiar maszyny wirtualnej, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 4 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

| Typ dysku | Maks. rozmiar | Rzeczywiste dane |
| --------- | -------- | ----------- |
| Dysk systemu operacyjnego |4095 GB |17 GB |
| Dysk lokalny / tymczasowe dysku |135 GB |5 GB (nie dołączona do tworzenia kopii zapasowych) |
| Dysk danych 1 |4095 GB |30 GB |
| Dysk danych 2 |4095 GB |0 GB |

- Rzeczywisty rozmiar maszyny wirtualnej maszyny Wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB.
- Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięczny rachunek.
- Wzrostem ilości danych na maszynie wirtualnej rozmiar chronionego wystąpienia w związku z tym używany dla zmiany w rozliczeniach.


## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z procesu kopii zapasowej i zagadnienia dotyczące wydajności, wykonaj następujące czynności:

- Pobierz [arkusz kalkulacyjny programu Excel planowania pojemności na potrzeby](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) możesz wypróbować dysku i kopii zapasowych, planowanie liczb.
- [Dowiedz się więcej o](../virtual-machines/windows/premium-storage-performance.md) Dostrajanie aplikacji, aby uzyskać optymalną wydajność z usługą Azure storage. Fokus artykuł w magazynie premium storage, ale jest również zastosowanie w przypadku dysków magazynu w warstwie standardowa.
- [Rozpoczynanie pracy](backup-azure-arm-vms-prepare.md) przy użyciu kopii zapasowej, przeglądając maszyn wirtualnych z osłoną i ograniczenia, tworzenie magazynu i przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych.
