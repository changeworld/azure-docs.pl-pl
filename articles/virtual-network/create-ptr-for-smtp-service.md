---
title: Konfigurowanie strefy wyszukiwania wstecznego sprawdzanie transparent SMTP na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób konfigurowania strefy wyszukiwania wstecznego sprawdzanie transparent SMTP na platformie Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2018
ms.locfileid: "29151873"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurowanie strefy wyszukiwania wstecznego sprawdzanie transparent SMTP

W tym artykule opisano sposób używania strefy wyszukiwania wstecznego w usłudze Azure DNS i tworzenie rekordu wstecznego DNS (PTR) dla Sprawdź transparent SMTP. 

## <a name="symptom"></a>Objaw

Hosta serwera SMTP w Microsoft Azure, może zostać wyświetlony następujący komunikat o błędzie podczas wysyłać ani odbierać wiadomości z serwerów poczty zdalnej:

**554: nie rekordu PTR** 

## <a name="solution"></a>Rozwiązanie

Dla wirtualnego adresu IP na platformie Azure odwrotnej rekordy są tworzone w programie Microsoft należące do domeny strefy, strefy domeny niestandardowej nie.

Aby skonfigurować rekordów PTR w programie Microsoft należące do strefy, użyj właściwości ReverseFqdn — w zasobie publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Konfiguruj reverse DNS dla usługi hostowanej na platformie Azure](../dns/dns-reverse-dns-for-azure-services.md). 

Do skonfigurowania rekordów PTR, upewnij się, że adres IP i odwrotnej nazwy FQDN są własnością subskrypcji. Jeśli spróbujesz ustawić odwrotnej nazwy FQDN, która nie należy do subskrypcji, pojawi się następujący komunikat o błędzie:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn jest zgodny fqdn dowolnego zasobu z publicznym adresem ip w ramach subskrypcji; 
    2) ReverseFqdn jest rozpoznawany jako nazwa fqdn (poprzez łańcuch rekordów CName) dowolnego zasobu z publicznym adresem ip w obszarze subskrypcji; 
    3) Rozpoznaje go jako adresu ip zasobu statycznego publicznego adresu ip w ramach subskrypcji (poprzez łańcuch rekordów CName i A). 

Jeśli ręcznie zmienisz transparencie SMTP odpowiadające domyślnego odwrotnej nazwy FQDN, serwera poczty zdalnego może nadal się niepowodzeniem, ponieważ może oczekiwać hosta SMTP transparent na podstawie rekordu MX dla domeny.