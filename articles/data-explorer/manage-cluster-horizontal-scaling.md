---
title: Zarządzanie skalowaniem w poziomie klastra (skalowanie w dół) w usłudze Azure Eksplorator danych w celu uwzględnienia zmiany zapotrzebowania
description: W tym artykule opisano kroki umożliwiające skalowanie w poziomie i skalowanie w klastrze usługi Azure Eksplorator danych w oparciu o zmieniające się zapotrzebowanie.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: eb204701b42436a5ae95bac97ed6fd97cf272860
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561862"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Zarządzanie skalowaniem w poziomie klastra (skalowanie w dół) w usłudze Azure Eksplorator danych w celu uwzględnienia zmiany zapotrzebowania

Odpowiednio ustalanie wielkości klastra ma kluczowe znaczenie dla wydajności Eksplorator danych platformy Azure. Statyczny rozmiar klastra może prowadzić do użycia lub nadmiernego użycia, nie jest idealnym rozwiązaniem.

Ze względu na to, że zapotrzebowanie w klastrze nie może być przewidywane z dokładnością bezwzględną, lepiej jest *skalować* klaster, dodawać i usuwać zasoby procesora CPU i zmieniać zapotrzebowanie. 

Istnieją dwa przepływy pracy umożliwiające skalowanie klastra Eksplorator danych platformy Azure: 

* Skalowanie w poziomie, nazywane również skalowaniem do wewnątrz i na zewnątrz.
* [Skalowanie w pionie](manage-cluster-vertical-scaling.md), nazywane również skalowaniem w górę i w dół.

W tym artykule opisano przepływ pracy skalowania w poziomie.

## <a name="configure-horizontal-scaling"></a>Konfiguruj skalowanie w poziomie

Używając skalowania w poziomie, można automatycznie skalować liczbę wystąpień na podstawie wstępnie zdefiniowanych reguł i harmonogramów. Aby określić ustawienia automatycznego skalowania dla klastra:

1. W Azure Portal przejdź do zasobu klastra usługi Azure Eksplorator danych. W obszarze **Ustawienia**wybierz pozycję **Skaluj w poziomie**. 

2. W oknie **skalowanie w poziomie** wybierz metodę automatycznego skalowania: **skalowanie ręczne**, **zoptymalizowane automatyczne skalowanie**lub **niestandardowe Skalowanie automatyczne**.

### <a name="manual-scale"></a>Skalowanie ręczne

Skalowanie ręczne jest ustawieniem domyślnym podczas tworzenia klastra. Klaster ma statyczną pojemność, która nie zmienia się automatycznie. Możesz wybrać pojemność statyczną za pomocą paska **Liczba wystąpień** . Skalowanie klastra pozostaje w tym ustawieniu do momentu wprowadzenia kolejnej zmiany.

   ![Metoda skalowania ręcznego](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Zoptymalizowane automatyczne skalowanie (wersja zapoznawcza)

Zoptymalizowane automatyczne skalowanie jest zalecaną metodą automatycznego skalowania. Ta metoda optymalizuje wydajność i koszty klastra. Jeśli klaster zbliża się do stanu pod kątem użycia, zostanie on przeskalowany w. Ta akcja obniża koszty, ale utrzymuje poziom wydajności. Jeśli klaster zbliża się do nadmiernego użycia, będzie skalowany w poziomie w celu zapewnienia optymalnej wydajności. Aby skonfigurować optymalizację skalowania automatycznego:

1. Wybierz pozycję **zoptymalizowane automatyczne skalowanie**. 

1. Wybierz minimalną liczbę wystąpień i maksymalną liczbę wystąpień. Automatyczne skalowanie klastra między tymi dwoma liczbami jest zależne od obciążenia.

1. Wybierz pozycję **Zapisz**.

   ![Zoptymalizowana metoda automatycznego skalowania](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Zoptymalizowana funkcja automatycznego skalowania jest uruchamiana. Jego działania są teraz widoczne w dzienniku aktywności platformy Azure klastra.

### <a name="custom-autoscale"></a>Niestandardowe Skalowanie automatyczne

Za pomocą niestandardowego skalowania automatycznego można skalować klaster dynamicznie na podstawie określonych metryk. Poniższa ilustracja przedstawia przepływ i kroki konfigurowania niestandardowego skalowania automatycznego. Więcej szczegółów znajduje się na ilustracji.

1. W polu **Nazwa ustawienia automatycznego skalowania** wprowadź nazwę, na przykład *skalowalny w poziomie: użycie pamięci podręcznej*. 

   ![Reguła skalowania](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. W obszarze **tryb skalowania**wybierz pozycję **skalowanie na podstawie metryki**. Ten tryb zapewnia dynamiczne skalowanie. Możesz również wybrać **skalowanie do określonej liczby wystąpień**.

3. Wybierz pozycję **+ Dodaj regułę**.

4. W sekcji **reguła skalowania** po prawej stronie Wprowadź wartości dla każdego ustawienia.

    **Kryteria**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Agregacja czasu** | Wybierz kryteria agregacji, takie jak **średnia**. |
    | **Nazwa metryki** | Wybierz metrykę, na której ma być oparta Operacja skalowania, na przykład **użycie pamięci podręcznej**. |
    | **Statystyka ziarna czasu** | Wybierz między **orednimi**, **minimum**, **maksimum**i **sum**. |
    | **Zakład** | Wybierz odpowiednią opcję, **na przykład większą lub równą**. |
    | **Próg** | Wybierz odpowiednią wartość. Na przykład w przypadku użycia pamięci podręcznej 80 procent jest dobrym punktem początkowym. |
    | **Czas trwania (w minutach)** | Wybierz odpowiedni czas na wyszukanie systemu podczas obliczania metryk. Rozpocznij od wartości domyślnej 10 minut. |
    |  |  |

    **Akcja**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Operacja** | Wybierz odpowiednią opcję, aby skalować w poziomie lub na zewnątrz. |
    | **Liczba wystąpień** | Wybierz liczbę węzłów lub wystąpień, które chcesz dodać lub usunąć po spełnieniu warunku metryki. |
    | **Chłodnie (minuty)** | Wybierz odpowiedni przedział czasu, aby oczekiwać między operacjami skalowania. Zacznij od domyślnej wartości pięciu minut. |
    |  |  |

5. Wybierz pozycję **Dodaj**.

6. W sekcji **limity wystąpień** po lewej stronie Wprowadź wartości dla każdego ustawienia.

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Minimalny** | Liczba wystąpień, które nie będą skalowane przez klaster, niezależnie od użycia. |
    | **Długość** | Liczba wystąpień, które nie będą skalowane przez klaster, niezależnie od użycia. |
    | **Domyślne** | Domyślna liczba wystąpień. To ustawienie jest używane w przypadku problemów z odczytem metryk zasobów. |
    |  |  |

7. Wybierz pozycję **Zapisz**.

Skalowanie poziome dla klastra usługi Azure Eksplorator danych zostało już skonfigurowane. Dodaj kolejną regułę dla skalowania w pionie. Jeśli potrzebujesz pomocy w rozwiązywaniu problemów ze skalowaniem klastra, [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w Azure Portal.

## <a name="next-steps"></a>Następne kroki

* [Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk](using-metrics.md)

* [Zarządzanie skalowaniem](manage-cluster-vertical-scaling.md) w poziomie klastra w celu odpowiedniego ustalania rozmiarów klastra.
