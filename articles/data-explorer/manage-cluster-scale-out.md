---
title: Skalowanie klastra usługi Azure Eksplorator danych
description: W tym artykule opisano kroki, aby skalować w poziomie i skalowanie w klastrze usługi Azure Eksplorator danych zależności od zmieniających się żądanie.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 24bbddd28943adc929fbaea456eeae8165db290c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758668"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Zarządzaj klastra skalowalnego w poziomie do uwzględnienia zmiennym obciążeniem

Odpowiednio rozmiaru klastra ma kluczowe znaczenie dla wydajności Eksploratora danych usługi Azure. Jednak żądanie w klastrze nie można przewidzieć z dokładnością bezwzględne. Rozmiar klastra statyczne może prowadzić do niepełnego lub overutilization, z których żadna nie jest idealnym rozwiązaniem.

Lepszym rozwiązaniem jest *skalowania* klastra, dodając i usuwając pojemności zmieniającymi się żądanie. Istnieją dwa przepływy pracy na potrzeby skalowania: skalowanie w górę i skalowania w poziomie. W tym artykule opisano przepływu pracy skalowalny w poziomie.

W tym artykule przedstawiono sposób zarządzania klastra skalowalnego w poziomie, znany także jako funkcja automatycznego skalowania. Skalowanie automatyczne pozwala skalować liczbę wystąpień, automatycznie na podstawie wstępnie zdefiniowanych reguł i harmonogramy. Określ ustawienia automatycznego skalowania klastra w witrynie Azure portal, zgodnie z opisem w tym artykule.

## <a name="steps-to-configure-autoscale"></a>Kroki, aby skonfigurować automatyczne skalowanie

W witrynie Azure portal przejdź do zasobu klastra Eksploratora danych. W obszarze **ustawienia** nagłówka, wybierz **skalowanie w poziomie**. Na **Konfiguruj** zaznacz **włączyć Skalowanie automatyczne**.

   ![Włącz skalowanie automatyczne](media/manage-cluster-scaling/enable-autoscale.png)

Na poniższym rysunku przedstawiono przepływ następnych krokach. Więcej szczegółów postępuj zgodnie z grafiki.

1. W **Nazwa ustawienia skalowania automatycznego** Podaj nazwę, taką jak *skalowalnego w poziomie: wykorzystanie w pamięci podręcznej*. 

   ![Reguła skalowania](media/manage-cluster-scaling/scale-rule.png)

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

Operacja skalowania w poziomie, dla klastra Eksploratora danych usługi Azure został skonfigurowany. Dodaj inną regułę dla operacji skalowania na zewnątrz. Ta konfiguracja umożliwia skalowanie klastra dynamicznie na podstawie metryk, które określisz.

Możesz również [Zarządzanie klastrem skalowanie w górę](manage-cluster-scale-up.md) dla odpowiedniego rozmiaru klastra.

Jeśli potrzebujesz pomocy w przypadku problemów skalowanie klastra [Otwórz żądanie obsługi](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksplorator danych platformy Azure](using-metrics.md)
