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
ms.date: 09/24/2018
ms.openlocfilehash: b75fba2ba0e9fa922b1252378e0bab326cada7d2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974310"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Model cen usługi Azure Logic Apps

Można tworzyć i uruchamiać przepływy pracy zautomatyzowanych skalowalnych integracji w chmurze z usługą Azure Logic Apps. Poniżej przedstawiono szczegółowe informacje o współdziałaniu rozliczeniach i cenach dla usługi Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Użycie modelu cen

W przypadku nowych aplikacji logiki, które tworzysz przy użyciu usługi Logic Apps publicznego lub "global" płacisz tylko za rzeczywiste użycie. Te aplikacje logiki użyć, na podstawie użycia planu i modelu cen, co oznacza, że opłaty są naliczane każdą akcję wykonaną przez aplikację logiki. Każdy krok w definicji aplikacji logiki jest akcję, która zawiera wyzwalacze, kroki przepływu sterowania, wywołania wbudowane akcje i wywołań łączników. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Naprawiono modelu cen

> [!NOTE]
> Środowisko usługi integracji znajduje się w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview).

W przypadku nowych aplikacji logiki, które utworzyłeś z [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), czyli prywatny izolowane wystąpienie aplikacji logiki, korzystającą z dedykowanych zasobów, płatność stałą cenę miesięczną dla wbudowane akcje i łączniki standardowe etykietą ISE. Twoje ISE zawiera jeden łącznik przedsiębiorstwa bez dodatkowych opłat, podczas gdy dodatkowe łączniki dla przedsiębiorstw są naliczane na podstawie których cena za użycie przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze to stosowanie specjalnych działań, które utworzyć wystąpienie aplikacji logiki w przypadku określonego zdarzenia. Wyzwalacze działają w różny sposób, co wpływa na sposób aplikacja logiki jest taryfowe.

* **Wyzwalacz sondowania** — ten wyzwalacz sprawdza stale punkt końcowy dla komunikatów, które spełniają kryteria służące do tworzenia wystąpienie aplikacji logiki i uruchamiania przepływu pracy. Każde żądanie sondowania jest liczone jako środowisko wykonawcze i są naliczane nawet wtedy, gdy nie wystąpienie aplikacji logiki jest tworzony. Aby określić interwał sondowania, należy skonfigurować wyzwalacz za pomocą projektanta aplikacji logiki.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacza elementu Webhook** — ten wyzwalacz czeka na klienta wysłać żądanie do określonego punktu końcowego. Każdego żądania wysyłanego do punktu końcowego elementu webhook jest liczone jako środowisko wykonawcze akcji. Na przykład wyzwalacz żądania i element Webhook protokołu HTTP są oba wyzwalaczy elementu webhook.

* **Wyzwalacz cykliczny** — ten wyzwalacz tworzy wystąpienie aplikacji logiki na podstawie skonfigurowanego w wyzwalaczu interwału cyklu. Na przykład można ustawić górę wyzwalacz cykliczny, które jest uruchamiane co trzy dni lub bardziej złożone zgodnie z harmonogramem.

W okienku przeglądu aplikacji logiki w sekcji historii wyzwalania, można znaleźć wykonań wyzwalacza.

## <a name="actions"></a>Akcje

Wbudowane akcje, takie jak akcje wywołania HTTP, usługi Azure Functions lub usługa API Management, a także kontrolować przepływ kroków są mierzone jako natywny akcje, które mają ich odpowiednich typów. Akcje, które wywołują [łączników](https://docs.microsoft.com/connectors) mieć typu "ApiConnection". Te łączniki są klasyfikowane jako standard lub enterprise łączniki, które są oparte na odpowiednich [ceny][pricing]. 

Wszystkie akcje pomyślnie i niepomyślnie wykonywania są liczone i naliczane jako wykonania akcji. Jednak akcje, które są pomijane, z powodu niewypełnienia warunków ani akcji, które nie działają, ponieważ aplikacja logiki przerwane przed ukończeniem, nie są traktowane jako wykonania akcji. Aplikacje logiki wyłączone nie można utworzyć wystąpienia nowe wystąpienia, więc one za nie są naliczane, gdy są one wyłączone.

> [!NOTE]
> Po wyłączeniu aplikację logiki obecnie uruchomione wystąpienia może potrwać pewien czas, zanim całkowite zatrzymanie.

Akcje, które są uruchamiane wewnątrz pętli są liczone na każdy cykl w pętli. Na przykład jako jedna akcja w pętli "for each", która przetwarza listę 10 elementów jest liczony mnożąc liczbę elementów listy (10) przez liczbę działań w pętli (1) plus jeden do uruchamiania w pętli. Na przykład obliczenie jest więc (10 * 1) + 1, co skutkuje 11 wykonania akcji.

## <a name="integration-account-usage"></a>Użycie konta integracji

Obejmuje użycie na podstawie użycia [konta integracji](logic-apps-enterprise-integration-create-integration-account.md) gdzie możesz eksplorować, opracowywania i testowania [B2B/EDI](logic-apps-enterprise-integration-b2b.md) i [przetwarzania XML](logic-apps-enterprise-integration-xml.md) bez funkcji Logic Apps dodatkowych kosztów. Może mieć jedno konto integracji na region i magazynu do określonych [liczby artefaktów](../logic-apps/logic-apps-limits-and-config.md), takie jak partnerzy handlowi EDI i umowy, mapy, schematów, zestawy, certyfikatów i konfiguracji partii.

Usługa Logic Apps oferuje również kont integracji podstawowa i standardowa obsługiwanych SLA aplikacji logiki. Możesz użyć kont integracji w warstwie podstawowa, albo ma być używany tylko komunikatów lub działa jako partner małych firm, która ma relację partnerów handlowych z większych jednostkach biznesowych. Konta integracji w warstwie standardowa obsługują bardziej złożone relacje B2B i zwiększyć liczbę jednostek, którymi można zarządzać. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o usłudze Logic Apps][whatis]
* [Tworzenie pierwszej aplikacji logiki][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

