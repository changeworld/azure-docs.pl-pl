---
title: Uwierzytelnianie za pomocą tożsamości zarządzanych — Azure Logic Apps
description: Dostęp do zasobów w innych dzierżawach Azure Active Directory bez logowania się przy użyciu poświadczeń lub wpisów tajnych za pomocą tożsamości zarządzanej
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: fdc5340c9affa7137815577af842aa8b43a552a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799647"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Uwierzytelnianie dostępu do zasobów platformy Azure przy użyciu tożsamości zarządzanych w programie Azure Logic Apps

Aby uzyskać dostęp do zasobów w innych dzierżawach usługi Azure Active Directory (Azure AD) i uwierzytelnić swoją tożsamość bez logowania, aplikacja logiki może korzystać z [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) przypisanej przez system (znanej wcześniej jako tożsamość usługi ZARZĄDZANEJ lub MSI) zamiast poświadczenia lub wpisy tajne. Platforma Azure zarządza tą tożsamością i pomaga zabezpieczyć Twoje poświadczenia, ponieważ nie trzeba podawać ani obrócić wpisów tajnych. W tym artykule przedstawiono sposób konfigurowania tożsamości zarządzanej przypisanej przez system i używania jej w aplikacji logiki.

Aby uzyskać więcej informacji, zobacz następujące tematy:

* [Wyzwalacze i akcje obsługujące tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD z tożsamościami zarządzanymi](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Obsługiwane typy uwierzytelniania dla wywołań wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limity zarządzanej tożsamości dla usługi Logic Apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure lub jeśli nie masz subskrypcji, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). Zarówno tożsamość zarządzana, jak i docelowy zasób platformy Azure, do którego chcesz uzyskać dostęp, musisz używać tej samej subskrypcji platformy Azure.

* [Uprawnienia administratora usługi Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) , które mogą przypisywać role do tożsamości zarządzanych w tej samej dzierżawie usługi Azure AD jako zasób docelowy. Aby zapewnić zarządzaną tożsamość dostęp do zasobu platformy Azure, należy dodać rolę dla tej tożsamości w zasobie docelowym.

* Docelowy zasób platformy Azure, do którego chcesz uzyskać dostęp

* Aplikacja logiki korzystająca z [wyzwalaczy i akcji, które obsługują tożsamości zarządzane](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Włącz tożsamość przypisaną przez system

W przeciwieństwie do tożsamości przypisanych przez użytkownika nie trzeba ręcznie tworzyć tożsamości przypisanej do systemu. Aby skonfigurować tożsamość przypisaną przez system do aplikacji logiki, poniżej przedstawiono opcje, których można użyć:

* [Azure Portal](#azure-portal-system-logic-app)
* [Szablony Azure Resource Manager](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Włącz tożsamość przypisaną przez system w Azure Portal

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **tożsamość** > **system przypisany**. W **obszarze stan**wybierz **pozycję** > **Zapisz** > **tak**.

   ![Włączanie tożsamości przypisanej do systemu](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Aplikacja logiki może teraz korzystać z tożsamości przypisanej do systemu, która jest zarejestrowana w Azure Active Directory i jest reprezentowana przez identyfikator obiektu.

   ![Identyfikator obiektu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/object-id.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Identyfikator obiektu** | <*identyfikatorem zasobu*> | Unikatowy identyfikator globalny (GUID) reprezentujący tożsamość przypisaną przez system dla aplikacji logiki w dzierżawie usługi Azure AD |
   ||||

1. Wykonaj [kroki, które zapewniają tożsamości dostęp do zasobu](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Włącz tożsamość przypisaną przez system w Azure Resource Manager szablonie

Aby zautomatyzować tworzenie i wdrażanie zasobów platformy Azure, takich jak Logic Apps, możesz użyć [szablonów Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Aby włączyć do szablonu zarządzaną tożsamość przypisaną przez system dla aplikacji logiki, Dodaj obiekt `identity` i Właściwość podrzędną `type` do definicji zasobu aplikacji logiki w szablonie, na przykład:

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

Gdy platforma Azure utworzy definicję zasobu aplikacji logiki, obiekt `identity` pobiera te dodatkowe właściwości:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Właściwość (JSON) | Wartość | Opis |
|-----------------|-------|-------------|
| `principalId` | *Identyfikator głównej* <> | Unikatowy identyfikator globalny (GUID) obiektu jednostki usługi dla tożsamości zarządzanej, która reprezentuje aplikację logiki w dzierżawie usługi Azure AD. Ten identyfikator GUID czasami pojawia się jako "Identyfikator obiektu" lub `objectID`. |
| `tenantId` | <*Azure-AD-dzierżawca-ID*> | Unikatowy identyfikator globalny (GUID) reprezentujący dzierżawę usługi Azure AD, w której aplikacja logiki jest teraz członkiem. W ramach dzierżawy usługi Azure AD główna nazwa ma taką samą nazwę jak wystąpienie aplikacji logiki. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Przyznaj tożsamości dostęp do zasobów

Po skonfigurowaniu zarządzanej tożsamości dla aplikacji logiki możesz [nadać tej tożsamości dostęp do innych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Tej tożsamości można następnie użyć do uwierzytelnienia.

1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu platformy Azure, do którego Twoja tożsamość zarządza ma mieć dostęp.

1. Z menu zasób wybierz pozycję **Kontrola dostępu (IAM)**  > **przypisania ról** , w których można przejrzeć bieżące przypisania ról dla tego zasobu. Na pasku narzędzi wybierz pozycję **dodaj** > **Dodaj przypisanie roli**.

   ![Wybierz pozycję "Dodaj" > "Dodaj przypisanie roli"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Jeśli opcja **Dodaj przypisanie roli** jest wyłączona, prawdopodobnie nie masz uprawnień. Aby uzyskać więcej informacji o uprawnieniach, które umożliwiają zarządzanie rolami dla zasobów, zobacz [uprawnienia roli administrator w Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. W obszarze **Dodaj przypisanie roli**wybierz **rolę** , która zapewnia tożsamości wymagane do uzyskania dostępu do zasobu docelowego.

   Na przykład w tym temacie tożsamość musi mieć [rolę, która może uzyskiwać dostęp do obiektu BLOB w kontenerze usługi Azure Storage](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Wybierz rolę "Współautor danych obiektu blob magazynu"](./media/create-managed-service-identity/assign-role.png)

1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.

   ![Wybieranie dostępu dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/assign-access-system.png)

1. W polu **Wybierz** Znajdź i wybierz aplikację logiki.

   ![Wybierz aplikację logiki dla tożsamości przypisanej do systemu](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

   Lista przypisań ról zasobu docelowego zawiera teraz wybraną zarządzaną tożsamość i rolę.

   ![Dodano zarządzane tożsamości i role do zasobu docelowego](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Wykonaj kroki w [celu uwierzytelnienia dostępu za pomocą tożsamości](#authenticate-access-with-identity) w wyzwalaczu lub akcji, która obsługuje zarządzane tożsamości.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Uwierzytelnianie dostępu przy użyciu tożsamości zarządzanej

Po [włączeniu zarządzanej tożsamości dla aplikacji logiki](#azure-portal-system-logic-app) i [udzieleniu tej tożsamości dostępu do zasobu docelowego](#access-other-resources)można użyć tej tożsamości w [wyzwalaczach i akcjach, które obsługują tożsamości zarządzane](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Jeśli masz funkcję platformy Azure, w której chcesz użyć tożsamości przypisanej do systemu, najpierw [Włącz uwierzytelnianie dla usługi Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

W tych krokach pokazano, jak używać zarządzanej tożsamości z wyzwalaczem lub akcją za pomocą Azure Portal. Aby określić zarządzaną tożsamość w wyzwalaczu lub podstawowej definicji JSON akcji, zobacz [uwierzytelnianie tożsamości zarządzanej](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Jeśli jeszcze tego nie zrobiono, Dodaj wyzwalacz lub akcję [, która obsługuje zarządzane tożsamości](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Załóżmy na przykład, że chcesz uruchomić [operację tworzenia migawek obiektów](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) BLOB na obiekcie BLOB na koncie usługi Azure Storage, na którym wcześniej skonfigurowano dostęp do Twojej tożsamości, ale [łącznik usługi Azure Blob Storage](/connectors/azureblob/) nie oferuje obecnie tej operacji. Zamiast tego można użyć [akcji http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) do uruchomienia operacji lub innych [operacji interfejsu API REST usługi BLOB Service](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs). W celu uwierzytelnienia akcja HTTP może korzystać z tożsamości przypisanej do systemu, która została włączona dla aplikacji logiki. Akcja HTTP używa również tych właściwości do określenia zasobu, do którego chcesz uzyskać dostęp:

   * Właściwość **URI** określa adres URL punktu końcowego służący do uzyskiwania dostępu do docelowego zasobu platformy Azure. Ta składnia identyfikatora URI zwykle zawiera [Identyfikator zasobu](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) dla usługi lub zasobu platformy Azure.

   * Właściwość **Headers** określa wszelkie wartości nagłówka, które są potrzebne lub które mają zostać uwzględnione w żądaniu, na przykład wersję interfejsu API dla operacji, która ma być uruchamiana w zasobie docelowym.

   * Właściwość **zapytania** określa wszelkie parametry zapytania, które należy uwzględnić w żądaniu, takie jak parametr określonej operacji lub określona wersja interfejsu API, gdy jest to wymagane.

   Aby uruchomić [operację tworzenia migawek obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), Akcja http określa następujące właściwości:

   * **Metoda**: określa `PUT` operacji.

   * Identyfikator **URI**: Określa identyfikator zasobu dla pliku BLOB Storage platformy Azure w środowisku globalnym (publicznym) platformy Azure i używa następującej składni:

     `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}`

   * **Nagłówki**: określa `x-ms-blob-type` jako `BlockBlob` i `x-ms-version` jako `2019-02-02` dla operacji migawki obiektu BLOB. Aby uzyskać więcej informacji, zobacz temat [nagłówki żądań — obiekt BLOB migawek](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) i [przechowywanie wersji dla usług Azure Storage](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services).

   * **Zapytania**: określa `comp` jako nazwę parametru zapytania i `snapshot` jako wartość parametru.

   Oto przykładowa akcja HTTP, która wyświetla wszystkie te wartości właściwości:

   ![Dodawanie akcji HTTP w celu uzyskania dostępu do zasobu platformy Azure](./media/create-managed-service-identity/http-action-example.png)

   Aby uzyskać więcej informacji na temat wszystkich dostępnych operacji interfejsu API REST platformy Azure, zobacz [Dokumentacja interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/).

1. Z listy **uwierzytelnianie** wybierz pozycję **zarządzana tożsamość**. Jeśli właściwość [ **uwierzytelnianie** jest obsługiwana](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , ale ukryta, Otwórz listę **Dodaj nowy parametr** , a następnie wybierz pozycję **uwierzytelnianie**.

   > [!NOTE]
   > Nie wszystkie wyzwalacze i akcje pozwalają wybrać typ uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![We właściwości "Authentication" Wybierz pozycję "tożsamość zarządzana"](./media/create-managed-service-identity/select-managed-identity.png)

1. Po wybraniu opcji **tożsamość zarządzana**zostanie wyświetlona Właściwość **odbiorców** dla niektórych wyzwalaczy i akcji. Jeśli właściwość **odbiorców** jest obsługiwana, ale ukryta, Otwórz listę **Dodaj nowy parametr** i wybierz pozycję **odbiorcy**.

1. Upewnij się, że wartość **odbiorcy** jest ustawiona na identyfikator zasobu dla zasobu lub usługi docelowej. W przeciwnym razie domyślnie Właściwość **odbiorców** używa identyfikatora zasobu `https://management.azure.com/`, który jest identyfikatorem zasobu dla Azure Resource Manager.

   > [!IMPORTANT]
   > Upewnij się, że identyfikator zasobu docelowego *dokładnie pasuje* do wartości, która oczekuje Azure Active Directory (AD), w tym wszystkich wymaganych końcowych ukośników. Na przykład identyfikator zasobu dla wszystkich kont usługi Azure Blob Storage wymaga końcowego ukośnika. Jednak identyfikator zasobu dla określonego konta magazynu nie wymaga końcowej kreski ułamkowej. Sprawdź [identyfikatory zasobów usług platformy Azure, które obsługują usługę Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Ten przykład ustawia właściwość **odbiorców** na `https://storage.azure.com/` tak, aby tokeny dostępu używane do uwierzytelniania były prawidłowe dla wszystkich kont magazynu. Można jednak również określić adres URL usługi głównej, `https://fabrikamstorageaccount.blob.core.windows.net`dla określonego konta magazynu.

   ![Określ identyfikator zasobu docelowego we właściwości "odbiorców"](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Aby uzyskać więcej informacji na temat autoryzowania dostępu za pomocą usługi Azure AD dla usługi Azure Storage, zobacz następujące tematy:

   * [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autoryzuj dostęp do usługi Azure Storage za pomocą Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Usuwanie tożsamości przypisanej do systemu

Aby zatrzymać korzystanie z tożsamości przypisanej do systemu dla aplikacji logiki, możesz skorzystać z następujących opcji:

* [Azure Portal](#azure-portal-disable)
* [Szablony Azure Resource Manager](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Jeśli usuniesz aplikację logiki, platforma Azure automatycznie usunie zarządzaną tożsamość z usługi Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Usuń tożsamość przypisaną przez system w Azure Portal

W Azure Portal Usuń tożsamość przypisaną przez system [z aplikacji logiki](#disable-identity-logic-app) i dostęp do tej tożsamości [z zasobu docelowego](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Usuwanie tożsamości przypisanej do systemu z aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W menu aplikacji logiki w obszarze **Ustawienia**wybierz pozycję **tożsamość** > **system przypisany**. W obszarze **stan**wybierz pozycję **wyłącz** > **Zapisz** > **tak**.

   ![Zatrzymywanie przy użyciu tożsamości przypisanej do systemu](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Usuń dostęp do tożsamości z zasobów

1. W [Azure Portal](https://portal.azure.com)przejdź do docelowego zasobu platformy Azure, w którym chcesz usunąć dostęp do tożsamości zarządzanej.

1. Z menu zasobu docelowego wybierz pozycję **Kontrola dostępu (IAM)** . Na pasku narzędzi wybierz pozycję **przypisania ról**.

1. Na liście Role wybierz zarządzane tożsamości, które chcesz usunąć. Na pasku narzędzi wybierz pozycję **Usuń**.

   > [!TIP]
   > Jeśli opcja **Usuń** jest wyłączona, prawdopodobnie nie masz uprawnień. Aby uzyskać więcej informacji o uprawnieniach, które umożliwiają zarządzanie rolami dla zasobów, zobacz [uprawnienia roli administrator w Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Zarządzana tożsamość została teraz usunięta i nie ma już dostępu do zasobu docelowego.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Wyłącz tożsamość zarządzaną w szablonie Azure Resource Manager

Jeśli została włączona tożsamość zarządzana przez system aplikacji logiki przy użyciu szablonu Azure Resource Manager, ustaw właściwość podrzędną `type` obiektu `identity` na `None`. Ta akcja powoduje także usunięcie identyfikatora podmiotu zabezpieczeń dla tożsamości zarządzanej przez system z usługi Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie dostępu i danych w Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
