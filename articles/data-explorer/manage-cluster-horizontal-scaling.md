---
title: Skalowanie klastra usługi Azure Eksplorator danych
description: W tym artykule opisano kroki, aby skalować w poziomie i skalowanie w klastrze usługi Azure Eksplorator danych zależności od zmieniających się żądanie.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571519"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Zarządzanie, klaster skalowanie w poziomie do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Jednak żądanie w klastrze nie można przewidzieć z dokładnością bezwzględne. Rozmiar klastra statyczne może prowadzić do niepełnego lub overutilization, z których żadna nie jest idealnym rozwiązaniem.

Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności zmieniającymi się żądanie. Istnieją dwa przepływy pracy na potrzeby skalowania: 
* Skalowanie w poziomie, nazywane również skalowaniem na zewnątrz i w.
* Skalowanie w pionie, nazywane również skalowaniem w górę i w dół.

W tym artykule opisano poziomy skalowania przepływu pracy.

Skalowanie w poziomie umożliwia skalowanie liczby wystąpień automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramy. Określ ustawienia automatycznego skalowania klastra w witrynie Azure portal, zgodnie z opisem w tym artykule.

## <a name="steps-to-configure-horizontal-scaling"></a>Kroki, aby skonfigurować skalowanie w poziomie

W witrynie Azure portal przejdź do zasobu klastra Eksploratora danych. W obszarze **ustawienia** nagłówka, wybierz **skalowanie w poziomie**. 

Wybierz metodę odpowiednią skalowania automatycznego: **Skalowanie ręczne**, **zoptymalizowane pod kątem skalowania automatycznego** lub **niestandardowego automatycznego skalowania**.

### <a name="manual-scale"></a>Skalowanie ręczne

Skalowanie ręczne jest ustawieniem domyślnym z tworzenia klastra. Oznacza to, że klaster ma pojemność klastra statycznych, który nie zmieni się automatycznie. Możesz wybrać pojemność statyczne, korzystając z paska i nie zmieni się aż do następnego będzie zmiany skalowania klastra w poziomie ustawienie.

   ![Skalowanie ręczne — metoda](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Zoptymalizowane automatycznego skalowania

Zoptymalizowane skalowania automatycznego jest metodą zalecaną skalowania automatycznego. Kroki, aby skonfigurować automatyczne skalowanie zoptymalizowany pod kątem:

1. Wybrana opcja automatycznego skalowania zoptymalizowany pod kątem i wybierz dolną granicę i górnego limitu ilości wystąpienia klastra, skalowania automatycznego zostanie wykonane między tymi limitami.
2. Kliknij pozycję Zapisz.

   ![Metoda zoptymalizowane automatycznego skalowania](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Po kliknięcie przycisku Zapisz mechanizm skalowania automatycznego zoptymalizowany pod kątem rozpocznie się pracę i jest akcje będą widoczne w dzienniku aktywności klastra. Ta metoda skalowania automatycznego jest Optymalizacja wydajności klastra i koszty: klaster rozpocznie się uzyskać dostęp do stanu niepełnego go będzie można skalować w, co pozostawienie tej samej i niższe koszty wydajności, a klaster zostanie uruchomiony do stanu overutilization, będzie on skalowany w poziomie do upewnij się, że działa poprawnie

### <a name="custom-autoscale"></a>Niestandardowe automatyczne skalowanie

Metoda niestandardowe automatyczne skalowanie pozwala skalowanie klastra dynamicznie na podstawie metryk, które określisz. Na poniższym rysunku przedstawiono przepływ i kroki, aby skonfigurować automatyczne skalowanie niestandardowe. Więcej szczegółów postępuj zgodnie z grafiki.

1. W **Nazwa ustawienia skalowania automatycznego** Podaj nazwę, taką jak *skalowalnego w poziomie: wykorzystanie w pamięci podręcznej*. 

   ![Reguły skalowania](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Aby uzyskać **tryb skalowania**, wybierz opcję **skalowania na podstawie metryki**. Ten tryb zapewnia dynamiczne skalowanie. Możesz również wybrać **Skaluj do określonej liczby wystąpień**.

3. Wybierz **+ Dodaj regułę**.

4. W **reguły skalowania** sekcji po prawej stronie, podaj wartości dla każdego ustawienia.

    **Kryteria**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Agregacja czasu** | Wybierz kryteria agregacji, takich jak **średni**. |
    | **Nazwa metryki** | Wybierz metrykę, chcesz, aby operacja skalowania na podstawie, takich jak **wykorzystania pamięci podręcznej**. |
    | **Statystyka ziarna czasu** | Wybór między **średni**, **Minimum**, **maksymalna**, i **suma**. |
    | **Operator** | Wybierz odpowiednią opcję, takich jak **większa lub równa**. |
    | **Próg** | Wybierz odpowiednią wartość. Na przykład o 80 procent wykorzystania pamięci podręcznej jest dobry punkt wyjścia. |
    | **Czas trwania (w minutach)** | Wybierz odpowiednią ilość czasu, sprawdź ponownie, gdy trwa obliczanie metryki systemu. Rozpocznij od domyślnego 10 minut. |
    |  |  |

    **Akcja**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Operacja** | Wybierz odpowiednią opcję na skalowanie w pionie lub w poziomie. |
    | **Liczba wystąpień** | Wybierz liczbę węzłów lub wystąpienia, które chcesz dodać lub usunąć po spełnieniu warunku metryki. |
    | **Czas ochładzania (minuty)** | Wybierz odpowiedni interwał między operacji skalowania. Rozpocznij od domyślnego pięć minut. |
    |  |  |

5. Wybierz pozycję **Dodaj**.

6. W **limity wystąpień** sekcji po lewej stronie, podaj wartości dla każdego ustawienia.

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Minimum** | Liczba wystąpień skalowanych klastra nie będzie poniżej, niezależnie od użycia. |
    | **Maksymalna** | Liczba wystąpień skalowanych klastra nie będzie powyżej, niezależnie od użycia. |
    | **Domyślne** | Domyślna liczba wystąpień. To ustawienie jest używane, jeśli występują problemy z odczytaniem metryk zasobów. |
    |  |  |

7. Wybierz pozycję **Zapisz**.

Operacja skalowania w poziomie, dla klastra Eksploratora danych usługi Azure został skonfigurowany. Dodaj inną regułę dla operacji skalowania na zewnątrz. Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksplorator danych platformy Azure](using-metrics.md)
* [Zarządzanie klastrem skalowanie w pionie](manage-cluster-vertical-scaling.md) dla odpowiedniego rozmiaru klastra.
