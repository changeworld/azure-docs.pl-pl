---
title: Dostęp i Uwierzytelnij się bez logowania — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Utwórz tożsamość zarządzana, dzięki czemu Twoja aplikacja logiki może uwierzytelnianie i dostęp do zasobów w innych dzierżaw usługi Azure Active Directory (Azure AD) bez poświadczeń
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973970"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Dostęp do zasobów i Uwierzytelnij się jako zarządzanych tożsamości w usłudze Azure Logic Apps

Aby uzyskać dostęp do zasobów w innych dzierżaw usługi Azure Active Directory (Azure AD) i uwierzytelnianie tożsamości bez logowania, należy utworzyć [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) , aplikacja logiki używa zamiast swoje poświadczenia. Platforma Azure zarządza tą tożsamością dla Ciebie i pomaga zabezpieczyć swoje poświadczenia, ponieważ nie trzeba podać lub obracanie kluczy tajnych. W tym artykule przedstawiono sposób tworzenia i używania tożsamości zarządzanej aplikacji logiki. Aby uzyskać więcej informacji, zobacz [Zarządzanie tożsamościami dla zasobów platformy Azure](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Zarządzane tożsamości dla zasobów platformy Azure to nazwa zastąpienia dla usługi, znana wcześniej jako tożsamość usługi zarządzanej (MSI).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcji platformy Azure, lub jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

* Aplikacja logiki gdzie chcesz użyć tożsamość zarządzaną. Jeśli nie masz aplikacji logiki, zobacz [Tworzenie pierwszego przepływu pracy aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Utwórz tożsamość zarządzaną

Można utworzyć lub Włącz tożsamości zarządzanej aplikacji logiki za pomocą witryny Azure portal, szablonów usługi Azure Resource Manager lub programu Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal

Aby utworzyć tożsamość zarządzaną w aplikacji logiki w witrynie Azure portal, należy włączyć funkcję **rejestrowanie w usłudze Azure Active Directory** ustawienie w ustawieniach przepływu pracy aplikacji logiki.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Wykonaj następujące kroki: 

   1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**. 

   1. W obszarze **tożsamości usługi zarządzanej** > 
    **rejestrowanie w usłudze Azure Active Directory**, wybierz **na**.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** na pasku narzędzi.

      ![Włącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure zawiera obecnie tych właściwości i wartości tożsamość zarządzaną aplikację logiki:

      ![Identyfikatory GUID dla Identyfikatora podmiotu zabezpieczeń i identyfikator dzierżawy](./media/create-managed-service-identity/principal-tenant-id.png)

      | Właściwość | Wartość | Opis | 
      |----------|-------|-------------| 
      | **Identyfikator podmiotu zabezpieczeń** | <*Jednostka Identyfikatora GUID*> | Unikatowy identyfikator globalny (GUID), reprezentujący aplikację logiki w dzierżawie usługi Azure AD | 
      | **Identyfikator dzierżawy** | <*-Dzierżawy usługi Azure AD — identyfikator GUID*> | Globalnie unikatowy identyfikator (GUID) reprezentujący dzierżawy usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W dzierżawie usługi Azure AD jednostki usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. | 
      ||| 

### <a name="deployment-template"></a>Szablon wdrożenia

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak logic apps, należy skonfigurować szablony usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [tworzenie i wdrażanie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

Aby utworzyć tożsamość zarządzaną w aplikacji logiki za pomocą szablonu, należy dodać **tożsamości** elementu i **typu** właściwości definicji przepływu pracy aplikacji logiki do wdrożenia szablonu. Te ustawienia określają, że platforma Azure utworzy i zarządza tej tożsamości dla twojej aplikacji logiki:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Na przykład aplikacja logiki może wyglądać tej wersji:

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Właściwość | Wartość | Opis | 
|----------|-------|-------------|
| **Identyfikator principalId** | <*Jednostka Identyfikatora GUID*> | Unikatowy identyfikator globalny (GUID), reprezentujący aplikację logiki w dzierżawie usługi Azure AD | 
| **Identyfikator dzierżawy** | <*-Dzierżawy usługi Azure AD — identyfikator GUID*> | Globalnie unikatowy identyfikator (GUID) reprezentujący dzierżawy usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W dzierżawie usługi Azure AD jednostki usługi ma taką samą nazwę jak wystąpienie aplikacji logiki. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Uzyskaj dostęp do zasobów za pomocą tożsamości zarządzanych

Po utworzeniu tożsamości zarządzanej aplikacji logiki możesz [udzielić tej tożsamości dostępu do innych zasobów](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Można następnie użyć tej tożsamości zarządzanej do uwierzytelniania, podobnie jak każdy inny [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md). 

Na przykład załóżmy, że zostały już skonfigurowane za pomocą tożsamości zarządzanej, które ma dostęp do innego zasobu aplikacji logiki. Możesz teraz dodać akcji HTTP, aby wysyłać żądania HTTP lub wywołanie tego zasobu aplikacji logiki. 

1. W aplikacji logiki, Dodaj **HTTP** akcji. 

1. Podaj odpowiednie szczegóły dla tej akcji, takich jak żądania **metoda** i **URI** lokalizacji zasobu ma zostać wywołana.

1. W akcji HTTP wybierz **Pokaż opcje zaawansowane**. 

1. Z **uwierzytelniania** listy wybierz **tożsamości usługi zarządzanej**, który następnie pokazuje **odbiorców** właściwość można ustawić:

   ![Wybierz pozycję "Tożsamość usługi zarządzanej"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Kontynuuj Kompilowanie aplikacji logiki w żądany sposób.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Usuń tożsamość zarządzaną

Aby wyłączyć tożsamość zarządzaną w aplikacji logiki, możesz postępuj zgodnie z instrukcjami podobne do tworzenia tożsamości za pośrednictwem witryny Azure portal, szablony wdrażania usługi Azure Resource Manager lub programu Azure PowerShell. 

Po usunięciu aplikacji logiki platformy Azure automatycznie usuwa tożsamości przypisanych przez system aplikację logiki z usługi Azure AD.

### <a name="azure-portal"></a>Azure Portal

1. W Projektancie aplikacji logiki Otwórz aplikację logiki.

1. Wykonaj następujące kroki: 

   1. W menu aplikacji logiki w ramach **ustawienia**, wybierz opcję **ustawienia przepływu pracy**. 
   
   1. W obszarze **tożsamości usługi zarządzanej**, wybierz **poza** dla **rejestrowanie w usłudze Azure Active Directory** właściwości.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz** na pasku narzędzi.

      ![Wyłącz ustawienie tożsamości zarządzanej](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Szablon wdrożenia

Jeśli utworzono aplikację logiki tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager deployment ustaw `"identity"` elementu `"type"` właściwość `"None"`. Ta akcja spowoduje również usunięcie Identyfikatora podmiotu zabezpieczeń z usługi Azure AD. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).
