---
title: Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure | Microsoft Docs
description: Rozwiązywanie problemów z kontrolą dostępu opartą na rolach (RBAC) dla zasobów platformy Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 158222c256e3efc7ca87d7a3781ca68e1c4307b1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750184"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Rozwiązywanie problemów z funkcją RBAC dla zasobów platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące kontroli dostępu opartej na rolach (RBAC) dla zasobów platformy Azure, aby wiedzieć, czego można oczekiwać w przypadku używania ról w Azure Portal i rozwiązywania problemów z dostępem.

## <a name="problems-with-rbac-role-assignments"></a>Problemy z przypisaniami ról kontroli dostępu opartej na rolach

- Jeśli nie można dodać przypisania roli w Azure Portal na **kontroli dostępu (IAM)** , ponieważ opcja **Dodaj**  > **Dodaj przypisanie roli** jest wyłączona lub z powodu błędu uprawnień "klient z identyfikatorem obiektu nie ma Autoryzacja do wykonania akcji ", sprawdź, czy jesteś zalogowany przy użyciu użytkownika, który ma przypisaną rolę z uprawnieniami `Microsoft.Authorization/roleAssignments/write`, takimi jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) w zakresie, w którym próbujesz przypisać rolę.
- Jeśli zostanie wyświetlony komunikat o błędzie "nie można utworzyć więcej przypisań ról (kod: RoleAssignmentLimitExceeded)" podczas próby przypisania roli, spróbuj zmniejszyć liczbę przypisań ról, przypisując role do grup. Platforma Azure obsługuje maksymalnie **2000** przypisań ról na subskrypcję.

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Jeśli potrzebujesz kroków dotyczących sposobu tworzenia roli niestandardowej, zobacz samouczki ról niestandardowych przy użyciu [Azure PowerShell](tutorial-custom-role-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](tutorial-custom-role-cli.md).
- Jeśli nie możesz zaktualizować istniejącej roli niestandardowej, sprawdź, czy użytkownik jest zalogowany przy użyciu przypisanej roli z uprawnieniami `Microsoft.Authorization/roleDefinition/write`, takich jak [właściciel](built-in-roles.md#owner) lub [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator).
- Jeśli nie można usunąć roli niestandardowej i uzyskać komunikatu o błędzie "istnieją istniejące przypisania ról odwołujące się do roli (kod: RoleDefinitionHasAssignments)", a następnie istnieją przypisania ról nadal korzystające z roli niestandardowej. Usuń te przypisania ról i spróbuj ponownie usunąć rolę niestandardową.
- Jeśli otrzymujesz komunikat o błędzie „Przekroczono limit definicji ról. Nie można utworzyć więcej definicji ról (kod: RoleDefinitionLimitExceeded) "podczas próby utworzenia nowej roli niestandardowej, Usuń wszystkie niestandardowe role, które nie są używane. Platforma Azure obsługuje do **5000** ról niestandardowych w dzierżawie. (W przypadku chmur wyspecjalizowanych, takich jak platformy Azure Government, Azure Niemcy i Azure Chiny 21Vianet limit wynosi 2000 ról niestandardowych).
- Jeśli zostanie wyświetlony komunikat o błędzie podobny do "klient ma uprawnienia do wykonania akcji" Microsoft. Authorization/roleDefinitions/Write "w zakresie"/subscriptions/{SubscriptionID} ", ale nie znaleziono połączonej subskrypcji" podczas próby zaktualizowania roli niestandardowej, sprawdź czy co najmniej jeden [przypisany zakres](role-definitions.md#assignablescopes) został usunięty w dzierżawie. Jeśli zakres został usunięty, utwórz bilet pomocy technicznej, ponieważ w tej chwili nie jest dostępne żadne rozwiązanie samoobsługowe.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Odzyskiwanie kontroli dostępu opartej na rolach po przeniesieniu subskrypcji między dzierżawami

- Jeśli potrzebujesz kroków dotyczących sposobu transferowania subskrypcji do innej dzierżawy usługi Azure AD, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../billing/billing-subscription-transfer.md).
- Jeśli przeniesiesz subskrypcję do innej dzierżawy usługi Azure AD, wszystkie przypisania ról zostaną trwale usunięte ze źródłowej dzierżawy usługi Azure AD i nie zostaną zmigrowane do docelowej dzierżawy usługi Azure AD. Należy ponownie utworzyć swoje przypisania ról w dzierżawie docelowej. Należy również ręcznie odtworzyć zarządzane tożsamości dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy związane z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/known-issues.md).
- Jeśli jesteś administratorem globalnym usługi Azure AD i nie masz dostępu do subskrypcji po przeniesieniu między dzierżawcami, użyj przełącznika **Zarządzanie dostępem do zasobów platformy Azure** , aby tymczasowo [podnieść dostęp](elevate-access-global-admin.md) do subskrypcji.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemy z administratorami usług lub współadministratorami

- Jeśli masz problemy z administratorem usługi lub współadministratorem, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../billing/billing-add-change-azure-subscription-administrator.md) i [ról administratora klasycznej subskrypcji, ról RBAC platformy Azure i ról administratorów usługi Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odmowa dostępu lub błędy uprawnień

- Jeśli zostanie wyświetlony komunikat o błędzie "klient z identyfikatorem obiektu nie ma autoryzacji do wykonania akcji względem zakresu (kod: AuthorizationFailed)" podczas próby utworzenia zasobu, należy sprawdzić, czy użytkownik jest zalogowany przy użyciu przypisanej roli z uprawnieniami zapisu uprawnienie do zasobu w wybranym zakresie. Na przykład do zarządzania maszynami wirtualnymi w grupie zasobów musisz mieć rolę [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w grupie zasobów (lub zakresie nadrzędnym). Aby uzyskać listę uprawnień dla każdej roli wbudowanej, zobacz [Role wbudowane dla zasobów platformy Azure](built-in-roles.md).
- Jeśli zostanie wyświetlony komunikat o błędzie "nie masz uprawnień do utworzenia żądania pomocy technicznej" podczas próby utworzenia lub zaktualizowania biletu pomocy technicznej, sprawdź, czy jesteś zalogowanym użytkownikiem, do którego przypisano rolę z uprawnieniem `Microsoft.Support/supportTickets/write` , na przykład [współautor żądania pomocy technicznej](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Przypisania ról z nieznanym podmiotem zabezpieczeń

Po wyświetleniu listy przypisań ról przy użyciu Azure PowerShell można zobaczyć przydziały z pustą `DisplayName` i `ObjectType` ustawione jako nieznane. Na przykład polecenie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zwraca przypisanie roli podobne do następujących:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Podobnie podczas wyświetlania listy przypisań ról przy użyciu interfejsu wiersza polecenia platformy Azure mogą być widoczne przydziały z pustą `principalName`. Na przykład [AZ role list przypisywanie](/cli/azure/role/assignment#az-role-assignment-list) zwraca przypisanie roli, które jest podobne do następujących:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Te przypisania ról są wykonywane, gdy przypiszesz rolę do podmiotu zabezpieczeń (użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej) i później usuniesz ten podmiot zabezpieczeń. Te przydziały ról nie są wyświetlane w Azure Portal i nie jest to problem, aby je opuścić. Jeśli jednak chcesz, możesz usunąć te przypisania ról.

Aby usunąć te przypisania ról, Użyj poleceń [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) lub [AZ role Delete](/cli/azure/role/assignment#az-role-assignment-delete) .

W programie PowerShell, jeśli spróbujesz usunąć przypisania roli przy użyciu identyfikatora obiektu i nazwy definicji roli, a więcej niż jedno przypisanie roli jest zgodne z parametrami, zostanie wyświetlony komunikat o błędzie: "podane informacje nie są mapowane na przypisanie roli". Poniżej przedstawiono przykładowy komunikat o błędzie:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Jeśli ten komunikat o błędzie jest wyświetlany, upewnij się, że określono również parametry `-Scope` lub `-ResourceGroupName`.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Zmiany RBAC nie są wykrywane

Czasami Azure Resource Manager buforuje konfiguracje i dane w celu zwiększenia wydajności. W przypadku tworzenia lub usuwania przypisań ról może upłynąć do 30 minut, aby zmiany zaczęły obowiązywać. Jeśli używasz Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmian przypisania roli przez wylogowanie się i zalogowanie się. W przypadku wprowadzania zmian przypisywania ról przy użyciu wywołań interfejsu API REST można wymusić odświeżenie tokenu dostępu.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji sieci Web, które wymagają dostępu do zapisu

Jeśli przyznasz użytkownikowi dostęp tylko do odczytu do pojedynczej aplikacji sieci Web, niektóre funkcje są wyłączone, które mogą być nieoczekiwane. Następujące funkcje zarządzania wymagają dostępu do **zapisu** do aplikacji sieci Web (współautor lub właściciela) i nie są dostępne w żadnym scenariuszu tylko do odczytu.

* Polecenia (takie jak uruchamianie, zatrzymywanie itp.)
* Zmiana ustawień, takich jak Konfiguracja ogólna, ustawienia skalowania, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i innych wpisów tajnych, takich jak ustawienia aplikacji i parametry połączenia
* Dzienniki przesyłania strumieniowego
* Konfiguracja dzienników diagnostycznych
* Konsola (wiersz polecenia)
* Aktywne i niedawne wdrożenia (dla lokalnego wdrożenia usługi Git)
* Szacowane wydatki
* Testy sieci Web
* Sieć wirtualna (widoczna tylko dla czytnika, jeśli sieć wirtualna została wcześniej skonfigurowana przez użytkownika z prawem do zapisu).

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, musisz polecić administratorowi dostęp współautora do aplikacji sieci Web.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji sieci Web, które wymagają dostępu do zapisu

Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które współpraca. Poniżej przedstawiono typową grupę zasobów z kilkoma witrynami sieci Web:

![Grupa zasobów aplikacji sieci Web](./media/troubleshooting/website-resource-model.png)

W związku z tym, jeśli przyznasz komuś dostęp tylko do aplikacji sieci Web, większość funkcji w bloku witryna sieci Web w Azure Portal jest wyłączona.

Elementy te wymagają dostępu do **zapisu** do **planu App Service** , który odpowiada witrynie sieci Web:  

* Wyświetlanie warstwy cenowej aplikacji sieci Web (bezpłatna lub standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia skalowania automatycznego)  
* Przydziały (magazyn, przepustowość, procesor CPU)  

Te elementy wymagają dostępu do **zapisu** w całej **grupie zasobów** zawierającej witrynę sieci Web:  

* Certyfikaty i powiązania SSL (certyfikaty SSL mogą być współużytkowane między lokacjami w tej samej grupie zasobów i lokalizacji geograficznej)  
* Reguły alertów  
* Ustawienia skalowania automatycznego  
* Składniki usługi Application Insights  
* Testy sieci Web  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w przypadku aplikacji sieci Web, niektóre funkcje w bloku maszyny wirtualnej wymagają dostępu do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są powiązane z nazwami domen, sieciami wirtualnymi, kontami magazynu i regułami alertów.

Elementy te wymagają dostępu do **zapisu** dla **maszyny wirtualnej**:

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one dostępu do **zapisu** zarówno dla **maszyny wirtualnej**, jak i **grupy zasobów** (wraz z nazwą domeny), w której znajduje się:  

* Zestaw dostępności  
* Zestaw o zrównoważonym obciążeniu  
* Reguły alertów  

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, poprosimy administratora o dostęp współautora do grupy zasobów.

## <a name="azure-functions-and-write-access"></a>Dostęp Azure Functions i zapisu

Niektóre funkcje [Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Jeśli na przykład użytkownik ma przypisaną rolę [czytelnik](built-in-roles.md#reader) , nie będzie mógł wyświetlać funkcji w ramach aplikacji funkcji. Zostanie wyświetlony portal **(brak dostępu)** .

![Aplikacje funkcji Brak dostępu](./media/troubleshooting/functionapps-noaccess.png)

Czytelnik może kliknąć kartę **funkcje platformy** , a następnie kliknąć pozycję **wszystkie ustawienia** , aby wyświetlić niektóre ustawienia związane z aplikacją funkcji (podobną do aplikacji sieci Web), ale nie mogą modyfikować żadnego z tych ustawień. Aby uzyskać dostęp do tych funkcji, będzie potrzebna rola [współautor](built-in-roles.md#contributor) .

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z użytkownikami-Gośćmi](role-assignments-external-users.md#troubleshoot)
- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](role-assignments-portal.md)
- [Wyświetlanie dzienników aktywności związanych ze zmianami RBAC w zasobach platformy Azure](change-history-report.md)

