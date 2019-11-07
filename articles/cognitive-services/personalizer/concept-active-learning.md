---
title: Zdarzenia aktywne i nieaktywne — Personalizacja
titleSuffix: Azure Cognitive Services
description: W tym artykule omówiono użycie aktywnych i nieaktywnych zdarzeń, ustawień uczenia i zasad uczenia w ramach usługi personalizacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681872"
---
# <a name="active-and-inactive-events"></a>Zdarzenia aktywne i nieaktywne

Gdy aplikacja wywołuje interfejs API rangi, otrzymujesz akcję, którą aplikacja powinna wyświetlić w polu **rewardActionId** .  Od tego momentu Personalizacja oczekuje wywołania płatnego, które ma ten sam eventId. Wynik nagrody będzie używany do uczenia modelu dla przyszłych wywołań rangi. Jeśli dla eventId nie zostanie odebrane żadne wynagrodzenie, zostanie zastosowane wynagrodzenie domyślne. Domyślne nagrody są ustawiane w Azure Portal.

W niektórych scenariuszach aplikacja może wymagać wywołania rangi, aby nie wie, czy wynik zostanie użyty lub wyświetlony użytkownikowi. Może się tak zdarzyć w sytuacjach, gdy na przykład renderowanie strony z podwyższoną zawartością zostanie zastąpione kampanią marketingową. Jeśli wynik wywołania rangi nigdy nie był używany i użytkownik go nigdy nie wywołał, nie wysyłaj odpowiedniego wywołania.

Zwykle te scenariusze są wykonywane, gdy:

* Korzystasz z interfejsu użytkownika, który może zostać wyświetlony przez użytkownika lub może się nie powieść. 
* Aplikacja wykonuje personalizację predykcyjną, w której wywołania rangi są wykonywane z niewielkim kontekstem w czasie rzeczywistym, a aplikacja może nie używać danych wyjściowych. 

W takich przypadkach należy użyć personalizacji do wywołania rangi, żądając zdarzenia jako _nieaktywnego_. Personalizacja nie będzie oczekiwać dla tego zdarzenia i nie będzie stosowała domyślnego wynagrodzenia. W dalszej części logiki biznesowej, jeśli aplikacja używa informacji z wywołania rangi, po prostu _Aktywuj_ zdarzenie. Gdy tylko zdarzenie jest aktywne, Personalizacja oczekuje na odłożenie zdarzenia. Jeśli w interfejsie API nagradzania nie jest wykonywane żadne jawne wywołanie, Personalizacja stosuje wynagrodzenie domyślne.

## <a name="inactive-events"></a>Zdarzenia nieaktywne

Aby wyłączyć szkolenie dla zdarzenia, zadzwoń do rangi przy użyciu `learningEnabled = False`. W przypadku nieaktywnego zdarzenia uczenie jest niejawnie aktywowane w przypadku wysłania nagrody dla eventId lub wywołania interfejsu API `activate` dla tego eventId.

## <a name="learning-settings"></a>Ustawienia uczenia

Ustawienia uczenia określają *Parametry* szkolenia modelu. Dwa modele tych samych danych, które są przeszkolone przy użyciu różnych ustawień uczenia, będą kończyć się różnymi.

### <a name="import-and-export-learning-policies"></a>Importowanie i eksportowanie zasad uczenia

Można importować i eksportować pliki zasad uczenia z Azure Portal. Ta metoda służy do zapisywania istniejących zasad, testowania ich, zastępowania i archiwizowania ich w kontroli kodu źródłowego jako artefaktów do przyszłego odwołania i inspekcji.

### <a name="understand-learning-policy-settings"></a>Informacje o ustawieniach zasad nauki

Ustawienia zasad nauki nie są przeznaczone do zmiany. Zmień ustawienia tylko wtedy, gdy zrozumiesz, jak mają wpływ na personalizowanie. Bez tej wiedzy mogą wystąpić problemy, w tym unieważnienie modeli personalizacji.

### <a name="compare-learning-policies"></a>Porównanie zasad uczenia

Można porównać, w jaki sposób różne zasady uczenia są wykonywane względem wcześniejszych danych w dziennikach personalizacji przez wykonywanie [ocen w trybie offline](concepts-offline-evaluation.md).

[Przekaż własne zasady uczenia](how-to-offline-evaluation.md) , aby porównać je z bieżącymi zasadami nauki.

### <a name="optimize-learning-policies"></a>Optymalizowanie zasad uczenia

Personalizowanie może utworzyć zoptymalizowane zasady uczenia w [ocenie w trybie offline](how-to-offline-evaluation.md). Zoptymalizowane zasady uczenia, które mają lepsze korzyści wynikające z oceny w trybie offline, będą zwracać lepsze wyniki, gdy są używane w trybie online w programie personalizacji.

Po zoptymalizowaniu zasad uczenia można zastosować je bezpośrednio do personalizacji, aby natychmiast zastąpić bieżące zasady. Można też zapisać zoptymalizowane zasady do dalszej oceny, a następnie zdecydować, czy należy je odrzucić, zapisać lub zastosować.
