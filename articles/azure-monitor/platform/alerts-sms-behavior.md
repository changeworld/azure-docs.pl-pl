---
title: Zachowanie alertu programu SMS w grupach akcji
description: Format wiadomości SMS i odpowiadanie na wiadomości SMS w celu anulowania subskrypcji, ponownego subskrypcji lub żądania pomocy.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.subservice: alerts
ms.openlocfilehash: b75bda626f887f1224c1b0f18a80887983a2367d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665310"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Zachowanie alertu programu SMS w grupach akcji

## <a name="overview"></a>Omówienie 
Grupy akcji umożliwiają skonfigurowanie listy akcji. Te grupy są używane podczas definiowania alertów; zapewnienie, że określona grupa akcji jest powiadamiana o wyzwoleniu wpisu. Jedną z obsługiwanych akcji jest SMS; Powiadomienia SMS obsługują komunikację dwukierunkową. Użytkownik może odpowiedzieć na wiadomość SMS na:

- **Wypisuj się z alertów:** Użytkownik może anulować subskrypcję wszystkich alertów SMS dla wszystkich grup akcji lub jednej grupy akcji.
- **Ponowne zapisywanie alertów:** Użytkownik może ponownie subskrybować wszystkie alerty SMS dla wszystkich grup akcji lub pojedynczej grupy akcji.  
- **Poproś o pomoc:** Użytkownik może poprosić o więcej informacji na temat wiadomości SMS. Są one przekierowywane do tego artykułu.

Ten artykuł obejmuje zachowanie alertów SMS i akcje odpowiedzi, które użytkownik może podjąć na podstawie ustawień regionalnych użytkownika:

## <a name="receiving-an-sms-alert"></a>Odbieranie alertu SMS
Odbiornik programu SMS skonfigurowany jako część grupy akcji odbiera wiadomość SMS po wyzwoleniu alertu. Sms zawiera następujące informacje:
* Skrócona nazwa grupy akcji, do
* Tytuł wpisu

| REPLY | Opis |
| ----- | ----------- |
| Wyłączyć`<Action Group Short name>` | Wyłącza kolejne wiadomości SMS z grupy działania |
| Włączyć`<Action Group Short name>` | Ponowne włączanie wiadomości SMS z grupy działania |
| Zatrzymać | Wyłącza kolejne wiadomości SMS ze wszystkich grup akcji |
| Uruchomić | Ponowne włączanie wiadomości SMS ze wszystkich grup akcji |
| POMOC | Odpowiedź jest wysyłana do użytkownika z łączem do tego artykułu. |

>[!NOTE]
>Jeśli użytkownik anulował subskrypcję alertów SMS, ale jest następnie dodawany do nowej grupy akcji; otrzymają alerty SMS dla tej nowej grupy akcji, ale pozostaną anulowane ze wszystkich poprzednich grup akcji.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się [z omówieniem alertów dziennika aktywności](alerts-overview.md) i dowiedz się, jak otrzymywać alerty  
Dowiedz się więcej o [ograniczaniu szybkości sms](alerts-rate-limiting.md)  
Dowiedz się więcej o [grupach akcji](../../azure-monitor/platform/action-groups.md)

