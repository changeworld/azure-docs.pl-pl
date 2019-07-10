---
title: Ocena w trybie offline — Personalizer
titleSuffix: Azure Cognitive Services
description: Tworzenie pętli opinii, w tym C# Szybki Start z usługą Personalizer.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 3fdedd1af9b683b221dfa4aebad7a30559b7abff
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722490"
---
# <a name="offline-evaluation"></a>Ocena w trybie offline

Ocena w trybie offline jest metoda, która umożliwia testowanie i ocena skuteczności usługi Personalizer bez zmieniania kodu ani wpływu na środowisko użytkownika. W ciągu ostatnich wykonano danych wysyłane z aplikacji do interfejsu API rangę, aby porównać jak różnym stopniu korzysta z oceny w trybie offline.

W trybie offline oceny odbywa się w zakresie dat. Zakres może zakończyć najpóźniej bieżący czas. Początek zakresu nie może być większa niż liczba dni określona dla [przechowywanie danych](how-to-settings.md).

Ocena w trybie offline może pomóc odpowiedzieć na następujące pytania:

* Jak efektywne są Personalizer rangę personalizacji pomyślnie?
    * Jakie są średnie nagrody, osiągnięte przez Personalizer uczenia maszynowego online zasady?
    * Jak wygląda porównanie Personalizer skuteczności co aplikacja będzie mieć wykonywana domyślnie?
    * Jaki byłby porównawczych skuteczności losowym na potrzeby personalizacji?
    * Jaki byłby porównawczych skuteczności zasad learning inny podany ręcznie?
* Które funkcje kontekstu biorących udział w tworzeniu bardziej lub mniej do personalizacji pomyślnie?
* Które funkcje akcje biorących udział w tworzeniu bardziej lub mniej do personalizacji pomyślnie?

Ponadto oceny w trybie Offline może służyć do wykrywania bardziej zoptymalizowane pod kątem uczenia zasad, które Personalizer umożliwia poprawienie wyników w przyszłości.

Obliczanie w trybie offline nie zapewniają wskazówki dotyczące procentową wartość zdarzeń na potrzeby eksploracji.

## <a name="prerequisites-for-offline-evaluation"></a>Wymagania wstępne dla oceny w trybie offline

Poniżej przedstawiono ważne informacje dotyczące reprezentatywny oceny w trybie offline:

* Mają wystarczającą ilość danych. Minimalna zalecana jest co najmniej 50 000 zdarzeń.
* Zbieranie danych z okresów z zachowania użytkowników reprezentatywny i ruch.

## <a name="discovering-the-optimized-learning-policy"></a>Odnajdywanie zasad zoptymalizowanych nauki

Personalizer umożliwia automatycznie odnajdywać bardziej optymalne zasad learning procesu oceny w trybie offline.

Po przeprowadzeniu oceny w trybie offline, możesz zobaczyć porównawczych skuteczności Personalizer za pomocą tego nowych zasad w porównaniu do bieżących zasad online. Można następnie zastosować tę zasadę uczenia się obowiązuje od razu Personalizer lub pobrać go do użytku lub przyszłej analizy.

## <a name="understanding-the-relevance-of-offline-evaluation-results"></a>Opis znaczenia wyniki oceny w trybie offline

Kiedy uruchamiasz ocenę w trybie offline, jest bardzo ważne analizować _granice zaufania_ wyników. Jeśli są one szerokie, oznacza to, że aplikacja nie odebrał wystarczającej ilości danych do szacowania nagradzania być dokładne lub ważne. System gromadzi większej ilości danych i uruchom oceny w trybie offline przez dłuższy czas, przedziału ufności stają się bardziej szczegółowy.

## <a name="how-offline-evaluations-are-done"></a>Jak w trybie offline są wykonywane obliczenia

Obliczanie w trybie offline są wykonywane przy użyciu metody o nazwie **Counterfactual oceny**. 

Personalizer jest oparty na założeniu użytkowników zachowanie (i tym samym rewards) są niemożliwe do prognozowania wsteczną (Personalizer nie wiedzieć, jakie miałby wystąpił, jeśli użytkownik ma zostały wyświetlone coś innego niż to widać) i tylko po to, aby dowiedzieć się od zmierzona nagrody. 

Jest to proces pojęciach, używane do oceny:

```
[For a given _learning policy), such as the online learning policy, uploaded learning policies, or optimized candidate policies]:
{
    Initialize a virtual instance of Personalizer with that policy and a blank model;

    [For every chronological event in the logs]
    {
        - Perform a Rank call
    
        - Compare the reward of the results against the logged user behavior.
            - If they match, train the model on the observed reward in the logs.
            - If they don't match, then what the user would have done is unknown, so the event is discarded and not used for training or measurement.
        
    }

    Add up the rewards and statistics that were predicted, do some aggregation to aid visualizations, and save the results.
}
```

W trybie offline oceny używa tylko zachowanie użytkowników obserwowanych. Ten proces powoduje odrzucenie dużych ilości danych, zwłaszcza wtedy, gdy aplikacja Rank wywołań w przypadku dużej liczby akcji.


## <a name="evaluation-of-features"></a>Obliczanie funkcji

Ocen w trybie offline może dostarczyć informacji o ile określonych funkcji, dla akcji lub kontekst są o wadze dla wyższe wynagrodzenie. Informacje jest obliczana na podstawie oceny względem danego okresu i danych i mogą się różnić w czasie.

Zalecamy spojrzenie na obliczanie funkcji ani zastanawiać się:

* Jakie funkcje inne, dodatkowe, można do aplikacji lub systemu zapewnia wzdłuż linii tych, które są bardziej skuteczne?
* Jakie funkcje może zostać usunięty z powodu niskiej efektywności? Dodawanie funkcji niskiej efektywności _szumu_ do uczenia maszynowego.
* Czy istnieją wszystkie funkcje wchodzące w skład przypadkowo? Te przykłady: identyfikowalne dane osobowe (PII) zduplikowane identyfikatory itp.
* Czy istnieje żadnych niepożądanych funkcji, które nie powinny służyć do personalizacji z powodu prawnych lub odpowiada Użyj uwagi? Dostępne są funkcje, które mogłyby serwera proxy (czyli ściśle duplikatów lub skorelować z) niepożądanych funkcji?


## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie Personalizer](how-to-settings.md)
