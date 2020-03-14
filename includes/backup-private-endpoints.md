---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137028"
---
Możesz teraz używać [prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) do bezpiecznego wykonywania kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które działają w ramach maszyn wirtualnych platformy Azure. Można go również użyć dla serwerów lokalnych przy użyciu agenta MARS.

Kopia zapasowa maszyny wirtualnej platformy Azure nie wymaga połączenia z Internetem i dlatego nie wymaga prywatnych punktów końcowych, aby umożliwić izolację sieci.

>[!NOTE]
> Ta funkcja jest obecnie używana na wczesnej platformie. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) i Wyślij wiadomość e-mail na adres azbackupnetsec@microsoft.com, Jeśli interesuje Cię używanie prywatnych punktów końcowych dla Azure Backup. Możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.
