---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: Więcej informacji na temat kopii zapasowych maszyn wirtualnych platformy Azure i zanotuj niektóre najlepsze rozwiązania.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: c38c457bbf428d7252cf57168685201a2ca227ba
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446804"
---
# <a name="about-azure-vm-backup"></a>Informacje o kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano sposób, w jaki [usługi Azure Backup](backup-introduction-to-azure-backup.md) tworzy kopie zapasowe maszyn wirtualnych platformy Azure.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Oto, jak usługi Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure.

1. Dla maszyn wirtualnych platformy Azure, które są wybrane do kopii zapasowej usługa Azure Backup Inicjuje zadania tworzenia kopii zapasowej zgodnie z harmonogramem tworzenia kopii zapasowej, które określisz.
2. Podczas pierwszego tworzenia kopii zapasowej kopii zapasowej rozszerzenie jest zainstalowane na maszynie Wirtualnej, jeśli jest uruchomiony.
    - W przypadku maszyn wirtualnych Windows _VMSnapshot_ rozszerzenie jest zainstalowane.
    - W przypadku maszyn wirtualnych systemu Linux _VMSnapshotLinux_ rozszerzenie jest zainstalowane.
3. W przypadku maszyn wirtualnych Windows, które są uruchomione kopia zapasowa służy do koordynowania z usługami VSS do spójnego na poziomie aplikacji migawki maszyny wirtualnej.
    - Domyślnie kopie zapasowe pełnych kopii zapasowych usługi VSS.
    - Jeśli kopia zapasowa nie migawki spójności aplikacji, a następnie tworzy spójna na poziomie plików migawkę bazowego magazynu, (ponieważ nie występują zapisy aplikacji, podczas gdy maszyna wirtualna jest zatrzymana).
4. Dla kopii zapasowych maszyn wirtualnych z systemem Linux ma spójna na poziomie plików kopia zapasowa. Dla migawek spójności aplikacji, musisz ręcznie dostosować wstępnie lub używanego po nim skrypty.
5. Po migawki dane są przesyłane do magazynu. 
    - Kopia zapasowa jest zoptymalizowany przez utworzenie kopii zapasowej każdego dysku maszyny Wirtualnej w sposób równoległy.
    - Dla każdego dysku, w których powstaje kopia zapasowa kopia zapasowa Azure odczytuje bloków na dysku i identyfikuje i przesyła jedynie te bloki danych, które zostały zmienione od poprzedniej kopii zapasowej (delta).
    - Po migawki, dane są przesyłane do magazynu.
    - Dane migawki nie może zostać natychmiast skopiowany do magazynu. Może upłynąć kilka godzin w godzinach szczytu. Łączny czas tworzenia kopii zapasowej dla maszyny Wirtualnej będzie mniejsza tego 24 godziny na codzienne zasady tworzenia kopii zapasowych.

6. Po ukończeniu przesyłania danych migawka jest usuwana, a utworzony zostaje punkt odzyskiwania.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encrypting-azure-vm-backups"></a>Szyfrowanie kopii zapasowych maszyn wirtualnych platformy Azure

Podczas wykonywania kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup, maszyn wirtualnych są szyfrowane, gdy szyfrowanie usługi Storage (SSE). Ponadto usługi Azure Backup można utworzyć kopię zapasową maszyn wirtualnych platformy Azure są szyfrowane za pomocą szyfrowania dysków Azure (ADE).


**Szyfrowanie** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**ADE** | ADE są szyfrowane zarówno systemu operacyjnego i dysków z danymi maszyn wirtualnych platformy Azure.<br/><br/> ADE integruje się przy użyciu kluczy szyfrowania funkcją BitLocker (klucz szyfrowania bloków) chronione w magazynie kluczy jako wpisy tajne, lub przy użyciu kluczy szyfrowania klucza usługi Azure Key Vault (KEK). | Usługa Azure Backup obsługuje kopię zapasową zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure zaszyfrowane przy użyciu tylko bloków lub za pomocą klucza szyfrowania bloków wraz z KEK.<br/><br/> Zarówno klucz szyfrowania bloków i kopie zapasowe i szyfrowane.<br/><br/> Ponieważ KEK oraz klucz szyfrowania bloków kopię zapasową, w razie potrzeby użytkownicy z uprawnieniami można przywrócić klucze i wpisy tajne usługi key vault i odzyskać zaszyfrowanych maszyn wirtualnych.<br/><br/> Nie można odczytać zaszyfrowanych kluczy i wpisów tajnych, przez nieautoryzowanych użytkowników lub przez platformę Azure.
**SSE** | Za pomocą funkcji SSE usługa Azure storage oferuje szyfrowanie w spoczynku, automatycznie szyfrując dane przed przekazaniem jej i odszyfrowuje je przed pobierania. | Usługa Azure Backup używa SSE dla szyfrowanie rest maszyn wirtualnych platformy Azure.

- Kopia zapasowa maszyny wirtualne szyfrowane przy użyciu funkcji BitLocker szyfrowania Key (klucz szyfrowania bloków) tylko i klucz szyfrowania bloków oraz klucz szyfrowania Key(KEK) jest obsługiwana w zarządzanym i niezarządzanym maszyn wirtualnych platformy Azure.
- Kopię klucza szyfrowania bloków (hasła) i KEK (klucze) są zaszyfrowane. Można je odczytać i używane tylko wtedy, gdy przywrócone do magazynu kluczy przez autoryzowanych użytkowników. Nieautoryzowani użytkownicy ani platformy Azure nie może odczytać lub użyj kopii zapasowej kluczy lub wpisów tajnych.
- Ponieważ klucz szyfrowania bloków jest również kopię zapasową, w przypadku utraty klucza szyfrowania bloków, autoryzowanych użytkowników można przywrócić klucz szyfrowania bloków do magazynu kluczy, a następnie odzyskać zaszyfrowanych maszyn wirtualnych. 
- Tylko użytkownicy z odpowiedni poziom uprawnień można i przywracanie kopii zapasowej zaszyfrowanych maszyn wirtualnych, a także kluczy i wpisów tajnych.



## <a name="taking-snapshots"></a>Tworzenie migawek

Migawki kopii zapasowej platformy Azure zgodnie z harmonogramem tworzenia kopii zapasowej. 

- **Maszyny wirtualne z systemem Windows**: W przypadku maszyn wirtualnych Windows usługa Backup służy do koordynowania z woluminów w tle kopii Service (VSS) do spójnego na poziomie aplikacji migawki dysków maszyny Wirtualnej.
    - Domyślnie program Azure kopie zapasowe pełnych kopii zapasowych usługi VSS. [Dowiedz się więcej](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
    - Jeśli chcesz zmienić ustawienie kopii zapasowych Azure przyjmuje kopie kopii zapasowych usługi VSS, należy ustawić następujący klucz rejestru w wierszu polecenia: **Polecenie REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**.
- **Maszyny wirtualne z systemem Linux**: Jeśli chcesz móc spójne migawki maszyny Wirtualnej systemu Linux, użyj skryptu poprzedzającego systemu Linux i po utworzeniu skryptu platformę, by pisania własnych skryptów niestandardowych w celu zapewnienia spójności.
    -  Usługa Azure Backup wywołuje tylko skrypty pre lub używanego po nim zapisane przez użytkownika.
    - Jeśli skryptu poprzedzającego i skryptu używanego po utworzeniu wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako spójnych z aplikacją. Jednak możesz ponoszą ostateczną odpowiedzialność za spójności aplikacji podczas za pomocą skryptów niestandardowych.
    - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o konfigurowaniu skryptów.


### <a name="snapshot-consistency"></a>Migawka spójności

W poniższej tabeli opisano różne typy spójności.

**migawki** | **Szczegóły** | **Odzyskiwanie** | **Zagadnienia**
--- | --- | --- | ---
**Spójna na poziomie aplikacji** | Wykonywanie kopii zapasowych spójnych aplikacji przechwytywania pamięci zawartości i oczekujących operacji We/Wy. Migawki spójne z aplikacji, użyj składnika zapisywania usługi VSS (lub pre lub używanego po nim skryptu dla systemu Linux), który zapewnienia spójności danych aplikacji, zanim wystąpi kopii zapasowej. | Podczas przywracania z migawki spójności aplikacji, maszyna wirtualna jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje mają swój początek w spójnym stanie. | W systemie Windows: Wszystkie składniki zapisywania usługi VSS powiodło się.<br/><br/> W systemie Linux: Wstępnie lub używanego po nim skrypty są konfigurowane i zakończyło się pomyślnie
**Spójne na poziomie systemu plików** | Kopie zapasowe spójne z pliku zapewnić spójne tworzenie kopii zapasowych plików na dysku, robienie wszystkie pliki w tym samym czasie.<br/><br/> | W przypadku odzyskiwania przy użyciu migawek spójna na poziomie plików, maszyna wirtualna jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje muszą implementować ich własny mechanizm "poprawki", aby upewnić się, że przywróconych danych są spójne. | W systemie Windows: Niektóre składniki zapisywania usługi VSS nie powiodła się <br/><br/> W systemie Linux: Domyślne (Jeśli wstępnie lub używanego po nim skrypty nie są skonfigurowane lub nie powiodło się)
**Spójne na poziomie awarii** | Spójność awarii często zdarza się, wyłączaniu Maszynie wirtualnej platformy Azure w czasie wykonywania kopii zapasowej.  Tylko dane, które już istnieje na dysku w czasie wykonywania kopii zapasowej jest przechwytywane i kopii zapasowej.<br/><br/> Punktu odzyskiwania spójnego na poziomie awarii nie gwarantuje spójności danych dotyczące systemu operacyjnego lub aplikacji. | Istnieją żadnej gwarancji, ale zazwyczaj rozruchów maszyn wirtualnych i zgodny z dyskiem zaznacz, aby naprawić błędy uszkodzenia. Wszystkie dane w pamięci lub zapisu, które nie zostały przeniesione do dysku zostaną utracone. Aplikacje implementować własne Weryfikacja danych. Na przykład dla aplikacji bazy danych, jeśli dziennik transakcji zawiera wpisy, które nie znajdują się w bazie danych, oprogramowanie bazy danych przedstawia dopóki dane są spójne. | Maszyna wirtualna jest w stanie zamknięcie


## <a name="restore-considerations"></a>Zagadnienia dotyczące przywracania 



**Zagadnienia** | **Szczegóły**
--- | ---
**Dysk** | Kopia zapasowa dysku maszyny Wirtualnej jest równoległe. Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa próbuje utworzyć kopię zapasową wszystkie cztery dyski równolegle. Kopia zapasowa jest przyrostowe (tylko zmienione dane).
**Planowanie** |  Aby zmniejszyć ruch kopii zapasowej, kopii zapasowych różnych maszyn wirtualnych o różnych porach dnia, na których nie nakłada. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje, że ruch drogach.
**Przygotowywanie kopii zapasowych** | Należy wziąć pod uwagę czas Przygotowanie kopii zapasowej, który obejmuje Instalowanie lub aktualizowanie rozszerzenie kopii zapasowej i wyzwalając migawki zgodnie z harmonogramem tworzenia kopii zapasowej.
**Transfer danych** | Czas potrzebny na usługi kopii zapasowej do obliczenia przyrostowych zmian od poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej określenia zmiany obsługę komputerów sumy kontrolnej bloku. Jeśli blok zostanie zmienione jego zidentyfikowanych wysyłania w magazynie. Awarii usługi, na wskazany bloki próbę dalszego zminimalizowanie ilości danych do transferu. Po ocenie wszystkich zmianie zablokowanych zmiany są przesyłane do magazynu.<br/><br/> Może to być opóźnienie między Tworzenie migawki, a następnie skopiować go do magazynu.<br/><br/> W godzinach szczytu może potrwać do ośmiu godzin dla kopii zapasowych jako proces. Podczas tworzenia kopii zapasowej dla maszyny Wirtualnej będzie mniej niż 24 godziny tworzenia codziennej kopii zapasowej.
**Tworzenie początkowej kopii zapasowej** | Mimo że całkowity czas tworzenia kopii zapasowych w mniej niż 24 godziny jest prawidłowa dla przyrostowych kopii zapasowych, nie może być dla pierwszej kopii zapasowej. Czas potrzebny będzie zależeć od rozmiaru danych i tworzenia kopii zapasowej.
**Przywrócić kolejki** | Przetwarzanych przez platformę Azure Backup przywrócić zadania z wielu kont magazynu w tym samym czasie i przywracanie żądania są umieszczane w kolejce.
**Przywracanie z kopii** | Podczas procesu przywracania dane są kopiowane z magazynu do konta magazynu.<br/><br/> Przywróć czas zależy od operacji We/Wy i przepływność na koncie magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, który nie został załadowany z innymi aplikacji zapisu i odczytu.


### <a name="backup-performance"></a>Wydajność tworzenia kopii zapasowej

Następujących typowych scenariuszach mogą mieć wpływ na czas wykonywania kopii zapasowych:

- Dodaj nowy dysk do chronionych maszyn wirtualnych platformy Azure: Jeśli zostanie dodany nowy dysk maszyny Wirtualnej jest w trakcie tworzenia przyrostowych kopii zapasowych, kopia zapasowa może trwać dłużej niż 24 godziny z powodu Replikacja początkowa nowy dysk, wraz z replikacji różnicowej istniejących dysków.
- Pofragmentowane dyski: Operacje tworzenia kopii zapasowej jest szybsze zmiany dysku są zlokalizowana. Jeśli zmiany są rozszerzane, podzielonej zawartości na dysku kopii zapasowej będzie przebiegać wolniej. 
- Współczynnik zmian na dysku: Jeżeli chronionych dysków w trakcie tworzenia przyrostowych kopii zapasowych muszą dziennego współczynnika zmian więcej niż 200 GB miejsca, kopia zapasowa może zająć dużo czasu (więcej niż ośmiu godzin). 
- Kopii zapasowych: Jeśli używasz najnowszej wersji kopii zapasowej (tzn. wersja natychmiastowe przywracanie) używa procesem bardziej zoptymalizowanego porównanie sumy kontrolnej porównywanie zmian. Jeśli używasz najnowszej wersji i usunięto migawkę kopii zapasowej, kopii zapasowej przełączników porównanie sumy kontrolnej i operacji tworzenia kopii zapasowej będzie niż co 24 godziny (lub niepowodzenie).

## <a name="best-practices"></a>Najlepsze praktyki 
Sugerujemy następujące rozwiązania w zakresie podczas konfigurowania kopii zapasowych maszyn wirtualnych:

- Należy rozważyć zmodyfikowanie harmonogram domyślny czas ustawiony w zasadach. Rozważmy na przykład w przypadku zasad to 12:00 AM domyślny czas jego inkrementacją w ciągu kilku minut, aby optymalne wykorzystanie zasobów.
- Dla maszyny Wirtualnej usługi Premium kopii zapasowej na temat funkcji innych niż - błyskawiczne RP przydziela ~ 50% całkowitego konta miejsca do magazynowania. Usługa Kopia zapasowa wymaga to miejsce, do kopiowania migawki do tego samego konta magazynu, a także podczas przenoszenia go do magazynu.
- Do przywrócenia maszyn wirtualnych z jednego magazynu, zalecane jest aby stosować różne [kont magazynu w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) aby upewnić się, nie ograniczeni docelowe konto magazynu. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu (Jeśli 10 maszyn wirtualnych zostaną przywrócone, a następnie należy wziąć pod uwagę przy użyciu 10 różnych kont magazynu).
- Przywracanie z warstwy magazynu warstwy 1 (Migawka) zakończy się w ciągu kilku minut (ponieważ jest to tego samego konta magazynu) dla warstwy magazynu warstwy 2 (magazynu), co może zająć do godziny. Zaleca się używać [natychmiastowe Przywracanie](backup-instant-restore-capability.md) funkcji, aby szybciej przeprowadzać operacje przywracania w przypadku których dane są dostępne w warstwie 1 (Jeśli dane mają zostać przywrócone z magazynu, a następnie potrwa czasu).
- Limit liczby dysków na konto magazynu jest względem jak duże dyski są wykorzystywane przez aplikacje działające na maszynie Wirtualnej IaaS. Upewnij się, jeśli wiele dysków znajdują się w ramach pojedynczego konta magazynu. Ogólną praktyką jest Jeśli w 5 – 10 dysków co najmniej jednego konta magazynu, równoważenie obciążenia, przenosząc niektóre dyski na oddzielnych kontach magazynu.

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowej

Maszyny wirtualne platformy Azure, kopii zapasowej w usłudze Azure Backup są podlegają [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

- Rozliczenia nie zaczyna się dopiero po zakończeniu pierwszego pomyślne tworzenie kopii zapasowych. W tym momencie rozpoczyna się okres rozliczeniowy magazynu i chronionych maszyn wirtualnych.
- Rozliczanie jest kontynuowane tak długo, jak dowolne dane kopii zapasowych są przechowywane w magazynie dla maszyny Wirtualnej. Jeśli użytkownik zaprzestanie ochrony dla maszyny Wirtualnej, ale dane kopii zapasowej dla maszyny Wirtualnej istnieje w magazynie, nadal rozliczeń.
- Rozliczenia dla określonej maszyny Wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana, a wszystkie dane kopii zapasowej zostaną usunięte.
- Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar Ostatnia pomyślna kopia zapasowa maszyny Wirtualnej staje się rozmiar chronionego wystąpienia, umożliwiający miesięczny rachunek.
- Obliczenia chronionych wystąpień są oparte na *rzeczywiste* rozmiar maszyny wirtualnej, która jest sumą wszystkich danych na maszynie Wirtualnej, z wyłączeniem magazynu tymczasowego.
- Cennik zależy od rzeczywistych danych, które są przechowywane na dysku danych.
- Cennik tworzenia kopii zapasowych maszyn wirtualnych nie jest oparty na maksymalny obsługiwany rozmiar dla każdego dysku danych dołączonych do maszyny Wirtualnej.
- Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych, który jest przechowywany w usłudze Azure Backup, który jest sumą rzeczywiste dane w poszczególnych punktach odzyskiwania.

Za przykład niech posłuży A2 standardowy rozmiar maszyny Wirtualnej, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 4 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**Dysk** | **Maksymalny rozmiar** | **Rzeczywiste dane**
--- | --- | ---
Dysk systemu operacyjnego | 4095 GB | 17 GB 
Dysk lokalny/tymczasowa | 135 GB | 5 GB (nie dołączona do tworzenia kopii zapasowych) 
Dysk danych 1 | 4095 GB | 30 GB 
Dysk danych 2 | 4095 GB | 0 GB 

- Rzeczywisty rozmiar maszyny Wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB.
- Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięczny rachunek.
- Wzrostem ilości danych na maszynie wirtualnej i rozmiar chronionego wystąpienia w związku z tym używany dla zmiany w rozliczeniach.


## <a name="next-steps"></a>Kolejne kroki

Teraz [przygotowanie](backup-azure-arm-vms-prepare.md) do utworzenia kopii zapasowej maszyny Wirtualnej platformy Azure.
