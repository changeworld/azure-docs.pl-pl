---
title: Dodawanie opóźnienia w usłudze logic apps | Dokumentacja firmy Microsoft
description: Przegląd opóźnienia i opóźnienia — aż do akcji i jak ich używać za pomocą aplikacji logiki platformy Azure.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893728"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Rozpoczynanie pracy z usługą opóźnienia i opóźnienia — aż do akcji
Za pomocą opóźnienie i "opóźnienie — do momentu" czynności, można realizować scenariusze przepływu pracy.

Można na przykład:

* Poczekaj, aż weekday do wysyłania aktualizacji stanu za pośrednictwem poczty e-mail.
* Opóźnienie przepływu pracy, dopóki wywołania HTTP ma czasu na zakończenie przed wznowieniem i pobieranie wyników.

Aby rozpocząć pracę, korzystając z akcji opóźnienia w aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Za pomocą akcji opóźnienia

Akcja jest operacja, która jest przeprowadzane przez przepływ pracy, który jest zdefiniowany w aplikacji logiki. 
[Dowiedz się więcej o akcjach](../connectors/apis-list.md).

Oto przykładowa sekwencja sposób używania kroku opóźnienia w aplikacji logiki:

1. Po dodaniu wyzwalacza, kliknij przycisk **nowy krok** umożliwiające dodanie akcji.
2. Wyszukaj **opóźnienie** Aby wyświetlić akcje opóźnienia. W tym przykładzie wybierzemy pozycję **opóźnienie**.
   
    ![Opóźnienie akcji](./media/connectors-native-delay/using-action-1.png)
3. Wykonaj jedną z właściwości akcji, aby skonfigurować opóźnienie.
   
    ![Opóźnienie konfiguracji](./media/connectors-native-delay/using-action-2.png)
4. Kliknij przycisk **Zapisz** do publikowania i aktywować aplikację logiki.

## <a name="action-details"></a>Szczegóły akcji
Wyzwalacz cykliczny ma następujące właściwości, które można skonfigurować.

### <a name="delay-action"></a>Opóźnienie akcji
Ta akcja opóźnia uruchamia określonym przedziale czasu.
A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Liczba * |count |Liczba jednostek czasu opóźnienia |
| Jednostka * |jednostka |Jednostka czasu: `Second`, `Minute`, `Hour`, lub `Day` |

<br>

### <a name="delay-until-action"></a>Opóźnienie — aż do akcji
Ta akcja opóźnienia uruchomienia aż do określonej daty/godziny.
A * oznacza, że jest polem wymaganym.

| Nazwa wyświetlana | Nazwa właściwości | Opis |
| --- | --- | --- |
| Roku * |sygnatura czasowa |Opóźnienie do (GMT) od początku roku |
| Miesiąc * |sygnatura czasowa |Miesiąc opóźnienie do (GMT) |
| Dzień * |sygnatura czasowa |Dnia ma być opóźnienie do (GMT) |

<br>

## <a name="next-steps"></a>Kolejne kroki
Teraz wypróbuj platformę i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Możesz zapoznać się z innych dostępnych łączników w usłudze logic apps, analizując naszych [listy interfejsów API](apis-list.md).

