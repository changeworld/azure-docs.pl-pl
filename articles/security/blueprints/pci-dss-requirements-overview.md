---
title: Plan przetwarzania płatności Azure — omówienie
description: Plan przetwarzania płatności Azure - macierzy odpowiedzialność klienta (omówienie)
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>Wymagania dotyczące PCI DSS — omówienie

Payment Card Industry Data zabezpieczeń Standard (PCI DSS) został opracowany do wspierania i zwiększyć bezpieczeństwo danych posiadacza globalnie ułatwienia szerokie przyjęcia środków bezpieczeństwa spójności danych. PCI DSS zapewnia linii bazowej wymagania techniczne i operacyjne przeznaczone do ochrony danych konta. PCI DSS ma zastosowanie do wszystkich jednostki biorące udział w przetwarzania kart płatności, w tym sprzedawców, procesory, nabywców, wystawców i dostawcy usług. PCI DSS ma również zastosowanie do wszystkich jednostek, które przechowywania, przetwarzania lub przesyłania danych posiadacza (CHD) i/lub dane poufne uwierzytelniania (SAD). Poniżej znajduje się omówienie wymagań PCI DSS 12.

> [!NOTE]
> Te wymagania są definiowane przez [Radę standardów zabezpieczeń Payment Card Industry (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako część [PCI danych zabezpieczeń Standard (DSS) w wersji 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Zapoznaj się PCI DSS, aby uzyskać informacje na temat testowania wskazówki dla każdego wymagania i procedury.

|   |   |
|---|---|
| **Tworzenie i obsługa bezpiecznej<br/>sieci i systemów** | 1. [Zainstalowanie i konserwowanie konfigurację zapory, aby chronić dane posiadacza](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Nie używaj dostarczonego przez dostawcę wartości domyślnych dla systemu hasła i inne parametry zabezpieczeń](pci-dss-requirement-2-password.md) |  
| **Ochrona danych posiadacza** | 3. [Chronić dane przechowywane posiadacza](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Szyfrowanie transmisji danych posiadacza sieciach otwarty, publiczny](pci-dss-requirement-4-encryption.md) |
| **Obsługa luka w zabezpieczeniach<br/>Program do zarządzania** | 5. [Ochrona wszystkich systemów przed złośliwym oprogramowaniem i regularnego aktualizowania oprogramowania antywirusowego lub programy](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Tworzenie i obsługa systemów zabezpieczeń i aplikacji](pci-dss-requirement-6-secure-system.md) |
| **Wdrożenie silnych dostępu<br/>środków kontroli** | 7. [Ograniczanie dostępu do danych posiadacza według potrzeb biznesowych należy wiedzieć](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identyfikację i uwierzytelnienie dostęp do składników systemu](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Ogranicz dostęp fizyczny do danych posiadacza](pci-dss-requirement-9-physical-access.md) |
| **Regularnie monitorować i<br/>Test sieci** | 10. [Śledzenie i monitorowanie dostęp do zasobów sieciowych i dane posiadacza](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Regularnie testować zabezpieczeń systemy i procesy](pci-dss-requirement-11-testing.md) |
| **Obsługa informacji o<br/>zasady zabezpieczeń** | 12. [Obsługa zasad, które dotyczy informacji zabezpieczeń dla wszystkich pracowników](pci-dss-requirement-12-policy.md) |

