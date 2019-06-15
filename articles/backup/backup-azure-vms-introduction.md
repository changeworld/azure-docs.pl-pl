---
title: Informacje o kopii zapasowej maszyny wirtualnej platformy Azure
description: Więcej informacji na temat kopii zapasowych maszyn wirtualnych platformy Azure i zanotuj niektóre najlepsze rozwiązania.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: 93be913182db56941c346ef0cad47f70c0d614c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706836"
---
# <a name="about-azure-vm-backup"></a>Informacje o kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule opisano sposób, w jaki [usługi Azure Backup](backup-introduction-to-azure-backup.md) tworzy kopie zapasowe maszyn wirtualnych (VM).

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

Poniżej przedstawiono, jak usługi Azure Backup wykonuje kopię zapasową maszyn wirtualnych platformy Azure:

1. Maszyny wirtualne platformy Azure, które są wybrane do kopii zapasowej kopia zapasowa Azure rozpoczyna się zadanie tworzenia kopii zapasowej zgodnie z harmonogramem tworzenia kopii zapasowej, które określisz.
1. Podczas pierwszego tworzenia kopii zapasowej kopii zapasowej rozszerzenie jest zainstalowane na maszynie Wirtualnej, jeśli maszyna wirtualna jest uruchomiona.
    - W przypadku maszyn wirtualnych Windows _VMSnapshot_ rozszerzenie jest zainstalowane.
    - W przypadku maszyn wirtualnych systemu Linux _VMSnapshotLinux_ rozszerzenie jest zainstalowane.
1. Dla maszyn wirtualnych Windows, które są uruchomione kopia zapasowa służy do koordynowania przy użyciu Windows woluminów w tle kopii Service (VSS) do spójnego na poziomie aplikacji migawki maszyny wirtualnej.
    - Domyślnie kopie zapasowe pełnych kopii zapasowych usługi VSS.
    - Jeśli kopia zapasowa nie może zastosować migawki spójności aplikacji, następnie dokonuje spójna na poziomie plików zrzutu podstawowego magazynu (ponieważ nie występują zapisy aplikacji, podczas gdy maszyna wirtualna jest zatrzymana).
1. W przypadku maszyn wirtualnych systemu Linux kopie zapasowe spójna na poziomie plików kopia zapasowa. Migawki spójne z aplikacji musisz ręcznie dostosować wstępnie lub używanego po nim skrypty.
1. Po kopie zapasowe migawki, przesyła dane do magazynu.
    - Tworzenie kopii zapasowej jest zoptymalizowany przez utworzenie kopii zapasowej każdego dysku maszyny Wirtualnej w sposób równoległy.
    - Dla każdego dysku, która jest tworzona kopia zapasowa kopia zapasowa Azure odczytuje bloków na dysku i identyfikuje i przesyła jedynie te bloki danych, które zmianie (delta) od czasu poprzedniej kopii zapasowej.
    - Dane migawki nie może zostać natychmiast skopiowany do magazynu. Może upłynąć kilka godzin w godzinach szczytu. Łączny czas tworzenia kopii zapasowej dla maszyny Wirtualnej będzie mniej niż 24 godziny dla zasad tworzenia kopii zapasowej dzienny.
 1. Zmiany wprowadzone do maszyny Wirtualnej z systemem Windows, po włączeniu usługi Azure Backup na nim są następujące:
    -   Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660 jest zainstalowany na maszynie wirtualnej
    -   Typ uruchomienia usługi kopiowania woluminów w tle (VSS) zmieniła się na automatyczny z ręcznego
    -   Usługa Windows IaaSVmProvider zostanie dodany.

1. Po ukończeniu przesyłania danych migawka jest usuwana, a punkt odzyskiwania jest tworzony.

![Architektura kopii zapasowych maszyn wirtualnych platformy Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>Szyfrowania kopii zapasowych maszyn wirtualnych platformy Azure

Podczas wykonywania kopii zapasowych maszyn wirtualnych platformy Azure z usługą Azure Backup, maszyn wirtualnych są szyfrowane, gdy szyfrowanie usługi Storage (SSE). Usługa Azure Backup można również wykonać kopię zapasową maszyn wirtualnych platformy Azure są szyfrowane przy użyciu usługi Azure Disk Encryption.

**Szyfrowanie** | **Szczegóły** | **Pomoc techniczna**
--- | --- | ---
**Usługa Azure Disk Encryption** | Usługa Azure Disk Encryption są szyfrowane zarówno systemu operacyjnego i dysków z danymi maszyn wirtualnych platformy Azure.<br/><br/> Usługa Azure Disk Encryption integruje się przy użyciu kluczy szyfrowania funkcją BitLocker (BEKs), których zabezpieczania się jako wpisy tajne w magazynie kluczy. Usługa Azure Disk Encryption integruje się również przy użyciu kluczy szyfrowania klucza usługi Azure Key Vault (kluczy Kek). | Usługa Azure Backup obsługuje kopię zapasową zarządzanych i niezarządzanych maszyn wirtualnych platformy Azure szyfrowane tylko BEKs lub z BEKs wraz z kluczy Kek.<br/><br/> Zarówno BEKs kluczy Kek kopii zapasowej i szyfrowane.<br/><br/> Ponieważ kluczy Kek oraz BEKs kopię zapasową, użytkownicy z odpowiednimi uprawnieniami mogą przywracać klucze i wpisy tajne z powrotem do magazynu kluczy, jeśli to konieczne. Tacy użytkownicy mogą także odzyskać zaszyfrowanych maszyn wirtualnych.<br/><br/> Nie można odczytać zaszyfrowanych kluczy i wpisów tajnych, przez nieautoryzowanych użytkowników lub przez platformę Azure.
**SSE** | Za pomocą funkcji SSE Azure Storage zapewnia szyfrowanie w spoczynku, automatycznie szyfrując dane przed przekazaniem jej. Usługa Azure Storage również odszyfrowuje przed pobraniem go. | Usługa Azure Backup używa SSE dla nieaktywnych maszyn wirtualnych platformy Azure.

Zarządzane i niezarządzane maszyn wirtualnych platformy Azure kopii zapasowej obsługuje zarówno maszyny wirtualne szyfrowane przy użyciu BEKs tylko lub maszyny wirtualne szyfrowane przy użyciu BEKs wraz z kluczy Kek.

Kopia zapasowa BEKs (hasła) i kluczy Kek (klucze) są szyfrowane. Można je odczytać i używane tylko wtedy, gdy są przywracane do magazynu kluczy przez autoryzowanych użytkowników. Nieautoryzowani użytkownicy ani platformy Azure nie można odczytać lub kopię zapasową kluczy lub wpisów tajnych.

BEKs również kopię zapasową. Dlatego w przypadku utraty BEKs autoryzowanych użytkowników można przywrócić BEKs do magazynu kluczy i odzyskać zaszyfrowanych maszyn wirtualnych. Tylko użytkownicy z poziomu uprawnień można i przywracanie kopii zapasowej zaszyfrowanych maszyn wirtualnych lub kluczy i wpisów tajnych.

## <a name="snapshot-creation"></a>Tworzenie migawki

Usługa Azure Backup tworzy migawki zgodnie z harmonogramem tworzenia kopii zapasowej.

- **Maszyny wirtualne Windows:** W przypadku maszyn wirtualnych Windows usługa Backup służy do koordynowania z usługami VSS do spójnego na poziomie aplikacji migawki dysków maszyny Wirtualnej.

  - Domyślnie program Azure kopie zapasowe pełnych kopii zapasowych usługi VSS. [Dowiedz się więcej](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx).
  - Aby zmienić ustawienie Azure kopie zapasowe usługi VSS skopiowanie kopii zapasowych, należy ustawić następujący klucz rejestru z poziomu wiersza polecenia:

    **Polecenie REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v / USEVSSCOPYBACKUP/t REG_SZ /d TRUE f**

- **Maszyny wirtualne systemu Linux:** Do wykonania migawek spójności aplikacji maszyn wirtualnych systemu Linux, użyj skryptu wstępne systemu Linux i po utworzeniu skryptu platformę, by pisania własnych skryptów niestandardowych w celu zapewnienia spójności.

  - Usługa Azure Backup wywołuje tylko skrypty pre lub używanego po nim zapisane przez użytkownika.
  - Jeśli skrypty przed i skryptu używanego po utworzeniu wykonane pomyślnie, kopia zapasowa Azure oznacza punkt odzyskiwania jako spójnych z aplikacją. Jednak podczas korzystania z niestandardowych skryptów, możesz teraz ponoszą ostateczną odpowiedzialność za spójność aplikacji.
  - [Dowiedz się więcej](backup-azure-linux-app-consistent.md) o sposobie konfigurowania skryptów.

### <a name="snapshot-consistency"></a>Migawka spójności

W poniższej tabeli opisano różne rodzaje migawki spójności:

**migawki** | **Szczegóły** | **Odzyskiwanie** | **Zagadnienia**
--- | --- | --- | ---
**Spójna na poziomie aplikacji** | Wykonywanie kopii zapasowych spójnych aplikacji przechwytywania pamięci zawartości i oczekujących operacji We/Wy. Migawki spójne z aplikacji należy użyć składnika zapisywania usługi VSS (lub pre lub używanego po nim skrypty dla systemu Linux) w celu zapewnienia spójności danych aplikacji, zanim wystąpi kopii zapasowej. | W przypadku odzyskiwania maszyny Wirtualnej z migawki spójności aplikacji, maszyna wirtualna jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje mają swój początek w spójnym stanie. | W systemie Windows: Wszystkie składniki zapisywania usługi VSS powiodło się.<br/><br/> W systemie Linux: Wstępnie lub używanego po nim skrypty są konfigurowane i zakończyło się pomyślnie
**Spójne na poziomie pliku systemu** | Kopie zapasowe spójne z systemu plików zapewniają spójność wykonywania migawki wszystkie pliki w tym samym czasie.<br/><br/> | W przypadku odzyskiwania maszyny Wirtualnej przy użyciu systemu plików spójnej migawki maszyny Wirtualnej jest uruchamiany. Brak nie utraty lub uszkodzenia danych. Aplikacje muszą implementować ich własny mechanizm "poprawki", aby upewnić się, że przywróconych danych są spójne. | W systemie Windows: Niektóre składniki zapisywania usługi VSS nie powiodła się <br/><br/> W systemie Linux: Domyślne (Jeśli wstępnie lub używanego po nim skrypty nie są skonfigurowane lub nie powiodło się)
**Spójne na poziomie awarii** | Migawki spójne na poziomie awarii zazwyczaj wystąpić jeśli Maszynę wirtualną platformy Azure zostanie wyłączony w momencie tworzenia kopii zapasowej. Tylko dane, które już istnieje na dysku w czasie wykonywania kopii zapasowej jest przechwytywane i kopii zapasowej.<br/><br/> Punktu odzyskiwania spójnego na poziomie awarii nie gwarantuje spójności danych dotyczące systemu operacyjnego lub aplikacji. | Mimo że nie ma żadnej gwarancji, zwykle wykonuje rozruch maszyny Wirtualnej, a następnie rozpoczyna się sprawdzenie dysku, aby naprawić błędy uszkodzenia. Wszelkie dane w pamięci i operacji zapisu, które nie zostały przeniesione na dysku przed awarią zostaną utracone. Aplikacje implementować własne Weryfikacja danych. Na przykład aplikacji bazy danych można użyć swojego dziennika transakcji do weryfikacji. Jeśli dziennik transakcji zawiera wpisy, które nie znajdują się w bazie danych, oprogramowanie bazy danych gromadzi informacje o transakcji ponownie, dopóki dane są spójne. | Maszyna wirtualna jest w stanie zamknięcie

## <a name="backup-and-restore-considerations"></a>Zagadnienia dotyczące tworzenia kopii zapasowych i przywracania

**Zagadnienia** | **Szczegóły**
--- | ---
**Dysk** | Kopii zapasowych dysków maszyny Wirtualnej jest równoległe. Na przykład jeśli maszyna wirtualna ma cztery dyski, usługa Backup próbuje utworzyć kopię zapasową wszystkie cztery dyski równolegle. Kopia zapasowa jest przyrostowe (tylko zmienione dane).
**Planowanie** |  Aby zmniejszyć ruch kopii zapasowej, tworzenie kopii zapasowych różnych maszyn wirtualnych o różnych porach dnia i upewnij się, że czas, w którym nie nakłada się na. Tworzenie kopii zapasowych maszyn wirtualnych w tym samym czasie powoduje, że ruch drogach.
**Przygotowywanie kopii zapasowych** | Należy pamiętać, czas potrzebny na przygotowanie kopii zapasowej. Podczas przygotowywania obejmuje Instalowanie lub aktualizowanie rozszerzenie kopii zapasowej i wyzwalając migawki zgodnie z harmonogramem tworzenia kopii zapasowej.
**Transfer danych** | Należy wziąć pod uwagę czas potrzebny do usługi Azure Backup zidentyfikować zmiany przyrostowe z poprzedniej kopii zapasowej.<br/><br/> W przyrostowej kopii zapasowej kopia zapasowa Azure określa zmiany, obliczając sumę kontrolną bloku. Jeśli blok zostanie zmieniona, jest on oznaczony do przeniesienia do magazynu. Usługa analizuje zidentyfikowanych bloki próbę jeszcze bardziej zminimalizować ilość danych do przesłania. Po dokonaniu oceny zmienione bloki, kopia zapasowa Azure przesyła zmiany do magazynu.<br/><br/> Może to być opóźnienie między Tworzenie migawki, a następnie skopiować go do magazynu.<br/><br/> W godzinach szczytu może potrwać do ośmiu godzin kopie zapasowe mają być przetwarzane. Podczas tworzenia kopii zapasowej dla maszyny Wirtualnej będzie mniej niż 24 godziny tworzenia codziennej kopii zapasowej.
**Tworzenie początkowej kopii zapasowej** | Mimo że całkowity czas tworzenia kopii zapasowych dla przyrostowych kopii zapasowych jest krótszy niż 24 godziny, który może nie być w przypadku pierwszej kopii zapasowej. Czas potrzebny na początkową kopię zapasową będzie zależeć od rozmiaru danych i tworzenia kopii zapasowej jest przetwarzany.
**Przywrócić kolejki** | Przetwarzanych przez platformę Azure Backup przywrócić zadania z wielu kont magazynu w tym samym czasie. Dzięki niemu przywracania żądań w kolejce.
**Przywracanie z kopii** | Podczas procesu przywracania dane są kopiowane z magazynu do konta magazynu.<br/><br/> Czas całkowity przywracania zależy od operacji We/Wy na sekundę (IOPS) i przepływność na koncie magazynu.<br/><br/> Aby skrócić czas kopiowania, wybierz konto magazynu, który nie został załadowany z innymi aplikacji zapisu i odczytu.

### <a name="backup-performance"></a>Wydajność tworzenia kopii zapasowej

Następujących typowych scenariuszach mogą mieć wpływ na całkowity czas tworzenia kopii zapasowej:

- **Dodawanie nowego dysku do chronionych maszyn wirtualnych platformy Azure:** Jeśli zostanie dodany nowy dysk maszyny Wirtualnej jest w trakcie tworzenia przyrostowych kopii zapasowych, zwiększy się czas wykonywania kopii zapasowych. Łączny czas tworzenia kopii zapasowej może trwać dłużej niż 24 godziny, z powodu Replikacja początkowa nowy dysk, wraz z replikacji różnicowej istniejących dysków.
- **Pofragmentowane dyski:** Operacje tworzenia kopii zapasowej jest szybsze zmiany dysku są ciągłe. Jeśli zmiany są rozszerzane, podzielonej zawartości na dysku kopii zapasowej będzie przebiegać wolniej.
- **Współczynnik zmian na dysku:** Jeśli chronione dyski, które są w trakcie tworzenia przyrostowych kopii zapasowych dziennego współczynnika zmian więcej niż 200 GB miejsca, kopia zapasowa może zająć dużo czasu (więcej niż ośmiu godzin) do wykonania.
- **Kopii zapasowych:** Najnowszą wersję kopii zapasowej (tzn. wersja natychmiastowe przywracanie) używa procesem bardziej zoptymalizowanego porównanie sumy kontrolnej do identyfikowania zmian. Ale jeśli używasz, natychmiastowe Przywracanie i usunięto migawkę kopii zapasowej, kopii zapasowych zmienia się na porównanie sumy kontrolnej. W tym przypadku operacji tworzenia kopii zapasowej będzie niż co 24 godziny (lub niepowodzenie).

## <a name="best-practices"></a>Najlepsze praktyki

Gdy konfigurujesz kopie zapasowe maszyn wirtualnych, sugerujemy następujące następujące rozwiązania:

- Modyfikowanie czasów harmonogramu domyślnego, ustawionych w zasadach. Na przykład jeśli domyślny czas w zasadach jest 12:00 AM, zwiększyć czas przez kilka minut, tak, aby optymalne wykorzystanie zasobów.
- Do tworzenia kopii zapasowych maszyn wirtualnych, które korzystają z usługi premium storage, zaleca się uruchomienie najnowszą wersję usługi Azure Backup ([natychmiastowe Przywracanie](backup-instant-restore-capability.md)). Jeśli nie korzystasz z najnowszej wersji, kopia zapasowa przydziela około 50 procent całkowitego miejsca do magazynowania. Usługa Kopia zapasowa wymaga to miejsce, do kopiowania migawki do tego samego konta magazynu, a także podczas przenoszenia go do magazynu.
- W przypadku przywracania maszyn wirtualnych z jednego magazynu, zdecydowanie zalecamy używanie innego [kont magazynu ogólnego przeznaczenia v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) aby upewnić się, że nie ograniczeni docelowe konto magazynu. Na przykład każda maszyna wirtualna musi mieć inne konto magazynu. Na przykład jeśli zostaną przywrócone 10 maszyn wirtualnych, należy użyć 10 różnych kont magazynu.
- Będzie można wykonać przywracania z warstwy magazynu ogólnego przeznaczenia w wersji 1 (Migawka) w ciągu kilku minut, ponieważ migawka jest tym samym koncie magazynu. Przywracanie z warstwy magazynu ogólnego przeznaczenia w wersji 2 (magazyn) może zająć godziny. W przypadkach, w którym dane są dostępne w magazynie ogólnego przeznaczenia w wersji 1, zaleca się, że używasz [natychmiastowe Przywracanie](backup-instant-restore-capability.md) funkcji, aby szybciej przeprowadzać operacje przywracania. (Jeśli danych musi zostać przywrócona z magazynu, następnie potrwa dłużej.)
- Limit liczby dysków na konto magazynu jest określana względem stopnia dyski są wykorzystywane przez aplikacje, które działają w infrastrukturze jako usługa (IaaS) maszyny Wirtualnej. Ogólną praktyką jest Jeśli dyski 5 – 10 lub więcej znajduje się w ramach pojedynczego konta magazynu, równoważenie obciążenia, przenosząc niektóre dyski na oddzielnych kontach magazynu.

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowej

Maszyny wirtualne platformy Azure, kopii zapasowej w usłudze Azure Backup są podlegają [cennika usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

Rozliczenia nie zaczyna się dopóki nie zakończy się pierwsza kopia zapasowa pomyślnie. W tym momencie rozpoczyna się okres rozliczeniowy magazynu i chronionych maszyn wirtualnych. Rozliczanie jest kontynuowane tak długo, jak wszystkie dane kopii zapasowej dla maszyny Wirtualnej są przechowywane w magazynie. Jeśli użytkownik zaprzestanie ochrony dla maszyny Wirtualnej, ale dane kopii zapasowej dla maszyny Wirtualnej istnieje w magazynie, nadal rozliczeń.

Rozliczenia dla określonej maszyny Wirtualnej zatrzymuje tylko wtedy, gdy ochrona jest zatrzymana, a wszystkie dane kopii zapasowej zostaną usunięte. Po zatrzymaniu ochrony i nie istnieją aktywne zadania tworzenia kopii zapasowej, rozmiar Ostatnia pomyślna kopia zapasowa maszyny Wirtualnej staje się rozmiar chronionego wystąpienia, umożliwiający miesięczny rachunek.

Obliczanie rozmiaru chronionego wystąpienia zależy od *rzeczywiste* rozmiar maszyny wirtualnej. Rozmiar maszyny Wirtualnej jest sumą wszystkich danych na maszynie Wirtualnej, z wyłączeniem magazynu tymczasowego. Cennik zależy od rzeczywistych danych, który ma przechowywane na dyskach danych, nie na maksymalny obsługiwany rozmiar każdego dysku danych, który jest dołączony do maszyny Wirtualnej.

Podobnie opłata za magazyn kopii zapasowych zależy od ilości danych, który jest przechowywany w usłudze Azure Backup, który jest sumą rzeczywiste dane w poszczególnych punktach odzyskiwania.

Za przykład niech posłuży wielkości Standard A2 maszynę Wirtualną, która ma dwa dyski dodatkowe dane o maksymalnym rozmiarze 4 TB. W poniższej tabeli przedstawiono rzeczywiste dane przechowywane na każdym z tych dysków:

**Dysk** | **Maksymalny rozmiar** | **Rzeczywiste dane**
--- | --- | ---
Dysk systemu operacyjnego | 4095 GB | 17 GB
Dysk lokalny/tymczasowa | 135 GB | 5 GB (nie dołączona do tworzenia kopii zapasowych)
Dysk danych 1 | 4095 GB | 30 GB
Dysk danych 2 | 4095 GB | 0 GB

Rzeczywisty rozmiar maszyny Wirtualnej jest w tym przypadku 17 GB + 30 GB + 0 GB = 47 GB. Ten rozmiar chronionego wystąpienia (47 GB) staje się podstawą miesięczny rachunek. Wzrostem ilości danych na maszynie wirtualnej i rozmiar chronionego wystąpienia służy do zmiany w rozliczeniach do dopasowania.

## <a name="next-steps"></a>Kolejne kroki

Teraz [przygotowanie do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](backup-azure-arm-vms-prepare.md).
