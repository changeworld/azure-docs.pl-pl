---
title: Wyświetlanie i zarządzanie nimi dostawców usług w witrynie Azure portal
description: Klienci mogą używać stronę dostawców usługi w witrynie Azure portal do wyświetlania informacji o dostawcy usług, oferty dostawcy usług i zasobów delegowanego.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a45458e7417bba058522fdc0dbc34fee04ad9af8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809841"
---
# <a name="view-and-manage-service-providers"></a>Wyświetlanie i zarządzanie nimi dostawców usług

Klienci mogą używać **dostawców usług** strony w [witryny Azure portal](https://portal.azure.com) Aby wyświetlić informacje na temat dostawców usług i ofert dostawcy usług, delegowanie określonych zasobów poprzez [platformy Azure delegowane zarządzanie zasobami](../concepts/azure-delegated-resource-management.md)i wybierz oferty dostawcy usług dodatkowych. Gdy będziemy odnosić się do dostawców usług i klientów w tym miejscu, przedsiębiorstw Zarządzanie wieloma dzierżawcami umożliwia ten sam proces konsolidować swoje środowisko zarządzania.

Aby uzyskać dostęp do **dostawców usług** strony w witrynie Azure portal, klient może wybrać **wszystkich usług**, a następnie wyszukaj **dostawców usług** i wybierz ją. Mogą również znaleźć ją, wpisując "Dostawców usług" w polu wyszukiwania w górnej części witryny Azure portal.

Należy pamiętać, że **dostawców usług** strona zawiera tylko informacje na temat dostawców usług, które mają dostęp do subskrypcji lub grup zasobów za pomocą usługi Azure resource delegowanego management odbiorcy. Jeśli klient działa z dostawcami usług dodatkowych, którzy nie używają zarządzanie zasobami platformy Azure delegowanego dostępu do zasobów przez klienta, informacje na temat tych dostawców usług nie jest wyświetlane w tym miejscu.

> [!NOTE]
> Usługodawcy mogą wyświetlać informacje na temat swoich klientów, przechodząc do **moich klientów** w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [widoku klientów i delegowanego zasobów oraz zarządzanie nimi](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Wyświetl szczegóły dostawcy usług

Aby wyświetlić informacje na temat dostawców usług, który współpracuje z klientem, można wybrać **dostawca oferuje** w lewej części **dostawców usług** strony.

Dla każdej oferty dostawcy usług klienta będzie widoczna nazwa dostawcy usługi i oferty skojarzony z nim, wraz z nazwą, które klient wprowadzone podczas procesu dołączania.

W **delegacji** kolumny, klient zobaczy, jak wiele subskrypcji i/lub grupy zasobów zostały delegowane do dostawcy usług w celu skorzystania z tej oferty. Dostawca usług będą mogli dostępu i zarządzania subskrypcjami i/lub grup zasobów zgodnie z poziomami dostępu określone w tej ofercie.

## <a name="delegate-resources"></a>Zasoby delegata

Zanim dostawcy usług mogą dostęp do zasobów i zarządzanie nimi klienta, musi być delegowane. Jeśli klient zaakceptował oferty, ale nie przekazał jeszcze żadnych zasobów, użytkownik zobaczy Uwaga na początku **dostawca oferuje** sekcji. Dzięki temu klient wiedzieć, że muszą się na podjęcie działań przed dostawcy usług mogą uzyskać dostępu do żadnych zasobów przez klienta.

Aby delegować grup zasobów lub subskrypcji:

1. Pole wyboru dla wiersza zawierającego usługodawcy, oferty i nazwy. Następnie wybierz pozycję **delegować zasobów** w górnej części ekranu.
1. W **szczegóły oferty** części **delegować zasobów** strony, przejrzyj szczegóły dotyczące dostawcy usług i oferują. Aby przejrzeć przypisania roli dla tej oferty, wybrać **kliknij tutaj, aby wyświetlić szczegóły wybranej oferty**.
1. W **delegata** zaznacz **delegować subskrypcje** lub **delegować grup zasobów**.
1. Wybierz subskrypcje i/lub grupy zasobów, które chcesz delegować tej oferty, następnie wybierz pozycję **Dodaj**.
1. Zaznacz pole wyboru w dolnej części strony aby potwierdzić, że chcesz udzielić dostępu do zasobów, które zostanie wybrana, a następnie wybierz dostawcę usługi **delegata**.

## <a name="add-or-remove-service-provider-offers"></a>Dodawanie lub usuwanie oferty dostawcy usług

Klienta można dodać nowej oferty dostawcy usług z **dostawca oferuje** strony, wybierając **oferty Dodaj**. Dostawca usług musi opublikowane oferty dla danego klienta. Klient może wybrać tę ofertę z **oferty prywatne** ekranu, a następnie wybierz pozycję **Utwórz**.

Jeśli klient chce, aby usunąć oferty dostawcy usług, można wybrać ikonę Kosza na śmieci, w wierszu dla tej oferty. Po potwierdzeniu operacji usuwania, tego dostawcy usług mają już dostęp do zasobów klienta, które zostały wcześniej przekazane do tej oferty.

## <a name="view-delegations"></a>Wyświetl delegacji

Delegacji reprezentują przypisania ról, które udzielić uprawnień do dostawcy usług dla zasobów, które zostały przekazane przez klienta. Aby wyświetlić te informacje, wybierz **delegacji** w lewej części **dostawców usług** strony.

Filtry w górnej części strony umożliwiają Sortuj i Grupuj swoje informacje o delegowaniu lub Filtruj według określonych odbiorców, oferty i słów kluczowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [morskiej Azure](../overview.md).
- Dowiedz się, jak usługodawca może [wyświetlania i zarządzania nimi klienci](view-manage-customers.md) , przechodząc do **moich klientów** w witrynie Azure portal.