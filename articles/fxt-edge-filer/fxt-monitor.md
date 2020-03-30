---
title: Monitorowanie urządzenia Azure FXT Edge Filer
description: Jak monitorować stan sprzętu pamięci podręcznej hybrydowej pamięci masowej azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254873"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorowanie stanu sprzętu usługi Azure FXT Edge

System hybrydowej pamięci podręcznej magazynu usługi Azure FXT Edge Filer ma wiele świateł stanu wbudowanych w obudowę, aby pomóc administratorom zrozumieć, jak działa sprzęt.

## <a name="system-health-status"></a>Stan kondycji systemu

Aby monitorować operacje pamięci podręcznej na wyższym poziomie, użyj strony **pulpitu nawigacyjnego** panelu sterowania oprogramowania, zgodnie z opisem w [Przewodniku po panelu panelu sterowania panelu sterowania](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Diody LED stanu sprzętu

W tej sekcji opisano różne kontrolki stanu wbudowane w sprzęt Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>Diody LED stanu dysku twardego

![obraz z przodu dysku twardego, poziomy, z etykietami objaśnień 2 (lewy górny róg), 1 (lewy dolny róg) i 3 (prawa strona)](media/fxt-monitor/fxt-drive-callouts.png)

Każdy nośnik dysku ma dwie diody LED stanu: wskaźnik aktywności (1) i wskaźnik stanu (2). 

* Dioda LED aktywności (1) świeci, gdy dysk jest w użyciu.  
* Dioda LED stanu (2) wskazuje stan napędu za pomocą kodów w poniższej tabeli.

| Stan dysku STAN LED              | Znaczenie  |
|-------------------------------------|----------------------------------------------------------|
| Miga na zielono dwa razy na sekundę      | Identyfikacja dysku *lub* <br> Przygotowanie dysku do usunięcia  |
| Wył.(nieoświetlone)                         | System nie zakończył uruchamiania *lub* <br>Dysk jest gotowy do usunięcia |
| Miga na zielono, bursztynowo i wyłącza       | Przewiduje się awarię dysku   |
| Miga na pomarańczowo cztery razy na sekundę | Dysk nie powiódł się   |
| Solidna zieleń                         | Dysk jest online |

Prawa strona napędu (3) jest oznaczona pojemność dysku i inne informacje.

Numery dysków są drukowane na przestrzeni między dyskami. W usłudze Azure FXT Edge Filer dysk 0 jest lewym górnym dyskiem, a dysk 1 znajduje się bezpośrednio pod nim. Numerowanie jest kontynuowane w tym wzorzec. 

![zdjęcie jednej wnęki na dysk twardy w obudowie FXT, przedstawiające numery dysków i etykiety pojemności](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Lewy panel sterowania

Lewy przedni panel sterowania ma różne wskaźniki LED stanu (1) i duży oświetlony wskaźnik kondycji systemu (2). 

![lewego panelu stanu, z 1 wskaźnikami stanu etykietowania po lewej stronie i 2 etykietami dużej kontrolki kondycji systemu po prawej stronie](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Wskaźniki stanu panelu sterowania 

Wskaźniki stanu po lewej stronie pokazują stałe pomarańczowe światło, jeśli wystąpił błąd w tym systemie. W poniższej tabeli opisano możliwe przyczyny i rozwiązania błędów. 

Jeśli po wypróbowaniu tych rozwiązań nadal wystąpił błąd, [skontaktuj się z pomocą techniczną,](fxt-support-ticket.md) aby uzyskać pomoc. 

| Ikona | Opis | Błąd | Możliwe rozwiązania |
|----------------|---------------|--------------------|----------------------|
| ![ikona dysku](media/fxt-monitor/fxt-hd-icon.jpg) | Stan dysku | Błąd dysku | Sprawdź w dzienniku zdarzeń systemowych, czy dysk ma błąd, lub <br>Uruchom odpowiedni test diagnostyczny online; ponownego uruchomienia systemu i uruchomienia wbudowanej diagnostyki (ePSA), lub <br>Jeśli dyski są skonfigurowane w macierzy RAID, uruchom ponownie system i wprowadź program narzędziowy konfiguracji karty hosta |
|![ikona temperatury](media/fxt-monitor/fxt-temp-icon.jpg) | Stan temperatury | Błąd termiczny - na przykład wentylator uległ awarii lub temperatura otoczenia jest poza zakresem | Sprawdź następujące adresowalne warunki: <br>Brakuje wentylatora chłodzącego lub nie <br>Osłona systemu, osłona powietrza, pusty moduł pamięci lub tylny wspornik wypełniacza jest usuwany <br>Temperatura otoczenia jest zbyt wysoka <br>Zewnętrzny przepływ powietrza jest zasłaniany |
|![ikona energii elektrycznej](media/fxt-monitor/fxt-electric-icon.jpg) | Stan elektryczny | Błąd elektryczny - na przykład napięcie poza zasięgiem, nieudany zasilacz lub nieudany regulator napięcia |  Sprawdź dziennik zdarzeń systemu lub komunikaty systemowe dla określonego problemu. Jeśli występuje problem z zasilaczem, sprawdź diodę LED stanu zasilacza i w razie potrzeby ponownie wsażu zasilacza. | 
|![ikona pamięci](media/fxt-monitor/fxt-memory-icon.jpg) | Stan pamięci | Błąd pamięci | Sprawdź dziennik zdarzeń systemowych lub komunikaty systemowe pod kątem lokalizacji pamięci, która uległa awarii; ponownie wsadzić moduł pamięci. |
|![Ikona pcie](media/fxt-monitor/fxt-pcie-icon.jpg) | Stan PCIe | Błąd karty PCIe | Uruchom ponownie system; aktualizacja sterowników kart PCIe; ponownej instalacji karty |


### <a name="system-health-status-indicator"></a>Wskaźnik stanu kondycji systemu

Duży podświetlany przycisk po prawej stronie lewego panelu sterowania wskazuje ogólny stan systemu, a także jest używany jako światło lokalizatora jednostek w trybie identyfikatora systemu.

Naciśnij przycisk kondycja i identyfikator systemu, aby przełączać się między trybem identyfikatora systemu a trybem kondycji systemu.

|Stan kondycji systemu | Warunek |
|-------------------------------------------|-----------------------------------------------|
| Stały niebieski | Normalna praca: system jest włączony, działa normalnie, a tryb identyfikatora systemu nie jest aktywny. <br/>Jeśli chcesz przełączyć się na tryb identyfikatora systemu, naciśnij przycisk kondycja i identyfikator systemu. |
| Miga na niebiesko | Tryb identyfikatora systemu jest aktywny. Jeśli chcesz przełączyć się na tryb kondycji systemu, naciśnij przycisk kondycji systemu i identyfikatora systemu. |
| Lity bursztyn | System jest w trybie awaryjnym. Jeśli problem będzie się powtarzał, [skontaktuj się z działem obsługi klienta i pomocy technicznej firmy Microsoft](fxt-support-ticket.md). |
| Miga na pomarańczowo | Usterka systemu. Sprawdź w dzienniku zdarzeń systemowych określone komunikaty o błędach. Aby uzyskać informacje o zdarzeniach i komunikatach o błędach generowanych przez oprogramowanie układowe systemu i agentów monitorujących składniki systemu, zobacz stronę wyszukiwania kodu błędu w qrl.dell.com. |


