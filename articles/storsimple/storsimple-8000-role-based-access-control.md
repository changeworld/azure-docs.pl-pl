---
title: Za pomocą kontroli dostępu opartej na rolach dla urządzenia StorSimple | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania kontroli dostępu opartej na rolach platformy Azure (RBAC) w kontekście StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 5d68df424fbedbb9f0d17b87ceb243b079b3da14
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="role-based-access-control-for-storsimple"></a>Kontrola dostępu oparta na rolach dla urządzenia StorSimple

Ten artykuł zawiera krótki opis sposobu użycia based kontroli dostępu (RBAC) dla urządzenia StorSimple. RBAC oferuje precyzyjne zarządzanie dostępem dla platformy Azure. Użycie funkcji RBAC przyznanie dokładnie takiej ilości dostępu do wykonywania zadań zamiast umożliwienie wszystkim użytkownikom StorSimple nieograniczony dostęp. Aby uzyskać więcej informacji na podstawowe informacje dotyczące zarządzania dostępem na platformie Azure, zobacz [wprowadzenie opartej na rolach kontroli dostępu w portalu Azure](../role-based-access-control/overview.md).

Ten artykuł dotyczy urządzeń z serii StorSimple 8000 systemem 3.0 aktualizacji lub później w portalu Azure.

## <a name="rbac-roles-for-storsimple"></a>Role RBAC dla urządzenia StorSimple

RBAC można przypisywać na podstawie ról. Upewnij się, role określone poziomy uprawnień na podstawie dostępnych zasobów w środowisku. Istnieją dwa typy ról, które można wybrać użytkowników StorSimple: wbudowanych lub niestandardowych.

* **Wbudowane role** -wbudowane role mogą być właściciela, współautora, czytnika lub administrator dostępu użytkowników. Aby uzyskać więcej informacji, zobacz [wbudowanych ról dla kontroli dostępu opartej na roli Azure](../role-based-access-control/overview.md#built-in-roles).

* **Role niestandardowe** — Jeśli wbudowane role nie własnych potrzeb, można tworzyć niestandardowe role RBAC dla urządzenia StorSimple. Aby utworzyć niestandardową rolę RBAC, rozpoczynać się od wbudowanej roli go edytować i zaimportuj go ponownie w środowisku. Pobieranie i przekazywania roli są zarządzane przy użyciu programu Azure PowerShell lub wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie niestandardowych ról dla kontroli dostępu opartej na rolach](../role-based-access-control/custom-roles.md).

Aby wyświetlić różne role dostępne dla użytkowników urządzenia StorSimple w portalu Azure, przejdź do usługi Menedżer StorSimple urządzenia, a następnie przejdź do **(IAM) kontroli dostępu > ról**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Tworzenie niestandardowej roli zabezpieczeń administratora infrastruktury StorSimple

W poniższym przykładzie Rozpoczniemy z rolą wbudowanych **czytnika** umożliwia użytkownikom można wyświetlać wszystkie zakresy zasobów, ale nie edytować je lub utworzyć nowe. Następnie rozbudowujemy tej roli do utworzenia nowej niestandardowej roli zabezpieczeń administratora infrastruktury StorSimple. Ta rola jest przypisywana do użytkowników, którzy mogą zarządzać infrastruktury dla urządzenia StorSimple.

1. Uruchom program Windows PowerShell jako administrator.

2. Loguje się do platformy Azure.

    `Login-AzureRMAccount`

3. Wyeksportuj rolę czytelnika jako szablon JSON na tym komputerze.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otwórz plik JSON w programie Visual Studio. Zobacz, czy typowej RBAC składa się z trzech głównych sekcji, **akcje**, **NotActions**, i **AssignableScopes**.

    W **akcji** sekcji wszystkie operacje dozwolone dla tej roli są wyświetlane. Każda akcja jest przypisany od dostawcy zasobów. Administrator infrastruktury StorSimple, użyj `Microsoft.StorSimple` dostawcy zasobów.

    Użyj programu PowerShell, aby wyświetlić wszystkich dostawców zasobów dostępnych i zarejestrowanych w Twojej subskrypcji.

    `Get-AzureRMResourceProvider`

    Możesz również sprawdzić dla wszystkich dostępnych poleceń cmdlet programu PowerShell do zarządzania dostawcy zasobów.

    W **NotActions** sekcje wszystkie ograniczeniami akcji dla określonej roli RBAC są wyświetlane. W tym przykładzie nie są ograniczone żadne akcje.
    
    W obszarze **AssignableScopes**, są wyświetlane identyfikatory subskrypcji. Upewnij się, że roli RBAC zawiera identyfikator subskrypcji jawne, gdzie są używane. Nie określono Identyfikatora poprawną subskrypcję, nie można zaimportować roli w ramach subskrypcji.

    Edytuj plik, pamiętając o poprzednim zagadnienia.

    ```
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importowanie niestandardowych roli RBAC wrócić do środowiska.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ta rola powinien zostać wyświetlony na liście ról w **kontrola dostępu** bloku.

![Widok role RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Aby uzyskać więcej informacji, przejdź do [utworzyć niestandardową rolę RBAC przy użyciu programu PowerShell](../role-based-access-control/role-assignments-external-users.md#create-a-custom-rbac-role-to-open-support-requests-using-powershell).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Przykładowe dane wyjściowe do utworzenia niestandardowej roli zabezpieczeń za pomocą programu PowerShell

```
PS C:\WINDOWS\system32> Login-AzureRMAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Dodawanie użytkowników do roli niestandardowej

Dostęp udzielany jest w ramach zasobu, grupy zasobów lub subskrypcji, która jest zakresem przypisania roli. Przy udzielaniu dostępu, pamiętać, że dostęp udzielany na węzeł nadrzędny jest dziedziczona przez dziecko. Aby uzyskać więcej informacji, przejdź do [zasobów dostępu i hierarchii dziedziczenia](../role-based-access-control/overview.md#resource-hierarchy-and-access-inheritance).

1. Przejdź do **(IAM) kontroli dostępu**. Kliknij przycisk **+ Dodaj** w bloku kontroli dostępu.

    ![Dodaj dostęp do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Wybierz rolę, którą chcesz przypisać, w tym przypadku jest **administrator infrastruktury StorSimple**.

3. Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

4. Wybierz **zapisać** Aby utworzyć przypisanie.

    ![Dodaj uprawnienia do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Dodawanie użytkownika** powiadomień śledzi postęp procesu. Po pomyślnym dodaniu użytkownika lista użytkowników w kontroli dostępu jest aktualizowana.

## <a name="view-permissions-for-the-custom-role"></a>Wyświetl uprawnienia dla roli niestandardowej

Po utworzeniu tej roli można wyświetlić uprawnienia skojarzone z tą rolą w portalu Azure.

1. Aby wyświetlić uprawnienia skojarzone z tą rolą, przejdź do **(IAM) kontroli dostępu > ról > administrator infrastruktury StorSimple**. Zostanie wyświetlona lista użytkowników w tej roli.

2. Wybierz użytkownika administratora infrastruktury StorSimple, a następnie kliknij przycisk **uprawnienia**.

    ![Wyświetlanie uprawnień do roli administratora StorSimple Infra](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Uprawnienia skojarzone z tą rolą są wyświetlane.

    ![Wyświetl użytkowników należących do roli administratora StorSimple Infra](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [przypisać role niestandardowe dla użytkowników wewnętrznych i zewnętrznych](../role-based-access-control/role-assignments-external-users.md).

