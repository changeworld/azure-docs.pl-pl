---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 9b8812b1fca6a72a69f06a6c0278da8ee4d4c852
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841394"
---
Agent usługi Azure File Sync jest aktualizowana w regularnych odstępach czasu, aby dodać nowe funkcje i rozwiązania problemów. Firma Microsoft zaleca się skonfigurowanie Microsoft Update, aby pobrać aktualizacje dla agenta usługi Azure File Sync, ponieważ są one dostępne.

#### <a name="major-vs-minor-agent-versions"></a>Główne a wersji pomocniczej agentów
* Wersje główne agentów często zawierają nowe funkcje i mieć coraz jako pierwsza część numeru wersji. Na przykład: \*2.\*.\*\*
* Wersje agentów pomocnicze są również nazywane "poprawki" i są wydawane częściej niż wersje główne. Zawierają one często poprawki błędów i ulepszenia mniejszy, ale żadne nowe funkcje. Na przykład: \* \*.3.\*\*

#### <a name="upgrade-paths"></a>Ścieżki uaktualnienia
Istnieją cztery zatwierdzone i przetestowane sposobów instalowania aktualizacji agenta usługi Azure File Sync. 
1. **(Preferowane) Skonfiguruj Microsoft Update w celu automatycznego pobierania i instalowania aktualizacji agenta.**  
    Zawsze zalecamy poświęcenie każdej aktualizacji usługi Azure File Sync, aby upewnić się, że masz dostęp do najnowszych poprawek dla agenta programu server. Microsoft Update sprawia, że ten proces jest bezproblemowe, przez automatyczne pobieranie i instalowanie aktualizacji dla Ciebie.
2. **Użyj AfsUpdater.exe do pobrania i zainstalowania aktualizacji agenta.**  
    AfsUpdater.exe znajduje się w katalogu instalacji agenta. Kliknij dwukrotnie plik wykonywalny, aby pobrać i zainstalować aktualizacje agenta. 
3. **Stosowanie poprawek do istniejącego agenta usługi Azure File Sync za pomocą plik poprawki usługi Microsoft Update lub MSP pliku wykonywalnego. Można pobrać najnowszy pakiet aktualizacji usługi Azure File Sync z [katalogu usługi Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Uruchamianie pliku wykonywalnego MSP uaktualni instalacji usługi Azure File Sync za pomocą tej samej metody, które są używane automatycznie przez usługę Microsoft Update w poprzednim ścieżki uaktualnienia. Stosowanie poprawki usługi Microsoft Update przeprowadzi uaktualnienie w miejscu instalacji usługi Azure File Sync.
4. **Pobierz najnowsze Instalatora agenta usługi Azure File Sync z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Aby uaktualnić istniejącą instalację agenta usługi Azure File Sync, odinstaluj starszą wersję, a następnie zainstaluj najnowszą wersję z pobranego Instalatora. Rejestracja serwera, grupy synchronizacji i inne ustawienia są obsługiwane przez Instalatora usługi Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Zarządzanie cyklem życia agentami automatycznymi
Za pomocą agenta w wersji 6 zespół synchronizacji plików wprowadziła funkcję automatycznej aktualizacji agenta. Można wybrać jedną z dwóch trybów i określ okno obsługi, w którym podjąć próbę uaktualnienia na serwerze. Ta funkcja jest przeznaczony do udzielenia odpowiedzi na zarządzanie cyklem życia agenta, podając barierkami, zapobiegając agenta z wygaśnięcia lub umożliwiającą bezproblemowe, pozostają bieżące ustawienie.
1. **Domyślne ustawienie** będzie podejmować próby zapobiegania agenta z poziomu ważności. W ciągu 21 dni od daty wygaśnięcia przesłanych agenta agent podejmie próbę własnym uaktualnienia. Rozpocznie się próba uaktualnienia raz w tygodniu w ciągu 21 dni przed wygaśnięciem i okna obsługi wybrane. **Ta opcja nie eliminuje potrzebę wykonywania regularnych poprawki usługi Microsoft Update.**
2. Opcjonalnie można wybrać, czy agent zostanie automatycznie uaktualniona się zaraz po udostępnieniu nowej wersji agenta (obecnie nieobsługiwane na klastrowanych serwerach). Ta aktualizacja będzie występować podczas okna obsługi wybranego i umożliwiają serwer do korzystania z nowych funkcji i ulepszeń, gdy tylko staną się ogólnie dostępne. Jest to ustawienie zalecane, obaw, zapewniające wersje główne agentów, a także poprawki regularnych aktualizacji na serwerze. Każdego agenta, ogólnie jest w wersji Ogólnodostępnej jakości. Nawet jeśli zostanie wybrana automatycznie aktualizowane, gdy nowa wersja jest dostępna opcja, może nie być oferowana aktualizację natychmiast po wydaniu. Nowych agentów są początkowo dostępne dla niewielkiej liczby serwerów, a następnie oferty firma Microsoft rozwiń stopniowo. Po zakończeniu pilotaż agenta również staną się dostępne w witrynie Microsoft Update i [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Gwarantuje agenta cyklu życia i zarządzanie zmianami
Usługa Azure File Sync to usługa w chmurze, które ciągle wprowadza nowe funkcje i ulepszenia. Oznacza to, że określonej wersji agenta usługi Azure File Sync jest możliwa tylko przez ograniczony czas. Aby ułatwić wdrożenie, następujące reguły gwarantuje, że masz wystarczająco dużo czasu i powiadomienie, aby pomieścić agent aktualizacji bądź uaktualnień w procesie zarządzania zmiany:

- Dla co najmniej sześciu miesięcy od daty początkowej wersji obsługiwane są wersje główne agenta.
- Firma Microsoft gwarantuje, że nakładanie się co najmniej trzech miesięcy od obsługi wersji agentów głównych. 
- Są wyświetlane ostrzeżenia dla zarejestrowanych serwerów przy użyciu agenta wygasłe wkrótce zostaną co najmniej trzy miesiące przed wygaśnięciem. Możesz sprawdzić, czy zarejestrowanego serwera używa starszej wersji agenta w sekcji zarejestrowane serwery usługi synchronizacji magazynu.
- Okres istnienia wersji pomocniczej agenta jest powiązany z skojarzone wersji głównej. Na przykład po wydaniu wersji agenta 3.0, agenta w wersji 2. \* będzie można ustawić wygaśnięcie ze sobą.

> [!Note]
> Zainstalowanie wersji agenta z ostrzeżeniem wygaśnięcia wyświetlić ostrzeżenie, ale powiodło się. Próby zainstalowania lub połączyć się z wersją agenta wygasłe nie jest obsługiwane i zostanie zablokowane.
