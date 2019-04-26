---
title: Uwierzytelnianie za pomocą zarządzanych tożsamości — usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Aby uwierzytelniać się bez logowania, można utworzyć tożsamości zarządzanej (dawniej nazywanych tożsamości usługi zarządzanej lub MSI), dzięki czemu Twoja aplikacja logiki ma dostęp do innych zasobów usługi Azure Active Directory (Azure AD) dzierżawy bez poświadczeń ani kluczy tajnych
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: 8445b67fa049116d93f3710ff108f904ca7ecd77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60428149"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie i dostęp do zasobów z zarządzanych tożsamości w usłudze Azure Logic Apps

Dostęp do zasobów w innych dzierżaw usługi Azure Active Directory (Azure AD) oraz uwierzytelnianie tożsamości bez logowania, Twoja aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (wcześniej znana pod nazwą tożsamości usługi zarządzanej MSI), zamiast poświadczenia lub kluczy tajnych. Platforma Azure zarządza tą tożsamością dla Ciebie i pomaga zabezpieczyć swoje poświadczenia, ponieważ nie trzeba podać lub obracanie kluczy tajnych. W tym artykule przedstawiono sposób konfigurowania i korzystania z tożsamości zarządzanej przypisana przez system dla twojej aplikacji logiki. Aby uzyskać więcej informacji o zarządzanych tożsamości, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Twoja aplikacja logiki za pomocą tożsamości zarządzanych tylko łączników, które obsługują zarządzanych tożsamości. Obecnie tylko łącznik protokołu HTTP obsługuje zarządzanych tożsamości.
>
> Można obecnie maksymalnie 10 przepływów pracy aplikacji logiki przy użyciu przypisany systemowo udało tożsamości w ramach każdej subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcji platformy Azure, lub jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Tożsamości zarządzanej aplikacji logiki, w której chcesz użyć, przypisany systemowo. Jeśli nie masz aplikacji logiki, zobacz [Tworzenie pierwszego przepływu pracy aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Włączanie tożsamości zarządzanej

Przypisana przez system tożsamości zarządzanej nie trzeba ręcznie utworzyć tej tożsamości. Aby skonfigurować przypisany systemowo tożsamości zarządzanej aplikacji logiki, można użyć następujących sposobów: 

* [Azure Portal](#azure-portal) 
* [Szablony usługi Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Aby włączyć przypisany systemowo tożsamości zarządzanej aplikacji logiki w witrynie Azure portal, należy włączyć funkcję **przypisanej w systemie** ustawienie w ustawieniach tożsamości aplikacji logiki.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **tożsamości**. 

1. W obszarze **przypisanej w systemie** > **stan**, wybierz **na**. Następnie wybierz **Zapisz** > **tak**.

   ![Włącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Twoja aplikacja logiki ma teraz przypisany systemowo tożsamości zarządzanej zarejestrowane w usłudze Azure Active Directory:

   ![Identyfikatory GUID dla Identyfikatora obiektu](./media/create-managed-service-identity/object-id.png)

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------| 
   | **Identyfikator obiektu:** | <*identity-resource-ID*> | Unikatowy identyfikator globalny (GUID), reprezentujący przypisany systemu tożsamości zarządzanej dla aplikacji logiki w dzierżawie usługi Azure AD | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Jeśli chcesz zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak logic apps, można użyć [szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Aby utworzyć przypisany systemowo tożsamości zarządzanej aplikacji logiki za pomocą szablonu, należy dodać `"identity"` elementu i `"type"` właściwości definicji przepływu pracy aplikacji logiki w szablonie wdrożenia: 

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

Gdy platforma Azure utworzy aplikację logiki, ta aplikacja logiki definicji przepływu pracy zawiera te dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość | Wartość | Opis | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Unikatowy identyfikator globalny (GUID), reprezentujący aplikację logiki w dzierżawie usługi Azure AD, a czasami jest wyświetlany jako "Identyfikator obiektu" lub `objectID` | 
| **Identyfikator dzierżawy** | <*Azure-AD-tenant-ID*> | Globalnie unikatowy identyfikator (GUID) reprezentujący dzierżawy usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W dzierżawie usługi Azure AD jednostki usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Uzyskaj dostęp do zasobów za pomocą tożsamości zarządzanych

Po utworzeniu przypisany systemowo tożsamości zarządzanej aplikacji logiki możesz [zapewniają dostęp do tej tożsamości do innych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Można następnie użyć tej tożsamości do uwierzytelniania, podobnie jak każdy inny [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Przypisany systemowo tożsamość zarządzaną i zasobów, w którym ma zostać przypisany dostęp musi mieć tej samej subskrypcji platformy Azure.

### <a name="assign-access-to-managed-identity"></a>Przypisywanie dostępu do tożsamości zarządzanej

Aby udzielić dostępu do innego zasobu platformy Azure dla aplikacji logiki przypisany systemowo tożsamości zarządzanej, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do zasobu platformy Azure której chcesz przypisać dostęp dla Twojej tożsamości zarządzanej. 

1. Wybierz z menu zasobu **kontrola dostępu (IAM)** i wybierz polecenie **Dodaj przypisanie roli**. 

   ![Dodaj przypisanie roli](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. W obszarze **Dodaj przypisanie roli**, wybierz opcję **roli** chcesz, aby dla tożsamości. 

1. W **Przypisz dostęp do** wybierz **użytkownika, grupy lub jednostki usługi Azure AD**, jeśli jeszcze nie wybrano.

1. W **wybierz** pola, rozpoczynając od pierwszego znaku w nazwie aplikacji logiki, wprowadź nazwę aplikacji logiki. Gdy pojawi się Twoja aplikacja logiki, wybierz aplikację logiki.

   ![Wybieranie aplikacji logiki za pomocą tożsamości zarządzanych](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Uwierzytelnianie za pomocą tożsamości zarządzanej w aplikacji logiki

Po skonfigurowaniu aplikacji logiki przy użyciu systemu przypisane tożsamości zarządzanej i przypisany dostęp do zasobu, który ma dla tej tożsamości, można teraz używać tej tożsamości do uwierzytelniania. Na przykład można użyć akcji HTTP, więc aplikacja logiki może Wyślij żądanie HTTP, lub zadzwoń do tego zasobu. 

1. W aplikacji logiki, Dodaj **HTTP** akcji.

1. Podaj odpowiednie szczegóły dla tej akcji, takich jak żądania **metoda** i **URI** lokalizacji zasobu ma zostać wywołana.

   Załóżmy, że używasz uwierzytelniania usługi Azure Active Directory (Azure AD) przy użyciu [jedną z tych usług platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   W **URI** wprowadź adres URL punktu końcowego dla tej usługi platformy Azure. 
   Dlatego jeśli używasz usługi Azure Resource Manager, wprowadź wartość w **URI** właściwości:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. W akcji HTTP wybierz **Pokaż opcje zaawansowane**.

1. Z **uwierzytelniania** listy wybierz **tożsamości zarządzanej**. Po wybraniu tego uwierzytelnienia **odbiorców** właściwość pojawia się z wartością domyślną identyfikator zasobu:

   ![Wybierz pozycję "Tożsamości zarządzanej"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > W **odbiorców** właściwości wartość Identyfikatora zasobu musi dokładnie odpowiadać oczekiwaniom usługi Azure AD, wraz ze wszystkimi wymagane końcowych ukośników. 
   > Te wartości Identyfikator zasobu można znaleźć w tym [Tabela zawierająca opis usługi Azure usług, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Na przykład jeśli używasz Identyfikatora zasobu Menedżera zasobów platformy Azure, upewnij się, że identyfikator URI ma znaku ukośnika na końcu.

1. Kontynuuj Kompilowanie aplikacji logiki w żądany sposób.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Usuń tożsamość zarządzaną

Aby wyłączyć przypisany systemowo tożsamości zarządzanej na aplikację logiki, możesz postępuj zgodnie z instrukcjami podobny do sposobu skonfigurowania tożsamości za pośrednictwem witryny Azure portal, szablony wdrażania usługi Azure Resource Manager lub programu Azure PowerShell.

Po usunięciu aplikacji logiki platformy Azure automatycznie usuwa tożsamości przypisanych przez system aplikację logiki z usługi Azure AD.

### <a name="azure-portal"></a>Azure Portal

Aby usunąć przypisany systemowo tożsamości zarządzanej aplikacji logiki w witrynie Azure portal, należy wyłączyć opcję **przypisanej w systemie** ustawienie w ustawieniach tożsamości aplikacji logiki.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **tożsamości**. 

1. W obszarze **przypisanej w systemie** > **stan**, wybierz **poza**. Następnie wybierz **Zapisz** > **tak**.

   ![Wyłącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Szablon wdrożenia

Jeśli utworzono aplikację logiki przypisany systemowo tożsamości zarządzanej przy użyciu szablonu wdrożenia usługi Azure Resource Manager, należy ustawić `"identity"` elementu `"type"` właściwość `"None"`. Ta akcja spowoduje również usunięcie Identyfikatora podmiotu zabezpieczeń z usługi Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).
