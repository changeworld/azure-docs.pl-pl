---
title: Microsoft Threat modelowania narzędzie - Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat wszystkich funkcji dostępnych w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2017
ms.locfileid: "26754749"
---
# <a name="threat-modeling-tool-feature-overview"></a>Omówienie funkcji narzędzia do modelowania zagrożeń

Narzędzie modelowania zagrożeń może pomóc z zagrożeniem, Twoje potrzeby modelowania. Podstawowe wprowadzenie do narzędzia, zobacz [Rozpoczynanie pracy z narzędziem modelowania zagrożeń](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Narzędzie modelowania zagrożeń jest często aktualizowana, więc sprawdzaj ten przewodnik często Zobacz nasze najnowsze funkcje i ulepszenia.

Aby otworzyć stronę pusty, wybierz **tworzenia modelu A**.

![Pusta strona](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Aby wyświetlić funkcji aktualnie dostępnych w narzędziu, użyj modelu zagrożeń utworzona przez nasz zespół w [wprowadzenie](./azure-security-threat-modeling-tool-getting-started.md) przykład.

![Podstawowe zagrożenia modelu](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Nawigacja

Zanim omówimy wbudowane funkcje umożliwia przejrzyj główne składniki znaleziono w narzędziu.

### <a name="menu-items"></a>Elementy menu

Proces jest podobny do innych produktów firmy Microsoft. Załóżmy Przejrzyj elementy menu najwyższego poziomu.

![Elementy menu](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Etykieta                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Plik** | <ul><li>Pliki otwarte, Zapisz i zamknij</li><li>Zaloguj się i wylogować się z konta usługi OneDrive.</li><li>Udostępnij linki (wyświetlanie i edytowanie).</li><li>Wyświetl informacje o pliku.</li><li>Zastosuj nowy szablon do istniejących modeli.</li></ul> |
| **Edytuj** | Cofnij i wykonaj ponownie czynności, a także kopiowania, wklejania i Usuń. |
| **Widok** | <ul><li>Przełączanie między **analizy** i **projekt** widoków.</li><li>Otwórz zamkniętego systemu windows (na przykład wzorników, właściwości elementu i wiadomości).</li><li>Układ Resetowanie do ustawień domyślnych.</li></ul> |
| **Diagram** | Dodawanie i usuwanie diagramów i przejść do karty diagramów. |
| **Raporty** | Tworzenie raportów w formacie HTML można udostępniać innym osobom. |
| **Pomoc** | Znajdź przewodniki ułatwiają korzystanie z narzędzia. |

Symbole są skróty do menu najwyższego poziomu:

| Symbol                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Otwórz** | Zostanie otwarty nowy plik. |
| **Zapisz** | Zapisuje bieżący plik. |
| **Projekt** | Otwiera **projekt** widok, w której utworzono modeli. |
| **Analiza** | Pokazuje generowane zagrożeń i ich właściwości. |
| **Dodawanie diagramu** | Dodaje nowy diagram (podobnie jak nowych kartach w programie Excel). |
| **Usuń diagramu** | Usuwa bieżącego diagramu. |
| **Wycinanie/kopiowanie/wklejanie** | Kopiuje Wytnij i wkleja elementów. |
| **Cofnij/ponów** | Cofa i ponowi wykonanie akcji. |
| **Powiększ / pomniejszyć** | Powiększa do i lepszego widoku na diagramie. |
| **Opinia** | Zostanie otwarty na Forum MSDN. |

### <a name="canvas"></a>Kanwy

Obszar roboczy to miejsce, w którym przeciąganie i upuszczanie elementów. Przeciąganie i upuszczanie jest najszybszym i najbardziej efektywny sposób tworzenia modeli. Można również kliknąć prawym przyciskiem myszy i wybierz elementy do menu Dodaj ogólny wersje elementów, jak pokazano:

#### <a name="drop-the-stencil-on-the-canvas"></a>Upuść wzornika w obszarze roboczym

![Upuść kanwy](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Wybierz wzornik

![Właściwości elementu](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Wzorników

Oparte na wybranego szablonu, można znaleźć wzorników dostępne do użycia. Jeśli nie możesz znaleźć prawo elementy, użyj innego szablonu. Lub możesz zmodyfikować szablon odpowiednio do potrzeb. Ogólnie rzecz biorąc można znaleźć kombinację kategorii, takich jak te:

| Nazwa wzornika                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikacje, wtyczek przeglądarki, wątki, maszyny wirtualne |
| **Zewnętrzny** | Dostawców uwierzytelniania, przeglądarek, użytkownicy, aplikacje sieci web |
| **Magazyn danych** | Pamięć podręczna, magazynu, pliki konfiguracji, baz danych i rejestru |
| **Przepływ danych** | Dane binarne ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, nazwany potok, RPC i modelu DCOM, SMB i protokołu UDP |
| **Obramowanie/granic zaufania** | Sieci firmowe, internet, maszyny, piaskownicy, trybu jądra/użytkownika |

### <a name="notesmessages"></a>Uwagi dotyczące/wiadomości

| Składnik                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Komunikaty** | Logiki wewnętrzne narzędzie, które generuje alerty dla użytkownika przy każdym wystąpieniu błędu, takie jak Brak przepływów danych między elementami. |
| **Uwagi** | Uwagi ręczne są dodawane do pliku przez zespoły inżynieryjne w całym projektu i przejrzeć proces. |

### <a name="element-properties"></a>Właściwości elementu

Właściwości elementu zależy od wybranych elementów. Oprócz granice zaufania wszystkie inne elementy zawiera trzy opcje ogólne:

| Właściwości elementu                               | Szczegóły      |
| --------------------------------------- | ------------ |
| **Nazwa** | Przydatne w przypadku nazw procesów, magazyny, obiektów interakcji i przepływów, dzięki czemu są one czytelna. |
| **Poza zakresem** | Jeśli zaznaczone, element są wykonywane poza macierzy generacji zagrożeń (niezalecane). |
| **Przyczyna poza zakresem** | Wybrano pola uzasadnienie, aby powiadomić użytkowników, dlaczego poza zakresem. |

Właściwości zostały zmienione w każdej kategorii elementu. Zaznacz każdy element, aby sprawdzić dostępne opcje. Alternatywnie możesz otworzyć szablon, aby dowiedzieć się więcej. Załóżmy Przejrzyj funkcje.

## <a name="welcome-screen"></a>Ekran powitalny

Po otwarciu aplikacji, zobacz **powitalnej** ekranu.

### <a name="open-a-model"></a>Otwieranie modelu

Umieść kursor nad **Otwórz modelu A** ujawniasz dwie opcje: **otwarty z tego komputera** i **Otwórz OneDrive z**. Pierwszym otwarciu opcja **Otwórz plik** ekranu. Druga opcja przejście przez proces logowania dla usługi OneDrive. Po pomyślnym uwierzytelnieniu można wybierać plików i folderów.

![Otwieranie modelu](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Otwórz z komputera lub usługi OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Opinie, sugestie i problemów

Po wybraniu **opinie i sugestie problemów**, przejdź na forum MSDN dla narzędzi do tworzenia oprogramowania. Możesz przeczytać inne osoby mówią o tym narzędziu, w tym rozwiązania i nowe pomysły.

![Opinia](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Widok projektu

Po otwarciu lub utworzyć nowy model **projekt** wyświetlić zostanie otwarta.

### <a name="add-elements"></a>Dodawanie elementów

Możesz dodawać elementy na siatce na dwa sposoby:

- **Przeciągnij i upuść**: przeciągnij określony element do siatki. Następnie użyj właściwości elementu, aby podać dodatkowe informacje.
- **Kliknij prawym przyciskiem myszy**: kliknij prawym przyciskiem myszy w dowolnym miejscu w siatce, a następnie wybierz elementy z menu rozwijanego. Na ekranie pojawi się ogólnego reprezentację wybranego elementu.

### <a name="connect-elements"></a>Połączyć elementy

Możesz połączyć elementy na dwa sposoby:

- **Przeciągnij i upuść**: przeciągnij żądaną przepływu danych do siatki, a następnie połączyć oba końce do odpowiednich elementów.
- **Kliknij pozycję + Shift**: kliknij pierwszy element (wysyłanie danych), naciśnij i przytrzymaj klawisz Shift, a następnie wybierz drugiego elementu (odbieranie danych). Kliknij prawym przyciskiem myszy i wybierz **Connect**. Jeśli używasz przepływ danych dwukierunkowe, kolejność nie jest równie ważne.

### <a name="properties"></a>Właściwości

 Aby wyświetlić właściwości, które można modyfikować na wzorników, wybierz wzornik i informacje o wypełnienie odpowiednio. W poniższym przykładzie przedstawiono przed i po **bazy danych** wzornika jest przeciągnięto diagramu:

#### <a name="before"></a>Przed

![Przed](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Komunikaty

Po utworzeniu modelu zagrożeń i pamiętaj, aby połączyć elementy przepływów danych, otrzymasz powiadomienie. Można zignorować ten komunikat, lub można postępuj zgodnie z instrukcjami, aby rozwiązać ten problem. 

![Komunikaty](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Uwagi

Aby dodać uwagi do diagramu, przełącz się z **wiadomości** karty, aby **uwagi** kartę.

## <a name="analysis-view"></a>Analityczny

Po utworzeniu diagramu wybierz **analizy** symbolu (Lupa) na pasku narzędzi skróty, aby przełączyć się do **analizy** widoku.

![Analityczny](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Wybór wygenerowanego zagrożeń

Po wybraniu zagrożenie służą trzy różne funkcje:

| Cecha                               | Informacje      |
| --------------------------------------- | ------------ |
| **Wskaźnik do odczytu** | <p>To zagrożenie jest oznaczona jako odczytu, które pomaga śledzić elementy, które należy przejrzeć.</p><p>![Odczyt/nieprzeczytana wskaźnika](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Fokus interakcji** | <p>Interakcja na diagramie, który należy do zagrożenie zostanie wyróżniona.</p><p>![Fokus interakcji](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Właściwości zagrożeń** | <p>Dodatkowe informacje na temat zagrożeń pojawia się w **właściwości zagrożeń** okna.</p><p>![Właściwości zagrożeń](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Priorytet zmian

Można zmienić poziom priorytetu każde zagrożenie wygenerowany. Różne kolory ułatwiają określenie wysokiej, średni i zagrożeń niskiego priorytetu.

![Priorytet zmian](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Pola można edytować właściwości zagrożeń

Jak w poprzednim obrazu można zmienić informacje generowane przez narzędzie. Można również dodać informacje do niektóre pola, takie jak uzasadnienie. Te pola są generowane przez szablon. Aby uzyskać więcej informacji na każde zagrożenie, można wprowadzić modyfikacje.

![Właściwości zagrożeń](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Raporty

Po zakończeniu zmiana priorytetów i zaktualizowanie stanu każde zagrożenie wygenerowanego możesz zapisać plik lub wydrukować raport. Przejdź do **raport** > **utworzyć pełny raport**. Nazwa raportu i powinny zostać wyświetlone informacje podobne do następujących obrazów:

![Raport](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Kolejne kroki

* Aby współtworzyć szablonu dla społeczności, przejdź do naszego [GitHub](https://github.com/Microsoft/threat-modeling-templates) strony. 
* Aby zacząć korzystać z narzędzia, przejdź do [Pobierz](https://aka.ms/tmtpreview) strony.
