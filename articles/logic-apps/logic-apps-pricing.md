---
title: Ceny i rozliczanie — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa cennik i rozliczenia dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 03/25/2019
ms.openlocfilehash: 7e1868dd5ce62c28c9a8aac724862c58a5e0e1da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58805180"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Model cen usługi Azure Logic Apps

[Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) ułatwia tworzenie i uruchamianie przepływów pracy integracji automatycznych, który można skalować w chmurze. W tym artykule opisano, jak działają rozliczeń i ceny dla usługi Azure Logic Apps. Aby uzyskać szczegółowe informacje dotyczące cen, zobacz [Azure Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Użycie modelu cen

W przypadku nowych aplikacji logiki, działających w usłudze Azure Logic Apps publicznej lub "global" płacisz tylko za rzeczywiste użycie. Te aplikacje logiki, użyj planu na podstawie użycia i modelu cenowego. W definicji aplikacji logiki każdy krok jest akcji. Na przykład akcje obejmują: 

* Wyzwalacze, które są stosowanie specjalnych działań. Wszystkie aplikacje logiki wymagają wyzwalacza w pierwszym kroku.
* "Wbudowane" lub macierzystym akcje, takie jak HTTP, wywołania do usługi Azure Functions i usługi API Management itd.
* Wywołania łącznikach, takich jak usługi Outlook 365, Dropbox i tak dalej
* Kontrolować przepływ kroków, takich jak pętle, instrukcje warunkowe i tak dalej

Usługa Azure Logic Apps liczniki — wszystkie akcje, które są uruchamiane w aplikacji logiki. Dowiedz się więcej o jak działa rozliczanie usługi [wyzwalaczy](#triggers) i [akcje](#actions).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Naprawiono modelu cen

[ *Środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) umożliwia prywatne, izolowane i dedykowane można tworzyć i uruchamiać aplikacje logiki, które mogą uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure. W przypadku nowych aplikacji logiki, które są uruchamiane wewnątrz środowiska ISE płatność stałą cenę miesięczną wbudowanych akcji i łączników standardowych. Twoje ISE obejmuje także jeden łącznik przedsiębiorstwa bezpłatne, w tym tyle połączeń. Użycie dodatkowe łączniki dla przedsiębiorstw jest obciążany cena za użycie przedsiębiorstwa. 

Jednostkę podstawową ISE ma ustaloną pojemność, jeśli potrzebujesz więcej przepływności, można więc [dodawać kolejne jednostki skalowania](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), albo podczas tworzenia lub później.

> [!NOTE]
> Trwa środowiska ISE [ *publicznej wersji zapoznawczej*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać szczegółowe informacje dotyczące cen, zobacz [Azure Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="connectors"></a>

## <a name="connectors"></a>Łączniki

Łączników usługi Azure Logic Apps pomaga swojej logiki aplikacji dostęp do aplikacji, usług i systemy w chmurze lub lokalnie, zapewniając [wyzwalaczy](#triggers), [akcje](#actions), lub obu. Łączniki są klasyfikowane jako Standard lub Enterprise. Aby uzyskać przegląd informacji o tych łączników, zobacz [łączniki dla usługi Azure Logic Apps](../connectors/apis-list.md). Poniższe sekcje zawierają więcej informacji na temat sposobu wyzwala opłata i akcje działają.

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze to stosowanie specjalnych działań, które utworzyć wystąpienie aplikacji logiki w przypadku określonego zdarzenia. Wyzwalacze działają w różny sposób, co wpływa na sposób aplikacja logiki jest taryfowe. Oto różnych rodzajów wyzwalaczy, które istnieją w usłudze Azure Logic Apps:

* **Wyzwalacz sondowania**: Ten wyzwalacz stale sprawdza, czy punkt końcowy dla komunikatów, które spełniają kryteria służące do tworzenia wystąpienie aplikacji logiki i uruchamiania przepływu pracy. Nawet jeśli nie wystąpienie aplikacji logiki zostanie utworzona, Logic Apps liczniki — każde żądanie sondowania jako środowisko wykonawcze. Aby określić interwał sondowania, należy skonfigurować wyzwalacz za pomocą projektanta aplikacji logiki.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacza elementu Webhook**: Ten wyzwalacz czeka na klienta wysłać żądanie do określonego punktu końcowego. Każdego żądania wysyłanego do punktu końcowego elementu webhook jest liczone jako środowisko wykonawcze akcji. Na przykład wyzwalacz żądania i element Webhook protokołu HTTP są oba wyzwalaczy elementu webhook.

* **Wyzwalacz cykliczny**: Ten wyzwalacz tworzy wystąpienie aplikacji logiki na podstawie skonfigurowanego w wyzwalaczu interwału cyklu. Na przykład można ustawić górę wyzwalacz cykliczny, które jest uruchamiane co trzy dni lub bardziej złożone zgodnie z harmonogramem.

<a name="actions"></a>

## <a name="actions"></a>Akcje

Usługa Azure Logic Apps liczniki — "wbudowane" czynności, takie jak HTTP, jako natywnej akcji. Na przykład wbudowane akcje obejmują wywołania HTTP, wywołania z usługi Azure Functions lub API Management i kontrolować przepływ kroków, takich jak warunki, pętle i instrukcje switch. Każde działanie ma swoje własne typ akcji. Na przykład w przypadku działań, takich jak wywoływanie [łączników](https://docs.microsoft.com/connectors) mieć typu "ApiConnection". Te łączniki są klasyfikowane jako Standard, lub łączniki dla przedsiębiorstw, które są naliczane w oparciu o ich odpowiednich [ceny](https://azure.microsoft.com/pricing/details/logic-apps). Łączniki dla przedsiębiorstw w *Podgląd* są rozliczane jako łączniki standardowe.

Usługa Azure Logic Apps liczniki — wszystkie akcje zakończone powodzeniem i niepowodzeniem jako wykonań. Jednak Logic Apps nie monitoruje następujące akcje:

* Akcje, które Pobierz pominięte z powodu niewypełnienia warunków
* Akcje, które nie działają, ponieważ aplikacja logiki została zatrzymana przed zakończeniem

Usługi Azure Logic Apps do akcje, które są uruchamiane wewnątrz pętli, liczy się każda akcja, dla każdego cyklu w pętli. Na przykład załóżmy, że masz pętli "for each", która przetwarza listy. Logic Apps liczniki akcję w tym pętli mnożąc liczbę listy elementy z liczby akcji w pętli i dodaje akcję, która rozpoczyna się pętli. Obliczanie listę 10 elementów jest więc (10 * 1) + 1, co skutkuje 11 wykonania akcji.

## <a name="disabled-logic-apps"></a>Aplikacje logiki wyłączone

Wyłączone logika aplikacji nie są naliczane, ponieważ nie można utworzyć nowego wystąpienia, gdy są one wyłączone.
Po wyłączeniu aplikację logiki obecnie uruchomione wystąpienia może potrwać pewien czas, zanim całkowite zatrzymanie.

## <a name="integration-accounts"></a>Konta integracji

Ceny za korzystanie z dotyczy [kont integracji](logic-apps-enterprise-integration-create-integration-account.md) gdzie możesz eksplorować, opracowywania i testowania [B2B i EDI](logic-apps-enterprise-integration-b2b.md) i [przetwarzania XML](logic-apps-enterprise-integration-xml.md) funkcji w usłudze Azure Logic Apps, bez dodatkowych kosztów.
W każdym regionie platformy Azure, możesz mieć jedno konto integracji. Każde konto integracji można przechowywać w do określonych [liczby artefaktów](../logic-apps/logic-apps-limits-and-config.md), które obejmują handlowymi partnerów, umów, mapy, schematów, zestawy, certyfikaty, konfiguracji partii i tak dalej.

Usługa Azure Logic Apps oferuje również podstawowa i standardowa konta integracji z obsługiwanych SLA aplikacji logiki. Poniżej przedstawiono sposób wybrać, czy użyć podstawowa lub standardowa konta integracji:

* Gdy po prostu chcesz Obsługa komunikatów lub działa jako partner małych firm, która ma relację partnerów handlowych z większych jednostkach biznesowych, należy użyć kont integracji w warstwie podstawowa.

* Po bardziej złożone relacje B2B i chcesz zwiększyć liczbę jednostek, którymi można zarządzać, należy użyć kont integracji w warstwie standardowa.

Aby uzyskać szczegółowe informacje dotyczące cen, zobacz [cennika usługi Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="data-retention"></a>

## <a name="data-retention"></a>Przechowywanie danych

Wszystkie dane wejściowe i wyjściowe, które są przechowywane w historii uruchamiania aplikacji logiki naliczana na podstawie aplikacji logiki [Uruchom okres przechowywania](logic-apps-limits-and-config.md#run-duration-retention-limits). Aby uzyskać szczegółowe informacje dotyczące cen, zobacz [cennika usługi Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

Aby pomóc monitorować użycie magazynu aplikację logiki, możesz wykonywać następujące czynności:

* Wyświetl liczbę jednostek magazynu w GB, który Twoja aplikacja logiki używa co miesiąc.
* Wyświetl rozmiary dla określonej akcji wejść i wyjść w historii uruchamiania aplikacji logiki.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Użycie magazynu aplikacji logiki widoku

1. W witrynie Azure portal należy znaleźć i otworzyć aplikację logiki.

1. Z menu aplikacji logiki w ramach **monitorowanie**, wybierz opcję **metryki**.

1. W okienku po prawej stronie w obszarze **tytułu wykresu**, z **metryki** listy wybierz **rozliczeń użycia dla wykonania zużycie pamięci masowej**.

   Ta metryka zapewnia liczba zużycia jednostek magazynu w GB na miesiąc, które są naliczane.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Wyświetl dane wejściowe akcji i dane wyjściowe rozmiarów

1. W witrynie Azure portal należy znaleźć i otworzyć aplikację logiki.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W okienku po prawej stronie w obszarze **Historia przebiegów**, wybierz polecenie Uruchom, dane wejściowe i generuje, którego chcesz sprawdzić.

1. W obszarze **przebiegu aplikacji logiki**, wybierz **szczegóły przebiegu**.

1. W **szczegóły przebiegu aplikacji logiki** okienko, w tabeli akcji, która wyświetla stan i czas trwania każdej akcji, wybierz akcję, którą chcesz wyświetlić.

1. W **akcji aplikacji logiki** okienko, należy znaleźć rozmiary dane wejściowe i wyjściowe tej akcji są wyświetlane odpowiednio w **link danych wejściowych** i **link danych wyjściowych**.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o usłudze Azure Logic Apps](logic-apps-overview.md)
* [Tworzenie pierwszej aplikacji logiki](quickstart-create-first-logic-app-workflow.md)