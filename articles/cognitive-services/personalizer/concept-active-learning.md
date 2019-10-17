---
title: Zdarzenia aktywne i nieaktywne — Personalizacja
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429037"
---
# <a name="active-and-inactive-events"></a>Zdarzenia aktywne i nieaktywne

Gdy aplikacja wywołuje interfejs API rangi, otrzymujesz akcję, którą aplikacja powinna wyświetlić w polu rewardActionId.  Od tego momentu Personalizacja będzie oczekiwać, że nastąpi wywołanie z tym samym eventId. Wynik nagrody będzie używany do uczenia modelu, który będzie używany w przyszłych wywołaniach rangi. Jeśli dla eventId nie zostanie odebrane żadne wynagrodzenie, zostanie zastosowane defaul wynagrodzenie. Domyślne nagrody są ustalane w witrynie Azure Portal.

W niektórych przypadkach może być konieczne wywołanie przez aplikację rangi oddzwonu, nawet jeśli wynik zostanie użyty lub displayedn do użytkownika. Może się tak zdarzyć w sytuacjach, gdy na przykład strona renderowania podwyższonej zawartości zostanie zastąpiona kampanią marketingową. Jeśli wynik wywołania rangi nie był nigdy używany i użytkownik nie zobaczy go, może być niewłaściwy do uczenia się z jakimkolwiek wynagrodzeniem, w ogóle lub w inny sposób.
Zwykle zdarza się to, gdy:

* Może być wstępnie wyrenderowany niektóre elementy interfejsu użytkownika, które użytkownik może lub może nie zobaczyć. 
* Aplikacja może przeprowadzać personalizację predykcyjną, w której są wykonywane wywołania rangi z mniejszym kontekstem w czasie rzeczywistym, a ich dane wyjściowe mogą być używane przez aplikację. 

W takich przypadkach prawidłowym sposobem korzystania z programu Personalizuj jest wywoływanie rangi żądającej zdarzenia jako _nieaktywnego_. Personalizacja nie będzie oczekiwać odnoszącego się do tego zdarzenia i nie będzie stosowała wynagrodzenia domyślnego. Letr w logice biznesowej, jeśli aplikacja używa informacji z wywołania rangi, wystarczy _aktywować_ zdarzenie. Od momentu, gdy zdarzenie jest aktywne, Personalizacja będzie oczekiwać odzyskania dla zdarzenia lub zastosować wynagrodzenie domyślne, jeśli żadne jawne wywołanie nie zostanie wykonane do interfejsu API nagradzania.

## <a name="get-inactive-events"></a>Pobierz zdarzenia nieaktywne

Aby wyłączyć szkolenie dla zdarzenia, zadzwoń do rangi o `learningEnabled = False`.

Uczenie się dla nieaktywnego zdarzenia jest niejawnie aktywowane w przypadku wysłania nagrody dla eventId lub wywołania interfejsu API `activate` dla tego eventId.

## <a name="learning-settings"></a>Ustawienia uczenia

Ustawienia uczenia określają konkretne *Parametry* szkolenia modeli. Dwa modele tych samych danych, przeszkolonych na różnych ustawieniach uczenia, zakończą się w inny sposób.

### <a name="import-and-export-learning-policies"></a>Importowanie i eksportowanie zasad uczenia

Możesz importować i eksportować pliki zasad nauki z Azure Portal. Pozwala to na zapisanie istniejących zasad, ich testowanie i zastępowanie oraz archiwizowanie ich w kontroli kodu źródłowego jako artefaktów do przyszłego odwołania i inspekcji.

### <a name="learning-policy-settings"></a>Ustawienia zasad uczenia

Ustawienia **zasad uczenia** nie są przeznaczone do zmiany. Zmień ustawienia tylko wtedy, gdy zrozumiesz, jak ma to wpływ na personalizowanie. Zmiana ustawień bez tej wiedzy spowoduje skutki uboczne, w tym unieważnienie modeli personalizacji.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porównanie skuteczności zasad uczenia

Można porównać różne zasady uczenia w odniesieniu do przeszłych danych w dziennikach personalizacji, przeprowadzając [oceny w trybie offline](concepts-offline-evaluation.md).

[Przekaż własne zasady uczenia](how-to-offline-evaluation.md) , aby porównać je z bieżącymi zasadami nauki.

### <a name="discovery-of-optimized-learning-policies"></a>Odnajdywanie zoptymalizowanych zasad uczenia

Personalizowanie może utworzyć bardziej zoptymalizowane zasady uczenia podczas przeprowadzania [oceny w trybie offline](how-to-offline-evaluation.md). Bardziej zoptymalizowane zasady uczenia, które są widoczne na potrzeby oceny w trybie offline, dają lepsze wyniki w przypadku korzystania z Internetu w programie personalizacji.

Po utworzeniu zoptymalizowanych zasad uczenia można zastosować je bezpośrednio do personalizacji, aby zastąpić bieżące zasady natychmiast. można też zapisać je do dalszej oceny i zdecydować, czy należy je odrzucić, zapisać lub zastosować później.
