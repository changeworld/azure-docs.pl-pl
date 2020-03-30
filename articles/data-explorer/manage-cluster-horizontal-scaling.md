---
title: Zarządzanie skalowaniem w poziomie klastra (skalowanie w poziomie) w celu dopasowania do popytu w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki skalowania w poziomie i skalowania w klastrze usługi Azure Data Explorer na podstawie zmieniającego się popytu.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: ff7420619cffc2287ab8ff6332df605d56329549
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664137"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Zarządzanie skalowaniem w poziomie klastra (skalowanie w poziomie) w Eksploratorze danych platformy Azure w celu uwzględnienia zmieniającego się popytu

Odpowiednie rozmiary klastra ma kluczowe znaczenie dla wydajności Eksploratora danych platformy Azure. Rozmiar klastra statycznego może prowadzić do niepełnego wykorzystania lub nadmiernego wykorzystania, z których żaden nie jest idealny. Ponieważ nie można przewidzieć zapotrzebowania na klaster z absolutną dokładnością, lepiej *skalować* klaster, dodawać i usuwać pojemność i zasoby procesora CPU przy zmieniającym się zapotrzebowaniu. 

Istnieją dwa przepływy pracy do skalowania klastra usługi Azure Data Explorer: 
* Skalowanie poziome, nazywane również skalowaniem i wyskakiwaniem.
* [Skalowanie w pionie](manage-cluster-vertical-scaling.md), zwane również skalowaniem w górę i w dół.
W tym artykule opisano przepływ pracy skalowania poziomego.

## <a name="configure-horizontal-scaling"></a>Konfigurowanie skalowania poziomego

Za pomocą skalowania poziomego, można skalować liczbę wystąpień automatycznie, na podstawie wstępnie zdefiniowanych reguł i harmonogramów. Aby określić ustawienia skalowania automatycznego klastra:

1. W witrynie Azure portal przejdź do zasobu klastra usługi Azure Data Explorer. W obszarze **Ustawienia**wybierz pozycję **Skaluj w poziomie**. 

2. W oknie **Skalowanie w poziomie wyjętym** wybierz odpowiednią metodę skalowania **automatycznego: Skalowanie ręczne,** **Zoptymalizowane skalowanie automatyczne**lub **Niestandardowe skalowanie automatyczne**.

### <a name="manual-scale"></a>Skala ręczna

Skala ręczna jest ustawieniem domyślnym podczas tworzenia klastra. Klaster ma pojemność statyczną, która nie zmienia się automatycznie. Pojemność statyczną można wybrać za pomocą paska **liczby wystąpień.** Skalowanie klastra pozostaje w tym ustawieniu, dopóki nie wniesiesz kolejnej zmiany.

   ![Metoda skalowania ręcznego](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Zoptymalizowana skalowanie automatyczne (wersja zapoznawcza)

Zoptymalizowana skalowanie automatyczne jest zalecaną metodą skalowania automatycznego. Ta metoda optymalizuje wydajność i koszty klastra. Jeśli klaster zbliża się do stanu niepełnego wykorzystania, zostanie on przeskalowany. Ta akcja obniża koszty, ale utrzymuje poziom wydajności. Jeśli klaster zbliża się do stanu nadmiernego wykorzystania, zostanie on przeskalowany w poziomie, aby zachować optymalną wydajność. Aby skonfigurować zoptymalizowaną skalę automatyczną:

1. Wybierz **opcję Zoptymalizowana skalowanie automatyczne**. 

1. Wybierz minimalną liczbę wystąpień i maksymalną liczbę wystąpień. Automatyczne skalowanie klastra waha się między tymi dwiema liczbami, na podstawie obciążenia.

1. Wybierz **pozycję Zapisz**.

   ![Zoptymalizowana metoda skalowania automatycznego](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Zoptymalizowana automatyczna skala zaczyna działać. Jego akcje są teraz widoczne w dzienniku aktywności platformy Azure klastra.

#### <a name="logic-of-optimized-autoscale"></a>Logika zoptymalizowanej skalowania automatycznego 

**Skalowanie w poziomie**

Gdy klaster zbliża się do stanu nadmiernego wykorzystania, skaluj w poziomie, aby zachować optymalną wydajność. Skalowanie w poziomie będzie występować, gdy:
* Liczba wystąpień klastra jest niższa od maksymalnej liczby wystąpień zdefiniowanych przez użytkownika.
* Wykorzystanie pamięci podręcznej jest wysoki przez ponad godzinę.
* Procesor jest wysoki przez ponad godzinę.
* Wykorzystanie spożycia jest wysokie przez ponad godzinę.

> [!NOTE]
> Logika skalowania w poziomie nie uwzględnia obecnie metryki wykorzystania pozyskiwania. Jeśli ta metryka jest ważna dla przypadku użycia, użyj [niestandardowej skalowania automatycznego](#custom-autoscale).

**Skalowanie w pionie**

Gdy klaster zbliża się do stanu niepełnego wykorzystania, skaluj w celu obniżenia kosztów, ale zachowaj wydajność. Wiele metryk są używane do sprawdzenia, czy jest bezpieczny do skalowania w klastrze. Następujące reguły są oceniane co godzinę przez 6 godzin przed wykonaniem skali:
* Liczba wystąpień jest powyżej 2 i powyżej minimalnej liczby zdefiniowanych wystąpień.
* Aby upewnić się, że nie ma przeciążenia zasobów, przed wykonaniem skali w następujących metrykach należy zweryfikować następujące metryki: 
    * Wykorzystanie pamięci podręcznej nie jest wysokie
    * Procesor jest poniżej średniej 
    * Wykorzystanie spożycia jest poniżej średniej 
    * Wykorzystanie pozyskiwania przesyłania strumieniowego (jeśli używane jest przesyłanie strumieniowe) nie jest wysokie
    * Zachowaj przy życiu zdarzenia są powyżej zdefiniowanego minimum, przetwarzane prawidłowo i na czas.
    * Brak ograniczania zapytania 
    * Liczba kwerend, które nie powiodły się, jest niższa od zdefiniowanego minimum.

> [!NOTE]
> Skala w logice wymaga obecnie 7-dniowej oceny przed wdrożeniem zoptymalizowanej skali. Ocena ta odbywa się raz na 24 godziny. Jeśli potrzebna jest szybka zmiana, użyj [ręcznej skali](#manual-scale).

### <a name="custom-autoscale"></a>Niestandardowe skalowanie automatyczne

Korzystając z niestandardowej skali automatycznej, można dynamicznie skalować klaster na podstawie określonych metryk. Na poniższej ilustracji przedstawiono przepływ i kroki konfigurowania niestandardowej skali automatycznej. Więcej szczegółów następuje po grafice.

1. W polu **Nazwa ustawienia Skalowania automatycznego** wprowadź nazwę, taką jak *Skalowanie w poziomie: wykorzystanie pamięci podręcznej*. 

   ![Reguła skali](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. W **trybie skalowania**wybierz **opcję Skaluj na podstawie metryki**. Ten tryb zapewnia dynamiczne skalowanie. Można również wybrać **opcję Skaluj do określonej liczby wystąpień**.

3. Wybierz **+ Dodaj regułę**.

4. W sekcji **Reguła skalowania** po prawej stronie wprowadź wartości dla każdego ustawienia.

    **Kryteria**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Agregacja czasu** | Wybierz kryteria agregacji, takie jak **Średnia**. |
    | **Nazwa metryki** | Wybierz metrykę, na której ma być oparta operacja skalowania, na przykład **Wykorzystanie pamięci podręcznej**. |
    | **Statystyka ziarna czasu** | Wybierz **wartość**średnia, **minimalna,** **maksymalna**i **suma.** |
    | **Operator** | Wybierz odpowiednią opcję, taką jak **Większa lub równa**. |
    | **Próg** | Wybierz odpowiednią wartość. Na przykład w przypadku wykorzystania pamięci podręcznej 80 procent jest dobrym punktem wyjścia. |
    | **Czas trwania (w minutach)** | Wybierz odpowiednią ilość czasu dla systemu, aby spojrzeć wstecz podczas obliczania metryk. Zacznij od domyślnego 10 minut. |
    |  |  |

    **Akcja**

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Operacji** | Wybierz odpowiednią opcję skalowania lub skalowania w poziomie. |
    | **Liczba wystąpień** | Wybierz liczbę węzłów lub wystąpień, które chcesz dodać lub usunąć po spełnieniu warunku metryki. |
    | **Czas schładzania (minuty)** | Wybierz odpowiedni przedział czasu oczekiwania między operacjami skalowania. Zacznij od domyślnej wartości pięciu minut. |
    |  |  |

5. Wybierz pozycję **Dodaj**.

6. W sekcji **Limity instancji** po lewej stronie wprowadź wartości dla każdego ustawienia.

    | Ustawienie | Opis i wartość |
    | --- | --- |
    | **Minimalne** | Liczba wystąpień, które klastra nie będzie skalowane poniżej, niezależnie od wykorzystania. |
    | **Maksimum** | Liczba wystąpień, które klastra nie będzie skalować powyżej, niezależnie od wykorzystania. |
    | **Domyślny** | Domyślna liczba wystąpień. To ustawienie jest używane, jeśli występują problemy z odczytaniem metryk zasobów. |
    |  |  |

7. Wybierz **pozycję Zapisz**.

Teraz skonfigurowano skalowanie w poziomie dla klastra usługi Azure Data Explorer. Dodaj kolejną regułę skalowania pionowego. Jeśli potrzebujesz pomocy dotyczącej problemów ze skalowaniem klastra, [otwórz żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie wydajności, kondycji i użycia usługi Azure Data Explorer za pomocą metryk](using-metrics.md)
* [Zarządzanie skalowaniem pionowym klastra](manage-cluster-vertical-scaling.md) w celu odpowiedniego rozmiaru klastra.
