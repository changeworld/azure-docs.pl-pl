---
title: Użyj InSpec automatyzacji zgodności infrastrukturę platformy Azure
description: Dowiedz się, jak używać InSpec do wykrywania problemów we wdrożeniach systemu Azure
keywords: Azure, chef, devops, maszyny wirtualne, omówienie można zautomatyzować, inspce
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Użyj InSpec automatyzacji zgodności infrastrukturę platformy Azure
[InSpec](https://www.chef.io/inspec/) jest bezpłatny i open source framework do testowania i inspekcji aplikacji i infrastruktury. InSpec działa na podstawie porównania ilości rzeczywisty stan systemu z pożądanego stanu, który express w kodzie InSpec łatwe do odczytania i łatwy do zapisu. InSpec wykrywa naruszeń i wyświetla wyniki w postaci raportu, ale pozwala w formancie korygowania. InSpec służy do sprawdzania stanu maszyn wirtualnych działających na platformie Azure. Umożliwia także InSpec do skanowania i sprawdzić stan zasobów i grup zasobów w ramach subskrypcji.

W tym artykule opisano korzyści wynikające ze stosowania InSpec ułatwiają zabezpieczeń i zgodności na platformie Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Umożliwiają łatwe do zrozumienia i oceny zgodności
Z InSpec możesz przekształcić wymagań do określonej wersji, pliku wykonywalnego, czytelny dla człowieka kodu. Umożliwia organizowanie testów w profilach zezwala na składanie, gdzie definiowanie i dostosowywanie wyjątki, zgodnie z potrzebami.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywaj problemy związane z floty całej oraz ustalić ich priorytety ich korygowania
Tryb wykrywania InSpec bez wykorzystania agentów pozwalają do szybkiej oceny — na dużą skalę — poziom zagrożeń. Wbudowanych metadanych oceniania wpływu/ważność pomaga określić obszary skoncentrować się na korygowania.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Sprawdź maszyny, danych i nowych interfejsów API usługi SaaS
Funkcje zgodności dotyczące interfejsu API InSpec chmury umożliwiają tworzenie zarówno grubą i szczegółowych potwierdzeniami dotyczącymi zgodności chmury, a raport na nim stale.

## <a name="satisfy-audits"></a>Spełniają inspekcji
Z InSpec może odpowiadać na inspekcji pytania w dowolnym momencie — nie tylko na poziomie wcześniej określonych przedziałach czasowych, takich jak co kwartał lub co rok. InSpec pozwala na wprowadzenie cyklu audytu uzyskiwanie informacji z postawie dokładnej zgodności, zamiast trwa oczekiwano przez ustalenia audytora.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Ograniczenia niejednoznaczności i powstanie dotyczące zasad
Dokumenty pozostaw konfiguracje i procesy Otwórz interpretacji. Kod wykonywalny usuwa konwersacji o powinien być oceniany na rzecz wymierne testy z zamiarem Wyczyść.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Nadąża z szybko ulegają zmianie krajobrazów zagrożeń i zgodność
InSpec służy do zapisu i Publikuj kod wykrywania tego samego dnia i zapisywanie nowych zasad w szybkich odpowiedzi na nowe przepisy. Oznacza to, że zmiany w zagrożeń lub rozporządzeń nie jest już równa zagrożeń.

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz maszynę wirtualną systemu Windows na platformie Azure przy użyciu Chef](/azure/virtual-machines/windows/chef-automation)