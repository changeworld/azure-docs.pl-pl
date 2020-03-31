---
title: View and manage customers and delegated resources (Wyświetlanie klientów i zasobów delegowanych oraz zarządzanie nimi)
description: Jako dostawca usług korzystający z zarządzania zasobami delegowanymi platformy Azure możesz wyświetlić wszystkie delegowane zasoby i subskrypcje klientów, przechodząc do moich klientów w witrynie Azure portal.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543430"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>View and manage customers and delegated resources (Wyświetlanie klientów i zasobów delegowanych oraz zarządzanie nimi)

Dostawcy usług korzystający z [usługi Azure zarządzania zasobami delegowanymi](../concepts/azure-delegated-resource-management.md) mogą używać strony **Moi klienci** w [witrynie Azure portal](https://portal.azure.com) do wyświetlania delegowanych zasobów i subskrypcji klientów. W tym miejscu będziemy odnosić się do dostawców usług i klientów, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji środowiska zarządzania.

Aby uzyskać dostęp do strony **Moi klienci** w witrynie Azure portal, wybierz pozycję **Wszystkie usługi**, a następnie wyszukaj pozycję **Moi klienci** i wybierz ją. Można go również znaleźć, wprowadzając "Moi klienci" w polu wyszukiwania w górnej części witryny Azure portal.

Należy pamiętać, że w sekcji **Najlepsi klienci** na stronie **Moi klienci** są wyświetlane tylko informacje o klientach, którzy mają delegowane subskrypcje lub grupy zasobów. Jeśli pracujesz z innymi klientami (na przykład za pośrednictwem [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), nie zobaczysz informacji o tych klientach w sekcji **Klienci,** chyba że masz wbudowane ich zasoby do zarządzania zasobami delegowanymi platformy Azure.

Poniżej na stronie osobna sekcja o nazwie **Cloud Solution Provider (Preview)** zawiera informacje rozliczeniowe i zasoby dla klientów dostawcy usług kryptograficznych, którzy [podpisali umowę klienta firmy Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) i są [w ramach planu platformy Azure](https://docs.microsoft.com/partner-center/azure-plan-get-started). Aby uzyskać więcej informacji, zobacz [Wprowadzenie do konta rozliczeniowego umowy z partnerem firmy Microsoft](../../billing/mpa-overview.md). Należy zauważyć, że tacy klienci dostawcy usług kryptograficznych są wyświetlane w tej sekcji, czy masz również dołączane do zarządzania zasobami delegowanymi platformy Azure. Podobnie klient dostawcy usług kryptograficznych nie musi pojawiać się w sekcji Dostawca rozwiązań w **chmurze (wersja zapoznawcza)** **w witrynie Moi klienci,** aby można było ich przywdzielić do zarządzania zasobami delegowanymi platformy Azure.

> [!NOTE]
> Klienci mogą wyświetlać informacje o dostawcach usług, przechodząc do **dostawców usług** w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie dostawców usług i zarządzanie nimi](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Wyświetlanie danych klientów i zarządzanie nimi

Aby wyświetlić szczegóły klienta, wybierz **pozycję Klienci** po lewej stronie strony **Moi klienci.**

Dla każdego klienta zobaczysz nazwę klienta, identyfikator klienta (identyfikator dzierżawy) i ofertę skojarzoną z zaangażowaniem. W kolumnie **Delegacje** zobaczysz liczbę delegowanych subskrypcji i/lub liczbę delegowanych grup zasobów.

> [!IMPORTANT]
> Aby wyświetlić delegowanie, użytkownicy muszą mieć przyznane [Reader](../../role-based-access-control/built-in-roles.md#reader) roli (lub innej wbudowanej roli, która obejmuje dostęp programu Reader) w procesie dołączania.

Filtry u góry strony umożliwiają sortowanie i grupowanie informacji o klientach lub filtrowanie według konkretnych klientów, ofert lub słów kluczowych.

Na tej stronie można wyświetlić następujące informacje:

- Aby wyświetlić wszystkie subskrypcje, oferty i delegacje skojarzone z klientem, wybierz nazwę klienta.
- Aby wyświetlić więcej szczegółów na temat oferty i jej delegacji, wybierz nazwę oferty.
- Aby wyświetlić więcej szczegółów dotyczących przydziałów ról dla delegowanych subskrypcji lub grup zasobów, wybierz wpis w kolumnie **Delegacje.**

## <a name="view-and-manage-delegations"></a>Wyświetlanie delegacji i zarządzanie nimi

Delegacje pokazują subskrypcję/grupę zasobów, która została delegowana, wraz z użytkownikami i uprawnieniami, które mają do niej dostęp. Aby wyświetlić te informacje, wybierz **pozycję Delegacje** po lewej stronie strony **Moi klienci.**

Filtry u góry strony umożliwiają sortowanie i grupowanie informacji o przypisaniu dostępu lub filtrowanie według określonych klientów, ofert lub słów kluczowych.

### <a name="view-role-assignments"></a>Wyświetlanie przypisań ról

Użytkownicy i uprawnienia skojarzone z każdym delegowaniem są wyświetlane w kolumnie **Przypisania ról.** Można wybrać każdy wpis, aby wyświetlić pełną listę użytkowników, grup i podmiotów usługi, którym przyznano dostęp do subskrypcji lub grupy zasobów. W tym miejscu można wybrać określoną nazwę użytkownika, grupy lub jednostki usługi, aby uzyskać więcej szczegółów.

### <a name="remove-delegations"></a>Usuwanie delegacji

Jeśli podczas dołączania klienta do zarządzania zasobami delegowanymi usługi Azure uwzględniono użytkowników z [rolą usuwania przydziału rejestracji usług zarządzanych,](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) użytkownicy ci mogą usunąć delegowanie, wybierając ikonę kosza wyświetlanego w wierszu dla tej delegacji. Gdy to zrobią, żaden użytkownik w dzierżawie dostawcy usług nie będzie mógł uzyskać dostępu do zasobów, które zostały wcześniej delegowane.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Praca w kontekście delegowanej subskrypcji

Możesz pracować bezpośrednio w kontekście delegowanej subskrypcji w witrynie Azure portal, bez przełączania katalogu, w którego pracujesz. W tym celu:

1. Wybierz ikonę **Katalog + Subskrypcja** w górnej części witryny Azure portal.
2. W filtrze **Subskrypcja globalna** upewnij się, że zaznaczone jest tylko pole dla tej delegowanej subskrypcji. Można użyć **bieżącego + delegowanego katalogu** rozwijanego, aby wyświetlić tylko subskrypcje w określonym katalogu. (Nie należy używać opcji **Przełącz katalog,** ponieważ spowoduje to zmianę katalogu, do którego użytkownik jest zalogowany).

Jeśli następnie uzyskać dostęp do usługi, która obsługuje [między dzierżawy środowiska zarządzania,](../concepts/cross-tenant-management-experience.md)usługa będzie domyślnie w kontekście delegowanej subskrypcji, który został wybrany. Można to zmienić, wykonując powyższe czynności i zaznaczając pole **Zaznacz wszystkie** (lub wybierając jedną lub więcej subskrypcji do pracy).

> [!NOTE]
> Jeśli przyznano dostęp do jednej lub więcej grup zasobów, a nie dostęp do całej subskrypcji, można wybrać subskrypcję, do której należy ta grupa zasobów. Następnie będziesz działać w kontekście tej subskrypcji, ale będzie można uzyskać dostęp tylko do wyznaczonych grup zasobów.

Można również uzyskać dostęp do funkcji związanych z delegowanych subskrypcji lub grup zasobów z poziomu usług, które obsługują środowisko zarządzania między dzierżawami, wybierając subskrypcji lub grupy zasobów z tej usługi.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [środowiskach zarządzania między dzierżawcami](../concepts/cross-tenant-management-experience.md).
- Dowiedz się, jak twoi klienci mogą [wyświetlać dostawców usług i zarządzać nimi,](view-manage-service-providers.md) przechodząc do **dostawców usług** w witrynie Azure portal.
