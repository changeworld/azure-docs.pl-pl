---
title: Przykłady szablonów zasad | Microsoft Docs
description: Przykłady szablonów zasad platformy Azure dla sieci wirtualnej
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: a0153cf11c3fe817ce397b4d0c47a786a4334f39
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214814"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Przykładowe szablony zasad platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do przykładowych szablonów usługi [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Przykłady znajdują się w [repozytorium przykładów usługi Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Sieć**||
| [NSG X on every NIC](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Sieciowa grupa zabezpieczeń X na każdej karcie sieciowej) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdym wirtualnym interfejsem sieciowym. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [NSG X on every subnet](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Sieciowa grupa zabezpieczeń X w każdej podsieci) | Wymaga, aby określona sieciowa grupa zabezpieczeń była używana z każdą podsiecią wirtualną. Należy określić identyfikator sieciowej grupy zabezpieczeń do użycia. |
| [No route table](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Bez tabeli tras)  |Uniemożliwia sieciom wirtualnym wdrażanie przy użyciu tabeli tras. |
| [Use approved subnet for VM network interfaces](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Użyj zatwierdzonej podsieci dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej podsieci przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej podsieci. |
| [Use approved vNet for VM network interfaces](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Użyj zatwierdzonej sieci wirtualnej dla interfejsów sieciowych maszyny wirtualnej) | Wymaga używania zatwierdzonej sieci wirtualnej przez interfejsy sieciowe. Należy określić identyfikator zatwierdzonej sieci wirtualnej. |
|**Monitorowanie**||
| [Audit diagnostic setting](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Przeprowadzaj inspekcję ustawienia diagnostyki) | Przeprowadza inspekcję, czy ustawienia diagnostyki nie zostały włączone dla określonych typów zasobów. Należy określić tablicę typów zasobów, aby sprawdzić, czy ustawienia diagnostyki są włączone. |
|**Konwencje nazw i tekstu**||
| [Allow multiple name patterns](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Zezwalaj na wiele wzorców nazw) | Zezwala na używanie jednego z wielu wzorców nazw dla zasobów. |
| [Require like pattern](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj wzorca like) | Zapewnia, że nazwy zasobów spełniają warunek *like* dla wzorca. |
| [Require match pattern](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj zgodności z wzorcem) | Zapewnia, że nazwy zasobów są zgodne z wybranym wzorcem nazewnictwa. |
| [Require tag match pattern](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymagaj zgodności tagu z wzorcem) | Zapewnia, że wartość tagu jest zgodna z wzorcem tekstu. |
|**Tagi**||
| [Billing tags policy initiative](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Inicjatywa zasad tagów rozliczeń) | Wymaga określonych wartości tagów centrum kosztu i nazwy produktu. Przy użyciu wbudowanych zasad określa wymagane tagi i wymusza ich stosowanie. Wymagane wartości tagów są określane przez Ciebie.  |
| [Enforce tag and its value on resource groups](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymuś tag i jego wartość w grupach zasobów) | Wymaga tagu i wartości w grupie zasobów. Należy określić wymaganą nazwę i wartość tagu.  |
| [Enforce tag and its value](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Wymuś tag i jego wartość) | Wymaga określonej nazwy i wartości tagu. Należy określić nazwę i wartość tagu do wymuszenia.  |
| [Apply tag and its default value](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Zastosuj tag i jego wartość domyślną) | Dołącza określoną nazwę i wartość tagu, jeśli nie podano tagu. Należy określić nazwę i wartość tagu do zastosowania.  |
|**Ogólne**||
| [Allowed locations](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Dozwolone lokalizacje) | Wymaga wdrożenia wszystkich zasobów w zatwierdzonych lokalizacjach. Należy określić tablicę zatwierdzonych lokalizacji.  |
| [Allowed resource types](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Dozwolone typy zasobów) | Zapewnia wdrażanie tylko zatwierdzonych typów zasobów. Należy określić tablicę typów zasobów, które są dozwolone.  |
| [Not allowed resource types](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Niedozwolone typy zasobów) | Uniemożliwia wdrażanie określonych typów zasobów. Należy określić tablicę typów zasobów do blokowania.  |