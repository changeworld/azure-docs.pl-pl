---
title: Przykłady dla programu Azure PowerShell — Service Fabric
description: Dowiedz się więcej o tworzeniu klastrów, aplikacji i usług usługi Azure Service Fabric za pomocą programu Powershell i zarządzania nimi.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: f9ba1b4833b3aff13284a68c23f398ea47ef2ae2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75645654"
---
# <a name="azure-service-fabric-powershell-samples"></a>Przykłady usługi Azure Service Fabric PowerShell

Poniższa tabela zawiera linki do przykładowych skryptów programu PowerShell, które umożliwiają tworzenie klastrów, aplikacji i usług w ramach usługi Service Fabric oraz zarządzanie nimi.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Tworzenie klastra** ||
| [Tworzenie klastra (platforma Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Tworzy klaster usługi Azure Service Fabric. |
| **Zarządzanie klastrem, węzłami i infrastrukturą** ||
| [Dodawanie certyfikatu aplikacji](./scripts/service-fabric-powershell-add-application-certificate.md)| Tworzy certyfikat X509 do usługi Key Vault i wdraża go w skali maszyny wirtualnej ustawionej w klastrze. |
| [Aktualizowanie zakresu portów protokołu RDP na maszynach wirtualnych klastra](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Modyfikuje zakres portów protokołu RDP na maszynach wirtualnych węzłów wdrożonego klastra.|
| [Aktualizowanie nazwy i hasła administratora dla maszyn wirtualnych węzłów klastra](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aktualizuje nazwę i hasło administratora dla maszyn wirtualnych węzłów klastra. |
| [Otwieranie portu w module równoważenia obciążenia](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Otwiera port aplikacji w module równoważenia obciążenia platformy Azure w celu zezwolenia na ruch przychodzący na określonym porcie. |
| [Tworzenie reguły sieciowej grupy zabezpieczeń dla ruchu przychodzącego](./scripts/service-fabric-powershell-add-nsg-rule.md) | Tworzy regułę sieciowej grupy zabezpieczeń dla ruchu przychodzącego w celu zezwolenia na ruch przychodzący do klastra na określonym porcie. |
| **Zarządzanie aplikacjami** ||
| [Wdrażanie aplikacji](./scripts/service-fabric-powershell-deploy-application.md)| Wdrażanie aplikacji w klastrze.|
| [Uaktualnianie aplikacji](./scripts/service-fabric-powershell-upgrade-application.md)| Uaktualnianie aplikacji.|
| [Usuwanie aplikacji](./scripts/service-fabric-powershell-remove-application.md)| Usuwanie aplikacji z klastra.|
