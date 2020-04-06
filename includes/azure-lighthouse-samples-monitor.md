---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421138"
---
Te przykłady pokazują, jak za pomocą usługi Azure Monitor tworzyć alerty dla subskrypcji, które zostały dołączone na potrzeby zarządzania zasobami delegowanymi na platformie Azure.

| **Szablon** | **Opis** |
|---------|---------|
| [monitorowanie–delegowanie–zmiany](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Wysyła zapytanie o ostatni 1 dzień działania w ramach zarządzania dzierżawą i [zgłasza wszelkie dodane lub usunięte delegowania](../articles/lighthouse/how-to/monitor-delegation-changes.md) (lub nieudane próby).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ten szablon tworzy alert platformy Azure i nawiązuje połączenie z istniejącą grupą akcji.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Tworzy wiele alertów dziennika opartych na zapytaniach języka Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Wdraża alert w dzierżawie, gdy użytkownik deleguje subskrypcję do dzierżawy zarządzającej.|
