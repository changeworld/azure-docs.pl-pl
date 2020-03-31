---
title: Typy funkcji w usługach Azure Funkcje trwałe
description: Dowiedz się więcej o typach funkcji i ról, które obsługują komunikację funkcji do funkcji w aranżacji funkcji trwałych w usłudze Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277884"
---
# <a name="durable-functions-types-and-features"></a>Typy i funkcje funkcji trwałych

Funkcje trwałe jest rozszerzeniem [funkcji platformy Azure](../functions-overview.md). Można użyć funkcji trwałych dla stanowej aranżacji wykonywania funkcji. Trwała aplikacja funkcji to rozwiązanie, które składa się z różnych funkcji platformy Azure. Funkcje mogą odgrywać różne role w aranżacji funkcji trwałe. 

Obecnie istnieją cztery typy funkcji trwałe w usłudze Azure Functions: działania, orchestrator, jednostki i klienta. W dalszej części tej sekcji przechodzi do bardziej szczegółowych informacji na temat typów funkcji zaangażowanych w aranżacji.

## <a name="orchestrator-functions"></a>Funkcje orkiestratora

Funkcje koordynatora opisują sposób wykonywania akcji i kolejność wykonywania akcji. Funkcje aranżatora opisują aranżację w kodzie (C# lub JavaScript), jak pokazano w [wzorcach aplikacji Trwałe funkcje](durable-functions-overview.md#application-patterns). Aranżacja może mieć wiele różnych typów akcji, w tym [funkcje działania,](#activity-functions) [podarancje,](durable-functions-orchestrations.md#sub-orchestrations) [oczekiwanie na zdarzenia zewnętrzne,](durable-functions-orchestrations.md#external-events) [HTTP](durable-functions-http-features.md)i [czasomierze.](durable-functions-orchestrations.md#durable-timers) Funkcje koordynatora mogą również wchodzić w interakcje z [funkcjami encji](#entity-functions).

> [!NOTE]
> Funkcje orchestrator są zapisywane przy użyciu zwykłego kodu, ale istnieją ścisłe wymagania dotyczące sposobu pisania kodu. W szczególności kod funkcji koordynatora musi być *deterministyczny*. Nieprzestrzeganie tych wymagań determinizm może spowodować, że funkcje programu orchestrator nie działać poprawnie. Szczegółowe informacje na temat tych wymagań i jak obejść je można znaleźć w temacie [ograniczenia kodu.](durable-functions-code-constraints.md)

Aby uzyskać bardziej szczegółowe informacje na temat funkcji koordynatora i ich funkcji, zobacz [artykuł trwałe aranżacje.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funkcje aktywności

Funkcje działania są podstawową jednostką pracy w trwałej aranżacji funkcji. Funkcje działania są funkcje i zadania, które są aranżowane w procesie. Na przykład można utworzyć funkcję koordynatora do przetwarzania zamówienia. Zadania obejmują sprawdzanie zapasów, obciążanie odbiorcy i tworzenie przesyłki. Każde zadanie będzie oddzielną funkcją działania. Te funkcje działania mogą być wykonywane szeregowo, równolegle lub niektóre kombinacje obu.

W przeciwieństwie do funkcji orkiestratora funkcje działania nie są ograniczone w typie pracy, którą można wykonać w nich. Funkcje działania są często używane do wykonywania wywołań sieciowych lub uruchamiania operacji intensywnie korzystających z procesora CPU. Funkcja działania może również zwracać dane z powrotem do funkcji koordynatora. Struktura zadań trwałe gwarantuje, że każda wywoływana funkcja działania zostanie wykonana *co najmniej raz* podczas wykonywania aranżacji.

> [!NOTE]
> Ponieważ funkcje działania gwarantują *tylko jedno tylko raz* wykonanie, zaleca się, aby identyfikator logiki funkcji *działania,* gdy tylko jest to możliwe.

Użyj [wyzwalacza działania,](durable-functions-bindings.md#activity-trigger) aby zdefiniować funkcję działania. .NET funkcje `DurableActivityContext` odbierają jako parametr. Wyzwalacz można również powiązać z dowolnym innym obiektem serializowania JSON, który ma być przekazywany w danych wejściowych do funkcji. W języku JavaScript można uzyskać `<activity trigger binding name>` dostęp do danych wejściowych za pośrednictwem właściwości [ `context.bindings` obiektu](../functions-reference-node.md#bindings). Funkcje działania mogą mieć tylko jedną wartość przekazywana do nich. Aby przekazać wiele wartości, należy użyć krotek, tablic lub typów złożonych.

> [!NOTE]
> Funkcję działania można wyzwolić tylko z funkcji koordynatora.

## <a name="entity-functions"></a>Funkcje encji

Funkcje encji definiują operacje do odczytu i aktualizowania małych fragmentów stanu. Często odnosimy się do tych jednostek stanowych jako *trwałych podmiotów.* Podobnie jak funkcje orkiestratora, funkcje encji są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczu jednostki*. Mogą być również wywoływane z funkcji klienta lub z funkcji aranżatora. W przeciwieństwie do funkcji aranżatora funkcje jednostki nie mają żadnych ograniczeń określonego kodu. Funkcje jednostki również zarządzać stan jawnie, a nie niejawnie reprezentujących stan za pośrednictwem przepływu sterowania.

> [!NOTE]
> Funkcje jednostki i związane z nimi funkcje są dostępne tylko w funkcji trwałe 2.0 i powyżej.

Aby uzyskać więcej informacji na temat funkcji jednostki, zobacz [trwałe jednostki](durable-functions-entities.md) artykułu.

## <a name="client-functions"></a>Funkcje klienta

Funkcje aranżatora są wyzwalane przez [powiązanie wyzwalacza aranżacji,](durable-functions-bindings.md#orchestration-trigger) a funkcje encji są wyzwalane przez [powiązanie wyzwalacza jednostki.](durable-functions-bindings.md#entity-trigger) Oba te wyzwalacze działają, reagując na wiadomości, które są w kolejce do [centrum zadań](durable-functions-task-hubs.md). Podstawowym sposobem dostarczania tych komunikatów jest użycie [powiązania klienta programu orchestrator](durable-functions-bindings.md#orchestration-client) lub [powiązania klienta jednostki](durable-functions-bindings.md#entity-client) z *funkcji klienta.* Każda funkcja nieadastora może być *funkcją klienta*. Na przykład można wyzwolić koordynatora z funkcji wyzwalanej przez HTTP, funkcji wyzwalanej usługi Azure Event Hub itp. Co sprawia, że funkcja *klienta funkcji* jest jego użycie powiązania wyjściowego klienta trwałe.

> [!NOTE]
> W przeciwieństwie do innych typów funkcji, funkcji koordynatora i jednostki nie można wyzwolić bezpośrednio za pomocą przycisków w witrynie Azure Portal. Jeśli chcesz przetestować funkcję koordynatora lub jednostki w witrynie Azure Portal, należy zamiast tego uruchomić *funkcję klienta,* która uruchamia funkcję koordynatora lub jednostki w ramach jego implementacji. Aby uzyskać najprostsze doświadczenie w testowaniu, zalecana jest funkcja *ręcznego wyzwalania.*

Oprócz wyzwalania funkcji koordynatora lub jednostki, trwałe powiązanie *klienta* może służyć do interakcji z uruchomionymi aranżacjami i jednostkami. Na przykład aranżacji można zbadać, zakończone i może mieć zdarzenia podniesione do nich. Aby uzyskać więcej informacji na temat zarządzania aranżacjami i jednostkami, zobacz artykuł [o zarządzaniu wystąpieniami.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, utwórz pierwszą trwałą funkcję w [języku C#](durable-functions-create-first-csharp.md) lub [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Dowiedz się więcej o aranżacjach funkcji trwałych](durable-functions-orchestrations.md)