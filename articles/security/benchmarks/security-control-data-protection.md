---
title: Kontrola zabezpieczeń platformy Azure — ochrona danych
description: Ochrona danych kontroli bezpieczeństwa
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934516"
---
# <a name="security-control-data-protection"></a>Kontrola bezpieczeństwa: Ochrona danych

Zalecenia dotyczące ochrony danych koncentrują się na rozwiązywaniu problemów związanych z szyfrowaniem, listami kontroli dostępu, kontrolą dostępu opartą na tożsamości i rejestrowaniem inspekcji w celu uzyskania dostępu do danych.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Prowadzenie wykazu informacji poufnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.1 | 13.1 | Klient |

Tagi ułatwią śledzenie zasobów platformy Azure, które przechowują lub przetwarzają poufne informacje.

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemy izolowania przechowujące lub przetwarzające informacje poufne

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.2 | 13.2 | Klient |

Zaimplementuj oddzielne subskrypcje i/lub grupy zarządzania do zarządzania, testowania i produkcji. Zasoby powinny być oddzielone siecią wirtualną/podsiecią, odpowiednio oznakowane i zabezpieczone przez grupę sieciowej sieciowej lub zaporę platformy Azure. Zasoby przechowujące lub przetwarzające dane wrażliwe powinny być wystarczająco odizolowane. W przypadku maszyn wirtualnych przechowujących lub przetwarzających poufne dane należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są używane.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Jak tworzyć i używać tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć nsg z configiem zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć Zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alerty lub alerty i odmówić za pomocą Zapory platformy Azure:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorowanie i blokowanie nieautoryzowanego przesyłania poufnych informacji

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.3 | 13.3 | Klient |

Wdrożenie zautomatyzowanego narzędzia na obwodzie sieci, które monitoruje nieautoryzowane przesyłanie poufnych informacji i blokuje takie transfery, jednocześnie ostrzegając specjalistów od zabezpieczeń informacji.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Szyfrowanie wszystkich poufnych informacji podczas przesyłania

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.4 | 14.4 | Udostępnione |

Szyfruj wszystkie poufne informacje podczas przesyłania. Upewnij się, że klienci łączący się z zasobami platformy Azure są w stanie negocjować TLS 1.2 lub więcej.

W stosownych przypadkach należy postępować zgodnie z zaleceniami usługi Azure Security Center dotyczącymi szyfrowania w spoczynku i szyfrowania podczas przesyłania.

Poznaj szyfrowanie podczas przesyłania za pomocą platformy Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Identyfikowanie poufnych danych za pomocą aktywnego narzędzia do wykrywania

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.5 | 14.5 | Klient |

Jeśli żadna funkcja nie jest dostępna dla danej usługi na platformie Azure, użyj aktywnego narzędzia do odnajdywania innych firm, aby zidentyfikować wszystkie poufne informacje przechowywane, przetwarzane lub przesyłane przez systemy technologiczne organizacji, w tym te znajdujące się na miejscu lub w zdalnego dostawcy usług i zaktualizować spis poufnych informacji organizacji.

Usługa Azure Information Protection umożliwia identyfikowanie poufnych informacji w dokumentach usługi Office 365.

Użyj usługi Azure SQL Information Protection, aby pomóc w klasyfikacji i etykietowaniu informacji przechowywanych w bazach danych SQL azure.

Jak zaimplementować usługę Azure SQL Data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Jak zaimplementować usługę Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Sterowanie dostępem do zasobów za pomocą narzędzia Azure RBAC

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.6 | 14.6 | Klient |

Użyj usługi Azure AD RBAC, aby kontrolować dostęp do danych i zasobów, w przeciwnym razie użyć metod kontroli dostępu określonych w usłudze.

Poznaj usługę Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak skonfigurować rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Wymuszanie kontroli dostępu za pomocą funkcji zapobiegania utracie danych opartych na hoście

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.7 | 14.7 | Klient |

Zaimplementuj narzędzie innej firmy, takie jak zautomatyzowane rozwiązanie do zapobiegania utracie danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane z systemu.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Szyfrowanie poufnych informacji w spoczynku

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.8 | 14.8 | Klient |

Użyj szyfrowania w spoczynku na wszystkich zasobach platformy Azure. Firma Microsoft zaleca zezwolenie platformie Azure na zarządzanie kluczami szyfrowania, jednak w niektórych przypadkach istnieje opcja zarządzania własnymi kluczami. 

Poznaj szyfrowanie w spoczynku na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Jak skonfigurować klucze szyfrowania zarządzane przez klienta:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Rejestrowanie i ostrzeganie o zmianach w krytycznych zasobach platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 4.9 | 14.9 | Klient |

Użyj usługi Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące tego, kiedy zmiany mają miejsce w krytycznych zasobach platformy Azure.

Jak tworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [Zarządzanie lukami w zabezpieczeniach](security-control-vulnerability-management.md)
