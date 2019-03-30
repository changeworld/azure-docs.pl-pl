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
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8a3a80fb6ae20eddc3237d986ecda1d4cb5b65a5
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666040"
---
# <a name="azure-powershell-samples"></a>Przykłady programu Azure PowerShell

Poniższa tabela zawiera linki do przykładowych skryptów programu PowerShell, które umożliwiają tworzenie klastrów, aplikacji i usług w ramach usługi Service Fabric oraz zarządzanie nimi.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Tworzenie klastra** ||
| [Tworzenie klastra (platforma Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Tworzy klaster usługi Azure Service Fabric. |
| **Zarządzanie klastrem, węzłami i infrastrukturą** ||
| [Dodawanie certyfikatu aplikacji](./scripts/service-fabric-powershell-add-application-certificate.md)| Dodaje certyfikat X.509 aplikacji do wszystkich węzłów w klastrze. |
| [Aktualizowanie zakresu portów protokołu RDP na maszynach wirtualnych klastra](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Modyfikuje zakres portów protokołu RDP na maszynach wirtualnych węzłów wdrożonego klastra.|
| [Aktualizowanie nazwy i hasła administratora dla maszyn wirtualnych węzłów klastra](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aktualizuje nazwę i hasło administratora dla maszyn wirtualnych węzłów klastra. |
| [Otwieranie portu w module równoważenia obciążenia](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Otwiera port aplikacji w module równoważenia obciążenia platformy Azure w celu zezwolenia na ruch przychodzący na określonym porcie. |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego](./scripts/service-fabric-powershell-add-nsg-rule.md) | Tworzy regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego w celu zezwolenia na ruch przychodzący do klastra na określonym porcie. |
| **Zarządzanie aplikacjami** ||
| [Wdrażanie aplikacji](./scripts/service-fabric-powershell-deploy-application.md)| Wdrażanie aplikacji w klastrze.|
| [Uaktualnianie aplikacji](./scripts/service-fabric-powershell-upgrade-application.md)| Uaktualnianie aplikacji.|
| [Usuwanie aplikacji](./scripts/service-fabric-powershell-remove-application.md)| Usuwanie aplikacji z klastra.|
