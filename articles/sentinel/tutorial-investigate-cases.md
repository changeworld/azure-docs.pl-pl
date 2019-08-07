---
title: Zbadaj zdarzenia za pomocą wersji zapoznawczej platformy Azure wskaźnikowej | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zbadać zdarzenia za pomocą platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: bad3fddd6caf7e6eb455e59280f181c787b95a4e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780396"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel-preview"></a>Samouczek: Zbadaj zdarzenia za pomocą wersji zapoznawczej platformy Azure wskaźnikowej

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ten samouczek pomaga wykrywać zagrożenia przy użyciu platformy Azure.

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) z platformą Azure — użytkownik chce otrzymywać powiadomienia o wystąpieniu podejrzanych sytuacji. Aby można było to zrobić, wskaźnik "platformy Azure" umożliwia tworzenie zaawansowanych reguł alertów, które generują zdarzenia, które można przypisać i wykorzystać do głębokiego zbadania anomalii i zagrożeń w środowisku. 

> [!div class="checklist"]
> * Tworzenie zdarzeń
> * Badanie zdarzeń
> * Reagowanie na zagrożenia

## <a name="investigate-incidents"></a>Badanie zdarzeń

zdarzenie może zawierać wiele alertów. Jest to agregacja wszystkich istotnych dowodów dla konkretnego badania. zdarzenie jest tworzone na podstawie alertów zdefiniowanych na stronie **analizy** . Właściwości związane z alertami, takie jak ważność i stan, są ustawiane na poziomie incydentu. Po przejściu na platformę Azure wskaż, jakiego rodzaju zagrożenia szukasz i jak je znaleźć, możesz monitorować zagrożenia wykryte przez badanie zdarzeń. 

1. Wybierz **zdarzenia**. Na stronie incydenty są dostępne informacje o liczbie zdarzeń, liczbie otwartych, liczbie ustawionych **w toku**i liczbie zamkniętych. Dla każdego zdarzenia można zobaczyć czas jego wystąpienia i stan zdarzenia. Przyjrzyj się ważności, aby zdecydować, co należy najpierw obsłużyć. Na stronie **zdarzenia** kliknij kartę **alerty** , aby wyświetlić wszystkie alerty związane ze zdarzeniem. Jednostki, które zostały zmapowane wcześniej jako część zdarzenia, można wyświetlić na karcie **jednostki** .  Zdarzenia można filtrować zgodnie z wymaganiami, na przykład według stanu lub ważności. Po przejściu na kartę **incydenty** zobaczysz otwarte zdarzenia, które zawierają alerty wyzwalane przez reguły wykrywania zdefiniowane w analizie. W górnej części zobaczysz Twoje aktywne incydenty, nowe incydenty i zdarzenia w toku. Możesz również zapoznać się z omówieniem wszystkich incydentów według ważności.

   ![Pulpit nawigacyjny alertów](./media/tutorial-investigate-cases/cases.png)

2. Aby rozpocząć badanie, kliknij określone zdarzenie. Po prawej stronie można wyświetlić szczegółowe informacje o zdarzeniu, w tym jego ważność, podsumowanie liczby jednostek, których dotyczy (na podstawie mapowania). Każde zdarzenie ma unikatowy identyfikator. Ważność zdarzenia jest określana zgodnie z najbardziej poważnym alertem zawartym w zdarzeniu.  

1. Aby wyświetlić więcej szczegółów na temat alertów i jednostek w zdarzeniu, kliknij przycisk **Wyświetl pełne szczegóły** na stronie incydentu i przejrzyj odpowiednie karty podsumowujące informacje o zdarzeniu.  Widok pełnych zdarzeń konsoliduje wszystkie dowody w alercie, skojarzone alerty i jednostki.

1. Na karcie **alerty** Przejrzyj sam alert — gdy został wyzwolony i ile przekracza ustawiony próg. Można wyświetlić wszystkie istotne informacje dotyczące alertu — zapytanie, które wyzwoliło alert, liczbę wyników zwróconych na zapytanie oraz możliwość uruchamiania elementy PlayBook na alertach. Aby przejść do szczegółów zdarzenia, kliknij liczbę trafień. Spowoduje to otwarcie kwerendy, która wygenerowała wyniki i wyniki, które wyzwoliły alert w Log Analytics.

3. Na karcie **jednostki** można zobaczyć wszystkie jednostki, które zostały zmapowane w ramach definicji reguły alertu. 

4. Jeśli aktywnie badasz zdarzenie, dobrym pomysłem jest ustawienie stanu zdarzenia na **w toku** do momentu jego zamknięcia. Możesz również zamknąć zdarzenie, gdzie **zamknięte rozwiązanie** jest stanem zdarzeń wskazujących, że zdarzenie zostało obsłużone, podczas gdy **zamknięte** odrzucanie jest stanem zdarzeń, które nie wymagają obsługi. Wyjaśnienia są wymagane z wyjaśnieniem przyczyny zamknięcia zdarzenia.

5. Zdarzenia mogą być przypisane do określonego użytkownika. Dla każdego zdarzenia można przypisać właściciela, ustawiając pole **właściciel** zdarzenia. Wszystkie incydenty są uruchamiane jako nieprzypisane. Możesz przejść do incydentów i filtrować według swojej nazwy, aby zobaczyć wszystkie incydenty, których dysponujesz. 

5. Kliknij przycisk Zbadaj, aby wyświetlić mapę badań i zakres naruszenia z etapami korygowania. 



## <a name="respond-to-threats"></a>Reagowanie na zagrożenia

Wskaźnik na platformie Azure przedstawia dwie podstawowe opcje reagowania na zagrożenia przy użyciu elementy PlayBook. Można ustawić element PlayBook do automatycznego uruchamiania podczas wyzwalania alertu lub można ręcznie uruchomić element PlayBook w odpowiedzi na alert.

- Można ustawić element PlayBook do automatycznego uruchamiania, gdy zostanie wyzwolony alert podczas konfigurowania element PlayBook. 

- Możesz ręcznie uruchomić element PlayBook z poziomu alertu, klikając pozycję **Wyświetl elementy PlayBook** , a następnie wybierając element PlayBook do uruchomienia.




## <a name="next-steps"></a>Następne kroki
W ramach tego samouczka nauczysz się rozpocząć badanie zdarzeń przy użyciu platformy Azure — wskaźnik. Przejdź do samouczka dotyczącego [sposobu reagowania na zagrożenia przy użyciu zautomatyzowanego elementy PlayBook](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) w celu zautomatyzowania swoich odpowiedzi na zagrożenia.

