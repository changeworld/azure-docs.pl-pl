---
title: Konfigurowanie strefy wyszukiwania wstecznego na potrzeby kontroli baneru SMTP na platformie Azure
titlesuffix: Azure Virtual Network
description: W tym artykule opisano sposób konfigurowania stref wyszukiwania wstecznego na potrzeby kontroli baneru SMTP na platformie Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 203c3c5f371af7de891f0949a35378294bb50a0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60713643"
---
# <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Konfigurowanie strefy wyszukiwania wstecznego na potrzeby kontroli baneru SMTP

W tym artykule opisano sposób użycia strefy wyszukiwania wstecznego w usłudze Azure DNS i tworzenie rekordu odwrotnego DNS (PTR) dla Sprawdź baneru SMTP.

## <a name="symptom"></a>Objaw

Jeśli hostowany serwer SMTP w systemie Microsoft Azure, otrzymasz następujący komunikat o błędzie podczas wysyłania lub komunikat o błędzie z serwerów poczty zdalnej:

**554: Żaden rekord PTR**

## <a name="solution"></a>Rozwiązanie

Dla wirtualnego adresu IP na platformie Azure rekordami odwrotnego są tworzone w programie Microsoft należące do strefy domeny, domeny niestandardowej nie strefy.

Aby skonfigurować rekordów PTR w programie Microsoft należące do strefy, należy użyć właściwości — element ReverseFqdn zasobu publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [Konfigurowanie odwrotnego systemu DNS dla usług hostowanych na platformie Azure](../dns/dns-reverse-dns-for-azure-services.md).

Po skonfigurowaniu rekordów PTR, upewnij się, że adres IP i odwrotnej nazwy FQDN należą do subskrypcji. Jeśli spróbujesz ustawić odwrotnej nazwy FQDN, który nie należy do subskrypcji, pojawi się następujący komunikat o błędzie:

    Set-AzPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership:
                        
    1) Element ReverseFqdn pasuje do nazwy fqdn dowolnego zasobu z publicznym adresem ip w ramach subskrypcji;
    2) Element ReverseFqdn jest rozpoznawany jako nazwy fqdn (poprzez łańcuch rekordów CName), dowolnego zasobu z publicznym adresem ip w ramach subskrypcji;
    3) Jest on rozpoznawany jako adres ip zasobu statycznego publicznego adresu ip w ramach subskrypcji (poprzez łańcuch rekordów CName i A).

Jeśli ręcznie zmienisz swoje baneru SMTP, aby dopasować domyślnego reverse nazwa FQDN, serwer poczty zdalnego może nadal się niepowodzeniem, ponieważ mogą oczekiwać, hosta transparent SMTP, aby dopasować rekord MX w domenie.
