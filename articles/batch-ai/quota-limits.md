---
title: Usługi limity przydziału i limity dla usługi Azure Batch AI | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o domyślne usługi Azure Batch AI przydziały, limity i ograniczenia i zwiększa sposobu wysyłania żądania limitu przydziału
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 16032ec5ba1e613462f92b86281ce93153b70923
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409721"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Limity i przydziały usługi Batch AI

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Jak z innymi usługami platformy Azure istnieją limity określonych zasobów skojarzonych z usługą Batch AI. W przypadku usługi Batch AI, te limity są domyślne limity przydziału stosowane na poziomie subskrypcji dla każdego regionu, w których ta usługa jest [dostępne](https://azure.microsoft.com/global-infrastructure/services/). W tym artykule omówiono te wartości domyślne i jak mogą żądać przydziału zwiększa.

Należy pamiętać tych limitów przydziału podczas projektowania i skalowania w górę swoje zasoby usługi Batch AI. Na przykład jeśli klaster nie dociera do docelowej liczby węzłów, który określiłeś, następnie może osiągnięto limit rdzeni usługi Batch AI dla subskrypcji.

Jeśli planujesz uruchamiania obciążeń produkcji na sztucznej Inteligencji usługi Batch, może być konieczne zwiększenie co najmniej jeden z limitów przydziału ponad wartość domyślną.

> [!NOTE]
> Przydział to limit środków, nie gwarancję możliwości obliczeniowych. Jeśli masz zapotrzebowanie na pojemność, skontaktuj się z pomocą techniczną platformy Azure.
> 
> 

## <a name="resource-quotas"></a>Limity przydziałów zasobów

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Inne limity

Poniżej przedstawiono limity ograniczeniami, które nie mogą zostać przekroczone raz trafień.

| **Zasób** | **Maksymalny limit** |
| --- | --- |
| Maksymalna obszarów roboczych dla grupy zasobów | 800 |
| Rozmiar maksymalny klastra | 100 węzłów |
| Maksymalna MPI GPU przetwarza każdy węzeł | 1 – 4 |
| Maksymalna pracowników procesora GPU na węzeł | 1 – 4 |
| Maksymalny okres istnienia | 7 dni<sup>1</sup> |
| Parametr maksymalnego serwerów na węzeł | 1 |

<sup>1</sup> maksymalny okres istnienia odwołuje się do czasu uruchamiania i po zakończeniu rozpoczyna się zadanie. Zadania ukończone utrwalić przez czas nieokreślony; danych dotyczących zadań nieukończonych w ciągu maksymalnego okresu istnienia nie jest dostępny.

## <a name="view-batch-ai-quotas"></a>Wyświetl przydziały usługi Batch AI

Wyświetl bieżące limity przydziału subskrypcji usługi Batch AI, w [witryny Azure portal][portal].

1. W okienku po lewej stronie kliknij **wszystkich usług**. Następnie wyszukaj **usługi Batch AI** i kliknij, aby otworzyć usługę.
2. Kliknij pozycję **użycie i przydziały** menu usługi Batch AI.
3. Wybierz swoją subskrypcję, aby wyświetlić limitów przydziału.

## <a name="increase-a-batch-ai-cores-quota"></a>Zwiększ limit przydziału rdzeni usługi Batch AI

Wykonaj następujące kroki, aby zażądać przydziału zwiększyć dla subskrypcji usługi Batch AI przy użyciu [witryny Azure portal][portal]. 

1. W okienku po lewej stronie kliknij **wszystkich usług**. Następnie wyszukaj **usługi Batch AI** i kliknij, aby otworzyć usługę.
2. Kliknij pozycję **nowe żądanie obsługi** menu usługi Batch AI.
3. W **podstawy**:
   
    a. **Typ problemu** > **limitu przydziału**
   
    b. **Subskrypcja** > Wybierz swoją subskrypcję.
   
    c. **Typ limitu przydziału** > **usługi Batch AI**
   
    d. **Plan pomocy technicznej** > Wybierz plan pomocy technicznej.

    Kliknij przycisk **Dalej**.
4. W **Problem**:
   
    a. Wybierz **ważność** zgodnie z opisem w swojej [wpływ na działalność][support_sev].
   
    b. W **szczegóły limitu przydziału**, określ lokalizację, typ limitu przydziału i typu zasobu. Określ nowy limit, który chcesz zażądać. Kliknij przycisk **Zapisz i Kontynuuj**.

    c. Opcjonalnie — przekazywanie żadnych odpowiednich plików za pomocą więcej informacji na temat przyczynę wzrost.
   
    Kliknij przycisk **Dalej**.
5. W **informacje kontaktowe**:
   
    a. Wybierz **preferowana metoda kontaktu**.
   
    b. Sprawdź, a następnie wprowadź wymagane szczegóły dotyczące kontaktu.
   
    Kliknij przycisk **Utwórz**, aby przesłać żądanie pomocy technicznej.

Po przesłaniu żądania pomocy technicznej, pomocy technicznej platformy Azure skontaktuje. Kończenie żądania może potrwać do 2 dni roboczych.


## <a name="next-steps"></a>Kolejne kroki

Po staje się zapoznać się z limitów przydziału, zapoznaj się z następującymi artykułami wprowadzenie przy użyciu usługi Batch AI.

> [!div class="nextstepaction"]
> [Samouczek szybki start dla usługi Batch AI](quickstart-tensorflow-training-cli.md)
> [przepisy usługi Batch AI](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Dowiedz się więcej o zasobach usługi Batch AI](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: http://aka.ms/supportseverity