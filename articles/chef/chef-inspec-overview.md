---
title: Używanie inspec do automatyzacji zgodności infrastruktury platformy Azure
description: Dowiedz się, jak używać inspec do wykrywania problemów we wdrożeniach platformy Azure
keywords: lazur, szef kuchni, devops, maszyny wirtualne, przegląd, automatyzować, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158225"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Używanie inspec do automatyzacji zgodności infrastruktury platformy Azure

[InSpec](https://www.chef.io/inspec/) to język open source firmy Chef do opisywania zasad zgodności zabezpieczeń &, które mogą być współużytkowane przez inżynierów oprogramowania, operacje i inżynierów zabezpieczeń. InSpec działa poprzez porównanie rzeczywistego stanu infrastruktury z żądanym stanem, który wyrażasz w łatwy do odczytania i łatwy do napisania kod InSpec. InSpec wykrywa naruszenia i wyświetla wyniki w formie raportu, ale daje ci kontrolę nad korygowania.

Za pomocą programu InSpec można sprawdzić poprawność stanu zasobów i grup zasobów w ramach subskrypcji, w tym maszyn wirtualnych, konfiguracji sieci, ustawień usługi Azure Active Directory i innych.

W tym artykule opisano korzyści wynikające z używania inspec, aby ułatwić bezpieczeństwo i zgodność na platformie Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Ułatw sobie zrozumienie i ocenę zgodności

Dokumentacja zgodności sporządzona w arkuszach kalkulacyjnych lub dokumentach programu Word pozostawia wymagania otwarte na interpretację. Za pomocą programu InSpec można przekształcić swoje wymagania w wersjonowany, wykonywalny, czytelny dla człowieka kod. Kod zastępuje konwersacje na temat tego, co należy oceniać na rzecz namacalnych testów z wyraźnym zamiarem.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywanie problemów całej floty i ustalanie priorytetów ich naprawy

Tryb wykrywania bez agenta InSpec umożliwia szybką ocenę - na dużą skalę - poziomu ekspozycji. Wbudowane metadane do oceniania wpływu/ważności pomaga określić, na jakich obszarach należy się skupić na korygowanie. Można również szybko napisać reguły w odpowiedzi na nowe luki lub przepisy i wdrożyć je natychmiast.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Inspekcja maszyn wirtualnych platformy Azure przy obliczu konfiguracji gościa zasad

Platforma Azure bezpośrednio obsługuje korzystanie z definicji programu Chef InSpec do inspekcji maszyn wirtualnych platformy Azure za pośrednictwem [konfiguracji gościa zasad azure.](/azure/governance/policy/concepts/guest-configuration) Konfiguracja gościa ocenia maszynę wirtualną systemu Linux do dostarczonej definicji programu Chef InSpec i raportuje zgodność z powrotem za pośrednictwem zasad platformy Azure. Wyniki tych inspekcji są również zgłaszane za pośrednictwem dzienników usługi Azure Monitor; włączanie alertów i innych scenariuszy automatyzacji.

## <a name="satisfy-audits"></a>Spełnianie audytów

Za pomocą programu InSpec możesz odpowiadać na pytania inspekcji w dowolnym momencie — nie tylko w ustalonych odstępach czasu, takich jak kwartalne lub roczne. Stale uruchamiając testy InSpec, należy wprowadzić cykl audytu, znając dokładną postawę zgodności i historię, zamiast być zaskoczony ustaleniami audytora.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Wypróbuj inspec w zabudowie chmury azure](https://shell.azure.com)