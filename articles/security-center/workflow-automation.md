---
title: Automatyzacja przepływu pracy w usłudze Azure Security Center | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć i automatyzować przepływy pracy w usłudze Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5d947cf41e13abdea9a2fd29f8a740d0c101dc6f
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397910"
---
# <a name="workflow-automation"></a>Automatyzacja przepływu pracy

Każdy program zabezpieczeń zawiera wiele przepływów pracy w odpowiedzi na zdarzenia. Procesy te mogą obejmować powiadamianie odpowiednich zainteresowanych stron, rozpoczęcie procesu zarządzania zmianami i stosowanie określonych kroków korygujących. Eksperci od zabezpieczeń zalecają zautomatyzowanie jak największej liczby kroków tych procedur. Automatyzacja zmniejsza obciążenie. Może również poprawić bezpieczeństwo, zapewniając, że kroki procesu są wykonywane szybko, konsekwentnie i zgodnie ze wstępnie zdefiniowanymi wymaganiami.

W tym artykule opisano funkcję automatyzacji przepływu pracy usługi Azure Security Center. Ta funkcja może wyzwolić aplikacje logiki w alertach i zaleceniach dotyczących zabezpieczeń. Na przykład można chcieć, aby usługa Security Center wysyłała określonej użytkownikowi wiadomość e-mail po wystąpieniu alertu. Dowiesz się również, jak tworzyć aplikacje logiki przy użyciu [aplikacji logiki Azure.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

> [!NOTE]
> Jeśli wcześniej był używany widok Playbooks (Preview) na pasku bocznym, znajdziesz te same funkcje wraz z rozszerzoną funkcją na nowej stronie automatyzacji przepływu pracy.


## <a name="requirements"></a>Wymagania

* Aby pracować z przepływami pracy usługi Azure Logic Apps, musisz mieć następujące role/uprawnienia aplikacji logiki:

    * [Uprawnienia operatora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) są wymagane lub dostęp do odczytu/wyzwalacza aplikacji logiki (ta rola nie może tworzyć ani edytować aplikacji logiki; *tylko uruchamianie* istniejących)

    * [Uprawnienia współautora aplikacji logiki](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) są wymagane do tworzenia i modyfikowania aplikacji logiki

* Jeśli chcesz używać łączników aplikacji logiki, może być konieczne dodatkowe poświadczenia, aby zalogować się do swoich odpowiednich usług (na przykład outlook/teams/slack wystąpień)


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Tworzenie aplikacji logiki i definiowanie, kiedy powinna być uruchamiana automatycznie 

1. Na pasku bocznym centrum zabezpieczeń wybierz pozycję **Automatyzacja przepływu pracy**.

    [![Lista automatyzacji przepływu pracy](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    Na tej stronie można tworzyć nowe reguły automatyzacji, a także włączać, wyłączać lub usuwać istniejące.  
1. Aby zdefiniować nowy przepływ pracy, kliknij przycisk **Dodaj automatyzację przepływu pracy**. 

    Pojawi się okienko z opcjami nowej automatyzacji. W tym miejscu można wprowadzić:
    1. Nazwa i opis automatyzacji.
    1. Wyzwalacze, które zainicjują ten automatyczny przepływ pracy. Na przykład może być aplikacja logiki do uruchomienia po wygenerowaniu alertu zabezpieczeń, który zawiera "SQL".
    1. Aplikacja logiki, która będzie działać po spełnieniu warunków wyzwalacza. 

        [![Lista automatyzacji przepływu pracy](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. W sekcji Akcje kliknij pozycję **Utwórz nowy,** aby rozpocząć proces tworzenia aplikacji logiki.

    Zostaniesz zabrany do usługi Azure Logic Apps.

    [![Tworzenie nowej aplikacji logiki](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Wprowadź nazwę, grupę zasobów i lokalizację, a następnie kliknij przycisk **Utwórz**.

1. W nowej aplikacji logiki można wybrać z wbudowanych, wstępnie zdefiniowanych szablonów z kategorii zabezpieczeń. Lub można zdefiniować niestandardowy przepływ zdarzeń, które wystąpią podczas wyzwalania tego procesu.

    W projektancie aplikacji logiki obsługiwane są następujące wyzwalacze z łączników usługi Security Center:

    * **Po utworzeniu lub wyzwoleniu zalecenia usługi Azure Security Center**
    * **Po utworzeniu lub wyzwoleniu alertu usługi Azure Security Center** 
    
    > [!TIP]
    > Wyzwalacz można dostosować tak, aby odnosił się tylko do alertów z poziomami ważności, które Cię interesują.
    
    > [!NOTE]
    > Jeśli używasz starszego wyzwalacza "Po wyzwoleniu odpowiedzi na alert usługi Azure Security Center", aplikacje logiki nie zostaną uruchomione przez funkcję automatyzacji przepływu pracy. Zamiast tego należy użyć jednego z wyzwalaczy wymienionych powyżej. 

    [![Przykładowa aplikacja logiki](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Po zdefiniowaniu aplikacji logiki wróć do okienka definicji automatyzacji przepływu pracy ("Dodaj automatyzację przepływu pracy"). Kliknij **przycisk Odśwież,** aby upewnić się, że nowa aplikacja logiki jest dostępna do wyboru.

    ![Odświeżanie](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Wybierz aplikację logiki i zapisz automatyzację. Należy zauważyć, że w rozwijaniu aplikacji logiki są wyświetlane tylko aplikacje logiki z wymienionymi powyżej łącznikami usługi Security Center.


## <a name="manually-trigger-a-logic-app"></a>Ręczne wyzwalanie aplikacji logiki

Aplikacje logiki można również uruchomić ręcznie podczas wyświetlania alertu zabezpieczeń lub dowolnej rekomendacji, która oferuje [szybkie korygowanie.](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations#quick-fix-remediation)

Aby ręcznie uruchomić aplikację logiki, otwórz alert lub zalecenie obsługujące szybkie korygowanie i kliknij pozycję **Trigger Logic App:**

[![Ręczne wyzwalanie aplikacji logiki](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schematy typów danych

Aby wyświetlić schematy zdarzeń nieprzetworzonych alertów zabezpieczeń lub zdarzeń zaleceń przekazanych do wystąpienia aplikacji logiki, odwiedź [schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas). Może to być przydatne w przypadkach, gdy nie używasz wbudowanych łączników aplikacji logiki usługi Security Center wymienionych powyżej, ale zamiast tego używasz ogólnego łącznika HTTP aplikacji logiki — można użyć schematu JSON zdarzenia, aby ręcznie przeanalizować go według własnego uznania.

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o tworzeniu aplikacji logiki, automatyzacji ich wykonywania w u centrum zabezpieczeń i uruchamianiu ich ręcznie. 

Zobacz też: 

- [Moduł Microsoft Learn dotyczący sposobu automatyzacji automatyzacji przepływu pracy w celu zautomatyzowania odpowiedzi na zabezpieczenia](https://docs.microsoft.com/learn/modules/resolve-threats-with-azure-security-center/)
- [Zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń Azure](security-center-recommendations.md)
- [Alerty zabezpieczeń w usłudze Azure Security Center](security-center-alerts-overview.md)
- [Azure Logic Apps — informacje](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Łączniki usługi Logic Apps](https://docs.microsoft.com/connectors/)
- [Schematy typów danych automatyzacji przepływu pracy](https://aka.ms/ASCAutomationSchemas)
