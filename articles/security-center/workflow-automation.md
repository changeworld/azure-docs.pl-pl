---
title: Automatyzacja przepływu pracy (wersja zapoznawcza) w Azure Security Center | Microsoft Docs
description: Dowiedz się, jak tworzyć i automatyzować przepływy pracy w Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 57351ccf0c6155a1a3532ec9e6481a724e3219aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462445"
---
# <a name="workflow-automation-preview"></a>Automatyzacja przepływu pracy (wersja zapoznawcza)

Każdy program zabezpieczeń zawiera wiele przepływów pracy dotyczących odpowiedzi na zdarzenia. Procesy te mogą obejmować Powiadamianie właściwych uczestników projektu, uruchamianie procesu zarządzania zmianami i stosowanie określonych czynności zaradczych. Specjaliści ds. zabezpieczeń zaleca się zautomatyzować dowolną liczbę kroków tych procedur. Automatyzacja redukuje obciążenie. Pozwala również zwiększyć bezpieczeństwo, zapewniając, że kroki procesu są wykonywane szybko, spójnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

W tym artykule opisano funkcję automatyzacji przepływu pracy (wersja zapoznawcza) Azure Security Center. Ta funkcja w wersji zapoznawczej może wyzwolić Logic Apps na temat alertów zabezpieczeń i zaleceń. Na przykład możesz chcieć, aby Security Center e-mail określonego użytkownika w przypadku wystąpienia alertu. Dowiesz się również, jak utworzyć Logic Apps przy użyciu [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Jeśli wcześniej używasz widoku elementy PlayBook (wersja zapoznawcza) na pasku bocznym, znajdziesz te same funkcje wraz z rozwiniętymi funkcjami na stronie nowe Automatyzacja przepływu pracy (wersja zapoznawcza).


## <a name="requirements"></a>Wymagania

* Aby można było korzystać z przepływów pracy Azure Logic Apps, musisz mieć następujące role/uprawnienia Logic Apps:

    * Wymagane są uprawnienia [operatora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) lub dostęp do odczytu/wyzwalacza aplikacji logiki (Ta rola nie może tworzyć ani edytować aplikacji logiki; można *uruchamiać* tylko istniejące)

    * Uprawnienia [współautora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) są wymagane do utworzenia i modyfikacji aplikacji logiki

* Jeśli chcesz używać łączników aplikacji logiki, możesz potrzebować dodatkowych poświadczeń, aby zalogować się do odpowiednich usług (na przykład wystąpień programu Outlook/zespołów/zapasowych).


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Tworzenie aplikacji logiki i Definiowanie jej automatycznego uruchamiania 

1. Na pasku bocznym Security Center wybierz pozycję **Automatyzacja przepływu pracy (wersja zapoznawcza)** .

    [![lista automatyzacji przepływu pracy](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Na tej stronie można tworzyć nowe reguły automatyzacji, a także włączać, wyłączać lub usuwać istniejące.  
1. Aby zdefiniować nowy przepływ pracy, kliknij przycisk **Dodaj automatyzację przepływu pracy**. 

    Zostanie wyświetlone okienko z opcjami nowej automatyzacji. W tym miejscu możesz wprowadzić:
    1. Nazwa i Opis automatyzacji.
    1. Wyzwalacze, które będą inicjować ten automatyczny przepływ pracy. Na przykład może być konieczne uruchomienie aplikacji logiki po wygenerowaniu alertu zabezpieczeń zawierającego "SQL".
    1. Aplikacja logiki, która będzie uruchamiana po spełnieniu warunków wyzwalacza. 

        [![lista automatyzacji przepływu pracy](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. W sekcji Actions (akcje) kliknij pozycję **Utwórz nową** , aby rozpocząć proces tworzenia aplikacji logiki.

    Nastąpi przekierowanie do Azure Logic Apps.

    [![tworzenia nowej aplikacji logiki](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Wprowadź nazwę, grupę zasobów i lokalizację, a następnie kliknij przycisk **Utwórz**.

1. W nowej aplikacji logiki można wybrać opcję z wbudowanych, wstępnie zdefiniowanych szablonów z kategorii zabezpieczenia. Można też zdefiniować niestandardowy przepływ zdarzeń, które mają być wykonywane w momencie wyzwolenia tego procesu.

    W Projektancie aplikacji logiki są obsługiwane następujące wyzwalacze z łączników Security Center:

    * **Po utworzeniu lub wyzwoleniu rekomendacji Azure Security Center (wersja zapoznawcza)**
    * **Po utworzeniu lub wyzwoleniu alertu Azure Security Center (wersja zapoznawcza)**
    
    > [!NOTE]
    > Jeśli używasz starszego wyzwalacza "gdy zostanie wyzwolona odpowiedź na Azure Security Center alertu", Logic Apps nie zostanie uruchomiona przez funkcję automatyzacji przepływu pracy. Zamiast tego należy użyć dowolnego wyzwalacza wymienionego powyżej. 

    [![Przykładowa aplikacja logiki](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po zdefiniowaniu aplikacji logiki Wróć do okienka definicji automatyzacji przepływu pracy ("Dodaj automatyzację przepływu pracy"). Kliknij przycisk **Odśwież** , aby upewnić się, że nowa aplikacja logiki jest dostępna do wybrania.

    ![Odśwież](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wybierz aplikację logiki i Zapisz automatyzację. Należy pamiętać, że lista rozwijana aplikacji logiki zawiera tylko Logic Apps z obsługą łączników Security Center wymienionych powyżej.


## <a name="manually-trigger-a-logic-app"></a>Ręczne wyzwalanie aplikacji logiki

Logic Apps można również uruchomić ręcznie podczas wyświetlania zalecenia dotyczącego zabezpieczeń.

Aby ręcznie uruchomić aplikację logiki, Otwórz rekomendację i kliknij pozycję wyzwalanie aplikacji logiki (wersja zapoznawcza):

[![ręcznie wyzwolić aplikację logiki](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schematy typów danych

Aby wyświetlić nieprzetworzone schematy zdarzeń alertów zabezpieczeń lub zaleceń przesyłanych do wystąpienia aplikacji logiki, odwiedź [schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas). Może to być przydatne w przypadkach, gdy nie Security Center korzystasz z wbudowanych łączników aplikacji logiki, które zostały wymienione powyżej, ale zamiast tego używamy ogólnego łącznika HTTP aplikacji logiki — możesz użyć schematu JSON zdarzenia, aby ręcznie przeanalizować ją zgodnie z oczekiwaniami.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje na temat tworzenia Logic Apps, uruchamiania ich ręcznie w programie Security Center i automatyzowania ich wykonywania. 

Inne powiązane materiały można znaleźć w następujących artykułach: 

- [Zalecenia dotyczące zabezpieczeń w Azure Security Center](security-center-recommendations.md)
- [Alerty zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md)
- [Informacje o Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Łączniki Logic Apps](https://docs.microsoft.com/connectors/)
- [Schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas)
