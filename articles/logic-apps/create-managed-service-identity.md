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
ms.date: 10/05/2018
ms.openlocfilehash: 84529e1097678ba7a039ffaeec57a9293c93dafd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229643"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie i dostęp do zasobów z zarządzanych tożsamości w usłudze Azure Logic Apps

Dostęp do zasobów w innych dzierżaw usługi Azure Active Directory (Azure AD) oraz uwierzytelnianie tożsamości bez logowania, Twoja aplikacja logiki może używać [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) (wcześniej znana pod nazwą tożsamości usługi zarządzanej MSI), zamiast poświadczenia lub kluczy tajnych. Platforma Azure zarządza tą tożsamością dla Ciebie i pomaga zabezpieczyć swoje poświadczenia, ponieważ nie trzeba podać lub obracanie kluczy tajnych. W tym artykule przedstawiono sposób tworzenia i korzystania z tożsamości zarządzanej przypisana przez system dla twojej aplikacji logiki. Aby uzyskać więcej informacji o zarządzanych tożsamości, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Można obecnie maksymalnie 10 przepływów pracy aplikacji logiki przy użyciu przypisany systemowo udało tożsamości w ramach każdej subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcji platformy Azure, lub jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Tożsamości zarządzanej aplikacji logiki, w której chcesz użyć, przypisany systemowo. Jeśli nie masz aplikacji logiki, zobacz [Tworzenie pierwszego przepływu pracy aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Utwórz tożsamość zarządzaną

Można utworzyć lub Włącz przypisany systemowo tożsamości zarządzanej aplikacji logiki za pomocą witryny Azure portal, szablonów usługi Azure Resource Manager lub programu Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Aby włączyć przypisany systemowo tożsamości zarządzanej aplikacji logiki w witrynie Azure portal, należy włączyć funkcję **rejestrowanie w usłudze Azure Active Directory** ustawienie w ustawieniach przepływu pracy aplikacji logiki.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Wykonaj następujące kroki: 

   1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**. 

   1. W obszarze **tożsamości usługi zarządzanej** > 
    **rejestrowanie w usłudze Azure Active Directory**, wybierz **na**.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** na pasku narzędzi.

      ![Włącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Twoja aplikacja logiki ma teraz przypisany systemowo tożsamości zarządzanej zarejestrowanych w usłudze Azure Active Directory z tych właściwości i wartości:

      ![Identyfikatory GUID dla Identyfikatora podmiotu zabezpieczeń i identyfikator dzierżawy](./media/create-managed-service-identity/principal-tenant-id.png)

      | Właściwość | Wartość | Opis | 
      |----------|-------|-------------| 
      | **Identyfikator podmiotu zabezpieczeń** | <*Identyfikator podmiotu zabezpieczeń*> | Unikatowy identyfikator globalny (GUID), reprezentujący aplikację logiki w dzierżawie usługi Azure AD | 
      | **Identyfikator dzierżawy** | <*Azure-AD-tenant-ID*> | Globalnie unikatowy identyfikator (GUID) reprezentujący dzierżawy usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W dzierżawie usługi Azure AD jednostki usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. | 
      ||| 

### <a name="deployment-template"></a>Szablon wdrożenia

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
| **Identyfikator principalId** | <*Identyfikator podmiotu zabezpieczeń*> | Unikatowy identyfikator globalny (GUID), reprezentujący aplikację logiki w dzierżawie usługi Azure AD | 
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

1. Wybierz z menu zasobu **kontrola dostępu (IAM)** i wybierz polecenie **Dodaj**. 

   ![Dodawanie uprawnień](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. W obszarze **Dodaj uprawnienia**, wybierz opcję **roli** chcesz, aby dla tożsamości. 

1. W **Przypisz dostęp do** wybierz **użytkownika usługi Azure AD, grupa lub aplikacja**, jeśli jeszcze nie wybrano.

1. W **wybierz** pola, rozpoczynając od pierwszego znaku w nazwie aplikacji logiki, wprowadź nazwę aplikacji logiki. Gdy pojawi się Twoja aplikacja logiki, wybierz aplikację logiki.

   ![Wybieranie aplikacji logiki za pomocą tożsamości zarządzanych](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Uwierzytelnianie za pomocą tożsamości zarządzanej w aplikacji logiki

Po skonfigurowaniu aplikacji logiki przy użyciu systemu przypisane tożsamości zarządzanej i przypisany dostęp do zasobu, który ma dla tej tożsamości, można teraz używać tej tożsamości do uwierzytelniania. Na przykład można użyć akcji HTTP, więc aplikacja logiki może Wyślij żądanie HTTP, lub zadzwoń do tego zasobu. 

1. W aplikacji logiki, Dodaj **HTTP** akcji. 

1. Podaj odpowiednie szczegóły dla tej akcji, takich jak żądania **metoda** i **URI** lokalizacji zasobu ma zostać wywołana.

1. W akcji HTTP wybierz **Pokaż opcje zaawansowane**. 

1. Z **uwierzytelniania** listy wybierz **tożsamości usługi zarządzanej**, który następnie pokazuje **odbiorców** właściwość można ustawić:

   ![Wybierz pozycję "Tożsamość usługi zarządzanej"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Kontynuuj Kompilowanie aplikacji logiki w żądany sposób.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Usuń tożsamość zarządzaną

Aby wyłączyć przypisany systemowo tożsamości zarządzanej na aplikację logiki, możesz postępuj zgodnie z instrukcjami podobne do tworzenia tożsamości za pośrednictwem witryny Azure portal, szablony wdrażania usługi Azure Resource Manager lub programu Azure PowerShell. 

Po usunięciu aplikacji logiki platformy Azure automatycznie usuwa tożsamości przypisanych przez system aplikację logiki z usługi Azure AD.

### <a name="azure-portal"></a>Azure Portal

1. W Projektancie aplikacji logiki Otwórz aplikację logiki.

1. Wykonaj następujące kroki: 

   1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**. 
   
   1. W obszarze **tożsamości usługi zarządzanej**, wybierz **poza** dla **rejestrowanie w usłudze Azure Active Directory** właściwości.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** na pasku narzędzi.

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

