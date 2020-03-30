---
title: Omówienie migawek udostępniania dla usługi Azure Files | Dokumenty firmy Microsoft
description: Migawka udziału jest tylko do odczytu wersji udziału usługi Azure Files, który jest wykonany w momencie w czasie, jako sposób na tworzenie kopii zapasowej udziału.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72333179"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Overview of share snapshots for Azure Files (Omówienie migawek udziałów w usłudze Azure Files) 
Usługa Azure Files umożliwia tworzenie migawek udziału w udziałach plików. Migawki udziału przechwytują stan udziału w tym momencie. W tym artykule opisano, jakie funkcje udostępniają migawki i jak można z nich korzystać w przypadku użycia niestandardowego.

## <a name="when-to-use-share-snapshots"></a>Kiedy używać migawek udostępniania

### <a name="protection-against-application-error-and-data-corruption"></a>Ochrona przed błędami aplikacji i uszkodzeniem danych
Aplikacje korzystające z udziałów plików wykonują operacje, takie jak zapisywanie, odczytywanie, przechowywanie, przesyłanie i przetwarzanie. Jeśli aplikacja jest nieprawidłowo skonfigurowana lub zostanie wprowadzony niezamierzony błąd, przypadkowe zastąpienie lub uszkodzenie może zdarzyć się w kilku blokach. Aby pomóc w ochronie przed tymi scenariuszami, można zrobić migawkę udziału przed wdrożeniem nowego kodu aplikacji. Jeśli błąd lub aplikacja zostanie wprowadzony z nowym wdrożeniem, można wrócić do poprzedniej wersji danych w tym udziale plików. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Ochrona przed przypadkowymi usunięciami lub niezamierzonych zmian
Wyobraź sobie, że pracujesz nad plikiem tekstowym w udziale plików. Po zamknięciu pliku tekstowego utracisz możliwość cofnięcia zmian. W takich przypadkach należy odzyskać poprzednią wersję pliku. Migawek udostępniania można użyć do odzyskania poprzednich wersji pliku, jeśli przypadkowo zmieniono jego nazwę lub usunięto.

### <a name="general-backup-purposes"></a>Ogólne cele tworzenia kopii zapasowych
Po utworzeniu udziału plików można okresowo tworzyć migawkę udziału w udziale, aby użyć go do tworzenia kopii zapasowych danych. Migawka udziału, gdy jest pobierana okresowo, pomaga w utrzymaniu poprzednich wersji danych, które mogą być używane do przyszłych wymagań inspekcji lub odzyskiwania po awarii.

## <a name="capabilities"></a>Możliwości
Migawka udziału jest kopią danych tylko do odczytu w czasie. Migawki można tworzyć, usuwać i zarządzać nimi przy użyciu interfejsu API REST. Te same możliwości są również dostępne w bibliotece klienta, interfejsu wiersza polecenia platformy Azure i witrynie Azure portal. 

Migawki udziału można wyświetlać za pomocą interfejsu API REST i SMB. Można pobrać listę wersji katalogu lub pliku, a określoną wersję można zainstalować bezpośrednio jako dysk (dostępny tylko w systemie Windows - patrz [Limity](#limits)). 

Po utworzeniu migawki udziału można ją odczytać, skopiować lub usunąć, ale nie zmodyfikować. Nie można skopiować migawki całego udziału na inne konto magazynu. Musisz zrobić ten plik po pliku, za pomocą AzCopy lub innych mechanizmów kopiowania.

Funkcja udostępniania migawki jest dostępna na poziomie udziału plików. Pobieranie odbywa się na poziomie poszczególnych plików, aby umożliwić przywracanie poszczególnych plików. Pełny udział plików można przywrócić przy użyciu modułu SMB, interfejsu API REST, portalu, biblioteki klienta lub narzędzi programu PowerShell/CLI.

Migawka udziału udziału w udziale plików jest identyczna z jego podstawowym udziałem plików. Jedyną różnicą jest to, że **DateTime** wartość jest dołączana do identyfikatora URI udziału, aby wskazać czas, w którym migawka udziału została podjęta. Na przykład jeśli identyfikator URI http://storagesample.core.file.windows.net/myshareudziału plików jest , identyfikator URI migawki udziału jest podobny do:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Migawki udziału utrzymują się, dopóki nie zostaną jawnie usunięte. Migawka udziału nie może przeżyć swojego udziału plików podstawowych. Można wyliczyć migawki skojarzone z udziałem plików podstawowych, aby śledzić bieżące migawki. 

Podczas tworzenia migawki udziału udziału, pliki we właściwościach systemu udziału są kopiowane do migawki udziału o tych samych wartościach. Pliki podstawowe i metadane udziału plików są również kopiowane do migawki udziału, chyba że podczas tworzenia zostanie określone oddzielne metadane migawki udziału.

Nie można usunąć udziału, który ma migawki udziału, chyba że najpierw usuniesz wszystkie migawki udziału.

## <a name="space-usage"></a>Wykorzystanie przestrzeni 
Migawki udziału mają charakter przyrostowy. Tylko dane, które uległy zmianie po zapisaniu ostatniej migawki udziału. Minimalizuje to czas wymagany do utworzenia migawki udziału i oszczędza na kosztach magazynowania. Każda operacja zapisu do obiektu lub właściwości lub operacji aktualizacji metadanych jest wliczany do "zmienionej zawartości" i jest przechowywany w migawce udziału. 

Aby zaoszczędzić miejsce, można usunąć migawkę udziału dla okresu, w którym współczynnik zmian był najwyższy.

Mimo że migawki udziału są zapisywane przyrostowo, należy zachować tylko najnowszą migawkę udziału, aby przywrócić udział. Po usunięciu migawki udziału usuwane są tylko dane unikatowe dla tej migawki udziału. Aktywne migawki zawierają wszystkie informacje potrzebne do przeglądania i przywracania danych (od momentu odebrania migawki udziału) do oryginalnej lokalizacji lub lokalizacji alternatywnej. Można przywrócić na poziomie elementu.

Migawki nie są wliczane do limitu udziału 5 TB. Nie ma limitu, ile miejsca migawki udziału zajmują w sumie. Limity kont magazynu nadal obowiązują.

## <a name="limits"></a>Limity
Maksymalna liczba migawek udziału, które usługa Azure Files zezwala obecnie jest 200. Po 200 migawek udziału, należy usunąć starsze migawki udziału w celu utworzenia nowych. 

Nie ma ograniczeń dla jednoczesnych wywołań tworzenia migawek udziału. Nie ma limitu ilości miejsca, które współużytkują migawki określonego udziału plików może zużyć. 

Obecnie nie jest możliwe instalowanie migawek udostępniania w systemie Linux. Dzieje się tak dlatego, że klient SMB systemu Linux nie obsługuje migawek montażowych, tak jak robi to system Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Kopiowanie danych z powrotem do udziału z migawki udziału
Operacje kopiowania, które obejmują pliki i udostępnianie migawek, są zgodne z poniższymi regułami:

Poszczególne pliki w migawce udziału plików można kopiować do udziału podstawowego lub w dowolnej innej lokalizacji. Można przywrócić wcześniejszą wersję pliku lub przywrócić pełny udział plików, kopiując plik według pliku z migawki udziału. Migawka udziału nie jest promowana do udziału podstawowego. 

Migawka udziału pozostaje nienaruszona po skopiowaniu, ale podstawowy udział plików jest zastępowany kopią danych, które były dostępne w migawce udziału. Wszystkie przywrócone pliki są wliczane do "zmienionej zawartości".

Plik w migawce udziału można skopiować do innego miejsca docelowego o innej nazwie. Wynikowy plik docelowy jest zapisywalnym plikiem, a nie migawką udziału. W takim przypadku podstawowy udział plików pozostanie nienaruszony.

Po zastąpieniu pliku docelowego kopią wszystkie migawki udziału skojarzone z oryginalnym plikiem docelowym pozostają nienaruszone.

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki 
Gdy korzystasz z infrastruktury na platformie Azure, zautomatyzuj kopie zapasowe w celu odzyskiwania danych, gdy tylko jest to możliwe. Zautomatyzowane działania są bardziej niezawodne niż procesy ręczne, co pomaga poprawić ochronę danych i możliwość odzyskania danych. Można użyć interfejsu API REST, SDK klienta lub skryptów do automatyzacji.

Przed wdrożeniem harmonogramu migawki udziału należy dokładnie rozważyć częstotliwość migawki udziału i ustawienia przechowywania, aby uniknąć ponoszenia niepotrzebnych opłat.

Migawki udostępniania zapewniają tylko ochronę na poziomie pliku. Migawki udostępniania nie uniemożliwiają usuwania tłuszczu palca na koncie udziału plików lub magazynu. Aby chronić konto magazynu przed przypadkowymi usunięciami, można zablokować konto magazynu lub grupę zasobów.

## <a name="next-steps"></a>Następne kroki
- Praca z migawkami udostępniania w:
    - [Powershell](storage-how-to-use-files-powershell.md)
    - [Cli](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Często zadawane pytania dotyczące migawki udostępniania](storage-files-faq.md#share-snapshots)
