---
title: Uwierzytelnianie za pomocą tożsamości zarządzanych — Azure Logic Apps
description: Aby uwierzytelnić się bez logowania, możesz utworzyć zarządzaną tożsamość (nazywaną wcześniej tożsamość usługi zarządzanej lub MSI), aby Twoja aplikacja logiki mogła uzyskiwać dostęp do zasobów w innych dzierżawach Azure Active Directory (Azure AD) bez poświadczeń lub wpisów tajnych
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: d6cf19a07829afea924d3d799b1309cfc5f6329f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959965"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie i dostęp do zasobów za pomocą tożsamości zarządzanych w Azure Logic Apps

Aby uzyskać dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (znanej wcześniej jako tożsamość usługi ZARZĄDZANEJ lub MSI), a nie poświadczeń lub wpisów tajnych. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. W tym artykule pokazano, jak można skonfigurować i użyć zarządzanej tożsamości przypisanej do systemu dla aplikacji logiki. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Aplikacja logiki może używać zarządzanych tożsamości tylko z łącznikami obsługującymi zarządzane tożsamości. Obecnie tylko łącznik protokołu HTTP obsługuje tożsamości zarządzane.
>
> Obecnie można korzystać z do 100 przepływów pracy aplikacji logiki z tożsamościami zarządzanymi przez system w każdej subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure lub jeśli nie masz subskrypcji, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki, w której ma zostać użyta tożsamość zarządzana przypisana przez system. Jeśli nie masz aplikacji logiki, zobacz [Tworzenie pierwszego przepływu pracy aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Włącz tożsamość zarządzaną

W przypadku tożsamości zarządzanych przypisanych do systemu nie trzeba ręcznie tworzyć tej tożsamości. Aby skonfigurować tożsamość zarządzaną przez system dla aplikacji logiki, można użyć następujących metod: 

* [Azure Portal](#azure-portal) 
* [Szablony Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć zarządzaną przez system tożsamość dla aplikacji logiki za pomocą Azure Portal, Włącz ustawienie **przypisane do systemu** w ustawieniach tożsamości aplikacji logiki.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **tożsamość**.

1. W obszarze**stan** **przypisane** > do systemu wybierz pozycję **włączone**. Następnie wybierz pozycję **Zapisz** > **tak**.

   ![Włącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Twoja aplikacja logiki ma teraz tożsamość zarządzaną przypisaną przez system w Azure Active Directory:

   ![Identyfikatory GUID dla identyfikatora obiektu](./media/create-managed-service-identity/object-id.png)

   | Właściwość | Value | Opis |
   |----------|-------|-------------|
   | **Identyfikator obiektu** | <*Identyfikator zasobu tożsamości*> | Unikatowy identyfikator globalny (GUID) reprezentujący tożsamość zarządzaną przez system dla aplikacji logiki w dzierżawie usługi Azure AD |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak Logic Apps, możesz użyć [szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Aby utworzyć tożsamość zarządzaną przypisaną przez system dla aplikacji logiki za pomocą szablonu, Dodaj `"identity"` element i `"type"` właściwość do definicji przepływu pracy aplikacji logiki w szablonie wdrożenia: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Na przykład:

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

Podczas tworzenia aplikacji logiki przez platformę Azure definicja przepływu pracy aplikacji logiki obejmuje następujące dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość | Value | Opis |
|----------|-------|-------------|
| **principalId** | <*Identyfikator podmiotu zabezpieczeń*> | Unikatowy identyfikator globalny (GUID) reprezentujący aplikację logiki w dzierżawie usługi Azure AD i czasami pojawia się jako "Identyfikator obiektu" lub`objectID` |
| **tenantId** | <*Azure-AD-dzierżawca-ID*> | Unikatowy identyfikator globalny (GUID) reprezentujący dzierżawę usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W ramach dzierżawy usługi Azure AD główna nazwa ma taką samą nazwę jak wystąpienie aplikacji logiki. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Dostęp do zasobów przy użyciu tożsamości zarządzanej

Po utworzeniu zarządzanej tożsamości przypisanej przez system dla aplikacji logiki możesz [nadać tej tożsamości dostęp do innych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Tej tożsamości można następnie użyć do uwierzytelniania, podobnie jak każda inna jednostka [usługi](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Zarówno tożsamość zarządzana przez system, jak i zasób, do którego chcesz przypisać dostęp, muszą mieć tę samą subskrypcję platformy Azure.

### <a name="assign-access-to-managed-identity"></a>Przypisywanie dostępu do tożsamości zarządzanej

Aby udzielić dostępu do innego zasobu platformy Azure dla tożsamości zarządzanej przypisanej do systemu przez aplikację logiki, wykonaj następujące kroki:

1. W Azure Portal przejdź do zasobu platformy Azure, do którego chcesz przypisać dostęp do tożsamości zarządzanej.

1. Z menu zasób wybierz pozycję **Kontrola dostępu (IAM)** . Na pasku narzędzi wybierz pozycję **Dodaj** > **Dodaj przypisanie roli**.

   ![Dodaj przypisanie roli](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. W obszarze **Dodaj przypisanie roli**wybierz żądaną **rolę** dla tożsamości.

1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**, jeśli nie została jeszcze wybrana.

1. W polu **Wybierz** , rozpoczynając od pierwszego znaku w nazwie aplikacji logiki, wprowadź nazwę aplikacji logiki. Gdy zostanie wyświetlona aplikacja logiki, wybierz aplikację logiki.

   ![Wybierz aplikację logiki z tożsamością zarządzaną](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Uwierzytelnianie przy użyciu tożsamości zarządzanej w aplikacji logiki

Po skonfigurowaniu aplikacji logiki za pomocą zarządzanej tożsamości przypisanej do systemu i przypisaniu dostępu do żądanego zasobu dla tej tożsamości możesz teraz użyć tej tożsamości do uwierzytelniania. Na przykład możesz użyć akcji HTTP, aby aplikacja logiki mogła wysłać żądanie HTTP lub wywołać do tego zasobu. 

1. W aplikacji logiki Dodaj akcję **http** .

1. Podaj niezbędne szczegóły dotyczące tej akcji, takie jak **Metoda** żądania i lokalizacja **URI** dla zasobu, który chcesz wywołać.

   Załóżmy na przykład, że używasz uwierzytelniania usługi Azure Active Directory (Azure AD) z [jedną z tych usług platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). W polu **Identyfikator URI** wprowadź adres URL punktu końcowego dla tej usługi platformy Azure. Tak więc, jeśli używasz Azure Resource Manager, wprowadź tę wartość we właściwości **URI** :

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. W akcji HTTP wybierz pozycję **Pokaż opcje zaawansowane**.

1. Z listy **uwierzytelnianie** wybierz pozycję **zarządzana tożsamość**. Po wybraniu tego uwierzytelniania Właściwość **odbiorców** zostanie wyświetlona z domyślną wartością identyfikatora zasobu:

   ![Wybierz pozycję "tożsamość zarządzana"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > We właściwości **odbiorców** wartość identyfikatora zasobu musi być dokładnie zgodna z oczekiwaniami usługi Azure AD, w tym wszystkich wymaganych końcowych ukośników. 
   > Te wartości identyfikatorów zasobów można znaleźć w tej [tabeli opisującej usługi platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Jeśli na przykład używasz identyfikatora zasobu Azure Resource Manager, upewnij się, że identyfikator URI ma końcowy ukośnik.

1. Kontynuuj tworzenie aplikacji logiki w odpowiedni sposób.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Usuwanie tożsamości zarządzanej

Aby wyłączyć tożsamość zarządzaną przypisaną przez system w aplikacji logiki, można wykonać kroki podobne do konfiguracji tożsamości za pomocą Azure Portal, Azure Resource Manager szablonów wdrożenia lub Azure PowerShell.

Usunięcie aplikacji logiki spowoduje automatyczne usunięcie tożsamości przypisanej do systemu przez aplikację logiki z usługi Azure AD.

### <a name="azure-portal"></a>Azure Portal

Aby usunąć tożsamość zarządzaną przez system dla aplikacji logiki za pomocą Azure Portal, wyłącz ustawienie **przypisany system** w ustawieniach tożsamości aplikacji logiki.

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **tożsamość**.

1. W obszarze**stan** **przypisane** > do systemu wybierz pozycję **wyłączone**. Następnie wybierz pozycję **Zapisz** > **tak**.

   ![Wyłącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Szablon wdrożenia

W przypadku utworzenia tożsamości zarządzanej przypisanej przez system do aplikacji logiki przy użyciu szablonu wdrożenia Azure Resource Manager należy ustawić `"identity"` `"type"` właściwość elementu na `"None"`. Ta akcja spowoduje również usunięcie identyfikatora podmiotu zabezpieczeń z usługi Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie dostępu i danych w Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
