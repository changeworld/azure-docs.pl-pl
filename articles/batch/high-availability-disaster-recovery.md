---
title: Wysoka dostępność i odzyskiwanie po awarii — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak projektować aplikacje usługi Batch awarii regionalnej
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: lahugh
ms.openlocfilehash: b863785575263fedd144b3d599962a8e1559e0a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549756"
---
# <a name="design-your-application-for-high-availability"></a>Projektowanie aplikacji wysokiej dostępności

Usługa Azure Batch to usługa regionalna. Usługi Batch jest dostępna we wszystkich regionach platformy Azure, ale po utworzeniu konta usługi Batch musi być skojarzone z regionem. Wszystkie operacje dla konta usługi Batch, która jest następnie zastosować do tego regionu. Na przykład pul i skojarzone maszyny wirtualne (VM) są tworzone w tym samym regionie co konto usługi Batch.

Podczas projektowania aplikacji, która używa usługi Batch, należy wziąć pod uwagę możliwości usługi Batch nie są dostępne w regionie. Użytkownik może wystąpić sytuacja, rzadko w przypadku problemu z regionem jako całości, całą partię usługi w regionie lub problem z określonym kontem usługi Batch.

Jeśli aplikacji lub rozwiązania za pomocą usługi Batch zawsze musi być dostępny, a następnie ją powinny zostać tak zaprojektowane, przejście w tryb failover do innego regionu lub zawsze mieć obciążenia podzielony między co najmniej dwóch regionach. Obie opcje wymagają co najmniej dwa konta usługi Batch w każdym z nich znajduje się w innym regionie.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Wiele kont usługi Batch w wielu regionach

Używanie wielu kont usługi Batch w różnych regionach umożliwia aplikacji dalsze działanie, jeśli konto usługi Batch w innym regionie staje się niedostępny. Używanie wielu kont jest szczególnie ważne, jeśli aplikacja wymaga, by zapewnić wysoką dostępność.

W niektórych przypadkach aplikacji mogą być przeznaczone do zawsze należy używać co najmniej dwóch regionach. Na przykład gdy będziesz potrzebować znaczną ilość miejsca, przy użyciu wielu regionów mogą być potrzebne do obsługi dużych aplikacji lub o pracownikach na przyszły rozwój.

## <a name="design-considerations-for-providing-failover"></a>Zagadnienia dotyczące projektowania zapewniające trybu failover

Kluczowym elementem do rozważenia po daje możliwość pracy awaryjnej w alternatywnym regionie wszystkich składników w ramach rozwiązania muszą być uwzględnione; nie wystarczy po prostu mają drugiego konta usługi Batch. Na przykład w większości aplikacji usługi Batch, konto magazynu platformy Azure jest wymagany, konto magazynu i konto usługi Batch musi znajdować się w tym samym regionie akceptowalny poziom wydajności.

Podczas projektowania rozwiązania, która może być trybu failover, należy wziąć pod uwagę następujące kwestie:

- Wstępne tworzenie wszystkich wymaganych kont w poszczególnych regionach, takich jak konto usługi Batch i konta magazynu. Często nie ma opłaty za kont utworzonych, tylko wtedy, gdy istnieje przechowywanych danych lub konto jest używane.
- Upewnij się, że przydziały są ustawiane na kontach wyprzedzeniem, dzięki czemu można przydzielić wymagana liczba rdzeni, przy użyciu konta usługi Batch.
- Użyj szablonów i/lub skrypty, aby zautomatyzować wdrożenie aplikacji w regionie.
- Aktualizowanie plików binarnych aplikacji i danych referencyjnych we wszystkich regionach. Uzyskuje pewność, że region może być udostępnione online, szybko bez potrzeby czekania na przekazywanie i wdrażanie plików. Na przykład w przypadku niestandardowych aplikacji do zainstalowania w węzłach puli usługi przechowywane i odwoływać się za pomocą pakietów aplikacji usługi Batch, następnie gdy nowa wersja aplikacji jest generowany, powinna być przekazany do każdego konta usługi Batch i przywoływany przez konfigurację puli (lub Wprowadź nową wersję domyślną wersję).
- W aplikacji, wywołanie usługi Batch, magazynu i innych usług, łatwe przełączenie klientów lub obciążenia w innym regionie.
- Najlepszym rozwiązaniem, aby upewnić się, że przejścia w tryb failover zakończy się pomyślnie jest często przełączenie w alternatywnym regionie w ramach normalnych operacji. Na przykład z dwoma wdrożeniami w oddzielnych regionach, przełączenie w alternatywnym regionie co miesiąc.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej na temat tworzenia konta usługi Batch z [witryny Azure portal](batch-account-create-portal.md), [wiersza polecenia platformy Azure](cli-samples.md), [Powershell](batch-powershell-cmdlets-get-started.md), lub [Batch management API](batch-management-dotnet.md).
- Domyślne limity przydziałów są skojarzone z kontem usługi Batch; [w tym artykule](batch-quota-limit.md) szczegóły domyślny limit przydziału wartości, a w tym artykule opisano, jak można zwiększyć limity przydziału.
