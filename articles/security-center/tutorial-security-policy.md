---
title: Praca z zasadami zabezpieczeń | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób pracy z zasadami zabezpieczeń w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: monhaber
ms.openlocfilehash: 1931026869e930caef2ff2f92fb85dade15a9c8c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111514"
---
# <a name="working-with-security-policies"></a>Praca z zasadami zabezpieczeń

W tym artykule wyjaśniono, jak są skonfigurowane zasady zabezpieczeń oraz jak wyświetlać je w usłudze Security Center. Usługa Azure Security Center automatycznie przypisuje jej [zasad wbudowanych rozwiązań zabezpieczeń](security-center-policy-definitions.md) w każdej subskrypcji, która jest dołączona. Można skonfigurować je w [usługi Azure Policy](../governance/policy/overview.md), który umożliwia także ustawić zasady w grupach zarządzania i w ramach wielu subskrypcji.

Aby uzyskać instrukcje na temat sposobu ustawiania zasad przy użyciu programu PowerShell, zobacz [Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu modułu Azure PowerShell](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> Usługa Security Center pracy integracji z usługą Azure Policy. Dotychczasowi użytkownicy zostaną automatycznie przeniesieni do nowego inicjatywy wbudowanych w zasadach usługi Azure, zamiast poprzednich zasad zabezpieczeń w usłudze Security Center. Ta zmiana nie wpłynie na Twoje zasoby lub środowisko z wyjątkiem obecności nowej inicjatywy w zasadach usługi Azure.

## <a name="what-are-security-policies"></a>Czym są zasady zabezpieczeń?
Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. Usługa Azure Policy można zdefiniować zasady dla subskrypcji platformy Azure i dostosowuj je do danego typu obciążenia lub wrażliwości danych. Na przykład aplikacje wykorzystujące dane podlegające ochronie, takie jak dane osobowe, mogą wymagać wyższego poziomu zabezpieczeń niż innych obciążeń. Aby ustawić zasady dla subskrypcji lub grupy zarządzania, należy ustawić je w [usługi Azure Policy](../governance/policy/overview.md).

Zasady zabezpieczeń dla dysku zalecenia dotyczące zabezpieczeń, który jest pobierany w usłudze Azure Security Center. Można monitorować zgodność z nimi ułatwiają znalezienie potencjalnych luk i uniknięcie zagrożeń. Aby uzyskać więcej informacji na temat sposobu określenia która opcja jest odpowiednia dla Ciebie, zobacz listę [zasad wbudowanych rozwiązań zabezpieczeń](security-center-policy-definitions.md).

Po włączeniu usługi Security Center, wbudowana w usłudze Security Center zasad zabezpieczeń jest odzwierciedlana w zasadach usługi Azure jako wbudowane inicjatywy kategorii usługi Security Center. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich subskrypcji zarejestrowanych w usłudze Security Center (w warstwach Bezpłatna lub Standardowa). Wbudowana inicjatywa zawiera tylko zasady inspekcji.


### <a name="management-groups"></a>Grupy zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje zasady nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Do każdego katalogu jest przypisywana grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. Aby skonfigurować grupy zarządzania do użycia z usługą Azure Security Center, postępuj zgodnie z instrukcjami [wgląd obowiązujące w dzierżawie usługi Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Ważne jest, aby poznać hierarchię grup zarządzania i subskrypcji. Zobacz [Organize your resources with Azure Management Groups](../governance/management-groups/index.md#root-management-group-for-each-directory) (Organizowanie zasobów za pomocą grup zarządzania platformy Azure), aby dowiedzieć się więcej na temat grup zarządzania, zarządzania grupą główną i dostępu do grup zarządzania.
>

## <a name="how-security-policies-work"></a>Jak działają zasady zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Możesz edytować zasady w zasadach usługi Azure, aby wykonać następujące czynności:
- Tworzenie nowych definicji zasad.
- Przypisywanie w subskrypcjach i grupach zarządzania zasad, które mogą reprezentować całą organizację lub jednostkę biznesową w organizacji.
- Monitorowanie zgodności zasad.

Aby uzyskać więcej informacji na temat usługi Azure Policy, zobacz [Create and manage policies to enforce compliance (Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności)](../governance/policy/tutorials/create-and-manage.md).

Zasady platformy Azure zawierają następujące składniki:

- A **zasad** reguła.
- **Inicjatywy** to zbiór zasad.
- **Przypisania** jest zastosowanie zasad lub inicjatywy do określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów).

## <a name="view-security-policies"></a>Wyświetlanie zasad zabezpieczeń

Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

1. W **usługi Security Center** pulpitu nawigacyjnego, wybierz opcję **zasady zabezpieczeń**.

    ![Okienko Zarządzanie zasadami](./media/security-center-policies/security-center-policy-mgt.png)

   W **Zarządzanie zasadami** ekranu, można wyświetlić liczbę grup zarządzania, subskrypcji i obszarów roboczych, a także strukturę grupy zarządzania.

   > [!NOTE]
   > - Na pulpicie nawigacyjnym usługi Security Center mogą być wyświetlane większej liczby subskrypcji **pokrycie subskrypcji** niż liczba wyświetlanych w obszarze subskrypcji **Zarządzanie zasadami**. W obszarze Pokrycie subskrypcji jest wyświetlana liczba subskrypcji w warstwie Standardowa, Bezpłatna i subskrypcji „nie pokrytych”. "Nie pokrytą" subskrypcji nie zostały włączone w usłudze Security Center i nie są wyświetlane w obszarze **Zarządzanie zasadami**.
   >

   Kolumny w tabeli wyświetlają następujące informacje:

   - **Przypisanie inicjatywy zasad** — Centrum zabezpieczeń [wbudowane zasady](security-center-policy-definitions.md) i inicjatyw, które są przypisane do subskrypcji lub grupę zarządzania.
   - **Pokrycie** — identyfikuje warstwę cenową bezpłatna lub standardowa, z systemem grupy zarządzania, subskrypcji lub obszaru roboczego.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
   - **Ustawienia** — subskrypcje mają łącze **edytować ustawienia**. Wybieranie **edytować ustawienia** pozwala zaktualizować swoje [ustawienia Centrum zabezpieczeń](security-center-policies-overview.md) dla każdej subskrypcji lub grupę zarządzania.
   - **Bezpieczne wynik** — [bezpiecznego wynik](security-center-secure-score.md) jest miarą jak bezpieczne stan zabezpieczeń obciążeń i pomaga określić priorytety zalecenia dotyczące poprawy jakości obsługi.

2. Wybierz subskrypcję lub grupę zarządzania zasad, których chcesz wyświetlić.

   - **Zasady zabezpieczeń** akcję podejmowaną przez przypisanych na subskrypcję lub grupę zarządzania wybrano zasad zostaną odzwierciedlone na ekranie.
   - U góry strony, należy użyć linków dostępnych do otwierania poszczególnych zasad **przypisania** stosująca na subskrypcję lub grupę zarządzania. Łącza umożliwia dostęp do przypisania, edytować i wyłączenie tych zasad. Na przykład jeśli przypisanie zasad o określonym skutecznie odmawia programu endpoint protection, służy link do zasad dostępu i go edytować lub wyłączyć.
   - Na liście zasad można wyświetlić skuteczne stosowanie zasad na grupy zarządzania lub subskrypcji. Oznacza to, że ustawienia poszczególnych zasad, które mają zastosowanie do zakresu są brane pod uwagę i znajdują się za pomocą zbiorczy wynik, jakie działania są podejmowane przez zasady. Na przykład jeśli w jedno przypisanie zasad jest wyłączona, ale w innym razie jest równa AuditIfNotExist, skumulowany efekt dotyczy AuditIfNotExist. Efekt bardziej aktywnego zawsze pierwszeństwo.
   - Efekt zasady może być: Dołącz inspekcji, AuditIfNotExists, Odmów, DeployIfNotExists, wyłączone. Aby uzyskać więcej informacji na temat sposobu stosowania skutków, zobacz [zrozumienie zasad efekty](../governance/policy/concepts/effects.md).

   ![ekran zasad](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Gdy można wyświetlić przypisane zasady, możesz zobaczyć przypisania wielu i możesz zobaczyć, jak każdego przypisania będzie konfigurowana na swój własny.

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
Można edytować domyślnych zasad zabezpieczeń dla każdej subskrypcji platformy Azure i grup zarządzania w [usługi Azure Policy](../governance/policy/tutorials/create-and-manage.md). Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń tej subskrypcji lub zawierającej ją grupy zarządzania.

Aby uzyskać instrukcje, jak edytować zasady zabezpieczeń, usługa Azure Policy, zobacz i [Tworzenie zasad w celu wymuszania zgodności i zarządzania nimi](../governance/policy/tutorials/create-and-manage.md).

Możesz edytować zasady zabezpieczeń w portalu usługi Azure Policy, za pośrednictwem interfejsu API REST lub przy użyciu programu Windows PowerShell. Poniższy przykład zawiera instrukcje dotyczące edycji przy użyciu interfejsu API REST.


## <a name="disable-security-policies"></a>Wyłączanie zasad zabezpieczeń
Jeśli domyślne zasady zabezpieczeń jest generowany zalecenia, która nie jest odpowiednia dla danego środowiska, można je zatrzymać, wyłączając definicji zasad, która wysyła zalecenia.
Aby uzyskać więcej informacji na temat zalecenia, zobacz [Zarządzanie zaleceniami dotyczącymi zabezpieczeń](security-center-recommendations.md).

1. W usłudze Security Center z **zasady i zgodność** kliknij **zasady zabezpieczeń**.

   ![Zarządzanie zasadami](./media/tutorial-security-policy/policy-management.png)

2. Kliknij grupę subskrypcję lub zarządzania, dla której chcesz wyłączyć zalecenia.

1. Kliknij przycisk przypisanych zasad.

   ![Wyłącz zasady](./media/tutorial-security-policy/security-policy.png)

1. W **parametry** sekcji wyszukiwanie zasad który wywołuje rekomendacji, który chcesz wyłączyć, a następnie z listy rozwijanej wybierz **wyłączone**

   ![Wyłącz zasady](./media/tutorial-security-policy/disable-policy.png)
1. Kliknij pozycję **Zapisz**.
   > [!Note]
   > Wyłącz zmiany zasad może potrwać do 12 godzin, aby zastosować zmiany.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń przy użyciu interfejsu API REST

W ramach natywnej integracji z usługą Azure Policy Azure Security Center pozwala na przejęcie interfejsu API REST zaletą usługi Azure Policy w celu utworzenia przypisania zasad. Poniższe instrukcje opisują Tworzenie przypisania zasad, a także dostosowywania istniejących przypisań. 

Ważne pojęcia dotyczące usługi Azure Policy: 

- A **definicji zasad** jest regułą 

-  **Inicjatywy** to kolekcja definicji zasad (zasad) 

-  **Przypisania** stosowania zasad lub inicjatywy do określonego zakresu (grupy zarządzania, subskrypcji, itp.) 

Usługa Security Center ma wbudowane inicjatywy, która obejmuje wszystkie jego zasadami zabezpieczeń. Aby ocenić zasady Centrum zabezpieczeń na zasoby platformy Azure, należy utworzyć przypisania na grupę zarządzania lub subskrypcji, którą chcesz ocenić.  

Wbudowane inicjatywy zawiera wszystkie zasady Centrum zabezpieczeń, domyślnie włączone. Użytkownik może wyłączyć określonych zasad z wbudowaną inicjatywy, na przykład można zastosować wszystkie zasady Centrum zabezpieczeń, z wyjątkiem **zapory aplikacji sieci web**, zmieniając wartość parametru efekt zasad  **Wyłączone**. 

### <a name="api-examples"></a>Przykłady interfejsu API

W poniższych przykładach należy zastąpić następujące zmienne:

- **{zakresu}**  wprowadź nazwę grupy zarządzania lub subskrypcji są stosowane zasady.
- **{policyAssignmentName}**  wprowadź [nazwa przypisania zasad odpowiednich](#policy-names).
- **{name}**  wprowadź swoją nazwę lub nazwę administratora, który zatwierdzone zmiany zasad.

W tym przykładzie pokazano, jak przypisać wbudowanych inicjatywy Centrum zabezpieczeń dla subskrypcji lub grupy zarządzania
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

W tym przykładzie pokazano, jak przypisać wbudowanych inicjatywy Centrum zabezpieczeń w ramach subskrypcji za pomocą następujących zasad, które są wyłączone: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Program Endpoint protection ("endpointProtectionMonitoringEffect") 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Treść żądania (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName": "Włączanie monitorowania w usłudze Azure Security Center", 
    
    "metadane": { 
    
    "assignedBy": "{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters": { 
    
    "systemUpdatesMonitoringEffect": {"value": "Wyłączone"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect": {"value": "Wyłączone"}, 
    
    }, 
    
     } 
    
    } 

W tym przykładzie pokazano, jak usunąć przypisanie:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### Odwołanie do nazwy zasad <a name="policy-names"></a>

|Nazwa zasad w usłudze Security Center|Nazwa zasad wyświetlana w usługi Azure Policy |Nazwa parametru efekt zasad|
|----|----|----|
|Szyfrowanie SQL |Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center |sqlEncryptionMonitoringEffect| 
|Inspekcja SQL |Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizacje systemu |Monitoruj brakujące aktualizacje systemu w usłudze Azure Security Center |systemUpdatesMonitoringEffect|
|Szyfrowanie w usłudze Storage |Inspekcja braku szyfrowania obiektów blob dla kont magazynu |storageEncryptionMonitoringEffect|
|Dostęp do sieci JIT |Monitoruj ewentualny dostęp just in time w usłudze Azure Security Center |jitNetworkAccessMonitoringEffect |
|Funkcje adaptacyjnego sterowania aplikacjami |Monitoruj ewentualną listę dozwolonych aplikacji w usłudze Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupy zabezpieczeń sieci |Monitoruj liberalny dostęp do sieci w usłudze Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfiguracje zabezpieczeń |Monitoruj luki w zabezpieczeniach systemu operacyjnego w usłudze Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrona punktów końcowych |Monitoruj brakujący program Endpoint Protection w usłudze Azure Security Center |endpointProtectionMonitoringEffect |
|Szyfrowanie dysków |Monitoruj niezaszyfrowane dyski maszyn wirtualnych w usłudze Azure Security Center |diskEncryptionMonitoringEffect|
|Ocena luk w zabezpieczeniach |Monitoruj luki w zabezpieczeniach maszyn wirtualnych w usłudze Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Zapora aplikacji internetowej |Monitoruj niechronione aplikacje internetowe w usłudze Azure Security Center |webApplicationFirewallMonitoringEffect |
|Zapora nowej generacji |Monitoruj niechronione punkty końcowe sieci w usłudze Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Usługa Security Center korzysta z opartej na rolach kontrola dostępu (RBAC), który zapewnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Po otwarciu Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, które mają dostęp do. Co oznacza, że użytkownicy przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- Czytelnik zabezpieczeń: Uprawnienia widok do usługi Security Center, który zawiera zalecenia, alerty, zasady i kondycję, ale nie mogą oni wprowadzić zmiany.
- Administrator zabezpieczeń: Te same prawa widok jako rola Czytelnik zabezpieczeń, a można również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.



## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób edytowanie zasad zabezpieczeń usługa Azure Policy. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania usługi Azure Security Center i obsługi](security-center-planning-and-operations-guide.md): Informacje o sposobie planowania i zagadnień projektowych dotyczących usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie i odpowiadanie na alerty zabezpieczeń w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Uzyskaj wgląd w całej dzierżawie usługi Azure Security Center](security-center-management-groups.md): Informacje o sposobie konfigurowania grupy zarządzania usługi Azure Security Center.
* [Usługa Azure Security Center — często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../governance/policy/overview.md)
