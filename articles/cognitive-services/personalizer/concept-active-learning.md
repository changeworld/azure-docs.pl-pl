---
title: Active Learning — Personalizacja
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
ms.openlocfilehash: 8c1579be3d11ae14ca45ee861de2d4f705e5d62c
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663709"
---
# <a name="active-learning-and-learning-policies"></a>Aktywne zasady uczenia i uczenia 

Gdy aplikacja wywołuje interfejs API rangi, otrzymujesz rangę zawartości. Logika biznesowa może korzystać z tej rangi, aby określić, czy zawartość ma być wyświetlana użytkownikowi. Gdy zostanie wyświetlona sklasyfikowana zawartość, która jest _aktywnym_ zdarzeniem rangi. Gdy aplikacja nie wyświetla tej sklasyfikowanej zawartości, to jest zdarzenie nieaktywnej rangi. 

Aktywne informacje o zdarzeniu rangi są zwracane do personalizacji. Te informacje służą do dalszego szkolenia modelu za pomocą bieżących zasad nauki.

## <a name="active-events"></a>Aktywne zdarzenia

Aktywne zdarzenia powinny zawsze być widoczne dla użytkownika, a połączenie płatne powinno zostać zwrócone, aby zamknąć pętlę uczenia. 

### <a name="inactive-events"></a>Zdarzenia nieaktywne 

Nieaktywne zdarzenia nie powinny zmieniać modelu bazowego, ponieważ użytkownik nie miał możliwości wyboru z sklasyfikowanej zawartości.

## <a name="dont-train-with-inactive-rank-events"></a>Nie pouczenie z nieaktywnymi zdarzeniami rangi 

W przypadku niektórych aplikacji może być konieczne wywołanie interfejsu API rangi bez znajomości tego, czy aplikacja będzie wyświetlać wyniki dla użytkownika. 

Dzieje się tak w przypadku:

* Może być wstępnie wyrenderowany niektóre elementy interfejsu użytkownika, które użytkownik może lub może nie zobaczyć. 
* Aplikacja może przeprowadzać personalizację predykcyjną, w której są wykonywane wywołania rangi z mniejszym kontekstem w czasie rzeczywistym, a ich dane wyjściowe mogą być używane przez aplikację. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Wyłącz aktywną naukę dla nieaktywnych zdarzeń rangi podczas wywołania rangi

Aby wyłączyć automatyczną naukę, zadzwoń do rangi z `learningEnabled = False`.

Uczenie się dla nieaktywnego zdarzenia jest niejawnie aktywowane w przypadku wysłania nagrody dla rangi.

## <a name="learning-policies"></a>Zasady uczenia

Zasady uczenia określają konkretne *Parametry* szkolenia modeli. Dwa modele tych samych danych, przeszkolonych dla różnych zasad uczenia się, zachowywać się inaczej.

### <a name="importing-and-exporting-learning-policies"></a>Importowanie i eksportowanie zasad uczenia

Możesz importować i eksportować pliki zasad nauki z Azure Portal. Pozwala to na zapisanie istniejących zasad, ich testowanie i zastępowanie oraz archiwizowanie ich w kontroli kodu źródłowego jako artefaktów do przyszłego odwołania i inspekcji.

### <a name="learning-policy-settings"></a>Ustawienia zasad uczenia

Ustawienia **zasad uczenia** nie są przeznaczone do zmiany. Zmień ustawienia tylko wtedy, gdy zrozumiesz, jak ma to wpływ na personalizowanie. Zmiana ustawień bez tej wiedzy spowoduje skutki uboczne, w tym unieważnienie modeli personalizacji.

### <a name="comparing-effectiveness-of-learning-policies"></a>Porównanie skuteczności zasad uczenia

Można porównać różne zasady uczenia w odniesieniu do przeszłych danych w dziennikach personalizacji, przeprowadzając [oceny w trybie offline](concepts-offline-evaluation.md).

[Przekaż własne zasady uczenia](how-to-offline-evaluation.md) , aby porównać je z bieżącymi zasadami nauki.

### <a name="discovery-of-optimized-learning-policies"></a>Odnajdywanie zoptymalizowanych zasad uczenia

Personalizowanie może utworzyć bardziej zoptymalizowane zasady uczenia podczas przeprowadzania [oceny w trybie offline](how-to-offline-evaluation.md). Bardziej zoptymalizowane zasady uczenia, które są widoczne na potrzeby oceny w trybie offline, dają lepsze wyniki w przypadku korzystania z Internetu w programie personalizacji.

Po utworzeniu zoptymalizowanych zasad uczenia można zastosować je bezpośrednio do personalizacji, aby zastąpić bieżące zasady natychmiast. można też zapisać je do dalszej oceny i zdecydować, czy należy je odrzucić, zapisać lub zastosować później.