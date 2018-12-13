---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: b099f5ff7e43f2deeb3b8c41adcb802cd431a65a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286217"
---
Agent usługi Azure File Sync jest aktualizowana w regularnych odstępach czasu, aby dodać nowe funkcje i rozwiązania problemów. Firma Microsoft zaleca się skonfigurowanie Microsoft Update, aby pobrać aktualizacje dla agenta usługi Azure File Sync, ponieważ są one dostępne.

#### <a name="major-vs-minor-agent-versions"></a>Główne a wersji pomocniczej agentów
* Wersje główne agentów często zawierają nowe funkcje i mieć coraz jako pierwsza część numeru wersji. Na przykład: *2.\*.\**
* Wersje agentów pomocnicze są również nazywane "poprawki" i są wydawane częściej niż wersje główne. Zawierają one często poprawki błędów i ulepszenia mniejszy, ale żadne nowe funkcje. Na przykład:  *\*.3.\**

#### <a name="upgrade-paths"></a>Ścieżki uaktualnienia
Istnieją cztery zatwierdzone i przetestowane sposobów instalowania aktualizacji agenta usługi Azure File Sync. 
1. **(Preferowane) Skonfiguruj Microsoft Update w celu automatycznego pobierania i instalowania aktualizacji agenta.**  
    Zawsze zalecamy poświęcenie każdej aktualizacji usługi Azure File Sync, aby upewnić się, że masz dostęp do najnowszych poprawek dla agenta programu server. Microsoft Update sprawia, że ten proces jest bezproblemowe, przez automatyczne pobieranie i instalowanie aktualizacji dla Ciebie.
2. **Użyj AfsUpdater.exe do pobrania i zainstalowania aktualizacji agenta.**  
    AfsUpdater.exe znajduje się w katalogu instalacji agenta. Kliknij dwukrotnie plik wykonywalny, aby pobrać i zainstalować aktualizacje agenta. 
3. **Stosowanie poprawek do istniejącego agenta usługi Azure File Sync za pomocą plik poprawki usługi Microsoft Update lub MSP pliku wykonywalnego. Można pobrać najnowszy pakiet aktualizacji usługi Azure File Sync z [katalogu usługi Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    Uruchamianie pliku wykonywalnego MSP uaktualni instalacji usługi Azure File Sync za pomocą tej samej metody, które są używane automatycznie przez usługę Microsoft Update w poprzednim ścieżki uaktualnienia. Stosowanie poprawki usługi Microsoft Update przeprowadzi uaktualnienie w miejscu instalacji usługi Azure File Sync.
4. **Pobierz najnowsze Instalatora agenta usługi Azure File Sync z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Pobierz Instalatora jest pakiet Microsoft Installer lub pliku wykonywalnego msi.**  
    Aby uaktualnić istniejącą instalację agenta usługi Azure File Sync, odinstaluj starszą wersję, a następnie zainstaluj najnowszą wersję z pobranego Instalatora. Rejestracja serwera, grupy synchronizacji i inne ustawienia są obsługiwane przez Instalatora usługi Azure File Sync.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Gwarantuje agenta cyklu życia i zarządzanie zmianami
Usługa Azure File Sync to usługa w chmurze, stale umożliwiający wprowadzenie nowych funkcji i funkcji. Oznacza to, że określonej wersji agenta usługi Azure File Sync jest możliwa tylko przez ograniczony czas. Aby ułatwić wdrożenie, poniższe reguły, aby zagwarantować, możesz mieć wystarczająco dużo czasu i powiadomienie, aby pomieścić agent aktualizacji bądź uaktualnień w procesie zarządzania zmiany:

- Dla co najmniej sześciu miesięcy od daty początkowej wersji obsługiwane są wersje główne agenta.
- Firma Microsoft gwarantuje, że nakładanie się co najmniej trzech miesięcy od obsługi wersji agentów głównych. 
- Są wyświetlane ostrzeżenia dla zarejestrowanych serwerów przy użyciu agenta wygasłe wkrótce zostaną co najmniej trzy miesiące przed wygaśnięciem. Możesz sprawdzić, czy zarejestrowanego serwera używa starszej wersji agenta w sekcji zarejestrowane serwery usługi synchronizacji magazynu.
- Okres istnienia wersji pomocniczej agenta jest powiązany z skojarzone wersji głównej. Na przykład po wydaniu wersji agenta 3.0, agenta w wersji 2. \* będzie można ustawić wygaśnięcie ze sobą.

> [!Note]
> Zainstalowanie wersji agenta z ostrzeżeniem wygaśnięcia wyświetlić ostrzeżenie, ale powiodło się. Próby zainstalowania lub połączyć się z wersją agenta wygasłe nie jest obsługiwane i zostanie zablokowane.
