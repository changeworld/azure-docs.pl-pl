---
title: Usługa Azure DevTest Labs — często zadawane pytania | Dokumentacja firmy Microsoft
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: a46d816c04d9f5629c2ee9538016d42c53f9a331
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244392"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs — często zadawane pytania
Uzyskaj odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure DevTest Labs.

## <a name="blog-post"></a>Wpis w blogu
Naszym blogu zespołu laboratoria DevTest został wycofany z 20 marca 2019 r. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Gdzie mogę śledzić aktualizacje funkcji od teraz?
Od teraz firma Microsoft będzie się publikowanie aktualizacji dotyczących funkcji i wpisy w blogu informacyjne na blogu platformy Azure i aktualizacje platformy Azure. Wpisy na blogu również połączy się z naszą dokumentację, wszędzie tam, gdzie wymagane.

Subskrybuj [DevTest Labs — Blog dotyczący platformy Azure](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) i [aktualizacji usługi DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) uzyskiwania informacji o nowych funkcjach w usłudze DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co się dzieje z istniejących wpisów w blogu?
Obecnie pracujemy nad Migrowanie istniejących wpisów w blogu (z wyjątkiem aktualizacji awarii) do naszych [dokumentacja usługi DevTest Labs](devtest-lab-overview.md). Gdy na blogu MSDN jest przestarzały, zostaną przekierowani do Przegląd dokumentacji dla usługi DevTest Labs. Gdy przekierowanie, możesz wyszukać artykuł, którego szukasz w tytule "Filtrowanie według". Firma Microsoft nie zostały jeszcze zmigrowane wszystkie wpisy, ale powinna być podejmowana z końcem tego miesiąca. 


### <a name="where-do-i-see-outage-updates"></a>Gdzie mogę zobaczyć aktualizacje awarii?
Firma Microsoft będzie publikowanie aktualizacji awarii przy użyciu naszej usłudze Twitter od teraz lub nowszy. Obserwuj nas w serwisie Twitter w celu uzyskania najnowszych aktualizacji na awarie i znanych błędów.

### <a name="twitter"></a>Twitter
Nasze uchwyt Twitter: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli moje pytanie nie ma tutaj odpowiedzi?
Jeśli Twoje pytanie nie ma na liście, dać nam znać, abyśmy mogli pomóc Ci znaleźć odpowiedź.

- Zadaj je na końcu tego — często zadawane pytania. Skontaktuj się z zespołem usługi Azure Cache i inni członkowie społeczności, informacje o tym artykule.
- Aby uzyskać dostęp do większej liczby osób, należy zadać pytanie na [forum usługi Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Skontaktuj się z zespołem usługi Azure DevTest Labs i inni członkowie społeczności.
- Dla żądania funkcji przesłać żądania i pomysłów dotyczących [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co to jest konto Microsoft?
Konto Microsoft jest konto, którego używasz dla prawie wszystko, co zrobić z firmy Microsoft, urządzeń i usług. Jest to adres e-mail i hasło, którego używasz do logowania się do usługi Skype, Outlook.com, OneDrive, Windows phone, Azure i Xbox Live. Jedno konto oznacza, że pliki, zdjęcia, kontaktów i ustawienia wykonać użytkownik na dowolnym urządzeniu.
 
> [!NOTE]
> Konto Microsoft używane do wywoływania identyfikator Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Dlaczego warto używać usługi Azure DevTest Labs?
Usługa Azure DevTest Labs można zapisać Twojego zespołu, czas i pieniądze. Deweloperzy mogą tworzyć własnych środowisk przy użyciu kilku różnych podstaw. Mogą również użyć artefaktów można szybko wdrożyć i skonfigurować aplikacje. Za pomocą niestandardowych obrazów i formuł, można zapisać maszyny wirtualne (VM) jako szablonów i łatwo odtworzyć je w zespół. DevTest Labs oferuje także kilka zasad można skonfigurować tego laboratorium, które Administratorzy mogą używać zmniejszeniu strat i zarządzanie środowiskami zespołu. Zasady te obejmują automatycznego zamykania, próg koszt maksymalna maszyn wirtualnych na użytkownika, a maksymalny rozmiar maszyny Wirtualnej. Aby uzyskać szczegółowe informacje o usłudze DevTest Labs, zobacz [Przegląd](devtest-lab-overview.md) lub [klip wideo z wprowadzeniem](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co oznacza "obaw samoobsługi"?
Samoobsługa bez zmartwień oznacza, że deweloperom i testerom tworzyć własne środowiska zgodnie z potrzebami. Administratorzy mają bezpieczeństwa, wiedząc, że usługa DevTest Labs może pomóc ustaw odpowiedni dostęp, minimalizowanie strat i kontrolowanie kosztów. Administratorzy mogą określić rozmiary maszyn wirtualnych, które są dozwolone, maksymalna liczba maszyn wirtualnych, a podczas pracy maszyny wirtualne i zamknij. DevTest Labs ułatwia także monitorowanie kosztów i Ustawiaj alerty zwiększające wiedzieć, jak są używane zasoby laboratorium.

### <a name="how-can-i-use-devtest-labs"></a>Jak używać usługi DevTest Labs?
DevTest Labs jest przydatne w dowolnym momencie wymagają deweloperów lub środowisk testowych i chcesz odtworzyć je szybko ani zarządzać nimi za pomocą zasad oszczędności.
Poniżej przedstawiono kilka scenariuszy, w których nasi klienci będą korzystać usługi DevTest Labs dla:

- Zarządzanie deweloperów i środowisk testowych w jednym miejscu. Używanie zasad do zmniejszenia kosztów i tworzenie niestandardowych obrazów, aby udostępnić opiera się na zespół.
- Tworzenie aplikacji przy użyciu obrazów niestandardowych można zapisać stan dysku etapach tworzenia.
- Śledzenie kosztów względem postępu.
- Twórz środowiska testowe pamięci masowej dla testów zapewnienia jakości.
- Użyj artefaktów i formuły można łatwo konfigurować i odtworzenia aplikacji w różnych środowiskach.
- Rozpraszanie maszyn wirtualnych na potrzeby hackathonów (pracy zespołowej deweloperskie lub testowe), a następnie łatwo cofnąć inicjowanie ich obsługi po zakończeniu zdarzenia.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak są rozliczane DevTest Labs?
DevTest Labs to bezpłatna usługa. Tworzenie laboratoriów i konfigurowanie zasad, szablonów i artefaktów w usłudze DevTest Labs jest bezpłatna. Płacisz tylko za zasoby platformy Azure używane w laboratorium, takie jak maszyny wirtualne, konta magazynu i sieci wirtualnych. Aby uzyskać więcej informacji o koszcie zasoby laboratorium, zobacz [cennika usługi Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Bezpieczeństwo

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Co to są poziomy zabezpieczeń w usłudze DevTest Labs?
Zabezpieczenia dostępu jest określany przez kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się, jak działa dostęp, ułatwia naukę różnice między uprawnienia, roli i zakresu, zgodnie z definicją RBAC.

- **Uprawnienie**: Uprawnienie jest zdefiniowany dostępu do określonej akcji. Uprawnienia można na przykład dostęp do odczytu do wszystkich maszyn wirtualnych.
- **Rola**: Rola to zestaw uprawnień, które mogą być grupowane i przypisane do użytkownika. Na przykład użytkownik mający rolę właściciela subskrypcji ma dostęp do wszystkich zasobów w ramach subskrypcji.
- **Zakres**: Zakres jest poziom w hierarchii zasobu platformy Azure. Na przykład zakres może być grupę zasobów, jednym laboratorium lub całej subskrypcji.

W zakresie usługi DevTest Labs istnieją dwa typy ról, które definiują uprawnienia użytkownika:

- **Właściciel laboratorium**: Właściciel laboratorium ma dostęp do wszystkich zasobów w środowisku laboratoryjnym. Właściciel laboratorium można modyfikować zasady, odczytu i zapisu do maszyn wirtualnych, zmień sieć wirtualną i tak dalej.
- **Użytkownik laboratorium**: Użytkownik laboratorium można wyświetlić wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasad i sieciami wirtualnymi. Jednak użytkownik laboratorium nie można zmodyfikować zasady lub maszyn wirtualnych, które zostały utworzone przez innych użytkowników.

Możesz również utworzyć niestandardowe role w usłudze DevTest Labs. Aby dowiedzieć się, jak utworzyć niestandardowe role w usłudze DevTest Labs, zobacz [udzielić użytkownikowi uprawnień do zasad określonych laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, jeśli użytkownik ma uprawnienia w określonym zakresie, użytkownik automatycznie otrzymuje te uprawnienia w każdym zakresie niższego poziomu w zakresie. Na przykład jeśli użytkownik przypisany do roli właściciela subskrypcji, użytkownik ma dostęp do wszystkich zasobów w ramach subskrypcji. Te zasoby obejmują maszyny wirtualne, sieci wirtualnych i labs. Właściciel subskrypcji automatycznie dziedziczy roli właściciel laboratorium. Odwrotny jest true. Właściciel laboratorium ma dostęp do laboratorium, która jest zakresem niższym niż poziom subskrypcji. Tak nie zobaczą właściciel laboratorium, maszyny wirtualne, sieci wirtualne lub inne zasoby, które wykraczają poza laboratorium.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Jak zdefiniować kontroli dostępu opartej na rolach dla moich DevTest Labs środowiska, aby upewnić się, że dział IT określają sposób, gdy deweloperzy i testowania można wykonać swoją pracę?
Istnieje szerokie wzorzec, jednak szczegóły zależy od organizacji.

Centralna IT powinny właścicielem tylko co jest potrzebne i umożliwiają zespołom projektu i aplikacji i mają wymagane poziom kontroli. Zwykle oznacza to, że centralne IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zbiór **właścicieli** dla subskrypcji powinna być niewielka. Właściciele, można wyznaczyć dodatkowych właścicieli, gdy istnieje potrzeba lub stosowanie zasad na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może to być podzbiór użytkowników, którzy wymagają dostępu do różnych subskrypcji, takie jak obsługa 1 lub 2 warstwy. W tym przypadku zaleca się tym użytkownikom nadać **Współautor** dostępu, tak aby mogli zarządzać zasobami, ale nie zapewnia użytkownikom dostęp lub dostosować zasady.

Zasób usługi DevTest Labs powinna być własnością właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. Jest to spowodowane zrozumieniu wymagań w zakresie maszyn i wymagane oprogramowanie. W większości organizacji właścicielem tego zasobu usługi DevTest Labs jest często realizacji projektu/programowania. Tego właściciela można zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkich maszyn wirtualnych w środowisku laboratorium.

Członkowie zespołu projektu/aplikacja powinna być dodana do **DevTest Labs użytkowników** roli. Tacy użytkownicy umożliwia tworzenie maszyn wirtualnych (wbudowane z laboratorium i zasadami poziomu subskrypcji). Można również zarządzać maszyn wirtualnych. Nie mogą zarządzać maszyn wirtualnych, które należą do innych użytkowników.

Aby uzyskać więcej informacji, zobacz [szkielet platformy Azure w przedsiębiorstwach — dokumentacja nadzoru subskrypcji normatywne](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Jak utworzyć rolę, aby umożliwić użytkownikom wykonania konkretnego zadania?
Aby uzyskać kompleksowy o tym, jak utworzyć niestandardowe role i przypisać uprawnienia do roli, zobacz [udzielić użytkownikowi uprawnień do zasad określonych laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Poniżej przedstawiono przykładowy skrypt, który tworzy rolę **użytkownik usługi DevTest Labs zaawansowane**, który ma uprawnienia do uruchamiania i zatrzymania wszystkich maszyn wirtualnych w laboratorium:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Jak organizacja upewnij się, że zasady zabezpieczeń firmy znajdują się w miejscu?
Organizacja może osiągnąć go, wykonując następujące czynności:

- Tworzenie i publikowanie zasad kompleksowych zabezpieczeń. Zasady articulates zasady dopuszczalnych działań związanych z korzystaniem z oprogramowania, zasobów w chmurze. Definiuje również, jakie wyraźnie narusza zasady.
- Tworzenie niestandardowego obrazu, niestandardowe artefakty i procesem wdrażania, która służy do aranżacji w obrębie obszaru zabezpieczeń, która jest zdefiniowana za pomocą usługi active directory. Ta metoda wymusza granic firmowych i ustawia wspólny zbiór kontroli środowiska. Te kontrolki w środowisku deweloper może wziąć pod uwagę ich opracowanie i przestrzeganie Security development lifecycle jako część ich całego procesu. Celem jest również zapewnić środowisko, które nie są zbyt restrykcyjne ten może utrudniać rozwoju, ale uzasadnione zbiór kontrolek. Zasady grupy w jednostce organizacyjnej (OU), która zawiera maszyny wirtualne laboratorium może być podzestawem zasad grupy całkowitej, które znajdują się w środowisku produkcyjnym. Alternatywnie można je dodatkowego zestawu poprawnie eliminowania wszelkich zidentyfikowanych.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Jak organizacja zapewnienia integralności danych, aby upewnić się, że deweloperzy usług zdalnych nie można usunąć kodu ani wprowadzenia złośliwego oprogramowania lub oprogramowania niezatwierdzone
Istnieje kilka warstw kontroli w celu ograniczenia zagrożenia od konsultantów zewnętrznych, Zleceniobiorcami ani pracownicy, którzy są remoting w do współpracy w usłudze DevTest Labs.

Jak wspomniano wcześniej, pierwszym krokiem musi mieć zasady dopuszczalnego użytkowania przygotowanego i zdefiniowane, która wyraźnie wskazuje konsekwencje, gdy ktoś narusza zasady.

Pierwszą warstwę kontroli dostępu zdalnego polega na zastosowaniu zasad dostępu zdalnego za pośrednictwem połączenia sieci VPN, który nie jest podłączone bezpośrednio do laboratorium.

Druga warstwa formantów polega na zastosowaniu zestaw obiektów zasad grupy, które zapobiec kopiowania i wklejania za pośrednictwem pulpitu zdalnego. Zasady sieci implementacji nie zezwalać na połączenia wychodzące usługi ze środowiska, takie jak FTP i usługi RDP poza środowiskiem. Routingu zdefiniowanego przez użytkownika może wymusić na cały ruch sieciowy platformy Azure do środowiska lokalnego, ale routingu nie konta dla wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, o ile nie jest kontrolowany za pośrednictwem serwera proxy w celu skanowania zawartości i sesji. Publiczne adresy IP może być ograniczona w sieci wirtualnej, obsługa DevTest Labs nie zezwalać na łączenie zasobu sieci zewnętrznej.

Ostatecznie ten sam typ ograniczenia musi zostać zastosowana w całej organizacji, co może uwzględnić wszystkie możliwe metody nośnik wymienny lub zewnętrzne adresy URL, które mogłyby zaakceptować wpis zawartości. Zapoznaj się z bezpieczeństwo professional, aby przejrzeć i zaimplementować zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft Security Cybernetycznymi](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfiguracja laboratorium

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak utworzyć laboratorium za pomocą szablonu usługi Resource Manager?
Firma Microsoft oferuje [repozytorium GitHub z szablonów usługi Azure Resource Manager w laboratorium](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) , którą można wdrożyć jako — lub zmodyfikować do tworzenia niestandardowych szablonów laboratorium. Każdy szablon zawiera link do wdrożenia laboratorium, ponieważ jest w ramach własnej subskrypcji platformy Azure. Alternatywnie można dostosować szablon i [wdrażanie przy użyciu programu PowerShell lub wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Czy można mieć wszystkie maszyny wirtualne, które zostały utworzone w typowych grupy zasobów, zamiast tego po każdej maszyny w jego własnej grupie zasobów? 
Tak, jako właściciel laboratorium możesz albo pozwolić laboratorium obsługi alokacji grupy zasobów dla Ciebie lub zostały wszystkie maszyny wirtualne utworzone w typowych grupy zasobów, należy określić. 

W scenariuszu z grupą osobnego zasobu:
-   DevTest Labs tworzy nową grupę zasobów dla każdej maszyny wirtualnej adresu IP publiczny/prywatny, który można zwiększać
-   DevTest Labs tworzy grupę zasobów dla udostępnionych maszynach adresów IP, które należą do tej samej wielkości.

Typowy scenariusz grupy zasobów:
-   Wszystkie maszyny wirtualne są uruchamiane w grupie zasobów wspólnego, które określisz. Dowiedz się więcej [alokacji grupy zasobów dla laboratorium](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Jak zachować konwencję nazewnictwa w całej mojego środowiska DevTest Labs?
Możesz chcieć rozszerzanie bieżących konwencjach nazewnictwa przedsiębiorstwa do operacji na platformie Azure, dzięki czemu będą zgodne w środowisku laboratorium. Podczas wdrażania usługi DevTest Labs, zaleca się, że masz określone zasady począwszy od. Możesz wdrożyć te zasady przez centralną skryptów i szablonów JSON, aby wymusić spójność. Nadawanie zasadom nazw może być implementowany przez zasady usługi Azure stosowane na poziomie subskrypcji. Aby uzyskać przykłady kodu JSON usługi Azure Policy, zobacz [przykładów usługi Azure Policy](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ile labs można tworzyć w ramach tej samej subskrypcji?
Nie ma określony limit liczby laboratoria, które mogą być tworzone na subskrypcję. Jednak ilość zasobów używanych w ramach jednej subskrypcji jest ograniczona. Informacje o [limity przydziału dla subskrypcji platformy Azure i](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Ile maszyn wirtualnych można utworzyć na laboratorium?
Nie ma żadnych określonych limitu liczby maszyn wirtualnych, które mogą być tworzone na laboratorium. Zasobów (rdzeni maszyn wirtualnych, publiczne adresy IP itd.), które są używane, są jednak ograniczone na subskrypcję. Informacje o [limity przydziału dla subskrypcji platformy Azure i](../azure-subscription-service-limits.md) i [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Jak określić współczynnik użytkowników na laboratorium oraz ogólną liczbę laboratoria, które są wymagane w całej organizacji?
Zaleca się, że jednostek biznesowych i grupy deweloperów, które są skojarzone z tym samym projekcie rozwoju są skojarzone z tym samym środowisku laboratoryjnym. Umożliwia ona te same typy zasad, obrazy i zamykania zasad mają być stosowane do obu grup.

Również może być konieczne należy rozważyć granicach geograficznych. Na przykład deweloperzy w Stanach Zjednoczonych wschód Północna (USA) mogą używać laboratorium aprowizowane w wschodnie stany USA 2. Ponadto deweloperzy w Dallas, Teksas i Warszawie mogą być kierowani do użycia zasobu w południowo-środkowych stanów USA. W przypadku wspólnego nakładu pracy z firm zewnętrznych, można można przypisać do laboratorium, który nie jest używany przez deweloperów wewnętrznego.

Możesz także użyć laboratorium dla określonego projektu w ramach usługi Azure DevOps Projects. Następnie możesz zastosować zabezpieczeń przy użyciu określonej grupy usługi Azure Active Directory, która zezwala na dostęp do obu zestawów zasobów. Sieci wirtualnej przypisany do laboratorium może być inny granic skonsolidować użytkowników.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak można zapobiec usuwania zasobów w ramach laboratorium?
Firma Microsoft zaleca, ustawić odpowiednie uprawnienia na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogą usunąć zasoby lub zmienić zasady laboratorium. Deweloperzy powinny zostać umieszczone w ramach **DevTest Labs użytkowników** grupy. Developer potencjalny klient lub kierownik infrastruktury powinny być **DevTest Labs właściciela**. Zaleca się, że masz tylko dwóch właścicieli laboratorium. Ta zasada rozszerza kierunku repozytorium kodu, aby uniknąć uszkodzenia. Użytkownicy laboratorium ma prawa do korzystania z zasobów, ale nie można zaktualizować zasad laboratorium. Zobacz następujący artykuł, który wyświetla listę ról i uprawnień, które każda grupa wbudowana ma w ramach laboratorium: [Dodawanie właścicieli i użytkowników w usłudze Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak udostępnić bezpośredni link do mojego laboratorium?

1. W [witryny Azure portal](https://portal.azure.com), przejdź do laboratorium.
2. Kopia **URL laboratorium** w przeglądarce, a następnie udostępnić użytkownikom laboratorium.

> [!NOTE]
> Jeśli użytkownik laboratorium jest użytkownikiem zewnętrznym, kto posiada konto Microsoft, ale który nie jest członkiem wystąpienia usługi Active Directory w Twojej organizacji, użytkownik może być wyświetlony komunikat o błędzie podczas próby uzyskania dostępu link udostępniony do. Jeśli użytkownik zewnętrzny zobaczy następujący komunikat o błędzie, należy poprosić użytkownika o najpierw wybierz jego nazwę w prawym górnym rogu witryny Azure portal. Następnie w sekcji katalogu w menu po użytkownik może wybrać katalog, w której istnieje laboratorium.

## <a name="virtual-machines"></a>Maszyny wirtualne

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Dlaczego nie widzę maszyn wirtualnych na stronie maszyny wirtualne, które widzę w usłudze DevTest Labs?
Po utworzeniu maszyny Wirtualnej w usłudze DevTest Labs otrzymuje uprawnienia do dostępu do tej maszyny Wirtualnej. Możesz wyświetlić maszyny Wirtualnej na stronie labs i na **maszyn wirtualnych** strony. Użytkownicy przypisani do **DevTest Labs właściciela** roli można zobaczyć wszystkie maszyny wirtualne, które zostały utworzone w laboratorium w laboratorium **wszystkie maszyny wirtualne** strony. Jednak użytkownicy, którzy mają **użytkownik usługi DevTest Labs** roli nie są automatycznie przyznawane dostęp do odczytu do zasobów maszyny Wirtualnej, które zostały utworzone przez innych użytkowników. Dlatego te maszyny wirtualne nie są wyświetlane na **maszyn wirtualnych** strony.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak utworzyć wiele maszyn wirtualnych z tego samego szablonu jednocześnie?
Masz dwie opcje jednocześnie tworzenia wielu maszyn wirtualnych z tego samego szablonu:

- Możesz użyć [rozszerzenie Azure DevOps zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Możesz [Generowanie szablonu usługi Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) podczas tworzenia maszyny Wirtualnej, a [wdrażanie szablonu usługi Resource Manager za pomocą programu Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Można również określić więcej niż jedno wystąpienie maszyny ma zostać utworzony podczas tworzenia maszyny wirtualnej. Aby dowiedzieć się więcej na temat tworzenia wielu wystąpień maszyn wirtualnych, zobacz dokumentów na [tworzenia maszyny wirtualnej laboratorium](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak przenieść Mój istniejący maszyn wirtualnych platformy Azure do mojego laboratorium DevTest Labs?
Aby skopiować istniejących maszyn wirtualnych, użytkownik usługi DevTest Labs:

1.  Skopiuj plik wirtualnego dysku twardego istniejącej maszyny wirtualnej przy użyciu [skrypt programu Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Tworzenie [obrazu niestandardowego](devtest-lab-create-template.md) wewnątrz laboratorium DevTest Labs.
3.  Tworzenie maszyny Wirtualnej w środowisku laboratoryjnym za pomocą obrazu niestandardowego.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Można dołączyć kilka dysków maszyn wirtualnych?

Tak, można dołączyć kilka dysków do maszyn wirtualnych.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Aby korzystać z obrazu systemu operacyjnego Windows na etapach testowania, czy muszę kupić subskrypcję MSDN?
Aby używać obrazów systemu operacyjnego klienta Windows (Windows 7 lub nowszy) dla rozwoju lub testowania na platformie Azure, wykonaj jedną z następujących czynności:

- [Kup subskrypcję MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Jeśli masz umowę Enterprise Agreement, Utwórz subskrypcję platformy Azure za pomocą [przedsiębiorstwa: tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p).

Aby uzyskać więcej informacji na temat środki na korzystanie z platformy Azure dla każdej oferty MSDN, zobacz [Azure miesięczne środki dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak zautomatyzować proces usuwania wszystkich maszyn wirtualnych w mojej laboratorium?
Jako właściciel laboratorium możesz usunąć maszyny wirtualne ze środowiska laboratoryjnego w witrynie Azure portal. Możesz również usunąć wszystkie maszyny wirtualne w środowisku laboratoryjnym przy użyciu skryptu programu PowerShell. W poniższym przykładzie w obszarze **wartości w celu zmiany** komentarz, zmodyfikuj wartości parametrów. Możesz pobrać labName wartości subscriptionId, labResourceGroup i w okienku laboratorium w witrynie Azure portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Środowiska 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak użyć szablonów usługi Resource Manager w mojej usługi DevTest Labs środowiska?
Wdrażanie szablonów usługi Resource Manager w środowisku laboratorium, wykonując kroki opisane w [funkcji środowisk w usłudze DevTest Labs](devtest-lab-test-env.md) artykułu. Po prostu zaznacz szablonów usługi Resource Manager do repozytorium Git (repozytoriów platformy Azure lub GitHub) i Dodaj [prywatnym repozytorium szablonów](devtest-lab-test-env.md) do laboratorium. Ten scenariusz może okazać się przydatne, jeśli używasz usługi DevTest Labs do komputerów-hostów rozwoju, ale mogą być przydatne, jeśli tworzysz środowisko przejściowe, który jest reprezentatywny dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub względem poszczególnych opcji użytkownika tylko ogranicza liczbę maszyn natywnie utworzonych w laboratorium, a nie przez dowolnego środowiska (szablony usługi Resource Manager).

## <a name="custom-images"></a>Obrazy niestandardowe

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak mogę skonfigurować łatwe powtarzalnego procesu do wprowadzenia Moje niestandardowe obrazy organizacji w środowisku usługi DevTest Labs?
Zobacz ten [wideo na wzorcu fabrycznie obrazu](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). W tym scenariuszu jest to zaawansowany scenariusz, a podane skrypty są przykładowe skrypty. Jeśli są wymagane zmiany, musisz zarządzać i obsługiwać skrypty używane w danym środowisku.

Aby uzyskać szczegółowe informacje na temat tworzenia fabryki danych obrazu, zobacz [Utwórz fabrykę obrazu niestandardowego w usłudze Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaka jest różnica między niestandardowego obrazu i formuły?
Niestandardowy obraz jest obrazem zarządzanych. Formuła jest obraz, który można skonfigurować z użyciem ustawień dodatkowych, a następnie zapisz i odtworzenia. Niestandardowy obraz może być korzystniejsze, jeśli chcesz szybko utworzyć kilka środowisk przy użyciu tego samego obrazu podstawowego, niezmienne. Formuła może być lepiej, jeśli chcesz odtworzyć konfiguracji maszyny wirtualnej za pomocą najnowsze elementy w ramach sieci wirtualnej lub podsieci lub jako Maszynę wirtualną o określonym rozmiarze. Aby uzyskać szczegółowe informacje, zobacz [porównanie niestandardowych obrazów i formuł w usłudze DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kiedy używać formuły, a obrazu niestandardowego?
Zazwyczaj decydującym czynnikiem w tym scenariuszu jest kosztowne i ponowne użycie. W przypadku scenariusza, gdy wielu użytkowników/labs wymagają obrazu z dużą ilością oprogramowania dodaną do obrazu podstawowego można zmniejszyć koszty przez utworzenie niestandardowego obrazu. Oznacza to, że po utworzeniu obrazu. Zmniejsza to czas instalacji maszyny wirtualnej oraz koszt ze względu na maszynie wirtualnej podczas przeprowadzana jest konfiguracja.

Dodatkowy czynnik należy pamiętać, jest jednak częstotliwość zmian do pakietu oprogramowania. Po uruchomieniu codziennie kompilacje i wymagają, że oprogramowanie do na maszynach wirtualnych użytkowników, należy wziąć pod uwagę przy użyciu formuły zamiast obrazu niestandardowego.

Aby uzyskać szczegółowe informacje, zobacz [porównanie niestandardowych obrazów i formuł](devtest-lab-comparing-vm-base-image-types.md) w usłudze DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak automatyzować proces przekazywania plików wirtualnego dysku twardego do tworzenia obrazów niestandardowych?

Aby zautomatyzować przekazywanie plików wirtualnego dysku twardego do tworzenia niestandardowych obrazów, masz dwie opcje:

- Użyj [AzCopy](../storage/common/storage-use-azcopy-v10.md) do kopiowania lub przekazywania plików VHD do konta magazynu, która jest skojarzona z laboratorium.
- Użyj [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Eksplorator usługi Storage jest aplikacją autonomiczną, z systemem Windows, OS X i Linux.

Aby znaleźć docelowe konto magazynu, która jest skojarzona z laboratorium:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  W menu po lewej stronie wybierz **grup zasobów**.
3.  Znajdź i wybierz grupę zasobów, która jest skojarzona z laboratorium.
4.  W obszarze **Przegląd**, wybierz jedno z kont magazynu.
5.  Wybierz pozycję **Obiekty blob**.
6.  Wygląd na potrzeby przekazywania na liście. Jeśli żaden nie istnieje, wróć do kroku 4 i spróbuj użyć innego konta magazynu.
7.  Użyj **adresu URL** jako miejsce docelowe w poleceniu narzędzia AzCopy.

Kiedy używać obrazu witryny Azure Marketplace a własnego niestandardowego obrazu organizacji?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kiedy używać obrazu witryny Azure Marketplace a własnego niestandardowego obrazu organizacji?
Portal Azure Marketplace należy używać domyślnie, chyba że masz określone problemy lub wymagań organizacyjnych. Typowe przykłady obejmują;

- Ustawienia skomplikowanego oprogramowania, wymagającego aplikację do uwzględnienia jako część obrazu podstawowego.
- Instalacja i Konfiguracja aplikacji może potrwać wiele godzin, które nie są efektywne wykorzystanie czasu obliczeń, które ma zostać dodany w obrazie w portalu Azure Marketplace.
- Deweloperom i testerom szybko wymagają dostępu do maszyny wirtualnej i chcesz zminimalizować czas instalacji nowej maszyny wirtualnej.
- Zgodność lub regulacji prawnych (na przykład zasady zabezpieczeń), które muszą być spełnione dla wszystkich maszyn.
- Przy użyciu niestandardowych obrazów nie należy uznać za niewielkim stopniu. Wprowadzają dodatkowe złożoność, ponieważ teraz trzeba zarządzać pliki VHD te obrazy podstawowe. Należy również regularnie patch te obrazy podstawowe z aktualizacjami oprogramowania. Te aktualizacje obejmują nowe aktualizacje systemu operacyjnego (OS) i wszelkie aktualizacje lub zmiany konfiguracji potrzebne do pakietu oprogramowania, sam.

## <a name="artifacts"></a>Artefakty

### <a name="what-are-artifacts"></a>Co to są artefaktów?
Artefakty są możliwe do dostosowania elementy, które można użyć, aby wdrożyć swoje najnowsze elementy lub wdrożyć narzędzia programistyczne na maszynie Wirtualnej. Podczas tworzenia maszyny Wirtualnej, dołączyć artefaktów z maszyną wirtualną. Po aprowizacji maszyny Wirtualnej artefakty wdrażanie i konfigurowanie maszyny Wirtualnej. Różne istniejące artefaktów dostępnych w naszej [publicznego repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Możesz również [tworzyć własne artefaktów](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Moje artefaktu nie powiodło się podczas tworzenia maszyny Wirtualnej. Jak rozwiązywać je
Aby dowiedzieć się, jak pobrać dzienniki dla Twojego artefaktu nie powiodło się, zobacz [jak diagnozowanie błędów artefaktów w usłudze DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Gdy organizacja należy używać publiczne repozytorium artefaktów i repozytorium artefaktów prywatne w usłudze DevTest Labs?
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zapewnia początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga przy użyciu szybkiego wdrażania bez konieczności inwestowania czasu do odtworzenia popularnych narzędzi dla deweloperów i dodatków. Można wdrożyć własne repozytorium prywatnego. Można użyć publicznego i prywatnego repozytorium równolegle. Można również wyłączyć repozytorium publicznego. Odpowiednie kryteria, aby wdrożyć repozytorium prywatnego powinien opierać się następujące pytania i uwagi:

- Czy organizacja ma wymagane są firmowych oprogramowania licencjonowanego w ramach swojej oferty usługi DevTest Labs? Jeśli odpowiedź jest twierdząca, powinny być tworzone w repozytorium prywatnym.
- Organizacji mogą tworzyć niestandardowe oprogramowanie, które udostępnia określoną operację, która jest wymagana całego procesu inicjowania obsługi administracyjnej? Jeśli odpowiedź jest twierdząca, powinny być wdrażane w repozytorium prywatnym.
- Jeśli zasady ładu organizacji wymaga izolacji i zewnętrznych repozytoriów nie są objęte zarządzaniem bezpośredniej konfiguracji przez organizację, powinny być wdrażane repozytorium prywatnego artefaktu. W ramach tego procesu tworzy początkową kopię repozytorium publicznego można skopiować i zintegrowane z prywatnym repozytorium. Następnie publicznego repozytorium można wyłączyć tak, aby nikt w organizacji będą mogli go dłużej. Ta metoda wymusza wszyscy użytkownicy w organizacji mieli tylko jednego repozytorium, które zostanie zaakceptowana przez organizację i zminimalizować nieaktualną konfigurację.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Należy organizacji planowanie jednym repozytorium lub zezwolić na wiele repozytoriów
Jako część ogólnej nadzoru Twojej organizacji i strategii zarządzania konfiguracji zaleca się, że używasz scentralizowane repozytorium. Korzystając z wieloma repozytoriami, może zostać silosów niezarządzanych oprogramowania wraz z upływem czasu. Z centralnym repozytorium wielu zespołów można skorzystać z artefaktów z tego repozytorium do swoich projektów. Wymusza normalizacji, zabezpieczenia i łatwość zarządzania i eliminuje dublowania wysiłków. W ramach centralnego wskazówki dotyczące zarządzania długoterminowego i dbałość zaleca się następujące akcje:

- Z tą samą dzierżawą usługi Azure Active Directory, używanej subskrypcji platformy Azure do uwierzytelniania i autoryzacji, należy skojarzyć repozytoriów platformy Azure.
- Utwórz grupę o nazwie `All DevTest Labs Developers` w usłudze Azure Active Directory, które jest zarządzane centralnie. Każdy deweloper, który przyczynia się do rozwoju artefaktu będzie umieszczona w tej grupie.
- Tej samej grupy usługi Azure Active Directory mogą służyć do zapewnienia dostępu do repozytorium Azure repozytoriów i laboratorium.
- W repozytoriach usługi Azure tworzenia rozgałęzień lub rozwidlenia powinny służyć do repozytorium oddzielne w rozwoju z repozytorium produkcji podstawowej. Zawartość jest dodawana tylko do gałęzi głównej, przy użyciu żądania ściągnięcia, po dokonaniu przeglądu prawidłowego kodu. Gdy recenzent kodu zatwierdza zmiany, główny programista, który jest odpowiedzialny za konserwację gałęzi głównej, scala zaktualizowany kod.

## <a name="cicd-integration"></a>Integracja ciągła Integracja/ciągłe dostarczanie

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Czy usługa DevTest Labs można zintegrować z Mój łańcuch narzędzi ciągłej integracji/ciągłego Dostarczania?
Jeśli używasz DevOps platformy Azure, możesz użyć [rozszerzenie usługi DevTest Labs zadania](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) zautomatyzować potok wydania w usłudze DevTest Labs. Oto niektóre z zadań, które można wykonać za pomocą tego rozszerzenia:

- Tworzenie i wdrażanie maszyny Wirtualnej automatycznie. Można także skonfigurować maszynę Wirtualną przy użyciu najnowszej kompilacji za pomocą zadania kopiowania plików na platformę Azure lub programu PowerShell usługi Azure DevOps Services.
- Automatyczne Przechwytywanie stan maszyny Wirtualnej po przetestowaniu do odtworzenia błędu w tej samej maszyny Wirtualnej w celu bliższego zbadania problemu.
- Gdy nie jest już potrzebny, należy usunąć maszynę Wirtualną na końcu potoku tworzenia wersji.

Wpisy na blogu następujące wskazówki oferty i informacje na temat przy użyciu rozszerzenia usługom DevOps platformy Azure:

- [Rozszerzenie DevOps platformy Azure i usłudze DevTest Labs](integrate-environments-devops-pipeline.md)
- [Wdrożenie nowej maszyny Wirtualnej w istniejącej laboratorium DevTest Labs z usługom DevOps platformy Azure](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Za pomocą usługi DevOps platformy Azure release management dla ciągłych wdrożeń użytkownik usługi DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Dla innych ciągłej integracji (CI) / kompilatorach ciągłe dostarczanie (CD) możesz uzyskać te same scenariusze przez wdrażanie [szablonów usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/) przy użyciu [poleceń cmdlet programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) i [Zestawy SDK platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Możesz również użyć [interfejsy API REST dla usługi DevTest Labs](https://aka.ms/dtlrestapis) do integracji z łańcucha narzędzi.

## <a name="networking"></a>Networking

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kiedy utworzyć nową sieć wirtualną dla środowiska Moje DevTest Labs, a za pomocą istniejącej sieci wirtualnej?
Jeśli Twoje maszyny wirtualne muszą wchodzić w interakcje z istniejącą infrastrukturą, rozważ użycie istniejącej sieci wirtualnej w środowisku laboratorium. Jeśli korzystasz z usługi ExpressRoute, możesz chcieć minimalizację liczby sieci wirtualnych / podsieci, aby nie fragmentu przestrzenią adresów IP, pobiera przypisaną do użycia w ramach subskrypcji. 

Rozważ użycie wzorca komunikacji równorzędnej sieci wirtualnej w tym miejscu ([modelu piasty i szprych](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) zbyt. Takie podejście umożliwia sieci wirtualnej/podsieci komunikacji między subskrypcjami. W przeciwnym razie każde środowisko usługi DevTest Labs może mieć własną sieć wirtualną. 

Istnieją [limity](../azure-subscription-service-limits.md) liczby sieci wirtualnych na subskrypcję. Wartość domyślna wynosi 50, chociaż ten limit może wzrosnąć do 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kiedy używać udostępnionego IP a publicznym adresem IP a prywatny adres IP?
 
Jeśli używasz sieci VPN lokacja lokacja lub Expressroute, należy wziąć pod uwagę przy użyciu prywatnych adresów IP, maszynach są dostępne za pośrednictwem sieci wewnętrznej i niedostępne za pośrednictwem publicznej sieci internet.

> [!NOTE]
> Laboratorium właścicieli można zmienić te zasady podsieci, upewnij się, nikt przypadkowo tworzy publiczne adresy IP dla maszyn wirtualnych. Właściciel subskrypcji należy utworzyć zasady subskrypcji, uniemożliwiając tworzona publicznych adresów IP.

Korzystając z udostępnionych, publicznych adresów IP, maszyny wirtualne w laboratorium udostępniać publicznego adresu IP. Takie podejście może być przydatne, gdy potrzebujesz w celu uniknięcia przekroczenia limitów dla publicznych adresów IP w ramach danej subskrypcji.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Jak upewnij się, tworzenia i testowania maszyny wirtualne są nie można nawiązać połączenia z publicznego Internetu? Czy istnieją wszystkie zalecane wzorców do ustawiania konfiguracji sieci?

Tak. Istnieją dwie kwestie należy wziąć pod uwagę — ruch przychodzący i wychodzący.

- **Ruch przychodzący** — Jeśli maszyna wirtualna nie ma publicznego adresu IP, wówczas nie można nawiązać połączenia przez internet. Typowym podejściem jest zapewnienie, że ustawiono zasady na poziomie subskrypcji, tak, aby żaden użytkownik nie można utworzyć publiczny adres IP.
- **Ruch wychodzący** — Jeśli chcesz zapobiec maszyn wirtualnych z bezpośrednim dostępem do publicznej sieci internet i wymuszają ruch za pośrednictwem zapory firmowej, a następnie może kierować ruch w środowisku lokalnym za pośrednictwem expressroute lub sieci VPN, przy użyciu wymuszają routingu.

> [!NOTE]
> Jeśli masz serwer proxy, która blokuje ruch bez ustawienia serwera proxy, nie zapomnij dodać wyjątki na konto magazynu artefaktów laboratorium.

Można również użyć grup zabezpieczeń sieci dla maszyn wirtualnych lub podsieci. Ten krok powoduje dodanie dodatkową warstwę ochrony, aby umożliwić / blokować ruch.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Dlaczego nie jest Mój istniejący wirtualny sieci zapisywanie prawidłowo?
Jedną z możliwości jest, że Twoja nazwa sieci wirtualnej zawiera kropek. Jeśli tak, spróbuj usuwanie okresy lub zastąpieniu łączników. Następnie spróbuj ponownie zapisać sieci wirtualnej.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Dlaczego otrzymuję błąd "Nie znaleziono zasobu nadrzędnego" podczas aprowizowania maszyny Wirtualnej za pomocą programu PowerShell?
Jeśli jeden zasób jest nadrzędny do innego zasobu, zasób nadrzędny musi istnieć przed przystąpieniem do tworzenia zasobów podrzędnych. Jeśli zasób nadrzędny nie istnieje, zostanie wyświetlony **ParentResourceNotFound** wiadomości. Jeśli nie określisz zależność od zasobu nadrzędnego, przed nadrzędnego może wdrożyć zasobów podrzędnych.

Maszyny wirtualne są zasobami podrzędnymi w ramach laboratorium w grupie zasobów. Korzystając z szablonów usługi Resource Manager do wdrażania maszyn wirtualnych przy użyciu programu PowerShell, nazwa grupy zasobów, które są udostępniane w skrypcie programu PowerShell powinna być nazwa grupy zasobów w środowisku laboratoryjnym. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie typowych problemów z wdrożeniem platformy](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Gdzie można znaleźć więcej informacji o błędzie w przypadku niepowodzenia wdrożenia maszyny Wirtualnej?
Błędy wdrożenia maszyny Wirtualnej są przechwytywane w dziennikach aktywności. Laboratorium można znaleźć w dziennikach aktywności maszyn wirtualnych w ramach **dzienniki inspekcji** lub **diagnostyki maszyny wirtualnej** w menu zasobów na stronie maszyny Wirtualnej w laboratorium (zostanie wyświetlona po wybraniu maszynę Wirtualną z listy maszyn wirtualnych).

Czasami błąd wdrożenia występuje przed rozpoczęciem wdrażania maszyny Wirtualnej. Przykładem jest, gdy zostanie przekroczony limit subskrypcji dla zasobu, który został utworzony z maszyną Wirtualną. W tym przypadku szczegóły błędu są przechwytywane w dziennikach aktywności lab poziomie. Dzienniki aktywności znajdują się w dolnej części **konfiguracji i zasad** ustawienia. Więcej informacji na temat używania działania dzienników na platformie Azure, zobacz [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](../azure-resource-manager/resource-group-audit.md).




