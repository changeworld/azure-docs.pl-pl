---
title: Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure
description: Dowiedz się, jak używać InSpec do wykrywania problemów z wdrożeń platformy Azure
keywords: Azure, programu chef, metodyki devops, maszyny wirtualne, przegląd, automatyzacja, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629802"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Użyj InSpec zgodności automatyzacji infrastruktury platformy Azure

[InSpec](https://www.chef.io/inspec/) jest związane z programem Chef typu open-source języka opisu reguły zabezpieczeń i zgodności, które mogą być współużytkowane między programistów, operacji i inżynierowie zabezpieczeń. InSpec działa przez porównanie rzeczywisty stan infrastruktury za pomocą żądanego stanu, który można wyrazić w łatwych do zrozumienia i łatwe w zapisie InSpec kodu. InSpec wykrywa naruszeń i wyświetla wyniki w postaci raportu, ale umożliwia kontrolę korygowania.

InSpec służy do sprawdzania stanu zasobów i grup zasobów w ramach subskrypcji, w tym maszyn wirtualnych, konfiguracje sieci, ustawienia usługi Azure Active Directory i inne.

W tym artykule opisano zalety używania InSpec, aby ułatwić zabezpieczeń i zgodności na platformie Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Wprowadź łatwy do zrozumienia i ocena zgodności

Dokumentacja zgodności w języku arkuszy kalkulacyjnych lub dokumentów programu Word pozostawia wymagania otwarte do interpretacji. Za pomocą InSpec przekształcasz wymagań kod numerów wersji, pliku wykonywalnego, czytelny dla człowieka. Kod zastępuje rozmowy dotyczące powinien być oceniany na rzecz testów zawierają wymiernych z zamiarem Wyczyść.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywanie problemów w całej flocie i ustalić ich priorytety ich korygowania

InSpec użytkownika bez wykorzystania agentów wykrywa tryb umożliwiają szybko ocenić - skali — poziom usługi ekspozycji. Wbudowanych metadanych do oceniania wpływ/ważność pomaga ustalić, jakie obszary, aby skoncentrować się na dotyczącym korygowania. Można również pisać reguły szybko w odpowiedzi na nowych luk w zabezpieczeniach lub regulacji prawnych i wprowadzane natychmiast.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Przeprowadź inspekcję maszyn wirtualnych platformy Azure przy użyciu konfiguracji gościa zasad

Platforma Azure obsługuje bezpośrednio użytkowania Chef InSpec definicje Przeprowadź inspekcję maszyn wirtualnych platformy Azure za pośrednictwem [konfiguracji gościa zasad platformy Azure](/azure/governance/policy/concepts/guest-configuration). Konfiguracja gościa daje w wyniku maszyny wirtualnej z systemem Linux do podanej definicji Chef InSpec i raporty zgodności powrotem przy użyciu usługi Azure Policy. Wyniki tych inspekcji są również zgłaszane przez dzienników usługi Azure Monitor Włączanie alertów i innych scenariuszy usługi automation.

## <a name="satisfy-audits"></a>Spełniają inspekcji

Dzięki InSpec możesz reagować z inspekcji pytania w dowolnym momencie — nie tylko na poziomie wcześniej określonych przedziałach czasowych, np. kwartalnych lub co rok. Według działających nieprzerwanie InSpec testów, możesz wprowadzić przechodzić audyt wiedząc swoją ocenę stanu zgodności dokładne i historii, zamiast oczekiwano tak, przez ustalenia audytora.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"] 
> [Spróbuj InSpec w usłudze Azure Cloud Shell](https://shell.azure.com)