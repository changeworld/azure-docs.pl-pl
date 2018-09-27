---
title: Eksportuj Twojej subskrypcji platformy Azure informacji najwyższego poziomu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można wyświetlić wszystkich subskrypcji platformy Azure identyfikatorów skojarzonych z Twoim kontem.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cwatson
ms.openlocfilehash: 5c32b90c8a291ff744b4894af12f8d623cb95137
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391408"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlić najwyższego poziomu informacji o subskrypcji
Jeśli chcesz wyświetlić zestaw subskrypcji identyfikatorów skojarzonych z poświadczeń użytkownika [Pobierz plik JSON, informacje o subskrypcji z Centrum kont Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Plik JSON pobranych zawiera następujące informacje:
- Adres e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z kontem rozliczeniowym.
- SubscriptionIds: Lista subskrypcji, które należą do swojego konta, wymienionych według identyfikatora subskrypcji.

### <a name="subscriptionsjson-sample"></a>Przykładowe Subscriptions.JSON
~~~~
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
~~~~
