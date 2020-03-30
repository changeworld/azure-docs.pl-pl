---
title: Wyświetlanie dostawców usług i zarządzanie nimi
description: Klienci mogą korzystać ze strony Dostawcy usług w witrynie Azure portal, aby wyświetlić informacje o dostawcach usług, ofertach dostawców usług i delegowanych zasobach.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270630"
---
# <a name="view-and-manage-service-providers"></a>Wyświetlanie dostawców usług i zarządzanie nimi

Klienci mogą korzystać ze strony **Dostawcy usług** w [witrynie Azure portal,](https://portal.azure.com) aby wyświetlić informacje o dostawcach usług i ofertach dostawców usług, delegować określone zasoby za pośrednictwem [usługi Azure do zarządzania zasobami delegowanymi](../concepts/azure-delegated-resource-management.md)oraz kupować nowe oferty dostawców usług. W tym miejscu będziemy odnosić się do dostawców usług i klientów, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji środowiska zarządzania.

Aby uzyskać dostęp do **strony Dostawcy usług** w witrynie Azure portal, klient może wybrać wszystkie **usługi,** a następnie wyszukać **dostawców usług** i wybrać go. Można go również znaleźć, wprowadzając "Usługodawcy" w polu wyszukiwania w górnej części witryny Azure portal.

Należy pamiętać, że **dostawcy usług** strona zawiera tylko informacje o dostawcach usług, którzy mają dostęp do subskrypcji klienta lub grup zasobów za pośrednictwem usługi Azure delegowanego zarządzania zasobami. Jeśli klient współpracuje z dostawcami usług dodatkowych, którzy nie używają zarządzania zasobami delegowanymi platformy Azure w celu uzyskania dostępu do zasobów klienta, informacje o tych dostawcach usług nie są wyświetlane w tym miejscu.

> [!NOTE]
> Dostawcy usług mogą wyświetlać informacje o swoich klientach, przechodząc do **moich klientów** w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie klientów i zasobów delegowanych oraz zarządzanie nimi](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Wyświetl szczegóły usługodawcy

Aby wyświetlić informacje o dostawcach usług, klient może wybrać **oferty dostawcy** po lewej stronie strony **Dostawcy usług.**

W przypadku każdej oferty usługodawcy klient zobaczy nazwę usługodawcy i powiązaną z nim ofertę wraz z nazwą, którą klient wprowadził podczas procesu dołączania.

W **delegowania** kolumny klient widzi, ile subskrypcji i/lub grup zasobów zostały delegowane do dostawcy usług dla tej oferty. Dostawca usług będzie mógł uzyskiwać dostęp do tych subskrypcji i/lub grup zasobów i zarządzać nimi zgodnie z poziomami dostępu określonymi w ofercie.

## <a name="delegate-resources"></a>Delegowanie zasobów

Aby dostawca usług mógł uzyskiwać dostęp do zasobów klienta i zarządzać nimi, należy je delegować. Jeśli klient zaakceptował ofertę, ale nie przekazał jeszcze żadnych zasobów, zobaczy notatkę w górnej części sekcji **Oferty dostawcy.** Dzięki temu klient wie, że musi podjąć działania, zanim dostawca usług będzie mógł uzyskać dostęp do zasobów klienta.

Aby delegować subskrypcje lub grupy zasobów:

1. Zaznacz pole wyboru wiersza zawierającego usługodawcę, ofertę i nazwę. Następnie wybierz **pozycję Deluj zasoby** u góry ekranu.
1. W sekcji **Szczegóły oferty** na stronie **Delegowanie zasobów** przejrzyj szczegółowe informacje o usługodawcy i ofercie. Aby przejrzeć przypisania ról dla oferty, wybierz pozycję **Kliknij tutaj, aby wyświetlić szczegóły wybranej oferty**.
1. W sekcji **Delegowanie** wybierz pozycję **Deluj subskrypcje** lub **Deluj grupy zasobów**.
1. Wybierz subskrypcje i/lub grupy zasobów, które chcesz delegować do tej oferty, a następnie wybierz pozycję **Dodaj**.
1. Zaznacz pole wyboru u dołu strony, aby potwierdzić, że chcesz udzielić temu dostawcy usług dostępu do wybranych zasobów, a następnie wybierz pozycję **Deluj**.

## <a name="add-or-remove-service-provider-offers"></a>Dodawanie lub usuwanie ofert usługodawców

Klient może dodać nową ofertę dostawcy usług ze strony **Dostawca,** wybierając **pozycję Dodaj ofertę**. Usługodawca musi opublikować ofertę dla tego klienta. Klient może następnie wybrać tę ofertę na ekranie **Oferty prywatne,** a następnie wybrać pozycję **Utwórz**.

Jeśli klient chce usunąć ofertę dostawcy usług, może wybrać ikonę kosza w wierszu dla tej oferty. Po potwierdzeniu usunięcia ten dostawca usług nie będzie już miał dostępu do zasobów klienta, które wcześniej były delegowane do tej oferty.

## <a name="update-service-provider-offers"></a>Oferty dostawcy usług aktualizacji

Po dodaniu oferty przez klienta dostawca usług może opublikować zaktualizowaną wersję tej samej oferty w portalu Azure Marketplace. Na przykład mogą chcieć dodać nową definicję roli. Jeśli nowa wersja oferty została opublikowana, **dostawca oferuje** stronę wyświetli ikonę "aktualizacja" w wierszu dla tej oferty. Klient może wybrać tę ikonę, aby zobaczyć różnice między bieżącą wersją oferty a nową.

 ![Ikona aktualizuj ofertę](../media/update-offer.jpg)

Po zapoznaniu się ze zmianami klient może zaktualizować do nowej wersji. Gdy to zrobią, autoryzacje i inne ustawienia określone w nowej wersji będą miały zastosowanie do wszystkich subskrypcji i/lub grup zasobów, które zostały delegowane dla tej oferty.

## <a name="view-delegations"></a>Wyświetlanie delegacji

Delegacje reprezentują przypisania ról, które udzielają uprawnień dostawcy usług dla zasobów delegowanych przez klienta. Aby wyświetlić te informacje, wybierz **przedstawicielstwa** po lewej stronie **strony Dostawcy usług.**

Filtry u góry strony umożliwiają sortowanie i grupowanie informacji o delegowaniu. Możesz też filtrować według konkretnych klientów, ofert lub słów kluczowych.

> [!NOTE]
> Klienci nie zobaczą tych przypisań ról ani żadnych użytkowników z dzierżawy dostawcy usług, którym przyznano te role, podczas [wyświetlania informacji o przypisywaniu ról dla zakresu delegowanego w witrynie Azure portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) lub za pośrednictwem interfejsów API.

## <a name="audit-delegations-in-your-environment"></a>Inspekcja delegacji w twoim środowisku

Klienci mogą chcieć uzyskać wgląd w subskrypcje i/lub grupy zasobów, które zostały delegowane do dostawców usług do [zarządzania zasobami delegowanymi platformy Azure](../concepts/azure-delegated-resource-management.md). Jest to szczególnie przydatne dla tych klientów z dużą liczbą subskrypcji lub którzy mają wielu użytkowników, którzy wykonują zadania zarządzania.

Zapewniamy [wbudowaną definicję zasad azure policy](../../governance/policy/samples/built-in-policies.md#lighthouse) do inspekcji delegowania zakresów do dzierżawy zarządzającej. Tę zasadę można przypisać do grupy zarządzania, która zawiera wszystkie subskrypcje, które mają zostać poddane inspekcji. Podczas sprawdzania zgodności z tymi zasadami wszelkie delegowane subskrypcje i/lub grupy zasobów (w ramach grupy zarządzania, do której przypisano zasady) będą wyświetlane w stanie niezgodnym. Następnie można przejrzeć wyniki i potwierdzić, że nie ma nieoczekiwanych delegacji.

Aby uzyskać więcej informacji na temat przypisywania zasad i wyświetlania wyników stanu zgodności, zobacz [Szybki start: Tworzenie przypisania zasad](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Następne kroki
 
- Dowiedz się więcej o [latarni morskiej platformy Azure](../overview.md).
- Dowiedz się, jak dostawcy usług mogą [wyświetlać klientów i zarządzać nimi,](view-manage-customers.md) przechodząc do **witryny Moi klienci** w witrynie Azure portal.