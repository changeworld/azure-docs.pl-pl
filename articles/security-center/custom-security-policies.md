---
title: Tworzenie niestandardowych zasad zabezpieczeń w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Niestandardowe definicje zasad platformy Azure monitorowane przez usługę Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258264"
---
# <a name="using-custom-security-policies"></a>Korzystanie z niestandardowych zasad zabezpieczeń

Aby zabezpieczyć swoje systemy i środowisko, usługa Azure Security Center generuje zalecenia dotyczące zabezpieczeń. Zalecenia te są oparte na najlepszych praktykach branżowych, które są włączone do ogólnych, domyślnych zasad zabezpieczeń dostarczanych wszystkim klientom. Mogą one również pochodzić z wiedzy Security Center na temat standardów branżowych i regulacyjnych.

Dzięki tej funkcji możesz dodawać własne inicjatywy *niestandardowe.* Jeśli środowisko nie będzie zgodne z utworzonymi zasadami, otrzymasz rekomendacje. Wszystkie tworzone inicjatywy niestandardowe pojawią się wraz z wbudowanymi inicjatywami na pulpicie nawigacyjnym zgodności z przepisami opisanym w samouczku [Poprawa zgodności z przepisami.](security-center-compliance-dashboard.md)

Jak wspomniano [w](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location) dokumentacji zasad platformy Azure, po określeniu lokalizacji dla inicjatywy niestandardowej musi to być grupa zarządzania lub subskrypcja. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Aby dodać niestandardową inicjatywę do subskrypcji 

1. Na pasku bocznym centrum zabezpieczeń otwórz stronę **Zasady zabezpieczeń.**

1. Wybierz subskrypcję lub grupę zarządzania, do której chcesz dodać inicjatywę niestandardową.

    [![Wybieranie subskrypcji, dla której utworzysz zasady niestandardowe](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Należy dodać standardy niestandardowe na poziomie subskrypcji (lub wyższym), aby były oceniane i wyświetlane w u centrum zabezpieczeń. 
    >
    > Po dodaniu standardu niestandardowego przypisuje *inicjatywę* do tego zakresu. W związku z tym zaleca się wybranie najszerszego zakresu wymaganego dla tego przypisania.

1. Na stronie Zasady zabezpieczeń w obszarze Inicjatywy niestandardowe kliknij pozycję **Dodaj inicjatywę niestandardową**.

    [![Kliknij **Dodaj inicjatywę niestandardową**](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Zostanie wyświetlona następująca strona:

    ![Tworzenie lub dodawanie zasad](media/custom-security-policies/create-or-add-custom-policy.png)

1. Na stronie Dodawanie inicjatyw niestandardowych przejrzyj listę zasad niestandardowych już utworzonych w organizacji. Jeśli widzisz taki, który chcesz przypisać do subskrypcji, kliknij przycisk **Dodaj**. Jeśli na liście nie ma inicjatywy spełnianej twoje potrzeby, pomiń ten krok.

1. Aby utworzyć nową inicjatywę niestandardową:

    1. Kliknij **pozycję Utwórz nowy**plik .
    1. Wprowadź lokalizację i nazwę definicji.
    1. Wybierz zasady do uwzględnienia i kliknij przycisk **Dodaj**.
    1. Wprowadź żądane parametry.
    1. Kliknij przycisk **Zapisz**.
    1. Na stronie Dodawanie inicjatyw niestandardowych kliknij przycisk Odśwież, a nowa inicjatywa będzie wyświetlana jako dostępna.
    1. Kliknij **pozycję Dodaj** i przypisz ją do subskrypcji.

    > [!NOTE]
    > Tworzenie nowych inicjatyw wymaga poświadczeń właściciela subskrypcji. Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [Uprawnienia w usłudze Azure Security Center](security-center-permissions.md).

    Twoja nowa inicjatywa wchodzi w życie i możesz zobaczyć wpływ na dwa sposoby:

    * Na pasku bocznym Centrum zabezpieczeń w obszarze Zgodność & zasady wybierz pozycję **Zgodność z przepisami**. Zostanie otwarty pulpit nawigacyjny zgodności, aby wyświetlić nową inicjatywę niestandardową wraz z wbudowanymi inicjatywami.
    
    * Zaczniesz otrzymywać rekomendacje, jeśli środowisko nie jest zgodne z zdefiniowanymi zasadami.

1. Aby wyświetlić wynikające z tego zalecenia dotyczące zasad, kliknij **pozycję Zalecenia** z paska bocznego, aby otworzyć stronę rekomendacji. Zalecenia pojawią się z etykietą "Niestandardowe" i będą dostępne w ciągu około godziny.

    [![Zalecenia niestandardowe](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak tworzyć niestandardowe zasady zabezpieczeń. 

W przypadku innych powiązanych materiałów zobacz następujące artykuły: 

- [Przegląd zasad bezpieczeństwa](tutorial-security-policy.md)
- [Lista wbudowanych zasad zabezpieczeń](security-center-policy-definitions.md)