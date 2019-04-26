---
title: Zachowanie alertów SMS w grupach akcji
description: Wiadomości SMS, formatem komunikatu i odpowiadanie na wiadomości SMS, aby anulować subskrypcję, potrzeby ponownej subskrypcji lub prosić o pomoc.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 74666149824627308b6c5b026e0c9ba7a7750ada
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346301"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Alert programu SMS, zachowanie w grupach akcji
## <a name="overview"></a>Omówienie ##
Grupy akcji umożliwiają skonfigurowanie listy akcji. Te grupy są używane podczas definiowania alerty; zapewnienie, że grupy określonej akcji jest powiadamiany po wyzwoleniu alertu. Jedną z akcji obsługiwanych jest SMS; Powiadomienia SMS obsługuje komunikację dwukierunkową. Użytkownik może odpowiadać na wiadomość SMS, aby:

- **Anulowanie subskrypcji alertów:** Użytkownik może anulować subskrypcję wszystkich alertów programu SMS dla wszystkich grup akcji lub grupy pojedynczej akcji.
- **Potrzeby ponownej subskrypcji alertów:** Użytkownik może dokonać ponownej subskrypcji dla wszystkich alertów programu SMS dla wszystkich grup akcji lub grupy pojedynczej akcji.  
- **Prosić o pomoc:** Użytkownik może zażądać więcej informacji na temat wiadomość SMS. Są one przekierowywane do tego artykułu.

W tym artykule opisano działanie alertów SMS i działania w odpowiedzi użytkownik może wykonania na podstawie ustawień regionalnych użytkownika:

## <a name="receiving-an-sms-alert"></a>Otrzymywanie alertów SMS
Odbiornik programu SMS, skonfigurowany jako część grupy akcji otrzymuje wiadomość SMS po wyzwoleniu alertu. Wiadomość SMS zawiera następujące informacje:
* SHORTNAME grupie akcji ten alert został wysłany do
* Tytuł alertu

| ODPOWIEDZ | Opis |
| ----- | ----------- |
| WYŁĄCZ `<Action Group Short name>` | Wyłącza dalsze SMS do grupy akcji |
| WŁĄCZ `<Action Group Short name>` | Ponownie włącza SMS do grupy akcji |
| STOP | Wyłącza SMS dalsze ze wszystkich grup akcji |
| ROZPOCZNIJ | Ponownie włącza SMS ze wszystkich grup akcji |
| HELP | Odpowiedź jest wysyłana do użytkownika za pomocą linku do tego artykułu. |

>[!NOTE]
>Jeśli użytkownik anulował subskrypcję alertów SMS, ale jest dodawane do nowej grupy akcji; one będą otrzymywać alerty programu SMS dla tej nowej grupy akcji, ale nadal Anulowano subskrypcję ze wszystkich grup, poprzednie działanie.

## <a name="next-steps"></a>Następne kroki
Pobierz [Przegląd alertów dziennika aktywności](alerts-overview.md) i Dowiedz się, jak otrzymywać alerty  
Dowiedz się więcej o [szybkości programu SMS](alerts-rate-limiting.md)  
Dowiedz się więcej o [grupy akcji](../../azure-monitor/platform/action-groups.md)

