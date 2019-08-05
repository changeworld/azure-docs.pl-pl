---
title: Co to jest wersja zapoznawcza usługi Azure wskaźnikowej? | Microsoft Docs
description: Dowiedz się więcej na temat platformy Azure, jej najważniejszych możliwości i sposobu działania.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 15cf770a372c9a1386dd0293abeac01fd3cacf63
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779742"
---
# <a name="what-is-azure-sentinel-preview"></a>Co to jest wersja zapoznawcza usługi Azure wskaźnikowej?

> [!IMPORTANT]
> W publicznej wersji zapoznawczej jest obecnie dostępna usługa Azure.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Microsoft Azure wskaźnikiem wydajności jest skalowalne i natywne rozwiązanie do **zarządzania zdarzeniami zabezpieczeń (Siem)** i **o (Security Orchestration)** . Platforma Azure — wskaźnik przedstawia inteligentne rozwiązania w zakresie zabezpieczeń i analizy zagrożeń w całym przedsiębiorstwie, zapewniając jedno rozwiązanie do wykrywania alertów, widoczności zagrożeń, aktywnego polowania i reagowania na zagrożenia. 

Azure — wskaźnikowy to Twój Widok z oczami w całym przedsiębiorstwie, zmniejszający nacisk coraz bardziej zaawansowanych ataków, zwiększając liczbę alertów i długotrwałe przedziały czasu rozwiązania.

- **Zbieraj dane w skali chmury** między wszystkimi użytkownikami, urządzeniami, aplikacjami i infrastrukturą, zarówno lokalnie, jak i w wielu chmurach. 

- **Wykrywaj wcześniej niewykryte zagrożenia**i Minimalizuj fałszywie dodatnie, korzystając z analizy i niezrównanej analizy zagrożeń firmy Microsoft. 

- **Zbadaj zagrożenia przy użyciu sztucznej analizy**i Wyszukaj podejrzane działania na dużą skalę, wybierając kolejno lata cybernetycznymi zabezpieczenia w firmie Microsoft. 

- **Szybko reaguj na zdarzenia** dzięki wbudowanej aranżacji i automatyzacji typowych zadań.


![Podstawowe możliwości platformy Azure wskaźnikowego](./media/overview/core-capabilities.png)

Kompilowanie w całym zakresie istniejących usług platformy Azure obejmuje natywne, udokumentowane fundacje, takie jak Log Analytics i Logic Apps. Platforma Azure wskaźnikowa wzbogaca badanie i wykrywanie przy użyciu AI oraz zapewnia strumień analizy zagrożeń firmy Microsoft i umożliwia korzystanie z własnej analizy zagrożeń. 

 
## <a name="connect-to-all-your-data"></a>Połącz ze wszystkimi danymi

Aby przejść do tablicy wskaźnikowej platformy Azure, musisz najpierw [nawiązać połączenie ze źródłami zabezpieczeń](connect-data-sources.md). Wskaźnik platformy Azure obejmuje wiele łączników dla rozwiązań firmy Microsoft, dostępnych poza platformą i zapewniania integracji w czasie rzeczywistym, w tym rozwiązań firmy Microsoft do ochrony przed zagrożeniami, a także źródeł Microsoft 365, takich jak Office 365, Azure AD, Azure ATP i Microsoft Cloud App Security i nie tylko. Ponadto istnieją wbudowane łączniki do szerszego ekosystemu zabezpieczeń dla rozwiązań firm innych niż Microsoft. Możesz również użyć typowego formatu zdarzeń, dziennika systemowego lub REST-API, aby połączyć źródła danych z systemem Azure.  

![Moduły zbierające dane](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Pulpity nawigacyjne

Po nawiązaniu połączenia ze źródłami danych można wybierać z galerii [ekspertów](quickstart-get-visibility.md#dashboards) , którzy utworzyli szczegółowe informacje ze źródeł danych. Każdy pulpit nawigacyjny jest w pełni dostosowywany — możesz dodać własne logiki lub zmodyfikować zapytania lub utworzyć pulpit nawigacyjny od podstaw.

Pulpity nawigacyjne zapewniają interaktywną wizualizację dzięki zaawansowanej analizie, która pomaga analitykom zabezpieczeń lepiej zrozumieć, co się dzieje podczas ataku. Narzędzia do badania umożliwiają Głębokie szczegółowe na dowolnym polu, z jakichkolwiek danych, do szybkiego tworzenia kontekstu zagrożeń. 

![Pulpity nawigacyjne](./media/overview/dashboards.png)

## <a name="analytics"></a>Analiza

Aby ułatwić zredukowanie szumu i zminimalizować liczbę alertów, które należy przejrzeć i zbadać, wskaźnik platformy Azure używa [analizy do skorelowania alertów](tutorial-detect-threats.md)z zdarzeniami. **Zdarzenia** to grupy powiązanych alertów, które wspólnie tworzą możliwe do podjęcia działania, które można zbadać i rozwiązać. Użyj wbudowanych reguł korelacji jako-lub użyj ich jako punktu wyjścia do tworzenia własnych. Usługa Azure Learning udostępnia także reguły uczenia maszynowego, które umożliwiają mapowanie zachowania sieci, a następnie poszukiwanie anomalii w zasobach. Te analizy łączą kropki, łącząc alerty o niskiej wierności dotyczące różnych jednostek w potencjalnych zdarzeniach związanych z zabezpieczeniami o wysokiej wierności.

![Padkach](./media/overview/cases.png)

## <a name="user-analytics"></a>Analiza użytkowników

Dzięki natywnej integracji uczenia maszynowego (ML) i [analizie użytkownika](user-analytics.md)usługa Azure wskaźnikowa może pomóc szybko wykrywać zagrożenia. Na platformie Azure wskaźnikiem wiedzy można bezproblemowo zintegrować usługę Azure Advanced Threat Protection, aby analizować zachowanie użytkowników i określić priorytety użytkowników, którzy powinni najpierw badać, na podstawie ich alertów oraz podejrzanych wzorców aktywności na platformie Azure i Microsoft 365.

![Analiza użytkowników](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>& Aranżacji usługi Security Automation

Automatyzuj typowe zadania i [Uprość aranżację zabezpieczeń dzięki usłudze elementy PlayBook](tutorial-respond-threats-playbook.md) , która integruje się z usługami platformy Azure, a także istniejącymi narzędziami. W oparciu o podstawę Azure Logic Apps rozwiązanie do automatyzacji i organizowania wskaźnikowego platformy Azure zapewnia wysoce rozszerzalną architekturę, która umożliwia skalowalną automatyzację w miarę pojawiania się nowych technologii i zagrożeń. Aby kompilować elementy PlayBook z Azure Logic Apps, możesz wybrać pozycję z galerii wbudowanych elementy PlayBook. Obejmują one [200 łączników](https://docs.microsoft.com/azure/connectors/apis-list) dla usług takich jak Azure Functions. Łączniki umożliwiają zastosowanie dowolnej logiki niestandardowej w kodzie, usługi ServiceNow, JIRA, systemu Zendesk, żądaniach HTTP, Microsoft Teams, zapasowych, Windows Defender ATP i Cloud App Security.

Na przykład w przypadku korzystania z systemu biletów usługi ServiceNow można użyć narzędzi dostępnych Azure Logic Apps do automatyzacji przepływów pracy i otworzyć bilet w usługi ServiceNow za każdym razem, gdy zostanie wykryte określone zdarzenie.

![Podręczniki](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Badanie

Zaawansowane narzędzia do [badania](tutorial-investigate-cases.md) wskaźnikowego platformy Azure ułatwiają zrozumienie zakresu i znalezienie głównej przyczyny potencjalnego zagrożenia bezpieczeństwa. Możesz wybrać jednostkę na interaktywnym wykresie, aby zadać interesujące pytania dotyczące określonej jednostki, i przejść do tej jednostki i jej połączeń, aby uzyskać dostęp do głównej przyczyny zagrożenia. 

![Badanie](./media/overview/investigation.png)


## <a name="hunting"></a>Wyszukiwanie zagrożeń

Korzystaj z [zaawansowanych narzędzi do wyszukiwania i wykonywania zapytań](hunting.md)na platformie Azure, opartych na strukturze Mitre, która umożliwia proaktywne wyszukiwanie zagrożeń bezpieczeństwa w źródłach danych organizacji przed wyzwoleniem alertu. Po ustaleniu, które zapytanie polowania zawiera szczegółowe informacje na temat możliwych ataków, można także utworzyć niestandardowe reguły wykrywania na podstawie zapytania, a następnie przedstawić te informacje jako alerty w odniesieniu do odpowiedzi na zdarzenia zabezpieczeń. Podczas polowania można tworzyć zakładki dla interesujących zdarzeń, co pozwala na powrót do nich później, udostępnianie ich innym osobom oraz grupowanie ich przy użyciu innych zdarzeń skorelowanych w celu utworzenia atrakcyjnych przypadków na potrzeby badania.

![Wyszukiwanie zagrożeń](./media/overview/hunting.png)

## <a name="community"></a>Społeczność

Społeczność wskaźnikowa Azure jest zaawansowanym zasobem do wykrywania zagrożeń i automatyzacji. Nasi analitycy zabezpieczeń firmy Microsoft stale tworzą i dodają nowe pulpity nawigacyjne, elementy PlayBook, zapytania łowieckie i inne elementy, które mogą być używane w danym środowisku. Możesz pobrać przykładową zawartość z [repozytorium](https://aka.ms/asicommunity) prywatnej społeczności GitHub, aby utworzyć niestandardowe pulpity nawigacyjne, zapytania polowające, Notesy i elementy PlayBook dla platformy Azure. 

![Społeczność](./media/overview/community.png)

## <a name="next-steps"></a>Kolejne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
