---
title: Konfigurowanie ustawień
titleSuffix: Azure Cognitive Services
description: Konfiguracja usługi zawiera, jak usługa traktuje korzyści, jak często usługa analizuje, jak często model jest retrained i jak dużo danych jest przechowywany.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 976830232453eee0993e64ac445c2e6a2f7e20ef
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478578"
---
# <a name="personalizer-settings"></a>Ustawienia personalizer

Konfiguracja usługi zawiera, jak usługa traktuje korzyści, jak często usługa analizuje, jak często model jest retrained i jak dużo danych jest przechowywany.

## <a name="create-personalizer-resource"></a>Utwórz zasób Personalizer

Utwórz zasób Personalizer dla każdej pętli opinii. 

1. Zaloguj się w [portalu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Poprzedni link spowoduje przejście do **Utwórz** stronę usługi Personalizer. 
1. Wprowadź nazwę usługi, wybierz subskrypcję, lokalizacji, warstwa cenowa i grupy zasobów.
1. Wybierz potwierdzenie i wybierz **Utwórz**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurowanie ustawień usługi w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Znajdź zasób Personalizer. 
1. W **zarządzania zasobami** zaznacz **ustawienia**.

    Przed opuszczeniem witryny Azure portal, skopiuj jeden z kluczy zasobu z **klucze** strony. Będzie on potrzebny do użycia [Personalizer SDK](https://go.microsoft.com/fwlink/?linkid=2092353).

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Konfigurowanie ustawień za wynagrodzeniem dla sprzężenia zwrotnego na podstawie przypadku użycia

Skonfiguruj ustawienia usługi dla swojej sprzężenia zwrotnego użytkowania nagrody. Zmiany w następujących ustawieniach spowoduje to zresetowanie bieżący model Personalizer i ponowne jej szkolenie przy użyciu ostatnie 2 dni danych:

![Skonfiguruj ustawienia nagradzania dla sprzężenia zwrotnego](media/settings/configure-model-reward-settings.png)

|Ustawienie|Przeznaczenie|
|--|--|
|Czas oczekiwania nagrody|Ustawia długość czasu, podczas których Personalizer będzie zbierać za wynagrodzeniem wartości rangi wywołania, już od momentu rangi wywołanie ma miejsce. Ta wartość jest ustawiana przez zadanie: "Jak długo ma Personalizer oczekiwać wywołania rewards?" Wszelkie nagradzania odebranych po tego okna będzie rejestrowane, ale nie są używane do uczenia.|
|Domyślne nagrody|Odebranie Brak wywołania nagrody za Personalizer przedziale czas oczekiwania nagradzania powiązanych z rangę wywołać, Personalizer przypisze nagradzania domyślne. Domyślnie i w większości przypadków nagradzania domyślny wynosi zero.|
|Agregacja za wynagrodzeniem|Jeśli wiele korzyści są odbierane tę samą rangę interfejsu API można wywoływać, ta metoda agregacji jest używana: **suma** lub **Najwcześniejsza**. Najwcześniejsza wybiera Najwcześniejsza wynik odebranych i odrzuca wszystkie pozostałe. Jest to przydatne, jeśli chcesz, aby nagradzania unikatowa wśród prawdopodobnie zduplikowanych wywołania. |

Po zmianie tych ustawień, upewnij się, że wybrano **Zapisz**.

### <a name="exploration-setting"></a>Ustawienie eksploracji 

Personalizacja jest możliwość odnajdywania nowych wzorców i dostosowania do zmiany zachowania użytkownika wraz z upływem czasu, eksplorując alternatyw. **Eksploracji** ustawienie określa, jaki procent rangi wywołania są odbierane z eksploracji. 

Zmiany tego ustawienia spowoduje to zresetowanie bieżący model Personalizer i ponowne jej szkolenie przy użyciu dane z ostatnich 2 dni.

![Ustawienie eksploracji Określa, jaki procent rangi wywołania są odbierane z eksploracji](media/settings/configure-exploration-setting.png)

Po zmianie tego ustawienia, pamiętaj wybrać **Zapisz**.

### <a name="model-update-frequency"></a>Częstotliwość aktualizacji modelu

Najnowszy model, uczony z wywołania interfejsu API za wynagrodzeniem z każdego aktywnego zdarzenia nie jest automatycznie używany przez wywołanie rangi Personalizer. **Częstotliwości aktualizacji modelu** ustawia się, jak często aktualizowane, model wyczerpaniu przez wywołanie rangi. 

Częstotliwości aktualizacji modelu wysokiej są przydatne w sytuacjach, w którym chcesz dokładnie śledzić zmiany w zachowania użytkowników. Przykłady obejmują lokacji z uruchomionymi na wiadomości na żywo, wirusowe zawartości lub ofert produktów na żywo. Częstotliwość 15-minutowy można użyć w następujących scenariuszach. W większości przypadków użycia stosuje się mniejszą częstotliwością aktualizacji. Co minutę częstotliwości aktualizacji są przydatne podczas debugowania kodu aplikacji przy użyciu Personalizer, wykonując pokazów lub interaktywnie testowania machine learning aspektów.

![Częstotliwość aktualizacji modelu określa, jak często jest retrained nowy model Personalizer.](media/settings/configure-model-update-frequency-settings.png)

Po zmianie tego ustawienia, pamiętaj wybrać **Zapisz**.

### <a name="data-retention"></a>Przechowywanie danych

**Okres przechowywania danych** Określa, ile dni Personalizer zachowuje dzienniki danych. Ostatnie dane dzienników są wymagane do wykonania [ocen w trybie offline](concepts-offline-evaluation.md), które są używane do Zmierz efektywność Personalizer i zoptymalizować nauka zasad.

Po zmianie tego ustawienia, pamiętaj wybrać **Zapisz**.

## <a name="export-the-personalizer-model"></a>Eksportowanie modelu Personalizer

W sekcji Zarządzanie zasobami dla **modelu i zasady**, przejrzyj tworzenia modelu i Data ostatniej aktualizacji i wyeksportować bieżącego modelu. Aby wyeksportować plik modelu w celu jego archiwizacji, można użyć witryny Azure portal lub interfejsów API Personalizer. 

![Eksportuj bieżący model Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importowanie i eksportowanie zasad nauki

W sekcji Zarządzanie zasobami dla **modelu i zasady**, zaimportować nowe zasady uczenia lub wyeksportować bieżące zasady uczenia.

## <a name="next-steps"></a>Kolejne kroki

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Dowiedz się więcej o dostępność w poszczególnych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
