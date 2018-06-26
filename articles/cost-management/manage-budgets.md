---
title: Zarządzanie budżetów w Azure kosztów zarządzania | Dokumentacja firmy Microsoft
description: W tym artykule opisano tworzenie i zarządzanie nimi budżetów kosztów zarządzania.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/25/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 9d6bf29909393846ec17a1bcc210fb989efd7f99
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938438"
---
# <a name="manage-budgets"></a>Zarządzanie budżetów

Ustawienie budżetów i na podstawie budżetu alerty pomoc, aby poprawić ładu chmury, a odpowiedzialności. Ten artykuł ułatwia szybkie tworzenie budżetów i rozpocząć zarządzanie nimi w kosztów zarządzania.

Jeśli masz konto Enterprise lub MSP, można użyć strukturę hierarchiczną koszt jednostki można przypisać do różnych jednostek biznesowych, działów lub dowolnej jednostki koszt miesięczne przydziały budżetu. Jeśli masz konto w warstwie Premium, można użyć funkcję zarządzania budżetu, która jest następnie stosowany do Twojej wydatków całego chmury. Wszystkie budżety są przypisywane ręcznie.

Na podstawie przypisanej budżetów, można ustawić próg alerty na podstawie procentu budżet wykorzystanie i zdefiniować ważność każdego progu.

Budżet Raporty zawierają przypisanej budżetu. Użytkownicy mogą wyświetlać, gdy ich wydatków za pośrednictwem, w obszarze lub na równi z ich użycia wraz z upływem czasu. Po wybraniu **pola Pokaż/Ukryj** u góry raportu budżetu, można wyświetlić koszt budżetu, koszt skumulowany albo całkowitego budżetu.

## <a name="create-budgets"></a>Tworzenie budżetów

Podczas tworzenia budżetu, należy ustawić dla roku obrachunkowego i dotyczy określonej jednostki.

Aby utworzyć budżet i przypisz je do jednostki:

1. Przejdź do **koszty** &gt; **koszt zarządzania** &gt; **budżetu**.
2. Na stronie Zarządzanie budżetu w obszarze **jednostek**, wybierz jednostki, w której chcesz utworzyć budżetu.
3. W roku budżetu wybierz roku, w którym chcesz utworzyć budżetu.
4. Dla każdego miesiąca ustaw wartość budżetu. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać**.
W tym przykładzie miesięczne budżet 2018 czerwca ustawiono 135,000 $. Całkowita liczba budżet roku jest 1,615,000.00 $.
![Tworzenie budżetu](./media/manage-budgets/set-budget.png)


Aby zaimportować plik do budżetu rocznego:

1. W obszarze **akcje**, wybierz pozycję **wyeksportować** pobrać pustego szablonu CSV, który ma być używana jako podstawa sieci budżetu.
2. Wypełnij plik CSV zawierający wpisy budżetu i zapisać go lokalnie.
3. W obszarze **akcje**, wybierz pozycję **importu**.
4. Wybierz zapisany plik, a następnie kliknij przycisk **OK**.

Aby wyeksportować ukończone Budżet jako plik CSV, w obszarze **akcje**, wybierz pozycję **wyeksportować** można pobrać pliku.

## <a name="view-budget-in-reports"></a>Wyświetlanie budżetu w raportach

Po zakończeniu budżetu jest wyświetlany w większości raportów kosztów w obszarze **koszty** &gt; **analizy kosztów** w vs kosztów. Raport budżetu w czasie. Można również zaplanować raporty na podstawie progów budżetu przy użyciu **akcje**.

Oto przykład raportu analizy kosztów. Przedstawia całkowity budżet i koszt w przeliczeniu na obciążenie i użycie typów od początku roku.

![Przykładowy raport analizy kosztów budżetu](./media/manage-budgets/cost-analysis-budget-example.png)

W tym przykładzie założono czerwca 22 jest data bieżąca. Koszt 2018 czerwca jest $71,611.28 w porównaniu do miesięcznego budżetu 135,000 $. Koszt jest znacznie niższa niż miesięcznego budżetu, ponieważ nadal są osiem dni wydatków do końca miesiąca.

Inny sposób, aby wyświetlić raport jest przyjrzeć się vs koszt skumulowany budżetu. Aby wyświetlić w obszarze koszt skumulowany **pola Pokaż/Ukryj**, wybierz pozycję **koszt skumulowany** i **całkowity budżet**. Oto przykład przedstawiający koszt skumulowany od początku roku.

![Skumulowany budżetu](./media/manage-budgets/accumulated-budget.png)

W przyszłości jakimś skumulowany koszt może przekroczyć budżetu. Można łatwiej stwierdzić, że w przypadku zmiany widoku wykresu do _wiersza_ typu.

![Budżet pokazano wykres liniowy](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Tworzenie budżetu alertów dla filtru

W poprzednim przykładzie widać, że koszt skumulowany osiągany budżet. Można tworzyć alerty automatyczne budżetu, dzięki czemu są powiadamiani o wydatków podejścia lub przekracza budżet. Alert jest zasadniczo zaplanowanych raportów o progu. Budżet próg alertu metryki obejmują:

- Pozostały koszt a budżet — Aby określić próg wartości waluty
- Procent kosztu a budżet — Aby określić procent wartości progowej

Oto przykład.

W programie vs kosztów. Budżet w czasie — raport, kliknij przycisk **akcje** , a następnie wybierz **zaplanować raport**. Na karcie wartości progowej wybierz metrykę próg. Na przykład **budżet vs procent kosztów**. Wybierz typ alertu i wprowadź wartość procentową budżetu. Jeśli chcesz uzyskać powiadomienia tylko raz, wybierz **liczba kolejnych alertów** , a następnie wpisz _1_. Kliknij pozycję **Zapisz**.

![Alert budżetu](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Kolejne kroki

- Jeśli nie zostało już ukończone pierwszy samouczek koszt zarządzania, przeczytaj je na [Przejrzyj użycia i koszty](https://docs.microsoft.com/en-us/azure/cost-management/tutorial-review-usage).
- Dowiedz się więcej o [raportów dostępnych w przystawce Zarządzanie koszt](use-reports.md).
