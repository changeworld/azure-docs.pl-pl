---
title: Korzystanie z kontroli dostępu opartej na rolach dla usługi StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób używania kontroli dostępu opartej na rolach platformy Azure (RBAC) w ramach usługi StorSimple.
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
ms.openlocfilehash: be0c1611856a1fa68d20696c32b5fadcd8572004
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793615"
---
# <a name="role-based-access-control-for-storsimple"></a>Kontrola dostępu oparta na rolach dla usługi StorSimple

Ten artykuł zawiera krótki opis sposobu użycia based kontroli dostępu (RBAC) dla urządzenia StorSimple. RBAC oferuje szczegółowe zarządzanie dostępem dla platformy Azure. Przy użyciu kontroli RBAC jest przyznanie tylko będzie odpowiednia ilość dostęp do wykonywania ich zadań, a nie umożliwia wszystkim użytkownikom StorSimple nieograniczony dostęp. Aby uzyskać więcej informacji na temat zarządzania dostępem na platformie Azure, zobacz [wprowadzenie opartej na rolach kontrola dostępu w witrynie Azure portal](../role-based-access-control/overview.md).

Ten artykuł ma zastosowanie do urządzeń z serii StorSimple 8000 systemem Update w wersji 3.0 lub później w witrynie Azure portal.

## <a name="rbac-roles-for-storsimple"></a>Role RBAC dla usługi StorSimple

RBAC można przypisywać na podstawie ról. Role zapewnia pewne poziomy uprawnień na podstawie dostępnych zasobów w środowisku. Istnieją dwa typy ról, które mogą wybierać użytkownicy StorSimple: wbudowanych lub niestandardowych.

* **Wbudowane role** — mogą być wbudowane role, właściciel, współautor, Czytelnik lub administrator dostępu użytkowników. Aby uzyskać więcej informacji, zobacz [wbudowanych ról dla kontroli dostępu opartej na roli Azure](../role-based-access-control/built-in-roles.md).

* **Role niestandardowe** — Jeśli wbudowanych ról nie spełnia Twoich potrzeb, można utworzyć niestandardowe role RBAC dla usługi StorSimple. Aby utworzyć niestandardową rolę RBAC, rozpoczynać rolę wbudowaną, edytuj go, a następnie zaimportuj je z powrotem w środowisku. Pobieranie i przekazywanie roli odbywa się przy użyciu programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [tworzenie ról niestandardowych dla kontroli dostępu opartej na rolach](../role-based-access-control/custom-roles.md).

Aby wyświetlić różne role dostępne dla użytkownika urządzenia StorSimple w witrynie Azure portal, przejdź do usługi Menedżer urządzeń StorSimple, a następnie przejdź do **kontrola dostępu (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Utworzyć niestandardową rolę Administrator infrastruktury usługi StorSimple

W poniższym przykładzie Rozpoczniemy pracę wbudowana rola **czytnika** umożliwia użytkownikom można wyświetlać wszystkie zakresy zasobów, ale nie edytować je lub utworzyć nowe. Następnie rozszerzmy tę rolę, aby utworzyć rolę niestandardową nowego administratora infrastruktury usługi StorSimple. Ta rola jest przypisywana do użytkowników, którzy mogą zarządzać infrastruktury dla urządzeń StorSimple.

1. Uruchom program Windows PowerShell jako administrator.

2. Loguje się do platformy Azure.

    `Connect-AzureRmAccount`

3. Eksportowanie roli Czytelnik jako szablon JSON na tym komputerze.

    ```powershell
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otwórz plik JSON w programie Visual Studio. Zobaczysz, że typowych ról RBAC składa się z trzema głównymi sekcjami, **akcje**, **NotActions**, i **AssignableScopes**.

    W **akcji** sekcji wszystkie dozwolone operacje dla tej roli są wyświetlane. Każda akcja jest przypisywany od dostawcy zasobów. Jako administrator infrastruktury StorSimple można używać `Microsoft.StorSimple` dostawcy zasobów.

    Zobacz wszystkich dostawców zasobów dostępnych i zarejestrowanych w ramach subskrypcji za pomocą programu PowerShell.

    `Get-AzureRMResourceProvider`

    Możesz również sprawdzić dla wszystkich dostępnych poleceń cmdlet programu PowerShell do zarządzania dostawcy zasobów.

    W **NotActions** sekcje, wszystkie ograniczeniami akcji dla określonej roli RBAC są wyświetlane. W tym przykładzie Brak akcji są ograniczone.
    
    W obszarze **AssignableScopes**, są wyświetlane identyfikatory subskrypcji. Upewnij się, że rola RBAC zawiera identyfikator subskrypcji jawne, gdy jest używany. Jeśli nie określono Identyfikatora poprawną subskrypcję, nie możesz zaimportować rolę w ramach subskrypcji.

    Edytuj plik, pamiętając o poprzednim zagadnienia.

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

6. Importuj niestandardową rolę RBAC, wróć do środowiska.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ta rola powinien zostać wyświetlony na liście ról w **kontroli dostępu** bloku.

![Wyświetl role RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Aby uzyskać więcej informacji, przejdź do [niestandardowych ról](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Przykładowe dane wyjściowe do tworzenia roli niestandardowej za pomocą programu PowerShell

```powershell
Connect-AzureRmAccount
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
Get-AzureRMRoleDefinition -Name "Reader"
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
Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"
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

Dostęp udzielany jest w ramach zasobu, grupy zasobów lub subskrypcji, która jest zakresem przypisania roli. Przy udzielaniu dostępu, pamiętać, że dostępu przyznane w węźle nadrzędnym jest dziedziczona przez dziecko. Aby uzyskać więcej informacji, przejdź do [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

1. Przejdź do **kontrola dostępu (IAM)**. Kliknij przycisk **+ Dodaj** w bloku kontrola dostępu.

    ![Dodawanie dostępu do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Wybierz rolę, którą chcesz przypisać, w tym przypadku jest **administrator infrastruktury StorSimple**.

3. Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

4. Wybierz **Zapisz** przypisanie.

    ![Dodaj uprawnienia do roli RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Dodawanie użytkownika** powiadomień śledzi postęp. Po użytkownik został pomyślnie dodany, listę użytkowników, kontroli dostępu jest aktualizowana.

## <a name="view-permissions-for-the-custom-role"></a>Wyświetl uprawnienia dla roli niestandardowej

Po utworzeniu tej roli można wyświetlić uprawnienia skojarzone z tą rolą w witrynie Azure portal.

1. Aby wyświetlić uprawnienia skojarzone z tą rolą, przejdź do **kontrola dostępu (IAM) > Role > administrator infrastruktury StorSimple**. Zostanie wyświetlona lista użytkowników w tej roli.

2. Wybierz użytkownika administracyjnego infrastruktury usługi StorSimple, a następnie kliknij przycisk **uprawnienia**.

    ![Wyświetl uprawnienia dla roli administratora usługi StorSimple Infra](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Skojarzone z tą rolą uprawnienia są wyświetlane.

    ![Wyświetl użytkowników należących do roli administratora usługi StorSimple Infra](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [przypisywanie ról niestandardowych dla użytkowników wewnętrznych i zewnętrznych](../role-based-access-control/role-assignments-external-users.md).