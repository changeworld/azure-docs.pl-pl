---
title: Zasoby, role i kontrolę dostępu w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Właściciele, współautorzy i czytelnicy wglądu w szczegółowe dane Twojej organizacji.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: mbullwin
ms.openlocfilehash: 0f348e3e7dc2812bf354d1f8ec86330b0742439a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60373696"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Zasoby, role i kontrola dostępu w usłudze Application Insights

Można kontrolować, kto ma do odczytu i aktualizacji dostępu do danych na platformie Azure [usługi Application Insights][start], za pomocą [kontroli dostępu opartej na rolach w systemie Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Przypisywanie dostępu do użytkowników w **grupy zasobów lub subskrypcji** do której należy dany zasób aplikacji — nie w samym zasobem. Przypisz **współautora składników usługi Application Insights** roli. Dzięki temu jednolitego kontroli dostępu do testów sieci web i alerty wraz z dany zasób aplikacji. [Dowiedz się więcej](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Zasoby, grupy i subskrypcje

Pierwszy, definicje:

* **Zasób** — wystąpienie usługi Microsoft Azure. Zasób usługi Application Insights zbiera, analizuje i wyświetla dane telemetryczne wysyłane z aplikacji.  Inne typy zasobów platformy Azure obejmują aplikacje sieci web, baz danych i maszyn wirtualnych.
  
    Aby wyświetlić swoje zasoby, otwórz [witryny Azure portal][portal], zaloguj się i kliknij pozycję wszystkie zasoby. Aby odnaleźć zasób, wpisz część nazwy w polu filtru.
  
    ![Lista zasobów platformy Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupa zasobów** ] [ group] — każdy zasób należy do jednej grupy. Grupa jest wygodny sposób zarządzać powiązanymi zasobami, szczególnie w przypadku kontroli dostępu. Na przykład w jednej grupie zasobów można umieścić aplikacji sieci Web, zasób usługi Application Insights do monitorowania aplikacji i zasobów magazynu, zapewnienie wyeksportowane dane.

* [**Subskrypcja** ](https://portal.azure.com) — aby za pomocą usługi Application Insights lub innych zasobów platformy Azure, możesz zarejestrować się w ramach subskrypcji platformy Azure. Każda grupa zasobów należy do jednej subskrypcji platformy Azure, w którym wybierz pakiet cen i, jeśli subskrypcji organizacji, wybierz członków i uprawnień dostępu.
* [**Konto Microsoft** ] [ account] — nazwa użytkownika i hasło, którego używasz do logowania się na Microsoft Azure subskrypcje, usługi XBox Live, Outlook.com i innych usług firmy Microsoft.

## <a name="access"></a> Kontrola dostępu w grupie zasobów

Jest ważne dowiedzieć się, że oprócz zasób, który został utworzony dla aplikacji, istnieją także osobne zasoby ukryte alerty i testy sieci web. Są one dołączone do tej samej [grupy zasobów](#resource-group) jako zasób usługi Application Insights. Może również umieszczono innymi usługami Azure tam, takich jak witryny sieci Web lub magazynu.

Aby kontrolować dostęp do tych zasobów w związku z tym zalecane jest:

* Kontrola dostępu na **grupy zasobów lub subskrypcji** poziom.
* Przypisz **Współautor składnika usługi Application Insights** ról do użytkowników. Uprawnienie pozwoli im edytować testy sieci web, alerty i zasoby usługi Application Insights bez podawania dostępu do innych usług, w grupie.

## <a name="to-provide-access-to-another-user"></a>Aby uzyskać dostęp do innego użytkownika

Musisz mieć uprawnienia właściciela do subskrypcji lub grupy zasobów.

Użytkownik musi mieć [Account Microsoft][account], lub dostęp do ich [Account organizacji Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Można zapewnić dostęp do osób i grup użytkowników zdefiniowanych w usłudze Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Przejdź do grupy zasobów lub bezpośrednio z samego zasobu

Wybierz **kontrola dostępu (IAM)** z menu po lewej stronie.

![Zrzut ekranu dostęp do przycisku sterowania w witrynie Azure portal](./media/resources-roles-access-control/0001-access-control.png)

Wybierz **Dodaj przypisanie roli**

![Zrzut ekranu dostęp do menu sterowania za pomocą przycisku Dodaj wyróżniony czerwonym kolorem](./media/resources-roles-access-control/0002-add.png)

**Dodaj uprawnienia** widoku poniżej dotyczy przede wszystkim do zasobów usługi Application Insights, w przypadku był wyświetlany uprawnień kontroli dostępu z wyższego poziomu, takich jak grupy zasobów widział dodatkowe nienależącym do aplikacji Role skoncentrowane na szczegółowych informacji.

Aby wyświetlić informacje o wszystkich kontroli dostępu opartej na rolach na platformie Azure, Użyj wbudowanych ról [zawartość dokumentacji oficjalnego](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Lista ról użytkownika kontroli zrzut ekranu z dostępu](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Wybierz rolę

Gdy to stosowne, można przejść do skojarzonego dokumentacji oficjalnego.

| Rola | W grupie zasobów |
| --- | --- |
| [Właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Mogą wprowadzać dowolne zmiany, łącznie z dostępem użytkownika. |
| [Współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Można edytować żadnych wraz ze wszystkimi zasobami. |
| [Współautor składników usługi Insights aplikacji](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Można edytować zasoby usługi Application Insights, testy sieci web i alerty. |
| [Czytelnik](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Można wyświetlać, ale nie nastąpią żadne zmiany. |
| [Application Insights Snapshot Debugger](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Nadaje użytkownikowi uprawnienie korzystanie z funkcji Application Insights Snapshot Debugger. Należy pamiętać, że tej roli znajduje się w roli właściciela ani współautora. |
| Współautor zarządzania wersjami wdrażania usługi platformy Azure | Roli współautora dla usług wdrażania za pomocą wdrażania usługi Azure. |
| [Purger danych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Specjalne rola przeczyszczania danych osobowych. Zobacz nasze [wskazówki dotyczące danych osobowych](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) Aby uzyskać więcej informacji.   |
| Administrator usługi ExpressRoute | Można tworzyć delete i Zarządzaj trasami platformy express.|
| [Współautor usługi log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Współautor usługi log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia do maszyn wirtualnych; Odczytywanie kluczy kont magazynu, aby można było skonfigurować zbieranie dzienników z usługi Azure Storage; Tworzenie i konfigurowanie kont usługi Automation; Dodawanie rozwiązań. i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.  |
| [Czytelnik usługi log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| masterreader | Umożliwia użytkownikowi przeglądać wszystko, ale nie wprowadzać zmian. |
| [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i aktualizować ustawienia monitorowania. |
| [Monitorowanie metryk wydawcy](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Umożliwia publikowanie metryk dotyczących zasobów platformy Azure. |
| [Czytelnik monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Może odczytywać wszystkie dane monitorowania. |
| Współautor zasad zasobów (wersja zapoznawcza) | Użytkownicy uzupełnieni z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, Utwórz bilet pomocy technicznej i odczytu zasobów/hierarchii.  |
| [Administrator dostępu użytkowników](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Umożliwia użytkownikowi zarządzanie dostępem dla innych użytkowników do zasobów platformy Azure.|
| [Współautor witryny sieci Web](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami sieci web), ale nie umożliwia uzyskiwania dostępu do nich...|

"Edycja" obejmuje tworzenie, usuwanie i aktualizowanie:

* Zasoby
* Testy sieci Web
* Alerty
* Eksport ciągły

#### <a name="select-the-user"></a>Wybierz użytkownika

Jeśli użytkownik, który ma nie znajduje się w katalogu, możesz zaprosić każda osoba mająca konto Microsoft.
(Jeśli korzystają z usług, takich jak Outlook.com, OneDrive, Windows Phone i XBox Live, mają konta Microsoft.)

## <a name="related-content"></a>Powiązana zawartość

* [Oparta na rolach kontrola dostępu na platformie Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Zapytania programu PowerShell w celu ustalenia członkostwa w roli

Ponieważ niektóre role mogą być połączone z powiadomienia i wiadomości e-mail alertów może być przydatne można było wygenerować listę użytkowników, którzy należą do danej roli. Pomocne w generowaniu tych typów list, firma Microsoft oferuje następujące przykładowe zapytania, które można dostosować do określonych potrzeb:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Zapytanie całej subskrypcji dla ról administratora + rolą Współautor

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Właściciele i współautorzy kwerendy w kontekście określonego zasobu usługi Application Insights

```powershell
$resourceGroup = “RGNAME”
$resourceName = “AppInsightsName”
$resourceType = “microsoft.insights/components”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Zapytania w kontekście konkretnej grupy zasobów dla właściciele i współautorzy

```powershell
$resourceGroup = “RGNAME”
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
