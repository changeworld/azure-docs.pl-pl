---
title: Skalowanie klastrze Eksplorator danych platformy Azure w celu uwzględnienia zmiennym obciążeniem
description: W tym artykule opisano kroki w celu skalowania w poziomie i klaster Eksploratora danych usługi Azure, w zależności od zmieniających się żądanie w skali.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 38dc7b70630276d51c75ca7e87f0b69ea7fe040a
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735620"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Zarządzanie skalowania klastra w poziomie do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Ale nie można przewidzieć zapotrzebowanie na klastrze z dokładnością 100%. Rozmiar klastra statyczne może prowadzić do niepełnego wykorzystania lub nadmiernego wykorzystania, z których żadna nie jest idealnym rozwiązaniem. Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności zmieniającymi się żądanie. W tym artykule dowiesz się, jak zarządzać skalowania klastra w poziomie.

Przejdź do klastra, a w obszarze **ustawienia** wybierz **skalowanie w poziomie**. W obszarze **Konfiguruj**, wybierz opcję **włączyć Skalowanie automatyczne**.

![Włącz skalowanie automatyczne](media/manage-cluster-scaling/enable-autoscale.png)

Na poniższym rysunku przedstawiono przepływ następnych krokach. Firma Microsoft zapewnia szczegółowe informacje poniżej grafiki.

![Reguła skalowania](media/manage-cluster-scaling/scale-rule.png)

1. W obszarze **Nazwa ustawienia skalowania automatycznego**, podaj nazwę, taką jak *skalowalnego w poziomie: wykorzystanie w pamięci podręcznej*.

1. W obszarze **tryb skalowania**, wybierz opcję **skalowania na podstawie metryki**. Ten tryb zapewnia dynamiczne skalowanie; Możesz również wybrać **Skaluj do określonej liczby wystąpień**.

1. Wybierz **Dodaj regułę**.

1. W **reguły skalowania** sekcji po prawej stronie, podaj wartości dla każdego ustawienia.

    | Ustawienie | Opis i wartość |
    | --- | --- | --- |
    | **Agregacja czasu** | Wybierz kryteria agregacji, takich jak **średni**. |
    | **Nazwa metryki** | Wybierz metrykę, chcesz, aby operacja skalowania na podstawie, takich jak **wykorzystania pamięci podręcznej**. |
    | **Statystyka ziarna czasu** | Wybór między **średni**, **Minimum**, **maksymalna**, i **suma**. |
    | **Operator** | Wybierz odpowiednią opcję, takich jak **większa lub równa**. |
    | **Próg** | Wybierz odpowiednią wartość. Na przykład 80% wykorzystania pamięci podręcznej jest dobry punkt wyjścia. |
    | **Czas trwania** | Wybierz odpowiednią ilość czasu, sprawdź ponownie, gdy trwa obliczanie metryki systemu. Rozpocznij od domyślnego dziesięć minut. |
    | **Operacja** | Wybierz odpowiednią opcję na skalowanie w pionie lub w poziomie. |
    | **Liczba wystąpień** | Wybierz liczbę węzłów lub wystąpienia, które chcesz dodać lub usunąć po spełnieniu warunku metryki. |
    | **Czas ochładzania (minuty)** | Wybierz odpowiedni interwał między operacji skalowania. Rozpocznij od domyślnego pięć minut. |
    |  |  |

1. Wybierz pozycję **Dodaj**.

1. W **limity wystąpień** sekcji po lewej stronie, podaj wartości dla każdego ustawienia.

    | Ustawienie | Opis i wartość |
    | --- | --- | --- |
    | *Minimum* | Jest to liczba wystąpień, które klaster nie będą skalowane poniżej, niezależnie od użycia. |
    | *Maksymalna* | Jest to liczba wystąpień, które klaster nie będą skalowane powyżej, niezależnie od użycia. |
    | *Domyślne* | Domyślna liczba wystąpień, jeśli istnieje problem z odczytem metryk zasobów. |
    |  |  |

1. Wybierz pozycję **Zapisz**.

Operacja skalowania w poziomie, dla klastra Eksploratora danych usługi Azure został skonfigurowany. Dodaj inną regułę dla operacji skalowania na zewnątrz. Pozwoli to klastrowi skalowania dynamicznie na podstawie metryk, które określisz.

Jeśli potrzebujesz pomocy z klastrem skalowanie problemy, otwórz żądanie obsługi w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).