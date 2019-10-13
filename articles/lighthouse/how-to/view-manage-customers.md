---
title: Wyświetlanie klientów i delegowanych zasobów oraz zarządzanie nimi
description: Jako dostawca usług korzystający z funkcji zarządzania zasobami delegowanymi przez platformę Azure możesz wyświetlić wszystkie delegowane zasoby i subskrypcje klientów, przechodząc do moich klientów w Azure Portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 8163bcb4189cbec545544b7d43ba7142d34e29dd
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286158"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Wyświetlanie klientów i delegowanych zasobów oraz zarządzanie nimi

Dostawcy usług korzystający z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](../concepts/azure-delegated-resource-management.md) mogą używać strony **moi klienci** w [Azure Portal](https://portal.azure.com) , aby wyświetlić delegowane zasoby i subskrypcje klientów. Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Aby uzyskać dostęp do strony **moi klienci** w Azure Portal, wybierz pozycję **wszystkie usługi**, a następnie wyszukaj pozycję **moi klienci** i wybierz ją. Można ją również znaleźć, wpisując "My Customers" w polu wyszukiwania w górnej części Azure Portal.

Należy pamiętać, że na stronie **My Customers** są wyświetlane tylko informacje o klientach, którzy mają delegowane subskrypcje lub grupy zasobów. Jeśli pracujesz z innymi klientami (na przykład za pomocą [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview)), nie zobaczysz tutaj informacji o tych klientach, chyba że połączysz swoje zasoby w celu zarządzania delegowanymi zasobami.

> [!NOTE]
> Klienci mogą wyświetlać informacje o dostawcach usług, przechodząc do **dostawców usług** w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie dostawcami usług](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Wyświetlanie szczegółów klienta i zarządzanie nimi

Aby wyświetlić szczegóły klienta, wybierz pozycję **klienci** po lewej stronie strony **moi klienci** .

Dla każdego klienta zobaczysz nazwę klienta, identyfikator klienta (identyfikator dzierżawy) i ofertę skojarzoną z zaangażowaniem. W kolumnie **delegacje** zostanie wyświetlona liczba delegowanych subskrypcji i/lub liczba delegowanych grup zasobów.

Filtry w górnej części strony pozwalają sortować i grupować informacje o kliencie lub filtrować według określonych klientów, ofert lub słów kluczowych.

Na tej stronie można wyświetlić następujące informacje:

- Aby wyświetlić wszystkie subskrypcje, oferty i delegacje skojarzone z klientem, wybierz nazwę klienta.
- Aby wyświetlić więcej szczegółów na temat oferty i jej delegowania, wybierz nazwę oferty.
- Aby wyświetlić więcej szczegółów na temat przypisań acrolecess dla delegowanych subskrypcji lub grup zasobów, wybierz wpis w kolumnie **delegacje** .

## <a name="view-delegations"></a>Wyświetl delegowania

Delegacje pokazują delegowaną subskrypcję/grupę zasobów wraz z użytkownikami i uprawnieniami, które mają do niej dostęp. Aby wyświetlić te informacje, wybierz opcję **delegacje** po lewej stronie strony **moi klienci** .

Filtry w górnej części strony pozwalają sortować i grupować informacje o przypisaniu dostępu lub filtrować według określonych klientów, ofert lub słów kluczowych.

Użytkownicy i uprawnienia skojarzone z każdym delegowaniem są wyświetlane w kolumnie **przypisania ról** . Można wybrać każdy wpis, aby wyświetlić pełną listę użytkowników, grup i jednostek usługi, którym udzielono dostępu do subskrypcji lub grupy zasobów. W tym miejscu możesz wybrać określonego użytkownika, grupę lub nazwę główną usługi, aby uzyskać więcej szczegółów.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Pracuj w kontekście delegowanej subskrypcji

Możesz pracować bezpośrednio w kontekście delegowanej subskrypcji w ramach Azure Portal bez przełączania katalogu, w którym pracujesz. W tym celu:

1. Wybierz ikonę **katalogu i subskrypcji** w górnej części Azure Portal.
2. W **globalnym** filtrze subskrypcji upewnij się, że wybrano tylko pole dla tej delegowanej subskrypcji. Za pomocą pola listy rozwijanej **bieżące i delegowane katalogi** można wyświetlić tylko subskrypcje w określonym katalogu. (Nie należy używać opcji **Switch Directory** , ponieważ zmienia katalog, z którym użytkownik jest zalogowany).

Jeśli następnie uzyskasz dostęp do usługi, która obsługuje [środowiska zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md), usługa będzie domyślnie w kontekście wybranej subskrypcji delegowanej. Możesz to zmienić, wykonując powyższe kroki i zaznaczając pole **wyboru Zaznacz wszystko** (lub wybierając co najmniej jedną subskrypcję, w której ma zostać zadziałała).

> [!NOTE]
> Jeśli udzielono dostępu do co najmniej jednej grupy zasobów, a nie dostęp do całej subskrypcji, możesz wybrać subskrypcję, do której należy ta grupa zasobów. Następnie będziesz działać w kontekście tej subskrypcji, ale będzie można uzyskać dostęp tylko do określonych grup zasobów.

Możesz również uzyskać dostęp do funkcji związanych z delegowanymi subskrypcjami lub grupami zasobów z poziomu usług, które obsługują obsługę wielu dzierżawców, wybierając subskrypcję lub grupę zasobów z tej usługi.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [środowisk zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- Dowiedz się, w jaki sposób klienci mogą [wyświetlać dostawców usług i zarządzać nimi](view-manage-service-providers.md) , przechodząc do **dostawców usług** w Azure Portal.
