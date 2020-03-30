---
title: Diagnostyka wydajności dla maszyn wirtualnych platformy Azure| Dokumenty firmy Microsoft
description: Przedstawia diagnostykę wydajności platformy Azure dla systemu Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080596"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostyka wydajności maszyn wirtualnych platformy Azure

Narzędzie do diagnostyki wydajności pomaga rozwiązywać problemy z wydajnością, które mogą mieć wpływ na maszynę wirtualną systemu Windows lub Linux (VM). Obsługiwane scenariusze rozwiązywania problemów obejmują szybkie sprawdzanie znanych problemów i najlepszych rozwiązań oraz złożone problemy, które wiążą się z powolną wydajnością maszyny Wirtualnej lub wysokim użyciem procesora CPU, miejsca na dysku lub pamięci.

Diagnostykę wydajności można uruchomić bezpośrednio z witryny Azure Portal, gdzie można również przeglądać szczegółowe informacje i raport na temat różnych dzienników, zaawansowanej konfiguracji i danych diagnostycznych. Przed skontaktowaniem się z pomocą techniczną firmy Microsoft zaleca się uruchomienie diagnostyki wydajności i zapoznanie się z danymi szczegółowych i diagnostycznych.

> [!NOTE]
> W systemie Windows diagnostyka wydajności jest obecnie obsługiwana na maszynach wirtualnych z zainstalowanym pakietem .NET SDK w wersji 4.5 lub nowszej. Aby zapoznać się z instrukcjami dotyczącymi uruchamiania diagnostyki wydajności na klasycznych maszynach wirtualnych, zobacz [rozszerzenie maszyny wirtualnej diagnostyki wydajności platformy Azure.](performance-diagnostics-vm-extension.md)

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server`*`6.10 [ ], 7.3, 7.6, 7.5 (Obraz rynku Oracle-Database-Ee 13.8), CentOS 6.5 [`*`], 7.6, RHEL`*`7.2, 7.5, 8.0 [ ], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Zapoznaj się [ze znanymi problemami](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalowanie i uruchamianie diagnostyki wydajności na maszynie wirtualnej

Diagnostyka wydajności instaluje rozszerzenie maszyny Wirtualnej, która uruchamia narzędzie diagnostyczne o nazwie PerfInsights. PerfInsights jest dostępny zarówno dla [systemu Windows,](https://aka.ms/perfinsights) jak i [Linux](https://aka.ms/perfinsightslinux). Aby zainstalować i uruchomić diagnostykę wydajności, wykonaj następujące kroki:

1. W lewej kolumnie poleceń wybierz pozycję **Maszyny wirtualne**.
1. Z listy nazw maszyn wirtualnych wybierz maszynę wirtualną, na której chcesz uruchomić diagnostykę.
1. W prawej kolumnie poleceń wybierz pozycję **Diagnostyka wydajności**.

    ![Zrzut ekranu przedstawiający witrynę Azure portal z wyróżnionym przyciskiem Diagnostyka wydajności instalacji](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Na tym zrzucie ekranu blok nazw maszyn wirtualnych jest ukryty.
1. Wybieranie konta magazynu (opcjonalnie)

    Jeśli chcesz użyć jednego konta magazynu do przechowywania wyników diagnostyki wydajności dla wielu maszyn wirtualnych, możesz wybrać konto magazynu, klikając przycisk **Ustawienia** na pasku narzędzi. Po wybraniu konta magazynu kliknij przycisk **OK.**

    Jeśli konto magazynu nie zostanie określone, zostanie utworzone domyślnie nowe konto magazynu.

    ![Zrzut ekranu przedstawiający blok Diagnostyka wydajności z wyróżnionym przyciskiem paska narzędzi Ustawienia](media/performance-diagnostics/settings-button.png)

    ![Zrzut ekranu przedstawiający wybór konta magazynu w bloku ustawienia diagnostyki wydajności](media/performance-diagnostics/select-storage-account.png)

1. Wybierz przycisk **Zainstaluj diagnostykę wydajności.**
1. Zaznacz pole wyboru **Uruchom diagnostykę,** jeśli chcesz uruchomić diagnostykę po zakończeniu instalacji. Jeśli dokonasz tego wyboru, będziesz mógł wybrać scenariusz analizy wydajności i powiązane opcje.

    ![Zrzut ekranu przedstawiający przycisk instalacji diagnostyki wydajności](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Wybierz scenariusz analizy do uruchomienia

Poniższe scenariusze analizy są dostępne w witrynie Azure portal. Wybierz analizę, w zależności od problemu z wydajnością, który masz. Wybierz opcje czasu trwania i śledzenia, zgodnie z koniecznością dla analizy.

* **Szybka analiza wydajności**  
    Sprawdza znane problemy, analizuje najlepsze rozwiązania i zbiera dane diagnostyczne. Ta analiza trwa kilka minut, aby uruchomić. Dowiedz się więcej [Windows](https://aka.ms/perfinsights/quick) lub [Linux](https://aka.ms/perfinsightslinux/quick)

* **Analiza wydajności**  
    Obejmuje wszystkie kontrole w szybkiej analizie wydajności i monitoruje wysokie zużycie zasobów. Ta wersja służy do rozwiązywania problemów z ogólną wydajnością, takich jak wysokie użycie procesora CPU, pamięci i dysku. Ta analiza trwa od 30 sekund do 15 minut, w zależności od wybranego czasu trwania. Dowiedz się więcej [Windows](https://aka.ms/perfinsights/vmslow) lub [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Zaawansowana analiza wydajności**`*`  
    Zawiera wszystkie kontrole w analizie wydajności i zbiera jeden lub więcej śladów, jak wymieniono w poniższych sekcjach. Ten scenariusz służy do rozwiązywania złożonych problemów, które wymagają dodatkowych śladów. Uruchamianie tego scenariusza przez dłuższy czas zwiększy ogólny rozmiar danych wyjściowych diagnostyki, w zależności od rozmiaru maszyny Wirtualnej i opcji śledzenia, które są zaznaczone. Ta analiza trwa od 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](https://aka.ms/perfinsights/advanced)

* **Analiza plików platformy Azure**`*`  
    Zawiera wszystkie kontrole w analizie wydajności i przechwytuje śledzenia sieci i liczniki SMB. Użyj tego scenariusza, aby rozwiązać problem z wydajnością plików platformy Azure. Ta analiza trwa od 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Te scenariusze analizy są obsługiwane tylko w systemie Windows.

![Zrzut ekranu przedstawiający okienko diagnostyki uruchamiania wewnątrz bloku diagnostyki wydajności](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Podaj objawy (opcjonalnie)

Wybierz wstępnie wybrane objawy z listy lub dodaj nowe objawy. Pomaga nam to poprawić analizę w przyszłości.

### <a name="provide-support-request-number-if-available-optional"></a>Podaj numer żądania pomocy technicznej, jeśli jest dostępny (opcjonalnie)

Jeśli pracujesz z inżynierem pomocy technicznej firmy Microsoft na istniejącym bilecie pomocy technicznej, podaj numer biletu pomocy technicznej.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Zapoznaj się z polityką prywatności i warunkami prawnymi i zaznacz pole wyboru, aby je potwierdzić (wymagane)

Aby uruchomić diagnostykę, musisz zaakceptować warunki prawne i zaakceptować politykę prywatności.

### <a name="select-ok-to-run-the-diagnostics"></a>Wybierz przycisk OK, aby uruchomić diagnostykę

Powiadomienie jest wyświetlane jako diagnostyka wydajności rozpoczyna się instalacja. Po zakończeniu instalacji zostanie wyświetlone powiadomienie wskazujące, że instalacja zakończyła się pomyślnie. Wybrana analiza jest następnie uruchamiana przez określony czas. Byłoby to dobry moment, aby odtworzyć problem z wydajnością, tak aby dane diagnostyczne mogą być przechwytywane w odpowiednim czasie.

Po zakończeniu analizy następujące elementy są przekazywane do tabel platformy Azure i kontenera dużego obiektu binarnego (BLOB) na określonym koncie magazynu:

* Wszystkie szczegółowe informacje i powiązane informacje o przebiegu
* Plik skompresowany (.zip) (o nazwie **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip)** w systemie Windows i plik tar ball (o nazwie **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz** ) w systemie Linux, który zawiera pliki dziennika
* Raport HTML

Po przekazaniu nowy raport diagnostyczny jest wyświetlany w witrynie Azure portal.

![Zrzut ekranu przedstawiający listę raportu diagnostycznego w bloku diagnostyki wydajności](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Jak zmienić ustawienia diagnostyki wydajności

Użyj przycisku Pasek narzędzi **Ustawienia,** aby zmienić konto magazynu, na którym mogą być przechowywane szczegółowe informacje diagnostyczne i dane wyjściowe. Można użyć tego samego konta magazynu dla wielu maszyn wirtualnych, które używają diagnostyki wydajności. Po zmianie konta magazynu stare raporty i szczegółowe informacje nie są usuwane. Jednak nie będą one już wyświetlane na liście raportów diagnostycznych.

## <a name="review-insights-and-performance-diagnostics-report"></a>Przejrzyj raport dotyczący szczegółowych informacji i diagnostyki wydajności

Każde uruchomienie diagnostyczne zawiera listę szczegółowych informacji i zaleceń, zasoby, których dotyczy problem, pliki dziennika i inne szczegółowe informacje diagnostyczne, które są zbierane, a także raport do przeglądania w trybie offline. Aby uzyskać pełną listę wszystkich zebranych danych diagnostycznych, zobacz **Jakiego rodzaju informacje są zbierane przez PerfInsights?** w [systemie Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) lub [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Wybieranie raportu diagnostyki wydajności

Można użyć listy raportów diagnostycznych, aby znaleźć wszystkie raporty diagnostyczne, które zostały uruchomione. Lista zawiera szczegółowe informacje na temat analizy, która została użyta, szczegółowe informacje, które zostały znalezione i ich poziomy wpływu. Wybierz wiersz, aby wyświetlić więcej szczegółów.

![Zrzut ekranu przedstawiający wybieranie raportu diagnostycznego z bloku diagnostyki wydajności](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Przeglądanie raportu diagnostyki wydajności

Każdy raport diagnostyki wydajności może zawierać kilka szczegółowych informacji i wskazywać poziom wpływu wysoki, średni lub niski. Każdy wgląd zawiera również zalecenia, które pomogą zmniejszyć obawy. Szczegółowe informacje są pogrupowane w celu łatwego filtrowania.

Poziomy wpływu reprezentują potencjał problemów z wydajnością, na podstawie czynników, takich jak błędna konfiguracja, znane problemy lub problemy, które są zgłaszane przez innych użytkowników. Być może nie występują jeszcze co najmniej jeden z wymienionych problemów. Na przykład może mieć pliki dziennika SQL i plików bazy danych na tym samym dysku danych. Ten warunek ma duży potencjał wąskich gardeł i innych problemów z wydajnością, jeśli użycie bazy danych jest wysoki, podczas gdy nie można zauważyć problemu, jeśli użycie jest niskie.

![Zrzut ekranu przedstawiający przegląd raportu diagnostyki wydajności](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Przeglądanie szczegółowych informacji i zaleceń dotyczących diagnostyki wydajności

Można wybrać wgląd, aby wyświetlić więcej szczegółów na temat zasobów, których dotyczy problem, sugerowane środki zaradcze i łącza referencyjne.

![Zrzut ekranu przedstawiający szczegółowe informacje dotyczące diagnostyki wydajności](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Pobieranie i przeglądanie raportu z pełną diagnostyką wydajności

Za pomocą przycisku **Pobierz raport** można pobrać raport HTML zawierający dodatkowe zaawansowane informacje diagnostyczne, takie jak konfiguracja magazynu i sieci, liczniki wydajności, ślady, lista procesów i dzienniki. Zawartość zależy od wybranej analizy. W przypadku zaawansowanego rozwiązywania problemów raport może zawierać dodatkowe informacje i interaktywne wykresy, które są związane z wysokim użyciem procesora CPU, wysokim użyciem dysku i procesami zużywającymi nadmierną ilość pamięci. Aby uzyskać więcej informacji na temat raportu diagnostyki wydajności, zobacz [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) lub [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Zarządzanie raportami diagnostyki wydajności

Można usunąć jeden lub więcej raportów diagnostyki wydajności za pomocą przycisku **Usuń raport.**

## <a name="how-to-uninstall-performance-diagnostics"></a>Jak odinstalować diagnostykę wydajności

Diagnostykę wydajności można odinstalować z maszyny Wirtualnej. Ta akcja usuwa rozszerzenie maszyny Wirtualnej, ale nie wpływa na dane diagnostyczne, które są na koncie magazynu.

![Zrzut ekranu przedstawiający pasek narzędzi bloku diagnostyki wydajności z wyróżnionym przyciskiem Odinstaluj](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Gdzie są przechowywane dane diagnostyczne z mojej maszyny Wirtualnej

Wszystkie szczegółowe informacje i raporty dotyczące diagnostyki wydajności są przechowywane na twoim koncie magazynu. Szczegółowe informacje są przechowywane w tabelach platformy Azure. Raporty skompresowany plik jest przechowywany w kontenerze binarnym dużych obiektów (BLOB), który jest nazwany azdiagextnresults.

Informacje o koncie magazynu można wyświetlić za pomocą przycisku Ustawienia na pasku narzędzi.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Jak udostępnić te dane działowi obsługi klienta firmy Microsoft

Istnieje wiele sposobów udostępniania raportu diagnostycznego firmie Microsoft.

**Wariant 1:** Automatyczne udostępnianie najnowszego raportu  
Po otwarciu biletu pomocy technicznej z firmą Microsoft, ważne jest, aby udostępnić raport diagnostyki wydajności. Jeśli użytkownik zdecyduje się udostępnić te informacje firmie Microsoft podczas uruchamiania diagnostyki (zaznaczając pole wyboru "**Wyrażam zgodę na udostępnianie informacji diagnostycznych firmie Microsoft"),** firma Microsoft będzie mogła uzyskać dostęp do raportu z konta magazynu przy użyciu łącza SAS do wyjściowego pliku zip przez okres do 30 dni od daty uruchomienia. Tylko najnowszy raport jest dostępny dla inżyniera pomocy technicznej.

**Wariant 2:** Generowanie podpisu dostępu współdzielonego dla skompresowanego pliku raportu diagnostycznego  
Łącze do skompresowanego pliku raportów można udostępnić przy użyciu podpisów dostępu współdzielonego. W tym celu wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do konta magazynu, w którym są przechowywane dane diagnostyczne.
1. Wybierz **pozycję Obiekty blob** w sekcji **usługa obiektów blob.**
1. Wybierz kontener **azdiagextnresults.**
1. Wybierz skompresowany plik wyjściowy diagnostyki wydajności, który chcesz udostępnić.
1. Na karcie **Generowanie sygnatury dostępu Współdzielonego** wybierz kryteria udostępniania.
1. Kliknij **pozycję Generuj token i adres URL sygnatury dostępu Współdzielonego**obiektu blob .
1. Skopiuj **adres URL sygnatury dostępu Współdzielonego**obiektu blob i udostępnij go inżynierowi pomocy technicznej.

**Wariant 3:** Pobierz raport z konta magazynu

Raport diagnostyki wydajności można również zlokalizować, korzystając z kroków 1–4 w opcji 2. Wybierz, aby pobrać plik, a następnie udostępnić go za pośrednictwem poczty e-mail lub poprosić inżyniera pomocy technicznej o instrukcje dotyczące przekazania pliku.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Jak przechwycić dane diagnostyczne we właściwym czasie

Każdy przebieg diagnostyki wydajności ma dwa etapy:

1. Zainstaluj lub zaktualizuj rozszerzenie maszyny wirtualnej diagnostyki wydajności.
1. Uruchom diagnostykę przez określony czas trwania.

Obecnie nie ma łatwego sposobu, aby dokładnie wiedzieć, kiedy instalacja rozszerzenia maszyny Wirtualnej jest zakończona. Zazwyczaj trwa około 45 sekund do 1 minuty, aby zainstalować rozszerzenie maszyny Wirtualnej. Po zainstalowaniu rozszerzenia maszyny Wirtualnej można uruchomić kroki repro, aby diagnostyka wydajności przechwyciła poprawny zestaw danych do rozwiązywania problemów.

## <a name="next-steps"></a>Następne kroki

Po przejrzeniu szczegółowych informacji i raportów diagnostyki wydajności, jeśli nadal nie można ustalić przyczyny problemu i potrzebujesz więcej pomocy, możesz otworzyć bilet pomocy technicznej za pomocą działu obsługi klienta firmy Microsoft.

Jeśli potrzebujesz więcej pomocy w dowolnym momencie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN Azure i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie można zgłosić zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz pozycję Uzyskaj pomoc **techniczną**. Aby uzyskać informacje na temat korzystania z pomocy technicznej platformy Azure, przeczytaj często zadawane [pytania dotyczące pomocy technicznej platformy Microsoft Azure](https://azure.microsoft.com/support/faq/).
