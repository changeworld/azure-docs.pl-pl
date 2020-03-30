---
title: Zasoby, role i kontrola dostępu w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Właściciele, współautorzy i czytelnicy informacji o organizacji.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473136"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Zasoby, role i kontrola dostępu w usłudze Application Insights

Możesz kontrolować, kto ma dostęp do danych odczytu i aktualizacji w usłudze Azure [Application Insights,][start]korzystając z [kontroli dostępu opartej na rolach na platformie Microsoft Azure.](../../role-based-access-control/role-assignments-portal.md)

> [!IMPORTANT]
> Przypisz dostęp do użytkowników w **grupie zasobów lub subskrypcji,** do której należy zasób aplikacji — nie w samym zasobie. Przypisz rolę **współautora składnika usługi Application Insights.** Zapewnia to jednolitą kontrolę dostępu do testów internetowych i alertów wraz z zasobem aplikacji. [Dowiedz się więcej](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Zasoby, grupy i subskrypcje

Po pierwsze, niektóre definicje:

* **Zasób** — wystąpienie usługi Microsoft Azure. Zasób usługi Application Insights zbiera, analizuje i wyświetla dane telemetryczne wysyłane z aplikacji.  Inne typy zasobów platformy Azure obejmują aplikacje sieci web, bazy danych i maszyny wirtualne.
  
    Aby wyświetlić swoje zasoby, otwórz [witrynę Azure portal][portal], zaloguj się i kliknij pozycję Wszystkie zasoby. Aby znaleźć zasób, wpisz część jego nazwy w polu filtru.
  
    ![Lista zasobów platformy Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupa zasobów**][group] — każdy zasób należy do jednej grupy. Grupa to wygodny sposób zarządzania powiązanymi zasobami, szczególnie w celu kontroli dostępu. Na przykład w jednej grupie zasobów można umieścić aplikację sieci Web, zasób usługi Application Insights do monitorowania aplikacji i zasób magazynu w celu zachowania eksportowanych danych.

* [**Subskrypcja**](https://portal.azure.com) — aby korzystać z usługi Application Insights lub innych zasobów platformy Azure, należy zalogować się do subskrypcji platformy Azure. Każda grupa zasobów należy do jednej subskrypcji platformy Azure, w której wybierasz pakiet cenowy, a jeśli jest to subskrypcja organizacji, wybierz członków i ich uprawnienia dostępu.
* [**Konto Microsoft**][account] — nazwa użytkownika i hasło używane do logowania się do subskrypcji platformy Microsoft Azure, XBox Live, Outlook.com i innych usług firmy Microsoft.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Kontrola dostępu w grupie zasobów

Ważne jest, aby zrozumieć, że oprócz zasobu utworzonego dla aplikacji, istnieją również oddzielne ukryte zasoby dla alertów i testów sieci web. Są one dołączone do tej samej [grupy zasobów](#resource-group) co zasób usługi Application Insights. Być może umieścino tam również inne usługi platformy Azure, takie jak witryny sieci Web lub magazyn.

Aby kontrolować dostęp do tych zasobów, zaleca się zatem:

* Kontroluj dostęp na poziomie **grupy zasobów lub subskrypcji.**
* Przypisz do użytkowników rolę **współautora składnika usługi Application Insights.** Dzięki temu mogą edytować testy sieci Web, alerty i zasoby usługi Application Insights bez zapewniania dostępu do innych usług w grupie.

## <a name="to-provide-access-to-another-user"></a>Aby zapewnić dostęp do innego użytkownika

Musisz mieć prawa właściciela do subskrypcji lub grupy zasobów.

Użytkownik musi mieć [konto Microsoft][account]lub dostęp do swojego [organizacyjnego konta Microsoft.](../../active-directory/fundamentals/sign-up-organization.md) Można zapewnić dostęp do osób fizycznych, a także do grup użytkowników zdefiniowanych w usłudze Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Przejdź do grupy zasobów lub bezpośrednio do samego zasobu

Z menu po lewej stronie wybierz **polecenie Kontrola dostępu (IAM).**

![Zrzut ekranu przedstawiający przycisk kontroli dostępu w witrynie Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Wybierz **pozycję Dodaj przypisanie roli**

![Zrzut ekranu przedstawiający menu kontroli dostępu z wyróżnionym przyciskiem dodaj na czerwono](./media/resources-roles-access-control/0002-add.png)

Widok **Dodaj uprawnienia** poniżej jest przede wszystkim specyficzne dla zasobów usługi Application Insights, jeśli były wyświetlane uprawnienia kontroli dostępu z wyższego poziomu, takich jak grupy zasobów, zobaczysz dodatkowe role nie-Application Insights-centric.

Aby wyświetlić informacje o wszystkich wbudowanych rolach kontroli dostępu opartych na rolach platformy Azure, użyj [oficjalnej zawartości referencyjnej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Zrzut ekranu przedstawiający listę roli użytkownika kontroli dostępu](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Wybierz rolę

W stosownych przypadkach łączymy się z powiązaną oficjalną dokumentacją referencyjną.

| Rola | W grupie zasobów |
| --- | --- |
| [Właściciel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Może zmienić wszystko, w tym dostęp użytkownika. |
| [Współautor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Może edytować wszystko, łącznie ze wszystkimi zasobami. |
| [Współautor składnika usługi Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Można edytować zasoby usługi Application Insights. |
| [Czytelnik](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Można przeglądać, ale nie zmieniać niczego. |
| [Debuger migawek usługi Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do korzystania z funkcji debugera migawki usługi Application Insights. Należy zauważyć, że ta rola nie jest uwzględniona w rolach właściciela ani współautora. |
| Współautor zarządzania wersjami wdrażania usługi Azure | Rola współautora dla usług wdrażających za pośrednictwem narzędzia Azure Service Deploy. |
| [Purger danych](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Szczególna rola w usuwaniu danych osobowych. Więcej informacji można znaleźć w naszych [wskazówkach dotyczących danych osobowych.](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)   |
| ExpressRoute Administrator | Można tworzyć usuwanie tras ekspresowych i zarządzać nimi.|
| [Współautor usługi Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Współautor usługi Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Ustawienia monitorowania edycji obejmują dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; odczytywanie kluczy konta magazynu, aby móc skonfigurować zbieranie dzienników z usługi Azure Storage; tworzenie i konfigurowanie kont automatyzacji; dodawanie rozwiązań; i konfigurowanie diagnostyki platformy Azure na wszystkich zasobach platformy Azure.  |
| [Czytelnik usługi Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Program Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym wyświetlać konfigurację diagnostyki platformy Azure na wszystkich zasobach platformy Azure. |
| masterreader | Umożliwia użytkownikowi wyświetlanie wszystkiego, ale nie wprowadzanie zmian. |
| [Współautor monitorowania](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i aktualizować ustawienia monitorowania.|
| [Monitorowanie metryki Wydawca](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Umożliwia publikowanie metryk względem zasobów platformy Azure. |
| [Czytnik monitoringu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Może odczytać wszystkie dane z monitoringu. |
| Współautor zasad zasobów (wersja zapoznawcza) | Zapełnieni użytkownicy z EA, z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytu zasobu/hierarchii.  |
| [Administrator dostępu użytkowników](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Umożliwia użytkownikowi zarządzanie dostępem dla innych użytkowników do zasobów platformy Azure.|
| [Współautor strony internetowej](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie ma do nich dostępu..|

"Edycja" obejmuje tworzenie, usuwanie i aktualizowanie:

* Resources
* Testy internetowe
* Alerty
* Eksport ciągły

#### <a name="select-the-user"></a>Wybierz użytkownika

Jeśli użytkownik, którego chcesz, nie znajduje się w katalogu, możesz zaprosić do tego wszystkie osoby z kontem Microsoft.
(Jeśli korzystają z usług takich jak Outlook.com, OneDrive, Windows Phone lub XBox Live, mają konto Microsoft).

## <a name="related-content"></a>Zawartość pokrewna

* [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Zapytanie programu PowerShell w celu określenia członkostwa w roli

Ponieważ niektóre role mogą być połączone z powiadomieniami i alertami e-mail, pomocne może być wygenerowanie listy użytkowników należących do danej roli. Aby ułatwić generowanie tych typów list, oferujemy następujące przykładowe zapytania, które można dostosować do konkretnych potrzeb:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Zapytanie o całą subskrypcję ról administratora + ról współautora

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Zapytanie w kontekście określonego zasobu usługi Application Insights dla właścicieli i współautorów

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Zapytanie w kontekście określonej grupy zasobów dla właścicieli i współautorów

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
