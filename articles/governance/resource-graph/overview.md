---
title: Omówienie usługi Azure Resource Graph
description: Dowiedz się, jak usługa wykres zasobów platformy Azure umożliwia złożonych zapytań zasobów na dużą skalę.
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d78c640f4269c799d3d371e6dd9db477faf96694
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807420"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Omówienie usługi Azure Graph zasobów

Wykres zasobów platformy Azure to usługa platformy Azure, której celem jest rozszerzenie usługi Azure Resource Management, zapewniając wydajne i eksploracja zasobów wydajne możliwość zapytań na dużą skalę w danym zestawie subskrypcji, dzięki czemu można efektywnie zarządzać usługi środowisko. Te zapytania zapewniają następujące funkcje:

- Zdolność do wysyłania zapytań do zasobów przy użyciu złożonego filtrowania, grupowania i sortowania według właściwości zasobu.
- Możliwość iteracyjne zapoznaj się z zasobami, w oparciu o wymagania ładu.
- Zdolność do oceny wpływu stosowania zasad na ogromne środowisko chmury.
- Możliwość [szczegółów zmiany wprowadzone do właściwości zasobu](./how-to/get-resource-changes.md) (wersja zapoznawcza).

W tej dokumentacji każda funkcja zostanie szczegółowo omówiona.

> [!NOTE]
> Wykres zasobów platformy Azure obsługuje pasek wyszukiwania w witrynie Azure portal, Przeglądaj nowe środowisko "Wszystkie zasoby" i usługi Azure Policy [historię zmian](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. Ustalono, aby pomóc klientom w zarządzaniu środowisk na dużą skalę.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak usługa Resource Graph uzupełnia usługę Azure Resource Manager

Usługa Azure Resource Manager obsługuje obecnie zapytania w polach podstawowych zasobów, w szczególności — nazwa zasobu, Identyfikatora, typu, grupa zasobów, subskrypcji i lokalizacji. Usługi Resource Manager udostępnia również funkcje służące do wywoływania dostawcy poszczególnych zasobów dla jednego zasobu szczegółowe właściwości w danym momencie.

Za pomocą usługi Azure Resource Graph możesz uzyskać dostęp do tych właściwości, które zwracają dostawców zasobów, bez konieczności wykonywania poszczególnych wywołań do każdego dostawcy zasobów. Aby uzyskać listę obsługiwane typy zasobów, poszukaj **tak** w [zasobów w przypadku wdrożeń w trybie](../../azure-resource-manager/complete-mode-deletion.md) tabeli.

Wykres zasobów platformy Azure możesz:

- Dostęp do właściwości zwracane przez dostawców zasobów bez konieczności wprowadzić poszczególne wywołania, aby każdy dostawca zasobów.
- Wyświetlanie ostatnich 14 dni historii zmian wprowadzonych do zasobu zmianie właściwości i kiedy. (wersja zapoznawcza)

## <a name="how-resource-graph-is-kept-current"></a>Jak wykres zasobów jest aktualizowany

Po zaktualizowaniu zasobów platformy Azure, wykres zasobów jest powiadamiany przez usługę Resource Manager zmiany.
Wykres zasobów następnie aktualizuje swoją bazę danych. Wykres zasobów wykonywane jest także wyrażenie _pełne skanowanie w poszukiwaniu_. To skanowanie zapewnia, że wykres zasobów danych jest bieżący, w przypadku brakujących powiadomienia, lub gdy zasób jest aktualizowany poza usługi Resource Manager.

## <a name="the-query-language"></a>Język zapytań

Teraz, gdy lepiej rozumiesz, czym jest usługa Azure Resource Graph, przyjrzyjmy się bliżej temu, jak tworzyć zapytania.

Ważne jest zrozumienie, że język zapytań usługi Azure Resource Graph opiera się na języku zapytań [Kusto Query Language](../../data-explorer/data-explorer-overview.md) używanym przez usługę Azure Data Explorer.

Po pierwsze, aby poznać szczegółowe informacje dotyczące operacji i funkcji, które mogą być używane z usługą Azure Resource Graph, zobacz [język zapytań usługi Resource Graph](./concepts/query-language.md).
Aby przejrzeć zasoby, zobacz [badanie zasobów](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Uprawnienia w usłudze Azure Resource Graph

Aby użyć usługi Resource Graph, musisz mieć odpowiednie prawa w [kontroli dostępu na podstawie ról](../../role-based-access-control/overview.md) (RBAC) dające co najmniej dostęp do odczytu do zasobów, które chcesz zbadać. Bez uprawnień do obiektu lub grupy obiektów platformy Azure na poziomie co najmniej `read` wyniki nie będą zwracane.

> [!NOTE]
> Wykres zasobów subskrypcji dostępnych na podmiot zabezpieczeń jest używany podczas logowania. Aby wyświetlić zasoby nową subskrypcję dodany podczas aktywnej sesji, podmiot zabezpieczeń należy odświeżyć kontekstu. Ta akcja odbywa się automatycznie podczas rejestrowania i ponownie do niej.

## <a name="throttling"></a>Ograniczanie przepływności

Jako to bezpłatna usługa zapytania do wykresu zasobów są ograniczone, aby zapewnić najlepsze środowisko i czasu odpowiedzi dla wszystkich klientów. Jeśli organizacja chce używać interfejsu API programu Graph zasobów dla dużych i często zapytań, użyć portalu "Opinie" [strony portalu wykres zasobów](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Podaj Twój przypadek biznesowy i zaznacz pole wyboru "Firmy Microsoft można wysłać pocztą e-mail możesz sprawie Twojej opinii" w kolejności dla zespołu do skontaktowania się z Tobą.

Wykres zasobów ogranicza zapytań na poziomie użytkownika. Odpowiedź usługi zawiera następujące nagłówki HTTP:

- `x-ms-user-quota-remaining` (int): Pozostały limit przydziału zasobów dla użytkownika. Ta wartość jest mapowany na liczby zapytań.
- `x-ms-user-quota-resets-after` (: mm: ss): Czas trwania, dopóki nie zostanie zresetowane przez użytkownika limit przydziału użycia

Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące żądania ograniczone](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Uruchamianie pierwszego zapytania

Wykres zasobów obsługuje wiersza polecenia platformy Azure, programu Azure PowerShell i zestawie Azure SDK dla platformy .NET. Zapytania są skonstruowane takie same dla każdego języka. Dowiedz się, jak włączyć usługę Resource Graph w [interfejsie wiersza polecenia platformy Azure](first-query-azurecli.md#add-the-resource-graph-extension) i [programie Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module).

## <a name="next-steps"></a>Następne kroki

- Uruchamianie pierwszego zapytania przy użyciu [wiersza polecenia platformy Azure](first-query-azurecli.md).
- Uruchamianie pierwszego zapytania przy użyciu [programu Azure PowerShell](first-query-powershell.md).
- Rozpoczynać [początkowego zapytania](./samples/starter.md).
- W lepszym zrozumieniu z [zaawansowane zapytania](./samples/advanced.md).