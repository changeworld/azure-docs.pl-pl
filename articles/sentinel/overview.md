---
title: Co to jest usługa Azure Sentinel?| Dokumenty firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Sentinel, jej kluczowych możliwościach i o tym, jak działa.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7d0f246a96072a6e433b0762256467f1c20a45fe
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77581722"
---
# <a name="what-is-azure-sentinel"></a>Co to jest usługa Azure Sentinel?

Microsoft Azure Sentinel to skalowalne, natywne dla chmury rozwiązanie **do zarządzania zdarzeniami informacji o zabezpieczeniach (SIEM)** i **automatycznej odpowiedzi aranżacji zabezpieczeń (SOAR).** Usługa Azure Sentinel zapewnia inteligentną analizę zabezpieczeń i analizę zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, proaktywnego polowania i reagowania na zagrożenia. 

Usługa Azure Sentinel to widok z lotu ptaka w całym przedsiębiorstwie, który łagodzi stres związany z coraz bardziej wyrafinowanymi atakami, zwiększa liczbę alertów i długich ram czasowych.

- **Zbieraj dane w skali chmury** dla wszystkich użytkowników, urządzeń, aplikacji i infrastruktury, zarówno lokalnie, jak i w wielu chmurach. 

- **Wykrywanie wcześniej niewykrytych zagrożeń**i minimalizowanie fałszywych alarmów przy użyciu analizy firmy Microsoft i niezrównanej analizy zagrożeń. 

- **Badaj zagrożenia za pomocą sztucznej inteligencji**i szukaj podejrzanych działań na dużą skalę, wykorzystując lata pracy nad bezpieczeństwem cybernetycznym w firmie Microsoft. 

- **Szybkie reagowanie na incydenty** dzięki wbudowanej aranżacji i automatyzacji typowych zadań.

![Podstawowe możliwości usługi Azure Sentinel](./media/overview/core-capabilities.png)

Opierając się na pełnym zakresie istniejących usług platformy Azure, usługa Azure Sentinel natywnie zawiera sprawdzone fundamenty, takie jak usługa Log Analytics i aplikacje logiki. Usługa Azure Sentinel wzbogaca twoje badania i wykrywanie za pomocą sztucznej inteligencji i udostępnia strumień analizy zagrożeń firmy Microsoft i umożliwia wprowadzenie własnych analiz zagrożeń. 

## <a name="connect-to-all-your-data"></a>Łączenie się ze wszystkimi danymi

Aby urządzenie Azure Sentinel było wbudowane, musisz najpierw [połączyć się ze źródłami zabezpieczeń.](connect-data-sources.md) Usługa Azure Sentinel jest dostarczana z wieloma łącznikami dla rozwiązań firmy Microsoft, dostępnymi po wyjęciu z pudełka i zapewniającymi integrację w czasie rzeczywistym, w tym rozwiązania microsoft threat protection i źródła usługi Microsoft 365, w tym usługę Office 365, azure ad, azure atp i Microsoft Cloud App Security i nie tylko. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań innych firm. Można również użyć wspólnego formatu zdarzeń, Syslog lub REST-API, aby połączyć źródła danych z usługą Azure Sentinel.  

![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Skoroszyty

Po [podłączeniu źródeł danych](quickstart-onboard.md) do usługi Azure Sentinel można monitorować dane przy użyciu integracji usługi Azure Sentinel ze skoroszytami usługi Azure Monitor, co zapewnia wszechstronność w tworzeniu niestandardowych skoroszytów. Skoroszyty są wyświetlane w inny sposób w usłudze Azure Sentinel, ale może być przydatne, aby zobaczyć, jak [tworzyć interaktywne raporty za pomocą skoroszytów usługi Azure Monitor](../azure-monitor/app/usage-workbooks.md). Usługa Azure Sentinel umożliwia tworzenie niestandardowych skoroszytów w danych, a także jest dostarczany z wbudowanymi szablonami skoroszytu, które umożliwiają szybkie uzyskiwanie szczegółowych informacji na temat danych zaraz po podłączeniu źródła danych.

![Pulpity nawigacyjne](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Analiza

Aby zmniejszyć hałas i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, usługa Azure Sentinel używa [analizy do skorelowania alertów z incydentami.](tutorial-detect-threats-built-in.md) **Zdarzenia** to grupy powiązanych alertów, które razem tworzą możliwe zagrożenie, które można zbadać i rozwiązać. Użyj wbudowanych reguł korelacji w stanie rzeczywistym lub użyj ich jako punktu wyjścia do tworzenia własnych. Usługa Azure Sentinel udostępnia również reguły uczenia maszynowego do mapowania zachowania sieci, a następnie wyszukiwania anomalii w zasobach. Te analizy łączą kropki, łącząc alerty o niskiej wierności o różnych jednostkach w potencjalnych zdarzeniach zabezpieczeń o wysokiej wierności.

![Zdarzenia](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automatyzacja zabezpieczeń & aranżacji

Zautomatyzuj typowe zadania i [uprość aranżację zabezpieczeń za pomocą podręczników](tutorial-respond-threats-playbook.md) integrujących się z usługami platformy Azure, a także istniejących narzędzi. Oparte na platformie Azure Logic Apps rozwiązanie automatyzacji i aranżacji usługi Azure Sentinel zapewnia wysoce rozszerzalną architekturę, która umożliwia skalowalną automatyzację w miarę pojawiania się nowych technologii i zagrożeń. Aby tworzyć podręczniki za pomocą usługi Azure Logic Apps, możesz wybierać spośród rosnącej galerii wbudowanych podręczników. Należą do nich [ponad 200 łączników](https://docs.microsoft.com/azure/connectors/apis-list) dla usług, takich jak funkcje platformy Azure. Łączniki umożliwiają zastosowanie dowolnej logiki niestandardowej w kodzie, ServiceNow, Jira, Zendesk, żądaniaCH HTTP, Microsoft Teams, Slack, Windows Defender ATP i Cloud App Security.

Na przykład jeśli używasz servicenow systemu biletowego, można użyć narzędzi dostarczonych do korzystania z usługi Azure Logic Apps do automatyzacji przepływów pracy i otworzyć bilet w ServiceNow za każdym razem, gdy zostanie wykryte określone zdarzenie.

![Elementy playbook](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Badanie

Obecnie w wersji zapoznawczej narzędzia [do głębokiego badania](tutorial-investigate-cases.md) usługi Azure Sentinel pomagają zrozumieć zakres i znaleźć główną przyczynę potencjalnego zagrożenia bezpieczeństwa. Można wybrać jednostkę na interaktywnym wykresie, aby zadać interesujące pytania dla określonej jednostki i przejść do szczegółów tej jednostki i jej połączeń, aby uzyskać dostęp do głównej przyczyny zagrożenia. 

![Badanie](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Wyszukiwanie zagrożeń

Użyj [zaawansowanych narzędzi wyszukiwania i zapytań typu](hunting.md)Azure Sentinel opartych na platformie MITRE, które umożliwiają proaktywne polowanie na zagrożenia bezpieczeństwa w źródłach danych organizacji przed wyzwoleniem alertu. Po odkryciu, które zapytanie myśliwskie zapewnia wysokiej wartości wgląd w możliwych ataków, można również utworzyć niestandardowe reguły wykrywania na podstawie zapytania i powierzchni tych spostrzeżeń jako alerty do odpowiedzi na zdarzenia zabezpieczeń. Podczas polowania możesz tworzyć zakładki dla interesujących wydarzeń, umożliwiając późniejszy powrót do nich, udostępnianie ich innym i grupowanie ich z innymi zdarzeniami korelującymi, aby utworzyć przekonujący incydent do zbadania.

![Wyszukiwanie zagrożeń](./media/overview/hunting.png)

## <a name="community"></a>Społeczność

Społeczność usługi Azure Sentinel jest potężnym zasobem do wykrywania zagrożeń i automatyzacji. Nasi analitycy zabezpieczeń firmy Microsoft stale tworzą i dodają nowe skoroszyty, podręczniki, zapytania myśliwskie i inne, publikując je w społeczności, aby można było ich używać w swoim środowisku. Możesz pobrać przykładową zawartość z [repozytorium](https://aka.ms/asicommunity) GitHub społeczności prywatnej, aby utworzyć niestandardowe skoroszyty, zapytania myśliwskie, notesy i podręczniki dla usługi Azure Sentinel. 

![Społeczność](./media/overview/community.png)

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć korzystanie z usługi Azure Sentinel, potrzebujesz subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączać dane do usługi Azure Sentinel](quickstart-onboard.md)i uzyskać wgląd w dane i potencjalne [zagrożenia.](quickstart-get-visibility.md)
