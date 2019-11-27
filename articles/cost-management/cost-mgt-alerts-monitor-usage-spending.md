---
title: Monitorowanie użycia i wydatków przy użyciu alertów o kosztach | Microsoft Docs
description: W tym artykule opisano sposób, w jaki alerty o kosztach ułatwiają monitorowanie użycia i wydatków w Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230115"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków za pomocą alertów o kosztach

Ten artykuł ułatwia zrozumienie i używanie alertów Cost Management do monitorowania użycia i wydatków platformy Azure. Alerty o kosztach są generowane automatycznie na podstawie zużycia zasobów platformy Azure. Alerty pokazują wszystkie aktywne zarządzanie kosztami i alerty dotyczące rozliczeń w jednym miejscu. Gdy użycie osiągnie daną wartość progową, alerty są generowane przez Cost Management. Istnieją trzy typy alertów dotyczących kosztów: alerty budżetu, alerty kredytowe i alerty dotyczące przydziału wydatków działu.

## <a name="budget-alerts"></a>Alerty budżetu

Alerty budżetowe powiadamiają o wykorzystaniu, w zależności od użycia lub kosztu, osiągnie lub przekroczy kwotę zdefiniowaną w [warunku alertu budżetu](tutorial-acm-create-budgets.md). Budżety Cost Management są tworzone przy użyciu Azure Portal lub interfejsu API [użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption) .

W Azure Portal budżety są definiowane według kosztu. W przypadku korzystania z interfejsu API użycia platformy Azure budżety są definiowane według kosztów lub użycia zużycia. Alerty budżetu obsługują zarówno budżety oparte na kosztach, jak i oparte na użyciu. Alerty budżetowe są generowane automatycznie za każdym razem, gdy spełnione są warunki alertu budżetowego. Wszystkie alerty dotyczące kosztów można wyświetlić w Azure Portal. Po wygenerowaniu alertu jest on wyświetlany w obszarze alerty o kosztach. Wiadomość e-mail z alertem jest również wysyłana do osób z listy adresatów alertów budżetu.

## <a name="credit-alerts"></a>Alerty kredytowe

Alerty kredytowe powiadamiają o wykorzystaniu zobowiązań pieniężnych środków na korzystanie z platformy Azure. Zobowiązania pieniężne są przeznaczone dla organizacji z umową Enterprise Agreement. Alerty kredytowe są generowane automatycznie o 90% i 100% salda środków na korzystanie z platformy Azure. Po wygenerowaniu alertu zostanie on uwzględniony w alertach o kosztach i w wiadomościach e-mail wysyłanych do właścicieli konta.

## <a name="department-spending-quota-alerts"></a>Alerty dotyczące przydziału wydatków działu

Alerty o limitach przydziału działu wydatków powiadamiają użytkownika, gdy dział wydatków działu osiągnie ustalony próg limitu przydziału. Limity przydziału wydatków są konfigurowane w portalu EA. Za każdym razem, gdy zostanie osiągnięty próg, generuje wiadomość e-mail do właścicieli działu i jest wyświetlana w alertach kosztów. Na przykład 50% lub 75% limitu przydziału.

## <a name="supported-alert-features-by-offer-categories"></a>Obsługiwane funkcje alertów według kategorii oferty

Obsługa typów alertów zależy od typu posiadanego konta platformy Azure (oferty firmy Microsoft). W poniższej tabeli przedstawiono funkcje alertów, które są obsługiwane przez różne oferty firmy Microsoft. Pełną listę ofert firmy Microsoft można znaleźć pod adresem [Cost Management danych](understand-cost-mgt-data.md).

| Typ alertu | Enterprise Agreement | Umowa klienta firmy Microsoft | Sieć Web Direct/płatność zgodnie z rzeczywistym użyciem |
|---|---|---|---|
| Wartościami | ✔ | ✔ | ✔ |
| Środki | ✔ |✘ | ✘ |
| Przydział wydatków działu | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Wyświetlanie alertów dotyczących kosztów

Aby wyświetlić alerty dotyczące kosztów, otwórz żądany zakres w Azure Portal a następnie wybierz pozycję **budżety** w menu. Użyj **zakresu** pill, aby przełączyć się do innego zakresu. Wybierz pozycję **alerty kosztów** w menu. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

![Przykład obrazu alertów pokazanych w Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Całkowita liczba aktywnych i odrzuconych alertów pojawia się na stronie alertów o kosztach.

Wszystkie alerty pokazują typ alertu. Alert dotyczący budżetu przedstawia przyczynę wygenerowania i nazwę budżetu, do którego ma zastosowanie. Każdy alert zawiera datę wygenerowania, jego stan oraz zakres (subskrypcję lub grupę zarządzania), do którego odnosi się ten alert.

Możliwy stan obejmuje **aktywny** i **odrzucony**. Stan aktywny oznacza, że alert jest nadal istotny. Stan odrzucony oznacza, że ktoś oznaczył alert, aby ustawić go jako nieistotny.

Wybierz alert z listy, aby wyświetlić jego szczegóły. Szczegóły alertu zawierają więcej informacji na temat alertu. Alerty budżetu obejmują łącze do budżetu. W przypadku udostępnienia zalecenia dotyczącego alertu dotyczącego budżetu zostanie również wyświetlony link do zalecenia. Alerty dotyczące budżetu, kredytu i działu wydatków mają związek z analizą kosztów w celu zbadania kosztów dla zakresu alertu. W poniższym przykładzie przedstawiono wydatki dotyczące działu z szczegółami alertu.

![Przykładowy obraz przedstawiający wydatki dla działu z informacjami o alercie](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Podczas przeglądania szczegółów odrzuconego alertu można go ponownie uaktywnić, jeśli jest potrzebne ręczne działanie. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład obrazu z opcjami Odrzuć i Uaktywnij ponownie](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zobacz też

- Jeśli nie utworzono jeszcze budżetu lub nie ustawisz warunków alertów dla budżetu, Ukończ samouczek [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) .
