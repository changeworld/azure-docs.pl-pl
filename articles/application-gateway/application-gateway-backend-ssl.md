---
title: Włączanie kompleksowej usługi SSL w usłudze Azure Application Gateway | Microsoft Docs
description: Ta strona zawiera omówienie kompleksowej obsługi protokołu SSL w usłudze Application Gateway.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
origin.date: 07/19/2017
ms.date: 04/15/2019
ms.author: v-junlch
ms.openlocfilehash: 856f23de8a8772255f570a923ecf1708dc819bb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831884"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Omówienie kompleksowej usługi SSL z usługą Application Gateway

Usługa Application Gateway obsługuje przerywanie połączenia SSL na bramie, po którym ruch na ogół płynie niezaszyfrowany do serwerów zaplecza. Ta funkcja umożliwia odciążenie serwerów sieci Web z nadmiaru kosztownych operacji szyfrowania i odszyfrowywania. Jednak dla niektórych klientów nieszyfrowana komunikacja z serwerami zaplecza jest opcją niemożliwą do zaakceptowania. Nieszyfrowana komunikacja może być spowodowana przez wymagania dotyczące zabezpieczeń lub zgodności albo aplikacja może akceptować jedynie bezpieczne połączenia. Na potrzeby takich aplikacji brama aplikacji obsługuje kompleksowe szyfrowanie SSL.

## <a name="overview"></a>Omówienie

Kompleksowa usługa SSL pozwala na bezpieczne przesyłanie zaszyfrowanych danych poufnych do zaplecza, umożliwiając jednocześnie korzystanie z funkcji równoważenia obciążenia warstwy 7, które oferuje usługa Application Gateway. Do tych funkcji należą koligacja sesji oparta na plikach cookie, routing oparty na adresach URL, obsługa routingu opartego na witrynach lub możliwość iniekcji nagłówków X-Forwarded-*.

Po skonfigurowaniu kompleksowego trybu komunikacji SSL usługa Application Gateway kończy sesje SSL na bramie i odszyfrowuje ruch użytkownika. Następnie stosuje skonfigurowane reguły, aby wybrać odpowiednie wystąpienie puli serwerów zaplecza w celu skierowania do nich ruchu. Następnie usługa Application Gateway inicjuje nowe połączenie SSL z serwerem zaplecza i ponownie szyfruje dane przy użyciu certyfikatu klucza publicznego serwera zaplecza przed przekazaniem żądania do zaplecza. Kompleksową usługę SSL można włączyć, konfigurując dla ustawienia protokołu BackendHTTPSetting wartość HTTPS, co jest następnie stosowane do puli zaplecza. Każdy serwer zaplecza w puli zaplecza z włączoną kompleksową usługą SSL należy skonfigurować przy użyciu certyfikatu, aby umożliwić bezpieczną komunikację.

![Scenariusz kompleksowej usługi SSL][1]

W tym przykładzie żądania używające protokołu TLS 1.2 są kierowane do serwerów zaplecza w puli Pula1 za pomocą kompleksowej usługi SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Kompleksowa usługa SSL i lista dozwolonych certyfikatów

Usługa Application Gateway komunikuje się tylko ze znanymi wystąpieniami zaplecza, których certyfikaty znajdują się na liście dozwolonych certyfikatów tej usługi. Aby włączyć listę dozwolonych certyfikatów, należy przekazać klucz publiczny certyfikatów serwera zaplecza do usługi Application Gateway (nie certyfikat główny). W takim przypadku możliwe będą tylko połączenia do znanych zapleczy, które znajdują się na liście dozwolonych. Połączenia do pozostałych zapleczy zakończą się błędem bramy. Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowych i nie są zalecane dla obciążeń w środowisku produkcyjnym. Takie certyfikaty także muszą zostać umieszczone na liście dozwolonych usługi Application Gateway, jak opisano w poprzednich krokach, zanim będzie można ich użyć.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z kompleksową usługą SSL zapoznaj się z informacjami dotyczącymi [włączania kompleksowej usługi SSL w bramie aplikacji](application-gateway-end-to-end-ssl-powershell.md), aby utworzyć bramę aplikacji korzystającą z kompleksowej usługi SSL.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png

<!-- Update_Description: update metedata properties -->