---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanych
description: Uzyskiwanie dostępu do zasobów w innych dzierżawach usługi Azure Active Directory bez logowania się przy użyciu poświadczeń lub wpisów tajnych przy użyciu tożsamości zarządzanej
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117458"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Uwierzytelnij dostęp do zasobów platformy Azure przy użyciu zarządzanych tożsamości w usłudze Azure Logic Apps

Aby uzyskać dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (dawniej tożsamości usługi zarządzanej lub MSI), a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć poświadczenia, ponieważ nie trzeba podawać ani obracać wpisów tajnych.

Usługa Azure Logic Apps obsługuje zarówno tożsamości zarządzane [*przypisane do systemu,*](../active-directory/managed-identities-azure-resources/overview.md) jak i [*przypisane przez użytkownika.*](../active-directory/managed-identities-azure-resources/overview.md) Aplikacja logiki można użyć tożsamości przypisanej do systemu lub *pojedynczej* tożsamości przypisanej przez użytkownika, które można udostępnić w grupie aplikacji logiki, ale nie oba. Obecnie tylko [określone wbudowane wyzwalacze i akcje](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) obsługują zarządzane tożsamości, nie zarządzane łączniki lub połączenia, na przykład:

* HTTP
* Azure Functions
* Usługa Azure API Management
* Azure App Services

W tym artykule pokazano, jak skonfigurować oba rodzaje tożsamości zarządzanych dla aplikacji logiki. Więcej informacji można znaleźć w następujących tematach:

* [Wyzwalacze i akcje obsługujące tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Obsługiwane typy uwierzytelniania w połączeniach wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Zarządzane limity tożsamości dla aplikacji logiki](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Usługi platformy Azure obsługujące uwierzytelnianie usługi Azure AD z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Zarówno tożsamość zarządzana, jak i docelowy zasób platformy Azure, do którego potrzebujesz dostępu, muszą korzystać z tej samej subskrypcji platformy Azure.

* Aby nadać tożsamości zarządzanej dostępu do zasobu platformy Azure, należy dodać rolę do zasobu docelowego dla tej tożsamości. Aby dodać role, potrzebujesz [uprawnień administratora usługi Azure AD,](../active-directory/users-groups-roles/directory-assign-admin-roles.md) który może przypisywać role do tożsamości w odpowiedniej dzierżawie usługi Azure AD.

* Docelowy zasób platformy Azure, do którego chcesz uzyskać dostęp. W tym zasobie dodasz rolę dla tożsamości zarządzanej, co pomaga aplikacji logiki uwierzytelniać dostęp do zasobu docelowego.

* Aplikacja logiki, w której chcesz użyć [wyzwalacza lub akcji obsługujących tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Włączanie tożsamości zarządzanej

Aby skonfigurować tożsamość zarządzana, której chcesz użyć, kliknij łącze dla tej tożsamości:

* [Tożsamość przypisana systemowi](#system-assigned)
* [Tożsamość przypisana przez użytkownika](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Włączanie tożsamości przypisanej do systemu

W przeciwieństwie do tożsamości przypisanych przez użytkownika nie trzeba ręcznie tworzyć tożsamości przypisanej do systemu. Aby skonfigurować tożsamość przypisaną do systemu dla aplikacji logiki, oto opcje, których można użyć:

* [Portal Azure](#azure-portal-system-logic-app)
* [Szablony usługi Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Włączanie tożsamości przypisanej systemowo w witrynie Azure portal

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w logice App Designer.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Tożsamość**. Wybierz **opcję System przypisany** > **przy** > **zapisywaniu**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak**.

   ![Włączanie tożsamości przypisanej do systemu](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd, który może mieć tylko jedną tożsamość zarządzaną, aplikacja logiki jest już skojarzona z tożsamością przypisaną przez użytkownika. Przed dodaniem tożsamości przypisanej do systemu należy najpierw *usunąć* tożsamość przypisaną przez użytkownika z aplikacji logiki.

   Aplikacja logiki może teraz używać tożsamości przypisanej do systemu, która jest zarejestrowana w usłudze Azure Active Directory i jest reprezentowana przez identyfikator obiektu.

   ![Identyfikator obiektu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Identyfikator obiektu** | <*identyfikator tożsamości i zasobu*> | Globalnie unikatowy identyfikator (GUID), który reprezentuje tożsamość przypisaną do systemu dla aplikacji logiki w dzierżawie usługi Azure AD |
   ||||

1. Teraz wykonaj [kroki, które dają tej tożsamości dostęp do zasobu w dalszej](#access-other-resources) części tego tematu.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Włączanie tożsamości przypisanej systemowo w szablonie usługi Azure Resource Manager

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak aplikacje logiki, można użyć [szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Aby włączyć tożsamość zarządzaną przypisaną przez system dla `identity` aplikacji logiki w szablonie, dodaj obiekt i właściwość podrzędną `type` do definicji zasobów aplikacji logiki w szablonie, na przykład:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Gdy platforma Azure tworzy definicję `identity` zasobów aplikacji logiki, obiekt pobiera te dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `principalId` | <*główny identyfikator*> | Globalnie unikatowy identyfikator (GUID) obiektu jednostki usługi dla tożsamości zarządzanej, która reprezentuje aplikację logiki w dzierżawie usługi Azure AD. Ten identyfikator GUID czasami pojawia się `objectID`jako "identyfikator obiektu" lub . |
| `tenantId` | <*Identyfikator-identyfikator azure-AD-tenant*> | Globalnie unikatowy identyfikator (GUID), który reprezentuje dzierżawę usługi Azure AD, w której aplikacja logiki jest teraz członkiem. Wewnątrz dzierżawy usługi Azure AD podmiotu zabezpieczeń usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Włączanie tożsamości przypisanej przez użytkownika

Aby skonfigurować tożsamość zarządzaną przypisaną przez użytkownika dla aplikacji logiki, należy najpierw utworzyć tę tożsamość jako oddzielny autonomiczny zasób platformy Azure. Oto opcje, których można użyć:

* [Portal Azure](#azure-portal-user-identity)
* [Szablony usługi Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Dodaj przypisanie roli](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Interfejs API REST platformy Azure
  * [Tworzenie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Dodaj przypisanie roli](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Tworzenie tożsamości przypisanej przez użytkownika w witrynie Azure portal

1. W [witrynie Azure portal](https://portal.azure.com)w polu wyszukiwania `managed identities`na dowolnej stronie wprowadź i wybierz pozycję **Zarządzane tożsamości**.

   ![Znajdź i wybierz "Zarządzane tożsamości"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. W obszarze **Tożsamości zarządzane**wybierz pozycję **Dodaj**.

   ![Dodawanie nowej tożsamości zarządzanej](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Podaj informacje o tożsamości zarządzanej, a następnie wybierz pozycję **Utwórz**, na przykład:

   ![Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa zasobu** | Tak | <*nazwa-tożsamość przypisana przez użytkownika*> | Nazwa, aby nadać tożsamości przypisanej przez użytkownika. W tym przykładzie użyto "Fabrikam-user-assigned-identity". |
   | **Subskrypcja** | Tak | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure do użycia |
   | **Grupa zasobów** | Tak | <*Nazwa-grupa zasobów platformy Azure*> | Nazwa grupy zasobów do użycia. Utwórz nową grupę lub wybierz istniejącą grupę. W tym przykładzie tworzy nową grupę o nazwie "fabrikam-managed-identities-RG". |
   | **Lokalizacja** | Tak | <*Region platformy Azure*> | Region platformy Azure, w którym można przechowywać informacje o zasobie. W tym przykładzie użyto "Zachodnie stany USA". |
   |||||

   Teraz możesz dodać tożsamość przypisaną przez użytkownika do aplikacji logiki. Nie można dodać więcej niż jednej tożsamości przypisanej przez użytkownika do aplikacji logiki.

1. W witrynie Azure portal znajdź i otwórz aplikację logiki w logic app designer.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Tożsamość**, a następnie wybierz pozycję **Użytkownik przypisany** > **do niego Add**.

   ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. W okienku **Dodaj użytkownikowi przypisane tożsamości zarządzanej** z listy **Subskrypcja** wybierz subskrypcję platformy Azure, jeśli nie jest jeszcze zaznaczona. Z listy zawierającej *wszystkie* tożsamości zarządzane w tej subskrypcji znajdź i wybierz odpowiednią tożsamość przypisaną przez użytkownika. Aby filtrować listę, w polu wyszukiwania **Tożsamości zarządzanej użytkownik** wprowadź nazwę grupy tożsamości lub zasobów. Po zakończeniu wybierz pozycję **Dodaj**.

   ![Wybierz tożsamość przypisaną przez użytkownika do użycia](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd, który może mieć tylko jedną tożsamość zarządzaną, aplikacja logiki jest już skojarzona z tożsamością przypisaną do systemu. Przed dodaniem tożsamości przypisanej przez użytkownika należy najpierw wyłączyć tożsamość przypisaną systemowi w aplikacji logiki.

   Aplikacja logiki jest teraz skojarzona z tożsamością zarządzaną przypisaną przez użytkownika.

   ![Skojarzenie z tożsamością przypisaną przez użytkownika](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Teraz wykonaj [kroki, które dają tej tożsamości dostęp do zasobu w dalszej](#access-other-resources) części tego tematu.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Tworzenie tożsamości przypisanej przez użytkownika w szablonie usługi Azure Resource Manager

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak aplikacje logiki, można użyć [szablonów usługi Azure Resource Manager,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)które obsługują [tożsamości przypisane przez użytkownika do uwierzytelniania.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) W `resources` sekcji szablonu definicja zasobów aplikacji logiki wymaga następujących elementów:

* Obiekt `identity` z `type` właściwością ustawioną na`UserAssigned`

* Obiekt `userAssignedIdentities` podrzędny, który określa identyfikator zasobu tożsamości, który jest `principalId` innym `clientId` obiektem podrzędnym, który ma właściwości i

W tym przykładzie przedstawiono definicję zasobów aplikacji logiki `identity` dla żądania HTTP PUT i zawiera obiekt niesparwowany. Odpowiedź na żądanie PUT i kolejne get `identity` operacji również ten obiekt:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `principalId` | <*główny identyfikator*> | Unikatowy identyfikator (GUID) dla tożsamości zarządzanej przypisanej przez użytkownika w dzierżawie usługi Azure AD |
| `clientId` | <*identyfikator klienta*> | Globalnie unikatowy identyfikator (GUID) dla nowej tożsamości aplikacji logiki, który jest używany do połączeń w czasie wykonywania |
||||

Jeśli szablon zawiera również definicję zasobów tożsamości zarządzanej, `identity` można sparametryzować obiekt. W tym przykładzie `userAssignedIdentities` pokazano, `userAssignedIdentity` jak obiekt podrzędny odwołuje `variables` się do zmiennej zdefiniowanej w sekcji szablonu. Ta zmienna odwołuje się do identyfikatora zasobu dla tożsamości przypisanej przez użytkownika.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `tenantId` | <*Identyfikator-identyfikator azure-AD-tenant*> | Globalnie unikatowy identyfikator (GUID), który reprezentuje dzierżawę usługi Azure AD, w której tożsamość przypisana przez użytkownika jest teraz członkiem. Wewnątrz dzierżawy usługi Azure AD podmiot zabezpieczeń usługi ma taką samą nazwę jak nazwa tożsamości przypisana przez użytkownika. |
| `principalId` | <*główny identyfikator*> | Unikatowy identyfikator (GUID) dla tożsamości zarządzanej przypisanej przez użytkownika w dzierżawie usługi Azure AD |
| `clientId` | <*identyfikator klienta*> | Globalnie unikatowy identyfikator (GUID) dla nowej tożsamości aplikacji logiki, który jest używany do połączeń w czasie wykonywania |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Udzielanie tożsamości dostępu do zasobów

Zanim będzie można użyć tożsamości zarządzanej aplikacji logiki do uwierzytelniania, skonfiguruj dostęp dla tej tożsamości w zasobie platformy Azure, w którym zamierzasz użyć tożsamości. Aby wykonać to zadanie, przypisz odpowiednią rolę do tej tożsamości w docelowym zasobie platformy Azure. Oto opcje, których można użyć:

* [Portal Azure](#azure-portal-assign-access)
* [Szablon usługi Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) — aby uzyskać więcej informacji, zobacz [Dodawanie przypisania roli przy użyciu usługi Azure RBAC i Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Interfejsu wiersza polecenia platformy Azure[(tworzenie przydziału ról az)](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)— aby uzyskać więcej informacji, zobacz [Dodawanie przypisania roli przy użyciu narzędzia Azure RBAC i interfejsu wiersza polecenia platformy Azure.](../role-based-access-control/role-assignments-cli.md)
* [Interfejs API REST platformy Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Przypisywanie dostępu w witrynie Azure portal

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do zasobu platformy Azure, do którego chcesz mieć dostęp do twojej tożsamości zarządzanej.

1. Z menu zasobu wybierz**przydziały ról** **kontroli dostępu (IAM),** > w których można przeglądać bieżące przypisania ról dla tego zasobu. Na pasku narzędzi wybierz pozycję **Dodaj** > **przypisanie roli**.

   ![Wybierz "Dodaj" > "Dodaj przypisanie roli"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Jeśli opcja **Dodaj przypisanie roli** jest wyłączona, najprawdopodobniej nie masz uprawnień. Aby uzyskać więcej informacji na temat uprawnień, które umożliwiają zarządzanie rolami zasobów, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. W obszarze **Dodaj przypisanie roli**wybierz **rolę,** która daje twojej tożsamości niezbędny dostęp do zasobu docelowego.

   W tym temacie przykład tożsamości potrzebuje [roli, która może uzyskać dostęp do obiektu blob w kontenerze usługi Azure Storage.](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![Wybierz rolę "Współautor danych obiektów blob magazynu"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Wykonaj następujące kroki dotyczące tożsamości zarządzanej:

   * **Tożsamość przypisana systemowi**

     1. W polu **Przypisz dostęp do** wybierz pozycję **Aplikacja logiki**. Gdy pojawi się **właściwość subskrypcja,** wybierz subskrypcję platformy Azure, która jest skojarzona z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/assign-access-system.png)

     1. W polu **Wybierz** wybierz aplikację logiki z listy. Jeśli lista jest zbyt długa, użyj pola **Zaznacz,** aby odfiltrować listę.

        ![Wybieranie aplikacji logiki dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Tożsamość przypisana przez użytkownika**

     1. W polu **Przypisz dostęp do** wybierz pozycję **Użytkownik przypisany tożsamości zarządzanej**. Gdy pojawi się **właściwość subskrypcja,** wybierz subskrypcję platformy Azure, która jest skojarzona z Twoją tożsamością.

        ![Wybieranie dostępu dla tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/assign-access-user.png)

     1. W polu **Wybierz** wybierz swoją tożsamość z listy. Jeśli lista jest zbyt długa, użyj pola **Zaznacz,** aby odfiltrować listę.

        ![Wybieranie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

   Lista przypisań ról zasobu docelowego pokazuje teraz wybraną tożsamość i rolę zarządzana. W tym przykładzie pokazano, jak można użyć tożsamości przypisanej do systemu dla jednej aplikacji logiki i tożsamości przypisanej przez użytkownika dla grupy innych aplikacji logiki.

   ![Dodano zarządzane tożsamości i role do zasobów docelowych](./media/create-managed-service-identity/added-roles-for-identities.png)

   Aby uzyskać więcej informacji, [przypisz dostęp do tożsamości zarządzanej do zasobu przy użyciu witryny Azure portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Teraz wykonaj [kroki, aby uwierzytelnić dostęp z tożsamością](#authenticate-access-with-identity) w wyzwalaczu lub akcji, która obsługuje tożsamości zarządzane.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Uwierzytelnij dostęp za pomocą tożsamości zarządzanej

Po [włączeniu tożsamości zarządzanej dla aplikacji logiki](#azure-portal-system-logic-app) i [przyznaniu tej tożsamości dostępu do zasobu docelowego lub encji](#access-other-resources)można użyć tej tożsamości w [wyzwalaczach i akcjach obsługujących tożsamości zarządzane](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Jeśli masz funkcję platformy Azure, w której chcesz użyć tożsamości przypisanej do systemu, najpierw [włącz uwierzytelnianie dla funkcji platformy Azure.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

Te kroki pokazują, jak używać tożsamości zarządzanej za pomocą wyzwalacza lub akcji za pośrednictwem witryny Azure portal. Aby określić tożsamość zarządzaną w wyzwalaczu lub podstawowej definicji JSON akcji, zobacz [Uwierzytelnianie tożsamości zarządzanej](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. Jeśli jeszcze tego nie zrobiono, dodaj [wyzwalacz lub akcję obsługującą tożsamości zarządzane](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Na przykład wyzwalacz lub akcja HTTP można użyć tożsamości przypisanej przez system, która została włączona dla aplikacji logiki. Ogólnie rzecz biorąc wyzwalacz lub akcja HTTP używa tych właściwości, aby określić zasób lub encję, do której chcesz uzyskać dostęp:

   | Właściwość | Wymagany | Opis |
   |----------|----------|-------------|
   | **Metoda** | Tak | Metoda HTTP używana przez operację, którą chcesz uruchomić |
   | **Identyfikator URI** | Tak | Adres URL punktu końcowego dostępu do docelowego zasobu lub jednostki platformy Azure. Składnia identyfikatora URI zwykle zawiera [identyfikator zasobu](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub usługi platformy Azure. |
   | **Nagłówki** | Nie | Wszystkie wartości nagłówka, które są potrzebne lub które chcesz uwzględnić w żądaniu wychodzącym, takie jak typ zawartości |
   | **Zapytania** | Nie | Wszelkie parametry kwerendy, które są potrzebne lub które chcesz uwzględnić w żądaniu, takie jak parametr dla określonej operacji lub wersja interfejsu API dla operacji, którą chcesz uruchomić |
   | **Uwierzytelnianie** | Tak | Typ uwierzytelniania używany do uwierzytelniania dostępu do zasobu docelowego lub encji |
   ||||

   W określonym przykładzie załóżmy, że chcesz uruchomić [operację obiektu blob migawki](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) na obiekcie blob na koncie usługi Azure Storage, w którym wcześniej skonfigurowałeś dostęp dla swojej tożsamości. Jednak [łącznik usługi Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/) obecnie nie oferuje tej operacji. Zamiast tego można uruchomić tę operację przy użyciu [akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) lub innej operacji interfejsu API rest usługi [obiektów blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Aby uzyskać dostęp do kont magazynu platformy Azure za zaporami przy użyciu żądań HTTP i tożsamości zarządzanych, upewnij się, że skonfigurowano również konto magazynu z [wyjątkiem, który umożliwia dostęp przez zaufane usługi firmy Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Aby uruchomić [operację obiektu Blob migawki,](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)akcja HTTP określa następujące właściwości:

   | Właściwość | Wymagany | Przykładowa wartość | Opis |
   |----------|----------|---------------|-------------|
   | **Metoda** | Tak | `PUT`| Metoda HTTP używana przez operację obiektu Blob migawka |
   | **Identyfikator URI** | Tak | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Identyfikator zasobu pliku usługi Azure Blob Storage w środowisku globalnym (publicznym) platformy Azure, który używa tej składni |
   | **Nagłówki** | Tak, dla usługi Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Wartości `x-ms-blob-type` `x-ms-version` nagłówka i nagłówka, które są wymagane dla operacji usługi Azure Storage. <p><p>**Ważne:** W wychodzących wyzwalaczy HTTP i żądania `x-ms-version` akcji dla usługi Azure Storage nagłówek wymaga właściwości i wersji interfejsu API dla operacji, które chcesz uruchomić. <p>Więcej informacji można znaleźć w następujących tematach: <p><p>- [Nagłówki żądań — obiekt blob migawki](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Przechowywanie wersji dla usług Usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Zapytania** | Tak, dla tej operacji | `comp` = `snapshot` | Nazwa i wartość parametru kwerendy dla operacji obiektu blob migawki. |
   |||||

   Oto przykładowa akcja HTTP, która pokazuje wszystkie te wartości właściwości:

   ![Dodawanie akcji HTTP w celu uzyskania dostępu do zasobu platformy Azure](./media/create-managed-service-identity/http-action-example.png)

1. Teraz dodaj **właściwość Uwierzytelnianie** do akcji HTTP. Z listy **Dodaj nowy parametr** wybierz pozycję **Uwierzytelnianie**.

   ![Dodaj właściwość "Uwierzytelnianie" do akcji HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje obsługują umożliwiające dodanie typu uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Z listy **Typ uwierzytelniania** wybierz pozycję **Tożsamość zarządzana**.

   ![W przypadku opcji "Uwierzytelnianie" wybierz "Tożsamość zarządzana"](./media/create-managed-service-identity/select-managed-identity.png)

1. Z listy tożsamości zarządzanej wybierz jedną z dostępnych opcji na podstawie scenariusza.

   * Jeśli skonfigurowana jest tożsamość przypisana do systemu, wybierz opcję **Tożsamość zarządzana przypisana do systemu,** jeśli nie jest jeszcze zaznaczona.

     ![Wybierz "Tożsamość zarządzana przypisana do systemu"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Jeśli skonfigurujesz tożsamość przypisaną przez użytkownika, wybierz tę tożsamość, jeśli nie jest jeszcze zaznaczona.

     ![Wybieranie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   W tym przykładzie kontynuowano dostęp do **tożsamości zarządzanej przypisanej przez system**.

1. W przypadku niektórych wyzwalaczy i akcji pojawi się również **właściwość Odbiorca,** aby ustawić identyfikator zasobu docelowego. Ustaw właściwość **Odbiorca** na [identyfikator zasobu docelowego lub usługi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). W przeciwnym razie domyślnie Audience `https://management.azure.com/` **właściwość** używa identyfikatora zasobu, który jest identyfikator zasobu dla usługi Azure Resource Manager.

   > [!IMPORTANT]
   > Upewnij się, że identyfikator zasobu *docelowego dokładnie odpowiada* wartości oczekiwanej przez usługę Azure Active Directory (AD), w tym wymaganych ukośników końcowych. Na przykład identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga ukośnika końcowego. Sprawdź [identyfikatory zasobów dla usług platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   W tym **Audience** przykładzie `https://storage.azure.com/` ustawia Audience właściwość tak, aby tokeny dostępu używane do uwierzytelniania są prawidłowe dla wszystkich kont magazynu. Można jednak również określić adres `https://fabrikamstorageaccount.blob.core.windows.net`URL usługi głównej dla określonego konta magazynu.

   ![Ustawianie właściwości "Odbiorca" w celu kierowania identyfikatora zasobu](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Aby uzyskać więcej informacji na temat autoryzowania dostępu za pomocą usługi Azure AD dla usługi Azure Storage, zobacz następujące tematy:

   * [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu usługi Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autoryzuj dostęp do usługi Azure Storage za pomocą usługi Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Kontynuuj tworzenie aplikacji logiki w odpowiedni sposób.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Wyłączanie tożsamości zarządzanej

Aby przestać używać tożsamości zarządzanej dla aplikacji logiki, dostępne są następujące opcje:

* [Portal Azure](#azure-portal-disable)
* [Szablony usługi Azure Resource Manager](#template-disable)
* Azure PowerShell
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-powershell.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Interfejs wiersza polecenia platformy Azure
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-cli.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Interfejs API REST platformy Azure
  * [Usuwanie przypisania roli](../role-based-access-control/role-assignments-rest.md)
  * [Usuwanie tożsamości przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Jeśli usuniesz aplikację logiki, platforma Azure automatycznie usunie tożsamość zarządzana z usługi Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Wyłączanie tożsamości zarządzanej w witrynie Azure portal

W witrynie Azure portal najpierw usuń dostęp tożsamości do [zasobu docelowego.](#disable-identity-target-resource) Następnie wyłącz tożsamość przypisaną do systemu lub usuń tożsamość przypisaną przez użytkownika z [aplikacji logiki](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Usuwanie dostępu do tożsamości z zasobów

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do docelowego zasobu platformy Azure, w którym chcesz usunąć dostęp dla tożsamości zarządzanej.

1. Z menu zasobu docelowego wybierz pozycję **Kontrola dostępu (IAM)**. W obszarze paska narzędzi wybierz pozycję **Przypisania ról**.

1. Na liście ról wybierz tożsamości zarządzane, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   > [!TIP]
   > Jeśli opcja **Usuń** jest wyłączona, najprawdopodobniej nie masz uprawnień. Aby uzyskać więcej informacji na temat uprawnień, które umożliwiają zarządzanie rolami zasobów, zobacz [Uprawnienia roli administratora w usłudze Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Tożsamość zarządzana jest teraz usuwana i nie ma już dostępu do zasobu docelowego.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Wyłączanie tożsamości zarządzanej w aplikacji logiki

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w logice App Designer.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **Tożsamość**, a następnie wykonaj kroki dotyczące tożsamości:

   * Wybierz **opcję System przypisany** > **przy** > **zapisywaniu**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak**.

     ![Wyłączanie tożsamości przypisanej przez system](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Wybierz **pozycję Użytkownik przypisany** i tożsamość zarządzana, a następnie wybierz pozycję **Usuń**. Gdy platforma Azure wyświetli monit o potwierdzenie, wybierz pozycję **Tak**.

     ![Usuwanie tożsamości przypisanej przez użytkownika](./media/create-managed-service-identity/remove-user-assigned-identity.png)

Tożsamość zarządzana jest teraz wyłączona w aplikacji logiki.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Wyłączanie tożsamości zarządzanej w szablonie usługi Azure Resource Manager

Jeśli aplikacja logiki jest zarządzana tożsamości przy użyciu szablonu `identity` Usługi `type` Azure Resource `None`Manager, ustaw właściwość podrzędną obiektu na . W przypadku tożsamości zarządzanej przez system ta akcja powoduje również usunięcie identyfikatora głównego z usługi Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Bezpieczny dostęp i dane w usłudze Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)