---
title: Omówienie usługi Azure Resource Manager | Microsoft Docs
description: Opis wdrażania zasobów na platformie Azure, kontrolowania dostępu do tych zasobów oraz zarządzania nimi za pomocą usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 76df7de1-1d3b-436e-9b44-e1b3766b3961
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 85dc16b07b72f2e8c1ed00fb5dd25288b985ae21
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603047"
---
# <a name="azure-resource-manager-overview"></a>Omówienie usługi Azure Resource Manager
Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja internetowa, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. Usługa Azure Resource Manager umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy. Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu. 

## <a name="terminology"></a>Terminologia
Jeśli dopiero zaczynasz korzystać z usługi Azure Resource Manager, oto kilka terminów, których możesz nie znać.

* **Zasób** — dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Niektóre typowe zasoby to: maszyna wirtualna, konto magazynu, aplikacja internetowa czy sieć wirtualna. Istnieje ich jednak wiele więcej.
* **Grupa zasobów** — kontener, który zawiera powiązane zasoby rozwiązania dla platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Zobacz [Grupy zasobów](#resource-groups).
* **Dostawca zasobów** — usługa dostarczająca zasoby, które można wdrażać i którymi można zarządzać za pomocą usługi Resource Manager. Każdy dostawca zasobów udostępnia operacje do pracy z wdrażanymi zasobami. Typowi dostawcy zasobów to: Microsoft.Compute dostarczający zasób maszyny wirtualnej, Microsoft.Storage dostarczający zasób konta magazynu i Microsoft.Web dostarczający zasoby dotyczące aplikacji internetowych. Zobacz [Dostawcy zasobów](#resource-providers).
* **Szablon usługi Resource Manager** — plik w formacie JavaScript Object Notation (JSON) definiujący jeden lub większą liczbę zasobów, które mają zostać wdrożone w grupie zasobów. Definiuje również zależności między wdrożonymi zasobami. Szablon może służyć do spójnego i wielokrotnego wdrażania zasobów. Zobacz [Wdrażanie na podstawie szablonu](#template-deployment).
* **Składnia deklaratywna** — składnia pozwalająca określić, co zamierzasz utworzyć, bez konieczności pisania w tym celu sekwencji poleceń programistycznych. Przykładem składni deklaratywnej jest szablon usługi Resource Manager. W tym pliku definiuje się właściwości infrastruktury do wdrożenia na platformie Azure. 

## <a name="the-benefits-of-using-resource-manager"></a>Zalety korzystania z usługi Resource Manager
Usługa Resource Manager zapewnia kilka korzyści:

* Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).
* Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.
* Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.
* Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.
* Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.
* Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.
* Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów korzystających z tego samego tagu.  

Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli znasz wcześniejszy model wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz artykuł [Understanding Resource Manager deployment and classic deployment](resource-manager-deployment-model.md) (Opis wdrażania za pomocą usługi Resource Manager oraz wdrażania klasycznego).

## <a name="consistent-management-layer"></a>Spójna warstwa zarządzania
Usługa Resource Manager zapewnia spójną warstwę zarządzania dla zadań wykonywanych za pomocą programu Azure PowerShell, interfejsu wiersza polecenia Azure, witryny Azure Portal, interfejsu API REST oraz narzędzi programistycznych. Wszystkie narzędzia korzystają ze wspólnego zestawu operacji. Możesz używać najwygodniejszych dla siebie narzędzi i stosować je zamiennie, bez jakichkolwiek problemów. 

Na poniższej ilustracji przedstawiono, jak poszczególne narzędzia wchodzą w interakcję z tym samym interfejsem API usługi Azure Resource Manager. Interfejs API przekazuje żądania do usługi Resource Manager, która je uwierzytelnia i autoryzuje. Usługa Resource Manager przekierowuje następnie żądania do odpowiednich dostawców zasobów.

![Model żądań usługi Resource Manager](./media/resource-group-overview/consistent-management-layer.png)

## <a name="guidance"></a>Wskazówki
Poniższe sugestie pomogą Ci w pełni wykorzystać możliwości usługi Resource Manager w pracy z rozwiązaniami.

1. Definiuj i wdrażaj infrastrukturę za pomocą składni deklaratywnej w szablonach usługi Resource Manager, a nie za pomocą poleceń imperatywnych.
2. Zdefiniuj w szablonie wszystkie etapy wdrażania i konfiguracji. W konfiguracji rozwiązania nie powinno być żadnych etapów ręcznych.
3. Korzystaj z poleceń imperatywnych do zarządzania zasobami, np. do uruchamiania i zatrzymywania aplikacji lub maszyny.
4. Rozmieść zasoby z tym samym cyklem życia w grupie zasobów. We wszystkich pozostałych operacjach związanych z organizacją zasobów używaj tagów.

Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).

## <a name="resource-groups"></a>Grupy zasobów
Definiując grupę zasobów, należy wziąć pod uwagę pewne ważne czynniki:

1. Wszystkie zasoby w grupie powinny mieć ten sam cykl życia. Są one wdrażane, aktualizowane i usuwane razem. Jeśli jakiś zasób, na przykład serwer bazy danych, ma mieć inny cykl wdrażania, powinien zostać umieszczony w innej grupie zasobów.
2. Każdy zasób może znajdować się tylko w jednej grupie zasobów.
3. Zasoby w grupie można dodawać i usuwać w dowolnym momencie.
4. Zasoby można przenosić między poszczególnymi grupami. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).
5. Grupa zasobów może zawierać zasoby, które znajdują się w różnych regionach.
6. Grupa zasobów może służyć do określania zakresu kontroli dostępu na potrzeby działań administracyjnych.
7. Zasób może wchodzić w interakcję z zasobami znajdującymi się w innych grupach zasobów. Ta interakcja jest typowa, gdy dwa zasoby są ze sobą powiązane, ale nie mają tego samego cyklu życia (na przykład aplikacje internetowe łączące się z bazą danych).

Podczas tworzenia grupy zasobów, należy podać lokalizację dla danej grupy zasobów. Być może zastanawiasz się, „Dlaczego grupa zasobów wymaga określenia lokalizacji? Ponadto dlaczego lokalizacja grupy zasobów jest w ogóle istotna, skoro zasoby mogą znajdować się w innej lokalizacji niż grupa zasobów?” Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

## <a name="resource-providers"></a>Dostawcy zasobów
Każdy dostawca zasobów udostępnia zestaw zasobów i operacji do pracy z usługą platformy Azure. Na przykład w celu przechowywania kluczy i kluczy tajnych należy użyć dostawcy zasobów **Microsoft.KeyVault**. Ten dostawca zasobów udostępnia typ zasobu o nazwie **magazyny** umożliwiający utworzenie magazynu kluczy. 

Nazwa typu zasobu ma następujący format: **{dostawca_zasobów}/{typ_zasobu}**. Typ magazynu kluczy to na przykład **Microsoft.KeyVault/vaults**.

Przed rozpoczęciem wdrażania zasobów należy uzyskać wiedzę na temat dostępnych dostawców zasobów. Znajomość nazw zasobów i ich dostawców pomaga określić zasoby, które mają zostać wdrożone na platformie Azure. Dodatkowo trzeba znać prawidłowe lokalizacje i wersje interfejsów API dla każdego typu zasobu. Aby uzyskać więcej informacji, zobacz [Dostawcy zasobów i ich typy](resource-manager-supported-services.md).

## <a name="template-deployment"></a>Wdrażanie na podstawie szablonu
Usługa Resource Manager umożliwia utworzenie szablonu (w formacie JSON) do definiowania infrastruktury i konfiguracji rozwiązania platformy Azure. Dzięki szablonowi można wielokrotnie wdrażać rozwiązanie w całym jego cyklu życia z gwarancją spójnego stanu zasobów po każdym wdrożeniu. W przypadku tworzenia rozwiązania z portalu rozwiązanie automatycznie zawiera szablon wdrożenia. Nie trzeba tworzyć szablonu od początku — można zacząć od szablonu istniejącego rozwiązania i dostosować go do konkretnych potrzeb. Aby uzyskać szablon dla istniejącej grupy zasobów, można wyeksportować bieżący stan grupy lub skorzystać z szablonu użytego do określonego wdrożenia. Przeglądając [wyeksportowany szablon](resource-manager-export-template.md), można poznać jego składnię.

Aby dowiedzieć się więcej o formacie szablonu i sposobie jego konstruowania, zobacz [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md). Aby wyświetlić składnię JSON dla typów zasobów, zobacz [Define resources in Azure Resource Manager templates](/azure/templates/) (Definiowanie zasobów w szablonach usługi Azure Resource Manager).

W usłudze Resource Manager szablon jest przetwarzany jak wszystkie pozostałe żądania (zobacz ilustrację przedstawiającą [Spójną warstwę zarządzania](#consistent-management-layer)). Szablon jest analizowany, a jego składnia zostaje poddana konwersji do operacji interfejsu API REST dla odpowiednich dostawców zasobów. Na przykład, gdy usługa Resource Manager odbiera szablon o następującej definicji zasobu:

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

Sposób definiowania szablonów i grup zasobów zależy wyłącznie od użytkownika, podobnie jak sposób zarządzania rozwiązaniem. Można na przykład wdrożyć aplikację trójwarstwową za pomocą jednego szablonu do pojedynczej grupy zasobów.

![szablon trójwarstwowy](./media/resource-group-overview/3-tier-template.png)

Nie trzeba jednak definiować całej infrastruktury w jednym szablonie. Często dobrym rozwiązaniem jest podział wymagań dotyczących wdrożenia na szablony przeznaczone do określonego celu. Te szablony mogą bez problemu być używane wielokrotnie w różnych rozwiązaniach. Aby wdrożyć dane rozwiązanie, należy utworzyć szablon wzorcowy połączony ze wszystkimi wymaganymi szablonami. Na poniższej ilustracji przedstawiono sposób wdrażania rozwiązania trójwarstwowego za pomocą szablonu nadrzędnego, który zawiera trzy szablony zagnieżdżone.

![zagnieżdżony szablon warstwowy](./media/resource-group-overview/nested-tiers-template.png)

Jeśli przewidujesz, że warstwy będą miały osobne cykle, możesz wdrożyć trzy warstwy do osobnych grup zasobów. Należy pamiętać, że zasoby mogą nadal być powiązane z zasobami w innych grupach zasobów.

![szablon warstwowy](./media/resource-group-overview/tier-templates.png)

Informacje dotyczące szablonów zagnieżdżonych można znaleźć w temacie [Using linked templates with Azure Resource Manager](resource-group-linked-templates.md) (Używanie szablonów połączonych w usłudze Azure Resource Manager).

Usługa Azure Resource Manager analizuje zależności i sprawdza, czy zasoby są tworzone we właściwej kolejności. Jeśli jeden zasób opiera się na wartości z innego zasobu (na przykład maszyna wirtualna wymagająca konta magazynu na potrzeby dysków), ustawiana jest zależność. Aby uzyskać więcej informacji, zobacz [Defining dependencies in Azure Resource Manager templates](resource-group-define-dependencies.md) (Definiowanie zależności w szablonach usługi Azure Resource Manager).

Szablonu można także używać w celu aktualizacji infrastruktury. Można na przykład dodać zasób do rozwiązania lub dodać reguły konfiguracji dla już wdrożonych zasobów. Jeśli szablon służy do utworzenia zasobu, ale ten zasób już istnieje, usługa Azure Resource Manager przeprowadzi aktualizację, zamiast tworzyć nowy element zawartości. Usługa Azure Resource Manager zaktualizuje istniejący zasób do stanu określonego dla nowego zasobu.  

Usługa Resource Manager zapewnia rozszerzenia na potrzeby sytuacji, gdy potrzebne są dodatkowe operacje, które nie są uwzględnione w konfiguracji (np. zainstalowanie konkretnego oprogramowania). Jeśli używasz już usługi do zarządzania konfiguracją, takiej jak DSC, Chef lub Puppet, dzięki rozszerzeniom możesz z nią dalej bez przeszkód pracować. Aby uzyskać informacje o rozszerzeniach i funkcjach maszyn wirtualnych, zobacz [Informacje o rozszerzeniach i funkcjach maszyn wirtualnych](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Ponadto szablon staje się częścią kodu źródłowego aplikacji. Można go zaewidencjonować w repozytorium kodu źródłowego i aktualizować w miarę rozwijania aplikacji. Do edycji szablonu można używać programu Visual Studio.

Po zdefiniowaniu szablonu można przystąpić do wdrażania zasobów na platformie Azure. Aby poznać polecenia służące do wdrażania zasobów, zobacz:

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](resource-group-template-deploy.md)
* [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](resource-group-template-deploy-cli.md)
* [Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)](resource-group-template-deploy-portal.md)
* [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](resource-group-template-deploy-rest.md)

## <a name="tags"></a>Tagi
Usługa Resource Manager udostępnia funkcję tagowania umożliwiającą dzielenie zasobów na kategorie zgodnie z wymaganiami zarządzania lub rozliczeń. Tagi są przydatne w przypadku złożonych kolekcji grup zasobów i zasobów, które trzeba uporządkować wizualnie w możliwie logicznej formie. Można na przykład oznaczyć tagami zasoby, które pełnią podobną rolę w organizacji lub należą do tego samego działu. Bez użycia tagów użytkownicy w organizacji mogą tworzyć wiele zasobów, które będą później bardzo trudne do znalezienia i zarządzania. Na przykład możesz chcieć usunąć wszystkie zasoby dla określonego projektu. Jeśli te zasoby nie są opatrzone tagiem dla projektu, trzeba je znaleźć ręcznie. Tagowanie może być istotnym sposobem na zredukowanie niepotrzebnych kosztów w ramach subskrypcji. 

Zasoby mogą być oznaczone tym samym tagiem, nawet jeśli nie znajdują się w tej samej grupie zasobów. Można utworzyć własną taksonomię tagów, aby mieć pewność, że wszyscy użytkownicy w organizacji używają tych samych tagów. Dzięki temu uniknie się pomyłek wynikających z użycia podobnych tagów (na przykład „wydział” zamiast „dział”).

W poniższym przykładzie przedstawiono tag zastosowany względem maszyny wirtualnej.

```json
"resources": [    
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2015-06-15",
    "name": "SimpleWindowsVM",
    "location": "[resourceGroup().location]",
    "tags": {
        "costCenter": "Finance"
    },
    ...
  }
]
```

Aby pobrać wszystkie zasoby z wartością tagu, użyj następującego polecenia cmdlet programu PowerShell:

```powershell
Find-AzureRmResource -TagName costCenter -TagValue Finance
```

Innym rozwiązaniem jest użycie następującego polecenia interfejsu wiersza polecenia platformy Azure w wersji 2.0:

```azurecli
az resource list --tag costCenter=Finance
```

Oznakowane zasoby można również wyświetlić za pośrednictwem witryny Azure Portal.

[Raport użycia](../billing/billing-understand-your-bill.md) dla subskrypcji obejmuje nazwy i wartości tagów, co pozwala na rozbicie kosztów według tagów. Aby uzyskać więcej informacji na temat tagów, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](resource-group-using-tags.md).

## <a name="access-control"></a>Kontrola dostępu
Usługa Resource Manager pozwala kontrolować, kto może wykonywać określone czynności w organizacji. Zapewnia ona natywną integrację kontroli dostępu opartej na rolach (RBAC, role-based access control) z platformą zarządzania, umożliwiając stosowanie kontroli dostępu do wszystkich usług w grupie zasobów. 

W przypadku pracy przy użyciu kontroli dostępu opartej na rolach warto zapoznać się z dwoma najważniejszymi pojęciami:

* Definicje ról — opisują zestaw uprawnień i można ich używać w wielu przypisaniach.
* Przypisania ról — kojarzą definicję z tożsamością (użytkownikiem lub grupą) dla określonego zakresu (subskrypcji, grupy zasobów lub zasobu). Przypisanie jest dziedziczone przez niższe zakresy.

Można dodać użytkowników do wstępnie zdefiniowanej platformy i ról określonych zasobów. Na przykład można skorzystać ze wstępnie zdefiniowanej roli o nazwie Czytelnik, która pozwala użytkownikom na wyświetlanie zasobów, ale nie na ich zmianę. Wystarczy dodać użytkowników w organizacji, którzy potrzebują tego typu dostępu, do roli Czytelnik i zastosować rolę do subskrypcji, grupy zasobów lub zasobu.

Platforma Azure udostępnia cztery następujące role platformy:

1. Właściciel — może zarządzać wszystkim, łącznie z dostępem
2. Współautor — może zarządzać wszystkim oprócz dostępu
3. Czytelnik — może przeglądać wszystko, ale nie może wprowadzać zmian
4. Administrator dostępu użytkownika — może zarządzać dostępem użytkowników do zasobów platformy Azure

Platforma Azure udostępnia kilka ról specyficznych dla zasobów. Niektóre typowe z nich to:

1. Współautor maszyny wirtualnej — może zarządzać maszynami wirtualnymi, ale nie może udzielać dostępu do nich ani nie może zarządzać siecią wirtualną lub kontem magazynu, do którego te maszyny wirtualne są podłączone
2. Współautor sieci — może zarządzać wszystkimi zasobami sieci, ale nie może udzielać dostępu do nich
3. Współautor konta magazynu — może zarządzać kontami magazynu, ale nie może udzielać dostępu do nich
4. Współautor serwera SQL — może zarządzać bazami danych i serwerami SQL, ale nie ich zasadami związanymi z zabezpieczeniami
5. Współautor witryny sieci Web — może zarządzać witrynami sieci Web, ale nie planami sieci Web, do których są podłączone

Aby uzyskać pełną listę ról i dozwolonych akcji, zobacz [Kontrola dostępu oparta na rolach (RBAC): wbudowane role](../role-based-access-control/built-in-roles.md). Aby uzyskać więcej informacji na temat kontroli dostępu na podstawie ról, zobacz temat [Azure Role-Based Access Control](../role-based-access-control/role-assignments-portal.md) (Kontrola dostępu na podstawie ról na platformie Azure). 

W niektórych przypadkach istnieje potrzeba uruchomienia kodu lub skryptu, który uzyskuje dostęp do zasobów, ale nie ma zostać uruchomiony w ramach poświadczeń użytkownika. Zamiast tego ma zostać utworzona jednostka usługi dla aplikacji. Do tej jednostki usługi ma następnie zostać przypisana odpowiednia rola. Usługa Resource Manager umożliwia tworzenie poświadczeń dla aplikacji i programowe uwierzytelnianie aplikacji. Aby dowiedzieć się więcej o tworzeniu jednostek usług, zobacz jeden z następujących tematów:

* [Use Azure PowerShell to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu programu Azure PowerShell)](resource-group-authenticate-service-principal.md)
* [Use Azure CLI to create a service principal to access resources (Tworzenie jednostki usługi używanej do uzyskiwania dostępu do zasobów przy użyciu interfejsu wiersza polecenia platformy Azure)](resource-group-authenticate-service-principal-cli.md)
* [Use portal to create Azure Active Directory application and service principal that can access resources (Używanie portalu do tworzenia aplikacji usługi Azure Active Directory i jednostki usługi używanej do uzyskiwania dostępu do zasobów)](resource-group-create-service-principal-portal.md)

Można również jawnie zablokować dostęp do kluczowych zasobów, aby uniemożliwić użytkownikom ich usuwanie i modyfikowanie. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="activity-logs"></a>Dzienniki aktywności
Usługa Resource Manager rejestruje wszystkie operacje służące do tworzenia, modyfikowania lub usuwania zasobu. Dzienników aktywności można użyć do znalezienia błędu podczas rozwiązywania problemów lub do monitorowania sposobu, w jaki zasób został zmodyfikowany przez użytkownika w organizacji. Aby wyświetlić dzienniki, wybierz pozycję **Dzienniki aktywności** w bloku **Ustawienia** dla grupy zasobów. Dzienniki można filtrować według wielu różnych wartości, w tym według użytkownika, który zainicjował operację. Aby uzyskać informacje dotyczące pracy z dziennikami aktywności, zobacz [View activity logs to manage Azure resources](resource-group-audit.md) (Wyświetlanie dzienników aktywności w celu zarządzania zasobami platformy Azure).

## <a name="customized-policies"></a>Zasady niestandardowe
Usługa Resource Manager umożliwia tworzenie zasad niestandardowych na potrzeby zarządzania zasobami. Typy tworzonych zasad mogą obejmować różne scenariusze. Można wymusić konwencję nazewnictwa zasobów, ograniczyć typy i wystąpienia zasobów, które można wdrożyć, lub wprowadzić ograniczenia dotyczące regionów, które mogą hostować dany typ zasobu. Można wymagać wartości tagu dla zasobów w celu organizowania rozliczania według działów. Tworzenie zasad umożliwia obniżenie kosztów i zachowanie spójności w ramach subskrypcji. 

Zasady są definiowane za pomocą pliku JSON, a następnie stosowane w ramach subskrypcji lub grupy zasobów. Zasady są inne niż kontrola dostępu oparta na rolach, ponieważ są stosowane względem typów zasobów.

W poniższym przykładzie przedstawiono zasady, które zapewniają spójność tagów dzięki określeniu, że wszystkie zasoby zawierają tag costCenter.

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

Można utworzyć o wiele więcej typów zasad. Aby uzyskać więcej informacji, zobacz artykuł [Co to jest usługa Azure Policy?](../azure-policy/azure-policy-introduction.md).

## <a name="sdks"></a>Zestawy SDK
Zestawy Azure SDK są dostępne dla wielu języków i platform. Implementacje dla poszczególnych języków są dostępne za pośrednictwem menedżera pakietów danego ekosystemu oraz w usłudze GitHub.

Oto nasze repozytoria zestawów SDK typu open source. Zachęcamy do wysyłania opinii, zgłaszania problemów i przesyłania żądań ściągnięcia.

* [Zestaw Azure SDK dla platformy .NET](https://github.com/Azure/azure-sdk-for-net)
* [Biblioteki zarządzania Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java)
* [Zestaw Azure SDK dla środowiska Node.js](https://github.com/Azure/azure-sdk-for-node)
* [Zestaw Azure SDK dla środowiska PHP](https://github.com/Azure/azure-sdk-for-php)
* [Zestaw Azure SDK dla środowiska Python](https://github.com/Azure/azure-sdk-for-python)
* [Zestaw Azure SDK dla środowiska Ruby](https://github.com/Azure/azure-sdk-for-ruby)

Aby uzyskać informacje na temat korzystania z tych języków do obsługi zasobów, zobacz:

* [Azure dla deweloperów platformy .NET](/dotnet/azure/?view=azure-dotnet)
* [Azure dla deweloperów języka Java](/java/azure/)
* [Azure dla deweloperów oprogramowania Node.js](/nodejs/azure/)
* [Azure dla deweloperów języka Python](/python/azure/)

> [!NOTE]
> Jeśli dany zestaw SDK nie udostępnia wymaganych funkcji, możesz również bezpośrednio wywołać [interfejs API REST platformy Azure](https://docs.microsoft.com/rest/api/resources/).
> 
> 

## <a name="next-steps"></a>Następne kroki
* Artykuł [Eksportowanie szablonu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md) zawiera proste instrukcje dotyczące pracy z szablonami.
* Bardziej szczegółowe instrukcje dotyczące tworzenia szablonu zawiera artykuł [Tworzenie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md).
* Aby poznać funkcje, których można użyć w szablonie, zobacz [Template functions](resource-group-template-functions.md) (Funkcje szablonu).
* Aby uzyskać informacje dotyczące korzystania z programu Visual Studio w połączeniu z usługą Resource Manager, zobacz [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

Oto film z omówieniem tego zagadnienia:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Azure-Resource-Manager-Overview/player]


[powershellref]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/azurerm.resources
