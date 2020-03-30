---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183032"
---
Ten przykład obejmuje przy użyciu usługi [Twilio](https://www.twilio.com/) do wysyłania wiadomości SMS do telefonu komórkowego. Usługa Azure Functions ma już obsługę usługi Twilio za pośrednictwem [powiązania usługi Twilio,](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)a przykładowa funkcja używa tej funkcji.

Pierwszą rzeczą, której potrzebujesz, jest konto usługi Twilio. Można utworzyć jeden https://www.twilio.com/try-twiliowolny w . Po uzyskaniu konta dodaj do aplikacji funkcji następujące trzy **następujące ustawienia aplikacji.**

| Nazwa ustawienia aplikacji | Opis wartości |
| - | - |
| **TwilioAccountSid**  | Identyfikator SID dla twojego konta usługi Twilio |
| **TwilioAuthToken (TwilioAuthToken)**   | Token YUTH dla Twojego konta Twilio |
| **Numer telefonu TwilioNumer** | Numer telefonu skojarzony z Twoim kontem usługi Twilio. Służy do wysyłania wiadomości SMS. |