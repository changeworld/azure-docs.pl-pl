---
title: Skalowanie usługi w chmurze platformy Azure w programie Windows PowerShell | Dokumentacja firmy Microsoft
description: (klasyczne) Dowiedz się, jak za pomocą programu PowerShell do skalowania roli sieci web lub roli proces roboczy lub na platformie Azure.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: a7ae8ff202d403dff19b8c9a6a09492235db27ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842951"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Sposób skalowania usługi w chmurze w programie PowerShell

Można użyć środowiska Windows PowerShell można skalować roli sieci web lub roli proces roboczy przychodzący lub wychodzący przez dodanie lub usunięcie wystąpień.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Przed wykonaniem jakichkolwiek działań na subskrypcję za pomocą programu PowerShell, należy zalogować się:

```powershell
Add-AzureAccount
```

Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, może być konieczna zmiana bieżącej subskrypcji, w zależności od tego, gdzie znajduje się usługa w chmurze. Aby sprawdzić bieżącej subskrypcji, uruchom polecenie:

```powershell
Get-AzureSubscription -Current
```

Jeśli musisz zmienić bieżącej subskrypcji, uruchom:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Sprawdź bieżącą liczbę wystąpień dla roli użytkownika

Aby sprawdzić aktualny stan roli użytkownika, uruchom polecenie:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Powinny zostać wyświetlone informacje o roli, włącznie z bieżącej wersji i wystąpienia licznika systemu operacyjnego. W takim przypadku rola ma jedno wystąpienie.

![Informacje o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skalowanie w poziomie roli, dodając więcej wystąpień

Aby skalować w poziomie roli użytkownika, należy przekazać żądaną liczbę wystąpień jako **liczba** parametr **AzureRole zestaw** polecenia cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Bloki polecenia cmdlet na chwilę podczas nowe wystąpienia są udostępniane i uruchomiony. W tym czasie możesz otworzyć nowe okno programu PowerShell i wywołanie **Get AzureRole** jak pokazano wcześniej, pojawi się nowy liczba wystąpień docelowych. I czy możesz sprawdzić stan roli w portalu, należy sprawdzić, nowe wystąpienie uruchamiania:

![Począwszy od portalu wystąpienia maszyny Wirtualnej](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Gdy nowe wystąpienia uruchomiona, polecenie cmdlet zwróci pomyślnie:

![Powodzenie wzrost wystąpienia roli](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skalowanie w roli przez usunięcie wystąpień

W roli można skalować, usuwając wystąpień w taki sam sposób. Ustaw **liczba** parametru **AzureRole zestaw** liczby wystąpień mają po zakończeniu skali w operacji.

## <a name="next-steps"></a>Następne kroki

Nie jest możliwe do skonfigurowania automatycznego skalowania usługi w chmurze z programu PowerShell. Aby to zrobić, zobacz [jak automatyczne skalowanie usługi w chmurze](cloud-services-how-to-scale-portal.md).
