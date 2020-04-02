---
title: Rozwiązywanie problemów z usługą Azure RBAC
description: Rozwiązywanie problemów z kontrolą dostępu opartą na rolach platformy Azure (Azure RBAC).
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 09d5b7a126a1b8832bfe40e2e25dd4000d5d9155
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548284"
---
# <a name="troubleshoot-azure-rbac"></a>Rozwiązywanie problemów z usługą Azure RBAC

W tym artykule odpowiedzieć na niektóre typowe pytania dotyczące kontroli dostępu opartej na rolach platformy Azure (Azure RBAC), dzięki czemu wiesz, czego się spodziewać podczas korzystania z ról i można rozwiązać problemy z dostępem.

## <a name="azure-role-assignments-limit"></a>Limit przydziałów ról platformy Azure

Platforma Azure obsługuje maksymalnie **2000** przypisań ról na subskrypcję. Jeśli podczas próby przypisania roli zostanie wyświetlony komunikat o błędzie "Nie można utworzyć więcej przypisań ról (kod: RoleAssignmentLimitExceeded)" podczas próby przypisania roli, spróbuj zmniejszyć liczbę przypisań ról w subskrypcji.

> [!NOTE]
> Limit **przypisania ról 2000** na subskrypcję jest stały i nie można go zwiększyć.

Jeśli zbliżasz się do tego limitu, oto kilka sposobów, które można zmniejszyć liczbę przypisań ról:

- Dodaj użytkowników do grup i przypisz role do grup. 
- Połącz wiele wbudowanych ról z rolą niestandardową. 
- Wykonuj typowe przypisania ról w wyższym zakresie, na przykład subskrypcji lub grupy zarządzania.
- Jeśli masz usługę Azure AD Premium P2, należy dokonać przypisania ról kwalifikują się w [usłudze Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) zamiast trwale przypisane. 
- Dodaj dodatkową subskrypcję. 

Aby uzyskać liczbę przypisań ról, można wyświetlić [wykres na kontroli dostępu (IAM) strony](role-assignments-list-portal.md#list-number-of-role-assignments) w witrynie Azure portal. Można również użyć następujących poleceń programu Azure PowerShell:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemy z przypisaniami ról platformy Azure

- Jeśli nie można dodać przypisania roli w witrynie Azure portal w **formancie dostępu (IAM),** ponieważ opcja **Dodaj** > **przypisanie roli** jest wyłączona lub ponieważ pojawia się błąd uprawnień "Klient z identyfikatorem `Microsoft.Authorization/roleAssignments/write` obiektu nie ma autoryzacji do wykonywania akcji", sprawdź, czy aktualnie zalogowano się z użytkownikiem, który ma przypisaną rolę, która ma uprawnienia, takie jak [Administrator dostępu do właściciela](built-in-roles.md#owner) lub [administratora dostępu użytkownika](built-in-roles.md#user-access-administrator) w zakresie, który próbujesz przypisać rolę.

## <a name="problems-with-custom-roles"></a>Problemy z rolami niestandardowymi

- Jeśli potrzebujesz kroków dotyczących tworzenia roli niestandardowej, zobacz samouczki ról niestandardowych przy użyciu [witryny Azure Portal](custom-roles-portal.md) (obecnie w wersji zapoznawczej), [programu Azure PowerShell](tutorial-custom-role-powershell.md)lub [interfejsu wiersza polecenia platformy Azure.](tutorial-custom-role-cli.md)
- Jeśli nie możesz zaktualizować istniejącej roli niestandardowej, sprawdź, czy aktualnie zalogowano się u `Microsoft.Authorization/roleDefinition/write` użytkownika, do którego przypisano rolę, która ma uprawnienia, taką jak [Administrator dostępu do użytkownika](built-in-roles.md#owner) lub Administrator dostępu [użytkownika.](built-in-roles.md#user-access-administrator)
- Jeśli nie można usunąć roli niestandardowej i uzyskać komunikat o błędzie "Istnieją istniejące przypisania ról odwołujących się do roli (kod: RoleDefinitionHasAsSignments)", a następnie istnieją przypisania ról nadal przy użyciu roli niestandardowej. Usuń te przypisania ról i spróbuj ponownie usunąć rolę niestandardową.
- Jeśli otrzymujesz komunikat o błędzie „Przekroczono limit definicji ról. Nie można utworzyć więcej definicji ról (kod: RoleDefinitionLimitExceeded)" podczas próby utworzenia nowej roli niestandardowej, usuń wszystkie role niestandardowe, które nie są używane. Platforma Azure obsługuje maksymalnie **5000** ról niestandardowych w katalogu. (Dla platformy Azure Niemcy i Azure China 21Vianet limit wynosi 2000 ról niestandardowych.)
- Jeśli pojawi się błąd podobny do "Klient ma uprawnienia do wykonywania akcji "Microsoft.Authorization/roleDefinitions/write" w zakresie "/subscriptions/{subscriptionid}", jednak nie znaleziono połączonej subskrypcji podczas próby zaktualizowania roli niestandardowej, sprawdź, czy jeden lub więcej [zakresów podlegania przydziałowi](role-definitions.md#assignablescopes) zostało usuniętych w katalogu. Jeśli zakres został usunięty, utwórz bilet pomocy technicznej, ponieważ w tej chwili nie jest dostępne żadne rozwiązanie samoobsługowe.

## <a name="custom-roles-and-management-groups"></a>Niestandardowe role i grupy zarządzania

- Można zdefiniować tylko jedną `AssignableScopes` grupę zarządzania w roli niestandardowej. Dodawanie grupy zarządzania do `AssignableScopes` jest obecnie w wersji zapoznawczej.
- Role niestandardowe z `DataActions` nie można przypisać w zakresie grupy zarządzania.
- Usługa Azure Resource Manager nie sprawdza poprawności istnienia grupy zarządzania w zakresie przypisywalnym definicji roli.
- Aby uzyskać więcej informacji na temat ról niestandardowych i grup zarządzania, zobacz [Organizowanie zasobów za pomocą grup zarządzania platformy Azure](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="transferring-a-subscription-to-a-different-directory"></a>Przenoszenie subskrypcji do innego katalogu

- Jeśli potrzebujesz kroków, aby przenieść subskrypcję do innego katalogu usługi Azure AD, zobacz [Przeniesienie własności subskrypcji platformy Azure na inne konto.](../cost-management-billing/manage/billing-subscription-transfer.md)
- Jeśli przeniesiesz subskrypcję do innego katalogu usługi Azure AD, wszystkie przypisania ról zostaną **trwale** usunięte ze źródłowego katalogu usługi Azure AD i nie zostaną zmigrowane do docelowego katalogu usługi Azure AD. Należy ponownie utworzyć przypisania ról w katalogu docelowym. Należy również ręcznie odtworzyć tożsamości zarządzane dla zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania i znane problemy z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/known-issues.md).
- Jeśli jesteś administratorem globalnym usługi Azure AD i nie masz dostępu do subskrypcji po jej przeniesieniu między katalogami, użyj **przełącznika Zarządzanie programem Access dla zasobów platformy Azure,** aby tymczasowo [podnieść poziom dostępu,](elevate-access-global-admin.md) aby uzyskać dostęp do subskrypcji.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemy z administratorami usług lub współadministratorami

- Jeśli masz problemy z administratorem usługi lub współadministratorami, zobacz [Dodawanie lub zmienianie administratorów subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) i ról [administratora subskrypcji klasycznej, ról platformy Azure i ról administratora usługi Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Odmowa dostępu lub błędy uprawnień

- Jeśli podczas próby utworzenia zasobu zostanie wyświetlony błąd uprawnień "Klient z identyfikatorem obiektu nie ma autoryzacji do wykonywania akcji w zakresie (kod: AuthorizationFailed)", podczas próby utworzenia zasobu, sprawdź, czy aktualnie zalogowano się z użytkownikiem, który ma przypisaną rolę, która ma uprawnienia do zapisu do zasobu w wybranym zakresie. Na przykład do zarządzania maszynami wirtualnymi w grupie zasobów musisz mieć rolę [Współautor maszyny wirtualnej](built-in-roles.md#virtual-machine-contributor) w grupie zasobów (lub zakresie nadrzędnym). Aby uzyskać listę uprawnień dla każdej roli wbudowanej, zobacz [Role wbudowane dla zasobów platformy Azure](built-in-roles.md).
- Jeśli podczas próby utworzenia lub zaktualizowania biletu pomocy technicznej zostanie wyświetlony błąd uprawnień "Nie masz uprawnień do tworzenia żądania pomocy technicznej", sprawdź, `Microsoft.Support/supportTickets/write` czy aktualnie zalogowano się u użytkownika, do którego przypisano rolę, która ma uprawnienie, na przykład [Współautor żądania pomocy technicznej.](built-in-roles.md#support-request-contributor)

## <a name="role-assignments-with-unknown-security-principal"></a>Przypisania ról z nieznanym podmiotem zabezpieczeń

Jeśli rola zostanie przypisana do podmiotu zabezpieczeń (użytkownika, grupy, jednostki usługi lub tożsamości zarządzanej), a następnie usuniesz tego podmiotu zabezpieczeń bez usuwania przypisania roli, typ jednostki zabezpieczeń dla przypisania roli zostanie wyświetlony jako **Nieznany**. Poniższy zrzut ekranu przedstawia przykład w witrynie Azure Portal. Nazwa podmiotu zabezpieczeń jest wyświetlana jako **tożsamość usunięta,** a **tożsamość już nie istnieje**. 

![Grupa zasobów aplikacji sieci Web](./media/troubleshooting/unknown-security-principal.png)

Jeśli listę tego przypisania roli przy użyciu programu `DisplayName` Azure `ObjectType` PowerShell, zobaczysz pusty i zestaw nieznany. Na przykład [get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) zwraca przypisanie roli, które jest podobne do następujących:

```
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

Podobnie, jeśli listę tego przypisania roli przy użyciu `principalName`interfejsu wiersza polecenia platformy Azure, zobaczysz pusty . Na przykład [lista przypisania roli az](/cli/azure/role/assignment#az-role-assignment-list) zwraca przypisanie roli, które jest podobne do następujących:

```
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

Nie jest to problem, aby pozostawić te przypisania ról, ale można je usunąć za pomocą kroków, które są podobne do innych przypisań ról. Aby uzyskać informacje dotyczące usuwania przypisań ról, zobacz [Witryna Azure Portal](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)lub [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

W programie PowerShell, jeśli spróbujesz usunąć przypisania ról przy użyciu identyfikatora obiektu i nazwy definicji roli, a więcej niż jedno przypisanie roli odpowiada parametrom, zostanie wyświetlony komunikat o błędzie: "Podane informacje nie są mapowane na przypisanie roli". Poniżej przedstawiono przykład komunikatu o błędzie:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Jeśli zostanie wyświetlony ten komunikat o błędzie, upewnij się, że również określić `-Scope` lub `-ResourceGroupName` parametry.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Zmiany przypisania roli nie są wykrywane

Usługa Azure Resource Manager czasami buforuje konfiguracje i dane w celu zwiększenia wydajności. Dodanie lub usunięcie przypisań ról może potrwać do 30 minut, aby zmiany zostały wprowadzone. Jeśli używasz witryny Azure portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, możesz wymusić odświeżenie zmian przypisania roli, wylogując się i logując się. Jeśli wprowadzasz zmiany przypisania ról za pomocą wywołań interfejsu API REST, możesz wymusić odświeżenie, odświeżając token dostępu.

Jeśli dodajesz lub usuwasz przypisanie roli w `DataActions`zakresie grupy zarządzania, a rola ma, dostęp na płaszczyźnie danych może nie być aktualizowany przez kilka godzin. Dotyczy to tylko zakresu grupy zarządzania i płaszczyzny danych.

## <a name="web-app-features-that-require-write-access"></a>Funkcje aplikacji sieci Web, które wymagają dostępu do zapisu

Jeśli udzielisz użytkownikowi dostępu tylko do odczytu do jednej aplikacji sieci web, niektóre funkcje są wyłączone, których nie można się spodziewać. Następujące funkcje zarządzania wymagają dostępu do **zapisu** do aplikacji sieci web (współautor lub właściciel) i nie są dostępne w żadnym scenariuszu tylko do odczytu.

* Polecenia (takie jak start, stop itp.)
* Zmiana ustawień, takich jak konfiguracja ogólna, ustawienia skali, ustawienia kopii zapasowej i ustawienia monitorowania
* Uzyskiwanie dostępu do poświadczeń publikowania i innych wpisów tajnych, takich jak ustawienia aplikacji i parametry połączenia
* Dzienniki przesyłania strumieniowego
* Konfiguracja dzienników diagnostycznych
* Konsola (wiersz polecenia)
* Aktywne i najnowsze wdrożenia (dla ciągłego wdrażania git lokalnego)
* Szacowane wydatki
* Testy internetowe
* Sieć wirtualna (widoczna dla czytnika tylko wtedy, gdy sieć wirtualna została wcześniej skonfigurowana przez użytkownika z dostępem do zapisu).

Jeśli nie możesz uzyskać dostępu do któregokolwiek z tych kafelków, musisz poprosić administratora o dostęp współautora do aplikacji sieci web.

## <a name="web-app-resources-that-require-write-access"></a>Zasoby aplikacji sieci Web, które wymagają dostępu do zapisu

Aplikacje sieci Web są skomplikowane przez obecność kilku różnych zasobów, które współdziałają. Oto typowa grupa zasobów z kilkoma stronami internetowymi:

![Grupa zasobów aplikacji sieci Web](./media/troubleshooting/website-resource-model.png)

W rezultacie jeśli udzielisz komuś dostępu tylko do aplikacji sieci web, wiele funkcji w bloku witryny sieci Web w witrynie Azure portal jest wyłączona.

Te elementy wymagają dostępu do **zapisu** do **planu usługi app service,** który odpowiada witrynie sieci Web:  

* Wyświetlanie warstwy cenowej aplikacji sieci web (bezpłatna lub standardowa)  
* Konfiguracja skalowania (liczba wystąpień, rozmiar maszyny wirtualnej, ustawienia skalowania automatycznego)  
* Przydziały (pamięć masowa, przepustowość, procesor)  

Te elementy wymagają dostępu do **zapisu** do całej **grupy zasobów,** która zawiera witrynę sieci Web:  

* Certyfikaty i powiązania TLS/SSL (certyfikaty TLS/SSL mogą być współużytkowane między lokacjami w tej samej grupie zasobów i lokalizacji geograficznej)  
* Reguły alertów  
* Ustawienia skalowania automatycznego  
* Składniki szczegółowych informacji o aplikacjach  
* Testy internetowe  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkcje maszyny wirtualnej, które wymagają dostępu do zapisu

Podobnie jak w przypadku aplikacji sieci web, niektóre funkcje w bloku maszyny wirtualnej wymagają dostępu do zapisu do maszyny wirtualnej lub do innych zasobów w grupie zasobów.

Maszyny wirtualne są powiązane z nazwami domen, sieciami wirtualnymi, kontami magazynu i regułami alertów.

Te elementy wymagają dostępu do **zapisu** do **maszyny wirtualnej:**

* Punkty końcowe  
* Adresy IP  
* Dyski  
* Rozszerzenia  

Wymagają one dostępu do **zapisu** zarówno do **maszyny wirtualnej,** jak i **do grupy Zasobów** (wraz z nazwą domeny), w jakiej się znajduje:  

* Zestaw dostępności  
* Zestaw z równoważenia obciążenia  
* Reguły alertów  

Jeśli nie możesz uzyskać dostępu do żadnego z tych kafelków, poproś administratora o dostęp współautora do grupy Zasoby.

## <a name="azure-functions-and-write-access"></a>Usługi Azure i dostęp do zapisu

Niektóre funkcje [usługi Azure Functions](../azure-functions/functions-overview.md) wymagają dostępu do zapisu. Na przykład jeśli użytkownik ma przypisany do roli [czytelnika,](built-in-roles.md#reader) nie będzie mógł wyświetlić funkcji w aplikacji funkcji. Portal wyświetli **(brak dostępu)**.

![Aplikacje funkcyjne bez dostępu](./media/troubleshooting/functionapps-noaccess.png)

Czytnik może kliknąć kartę **Funkcje platformy,** a następnie kliknąć wszystkie **ustawienia,** aby wyświetlić niektóre ustawienia związane z aplikacją funkcyjną (podobną do aplikacji sieci web), ale nie może zmodyfikować żadnego z tych ustawień. Aby uzyskać dostęp do tych funkcji, musisz roli [współautora.](built-in-roles.md#contributor)

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów dla użytkowników-gości](role-assignments-external-users.md#troubleshoot)
- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](role-assignments-portal.md)
- [Wyświetlanie dzienników aktywności dla zmian RBAC w zasobach platformy Azure](change-history-report.md)
