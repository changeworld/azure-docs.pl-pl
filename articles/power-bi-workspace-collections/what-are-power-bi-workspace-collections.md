---
title: Co to są Power BI obszaru roboczego kolekcji?
description: Usługa Power BI Embedded umożliwia integrację raportów usługi Power BI do sieci web lub aplikacji dla urządzeń przenośnych, dzięki czemu nie trzeba tworzyć niestandardowe rozwiązania.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 0bd6ba74d176ed1db1e8f1f1b38646182da2c379
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="what-are-power-bi-workspace-collections"></a>Co to są Power BI obszaru roboczego kolekcji?

Z **Power BI obszaru roboczego kolekcje**, można zintegrować raportów usługi Power BI bezpośrednio z sieci web lub aplikacji dla urządzeń przenośnych.

![Diagram aplikacji](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Power BI obszaru roboczego kolekcje są **usługi Azure** niezależnym dostawcom oprogramowania i deweloperom aplikacji na powierzchni środowiska danych usługi Power BI w swoich aplikacjach. Deweloper zostały już utworzone w aplikacji, a te aplikacje mają własne użytkowników i zestawem funkcji. Te aplikacje może się również zdarzyć mają niektóre elementy wbudowane danych, takich jak wykresy i raporty, które można teraz wyłączyć przez Microsoft Power BI obszaru roboczego kolekcje. Nie potrzebujesz konta usługi Power BI, aby móc korzystać z aplikacji. Możesz zalogować się do aplikacji, tak jak wcześniej i wyświetlić i korzystać z usługi Power BI reporting obsługi bez konieczności wprowadzania dodatkowych licencji.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licencji Microsoft Power BI roboczym kolekcji

W **Microsoft Power BI obszaru roboczego kolekcje** model zastosowania, licencjonowania dla usługi Power BI nie jest odpowiedzialny za użytkownika końcowego.  Zamiast tego **sesji** zakupionych przez dewelopera aplikacji, która zużywa wizualnych i zostają zobowiązane do subskrypcji, która jest właścicielem tych zasobów. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Microsoft Power BI obszaru roboczego kolekcje model koncepcyjny

![Przepływ aplikacji z kolekcjami obszaru roboczego](media/what-are-power-bi-workspace-collections/model.png)

Podobnie jak inne usługi na platformie Azure, zasobów dla kolekcji programu Power BI obszaru roboczego za pośrednictwem [interfejsów API usługi Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). W tym przypadku jest zasobem, który należy **kolekcji obszarów roboczych usługi Power BI**.

## <a name="workspace-collection"></a>Kolekcja obszarów roboczych

A **kolekcji obszarów roboczych** jest kontenerem najwyższego poziomu platformy Azure dla zasobów zawierający 0 lub więcej **obszarów roboczych**.  A **obszaru roboczego** **kolekcji** zawiera wszystkie standardowe właściwości platformy Azure, a także następujące:

* **Klucze dostępu** — kluczy używanych podczas bezpiecznego wywoływania interfejsów API Power BI (opisane w dalszej części artykułu).
* **Użytkownicy** — użytkowników usługi Azure Active Directory (AAD), które mają uprawnienia administratora do zarządzania kolekcji obszarów roboczych Power BI, za pośrednictwem portalu Azure lub interfejsu API usługi Azure Resource Manager.
* **Region** — w ramach udostępniania **kolekcji obszarów roboczych**, można wybrać region na potrzeby aprowizacji w. Aby uzyskać więcej informacji, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Obszar roboczy

A **obszaru roboczego** jest kontenerem usługi Power BI, zawartości, który może obejmować zestawów danych i raportów. A **obszaru roboczego** jest pusta w momencie utworzenia. Będziesz tworzyć zawartości przy użyciu Power BI Desktop i będzie programowe wdrażanie pliku PBIX do obszaru roboczego przy użyciu [API Import usługi Power BI](https://msdn.microsoft.com/library/mt711504.aspx). Można również programowane Tworzenie zestawu danych i następnie tworzenia raportów w aplikacji zamiast Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Przy użyciu obszaru roboczego kolekcje i obszary robocze

**Obszar roboczy kolekcje** i **obszarów roboczych** są kontenerami zawartości, które są używane i organizowane w niezależnie od sposobu najlepiej pasuje do projektu tworzenia aplikacji. Będzie wiele różnych sposobów, że można rozmieścić ich zawartości. Można umieścić całą zawartość w obrębie jednego obszaru roboczego, a następnie później użyć do dalszego podziału zawartości między klientami tokenów aplikacji. Możesz również umieścić wszystkich klientów w oddzielnych obszarów roboczych tak, aby pewne rozdzielenie. Alternatywnie można wybrać do organizowania użytkowników według regionu, a nie przez klienta. Ten elastyczny projekt można wybrać najlepszy sposób organizowania zawartości.

## <a name="cached-datasets"></a>Zestawy danych w pamięci podręcznej

Można pamięci podręcznej zestawów danych.  Jednak nie można odświeżyć danych z pamięci podręcznej, gdy został załadowany do **Microsoft Power BI obszaru roboczego kolekcje**. Zestaw danych pamięci podręcznej oznacza, że dane zostały zaimportowane do programu Power BI Desktop, zamiast korzystać z zapytania bezpośredniego.

## <a name="authentication-and-authorization-with-app-tokens"></a>Uwierzytelnianie i autoryzacja z tokenami aplikacji

**Microsoft Power BI obszaru roboczego kolekcje** różni się do aplikacji do wykonywania wszystkich użytkowników na potrzeby uwierzytelniania i autoryzacji. Nie jest wymagane jawne że użytkownicy końcowi mogą być klienci usługi Azure Active Directory (Azure AD).  Zamiast tego aplikacja wyraża do **Microsoft Power BI obszaru roboczego kolekcje** autoryzacji do renderowania raportu usługi Power BI za pomocą **tokeny uwierzytelniania aplikacji (tokenów aplikacji)**.  Te **tokenów aplikacji** są tworzone zgodnie z potrzebami, gdy aplikacja chce renderowania raportu.

![Diagram tokenu użycia aplikacji](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Tokeny uwierzytelniania aplikacji (tokenów aplikacji)** są używane do uwierzytelniania względem **Microsoft Power BI obszaru roboczego kolekcje**.  Istnieją trzy typy **tokenów aplikacji**:

1. Inicjowanie obsługi tokenów — używane podczas inicjowania obsługi administracyjnej nowej **obszaru roboczego** do **kolekcji obszarów roboczych**
2. Programowanie tokenów — używane podczas wykonywania wywołań bezpośrednio do **API REST usługi Power BI**
3. Osadzanie tokenów — używane podczas wykonywania wywołań do renderowania raportu w osadzonych iframe

Tokeny te służą do różnych faz interakcji z **Microsoft Power BI obszaru roboczego kolekcje**.  Tokeny są zaprojektowane tak, aby delegować uprawnienia z aplikacji do usługi Power BI. Aby uzyskać więcej informacji, zobacz [App Token Flow](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Tworzyć i edytować raporty w aplikacji

Można teraz edytować istniejące raporty lub tworzenia nowych raportów bezpośrednio w aplikacji bez konieczności używania Power BI Desktop. Wymaga to, czy zestaw danych istnieją w obszarze roboczym.

## <a name="see-also"></a>Zobacz także

[Typowe scenariusze Microsoft Power BI obszaru roboczego kolekcje](scenarios.md)  
[Wprowadzenie do programu Microsoft Power BI obszaru roboczego kolekcje](get-started.md)  
[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Usługa Power BI CSharp repozytorium Git](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git węzła usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)
