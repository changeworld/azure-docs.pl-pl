---
title: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń za pomocą platformy Azure. Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak tworzyć niestandardowe reguły analityczne w celu wykrywania podejrzanych zagrożeń przy użyciu platformy Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 18c11198f6b81e72e371b3ab06ed3a7330078c52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023774"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Samouczek: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md)@no__t 1To platformy Azure, możesz utworzyć reguły niestandardowe, które mogą wyszukiwać określone kryteria w środowisku i generować zdarzenia, gdy kryteria są dopasowane, aby można było je zbadać. Ten samouczek ułatwia tworzenie reguł niestandardowych w celu wykrywania zagrożeń przy użyciu platformy Azure.

Ten samouczek pomaga wykrywać zagrożenia przy użyciu platformy Azure.
> [!div class="checklist"]
> * Tworzenie reguł analitycznych
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="create-custom-analytic-rules"></a>Tworzenie niestandardowych reguł analitycznych

Można utworzyć niestandardowe reguły analityczne ułatwiające wyszukiwanie typów zagrożeń i anomalii podejrzanych w danym środowisku. Reguła gwarantuje, że od razu otrzymasz powiadomienie, dzięki czemu można klasyfikacja, zbadać i skorygować zagrożenia.

1. W Azure Portal w obszarze wskaźnik platformy Azure wybierz pozycję **Analiza**.

1. Na górnym pasku menu wybierz pozycję **+ Utwórz** i wybierz pozycję **zaplanowana reguła zapytania**. Spowoduje to otwarcie **Kreatora tworzenia reguły niestandardowej**.

    ![Utwórz zaplanowane zapytanie](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na karcie **Ogólne** Podaj nazwę opisową i opisz ją. W razie potrzeby ustaw **ważność alertu** . Po utworzeniu reguły można ją włączyć, co spowoduje, że zostanie ona uruchomiona natychmiast po zakończeniu jej tworzenia. Jeśli utworzysz ją jako wyłączoną, reguła zostanie dodana do karty **aktywne reguły** i będzie można ją włączyć z tego miejsca, gdy będzie potrzebna.

    ![Rozpocznij tworzenie niestandardowej reguły analitycznej](media/tutorial-detect-threats-custom/general-tab.png)

1. Na karcie **Ustawienia** można bezpośrednio napisać zapytanie lub utworzyć zapytanie w log Analytics, a następnie wkleić je do pola **wyszukiwania zapytania** . W miarę zmieniania i konfigurowania zapytania wskaźnik myszy platformy Azure symuluje wyniki zapytania w oknie **Podgląd wyników** po prawej stronie. Pozwala to uzyskać wgląd w ilość danych, które zostałyby wygenerowane w określonym przedziale czasu dla tworzonego alertu. Ta kwota zależy od tego, co jest ustawione na potrzeby **uruchamiania zapytań co** i **wyszukiwania danych od ostatniego**. Jeśli zobaczysz, że na średnim poziomie Alert wywoła zbyt często alerty, możesz ustawić liczbę wyników wyższą, tak aby była większa niż średnia linia bazowa.

   ![Utwórz zapytanie na platformie Azure — wskaźnik](media/tutorial-detect-threats-custom/settings-tab.png)

   Oto przykładowe zapytanie, które może wysyłać alerty po utworzeniu nietypowej liczby zasobów w działaniu platformy Azure.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > Długość zapytania powinna wynosić od 1 do 1, 0000 znaków i nie może zawierać wartości "Search \*" ani "Union \*".

    1. W obszarze **planowanie zapytań**ustaw następujące parametry:

        1.  Ustaw **Uruchom zapytanie co** , aby ustawić **częstotliwość** uruchamiania zapytania — tak często, jak co 5 minut, lub tak rzadko, jak raz dziennie.

        1.  Ustaw **dane odnośnika od ostatniej** , aby kontrolować przedział czasu, w którym są uruchamiane zapytania — na przykład może być uruchamiane co godzinę, przez 60 minut danych.

        1. Możesz ustawić wskaźnik punktowy platformy Azure, aby **zatrzymać wykonywanie zapytania po wygenerowaniu alertu** , jeśli chcesz otrzymywać alerty tylko raz po wystąpieniu. Należy ustawić okno, w którym ma zostać zatrzymane zapytanie, nawet do 24 godzin.

    1. Zdefiniuj warunki wyzwalania alertu w ramach **wyzwalacza alertu**. W obszarze **Mapowanie jednostek**można mapować kolumny w kwerendzie do pól jednostki rozpoznawanych przez wskaźnik platformy Azure. Dla każdego pola Mapuj odpowiednią kolumnę w zapytaniu utworzonym w Log Analytics do odpowiedniego pola jednostki. Każda jednostka zawiera wiele pól, na przykład SID i GUID. Możesz zmapować jednostkę zgodnie z dowolnymi polami, a nie tylko jednostką wyższego poziomu.

1.  Na karcie **Automatyzowanie odpowiedzi** zaznacz wszystkie elementy PlayBook, które mają być uruchamiane automatycznie po wygenerowaniu alertu przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzowania elementy PlayBook, zobacz [reagowanie na zagrożenia](tutorial-respond-threats-playbook.md).

    ![Automatyzowanie odpowiedzi na zagrożenia na platformie Azure — wskaźnik](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Wybierz pozycję **Przejrzyj** , aby przejrzeć wszystkie ustawienia dla nowej reguły alertu, a następnie wybierz pozycję **Utwórz, aby zainicjować regułę alertu**.

   ![Przeglądanie kwerendy niestandardowej](media/tutorial-detect-threats-custom/review-tab.png)

1.  Po utworzeniu alertu reguła niestandardowa zostanie dodana do tabeli w obszarze **aktywne reguły**. Z tej listy można włączać, wyłączać lub usuwać każdą regułę.

1.  Aby wyświetlić wyniki utworzonych reguł alertów, przejdź do strony **incydenty** , na której można klasyfikacja, [zbadać zdarzenia](tutorial-investigate-cases.md)i skorygować zagrożenia.


> [!NOTE]
> Alerty generowane na platformie Azure — wskaźnikiem dostępności są dostępne za pomocą [Microsoft Graph zabezpieczenia](https://aka.ms/securitygraphdocs). Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją dotyczącą alertów zabezpieczeń Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).

