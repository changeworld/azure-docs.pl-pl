---
title: Przykłady szablonów zasad | Microsoft Docs
description: Przykłady szablonów zasad platformy Azure dla sieci wirtualnej
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8228da778e6cf6a874868ef0206092913908352d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694286"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Przykładowe szablony zasad platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do przykładów dla usługi [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Przykłady znajdują się w [repozytorium przykładów usługi Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Sieć**||
| [NSG X on every NIC](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Sieciowa grupa zabezpieczeń X na każdej karcie sieciowej) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdym wirtualnym interfejsem sieciowym. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [NSG X on every subnet](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [No route table](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Bez tabeli tras)  |Uniemożliwia sieciom wirtualnym wdrażanie przy użyciu tabeli tras. |
| [Use approved subnet for VM network interfaces](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |
|**Monitorowanie**||
| [Audit diagnostic setting](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Przeprowadzaj inspekcję ustawienia diagnostyki) | Przeprowadza inspekcję, czy ustawienia diagnostyki nie zostały włączone dla określonych typów zasobów. Należy określić tablicę typów zasobów, aby sprawdzić, czy ustawienia diagnostyki są włączone. |
|**Konwencje nazw i tekstu**||
| [Allow multiple name patterns](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Zezwalaj na wiele wzorców nazw) | Zezwala na używanie jednego z wielu wzorców nazw dla zasobów. |
| [Require like pattern](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj wzorca like) | Zapewnia, że nazwy zasobów spełniają warunek *like* dla wzorca. |
| [Require match pattern](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj zgodności z wzorcem) | Zapewnia, że nazwy zasobów są zgodne z wybranym wzorcem nazewnictwa. |
| [Require tag match pattern](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj zgodności tagu z wzorcem) | Zapewnia, że wartość tagu jest zgodna z wzorcem tekstu. |
|**Tagi**||
| [Billing tags policy initiative](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Inicjatywa zasad tagów rozliczeń) | Wymaga określonych wartości tagów centrum kosztu i nazwy produktu. Przy użyciu wbudowanych zasad określa wymagane tagi i wymusza ich stosowanie. Wymagane wartości tagów są określane przez Ciebie.  |
| [Enforce tag and its value on resource groups](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymuś tag i jego wartość w grupach zasobów) | Wymaga tagu i wartości w grupie zasobów. Należy określić wymaganą nazwę i wartość tagu.  |
| [Enforce tag and its value](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymuś tag i jego wartość) | Wymaga określonej nazwy i wartości tagu. Należy określić nazwę i wartość tagu do wymuszenia.  |
| [Apply tag and its default value](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Zastosuj tag i jego wartość domyślną) | Dołącza określoną nazwę i wartość tagu, jeśli nie podano tagu. Należy określić nazwę i wartość tagu do zastosowania.  |
|**Ogólne**||
| [Allowed locations](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Dozwolone lokalizacje) | Wymaga wdrożenia wszystkich zasobów w zatwierdzonych lokalizacjach. Należy określić tablicę zatwierdzonych lokalizacji.  |
| [Allowed resource types](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Dozwolone typy zasobów) | Zapewnia wdrażanie tylko zatwierdzonych typów zasobów. Należy określić tablicę typów zasobów, które są dozwolone.  |
| [Not allowed resource types](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Niedozwolone typy zasobów) | Uniemożliwia wdrażanie określonych typów zasobów. Należy określić tablicę typów zasobów do blokowania.  |