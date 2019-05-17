---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure Policy | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć usługi Azure Monitor dla maszyn wirtualnych dla wielu maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu usługi Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: 9664ad5272ffeb55bd85e3d4c4f4b70d05e97702
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524148"
---
# <a name="enable-azure-monitor-for-vms-preview-using-azure-policy"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu usługi Azure Policy

W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu usługi Azure Policy. Po zakończeniu tego procesu zostaną pomyślnie skonfigurowane, umożliwiające usłudze Log Analytics oraz agenci zależności i zidentyfikowaliśmy maszyny wirtualne, które nie są zgodne.

Odkryj, zarządzanie i włączyć usługi Azure Monitor dla maszyn wirtualnych dla wszystkich maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych, można użyć zasad platformy Azure lub programu Azure PowerShell. Usługa Azure Policy jest zalecane, ponieważ zarządzasz definicje zasad, aby skutecznie zarządzać subskrypcji w celu zapewnienia zgodności spójne i automatyczne włączanie nowo zainicjowano obsługę administracyjną maszyn wirtualnych. Te definicje zasad:

* Wdróż agenta usługi Log Analytics i agenta zależności.
* Raport dotyczący wyniki sprawdzania zgodności.
* Korygowanie dla niezgodnych maszyn wirtualnych.

Jeśli interesuje Cię w realizacji tego zadania przy użyciu szablonu programu Azure PowerShell lub usługi Azure Resource Manager, zobacz [włączyć przy użyciu szablonu programu Azure PowerShell lub Menedżera zasobów](vminsights-enable-at-scale-powershell.md). 

## <a name="manage-policy-coverage-feature-overview"></a>Omówienie funkcji pokrycia zasad zarządzania

Pierwotnie doświadczenie z usługą Azure Policy do zarządzania i wdrażania definicje zasad dla usługi Azure Monitor dla maszyn wirtualnych została wykonana wyłącznie z usługi Azure Policy. Za pomocą **pokrycia zasad zarządzania** funkcji, ponieważ dzięki przyspiesza i ułatwia odnajdywanie, zarządzanie i włączyć na dużą skalę **włączyć usługi Azure Monitor dla maszyn wirtualnych** inicjatywy, która zawiera definicje zasad wymienione wcześniej. Dostęp ta nowa funkcja z **wprowadzenie** kartę w usłudze Azure Monitor dla maszyn wirtualnych przez wybranie **pokrycia zasad zarządzania**. Zostanie otwarta strona pokrycia zasad maszyn wirtualnych. 

![Usługa Azure Monitor na karcie wprowadzenie do maszyn wirtualnych](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

W tym miejscu możesz można sprawdzić i zarządzać pokrycia dla inicjatywy w subskrypcjach i grupach zarządzania, na których, a także zrozumieć, jak wiele maszyn wirtualnych istnieją w każdej z grup zarządzania i subskrypcje oraz ich stan zgodności.   

![Usługa Azure Monitor na stronę z zasadami zarządzania maszynami wirtualnymi](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Informacje te są przydatne do planowania i wykonywania scenariusz zarządzania dla usługi Azure Monitor dla maszyn wirtualnych w jednej centralnej lokalizacji. Gdy usługi Azure Policy zawiera widok zgodności, po przypisaniu zasad/inicjatywy do zakresu, przy użyciu tej nowej strony można wykryć, gdzie zasad/inicjatywy nie jest przypisany i przypisać ją w miejscu. Wszystkie akcje (przypisywanie, wyświetlanie, edytowanie) przekierowanie do usługi Azure Policy bezpośrednio. Usługa Azure Monitor dla strony pokrycia zasad maszyn wirtualnych jest rozwinięte i zintegrowane środowisko dla tylko inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych**. 

Na tej stronie można również skonfigurować obszar roboczy usługi Log Analytics dla usługi Azure Monitor dla maszyn wirtualnych, który wykonuje następujące czynności:

- Instaluje rozwiązania mapy usługi instalacji i Infrastructure Insights
- Umożliwia liczników wydajności systemu operacyjnego używany przez wykresy wydajności, skoroszyty i dzienników niestandardowych kwerend i alerty.

![Usługa Azure Monitor dla maszyn wirtualnych Konfiguruj obszar roboczy](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ta opcja nie jest powiązane z wszystkie akcje zasad i zapewnia łatwy sposób spełniają [wymagania wstępne](vminsights-enable-overview.md) wymagany do włączenia usługi Azure Monitor dla maszyn wirtualnych.  

### <a name="what-information-is-available-on-this-page"></a>Jakie informacje są dostępne na tej stronie?
Poniższa tabela zawiera podział, jakie informacje są prezentowane w stronę pokrycia zasad i jak go zinterpretować.

| Funkcja | Opis | 
|----------|-------------| 
| **Zakres** | Grupa zarządzania i subskrypcje, do których masz lub dziedziczone dostęp do hierarchią grup, przechodzenie do szczegółów za pośrednictwem funkcji zarządzania.|
| **Rola** | Twoja Rola do zakresu, może być czytnika, współautora lub właściciela. W niektórych przypadkach go może pojawić się puste, aby wskazać, że masz dostęp do subskrypcji, ale nie w grupie zarządzania należy on do. Informacji w innych kolumnach różnią się w zależności od Twojej roli, ponieważ klucz w określeniu, jakie dane można zobaczyć i akcje możesz wykonać pod względem przypisanie inicjatywy/zasad (właściciela), edytując je lub wyświetlania zgodności. |
| **Łączna liczba maszyn wirtualnych** | Liczba maszyn wirtualnych w ramach tego zakresu. Dla grupy zarządzania jest sumą zagnieżdżony w subskrypcji i/lub podrzędną grupę zarządzania maszynami wirtualnymi. |
| **Zakres przypisania** | Procent maszyn wirtualnych, które są objęte zasad/inicjatywy. |
| **Stan przypisania** | W tej kolumnie, można znaleźć informacje o stanie zasad / inicjatywy przypisania. |
| **Zgodne maszyny wirtualne** | Liczba maszyn wirtualnych, które są niezgodne z zasadami/inicjatywy. Dla tej inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych** jest to liczba maszyn wirtualnych, które posiadają agenta usługi Log Analytics oraz agenta zależności. W niektórych przypadkach może występować puste ze względu na nie przypisania lub żadnych maszyn wirtualnych lub Brak wystarczających uprawnień. Informacje są udostępniane w ramach stanu zgodności. |
| **Zgodność** | Ogólna liczba zgodności jest sumą różne zasoby, które są zgodne, podzielona przez sumę wszystkich odrębne zasoby. |
| **Stan zgodności** | Informacji na temat stanu zgodności zasad / inicjatywy przypisania.|

Podczas przypisywania inicjatywy/zasady zakresu wybrane przypisania można zakres na liście lub ich część. Na przykład które mogły zostać utworzone przydziału dla subskrypcji (zakres zasad) i nie grupy zarządzania (zakres pokrycia). W tym przypadku wartość **pokrycia przypisania** wskazuje zakres maszyn wirtualnych w zasadach (lub rozpoczęciu nowej inicjatywy) podzielony przez maszyny wirtualne w zakresie pokrycia. W innym przypadku może wykluczono niektóre maszyny wirtualne, grupy zasobów lub subskrypcji z zakresu zasad. Jeśli wartość jest pusta, oznacza to, że zasady/inicjatywy nie istnieje albo nie masz uprawnień (informacje znajdują się w stan przypisania).

## <a name="enable-using-azure-policy"></a>Włączanie obsługi usługi Azure Policy

Aby włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure Policy w dzierżawie:

- Przypisz inicjatywę do zakresu: grupy zarządzania, subskrypcji lub grupy zasobów
- Przejrzyj i Skoryguj wyniki sprawdzania zgodności

Aby uzyskać więcej informacji na temat przypisywania zasad platformy Azure, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md#policy-assignment) i przejrzyj [Przegląd grup zarządzania](../../governance/management-groups/index.md) przed kontynuowaniem.

### <a name="policies-for-azure-vms"></a>Zasady dla maszyn wirtualnych platformy Azure

Definicje zasad na Maszynie wirtualnej platformy Azure są wymienione w poniższej tabeli:

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|[Wersja zapoznawcza]: Włączanie usługi Azure Monitor dla maszyn wirtualnych |Włączanie usługi Azure Monitor dla maszyn wirtualnych (VM) z określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów). Obszar roboczy usługi Log Analytics przyjmuje jako parametr. |Inicjatywa |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agentów inspekcji Log Analytics — obrazu maszyny Wirtualnej (OS) nieznajdujące się na liście |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności maszyn wirtualnych systemu Linux |Wdrażanie agenta zależności maszyn wirtualnych systemu Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności dla maszyn wirtualnych Windows |Wdróż agenta zależności Windows VMs, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowana na liście i nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent na maszynach wirtualnych systemu Linux |Wdrażanie Log Analytics Agent maszyn wirtualnych systemu Linux, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent for Windows VMs |Wdróż Log Analytics Agent dla Windows maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowana na liście i nie zainstalowano agenta. |Zasady |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zestawy skalowania zasad dla maszyn wirtualnych platformy Azure

Definicje zasad dla zestawu skalowania maszyn wirtualnych platformy Azure są wymienione w poniższej tabeli:

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|[Wersja zapoznawcza]: Włączanie usługi Azure Monitor dla usługi VM Scale Sets (zestawu skalowania maszyn wirtualnych) |Włączanie usługi Azure Monitor dla zestawów skalowania maszyn wirtualnych z określonego zakresu (Zarządzanie grupy, subskrypcji lub grupy zasobów). Pobiera obszar roboczy usługi Log Analytics jako parametr. Uwaga: Jeśli Twoja upgradePolicy zestaw skalowania jest ustawiony na ręczny, należy zastosować rozszerzenie do wszystkich maszyn wirtualnych w zestawie przez wywołanie metody uaktualniania na nich. W interfejsie wiersza polecenia to az vmss update-instances. |Inicjatywa |
|[Wersja zapoznawcza]: Inspekcja wdrożenie agenta zależności w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście |Ustaw jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście i agenta skalowania maszyn wirtualnych z raportów nie jest zainstalowany. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agentów analizy dziennika inspekcji w zestawu skalowania maszyn wirtualnych — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście |Ustaw jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście i agenta skalowania maszyn wirtualnych z raportów nie jest zainstalowany. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności zestawów skalowania maszyn wirtualnych systemu Linux (zestawu skalowania maszyn wirtualnych) |Wdróż agenta zależności dla zestawów skalowania maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta systemu Linux. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie agenta zależności zestawów skalowania maszyn wirtualnych systemu Windows (zestawu skalowania maszyn wirtualnych) |Wdrażanie zestawów skalowania maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie jest zainstalowany agent programu Windows dla dla agenta zależności. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent for Linux VM Scale Sets (zestawu skalowania maszyn wirtualnych) |Wdróż agenta programu Log Analytics dla zestawów skalowania maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta systemu Linux. |Zasady |
|[Wersja zapoznawcza]: Wdrażanie programu Log Analytics Agent Windows VM Scale Sets (zestawu skalowania maszyn wirtualnych) |Wdróż Log Analytics Agent dla Windows zestawów skalowania maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta. |Zasady |

Zasady autonomicznym (nie dołączona inicjatywy) opisano tutaj:

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|[Wersja zapoznawcza]: Inspekcja Analytics obszar roboczy usługi Log dla maszyny Wirtualnej — niezgodność raportu |Raportu maszyn wirtualnych jako niezgodne, jeśli nie są one rejestrowanie do obszaru roboczego usługi Log Analytics w przypisaniu zasad/inicjatywy. |Zasady |

### <a name="assign-the-azure-monitor-initiative"></a>Przypisz inicjatywę usługi Azure Monitor
Aby utworzyć przypisanie zasad z usługi Azure Monitor dla strony pokrycia zasad maszyn wirtualnych, wykonaj następujące czynności. Aby dowiedzieć się, jak wykonać następujące czynności, zobacz [Tworzenie przypisania zasad w witrynie Azure portal](../../governance/policy/assign-policy-portal.md).

Podczas przypisywania inicjatywy/zasady zakresu wybrane przypisania może być zakresem wymienione w tym miejscu lub ich część. Na przykład może utworzono przypisania dla subskrypcji (zakres zasad) a nie grupą zarządzania (pokrycia zasięg) w którym % przypadków pokrycie wskazuje zakres maszyn wirtualnych w zasadach (lub rozpoczęciu nowej inicjatywy) podzielony przez maszyny wirtualne w zakresie pokrycia. W niektórych przypadkach może wykluczono niektóre maszyny wirtualne lub RGs lub subskrypcji z zakresu zasad.  W przypadku, gdy jest on pusty, oznacza to, że zasada / inicjatywy nie istnieje lub nie masz uprawnień (informacje podane w stan przypisania)  

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W witrynie Azure portal wybierz **Monitor**. 

3. Wybierz **maszyny wirtualne (wersja zapoznawcza)** w **Insights** sekcji.
 
4. Wybierz **wprowadzenie** , a następnie na stronie wybierz pozycję **pokrycia zasad zarządzania**.

5. Wybierz grupę zarządzania lub subskrypcji z tabeli, a następnie wybierz pozycję **zakres** , klikając przycisk wielokropka (...).      W tym przykładzie zakres ogranicza przypisania zasad w celu zgrupowania maszyn wirtualnych do wymuszania.

6. Na **przydziału usługi Azure Policy** strony jest wstępnie wypełniona przy użyciu tej inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych**. 
    **Nazwa przypisania** okno zostanie automatycznie wypełniona nazwę inicjatywy, ale można go zmienić. Można również dodać opcjonalny opis. **Przypisany przez** pole jest wypełniane automatycznie w oparciu kto jest zalogowany, a ta wartość jest opcjonalna.

7. (Opcjonalnie) Aby usunąć jeden lub więcej zasobów z zakresu, zaznacz **wykluczenia**.

8. W **obszaru roboczego usługi Log Analytics** listy rozwijanej liście dla obsługiwany region, wybierz obszar roboczy.

   > [!NOTE]
   > Jeśli obszar roboczy znajduje się poza zakresem przypisania, należy udzielić *Współautor usługi Log Analytics* uprawnień, aby identyfikator przypisania zasad podmiotu zabezpieczeń. Jeśli tego nie zrobisz, takich jak może zostać wyświetlony błąd wdrożenia: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Aby udzielić dostępu, zapoznaj się z [jak ręcznie skonfigurować tożsamość zarządzaną](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  **Tożsamości zarządzanej** pole wyboru jest zaznaczone, ponieważ inicjatywy przypisaniem zawiera zasady z *deployIfNotExists* efekt.
    
9. W **lokalizacji Zarządzanie tożsamościami** listy rozwijanej wybierz odpowiedni region.

10. Wybierz opcję **Przypisz**.

Po utworzeniu przypisania monitora platformy Azure dla maszyn wirtualnych pokrycia zasad strona zostanie zaktualizowana, pokrycia przydział, stan przypisania, zgodności maszyn wirtualnych i stan zgodności zgodnie ze zmianami. 

Następujące macierzy mapuje poszczególnymi stanami zgodności możliwe dla tej inicjatywy.  

| Stan zgodności | Opis | 
|------------------|-------------|
| **Zgodne** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych agentów usługi Log Analytics i zależności.|
| **Nie są zgodne** | Nie wszystkie maszyny wirtualne w zakresie mają usługi Log Analytics oraz agenci zależności wdrożonych i może wymagać korygowania.|
| **Nie uruchomiono** | Dodano nowy przydział. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Bez przypisanych zasad. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela, aby zapewnić dostęp lub zgodności oraz zarządzać przypisaniami za pośrednictwem podrzędne grupy zarządzania lub subskrypcji. 

Poniższa tabela zawiera mapowanie każdy stan możliwe przypisanie inicjatywy.

| Stan przydziału | Opis | 
|------------------|-------------|
| **Powodzenie** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych agentów usługi Log Analytics i zależności.|
| **Ostrzeżenie** | Subskrypcja nie jest w grupie zarządzania.|
| **Nie uruchomiono** | Dodano nowy przydział. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Istnieje żadnych maszyn wirtualnych lub zasady nie zostały przypisane. | 
| **Akcja** | Przypisywanie zasad lub edytowanie przypisania | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela, aby zapewnić dostęp lub zgodności oraz zarządzać przypisaniami za pośrednictwem podrzędne grupy zarządzania lub subskrypcji. 

## <a name="review-and-remediate-the-compliance-results"></a>Przejrzyj i Skoryguj wyniki sprawdzania zgodności

Poniższy przykład jest na Maszynie wirtualnej platformy Azure, ale ma również zastosowanie do zestawów skalowania maszyn wirtualnych. Możesz dowiedzieć się, jak można przejrzeć wyniki sprawdzania zgodności, czytając [zidentyfikować wyniki niezgodności](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). W usłudze Azure Monitor dla strony pokrycia zasad maszyn wirtualnych wybierz grupę zarządzania lub subskrypcji z tabeli, a następnie wybierz **Wyświetl informacje o zgodności** , klikając przycisk wielokropka (...).   

![Zgodność z zasadami dla maszyn wirtualnych platformy Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Na podstawie wyników zasad dołączone do tej inicjatywy, maszyny wirtualne są zgłaszane jako niezgodne w następujących scenariuszach:

* Nie jest wdrażana usługa log Analytics lub agenta zależności.  
    Ten scenariusz jest typowy dla zakresu przy użyciu istniejących maszyn wirtualnych. Aby go unikać, należy wdrożyć agentów wymaganych przez [tworzenie zadań korygowania](../../governance/policy/how-to/remediate-resources.md) niezgodne zasady.  
    - [Wersja zapoznawcza]: Deploy Dependency Agent for Linux VMs
    - [Wersja zapoznawcza]: Deploy Dependency Agent for Windows VMs
    - [Wersja zapoznawcza]: Deploy Log Analytics Agent for Linux VMs
    - [Wersja zapoznawcza]: Deploy Log Analytics Agent for Windows VMs

* Obraz maszyny Wirtualnej (OS) nie jest określona w definicji zasad.  
    Zasady wdrażania obejmują tylko maszyny wirtualne, które są wdrażane z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zapoznaj się z dokumentacją, aby zobaczyć, czy system operacyjny maszyny Wirtualnej jest obsługiwana. Jeśli nie jest obsługiwany, zduplikowania zasady wdrażania i aktualizacji lub zmodyfikuj go, aby utworzyć obraz zgodne.  
    - [Wersja zapoznawcza]: Wdrażanie agenta zależności inspekcji — obraz maszyny Wirtualnej (OS) nieznajdujące się na liście
    - [Wersja zapoznawcza]: Wdrażanie agentów inspekcji Log Analytics — obrazu maszyny Wirtualnej (OS) nieznajdujące się na liście

* Maszyny wirtualne nie są zalogowaniu się do określonego obszaru roboczego usługi Log Analytics.  
    Istnieje możliwość logowania niektórych maszyn wirtualnych w zakresie inicjatywy do obszaru roboczego usługi Log Analytics w innych niż ten, który jest określony w przypisaniu zasad. Ta zasada jest narzędziem, aby identyfikować, które maszyny wirtualne zgłaszanej niezgodny z obszarem roboczym.  
    - [Wersja zapoznawcza]: Audit Log Analytics Workspace for VM - Report Mismatch

## <a name="edit-initiative-assignment"></a>Edytuj przypisanie inicjatywy

W dowolnym momencie po przypisaniu inicjatywy do grupy zarządzania lub subskrypcji można edytować, aby zmodyfikować następujące właściwości:

- Nazwa przydziału
- Opis
- Przypisane przez
- Obszar roboczy usługi Log Analytics
- Wyjątki

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). Aby zidentyfikować wąskie gardła i ogólnego użycia za pomocą wydajność maszyn wirtualnych, zobacz [wydajność maszyny Wirtualnej platformy Azure w widoku](vminsights-performance.md), lub aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych jest mapowany](vminsights-maps.md).