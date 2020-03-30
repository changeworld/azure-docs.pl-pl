---
title: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń za pomocą usługi Azure Sentinel| Dokumenty firmy Microsoft
description: Ten samouczek służy do tworzenia niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń za pomocą usługi Azure Sentinel.
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
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: cea7429ecea105355b0afe306bfa334e55d5d9c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585111"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Samouczek: Tworzenie niestandardowych reguł analitycznych w celu wykrywania podejrzanych zagrożeń

Po [połączeniu źródeł danych z usługą](quickstart-onboard.md) Azure Sentinel można utworzyć reguły niestandardowe, które mogą wyszukiwać określone kryteria w danym środowisku i generować zdarzenia, gdy kryteria są zgodne, aby można je zbadać. Ten samouczek ułatwia tworzenie reguł niestandardowych do wykrywania zagrożeń za pomocą usługi Azure Sentinel.

Ten samouczek ułatwia wykrywanie zagrożeń za pomocą usługi Azure Sentinel.
> [!div class="checklist"]
> * Tworzenie reguł analitycznych
> * Automatyzacja reakcji na zagrożenia

## <a name="create-custom-analytic-rules"></a>Tworzenie niestandardowych reguł analitycznych

Można utworzyć niestandardowe reguły analityczne, aby ułatwić wyszukiwanie typów zagrożeń i anomalii, które są podejrzane w twoim środowisku. Reguła zapewnia, że są powiadamiane od razu, dzięki czemu można klasyfikować, badać i korygować zagrożenia.

1. W witrynie Azure portal w obszarze Azure Sentinel wybierz pozycję **Analytics**.

1. Na górnym pasku menu wybierz pozycję **+Utwórz** i wybierz **regułę zaplanowane zapytanie**. Spowoduje to otwarcie **kreatora reguł Analytics**.

    ![Tworzenie zaplanowanej kwerendy](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Na karcie **Ogólne** podaj unikatową **nazwę**i **opis**. W polu **Taktyka** możesz wybierać spośród kategorii ataków, za pomocą których można sklasyfikować regułę. Ustaw **ważność** alertu w razie potrzeby. Podczas tworzenia reguły jej **stan** jest domyślnie **włączony,** co oznacza, że zostanie uruchomiony natychmiast po zakończeniu tworzenia. Jeśli nie chcesz, aby działała natychmiast, wybierz **pozycję Wyłączone**, a reguła zostanie dodana do karty **Aktywne reguły** i możesz ją włączyć w tym miejscu, gdy jest potrzebna.

    ![Rozpoczynanie tworzenia niestandardowej reguły analitycznej](media/tutorial-detect-threats-custom/general-tab.png)

1. Na karcie **Logika reguły ustawiania** można napisać kwerendę bezpośrednio w polu **Kwerenda reguły** lub utworzyć kwerendę w usłudze Log Analytics, a następnie skopiować ją i wkleić.
 
   ![Tworzenie kwerendy w usłudze Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Zobacz obszar **podglądu wyników** po prawej stronie, gdzie usługa Azure Sentinel pokazuje liczbę wyników (zdarzeń dziennika), które kwerenda wygeneruje, zmieniając w locie podczas pisania i konfigurowania zapytania. Wykres pokazuje liczbę wyników w określonym okresie czasu, który jest określany przez ustawienia w sekcji **Planowanie kwerend.**
    - Jeśli widzisz, że kwerenda wyzwoli zbyt wiele lub zbyt częste alerty, można ustawić linię bazową w sekcji **Próg alertu.**

      Oto przykładowa kwerenda, która ostrzega, gdy zostanie utworzona nietypowa liczba zasobów w usłudze Azure Activity.

      `AzureActivity
     \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
     \| where ActivityStatus == "Succeeded"
     \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

      > [!NOTE]
      > Długość zapytania powinna wynosić od 1 do 10 000 znaków i nie może zawierać "wyszukiwania" \*lub "unii". \*

    1. Użyj **sekcji Mapowanie jednostek,** aby połączyć parametry z wyników kwerendy z encjami rozpoznawanymi przez wartownika platformy Azure. Te jednostki stanowią podstawę do dalszej analizy, w tym grupowania alertów w zdarzenia na karcie **Ustawienia incydentów.**
    1. W sekcji **Planowanie kwerend** ustaw następujące parametry:

       1. Ustaw **uruchom kwerendę,** aby kontrolować częstotliwość uruchamiania kwerendy — tak często, jak co 5 minut lub tak rzadko, jak raz dziennie.

       1. Ustaw **dane odnośne z ostatniego,** aby określić okres czasu danych objętych kwerendą — na przykład może wysyłać zapytania do ostatnich 10 minut danych lub ostatnich 6 godzin danych.

       > [!NOTE]
       > Te dwa ustawienia są niezależne od siebie, do punktu. Kwerendę można uruchomić w krótkim odstępie czasu obejmującym okres dłuższy niż interwał (w efekcie z nakładającymi się kwerendami), ale nie można uruchomić kwerendy w przedziale przekraczającym okres zapotrzebowania, w przeciwnym razie będą miały luki w ogólnym zakresie zapytań.

    1. Użyj sekcji **Próg alertu,** aby zdefiniować linię bazową. Na przykład ustaw **Generuj alert, gdy liczba wyników kwerendy** **jest większa niż** i wprowadź liczbę 1000, jeśli chcesz, aby reguła wygenerowała alert tylko wtedy, gdy kwerenda generuje więcej niż 1000 wyników za każdym razem, gdy jest uruchamiana. Ponieważ jest to wymagane pole, jeśli nie chcesz ustawiać planu bazowego , czyli jeśli chcesz, aby alert rejestrował każde zdarzenie , wprowadź 0 w polu liczbowym.

    1. W sekcji **Tłumienie** można włączyć **opcję Zatrzymaj uruchamianie kwerendy po wygenerowaniu alertu,** jeśli po otrzymaniu alertu chcesz zawiesić działanie tej reguły na okres przekraczający interwał kwerendy. **On** Jeśli włączysz tę funkcję, należy ustawić **funkcję Zatrzymaj uruchomione zapytanie na** czas, przez który kwerenda powinna przestać działać, do 24 godzin.

1. Na karcie **Ustawienia zdarzeń** możesz wybrać, czy i jak usługa Azure Sentinel zamienia alerty w zdarzenia, które można zasłaniać. Jeśli ta karta pozostanie sama, usługa Azure Sentinel utworzy pojedynczy, oddzielny incydent od każdego alertu. Możesz wybrać opcję nietworzonych zdarzeń lub pogrupować kilka alertów w jeden incydent, zmieniając ustawienia na tej karcie.

    1. W sekcji **Ustawienia zdarzeń** **Utwórz zdarzenia z alertów wyzwalanych przez tę regułę analizy** jest domyślnie **ustawiona na Włączone,** co oznacza, że usługa Azure Sentinel utworzy pojedynczy, oddzielny incydent od każdego alertu wyzwalanego przez regułę.<br></br>Jeśli nie chcesz, aby ta reguła powodowała tworzenie jakichkolwiek incydentów (na przykład, jeśli ta reguła służy tylko do zbierania informacji do późniejszej analizy), ustaw tę pozycję **Na Wyłączone**.

    1. Jeśli w sekcji **Grupowanie alertów** chcesz, aby pojedyncze zdarzenie zostało wygenerowane z grupy podobnych lub cyklicznych alertów, ustaw **alerty związane z grupą, wyzwalane przez tę regułę analizy, na zdarzenia na** **Włączone**i ustaw następujące parametry.

    1. **Ogranicz grupę do alertów utworzonych w wybranym przedziale czasowym:**<br></br> Określ przedział czasu, w którym podobne lub cykliczne alerty będą zgrupowane. Wszystkie odpowiednie alerty w tym przedziale czasowym będą zbiorczo generować zdarzenie lub zestaw zdarzeń (w zależności od poniższych ustawień grupowania). Alerty poza tym przedziałem czasowym wygenerują osobny incydent lub zestaw zdarzeń.

    2. **Alerty grupowe wyzwalane przez tę regułę analizy w jednym incydencie:** Wybierz podstawę, na której alerty będą grupowane razem:

        - **Grupuj alerty w pojedynczym incydencie, jeśli wszystkie jednostki są zgodne:** <br></br>Alerty są zgrupowane razem, jeśli mają identyczne wartości dla każdego z mapowanych jednostek (zdefiniowane na karcie Logika reguły ustawiania powyżej). Jest to zalecane ustawienie.

        - **Pogrupuj wszystkie alerty wyzwalane przez tę regułę w jeden incydent:** <br></br>Wszystkie alerty generowane przez tę regułę są zgrupowane razem, nawet jeśli nie mają identycznych wartości.

        - **Grupuj alerty w pojedynczym incydencie, jeśli wybrane encje są zgodne:** <br></br>Alerty są zgrupowane razem, jeśli mają identyczne wartości dla niektórych mapowanych encji (które można wybrać z listy rozwijanej). Tego ustawienia można użyć, jeśli na przykład chcesz utworzyć oddzielne zdarzenia na podstawie źródłowych lub docelowych adresów IP.

    3. **Ponownie otwórz zamknięte pasujące zdarzenia:** Jeśli zdarzenie zostało zamknięte (co oznacza, że podstawowy problem został rozwiązany), a następnie generowany jest inny alert, który zostałby zgrupowany w tym incydencie, ustaw to ustawienie na **Włączone,** jeśli chcesz ponownie otworzyć zamknięty incydent, i pozostaw jako **Wyłączony,** jeśli chcesz, aby alert utworzył nowy incydent.

1. Na karcie **Automatyczne odpowiedzi** wybierz wszystkie podręczniki, które chcesz uruchomić automatycznie, gdy alert jest generowany przez regułę niestandardową. Aby uzyskać więcej informacji na temat tworzenia i automatyzacji podręczników, zobacz [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md).

1. Wybierz **pozycję Przejrzyj i utwórz,** aby przejrzeć wszystkie ustawienia nowej reguły alertów, a następnie wybierz pozycję **Utwórz, aby zainicjować regułę alertu**.
  
1. Po utworzeniu alertu do tabeli w obszarze **Aktywne reguły**dodawana jest reguła niestandardowa . Z tej listy można włączyć, wyłączyć lub usunąć każdą regułę.

1. Aby wyświetlić wyniki utworzonych reguł **alertów,** przejdź do strony Zdarzenia, na której możesz klasyfikować, [badać zdarzenia](tutorial-investigate-cases.md)i korygować zagrożenia.


> [!NOTE]
> Alerty generowane w usłudze Azure Sentinel są dostępne za pośrednictwem [programu Microsoft Graph Security](https://aka.ms/securitygraphdocs). Aby uzyskać więcej informacji, zobacz [dokumentację alertów zabezpieczeń programu Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak rozpocząć wykrywanie zagrożeń przy użyciu usługi Azure Sentinel.

Aby dowiedzieć się, jak zautomatyzować reagowanie na zagrożenia, [skonfiguruj automatyczne odpowiedzi na zagrożenia w usłudze Azure Sentinel.](tutorial-respond-threats-playbook.md)

