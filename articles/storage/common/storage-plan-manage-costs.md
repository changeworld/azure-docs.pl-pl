---
title: Planowanie kosztów usługi Azure Storage i zarządzanie nimi
description: Dowiedz się, jak planować koszty usługi Azure Storage i zarządzać nimi, korzystając z analizy kosztów w witrynie Azure portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304526"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planowanie kosztów usługi Azure Storage i zarządzanie nimi

W tym artykule opisano sposób planowania i zarządzania kosztami usługi Azure Storage. Najpierw należy użyć kalkulatora cen platformy Azure, aby pomóc w planowaniu kosztów magazynu przed dodaniem jakichkolwiek zasobów. Po rozpoczęciu korzystania z zasobów usługi Azure Storage użyj funkcji zarządzania kosztami, aby ustawić budżety i monitorować koszty. Możesz również przejrzeć prognozowane koszty i monitorować trendy wydatków, aby zidentyfikować obszary, w których możesz chcieć działać.

Należy pamiętać, że koszty usługi Azure Storage stanowią tylko część miesięcznych kosztów na rachunku platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami usługi Azure Storage, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane dla subskrypcji platformy Azure, w tym usługi innych firm. Po zapoznaniu się z zarządzaniem kosztami usługi Azure Storage można zastosować podobne metody zarządzania kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Oszacuj koszty przed utworzeniem konta usługi Azure Storage

Kalkulator [cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) służy do szacowania kosztów przed utworzeniem i rozpoczęciem przenoszenia danych na konto usługi Azure Storage.

1. Na stronie [kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz kafelek Konta **magazynu.**

2. Przewiń stronę w dół i znajdź sekcję **Konta magazynu** w szacowanym oszacowaniu.

3. Wybierz opcje z list rozwijanych. 

   Podczas modyfikowania wartości tych list rozwijanych zmienia się szacowany koszt. Oszacowanie to pojawia się w górnym rogu, jak również w dolnej części oszacowania. 
    
   ![Monitorowanie kosztów za pomocą okienka Analiza kosztów](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Po zmianie wartości listy rozwijanej **Typ** zmieniają się również inne opcje wyświetlane w tym arkuszu. Skorzystaj z łączy w sekcji **Więcej informacji,** aby dowiedzieć się więcej o tym, co oznacza każda opcja i jak te opcje wpływają na cenę operacji związanych z magazynowaniem. 

4. Zmodyfikuj pozostałe opcje, aby zobaczyć ich wpływ na oszacowanie.

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, więc są one przydatne w ramach ogólnej strategii monitorowania kosztów. Jednak mogą one mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usługi platformy Azure, takimi jak koszt usługi Azure Storage, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitoruj koszty

Podczas korzystania z zasobów platformy Azure w usłudze Azure Storage ponosisz koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od przedziałów czasu (sekundy, minuty, godziny i dni) lub użycia jednostki (bajty, megabajty itd.) Koszty są ponoszone natychmiast po uruchomieniu użycia usługi Azure Storage. Koszty można zobaczyć w okienku [analizy kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md) w witrynie Azure portal.

Korzystając z analizy kosztów, można wyświetlić koszty usługi Azure Storage na wykresach i tabelach dla różnych przedziałów czasu. Niektóre przykłady to dzień, bieżący i poprzedni miesiąc i rok. Można również wyświetlać koszty w stosunku do budżetów i prognozowanych kosztów. Przełączanie się na dłuższe widoki w czasie może pomóc w zidentyfikowaniu trendów wydatków i zobaczeniu, gdzie mogło dojść do nadmiernych wydatków. Jeśli budżety zostały utworzone, możesz również łatwo zobaczyć, gdzie zostały przekroczone.

Aby wyświetlić koszty usługi Azure Storage w analizie kosztów:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).

2. Otwórz okno **Zarządzanie kosztami + Rozliczenia,** wybierz z menu **pozycję Zarządzanie kosztami,** a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **Zakres.**

   ![Monitorowanie kosztów za pomocą okienka Analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Aby wyświetlić tylko koszty usługi Azure Storage, wybierz pozycję **Dodaj filtr,** a następnie wybierz **pozycję Nazwa usługi**. Następnie wybierz **magazyn** z listy. 

   Oto przykład przedstawiający koszty tylko usługi Azure Storage:

   ![Monitorowanie kosztów magazynowania za pomocą okienka Analiza kosztów](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Koszty według regionów platformy Azure (lokalizacje) i według grupy zasobów również pojawiają się.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu kosztami za pomocą [analizy kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Zobacz następujące artykuły, aby dowiedzieć się więcej na temat funkcjonowania cen w usłudze Azure Storage:

- [Cennik przeglądu usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
- [Optymalizowanie kosztów magazynu obiektów blob przy użyciu pojemności zarezerwowanej](../blobs/storage-blob-reserved-capacity.md)
