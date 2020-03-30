---
title: Rozwiązywanie problemów z wizualizacją oceny i zależności w programie Azure Migrate
description: Uzyskaj pomoc dotyczącą rozwiązywania problemów z oceną i wizualizacją zależności w programie Azure Migrate.
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127674"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>Rozwiązywanie problemów z wizualizacją zależności/oceny

Ten artykuł ułatwia rozwiązywanie problemów z wizualizacją oceny i zależności za pomocą [programu Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool).


## <a name="assessment-readiness-issues"></a>Problemy z gotowością do oceny

Rozwiązać problemy z gotowością do oceny w następujący sposób:

**Problem** | **Poprawka**
--- | ---
Nieobsługiwał typ rozruchu | Platforma Azure nie obsługuje maszyn wirtualnych z typem rozruchu EFI. Przed uruchomieniem migracji zaleca się przekonwertowanie typu rozruchu na system BIOS. <br/><br/>Migracji serwera migracji usługi Azure można używać do obsługi migracji takich maszyn wirtualnych. Zostanie przekonwertowany typ rozruchu maszyny Wirtualnej do systemu BIOS podczas migracji.
Warunkowo obsługiwany system operacyjny Windows | System operacyjny przeszedł datę zakończenia pomocy technicznej i potrzebuje niestandardowej umowy pomocy technicznej (CSA) do [obsługi na platformie Azure](https://aka.ms/WSosstatement). Należy rozważyć uaktualnienie przed migracją na platformę Azure.
Nieobsługiwał się system operacyjny Windows | Platforma Azure obsługuje tylko [wybrane wersje systemu operacyjnego Windows](https://aka.ms/WSosstatement). Należy rozważyć uaktualnienie komputera przed migracją na platformę Azure.
Warunkowo zatwierdzony system operacyjny Linux | Platforma Azure promuje tylko [wybrane wersje systemu operacyjnego Linux](../virtual-machines/linux/endorsed-distros.md). Należy rozważyć uaktualnienie komputera przed migracją na platformę Azure.
Nienakazyowany system operacyjny Linux | Komputer może zostać uruchomiony na platformie Azure, ale platforma Azure nie zapewnia obsługi systemu operacyjnego. Należy rozważyć uaktualnienie do [zatwierdzonej wersji systemu Linux](../virtual-machines/linux/endorsed-distros.md) przed migracją na platformę Azure.
Nieznany system operacyjny | System operacyjny maszyny Wirtualnej został określony jako "Inne" w vCenter Server. To zachowanie blokuje migracji platformy Azure z weryfikacji gotowości platformy Azure maszyny Wirtualnej. Przed migracją komputera upewnij się, że system operacyjny jest [obsługiwany](https://aka.ms/azureoslist) przez platformę Azure.
Nieobsługiwała wersja bitowa | Maszyny wirtualne z 32-bitowymi systemami operacyjnymi mogą być uruchamiane na platformie Azure, ale zaleca się uaktualnienie do 64-bitowego przed migracją na platformę Azure.
Wymaga subskrypcji programu Microsoft Visual Studio | Na komputerze jest uruchomiony system operacyjny klienta systemu Windows, który jest obsługiwany tylko za pośrednictwem subskrypcji programu Visual Studio.
Nie znaleziono maszyny Wirtualnej dla wymaganej wydajności pamięci masowej | Wydajność magazynu (operacje wejścia/wyjścia na sekundę [IOPS] i przepływność) wymagane dla komputera przekracza obsługę maszyny Wirtualnej platformy Azure. Przed migracją zmniejsz wymagania dotyczące pamięci masowej dla komputera.
Nie znaleziono maszyny Wirtualnej dla wymaganej wydajności sieci | Wydajność sieci (w/na zewnątrz) wymagana dla komputera przekracza obsługę maszyny Wirtualnej platformy Azure. Zmniejsz wymagania sieciowe dla maszyny.
Nie znaleziono maszyny Wirtualnej w określonej lokalizacji | Użyj innej lokalizacji docelowej przed migracją.
Jeden lub więcej nieodpowiednich dysków | Co najmniej jeden dysk podłączony do maszyny Wirtualnej nie spełnia wymagań platformy Azure. A<br/><br/> Migracja platformy Azure: Ocena serwera obecnie nie obsługuje dysków Ultra SSD i ocenia dyski na podstawie limitów dysków dla dysków zarządzanych w wersji premium (32 TB).<br/><br/> Dla każdego dysku podłączonego do maszyny Wirtualnej upewnij się, że rozmiar dysku wynosi < 64 TB (obsługiwane przez dyski Ultra SSD).<br/><br/> Jeśli tak nie jest, zmniejsz rozmiar dysku przed migracją na platformę Azure lub użyj wielu dysków na platformie Azure i [rozstaw je razem,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) aby uzyskać wyższe limity miejsca do magazynowania. Upewnij się, że wydajność (Usługi We/Wy i przepływność) wymagana przez każdy dysk jest obsługiwana przez [dyski zarządzanej maszyny wirtualnej platformy](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)Azure.
Co najmniej jedna nieodpowiednia karta sieciowa. | Przed migracją usuń z komputera nieużywane karty sieciowe.
Liczba dysków przekracza limit | Przed migracją usuń nieużywane dyski z urządzenia.
Rozmiar dysku przekracza limit | Migracja platformy Azure: Ocena serwera obecnie nie obsługuje dysków Ultra SSD i ocenia dyski na podstawie limitów dysków w wersji premium (32 TB).<br/><br/> Jednak platforma Azure obsługuje dyski o rozmiarze do 64 TB (obsługiwane przez dyski Ultra SSD). Przed migracją zmniejsz dyski do mniej niż 64 TB lub użyj wielu dysków na platformie Azure i [połącz je,](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) aby uzyskać wyższe limity miejsca do magazynowania.
Dysk niedostępny w określonej lokalizacji | Przed migracją upewnij się, że dysk znajduje się w lokalizacji docelowej.
Dysk niedostępny dla określonej redundancji | Dysk powinien używać typu magazynu nadmiarowości zdefiniowanego w ustawieniach oceny (domyślnie LRS).
Nie można określić przydatności dysku z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie znaleziono maszyny Wirtualnej z wymaganymi rdzeniami i pamięcią | Platforma Azure nie może znaleźć odpowiedniego typu maszyny Wirtualnej. Przed migracją zmniejsz ilość pamięci i liczby rdzeni komputera lokalnego.
Nie można określić przydatności maszyny Wirtualnej z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla jednego lub więcej dysków z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.
Nie można określić przydatności dla jednej lub więcej kart sieciowych z powodu błędu wewnętrznego | Spróbuj utworzyć nową ocenę dla grupy.

## <a name="linux-vms-are-conditionally-ready"></a>Maszyny wirtualne z systemem Linux są "warunkowo gotowe"

Ocena serwera oznacza maszyny wirtualne z systemem Linux jako "warunkowo gotowe" ze względu na znaną lukę w ocenie serwera.

- Luka uniemożliwia wykrywanie pomocniczej wersji systemu operacyjnego Linux zainstalowanego na lokalnych maszynach wirtualnych.
- Na przykład dla RHEL 6.10, obecnie ocena serwera wykrywa tylko RHEL 6 jako wersja systemu operacyjnego.
-  Ponieważ platforma Azure promuje tylko określone wersje systemu Linux, maszyny wirtualne z systemem Linux są obecnie oznaczone jako warunkowo gotowe w ocenie serwera.
- Można określić, czy system operacyjny Linux działający na lokalnej maszynie wirtualnej jest zatwierdzony na platformie Azure, przeglądając [obsługę systemu Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros)
-  Po zweryfikowaniu zatwierdzonej dystrybucji możesz zignorować to ostrzeżenie.

## <a name="azure-skus-bigger-than-on-premises"></a>Jednostki SKU platformy Azure większe niż lokalne

Ocena serwera migracji usługi Azure może zalecić jednostki SKU maszyn wirtualnych platformy Azure z większą liczebą rdzeni i pamięci niż bieżąca alokacja lokalna na podstawie typu oceny:


- Zalecenie SKU maszyny Wirtualnej zależy od właściwości oceny.
- Ma na to wpływ typ oceny wykonywanej w programie Ocena serwera: oparte na *wydajności*lub *Jako lokalnie*.
- W przypadku ocen opartych na wydajności ocena serwera uwzględnia dane wykorzystania lokalnych maszyn wirtualnych (procesora CPU, pamięci, dysku i wykorzystania sieci) w celu określenia właściwej docelowej jednostki SKU maszyny Wirtualnej dla lokalnych maszyn wirtualnych. Dodaje również współczynnik komfortu przy określaniu skutecznego wykorzystania.
- W przypadku lokalnego rozmiaru dane dotyczące wydajności nie są brane pod uwagę, a docelowa jednostka SKU jest zalecana na podstawie alokacji lokalnej.

Aby pokazać, jak może to wpłynąć na zalecenia, weźmy przykład:

Mamy lokalną maszynę wirtualną z czterema rdzeniami i ośmioma GB pamięci, z 50% wykorzystaniem procesora i 50% wykorzystaniem pamięci i określonym współczynnikiem komfortu 1.3.

-  Jeśli ocena jest **jako lokalna,** zaleca się jednostkę SKU maszyny Wirtualnej platformy Azure z czterema rdzeniami i 8 GB pamięci.
- Jeśli ocena jest oparta na wydajności, w oparciu o efektywne wykorzystanie procesora i pamięci (50% z 4 rdzeni * 1,3 = 2,6 rdzeni i 50% pamięci 8 GB * 1,3 = 5,3 GB pamięci), najtańsza jednostka SKU maszyny Wirtualnej z czterema rdzeniami (najbliższa obsługiwana liczba rdzeni) i osiem GB pamięci (najbliższa obsługiwana pamięć pamięci).
- [Dowiedz się więcej](concepts-assessment-calculation.md#types-of-assessments) o doliczanie rozmiaru.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Jednostki SKU dysku platformy Azure większe niż lokalne

Ocena serwera migracji usługi Azure może zalecić większy dysk na podstawie typu oceny.
- Rozmiar dysku w ocenie serwera zależy od dwóch właściwości oceny: kryteriów zmiany rozmiaru i typu magazynu.
- Jeśli kryteria zmiany rozmiaru są **oparte na wydajności,** a typ magazynu jest ustawiony na **Automatyczne**, IOPS, a wartości przepływności dysku są brane pod uwagę podczas identyfikowania typu dysku docelowego (Standardowy dysk twardy, standardowy dysk SSD lub Premium). Następnie zaleca się jednostkę SKU dysku z typu dysku, a zalecenie uwzględnia wymagania dotyczące rozmiaru dysku lokalnego.
- Jeśli kryteria rozmiaru są **oparte na wydajności,** a typem magazynu jest **Premium,** jednostka SKU dysku premium na platformie Azure jest zalecana na podstawie wymagań dotyczących usług We/Wy, przepływności i rozmiaru dysku lokalnego. Ta sama logika jest używana do zmiany rozmiaru dysku, gdy kryterium zmiany rozmiaru jest **Jako lokalnie,** a typem magazynu jest **Standardowy dysk twardy,** **standardowy dysk SSD**lub **Premium.**

Na przykład, jeśli masz dysk lokalny z 32 GB pamięci, ale zagregowane operacjiE We/Wy odczytu i zapisu dla dysku to 800 Operacji We/Wy, ocena serwera zaleca dysk premium (ze względu na wyższe wymagania operacji We/Wy), a następnie zaleca sku dysku, który może obsługiwać wymaganych we/wy i rozmiarze. Najbliższym dopasowaniem w tym przykładzie byłaby jednostka P15 (256 GB, 1100 operacji we/wy na sekundę). Mimo że rozmiar wymagany przez dysk lokalny wynosił 32 GB, ocena serwera zaleca większy dysk ze względu na wysokie wymagania dotyczące we/wy dotyczące dysków lokalnych.

## <a name="utilized-corememory-percentage-missing"></a>Brak wykorzystania wartości procentowej rdzenia/pamięci

Raporty oceny serwera "PercentageOfCoresUtilizedMissing" lub "PercentageOfMemoryUtilizedMissing", gdy urządzenie migracji platformy Azure nie może zbierać danych o wydajności dla odpowiednich lokalnych maszyn wirtualnych.

- Taka możliwość może wystąpić, jeśli maszyny wirtualne są wyłączone podczas trwania oceny. Urządzenie nie może zbierać danych o wydajności maszyny Wirtualnej, gdy jest wyłączona.
- Jeśli brakuje tylko liczników pamięci i próbujesz ocenić maszyny wirtualne funkcji Hyper-V, sprawdź, czy na tych maszynach wirtualnych jest włączona pamięć dynamiczna. Istnieje znany problem tylko dla maszyn wirtualnych funkcji Hyper-V, w którym urządzenie migracji platformy Azure nie może zbierać danych wykorzystania pamięci dla maszyn wirtualnych, które nie mają włączonej pamięci dynamicznej.
- Jeśli brakuje dowolnego licznika wydajności, ocena usługi Azure Migrate Server powraca do przydzielonych rdzeni i pamięci i zaleca odpowiedni rozmiar maszyny Wirtualnej.
- Jeśli brakuje wszystkich liczników wydajności, upewnij się, że wymagania dotyczące dostępu do portu do oceny są spełnione. Dowiedz się więcej o wymaganiach dotyczących dostępu do portów dla [vmware,](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access) [funkcji Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access) i oceny serwera [fizycznego.](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)

## <a name="is-the-operating-system-license-included"></a>Czy licencja systemu operacyjnego jest dołączona?

Usługa Azure Migrate Server Assessment obecnie uwzględnia koszt licencji systemu operacyjnego tylko dla komputerów z systemem Windows. Koszty licencji na komputery z systemem Linux nie są obecnie brane pod uwagę.

## <a name="how-does-performance-based-sizing-work"></a>Jak działa zmiana rozmiaru oparta na wydajności?

Narzędzie Server Assessment nieprzerwanie zbiera dane wydajności maszyn lokalnych i używa ich do określania zalecanych jednostek SKU maszyn wirtualnych i jednostek SKU dysków na platformie Azure. [Dowiedz się, jak](concepts-assessment-calculation.md#calculate-sizing-performance-based) zbierane są dane oparte na wydajności.


## <a name="dependency-visualization-in-azure-government"></a>Wizualizacja zależności w usłudze Azure Dla instytucji rządowych

Usługa Azure Migrate zależy od mapy usługi dla funkcji wizualizacji zależności. Ponieważ mapa usługi jest obecnie niedostępna w usłudze Azure Government, ta funkcja nie jest dostępna w usłudze Azure Dla instytucji rządowych.

## <a name="dependencies-dont-show-after-agent-install"></a>Zależności nie są wyświetlane po zainstalowaniu agenta

Po zainstalowaniu agentów wizualizacji zależności na lokalnych maszynach wirtualnych usługa Azure Migrate zwykle trwa 15-30 minut, aby wyświetlić zależności w portalu. Jeśli czekałeś dłużej niż 30 minut, upewnij się, że agent monitorowania firmy Microsoft (MMA) może połączyć się z obszarem roboczym usługi Log Analytics.

W przypadku maszyn wirtualnych z systemem Windows:
1. W Panelu sterowania uruchom mma.
2. We **właściwościach** > usługi**Azure Log Analytics (OMS)** usługi Microsoft Monitoring Agent upewnij się, że **stan** obszaru roboczego jest zielony.
3. Jeśli stan nie jest zielony, spróbuj usunąć obszar roboczy i dodać go ponownie do programu MMA.

    ![Status MMA](./media/troubleshoot-assessment/mma-properties.png)

W przypadku maszyn wirtualnych z systemem Linux upewnij się, że polecenia instalacyjne mma i agenta zależności powiodły się.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

- **Agent MMS:** Przejrzyj obsługiwane systemy operacyjne [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)i [Linux.](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)
- **Agent zależności:** obsługiwane systemy operacyjne [Windows i Linux.](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)

## <a name="visualize-dependencies-for--hour"></a>Wizualizuj zależności dla > godziny

Mimo że usługa Azure Migrate umożliwia powrót do określonej daty w ostatnim miesiącu, maksymalny czas trwania, dla którego można wizualizować zależności wynosi jedną godzinę.

Na przykład można użyć funkcji czasu trwania w mapie zależności do wyświetlania zależności dla wczoraj, ale można wyświetlić je tylko przez okres jednej godziny.

Jednak można użyć dzienników usługi Azure Monitor do [kwerendy danych zależności](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) w dłuższym czasie trwania.

## <a name="visualized-dependencies-for--10-machines"></a>Wizualizowane zależności dla > 10 maszyn

W usłudze Azure Migrate Server Assessment można [wizualizować zależności dla grup](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) z maksymalnie 10 maszynami wirtualnymi. W przypadku większych grup zaleca się podzielenie maszyn wirtualnych na mniejsze grupy w celu wizualizacji zależności.

## <a name="machines-show-install-agent"></a>Maszyny pokazują "Zainstaluj agenta"

Po migracji maszyn z włączoną wizualizacją zależności na platformie Azure na maszynach może być wyświetlona akcja "Zainstaluj agenta" zamiast akcji "Wyświetl zależności" z powodu następującego zachowania:


- Po migracji na platformę Azure maszyny lokalne są wyłączone, a równoważne maszyny wirtualne są obracane na platformie Azure. Maszyny te nabywają inny adres MAC.
- Maszyny mogą mieć również inny adres IP, w zależności od tego, czy zachowano lokalny adres IP, czy nie.
- Jeśli adresy MAC i IP różnią się od lokalnych, usługa Azure Migrate nie kojarzy komputerów lokalnych z żadnymi danymi zależności mapy usługi. W takim przypadku wyświetli opcję zainstalowania agenta, a nie do wyświetlania zależności.
- Po migracji testowej na platformę Azure maszyny lokalne pozostają włączone zgodnie z oczekiwaniami. Równoważne maszyny utworzone na platformie Azure uzyskać inny adres MAC i może uzyskać różne adresy IP. Jeśli nie zablokujesz wychodzącego ruchu dziennika usługi Azure Monitor z tych maszyn, usługa Azure Migrate nie skojarzy komputerów lokalnych z dowolnymi danymi zależności mapy usługi, a tym samym wyświetli opcję instalowania agentów, a nie do wyświetlania zależności.


## <a name="capture-network-traffic"></a>Przechwytywanie ruchu sieciowego

Zbieranie dzienników ruchu sieciowego w następujący sposób:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Naciśnij klawisz F12, aby uruchomić narzędzia programistyczne. W razie potrzeby wyczyść ustawienie **Wyczyść wpisy w ustawieniach nawigacji.**
3. Wybierz kartę **Sieć** i rozpocznij przechwytywanie ruchu sieciowego:
   - W Chrome wybierz **pozycję Zachowaj dziennik**. Nagrywanie powinno rozpocząć się automatycznie. Czerwony okrąg wskazuje, że ruch jest przechwytywany. Jeśli czerwone kółko nie jest wyświetlane, wybierz czarne kółko, aby rozpocząć.
   - W programach Microsoft Edge i Internet Explorer nagrywanie powinno rozpoczynać się automatycznie. Jeśli tak nie jest, wybierz zielony przycisk odtwarzania.
4. Spróbuj odtworzyć błąd.
5. Po napotkaniu błędu podczas nagrywania zatrzymaj nagrywanie i zapisz kopię zarejestrowanej aktywności:
   - W Chrome kliknij prawym przyciskiem myszy i wybierz pozycję **Zapisz jako HAR z zawartością**. Ta akcja kompresuje i eksportuje dzienniki jako plik .har.
   - W programie Microsoft Edge lub Internet Explorer wybierz opcję **Eksportuj przechwycony ruch.** Ta akcja kompresuje i eksportuje dziennik.
6. Wybierz kartę **Konsola,** aby sprawdzić, czy nie ma ostrzeżeń lub błędów. Aby zapisać dziennik konsoli:
   - W Chrome kliknij prawym przyciskiem myszy dowolne miejsce w dzienniku konsoli. Wybierz **pozycję Zapisz jako**, aby wyeksportować, i skompresuj dziennik.
   - W programie Microsoft Edge lub Internet Explorer kliknij prawym przyciskiem myszy błędy i wybierz polecenie **Kopiuj wszystkie**.
7. Zamknij narzędzia programistyczne.

## <a name="next-steps"></a>Następne kroki

[Tworzenie](how-to-create-assessment.md) lub [dostosowywanie](how-to-modify-assessment.md) oceny.
