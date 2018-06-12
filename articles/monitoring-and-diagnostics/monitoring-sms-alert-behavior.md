---
title: Zachowanie alertów programu SMS w grupach akcji
description: Format wiadomości SMS i odpowiada na wiadomości SMS, aby anulować subskrypcję, należy dokonać ponownej subskrypcji lub prosić o pomoc.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: f2f463f6c428ce6c72e2640472376fa17a2bfe5a
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263011"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Zachowanie w grupach akcji alertów programu SMS
## <a name="overview"></a>Przegląd ##
Grupy akcji umożliwiają skonfigurowanie listy akcji. Te grupy są używane podczas definiowania alerty; zapewnienie, że po wyzwoleniu alertu jest powiadamiany o grupy określonej akcji. Jedna z akcji obsługiwanych jest SMS; Powiadomień programu SMS obsługuje komunikację dwukierunkową. Użytkownik może Odpowiedz na wiadomość SMS, aby:

- **Anulowanie subskrypcji alertów:** użytkownik może zrezygnować z wszystkich alertów programu SMS dla wszystkich grup akcji lub grupy jednej akcji.
- **Dokonać ponownej subskrypcji alertów:** użytkownik może dokonać ponownej subskrypcji dla wszystkich alertów programu SMS dla wszystkich grup akcji lub grupy jednej akcji.  
- **Prosić o pomoc:** użytkownik może uzyskać więcej informacji na temat programu SMS. Są one przekierowywane do tego artykułu.

W tym artykule opisano działanie alertów programu SMS i akcje reakcji użytkownika może przyjmować na podstawie ustawień regionalnych użytkownika:

## <a name="receiving-an-sms-alert"></a>Otrzymywanie alertów programu SMS
Odbiornik programu SMS, skonfigurowany jako część grupy akcji odbiera wiadomość SMS, po wyzwoleniu alertu. SMS zawiera następujące informacje:
* Nazwa_skrócona grupy akcji ten alert został wysłany do
- Nazwa alertu

| ODPOWIEDŹ | Opis |
| ----- | ----------- |
| WYŁĄCZ <Action Group Short name> | Wyłącza dalsze programu SMS z grupy akcji |
| WŁĄCZ <Action Group Short name> | Ponownie włącza programu SMS z grupy akcji |
| STOP | Wyłącza dalsze SMS ze wszystkich grup, akcja |
| POCZĄTEK | Ponownie włącza SMS ze wszystkich grup, akcja |
| HELP | Odpowiedź jest wysyłana do użytkownika z łączem do tego artykułu. |

>[!NOTE]
>Jeśli użytkownik anulował z alertów programu SMS, ale jest następnie dodawana do nowej grupy akcji; BĘDĄ one otrzymywać alerty programu SMS dla tej nowej grupy akcji, ale pozostaje anulować ze wszystkich grup poprzedniej akcji.

## <a name="next-steps"></a>Następne kroki
Pobierz [Przegląd alertów dotyczących działań w dzienniku](monitoring-overview-alerts.md) i Dowiedz się, jak otrzymywać alerty  
Dowiedz się więcej o [limitów szybkości programu SMS](monitoring-alerts-rate-limiting.md)  
Dowiedz się więcej o [grupy akcji](monitoring-action-groups.md)
