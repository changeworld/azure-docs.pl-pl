---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137028"
---
Teraz można użyć [prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) do bezpiecznego tworzenia kopii zapasowych danych z serwerów w sieci wirtualnej do magazynu usług odzyskiwania. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla magazynu. Ruch sieciowy między zasobami wewnątrz sieci wirtualnej a przechowalnią jest przesyłany przez sieć wirtualną i prywatne łącze w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego internetu. Prywatne punkty końcowe mogą służyć do tworzenia kopii zapasowych i przywracania baz danych SQL i SAP HANA, które są uruchamiane wewnątrz maszyn wirtualnych platformy Azure. Może być również używany dla serwerów lokalnych przy użyciu agenta MARS.

Tworzenie kopii zapasowej maszyny Wirtualnej platformy Azure nie wymaga łączności z Internetem, a więc nie wymaga prywatnych punktów końcowych, aby umożliwić izolację sieci.

>[!NOTE]
> Ta funkcja jest obecnie we wczesnym użyciu. Wypełnij [tę ankietę](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u) i azbackupnetsec@microsoft.com wyślij do nas wiadomość e-mail, jeśli jesteś zainteresowany korzystaniem z prywatnych punktów końcowych dla usługi Azure Backup. Możliwość korzystania z tej funkcji podlega zatwierdzeniu przez usługę Azure Backup.
