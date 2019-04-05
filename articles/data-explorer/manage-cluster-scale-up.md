---
title: Skalowanie klastra Eksploratora danych usługi Azure, aby pomieścić zmiennym obciążeniem
description: W tym artykule opisano kroki, aby skalować w górę lub dół klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 565953c8e0c6f9765d5eeb16a9fa18c3e79b8370
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044937"
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
