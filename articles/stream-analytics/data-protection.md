---
title: Ochrona danych w usłudze Azure Stream Analytics
description: W tym artykule wyjaśniono, jak zaszyfrować dane prywatne używane przez zadanie usługi Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 1b3bdad0125b5bddbba20c8d807924fc3ea87e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299400"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrona danych w usłudze Azure Stream Analytics 

Usługa Azure Stream Analytics to w pełni zarządzana platforma jako usługa, która umożliwia tworzenie potoków analizy w czasie rzeczywistym. Wszystkie podnoszenia ciężkich, takich jak aprowizacji klastra, skalowanie węzłów w celu dostosowania użytkowania i zarządzanie wewnętrznych punktów kontrolnych, jest zarządzany w tle.

## <a name="encrypt-your-data"></a>Szyfruj swoje dane

Usługa Stream Analytics automatycznie wykorzystuje najlepsze w swojej klasie standardy szyfrowania w całej swojej infrastrukturze do szyfrowania i zabezpieczania danych. Możesz po prostu zaufać usługi Stream Analytics, aby bezpiecznie przechowywać wszystkie dane, aby nie martwić się o zarządzanie infrastrukturą.

Jeśli chcesz użyć kluczy zarządzanych przez klienta (CMK) do szyfrowania danych, możesz użyć własnego konta magazynu (ogólnego przeznaczenia V1 lub V2) do przechowywania dowolnych zasobów danych prywatnych, które są wymagane przez środowisko uruchomieniowe usługi Stream Analytics. Twoje konto pamięci masowej może być szyfrowane w razie potrzeby. Żaden z prywatnych zasobów danych nie jest przechowywany na stałe przez infrastrukturę usługi Stream Analytics. 

To ustawienie musi być skonfigurowane w czasie tworzenia zadań usługi Stream Analytics i nie można go zmodyfikować w całym cyklu życia zadania. Nie zaleca się modyfikowania lub usuwania miejsca, które jest używane przez analizę stream analytics. Jeśli usuniesz swoje konto magazynu, trwale usuniesz wszystkie prywatne zasoby danych, co spowoduje niepowodzenie zadania. 

Aktualizowanie lub obracanie kluczy do konta magazynu nie jest możliwe za pomocą portalu Usługi Stream Analytics. Klucze można zaktualizować za pomocą interfejsów API REST.


## <a name="configure-storage-account-for-private-data"></a>Konfigurowanie konta magazynu dla danych prywatnych 

Aby skonfigurować konto magazynu dla prywatnych zasobów danych, należy wykonać następujące kroki. Ta konfiguracja jest wykonana z zadania usługi Stream Analytics, a nie z konta magazynu.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób**. 

1. Wybierz **Analytics** >zadanie  **Analytics Stream Analytics**z listy wyników. 

1. Wypełnij stronę zadania usługi Stream Analytics o niezbędne szczegóły, takie jak nazwa, region i skala. 

1. Zaznacz pole wyboru z podaniem *Zabezpiecz wszystkie prywatne zasoby danych potrzebne w tym zadaniu na moim koncie Magazyn.*

1. Wybierz konto magazynu z subskrypcji. Należy zauważyć, że to ustawienie nie może być modyfikowane w całym cyklu życia zadania. 

   ![Ustawienia konta prywatnego przechowywania danych](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Przechowywane zasoby danych prywatnych

Wszystkie prywatne dane, które muszą być utrwalone przez usługa Stream Analytics, są przechowywane na twoim koncie magazynu. Przykłady prywatnych zasobów danych obejmują: 

* Kwerendy, które zostały autorstwa i ich powiązanych konfiguracji  

* Funkcje zdefiniowane przez użytkownika 

* Punkty kontrolne potrzebne w czasie wykonywania usługi Stream Analytics

* Migawki danych referencyjnych 

Przechowywane są również szczegóły połączenia zasobów, które są używane przez zadanie usługi Stream Analytics. Zaszyfruj swoje konto pamięci masowej, aby zabezpieczyć wszystkie dane. 

Aby pomóc Ci w spełnić obowiązki związane z przestrzeganiem przepisów w dowolnej regulowanej branży lub środowisku, możesz przeczytać więcej o [ofertach firmy Microsoft dotyczących zgodności z przepisami.](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md)
* [Opis danych wejściowych dla usługi Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Pojęcia dotyczące punktów kontrolnych i powtarzania w zadaniach usługi Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Korzystanie z danych referencyjnych dla odnośnych w usłudze Stream Analytics](stream-analytics-use-reference-data.md)
