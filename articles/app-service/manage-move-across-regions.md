---
title: Przenoszenie aplikacji do innego regionu
description: Dowiedz się, jak przenieść zasoby usługi App Service z jednego regionu do drugiego.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925711"
---
# <a name="move-an-app-service-app-to-another-region"></a>Przenoszenie aplikacji usługi App Service do innego regionu

W tym artykule opisano sposób przenoszenia zasobów usługi App Service do innego regionu platformy Azure. Zasoby można przenieść do innego regionu z wielu powodów. Na przykład, aby skorzystać z nowego regionu platformy Azure, wdrożyć funkcje lub usługi dostępne tylko w określonych regionach, aby spełnić wymagania dotyczące zasad wewnętrznych i nadzoru lub w odpowiedzi na wymagania dotyczące planowania pojemności.

Zasoby usługi app service są specyficzne dla regionu i nie można ich przenosić między regionami. Należy utworzyć kopię istniejących zasobów usługi App Service w regionie docelowym, przenieść zawartość do nowej aplikacji. Jeśli aplikacja źródłowa korzysta z domeny niestandardowej, możesz [przeprowadzić migrację do nowej aplikacji w regionie docelowym](manage-custom-dns-migrate-domain.md) po zakończeniu.

Aby ułatwić kopiowanie aplikacji, możesz [sklonować pojedynczą aplikację usługi App Service](app-service-web-app-cloning.md) do planu usługi app service w innym regionie, ale ma ona [ograniczenia,](app-service-web-app-cloning.md#current-restrictions)zwłaszcza, że nie obsługuje aplikacji systemu Linux.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że aplikacja usługi App Service znajduje się w regionie platformy Azure, z którego chcesz przenieść.
- Upewnij się, że region docelowy obsługuje usługę App Service i wszelkie powiązane usługi, których zasoby chcesz przenieść.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Przygotowanie

Zidentyfikuj wszystkie zasoby usługi App Service, których aktualnie używasz. Przykład:

- Aplikacje usługi App Service
- [Plany usługi App Service](overview-hosting-plans.md)
- [Miejsca wdrożenia](deploy-staging-slots.md)
- [Domeny niestandardowe zakupione na platformie Azure](manage-custom-dns-buy-domain.md)
- [Certyfikaty SSL](configure-ssl-certificate.md)
- [Integracja z siecią wirtualną platformy Azure](web-sites-integrate-with-vnet.md)
- [Połączenia hybrydowe](app-service-hybrid-connections.md).
- [Tożsamości zarządzane](overview-managed-identity.md)
- [Ustawienia kopii zapasowej](manage-backup.md)

Niektóre zasoby, takie jak importowane certyfikaty lub połączenia hybrydowe, zawierają integrację z innymi usługami platformy Azure. Aby uzyskać informacje na temat przenoszenia tych zasobów między regionami, zobacz dokumentację odpowiednich usług.

## <a name="move"></a>Move

1. [Utwórz tworzenie kopii zapasowej aplikacji źródłowej](manage-backup.md).
1. [Utwórz aplikację w nowym planie usługi app service w regionie docelowym](app-service-plan-manage.md#create-an-app-service-plan).
2. [Przywracanie kopii zapasowej w aplikacji docelowej](web-sites-restore.md)
2. Jeśli używasz domeny niestandardowej, [powiąż ją z aplikacją docelową](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) `awverify.` i [włącz domenę w aplikacji docelowej](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Skonfiguruj wszystko inne w aplikacji docelowej tak, aby była taka sama jak aplikacja źródłowa i zweryfikuj konfigurację.
4. Gdy domena niestandardowa będzie gotowa wskazać aplikację [docelową, ponownie zamapuj nazwę domeny](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Oczyszczanie zasobów źródłowych

Usuń aplikację źródłową i plan usługi app service. [Plan usługi aplikacji w warstwie niewolna niesie opłatę, nawet jeśli nie jest w nim uruchomiona żadna aplikacja.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Następne kroki

[Klonowanie aplikacji usługi Azure App Service przy użyciu programu PowerShell](app-service-web-app-cloning.md)