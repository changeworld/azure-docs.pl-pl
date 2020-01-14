---
title: Kontrola zabezpieczeń platformy Azure — Ochrona danych
description: Ochrona danych kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934516"
---
# <a name="security-control-data-protection"></a>Kontrola zabezpieczeń: Ochrona danych

Zalecenia dotyczące ochrony danych koncentrują się na rozwiązywaniu problemów dotyczących szyfrowania, list kontroli dostępu, kontroli dostępu opartej na tożsamościach i rejestrowania inspekcji dostępu do danych.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: przechowywanie spisu poufnych informacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.1 | 13,1 | Klient |

Użyj tagów, aby pomóc w śledzeniu zasobów platformy Azure, które przechowują lub przetwarzają informacje poufne.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: Izoluj systemy przechowujące lub przetwarzające informacje poufne

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.2 | 13.2 | Klient |

Zaimplementuj osobne subskrypcje i/lub grupy zarządzania na potrzeby tworzenia, testowania i produkcji. Zasoby powinny być oddzielone siecią wirtualną/podsieć, odpowiednio otagowane i zabezpieczone przez sieciowej grupy zabezpieczeń lub zaporę platformy Azure. Zasoby przechowujące lub przetwarzające dane poufne powinny być wystarczająco odizolowane. W przypadku Virtual Machines przechowywania lub przetwarzania danych poufnych należy zaimplementować zasady i procedury, aby je wyłączyć, gdy nie są używane.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Jak utworzyć Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Jak wdrożyć zaporę platformy Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Jak skonfigurować alert lub alert i odmówić przy użyciu zapory platformy Azure:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: Monitoruj i blokuj nieautoryzowany transfer informacji poufnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.3 | 13,3 | Klient |

Wdróż narzędzie automatyczne na obrzeżach sieci, które monitoruje do nieautoryzowanego transferu poufnych informacji i blokuje takie transfery podczas powiadamiania specjalistów ds. bezpieczeństwa informacji.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: Szyfruj wszystkie poufne informacje podczas przesyłania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.4 | 14,4 | Współdzielona |

Szyfruj wszystkie poufne informacje podczas przesyłania. Upewnij się, że wszyscy klienci łączący się z zasobami platformy Azure mogą negocjować protokół TLS 1,2 lub nowszy.

Postępuj zgodnie z zaleceniami Azure Security Center na potrzeby szyfrowania w czasie spoczynku i szyfrowania podczas przesyłania, jeśli ma to zastosowanie.

Informacje o szyfrowaniu podczas przesyłania na platformę Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Użyj aktywnego narzędzia do odnajdywania, aby identyfikować poufne dane

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.5 | 14,5 | Klient |

Jeśli dla konkretnej usługi na platformie Azure nie jest dostępna żadna funkcja, użyj narzędzia do odnajdywania aktywnego innej firmy, aby zidentyfikować wszystkie informacje poufne przechowywane, przetwarzane lub przekazywane przez systemy technologiczne organizacji, w tym te znajdujące się w lokacji lub w Dostawca usług zdalnych i aktualizacja spisu informacji poufnych organizacji.

Użyj Azure Information Protection, aby identyfikować poufne informacje w dokumentach pakietu Office 365.

Użyj usługi Azure SQL Information Protection, aby pomóc w klasyfikacji i etykietowania informacji przechowywanych w bazach danych Azure SQL Database.

Jak zaimplementować usługę Azure SQL Data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Jak zaimplementować Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: korzystanie z usługi Azure RBAC do kontrolowania dostępu do zasobów

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.6 | 14,6 | Klient |

Funkcja RBAC usługi Azure AD pozwala kontrolować dostęp do danych i zasobów, w przeciwnym razie używać metod kontroli dostępu specyficznych dla usługi.

Opis kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

Jak skonfigurować RBAC na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Wymuś kontrolę dostępu przy użyciu ochrony przed utratą danych opartą na hoście

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.7 | 14,7 | Klient |

Zaimplementuj narzędzie innej firmy, takie jak automatyczne rozwiązanie do ochrony przed utratą danych oparte na hoście, aby wymusić kontrolę dostępu do danych nawet wtedy, gdy dane są kopiowane poza system.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: Szyfruj poufne informacje w spoczynku

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4.8 | 14,8 | Klient |

Używaj szyfrowania w spoczynku dla wszystkich zasobów platformy Azure. Firma Microsoft zaleca, aby platforma Azure mogła zarządzać kluczami szyfrowania, jednak istnieje możliwość zarządzania własnymi kluczami w niektórych wystąpieniach. 

Informacje o szyfrowaniu na platformie Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Jak skonfigurować klucze szyfrowania zarządzane przez klienta:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: Rejestruj i Ostrzegaj o zmianach krytycznych zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 4,9 | 14,9 | Klient |

Użyj Azure Monitor z dziennikiem aktywności platformy Azure, aby utworzyć alerty dotyczące sytuacji, w których zmiany dotyczą najważniejszych zasobów platformy Azure.

Jak utworzyć alerty dla zdarzeń dziennika aktywności platformy Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [Zarządzanie](security-control-vulnerability-management.md) lukami w zabezpieczeniach
