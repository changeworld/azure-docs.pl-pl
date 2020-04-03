---
title: Włączanie dzienników diagnostycznych usługi Azure Bastion i praca z nim
description: W tym artykule dowiesz się, jak włączyć dzienniki diagnostyczne usługi Azure Bastion i pracować z nim.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 97f0cdb1e93ef2ad06d2daa04b2f4893fd5dfac2
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619279"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>Włączanie dzienników diagnostycznych bastionu i praca z nim

Gdy użytkownicy łączą się z obciążeniami przy użyciu usługi Azure Bastion, bastion może rejestrować diagnostykę sesji zdalnych. Następnie można użyć diagnostyki, aby wyświetlić, którzy użytkownicy połączyli się z obciążeniami, w jakim czasie, skąd i inne takie istotne informacje rejestrowania. Aby korzystać z diagnostyki, należy włączyć dzienniki diagnostyczne na platformie Azure Bastion. Ten artykuł ułatwia włączenie dzienników diagnostycznych, a następnie wyświetlanie dzienników.

## <a name="enable-the-diagnostics-log"></a><a name="enable"></a>Włączanie dziennika diagnostyki

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do zasobu Bastion platformy Azure i wybierz **ustawienia diagnostyki** na stronie Bastion platformy Azure.

   ![ustawienia diagnostyki](./media/diagnostic-logs/1diagnostics-settings.png)
2. Wybierz **ustawienia diagnostyki**, a następnie wybierz **+Dodaj ustawienie diagnostyczne,** aby dodać miejsce docelowe dla dzienników.

   ![dodawanie ustawień diagnostycznych](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. Na stronie **Ustawienia diagnostyki** wybierz typ konta magazynu, które ma być używane do przechowywania dzienników diagnostycznych.

   ![wybierz lokalizację przechowywania](./media/diagnostic-logs/3add-storage-account.png)
4. Po zakończeniu ustawień będzie wyglądać podobnie do tego przykładu:

   ![przykładowe ustawienia](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Wyświetlanie dziennika diagnostyki

Aby uzyskać dostęp do dzienników diagnostycznych, można bezpośrednio użyć konta magazynu, które zostały określone podczas włączania ustawień diagnostyki.

1. Przejdź do zasobu konta magazynu, a następnie do **pozycji Kontenery**. Zobaczysz obiekt blob **insights-logs-bastionauditlogs** utworzony w kontenerze obiektów blob konta magazynu.

   ![ustawienia diagnostyki](./media/diagnostic-logs/1-navigate-to-logs.png)
2. Podczas przechodzenia do wewnątrz kontenera, widać różne foldery w blogu. Te foldery wskazują hierarchię zasobów dla zasobu Bastion platformy Azure.

   ![dodawanie ustawień diagnostycznych](./media/diagnostic-logs/2-resource-h.png)
3. Przejdź do pełnej hierarchii zasobu bastionu platformy Azure, którego dzienniki diagnostyczne chcesz uzyskać dostęp/widok. 'y=', 'm=', 'd=', 'h=' i 'm=' wskazują odpowiednio rok, miesiąc, dzień, godzinę i minutę dla dzienników diagnostycznych.

   ![wybierz lokalizację przechowywania](./media/diagnostic-logs/3-resource-location.png)
4. Zlokalizuj plik json utworzony przez usługę Azure Bastion, który zawiera dane dziennika diagnostyki dla okresu, do którym następuje nawigowanie.

5. Pobierz plik json z kontenera obiektów blob magazynu. Przykładowy wpis z pliku json jest pokazany poniżej w celach informacyjnych:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Następne kroki

Przeczytaj często zadawane pytania dotyczące [bastionu](bastion-faq.md).
