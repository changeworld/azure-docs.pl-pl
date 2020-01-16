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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988502"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Monitorowanie użycia i wydatków za pomocą alertów o kosztach

Ten artykuł ułatwia zrozumienie i używanie alertów Cost Management do monitorowania użycia i wydatków platformy Azure. Alerty o kosztach są generowane automatycznie na podstawie zużycia zasobów platformy Azure. Alerty pokazują wszystkie aktywne zarządzanie kosztami i alerty dotyczące rozliczeń w jednym miejscu. Gdy użycie osiągnie daną wartość progową, alerty są generowane przez Cost Management. Istnieją trzy typy alertów dotyczących kosztów: alerty dotyczące budżetu, alerty dotyczące środków i alerty dotyczące limitu przydziału wydatków dla działu.

## <a name="budget-alerts"></a>Alerty dotyczące budżetu

Alerty budżetowe powiadamiają o wykorzystaniu, w zależności od użycia lub kosztu, osiągnie lub przekroczy kwotę zdefiniowaną w [warunku alertu budżetu](tutorial-acm-create-budgets.md). Budżety Cost Management są tworzone przy użyciu Azure Portal lub interfejsu API [użycia platformy Azure](https://docs.microsoft.com/rest/api/consumption) .

W witrynie Azure Portal budżety są definiowane na podstawie kosztu. W przypadku korzystania z interfejsu API użycia platformy Azure budżety są definiowane na podstawie kosztu lub użycia. Alerty dotyczące budżetu obsługują zarówno budżety oparte na kosztach, jak i oparte na użyciu. Alerty dotyczące budżetu są generowane automatycznie za każdym razem, gdy spełnione są warunki alertu dotyczącego budżetu. Wszystkie alerty dotyczące kosztów można wyświetlić w witrynie Azure Portal. Po wygenerowaniu alertu jest on wyświetlany w obszarze alertów dotyczących kosztów. Wiadomość e-mail z alertem jest również wysyłana do osób z listy odbiorców alertów dotyczących budżetu.

## <a name="credit-alerts"></a>Alerty dotyczące środków

Alerty dotyczące środków powiadamiają o wykorzystaniu zobowiązań pieniężnych w postaci środków na korzystanie z platformy Azure. Zobowiązania pieniężne są przeznaczone dla organizacji z umową Enterprise Agreement. Alerty dotyczące środków są generowane automatycznie po osiągnięciu 90% i 100% salda środków na korzystanie z platformy Azure. Po wygenerowaniu alertu jest on uwzględniany w alertach dotyczących kosztów i w wiadomościach e-mail wysyłanych do właścicieli konta.

## <a name="department-spending-quota-alerts"></a>Alerty dotyczące limitów przydziału wydatków dla działu

Alerty dotyczące limitów przydziału wydatków dla działu powiadamiają użytkownika, gdy wydatki działu osiągną ustalony próg limitu przydziału. Limity przydziału wydatków są konfigurowane w witrynie EA Portal. Za każdym razem, gdy zostanie osiągnięty próg, generowana jest wiadomość e-mail do właścicieli działów i ten fakt jest wyświetlany w alertach dotyczących kosztów. Próg może na przykład wynosić 50% lub 75% limitu przydziału.

## <a name="supported-alert-features-by-offer-categories"></a>Obsługiwane funkcje alertów według kategorii ofert

Obsługa typów alertów zależy od typu posiadanego konta platformy Azure (oferty firmy Microsoft). W poniższej tabeli przedstawiono funkcje alertów, które są obsługiwane przez różne oferty firmy Microsoft. Pełną listę ofert firmy Microsoft można znaleźć pod adresem [Cost Management danych](understand-cost-mgt-data.md).

| Typ alertu | Umowa Enterprise | Umowa klienta firmy Microsoft | Sieć Web Direct/płatność zgodnie z rzeczywistym użyciem |
|---|---|---|---|
| Budżet | ✔ | ✔ | ✔ |
| Środki | ✔ |✘ | ✘ |
| Limity przydziału wydatków dla działu | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Wyświetlanie alertów dotyczących kosztów

Aby wyświetlić alerty dotyczące kosztów, otwórz żądany zakres w witrynie Azure Portal i w menu wybierz pozycję **Budżety**. Użyj kapsułki **Zakres**, aby przełączyć się na inny zakres. W menu wybierz pozycję **Alerty dotyczące kosztów**. Aby uzyskać więcej informacji na temat zakresów, zobacz [Opis i współpraca z zakresami](understand-work-scopes.md).

![Przykładowy obraz przedstawiający alerty wyświetlane w usłudze Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Całkowita liczba aktywnych i odrzuconych alertów jest wyświetlana na stronie alertów dotyczących kosztów.

Dla wszystkich alertów jest wyświetlany typ alertu. W przypadku alertu dotyczącego budżetu jest wyświetlana przyczyna jego wygenerowania i nazwa budżetu, do którego ma on zastosowanie. Każdy alert zawiera datę wygenerowania, jego stan oraz zakres (subskrypcję lub grupę zarządzania), do którego odnosi się ten alert.

Alert może mieć stan **aktywny** i **odrzucony**. Stan aktywny oznacza, że alert jest nadal istotny. Stan odrzucony oznacza, że ktoś oznaczył alert, aby ustawić go jako nieistotny.

Wybierz alert z listy, aby wyświetlić jego szczegóły. Szczegóły alertu zawierają więcej informacji na temat alertu. Alerty dotyczące budżetu obejmują link do odpowiedniego budżetu. Jeśli dla alertu dotyczącego budżetu jest dostępna rekomendacja, jest również wyświetlany link do tej rekomendacji. Alerty dotyczące budżetu i środków oraz alerty dotyczące limitów przydziału wydatków dla działu mają link do analizy kosztów, gdzie można zbadać koszty w zakresie alertu. W poniższym przykładzie przedstawiono wydatki dotyczące działu z szczegółami alertu.

![Przykładowy obraz przedstawiający wydatki dla działu z informacjami o alercie](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Podczas przeglądania szczegółów odrzuconego alertu można go ponownie uaktywnić, jeśli jest potrzebne ręczne działanie. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykład obrazu z opcjami Odrzuć i Uaktywnij ponownie](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zobacz także

- Jeśli nie utworzono jeszcze budżetu lub nie ustawisz warunków alertów dla budżetu, Ukończ samouczek [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md) .
