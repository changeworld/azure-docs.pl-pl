---
title: Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure
description: Dowiedz się, jak używać InSpec do wykrywania problemów z wdrożeń platformy Azure
keywords: Azure, programu chef, metodyki devops, maszyny wirtualne, przegląd, automatyzacja inspce
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 9256a4daf6564761553b495e559805a46e4eae32
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050731"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure
[InSpec](https://www.chef.io/inspec/) to bezpłatne i typu open source umożliwiająca badanie i przeprowadzanie inspekcji aplikacji i infrastruktury. InSpec działa przez porównanie z faktycznym stanem systemu za pomocą żądanego stanu, który można wyrazić w łatwych do zrozumienia i łatwe w zapisie InSpec kodu. InSpec wykrywa naruszeń i wyświetla wyniki w postaci raportu, ale umożliwia kontrolę korygowania. Można użyć InSpec, aby sprawdzić stan maszyn wirtualnych działających na platformie Azure. Można również użyć InSpec do skanowania i zweryfikuj stan zasobów i grup zasobów w ramach subskrypcji.

W tym artykule opisano zalety używania InSpec, aby ułatwić zabezpieczeń i zgodności na platformie Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Wprowadź łatwy do zrozumienia i ocena zgodności
Za pomocą InSpec przekształcasz wymagań kod numerów wersji, pliku wykonywalnego, czytelny dla człowieka. Dzięki temu można organizować testy w konfigurowalna profilów, gdzie definiowanie i dostosowywanie wyjątki, zgodnie z potrzebami.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywanie problemów w całej flocie i ustalić ich priorytety ich korygowania
Bez wykorzystania agentów InSpec wykrywa tryb umożliwiają szybko ocenić - skali — poziom usługi ekspozycji. Wbudowanych metadanych do oceniania wpływ/ważność pomaga ustalić, jakie obszary, aby skoncentrować się na dotyczącym korygowania.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Zbadaj maszyn, danych i nowych interfejsów API SaaS
Funkcje zgodności InSpec chmury interfejsu API umożliwiają tworzenie potwierdzenia zgrubnym i szczegółowych dotyczących zgodności w chmurze i raportować ją w stale.

## <a name="satisfy-audits"></a>Spełniają inspekcji
Dzięki InSpec możesz reagować z inspekcji pytania w dowolnym momencie — nie tylko na poziomie wcześniej określonych przedziałach czasowych, np. kwartalnych lub co rok. InSpec umożliwia wprowadzenie przechodzić audyt wiedząc poziomu dokładna zgodność zamiast oczekiwano tak, przez ustalenia audytora.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Zmniejsz niejednoznaczności i nieporozumienia dotyczące reguł
Dokumenty pozostaw konfiguracje i procesy Otwórz interpretacji. Kod wykonywalny usuwa konwersację na temat powinien być oceniany na rzecz testów zawierają wymiernych z zamiarem Wyczyść.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Na bieżąco gwałtownie zmieniające się krajobrazów zagrożenia i zgodności
InSpec pozwala na zapis i publikowania kod wykrywania tego samego dnia i zapisać nowe reguły w szybkich odpowiedzi na nowych regulacji. Oznacza to, że zmiany w zagrożenia lub regulacji prawnych nie jest już równa nagłych wypadkach.

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz maszynę wirtualną Windows na platformie Azure przy użyciu programu Chef](/azure/virtual-machines/windows/chef-automation)