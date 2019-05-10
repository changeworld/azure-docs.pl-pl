---
title: Omówienie migawek udziału dla usługi Azure Files | Dokumentacja firmy Microsoft
description: Migawkę udziału jest udział usługi Azure Files, która jest wykonywana w punkcie w czasie, jako sposób na tworzenie kopii zapasowej do udziału w wersji tylko do odczytu.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d83cf20c856d37d337f4eb22c30ee9b6823d096b
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235818"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Omówienie migawek udziału plików platformy Azure 
Usługa Azure Files oferuje możliwość robienia migawek udziału udziałów plików. Udostępnianie migawki przechwytywania stanu udziału w danym momencie. W tym artykule opisano możliwości, jakie zapewniają migawek udziałów i jak można było korzystać z nich w Twoim przypadku użycia niestandardowego.

## <a name="when-to-use-share-snapshots"></a>Kiedy należy używać migawek udziałów

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrona przed uszkodzeniem danych i błąd aplikacji
Aplikacje, które używają udziałów plików wykonywać operacje takie jak zapisu, odczytu, magazynu, przekazywania i przetwarzania. Jeśli aplikacja jest nieprawidłowo skonfigurowana lub wprowadzono niezamierzone usterki, Zastąp przypadkowego lub uszkodzenia mogą występować kilka bloków. Aby chronić się przed tych scenariuszy, należy wykonać migawkę udziału przed wdrożeniem nowego kodu aplikacji. Jeśli wprowadzono błąd usterki lub aplikacji za pomocą nowego wdrożenia, możesz przejść wstecz do poprzedniej wersji danych w tym udziale plików. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrona przed przypadkowym lub niezamierzone zmiany
Wyobraź sobie, że pracujesz w pliku tekstowym w udziale plików. Po zamknięciu pliku tekstowego, utracisz możliwość cofnięcia zmian. W takich przypadkach należy następnie odzyskać poprzednią wersję pliku. Migawki udziału można użyć, aby odzyskać poprzednie wersje pliku, jeśli została przypadkowo zmieniono nazwę lub usunięto.

### <a name="general-backup-purposes"></a>Ogólne wykonywania kopii zapasowych
Po utworzeniu udziału plików, można utworzyć okresowe migawki udziału, udziału plików na potrzeby tworzenia kopii zapasowych danych. Udział migawki, gdy wykonana okresowo pomaga zachować poprzednie wersje danych, które mogą służyć do wymagania dotyczące inspekcji w przyszłości lub odzyskiwanie po awarii.

## <a name="capabilities"></a>Możliwości
Migawkę udziału jest w momencie, tylko do odczytu kopię danych. Tworzenie, usuwanie i zarządzać migawki za pomocą interfejsu API REST. Te same możliwości również są dostępne w bibliotekę klienta wiersza polecenia platformy Azure i witryny Azure portal. 

Migawki udziału można wyświetlić przy użyciu interfejsu API REST i protokół SMB. Można pobrać listy wersji pliku lub katalogu, a następnie zainstalować określoną wersję bezpośrednio jako dysk (dostępne tylko na Windows — zobacz [limity](#limits)). 

Po utworzeniu migawki udziału go można odczytać, kopiowana, lub usunięte, ale nie zostały zmodyfikowane. Nie można skopiować migawki udziału całego do innego konta magazynu. Musisz zrobić to za plik, za pomocą narzędzia AzCopy lub innych mechanizmów kopiowania.

Funkcja migawki udziału znajduje się na poziomie udziału plików. Pobieranie znajduje się na poziomie poszczególnych plików, aby umożliwić Przywracanie pojedynczych plików. Udział plików pełną można przywrócić przy użyciu protokołu SMB, interfejsu API REST, portalu, biblioteki klienta usługi lub narzędzia PowerShell/interfejsu wiersza polecenia.

Migawkę udziału, udziału plików jest taka sama jak jego udziału pliku podstawowego. Jedyną różnicą jest to, że **daty/godziny** wartość jest dołączany do udziału identyfikatora URI, aby wskazać czas, w którym została wykonana migawki udziału. Na przykład, jeśli identyfikator URI udziału plików jest http://storagesample.core.file.windows.net/myshare, identyfikator URI jest podobne do migawki udziału:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Migawki udziałów są zachowywane aż jawnie są usuwane. Migawkę udziału nie może on nakreślał swój udział pliku podstawowego. Można wyliczyć migawki skojarzone z udziałem pliku podstawowego do śledzenia Twojej bieżącej migawki. 

Po utworzeniu migawki udziału, udziału plików, pliki we właściwościach systemu danego udziału są kopiowane do migawki udziału za pomocą tych samych wartości. Podstawowy plików i udział plików metadanych również są kopiowane do migawki udziału, chyba że określisz oddzielne metadanych dla udziału migawki podczas jego tworzenia.

Nie można usunąć udziału, który ma migawki udziału, chyba że najpierw usuń wszystkie migawki udziału.

## <a name="space-usage"></a>Użycie miejsca na 
Migawki udziałów są przyrostowe charakter. Tylko dane, które została zmieniona po zapisaniu najnowszych migawek udziału. Minimalizuje czas wymagany do utworzenia migawki udziału i zapisuje na koszty magazynowania. Wszelkie zapisu operacji do obiektu lub właściwości lub metadanych operacji aktualizacji jest wliczane do "zmieniona zawartość" i jest przechowywany w migawki udziału. 

Do oszczędzania przestrzeni dyskowej, możesz usunąć migawkę udziału okres, po najwyższą został zmian.

Nawet jeśli migawki udziałów są zapisywane przyrostowo, należy zachować tylko najnowsze migawkę udziału, aby przywrócić udziału. Po usunięciu migawki udziału, tylko te dane, które są unikatowe dla tej migawki udziału jest usuwany. Migawki aktywnego zawierają wszystkie informacje potrzebne do przeglądania i przywracania danych (czas utworzenia migawki udziału) do lokalizacji oryginalnej lub alternatywnej lokalizacji. Można przywrócić na poziomie elementu.

Migawki nie są wliczane do limitu 5 TB udziału. Nie ma żadnego limitu, ile miejsca migawek udziałów zajmować w sumie. Nadal mają zastosowanie limity konta magazynu.

## <a name="limits"></a>Limits
Maksymalna liczba migawek udziałów, które usługi Azure Files umożliwia obecnie to 200. Po migawek udziałów 200 musisz usunąć starsze migawek udziałów, aby utworzyć nowe. 

Nie ma żadnego limitu jednoczesnych wywołań do tworzenia migawek udziałów. Nie ma żadnego limitu ilości miejsca na za tego udziału, w których może używać migawek udziału określonego pliku. 

Obecnie nie jest możliwe do zainstalowania migawek udziału w systemie Linux. Jest to spowodowane klient SMB w systemie Linux nie obsługuje instalowania migawki, takich jak Windows jest.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiowanie danych do udziału z migawki udziału
Operacje kopiowania, które obejmują pliki i udostępniać migawki należy wykonać następujące czynności:

Możesz skopiować poszczególne pliki w migawki udziału plików za pośrednictwem jego podstawowego udziału lub dowolnej innej lokalizacji. Można przywrócić wcześniejszą wersję pliku lub Przywróć udział cały plik, kopiując przez plik z migawki udziału. Migawkę udziału nie zostanie podwyższony do podstawowego udziału. 

Migawkę udziału pozostaje niezmieniony po skopiowaniu, ale udziału pliku podstawowego jest zastępowany przy użyciu kopii danych, która była dostępna w migawki udziału. Liczba przywróconych plików kierunku "zmienić zawartość."

Plik migawki udziału można skopiować do miejsca docelowego pod inną nazwą. Wynikowy plik docelowy znajduje się plik zapisywalny i nie migawkę udziału.

Jeśli plik docelowy są zastępowane kopią, wszystkie migawki udziału, skojarzone z oryginalnego pliku docelowego pozostają bez zmian.

## <a name="general-best-practices"></a>Ogólne najlepsze rozwiązania 
Gdy korzystasz z infrastruktury na platformie Azure, Automatyzacja tworzenia kopii zapasowych, odzyskiwania danych, jeśli to możliwe. Akcje automatyczne są bardziej niezawodne niż procesów ręcznych, pozwalając zwiększyć ochronę danych i możliwość odzyskania danych. Można użyć interfejsu API REST, zestaw SDK klienta lub funkcje tworzenia skryptów automatyzacji.

Przed wdrożeniem usługi scheduler migawki udziału, zastanów się uważnie z ustawień przechowywania, aby uniknąć ponoszenia niepotrzebnych opłat i częstotliwość tworzenia migawek udziału.

Migawki udziałów zapewniają tylko ochrony na poziomie pliku. Migawki udziału nie uniemożliwiają usuwania systemu plików fat palcem w pliku udziału lub konta magazynu. Aby lepiej chronić konto magazynu przypadkowym, można zablokować konto magazynu lub grupy zasobów.

## <a name="next-steps"></a>Kolejne kroki
- Praca z migawek udziału:
    - [Program PowerShell](storage-how-to-use-files-powershell.md)
    - [Interfejs wiersza polecenia](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Udostępnianie migawki — często zadawane pytania](storage-files-faq.md#share-snapshots)
