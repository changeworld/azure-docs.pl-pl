---
title: Wyświetlanie i zarządzanie nimi, klientów i delegowanego zasoby w witrynie Azure portal
description: Jako dostawcę usług korzystających z platformy Azure delegowane zarządzanie zasobami, możesz wyświetlać wszystkie zasoby dla delegowanego klientów i subskrypcji, przechodząc do moich klientów w witrynie Azure portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809855"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Wyświetlanie i zarządzanie nimi, klientów i delegowanego zasoby

Dostawcy usług przy użyciu [Azure delegowane zarządzanie zasobami](../concepts/azure-delegated-resource-management.md) służy **moich klientów** strony w [witryny Azure portal](https://portal.azure.com) do wyświetlania zasobów klienta delegowanego i Subskrypcje. Gdy będziemy odnosić się do dostawców usług i klientów w tym miejscu, przedsiębiorstw Zarządzanie wieloma dzierżawcami umożliwia ten sam proces konsolidować swoje środowisko zarządzania.

Aby uzyskać dostęp do **moich klientów** strony w witrynie Azure portal, wybierz opcję **wszystkich usług**, a następnie wyszukaj **moich klientów** i wybierz ją. Można również znaleźć, wprowadzając "Klienci" w polu wyszukiwania w górnej części witryny Azure portal.

Należy pamiętać, że **moich klientów** strona zawiera tylko informacje na temat klientów, którzy mają delegowane grup zasobów lub subskrypcji. Jeśli pracujesz z innych klientów (takich jak za pomocą [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), nie będziesz widzieć informacje na temat tych klientów w tym miejscu, o ile nie można dołączyć ich zasobów dla delegowanego zarządzania zasobami.

> [!NOTE]
> Klienci mogą wyświetlać informacje na temat dostawców usług, przechodząc do **dostawców usług** w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [wyświetlanie i zarządzanie dostawcami usług](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Wyświetlanie i zarządzanie nimi szczegóły klienta

Zaznacz, aby wyświetlić szczegóły klienta **klientów** w lewej części **moich klientów** strony.

Dla każdego klienta zostaną wyświetlone nazwy klienta, identyfikator klienta (identyfikator dzierżawy) i oferty skojarzone z asysty. W **delegacji** kolumnie zobaczysz liczbę subskrypcji delegowanego i/lub liczbę grup zasobu delegowanego.

Filtry w górnej części strony umożliwiają Sortuj i Grupuj swoje informacje o kliencie lub Filtruj według określonych odbiorców, oferty i słów kluczowych.

Można wyświetlić następujące informacje z tej strony:

- Aby wyświetlić wszystkie subskrypcje, oferty i delegacji skojarzonych z klientem, wybierz nazwę klienta.
- Aby wyświetlić więcej szczegółów na temat oferty i jej delegacji, wybierz nazwę oferty.
- Aby wyświetlić więcej szczegółów na temat przypisania acrolecess dla delegowanego subskrypcji lub grupy zasobów, wybierz wpis w **delegacji** kolumny.

## <a name="view-delegations"></a>Wyświetl delegacji

Delegowanie umożliwia wyświetlenie subskrypcji/grupy zasobów, delegowania wraz z użytkownikami i uprawnieniami, które mają dostęp do niego. Aby wyświetlić te informacje, wybierz **delegacji** w lewej części **moich klientów** strony.

Filtry w górnej części strony umożliwiają Sortuj i Grupuj swoje informacje dotyczące przypisywania dostępu lub Filtruj według określonych odbiorców, oferty i słów kluczowych.

Użytkownicy i skojarzone z każdym delegowania uprawnień są wyświetlane w **przypisań ról** kolumny. Możesz wybrać każdego wpisu, aby wyświetlić pełną listę użytkowników, grup i nazwy główne usług, które ma zostać przyznany dostęp do subskrypcji lub grupy zasobów. Z tego miejsca można wybrać określonego użytkownika, grupy lub główną nazwę usługi, aby uzyskać więcej szczegółów.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Działa w kontekście delegowanego subskrypcji

Możesz pracować bezpośrednio w kontekście delegowanego subskrypcji w witrynie Azure portal, bez przełączania katalogu w którym pracujesz w. Aby to zrobić:

1. Wybierz **katalog i Subskrypcja** ikony w górnej części witryny Azure portal.
2. W **globalny subskrypcji** filtrów, upewnij się, że tylko pole dla delegowanego subskrypcji jest zaznaczone. Możesz użyć **bieżącego + delegowanego katalogi** pole listy rozwijanej, aby pokazać tylko subskrypcje w ramach określonego katalogu. (Nie używaj **Przełącz katalog** opcja, ponieważ zmienia się katalogu, do którego zalogowano Cię.)

Jeśli następnie uzyskiwanie dostępu do usługi, która obsługuje [międzydzierżawowa środowiska zarządzania](../concepts/cross-tenant-management-experience.md), usługi będą domyślnie do kontekstu delegowanego Wybrana subskrypcja. Możesz to zmienić, wykonując kroki opisane powyżej i sprawdzanie, czy **Zaznacz wszystko** pole (lub wybierając co najmniej jedną subskrypcję, aby pracować w zamian).

> [!NOTE]
> Jeśli musisz mieć przyznany dostęp do jednego lub więcej grup zasobów, a nie dostęp do całej subskrypcji, możesz wybrać subskrypcję, do której należy tej grupie zasobów. Następnie będziesz pracować w ramach tej subskrypcji, ale tylko wtedy będzie można uzyskać dostępu do grup zasobów wyznaczonym.

Dostępne funkcje związane z delegowanego subskrypcji lub grupy zasobów z w ramach usług, które obsługują środowiska zarządzania międzydzierżawowa, wybierając subskrypcji lub grupy zasobów z w ramach tej usługi.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](../concepts/cross-tenant-management-experience.md).
- Dowiedz się, jak klienci mogą [wyświetlanie i zarządzanie nimi usługodawców](view-manage-service-providers.md) , przechodząc do **dostawców usług** w witrynie Azure portal.
