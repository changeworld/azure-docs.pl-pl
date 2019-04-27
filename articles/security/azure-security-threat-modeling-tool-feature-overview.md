---
title: Zagrożeń firmy Microsoft, modelowanie narzędzi — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o wszystkich funkcji dostępnych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 601f3bf05388406c8f96a7351f7fb3aa4de2650a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588798"
---
# <a name="threat-modeling-tool-feature-overview"></a>Omówienie funkcji narzędzie do modelowania zagrożeń

Narzędzie do modelowania zagrożeń może pomóc przy użyciu usługi threat modelowania potrzeb. Podstawowe wprowadzenie do narzędzia, zobacz [wprowadzenie do narzędzia do modelowania zagrożeń](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Narzędzie do modelowania zagrożeń jest często aktualizowany, dlatego należy sprawdzić w przewodniku często, aby sprawdzić naszych najnowszych funkcji i ulepszeń.

Aby otworzyć stronę pusty, wybierz **Tworzenie modelu**.

![Pusta strona](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Aby zobaczyć funkcje, które są obecnie dostępne w narzędziu, użyj model zagrożeń, utworzone przez nasz zespół w [wprowadzenie](./azure-security-threat-modeling-tool-getting-started.md) przykład.

![Model zagrożeń podstawowe](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Nawigacja

Zanim omówimy wbudowane funkcje, Omówmy główne składniki w narzędziu.

### <a name="menu-items"></a>Elementy menu

Proces jest podobny do innych produktów firmy Microsoft. Omówmy elementy menu najwyższego poziomu.

![Elementy menu](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Plik** | <ul><li>Otwórz, Zapisz i zamknij pliki</li><li>Zaloguj się i wylogowania z kont w usłudze OneDrive.</li><li>Udostępnij linki (wyświetlanie i edytowanie).</li><li>Wyświetl informacje o pliku.</li><li>Zastosuj nowy szablon do istniejących modeli.</li></ul> |
| **Edytowanie** | Cofnij i wykonaj ponownie czynności, jak kopiowanie, wklejanie i usunąć. |
| **Widok** | <ul><li>Przełączanie między **analizy** i **projektowania** widoków.</li><li>Zamknięte okna (na przykład wzorników właściwości elementu i wiadomości).</li><li>Resetuj układ do domyślnych ustawień.</li></ul> |
| **Diagram** | Dodawanie i usuwanie diagramów i przejść do karty diagramów. |
| **Raporty** | Tworzenie raportów HTML na udostępnianie innym osobom. |
| **Pomoc** | Znajdź przewodniki ułatwiają korzystanie z narzędzia. |

Symbole są skróty do menu najwyższego poziomu:

| Symbol                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Otwórz** | Zostanie otwarty nowy plik. |
| **Zapisz** | Zapisuje bieżący plik. |
| **Projekt** | Otwiera **projektowania** widok, w którym możesz tworzyć modele. |
| **Analiza** | Pokazuje generowane zagrożenia i ich właściwości. |
| **Dodaj diagram** | Dodaje nowy diagram (podobne do nowych kartach w programie Excel). |
| **Usuń z diagramu** | Usuwa bieżący diagram. |
| **Kopiuj/Wytnij/Wklej** | Kopiuje, skraca i wkleja elementów. |
| **Cofnij/Ponów.** | Cofa i wykonuje ponownie akcje. |
| **Powiększyć / pomniejszyć** | Powiększa do i z diagramem lepszego widoku. |
| **Opinia** | Zostanie otwarty na Forum MSDN. |

### <a name="canvas"></a>Kanwy

Obszar roboczy to miejsce, gdzie przeciąganie i upuszczanie elementów. Przeciąganie i upuszczanie jest najszybszym i najbardziej wydajny sposób tworzyć modele. Możesz również kliknąć prawym przyciskiem myszy i wybierz elementy z menu, aby dodać ogólne wersje elementów, jak pokazano:

#### <a name="drop-the-stencil-on-the-canvas"></a>Upuść wzornika w obszarze roboczym

![Listy kanwy](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Wybierz wzornika

![Właściwości elementu](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Wzorników

Oparty na szablonie, którą wybierzesz, możesz znaleźć wzorników dostępne do użycia. Jeśli nie możesz znaleźć odpowiednie elementy, użyj innego szablonu. Lub można zmodyfikować szablon do własnych potrzeb. Ogólnie rzecz biorąc można znaleźć kombinacji kategorie, takie jak te:

| Nazwa wzornika                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikacje, dodatki plug-in do przeglądarki, wątki, maszyn wirtualnych |
| **Zewnętrzny** | Dostawcy uwierzytelniania, przeglądarek, użytkownicy, aplikacje sieci web |
| **Magazyn danych** | Pamięć podręczna, magazynu, pliki konfiguracyjne, bazy danych, rejestr |
| **Przepływ danych** | Plik binarny ALPC HTTP, HTTPS/protokołów TLS/SSL, IOCTL, IPSec, nazwany potok, RPC/DCOM, SMB i protokołu UDP |
| **Obramowanie linii/granicy zaufania** | Sieciach firmowych, internet, maszyna, piaskownicy, tryb jądra/użytkownika |

### <a name="notesmessages"></a>Informacje o/wiadomości

| Składnik                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Komunikaty** | Logika wewnętrznego narzędzia, która generuje alerty dla użytkownika przy każdym wystąpieniu błędu, takie jak Brak przepływów danych między elementami. |
| **Uwagi** | Ręczne uwagi są dodawane do pliku przez zespoły inżynierów w całym projekcie i proces przeglądu. |

### <a name="element-properties"></a>Właściwości elementu

Właściwości elementu zależą od elementów, którą wybierzesz. Poza granicami zaufania wszystkie inne elementy zawiera trzy opcje ogólne:

| Właściwości elementu                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Nazwa** | Przydatne nazewnictwa procesów, magazyny, obiektów interakcji i przepływów, dzięki czemu są one łatwe rozpoznane. |
| **Poza zakresem** | Jeśli zaznaczone, element jest wykluczana z macierzy threat-generation (niezalecane). |
| **Przyczyna poza zakresem** | Wybrano pole uzasadnienie, aby powiadomić użytkowników, dlatego poza zakres tej asysty. |

Właściwości zostały zmienione w ramach każdej kategorii elementu. Zaznacz każdy element, aby sprawdzić dostępne opcje. Alternatywnie możesz otworzyć szablon, aby dowiedzieć się więcej. Omówmy teraz funkcje.

## <a name="welcome-screen"></a>Ekran powitalny

Po otwarciu aplikacji, zobacz **powitalnej** ekranu.

### <a name="open-a-model"></a>Otwieranie modelu

Umieść kursor nad **modelu otwartego** Aby wyświetlić dostępne dwie opcje: **Otwórz z tego komputera** i **Otwórz w usłudze OneDrive**. Pierwszy otwiera opcji **Otwórz plik** ekranu. Druga opcja przeprowadzi Cię przez proces logowania do usługi OneDrive. Po pomyślnym uwierzytelnieniu można wybrać pliki i foldery.

![Otwieranie modelu](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Otwórz z komputera lub usługi OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Opinię, sugestie i zagadnienia

Po wybraniu **opinii, sugestii, a także problemach**, przejdź na forum MSDN dla narzędzi SDL. Może odczytywać, co mówią inni o tym narzędziu, w tym rozwiązania i nowe pomysły.

![Opinia](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Widok projektu

Po otwarciu lub utworzyć nowy model **projektowania** wyświetlić zostanie otwarta.

### <a name="add-elements"></a>Dodawanie elementów

Możesz dodawać elementy na siatce na dwa sposoby:

- **Przeciąganie i upuszczanie**: Przeciągnij żądanego elementu siatki. Następnie należy użyć właściwości elementu dostarczenie dodatkowych informacji.
- **Kliknij prawym przyciskiem myszy**: Kliknij prawym przyciskiem myszy w dowolnym miejscu w siatce, a następnie wybierz elementy z menu rozwijanego. Na ekranie pojawia się ogólny reprezentacja elementu, którą wybierzesz.

### <a name="connect-elements"></a>Połącz elementy

Można połączyć elementów na dwa sposoby:

- **Przeciąganie i upuszczanie**: Przeciągnij na siatkę żądanego przepływu danych, a następnie połączyć oba końce do odpowiednich elementów.
- **Kliknij pozycję + Shift**: Kliknij pozycję pierwszego elementu (wysyłanie danych), naciśnij i przytrzymaj klawisz Shift, a następnie wybierz drugi element (odbierania danych). Kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Connect**. Jeśli używasz przepływ danych dwukierunkowej, kolejność nie jest ważne.

### <a name="properties"></a>Właściwości

 Aby wyświetlić właściwości, które mogą być modyfikowane na wzorników, wybierz opcję wzornik i informacje o wypełnienie odpowiednio. W poniższym przykładzie pokazano przed i po nim **bazy danych** przeciągnięciu na niego przeciągnięte wzornika:

#### <a name="before"></a>Przed

![Przed](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Komunikaty

Jeśli utworzysz model zagrożeń i zapomnij łączenie przepływów danych z elementów, otrzymasz powiadomienie. Można zignorować ten komunikat lub zostaną wyświetlone instrukcje pozwalające rozwiązać problem. 

![Komunikaty](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Uwagi

Aby dodać komentarze do diagramu, przełączyć się z **wiadomości** kartę **informacje o** kartę.

## <a name="analysis-view"></a>Wyświetlanie analiz

Po skompilowaniu diagramu wybierz **analizy** symbolu (Lupa) na pasku narzędzi skróty, aby przełączyć się do **analizy** widoku.

![Wyświetlanie analiz](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Wyboru wygenerowanych zagrożeń

Po wybraniu za zagrożenie, można użyć trzech różnych funkcji:

| Cecha                               | Informacje      |
| --------------------------------------- | ------------ |
| **Wskaźnik do odczytu** | <p>Zagrożenia zostanie oznaczona jako odczytu, która pomaga Ci śledzić elementy, które możesz przejrzeć.</p><p>![Odczyt/nieprzeczytane wskaźnika](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Fokus interakcji** | <p>Interakcji na diagramie, który należy do związany z zagrożeniem zostanie wyróżniona.</p><p>![Fokus interakcji](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Właściwości przed zagrożeniami** | <p>Dodatkowe informacje na temat zagrożenia, który pojawia się w **właściwości zagrożeń** okna.</p><p>![Właściwości przed zagrożeniami](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Zmiana priorytetu

Można zmienić poziom priorytetu każde zagrożenie wygenerowany. Różne kolory ułatwiają identyfikowanie wysokiej, średniej i zagrożenia o niskim priorytecie.

![Zmiana priorytetu](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Pola można edytować właściwości przed zagrożeniami

Jak widać na poprzedniej ilustracji, możesz zmienić dane generowane przez narzędzie. Możesz także dodawać informacje w określonych polach, takich jak uzasadnienie. Te pola są generowane przez szablon. Jeśli potrzebujesz więcej informacji na każde zagrożenie, można wprowadzić modyfikacje.

![Właściwości przed zagrożeniami](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Raporty

Po zakończeniu zmieniających się priorytetów i aktualizowania stanu każdego wygenerowane przed zagrożeniami, użytkownik może zapisać plik i/lub wydrukuj raport. Przejdź do **raportu** > **Utwórz pełny raport**. Nazywanie raportu i powinny zostać wyświetlone informacje, które są podobne do poniższej ilustracji:

![Raport](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Kolejne kroki

- Wyślij swoje pytania, komentarze i zagadnień związanych z tmtextsupport@microsoft.com. **[Pobierz](https://aka.ms/threatmodelingtool)**  narzędzie do modelowania zagrożeń na rozpoczęcie pracy.
- Aby współtworzyć szablonu dla społeczności, przejdź do naszego [GitHub](https://github.com/Microsoft/threat-modeling-templates) strony.