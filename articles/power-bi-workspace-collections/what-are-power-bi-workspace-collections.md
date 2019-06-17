---
title: Co to są kolekcje obszarów roboczych usługi Power BI?
description: Power BI Embedded umożliwia integrowanie raportów usługi Power BI z aplikacjami internetowymi lub mobilnymi, dzięki czemu nie trzeba tworzyć niestandardowych rozwiązań.
services: power-bi-embedded
author: rkarlin
ROBOTS: NOINDEX
ms.assetid: 03649b72-b7d7-40ca-b077-12356d72d4f3
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: rkarlin
ms.openlocfilehash: 7f23856363b337a361f329ed54e2152842faf26e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705132"
---
# <a name="what-are-power-bi-workspace-collections"></a>Co to są kolekcje obszarów roboczych usługi Power BI?

Za pomocą **kolekcji obszarów roboczych usługi Power BI**, można zintegrować raporty usługi Power BI bezpośrednio z aplikacjami internetowymi lub mobilnymi.

![Diagram aplikacji](media/what-are-power-bi-workspace-collections/what-is.png)

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Kolekcje obszarów roboczych usługi Power BI **usługa** , który umożliwia niezależnym dostawcom oprogramowania i deweloperom aplikacji powierzchni interfejsy danych usługi Power BI w swoich aplikacjach. Jako deweloper skompilowaniu aplikacji, a te aplikacje mają własne użytkowników i odrębnym zestawem funkcji. Te aplikacje mogą również występuje niektóre elementy danych wbudowane, takie jak wykresy i raporty, które mogą być teraz wspierane przez kolekcji obszarów roboczych pakietu Microsoft Power BI. Nie potrzebujesz konta usługi Power BI w celu korzystania z aplikacji. Można kontynuować, zaloguj się do aplikacji, tak jak poprzednio, wyświetlać i wchodzić w interakcje z usługą Power BI środowiska tworzenia raportów bez żadnych dodatkowych licencji.

## <a name="licensing-for-microsoft-power-bi-workspace-collections"></a>Licencji na program Microsoft Power BI w obszarze roboczym kolekcji

W **kolekcji obszarów roboczych pakietu Microsoft Power BI** użycia modelu licencjonowania dla usługi Power BI jest odpowiedzialny za użytkownika końcowego.  Zamiast tego **sesje** można zakupić przez dewelopera aplikacji, która zużywa wizualizacje i jest obciążany opłatą za subskrypcję, która jest właścicielem tych zasobów. 

## <a name="microsoft-power-bi-workspace-collections-conceptual-model"></a>Model koncepcyjny kolekcji obszarów roboczych pakietu Microsoft Power BI

![Przepływ aplikacji za pomocą kolekcji obszarów roboczych](media/what-are-power-bi-workspace-collections/model.png)

Podobnie jak dowolnej innej usługi na platformie Azure, zasoby do kolekcji obszarów roboczych usługi Power BI są aprowizowane za pośrednictwem [interfejsów API usługi Azure Resource Manager](https://msdn.microsoft.com/library/mt712306.aspx). W tym przypadku jest zasobem, który jest aprowizowanie **kolekcji obszarów roboczych usługi Power BI**.

## <a name="workspace-collection"></a>Kolekcja obszarów roboczych

A **kolekcji obszarów roboczych** jest kontenerem najwyższego poziomu platformy Azure dla zasobów, która zawiera 0 lub więcej **obszary robocze**.  A **obszaru roboczego** **kolekcji** zawiera wszystkie standardowe właściwości platformy Azure, a także następujące czynności:

* **Klucze dostępu** — klucze używane podczas bezpiecznego wywoływania interfejsów API Power BI (opisany w dalszej części tego tematu).
* **Użytkownicy** — użytkownicy usługi Azure Active Directory (AAD), które mają uprawnienia administratora, aby zarządzać kolekcji obszarów roboczych usługi Power BI, za pośrednictwem witryny Azure portal lub interfejsu API usługi Azure Resource Manager.
* **Region** — w ramach inicjowania obsługi administracyjnej **kolekcji obszarów roboczych**, możesz wybrać region, aby zapewnić w. Aby uzyskać więcej informacji, zobacz [regionów platformy Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Obszar roboczy

A **obszaru roboczego** jest kontenerem usługi Power BI, zawartość, który obejmuje zestawy danych i raporty. A **obszaru roboczego** jest pusta, podczas pierwszego utworzenia. Będziesz tworzyć zawartość przy użyciu usługi Power BI Desktop i będzie programowe wdrażanie pliku PBIX do Twojego obszaru roboczego przy użyciu [API Import usługi Power BI](https://msdn.microsoft.com/library/mt711504.aspx). Można również utworzyć programowo zestawu danych i następnie utworzyć raporty w swojej aplikacji, zamiast korzystać z usługi Power BI Desktop.

## <a name="using-workspace-collections-and-workspaces"></a>Za pomocą kolekcji obszarów roboczych i obszary robocze

**Kolekcje obszarów roboczych** i **obszary robocze** to kontenery, treści, które są używane i organizowane w niezależnie od sposobu najlepiej pasuje do projektowania aplikacji, którą tworzysz. Będzie istnieć wiele różnych sposobów, że można rozmieścić ich zawartości. Można umieścić całej zawartości w ramach jednego obszaru roboczego, a następnie później użyć tokenów aplikacji do dalszego podziału zawartości przez klientów. Można także umieścić wszystkim klientom w oddzielnych obszarach roboczych, tak, aby istniała niektóre separacji między nimi. Lub możesz organizować użytkowników według regionu, a nie przez klienta. Ten elastyczny projekt, można wybrać najlepszy sposób organizowania zawartości.

## <a name="cached-datasets"></a>Buforowane zestawy danych

Należy używać pamięci podręcznej zestawów danych.  Jednak nie można odświeżyć dane w pamięci podręcznej po załadowaniu do **kolekcji obszarów roboczych pakietu Microsoft Power BI**. Zestaw danych w pamięci podręcznej oznacza, że dane zostały zaimportowane do programu Power BI Desktop, zamiast korzystania z zapytania bezpośredniego.

## <a name="authentication-and-authorization-with-app-tokens"></a>Uwierzytelnianie i autoryzacja przy użyciu tokenów aplikacji

**Obszar roboczy kolekcji usługi Microsoft Power BI** różni się do aplikacji w taki sposób, aby wykonać wszelkie niezbędne uwierzytelniania i autoryzacji użytkowników. Nie ma jawnej, użytkownicy końcowi można klientów usługi Azure Active Directory (Azure AD).  Zamiast tego aplikacja wyraża się **kolekcji obszarów roboczych pakietu Microsoft Power BI** autoryzacji do renderowania raportu usługi Power BI przy użyciu **tokeny uwierzytelniania aplikacji (aplikacji tokeny)** .  Te **tokenów aplikacji** są tworzone zgodnie z potrzebami, gdy aplikacja chce renderowania raportu.

![Diagram użycie tokenu aplikacji](media/what-are-power-bi-workspace-collections/app-tokens.png)

**Tokeny uwierzytelniania aplikacji (aplikacji tokeny)** są używane do uwierzytelniania względem **kolekcji obszarów roboczych pakietu Microsoft Power BI**.  Istnieją trzy typy **tokenów aplikacji**:

1. Inicjowanie obsługi administracyjnej tokenów - używanych podczas aprowizowania nowego **obszaru roboczego** do **kolekcji obszarów roboczych**
2. Tokeny rozwoju — używany podczas wywołań bezpośrednio do **interfejsów API REST usługi Power BI**
3. Tokeny osadzania — używany podczas wywołań do renderowania raportu w elemencie iframe osadzonego

Tokeny te służą do różnych faz interakcję z **kolekcji obszarów roboczych pakietu Microsoft Power BI**.  Tokeny są zaprojektowane tak, aby delegować uprawnienia z aplikacji do usługi Power BI. Aby uzyskać więcej informacji, zobacz [App Token Flow](app-token-flow.md).

## <a name="create-or-edit-reports-within-your-application"></a>Tworzenie lub edytowanie raportów w aplikacji

Można teraz edytować istniejące raporty lub tworzenia nowych raportów bezpośrednio w aplikacji bez konieczności używania programu Power BI Desktop. Wymaga to, że zestaw danych istnieją w obszarze roboczym.

## <a name="see-also"></a>Zobacz także

[Typowe scenariusze kolekcji obszarów roboczych pakietu Microsoft Power BI](scenarios.md)  
[Rozpoczynanie pracy z kolekcji obszarów roboczych pakietu Microsoft Power BI](get-started.md)  
[Rozpoczęcie pracy z przykładem](get-started-sample.md)  
[Embed a report](embed-report.md) (Osadzanie raportu)  
[Authenticating and authorizing with Power BI Workspace Collections (Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI)](app-token-flow.md)  
[Przykład osadzania skryptu JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Repozytorium Git języka CSharp usługi Power BI](https://github.com/Microsoft/PowerBI-CSharp)  
[Repozytorium Git w węźle usługi Power BI](https://github.com/Microsoft/PowerBI-Node)  

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
