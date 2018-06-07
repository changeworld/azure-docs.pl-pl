---
title: Eksportuj Twojej subskrypcji platformy Azure informacji najwyższego poziomu | Dokumentacja firmy Microsoft
description: Opisuje sposób wyświetlania wszystkich subskrypcji platformy Azure identyfikatorów skojarzonych z Twoim kontem.
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
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655453"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>Eksportowanie i wyświetlania informacji o subskrypcji najwyższego poziomu
Aby wyświetlić zestaw subskrypcji identyfikatorów skojarzonych z poświadczeń użytkownika [pobranie pliku JSON z informacjami o subskrypcji z Centrum konta platformy Azure](http://account.azure.com/subscriptions/download).

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Plik JSON pobranych zawiera następujące informacje:
- Adres e-mail: Adres e-mail skojarzony z Twoim kontem.
- Identyfikator PUID: Unikatowy identyfikator skojarzony z kontem rozliczeniowym.
- SubscriptionIds: Lista subskrypcji, które należą do Twojego konta, wymienionych według identyfikatora subskrypcji.

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
