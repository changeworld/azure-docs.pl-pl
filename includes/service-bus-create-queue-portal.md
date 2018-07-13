---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138164"
---
Upewnij się, że masz już utworzoną przestrzeń nazw usługi Service Bus, jak opisano [tutaj][namespace-how-to].

1. Zaloguj się w witrynie [Azure Portal][azure-portal].
2. W lewym okienku nawigacji portalu kliknij pozycję **Service Bus** (jeśli pozycja **Service Bus** nie jest widoczna, kliknij pozycję **Wszystkie usługi**).
3. Kliknij przestrzeń nazw, w której chcesz utworzyć kolejkę. W tym przypadku jest to **sbnstest1**.
   
    ![Tworzenie kolejki][createqueue1]
4. W oknie przestrzeni nazw kliknij pozycję **Kolejki**, a następnie w oknie **Kolejki** kliknij pozycję **+ Kolejka**.
   
    ![Wybieranie kolejek][createqueue2]
5. W polu **Nazwa** wprowadź nazwę kolejki, a pozostałe wartości pozostaw domyślne.
   
    ![Wybieranie nowych kolejek][createqueue3]
6. W dolnej części okna kliknij pozycję **Utwórz**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
