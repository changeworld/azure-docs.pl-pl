---
title: Narzędzie do kontroli ArrInspector
description: Instrukcja obsługi narzędzia ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680078"
---
# <a name="the-arrinspector-inspection-tool"></a>Narzędzie do kontroli ArrInspector

ArrInspector jest narzędziem opartym na sieci Web używanym do inspekcji uruchomionej sesji zdalnego renderowania platformy Azure. Jest przeznaczony do celów debugowania, do sprawdzania struktury sceny renderowane, pokaż komunikaty dziennika i monitorowania wydajności na żywo na serwerze.

![ArrInspector (ArrInspector)](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Podłączanie do ArrInspector

Po uzyskaniu nazwy hosta (kończącej się na) `mixedreality.azure.com`serwera ARR, połącz się za pomocą [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Ta funkcja `StartArrInspector.html` tworzy na urządzeniu, na którym aplikacja jest uruchomiona. Aby uruchomić ArrInspector, otwórz ten plik za pomocą przeglądarki (Edge, Firefox lub Chrome) na komputerze. Plik jest ważny tylko przez 24 godziny.

Jeśli aplikacja, `ConnectToArrInspectorAsync` która wywołuje jest już uruchomiona na komputerze:

* Jeśli używasz integracji Unity, może zostać uruchomiony automatycznie dla Ciebie.
* W przeciwnym razie plik zostanie odnalezieny w *folderach\\użytkownika LocalAppData\\\\[your_app] TEMP AC\\*.

Jeśli aplikacja jest uruchomiona na HoloLens:

1. Dostęp do urządzenia HoloLens za pomocą [portalu urządzeń systemu Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Przejdź do *Eksploratora plików > systemowej*.
1. Przejdź do *folderów\\użytkownika\\LocalAppData\\[your_app] Temp ac\\*.
1. Zapisz *StartArrInspector.html* na komputerze.
1. Otwórz *plik StartArrInspector.html,* aby załadować arrInspector sesji.

## <a name="the-performance-panel"></a>Panel Wydajność

![Panel wydajności](./media/performance-panel.png)

Ten panel pokazuje wykresy wszystkich wartości wydajności dla klatki udostępniane przez serwer. Wartości obejmują obecnie czas klatek, fps, użycie procesora i pamięci, statystyki pamięci, takie jak ogólne użycie pamięci RAM, liczba obiektów itp.

Aby zwizualizować jeden z tych parametrów, kliknij przycisk **Dodaj nowy** i wybierz jedną z dostępnych wartości wyświetlanych w oknie dialogowym. Ta akcja dodaje nowy wykres przewijania do panelu, śledząc wartości w czasie rzeczywistym. Po prawej stronie widać *minimalną,* *maksymalną* i *bieżącą* wartość.

Wykres można przesuwać, przeciągając jego zawartość za pomocą myszy, jednak przesuwanie w poziomie jest możliwe tylko wtedy, gdy ArrInspector jest w stanie wstrzymania.

Przytrzymanie ctrl podczas przeciągania, pozwala na powiększanie. Powiększenie poziome można również kontrolować za pomocą suwaka u dołu.

Zakres pionowy jest domyślnie obliczany na podstawie aktualnie wyświetlanych wartości, a wartości min i max są wyświetlane w polach tekstowych po prawej stronie. Gdy wartości są ustawiane ręcznie, wpisując je bezpośrednio w polu tekstowym lub przesuwając/powiększając, wykres użyje tych wartości. Aby przywrócić automatyczne ramki pionowe, kliknij ikonę w prawym górnym rogu.

![zakres pionowy](./media/vertical-range.png)

## <a name="the-log-panel"></a>Panel Dziennik

![Panel dziennika](./media/log-panel.png)

Panel dziennika zawiera listę komunikatów dziennika generowanych po stronie serwera. Po połączeniu pojawi się do 200 poprzednich komunikatów dziennika i będzie drukować nowe, gdy się pojawią.

Listę można filtrować na podstawie `[Error/Warning/Info/Debug]` typu dziennika za pomocą przycisków u góry.
![Przyciski filtru dziennika](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Panel Przechwytywanie danych czasowych

![Przechwytywanie danych chronometrażu](./media/timing-data-capture.png)

Ten panel służy do przechwytywania informacji o czasie z serwera i pobierania ich. Plik używa [formatu Chrome Tracing JSON](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Aby sprawdzić dane, otwórz Chrome `Chrome://tracing` w adresie URL i przeciągnij i upuść pobrany plik na stronę. Dane chronometrażu są stale zbierane w buforze pierścieniowym o stałym rozmiarze. Po wypisywanie, przechwytywanie zawiera tylko informacje o bezpośredniej przeszłości, co oznacza kilka sekund do kilku minut.

## <a name="the-scene-inspection-panel"></a>Panel kontroli scen

![Panel kontroli scen](./media/scene-inspection-panel.png)

Ten panel pokazuje strukturę renderowanego sceny. Hierarchia obiektów znajduje się po lewej stronie, zawartość zaznaczonego obiektu znajduje się po prawej stronie. Panel jest tylko do odczytu i jest aktualizowany w czasie rzeczywistym.

## <a name="the-vm-debug-information-panel"></a>Panel informacji debugowania maszyny Wirtualnej

![Panel informacyjny debugowania maszyny Wirtualnej](./media/state-debugger-panel.png)

Ten panel oferuje pewne funkcje debugowania.

### <a name="restart-service"></a>Ponownie uruchom usługę

Przycisk **Uruchom ponownie usługę** uruchamia środowisko uruchomieniowe na maszynie wirtualnej, z którą jest połączony arrInspector. Każdy dołączony klient zostanie rozłączony, a strona arrInspector musi zostać ponownie załadowana, aby połączyć się z uruchomioną ponownie usługą.

### <a name="collect-debug-information"></a>Zbieranie informacji o debugowaniu

Przycisk **Zbieraj informacje debugowania dla maszyny Wirtualnej** otwiera okno dialogowe, które umożliwia wyzwolenie wystąpienia ARR w celu zbierania informacji debugowania na maszynie wirtualnej:

![Okno dialogowe informacji debugowania maszyny Wirtualnej](./media/state-debugger-dialog.png)

Informacje debugowania pomaga zespołowi renderowania zdalnego platformy Azure analizować wszelkie problemy występujące w uruchomionym wystąpieniu ARR. Okno dialogowe zawiera pole tekstowe, aby podać dodatkowe szczegóły, na przykład kroki, aby odtworzyć problem.

Po kliknięciu przycisku **Rozpocznij zbieranie** okno dialogowe zostanie zamknięte i rozpocznie się proces zbierania. Zbieranie informacji na maszynie wirtualnej może potrwać kilka minut.

![Trwa zbieranie informacji o debugowaniu maszyn wirtualnych](./media/state-debugger-panel-in-progress.png)

Po zakończeniu kolekcji otrzymasz powiadomienie w oknie ArrInspector. To powiadomienie zawiera identyfikator, który identyfikuje tej konkretnej kolekcji. Pamiętaj, aby zapisać ten identyfikator, aby przekazać go do zespołu renderowania zdalnego platformy Azure.

![Sukces zbierania informacji debugowania maszyny Wirtualnej](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Nie można pobrać ani w inny sposób uzyskać informacji debugowania maszyny Wirtualnej. Tylko zespół renderowania zdalnego platformy Azure ma dostęp do zebranych danych. Musisz skontaktować się z nami i wysłać identyfikator kolekcji, abyśmy mogli zbadać problem, który widzisz.

## <a name="pause-mode"></a>Tryb pauzy

W prawym górnym rogu przełącznik umożliwia wstrzymanie aktualizacji paneli na żywo. Ten tryb może być przydatne do dokładnego sprawdzenia określonego stanu.

![Tryb pauzy](./media/pause-mode.png)

Po ponownym włączeniu aktualizacji na żywo wszystkie panele są resetowane.

## <a name="host-configuration"></a>Konfiguracja hosta

Domyślnie narzędzie łączy się z serwerem ARR, który jest uruchomiony na tym samym hoście obsługującym ArrInspector. Można jednak skonfigurować go do inspekcji innego serwera, przy założeniu, że jest uruchomiony wystąpienie ARR z portem narzędziowym otwarte.

Aby to zrobić, przejdź do menu głównego po lewej stronie paska nagłówka i wybierz *opcję Konfiguracja hosta*. Kliknij **pozycję Dodaj nowego hosta**i wprowadź nazwę i nazwę hosta. W przypadku *nazwy hosta* należy używać `.mixedreality.azure.com`tylko nazwy `http://` hosta kończącej się na , nie obejmują lub portu.

![Konfiguracja hosta](./media/host-configuration.png)

Aby szybko przełączyć się z jednego hosta na drugi, użyj listy rozwijanej w prawym górnym rogu.

![Kombinacja gospodarzy](./media/host-switch-combo.png)

Lista hostów jest przechowywana w lokalnej pamięci przeglądarki, więc zostanie zachowana podczas ponownego otwierania tej samej przeglądarki.
