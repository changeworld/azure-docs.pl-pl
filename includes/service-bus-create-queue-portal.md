---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ed298fc8f13685c4872c4c54ba1e447debea79f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702640"
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
