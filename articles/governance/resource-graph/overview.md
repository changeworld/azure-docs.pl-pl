---
title: Omówienie usługi Azure Resource Graph
description: Dowiedz się, jak usługa Azure Resource Graph umożliwia kompleksowe wykonywanie zapytań o zasoby na dużą skalę w ramach subskrypcji i dzierżaw.
ms.date: 03/02/2020
ms.topic: overview
ms.openlocfilehash: f5c091f60faedb76e3ca6cd68505c06f51be21b6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381516"
---
# <a name="what-is-azure-resource-graph"></a>Co to jest usługa Azure Resource Graph?

Usługa Azure Resource Graph to usługa na platformie Azure, która ma na celu rozszerzenie usługi Azure Resource Management, zapewniając wydajne i wydajne eksploracji zasobów z możliwością wykonywania zapytań na dużą skalę w danym zestawie subskrypcji, dzięki czemu można skutecznie zarządzać środowiskiem. Te zapytania zapewniają następujące funkcje:

- Zdolność do wysyłania zapytań do zasobów przy użyciu złożonego filtrowania, grupowania i sortowania według właściwości zasobu.
- Możliwość iteracyjnego eksplorowania zasobów na podstawie wymagań dotyczących nadzoru.
- Zdolność do oceny wpływu stosowania zasad na ogromne środowisko chmury.
- Możliwość [szczegółowego wyszczególnienia zmian wprowadzonych we właściwościach zasobów](./how-to/get-resource-changes.md) (wersja zapoznawcza).

W tej dokumentacji każda funkcja zostanie szczegółowo omówiona.

> [!NOTE]
> Usługa Azure Resource Graph zasila pasek wyszukiwania portalu Azure, nowe środowisko przeglądania "Wszystkie zasoby" oraz_wizualny dyferb_ [historii zmian](../policy/how-to/determine-non-compliance.md#change-history-preview)
> w usłudze Azure Policy. Został zaprojektowany, aby pomóc klientom zarządzać środowiskami na dużą skalę.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak usługa Resource Graph uzupełnia usługę Azure Resource Manager

Usługa Azure Resource Manager obsługuje obecnie kwerendy dotyczące podstawowych pól zasobów, w szczególności — nazwa zasobu, identyfikator, typ, grupa zasobów, subskrypcja i lokalizacja. Menedżer zasobów udostępnia również możliwości wywoływania poszczególnych dostawców zasobów dla szczegółowych właściwości po jednym zasobie naraz.

Za pomocą usługi Azure Resource Graph możesz uzyskać dostęp do tych właściwości, które zwracają dostawców zasobów, bez konieczności wykonywania poszczególnych wywołań do każdego dostawcy zasobów. Aby uzyskać listę obsługiwanych typów zasobów, przejrzyj [odwołanie do tabeli i typu zasobu](./reference/supported-tables-resources.md). Alternatywnym sposobem, aby zobaczyć obsługiwane typy zasobów jest za pośrednictwem [przeglądarki programu Azure Resource Graph Explorer Schema](./first-query-portal.md#schema-browser).

Za pomocą programu Azure Resource Graph można:

- Dostęp do właściwości zwracanych przez dostawców zasobów bez konieczności wykonywania indywidualnych wywołań do każdego dostawcy zasobów.
- Wyświetl ostatnie 14 dni historii zmian wprowadzone do zasobu, aby zobaczyć, jakie właściwości zmienione i kiedy. (wersja zapoznawcza)

## <a name="how-resource-graph-is-kept-current"></a>Jak wykres zasobów jest aktualizowany

Po zaktualizowaniu zasobu platformy Azure, wykres zasobów jest powiadamiany przez Menedżera zasobów o zmianie.
Wykres zasobów następnie aktualizuje swoją bazę danych. Wykres zasobów również wykonuje regularne _pełne skanowanie._ To skanowanie gwarantuje, że dane wykresu zasobów są aktualne, jeśli są nieodebrane powiadomienia lub gdy zasób jest aktualizowany poza Menedżerem zasobów.

> [!NOTE]
> Wykres zasobów używa `GET` do najnowszego interfejsu API bez podglądu każdego dostawcy zasobów do zbierania właściwości i wartości. W związku z tym oczekiwana nieruchomość może być niedostępna. W niektórych przypadkach wersja interfejsu API została zastąpiona, aby zapewnić bardziej aktualne lub powszechnie używane właściwości w wynikach. Zobacz [Pokaż wersję interfejsu API dla każdego](./samples/advanced.md#apiversion) przykładu typu zasobu, aby uzyskać pełną listę w twoim środowisku.

## <a name="the-query-language"></a>Język zapytań

Teraz, gdy masz lepsze zrozumienie, co to jest usługa Azure Resource Graph, przejdźmy do sposobu konstruowania zapytań.

Ważne jest zrozumienie, że język zapytań usługi Azure Resource Graph opiera się na języku zapytań [Kusto Query Language](/azure/data-explorer/data-explorer-overview) używanym przez usługę Azure Data Explorer.

Po pierwsze, aby poznać szczegółowe informacje dotyczące operacji i funkcji, które mogą być używane z usługą Azure Resource Graph, zobacz [język zapytań usługi Resource Graph](./concepts/query-language.md).
Aby przejrzeć zasoby, zobacz [badanie zasobów](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Uprawnienia w usłudze Azure Resource Graph

Aby użyć usługi Resource Graph, musisz mieć odpowiednie prawa w [kontroli dostępu na podstawie ról](../../role-based-access-control/overview.md) (RBAC) dające co najmniej dostęp do odczytu do zasobów, które chcesz zbadać. Bez uprawnień do obiektu lub grupy obiektów platformy Azure na poziomie co najmniej `read` wyniki nie będą zwracane.

> [!NOTE]
> Wykres zasobów używa subskrypcji dostępnych dla podmiotu zabezpieczeń podczas logowania. Aby wyświetlić zasoby nowej subskrypcji dodane podczas aktywnej sesji, podmiot zabezpieczeń musi odświeżyć kontekst. Ta akcja dzieje się automatycznie podczas wylogowywania się i powrotu.

Interfejs wiersza polecenia platformy Azure i usługi Azure PowerShell używają subskrypcji, do których użytkownik ma dostęp. W przypadku bezpośredniego korzystania z interfejsu API REST lista subskrypcji jest dostarczana przez użytkownika. Jeśli użytkownik ma dostęp do dowolnej z subskrypcji na liście, wyniki kwerendy są zwracane dla subskrypcji, do których użytkownik ma dostęp. To zachowanie jest takie samo jak podczas wywoływania [grup zasobów — lista](/rest/api/resources/resourcegroups/list) \- otrzymasz grup zasobów, do których masz dostęp bez żadnych oznak, że wynik może być częściowy.
Jeśli na liście subskrypcji nie ma żadnych subskrypcji, do których użytkownik ma odpowiednie prawa, odpowiedzią jest _403_ (zabronione).

## <a name="throttling"></a>Ograniczanie przepływności

Jako bezpłatna usługa zapytania do wykresu zasobów są ograniczane, aby zapewnić najlepsze środowisko i czas odpowiedzi dla wszystkich klientów. Jeśli organizacja chce używać interfejsu API wykresu zasobów do zapytań na dużą skalę i częstych zapytań, użyj portalu "Opinie" ze [strony portalu Wykres zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Podaj swoją sprawę biznesową i wybierz pole wyboru "Microsoft może wysłać ci wiadomość e-mail na temat twojej opinii", aby zespół mógł się z Tobą skontaktować.

Wykres zasobów ogranicza zapytania na poziomie użytkownika. Odpowiedź usługi zawiera następujące nagłówki HTTP:

- `x-ms-user-quota-remaining`(int): Pozostały przydział zasobów dla użytkownika. Ta wartość jest mapowana do liczby zapytań.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Czas trwania do momentu zresetowania zużycia przydziału przez użytkownika

Aby uzyskać więcej informacji, zobacz [Wskazówki dotyczące żądań z ograniczeniem](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Uruchamianie pierwszego zapytania

Eksplorator wykresu zasobów platformy Azure, część witryny Azure portal, umożliwia uruchamianie zapytań wykresu zasobów bezpośrednio w witrynie Azure portal. Przypnij wyniki jako wykresy dynamiczne, aby dostarczać dynamiczne informacje w czasie rzeczywistym do przepływu pracy portalu. Aby uzyskać więcej informacji, zobacz [Pierwsza kwerenda za pomocą Eksploratora grafów zasobów platformy Azure](first-query-portal.md).

Resource Graph obsługuje platformę Azure CLI, Azure PowerShell, Azure SDK dla platformy .NET i inne. Kwerenda jest tak samo skonstruowana dla każdego języka. Dowiedz się, jak włączyć wykres zasobów za pomocą:

- [Witryna Azure portal i Eksplorator wykresów zasobów](first-query-portal.md) 
- [Interfejs wiersza polecenia platformy Azure](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Następne kroki

- Uruchom pierwszą kwerendę przy użyciu [portalu Azure](first-query-portal.md).
- Uruchom pierwszą kwerendę za pomocą [narzędzia Azure CLI](first-query-azurecli.md).
- Uruchom pierwszą kwerendę za pomocą [programu Azure PowerShell](first-query-powershell.md).