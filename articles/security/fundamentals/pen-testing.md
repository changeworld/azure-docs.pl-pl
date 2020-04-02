---
title: Testowanie piór | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie procesu testowania penetracji (pentest) i jak wykonać pentest przeciwko aplikacjom uruchomionym w infrastrukturze platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549251"
---
# <a name="penetration-testing"></a>Testy penetracyjne
Jedną z zalet korzystania z platformy Azure do testowania i wdrażania aplikacji jest szybkie uzyskanie środowisk utworzonych. Nie musisz się martwić o rekwizycje, nabywanie i "regały i układanie" własnego sprzętu lokalnego.

To jest wielki - ale nadal trzeba upewnić się, że wykonujesz swoje normalne bezpieczeństwo due diligence. Jedną z rzeczy, które prawdopodobnie chcesz zrobić, to test penetracji aplikacji wdrażanych na platformie Azure.

Być może wiesz już, że firma Microsoft przeprowadza [testy penetracyjne naszego środowiska platformy Azure.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) Pomaga to w ulepszaniu platformy Azure.

Nie testujemy penetracji aplikacji dla Ciebie, ale rozumiemy, że będziesz chciał i musisz przeprowadzić testy na własnych aplikacjach. To dobrze, ponieważ gdy zwiększasz bezpieczeństwo aplikacji, możesz zwiększyć bezpieczeństwo całego ekosystemu platformy Azure.

Od 15 czerwca 2017 r. firma Microsoft nie wymaga już wstępnej zgody w celu przeprowadzenia testu penetracyjnego względem zasobów platformy Azure. Klienci, którzy chcą formalnie udokumentować nadchodzące działania związane z testowaniem penetracji platformy Microsoft Azure, są zachęcani do wypełnienia [formularza powiadomienia o testach penetracji usługi Azure.](https://portal.msrc.microsoft.com/en-us/engage/pentest) Ten proces jest powiązany tylko z platformą Microsoft Azure i nie ma zastosowania do żadnej innej usługi w chmurze firmy Microsoft.

>[!IMPORTANT]
>Chociaż powiadamianie firmy Microsoft o działaniach związanych z testowaniem pióra nie jest już wymagane, klienci muszą nadal przestrzegać [reguł testowania ujednoliconej penetracji](https://technet.microsoft.com/mt784683)przeglądarki Microsoft Cloud .

Standardowe testy, które można wykonać, obejmują:

* Testy punktów końcowych w celu wykrycia [10 największych luk w zabezpieczeniach projektu open web application security project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testy fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) punktów końcowych
* [Skanowanie portów](https://en.wikipedia.org/wiki/Port_scanner) punktów końcowych

Jednym z typów testów, których nie można wykonać, jest dowolny atak [typu "odmowa usługi" (DoS).](https://en.wikipedia.org/wiki/Denial-of-service_attack) Obejmuje to inicjowanie samego ataku DoS lub wykonywanie powiązanych testów, które mogą określać, demonstrować lub symulować dowolny typ ataku DoS.

## <a name="next-steps"></a>Następne kroki

- Jeśli chcesz formalnie udokumentować nadchodzące testy penetracyjne dotyczące aplikacji hostowanych na platformie Microsoft Azure, przejdź do [reguł testowania penetracji zaangażowania](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) i wypełnij formularz powiadomienia o testach.
