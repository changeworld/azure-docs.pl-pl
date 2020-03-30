---
title: Konfigurowanie stref wyszukiwania wstecznego dla sprawdzania baneru SMTP
titlesuffix: Azure Virtual Network
description: W tym artykule opisano sposób konfigurowania stref wyszukiwania wstecznego dla sprawdzania baneru SMTP na platformie Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6c37ba61005c9936e6421d06369d1f52b93ac264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201700"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurowanie stref wyszukiwania wstecznego dla sprawdzania baneru SMTP

W tym artykule opisano sposób używania strefy odwrotnej w usłudze Azure DNS i tworzenia rekordu odwrotnego dns (PTR) dla sprawdzania banerów SMTP.

## <a name="symptom"></a>Objaw

Jeśli serwer SMTP jest obsługiwany na platformie Microsoft Azure, podczas wysyłania lub odbierania wiadomości z serwerów poczty zdalnej może zostać wyświetlony następujący komunikat o błędzie:

**554: Brak rekordu PTR**

## <a name="solution"></a>Rozwiązanie

W przypadku wirtualnego adresu IP na platformie Azure rekordy odwrotne są tworzone w strefach domeny należących do firmy Microsoft, a nie w strefach domen niestandardowych.

Aby skonfigurować rekordy PTR w strefach należących do firmy Microsoft, należy użyć właściwości -ReverseFqdn w zasobie PublicIpAddress. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](../dns/dns-reverse-dns-for-azure-services.md).

Podczas konfigurowania rekordów PTR upewnij się, że adres IP i odwrócona sieć FQDN są własnością subskrypcji. Jeśli spróbujesz ustawić odwróconą numer FQDN, która nie należy do subskrypcji, zostanie wyświetlony następujący komunikat o błędzie:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) ReverseFqdn dopasowuje fqdn dowolnego publicznego zasobu ip w ramach subskrypcji;
    2) ReverseFqdn rozwiązuje fqdn (za pośrednictwem łańcucha rekordów CName) dowolnego publicznego zasobu ip w ramach subskrypcji;
    3) Jest rozpoznawany na adres IP (za pośrednictwem łańcucha rekordów CName i A) statycznego publicznego zasobu ip w ramach subskrypcji.

Jeśli ręcznie zmienisz baner SMTP, aby dopasować go do naszej domyślnej odwrotnej nazwy FQDN, zdalny serwer poczty może zakończyć się niepowodzeniem, ponieważ może oczekiwać, że host baneru SMTP będzie zgodny z rekordem MX dla domeny.
