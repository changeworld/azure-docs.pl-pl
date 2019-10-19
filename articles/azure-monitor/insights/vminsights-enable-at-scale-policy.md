---
title: Włączanie Azure Monitor dla maszyn wirtualnych przy użyciu Azure Policy | Microsoft Docs
description: W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych dla wielu maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych przy użyciu Azure Policy.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 068d320c85a1e10027c92b92c1b3c205a999bed7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553832"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) za pomocą Azure Policy

W tym artykule wyjaśniono, jak włączyć Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla usługi Azure Virtual Machines lub zestawu skalowania maszyn wirtualnych przy użyciu Azure Policy. Po zakończeniu tego procesu pomyślnie skonfigurowano włączenie Log Analytics i agentów zależności oraz zidentyfikowanie niezgodnych maszyn wirtualnych.

Aby odnajdywać i włączać Azure Monitor dla maszyn wirtualnych dla wszystkich maszyn wirtualnych platformy Azure lub zestawów skalowania maszyn wirtualnych, można użyć obu Azure Policy lub Azure PowerShell. Azure Policy to metoda, której zalecamy, ponieważ można zarządzać definicjami zasad w celu efektywnego zarządzania subskrypcjami w celu zapewnienia spójnej zgodności i automatycznego włączania nowo zainicjowanych maszyn wirtualnych. Następujące definicje zasad:

* Wdróż agenta Log Analytics i agenta zależności.
* Raport dotyczący wyników zgodności.
* Skoryguj dla niezgodnych maszyn wirtualnych.

Jeśli interesuje Cię wykonywanie tych zadań przy użyciu Azure PowerShell lub szablonu Azure Resource Manager, zobacz [włączanie Azure monitor dla maszyn wirtualnych (wersja zapoznawcza) przy użyciu szablonów Azure PowerShell i Azure Resource Manager](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Omówienie funkcji zarządzania pokryciem zasad

Początkowo środowisko Azure Policy do zarządzania i wdrażania definicji zasad dla Azure Monitor dla maszyn wirtualnych zostało wykonane wyłącznie przez Azure Policy. Funkcja Zarządzaj pokryciem zasad ułatwia i ułatwia odnajdywanie, zarządzanie i włączanie na dużą skalę **Azure monitor dla maszyn wirtualnych** inicjatywy, która obejmuje definicje zasad wymienione wcześniej. Dostęp do tej nowej funkcji **można uzyskać, korzystając z karty wprowadzenie** w Azure monitor dla maszyn wirtualnych. Wybierz pozycję **Zarządzaj pokryciem zasad** , aby otworzyć stronę **pokrycie zasad Azure monitor dla maszyn wirtualnych** .

![Karta wprowadzenie Azure Monitor z maszyn wirtualnych](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

W tym miejscu możesz sprawdzić pokrycie i zarządzać nim dla inicjatywy w ramach Twoich grup zarządzania i subskrypcji. Można zrozumieć, ile maszyn wirtualnych istnieje w każdej z grup zarządzania i subskrypcji i ich stanu zgodności.

![Azure Monitor dla maszyn wirtualnych Zarządzanie stroną zasad](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Te informacje ułatwiają planowanie i wykonywanie scenariusza ładu dla Azure Monitor dla maszyn wirtualnych z jednej centralnej lokalizacji. Gdy Azure Policy zapewnia widok zgodności, gdy zasady lub inicjatywa są przypisane do zakresu, to nowa strona umożliwia odnajdywanie, gdzie zasady lub inicjatywy nie są przypisane i nie przypisują. Wszystkie akcje, takie jak przypisanie, przeglądanie i edytowanie przekierowania do Azure Policy bezpośrednio. Strona **pokrycie zasad Azure monitor dla maszyn wirtualnych** jest rozszerzonym i zintegrowanym doświadczeniem tylko w przypadku inicjatywy **umożliwiającej Azure monitor dla maszyn wirtualnych**.

Na tej stronie można także skonfigurować obszar roboczy Log Analytics dla Azure Monitor dla maszyn wirtualnych, co:

- Instaluje rozwiązanie Service Map.
- Włącza liczniki wydajności systemu operacyjnego używane przez wykresy wydajności, skoroszyty oraz niestandardowe zapytania i alerty dziennika.

![Azure Monitor dla maszyn wirtualnych Skonfiguruj obszar roboczy](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Ta opcja nie jest powiązana z żadną akcją zasad. Jest ona dostępna, aby zapewnić łatwy sposób spełniania [wymagań wstępnych](vminsights-enable-overview.md) dotyczących włączania Azure monitor dla maszyn wirtualnych.  

### <a name="what-information-is-available-on-this-page"></a>Jakie informacje są dostępne na tej stronie?

Poniższa tabela zawiera podział informacji przedstawionych na stronie pokrycie zasad i sposób ich interpretacji.

| Funkcja | Opis | 
|----------|-------------| 
| **Zakres** | Grupa zarządzania i subskrypcje, do których masz lub odziedziczył dostęp, z możliwością przechodzenia do szczegółów w hierarchii grupy zarządzania.|
| **Rola** | Rola do zakresu, która może być czytelnikem, właścicielem lub współautorem. W niektórych przypadkach może pojawić się puste, aby wskazać, że użytkownik może mieć dostęp do subskrypcji, ale nie do grupy zarządzania, do której należy. Informacje w innych kolumnach różnią się w zależności od roli. Rola jest kluczem do określania, jakie dane można zobaczyć i jakie akcje można wykonać w kontekście przypisywania zasad lub inicjatyw (właściciel), edytowania ich lub wyświetlania zgodności. |
| **Łączna liczba maszyn wirtualnych** | Liczba maszyn wirtualnych w tym zakresie. W przypadku grupy zarządzania jest to łączna liczba maszyn wirtualnych zagnieżdżonych w ramach subskrypcji lub podrzędnej grupy zarządzania. |
| **Pokrycie przypisania** | Procent maszyn wirtualnych objętych zasadami lub inicjatywą. |
| **Stan przypisania** | Informacje dotyczące stanu przypisania zasad lub inicjatywy. |
| **Zgodne maszyny wirtualne** | Liczba maszyn wirtualnych, które są zgodne z zasadami lub inicjatywą. Dla inicjatywy **włączanie Azure monitor dla maszyn wirtualnych**jest to liczba maszyn wirtualnych, które mają zarówno agenta log Analytics, jak i agenta zależności. W niektórych przypadkach może pojawić się puste ze względu na brak przypisania, brak maszyn wirtualnych lub nie ma wystarczających uprawnień. Informacje są dostępne w obszarze **stan zgodności**. |
| **Zgodność** | Ogólny numer zgodności to suma różnych zasobów, które są zgodne, podzielone przez sumę wszystkich różnych zasobów. |
| **Stan zgodności** | Informacje dotyczące stanu zgodności dla przypisania zasad lub inicjatywy.|

Po przypisaniu zasad lub inicjatywy zakresem wybranym w przypisaniu może być zakres na liście lub jego podzestaw. Na przykład mogło zostać utworzone przypisanie dla subskrypcji (zakres zasad), a nie grupy zarządzania (zakres pokrycia). W takim przypadku wartość **pokrycie przypisania** wskazuje maszyny wirtualne w zakresie zasad lub inicjatywy podzielone przez maszyny wirtualne w zakresie pokrycia. W innym przypadku niektóre maszyny wirtualne, grupy zasobów lub subskrypcje z zakresu zasad mogły zostać wykluczone. Jeśli wartość jest pusta, oznacza to, że zasady lub inicjatywy nie istnieją lub nie masz odpowiedniego uprawnienia. Informacje są dostępne w obszarze **stan przypisania**.

## <a name="enable-by-using-azure-policy"></a>Włącz przy użyciu Azure Policy

Aby włączyć Azure Monitor dla maszyn wirtualnych przy użyciu Azure Policy w dzierżawie:

- Przypisz inicjatywę do zakresu: grupy zarządzania, subskrypcji lub grupy zasobów.
- Przejrzyj i skoryguj wyniki zgodności.

Aby uzyskać więcej informacji na temat przypisywania Azure Policy, zobacz [Azure Policy Omówienie](../../governance/policy/overview.md#policy-assignment) i przejrzyj [grupy zarządzania](../../governance/management-groups/overview.md) przed kontynuowaniem.

### <a name="policies-for-azure-vms"></a>Zasady dotyczące maszyn wirtualnych platformy Azure

Definicje zasad dla maszyny wirtualnej platformy Azure są wymienione w poniższej tabeli.

|Nazwa |Opis |Typ |
|-----|------------|-----|
|\[Preview \]: Włącz Azure Monitor dla maszyn wirtualnych |Włącz Azure Monitor dla maszyn wirtualnych w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. |Podjęł |
|\[Preview \]: Inspekcja wdrożenia agenta zależności — obraz maszyny wirtualnej (OS) nie został wystawiony |Zgłasza maszyny wirtualne jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: Przeprowadź inspekcję wdrożenia agenta Log Analytics — obraz maszyny wirtualnej (OS) nie został wystawiony |Zgłasza maszyny wirtualne jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta zależności dla maszyn wirtualnych z systemem Linux |Wdróż agenta zależności dla maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta zależności dla maszyn wirtualnych z systemem Windows |Wdróż agenta zależności dla maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux |Wdróż Log Analytics agenta dla maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows |Wdróż agenta Log Analytics dla maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Zasady dla zestawów skalowania maszyn wirtualnych platformy Azure

Definicje zasad dla zestawu skalowania maszyn wirtualnych platformy Azure są wymienione w poniższej tabeli.

|Nazwa |Opis |Typ |
|-----|------------|-----|
|\[Preview \]: Włączanie Azure Monitor dla zestawów skalowania maszyn wirtualnych |Włącz Azure Monitor dla zestawów skalowania maszyn wirtualnych w określonym zakresie (grupy zarządzania, subskrypcji lub grupy zasobów). Przyjmuje Log Analytics obszar roboczy jako parametr. Uwaga: Jeśli zasady uaktualniania zestawu skalowania zostały ustawione na ręczne, Zastosuj rozszerzenie do wszystkich maszyn wirtualnych w zestawie, wywołując na nich uaktualnienie. W interfejsie wiersza polecenia jest to AZ VMSS Update-Instances. |Podjęł |
|\[Preview \]: Inspekcja wdrożenia agenta zależności w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony |Raporty zestawu skalowania maszyn wirtualnych są wyświetlane jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: Przeprowadź inspekcję wdrożenia agenta Log Analytics w zestawach skalowania maszyn wirtualnych — obraz maszyny wirtualnej (OS) nie został wystawiony |Raporty zestawu skalowania maszyn wirtualnych są wyświetlane jako niezgodne, jeśli na liście nie ma zdefiniowanego obrazu maszyny wirtualnej (OS), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta zależności dla zestawów skalowania maszyn wirtualnych z systemem Linux |Wdróż agenta zależności dla zestawów skalowania maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta zależności dla zestawów skalowania maszyn wirtualnych z systemem Windows |Wdróż agenta zależności dla zestawów skalowania maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Linux |Wdróż Log Analytics agenta dla maszyn wirtualnych z systemem Linux, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |
|\[Preview \]: wdrażanie agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows |Wdróż agenta Log Analytics dla zestawów skalowania maszyn wirtualnych z systemem Windows, jeśli na liście jest zdefiniowany obraz maszyny wirtualnej (system operacyjny), a agent nie jest zainstalowany. |Zasady |

Zasady autonomiczne (nieuwzględnione w ramach inicjatywy) opisano tutaj:

|Nazwa |Opis |Typ |
|-----|------------|-----|
|\[Preview \]: obszar roboczy inspekcji Log Analytics dla maszyny wirtualnej — niezgodność raportów |Zgłaszaj maszyny wirtualne jako niezgodne, jeśli nie są rejestrowane w obszarze roboczym Log Analytics określonym w przypisaniu zasad lub inicjatyw. |Zasady |

### <a name="assign-the-azure-monitor-initiative"></a>Przypisywanie inicjatywy Azure Monitor

Aby utworzyć przypisanie zasad na stronie **pokrycie zasad Azure monitor dla maszyn wirtualnych** , wykonaj następujące czynności. Aby dowiedzieć się, jak wykonać te kroki, zobacz [Tworzenie przypisania zasad z Azure Portal](../../governance/policy/assign-policy-portal.md).

Po przypisaniu zasad lub inicjatywy zakres wybrany w ramach przypisania może być zakresem wymienionym tutaj lub jego podzbiorem. Na przykład mogło zostać utworzone przypisanie dla subskrypcji (zakres zasad), a nie do grupy zarządzania (zakres pokrycia). W takim przypadku procent pokrycia będzie wskazywał maszyny wirtualne w zakresie zasad lub inicjatywy podzielone przez maszyny wirtualne w zakresie pokrycia. W innym przypadku można wykluczyć niektóre maszyny wirtualne lub grupy zasobów albo subskrypcję z zakresu zasad. Jeśli ta wartość jest pusta, oznacza to, że zasady lub inicjatywy nie istnieją lub nie masz uprawnień. Informacje są dostępne w obszarze **stan przypisania**.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

2. W Azure Portal wybierz pozycję **Monitoruj**. 

3. Wybierz pozycję **Virtual Machines (wersja zapoznawcza)** w sekcji **szczegółowe informacje** .
 
4. Wybierz kartę **wprowadzenie** . Na stronie wybierz pozycję **Zarządzaj pokryciem zasad**.

5. Wybierz grupę zarządzania lub subskrypcję z tabeli. Wybierz **zakres** , wybierając wielokropek (...). W tym przykładzie zakres ogranicza przypisanie zasad do grupy maszyn wirtualnych do wymuszania.

6. Na stronie **przypisanie Azure Policy** zostanie wstępnie wypełniony z inicjatywy **Włączanie Azure monitor dla maszyn wirtualnych**. 
    Pole **Nazwa przypisania** jest automatycznie wypełniane nazwą inicjatywy, ale można ją zmienić. Możesz również dodać opcjonalny opis. Pole **przypisane przez** jest wypełniane automatycznie na podstawie tego, kto jest zalogowany. Ta wartość jest opcjonalna.

7. Obowiązkowe Aby usunąć co najmniej jeden zasób z zakresu, wybierz pozycję **wykluczenia**.

8. Z listy rozwijanej **obszar roboczy log Analytics** dla obsługiwanego regionu wybierz obszar roboczy.

   > [!NOTE]
   > Jeśli obszar roboczy przekracza zakres przypisania, udziel *log Analytics uprawnienia współautora* do identyfikatora podmiotu zabezpieczeń przypisania zasad. Jeśli tego nie zrobisz, może zostać wyświetlony błąd wdrażania, na przykład `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`, aby udzielić dostępu, zapoznaj [się z tematem jak ręcznie skonfigurować tożsamość zarządzaną](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Pole wyboru **tożsamość zarządzana** jest zaznaczone, ponieważ przypisana inicjatywa zawiera zasady z efektem *deployIfNotExists* .
    
9. Z listy rozwijanej **Zarządzaj lokalizacją tożsamości** wybierz odpowiedni region.

10. Wybierz opcję **Przypisz**.

Po utworzeniu przypisania strona **pokrycie zasad Azure monitor dla maszyn wirtualnych** aktualizuje **pokrycie przypisania**, **stan przypisania**, **zgodne maszyny wirtualne**i **stan zgodności** w celu odzwierciedlenia zmian. 

Poniższa macierz odwzorowuje każdy możliwy stan zgodności dla inicjatywy.  

| Stan zgodności | Opis | 
|------------------|-------------|
| **Komputera** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych Log Analytics i agentów zależności.|
| **Niezgodne** | Nie wszystkie maszyny wirtualne w zakresie mają wdrożone Log Analytics i agentów zależności i mogą wymagać skorygowania.|
| **Nie uruchomiono** | Dodano nowe przypisanie. |
| **Skręt** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Nie przypisano żadnych zasad. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproszenie właściciela o udzielenie dostępu. Można też wyświetlić zgodność i zarządzać przypisaniami za pomocą podrzędnych grup zarządzania lub subskrypcji. 

Poniższa tabela odwzorowuje każdy możliwy stan przypisania dla inicjatywy.

| Stan przypisania | Opis | 
|------------------|-------------|
| **Prawnego** | Wszystkie maszyny wirtualne w zakresie mają wdrożonych Log Analytics i agentów zależności.|
| **Wyświetlania** | Subskrypcja nie należy do grupy zarządzania.|
| **Nie uruchomiono** | Dodano nowe przypisanie. |
| **Skręt** | Nie masz wystarczających uprawnień do grupy zarządzania. <sup>1</sup> | 
| **Puste** | Nie istnieją żadne maszyny wirtualne lub nie przypisano zasad. | 
| **Akcja** | Przypisz zasady lub Edytuj przypisanie. | 

<sup>1</sup> Jeśli nie masz dostępu do grupy zarządzania, poproszenie właściciela o udzielenie dostępu. Można też wyświetlić zgodność i zarządzać przypisaniami za pomocą podrzędnych grup zarządzania lub subskrypcji.

## <a name="review-and-remediate-the-compliance-results"></a>Przeglądanie i korygowanie wyników zgodności

Poniższy przykład dotyczy maszyny wirtualnej platformy Azure, ale ma również zastosowanie do zestawów skalowania maszyn wirtualnych. Aby dowiedzieć się, jak przeglądać wyniki zgodności, zobacz [identyfikowanie wyników braku zgodności](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Na stronie **pokrycie zasad Azure monitor dla maszyn wirtualnych** wybierz grupę zarządzania lub subskrypcję z tabeli. Wybierz pozycję **Wyświetl zgodność** , wybierając wielokropek (...).   

![Zgodność zasad dla maszyn wirtualnych platformy Azure](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Na podstawie wyników zasad zawartych w ramach inicjatywy maszyny wirtualne są zgłaszane jako niezgodne w następujących scenariuszach:

* Agent Log Analytics lub Agent zależności nie został wdrożony.  
    Ten scenariusz jest typowy dla zakresu z istniejącymi maszynami wirtualnymi. Aby rozwiązać ten problem, należy wdrożyć wymaganych agentów przez [utworzenie zadań korygowania](../../governance/policy/how-to/remediate-resources.md) dla niezgodnych zasad.  
    - \[Preview \]: wdrażanie agenta zależności dla maszyn wirtualnych z systemem Linux
    - \[Preview \]: wdrażanie agenta zależności dla maszyn wirtualnych z systemem Windows
    - \[Preview \]: wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Linux
    - \[Preview \]: wdrażanie agenta Log Analytics dla maszyn wirtualnych z systemem Windows

* Obraz maszyny wirtualnej (OS) nie został zidentyfikowany w definicji zasad.  
    Kryteria zasad wdrażania obejmują tylko maszyny wirtualne wdrożone z dobrze znanych obrazów maszyn wirtualnych platformy Azure. Zapoznaj się z dokumentacją, aby sprawdzić, czy system operacyjny maszyny wirtualnej jest obsługiwany. Jeśli nie jest obsługiwana, należy zduplikować zasady wdrażania i zaktualizować lub zmodyfikować je w celu uzyskania zgodności obrazu.  
    - \[Preview \]: Inspekcja wdrożenia agenta zależności — obraz maszyny wirtualnej (OS) nie został wystawiony
    - \[Preview \]: Przeprowadź inspekcję wdrożenia agenta Log Analytics — obraz maszyny wirtualnej (OS) nie został wystawiony

* Maszyny wirtualne nie logują się do określonego obszaru roboczego Log Analytics.  
    Istnieje możliwość, że niektóre maszyny wirtualne w zakresie inicjatywy logują się do obszaru roboczego Log Analytics innego niż ten, który jest określony w przypisaniu zasad. Te zasady służą do identyfikowania, które maszyny wirtualne zgłaszają do niezgodnego obszaru roboczego.  
    - \[Preview \]: obszar roboczy inspekcji Log Analytics dla maszyny wirtualnej — niezgodność raportów

## <a name="edit-an-initiative-assignment"></a>Edytowanie przypisania inicjatywy

W dowolnym momencie po przypisaniu inicjatywy do grupy zarządzania lub subskrypcji można edytować ją w celu zmodyfikowania następujących właściwości:

- Nazwa przypisania
- Opis
- Przypisane przez
- Log Analytics obszar roboczy
- Wyjątki

## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą Azure Monitor dla maszyn wirtualnych. 

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [view Azure monitor dla maszyn wirtualnych map](vminsights-maps.md). 

- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md). 
