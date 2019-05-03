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
ms.openlocfilehash: 0cd08e1191c68c57975d3e68648134925155e7f2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027222"
---
# <a name="personalizer-settings"></a>Ustawienia personalizer

Konfiguracja usługi zawiera, jak usługa traktuje korzyści, jak często usługa analizuje, jak często model jest retrained i jak dużo danych jest przechowywany.

Utwórz zasób Personalizer dla każdej pętli opinii. 

## <a name="configure-service-settings-in-the-azure-portal"></a>Konfigurowanie ustawień usługi w witrynie Azure portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Znajdź zasób Personalizer. 
1. W **zarządzania zasobami** zaznacz **ustawienia**.

    ![Zaloguj się do Portalu Azure. Znajdź zasób Personalizer. W sekcji Zarządzanie zasobów wybierz ustawienia.](media/settings/how-to-configure.png)

### <a name="reward-settings-for-the-feedback-loop"></a>Ustawienia nagradzania dla sprzężenia zwrotnego

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

**Częstotliwość aktualizacji modelu** ustawia się, jak często jest retrained nowy model Personalizer. 

![Częstotliwość aktualizacji modelu określa, jak często jest retrained nowy model Personalizer.](media/settings/configure-model-update-frequency-settings.png)

Po zmianie tego ustawienia, pamiętaj wybrać **Zapisz**.

### <a name="data-retention"></a>Przechowywanie danych

**Okres przechowywania danych** Określa, ile dni Personalizer zachowuje dzienniki danych. Ostatnie dane dzienników są wymagane do wykonania [ocen w trybie offline](concepts-offline-evaluation.md), które są używane do Zmierz efektywność Personalizer i zoptymalizować nauka zasad.

Po zmianie tego ustawienia, pamiętaj wybrać **Zapisz**.

## <a name="export-the-personalizer-model"></a>Eksportowanie modelu Personalizer

W sekcji Zarządzanie zasobami dla **modelu i zasady**, przejrzyj tworzenia modelu i Data ostatniej aktualizacji i wyeksportować bieżącego modelu.

![Eksportuj bieżący model Personalizer](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importowanie i eksportowanie zasad nauki

W sekcji Zarządzanie zasobami dla **modelu i zasady**, zaimportować nowe zasady uczenia lub wyeksportować bieżące zasady uczenia.

## <a name="next-steps"></a>Kolejne kroki

[Uczenia przez wzmacnianie](concepts-reinforcement-learning.md) 
