---
title: Szablony usługi Azure Resource Manager
description: Opisuje sposób używania szablonów Azure Resource Manager do wdrażania zasobów.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/07/2019
ms.author: tomfitz
ms.openlocfilehash: 61e9bbabee969280c07521edb05d67ba68c0c58e
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802024"
---
# <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Dzięki przeniesieniu do chmury wiele zespołów przyjmuje metody programowania Agile. Te zespoły szybko iterą. Muszą one wielokrotnie wdrażać swoje rozwiązania w chmurze i wiedzieć, że ich infrastruktura jest w niezawodnym stanie. Ponieważ infrastruktura stała się częścią procesu iteracji, podział między operacjami i programowaniem zniknął. Zespoły muszą zarządzać infrastrukturą i kodem aplikacji za pomocą ujednoliconego procesu.

Aby sprostać tym wyzwaniom, można zautomatyzować wdrożenia i korzystać z zalet infrastruktury jako kodu. W kodzie można zdefiniować infrastrukturę, która musi zostać wdrożona. Kod infrastruktury jest częścią projektu. Podobnie jak w przypadku kodu aplikacji, kod infrastruktury jest przechowywany w repozytorium źródłowym i w wersji. Każdy z nich w zespole może uruchomić kod i wdrożyć podobne środowiska.

Aby zaimplementować infrastrukturę jako kod dla rozwiązań platformy Azure, użyj szablonów Azure Resource Manager. Szablon jest plikiem JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaracyjnej, która pozwala na określenie, co zamierzasz wdrożyć, bez konieczności pisania sekwencji poleceń programowania, aby je utworzyć. W szablonie należy określić zasoby do wdrożenia oraz właściwości tych zasobów.

## <a name="benefits-of-resource-manager-templates"></a>Zalety szablonów Menedżer zasobów

Szablony Menedżer zasobów oferują następujące korzyści:

* Wdrażaj i Monitoruj wszystkie zasoby dla rozwiązania jako grupę, a także zarządzaj nimi, a nie Przetwarzaj tych zasobów pojedynczo.

* Wielokrotnie Wdrażaj swoje rozwiązanie w całym cyklu programowania i zapewniaj pewność, że zasoby są wdrażane w spójnym stanie.

* Zarządzaj infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

Jeśli próbujesz zdecydować się na korzystanie z szablonów Menedżer zasobów lub jednej z innych infrastruktury jako usług kodu, weź pod uwagę następujące zalety szablonów:

* Nowe usługi i funkcje platformy Azure są natychmiast dostępne w szablonach. Gdy tylko dostawca zasobów wprowadza nowe zasoby, można wdrożyć te zasoby za pomocą szablonów. W przypadku innych infrastruktury jako usług kodu należy poczekać, aż inne strony zaimplementują interfejsy dla nowych zasobów.

* Wdrożenia szablonów są obsługiwane za pomocą pojedynczego przesłania szablonu, a nie przez wiele nieprawidłowych poleceń. Menedżer zasobów organizuje wdrażanie zasobów zależnych, aby zostały utworzone w odpowiedniej kolejności. Analizuje szablon i określa prawidłową kolejność wdrażania na podstawie odwołań między zasobami.

   ![Porównanie Template deployment](./media/template-deployment-overview/template-processing.png)

* Wdrożenia szablonów są śledzone w Azure Portal. Możesz przejrzeć historię wdrożenia i uzyskać informacje o wdrożeniu szablonu. Zobaczysz szablon, który został wdrożony, wartości parametrów przekazywane i wszystkie wartości wyjściowe. Inne infrastruktury jako usługi kodu nie są śledzone za pomocą portalu.

   ![Historia wdrożenia](./media/template-deployment-overview/deployment-history.png)

* Wdrożenia szablonów podlegają weryfikacji przed inspekcją. Menedżer zasobów sprawdza szablon przed rozpoczęciem wdrażania, aby upewnić się, że wdrożenie zakończy się pomyślnie. Wdrożenie jest mniej prawdopodobnie zatrzymane w stanie Half-gotowym.

* Jeśli korzystasz z [zasad platformy Azure](../governance/policy/overview.md), korygowanie zasad odbywa się w przypadku niezgodnych zasobów w przypadku ich wdrożenia za pomocą szablonów.

* Firma Microsoft udostępnia [plany](../governance/blueprints/overview.md) wdrożenia w celu spełnienia standardów zgodności z przepisami. Te plany obejmują wstępnie skompilowane szablony dla różnych architektur.

## <a name="idempotent"></a>Idempotentne

Idempotentne oznacza, że można uruchamiać te same operacje wiele razy i uzyskać ten sam wynik. Wdrożenie szablonu Menedżer zasobów jest idempotentne. Ten sam szablon można wdrożyć wiele razy i uzyskać te same typy zasobów w tym samym stanie. Jest to ważne, ponieważ oznacza to, że można uzyskać spójne wyniki, niezależnie od tego, czy szablon zostanie wdrożony w istniejącej grupie zasobów, czy wdrożyć szablon nowej grupy zasobów.

Załóżmy, że zostały wdrożone trzy zasoby do grupy zasobów, a następnie zdecydujesz, że należy dodać czwarty zasób. Zamiast tworzyć nowy szablon, który zawiera tylko nowy zasób, można dodać czwarty zasób do istniejącego szablonu. Po wdrożeniu nowego szablonu do grupy zasobów, która ma już trzy zasoby, Menedżer zasobów, które akcje należy wykonać.

Jeśli zasób istnieje w grupie zasobów, a żądanie nie zawiera żadnych aktualizacji właściwości, nie zostanie podjęta żadna akcja. Jeśli zasób istnieje, ale zmieniono właściwości, istniejący zasób zostanie zaktualizowany. Jeśli zasób nie istnieje, zostanie utworzony nowy zasób.

Po zakończeniu wdrażania zasoby są zawsze w oczekiwanym stanie.

## <a name="template-file"></a>Plik szablonu

W ramach szablonu można napisać [wyrażenia szablonu](template-expressions.md) , które zwiększają możliwości JSON. Te wyrażenia wykorzystują [funkcje](resource-group-template-functions.md) udostępniane przez Menedżer zasobów.

Szablon zawiera następujące sekcje:

* [Parametry](template-parameters.md) — podaj wartości podczas wdrażania, które umożliwiają użycie tego samego szablonu w różnych środowiskach.

* [Zmienne](template-variables.md) — Zdefiniuj wartości, które są ponownie używane w szablonach. Mogą być zbudowane z wartości parametrów.

* [Funkcje zdefiniowane przez użytkownika](template-user-defined-functions.md) — tworzenie dostosowanych funkcji, które upraszczają szablon.

* [Zasoby](resource-group-authoring-templates.md#resources) — Określ zasoby do wdrożenia.

* Dane [wyjściowe](template-outputs.md) — zwraca wartości ze wdrożonych zasobów.

## <a name="template-features"></a>Funkcje szablonu

Menedżer zasobów analizuje zależności, aby zapewnić, że zasoby są tworzone w odpowiedniej kolejności. Większość zależności jest określana niejawnie. Można jednak jawnie ustawić zależność, aby upewnić się, że jeden zasób został wdrożony przed innym zasobem. Aby uzyskać więcej informacji, zobacz [Defining dependencies in Azure Resource Manager templates](resource-group-define-dependencies.md) (Definiowanie zależności w szablonach usługi Azure Resource Manager).

Możesz dodać zasób do szablonu i opcjonalnie go wdrożyć. Zwykle przekazuje się wartość parametru, która wskazuje, czy należy wdrożyć zasób. Aby uzyskać więcej informacji, zobacz [wdrażanie warunkowe w szablonach Menedżer zasobów](conditional-resource-deployment.md).

Zamiast powtarzających się bloków JSON wiele razy w szablonie, można użyć elementu Copy do określenia więcej niż jednego wystąpienia zmiennej, właściwości lub zasobu. Aby uzyskać więcej informacji, zobacz temat " [Resource, Property" lub "zmienna" w szablonach Azure Resource Manager](resource-group-create-multiple.md).

## <a name="export-templates"></a>Eksportowanie szablonów

Możesz uzyskać szablon dla istniejącej grupy zasobów, eksportując bieżący stan grupy zasobów lub wyświetlając szablon używany do określonego wdrożenia. Przeglądając [wyeksportowany szablon](export-template-portal.md), można poznać jego składnię.

W przypadku tworzenia rozwiązania z portalu rozwiązanie automatycznie zawiera szablon wdrożenia. Nie trzeba tworzyć szablonu od początku — można zacząć od szablonu istniejącego rozwiązania i dostosować go do konkretnych potrzeb. Aby zapoznać się z przykładem, zobacz [Szybki start: Tworzenie i wdrażanie szablonów usługi Azure Resource Manager przy użyciu witryny Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).

## <a name="template-deployment-process"></a>Proces Template deployment

Podczas wdrażania szablonu Menedżer zasobów konwertuje szablon na operacje interfejsu API REST. Na przykład, gdy usługa Resource Manager odbiera szablon o następującej definicji zasobu:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Konwertuje definicję do następującej operacji interfejsu API REST, która zostaje wysłana do dostawcy zasobów Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

## <a name="template-design"></a>Projekt szablonu

Sposób definiowania szablonów i grup zasobów zależy wyłącznie od użytkownika, podobnie jak sposób zarządzania rozwiązaniem. Można na przykład wdrożyć aplikację trójwarstwową za pomocą jednego szablonu do pojedynczej grupy zasobów.

![szablon trójwarstwowy](./media/template-deployment-overview/3-tier-template.png)

Nie trzeba jednak definiować całej infrastruktury w jednym szablonie. Często dobrym rozwiązaniem jest podział wymagań dotyczących wdrożenia na szablony przeznaczone do określonego celu. Te szablony mogą bez problemu być używane wielokrotnie w różnych rozwiązaniach. Aby wdrożyć dane rozwiązanie, należy utworzyć szablon wzorcowy połączony ze wszystkimi wymaganymi szablonami. Na poniższej ilustracji przedstawiono sposób wdrażania rozwiązania trójwarstwowego za pomocą szablonu nadrzędnego, który zawiera trzy szablony zagnieżdżone.

![zagnieżdżony szablon warstwowy](./media/template-deployment-overview/nested-tiers-template.png)

Jeśli przewidujesz, że warstwy będą miały osobne cykle, możesz wdrożyć trzy warstwy do osobnych grup zasobów. Należy pamiętać, że zasoby mogą nadal być powiązane z zasobami w innych grupach zasobów.

![szablon warstwowy](./media/template-deployment-overview/tier-templates.png)

Informacje dotyczące szablonów zagnieżdżonych można znaleźć w temacie [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md) (Używanie szablonów połączonych w usłudze Azure Resource Manager).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o właściwościach w plikach szablonów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
* Aby rozpocząć tworzenie szablonu, zobacz [tworzenie Azure Resource Manager szablonów przy użyciu Visual Studio Code](resource-manager-tools-vs-code.md).
* Aby zapoznać się z wprowadzeniem do usługi Menedżer zasobów, w tym funkcji zarządzania, zobacz [Azure Resource Manager omówienie](resource-group-overview.md).

