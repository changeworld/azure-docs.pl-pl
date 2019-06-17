---
title: Monitorowanie użycia i wydatków z alertami koszt | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak koszt alerty pomocy monitorowanie użycia i wydatków w usłudze Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: f1bf62596b6edcc6fff6572e431f3a777be93f05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002090"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Używać alertów koszt monitorowanie użycia i wydatków

Ten artykuł pomoże Ci zrozumieć i zastosować Cost Management alerty monitorowania platformy Azure użycia i wydatków. Koszt alerty są generowane automatycznie na podstawie przypadku zasobów platformy Azure są używane. Alerty umożliwia wyświetlenie wszystkich aktywnych koszty zarządzania i alertów dotyczących rozliczeń ze sobą w jednym miejscu. Gdy swoje użycie osiąga wartość progową, alerty są generowane przez usługę Cost Management. Istnieją trzy typy alertów na koszt: alerty budżetu, środki alertów i alerty limit przydziału wydatków działu.

## <a name="budget-alerts"></a>Alerty budżetu

Budżet alerty powiadamiają użytkownika, na podstawie użycia lub koszt, osiągnie lub nadmiarowe wydatki przekracza kwotę zdefiniowane w [alert, stanu budżetu](tutorial-acm-create-budgets.md). Usługa Cost Management budżetów są tworzone przy użyciu witryny Azure portal lub [Azure Consumption](https://docs.microsoft.com/rest/api/consumption) interfejsu API.

W witrynie Azure portal budżety są definiowane przez kosztów. Za pomocą interfejsu API użycia platformy Azure, budżety są definiowane przez koszt lub za rzeczywiste użycie. Alerty budżetu obsługuje budżetów opartych na kosztach i na podstawie użycia. Budżet alerty są generowane automatycznie zawsze wtedy, gdy są spełnione warunki alertu budżetu. Wszystkie alerty koszt można wyświetlić w witrynie Azure portal. Zawsze, gdy alert jest generowany, jest wyświetlana na koszt alertów. Wiadomość e-mail z alertem również są wysyłane do osób z listy adresatów alertów budżetu.

## <a name="credit-alerts"></a>Alerty środków

Środki alerty informujące o tym, gdy są używane usługi zobowiązań pieniężnych platformy Azure w wysokości. Zobowiązań pieniężnych są przeznaczone dla organizacji mających umowy Enterprise Agreement. Środki alerty są generowane automatycznie na 90% i w 100% Saldo środków platformy Azure. Zawsze, gdy alert jest generowany, znajduje to odzwierciedlenie w alertach kosztów i w wiadomości e-mail wysyłanej do właścicieli konta.

## <a name="department-spending-quota-alerts"></a>Dział wydatków przydział alertów

Dział wydatków przydziału alerty powiadamiają użytkownika wydatków działu osiągnie Stały próg limitu przydziału. Limity przydziału wydatków są konfigurowane w portalu EA. Zawsze, gdy jest próg generuje wiadomość e-mail do działu właścicieli i jest wyświetlany w alertach kosztów. Na przykład co najmniej 75% przydziału 50%.

## <a name="supported-alert-features-by-offer-categories"></a>Obsługiwane funkcje alertów według kategorii oferty

Obsługa typów alertów, zależy od typu konta platformy Azure, czy masz (Microsoft oferują). W poniższej tabeli przedstawiono funkcje alertów, które są obsługiwane przez różne oferty firmy Microsoft. Można wyświetlić pełną listę ofert Microsoft [danych rozwiązania Cost Management zrozumieć](understand-cost-mgt-data.md).

| Typ alertu | Enterprise Agreement | Umowa klienta firmy Microsoft | W sieci Web bezpośrednio/płatności-zgodnie-You-rzeczywistym |
|---|---|---|---|
| Budżet | ✔ | ✔ | ✔ |
| Środki | ✔ |✘ | ✘ |
| Limit przydziału wydatków działu | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Wyświetlanie kosztów alertów

Aby wyświetlić alerty kosztów, otwórz żądany zakres w witrynie Azure portal i wybierz pozycję **budżetów** w menu. Użyj **zakres** skażone, aby przełączyć się do innego zasięgu. Wybierz **koszt alerty** w menu. Aby uzyskać więcej informacji na temat zakresów, zobacz [poznawanie i Praca z zakresami](understand-work-scopes.md).

![Przykładowy obraz alertów przedstawianych w Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Całkowita liczba alertów aktywnych i odrzuconych pojawia się na stronie alertów kosztów.

Wszystkie alerty Pokaż typu alertu. Alert dotyczący budżetu przedstawiono przyczyny, dlaczego został wygenerowany i nazwę budżetu, których on obowiązuje. Każdy alert zawiera datę został wygenerowany, jego stan i zakres (subskrypcję lub grupę zarządzania), który dotyczy alert.

Stan możliwe obejmuje **active** i **odrzucono**. Aktywny stan wskazuje, czy alert jest nadal obowiązują. Odrzuconych stan wskazuje, czy ktoś oznaczył alert, aby ustawić go jako nie są już odpowiednie.

Wybierz alert z listy, aby wyświetlić jego szczegóły. Szczegóły alertu pokazują więcej informacji na temat alertu. Alerty budżetu zawierają łącza do budżetu. Jeśli zalecenie jest dostępny dla alert dotyczący budżetu, łącza do zalecenia również jest wyświetlany. Budżetu, środki i działu wydatków przydziału alerty mają łącza do analizować na analizy kosztów, gdzie możesz eksplorować kosztów dla zakresu ten alert. Poniższy przykład pokazuje wydatków dla działu ze szczegółami alertu.

![Przykładowy obraz przedstawiający wydatków dla działu ze szczegółami alertu](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Podczas wyświetlania szczegółów alertu odrzuconych możesz uaktywnić go ponownie, jeśli jest wymagana ręczna Akcja. Na poniższej ilustracji przedstawiono przykładowy raport.

![Przykładowy obraz przedstawiający odrzucić, a następnie ponownie aktywować opcje](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zobacz także

- Jeśli nie zostały już utworzone budżetu lub ustawić warunki alertu budżetu, wykonaj [tworzenie i zarządzanie nimi budżetów](tutorial-acm-create-budgets.md) samouczka.
