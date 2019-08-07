---
title: Zbadaj alerty za pomocą wersji zapoznawczej platformy Azure wskaźnikowej | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zbadać alerty za pomocą usługi Azure wskaźnikowej.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780424"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Samouczek: Wykrywaj zagrożenia przy użyciu wersji zapoznawczej platformy Azure wskaźnikowej

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) z platformą Azure — użytkownik chce otrzymywać powiadomienia o wystąpieniu podejrzanych sytuacji. Aby można było to zrobić, wskaźnik "platformy Azure" umożliwia tworzenie zaawansowanych reguł alertów, które generują zdarzenia, które można przypisać i wykorzystać do głębokiego zbadania anomalii i zagrożeń w środowisku. 

Ten samouczek pomaga wykrywać zagrożenia przy użyciu platformy Azure.
> [!div class="checklist"]
> * Tworzenie reguł wykrywania
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="create-detection-rules"></a>Tworzenie reguł wykrywania

Aby zbadać zdarzenia, najpierw musisz utworzyć reguły wykrywania. 

> [!NOTE]
> Alerty generowane na platformie Azure — wskaźnikiem dostępności są dostępne za pomocą [Microsoft Graph zabezpieczenia](https://aka.ms/securitygraphdocs). Zapoznaj się z dokumentacją dotyczącą [alertów zabezpieczeń Microsoft Graph](https://aka.ms/graphsecurityreferencebetadocs) , aby uzyskać więcej informacji i partnerów integracji.

Reguły wykrywania są oparte na typach zagrożeń i anomalii, które mogą być podejrzane w danym środowisku, które mają być od razu znane, dzięki czemu są one nawiązane, zbadane i skorygowane. 

1. W Azure Portal w obszarze wskaźnik platformy Azure wybierz pozycję **Analiza**.

   ![Analiza](./media/tutorial-detect-threats/alert-rules.png)

2. Na górnym pasku menu kliknij pozycję **+ Dodaj**.  

   ![Utwórz regułę alertu](./media/tutorial-detect-threats/create-alert-rule.png)

3. W obszarze **Utwórz regułę alertu**Podaj nazwę opisową i ustaw **ważność** odpowiednio do potrzeb. 

4. Utwórz zapytanie w Log Analytics, a następnie wklej je do pola **Ustaw regułę alertu** . Oto przykładowe zapytanie, które może wysyłać alerty po utworzeniu nietypowej liczby zasobów w działaniu platformy Azure.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > Długość zapytania powinna wynosić od 1 do 10000 znaków i nie może zawierać znaku "Search *" i "Union *".


5. W sekcji **Mapowanie jednostki** Użyj pól w obszarze **Typ jednostki** do mapowania kolumn w zapytaniu na pola jednostki rozpoznawane przez wskaźnik na platformie Azure. Dla każdego pola zamapuj odpowiednią kolumnę w zapytaniu, które zostało utworzone w Log Analytics, do odpowiedniego pola jednostki. Wybierz odpowiednią nazwę kolumny pod właściwością. Każda jednostka zawiera wiele pól, na przykład SID, GUID, itp. Możesz zmapować jednostkę zgodnie z dowolnymi polami, a nie tylko jednostką wyższego poziomu.

6. Zdefiniuj warunki wyzwalania alertu w ramach **wyzwalacza alertu**. Definiuje to warunki wyzwalające alert. 

7. Ustaw **częstotliwość** uruchamiania zapytania — tak często, jak co 5 minut, lub tak rzadko, jak raz dziennie. 

8. Ustaw **okres** kontroli przedziału czasu dla ilości danych, na które są uruchamiane zapytania — na przykład może być uruchamiany co godzinę przez 60 minut danych.

9. Można również ustawić pomijanie. Pomijanie jest przydatne, gdy chcesz zatrzymać duplikowanie alertów dla tego samego zdarzenia. W ten sposób można zatrzymać wyzwalanie alertów w określonym przedziale czasu. Może to pomóc uniknąć zduplikowanych alertów dla tego samego zdarzenia i pozwala na pomijanie kolejnych alertów w danym okresie czasu. Na przykład jeśli **częstotliwość** **planowania alertów** jest ustawiona na 60 minut, a **okres planowania alertów** jest ustawiony na dwie godziny, a wyniki zapytania przekraczają zdefiniowany próg, spowoduje to wyzwolenie alertu dwa razy, raz podczas pierwszego wykrycia w ciągu ostatnich 60 minut i ponownie, gdy trwa pierwsze 60 minut z 2-godzinnymi próbkami danych. Zaleca się, aby w przypadku wyzwolenia alertu pominięcie dla czasu ustawionego w okresie alertu. W naszym przykładzie możesz chcieć ustawić pomijanie przez 60 minut, aby alerty były wyzwalane tylko dla zdarzeń, które wystąpiły w ciągu ostatniej godziny.

8. Po wklejeniu zapytania do pola **Ustaw regułę alertów** można natychmiast zobaczyć symulację alertu w obszarze **symulacja alertów logiki** , aby uzyskać informacje o tym, ile danych zostanie wygenerowanych w określonym przedziale czasu dla alertu. został utworzony. Będzie to zależeć od tego, co ustawisz dla **częstotliwości** i **wartości progowej**. Jeśli widzisz tę wartość średnio, alert zostanie wywyzwalany zbyt często, należy ustawić liczbę wyników powyżej, tak aby była większa niż średnia linia bazowa.

9. Kliknij przycisk **Utwórz** , aby zainicjować regułę alertu. Po utworzeniu alertu zostanie utworzone zdarzenie zawierające alert. Zdefiniowane reguły wykrywania można wyświetlić jako wiersze na karcie **analiza zabezpieczeń** . Można także sprawdzić liczbę dopasowań dla każdej reguły — wyzwolone alerty. Z tej listy można włączać, wyłączać lub usuwać każdą regułę. Możesz również wybrać przycisk wielokropka (...) na końcu wiersza dla każdego alertu, aby edytować, wyłączyć, sklonować, pokazać dopasowania lub usunąć regułę. Strona **Analiza** jest galerią wszystkich aktywnych reguł alertów, w tym szablonów, które można włączyć, i reguł alertów tworzonych na podstawie szablonów.

1. Wyniki reguł alertów można zobaczyć na stronie **zdarzenia** , gdzie można klasyfikacja, [zbadać zdarzenia](tutorial-investigate-cases.md)i skorygować zagrożenia.



## <a name="automate-threat-responses"></a>Automatyzowanie odpowiedzi na zagrożenia

Zespoły SIEM/SOC mogą regularnie zasypaniu z alertami zabezpieczeń. Wygenerowane alerty są bardzo ogromne, a dostępni administratorzy zabezpieczeń są zapychani. W wyniku tego często zdarza się, że nie można zbadać wielu alertów, pozostawiając organizację narażoną na ataki, które nie są zauważalne. 

Wiele, jeśli nie większość z tych alertów, jest zgodny ze wzorcami cyklicznymi, które mogą być rozkierowane przez określone i zdefiniowane akcje naprawcze. Na platformie Azure wskaźnikiem można już definiować korygowanie w elementy PlayBook. Istnieje również możliwość ustawienia automatyzacji w czasie rzeczywistym jako części definicji element PlayBook, aby umożliwić pełne Automatyzowanie zdefiniowanej odpowiedzi dla konkretnych alertów zabezpieczeń. Korzystając z automatyzacji w czasie rzeczywistym, zespoły odpowiedzi mogą znacząco ograniczyć obciążenie, w pełni automatyzując rutynowe odpowiedzi na cykliczne typy alertów, co pozwala skoncentrować się na unikatowych alertach, analizowanie wzorców, łowiectwu zagrożeń i nie tylko.

Aby zautomatyzować odpowiedzi:

1. Wybierz Alert, dla którego chcesz zautomatyzować odpowiedź.
1. Z menu nawigacji obszaru roboczego wskaźnikowego platformy Azure wybierz pozycję **Analiza**.
1. Wybierz Alert, który chcesz zautomatyzować. 
1. Na stronie **Edytowanie reguły alertu** w obszarze **Automatyzacja w czasie rzeczywistym**wybierz **wyzwolone element PlayBook** , które chcesz uruchomić po dopasowaniu tej reguły alertu.
1. Wybierz pozycję **Zapisz**.

   ![Automatyzacja w czasie rzeczywistym](./media/tutorial-detect-threats/rt-configuration.png)


Ponadto można ręcznie skorygować alert, uruchamiając element PlayBook z poziomu alertu, klikając polecenie **Wyświetl elementy PlayBook** , a następnie wybierając element PlayBook do uruchomienia. Aby dowiedzieć się, jak utworzyć nowy element PlayBook lub edytować istniejący, zobacz [Praca z elementy PlayBook na platformie Azure — wskaźnik](tutorial-respond-threats-playbook.md).



## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure. 

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [jak reagować na zagrożenia przy użyciu zautomatyzowanego elementy PlayBook](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) w celu zautomatyzowania swoich odpowiedzi na zagrożenia.

