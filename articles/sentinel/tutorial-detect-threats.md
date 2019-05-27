---
title: Badanie alertów z platformy Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak badać alerty z usługą Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: 6cb40f8c9f1ee85848b5e3db311d0fb652ec1bc3
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921818"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Samouczek: Wykrywanie zagrożeń za pomocą platformy Azure przez wartownika w wersji zapoznawczej

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po zakończeniu [połączonych źródeł danych](quickstart-onboard.md) do platformy Azure przez wartownika, chcesz otrzymywać powiadomienia, gdy coś podejrzanego się dzieje. Umożliwia to zrobić, przez wartownika Azure pozwala tworzenia zaawansowanych reguł alertów, które generują przypadki, które można przypisać do użytkowania głęboko badania anomalii i zagrożeń w danym środowisku. 

Ten samouczek ułatwia wykrywanie zagrożeń za pomocą platformy Azure przez wartownika.
> [!div class="checklist"]
> * Tworzenie reguł wykrywania
> * Reagowanie na zagrożenia

## <a name="create-detection-rules"></a>Tworzenie reguł wykrywania

Aby zbadać przypadków, należy najpierw utworzyć reguły wykrywania. 

> [!NOTE]
> Alerty generowane przez wartownika platformy Azure są dostępne za pośrednictwem [Microsoft Graph Security](https://aka.ms/securitygraphdocs). Zapoznaj się [Microsoft Graph Security alerty dokumentacji](https://aka.ms/graphsecurityreferencebetadocs) uzyskać dalsze szczegóły oraz integracja z partnerami.

Reguły wykrywania bazują na typy zagrożeń i anomalie, które mogą być podejrzane w danym środowisku, którą chcesz wiedzieć o natychmiast, zapewniając są udostępniane, zbadać i skorygować. 

1. W witrynie Azure portal w obszarze Azure przez wartownika wybierz **analizy**.

   ![Analiza](./media/tutorial-detect-threats/alert-rules.png)

2. Na pasku menu u góry kliknij **+ Dodaj**.  

   ![Utwórz regułę alertu](./media/tutorial-detect-threats/create-alert-rule.png)

3. W obszarze **Utwórz regułę alertu**, wprowadź opisową nazwę i ustawić **ważność** zgodnie z potrzebami. 

4. Utwórz zapytanie w usłudze Log Analytics, a następnie wklej go do **reguły alertu zestaw** pola. Poniżej przedstawiono przykładowe zapytanie, która będzie wysyłania alertów, gdy nietypową liczbę zasobów jest tworzony w aktywności platformy Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > Długość zapytania powinny należeć do zakresu od 1 do 10000 znaków i nie może zawierać "search *" i "union *".


5. W **mapowania jednostki** sekcji, użyj pola w obszarze **typu jednostki** do mapowania pola jednostki rozpoznawane przez Azure przez wartownika kolumn w zapytaniu. Mapowanie odpowiedniej kolumnie w kwerendzie utworzonymi w usłudze Log Analytics do pola jednostki odpowiednią dla każdego pola. Wybierz nazwę odpowiedniej kolumnie w obszarze **właściwość**. Każda jednostka zawiera wiele pól, na przykład identyfikator SID identyfikatorem GUID, itp. Można mapować jednostki w kontekście dowolnego pola, a nie tylko górnego poziomu jednostki.

6. Zdefiniuj warunki wyzwalania alertu w obszarze **wyzwolenie alertu**. Definiuje warunki, które wyzwolenia alertu. 

7. Ustaw **częstotliwość** dla częstotliwości uruchamiane zapytanie — często co 5 minut lub rzadko jako raz dziennie. 

8. Ustaw **okres** do kontrolowania przedział czasu dla ilości danych, wykonywania kwerendy — na przykład go można uruchomić co godzinę przez 60 minut danych.

9. Można również ustawić **pomijanie**. Pomijanie jest przydatne, gdy chcesz zatrzymać zduplikowane alerty wyzwalane dla tego samego zdarzenia. W ten sposób można zatrzymać alerty wyzwalane w określonym czasie. Może to pomóc uniknąć zduplikowanych alertów dotyczących tego samego zdarzenia i pozwalają na pomijanie kolejnych alertów w okresie czasu. Na przykład jeśli **Alert planowania** **częstotliwość** jest ustawiona na 60 minut i **Alert planowania okres** jest ustawiony do dwóch godzin i wyniki zapytania przekroczenia zdefiniowane Próg, jego wyzwoli alert dwa razy, gdy kiedy najpierw zostanie wykryty w ciągu ostatnich 60 minut, i ponownie, gdy jest on w pierwszym 60 minut z 2 godzin próbkowane dane. Zaleca się, że jeśli alert jest wyzwalany, pomijanie powinny być ilości czasu w okresie alertu. W naszym przykładzie można ustawić pomijanie przez 60 minut, tak, aby tylko alertów dla zdarzeń, które wystąpiły podczas najbardziej aktualną godzinę.

8. Po wklejeniu zapytania do **reguły alertu zestaw** pola, można natychmiast zobaczyć symulacji alertu w obszarze **symulacji alert Logic** , dzięki czemu można uzyskiwać zrozumienie, jak dużo danych będzie generowane przez alert, który został utworzony z określonym interwałem. To będzie zależeć od skonfigurowanych dla **częstotliwość** i **próg**. Jeśli widzisz, że średnio, alert zostanie wyzwolony zbyt często, należy ustawić liczbę wyników wyższe, tak, aby powyżej średniej według planu bazowego.

9. Kliknij przycisk **Utwórz** można zainicjować reguły alertu. Po utworzeniu alertu, tworzony jest przypadek, który zawiera dany alert. Możesz zobaczyć reguł wykrywania zdefiniowane jako wiersze w **analizy zabezpieczeń** kartę. Widać również liczbę dopasowań dla każdej reguły - wyzwolonych alertów. Z tej listy możesz włączyć, wyłączyć lub usunąć każdej reguły. Użytkownik może również po prawej stronie — wybierz wielokropek (...) na końcu wiersza dla każdego alertu edytować, wyłączyć, klonowanie, Pokaż dopasowania lub usunąć regułę. **Analytics** strona jest galerii wszystkie aktywne reguły alertu, łącznie z szablonów zostanie włączone i reguły alertów można tworzyć oparte na szablonach.

1. Wyniki reguły alertów można zobaczyć w **przypadków** strony, w którym można klasyfikować, [badać przypadków](tutorial-investigate-cases.md), i usuwania zagrożeń.



## <a name="respond-to-threats"></a>Reagowanie na zagrożenia

Wartownik Azure daje dwie podstawowe opcje reagowanie na zagrożenia za pomocą elementów playbook. Można ustawić elementu playbook do automatycznego uruchamiania, gdy zostanie wyzwolony alert lub można ręcznie uruchomić elementu playbook w odpowiedzi na alert.

- Ustaw elementu playbook do automatycznego uruchamiania, gdy alert jest wyzwalany, gdy skonfigurujesz element playbook. 

- Ręcznie uruchomić elementu playbook z wewnątrz alert, klikając **Wyświetl elementy playbook** , a następnie wybierając elementu playbook do uruchomienia.




## <a name="next-steps"></a>Kolejne kroki
W tym samouczku pokazaliśmy ci, jak rozpocząć pracę, wykrywanie zagrożeń przy użyciu platformy Azure przez wartownika. 

Aby dowiedzieć się, jak zautomatyzować Twoje odpowiedzi na zagrożenia [sposobu reagowania na zagrożenia za pomocą automatycznych elementy playbook](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) do automatyzowania odpowiedzi na zagrożenia.

