---
title: Inteligentne korelacji alertów w usłudze Azure Security Center (zdarzenia) w chmurze | Dokumentacja firmy Microsoft
description: W tym temacie wyjaśniono, jak fusion używa chmury inteligentne korelacji alertów pod kątem generowania zdarzeń zabezpieczeń znajdują się w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571779"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Inteligentne korelacji alertów w usłudze Azure Security Center (zdarzenia) w chmurze

Usługa Security Center w sposób ciągły analizuje obciążeniach chmury hybrydowej przy użyciu zaawansowanej analizy i analizy zagrożeń ostrzega o złośliwych działań.

Szerokość pokrycia zagrożeń wraz ze wzrostem, i konieczność wykryć nawet najmniejszych oznaczenie naruszenia zabezpieczeń rośnie, może być trudne dla analityków zabezpieczeń do klasyfikowania różnych alertów i identyfikowania rzeczywistego ataku. Usługa Security Center pomaga analitykom poradzić sobie z zmęczenie alertów i diagnozowania ataków w miarę ich występowania, przez skorelowanie sygnałów o niskiej wierności i różne alerty na zdarzenia związane z bezpieczeństwem.

Łączenia jest technologia i analitycznych zaplecza z użyciem tego uprawnienia zdarzenia usługi Security Center, co umożliwia korelowanie różnych alertów i kontekstowych sygnały razem. Działania syntezy jądrowej, analizując różnym sygnałom zgłoszone w ramach subskrypcji w zasobach i znalezienie powszechnie znane wzorce, które pokazuje postęp ataku lub sygnalizuje udostępnione informacje kontekstowe, które wskazuje procedurę ujednoliconego odpowiedzi powinna być przekierowanie dla nich.

Analizy Fusion łączyć znajomość domeny zabezpieczeń dzięki sztucznej Inteligencji do analizowania alertów, odnajdywanie nowe wzorce ataków w miarę ich występowania. 

Usługa Security Center korzysta macierzy ataku MITRE skojarzyć alerty z ich zamiar postrzegany, pomagając formalnego znajomość domeny zabezpieczeń. Ponadto korzystając z informacji zebranych dla każdego kroku ataku, usługa Security Center można wykluczyć działanie, które wydaje się być kroki ataku, ale nie jest.  

Ponieważ ataków często występują w wielu różnych dzierżawach, usługa Security Center można połączyć algorytmów sztucznej Inteligencji do analizowania sekwencje ataków, które są zgłaszane w ramach każdej subskrypcji, aby zidentyfikować ich jako powszechnie znane wzorce alertu zamiast trwa przy okazji związany z każdą inne.

Podczas badania incydentu analitycy często muszą dodatkowego kontekstu w celu określenia rodzaju zagrożenia i sposobu jego wyeliminowania. Na przykład nawet w przypadku wykrycia anomalii sieci bez zrozumienia, co jeszcze jest wykonywane w sieci lub w odniesieniu do zasobów docelowych trudno jest zrozumieć, jakie działania należy podjąć w następnej kolejności. Aby ułatwić, zdarzenie naruszenia zabezpieczeń może zawierać artefakty, powiązane zdarzenia i informacje. Dodatkowe informacje dostępne dla zdarzenia związane z bezpieczeństwem różni się zależnie od typu wykrytego zagrożenia i konfiguracji środowiska. 

![Szczegóły zdarzenia zabezpieczeń](./media/security-center-alerts-cloud-smart/security-incident.png)

Aby lepiej zrozumieć związane z bezpieczeństwem, zobacz [sposób obsługi zdarzeń naruszenia zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

