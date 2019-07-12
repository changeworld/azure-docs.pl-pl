---
title: Monitor filtr Edge FXT platformy Azure
description: Jak monitorować stan sprzętu pod kątem pamięci podręcznej magazynowania hybrydowego filtr Edge FXT platformy Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827457"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorowanie stanu sprzętu filtr Edge FXT platformy Azure

Filtr Edge FXT Azure hybrydowego magazynu pamięci podręcznej system ma wiele światła stan wbudowanej w obudowie ułatwia administratorom zrozumieć, jak działa.

## <a name="system-health-status"></a>Stan kondycji systemu

Aby monitorować operacji pamięci podręcznej na wyższym poziomie, użyj panelu sterowania na oprogramowanie **pulpit nawigacyjny** stronie zgodnie z opisem w [przewodnik pulpit nawigacyjny Panelu sterowania](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Stan sprzętu diody LED

W tej sekcji opisano różne światła stan wbudowanej w sprzęt filtr Edge FXT platformy Azure.

### <a name="hard-drive-status-leds"></a>Stan dysku twardego diody LED

![obraz dysku twardego z przodu poziomo, objaśnienia etykiety 2 (lewym górnym rogu), 1 (lewym dolnym rogu) i 3 (prawa strona)](media/fxt-monitor/fxt-drive-callouts.png)

Operatora każdego dysku ma dwie stan diody LED: wskaźnik aktywności (1) i (2) wskaźnik stanu. 

* Działania DOPROWADZIŁY światła (1), gdy dysk jest w użyciu.  
* Stan diody LED (2) wskazuje stan dysku za pomocą kodów w poniższej tabeli.

| Stan LED stan dysku              | Znaczenie  |
|-------------------------------------|----------------------------------------------------------|
| Błysków zielony dwa razy na sekundę      | Identyfikowanie dysku *lub* <br> Przygotowywanie dysku do usunięcia  |
| Wyłączone (Nieoświetlona)                         | System nie zakończył uruchamiania *lub* <br>Dysk jest gotowe do usunięcia |
| Błysków zielony, żółtą i wyłączone       | Przewiduje się awarię dysku   |
| Żółtą błysków cztery razy na sekundę | Dysk nie powiodło się   |
| Zielony                         | Dysk jest w trybie online |

Po prawej stronie dysku (3) jest oznaczony miejsca na dysku i inne informacje.

Numery dysków są drukowane na miejsce między dysków. W filtr Edge FXT platformy Azure dysk 0 jest u góry z lewej stacji, a dysk 1 jest bezpośrednio poniżej. Numerowanie jest kontynuowane w tym wzorze. 

![zdjęcie jeden dysk twardy wnękami w obudowie FXT wyświetlane etykiety liczb i pojemność dysku](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Panel sterowania po lewej stronie

Po lewej stronie apletu frontonu ma różne wskaźniki stanu LED (1) i wskaźnik kondycji integratorów podświetlany (2). 

![panel stanu po lewej stronie, z 1 etykietowania wskaźniki stanu z lewej strony oraz 2 etykietowania wskaźnika kondycji integratorów światła po prawej stronie](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Wskaźniki stanu Panelu sterowania 

Wskaźniki stanu po lewej stronie pokazują solid żółte światła, jeśli istnieje błąd w tym systemie. W poniższej tabeli opisano możliwe przyczyny i potencjalne rozwiązania błędów. 

Jeśli błąd nadal występuje po wykonaniu tych rozwiązań [się z pomocą techniczną](fxt-support-ticket.md) Aby uzyskać pomoc. 

| Ikona | Opis | Błąd | Możliwe rozwiązania |
|----------------|---------------|--------------------|----------------------|
| ![ikona dysku](media/fxt-monitor/fxt-hd-icon.jpg) | Stan dysku | Błąd stacji | Sprawdź dziennik zdarzeń systemu, aby sprawdzić, czy dysk jest błąd, lub <br>Uruchom test odpowiednie diagnostyki online; Uruchom ponownie system i uruchom diagnostykę embedded (ePSA), lub <br>Jeśli dyski są konfigurowane w macierzy RAID, uruchom ponownie system i wprowadź programu narzędziowego konfiguracji adaptera hosta |
|![Ikona temperatury](media/fxt-monitor/fxt-temp-icon.jpg) | Stan temperatury | Błąd termiczny — na przykład kibicem nie powiodło się lub temperatury otoczenia jest poza zakresem | Sprawdź, czy mogą być adresowane następujące warunki: <br>Wentylator chłodzenia nie istnieje lub nie powiodło się <br>System cover air shroud moduł pamięci puste lub Wstecz nawiasu wypełniacza zostanie usunięty <br>Temperatury otoczenia jest zbyt wysoka <br>Zewnętrzne powietrza jest zablokowane |
|![Ikona energii elektrycznej](media/fxt-monitor/fxt-electric-icon.jpg) | Stan elektrycznych | Błąd elektrycznych — na przykład napięcia poza zakresem, nie zasilania lub regulatora napięcia nie powiodło się |  Sprawdź dziennik zdarzeń systemowych lub komunikatów systemowych dla konkretnego problemu. W przypadku problemu zasilania, sprawdź stan zasilania LED i wyjmowania zasilania, jeśli to konieczne. | 
|![Ikona pamięci](media/fxt-monitor/fxt-memory-icon.jpg) | Stan pamięci | Błąd pamięci | Sprawdź dziennik zdarzeń systemowych lub komunikatów systemowych dla lokalizacji pamięci nie powiodło się. Odłącz i ponownie Podłącz moduł pamięci. |
|![Ikona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stan PCIe | Błąd karty PCIe | Ponowne uruchamianie systemu; Zaktualizuj sterowniki karty PCIe; Zainstaluj ponownie karty |


### <a name="system-health-status-indicator"></a>Wskaźnik stanu kondycji systemu

Duży przycisk podświetlone w prawym panelu sterowania po lewej stronie wskazuje ogólny stan systemu i jest używana jako światła lokalizatora jednostki w trybie Identyfikatora systemu.

Naciśnij klawisz kondycji systemu i identyfikator przycisk, aby przełączyć między trybem identyfikator systemu a trybem kondycji systemu.

|Stan kondycji systemu | Warunek |
|-------------------------------------------|-----------------------------------------------|
| Stałe niebieski | Normalne działanie: system jest włączony, działają normalnie, i nie będzie aktywny tryb Identyfikatora systemu. <br/>Naciśnij przycisk identyfikator i kondycji systemu, jeśli chcesz się przełączyć do trybu Identyfikatora systemu. |
| Migająca dioda niebieski | Tryb identyfikator systemu jest aktywny. Naciśnij klawisz kondycji systemu i przycisk Identyfikator systemu, jeśli chcesz się przełączyć do trybu kondycji systemu. |
| Stałe żółtą | System jest w trybie awaryjnym. Jeśli problem będzie się powtarzać, [skontaktuj się z działem obsługi klienta firmy Microsoft i pomocy technicznej](fxt-support-ticket.md). |
| Migająca dioda żółtą | Błąd systemowy. Sprawdź dziennik zdarzeń systemu, aby uzyskać określone komunikaty o błędach. Dla informacji na temat zdarzeń i komunikatów o błędach generowane przez system oprogramowania układowego i agentów, które monitorują składników systemu zobacz stronę wyszukiwania kodu błędu w qrl.dell.com. |


