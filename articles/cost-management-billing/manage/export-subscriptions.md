---
title: Eksportowanie informacji najwyższego poziomu dotyczących subskrypcji platformy Azure | Microsoft Docs
description: Opisuje sposób wyświetlania wszystkich identyfikatorów subskrypcji platformy Azure skojarzonych z Twoim kontem.
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 553cc7fd27571ebc925e33f824060c023664a369
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991778"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlanie informacji najwyższego poziomu dotyczących subskrypcji
Jeśli chcesz wyświetlić zestaw identyfikatorów subskrypcji skojarzonych z poświadczeniami użytkownika, [pobierz plik JSON z informacjami o subskrypcji z Centrum konta platformy Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

Pobrany plik JSON zawiera następujące informacje:
- E-mail: adres e-mail skojarzony z Twoim kontem.
- PUID: unikatowy identyfikator skojarzony z kontem rozliczeniowym.
- SubscriptionIds: Lista subskrypcji, które należą do Twojego konta, wyliczane według identyfikatora subskrypcji.

### <a name="subscriptionsjson-sample"></a>Przykładowy plik subscriptions.json

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
