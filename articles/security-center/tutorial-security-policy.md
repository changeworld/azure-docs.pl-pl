---
title: Praca z zasadami zabezpieczeń | Microsoft Docs
description: W tym artykule opisano sposób pracy z zasadami zabezpieczeń w programie Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 1820c31053c13a0f3039812b31df415bc9fa4fdc
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910373"
---
# <a name="working-with-security-policies"></a>Praca z zasadami zabezpieczeń

W tym artykule opisano sposób konfigurowania zasad zabezpieczeń oraz sposób ich wyświetlania w Security Center. Azure Security Center automatycznie przypisuje [wbudowane zasady zabezpieczeń](security-center-policy-definitions.md) dla każdej subskrypcji, która została dołączona. Można je skonfigurować w [Azure Policy](../governance/policy/overview.md), co umożliwia także Ustawianie zasad w grupach zarządzania i wielu subskrypcjach.

Aby uzyskać instrukcje dotyczące sposobu ustawiania zasad przy użyciu programu PowerShell [, zobacz Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu modułu](../governance/policy/assign-policy-powershell.md)Azure PowerShell.

>[!NOTE]
> Security Center rozpoczęła integrację z Azure Policy. Istniejący klienci zostaną automatycznie przeniesieni do nowej inicjatywy wbudowanej w Azure Policy, zamiast wcześniejszych zasad zabezpieczeń w programie Security Center. Ta zmiana nie wpłynie na zasoby lub środowisko, z wyjątkiem obecności nowej inicjatywy w Azure Policy.

## <a name="what-are-security-policies"></a>Czym są zasady zabezpieczeń?
Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. W Azure Policy można zdefiniować zasady dla subskrypcji platformy Azure i dopasować je do typu obciążenia lub poufności danych. Na przykład aplikacje korzystające z danych podlegających regulacjom, takie jak dane osobowe lub dane klientów, mogą wymagać wyższego poziomu zabezpieczeń niż inne obciążenia. Aby ustawić zasady w ramach subskrypcji lub grup zarządzania, ustaw je w [Azure Policy](../governance/policy/overview.md).

W zasadach zabezpieczeń są używane zalecenia dotyczące zabezpieczeń, które uzyskasz w Azure Security Center. Można monitorować zgodność z nimi, aby pomóc w zidentyfikowaniu potencjalnych luk w zabezpieczeniach i łagodzeniu zagrożeń. Aby uzyskać więcej informacji na temat sposobu ustalenia opcji, która jest odpowiednia dla Ciebie, zobacz listę [wbudowanych zasad zabezpieczeń](security-center-policy-definitions.md).

Po włączeniu Security Center zasady zabezpieczeń wbudowane w Security Center zostaną odzwierciedlone w Azure Policy jako wbudowana inicjatywa w kategorii Security Center. Wbudowana inicjatywa jest automatycznie przypisywana do wszystkich subskrypcji zarejestrowanych w usłudze Security Center (w warstwach Bezpłatna lub Standardowa). Wbudowana inicjatywa zawiera tylko zasady inspekcji.


### <a name="management-groups"></a>Grupy zarządzania
Jeśli Twoja organizacja ma wiele subskrypcji, możesz potrzebować sposobu na wydajne zarządzanie dostępem, zasadami i zgodnością dla tych subskrypcji. Grupy zarządzania platformy Azure zapewniają poziom zakresu powyżej subskrypcji. Subskrypcje są organizowane w kontenerach nazywanych „grupami zarządzania”, do których należy zastosować swoje zasady nadzoru. Wszystkie subskrypcje w grupie zarządzania automatycznie dziedziczą zasady zastosowane do tej grupy zarządzania. Do każdego katalogu jest przypisywana grupa zarządzania najwyższego poziomu nazywana „główną” grupą zarządzania. Główna grupa zarządzania jest wbudowana w hierarchię, aby wszystkie grupy zarządzania i subskrypcje pod nią podlegały. Główna grupa zarządzania umożliwia stosowanie zasad globalnych i przypisań RBAC na poziomie katalogu. Aby skonfigurować grupy zarządzania do użycia z Azure Security Center, postępuj zgodnie z instrukcjami w temacie [Uzyskaj widoczność dla całej dzierżawy dla Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Ważne jest, aby poznać hierarchię grup zarządzania i subskrypcji. Zobacz [Organize your resources with Azure Management Groups](../governance/management-groups/index.md#root-management-group-for-each-directory) (Organizowanie zasobów za pomocą grup zarządzania platformy Azure), aby dowiedzieć się więcej na temat grup zarządzania, zarządzania grupą główną i dostępu do grup zarządzania.
>

## <a name="how-security-policies-work"></a>Jak działają zasady zabezpieczeń
Usługa Security Center automatycznie tworzy domyślne zasady zabezpieczeń dla każdej Twojej subskrypcji platformy Azure. Możesz edytować zasady w Azure Policy, aby wykonać następujące czynności:
- Tworzenie nowych definicji zasad.
- Przypisywanie w subskrypcjach i grupach zarządzania zasad, które mogą reprezentować całą organizację lub jednostkę biznesową w organizacji.
- Monitorowanie zgodności zasad.

Aby uzyskać więcej informacji na temat usługi Azure Policy, zobacz [Create and manage policies to enforce compliance (Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności)](../governance/policy/tutorials/create-and-manage.md).

Zasady platformy Azure zawierają następujące składniki:

- **Zasada** jest regułą.
- **Inicjatywa** jest kolekcją zasad.
- **Przypisanie** jest aplikacją inicjatywy lub zasadami do określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów).

## <a name="view-security-policies"></a>Wyświetlanie zasad zabezpieczeń

Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

1. Na pulpicie nawigacyjnym **Security Center** wybierz pozycję **zasady zabezpieczeń**.

    ![Okienko Zarządzanie zasadami](./media/security-center-policies/security-center-policy-mgt.png)

   Na ekranie **Zarządzanie zasadami** można zobaczyć liczbę grup zarządzania, subskrypcji i obszarów roboczych, a także strukturę grupy zarządzania.

   > [!NOTE]
   > Pulpit nawigacyjny Security Center może zawierać większą liczbę subskrypcji w ramach **pokrycia subskrypcji** niż liczba subskrypcji pokazanych w obszarze **Zarządzanie zasadami**. W obszarze Pokrycie subskrypcji jest wyświetlana liczba subskrypcji w warstwie Standardowa, Bezpłatna i subskrypcji „nie pokrytych”. Subskrypcje "nie objęte usługą" nie mają włączonej Security Center i nie są wyświetlane w obszarze **Zarządzanie zasadami**.
   >

2. Wybierz subskrypcję lub grupę zarządzania, której zasady chcesz wyświetlić.

   - Ekran **zasady zabezpieczeń** odzwierciedla akcję podejmowaną przez zasady przypisane do wybranej subskrypcji lub grupy zarządzania.
   - W górnej części Skorzystaj z podanych linków, aby otworzyć każde **przypisanie** zasad, które ma zastosowanie do subskrypcji lub grupy zarządzania. Możesz użyć linków, aby uzyskać dostęp do przypisania i edytować lub wyłączyć zasady. Jeśli na przykład widzisz, że określone przypisanie zasad skutecznie odmawia ochrony punktu końcowego, możesz użyć linku, aby uzyskać dostęp do zasad i zmodyfikować go lub wyłączyć.
   - Na liście zasad można zobaczyć obowiązującą aplikację zasad w ramach subskrypcji lub grupy zarządzania. Oznacza to, że należy wziąć pod uwagę ustawienia poszczególnych zasad, które mają zastosowanie do zakresu, a także uzyskać łączny wynik akcji podejmowanych przez zasady. Na przykład jeśli w jednym przypisaniu zasady są wyłączone, ale w innym jest ustawiona na AuditIfNotExist, to efekt skumulowany stosuje AuditIfNotExist. Większym aktywnym efektem jest zawsze pierwszeństwo.
   - Efektem zasad może być: Dołącz, inspekcja, AuditIfNotExists, Odmów, DeployIfNotExists, wyłączone. Aby uzyskać więcej informacji na temat sposobu stosowania efektów, zobacz [opis efektów zasad](../governance/policy/concepts/effects.md).

   ![ekran zasad](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Podczas wyświetlania przypisanych zasad można zobaczyć wiele przypisań i zobaczyć, jak poszczególne przypisania są skonfigurowane samodzielnie.

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
W usłudze [Azure Policy](../governance/policy/tutorials/create-and-manage.md) możesz edytować domyślne zasady zabezpieczeń dla każdej Twojej grupy zarządzania i subskrypcji platformy Azure. Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem lub administratorem zabezpieczeń subskrypcji lub grupy zarządzania zawierającej.

Aby uzyskać instrukcje dotyczące edytowania zasad zabezpieczeń w programie Azure Policy, zobacz i [Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności](../governance/policy/tutorials/create-and-manage.md).

Zasady zabezpieczeń można edytować za pośrednictwem portalu Azure Policy przy użyciu interfejsu API REST lub środowiska Windows PowerShell. Poniższy przykład zawiera instrukcje dotyczące edytowania za pomocą interfejsu API REST.


## <a name="disable-security-policies"></a>Wyłącz zasady zabezpieczeń
Jeśli domyślne zasady zabezpieczeń generują rekomendację, która nie jest odpowiednia dla danego środowiska, można ją zatrzymać, wyłączając definicję zasad, która wysyła zalecenie.
Aby uzyskać więcej informacji na temat zaleceń, zobacz [Zarządzanie zaleceniami](security-center-recommendations.md)dotyczącymi zabezpieczeń.

1. W Security Center z sekcji **zgodność zasad &** kliknij pozycję **zasady zabezpieczeń**.

   ![Zarządzanie zasadami](./media/tutorial-security-policy/policy-management.png)

2. Kliknij subskrypcję lub grupę zarządzania, dla której chcesz wyłączyć zalecenie.

   > [!Note]
   > Należy pamiętać, że grupa zarządzania stosuje swoje zasady do swoich subskrypcji. W związku z tym Jeśli wyłączysz zasady subskrypcji, a subskrypcja należy do grupy zarządzania, która nadal używa tych samych zasad, będziesz nadal otrzymywać zalecenia dotyczące zasad. Zasady będą nadal stosowane z poziomu zarządzania i nadal będą generowane zalecenia.

1. Kliknij przypisane zasady.

   ![Wyłącz zasady](./media/tutorial-security-policy/security-policy.png)

1. W sekcji **Parametry** Wyszukaj zasady wywołujące zalecenie, które chcesz wyłączyć, a następnie z listy rozwijanej wybierz pozycję **wyłączone** .

   ![Wyłącz zasady](./media/tutorial-security-policy/disable-policy.png)
1. Kliknij polecenie **Zapisz**.
   > [!Note]
   > Wyłączenie zmian zasad może potrwać do 12 godzin.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń przy użyciu interfejsu API REST

W ramach integracji natywnej z Azure Policy Azure Security Center umożliwia korzystanie z interfejsu API REST Azure Policy do tworzenia przypisań zasad. Poniższe instrukcje przeprowadzą Cię przez proces tworzenia przypisań zasad, a także dostosowywania istniejących przypisań. 

Ważne pojęcia dotyczące Azure Policy: 

- **Definicja zasad** jest regułą 

- **Inicjatywa** jest kolekcją definicji zasad (reguł) 

- **Przypisanie** jest aplikacją inicjatywy lub zasadami do określonego zakresu (grupy zarządzania, subskrypcji itp.) 

Security Center ma wbudowaną inicjatywę obejmującą wszystkie jej zasady zabezpieczeń. Aby ocenić zasady Security Center w swoich zasobach platformy Azure, należy utworzyć przypisanie do grupy zarządzania lub subskrypcję, którą chcesz ocenić.

Z wbudowaną inicjatywą są domyślnie włączone wszystkie zasady Security Center. Niektóre zasady można wyłączyć z inicjatywy wbudowanej, na przykład można zastosować wszystkie zasady Security Center z wyjątkiem **zapory aplikacji sieci Web**, zmieniając wartość parametru efektu zasad na **wyłączone**. 

### <a name="api-examples"></a>Przykłady interfejsu API

W poniższych przykładach Zamień te zmienne:

- **{SCOPE}** wprowadź nazwę grupy zarządzania lub subskrypcji, do której zastosowano zasady.
- **{policyAssignmentName}** wprowadź [nazwę odpowiedniego przypisania zasad](#policy-names).
- **{name}** wprowadź swoją nazwę lub nazwę administratora, który zatwierdził zmianę zasad.

W tym przykładzie przedstawiono sposób przypisywania wbudowanej inicjatywy Security Center w ramach subskrypcji lub grupy zarządzania
 
 ```
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
 ```

W tym przykładzie przedstawiono sposób przypisywania wbudowanej inicjatywy Security Center w ramach subskrypcji, z wyłączeniem następujących zasad: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
Ten przykład pokazuje, jak usunąć przypisanie:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

### Odwołania do nazw zasad<a name="policy-names"></a>

|Nazwa zasad w Security Center|Nazwa zasad wyświetlana w Azure Policy |Nazwa parametru efektu zasad|
|----|----|----|
|Szyfrowanie danych SQL |Monitorowanie nieszyfrowanej bazy danych SQL w Azure Security Center |sqlEncryptionMonitoringEffect| 
|Inspekcja SQL |Monitoruj niepoddaną inspekcję bazy danych SQL w Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizacje systemu |Monitoruj brakujące aktualizacje systemu w Azure Security Center |systemUpdatesMonitoringEffect|
|Szyfrowanie w usłudze Storage |Inspekcja braku szyfrowania obiektów BLOB dla kont magazynu |storageEncryptionMonitoringEffect|
|Dostęp do sieci JIT |Monitoruj możliwy dostęp do sieci just in Time (JIT) w Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptacyjne kontrole aplikacji |Monitoruj możliwe listy dozwolonych aplikacji w Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupy zabezpieczeń sieci |Monitoruj dostęp do sieci w Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfiguracje zabezpieczeń |Monitoruj luki w zabezpieczeniach systemu operacyjnego w Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrona punktu końcowego |Monitoruj brakujące Endpoint Protection w Azure Security Center |endpointProtectionMonitoringEffect |
|Szyfrowanie dysków |Monitoruj nieszyfrowane dyski maszyn wirtualnych w Azure Security Center |diskEncryptionMonitoringEffect|
|Ocena luk w zabezpieczeniach |Monitoruj luki w zabezpieczeniach maszyn wirtualnych w Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Zapora aplikacji internetowej |Monitoruj niechronione aplikacje sieci Web w Azure Security Center |webApplicationFirewallMonitoringEffect |
|Zapora nowej generacji |Monitoruj niechronione punkty końcowe sieci w Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Security Center korzysta z Access Control opartych na rolach (RBAC), które udostępnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Gdy użytkownicy otworzą Security Center, zobaczą tylko te informacje, które są związane z zasobami, do których mają dostęp. Oznacza to, że użytkownicy mają przypisaną rolę właściciela, współautora lub czytelnika do subskrypcji lub grupy zasobów, do której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- Czytelnik zabezpieczeń: Mieć uprawnienia do widoku Security Center, w tym zalecenia, alerty, zasady i kondycję, ale nie mogą wprowadzać zmian.
- Administrator zabezpieczeń: Mają takie same uprawnienia do wyświetlania jak czytelnik zabezpieczeń i mogą także aktualizować zasady zabezpieczeń i odrzucać zalecenia i alerty.



## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób edytowania zasad zabezpieczeń w programie Azure Policy. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Azure Security Center Przewodnik planowania i](security-center-planning-and-operations-guide.md)obsługi: Dowiedz się, jak planować i zrozumieć zagadnienia projektowe dotyczące Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md): Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Azure Security Center](security-center-managing-and-responding-alerts.md): Informacje na temat sposobu zarządzania alertami zabezpieczeń i reagowania na nie.
* [Monitoruj rozwiązania partnerskie Azure Security Center](security-center-partner-solutions.md): Informacje na temat sposobu monitorowania stanu kondycji rozwiązań partnerskich.
* [Uzyskaj widoczność dla całej dzierżawy dla Azure Security Center](security-center-management-groups.md): Dowiedz się, jak skonfigurować grupy zarządzania dla Azure Security Center.
* [Azure Security Center często zadawane pytania](security-center-faq.md): Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/): Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../governance/policy/overview.md)
