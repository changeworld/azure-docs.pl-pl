---
title: Badanie przypadków z platformy Azure przez wartownika w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, dowiesz się, jak w celu zbadania sprawy z platformy Azure przez wartownika.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 6b3357ec06c89645b9c41e9efdb582a18af40672
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614633"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Samouczek: Badanie przypadków z platformy Azure przez wartownika w wersji zapoznawczej

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten samouczek ułatwia wykrywanie zagrożeń za pomocą platformy Azure przez wartownika.

Po zakończeniu [połączonych źródeł danych](quickstart-onboard.md) do platformy Azure przez wartownika, chcesz otrzymywać powiadomienia, gdy coś podejrzanego się dzieje. Umożliwia to zrobić, przez wartownika Azure pozwala tworzenia zaawansowanych reguł alertów, które generują przypadki, które można przypisać do użytkowania głęboko badania anomalii i zagrożeń w danym środowisku. 

> [!div class="checklist"]
> * Tworzenie przypadków
> * Badanie przypadków
> * Reagowanie na zagrożenia

## <a name="investigate-cases"></a>Badanie przypadków

Przypadek może obejmować wiele alertów. To Agregacja wszystkich odpowiednich dowodów w przypadku określonego dochodzenia. Alerty zdefiniowane w oparciu o utworzeniu sprawy **Analytics** strony. Właściwości związane z alertami, takie jak ważność i stan są ustawiane na poziomie przypadku. Po umożliwisz Azure przez wartownika wiedzieć, jakiego rodzaju zagrożenia, których szukasz i jak je znaleźć, można monitorować zagrożenia, które są wykrywane przez badanie przypadków. 

1. Wybierz **przypadków**. **Przypadków** strony informuje o tym, jak wiele przypadków, masz, ile jest otworzyć, ile ustawiono **w toku**, a ilu są zamknięte. W każdym przypadku widać, czas, w którym wystąpił, a stan sprawy. Przyjrzyj się ważność do podejmowania decyzji o obsługę pierwszy. W **przypadków** kliknij **alerty** kartę, aby wyświetlić wszystkie alerty, które są powiązane z przypadkiem. Jednostki, które są mapowane w wcześniej części mogą być wyświetlane w **jednostek** kartę.  Można filtrować przypadki, zgodnie z potrzebami, na przykład według stanu lub ważności. Podczas przeglądania **przypadków** kartę, zobaczysz otwartych spraw, które zawierają alertów wyzwalanych przez reguł wykrywania zdefiniowane w **analizy**. W górnej części zobaczysz swoje aktywnych spraw, nowe sprawy w przypadkach postępu. Widać również omówienie swoje przypadki według ważności.

   ![Pulpit nawigacyjny alertów](./media/tutorial-investigate-cases/cases.png)

2. Aby rozpocząć badanie, kliknij na konkretnym przypadku. Po prawej stronie można wyświetlić szczegółowe informacje, w przypadku, w tym o jego ważność, podsumowanie liczby jednostek (oparte na mapowanie). Każdy przypadek ma unikatowy identyfikator. Ważność przypadku jest określana zgodnie z najpoważniejsze alert zawarte w tym przypadku.  

1. Aby wyświetlić więcej szczegółów na temat alertów i jednostek w przypadku, kliknij **Wyświetl pełne szczegóły** w przypadku strony, a następnie przejrzyj odpowiednie karty, które podsumowują przypadków informacji.  Wyświetl pełne przypadków konsoliduje wszystkie dowody w alercie, skojarzonych alertach i jednostek.

1. W **alerty** pozycję Sprawdź sam - alert wyzwolenia została i za ile przekroczenia progów, zostanie ustawiona. Widać wszystkie informacje dotyczące alertu — kwerendę, która wyzwoliła alert liczbę wyników zwracanych przez zapytanie oraz możliwość Uruchom elementy playbook na alerty. Aby przejść do szczegółów w dół jeszcze więcej w tym przypadku kliknij liczbę trafień. Spowoduje to otwarcie kwerendy, który wygenerował wyników, a wyniki, które wyzwolony alert w usłudze Log Analytics.

3. W **jednostek** kartę, widać wszystkie jednostki, które można zamapować jako część definicji reguły alertu. 

4. Jeśli aktywnie analizujesz przypadek, to dobry pomysł, aby ustawić stan przypadku **w toku** dopóki nie zostanie zamknięty. Możesz też zamknąć przypadek, gdzie **zamknięty do rozwiązany** jest jego stan w sytuacjach, które wskazują, że incydent został obsłużony, podczas **zamknięte odrzuconych** jest jego stan w sytuacjach, które nie wymagają obsługi. Wyjaśnienia są wymagane, wyjaśniające, uzasadnienie zamykanie przypadek.

5. Można przypisać przypadków dla określonego użytkownika. Dla każdego przypadku może przypisać właściciela, ustawiając przypadku **właściciela** pola. Uruchomienie wszystkich przypadków jako nieprzypisane. Możesz przejść do przypadków i Filtruj według nazwy, aby zobaczyć wszystkie przypadki, których jesteś właścicielem. 

5. Kliknij przycisk **zbadaj** służy do wyświetlania Mapa badania i zakresu naruszenia z krokami korygującymi. 



## <a name="respond-to-threats"></a>Reagowanie na zagrożenia

Wartownik Azure daje dwie podstawowe opcje reagowanie na zagrożenia za pomocą elementów playbook. Można ustawić elementu playbook do automatycznego uruchamiania, gdy zostanie wyzwolony alert lub można ręcznie uruchomić elementu playbook w odpowiedzi na alert.

- Można ustawić elementu playbook do automatycznego uruchamiania, gdy alert jest wyzwalany, gdy skonfigurujesz element playbook. 

- Można ręcznie uruchomić elementu playbook z wewnątrz alert, klikając **Wyświetl elementy playbook** , a następnie wybierając elementu playbook do uruchomienia.




## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób rozpocząć badanie przy użyciu platformy Azure przez wartownika przypadków. Przejdź do samouczka dotyczącego [sposobu reagowania na zagrożenia za pomocą automatycznych elementy playbook](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) do automatyzowania odpowiedzi na zagrożenia.

