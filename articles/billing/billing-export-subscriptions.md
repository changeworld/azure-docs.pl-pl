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
ms.author: banders
ms.openlocfilehash: 8a3d1a3a6b1dce1d729942715bbe5962837ff093
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918803"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlić najwyższego poziomu informacji o subskrypcji
Jeśli chcesz wyświetlić zestaw subskrypcji identyfikatorów skojarzonych z poświadczeń użytkownika [Pobierz plik JSON, informacje o subskrypcji z Centrum kont Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Plik JSON pobranych zawiera następujące informacje:
- Wiadomość e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z kontem rozliczeniowym.
- SubscriptionIds: Lista subskrypcji należących do swojego konta, wymienionych według identyfikatora subskrypcji.

### <a name="subscriptionsjson-sample"></a>Przykładowe Subscriptions.JSON

```json
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
```
