---
title: Zasady zabezpieczeń w usłudze Azure Security Center można ustawić jako część zasad platformy Azure i wyświetlane w usłudze Security Center | Dokumentacja firmy Microsoft
description: Ten dokument pomaga ustawienia zasad w zasadach usługi Azure lub wyświetlać je w usłudze Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334673"
---
# <a name="view-security-policies"></a>Wyświetl zasady zabezpieczeń

W tym artykule wyjaśniono, jak są skonfigurowane zasady zabezpieczeń oraz jak wyświetlać je w usłudze Security Center. Usługa Azure Security Center automatycznie przypisuje jej [zasad wbudowanych rozwiązań zabezpieczeń](security-center-policy-definitions.md) w każdej subskrypcji, która jest dołączona. Można skonfigurować je w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md), który umożliwia także ustawić zasady w grupach zarządzania i w ramach wielu subskrypcji.

Aby uzyskać instrukcje na temat sposobu ustawiania zasad przy użyciu programu PowerShell, zobacz [Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu modułu Azure RM PowerShell](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Czym są zasady zabezpieczeń?
Zasady zabezpieczeń definiują pożądaną konfigurację Twoich obciążeń oraz pomagają zapewnić zgodność z wymaganiami dotyczącymi zabezpieczeń określonymi przez firmę lub przepisy. Usługa Azure Policy można zdefiniować zasady dla subskrypcji platformy Azure i dostosowuj je do danego typu obciążenia lub wrażliwości danych. Na przykład aplikacje wykorzystujące dane podlegające ochronie, takie jak dane osobowe, mogą wymagać wyższego poziomu zabezpieczeń niż innych obciążeń. Aby ustawić zasady dla subskrypcji lub grupy zarządzania, należy ustawić je w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md).



Zasady zabezpieczeń dla dysku zalecenia dotyczące zabezpieczeń, który jest pobierany w usłudze Azure Security Center. Można monitorować zgodność z nimi ułatwiają znalezienie potencjalnych luk i uniknięcie zagrożeń. Aby uzyskać więcej informacji na temat sposobu określenia która opcja jest odpowiednia dla Ciebie, zobacz listę [zasad wbudowanych rozwiązań zabezpieczeń](security-center-policy-definitions.md).

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

Aby uzyskać więcej informacji na temat usługi Azure Policy, zobacz [Create and manage policies to enforce compliance (Tworzenie zasad i zarządzanie nimi w celu wymuszenia zgodności)](../azure-policy/create-manage-policy.md).

Zasady platformy Azure zawierają następujące składniki:

- A **zasad** reguła.
- **Inicjatywy** to zbiór zasad.
- **Przypisania** jest zastosowanie zasad lub inicjatywy do określonego zakresu (grupy zarządzania, subskrypcji lub grupy zasobów).

Zasób jest oceniany pod kątem zasad, które zostały do niego przypisane, i otrzymuje współczynnik zgodności w zależności od liczby zasad, z którymi jest zgodny.

## <a name="who-can-edit-security-policies"></a>Kto może edytować zasady zabezpieczeń?
Usługa Security Center korzysta z opartej na rolach kontrola dostępu (RBAC), który zapewnia wbudowane role, które można przypisać do użytkowników, grup i usług na platformie Azure. Po otwarciu Centrum zabezpieczeń, widzą tylko informacje związane z zasobami, które mają dostęp do. Co oznacza, że użytkownicy przypisano rolę właściciela, współautora lub czytelnika subskrypcji lub grupy zasobów, której należy zasób. Oprócz tych ról istnieją dwie określone role usługi Security Center:

- Czytelnik zabezpieczeń: ma widoku praw do usługi Security Center, który zawiera zalecenia, alerty, zasady i kondycję, ale one nie może wprowadzać zmian.
- Administrator zabezpieczeń: ma te same prawa widok jako rola Czytelnik zabezpieczeń, a można również możliwość aktualizowania zasad zabezpieczeń oraz odrzucania zaleceń i alertów.

## <a name="edit-security-policies"></a>Edytowanie zasad zabezpieczeń
Można edytować domyślnych zasad zabezpieczeń dla każdej subskrypcji platformy Azure i grup zarządzania w [usługi Azure Policy](../governance/policy/tutorials/create-and-manage.md). Aby zmodyfikować zasady zabezpieczeń, musisz być właścicielem, współautorem lub administratorem zabezpieczeń tej subskrypcji lub zawierającej ją grupy zarządzania.

Aby uzyskać instrukcje, jak edytować zasady zabezpieczeń, usługa Azure Policy, zobacz i [Tworzenie zasad w celu wymuszania zgodności i zarządzania nimi](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Wyświetl zasady zabezpieczeń

Aby wyświetlić zasady zabezpieczeń w usłudze Security Center:

1. W **usługi Security Center** pulpitu nawigacyjnego, wybierz opcję **zasady zabezpieczeń**.

    ![Okienko Zarządzanie zasadami](./media/security-center-policies/security-center-policy-mgt.png)

  W **Zarządzanie zasadami** ekranu, można wyświetlić liczbę grup zarządzania, subskrypcji i obszarów roboczych, a także strukturę grupy zarządzania.

  > [!NOTE]
  > - Na pulpicie nawigacyjnym usługi Security Center mogą być wyświetlane większej liczby subskrypcji **pokrycie subskrypcji** niż liczba wyświetlanych w obszarze subskrypcji **Zarządzanie zasadami**. W obszarze Pokrycie subskrypcji jest wyświetlana liczba subskrypcji w warstwie Standardowa, Bezpłatna i subskrypcji „nie pokrytych”. "Nie pokrytą" subskrypcji nie zostały włączone w usłudze Security Center i nie są wyświetlane w obszarze **Zarządzanie zasadami**.
  >

  Kolumny w tabeli wyświetlają następujące informacje:

 - **Przypisanie inicjatywy zasad** — Centrum zabezpieczeń [wbudowane zasady](security-center-policy-definitions.md) i inicjatyw, które są przypisane do subskrypcji lub grupę zarządzania.
 - **Zgodność** — ogólną ocenę zgodności dla grupy zarządzania, subskrypcji lub obszaru roboczego. Wynik ten jest średnią ważoną przypisań. Średnia ważona jest uwzględniania w liczbie zasad pojedynczego przypisania i liczbie zasobów, których dotyczy to przypisanie.

 Jeśli na przykład Twoja subskrypcja obejmuje dwie maszyny wirtualne i inicjatywę z przypisanymi do niej pięcioma zasadami, to masz w tej subskrypcji 10 ocen. Jeśli jedna z maszyn wirtualnych nie jest zgodna z dwoma zasadami, to ogólny wynik zgodności przypisania Twojej subskrypcji wynosi 80%.

 - **Pokrycie** — identyfikuje warstwę cenową bezpłatna lub standardowa, z systemem grupy zarządzania, subskrypcji lub obszaru roboczego.  Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center.
 - **Ustawienia** — subskrypcje mają łącze **edytować ustawienia**. Wybieranie **edytować ustawienia** pozwala zaktualizować swoje [ustawienia Centrum zabezpieczeń](security-center-policies-overview.md) dla każdej subskrypcji lub grupę zarządzania.

2. Wybierz subskrypcję lub grupę zarządzania zasad, których chcesz wyświetlić.

  - **Zasady zabezpieczeń** akcję podejmowaną przez przypisanych na subskrypcję lub grupę zarządzania wybrano zasad zostaną odzwierciedlone na ekranie.
  - U góry strony, należy użyć linków dostępnych do otwierania poszczególnych zasad **przypisania** stosująca na subskrypcję lub grupę zarządzania. Łącza umożliwia dostęp do przypisania, edytować i wyłączenie tych zasad. Na przykład jeśli przypisanie zasad o określonym skutecznie odmawia programu endpoint protection, służy link do zasad dostępu i go edytować lub wyłączyć.
  - Na liście zasad można wyświetlić skuteczne stosowanie zasad na grupy zarządzania lub subskrypcji. Oznacza to, że ustawienia poszczególnych zasad, które mają zastosowanie do zakresu są brane pod uwagę i znajdują się za pomocą zbiorczy wynik, jakie działania są podejmowane przez zasady. Na przykład jeśli w jedno przypisanie zasad jest wyłączona, ale w innym razie jest równa AuditIfNotExist, skumulowany efekt dotyczy AuditIfNotExist. Efekt bardziej aktywnego zawsze pierwszeństwo.
  - Może być skutku zasady: Dołącz inspekcji, AuditIfNotExists, Odmów, DeployIfNotExists, wyłączone. Aby uzyskać więcej informacji na temat sposobu stosowania skutków, zobacz [zrozumienie zasad efekty](../governance/policy/concepts/effects.md).

   ![ekran zasad](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Gdy można wyświetlić przypisane zasady, możesz zobaczyć przypisania wielu i możesz zobaczyć, jak każdego przypisania będzie konfigurowana na swój własny.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób konfigurowania zasad zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Uzyskiwanie wglądu na poziomie dzierżawy w usłudze Azure Security Center](security-center-management-groups.md) — informacje na temat sposobu konfigurowania grup zarządzania dla usługi Azure Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../azure-policy/azure-policy-introduction.md)
