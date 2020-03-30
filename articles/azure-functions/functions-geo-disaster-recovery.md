---
title: Odzyskiwanie po awarii geograficznej usługi Azure Functions i wysoka dostępność
description: Jak używać regionów geograficznych do nadmiarowości i pracy awaryjnej w usłudze Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080228"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Odzyskiwanie po awarii geograficznej usługi Azure Functions

Gdy całe regiony platformy Azure lub centra danych doświadczają przestojów, kluczowe znaczenie ma kontynuowanie przetwarzania w innym regionie.  W tym artykule wyjaśniono niektóre strategie, których można użyć do wdrożenia funkcji, aby umożliwić odzyskiwanie po awarii.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługi Azure Functions są uruchamiane w określonym regionie.  Aby uzyskać wyższą dostępność, można wdrożyć te same funkcje w wielu regionach.  W wielu regionach funkcje mogą być uruchamiane w *wzorcu aktywnym/aktywnym* lub *aktywnym/pasywnym.*  

* Aktywny/aktywny. Oba regiony są aktywne i odbierające zdarzenia (zduplikowane lub rotacyjne). Aktywne/aktywne jest zalecane dla funkcji HTTPS w połączeniu z drzwiami frontowymi platformy Azure.
* Aktywny/pasywny. Jeden region jest aktywny i odbiera zdarzenia, podczas gdy pomocniczy jest bezczynny.  Gdy jest to wymagane w pracy awaryjnej, region pomocniczy jest aktywowany i przejmuje przetwarzanie.  Jest to zalecane w przypadku funkcji innych niż HTTP, takich jak usługa Service Bus i Centra zdarzeń.

Przeczytaj, jak [uruchamiać aplikacje w wielu regionach,](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) aby uzyskać więcej informacji na temat wdrożeń w wielu regionach.

## <a name="activeactive-for-https-functions"></a>Aktywny/aktywny dla funkcji HTTPS

Aby osiągnąć aktywne/aktywne wdrożenia funkcji, wymaga pewnego składnika, który może koordynować zdarzenia między obu regionach.  W przypadku funkcji HTTPS ta koordynacja jest realizowana przy użyciu [drzwi ekwiomu azure.](../frontdoor/front-door-overview.md)  Usługi Azure Front Door mogą kierować i okrężne żądania HTTPS między wieloma funkcjami regionalnymi.  Okresowo sprawdza również kondycję każdego punktu końcowego.  Jeśli funkcja regionalna przestanie odpowiadać na kontrole kondycji, usługi Azure Front Door wyjmuje ją z rotacji i przekazuje ruch tylko do funkcji w dobrej kondycji.  

![Architektura drzwi i funkcji azure front door i function](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Aktywne/aktywne dla funkcji innych niż HTTPS

Nadal można uzyskać aktywne/aktywne wdrożenia dla funkcji innych niż HTTPS.  Należy jednak wziąć pod uwagę, jak oba regiony będą współdziałać lub koordynować ze sobą.  Jeśli wdrożono tę samą aplikację funkcji w dwóch regionach, z których każdy wyzwala w tej samej kolejce usługi Service Bus, będą działać jako konkurujący konsumenci w usuwaniu kolejki tej kolejki.  Chociaż oznacza to, że każda wiadomość jest przetwarzana tylko przez jedno z wystąpień, oznacza to również, że nadal istnieje jeden punkt awarii w pojedynczej usłudze Service Bus.  Jeśli wdrożysz dwie kolejki usługi Service Bus (jedna w regionie podstawowym, jedna w regionie pomocniczym) i dwie aplikacje funkcji wskazały na ich kolejkę regionu, wyzwanie pojawia się teraz w sposobie rozmieszczania wiadomości kolejki między dwoma regionami.  Często oznacza to, że każdy wydawca próbuje opublikować wiadomość w *obu* regionach, a każda wiadomość jest przetwarzana przez obie aktywne aplikacje funkcji.  Podczas gdy tworzy to aktywny/aktywny wzorzec, tworzy inne wyzwania związane z powielaniem obliczeń oraz kiedy i jak dane są konsolidowane.  Z tych powodów zaleca się wyzwalacze inne niż HTTPS do używania wzorca aktywnego/pasywnego.

## <a name="activepassive-for-non-https-functions"></a>Aktywne/pasywne dla funkcji innych niż HTTPS

Aktywne/pasywne zapewnia sposób tylko jednej funkcji do przetwarzania każdej wiadomości, ale zapewnia mechanizm awaryjnego do pomocniczego regionu w przypadku awarii.  Usługa Azure Functions współpracuje z [usługą Azure Service Bus geo recovery](../service-bus-messaging/service-bus-geo-dr.md) i azure event [hubs geo-recovery.](../event-hubs/event-hubs-geo-dr.md)

Przy użyciu usługi Azure Event Hubs wyzwala jako przykład, aktywny/pasywny wzorzec będzie obejmować następujące czynności:

* Usługa Azure Event Hub wdrożona zarówno w regionie podstawowym, jak i pomocniczym.
* Awaria geograficzna włączona do parowania centrum zdarzeń podstawowych i pomocniczych.  Spowoduje to również utworzenie "aliasu", którego można użyć do łączenia się z koncentratorami zdarzeń i przełączania się z podstawowego na pomocniczy bez zmiany informacji o połączeniu.
* Aplikacje funkcyjne wdrożone zarówno w regionie podstawowym, jak i pomocniczym.
* Aplikacje funkcji są wyzwalane na ciąg połączenia *bezpośredniego* (non-alias) dla jego odpowiedniego centrum zdarzeń. 
* Wydawcy do centrum zdarzeń należy opublikować do ciągu połączenia aliasu. 

![Architektura przykładów aktywnych i pasywnych](media/functions-geo-dr/active-passive.png)

Przed przełączeniem awaryjnym wydawcy wysyłający do udostępnionego aliasu będą kierować do centrum zdarzeń podstawowych.  Aplikacja funkcji podstawowej nasłuchuje wyłącznie centrum zdarzeń podstawowych.  Aplikacja funkcji pomocniczej będzie pasywna i bezczynna.  Po zainicjowaniu pracy awaryjnej wydawcy wysyłający do udostępnionego aliasu będą teraz kierowani do pomocniczego centrum zdarzeń.  Aplikacja funkcji pomocniczej stanie się teraz aktywna i rozpocznie wyzwalanie automatycznie.  Skuteczne przewijanie awaryjne do regionu pomocniczego można w całości kierować z Centrum zdarzeń, z funkcjami staje się aktywny tylko wtedy, gdy odpowiednie centrum zdarzeń jest aktywny.

Dowiedz się więcej o informacjach i zagadnieniach dotyczących pracy awaryjnej w [usłudze Service Bus](../service-bus-messaging/service-bus-geo-dr.md) i [centrach zdarzeń](../event-hubs/event-hubs-geo-dr.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie drzwi y frontowej platformy Azure](../frontdoor/quickstart-create-front-door.md)
* [Zagadnienia dotyczące trybu failover w centrach zdarzeń](../event-hubs/event-hubs-geo-dr.md#considerations)
