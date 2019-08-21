---
title: Użyj zaleceń Azure Security Center, aby zwiększyć bezpieczeństwo | Microsoft Docs
description: " Dowiedz się, jak korzystać z zasad zabezpieczeń i zaleceń w Azure Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 79a5f9a1269bdfc63d9d0b6fffd8458d011b777b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640880"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Użyj zaleceń Azure Security Center, aby zwiększyć bezpieczeństwo
Aby zmniejszyć prawdopodobieństwo wystąpienia poważnych zdarzeń zabezpieczeń, można skonfigurować zasady zabezpieczeń, a następnie zaimplementować zalecenia udostępniane przez Azure Security Center. W tym artykule pokazano, jak używać zasad zabezpieczeń i zaleceń w Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń. 

Security Center automatycznie uruchamia ciągłego skanowania w celu przeanalizowania stanu zabezpieczeń zasobów platformy Azure. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy zalecenia, które przeprowadzą Cię przez proces konfigurowania wymaganych kontroli zabezpieczeń. Security Center aktualizuje zalecenia w ciągu 24 godzin, z następującymi wyjątkami:

- Zalecenia dotyczące konfiguracji zabezpieczeń systemu operacyjnego zostały zaktualizowane w ciągu 48 godzin
- Zalecenia dotyczące Endpoint Protection problemów są aktualizowane w ciągu 8 godzin

## <a name="scenario"></a>Scenariusz
W tym scenariuszu przedstawiono sposób użycia Security Center, aby zmniejszyć prawdopodobieństwo wystąpienia zdarzenia związanego z zabezpieczeniami przez monitorowanie Security Center zaleceń i podejmowanie działań. W scenariuszu jest stosowana fikcyjna firma, firma Contoso i role przedstawione w [przewodniku planowania i](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)obsługi Security Center. W tym scenariuszu koncentrujemy się na rolach następujących osób:

![Role scenariusza](./media/security-center-using-recommendations/scenario-roles.png)

Firma Contoso niedawno przeprowadzono migrację niektórych zasobów lokalnych na platformę Azure. Firma Contoso chce chronić swoje zasoby i ograniczyć liczbę luk w zabezpieczeniach w chmurze.

## <a name="use-azure-security-center"></a>Użyj usługi Azure Security Center
David, od zabezpieczeń IT firmy Contoso, został już wybrany do dołączenia Security Center w subskrypcjach firmy Contoso w celu Azure Security Center zapobiegania i wykrywania luk w zabezpieczeniach. 

Security Center automatycznie analizuje stan zabezpieczeń zasobów platformy Azure firmy Contoso i stosuje domyślne zasady zabezpieczeń. Gdy Security Center identyfikuje potencjalne luki w zabezpieczeniach, tworzy **zalecenia** na podstawie kontrolek ustawionych w zasadach zabezpieczeń. 

David uruchamia usługę Azure Security Standard w ramach wszystkich subskrypcji w celu uzyskania pełnego zestawu zaleceń i funkcji zabezpieczeń. Jan również dołącza wszystkie istniejące serwery lokalne, które nie zostały jeszcze poddane migracji do chmury, dzięki czemu mogą korzystać z pomocy technicznej hybrydowej Security Center na serwerach z [systemami Windows](quick-onboard-windows-computer.md) i [Linux](quick-onboard-linux-computer.md) .

Jan jest właścicielem obciążenia w chmurze. Jan jest odpowiedzialny za stosowanie kontroli zabezpieczeń zgodnie z zasadami zabezpieczeń firmy Contoso. 

Jan wykonuje następujące zadania:

- Monitoruj zalecenia dotyczące zabezpieczeń udostępniane przez Security Center
- Oceń zalecenia dotyczące zabezpieczeń i zdecyduj, czy mają być stosowane lub odrzucane zalecenia.
- Zastosuj zalecenia dotyczące zabezpieczeń

### <a name="remediate-threats-using-recommendations"></a>Koryguj zagrożenia przy użyciu rekomendacji
W ramach codziennych działań monitorowania Jan loguje się do platformy Azure i otwiera Security Center. 

1. Jan wybiera subskrypcje obciążeń.

2. Jan sprawdza **bezpieczny wynik** , aby uzyskać ogólny obraz tego, jak zabezpieczy się subskrypcje i widzi, że wynik to 548.

3. Jan musi zdecydować, które zalecenia należy obsłużyć w pierwszej kolejności. Dlatego Jan klika polecenie Secure Score i zaczyna obsługiwać zalecenia w zależności od tego, jak znacznie zwiększa on [bezpieczny wpływ na ocenę](security-center-secure-score.md).

4. Ze względu na to, że program Jan ma wiele połączonych maszyn wirtualnych i serwerów, Marcin decyduje się skoncentrować na **obliczeniach i aplikacjach**.

5. Gdy Marcin kliknie pozycję **obliczeniową i aplikacje**, zobaczy listę zaleceń i obsłuży je zgodnie z bezpiecznym wpływem na ocenę.

6. Marcin ma wiele maszyn wirtualnych połączonych z Internetem, a ponieważ ich porty są ujawniane, są martwisz się, że osoba atakująca może przejąć kontrolę nad serwerami. Dlatego Jan wybiera, aby użyć (**dostęp just in Time do maszyny wirtualnej**) [Security-Center-just-in-Time.MD].

Marcin kontynuuje przechodzenie między zaleceniami o wysokim priorytecie i o średnim priorytecie i podejmuje decyzje dotyczące wdrożenia. W przypadku każdego zalecenia firma Jan przegląda szczegółowe informacje udostępniane przez Security Center, aby zrozumieć, jakie zasoby mają wpływ, jaki jest wpływ na to, jakie są te zalecenia, co oznacza, jakie są środki zaradcze w zakresie rozwiązywania problemów.

## <a name="conclusion"></a>Wniosek
Zalecenia dotyczące monitorowania w Security Center pomagają wyeliminować luki w zabezpieczeniach przed wystąpieniem ataku. W przypadku korygowania zaleceń Twój bezpieczny wynik i stan zabezpieczenia są ulepszane. Security Center automatycznie odnajduje nowe wdrożone zasoby, ocenia je przed zasadami zabezpieczeń i udostępnia nowe zalecenia dotyczące zabezpieczania tych zasobów.


## <a name="next-steps"></a>Następne kroki
Upewnij się, że masz proces monitorowania, w którym regularnie sprawdzasz zalecenia w Security Center, aby upewnić się, że zasoby są bezpieczne z upływem czasu.

W tym scenariuszu pokazano, jak używać zasad zabezpieczeń i zaleceń w Security Center, aby pomóc w ograniczeniu ataku zabezpieczeń. Zapoznaj się z scenariuszem reagowania na [zdarzenia](security-center-incident-response.md) , aby dowiedzieć się, jak mieć plan reagowania na zdarzenia przed rozpoczęciem ataku.

Dowiedz się, jak reagować na zagrożenia z odpowiedzią na [zdarzenia](security-center-incident-response.md).
