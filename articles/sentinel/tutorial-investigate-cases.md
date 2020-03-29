---
title: Badanie incydentów za pomocą usługi Azure Sentinel| Dokumenty firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak zbadać zdarzenia za pomocą usługi Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587196"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Samouczek: Badanie incydentów za pomocą usługi Azure Sentinel

> [!IMPORTANT]
> Wykres dochodzenia jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Ten samouczek ułatwia badanie zdarzeń za pomocą usługi Azure Sentinel. Po połączeniu źródeł danych z usługą Azure Sentinel użytkownik ma otrzymywać powiadomienia o podejrzanych zdarzeniach. Aby umożliwić ci to zrobić, usługa Azure Sentinel umożliwia tworzenie zaawansowanych reguł alertów, które generują zdarzenia, które można przypisać i zbadać.

Ten artykuł obejmuje:
> [!div class="checklist"]
> * Badanie zdarzeń
> * Użyj wykresu badania
> * Reagowanie na zagrożenia

Zdarzenie może zawierać wiele alertów. Jest to zestawienie wszystkich istotnych dowodów do konkretnego dochodzenia. Zdarzenie jest tworzone na podstawie reguł analitycznych utworzonych na stronie **Analytics.** Właściwości związane z alertami, takie jak ważność i stan, są ustawiane na poziomie zdarzenia. Po poinformowaniu usługi Azure Sentinel, jakiego rodzaju zagrożeń szukasz i jak je znaleźć, możesz monitorować wykryte zagrożenia, badając zdarzenia.

## <a name="prerequisites"></a>Wymagania wstępne
Zdarzenie będzie można badać tylko wtedy, gdy pola mapowania encji były używane podczas konfigurowania reguły analitycznej. Wykres dochodzenia wymaga, aby oryginalny incydent obejmował jednostki.

## <a name="how-to-investigate-incidents"></a>Jak badać zdarzenia

1. Wybierz **opcję Incydenty**. Strona **Zdarzenia** informuje, ile masz incydentów, ile jest otwartych, ile zostało **ustawionych**na W toku i ile jest zamkniętych. Dla każdego zdarzenia można zobaczyć czas, jaki wystąpił i stan zdarzenia. Spójrz na powagę, aby zdecydować, które zdarzenia do obsługi w pierwszej kolejności.

    ![Wyświetlanie ważności incydentu](media/tutorial-investigate-cases/incident-severity.png)

1. W razie potrzeby można filtrować zdarzenia, na przykład według stanu lub ważności.

1. Aby rozpocząć dochodzenie, wybierz konkretny incydent. Po prawej stronie można zobaczyć szczegółowe informacje o incydencie, w tym jego ważność, podsumowanie liczby zaangażowanych jednostek, nieprzetworzone zdarzenia, które wywołały ten incydent, oraz unikatowy identyfikator incydentu.

1. Aby wyświetlić więcej szczegółów na temat alertów i jednostek w zdarzeniu, wybierz **pozycję Wyświetl pełne szczegóły** na stronie zdarzenia i przejrzyj odpowiednie karty, które podsumowują informacje o zdarzeniu. Na karcie **Alerty** przejrzyj sam alert. Można zobaczyć wszystkie istotne informacje o alertie — kwerenda, która wyzwoliła alert, liczbę wyników zwróconych na kwerendę i możliwość uruchamiania podręczników w alertach. Aby przejść jeszcze dalej do incydentu, wybierz liczbę **zdarzeń**. Spowoduje to otwarcie kwerendy, która wygenerowała wyniki i zdarzenia, które wyzwoliły alert w usłudze Log Analytics. Na karcie **Jednostki** można wyświetlić wszystkie encje, które zostały zamapowane jako część definicji reguły alertu.

    ![Wyświetlanie szczegółów alertu](media/tutorial-investigate-cases/alert-details.png)

1. Jeśli aktywnie badasz incydent, warto ustawić stan zdarzenia na **W toku,** dopóki go nie zamkniesz.

1. Zdarzenia można przypisać do określonego użytkownika. Dla każdego zdarzenia można przypisać właściciela, ustawiając pole **Właściciel incydentu.** Wszystkie zdarzenia rozpoczynają się jako nieprzypisane. Możesz również dodać komentarze, aby inni analitycy mogli zrozumieć, co zbadałeś i jakie są Twoje obawy wokół incydentu.

    ![Przypisywanie zdarzenia do użytkownika](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Wybierz **opcję Zbadaj,** aby wyświetlić mapę dochodzenia.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Użyj wykresu badania do głębokiego nurkowania

Wykres dochodzenia umożliwia analitykom zadawanie właściwych pytań dla każdego dochodzenia. Wykres badania pomaga zrozumieć zakres i zidentyfikować główną przyczynę potencjalnego zagrożenia bezpieczeństwa, korelując odpowiednie dane z dowolną zaangażowaną jednostką. Można nurkować głębiej i zbadać dowolny podmiot przedstawiony na wykresie, wybierając go i wybierając między różnymi opcjami rozszerzenia.  
  
Wykres dochodzenia zapewnia:

- **Kontekst wizualny z nieprzetworzonych danych:** wykres na żywo, wizualny wyświetla relacje encji wyodrębnione automatycznie z nieprzetworzonych danych. Dzięki temu można łatwo zobaczyć połączenia między różnymi źródłami danych.

- **Odnajdowanie zakresu pełnego badania:** Rozwiń zakres badania przy użyciu wbudowanych zapytań eksploracyjnej, aby wyświetlić pełny zakres naruszenia.

- **Wbudowane kroki dochodzeniowe:** Użyj wstępnie zdefiniowanych opcji eksploracji, aby upewnić się, że zadajesz właściwe pytania w obliczu zagrożenia.

Aby użyć wykresu badania:

1. Wybierz zdarzenie, a następnie wybierz pozycję **Zbadaj**. Spowoduje to przejście do wykresu dochodzenia. Wykres zawiera mapę ilustracyjne jednostek bezpośrednio połączonych z alertem i każdego zasobu połączonego dalej.

   > [!IMPORTANT] 
   > Zdarzenie będzie można badać tylko wtedy, gdy pola mapowania encji były używane podczas konfigurowania reguły analitycznej. Wykres dochodzenia wymaga, aby oryginalny incydent obejmował jednostki.

   ![Wyświetlanie mapy](media/tutorial-investigate-cases/map1.png)

1. Wybierz encję, aby otworzyć okienko **Jednostki,** aby można było przeglądać informacje dotyczące tej encji.

    ![Wyświetlanie elementów na mapie](media/tutorial-investigate-cases/map-entities.png)
  
1. Rozwiń swoje dochodzenie, najeżdżając kursorem na każdą jednostkę, aby wyświetlić listę pytań, które zostały zaprojektowane przez naszych ekspertów w dziedzinie bezpieczeństwa i analityków według typu podmiotu w celu pogłębienia dochodzenia. Nazywamy te **opcje poszukiwania zapytań**.

    ![Poznaj więcej szczegółów](media/tutorial-investigate-cases/exploration-cases.png)

   Na przykład na komputerze można zażądać powiązanych alertów. Jeśli wybierzesz kwerendę poszukiwawczą, wynikowe wartości są dodawane z powrotem do wykresu. W tym przykładzie wybranie **alertów pokrewnych** zwróciło następujące alerty na wykresie:

    ![Wyświetlanie powiązanych alertów](media/tutorial-investigate-cases/related-alerts.png)

1. Dla każdej kwerendy poszukiwawczej można wybrać opcję otwierania wyników zdarzeń pierwotnych i kwerendy używanej w usłudze Log Analytics, wybierając opcję **Zdarzenia\>**.

1. Aby zrozumieć zdarzenie, wykres daje równoległą oś czasu.

    ![Wyświetlanie osi czasu na mapie](media/tutorial-investigate-cases/map-timeline.png)

1. Umieść wskaźnik myszy na osi czasu, aby zobaczyć, które rzeczy na wykresie wystąpiły w jakim momencie.

    ![Używanie osi czasu na mapie do badania alertów](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Następne kroki
W tym samouczku dowiesz się, jak rozpocząć badanie zdarzeń przy użyciu usługi Azure Sentinel. Przejdź do samouczka, [jak reagować na zagrożenia za pomocą automatycznych podręczników](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia,](tutorial-respond-threats-playbook.md) aby zautomatyzować reagowanie na zagrożenia.

