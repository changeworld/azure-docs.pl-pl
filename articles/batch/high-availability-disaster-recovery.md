---
title: Wysoka dostępność i odzyskiwanie po awarii — usługa Azure Batch
description: Dowiedz się, jak zaprojektować aplikację batch dla regionalnej awarii. Obciążenia powinny przewinąć awaryjnie do innego regionu lub być podzielone między dwa lub więcej regionów.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2019
ms.author: labrenne
ms.openlocfilehash: 84b0cce9557b4ae05586579f175cd0f5db14fdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77026085"
---
# <a name="design-your-application-for-high-availability"></a>Projektowanie aplikacji wysokiej dostępności

Usługa Azure Batch jest usługą regionalną. Usługa Batch jest dostępna we wszystkich regionach platformy Azure, ale po utworzeniu konta usługi Batch musi być skojarzona z regionem. Wszystkie operacje dla konta usługi Batch następnie stosuje się do tego regionu. Na przykład pule i skojarzone maszyny wirtualne (maszyny wirtualne) są tworzone w tym samym regionie co konto usługi Batch.

Podczas projektowania aplikacji, która używa batch, należy wziąć pod uwagę możliwość Batch nie są dostępne w regionie. Istnieje możliwość wystąpienia rzadkiej sytuacji, w której występuje problem z regionem jako całością, całą usługą wsadową w regionie lub problem z określonym kontem usługi Batch.

Jeśli aplikacja lub rozwiązanie przy użyciu usługi Batch zawsze musi być dostępna, należy go zaprojektować tak, aby przewidzieli pracę awaryjną do innego regionu lub zawsze mieć obciążenie podzielone między dwa lub więcej regionów. Oba podejścia wymagają co najmniej dwóch kont usługi Batch, przy czym każde konto znajduje się w innym regionie.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Wiele kont usługi Batch w wielu regionach

Korzystanie z wielu kont usługi Batch w różnych regionach zapewnia aplikacji możliwość kontynuowania działania, jeśli konto usługi Batch w innym regionie stanie się niedostępne. Korzystanie z wielu kont jest szczególnie ważne, jeśli aplikacja musi być wysoce dostępna.

W niektórych przypadkach aplikacja może być zaprojektowana tak, aby zawsze używać dwóch lub więcej regionów. Na przykład, gdy potrzebujesz znacznej ilości pojemności, przy użyciu wielu regionów może być potrzebne do obsługi aplikacji na dużą skalę lub zaspokoić przyszłego wzrostu.

## <a name="design-considerations-for-providing-failover"></a>Zagadnienia dotyczące projektu dotyczące zapewnienia trybu failover

Kluczowym punktem, który należy wziąć pod uwagę przy udzielaniu możliwości pracy awaryjnej w alternatywnym regionie, jest to, że należy wziąć pod uwagę wszystkie składniki rozwiązania; nie wystarczy po prostu mieć drugie konto usługi Batch. Na przykład w większości aplikacji usługi Batch wymagane jest konto magazynu platformy Azure, z kontem magazynu i kontem usługi Batch, które muszą znajdować się w tym samym regionie, aby uzyskać akceptowalną wydajność.

Podczas projektowania rozwiązania, które może zakończyć się awaryjnie, należy wziąć pod uwagę następujące kwestie:

- Wstępnie utwórz wszystkie wymagane konta w każdym regionie, takie jak konto usługi Batch i konto magazynu. Często nie ma żadnych opłat za tworzenie kont, tylko wtedy, gdy są przechowywane dane lub konto jest używane.
- Upewnij się, że przydziały są ustawiane na kontach z wyprzedzeniem, dzięki czemu można przydzielić wymaganą liczbę rdzeni przy użyciu konta usługi Batch.
- Użyj szablonów i/lub skryptów, aby zautomatyzować wdrażanie aplikacji w regionie.
- Aktualizuj pliki binarne aplikacji i dane referencyjne we wszystkich regionach. Bądź na bieżąco zapewni region może być szybko przesuń do trybu online bez konieczności oczekiwania na przekazywanie i wdrażanie plików. Na przykład jeśli niestandardowa aplikacja do zainstalowania w węzłach puli jest przechowywana i odwołuje się przy użyciu pakietów aplikacji Batch, a następnie po wyprodukowaniu nowej wersji aplikacji, należy przekazać do każdego konta usługi Batch i odwołuje się do konfiguracji puli (lub aby nowa wersja była wersją domyślną).
- W aplikacji wywołującej batch, magazyn i inne usługi, łatwo przełączają się klienci lub ładunek do innego regionu.
- Najlepszym rozwiązaniem, aby upewnić się, że praca awaryjna zakończy się pomyślnie, jest częste przełączanie do alternatywnego regionu w ramach normalnej pracy. Na przykład z dwóch wdrożeń w oddzielnych regionach, przełączania do regionu alternatywnego co miesiąc.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o tworzeniu kont wsadowych za pomocą [witryny Azure portal,](batch-account-create-portal.md) [interfejsu wiersza polecenia platformy Azure,](cli-samples.md) [programu PowerShell](batch-powershell-cmdlets-get-started.md)lub [interfejsu API zarządzania wsadem.](batch-management-dotnet.md)
- Przydziały domyślne są skojarzone z kontem usługi Batch; [w tym artykule](batch-quota-limit.md) opisano domyślne wartości przydziałów i opisano sposób zwiększania przydziałów.
