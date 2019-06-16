---
title: Skalowanie usługi w chmurze platformy Azure w programie Windows PowerShell | Dokumentacja firmy Microsoft
description: (wersja klasyczna) Dowiedz się, jak skalować roli Internet lub roli procesu roboczego lub na platformie Azure przy użyciu programu PowerShell.
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
ms.author: memccror
ms.openlocfilehash: 6c013687faaca33938d0b27303e9b1252482fcb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963320"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Jak skalować usługi w chmurze w programie PowerShell

Można użyć programu Windows PowerShell, skalować, dodając lub usuwając wystąpienia roli Internet lub roli procesu roboczego wewnątrz lub na zewnątrz.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zanim będzie można wykonywać dowolne operacje w ramach subskrypcji za pomocą programu PowerShell, musisz zalogować się:

```powershell
Add-AzureAccount
```

Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, może być konieczne zmiany bieżącej subskrypcji, w zależności od tego, w którym znajduje się usługa w chmurze. Aby sprawdzić bieżącą subskrypcję, uruchom polecenie:

```powershell
Get-AzureSubscription -Current
```

Jeśli zachodzi potrzeba zmiany bieżącej subskrypcji, uruchom:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Sprawdź bieżąca liczba wystąpień dla roli użytkownika

Aby sprawdzić bieżący stan roli użytkownika, uruchom polecenie:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Ponownie należy uzyskać informacji na temat tej roli, w tym jego bieżący system operacyjny w wersji i liczbę wystąpień. W tym przypadku Rola zawiera pojedyncze wystąpienie.

![Informacje o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skalowanie w poziomie rolę poprzez dodanie większej liczby wystąpień

Aby skalować w poziomie roli, należy przekazać żądaną liczbę wystąpień jako **liczba** parametr **AzureRole zestaw** polecenia cmdlet:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Bloki polecenia cmdlet chwilowo podczas nowe wystąpienia są aprowizowane i uruchomić. W tym czasie możesz otworzyć nowe okno programu PowerShell i wywołania **Get AzureRole** jak pokazano wcześniej, zostanie wyświetlony nowy docelowa liczba wystąpień. A jeśli możesz sprawdzić stan roli w portalu, powinien zostać wyświetlony nowe wystąpienie uruchamiania:

![Wystąpienie maszyny Wirtualnej w witrynie portal](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Gdy nowe wystąpienia zostały uruchomione, polecenie cmdlet zwróci się pomyślnie:

![Powodzenie wzrost wystąpienia roli](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skalowanie w roli, usuwając wystąpień

Możesz skalować w roli przez usunięcie wystąpień w taki sam sposób. Ustaw **liczba** parametru **AzureRole zestaw** do liczby wystąpień, o których chcesz mieć po zakończeniu skalowanie w operacji.

## <a name="next-steps"></a>Kolejne kroki

Nie jest możliwe skonfigurować automatyczne skalowanie usług w chmurze za pomocą programu PowerShell. Aby to zrobić, zobacz [jak automatyczne skalowanie usługi w chmurze](cloud-services-how-to-scale-portal.md).
