---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986631"
---
Te przykłady pokazują, jak używać usługi Azure Policy z subskrypcjami, które zostały dołączone na potrzeby zarządzania zasobami delegowanymi na platformie Azure.

| **Szablon** | **Opis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Przypisuje zasady, które dodają tag do subskrypcji delegowanej lub go z niej usuwają (za pomocą efektu modify). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Przypisuje zasady, które będą przeprowadzać inspekcję przypisań delegacji. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Przypisuje zasady, które umożliwiają diagnostykę zasobów usługi Azure Key Vault w subskrypcjach delegowanych (przy użyciu efektu deployIfNotExists). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Przypisuje kilka zasad w celu włączenia diagnostyki w subskrypcji delegowanej oraz łączy wszystkie maszyny wirtualne z systemem Windows i Linux z obszarem roboczym usługi Log Analytics utworzonym przez te zasady. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Stosuje inicjatywę (wiele powiązanych definicji zasad) do subskrypcji delegowanej. |

