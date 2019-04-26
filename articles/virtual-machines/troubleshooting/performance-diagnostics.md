---
title: Diagnostyka wydajności maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Wprowadzono Windows Diagnostyka wydajności platformy Azure.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308197"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnostyka wydajności maszyn wirtualnych platformy Azure

Narzędzia do diagnostyki wydajności pomagają w rozwiązywaniu problemów z wydajnością, które mogą wpłynąć na maszynę wirtualną (VM) Windows. Obsługiwane scenariusze dotyczące rozwiązywania problemów obejmują szybkie kontrole dotyczące znanych problemów i najlepsze rozwiązania i złożonych problemów, które obejmują niską wydajnością maszyny Wirtualnej lub dużego wykorzystania procesora CPU, miejsca na dysku lub pamięci. 

Diagnostyka wydajności można uruchomić bezpośrednio z witryny Azure portal, gdzie możesz również przejrzeć szczegółowe informacje i raport na temat różnych dzienników, zaawansowanych konfiguracji i danych diagnostycznych. Firma Microsoft zaleca uruchamianie diagnostyki wydajności, a następnie przejrzyj dane szczegółowe informacje i Diagnostyka przed skontaktuj się z pomocą firmy Microsoft Support.

> [!NOTE]
> Diagnostyka wydajności jest obecnie obsługiwane dla maszyn wirtualnych Windows, które mają, zestaw SDK platformy .NET w wersji 4.5 lub nowszy. Instrukcje systemem Diagnostyka wydajności klasycznych maszyn wirtualnych, zobacz [rozszerzenia maszyny Wirtualnej diagnostyki wydajności Azure](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
System Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Wersje Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Zainstaluj i uruchom funkcję diagnostyki wydajności na maszynie Wirtualnej
Diagnostyka wydajności instaluje rozszerzenia maszyny Wirtualnej, które jest uruchamiane narzędzie Diagnostyka, który nosi nazwę [program PerfInsights](https://aka.ms/perfinsights). Aby zainstalować i uruchomić diagnostyki wydajności, wykonaj następujące kroki:
1.  W lewej kolumnie zaznacz **maszyn wirtualnych**.
1.  Z listy nazw maszyn wirtualnych wybierz maszynę Wirtualną, którą chcesz uruchomić diagnostykę.
1.  W prawej kolumnie zaznacz **Diagnostyka wydajności**.

    ![Zrzut ekranu usługi Azure portal, z wyróżnionym przyciskiem diagnostyki wydajności instalacji](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > W tym zrzucie ekranu jest ukryty bloku nazw maszyn wirtualnych.
1. Wybierz konto magazynu (opcjonalnie)

    Jeśli chcesz używać jednego konta magazynu do przechowywania wyników diagnostyki wydajności dla wielu maszyn wirtualnych, można wybrać konto magazynu, klikając **ustawienia** przycisku na pasku narzędzi. Kliknij przycisk **OK** przycisku po wybraniu konta magazynu.

    Jeśli nie określisz konta magazynu, będą domyślnie tworzone nowe konto magazynu.

    ![Zrzut ekranu wydajności diagnostyki bloku z wyróżnionym przyciskiem paska narzędzi ustawienia](media/performance-diagnostics/settings-button.png)

    ![Zrzut ekranu przedstawiający wybór konta magazynu z bloku ustawień diagnostyki wydajności](media/performance-diagnostics/select-storage-account.png)

1. Wybierz **zainstalować Diagnostyka wydajności** przycisku.
1. Wybierz **Uruchom diagnostykę** pole wyboru, jeśli chcesz uruchomić diagnostyki, po zakończeniu instalacji. Jeśli wprowadzisz zaznacz to pole wyboru, będzie można wybrać scenariuszowi analizy wydajności i powiązane opcje.

    ![Zrzut ekranu wydajności diagnostyki przycisk Zainstaluj](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Wybierz scenariusz analizy do uruchomienia

Następujące scenariusze analizy są dostępne w witrynie Azure portal. Wybierz analizy, w zależności od problemu z wydajnością, które występują. Wybierz czas trwania i śledzenia zgodnie z potrzebami analizy.

* **Analiza wydajności szybki**  
    Sprawdza, czy znane problemy, analizuje najlepszych rozwiązań i zbiera dane diagnostyczne. Ta analiza trwa kilka minut. [Dowiedz się więcej](https://aka.ms/perfinsights/quick)

* **Analiza wydajności**  
    Zawiera wszystkie testy w analizy wydajności szybki i monitoruje zużycie zasobów. Użyj tej wersji Ogólne rozwiązywanie problemów z wydajnością, takie jak wysokie użycie procesora CPU, pamięci i użycie dysku. Ta analiza trwa 30 sekund do 15 minut w zależności od wybranego czasu trwania. [Dowiedz się więcej](https://aka.ms/perfinsights/vmslow) 
    
* **Analiza wydajności Zaawansowane**  
    Zawiera wszystkie testy w analizy wydajności i umożliwia zbieranie informacji o co najmniej jeden ślady, zgodnie z opisem w poniższych sekcjach. W tym scenariuszu należy użyć do rozwiązywania złożonych problemów, które wymagają dodatkowe dane śledzenia. Uruchomione w tym scenariuszu przez dłuższy czas, zwiększy całkowity rozmiar danych wyjściowych diagnostyki, która znajduje się w zależności od rozmiaru maszyny Wirtualnej i opcje śledzenia, które są wybrane. Ta analiza trwa 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](https://aka.ms/perfinsights/advanced) 
    
* **Usługi Azure analysis plików**  
    Zawiera wszystkie testy w analizy wydajności, a następnie przechwytuje śledzenia sieci i liczniki SMB. Ten scenariusz umożliwia rozwiązywanie problemów z wydajnością usługi Azure files. Ta analiza trwa 30 sekund do 15 minut, w zależności od wybranego czasu trwania. [Dowiedz się więcej](https://aka.ms/perfinsights/azurefiles)


![Zrzut ekranu uruchamiania okienko diagnostyki wewnątrz bloku diagnostyki wydajności](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Podaj objawy (opcjonalnie)
Wybierz żadne objawy wstępnie wybrane z listy lub Dodaj nowe objawy. Ułatwia to nam ulepszyć analizy w przyszłości. 

### <a name="provide-support-request-number-if-available-optional"></a>Podaj numer żądania pomocy technicznej, jeśli jest dostępny (opcjonalnie)
Jeśli pracujesz z pracownikiem pomocy technicznej firmy Microsoft z istniejącego biletu pomocy technicznej, należy podać numer biletu pomocy technicznej. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Przejrzyj postanowienia prawne i zasady zachowania poufności informacji i zaznacz pole wyboru, aby potwierdzić (wymagane)
Aby uruchomić diagnostykę, należy zaakceptować warunki prawne i zaakceptuj zasady ochrony prywatności.

### <a name="select-ok-to-run-the-diagnostics"></a>Wybierz przycisk OK, aby uruchomić diagnostyki 
Diagnostyka wydajności po uruchomieniu instalacji, zostanie wyświetlone powiadomienie. Po zakończeniu instalacji zostanie wyświetlone powiadomienie, która wskazuje, czy Instalacja powiodła się. Wybrana analiza następnie jest uruchamiana dla określonego czasu trwania. Jest to dobry moment, aby odtworzyć problem z wydajnością, dzięki czemu dane diagnostyczne mogą być przechwytywane w odpowiednim czasie. 

Po zakończeniu analizy następujące elementy są przekazywane do tabel platformy Azure i kontener na koncie magazynu określonym duży obiekt binarny (BLOB):

*   Wszystkie szczegółowe informacje i powiązane informacje o przebiegu
*   Plik skompresowany (.zip) danych wyjściowych (o nazwie **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**) zawierający pliki dziennika
*   Raport HTML

Po przekazaniu nowy raport diagnostyki znajduje się w witrynie Azure portal.

![Zrzut ekranu przedstawiający listę raportów diagnostycznych w bloku diagnostyki wydajności](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Jak zmienić ustawienia diagnostyki wydajności
Użyj **ustawienia** przycisku paska narzędzi, aby zmienić konto magazynu, w którym można przechowywać szczegółowych informacji diagnostycznych i danych wyjściowych. Można użyć tego samego konta magazynu dla wielu maszyn wirtualnych, które używają Diagnostyka wydajności. Jeśli zmienisz konto magazynu, stare raportów oraz szczegółowych informacji nie są usuwane. Jednak ich już nie pojawi się na liście raportów diagnostycznych. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Przejrzyj informacje i raport dotyczący wydajności diagnostyki
Każdy diagnostyki uruchamiania zawiera listę szczegółowych informacji i zaleceń, dotkniętych zasobów, plików dziennika i innych zaawansowaną diagnostykę obejmującą uwzględnianie zebranych informacji o, a także raportów do przeglądania w trybie offline. Aby uzyskać pełną listę wszystkich danych diagnostycznych zebranych, zobacz [jakiego rodzaju informacje są zbierane przez program PerfInsights?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Wybierz raport wydajności diagnostyki
Aby znaleźć wszystkie raporty diagnostyczne, które zostały uruchomione, można użyć listy raportów diagnostycznych. Lista zawiera szczegółowe informacje na temat analizy, który został użyty, szczegółowe informacje, które zostały wykryte i ich poziomy wpływu. Zaznacz wiersz, aby wyświetlić więcej szczegółów.

![Zrzut ekranu przedstawiający Wybieranie raport diagnostyczny w bloku diagnostyki wydajności](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Przejrzyj raport Diagnostyka wydajności
Każdy raport diagnostyki wydajności może zawierać wiele szczegółowych informacji i wskazuje stopień wpływu na wysoki, średni lub niski. Każdy szczegółowe informacje zawiera także zalecenia, aby zmniejszyć kwestią. Szczegółowe informacje są grupowane w celu łatwego filtrowania. 

Poziomy wpływu reprezentują potencjalnych problemów z wydajnością, na podstawie czynników, takich jak błędnej konfiguracji, znanych problemów, lub problemów, które są zgłaszane przez innych użytkowników. Nie jeszcze być może wystąpiły co najmniej jeden z wymienionych problemów. Może na przykład mieć pliki dziennika SQL i plików bazy danych na tym samym dysku danych. Ten warunek ma duże ryzyko wąskich gardeł i inne problemy z wydajnością, jeśli użycie bazy danych jest wysokie, natomiast może nie zostać zauważony problemu w przypadku użycia jest niska.

![Zrzut ekranu wydajności diagnostyki raportu bloku przeglądu](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Przeglądanie szczegółowe informacje o wydajności diagnostyki i zalecenia
Możesz wybrać wglądu, aby wyświetlić więcej szczegółów na temat dotkniętych zasobów, sugerowane środki zaradcze i łączy odnośnika. 

![Zrzut ekranu przedstawiający szczegóły insight Diagnostyka wydajności](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Pobrać i przejrzeć raport diagnostyczny pełnej wydajności
Możesz użyć **Pobierz raport** przycisk, aby pobrać raport HTML, który zawiera dodatkowe zaawansowaną diagnostykę obejmującą uwzględnianie informacji, takich jak storage i konfiguracji sieci, liczniki wydajności, ślady, listy procesów i dzienniki. Zawartość zależy od wybranej analizy. Do zaawansowanego rozwiązywania problemów, raport może zawierać dodatkowe informacje i wykresy interaktywne, that are related to wysokie użycie procesora CPU, wysokiemu użyciu dysku i procesy, które zużywają zbyt dużej ilości pamięci. Aby uzyskać więcej informacji o raporcie dotyczącym wydajności diagnostyki, zobacz [Przejrzyj raport diagnostyczny](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Zarządzanie raportami Diagnostyka wydajności
Można usunąć co najmniej jeden raport diagnostyki wydajności za pomocą **usunąć raport** przycisku.

## <a name="how-to-uninstall-performance-diagnostics"></a>Jak odinstalować Diagnostyka wydajności
Diagnostyka wydajności można odinstalować z maszyny Wirtualnej. Ta akcja usuwa rozszerzenie maszyny Wirtualnej, ale nie ma wpływu na żadnych danych diagnostycznych na koncie magazynu. 

![Zrzut ekranu przedstawiający paska narzędzi bloku diagnostyki wydajności z wyróżnionym przyciskiem dezinstalacji](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Gdzie są dane diagnostyczne z mojej maszyny Wirtualnej przechowywany? 
Wszystkie szczegółowe informacje diagnostyczne o wydajności i raporty są przechowywane na koncie magazynu. Szczegółowe informacje są przechowywane w tabelach platformy Azure. Skompresowany plik raportów znajduje się w kontenerze duży obiekt binarny (BLOB), który nosi nazwę azdiagextnresults.

Możesz wyświetlić informacje o koncie magazynu przy użyciu przycisku Ustawienia na pasku narzędzi. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Jak udostępnić te dane za pomocą techniczną firmy Microsoft? 
Istnieje wiele sposobów, aby udostępnić raport diagnostyczny z firmą Microsoft.

**Opcja 1:** Automatycznie udostępniaj najnowszy raport  
Możesz otworzyć bilet pomocy technicznej firmy Microsoft, jest ważne, aby udostępnić raport diagnostyki wydajności. Jeśli wybrana została opcja udostępniać tych informacji z firmą Microsoft, podczas gdy uruchomić diagnostyki (, wybierając pozycję "**zgodę na udostępnianie informacji diagnostycznych firmie Microsoft**" pole wyboru), firma Microsoft będzie uzyskiwać dostęp do raportu z usługi storage konta, używając linku sygnatury dostępu Współdzielonego do pliku zip, dane wyjściowe do 30 dni od daty uruchomienia. Tylko najnowszy raport jest dostępny do ze specjalistą pomocy technicznej. 

**Opcja 2:** Generuj sygnaturę dostępu współdzielonego dla skompresowanego pliku raportu diagnostyki  
Link do skompresowanego pliku raportów może udostępniać za pomocą sygnatur dostępu współdzielonego. W tym celu wykonaj następujące kroki: 
1.  W witrynie Azure portal przejdź do konta magazynu, w którym przechowywane są dane diagnostyczne.
1.  Wybierz **obiektów blob** w obszarze **usługi Blob service** sekcji. 
1.  Wybierz **azdiagextnresults** kontenera.
1.  Wybierz wydajności diagnostyki skompresowany plik wyjściowy, którą chcesz udostępnić.
1.  Na **Generowanie sygnatury dostępu Współdzielonego** , a następnie wybierz kryteria do udostępniania. 
1.  Kliknij przycisk **generowania tokenu sygnatury dostępu Współdzielonego obiektów blob i adres URL**.
1.  Kopiuj **adresu URL sygnatury dostępu Współdzielonego obiektu Blob**i udostępnij je ze specjalistą pomocy technicznej. 

**Opcja 3:** Pobierz raport z konta magazynu

Możesz również znaleźć skompresowany plik raportu diagnostyki wydajności, wykonując kroki 1 – 4 w — opcja 2. Zaznacz, aby pobrać plik, a następnie udostępnić je za pośrednictwem poczty e-mail lub poproś ze specjalistą pomocy technicznej, aby uzyskać instrukcje przekazać plik.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Jak przechwycić dane diagnostyczne w odpowiednim czasie?
Każdy Diagnostyka wydajności uruchamiania ma dwa etapy: 
1.  Instalowanie lub aktualizowanie Diagnostyka wydajności rozszerzenia maszyny Wirtualnej.
1.  Uruchamianie diagnostyki dla określonego czasu trwania.

Obecnie nie istnieje łatwy sposób dowiedzieć się, kiedy instalacja rozszerzenia maszyny Wirtualnej zostało zakończone. Zwykle trwa około 45 sekund do 1 minuty, można zainstalować rozszerzenia maszyny Wirtualnej. Po zainstalowaniu rozszerzenia maszyny Wirtualnej możesz uruchomić kroków odtwarzania mieć Diagnostyka wydajności przechwycenia odpowiednich zestawów danych do rozwiązywania problemów. 

## <a name="next-steps"></a>Kolejne kroki
Po przejrzeniu szczegółowe informacje diagnostyczne o wydajności i raportów, jeśli nadal nie można ustalić przyczynę problemu i potrzebujesz więcej pomocy dotyczącej możesz otworzyć bilet pomocy technicznej z obsługą klienta firmy Microsoft. 

Jeśli potrzebujesz dodatkowej pomocy w dowolnym momencie, w tym artykule, możesz skontaktować się ze ekspertów platformy Azure na [forów platformy Azure z subskrypcją MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/)i wybierz **uzyskać pomoc techniczną**. Aby uzyskać informacje o korzystaniu z pomocy technicznej platformy Azure, przeczytaj [pomocy technicznej Microsoft Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/).
