---
title: Zdarzenia usługi Azure Security Center — inteligentne korelacje alertów
description: W tym temacie wyjaśniono, jak fusion używa korelacji alertów inteligentnych w chmurze do generowania zdarzeń zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686496"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelacja alertów inteligentnych w chmurze w usłudze Azure Security Center (zdarzenia)

Usługa Azure Security Center stale analizuje obciążenia chmury hybrydowej przy użyciu zaawansowanej analizy i analizy zagrożeń, aby ostrzegać o złośliwej aktywności.

Zasięg zagrożenia rośnie. Potrzeba wykrycia nawet najmniejszego kompromisu jest ważna i może być wyzwaniem dla analityków bezpieczeństwa, aby klasyfikować różne alerty i zidentyfikować rzeczywisty atak. Usługa Security Center pomaga analitykom radzić sobie z tym zmęczeniem alertów. Pomaga diagnozować ataki w miarę ich występowania, korelując różne alerty i sygnały o niskiej wierności z zdarzeniami bezpieczeństwa.

Fusion analytics to technologia i analityczna zaplecze, które zasila incydenty w Centrum bezpieczeństwa, umożliwiając jej skorelowanie różnych alertów i sygnałów kontekstowych. Fusion analizuje różne sygnały zgłaszane w ramach subskrypcji w zasobach. Fusion wyszukuje wzorce, które ujawniają postęp ataku lub sygnały z udostępnionymi informacjami kontekstowymi, co wskazuje, że należy użyć dla nich ujednoliconej procedury odpowiedzi.

Analiza fuzji łączy wiedzę o domenie zabezpieczeń ze sztuczną inteligencją w celu analizowania alertów, odkrywania nowych wzorców ataków w miarę ich występowania. 

Usługa Security Center wykorzystuje macierz ataków MITRE do kojarzenia alertów z ich postrzeganymi intencjami, pomagając sformalizować wiedzę o domenie zabezpieczeń. Ponadto korzystając z informacji zebranych dla każdego kroku ataku, Usługa Security Center może wykluczyć działanie, które wydaje się być krokiem ataku, ale w rzeczywistości nie jest.

Ponieważ ataki często występują w różnych dzierżawach, usługa Security Center może łączyć algorytmy sztucznej inteligencji w celu analizowania sekwencji ataków zgłaszanych w każdej subskrypcji. Ta technika identyfikuje sekwencje ataków jako powszechne wzorce alertów, a nie tylko przypadkowo skojarzone ze sobą.

Podczas badania incydentu analitycy często potrzebują dodatkowego kontekstu, aby dojść do werdyktu na temat charakteru zagrożenia i sposobu jego złagodzenia. Na przykład nawet po wykryciu anomalii sieci, bez zrozumienia, co jeszcze dzieje się w sieci lub w odniesieniu do zasobu docelowego, trudno jest zrozumieć, jakie działania należy podjąć dalej. Aby pomóc, zdarzenie zabezpieczeń może zawierać artefakty, powiązane zdarzenia i informacje. Dodatkowe informacje dostępne dla zdarzeń związanych z zabezpieczeniami różnią się w zależności od typu wykrytego zagrożenia i konfiguracji środowiska. 

![Zrzut ekranu przedstawiający raport wykryty w przypadku zdarzenia zabezpieczeń](./media/security-center-alerts-cloud-smart/security-incident.png)

Aby lepiej zrozumieć zdarzenia związane z zabezpieczeniami, zobacz [Jak obsługiwać zdarzenia zabezpieczeń w usłudze Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-incident)

