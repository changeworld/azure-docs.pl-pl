---
title: Przykłady dla programu Azure PowerShell — Service Fabric | Microsoft Docs
description: Przykłady dla programu Azure PowerShell — Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: cae88e142c3bca15e837db7f084eef68434a78ca
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903267"
---
# <a name="azure-powershell-samples"></a>Przykłady programu Azure PowerShell

Poniższa tabela zawiera linki do przykładowych skryptów programu PowerShell, które umożliwiają tworzenie klastrów, aplikacji i usług w ramach usługi Service Fabric oraz zarządzanie nimi.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Tworzenie klastra** ||
| [Tworzenie klastra (platforma Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Tworzy klaster usługi Azure Service Fabric. |
| **Zarządzanie klastrem, węzłami i infrastrukturą** ||
| [Dodawanie certyfikatu aplikacji](./scripts/service-fabric-powershell-add-application-certificate.md)| Tworzy certyfikat x509 do Key Vault i wdraża go w zestawie skalowania maszyn wirtualnych w klastrze. |
| [Aktualizowanie zakresu portów protokołu RDP na maszynach wirtualnych klastra](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Modyfikuje zakres portów protokołu RDP na maszynach wirtualnych węzłów wdrożonego klastra.|
| [Aktualizowanie nazwy i hasła administratora dla maszyn wirtualnych węzłów klastra](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aktualizuje nazwę i hasło administratora dla maszyn wirtualnych węzłów klastra. |
| [Otwieranie portu w module równoważenia obciążenia](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Otwiera port aplikacji w module równoważenia obciążenia platformy Azure w celu zezwolenia na ruch przychodzący na określonym porcie. |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego](./scripts/service-fabric-powershell-add-nsg-rule.md) | Tworzy regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego w celu zezwolenia na ruch przychodzący do klastra na określonym porcie. |
| **Zarządzanie aplikacjami** ||
| [Wdrażanie aplikacji](./scripts/service-fabric-powershell-deploy-application.md)| Wdrażanie aplikacji w klastrze.|
| [Uaktualnianie aplikacji](./scripts/service-fabric-powershell-upgrade-application.md)| Uaktualnianie aplikacji.|
| [Usuwanie aplikacji](./scripts/service-fabric-powershell-remove-application.md)| Usuwanie aplikacji z klastra.|
