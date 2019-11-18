---
title: Wyświetlanie dostawców usług i zarządzanie nimi
description: Klienci mogą używać strony dostawcy usług w Azure Portal do wyświetlania informacji o dostawcach usług, ofertach dostawcy usług i delegowanych zasobach.
ms.date: 11/15/2019
ms.topic: overview
ms.openlocfilehash: baa5a77b481779ab511ff473aacda5d40e0d771c
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132182"
---
# <a name="view-and-manage-service-providers"></a>Wyświetlanie dostawców usług i zarządzanie nimi

Klienci mogą używać strony **dostawcy usług** w [Azure Portal](https://portal.azure.com) , aby wyświetlić informacje o dostawcach usług i ofertach dostawcy usług, delegować określone zasoby za pomocą [usługi Azure delegowane zarządzanie zasobami](../concepts/azure-delegated-resource-management.md)i kupować dodatkowe oferty dostawców usług. Mimo że będziemy odnieść się do dostawców usług i klientów w tym miejscu, przedsiębiorstwa zarządzające wieloma dzierżawcami mogą używać tego samego procesu do konsolidacji ich środowiska zarządzania.

Aby uzyskać dostęp do strony **dostawcy usług** w Azure Portal, klient może wybrać **wszystkie usługi**, a następnie wyszukać **dostawców usług** i wybrać ją. Można je również znaleźć, wprowadzając "dostawcy usług" w polu wyszukiwania w górnej części Azure Portal.

Należy pamiętać, że na stronie **dostawcy usług** są wyświetlane tylko informacje o dostawcach usług, które mają dostęp do subskrypcji lub grup zasobów klienta za pomocą delegowanego zarządzania zasobami platformy Azure. Jeśli klient współpracuje z dodatkowymi dostawcami usług, którzy nie korzystają z zarządzania zasobami delegowanymi przez platformę Azure w celu uzyskania dostępu do zasobów klienta, informacje o tych dostawcach usług nie są wyświetlane w tym miejscu.

> [!NOTE]
> Dostawcy usług mogą przeglądać informacje o swoich klientach, przechodząc do **moich klientów** w Azure Portal. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i zarządzanie klientami oraz delegowanymi zasobami](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Wyświetl szczegóły dostawcy usług

Aby wyświetlić informacje o dostawcach usług, z którymi pracuje klient, można wybrać **oferty dostawcy** po lewej stronie strony **dostawcy usług** .

Dla każdej oferty dostawcy usług klient zobaczy nazwę dostawcy usług i skojarzoną z nim ofertę, a także nazwę wprowadzoną przez klienta podczas procesu dołączania.

W kolumnie **delegacje** klient widzi, ile subskrypcji i/lub grup zasobów zostało delegowanych do dostawcy usług dla tej oferty. Dostawca usług będzie mógł uzyskiwać dostęp do tych subskrypcji i/lub grup zasobów oraz nimi zarządzać zgodnie z poziomami dostępu określonymi w ofercie.

## <a name="delegate-resources"></a>Delegowanie zasobów

Zanim dostawca usług będzie mógł uzyskać dostęp do zasobów klienta i zarządzać nim, muszą być delegowane. Jeśli klient zaakceptuje ofertę, ale nie oddelegowano jeszcze żadnych zasobów, zobaczy uwagę w górnej części sekcji **oferty dostawcy** . Pozwala to klientowi na uzyskanie dostępu do dowolnych zasobów klienta, którzy muszą podjąć odpowiednie działania.

Aby delegować subskrypcje lub grupy zasobów:

1. Zaznacz pole wyboru dla wiersza zawierającego dostawcę usług, ofertę i nazwę. Następnie wybierz pozycję **Deleguj zasoby** w górnej części ekranu.
1. W sekcji **szczegóły oferty** na stronie **delegowanie zasobów** Przejrzyj szczegóły dotyczące dostawcy usług i oferty. Aby przejrzeć przypisania ról dla oferty, wybierz **pozycję kliknij tutaj, aby wyświetlić szczegóły wybranej oferty**.
1. W sekcji **Delegat** wybierz opcję **Deleguj subskrypcje** lub **delegowanie grup zasobów**.
1. Wybierz subskrypcje i/lub grupy zasobów, które chcesz delegować dla tej oferty, a następnie wybierz pozycję **Dodaj**.
1. Zaznacz pole wyboru u dołu strony, aby potwierdzić, że chcesz udzielić temu dostawcy usług dostępu do wybranych zasobów, a następnie wybierz opcję **Delegat**.

## <a name="add-or-remove-service-provider-offers"></a>Dodaj lub Usuń oferty dostawcy usług

Klient może dodać nową ofertę dostawcy usług na stronie **oferty dostawcy** , wybierając pozycję **Dodaj ofertę**. Dostawca usług musi mieć opublikowaną ofertę dla tego klienta. Klient może następnie wybrać tę ofertę na ekranie **oferty prywatne** , a następnie wybrać pozycję **Utwórz**.

Jeśli klient chce usunąć ofertę dostawcy usług, może wybrać ikonę kosza w wierszu dla tej oferty. Po potwierdzeniu usunięcia ten dostawca usług nie będzie już miał dostępu do zasobów klienta, które były wcześniej delegowane dla tej oferty.

## <a name="update-service-provider-offers"></a>Oferty aktualizacji dostawcy usług

Po dodaniu oferty przez klienta dostawca usług może opublikować zaktualizowaną wersję tej samej oferty w witrynie Azure Marketplace. Na przykład mogą chcieć dodać nową definicję roli. Jeśli nowa wersja oferty została opublikowana, na stronie **oferty dostawcy** zostanie wyświetlona ikona "Aktualizacja" w wierszu dla tej oferty. Klient może wybrać tę ikonę, aby zobaczyć różnice między bieżącą wersją oferty a nową. 

Po przejrzeniu zmian klient może zdecydować się na zaktualizowanie nowej wersji. Po wykonaniu tych czynności autoryzacje i inne ustawienia określone w nowej wersji będą stosowane do wszystkich subskrypcji i/lub grup zasobów, które zostały delegowane dla tej oferty.

## <a name="view-delegations"></a>Wyświetl delegowania

Delegacje reprezentują przypisania ról, które przyznają uprawnienia dostawcy usług dla zasobów delegowanych przez klienta. Aby wyświetlić te informacje, wybierz pozycję **delegacje** w lewej części strony **dostawcy usług** .

Filtry w górnej części strony pozwalają sortować i grupować informacje o delegowaniu lub filtrować według określonych klientów, ofert lub słów kluczowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Lighthouse](../overview.md).
- Dowiedz się, jak dostawcy usług mogą [wyświetlać klientów i zarządzać nimi](view-manage-customers.md) , przechodząc do **moich klientów** w Azure Portal.