---
title: Co to jest Azure przez wartownika w wersji zapoznawczej? | Dokumentacja firmy Microsoft
description: Więcej informacji na temat platformy Azure przez wartownika, jego kluczowych możliwości i jak to działa.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2859df94b1418cfa9fbfaa341e6bf08b257875bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715081"
---
# <a name="what-is-azure-sentinel-preview"></a>Co to jest Azure przez wartownika w wersji zapoznawczej?

> [!IMPORTANT]
> Wartownik platformy Azure jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wskaźnikowe systemu Microsoft Azure to skalowalne, natywnych dla chmury, **zarządzania zdarzeniami informacje zabezpieczeń (SIEM)** i **aranżacji zabezpieczeń automatycznych odpowiedzi (WZRASTAĆ)** rozwiązania. Wartownik platformy Azure oferuje usługę intelligent security analizę zagrożeń całe przedsiębiorstwo, przewidujące jednego rozwiązania alertu wykrywania, widoczność zagrożeń, myślistwo aktywnego i reagowania na zagrożenia. 

Wartownik platformy Azure jest widok ptaka w całym przedsiębiorstwie złagodzenia wpływem coraz bardziej zaawansowanymi atakami, zwiększenie ilości alertów i czas rozpoznawania harmonogramów.

- **Zbieranie danych w skali chmury** we wszystkich użytkowników, urządzeń, aplikacji i infrastruktury, zarówno lokalnie, jak i w wielu chmur. 

- **Wykrywanie zagrożeń którego wcześniej nie wykryto**i minimalizacji fałszywych alarmów przy użyciu analizy i analizy zagrożeń niezrównaną firmy Microsoft. 

- **Badanie zagrożeń dzięki sztucznej inteligencji**i możliwe dla podejrzanych działań na dużą skalę, korzystając lat. cybernetycznymi zabezpieczeń w firmie Microsoft. 

- **Szybko reagować na zdarzenia związane z** za pomocą wbudowanych aranżacji i automatyzacji typowych zadań.


![Usługa Azure wartownik podstawowe możliwości](./media/overview/core-capabilities.png)

Korzystając z pełnego zakresu istniejące usługi platformy Azure, Azure przez wartownika natywnie zawiera podstawy sprawdzone, takich jak usługa Log Analytics i Logic Apps. Wartownik Azure wzbogaca Twoje badania i wykrywanie ze sztuczną Inteligencją i zapewnia strumienia analizy zagrożeń firmy Microsoft i umożliwia zebranie własne analizy zagrożeń. 

 
## <a name="connect-to-all-your-data"></a>Łączenie do Twoich danych

Aby dołączyć przez wartownika platformy Azure, najpierw musisz [łączenie ze źródłami zabezpieczeń](connect-data-sources.md). Azure wartownik dołączono wiele łączników dla rozwiązań firmy Microsoft, dostępne poza pole, a także w czasie rzeczywistym integracji, w tym rozwiązań do ochrony przed zagrożeniami firmy Microsoft i Microsoft 365 źródeł, takich jak usługi Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto ma wbudowanych łączników szerszy ekosystemem zabezpieczenia dla rozwiązań firmy Microsoft. Można również użyć typowego formatu zdarzeń dziennika systemu lub interfejsu API REST, nawiązywanie połączeń ze źródłami danych z platformy Azure przez wartownika także.  

![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Pulpity nawigacyjne

Po nawiązaniu połączenia źródła danych mogą wybierać z galerii [profesjonalnego utworzone pulpity nawigacyjne](quickstart-get-visibility.md#dashboards) , Prezentuj wnioski ze swoich źródeł danych. Każdy pulpit nawigacyjny jest w pełni dostosowywalnych — można dodać własną logiką lub modyfikować zapytania lub pulpitu nawigacyjnego można utworzyć od podstaw.

Pulpity nawigacyjne zapewniają interaktywnej wizualizacji za pomocą zaawansowanych analiz ułatwiających Twojej analityków zabezpieczeń, która pozwoli lepiej zrozumieć co się dzieje podczas ataku. Narzędzia do badania pozwalają na szczegółowe informacje dotyczące dowolne pole, wszelkie dane, do szybkiego tworzenia kontekstu zagrożeń. 

![Pulpity nawigacyjne](./media/overview/dashboards.png)

## <a name="analytics"></a>Analiza

Ułatwiające zmniejszenia szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, korzysta z platformy Azure przez wartownika [analytics w celu skorelowania alerty w przypadkach](tutorial-detect-threats.md). **Przypadków** grup powiązanych alertów, tworzonych możliwością wykonywania akcji możliwych zagrożenie, którą można zbadać i rozwiązać. Użyj reguł korelacji wbudowanych jako — jest lub używać ich jako punktu wyjścia do tworzenia własnych. Wartownik platformy Azure zapewnia również maszyny uczenia reguły, aby zmapować swoje zachowanie sieci, a następnie poszukaj anomalie między zasobami. Te dane analityczne połączyć z kropek, przez połączenie o niskiej wierności alertów dotyczących różnych obiektów w potencjalne o dużej wierności związane z bezpieczeństwem.

![Przypadków](./media/overview/cases.png)

## <a name="user-analytics"></a>Analiza użytkowników

Dzięki integracji z funkcjami macierzystymi (ML), uczenia maszynowego i [analizy użytkowników](user-analytics.md), przez wartownika Azure może pomóc szybko wykryć zagrożenia. Wartownik Azure bezproblemowo integrują się z usługą Azure Advanced Threat Protection do analizy zachowania użytkowników i priorytety użytkowników, którzy należy badać w pierwszym, na podstawie ich alerty i wzorców podejrzanej aktywności na platformie Azure przez wartownika i Microsoft 365.

![Analiza użytkowników](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Zabezpieczenia automatyzacji i aranżacji

Automatyzuj typowe i [uprościć aranżacji zabezpieczeń z elementów playbook](tutorial-respond-threats-playbook.md) integrowane z usługami platformy Azure, a także z istniejących narzędzi. Zbudowany na fundamencie usługi Azure Logic Apps, wskaźnikowe Azure automatyzacji i aranżacji rozwiązanie zapewnia wysoce rozszerzalną architekturą, która umożliwia automatyzację skalowalne jako nowych technologii i że zagrożenia. Tworzenie elementów playbook w usłudze Azure Logic Apps, możesz wybrać spośród rosnącą z galerii wbudowane elementy playbook. Obejmują one [ponad 200 łączników](https://docs.microsoft.com/azure/connectors/apis-list) dla usług, takich jak usługa Azure functions. Łączniki umożliwiają zastosowanie dowolnej logiki niestandardowej kodu, ServiceNow, Jira, Zendesk, HTTP żądania, Microsoft Teams, Slack, usługi Windows Defender ATP i usługa Cloud App Security.

Na przykład jeśli używasz systemu obsługi biletów usługi ServiceNow, można użyć narzędzi dostarczanych za pomocą usługi Azure Logic Apps Automatyzowanie przepływów pracy i otwórz bilet w każdym wykryciu konkretnego zdarzenia usługi ServiceNow.

![Elementy playbook](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Badanie

Azure wartownik [szczegółowe badanie](tutorial-investigate-cases.md) narzędzia ułatwiają zrozumieć zakres i znalezienia głównej przyczyny, potencjalnym zagrożeniem dla bezpieczeństwa. Możesz wybrać jednostkę na wykresie interaktywnego, aby zadawać pytania interesujące dla określonej jednostki i przejść do tej jednostki i jego połączenia, aby przejść do głównej przyczyny zagrożenia. 

![Badanie](./media/overview/investigation.png)


## <a name="hunting"></a>Wyszukiwanie zagrożeń

Użyj wskaźnikowe Azure [myślistwo zaawansowane narzędzia do wyszukiwania i zapytania](hunting.md), zależnie od struktury MITRE, która umożliwia proaktywne możliwe pod kątem zagrożeń bezpieczeństwa w źródłach danych Twojej organizacji, zanim alert zostanie wywołany. Po odkryć, którym zapytaniu myślistwo informują o wysokiej wartości możliwych ataków, możesz również tworzyć reguły niestandardowe wykrywania oparte na kwerendzie i prezentuj takie wnioski jako alerty do usługi obiektów odpowiadających w zdarzenia zabezpieczeń. Podczas myślistwo, możesz utworzyć zakładki dla interesujących zdarzeń, dzięki któremu można wrócić do nich później, udostępnić je innym osobom i zgrupować je z innymi zdarzeniami korelację do tworzenia atrakcyjnych przypadku badania.

![Wyszukiwanie zagrożeń](./media/overview/hunting.png)

## <a name="community"></a>Społeczność

Społeczność platformy Azure przez wartownika jest potężnym zasobem dla wykrywania zagrożeń i automatyzacji. Nasze analityków zabezpieczeń firmy Microsoft stale tworzenie i dodawać nowe pulpity nawigacyjne, elementy playbook, myślistwo zapytań i uzyskać więcej informacji, publikując je społeczności do użycia w danym środowisku. Możesz pobrać przykładowej zawartości z prywatnej społeczności usługi GitHub [repozytorium](https://aka.ms/asicommunity) Tworzenie niestandardowych pulpitów nawigacyjnych, zapytań myślistwo, notesy i elementy playbook dla platformy Azure przez wartownika. 

![Społeczność](./media/overview/community.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć korzystanie z platformy Azure przez wartownika, musisz mieć subskrypcji platformy Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak [dołączyć dane do platformy Azure przez wartownika](quickstart-onboard.md), i [Uzyskaj wgląd w dane i potencjalne zagrożenia](quickstart-get-visibility.md).
