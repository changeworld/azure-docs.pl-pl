---
title: Centralne zarządzanie zaporą platformy Azure
description: Dowiedz się więcej o centralnym zarządzaniu usługi Azure Firewall Manager
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444552"
---
# <a name="azure-firewall-central-management"></a>Centralne zarządzanie zaporą platformy Azure

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Jeśli zarządzasz wieloma zaporami, wiesz, że ciągłe zmienianie reguł zapory utrudnia ich synchronizację. Centralne zespoły IT potrzebują sposobu definiowania podstawowych zasad zapory i wymuszania ich w wielu jednostkach biznesowych. W tym samym czasie zespoły DevOps chcą utworzyć własne lokalne zasady zapory pochodnej dla lepszej elastyczności.

Usługa Azure Firewall Manager Preview może pomóc w rozwiązaniu tych problemów.


## <a name="azure-firewall-manager-preview"></a>Azure Firewall Manager Preview

Usługa Azure Firewall Manager Preview to usługa zarządzania zabezpieczeniami sieci, która zapewnia centralne zasady zabezpieczeń i zarządzanie trasami dla obszarów zabezpieczeń opartych na chmurze. Ułatwia to zespołom IT przedsiębiorstwa centralne definiowanie reguł na poziomie sieci i aplikacji do filtrowania ruchu w wielu wystąpieniach zapory platformy Azure. Można obejmować różne regiony platformy Azure i subskrypcji w architekturze centrum i szprychy dla zarządzania ruchem i ochrony. Zapewnia również DevOps lepszą elastyczność z pochodnymi lokalnymi zasadami zabezpieczeń zapory, które są implementowane w różnych organizacjach.

### <a name="firewall-policy"></a>Zasady zapory sieciowej

Zasady zapory to zasób platformy Azure zawierający kolekcje nat, kolekcje reguł sieciowych i aplikacji oraz ustawienia analizy zagrożeń. Jest to zasób globalny, który może być używany w wielu wystąpieniach Zapory Platformy Azure w *zabezpieczonych centrach wirtualnych* i *sieciach wirtualnych koncentratora.* Nowe zasady można tworzyć od podstaw lub dziedziczyć z istniejących zasad. Dziedziczenie umożliwia DevOps tworzenie zasad zapory lokalnej na podstawie zasad podstawowych zlecanym organizacji. Zasady działają w różnych regionach i subskrypcjach.
 
Zasady zapory i skojarzenia można utworzyć za pomocą usługi Azure Firewall Manager. Można jednak również utworzyć zasady i zarządzać nimi przy użyciu interfejsu API REST, szablonów, programu Azure PowerShell i interfejsu wiersza polecenia. Po utworzeniu zasady można ją skojarzyć z zaporą w wirtualnym centrum sieci WAN, dzięki czemu jest to *bezpieczne centrum wirtualne* i/lub zapora w sieci wirtualnej, dzięki czemu jest *to sieć wirtualna Hub*.

### <a name="pricing"></a>Cennik

Zasady są rozliczane na podstawie skojarzeń zapory. Zasady z zerowym lub jednym skojarzeniem zapory są bezpłatne. Zasady z wieloma skojarzeniami zapory są rozliczane według stałej stawki. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="azure-firewall-management-partners"></a>Partnerzy usługi Azure Firewall Management

Następujące wiodące rozwiązania innych firm obsługują centralne zarządzanie zaporą platformy Azure przy użyciu standardowych interfejsów API usługi Azure REST. Każde z tych rozwiązań ma swoje unikalne cechy i cechy:

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Strażnik bezpieczeństwa w chmurze Barracuda](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Orka Tufin](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Firewall Manager Preview, zobacz [Co to jest Usługa Azure Firewall Manager Preview?](../firewall-manager/overview.md)