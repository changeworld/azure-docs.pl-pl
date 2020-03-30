---
title: Omówienie funkcji narzędzia do modelowania zagrożeń firmy Microsoft — platforma Azure
description: Dowiedz się więcej o wszystkich funkcjach dostępnych w narzędziu do modelowania zagrożeń
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552121"
---
# <a name="threat-modeling-tool-feature-overview"></a>Omówienie funkcji narzędzia do modelowania zagrożeń

Narzędzie do modelowania zagrożeń może pomóc w spełnieniu potrzeb modelowania zagrożeń. Aby uzyskać podstawowe wprowadzenie do narzędzia, zobacz [Wprowadzenie do narzędzia do modelowania zagrożeń](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Narzędzie do modelowania zagrożeń jest często aktualizowane, dlatego często sprawdzaj ten przewodnik, aby zobaczyć nasze najnowsze funkcje i ulepszenia.

Aby otworzyć pustą stronę, wybierz pozycję **Utwórz model**.

![Pusta strona](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Aby wyświetlić funkcje obecnie dostępne w narzędziu, użyj modelu zagrożeń utworzonego przez nasz zespół w przykładzie [Wprowadzenie.](threat-modeling-tool-getting-started.md)

![Podstawowy model zagrożenia](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Nawigacja

Zanim omówimy wbudowane funkcje, przejrzyjmy główne składniki znalezione w narzędziu.

### <a name="menu-items"></a>Elementy menu

Środowisko jest podobne do innych produktów firmy Microsoft. Przejrzyjmy elementy menu najwyższego poziomu.

![Elementy menu](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Plik** | <ul><li>Otwieranie, zapisywanie i zamykanie plików</li><li>Zaloguj się i wyloguj się z kont usługi OneDrive.</li><li>Udostępnianie łączy (wyświetlanie i edytowanie).</li><li>Wyświetlanie informacji o pliku.</li><li>Zastosuj nowy szablon do istniejących modeli.</li></ul> |
| **Edytuj** | Cofanie i ponawianie akcji, a także kopiowanie, wklejanie i usuwanie. |
| **Widok** | <ul><li>Przełączanie między widokami **Analiza** i **Projekt.**</li><li>Otwórz zamknięte okna (na przykład wzorniki, właściwości elementu i komunikaty).</li><li>Zresetuj układ do ustawień domyślnych.</li></ul> |
| **Diagram** | Dodawanie i usuwanie diagramów oraz przechodzenie między kartami diagramów. |
| **Raporty** | Tworzenie raportów HTML w celu udostępniania innym osobom. |
| **Pomoc** | Znajdź przewodniki ułatwiające korzystanie z narzędzia. |

Symbole są skrótami do menu najwyższego poziomu:

| Symbol                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Otwórz** | Otwiera nowy plik. |
| **Zapisz** | Zapisuje bieżący plik. |
| **Projekt** | Otwiera widok **Projekt,** w którym można tworzyć modele. |
| **Analiza** | Pokazuje wygenerowane zagrożenia i ich właściwości. |
| **Dodaj diagram** | Dodaje nowy diagram (podobny do nowych kart w programie Excel). |
| **Usuwanie diagramu** | Usuwa bieżący diagram. |
| **Kopiowanie/wycinanie/wklejanie** | Kopiuje, wycina i wkleja elementy. |
| **Cofanie/ponawianie** | Cofa i ponawia akcje. |
| **Powiększanie/pomniejszanie** | Powiększa i pomniejsza diagram, aby uzyskać lepszy widok. |
| **Opinie** | Otwiera forum MSDN. |

### <a name="canvas"></a>Kanwa

Kanwa to przestrzeń, w której przeciągasz i upuszczasz elementy. Przeciąganie i upuszczanie jest najszybszym i najbardziej efektywnym sposobem tworzenia modeli. Można również kliknąć prawym przyciskiem myszy i wybrać elementy z menu, aby dodać ogólne wersje elementów, jak pokazano na rysunku:

#### <a name="drop-the-stencil-on-the-canvas"></a>Upuść wzornik na płótnie

![Upuszczenie kanwy](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Wybierz wzornik

![Właściwości elementu](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Wzorników

Na podstawie wybranego szablonu można znaleźć wszystkie wzorniki dostępne do użycia. Jeśli nie możesz znaleźć odpowiednich elementów, użyj innego szablonu. Możesz też zmodyfikować szablon, aby dopasować go do swoich potrzeb. Ogólnie rzecz biorąc, można znaleźć kombinację kategorii, takich jak te:

| Nazwa wzornika                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikacje, wtyczki przeglądarki, wątki, maszyny wirtualne |
| **Zewnętrzny interakator** | Dostawcy uwierzytelniania, przeglądarki, użytkownicy, aplikacje internetowe |
| **Magazyn danych** | Pamięć podręczna, przechowywanie, pliki konfiguracyjne, bazy danych, rejestr |
| **Przepływ danych** | Binarny, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, nazwany potok, RPC/DCOM, SMB, UDP |
| **Granica linii zaufania/granicy obramowania** | Sieci firmowe, Internet, maszyna, piaskownica, tryb użytkownika/jądra |

### <a name="notesmessages"></a>Notatki/wiadomości

| Składnik                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Wiadomości** | Wewnętrzna logika narzędzia, która ostrzega użytkowników, gdy występuje błąd, takich jak brak przepływów danych między elementami. |
| **Uwagi** | Notatki ręczne są dodawane do pliku przez zespoły inżynierów w trakcie procesu projektowania i przeglądu. |

### <a name="element-properties"></a>Właściwości elementu

Właściwości elementu różnią się w zależności od wybranych elementów. Oprócz granic zaufania wszystkie inne elementy zawierają trzy ogólne wybory:

| Właściwość elementu                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Nazwa** | Przydatne do nazywania procesów, sklepów, interakcjorów i przepływów, dzięki czemu są one łatwo rozpoznawane. |
| **Poza zakresem** | Jeśli ta opcja jest zaznaczona, element jest wyjęty z macierzy generowania zagrożeń (nie jest zalecane). |
| **Powód braku zakresu** | Pole Uzasadnienie, aby poinformować użytkowników, dlaczego nie ma zakresu został wybrany. |

Właściwości są zmieniane w ramach każdej kategorii elementów. Wybierz każdy element, aby sprawdzić dostępne opcje. Możesz też otworzyć szablon, aby dowiedzieć się więcej. Przejrzyjmy funkcje.

## <a name="welcome-screen"></a>Ekran powitalny

Po otwarciu aplikacji zostanie wyświetlony ekran **powitalny.**

### <a name="open-a-model"></a>Otwieranie modelu

Umieść wskaźnik myszy **na otwórz model,** aby wyświetlić dwie opcje: **Otwórz z tego komputera** i Otwórz z usługi **OneDrive**. Pierwsza opcja otwiera ekran **Otwieranie pliku.** Druga opcja prowadzi przez proces logowania do usługi OneDrive. Po pomyślnym uwierzytelnieniu można wybrać foldery i pliki.

![Otwórz model](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Otwieranie z komputera lub usługi OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Opinie, sugestie i problemy

Po **wybraniu opinii, sugestii i problemów**przejdź do forum MSDN dla narzędzi SDL. Możesz przeczytać, co inni mówią o narzędziu, w tym obejścia i nowe pomysły.

![Opinia](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Widok projektu

Po otwarciu lub utworzeniu nowego modelu zostanie otwarty widok **Projekt.**

### <a name="add-elements"></a>Dodawanie elementów

Elementy na siatce można dodawać na dwa sposoby:

- **Przeciąganie i upuszczanie:** Przeciągnij żądany element do siatki. Następnie użyj właściwości elementu, aby zapewnić dodatkowe informacje.
- **Kliknij prawym przyciskiem myszy**: Kliknij prawym przyciskiem myszy dowolne miejsce na siatce i wybierz elementy z menu rozwijanego. Na ekranie pojawi się ogólna reprezentacja wybranego elementu.

### <a name="connect-elements"></a>Łączenie elementów

Elementy można łączyć na dwa sposoby:

- **Przeciąganie i upuszczanie:** Przeciągnij żądany przepływ danych do siatki i połącz oba końce z odpowiednimi elementami.
- **Kliknij + Shift**: Kliknij pierwszy element (wysyłanie danych), naciśnij i przytrzymaj klawisz Shift, a następnie wybierz drugi element (odbieranie danych). Kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Połącz**. Jeśli używasz dwukierunkowego przepływu danych, kolejność nie jest tak ważna.

### <a name="properties"></a>Właściwości

 Aby wyświetlić właściwości, które mogą być modyfikowane we wzornikach, wybierz wzornik i informacje wypełnia odpowiednio. Poniższy przykład pokazuje przed i po wzornik **bazy danych** jest przeciągany na diagramie:

#### <a name="before"></a>Przed

![Przed](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Komunikaty

Jeśli utworzysz model zagrożeń i zapomnisz połączyć przepływy danych z elementami, otrzymasz powiadomienie. Możesz zignorować wiadomość lub postępować zgodnie z instrukcjami, aby rozwiązać problem. 

![Komunikaty](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Uwagi

Aby dodać notatki do diagramu, przełącz się z karty **Wiadomości** na kartę **Notatki.**

## <a name="analysis-view"></a>Widok analizy

Po utworzeniu diagramu wybierz symbol **Analiza** (lupę) na pasku narzędzi skróty, aby przełączyć się do widoku **Analiza.**

![Widok analizy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Wygenerowany wybór zagrożenia

Po wybraniu zagrożenia można użyć trzech różnych funkcji:

| Funkcja                               | Informacje      |
| --------------------------------------- | ------------ |
| **Wskaźnik odczytu** | <p>Zagrożenie jest oznaczone jako przeczytane, co ułatwia śledzenie recenzowanych elementów.</p><p>![Wskaźnik odczytu/nieprzeczytane](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Koncentracja na interakcji** | <p>Interakcja na diagramie, który należy do zagrożenia jest wyróżniona.</p><p>![Koncentracja na interakcji](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Właściwości zagrożenia** | <p>Dodatkowe informacje o zagrożeniu są wyświetlane w oknie **Właściwości zagrożenia.**</p><p>![Właściwości zagrożenia](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Zmiana priorytetu

Można zmienić poziom priorytetu każdego wygenerowanego zagrożenia. Różne kolory ułatwiają identyfikowanie zagrożeń o wysokim, średnim i niskim priorytecie.

![Zmiana priorytetu](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Pola edytowalne właściwości zagrożenia

Jak widać na poprzednim obrazie, można zmienić informacje generowane przez narzędzie. Można również dodać informacje do niektórych pól, takich jak uzasadnienie. Te pola są generowane przez szablon. Jeśli potrzebujesz więcej informacji dla każdego zagrożenia, możesz wprowadzić zmiany.

![Właściwości zagrożenia](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Raporty

Po zakończeniu zmiany priorytetów i aktualizacji stanu każdego wygenerowanego zagrożenia można zapisać plik i/lub wydrukować raport. Przejdź do **raportu** > **Utwórz pełny raport**. Nazwij raport, a powinieneś zobaczyć coś podobnego do następującego obrazu:

![Raport](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Następne kroki

- Wyślij swoje pytania, uwagi tmtextsupport@microsoft.comi wątpliwości do . **[Pobierz](https://aka.ms/threatmodelingtool)** narzędzie do modelowania zagrożeń, aby rozpocząć.
- Aby dodać szablon dla społeczności, przejdź do naszej strony [GitHub.](https://github.com/Microsoft/threat-modeling-templates)