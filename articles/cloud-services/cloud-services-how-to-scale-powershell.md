---
title: Skalowanie usługi w chmurze platformy Azure w programie Windows PowerShell | Dokumenty firmy Microsoft
description: (klasyczny) Dowiedz się, jak używać programu PowerShell do skalowania roli sieci Web lub roli procesu roboczego na platformie Azure.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359042"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Jak skalować usługę w chmurze w programie PowerShell

Za pomocą programu Windows PowerShell można skalować rolę sieci Web lub rolę procesu roboczego, dodając lub usuwając wystąpienia.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Przed wykonaniem jakichkolwiek operacji w ramach subskrypcji za pośrednictwem programu PowerShell należy się zalogować:

```powershell
Add-AzureAccount
```

Jeśli masz wiele subskrypcji skojarzonych z kontem, może być konieczna zmiana bieżącej subskrypcji w zależności od tego, gdzie znajduje się usługa w chmurze. Aby sprawdzić bieżącą subskrypcję, uruchom:

```powershell
Get-AzureSubscription -Current
```

Jeśli chcesz zmienić bieżącą subskrypcję, uruchom:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Sprawdź bieżącą liczbę wystąpień dla swojej roli

Aby sprawdzić bieżący stan roli, uruchom:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Należy odzyskać informacje o roli, w tym jego bieżącej wersji systemu operacyjnego i liczby wystąpień. W takim przypadku rola ma jedno wystąpienie.

![Informacje o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skalowanie w poziomie roli przez dodanie kolejnych wystąpień

Aby skalować w poziomie roli, przekaż żądaną liczbę wystąpień jako **count** parametr do polecenia cmdlet **Set-AzureRole:**

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Polecenie cmdlet blokuje się na chwilę, podczas gdy nowe wystąpienia są aprowizowana i uruchamiana. W tym czasie, jeśli otworzysz nowe okno programu PowerShell i wywołasz **Get-AzureRole,** jak pokazano wcześniej, zobaczysz nową liczbę wystąpień docelowych. A jeśli sprawdzisz stan roli w portalu, powinieneś zobaczyć nowe wystąpienie uruchamiania:

![Wystąpienie maszyny Wirtualnej rozpoczynające się w portalu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Po rozpoczęciu nowych wystąpień polecenie cmdlet powróci pomyślnie:

![Wystąpienie roli zwiększa sukces](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skalowanie w roli przez usunięcie wystąpień

Można skalować w roli, usuwając wystąpienia w ten sam sposób. Ustaw **count** parametr na **Set-AzureRole** do liczby wystąpień, które mają mieć po zakończeniu skalowania w operacji.

## <a name="next-steps"></a>Następne kroki

Nie można skonfigurować automatycznego skalowania dla usług w chmurze z programu PowerShell. Aby to zrobić, zobacz [Jak automatycznie skalować usługę w chmurze](cloud-services-how-to-scale-portal.md).
