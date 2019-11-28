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
ms.openlocfilehash: 87c31e663529ea20d119753094133941b02e2756
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226279"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlanie informacji najwyższego poziomu dotyczących subskrypcji
Jeśli chcesz wyświetlić zestaw identyfikatorów subskrypcji skojarzonych z poświadczeniami użytkownika, [pobierz plik JSON z informacjami o subskrypcji z Centrum konta platformy Azure](https://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Pobrany plik JSON zawiera następujące informacje:
- Adres e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z Twoim kontem rozliczeniowym.
- Identyfikatory subskrypcji: Lista subskrypcji należących do Twojego konta uporządkowane według identyfikatora subskrypcji.

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
