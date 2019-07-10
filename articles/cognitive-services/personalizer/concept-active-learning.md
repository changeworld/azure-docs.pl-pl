---
title: Aktywna nauka - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722529"
---
# <a name="active-learning-and-learning-policies"></a>Aktywne uczenie i nauki zasad 

Twoja aplikacja wywołuje interfejs API rangę, pojawi się pozycja zawartości. Logika biznesowa służy ta pozycja można określić, czy zawartość powinna być wyświetlany obraz do potrzeb użytkownika. Podczas wyświetlania zawartości w rankingu, to znaczy _active_ rangi zdarzeń. Gdy aplikacji nie są wyświetlane, randze spośród wszystkich dokumentów zawartości, czyli _nieaktywne_ rangi zdarzeń. 

Informacje dotyczące aktywnego rangi zdarzenia są zwracane do Personalizer. Te informacje są używane, aby kontynuować, uczenia modelu przez bieżące zasady uczenia.

## <a name="active-events"></a>Aktywne wydarzenia

Aktywne wydarzenia powinien zawsze pokazywane użytkownikowi i wywołań za wynagrodzeniem powinien nastąpić powrót do fazą uczenia. 

### <a name="inactive-events"></a>Nieaktywne zdarzenia 

Nieaktywne zdarzenia nie należy zmienić odpowiedni model, ponieważ użytkownik nie miał możliwość wyboru rangi zawartości.

## <a name="dont-train-with-inactive-rank-events"></a>Nie szkolenie przy użyciu nieaktywne rangi zdarzenia 

W przypadku niektórych aplikacji może być konieczne wywołania interfejsu API rangę, nie wiedząc o tym jeszcze, jeśli aplikacja wyświetli wyniki dla użytkownika. 

Dzieje się tak po:

* Możesz może być wstępnie renderowanie niektóre interfejsu użytkownika, który użytkownik może lub nie może pobrać się. 
* Aplikacja może wykonywania predykcyjne personalizacji, w którym rangi wywołań z kontekstem mniej w czasie rzeczywistym i swoje dane wyjściowe mogą lub nie mogą być używane przez aplikację. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Wyłącz aktywne uczenie nieaktywne rangi zdarzenia podczas wywołania rangi

Aby wyłączenie automatycznej nauki, należy wywołać ranga z `learningEnabled = False`.

Learning nieaktywne zdarzenia niejawnie została aktywowana, jeśli wyślesz nagradzania do rangi.

## <a name="learning-policies"></a>Nauka zasad

Nauka zasad określa, szczególnymi *hiperparametrów* szkolenia modelu. Dwa modele te same dane, skonfigurowanych pod kątem uczenia różne zasady, będą zachowywać się inaczej.

### <a name="importing-and-exporting-learning-policies"></a>Importowanie i eksportowanie zasad nauki

Można importować i eksportować learning pliki zasad w witrynie Azure portal. Dzięki temu można zapisać istniejące zasady, przetestuj je, zastąpić je i archiwum je z kontroli kodu źródłowego jako artefakty do użytku w przyszłości i inspekcji.

### <a name="learning-policy-settings"></a>Ustawienia zasad nauki

Ustawienia w **zasad Learning** nie są przeznaczone do można zmienić. Tylko zmienić ustawienia, gdy już poznasz ich wpływ na Personalizer. Zmiana ustawień bez tej wiedzy spowoduje, że efekty uboczne, w tym, co unieważniło Personalizer modeli.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porównywanie skuteczności nauka zasad

Możesz porównać jak różne zasady Learning czy wykonano względem historycznych danych w dziennikach Personalizer wykonując [ocen w trybie offline](concepts-offline-evaluation.md).

[Przekaż własne zasady uczenia](how-to-offline-evaluation.md) do porównania z obecnych zasad uczenia.

### <a name="discovery-of-optimized-learning-policies"></a>Odnajdywanie zasad zoptymalizowanych nauki

Personalizer można utworzyć bardziej zoptymalizowanego zasady nauki, w trakcie [oceny w trybie offline](how-to-offline-evaluation.md). Bardziej zoptymalizowanego zasada learning, która jest wyświetlana w ma być lepsze możliwości oceny w trybie offline, umożliwia uzyskanie lepsze wyniki, gdy są używane w trybie online w Personalizer.

Po utworzeniu zasad zoptymalizowanych learning można zastosować go bezpośrednio do Personalizer, więc Zamienia bieżące zasady natychmiast lub zapisać go do dalszej oceny i w przyszłości zdecyduj, czy chcesz odrzucić, Zapisz lub zastosować je później.