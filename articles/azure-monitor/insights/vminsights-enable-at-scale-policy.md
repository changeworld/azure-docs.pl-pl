---
title: Włączanie usługi Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure Policy | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć usługi Azure Monitor dla maszyn wirtualnych, wiele maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu usługi Azure Policy.
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
ms.openlocfilehash: cf06004c70609dbea59a47b207e3568299260a82
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594448"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu usługi Azure Policy

W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych przy użyciu usługi Azure Policy. Po zakończeniu tego procesu zostaną pomyślnie skonfigurowane, umożliwiające usłudze Log Analytics oraz agenci zależności i zidentyfikowaliśmy maszyny wirtualne, które nie są zgodne.

Odkryj, zarządzanie i włączyć usługi Azure Monitor dla maszyn wirtualnych dla wszystkich maszyn wirtualnych platformy Azure lub zestawy skalowania maszyn wirtualnych, można użyć zasad platformy Azure lub programu Azure PowerShell. Usługa Azure Policy jest metodę, którą firma Microsoft zaleca, ponieważ zarządzasz definicje zasad, aby skutecznie zarządzać subskrypcji w celu zapewnienia zgodności spójne i automatyczne włączanie nowo zainicjowano obsługę administracyjną maszyn wirtualnych. Te definicje zasad:

* Wdróż agenta usługi Log Analytics i agenta zależności.
* Raport dotyczący wyniki sprawdzania zgodności.
* Korygowanie dla niezgodnych maszyn wirtualnych.

Jeśli interesuje Cię wykonywanie tych zadań za pomocą programu Azure PowerShell lub szablonu usługi Azure Resource Manager, zobacz [włączyć usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu programu Azure PowerShell lub usługi Azure Resource Manager szablonów](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Omówienie funkcji pokrycia zasad zarządzania

Początkowo doświadczenie z usługą Azure Policy do zarządzania i wdrażania definicje zasad dla usługi Azure Monitor dla maszyn wirtualnych było to wyłącznie z usługi Azure Policy. Funkcja zarządzania pokrycia zasad sprawia, że przyspiesza i ułatwia odnajdywanie, zarządzanie i włączyć na dużą skalę **włączyć usługi Azure Monitor dla maszyn wirtualnych** inicjatywy, która zawiera definicje zasad wymienione wcześniej. Dostęp ta nowa funkcja z **wprowadzenie** kartę w usłudze Azure Monitor dla maszyn wirtualnych. Wybierz **pokrycia zasad zarządzania** otworzyć **usługi Azure Monitor na potrzeby pokrycia zasad maszyn wirtualnych** strony.

![Usługa Azure Monitor na karcie wprowadzenie do maszyn wirtualnych](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

W tym miejscu możesz sprawdzić i zarządzania pokrycia dla inicjatywy w subskrypcjach i grupach zarządzania, na których. Możesz zrozumieć, jak wiele maszyn wirtualnych istnieją w każdej z grup zarządzania i subskrypcje oraz ich stan zgodności.

![Usługa Azure Monitor na stronę z zasadami zarządzania maszynami wirtualnymi](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Te informacje są przydatne do planowania i wykonywania scenariusz zarządzania dla usługi Azure Monitor dla maszyn wirtualnych w jednej centralnej lokalizacji. Gdy usługi Azure Policy zawiera widok zgodności, po przypisaniu zasad lub inicjatywy do zakresu, przy użyciu tej nowej strony można wykryć, gdy zasad lub inicjatywy nie jest przypisany i przypisać ją w miejscu. Wszystkie akcje, takie jak przypisywanie, widoków i Przekierowanie do usługi Azure Policy można edytować bezpośrednio. **Usługi Azure Monitor na potrzeby pokrycia zasad maszyn wirtualnych** strona jest rozwinięte i zintegrowane środowisko dla tylko inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych**.

Na tej stronie można także skonfigurować obszar roboczy usługi Log Analytics dla usługi Azure Monitor dla maszyn wirtualnych, które:

- Instaluje rozwiązania mapy usługi instalacji i Infrastructure Insights.
- Umożliwia liczników wydajności systemu operacyjnego używany przez wykresy wydajności, skoroszyty i dzienników niestandardowych kwerend i alerty.

![Usługa Azure Monitor dla maszyn wirtualnych Konfiguruj obszar roboczy](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ta opcja nie jest powiązany z wszelkie akcje zasad. Jest ona dostępna do umożliwiają łatwe do zaspokojenia [wymagania wstępne](vminsights-enable-overview.md) wymagany do włączenia usługi Azure Monitor dla maszyn wirtualnych.  

### <a name="what-information-is-available-on-this-page"></a>Jakie informacje są dostępne na tej stronie?
Poniższa tabela zawiera podział informacje wyświetlane na stronie pokrycia zasad i jak go zinterpretować.

| Funkcja | Opis | 
|----------|-------------| 
| **Zakres** | Grupa zarządzania i subskrypcje, do których masz lub dziedziczone dostęp do z możliwością przejść do szczegółów w hierarchii grupy zarządzania.|
| **Rola** | Twoja Rola do zakresu, który może być czytnika, właściciela lub współautora. W niektórych przypadkach może się pojawić puste, aby wskazać, że możesz mieć dostępu do subskrypcji, ale nie w grupie zarządzania należy do. Informacje przedstawione w innych kolumnach różni się w zależności od Twojej roli. Rola jest klucz w określeniu, jakie dane można zobaczyć i akcje możesz wykonać pod względem przypisywania zasad lub inicjatyw (właściciela), edytując je lub wyświetlania zgodności. |
| **Łączna liczba maszyn wirtualnych** | Liczba maszyn wirtualnych w ramach tego zakresu. Dla grupy zarządzania jest sumą zagnieżdżonych w ramach subskrypcji lub podrzędną grupę zarządzania maszynami wirtualnymi. |
| **Zakres przypisania** | Procent maszyn wirtualnych, które są objęte zasad lub inicjatywy. |
| **Stan przypisania** | Informacje dotyczące stanu przypisania zasad lub inicjatywy. |
| **Zgodne maszyny wirtualne** | Liczba maszyn wirtualnych, które są zgodne w ramach zasad lub inicjatywy. Dla tej inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych**, jest to liczba maszyn wirtualnych, które posiadają agenta usługi Log Analytics oraz agenta zależności. W niektórych przypadkach może się pojawić puste ze względu na nie przypisania, bez maszyn wirtualnych lub Brak wystarczających uprawnień. Informacje w obszarze **stan zgodności**. |
| **Zgodność** | Ogólna liczba zgodności jest sumą odrębne zasoby, które są zgodne, podzielona przez sumę wszystkich odrębne zasoby. |
| **Stan zgodności** | Informacje na temat stanu zgodności dla przypisania zasad lub inicjatywy.|

Po przypisaniu zasad lub inicjatywy zakresu wybrane przypisania można zakres na liście lub ich część. Na przykład może być utworzono przydziału dla subskrypcji (zakres zasad) i nie grupy zarządzania (zakres pokrycia). W tym przypadku wartość **pokrycia przypisania** wskazuje maszyn wirtualnych w zasad lub inicjatywy zakresu podzielona przez maszyny wirtualne w zakresie pokrycia. W innym przypadku może wykluczono niektóre maszyny wirtualne, grupy zasobów lub subskrypcji z zakresu zasad. Jeśli wartość jest pusta, oznacza to, że zasad lub inicjatywy nie istnieje albo nie masz uprawnień. Informacje w obszarze **stan przypisania**.

## <a name="enable-by-using-azure-policy"></a>Włączyć za pomocą usługi Azure Policy

Aby włączyć usługi Azure Monitor dla maszyn wirtualnych przy użyciu usługi Azure Policy w dzierżawie:

- Przypisz inicjatywę do zakresu: grupy zarządzania, subskrypcji lub grupy zasobów.
- Przejrzyj i Skoryguj wyniki sprawdzania zgodności.

Aby uzyskać więcej informacji na temat przypisywania zasad platformy Azure, zobacz [Omówienie usługi Azure Policy](../../governance/policy/overview.md#policy-assignment) i przejrzyj [Przegląd grup zarządzania](../../governance/management-groups/index.md) przed kontynuowaniem.

### <a name="policies-for-azure-vms"></a>Zasady dla maszyn wirtualnych platformy Azure

Definicje zasad na Maszynie wirtualnej platformy Azure są wymienione w poniższej tabeli.

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|\[Podgląd\]: Włączanie usługi Azure Monitor dla maszyn wirtualnych |Włączanie usługi Azure Monitor dla maszyn wirtualnych z określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów). Obszar roboczy usługi Log Analytics przyjmuje jako parametr. |Inicjatywa |
|\[Podgląd\]: Inspekcja wdrożenie agenta zależności — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS) |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|\[Podgląd\]: Inspekcja wdrożenie agenta usługi Log Analytics — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS) |Raporty maszyny wirtualne jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta. |Zasady |
|\[Podgląd\]: Wdrażanie agenta zależności maszyn wirtualnych systemu Linux |Jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie zainstalowano agenta, należy wdrożyć agenta zależności maszyn wirtualnych systemu Linux. |Zasady |
|\[Podgląd\]: Wdrażanie agenta zależności dla maszyn wirtualnych Windows |Jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie zainstalowano agenta, należy wdrożyć agenta zależności dla maszyn wirtualnych Windows. |Zasady |
|\[Podgląd\]: Wdrożyć agenta Log Analytics dla maszyn wirtualnych systemu Linux |Jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie jest zainstalowany agent programu, należy wdrożyć agenta Log Analytics dla maszyn wirtualnych systemu Linux. |Zasady |
|\[Podgląd\]: Wdróż agenta usługi Log Analytics dla maszyn wirtualnych Windows |Jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie zainstalowano agenta, należy wdrożyć agenta usługi Log Analytics dla maszyn wirtualnych Windows. |Zasady |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zestawy skalowania zasad dla maszyn wirtualnych platformy Azure

Definicje zasad dla zestawu skalowania maszyn wirtualnych platformy Azure są wymienione w poniższej tabeli.

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|\[Podgląd\]: Włączanie usługi Azure Monitor dla zestawów skalowania maszyn wirtualnych |Włączanie usługi Azure Monitor dla zestawów skalowania maszyn wirtualnych z określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów). Obszar roboczy usługi Log Analytics przyjmuje jako parametr. Uwaga: Jeśli zasady uaktualniania zestawu skalowania jest ustawiona na ręczny, zastosuj rozszerzenie do wszystkich maszyn wirtualnych w zestawie, przez wywołanie metody uaktualniania na nich. W interfejsie wiersza polecenia to az vmss update-instances. |Inicjatywa |
|\[Podgląd\]: Inspekcja wdrożenie agenta zależności w zestawach skalowania maszyn wirtualnych — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS) |Raporty zestawu jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta skalowania maszyn wirtualnych. |Zasady |
|\[Podgląd\]: Inspekcja wdrożenie agenta usługi Log Analytics w zestawach skalowania maszyn wirtualnych — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS) |Raporty zestawu jako niezgodne, jeśli obraz maszyny Wirtualnej (OS) nie jest zdefiniowana na liście, a nie zainstalowano agenta skalowania maszyn wirtualnych. |Zasady |
|\[Podgląd\]: Wdrażanie agenta zależności dla zestawów skalowania maszyn wirtualnych systemu Linux |Wdróż agenta zależności dla zestawów skalowania maszyn wirtualnych, jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie zainstalowano agenta systemu Linux. |Zasady |
|\[Podgląd\]: Wdrażanie agenta zależności dla zestawów skalowania maszyn wirtualnych Windows |Wdróż agenta zależności dla zestawów skalowania maszyn wirtualnych, jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie jest zainstalowany agent programu Windows. |Zasady |
|\[Podgląd\]: Wdrożyć agenta Log Analytics dla zestawów skalowania maszyn wirtualnych systemu Linux |Wdróż agenta usługi Log Analytics dla zestawów skalowania maszyn wirtualnych, jeśli obraz maszyny Wirtualnej (OS) jest zdefiniowany na liście, a nie zainstalowano agenta systemu Linux. |Zasady |
|\[Podgląd\]: Wdrożyć agenta Log Analytics dla zestawów skalowania maszyn wirtualnych Windows |Należy wdrożyć agenta Log Analytics dla zestawów skalowania maszyn wirtualnych, jeśli zdefiniowano obrazu maszyny Wirtualnej (systemu operacyjnego) na liście, a nie jest zainstalowany agent programu Windows. |Zasady |

Zasady autonomicznym (nie dołączona inicjatywy) opisano tutaj:

|Name (Nazwa) |Opis |Type |
|-----|------------|-----|
|\[Podgląd\]: Inspekcja obszaru roboczego analizy dzienników dla maszyny Wirtualnej — niezgodność raportu |Maszyny wirtualne raporcie jako niezgodne, jeśli nie są one rejestrowania do obszaru roboczego usługi Log Analytics w przypisaniu zasad lub inicjatywy. |Zasady |

### <a name="assign-the-azure-monitor-initiative"></a>Przypisz inicjatywę usługi Azure Monitor
Tworzenie przypisania zasad od **usługi Azure Monitor na potrzeby pokrycia zasad maszyn wirtualnych** strony, wykonaj następujące kroki. Aby dowiedzieć się, jak wykonać następujące czynności, zobacz [Tworzenie przypisania zasad w witrynie Azure portal](../../governance/policy/assign-policy-portal.md).

Po przypisaniu zasad lub inicjatywy zakresu wybrane przypisania może być zakresem wymienione w tym miejscu lub ich część. Na przykład zostały utworzone przypisanie do subskrypcji (zakres zasad), a nie grupą zarządzania (zakres pokrycia). W tym przypadku procent pokrycia wskazuje maszyn wirtualnych w zasadach lub w zakresie inicjatywy podzielona przez maszyny wirtualne w zakresie pokrycia. W innym przypadku może wykluczono niektóre maszyny wirtualne, lub grupy zasobów lub subskrypcji z zakresu zasad. Jeśli to pole jest puste, oznacza to, że zasad lub inicjatywy nie istnieje albo nie masz uprawnień. Informacje w obszarze **stan przypisania**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W witrynie Azure portal wybierz **Monitor**. 

3. Wybierz **maszyny wirtualne (wersja zapoznawcza)** w **Insights** sekcji.
 
4. Wybierz **wprowadzenie** kartę. Na stronie, wybierz **pokrycia zasad zarządzania**.

5. Wybierz grupę zarządzania lub subskrypcji z tabeli. Wybierz **zakres** , wybierając wielokropek (...). W tym przykładzie zakres ogranicza przypisania zasad w celu zgrupowania maszyn wirtualnych do wymuszania.

6. Na **przydziału usługi Azure Policy** stronie go ma wstępnie wypełniane przy użyciu tej inicjatywy **włączyć usługi Azure Monitor dla maszyn wirtualnych**. 
    **Nazwa przypisania** okno zostanie automatycznie wypełniona nazwę inicjatywy, ale można go zmienić. Możesz również dodać opcjonalny opis. **Przypisany przez** pole jest wypełniane automatycznie w oparciu kto jest zalogowany. Ta wartość jest opcjonalna.

7. (Opcjonalnie) Aby usunąć jeden lub więcej zasobów z zakresu, zaznacz **wykluczenia**.

8. W **obszaru roboczego usługi Log Analytics** listy rozwijanej liście dla obsługiwany region, wybierz obszar roboczy.

   > [!NOTE]
   > Jeśli obszar roboczy znajduje się poza zakresem przypisania, należy udzielić *Współautor usługi Log Analytics* uprawnień, aby identyfikator przypisania zasad podmiotu zabezpieczeń. Jeśli nie możesz tego zrobić, można napotkać błąd wdrażania, takie jak `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` Aby udzielić dostępu, zapoznaj się z [jak ręcznie skonfigurować tożsamość zarządzaną](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  **Tożsamości zarządzanej** pole wyboru jest zaznaczone, ponieważ inicjatywy przypisaniem zawiera zasady z *deployIfNotExists* efekt.
    
9. W **lokalizacji Zarządzanie tożsamościami** listy rozwijanej wybierz odpowiedni region.

10. Wybierz opcję **Przypisz**.

Po utworzeniu przypisania, **usługi Azure Monitor na potrzeby pokrycia zasad maszyn wirtualnych** aktualizacji stron **pokrycia przypisania**, **stan przypisania**, **zgodne Maszyny wirtualne**, i **stan zgodności** zgodnie ze zmianami. 

Następujące macierzy mapuje poszczególnymi stanami zgodności możliwe dla tej inicjatywy.  

| Stan zgodności | Opis | 
|------------------|-------------|
| **Zgodne** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych agentów usługi Log Analytics i zależności.|
| **Nie są zgodne** | Nie wszystkie maszyny wirtualne w zakresie mają usługi Log Analytics oraz agenci zależności wdrożonych i może wymagać korygowania.|
| **Nie uruchomiono** | Dodano nowy przydział. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Nie przypisano żadnych zasad. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela, aby zapewnić dostęp. Lub, zgodności i zarządzać przypisaniami za pośrednictwem podrzędne grupy zarządzania lub subskrypcji. 

Poniższa tabela zawiera mapowanie każdy stan możliwe przypisanie inicjatywy.

| Stan przypisania | Opis | 
|------------------|-------------|
| **Powodzenie** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych agentów usługi Log Analytics i zależności.|
| **Ostrzeżenie** | Subskrypcja nie jest w grupie zarządzania.|
| **Nie uruchomiono** | Dodano nowy przydział. |
| **Blokady** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Nie maszyny wirtualne istnieją lub nie jest przypisane zasady. | 
| **Akcja** | Przypisywanie zasad lub edytować przypisania. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproś właściciela, aby zapewnić dostęp. Lub, zgodności i zarządzać przypisaniami za pośrednictwem podrzędne grupy zarządzania lub subskrypcji.

## <a name="review-and-remediate-the-compliance-results"></a>Przejrzyj i Skoryguj wyniki sprawdzania zgodności

Poniższy przykład dotyczy programu Maszynie wirtualnej platformy Azure, ale ma również zastosowanie do zestawów skalowania maszyn wirtualnych. Aby dowiedzieć się, jak przeglądać wyniki sprawdzania zgodności, zobacz [zidentyfikować wyniki niezgodności](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na **usługi Azure Monitor na potrzeby pokrycia zasad maszyn wirtualnych** wybierz grupę zarządzania lub subskrypcji z tabeli. Wybierz **Wyświetl informacje o zgodności** , wybierając wielokropek (...).   

![Zgodność z zasadami dla maszyn wirtualnych platformy Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Na podstawie wyników zasad dołączone do tej inicjatywy, maszyny wirtualne są zgłaszane jako niezgodne w następujących scenariuszach:

* Log Analytics agent lub agenta zależności nie są wdrażane.  
    Ten scenariusz jest typowy dla zakresu przy użyciu istniejących maszyn wirtualnych. Aby go unikać, należy wdrożyć agentów wymaganych przez [tworzenie zadań korygowania](../../governance/policy/how-to/remediate-resources.md) niezgodne zasady.  
    - \[Podgląd\]: Wdrażanie agenta zależności maszyn wirtualnych systemu Linux
    - \[Podgląd\]: Wdrażanie agenta zależności dla maszyn wirtualnych Windows
    - \[Podgląd\]: Wdrożyć agenta Log Analytics dla maszyn wirtualnych systemu Linux
    - \[Podgląd\]: Wdróż agenta usługi Log Analytics dla maszyn wirtualnych Windows

* Obraz maszyny Wirtualnej (OS) nie jest określona w definicji zasad.  
    Zasady wdrażania obejmują tylko maszyny wirtualne, które są wdrażane z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zapoznaj się z dokumentacją, aby zobaczyć, czy system operacyjny maszyny Wirtualnej jest obsługiwana. Jeśli nie jest obsługiwany, zduplikowania zasady wdrażania i aktualizacji lub zmodyfikuj go, aby utworzyć obraz zgodne.  
    - \[Podgląd\]: Inspekcja wdrożenie agenta zależności — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS)
    - \[Podgląd\]: Inspekcja wdrożenie agenta usługi Log Analytics — nieznajdujące się na liście obrazu maszyny Wirtualnej (OS)

* Maszyny wirtualne nie są zalogowaniu się do określonego obszaru roboczego usługi Log Analytics.  
    Istnieje możliwość logowania niektórych maszyn wirtualnych w zakresie inicjatywy do obszaru roboczego usługi Log Analytics w innych niż ten, który jest określony w przypisaniu zasad. Ta zasada jest narzędziem, aby identyfikować, które raportują do niezgodnych obszaru roboczego maszyny wirtualne.  
    - \[Podgląd\]: Inspekcja obszaru roboczego analizy dzienników dla maszyny Wirtualnej — niezgodność raportu

## <a name="edit-an-initiative-assignment"></a>Edytuj przypisanie inicjatywy

W dowolnym momencie po przypisania inicjatywy do grupy zarządzania lub subskrypcji, możesz edytować go, aby zmodyfikować następujące właściwości:

- Nazwa przypisania
- Opis
- Przypisane przez
- Obszar roboczy usługi log Analytics
- Wyjątki

## <a name="next-steps"></a>Następne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych. 

- Aby dowiedzieć się, jak korzystać z funkcji health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md). 
- Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md). 
- Aby zidentyfikować wąskie gardła i ogólnego użycia z wydajnością maszyny Wirtualnej, zobacz [maszyny Wirtualnej platformy Azure w widoku wydajności](vminsights-performance.md). 
- Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
