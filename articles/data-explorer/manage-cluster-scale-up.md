---
title: Skalowanie klastrze Eksplorator danych platformy Azure w celu uwzględnienia zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę lub dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 71f1d197b4a3fb437607f876ea524e582e62c0fd
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756323"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Zarządzaj klastra skalowanie w górę do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Jednak żądanie w klastrze nie można przewidzieć z dokładnością bezwzględne. Rozmiar klastra statyczne może prowadzić do niepełnego lub overutilization, z których żadna nie jest idealnym rozwiązaniem.

Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności i zasobów procesora CPU zmieniającymi się żądanie. Istnieją dwa przepływy pracy na potrzeby skalowania: skalowanie w górę i skalowania w poziomie. W tym artykule przedstawiono sposób zarządzania klastrem skalowanie w górę.

1. Przejdź do klastra. W obszarze **ustawienia**, wybierz opcję **skalowanie w górę**.

    Jest wyświetlana lista dostępnych jednostek SKU. Na przykład na poniższej ilustracji, tylko czterech jednostek SKU są dostępne.

    ![Skalowanie w górę](media/manage-cluster-scale-up/scale-up.png)

    Jednostki SKU są wyłączone, ponieważ są one bieżąca jednostka SKU lub nie są dostępne w regionie, w której zlokalizowany jest klaster.

1. Aby zmienić jednostki SKU, wybierz jednostkę SKU ma i wybierz polecenie **wybierz** przycisku.

> [!NOTE]
> Skalowanie w górę może potrwać kilka minut, a w tym czasie zostanie zawieszony klastra. Należy pamiętać, że skalowanie w dół może negatywnie wpłynąć na wydajność klastra.

Teraz wykonano operację skalowania w górę lub w dół dla klastra Eksploratora danych usługi Azure. Możesz również [Zarządzanie klastra skalowalnego w poziomie](manage-cluster-scale-out.md) do dynamicznego skalowania liczby wystąpień na podstawie metryk, które określisz.

Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.
