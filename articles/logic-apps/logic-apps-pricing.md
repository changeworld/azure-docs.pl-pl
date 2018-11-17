---
title: Ceny i rozliczanie — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak działa cennik i rozliczenia dla usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 04fb86f9b8f8be2c013f9bd7449dd5a4b2bcf90c
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854122"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Model cen usługi Azure Logic Apps

Możesz tworzyć i uruchamiać zautomatyzowane integracji przepływów pracy, które można skalować w chmurze, korzystając z usługi Azure Logic Apps. Poniżej przedstawiono szczegółowe informacje o współdziałaniu rozliczeniach i cenach dla usługi Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Użycie modelu cen

W przypadku nowych aplikacji logiki, działających w usłudze Logic Apps publicznej lub "global" płacisz tylko za rzeczywiste użycie. Te aplikacje logiki, użyj planu na podstawie użycia i modelu cenowego. W definicji aplikacji logiki każdy krok jest akcji. Akcje obejmują wyzwalacz, wszystkie kroki przepływu sterowania, wbudowane akcje i wywołania łącznika. Usługa Logic Apps liczniki — wszystkie akcje, które są uruchamiane w aplikacji logiki.  
Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Naprawiono modelu cen

W przypadku nowych aplikacji logiki, które są uruchamiane wewnątrz [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), płacisz za stałą cenę miesięczną wbudowane akcje i łączniki standardowe etykietą ISE. ISE umożliwia tworzenie i uruchamiać aplikacje izolowane logiki, które mogą uzyskiwać dostęp do zasobów w sieci wirtualnej platformy Azure.  

Twoje ISE zawiera jeden łącznik przedsiębiorstwa bezpłatne, w tym tyle połączeń. Użycie dla przedsiębiorstw dodatkowe łączniki są naliczane na podstawie cena za użycie przedsiębiorstwa. 

> [!NOTE]
> Środowisko usługi integracji znajduje się w *prywatnej wersji zapoznawczej*. Aby zażądać dostępu, [utworzyć Twoje żądanie dołączenia do tutaj](https://aka.ms/iseprivatepreview). Aby uzyskać więcej informacji, zobacz [Logic Apps — cennik](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Wyzwalacze

Wyzwalacze to stosowanie specjalnych działań, które utworzyć wystąpienie aplikacji logiki w przypadku określonego zdarzenia. Wyzwalacze działają w różny sposób, co wpływa na sposób aplikacja logiki jest taryfowe.

* **Wyzwalacz sondowania** — ten wyzwalacz sprawdza stale punkt końcowy dla komunikatów, które spełniają kryteria służące do tworzenia wystąpienie aplikacji logiki i uruchamiania przepływu pracy. Nawet jeśli nie wystąpienie aplikacji logiki zostanie utworzona, Logic Apps liczniki — każde żądanie sondowania jako środowisko wykonawcze. Aby określić interwał sondowania, należy skonfigurować wyzwalacz za pomocą projektanta aplikacji logiki.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Wyzwalacza elementu Webhook** — ten wyzwalacz czeka na klienta wysłać żądanie do określonego punktu końcowego. Każdego żądania wysyłanego do punktu końcowego elementu webhook jest liczone jako środowisko wykonawcze akcji. Na przykład wyzwalacz żądania i element Webhook protokołu HTTP są oba wyzwalaczy elementu webhook.

* **Wyzwalacz cykliczny** — ten wyzwalacz tworzy wystąpienie aplikacji logiki na podstawie skonfigurowanego w wyzwalaczu interwału cyklu. Na przykład można ustawić górę wyzwalacz cykliczny, które jest uruchamiane co trzy dni lub bardziej złożone zgodnie z harmonogramem.

## <a name="actions"></a>Akcje

Usługa Logic Apps liczniki — wbudowane akcje jako natywnej akcji. Na przykład wbudowane akcje obejmują wywołania za pośrednictwem protokołu HTTP, wywołania z usługi Azure Functions lub usługa API Management i kroki przepływu sterowania, takie jak pętle i warunki 
- Każdy z typem akcji. Akcje, które wywołują [łączników](https://docs.microsoft.com/connectors) mieć typu "ApiConnection". Te łączniki są klasyfikowane jako standard lub enterprise łączniki, które są oparte na odpowiednich [ceny][pricing]. Łączniki dla przedsiębiorstw w *Podgląd* są rozliczane jako łączniki standardowe.

Logic Apps liczniki wszystkich pomyślnie i niepomyślnie do uruchamiania działań na jako wykonania akcji. Usługa Logic Apps nie monitoruje następujące akcje: 

* Akcje, które Pobierz pominięte z powodu niewypełnienia warunków
* Akcje, które nie działają, ponieważ aplikacja logiki została zatrzymana przed zakończeniem

Wyłączone logic apps nie są naliczane podczas wyłączone, ponieważ nie mogą tworzyć nowe wystąpienia.

> [!NOTE]
> Po wyłączeniu aplikację logiki obecnie uruchomione wystąpienia może potrwać pewien czas, zanim całkowite zatrzymanie.

Logic Apps do akcje, które są uruchamiane wewnątrz pętli, liczy się każda akcja cyklu w pętli. Na przykład załóżmy, że masz pętli "for each", która przetwarza listy. Logic Apps liczniki akcję w tym pętli mnożąc liczbę listy elementy z liczby akcji w pętli i dodaje akcję, która rozpoczyna się pętli. Obliczanie listę 10 elementów jest (10 * 1) + 1, co skutkuje 11 wykonania akcji.

## <a name="integration-account-usage"></a>Użycie konta integracji

Użycie na podstawie użycia ma zastosowanie do [kont integracji](logic-apps-enterprise-integration-create-integration-account.md) gdzie możesz eksplorować, opracowywania i testowania [B2B/EDI](logic-apps-enterprise-integration-b2b.md) i [przetwarzania XML](logic-apps-enterprise-integration-xml.md) funkcji w usłudze Logic Apps, bez dodatkowych kosztów. Możesz mieć jedno konto integracji na region. Każde konto integracji można przechowywać w do określonych [liczby artefaktów](../logic-apps/logic-apps-limits-and-config.md), które obejmują handlowymi partnerów, umów, mapy, schematów, zestawy, certyfikaty, konfiguracji partii i tak dalej.

Usługa Logic Apps oferuje również kont integracji podstawowa i standardowa obsługiwanych SLA aplikacji logiki. Możesz użyć kont integracji w warstwie podstawowa, po prostu ma Obsługa komunikatów lub działa jako partner małych firm, która ma relację partnerów handlowych z większych jednostkach biznesowych. Konta integracji w warstwie standardowa obsługują bardziej złożone relacje B2B i zwiększyć liczbę jednostek, którymi można zarządzać. Aby uzyskać więcej informacji, zobacz [cennik usługi Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o usłudze Logic Apps][whatis]
* [Tworzenie pierwszej aplikacji logiki][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

