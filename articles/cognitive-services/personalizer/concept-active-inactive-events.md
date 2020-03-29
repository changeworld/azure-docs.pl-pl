---
title: Zdarzenia aktywne i nieaktywne - Personalizer
description: W tym artykule omówiono użycie aktywnych i nieaktywnych zdarzeń w ramach usługi Personalizer.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624254"
---
# <a name="active-and-inactive-events"></a>Zdarzenia aktywne i nieaktywne

**Aktywnym** wydarzeniem jest każde wezwanie do rangi, w którym wiesz, że pokażesz wynik klientowi i określisz wynik nagrody. Jest to zachowanie domyślne.

Zdarzenie **nieaktywne** jest wywołanie rangi, gdzie nie masz pewności, czy użytkownik kiedykolwiek zobaczy zalecane działania, ze względu na logikę biznesową. Dzięki temu można odrzucić zdarzenie, więc Personalizer nie jest przeszkolony z domyślną nagrodą. Nieaktywne zdarzenia nie powinny wywoływać interfejsu API nagrody.

Ważne jest, aby pętla uczenia się znała rzeczywisty typ zdarzenia. Nieaktywne wydarzenie nie będzie miało zaproszenia do nagrody. Aktywne zdarzenie powinno mieć połączenie reward, ale jeśli wywołanie interfejsu API nigdy nie zostanie wykonane, zostanie zastosowany domyślny wynik nagrody. Zmień stan zdarzenia z nieaktywnego na aktywny, gdy tylko wiadomo, że będzie miało wpływ na środowisko użytkownika.

## <a name="typical-active-events-scenario"></a>Typowy scenariusz aktywnych zdarzeń

Gdy aplikacja wywołuje interfejs API rangi, otrzymasz akcję, którą aplikacja powinna pokazać w polu **rewardActionId.**  Od tego momentu Personalizer oczekuje zaproszenia do nagrody z wynikiem nagrody, który ma ten sam identyfikator eventId. Wynik nagrody służy do szkolenia modelu dla przyszłych wywołań rangi. Jeśli dlaidu wydarzenia nie zostanie odebrane żadne zaproszenie do nagrody, zostanie zastosowana nagroda domyślna. [Domyślne nagrody](how-to-settings.md#configure-rewards-for-the-feedback-loop) są ustawiane w zasobie Personalizer w witrynie Azure portal.

## <a name="other-event-type-scenarios"></a>Inne scenariusze typu zdarzenia

W niektórych scenariuszach aplikacja może być konieczne wywołanie Ranga, zanim nawet wie, czy wynik będzie używany lub wyświetlany użytkownikowi. Może się tak zdarzyć w sytuacjach, gdy na przykład renderowanie strony promowanej zawartości jest zastępowane przez kampanię marketingową. Jeśli wynik wywołania rangi nigdy nie był używany, a użytkownik nigdy go nie widział, nie wysyłaj odpowiedniego połączenia reward.

Zazwyczaj te scenariusze zdarzają się, gdy:

* Jesteś prerendering interfejsu użytkownika, który użytkownik może lub nie może zobaczyć.
* Aplikacja robi predykcyjnej personalizacji, w którym Rank wywołania są wykonane z mało kontekstu w czasie rzeczywistym i aplikacji może lub nie może używać danych wyjściowych.

W takich przypadkach należy użyć personalizatora, aby wywołać rangę, żądając, aby zdarzenie było _nieaktywne._ Personalizator nie oczekuje nagrody za to wydarzenie i nie będzie stosować nagrody domyślnej.

W dalszej części logiki biznesowej, jeśli aplikacja używa informacji z wywołania Ranga, po prostu _aktywuj_ zdarzenie. Gdy tylko wydarzenie będzie aktywne, Personalizer oczekuje nagrody za wydarzenie. Jeśli do interfejsu API nagrody nie zostanie nawiązywać jawne połączenie, personalizator stosuje nagrodę domyślną.

## <a name="inactive-events"></a>Zdarzenia nieaktywne

Aby wyłączyć szkolenie dla wydarzenia, `learningEnabled = False`zadzwoń do rangi za pomocą programu .

W przypadku zdarzenia nieaktywnego nauka jest niejawnie aktywowana, jeśli `activate` wyślesz nagrodę za identyfikator zdarzenia lub wywołasz interfejs API dla tego identyfikatora zdarzenia.

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak określić wynik nagrody i jakie dane należy wziąć pod uwagę.](concept-rewards.md)
