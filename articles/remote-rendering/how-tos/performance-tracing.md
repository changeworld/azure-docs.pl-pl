---
title: Tworzenie śladów wydajności po stronie klienta
description: Najważniejsze wskazówki dotyczące profilowania wydajności po stronie klienta przy użyciu WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681313"
---
# <a name="create-client-side-performance-traces"></a>Tworzenie śladów wydajności po stronie klienta

Istnieje wiele powodów, dla których wydajność renderowania zdalnego platformy Azure może nie być tak dobra, jak jest to pożądane. Oprócz czystej wydajności renderowania na serwerze w chmurze, zwłaszcza jakość połączenia sieciowego ma znaczący wpływ na środowisko. Aby profilować wydajność serwera, zapoznaj się z [rozdziałem Kwerendy wydajności po stronie serwera](../overview/features/performance-queries.md).

W tym rozdziale skupiono się na tym, jak zidentyfikować potencjalne wąskie gardła po stronie klienta za pomocą *śladów wydajności.*

## <a name="getting-started"></a>Wprowadzenie

Jeśli jesteś nowy w funkcji śledzenia wydajności systemu Windows, w tej sekcji będzie mowa o najbardziej podstawowych terminów i aplikacji, aby rozpocząć.

### <a name="installation"></a>Instalacja

Aplikacje używane do śledzenia za pomocą ARR są narzędzia ogólnego przeznaczenia, które mogą być używane do wszystkich programów rozwoju systemu Windows. Są one dostarczane za pośrednictwem [zestawu narzędzi wydajności systemu Windows](https://docs.microsoft.com/windows-hardware/test/wpt/). Aby uzyskać ten zestaw narzędzi, pobierz [zestaw oceny i wdrażania systemu Windows](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Podczas wyszukiwania informacji o ślady wydajności, nieuchronnie natkniesz się na szereg terminów. Najważniejsze z nich to:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** oznacza [ **E**vent **T**racing dla **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Jest to po prostu nadrzędna nazwa wydajnej funkcji śledzenia na poziomie jądra, która jest wbudowana w system Windows. Nazywa się śledzenie *zdarzeń,* ponieważ aplikacje obsługujące ETW będą emitować zdarzenia do rejestrowania akcji, które mogą pomóc w śledzeniu problemów z wydajnością. Domyślnie system operacyjny już emituje zdarzenia dla rzeczy, takich jak dostęp do dysku, przełączniki zadań i takie. Aplikacje takie jak ARR dodatkowo emitują niestandardowe zdarzenia, na przykład o porzuconych klatkach, opóźnieniach sieciowych itp.

**ETL** oznacza **E**vent **T**wyścigu **L**ogging. Oznacza to po prostu, że ślad został zebrany (zarejestrowany) i dlatego jest zazwyczaj używany jako rozszerzenie pliku dla plików, które przechowują dane śledzenia. W związku z tym, gdy zrobić \*śledzenia, zazwyczaj będzie miał plik .etl później.

**WPR** oznacza [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) i jest nazwą aplikacji, która uruchamia i zatrzymuje rejestrowanie śladów zdarzeń. WPR przyjmuje plik\*profilu ( .wprp), który konfiguruje, które dokładne zdarzenia do zarejestrowania. Taki `wprp` plik jest dostarczany z SDK ARR. Podczas wykonywania śladów na komputerze stacjonarnym można uruchomić WPR bezpośrednio. Podczas wykonywania śledzenia na HoloLens, zazwyczaj przejść przez interfejs sieci web zamiast tego.

**WPA** oznacza [ **W**indows **P**erformance **nalyzer**](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) i jest nazwą aplikacji \*GUI, która jest używana do otwierania plików .etl i przesiewać dane w celu zidentyfikowania problemów z wydajnością. WPA umożliwia sortowanie danych według różnych kryteriów, wyświetlanie danych na kilka sposobów, odkopanie szczegółów i skorelowanie informacji.

Ślady ETL można tworzyć na dowolnym urządzeniu z systemem Windows (komputer lokalny, HoloLens, serwer w chmurze itp.), są one zazwyczaj zapisywane na dysku i analizowane za pomocą WPA na komputerze stacjonarnym. Pliki ETL mogą być wysyłane do innych programistów, aby mogli się przyjrzeć. Należy pamiętać, że poufne informacje, takie jak ścieżki plików i adresy IP, mogą być przechwytywane w ślady ETL, choć. EtW można użyć na dwa sposoby: do rejestrowania śladów lub do analizowania śladów. Rejestrowanie śladów jest proste i wymaga minimalnej konfiguracji. Analiza śladów z drugiej strony wymaga przyzwoitego zrozumienia zarówno narzędzia WPA, jak i badanego problemu. Poniżej podano ogólne materiały do nauki WPA, a także wytyczne dotyczące interpretacji śladów specyficznych dla ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Rejestrowanie śladu na komputerze lokalnym

Aby zidentyfikować problemy z wydajnością ARR należy wolisz zrobić śledzenie bezpośrednio na HoloLens, ponieważ jest to jedyny sposób, aby uzyskać migawkę prawdziwych cech wydajności. Jednak jeśli w szczególności chcesz zrobić śledzenie bez ograniczeń wydajności HoloLens lub po prostu chcesz dowiedzieć się, jak korzystać z WPA i nie potrzebujesz realistycznego śledzenia, oto jak to zrobić.

### <a name="wpr-configuration"></a>Konfiguracja WPR

1. Uruchom [rejestrator wydajności systemu Windows](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) z *menu Start*.
1. Rozwiń **więcej opcji**
1. Kliknij **pozycję Dodaj profile...**
1. Wybierz plik *AzureRemoteRenderingNetworkProfiling.wprp*. Ten plik można znaleźć w SDK ARR w obszarze *Narzędzia/ETLProfiles*.
   Profil zostanie wyświetlony w obszarze WPR w obszarze *Pomiary niestandardowe*. Upewnij się, że jest to jedyny włączony profil.
1. Rozwiń *pierwszy poziom triage:*
    * Jeśli wszystko, co chcesz zrobić, to przechwycić szybki ślad zdarzeń sieciowych ARR, **wyłącz** tę opcję.
    * Jeśli chcesz skorelować zdarzenia sieciowe ARR z innymi cechami systemu, takimi jak użycie procesora CPU lub pamięci, **włącz** tę opcję.
    * Jeśli ta opcja zostanie włączona, śledzenie będzie najprawdopodobniej ma rozmiar wielu gigabajtów i zajmie dużo czasu, aby zapisać i otworzyć w WPA.

Następnie konfiguracja WPR powinna wyglądać następująco:

![Konfiguracja WPR](./media/wpr.png)

### <a name="recording"></a>Nagrywanie

Kliknij **przycisk Start,** aby rozpocząć nagrywanie śledzenia. Nagrywanie można rozpocząć i zatrzymać w dowolnym momencie; nie trzeba zamykać aplikacji przed wykonaniem tego. Jak widać nie trzeba określać, które aplikacje do śledzenia, jak ETW zawsze rejestruje ślad dla całego systemu. Plik `wprp` określa, które typy zdarzeń do zarejestrowania.

Kliknij **przycisk Zapisz,** aby zatrzymać nagrywanie i określić miejsce przechowywania pliku ETL.

Masz teraz plik ETL, który możesz otworzyć bezpośrednio w WPA lub wysłać do innej osoby.

## <a name="recording-a-trace-on-a-hololens"></a>Nagrywanie śladu na HoloLens

Aby nagrać ślad na urządzeniu HoloLens, uruchom urządzenie i wprowadź jego adres IP w przeglądarce, aby otworzyć *portal urządzeń*.

![Portal urządzeń](./media/wpr-hl.png)

1. Po lewej stronie przejdź do *pozycji Śledzenie wydajności > wydajność*.
1. Wybieranie **profili niestandardowych**
1. Kliknij **pozycję Przeglądaj...**
1. Wybierz plik *AzureRemoteRenderingNetworkProfiling.wprp*. Ten plik można znaleźć w SDK ARR w obszarze *Narzędzia/ETLProfiles*.
1. Kliknij **pozycję Rozpocznij śledzenie**
1. HoloLens jest teraz nagrywanie śladu. Upewnij się, że wyzwolić problemy z wydajnością, które chcesz zbadać. Następnie kliknij przycisk **Zatrzymaj śledzenie**.
1. Ślad zostanie wyświetlony w dolnej części strony sieci Web. Kliknij ikonę dysku po prawej stronie, aby pobrać plik ETL.

Masz teraz plik ETL, który możesz otworzyć bezpośrednio w WPA lub wysłać do innej osoby.

## <a name="analyzing-traces-with-wpa"></a>Analizowanie śladów za pomocą WPA

### <a name="wpa-basics"></a>Podstawy WPA

Analizator wydajności systemu Windows jest standardowym narzędziem do otwierania plików ETL i sprawdzania śladów. Wyjaśnienie, jak działa WPA jest poza zakresem dla tego artykułu. Aby rozpocząć, zapoznaj się z tymi zasobami:

* Obejrzyj [filmy wprowadzające,](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) aby uzyskać pierwszy przegląd.
* Sama WPA ma kartę *Wprowadzenie,* która wyjaśnia typowe kroki. Zapoznaj się z dostępnymi tematami. Szczególnie w obszarze "Wyświetl dane" otrzymujesz krótkie wprowadzenie do tworzenia wykresów dla określonych danych.
* Jest doskonała informacja [na tej stronie,](https://randomascii.wordpress.com/2015/09/24/etw-central/)jednak nie wszystko jest istotne dla początkujących.

### <a name="graphing-data"></a>Dane wykresów

Aby rozpocząć śledzenie ARR, warto wiedzieć o następujących elementach.

![Wykres wydajności](./media/wpa-graph.png)

Powyższy obraz ekspozycyjny przedstawia tabelę danych śledzenia i reprezentację wykresu tych samych danych.

W tabeli na dole zanotuj żółty (złoty) pasek i niebieski pasek. Można przeciągnąć te paski i umieścić je w dowolnym miejscu.

Wszystkie **kolumny po lewej stronie żółtego paska** są interpretowane jako **klawisze**. Klawisze są używane do struktury drzewa w lewym górnym oknie. Tutaj mamy dwie *kolumny kluczy,* "Nazwa dostawcy" i "Nazwa zadania". W związku z tym struktura drzewa w lewym górnym oknie jest dwa poziomy głębokości. Jeśli zmienisz kolejność kolumn lub dodasz lub usuniesz kolumny z obszaru klucza, struktura w widoku drzewa ulegnie zmianie.

**Kolumny po prawej stronie niebieskiego paska** są używane do **wyświetlania wykresu** w prawym górnym oknie. W większości przypadków używana jest tylko pierwsza kolumna, ale niektóre tryby wykresu wymagają wielu kolumn danych. Aby wykresy liniowe działały, należy ustawić *tryb agregacji* w tej kolumnie. Użyj "Avg" lub "Max". Tryb agregacji służy do określania wartości wykresu w danym pikselu, gdy piksel obejmuje zakres z wieloma zdarzeniami. Można to zaobserwować, ustawiając agregację na "Suma", a następnie powiększając i pomniejszając.

Kolumny w środku nie mają specjalnego znaczenia.

![Widok zdarzeń](./media/wpa-event-view.png)

W *Edytorze widoku zdarzeń ogólnych* można skonfigurować wszystkie kolumny do wyświetlania, tryb agregacji, sortowanie i kolumny, które są używane jako klucze lub do wykresów. W powyższym **przykładzie pole 2** jest włączone, a pole 3 - 6 jest wyłączone. Pole 2 jest zazwyczaj pierwszym *polem danych niestandardowych* zdarzenia ETW, a tym samym dla zdarzeń ARR "FrameStatistics", które reprezentują pewną wartość opóźnienia sieci. Włącz inne kolumny "Pole", aby wyświetlić dalsze wartości tego zdarzenia.

### <a name="presets"></a>Ustawień

Aby prawidłowo przeanalizować ślad, należy ustalić własny przepływ pracy i preferowany wyświetlacz danych. Jednak, aby móc uzyskać szybki przegląd zdarzeń specyficznych dla ARR, dołączamy profil platformy ochrony oprogramowania systemu Windows i wstępnie ustawione pliki w folderze *Tools/ETLProfiles*. Aby załadować pełny profil, wybierz *profile > Zastosuj...* z paska menu WPA lub otwórz panel *Moje ustawienia wstępne* *(Okno > Moje ustawienia predefiniowane)* i wybierz polecenie *Importuj*. Pierwsza z nich skonfiguruje pełną konfigurację WPA, jak na poniższym obrazku. Ten ostatni będzie tylko presety dla różnych konfiguracji widoku dostępne i pozwalają szybko otworzyć widok, aby spojrzeć na określony fragment danych zdarzeń ARR.

![Ustawień](./media/wpa-arr-trace.png)

Powyższy obraz ekspozycyjny przedstawia widoki różnych zdarzeń specyficznych dla ARR oraz widok ogólnego wykorzystania procesora CPU.

## <a name="next-steps"></a>Następne kroki

* [Kwerendy wydajności po stronie serwera](../overview/features/performance-queries.md)
