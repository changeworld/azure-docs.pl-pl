---
title: Korzystanie z kontroli dostępu opartej na rolach dla StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób korzystania z kontroli dostępu opartej na rolach platformy Azure (RBAC) w kontekście StorSimple.
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
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159123"
---
# <a name="role-based-access-control-for-storsimple"></a>Kontrola dostępu oparta na rolach dla StorSimple

Ten artykuł zawiera krótki opis sposobu, w jaki azure oparte na kontroli dostępu (RBAC) może służyć do urządzenia StorSimple. RBAC oferuje szczegółowe zarządzanie dostępem dla platformy Azure. Użyj RBAC, aby udzielić odpowiedniej ilości dostępu do użytkowników StorSimple do wykonywania swoich zadań zamiast dawać wszystkim nieograniczony dostęp. Aby uzyskać więcej informacji na temat podstaw zarządzania dostępem na platformie Azure, zobacz [Wprowadzenie do kontroli dostępu opartej na rolach w witrynie Azure portal.](../role-based-access-control/overview.md)

Ten artykuł dotyczy urządzeń z serii StorSimple 8000 z aktualizacją 3.0 lub nowszą w witrynie Azure portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>Role RBAC dla StorSimple

RBAC można przypisać na podstawie ról. Role zapewniają pewne poziomy uprawnień na podstawie dostępnych zasobów w środowisku. Istnieją dwa typy ról, które użytkownicy StorSimple mogą wybierać: wbudowany lub niestandardowy.

* **Wbudowane role** — wbudowane role mogą być administratorem dostępu właściciela, współautora, czytelnika lub użytkownika. Aby uzyskać więcej informacji, zobacz [Wbudowane role dla kontroli dostępu opartej na rolach platformy Azure.](../role-based-access-control/built-in-roles.md)

* **Role niestandardowe** — jeśli wbudowane role nie odpowiadają Twoim potrzebom, można utworzyć niestandardowe role RBAC dla StorSimple. Aby utworzyć niestandardową rolę RBAC, należy rozpocząć od wbudowanej roli, edytować ją, a następnie zaimportować z powrotem do środowiska. Pobieranie i przekazywanie roli są zarządzane przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach](../role-based-access-control/custom-roles.md).

Aby wyświetlić różne role dostępne dla użytkownika urządzenia StorSimple w witrynie Azure portal, przejdź do usługi Menedżer urządzeń StorSimple, a następnie przejdź do **kontroli dostępu (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Tworzenie roli niestandardowej dla administratora infrastruktury StorSimple

W poniższym przykładzie rozpoczynamy od wbudowanej roli **Reader,** która umożliwia użytkownikom wyświetlanie wszystkich zakresów zasobów, ale nie do ich edytowania lub tworzenia nowych. Następnie rozszerzamy tę rolę, aby utworzyć nową rolę niestandardową StorSimple Infrastructure admin. Ta rola jest przypisana do użytkowników, którzy mogą zarządzać infrastrukturą dla urządzeń StorSimple.

1. Uruchom program Windows PowerShell jako administrator.

2. Loguje się do platformy Azure.

    `Connect-AzAccount`

3. Wyeksportuj rolę czytnika jako szablon JSON na komputerze.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otwórz plik JSON w programie Visual Studio. Widzisz, że typowa rola RBAC składa się z trzech głównych sekcji, **Akcje**, **NotActions**i **AssignableScopes**.

    W **akcji** sekcji wszystkie dozwolone operacje dla tej roli są wymienione. Każda akcja jest przypisywana od dostawcy zasobów. W przypadku administratora infrastruktury StorSimple użyj dostawcy `Microsoft.StorSimple` zasobów.

    Użyj programu PowerShell, aby wyświetlić wszystkich dostawców zasobów dostępnych i zarejestrowanych w subskrypcji.

    `Get-AzResourceProvider`

    Można również sprawdzić, czy wszystkie dostępne polecenia cmdlet programu PowerShell do zarządzania dostawcami zasobów.

    W **sekcjach NotActions** zostaną wyświetlone wszystkie akcje z ograniczeniami dla określonej roli RBAC. W tym przykładzie żadne akcje nie są ograniczone.
    
    W obszarze **AssignableScopes**zostaną wyświetlone identyfikatory subskrypcji. Upewnij się, że rola RBAC zawiera jawny identyfikator subskrypcji, w którym jest używany. Jeśli nie określono prawidłowego identyfikatora subskrypcji, nie można zaimportować roli w ramach subskrypcji.

    Edytuj plik, mając na uwadze powyższe uwagi.

    ```json
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

6. Zaimportuj niestandardową rolę RBAC z powrotem do środowiska.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ta rola powinna teraz pojawić się na liście ról w bloku **kontroli dostępu.**

![Wyświetlanie ról RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Aby uzyskać więcej informacji, przejdź do [niestandardowych ról](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Przykładowe dane wyjściowe do tworzenia ról niestandardowych za pośrednictwem programu PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
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
```

## <a name="add-users-to-the-custom-role"></a>Dodawanie użytkowników do roli niestandardowej

Dostęp udzielany jest w ramach zasobu, grupy zasobów lub subskrypcji, która jest zakresem przypisania roli. Podczas udzielania dostępu należy pamiętać, że dostęp przyznany w węźle nadrzędnym jest dziedziczony przez element podrzędny. Aby uzyskać więcej informacji, przejdź do [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

1. Przejdź do **kontroli dostępu (IAM)**. Kliknij **przycisk + Dodaj** na bloku kontroli dostępu.

    ![Dodawanie dostępu do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Wybierz rolę, którą chcesz przypisać, w tym przypadku jest to **Administrator infrastruktury StorSimple**.

3. Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

4. Wybierz **pozycję Zapisz,** aby utworzyć przypisanie.

    ![Dodawanie uprawnień do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Powiadomienie **o dodaniu użytkownika** śledzi postęp. Po pomyślnym dodaniu użytkownika lista użytkowników w formancie dostępu jest aktualizowana.

## <a name="view-permissions-for-the-custom-role"></a>Wyświetlanie uprawnień dla roli niestandardowej

Po utworzeniu tej roli można wyświetlić uprawnienia skojarzone z tą rolą w witrynie Azure portal.

1. Aby wyświetlić uprawnienia skojarzone z tą rolą, przejdź do sekcji **Kontrola dostępu (IAM) > Role > Administrator infrastruktury StorSimple**. Zostanie wyświetlona lista użytkowników w tej roli.

2. Wybierz użytkownika administratora infrastruktury StorSimple i kliknij przycisk **Uprawnienia**.

    ![Wyświetlanie uprawnień dla roli Administratora infrastruktury StorSimple Infra](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Zostaną wyświetlone uprawnienia skojarzone z tą rolą.

    ![Wyświetlanie użytkowników w roli Administratora infrastruktury StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przypisać role niestandardowe użytkownikom wewnętrznym i zewnętrznym](../role-based-access-control/role-assignments-external-users.md).
