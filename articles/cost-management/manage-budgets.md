---
title: Zarządzanie budżetami Cloudyn na platformie Azure
description: Ten artykuł ułatwia szybkie tworzenie budżetów i rozpocząć zarządzanie nimi w rozwiązaniu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9d7d0e049d3c35aab56145beb94c8e41e56c5785
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219096"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Zarządzanie budżetów platformy Azure z usługą Cloudyn

Ustawienie zapasowej budżetów i oparte na budżecie alerty pomocy, aby ulepszyć swoje nadzoru na chmurę i odpowiedzialności. Ten artykuł ułatwia szybkie tworzenie budżetów i rozpocząć zarządzanie nimi w rozwiązaniu Cloudyn.

Jeśli masz konto przedsiębiorstwa lub MSP, można użyć strukturę hierarchiczną koszt jednostki na potrzeby przypisywania miesięczne przydziały budżetu różne jednostki biznesowe, działów lub innych jednostek kosztów. Jeśli masz konto magazynu Premium, można użyć funkcji zarządzania budżetem, co jest następnie stosowane do Twojego wydatków całej chmurze. Wszystkich budżetów ręczne są przypisane.

Na podstawie przypisanej budżetów, można ustawić alerty progu na podstawie procentu budżetu, który jest używany i zdefiniuj ważność każdego progu.

Budżet Raporty zawierają przypisane budżetu. Użytkownicy mogą wyświetlać, po ich wydatków za pośrednictwem, w obszarze lub w wartości nominalnej z ich użycia wraz z upływem czasu. Po wybraniu opcji **Pokaż/Ukryj pola** u góry raportu budżetu można wyświetlić koszt, budżet, skumulowany koszt lub łączny budżet.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji na temat budżetów w Cost Management, zobacz [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Tworzenie budżetów

Kiedy tworzysz budżetu, ustaw ją na rok obrachunkowy i ma zastosowanie do określonej jednostki.

Aby utworzyć budżet i przypisać ją do jednostki:

1. Przejdź do **kosztów** &gt; **Cost Management** &gt; **budżetu**.
2. Na stronie Zarządzanie budżetem w obszarze **jednostki**wybierz jednostkę, w której chcesz utworzyć budżet.
3. W tym roku budżetu wybierz roku, w którym chcesz utworzyć budżetu.
4. W każdym miesiącu ustaw wartość budżetu. Gdy skończysz, kliknij przycisk **Zapisz**.
W tym przykładzie $135,000 ustawiono miesięczne budżetu czerwca 2018 r. Łączny budżet na rok jest 1,615,000.00 $.
![utworzyć stronę budżetu, na której ustawisz budżet dla każdego miesiąca](./media/manage-budgets/set-budget.png)


Aby zaimportować plik do rocznych budżetu:

1. W obszarze **Akcje**wybierz pozycję **Eksportuj** , aby pobrać pusty szablon CSV, który ma być używany jako podstawa budżetu.
2. Podaj plik CSV z wpisy budżetu i zapisać je lokalnie.
3. W obszarze **Akcje**wybierz pozycję **Importuj**.
4. Wybierz zapisany plik, a następnie kliknij przycisk **OK**.

Aby wyeksportować ukończony budżet jako plik CSV, w obszarze **Akcje**wybierz pozycję **Eksportuj** , aby pobrać plik.

## <a name="view-budget-in-reports"></a>Wyświetlanie budżetu w raportach

Po zakończeniu budżet jest pokazywany w większości raportów kosztów w ramach **kosztów** &gt; **analizy kosztów** i w raporcie koszt a budżet w czasie. Możesz również zaplanować raporty na podstawie progów budżetu przy użyciu **akcji**.

Poniżej przedstawiono przykładowy raport analizy kosztów. Przedstawia łączny budżet i koszt według typów obciążenia pracą i użycia od początku roku.

![Przykładowy raport analizy kosztów z budżetem](./media/manage-budgets/cost-analysis-budget-example.png)

W tym przykładzie przyjęto założenie, bieżąca data to 22 czerwca. Koszt czerwca 2018 r. jest $71,611.28 w porównaniu do budżetu miesięczne 135,000 $. Koszt jest znacznie niższa niż miesięcznego budżetu, ponieważ nadal istnieją ośmiu dni wydatków do końca miesiąca.

Innym sposobem, aby wyświetlić raport jest Przyjrzyj się programu vs skumulowany koszt Twojego budżetu. Aby wyświetlić skumulowane koszty, w obszarze **Pokaż/Ukryj pola**wybierz pozycję **skumulowany koszt** i **łączny budżet**. Oto przykład pokazujący skumulowany koszt od początku roku.

![Przykład skumulowanego kosztu i łącznego budżetu widocznego w raporcie koszt a budżet względem czasu](./media/manage-budgets/accumulated-budget.png)

W przyszłości chwilę skumulowany koszt może przekraczać Twojego budżetu. Można łatwiej zobaczyć, że w przypadku zmiany widoku wykresu na typ _wiersza_ .

![Budżet wyświetlane na wykresie liniowym kosztów według miesięcy raportu](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Tworzenie alertów budżetu filtru

W poprzednim przykładzie widać, że skumulowany koszt skontaktowali budżetu. Można tworzyć alerty automatyczne budżetu, dzięki czemu otrzymasz powiadomienie, gdy wydatki metod lub przekracza swój budżet. Zasadniczo ten alert to zaplanowany raport o progu. Budżet próg alertu dotyczącego metryki obejmują:

- Koszt pozostały a budżet — można określić próg wartość waluty
- Procent kosztów a budżet — Aby określić procent wartości progowej

Przyjrzyjmy się przykładowi.

W raporcie koszt a budżet w czasie kliknij pozycję **Akcje** , a następnie wybierz pozycję **raport o harmonogramie**. Na karcie wartości progowej wybierz metrykę, wartość progową. Na przykład **koszt procentu w porównaniu z budżetem**. Wybierz typ alertu, a następnie wprowadź wartość procentową budżetu. Jeśli chcesz otrzymywać powiadomienia tylko raz, wybierz **liczbę kolejnych alertów** , a następnie wpisz _1_. Kliknij pozycję **Zapisz**.

![Tworzenie alert dotyczący budżetu na Zapisz lub harmonogram tego pola raportu](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli nie wykonano jeszcze pierwszego samouczka dla programu Cloudyn, przeczytaj go przy [użyciu przeglądu użycia i kosztów](tutorial-review-usage.md).
- Dowiedz się więcej o [raportach dostępnych w programie Cloudyn](use-reports.md).
