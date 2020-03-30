---
title: Często zadawane pytania dotyczące laboratoriów testów deweloperskich platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na niektóre z często zadawanych pytań (FAQ) dotyczących usługi Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270786"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs — często zadawane pytania
Uzyskaj odpowiedzi na niektóre z najczęstszych pytań dotyczących laboratoriów DevTest Platformy Azure.

## <a name="blog-post"></a>Blogu
Nasz blog DevTest Labs Team został wycofany z 20 marca 2019 r. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Gdzie mogę śledzić aktualizacje funkcji od teraz?
Od tej pory będziemy publikować aktualizacje funkcji i pouczające wpisy w blogu na blogu platformy Azure i aktualizacje platformy Azure. Te wpisy na blogu będą również link do naszej dokumentacji, gdziekolwiek jest to wymagane.

Subskrybuj aktualizacje [bloga Platformy Azure devtest labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) i [devtest labs,](https://azure.microsoft.com/updates/?product=devtest-lab) aby być na bieżąco z nowymi funkcjami w laboratoriach DevTest.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co dzieje się z istniejącymi wpisami w blogu?
Obecnie pracujemy nad migracją istniejących wpisów w blogu (z wyłączeniem aktualizacji awarii) do naszej [dokumentacji DevTest Labs.](devtest-lab-overview.md) Gdy blog MSDN jest przestarzały, zostanie przekierowany do przeglądu dokumentacji devtest labs. Po przekierowaniu możesz wyszukać artykuł, którego szukasz, w tytule "Filtruj według". Nie przeprowadziliśmy jeszcze migracji wszystkich postów, ale powinny zostać wykonane do końca tego miesiąca. 


### <a name="where-do-i-see-outage-updates"></a>Gdzie mogę zobaczyć aktualizacje awarii?
Od teraz będziemy publikować aktualizacje dotyczące awarii za pomocą uchwytu Twittera. Śledź nas na Twitterze, aby uzyskać najnowsze informacje na temat awarii i znanych błędów.

### <a name="twitter"></a>Twitter
Nasz uchwyt Twitter:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli nie ma tutaj odpowiedzi na moje pytanie?
Jeśli twojego pytania nie ma na liście, poinformuj nas o tym, abyśmy mogli pomóc Ci znaleźć odpowiedź.

- Zadaj pytanie na końcu tego faq.
- Aby dotrzeć do szerszego grona odbiorców, opublikuj pytanie na [forum usługi Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Nawiąż kontakt z zespołem Azure DevTest Labs i innymi członkami społeczności.
- W przypadku żądań funkcji prześlij swoje żądania i pomysły do [usługi Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co to jest konto Microsoft?
Konto Microsoft to konto używane do prawie wszystkiego, co robisz z urządzeniami i usługami firmy Microsoft. Jest to adres e-mail i hasło używane do logowania się do Skype, Outlook.com, OneDrive, Windows Phone, Azure i Xbox Live. Jedno konto oznacza, że twoje pliki, zdjęcia, kontakty i ustawienia mogą cię obserwować na dowolnym urządzeniu.

> [!NOTE]
> Konto Microsoft było kiedyś nazywane identyfikatorem Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Dlaczego warto korzystać z usługi Azure DevTest Labs?
Laboratorium DevTest w usłudze Azure może zaoszczędzić czas i pieniądze zespołu. Deweloperzy mogą tworzyć własne środowiska przy użyciu kilku różnych baz. Mogą również używać artefaktów do szybkiego wdrażania i konfigurowania aplikacji. Za pomocą niestandardowych obrazów i formuł można zapisywać maszyny wirtualne (maszyny wirtualne) jako szablony i łatwo odtworzyć je w całym zespole. DevTest Labs oferuje również kilka konfigurowalnych zasad, których administratorzy laboratorium mogą używać do zmniejszania ilości odpadów i zarządzania środowiskami zespołu. Zasady te obejmują automatyczne zamykanie, próg kosztów, maksymalną liczbę maszyn wirtualnych na użytkownika i maksymalny rozmiar maszyny Wirtualnej. Aby uzyskać bardziej szczegółowe wyjaśnienie DevTest Labs, zobacz [omówienie](devtest-lab-overview.md) lub [film wprowadzający](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co oznacza "bezproblemowa samoobsługa"?
Bezproblemowa samoobsługa oznacza, że deweloperzy i testerzy tworzą własne środowiska w razie potrzeby. Administratorzy mają zabezpieczenia wiedząc, że DevTest Labs może pomóc ustawić odpowiedni dostęp, zminimalizować koszty odpadów i kontroli. Administratorzy mogą określić, które rozmiary maszyn wirtualnych są dozwolone, maksymalna liczba maszyn wirtualnych oraz kiedy maszyny wirtualne są uruchamiane i zamykane. DevTest Labs ułatwia również monitorowanie kosztów i ustawianie alertów, aby pomóc Ci być świadomym tego, jak używane są zasoby laboratoryjne.

### <a name="how-can-i-use-devtest-labs"></a>Jak korzystać z DevTest Labs?
DevTest Labs jest przydatne w każdej chwili wymagają środowisk deweloperskich lub testowych i chcesz je szybko odtworzyć lub zarządzać nimi przy użyciu zasad oszczędzania kosztów.
Oto kilka scenariuszy, dla których nasi klienci używają DevTest Labs:

- Zarządzaj środowiskami deweloperskimi i testowymi w jednym miejscu. Użyj zasad, aby zmniejszyć koszty i tworzyć niestandardowe obrazy do udostępniania kompilacji w całym zespole.
- Opracowanie aplikacji przy użyciu obrazów niestandardowych, aby zapisać stan dysku na wszystkich etapach rozwoju.
- Śledzenie kosztów w odniesieniu do postępu.
- Tworzenie środowisk testów masowych do testowania zapewnienia jakości.
- Użyj artefaktów i formuł, aby łatwo skonfigurować i odtworzyć aplikację w różnych środowiskach.
- Rozpowszechniaj maszyny wirtualne dla hackathonów (wspólne tworzenie lub testowanie), a następnie łatwo usuwaj ich aprowizowanie po zakończeniu zdarzenia.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak rozliczane są płatności za DevTest Labs?
DevTest Labs to bezpłatna usługa. Tworzenie laboratoriów i konfigurowanie zasad, szablonów i artefaktów w DevTest Labs jest bezpłatne. Płacisz tylko za zasoby platformy Azure używane w laboratoriach, takie jak maszyny wirtualne, konta magazynu i sieci wirtualne. Aby uzyskać więcej informacji na temat kosztów zasobów laboratoryjnych, zobacz [Cennik laboratoriów DevTest Platformy Azure.](https://azure.microsoft.com/pricing/details/devtest-lab/)

## <a name="security"></a>Zabezpieczenia

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Jakie są różne poziomy zabezpieczeń w DevTest Labs?
Dostęp do zabezpieczeń jest określany przez kontrolę dostępu opartą na rolach (RBAC). Aby dowiedzieć się, jak działa dostęp, pomaga dowiedzieć się różnice między uprawnienia, roli i zakresu, zgodnie z definicją RBAC.

- **Uprawnienie:** Uprawnienie jest zdefiniowanym dostępem do określonej akcji. Na przykład uprawnienia można odczytać dostęp do wszystkich maszyn wirtualnych.
- **Rola:** Rola to zestaw uprawnień, które można zgrupować i przypisać do użytkownika. Na przykład użytkownik z rolą właściciela subskrypcji ma dostęp do wszystkich zasobów w ramach subskrypcji.
- **Zakres:** Zakres jest poziomem w hierarchii zasobu platformy Azure. Na przykład zakres może być grupa zasobów, pojedyncze laboratorium lub całej subskrypcji.

W zakresie DevTest Labs istnieją dwa typy ról definiujących uprawnienia użytkownika:

- **Właściciel laboratorium:** Właściciel laboratorium ma dostęp do wszystkich zasobów w laboratorium. Właściciel laboratorium może modyfikować zasady, odczytywać i zapisywać na dowolnej maszynie wirtualnej, zmieniać sieć wirtualną i tak dalej.
- **Użytkownik laboratorium:** użytkownik laboratorium może wyświetlać wszystkie zasoby laboratoryjne, takie jak maszyny wirtualne, zasady i sieci wirtualne. Jednak użytkownik laboratorium nie można modyfikować zasad lub maszyn wirtualnych, które zostały utworzone przez innych użytkowników.

Można również utworzyć role niestandardowe w DevTest Labs. Aby dowiedzieć się, jak tworzyć role niestandardowe w laboratoriach DevTest, zobacz [Przyznawanie uprawnień użytkowników do określonych zasad laboratoryjnych.](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)

Ponieważ zakresy są hierarchiczne, gdy użytkownik ma uprawnienia w określonym zakresie, użytkownik jest automatycznie przyznawane te uprawnienia w każdym zakresie niższego poziomu w zakresie. Na przykład jeśli użytkownik jest przypisany rolę właściciela subskrypcji, użytkownik ma dostęp do wszystkich zasobów w ramach subskrypcji. Zasoby te obejmują maszyny wirtualne, sieci wirtualne i laboratoria. Właściciel subskrypcji automatycznie dziedziczy rolę właściciela laboratorium. Jednak nie jest odwrotnie. Właściciel laboratorium ma dostęp do laboratorium, który jest niższy zakres niż poziom subskrypcji. Dlatego właściciel laboratorium nie widzi maszyn wirtualnych, sieci wirtualnych ani innych zasobów, które znajdują się poza laboratorium.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Jak zdefiniować kontrolę dostępu opartą na rolach dla moich środowisk DevTest Labs, aby upewnić się, że it może regulować, podczas gdy deweloperzy /test mogą wykonywać swoją pracę?
Istnieje szeroki wzorzec, jednak szczegóły zależą od organizacji.

Centralna dział IT powinna posiadać tylko to, co jest konieczne i umożliwiać zespołom projektowym i zespołowym posiadanie wymaganego poziomu kontroli. Zazwyczaj oznacza to, że centralna firma IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zestaw **właścicieli** subskrypcji powinien być mały. Ci właściciele mogą wyznaczyć dodatkowych właścicieli, gdy zajdzie taka potrzeba, lub zastosować zasady na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może istnieć podzbiór użytkowników, którzy wymagają dostępu w ramach subskrypcji, takich jak obsługa warstwy 1 lub warstwy 2. W takim przypadku zaleca się przyznanie tym użytkownikom dostępu **współautora,** aby mogli zarządzać zasobami, ale nie zapewniali dostępu użytkowników ani nie dostosowywali zasad.

Zasób DevTest Labs powinien być własnością właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. To dlatego, że rozumieją swoje wymagania dotyczące maszyn i wymagane oprogramowanie. W większości organizacji właścicielem tego zasobu DevTest Labs jest zwykle potencjalny klient projektu/rozwoju. Ten właściciel może zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkimi maszynami wirtualnymi w środowisku DevTest Labs.

Członkowie zespołu projektu/aplikacji powinny zostać dodane do roli **Użytkownicy DevTest Labs.** Ci użytkownicy mogą tworzyć maszyny wirtualne (zgodne z zasadami na poziomie laboratorium i subskrypcji). Mogą również zarządzać własnymi maszynami wirtualnymi. Nie mogą zarządzać maszynami wirtualnymi należącymi do innych użytkowników.

Aby uzyskać więcej informacji, zobacz [szkielet przedsiębiorstwa platformy Azure — dokumentacja zarządzania preskrypcyjną subskrypcji.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Jak utworzyć rolę, aby umożliwić użytkownikom wykonać określone zadanie?
Aby uzyskać obszerny artykuł dotyczący tworzenia ról niestandardowych i przypisywania uprawnień do roli, zobacz [Przyznawanie uprawnień użytkowników do określonych zasad laboratoryjnych](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Oto przykład skryptu, który tworzy rolę **DevTest Labs Advanced User**, który ma uprawnienia do uruchamiania i zatrzymywania wszystkich maszyn wirtualnych w laboratorium:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>W jaki sposób organizacja może zapewnić, że zasady zabezpieczeń firmy są w miejscu?
Organizacja może to osiągnąć, wykonując następujące czynności:

- Opracowanie i opublikowanie kompleksowej polityki bezpieczeństwa. Zasady określa zasady dopuszczalnego użytkowania związane z oprogramowaniem, zasobami w chmurze. Określa również, co wyraźnie narusza zasady.
- Opracowanie niestandardowego obrazu, artefaktów niestandardowych i procesu wdrażania, który umożliwia aranżację w obrębie obszaru zabezpieczeń zdefiniowanego za pomocą usługi Active Directory. Takie podejście wymusza granice firmy i ustawia wspólny zestaw kontroli środowiska. Te formanty środowiska dewelopera można wziąć pod uwagę, jak opracowują i postępuj zgodnie z bezpiecznym cyklem życia rozwoju w ramach ich ogólnego procesu. Celem jest również zapewnienie środowiska, które nie jest zbyt restrykcyjne, co może utrudniać rozwój, ale rozsądny zestaw kontroli. Zasady grupy w jednostce organizacyjnej (OU), która zawiera maszyny wirtualne w laboratorium może być podzbiorem zasad grupy całkowitej, które znajdują się w środowisku produkcyjnym. Alternatywnie mogą one być dodatkowym zestawem, aby właściwie ograniczyć wszelkie zidentyfikowane zagrożenia.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>W jaki sposób organizacja może zapewnić integralność danych, aby zapewnić, że deweloperzy zajmujący się zdalnym tworzeniem danych nie mogą usunąć kodu ani wprowadzić złośliwego oprogramowania lub niezatwierdzonego oprogramowania?
Istnieje kilka warstw kontroli w celu złagodzenia zagrożenia ze strony zewnętrznych konsultantów, wykonawców lub pracowników, którzy są komunikacji zdalnej w celu współpracy w DevTest Labs.

Jak wspomniano wcześniej, pierwszy krok musi mieć zasady dopuszczalnego użytkowania opracowane i zdefiniowane, które jasno określają konsekwencje, gdy ktoś narusza zasady.

Pierwszą warstwą formantów dostępu zdalnego jest zastosowanie zasad dostępu zdalnego za pośrednictwem połączenia sieci VPN, które nie jest bezpośrednio połączone z laboratorium.

Druga warstwa formantów polega na zastosowaniu zestawu obiektów zasad grupy, które uniemożliwiają kopiowanie i wklejanie za pośrednictwem pulpitu zdalnego. Można zaimplementować zasady sieciowe, aby nie zezwalać na korzystanie ze środowiska usług wychodzących, takich jak usługi FTP i RDP. Routing zdefiniowany przez użytkownika może wymusić cały ruch sieciowy platformy Azure z powrotem do lokalnego, ale routing nie może uwzględniać wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, chyba że jest kontrolowana za pośrednictwem serwera proxy do skanowania zawartości i sesji. Publiczne adresy IP mogą być ograniczone w sieci wirtualnej obsługującej DevTest Labs, aby nie zezwalać na mostkowanie zewnętrznego zasobu sieciowego.

Ostatecznie ten sam typ ograniczeń musi być stosowany w całej organizacji, co uwzględniałoby wszystkie możliwe metody nośników wymiennych lub zewnętrznych adresów URL, które mogłyby akceptować wpis treści. Skonsultuj się ze swoim specjalistą w dziedzinie zabezpieczeń, aby przejrzeć i wdrożyć zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfiguracja laboratorium

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak utworzyć laboratorium z szablonu Menedżera zasobów?
Oferujemy [repozytorium GitHub szablonów usługi Azure Resource Manager laboratorium,](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) które można wdrożyć w stanie takim, w jakim jest lub zmodyfikować, aby utworzyć szablony niestandardowe dla laboratoriów. Każdy szablon ma łącze do wdrażania laboratorium, jak to jest w ramach własnej subskrypcji platformy Azure. Można też dostosować szablon i wdrożyć za [pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure.](../azure-resource-manager/templates/deploy-powershell.md)


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Czy mogę mieć wszystkie maszyny wirtualne do utworzenia w wspólnej grupie zasobów zamiast każdej maszyny w własnej grupie zasobów?
Tak, jako właściciel laboratorium możesz pozwolić laboratorium obsługiwać alokację grupy zasobów dla Ciebie lub mieć wszystkie maszyny wirtualne utworzone we wspólnej grupie zasobów, które określisz.

Osobny scenariusz grupy zasobów:
-   DevTest Labs tworzy nową grupę zasobów dla każdej publicznej/prywatnej maszyny wirtualnej IP, którą rozkręcasz
-   DevTest Labs tworzy grupę zasobów dla udostępnionych komputerów IP, które należą do tego samego rozmiaru.

Typowy scenariusz grupy zasobów:
-   Wszystkie maszyny wirtualne są obracane w określonej wspólnej grupie zasobów. Dowiedz się więcej [Alokacja grupy zasobów dla laboratorium](https://aka.ms/RGControl).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Jak utrzymywać konwencję nazewnictwa w moim środowisku DevTest Labs?
Można rozszerzyć bieżące konwencje nazewnictwa przedsiębiorstwa do operacji platformy Azure i uczynić je spójne w środowisku DevTest Labs. Podczas wdrażania DevTest Labs, zaleca się, że masz określone zasady uruchamiania. Te zasady można wdrożyć za pomocą skryptu centralnego i szablonów JSON, aby wymusić spójność. Zasady nazewnictwa można zaimplementować za pomocą zasad platformy Azure stosowanych na poziomie subskrypcji. W przypadku przykładów JSON dla zasad platformy Azure zobacz [przykłady zasad platformy Azure](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ile laboratoriów można utworzyć w ramach tej samej subskrypcji?
Nie ma określonego limitu liczby laboratoriów, które można utworzyć na subskrypcję. Jednak ilość zasobów używanych na subskrypcję jest ograniczona. Możesz przeczytać o [limitach i przydziałach dla subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) oraz o [tym, jak zwiększyć te limity.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)


### <a name="how-many-vms-can-i-create-per-lab"></a>Ile maszyn wirtualnych można utworzyć na laboratorium?
Nie ma określonego limitu liczby maszyn wirtualnych, które mogą być tworzone w laboratorium. Jednak zasoby (rdzenie maszyn wirtualnych, publiczne adresy IP i tak dalej), które są używane są ograniczone dla subskrypcji. Możesz przeczytać o [limitach i przydziałach dla subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) oraz o [tym, jak zwiększyć te limity.](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Jak określić stosunek użytkowników do laboratorium i ogólną liczbę laboratoriów, które są potrzebne w całej organizacji?
Zaleca się, że jednostki biznesowe i grupy programistyczne, które są skojarzone z tym samym projektem rozwoju są skojarzone z tego samego laboratorium. Umożliwia stosowanie tych samych typów zasad, obrazów i zasad zamknięcia systemu w obu grupach.

Konieczne może być również rozważenie granic geograficznych. Na przykład deweloperzy w północno-wschodnich Stanach Zjednoczonych (USA) mogą używać laboratorium aprowizacji we wschodnim us2. Deweloperzy w Dallas w Teksasie i Denver w stanie Kolorado mogą zostać skierowani do wykorzystania zasobu w południowo-środkowym usa. Jeśli istnieje wspólny wysiłek z zewnętrzną stroną trzecią, mogą one być przypisane do laboratorium, które nie jest używane przez wewnętrznych deweloperów.

Można również użyć laboratorium dla określonego projektu w ramach projektów usługi Azure DevOps. Następnie należy zastosować zabezpieczenia za pośrednictwem określonej grupy usługi Azure Active Directory, która umożliwia dostęp do obu zestawów zasobów. Sieć wirtualna przypisana do laboratorium może być inną granicą konsolidacji użytkowników.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak możemy zapobiec usuwaniu zasobów w laboratorium?
Zaleca się ustawienie odpowiednich uprawnień na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogli usuwać zasoby lub zmieniać zasady laboratorium. Deweloperzy powinni być umieszczane w grupie **Użytkownicy DevTest Labs.** Głównym deweloperem lub potencjalnym klientem infrastruktury powinien być **DevTest Labs Owner**. Zalecamy, aby mieć tylko dwóch właścicieli laboratorium. Ta zasada rozciąga się na repozytorium kodu, aby uniknąć uszkodzenia. Użytkownicy laboratorium mają prawa do korzystania z zasobów, ale nie można zaktualizować zasad laboratoryjnych. Zobacz następujący artykuł, który zawiera listę ról i praw, które każda wbudowana grupa ma w laboratorium: [Dodawanie właścicieli i użytkowników w laboratorium DevTest Azure](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak udostępnić bezpośredni link do laboratorium?

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do laboratorium.
2. Skopiuj **adres URL laboratorium** z przeglądarki, a następnie udostępnij go użytkownikom laboratorium.

> [!NOTE]
> Jeśli użytkownik laboratorium jest użytkownikiem zewnętrznym, który ma konto Microsoft, ale nie jest członkiem wystąpienia usługi Active Directory w organizacji, podczas próby uzyskania dostępu do łącza udostępnionego użytkownik może zobaczyć komunikat o błędzie. Jeśli użytkownik zewnętrzny widzi komunikat o błędzie, poproś użytkownika, aby najpierw wybrał swoją nazwę w prawym górnym rogu witryny Azure portal. Następnie w sekcji Katalog menu użytkownik może wybrać katalog, w którym istnieje laboratorium.

## <a name="virtual-machines"></a>Maszyny wirtualne

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Dlaczego nie widzę maszyn wirtualnych na stronie Maszyny wirtualne, które widzę w Laboratorium DevTest?
Podczas tworzenia maszyny Wirtualnej w DevTest Labs, masz uprawnienia dostępu do tej maszyny Wirtualnej. Maszynę Wirtualną można wyświetlić zarówno na stronie laboratoriów, jak i na stronie **Maszyny wirtualne.** Użytkownicy przypisani do roli **właściciel laboratoriów DevTest** mogą zobaczyć wszystkie maszyny wirtualne, które zostały utworzone w laboratorium na stronie **Wszystkie maszyny wirtualne** laboratorium. Jednak użytkownicy, którzy mają **devtest labs roli użytkownika** nie są automatycznie przyznane dostęp do odczytu do zasobów maszyn wirtualnych, które inni użytkownicy utworzyli. Dlatego te maszyny wirtualne nie są wyświetlane na stronie **Maszyny wirtualne.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak utworzyć wiele maszyn wirtualnych z tego samego szablonu naraz?
Dostępne są dwie opcje jednoczesnego tworzenia wielu maszyn wirtualnych z tego samego szablonu:

- Można użyć [rozszerzenia zadania programu Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Podczas tworzenia maszyny wirtualnej można [wygenerować szablon Menedżera zasobów](devtest-lab-add-vm.md#save-azure-resource-manager-template) i [wdrożyć szablon Menedżera zasobów w programie Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Można również określić więcej niż jedno wystąpienie maszyny, która ma zostać utworzona podczas tworzenia maszyny wirtualnej. Aby dowiedzieć się więcej na temat tworzenia wielu wystąpień maszyn wirtualnych, zobacz [dok.](devtest-lab-add-vm.md)

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak przenieść istniejące maszyny wirtualne platformy Azure do laboratorium DevTest Labs?
Aby skopiować istniejące maszyny wirtualne do laboratorium DevTest:

1.  Skopiuj plik VHD istniejącej maszyny Wirtualnej przy użyciu [skryptu programu Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Utwórz [obraz niestandardowy](devtest-lab-create-template.md) w laboratorium DevTest Labs.
3.  Utwórz maszynę wirtualną w laboratorium na podstawie obrazu niestandardowego.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Czy mogę podłączyć wiele dysków do maszyn wirtualnych?

Tak, można dołączyć wiele dysków do maszyn wirtualnych.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Jeśli chcę używać obrazu systemu operacyjnego Windows do testowania, czy muszę kupić subskrypcję MSDN?
Aby użyć obrazów systemu operacyjnego klienta systemu Windows (Windows 7 lub nowszej wersji) do tworzenia lub testowania na platformie Azure, należy wykonać jedną z następujących czynności:

- [Kup subskrypcję MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Jeśli masz umowę Enterprise Agreement, utwórz subskrypcję platformy Azure z [ofertą deweloper/test](https://azure.microsoft.com/offers/ms-azr-0148p)przedsiębiorstwa.

Aby uzyskać więcej informacji na temat kredytów platformy Azure dla każdej oferty MSDN, zobacz [Miesięczne środki platformy Azure dla subskrybentów programu Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak zautomatyzować proces usuwania wszystkich maszyn wirtualnych w laboratorium?
Jako właściciel laboratorium możesz usuwać maszyny wirtualne z laboratorium w witrynie Azure portal. Można również usunąć wszystkie maszyny wirtualne w laboratorium przy użyciu skryptu programu PowerShell. W poniższym przykładzie w obszarze **wartości, aby zmienić** komentarz, zmodyfikuj wartości parametrów. Można pobrać subscriptionId, labResourceGroup i labName wartości z okienka laboratorium w witrynie Azure portal.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak używać szablonów Menedżera zasobów w moim środowisku DevTest Labs?
Wdrażanie szablonów Menedżera zasobów w środowisku Laboratoriów DevTest przy użyciu kroków wymienionych w [funkcji Środowiska w DevTest Labs](devtest-lab-test-env.md) artykułu. Zasadniczo można sprawdzić szablony Menedżera zasobów w repozytorium Git (repozytorium platformy Azure lub GitHub) i dodać [prywatne repozytorium szablonów](devtest-lab-test-env.md) do laboratorium. Ten scenariusz może nie być przydatne, jeśli używasz DevTest Labs do obsługi maszyn deweloperskich, ale może być przydatne, jeśli budujesz środowisko przejściowe, który jest reprezentatywny dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub na użytkownika opcja ogranicza tylko liczbę maszyn natywnie utworzonych w samym laboratorium, a nie przez żadne środowiska (szablony Menedżera zasobów).

## <a name="custom-images"></a>Obrazy niestandardowe

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak skonfigurować łatwy do powtórzenia proces, aby przenieść niestandardowe obrazy organizacyjne do środowiska DevTest Labs?
Zobacz ten [film na wzór fabryki obrazu](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ten scenariusz jest scenariusz zaawansowany, a skrypty dostarczone są tylko przykładowe skrypty. Jeśli wymagane są jakiekolwiek zmiany, należy zarządzać i obsługiwać skrypty używane w danym środowisku.

Aby uzyskać szczegółowe informacje dotyczące tworzenia fabryki obrazów, zobacz [Tworzenie niestandardowej fabryki obrazów w laboratoriach DevTest Azure](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaka jest różnica między obrazem niestandardowym a formułą?
Obraz niestandardowy jest obrazem zarządzanym. Formuła to obraz, który można skonfigurować z dodatkowymi ustawieniami, a następnie zapisać i odtworzyć. Obraz niestandardowy może być korzystniejszy, jeśli chcesz szybko utworzyć kilka środowisk przy użyciu tego samego podstawowego, niezmiennego obrazu. Formuła może być lepsza, jeśli chcesz odtworzyć konfigurację maszyny wirtualnej z najnowszymi bitami, jako część sieci wirtualnej lub podsieci lub jako maszyna wirtualna o określonym rozmiarze. Aby uzyskać bardziej szczegółowe wyjaśnienie, zobacz [Porównywanie niestandardowych obrazów i formuł w devtest labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kiedy należy używać formuły a obrazu niestandardowego?
Zazwyczaj czynnikiem decydującym w tym scenariuszu jest koszt i ponowne użycie. Jeśli masz scenariusz, w którym wielu użytkowników /laboratoriów wymaga obrazu z dużą ilością oprogramowania na górze obrazu podstawowego, można zmniejszyć koszty, tworząc obraz niestandardowy. Oznacza to, że obraz jest tworzony raz. Skraca czas instalacji maszyny wirtualnej i koszty poniesione z powodu maszyny wirtualnej uruchomionej po zakończeniu instalacji.

Dodatkowym czynnikiem, na który należy zwrócić uwagę, jest częstotliwość zmian w pakiecie oprogramowania. Jeśli uruchamiasz codzienne kompilacje i wymagasz, aby oprogramowanie było na maszynach wirtualnych użytkowników, rozważ użycie formuły zamiast niestandardowego obrazu.

Aby uzyskać bardziej szczegółowe wyjaśnienie, zobacz [Porównywanie niestandardowych obrazów i formuł](devtest-lab-comparing-vm-base-image-types.md) w laboratorium DevTest.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak zautomatyzować proces przesyłania plików VHD w celu utworzenia obrazów niestandardowych?

Aby zautomatyzować przesyłanie plików VHD w celu utworzenia obrazów niestandardowych, dostępne są dwie opcje:

- Użyj [AzCopy,](../storage/common/storage-use-azcopy-v10.md) aby skopiować lub przekazać pliki VHD do konta magazynu skojarzonego z laboratorium.
- Użyj [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Eksplorator magazynu to autonomiczna aplikacja, która działa w systemach Windows, OS X i Linux.

Aby znaleźć docelowe konto magazynu skojarzone z laboratorium:

1.  Zaloguj się do [Portalu Azure](https://portal.azure.com).
2.  W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
3.  Znajdź i wybierz grupę zasobów skojarzoną z laboratorium.
4.  W **obszarze Przegląd**wybierz jedno z kont magazynu.
5.  Wybierz **pozycję Obiekty blob**.
6.  Poszukaj przesłanych na liście. Jeśli nie istnieje, wróć do kroku 4 i spróbuj użyć innego konta magazynu.
7.  Użyj **adresu URL** jako miejsca docelowego w poleceniu AzCopy.

Kiedy należy używać obrazu portalu Azure Marketplace w porównaniu z własnym niestandardowym obrazem organizacyjnym?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kiedy należy używać obrazu portalu Azure Marketplace w porównaniu z własnym niestandardowym obrazem organizacyjnym?
Usługa Azure Marketplace powinna być używana domyślnie, chyba że masz określone problemy lub wymagania organizacyjne. Niektóre typowe przykłady obejmują;

- Złożona konfiguracja oprogramowania, która wymaga, aby aplikacja została uwzględniona jako część obrazu podstawowego.
- Instalacja i konfiguracja aplikacji może potrwać wiele godzin, które nie są efektywne wykorzystanie czasu obliczeniowego, które mają być dodawane na obrazie portalu Azure Marketplace.
- Deweloperzy i testerzy wymagają szybkiego dostępu do maszyny wirtualnej i chcą zminimalizować czas instalacji nowej maszyny wirtualnej.
- Zgodność lub warunki regulacyjne (na przykład zasady zabezpieczeń), które muszą być obowiązują dla wszystkich komputerów.
- Używanie obrazów niestandardowych nie powinno być traktowane lekko. Wprowadzają one dodatkową złożoność, ponieważ teraz musisz zarządzać plikami VHD dla tych podstawowych obrazów podstawowych. Należy również rutynowo załatać te obrazy podstawowe za pomocą aktualizacji oprogramowania. Te aktualizacje obejmują nowe aktualizacje systemu operacyjnego (OS) oraz wszelkie aktualizacje lub zmiany konfiguracji potrzebne dla samego pakietu oprogramowania.

## <a name="artifacts"></a>Artefakty

### <a name="what-are-artifacts"></a>Co to są artefakty?
Artefakty to konfigurowalne elementy, których można użyć do wdrożenia najnowszych bitów lub wdrożenia narzędzi deweloperskich na maszynie wirtualnej. Dołączanie artefaktów do maszyny Wirtualnej podczas tworzenia maszyny Wirtualnej. Po zainicjowaniu obsługi administracyjnej artefakty wdrażają i konfigurują maszynę wirtualną. Różne istniejące artefakty są dostępne w naszym [publicznym repozytorium GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) Można również [tworzyć własne artefakty](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mój artefakt nie powiódł się podczas tworzenia maszyny Wirtualnej. Jak rozwiązać problem?
Aby dowiedzieć się, jak uzyskać dzienniki dla nieudanego artefaktu, zobacz [Jak zdiagnozować błędy artefaktów w devtest labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kiedy organizacja powinna używać publicznego repozytorium artefaktów w porównaniu z prywatnym repozytorium artefaktów w laboratorium DevTest?
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zawiera początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga w szybkim wdrażaniu bez konieczności inwestowania czasu na odtworzenie wspólnych narzędzi deweloperskich i dodatków. Można wdrożyć własne repozytorium prywatne. Można korzystać z repozytorium publicznego i prywatnego równolegle. Można również wyłączyć repozytorium publiczne. Kryteria wdrażania repozytorium prywatnego powinny być oparte na następujących pytaniach i uwagach:

- Czy organizacja ma wymóg posiadania licencjonowanego oprogramowania firmowego w ramach oferty DevTest Labs? Jeśli odpowiedź jest twierdząca, należy utworzyć repozytorium prywatne.
- Czy organizacja opracowuje niestandardowe oprogramowanie, które zapewnia określoną operację, która jest wymagana w ramach ogólnego procesu inicjowania obsługi administracyjnej? Jeśli odpowiedź jest twierdząca, należy wdrożyć repozytorium prywatne.
- Jeśli zasady ładu organizacji wymagają izolacji, a repozytoria zewnętrzne nie są zarządzane przez organizację bezpośrednio, należy wdrożyć prywatne repozytorium artefaktów. W ramach tego procesu początkowa kopia publicznego repozytorium może być kopiowana i integrowana z repozytorium prywatnym. Następnie publiczne repozytorium można wyłączyć, aby nikt w organizacji nie miał już do niego dostępu. Takie podejście wymusza wszystkich użytkowników w organizacji, aby mieć tylko jedno repozytorium, który jest zatwierdzony przez organizację i zminimalizować dryf konfiguracji.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Czy plan organizacyjny dla pojedynczego repozytorium lub zezwolić na wiele repozytoriów?
W ramach ogólnej strategii zarządzania i zarządzania konfiguracją w organizacji zaleca się użycie scentralizowanego repozytorium. Gdy używasz wielu repozytoriów, mogą one stać się silosami niezarządzanego oprogramowania w czasie. Za pomocą centralnego repozytorium wiele zespołów może korzystać z artefaktów z tego repozytorium dla swoich projektów. Wymusza standaryzacji, bezpieczeństwa, łatwość zarządzania i eliminuje powielanie wysiłków. W ramach centralizacji zalecane są następujące działania w zakresie długoterminowego zarządzania i zrównoważonego rozwoju:

- Skojarz repozytorium platformy Azure z tą samą dzierżawą usługi Azure Active Directory, z którą korzysta subskrypcja platformy Azure do uwierzytelniania i autoryzacji.
- Utwórz grupę o nazwie `All DevTest Labs Developers` w usłudze Azure Active Directory, która jest zarządzana centralnie. Każdy deweloper, który przyczynia się do tworzenia artefaktów powinny być umieszczone w tej grupie.
- Tej samej grupy usługi Azure Active Directory można użyć do zapewnienia dostępu do repozytorium repozytorium repozytorium usługi Azure i do laboratorium.
- W repozytorium platformy Azure rozgałęzianie lub rozwidanie powinny być używane do oddzielnego repozytorium w rozwoju od podstawowego repozytorium produkcji. Zawartość jest dodawana tylko do gałęzi głównej z żądaniem ściągnięcia po dokonaniu prawidłowego przeglądu kodu. Gdy recenzent kodu zatwierdzi zmianę, główny deweloper, który jest odpowiedzialny za konserwację gałęzi głównej, scala zaktualizowany kod.

## <a name="cicd-integration"></a>Integracja ciągłej integracji z ciągłą integracją/dyskami

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Czy DevTest Labs integruje się z moim pasem narzędzi CI/CD?
Jeśli używasz usługi Azure DevOps, możesz użyć [rozszerzenia Zadania DevTest Labs,](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) aby zautomatyzować potok wersji w laboratoriach DevTest. Oto niektóre zadania, które można wykonać za pomocą tego rozszerzenia:

- Automatyczne tworzenie i wdrażanie maszyny wirtualnej. Maszynę Wirtualną można również skonfigurować przy użyciu najnowszej kompilacji przy użyciu zadań usługi Azure File Copy lub PowerShell Azure DevOps Services.
- Automatycznie przechwytuj stan maszyny Wirtualnej po przetestowaniu, aby odtworzyć błąd na tej samej maszynie wirtualnej do dalszego badania.
- Usuń maszynę wirtualną na końcu potoku wydania, gdy nie jest już potrzebna.

Następujące wpisy w blogu zawierają wskazówki i informacje dotyczące korzystania z rozszerzenia usług Azure DevOps:

- [Laboratoria devtest i rozszerzenie DevOps platformy Azure](integrate-environments-devops-pipeline.md)
- [Wdrażanie nowej maszyny Wirtualnej w istniejącym laboratorium DevTest Labs z usług DevOps azure](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Korzystanie z zarządzania wersjami usługi Azure DevOps dla ciągłych wdrożeń w laboratoriach DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

W przypadku innych narzędzi ciągłej integracji (CI)/ciągłego dostarczania (CD) można osiągnąć te same scenariusze, wdrażając [szablony usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/) przy użyciu [poleceń cmdlet azure powershell](../azure-resource-manager/templates/deploy-powershell.md) i [zestawów SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Można również użyć [interfejsów API REST dla DevTest Labs](https://aka.ms/dtlrestapis) do integracji z pęcznienie narzędzi.

## <a name="networking"></a>Obsługa sieci

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kiedy należy utworzyć nową sieć wirtualną dla mojego środowiska DevTest Labs w porównaniu z istniejącą siecią wirtualną?
Jeśli maszyny wirtualne muszą wchodzić w interakcje z istniejącą infrastrukturą, należy rozważyć użycie istniejącej sieci wirtualnej w środowisku DevTest Labs. Jeśli używasz expressroute, można zminimalizować ilość sieci wirtualnych / podsieci, tak aby nie fragmentowanie przestrzeni adresowej IP, który zostanie przypisany do użycia w subskrypcjach.

Należy rozważyć użycie wzorzec komunikacji równorzędnej sieci wirtualnej tutaj[(Model szprychy koncentratora)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)zbyt. Takie podejście umożliwia komunikację vnet/podsieć między subskrypcjami. W przeciwnym razie każde środowisko DevTest Labs może mieć własną sieć wirtualną.

Istnieją [ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md) dotyczące liczby sieci wirtualnych na subskrypcję. Domyślna kwota wynosi 50, chociaż ten limit można podnieść do 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kiedy należy używać współdzielonego adresu IP a publicznego adresu IP a prywatnego adresu IP?

Jeśli korzystasz z sieci VPN typu lokacja-lokacja lub trasy ekspresowej, rozważ użycie prywatnych adresów IP, aby twoje maszyny były dostępne za pośrednictwem sieci wewnętrznej i niedostępne za pośrednictwem publicznego Internetu.

> [!NOTE]
> Właściciele laboratoriów mogą zmienić te zasady podsieci, aby upewnić się, że nikt przypadkowo nie tworzy publicznych adresów IP dla swoich maszyn wirtualnych. Właściciel subskrypcji należy utworzyć zasady subskrypcji uniemożliwiające publiczne wiadomości IP z tworzone.

Podczas korzystania z udostępnionych publicznych adresów IP maszyny wirtualne w laboratorium współużytkuje publiczny adres IP. Takie podejście może być przydatne, gdy trzeba uniknąć naruszenia limitów publicznych adresów IP dla danej subskrypcji.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Jak upewnić się, że maszyny programistyczne i testowe wirtualne nie mogą dotrzeć do publicznego Internetu? Czy istnieją jakieś zalecane wzorce do skonfigurowania konfiguracji sieci?

Tak. Istnieją dwa aspekty do rozważenia - ruch przychodzący i wychodzący.

- **Ruch przychodzący** — jeśli maszyna wirtualna nie ma publicznego adresu IP, nie można do niego uzyskać dostępu przez Internet. Typowym podejściem jest zapewnienie, że zasady na poziomie subskrypcji jest ustawiona, tak, że żaden użytkownik nie można utworzyć publiczny adres IP.
- **Ruch wychodzący** — jeśli chcesz uniemożliwić maszynom wirtualnym bezpośredni dostęp do publicznego Internetu i wymusić ruch przez firmową zaporę, możesz kierować ruch lokalny za pośrednictwem trasy ekspresowej lub sieci VPN przy użyciu wymuszonego routingu.

> [!NOTE]
> Jeśli masz serwer proxy, który blokuje ruch bez ustawień serwera proxy, nie zapomnij dodać wyjątków do konta magazynu artefaktów laboratorium.

Można również użyć sieciowych grup zabezpieczeń dla maszyn wirtualnych lub podsieci. Ten krok dodaje dodatkową warstwę ochrony, aby umożliwić / zablokować ruch.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Dlaczego moja istniejąca sieć wirtualna nie jest poprawnie zapisywana?
Jedną z możliwości jest to, że nazwa sieci wirtualnej zawiera okresy. Jeśli tak, spróbuj usunąć kropki lub zastąpić je myślnikami. Następnie spróbuj ponownie zapisać sieć wirtualną.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Dlaczego podczas inicjowania obsługi administracyjnej maszyny wirtualnej z programu PowerShell pojawia się błąd "Nie znaleziono zasobu nadrzędnego"?
Gdy jeden zasób jest elementem nadrzędnym do innego zasobu, zasób nadrzędny musi istnieć przed utworzeniem zasobu podrzędnego. Jeśli zasób nadrzędny nie istnieje, zostanie wyświetlony komunikat **ParentResourceNotFound.** Jeśli nie określisz zależności od zasobu nadrzędnego, zasób podrzędny może zostać wdrożony przed elementem nadrzędnym.

Maszyny wirtualne są zasobami podrzędnymi w laboratorium w grupie zasobów. W przypadku wdrażania szablonów Menedżera zasobów przy użyciu programu PowerShell za pomocą szablonów Menedżera zasobów nazwa grupy zasobów podana w skrypcie programu PowerShell powinna być nazwą grupy zasobów laboratorium. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z typowymi błędami wdrażania platformy Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Gdzie można znaleźć więcej informacji o błędzie, jeśli wdrożenie maszyny Wirtualnej nie powiedzie się?
Błędy wdrażania maszyn wirtualnych są przechwytywane w dziennikach aktywności. Dzienniki aktywności maszyn wirtualnych w laboratorium można znaleźć w obszarze **Dzienniki inspekcji** lub **Diagnostyka maszyny wirtualnej** w menu zasobów na stronie maszyny wirtualnej laboratorium (strona jest wyświetlana po wybraniu maszyny wirtualnej z listy Moje maszyny wirtualne).

Czasami błąd wdrażania występuje przed rozpoczęciem wdrażania maszyny Wirtualnej. Przykładem jest, gdy limit subskrypcji dla zasobu, który został utworzony z maszyną wirtualną jest przekroczona. W takim przypadku szczegóły błędu są przechwytywane w dziennikach aktywności na poziomie laboratorium. Dzienniki aktywności znajdują się w dolnej części **ustawień konfiguracji i zasad.** Aby uzyskać więcej informacji na temat korzystania z dzienników aktywności na platformie Azure, zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji zasobów.](../azure-resource-manager/management/view-activity-logs.md)

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Dlaczego podczas próby utworzenia laboratorium pojawia się komunikat "lokalizacja nie jest dostępna dla typu zasobu"?
Podczas próby utworzenia laboratorium może zostać wyświetlony komunikat o błędzie podobny do następującego:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Ten błąd można rozwiązać, wykonując jedną z następujących czynności:

#### <a name="option-1"></a>Opcja 1
Sprawdź dostępność typu zasobu w regionach platformy Azure na stronie [Produkty dostępne według regionów.](https://azure.microsoft.com/global-infrastructure/services/) Jeśli typ zasobu nie jest dostępny w określonym regionie, DevTest Labs nie obsługuje tworzenia laboratorium w tym regionie. Wybierz inny region podczas tworzenia laboratorium.

#### <a name="option-2"></a>Opcja 2
Jeśli typ zasobu jest dostępny w Twoim regionie, sprawdź, czy jest on zarejestrowany w subskrypcji. Można to zrobić na poziomie właściciela subskrypcji, jak pokazano w [tym artykule](../azure-resource-manager/management/resource-providers-and-types.md).
