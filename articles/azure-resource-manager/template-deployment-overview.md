---
title: Przegląd szablonów
description: Opisuje zalety korzystania z szablonów Azure Resource Manager na potrzeby wdrażania zasobów.
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0765ec1cd5952a05a168dfed72ba4d6fd78ed89c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150288"
---
# <a name="azure-resource-manager-templates-overview"></a>Przegląd szablonów Azure Resource Manager

Dzięki przeniesieniu do chmury wiele zespołów przyjmuje metody programowania Agile. Te zespoły szybko iterą. Muszą one wielokrotnie wdrażać swoje rozwiązania w chmurze i wiedzieć, że ich infrastruktura jest w niezawodnym stanie. Ponieważ infrastruktura stała się częścią procesu iteracji, podział między operacjami i programowaniem zniknął. Zespoły muszą zarządzać infrastrukturą i kodem aplikacji za pomocą ujednoliconego procesu.

Aby sprostać tym wyzwaniom, można zautomatyzować wdrożenia i korzystać z zalet infrastruktury jako kodu. W kodzie można zdefiniować infrastrukturę, która musi zostać wdrożona. Kod infrastruktury jest częścią projektu. Podobnie jak w przypadku kodu aplikacji, kod infrastruktury jest przechowywany w repozytorium źródłowym i w wersji. Każdy z nich w zespole może uruchomić kod i wdrożyć podobne środowiska.

Aby zaimplementować infrastrukturę jako kod dla rozwiązań platformy Azure, użyj szablonów Azure Resource Manager. Szablon jest plikiem JavaScript Object Notation (JSON), który definiuje infrastrukturę i konfigurację projektu. Szablon używa składni deklaracyjnej, która pozwala na określenie, co zamierzasz wdrożyć, bez konieczności pisania sekwencji poleceń programowania, aby je utworzyć. W szablonie należy określić zasoby do wdrożenia oraz właściwości tych zasobów.

## <a name="why-choose-resource-manager-templates"></a>Dlaczego warto wybrać Menedżer zasobów szablony?

Jeśli próbujesz zdecydować się na korzystanie z szablonów Menedżer zasobów i jednej z innych infrastruktury jako usług kodu, weź pod uwagę następujące korzyści wynikające z używania szablonów:

* **Składnia deklaracyjne**: szablony Menedżer zasobów umożliwiają deklaratywne tworzenie i wdrażanie całej infrastruktury platformy Azure. Można na przykład wdrożyć nie tylko maszyny wirtualne, ale również infrastrukturę sieci, systemy magazynu i inne zasoby, które mogą być potrzebne.

* **Powtarzalne wyniki**: wielokrotnie wdrażać infrastrukturę w całym cyklu rozwoju i mieć pewność, że zasoby są wdrażane w spójny sposób. Szablony są idempotentne, co oznacza, że można wdrożyć ten sam szablon wiele razy i uzyskać te same typy zasobów w tym samym stanie. Możesz opracować jeden szablon, który reprezentuje żądany stan, a nie opracowując wielu oddzielnych szablonów do reprezentowania aktualizacji.

* **Aranżacja**: nie trzeba martwić się o złożone operacje porządkowania. Menedżer zasobów organizuje wdrażanie zasobów zależnych, aby zostały utworzone w odpowiedniej kolejności. Gdy jest to możliwe, Menedżer zasobów wdraża zasoby równolegle, dzięki czemu wdrożenia kończą się szybciej niż wdrożenia seryjne. Szablon jest wdrażany za pomocą jednego polecenia, a nie przez wiele niekoniecznych poleceń.

   ![Porównanie Template deployment](./media/template-deployment-overview/template-processing.png)

* **Wbudowana weryfikacja**: szablon jest wdrażany dopiero po przekazaniu walidacji. Menedżer zasobów sprawdza szablon przed rozpoczęciem wdrażania, aby upewnić się, że wdrożenie zakończy się pomyślnie. Wdrożenie jest mniej prawdopodobnie zatrzymane w stanie Half-gotowym.

* **Pliki modularne**: można rozbić szablony na mniejsze składniki, wielokrotnego użytku i łączyć je ze sobą w czasie wdrażania. Możesz również zagnieżdżać jeden szablon w innych szablonach.

* **Utwórz dowolny zasób platformy Azure**: możesz od razu korzystać z nowych usług i funkcji platformy Azure w szablonach. Gdy tylko dostawca zasobów wprowadza nowe zasoby, można wdrożyć te zasoby za pomocą szablonów. Nie musisz czekać na aktualizację narzędzi lub modułów przed rozpoczęciem korzystania z nowych usług.

* **Śledzone wdrożenia**: w Azure Portal można przejrzeć historię wdrożenia i uzyskać informacje o wdrożeniu szablonu. Zobaczysz szablon, który został wdrożony, wartości parametrów przekazywane i wszystkie wartości wyjściowe. Inne infrastruktury jako usługi kodu nie są śledzone za pomocą portalu.

   ![Historia wdrożenia](./media/template-deployment-overview/deployment-history.png)

* **Zasady jako kod**: [Azure Policy](../governance/policy/overview.md) jest zasadą jako strukturę kodu do automatyzowania zarządzania. Jeśli korzystasz z zasad platformy Azure, korygowanie zasad odbywa się w przypadku niezgodnych zasobów w przypadku ich wdrożenia za pomocą szablonów.

* **Plany wdrożenia**: możesz skorzystać z [planów](../governance/blueprints/overview.md) zapewnianych przez firmę Microsoft, aby spełnić standardy zgodności z przepisami. Te plany obejmują wstępnie skompilowane szablony dla różnych architektur.

* **Integracja**ciągłej integracji i ciągłego wdrażania: możesz zintegrować szablony z narzędziami do ciągłego i nieciągłego wdrożenia (Ci/CD), które mogą zautomatyzować potoki wydań w celu uzyskania szybkich i niezawodnych aktualizacji aplikacji i infrastruktury. Za pomocą zadania DevOps platformy Azure i szablonu Menedżer zasobów można używać Azure Pipelines do ciągłego kompilowania i wdrażania projektów szablonów Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [projekt programu vs z potokami](./vs-resource-groups-project-devops-pipelines.md) i [ciągłą integracją z Azure Pipelines](./resource-manager-tutorial-use-azure-pipelines.md).

* **Kod możliwy do eksportu**: można uzyskać szablon dla istniejącej grupy zasobów, eksportując bieżący stan grupy zasobów lub wyświetlając szablon używany do określonego wdrożenia. Przeglądając [wyeksportowany szablon](export-template-portal.md), można poznać jego składnię.

* **Narzędzia autorskie**: szablony można tworzyć za pomocą [Visual Studio Code](resource-manager-tools-vs-code.md) i rozszerzenia narzędzia szablonu. Uzyskasz funkcję IntelliSense, wyróżnianie składni, pomoc w wierszu i wiele innych funkcji języka. Oprócz programu Visual Studio Code można również użyć [programu Visual Studio](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="template-file"></a>Plik szablonu

W ramach szablonu można napisać [wyrażenia szablonu](template-expressions.md) , które zwiększają możliwości JSON. Te wyrażenia wykorzystują [funkcje](resource-group-template-functions.md) udostępniane przez Menedżer zasobów.

Szablon zawiera następujące sekcje:

* [Parametry](template-parameters.md) — podaj wartości podczas wdrażania, które umożliwiają użycie tego samego szablonu w różnych środowiskach.

* [Zmienne](template-variables.md) — Zdefiniuj wartości, które są ponownie używane w szablonach. Mogą być zbudowane z wartości parametrów.

* [Funkcje zdefiniowane przez użytkownika](template-user-defined-functions.md) — tworzenie dostosowanych funkcji, które upraszczają szablon.

* [Zasoby](resource-group-authoring-templates.md#resources) — Określ zasoby do wdrożenia.

* Dane [wyjściowe](template-outputs.md) — zwraca wartości ze wdrożonych zasobów.

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

* Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego szablonu Azure Resource Manager](template-tutorial-create-first-template.md).
* Aby uzyskać informacje o właściwościach w plikach szablonów, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat eksportowania szablonów, zobacz [Szybki Start: Tworzenie i wdrażanie szablonów Azure Resource Manager przy użyciu Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md).
