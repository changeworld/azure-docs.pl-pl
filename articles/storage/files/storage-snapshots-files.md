---
title: Omówienie migawek udział plików Azure | Dokumentacja firmy Microsoft
description: Migawka udziału jest tylko do odczytu wersji udziału plików platformy Azure, która jest wykonywana w punkcie w czasie, aby utworzyć kopię zapasową udziału.
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: renash
ms.openlocfilehash: af113ae76d81c82ff6c4ced1569aa16f3a9ee27c
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063921"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Omówienie migawek udział plików Azure 
Usługa pliki Azure oferuje możliwość migawek udziału udziałów plików. Udostępnianie przechwytywania migawek stanu udziału w danym momencie. W tym artykule opisano możliwości, jakie Podaj migawki udziału i jak można korzystać z nich w Twoim przypadku wykorzystania niestandardowego.

## <a name="when-to-use-share-snapshots"></a>Kiedy należy używać udziału migawki

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrona przed uszkodzeniem o błędach i dane aplikacji
Aplikacje korzystające z udziałów plików wykonywać operacje, takie jak zapisywanie, odczytywanie, magazynu, transmisji i przetwarzania. Jeśli aplikacja jest nieprawidłowo skonfigurowana lub wprowadzono niezamierzone usterki, przypadkowe nadpisanie lub uszkodzenia możliwe do kilku bloków. Aby lepiej chronić przed tych scenariuszy, można utworzyć migawkę udziału przed wdrożeniem nowego kodu aplikacji. Jeśli wprowadzono błąd usterki lub aplikacji z nowego wdrożenia, można przejść wstecz do poprzedniej wersji danych w tym udziale plików. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrona przed przypadkowym usunięciu lub niezamierzonych zmian
Załóżmy, że pracujesz w pliku tekstowym w udziale plików. Po zamknięciu pliku tekstowego, utracisz możliwość Cofnij zmiany. W takich sytuacjach należy następnie odzyskać poprzednią wersję pliku. Przy użyciu migawek udziału Aby odzyskać poprzednie wersje pliku, jeśli została przypadkowo zmienione lub usunięte.

### <a name="general-backup-purposes"></a>Ogólnych celów tworzenia kopii zapasowej
Po utworzeniu udziału plików, należy okresowo tworzyć migawki udziału udziału plików, aby użyć go do utworzenia kopii zapasowej danych. Udział migawki po podjęciu okresowo, pomaga zachować poprzednie wersje danych, który może służyć do wymagań przyszłych inspekcji lub odzyskiwania po awarii.

## <a name="capabilities"></a>Możliwości
Migawka udziału jest w momencie, tylko do odczytu kopię danych. Tworzenie, usuwanie i zarządzać migawki za pomocą interfejsu API REST. Takie same możliwości są również dostępne w bibliotece klienta, wiersza polecenia platformy Azure i portalu Azure. 

Migawki udziału można wyświetlić przy użyciu interfejsu API REST i protokołu SMB. Można pobrać listy wersji pliku lub katalogu, a w przypadku zainstalowania określonej wersji bezpośrednio jako dysk. 

Po utworzeniu migawki udziału, to można można odczytać, kopiowane, lub usunięte, ale nie został zmodyfikowany. Nie można skopiować migawki całego udziału do innego konta magazynu. Wystarczy wykonać to za plik, za pomocą narzędzia AzCopy lub innych mechanizmów kopiowania.

Funkcja migawki udziału znajduje się na poziomie udziału plików. Pobieranie znajduje się na poziomie poszczególnych plików, aby umożliwić Przywracanie pojedynczych plików. Udział plików pełną można przywrócić za pomocą protokołu SMB, interfejsu API REST, portalu, Biblioteka klienta lub narzędzi programu PowerShell/interfejsu wiersza polecenia.

Migawki udziału udział plików jest identyczny z udziału pliku podstawowego. Jedyną różnicą jest to, że **DateTime** wartość jest dołączany do udziału URI wskazująca czas, w którym udziału migawki. Na przykład jeśli identyfikator URI udostępniania plików jest http://storagesample.core.file.windows.net/myshare, identyfikator URI jest podobny do migawki udziału:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Migawki udziału utrzymana do jawnie są usuwane. Migawki udziału nie outlive udziału pliku podstawowego. Można wyliczyć migawki skojarzone z udziałem pliku podstawowego do śledzenia sieci bieżącej migawki. 

Po utworzeniu migawki udziału udziału plików, pliki we właściwościach systemu udziału są kopiowane do migawki udziału o takich samych wartości. Podstawowy plików i metadanych udziału plików są również kopiowane do migawki udziału, chyba że zostanie oddzielne metadanych dla udziału migawki podczas jego tworzenia.

Nie można usunąć udziału dysponującej migawkami udziału, chyba że najpierw usuń wszystkie migawki udziału.

## <a name="space-usage"></a>Użycie miejsca 
Udział migawki są przyrostowe charakter. Tylko dane o zmianie po zapisaniu ostatniej migawki udziału. To zminimalizować czas wymagany do utworzenia migawki udziału i zapisuje kosztów magazynowania. Operacja żadnego zapisu do obiektu lub właściwości lub metadane operacji aktualizacji są wliczane do "zmieniona zawartość" i jest przechowywany w udziale migawki. 

Aby zaoszczędzić miejsce, można usunąć migawki udziału w okresie, kiedy ilość danych churn był najwyższy.

Mimo że udziału migawki są zapisywane przyrostowo, należy zachować tylko najnowsze migawka udziału w celu przywrócenia udziału. Po usunięciu migawki udziału unikatowe dla tej migawki udziału dane zostaną usunięte. Migawki Active zawierają wszystkie informacje potrzebne do przeglądania i przywrócić dane (od momentu, w którym migawki udziału) do lokalizacji oryginalnej lub alternatywnej lokalizacji. Można przywrócić na poziomie elementu.

Migawki nie są wliczane do limitu udziału 5 TB. Nie ma żadnego limitu ilości miejsca na udział migawki zajmować całkowita liczba. Limity konta magazynu jest nadal mają zastosowanie.

## <a name="limits"></a>Limity
Maksymalna liczba migawek udział plików Azure umożliwia obecnie to 200. Po 200 udziału migawki musisz usunąć starsze migawki udziału Aby utworzyć nowe. 

Nie ma żadnego limitu równoczesnych wywołań do tworzenia migawek udziału. Nie ma żadnego limitu miejsce za tego udziału, który może używać migawki udziału określonego pliku. 

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiowanie danych z powrotem do udziału z migawki udziału
Operacje kopiowania, które obejmują pliki i udostępniać migawki wykonać następujące czynności:

Możesz skopiować poszczególnych plików w migawce udziału pliku za pośrednictwem udziału w podstawowej lub innej lokalizacji. Można przywrócić wcześniejszą wersję pliku lub Przywróć udziału plików pełną przez skopiowanie pliku przez plik z migawki udziału. Migawki udział nie jest podwyższany do podstawowego udziału. 

Migawki udziału pozostanie niezmieniona po skopiowaniu, ale udziału pliku podstawowego jest zastępowany kopii danych, które były dostępne w migawce udziału. Wartość licznika przywróconych plików kierunku "zmienić zawartość."

Plik migawki udziału można skopiować do lokalizacji docelowej z inną nazwą. Wynikowy plik docelowy znajduje się plik zapisywalny, a nie migawki udziału.

Jeśli plik docelowy są zastępowane kopią, wszystkie skojarzone z oryginalnym plikiem docelowym migawki udziału pozostaną nienaruszone.

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki 
Jeśli korzystasz z infrastruktury na platformie Azure, zautomatyzować tworzenie kopii zapasowych, odzyskiwania danych, jeśli to możliwe. Akcje automatyczne są bardziej niezawodna niż procesów ręcznych, pomoc w celu zwiększenia ochrony danych i odzyskiwania. Można użyć interfejsu API REST, zestawu SDK klienta lub wykonywanie skryptów automatyzacji.

Przed wdrożeniem harmonogramu migawek udziału, należy rozważyć, częstotliwości migawki udziału, a ustawienia przechowywania, aby uniknąć naliczania opłat niepotrzebne.

Migawki udziału Podaj tylko ochrony na poziomie pliku. Migawki udziału nie uniemożliwiają usunięć palca fat na koncie udziału lub magazynu plików. Aby lepiej chronić konto magazynu po przypadkowym usunięciu, można zablokować konta magazynu lub grupy zasobów.

## <a name="next-steps"></a>Kolejne kroki
- Praca z migawek udziału:
    - [Portal](storage-how-to-use-files-portal.md#create-and-modify-share-snapshots)
    - [Program PowerShell](storage-how-to-use-files-powershell.md#create-and-modify-share-snapshots)
    - [Interfejs wiersza polecenia](storage-how-to-use-files-cli.md#create-and-modify-share-snapshots)
- [Udostępnianie migawki — często zadawane pytania](storage-files-faq.md#share-snapshots)
