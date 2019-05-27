---
title: Zarządzanie budżetów Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia szybkie tworzenie budżetów i rozpocząć zarządzanie nimi w rozwiązaniu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9223f6d20f4f5e3901465379fe53c7b9c338e53f
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969165"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Zarządzanie budżetów platformy Azure z usługą Cloudyn

Ustawienie zapasowej budżetów i oparte na budżecie alerty pomocy, aby ulepszyć swoje nadzoru na chmurę i odpowiedzialności. Ten artykuł ułatwia szybkie tworzenie budżetów i rozpocząć zarządzanie nimi w rozwiązaniu Cloudyn.

Jeśli masz konto przedsiębiorstwa lub MSP, można użyć strukturę hierarchiczną koszt jednostki na potrzeby przypisywania miesięczne przydziały budżetu różne jednostki biznesowe, działów lub innych jednostek kosztów. Jeśli masz konto magazynu Premium, można użyć funkcji zarządzania budżetem, co jest następnie stosowane do Twojego wydatków całej chmurze. Wszystkich budżetów ręczne są przypisane.

Na podstawie przypisanej budżetów, można ustawić alerty progu na podstawie procentu budżetu, który jest używany i zdefiniuj ważność każdego progu.

Budżet Raporty zawierają przypisane budżetu. Użytkownicy mogą wyświetlać, po ich wydatków za pośrednictwem, w obszarze lub w wartości nominalnej z ich użycia wraz z upływem czasu. Po wybraniu **Pokaż/Ukryj pola** u góry raportu budżetu, można wyświetlić kosztów, budżet, skumulowany koszt lub Łączny budżet.

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji dotyczących budżetów kosztów zarządzania, zobacz [tworzenie i zarządzanie nimi budżetów](tutorial-acm-create-budgets.md).

## <a name="create-budgets"></a>Tworzenie budżetów

Kiedy tworzysz budżetu, ustaw ją na rok obrachunkowy i ma zastosowanie do określonej jednostki.

Aby utworzyć budżet i przypisać ją do jednostki:

1. Przejdź do **koszty** &gt; **Cost Management** &gt; **budżetu**.
2. Na stronie zarządzania budżetem w obszarze **jednostek**, wybierz jednostkę, której chcesz utworzyć budżetu.
3. W tym roku budżetu wybierz roku, w którym chcesz utworzyć budżetu.
4. W każdym miesiącu ustaw wartość budżetu. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz**.
W tym przykładzie $135,000 ustawiono miesięczne budżetu czerwca 2018 r. Łączny budżet na rok jest 1,615,000.00 $.
![Utwórz stronę budżetu, gdzie ustawiasz budżetu w każdym miesiącu](./media/manage-budgets/set-budget.png)


Aby zaimportować plik do rocznych budżetu:

1. W obszarze **akcje**, wybierz opcję **wyeksportować** można pobrać pusty szablon pliku CSV do użytku jako podstawa Twojego budżetu.
2. Podaj plik CSV z wpisy budżetu i zapisać je lokalnie.
3. W obszarze **akcje**, wybierz opcję **importu**.
4. Wybierz zapisany plik, a następnie kliknij przycisk **OK**.

Aby wyeksportować budżetu ukończone jako plik CSV, w obszarze **akcje**, wybierz opcję **wyeksportować** można pobrać pliku.

## <a name="view-budget-in-reports"></a>Wyświetlanie budżetu w raportach

Po zakończeniu swój budżet jest wyświetlany w większości raportach kosztów w obszarze **koszty** &gt; **analiza kosztów** w Cost vs. Raport budżetu względem czasu. Można także zaplanować raporty na podstawie progów budżetu za pomocą **akcje**.

Poniżej przedstawiono przykładowy raport analizy kosztów. Przedstawia łączny budżet i koszt według typów obciążenia pracą i użycia od początku roku.

![Przykładowy raport analizy kosztów z budżetem](./media/manage-budgets/cost-analysis-budget-example.png)

W tym przykładzie przyjęto założenie, bieżąca data to 22 czerwca. Koszt czerwca 2018 r. jest $71,611.28 w porównaniu do budżetu miesięczne 135,000 $. Koszt jest znacznie niższa niż miesięcznego budżetu, ponieważ nadal istnieją ośmiu dni wydatków do końca miesiąca.

Innym sposobem, aby wyświetlić raport jest Przyjrzyj się programu vs skumulowany koszt Twojego budżetu. Aby wyświetlić w obszarze łączne koszty **Pokaż/Ukryj pola**, wybierz opcję **skumulowany koszt** i **łączny budżet**. Oto przykład pokazujący skumulowany koszt od początku roku.

![Przykład zgromadzonych kosztów i Łączny budżet objętego Cost vs. Raport budżetu względem czasu](./media/manage-budgets/accumulated-budget.png)

W przyszłości chwilę skumulowany koszt może przekraczać Twojego budżetu. Możesz łatwiej zobaczyć, że w przypadku zmiany widoku wykresu do _wiersza_ typu.

![Budżet wyświetlane na wykresie liniowym kosztów według miesięcy raportu](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Tworzenie alertów budżetu filtru

W poprzednim przykładzie widać, że skumulowany koszt skontaktowali budżetu. Można tworzyć alerty automatyczne budżetu, dzięki czemu otrzymasz powiadomienie, gdy wydatki metod lub przekracza swój budżet. Zasadniczo ten alert to zaplanowany raport o progu. Budżet próg alertu dotyczącego metryki obejmują:

- Koszt pozostały a budżet — można określić próg wartość waluty
- Procent kosztów a budżet — Aby określić procent wartości progowej

Przyjrzyjmy się przykładowi.

W porównaniu z kosztami. Budżetu w czasie — raport, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Na karcie wartości progowej wybierz metrykę, wartość progową. Na przykład **budżet vs procent kosztów**. Wybierz typ alertu, a następnie wprowadź wartość procentową budżetu. Wybrać opcję otrzymywania tylko raz, **liczbę kolejnych alertów** , a następnie wpisz _1_. Kliknij pozycję **Zapisz**.

![Tworzenie alert dotyczący budżetu na Zapisz lub harmonogram tego pola raportu](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie została już zakończona pierwszego samouczka dotyczącego na platformie Cloudyn, zapoznaj się z adresem [przeglądanie użycia i kosztów](tutorial-review-usage.md).
- Dowiedz się więcej o [raportów dostępnych w rozwiązaniu Cloudyn](use-reports.md).
