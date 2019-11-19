---
title: Używanie funkcji INSPEC do automatyzacji zgodności infrastruktury platformy Azure
description: Dowiedz się, jak używać metody INSPEC do wykrywania problemów z wdrożeniami platformy Azure
keywords: Azure, Chef, DevOps, Virtual Machines, przegląd, Automatyzacja, niespecyfikacja
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158225"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Używanie funkcji INSPEC do automatyzacji zgodności infrastruktury platformy Azure

[Inspecd](https://www.chef.io/inspec/) to Chef język "open source" umożliwiający opisywanie reguł zgodności & zabezpieczeń, które mogą być współużytkowane przez inżynierów oprogramowania, operacje i inżynierów ds. zabezpieczeń. Niespecyfikacja działa przez porównanie rzeczywistego stanu infrastruktury z żądanym stanem, który można łatwo odczytać i łatwo napisać kod specyfikacji. Specyfikacja INSPEC wykrywa naruszenia i wyświetla wyniki w postaci raportu, ale wprowadza kontrolę nad korygowaniem.

Funkcja INSPEC służy do sprawdzania stanu zasobów i grup zasobów w ramach subskrypcji, w tym maszyn wirtualnych, konfiguracji sieci, Azure Active Directory ustawień i innych elementów.

W tym artykule opisano zalety korzystania z funkcji INSPEC w celu łatwiejszego zapewniania bezpieczeństwa i zgodności z platformą Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Ułatwianie zrozumienia i oceny zgodności

Dokumentacja dotycząca zgodności zapisywana w arkuszach kalkulacyjnych lub dokumentach programu Word pozostawia wymagania otwarte do interpretacji. W przypadku niespecyfikacji można przekształcić wymagania do wersji, wykonywalnej, czytelnej dla człowieka kodu. Kod zastępuje konwersacje na temat tego, co powinno się ocenić na rzecz materialnych testów z wyraźnym zamiarem.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Wykrywanie problemów obejmujących wiele flot i określanie priorytetów ich korygowania

Tryb wykrywania bezagentowego INSPEC umożliwia szybką ocenę w skali — poziom ekspozycji. Wbudowane metadane dla oceny wpływu/ważności pomagają określić, które obszary mają być skoncentrowane na korygowaniu. Możesz również szybko pisać reguły w odpowiedzi na nowe luki lub regulacje i natychmiast je wycofać.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Inspekcja maszyn wirtualnych platformy Azure z konfiguracją gościa zasad

Platforma Azure bezpośrednio obsługuje używanie definicji specyfikacji Chef do inspekcji maszyn wirtualnych platformy Azure za pomocą [Azure Policy konfiguracji gościa](/azure/governance/policy/concepts/guest-configuration). Konfiguracja gościa szacuje maszynę wirtualną z systemem Linux w podanej definicji specyfikacji Chef i zgłasza zgodność z powrotem za pomocą Azure Policy. Wyniki tych inspekcji są również raportowane za pomocą dzienników Azure Monitor; Włączanie alertów i innych scenariuszy automatyzacji.

## <a name="satisfy-audits"></a>Zaspokoj inspekcje

W przypadku określenia niespecyfikacji można w dowolnym momencie odpowiedzieć na pytania dotyczące inspekcji — nie tylko w ustalonych odstępach czasu, takich jak co kwartał lub co rok. Nieustannie uruchamiając testy w firmie, należy wprowadzić cykl inspekcji wiedzący o dokładnym stan zgodności i historii, a nie jest to pożądane przez ustalenia audytorów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Wypróbuj niespecyfikację w Azure Cloud Shell](https://shell.azure.com)