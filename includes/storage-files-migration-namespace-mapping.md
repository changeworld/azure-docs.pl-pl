---
title: Mapowanie struktury folderów do topologii Azure File Sync
description: Mapowanie istniejącej struktury plików i folderów do udziałów plików platformy Azure do użycia z Azure File Sync. Wspólny blok tekstu współużytkowany przez dokumenty migracji.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209560"
---
W tym kroku oceniasz liczbę potrzebnych udziałów plików platformy Azure. Pojedynczy serwer systemu Windows (lub klaster) może synchronizować do 30 udziałów plików platformy Azure.

Możesz mieć więcej folderów w StorSimple, które obecnie są udostępniane lokalnie jako udziały SMB dla użytkowników i aplikacji. Najłatwiej byłoby Envision dla udziału lokalnego, aby mapować 1:1 do udziału plików platformy Azure. Jeśli ta liczba jest w niewielkim zakresie, co jest poniżej 30 dla jednego serwera z systemem Windows lub planujesz dwa serwery z systemem Windows (60) i tak dalej, zaleca się mapowanie 1:1.

Jeśli masz więcej udziałów niż 30, często nie trzeba mapować lokalnego udziału 1:1 do udziału plików platformy Azure.
należy wziąć pod uwagę następujące opcje:

#### <a name="share-grouping"></a>Grupowanie udziałów

Na przykład jeśli Dział kadr ma łącznie 15 udziałów, można rozważyć przechowywanie wszystkich danych KADRy w pojedynczym udziale plików platformy Azure. Przechowywanie wielu udziałów lokalnych w jednym udziale plików platformy Azure nie uniemożliwia tworzenia zwykłych 15 udziałów SMB na lokalnym serwerze Windows Server. Oznacza to, że można organizować foldery główne tych 15 udziałów jako podfoldery w ramach wspólnego folderu. Następnie zsynchronizuj ten wspólny folder z udziałem plików platformy Azure. W ten sposób tylko jeden udział plików platformy Azure w chmurze jest wymagany dla tej grupy udziałów lokalnych.

#### <a name="volume-sync"></a>Synchronizacja woluminu

Azure File Sync obsługuje synchronizowanie katalogu głównego woluminu z udziałem plików platformy Azure.
W przypadku synchronizacji folderu głównego wszystkie podfoldery i pliki będą kończyć się w tym samym udziale plików platformy Azure.

#### <a name="other-best-practices-to-consider"></a>Inne najlepsze rozwiązania, które należy wziąć pod uwagę

Poza limitem liczby synchronizacji udziałów plików platformy Azure na serwer, wiodącą kwestią jest wydajność synchronizacji.

Jeśli na serwerze istnieje wiele udziałów, które są synchronizowane z własnym udziałem plików platformy Azure, synchronizacja może równolegle współpracować ze wszystkimi nimi. Wektor skali nie jest rozmiarem wszystkich plików w zakresie synchronizacji. Jest to liczba elementów (plików i folderów), które wymagają przetworzenia.

Pojedynczy udział plików platformy Azure może zawierać maksymalnie 100 TiB.
Azure File Sync obsługuje synchronizację do 100 000 000 elementów na udział plików platformy Azure.

Synchronizacja woluminu głównego nie zawsze będzie najlepszą odpowiedzią. Synchronizacja wielu lokalizacji wiąże się z zaletami, co pozwala zachować liczbę elementów niższych dla zakresu synchronizacji. Konfigurowanie usługi Azure File Sync o mniejszej liczbie elementów nie jest istotne dla synchronizacji, ale również umożliwia przywracanie po stronie chmury z kopii zapasowych, a także zwiększa szybkość odzyskiwania po awarii na wypadek utraty serwera i udostępnienia nowego połączenia z tym samym pliem platformy Azure udziały e.

Skorzystaj z kombinacji powyższych koncepcji, aby pomóc określić liczbę potrzebnych udziałów plików platformy Azure, a które części istniejących danych StorSimple będą kończyć udział plików platformy Azure.

Utwórz listę, która rejestruje Twoje przemyślenia, aby można było odwołać się do niej w następnym kroku. Zorganizowane tutaj jest ważne, ponieważ może być łatwo utracone szczegóły planu mapowania podczas aprowizacji wielu zasobów jednocześnie.
