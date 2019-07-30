---
title: Konfigurowanie ustawień — Personalizacja
titleSuffix: Azure Cognitive Services
description: Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f0ccf0e480fa57e0ffdfc94ca35cfaceded37a0b
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663893"
---
# <a name="personalizer-settings"></a>Ustawienia personalizacji

Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.

## <a name="create-personalizer-resource"></a>Utwórz zasób personalizacji

Utwórz zasób personalizacji dla każdej pętli opinii. 

1. Zaloguj się w [portalu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Poprzedni link prowadzi do strony **Tworzenie** dla usługi personalizacji. 
1. Wprowadź nazwę usługi, wybierz subskrypcję, lokalizację, warstwę cenową i grupę zasobów.
1. Wybierz potwierdzenie i wybierz pozycję **Utwórz**.

## <a name="configure-service-settings-in-the-azure-portal"></a>Skonfiguruj ustawienia usługi w Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Znajdź zasób personalizowania. 
1. W sekcji **Zarządzanie zasobami** wybierz pozycję **Ustawienia**.

    Przed opuszczeniem Azure Portal Skopiuj jeden z kluczy zasobów ze strony **klucze** . Będzie on potrzebny do korzystania z [zestawu SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)programu Personalizacja.

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>Skonfiguruj ustawienia nagrody dla pętli opinii na podstawie przypadku użycia

Skonfiguruj ustawienia usługi dla używanej przez pętlę opinii. Zmiany następujących ustawień spowodują zresetowanie bieżącego modelu personalizowania i ponowne nauczenie go przy użyciu ostatnich 2 dni danych:

![Skonfiguruj ustawienia nagrody dla pętli opinii](media/settings/configure-model-reward-settings.png)

|Ustawienie|Cel|
|--|--|
|Nagradzany czas oczekiwania|Ustawia długość czasu, przez który program Personalizuj będzie zbierać wartości nagrody dla wywołania rangi, rozpoczynając od momentu wywołania rangi. Ta wartość jest ustawiana przez pytanie: "Jak długo Personalizacja ma czekać na nagrody?" Wszystkie nagrody przychodzące po tym oknie będą rejestrowane, ale nie są używane do uczenia się.|
|Wynagrodzenie domyślne|Jeśli Personalizacja nie otrzymuje żadnego nadawania w czasie oczekiwania w przedziale czasowym, skojarzonym z wywołaniem rangi, Personalizowanie przypisze domyślne wynagrodzenie. Domyślnie, a w większości scenariuszy wartość domyślna to zero.|
|Agregacja nagrody|Jeśli odebrane zostanie wiele nagrody dla tego samego wywołania interfejsu API rangi, używana jest ta metoda agregacji: **sum** lub Najwcześniejsza. Najwcześniej wybierany jest najwcześniejszy wynik otrzymany i odrzuca resztę. Jest to przydatne, jeśli chcesz uzyskać unikatowe wynagrodzenie między możliwymi duplikatami wywołań. |

Po zmianie tych ustawień upewnij się, że wybrano pozycję **Zapisz**.

### <a name="exploration-setting"></a>Ustawienie eksploracji 

Personalizacja jest w stanie wykrywać nowe wzorce i dostosowywać je do zmian w czasie, przedając alternatywy. Ustawienie **Eksploracja** określa, jaki procent wywołań rangi jest odpowiedzią na eksplorację. 

Zmiany tego ustawienia spowodują zresetowanie bieżącego modelu personalizowania i ponowne nauczenie go z ostatnich 2 dni.

![Ustawienie eksploracji określa, jaki procent wywołań rangi jest odpowiedzią na eksplorację](media/settings/configure-exploration-setting.png)

Po zmianie tego ustawienia upewnij się, że wybrano pozycję **Zapisz**.

### <a name="model-update-frequency"></a>Częstotliwość aktualizacji modelu

Najnowszy model, przeszkolony z nagradzania wywołań interfejsu API z każdego aktywnego zdarzenia, nie jest automatycznie używany przez funkcję personalizacji wywołania rangi. **Częstotliwość aktualizacji modelu** określa, jak często model używany przez tę rangę zostanie zaktualizowany. 

Wysokie częstotliwości aktualizacji modelu są przydatne w sytuacjach, w których chcesz ściśle śledzić zmiany zachowań użytkownika. Przykłady obejmują witryny, które są uruchamiane na żywo wiadomości, zawartości wirusowej lub licytacji produktu na żywo. W tych scenariuszach można użyć częstotliwości 15 minut. W większości przypadków użycia niższa częstotliwość aktualizacji jest skuteczna. Częstotliwości aktualizacji o pojedynczej minucie są przydatne podczas debugowania kodu aplikacji przy użyciu narzędzia do personalizacji, wykonywania pokazów lub interaktywnego testowania aspektów uczenia maszynowego.

![Częstotliwość aktualizacji modelu określa, jak często nowy model personalizowania jest przeszkolny.](media/settings/configure-model-update-frequency-settings.png)

Po zmianie tego ustawienia upewnij się, że wybrano pozycję **Zapisz**.

### <a name="data-retention"></a>Przechowywanie danych

**Okres przechowywania danych** określa, ile dni personalizacji ma przechowywać dzienniki danych. Dzienniki danych przeszłych są wymagane do przeprowadzenia [oceny w trybie offline](concepts-offline-evaluation.md), które służą do mierzenia skuteczności działania programu personalizowania i optymalizowania zasad uczenia.

Po zmianie tego ustawienia upewnij się, że wybrano pozycję **Zapisz**.

## <a name="export-the-personalizer-model"></a>Eksportowanie modelu personalizowania

Z sekcji Zarządzanie zasobami dla **modelu i zasad**przejrzyj temat Tworzenie modelu i Data ostatniej aktualizacji, a następnie wyeksportuj bieżący model. Za pomocą Azure Portal lub interfejsów API personalizacji można eksportować plik modelu na potrzeby archiwizowania. 

![Eksportuj bieżący model personalizacji](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>Importowanie i eksportowanie zasad uczenia

Z sekcji Zarządzanie zasobami dotyczącymi **modelu i zasad**należy zaimportować nowe zasady uczenia lub wyeksportować bieżące zasady nauki.

## <a name="next-steps"></a>Następne kroki

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[Informacje o dostępności regionów](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
