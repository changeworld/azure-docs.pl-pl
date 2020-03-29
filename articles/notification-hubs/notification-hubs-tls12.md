---
title: Aktualizacje protokołu TLS w centrach powiadomień
description: Dowiedz się więcej o obsłudze protokołu TLS w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908529"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Aby zapewnić wyższy poziom zabezpieczeń, 30 kwietnia 2020 r. centra powiadomień wyłączą obsługę protokołu TLS w wersji 1.0 i 1.1. Te starsze protokoły zapewniają słabą kryptografię i są narażone na ataki BESTII i PUDEL. Zmiany te nie mają wpływu na aplikacje wdrożone na urządzeniach z systemem Android w wersji 5 lub większej lub iOS w wersji 5 lub większej, ponieważ te systemy operacyjne obsługują tls 1.2, a klient i serwer będą negocjować najwyższą wzajemnie obsługiwaną wersję protokołu po podłączeniu.

Zaleca się przejrzenie wszystkich aplikacji korzystających z usługi Azure Notification Hubs, aby upewnić się, że używają one najbardziej odpowiednich bibliotek i stosów TLS obsługujących usługę TLS 1.2.

## <a name="update-apps"></a>Aktualizowanie aplikacji

Aplikacje dla systemu iOS korzystają z protokołu TLS 1.2 przy użyciu funkcji zabezpieczeń sieciowych firmy Apple o nazwie App Transport Security (ATS). Ats nie może być używany dla SDK starszych niż iOS 9.0 lub macOS 10.11, a więcej można przeczytać o tym z [dokumentacji firmy Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

W przypadku aplikacji systemu Android korzystających z wystąpień SSLSocket ustaw włączone protokoły dla każdego wystąpienia SSLSocket, jak wspomniano w [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabela na stronie pomocy technicznej dotyczącej [zgodności protokołów TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ułatwia mapowanie systemów operacyjnych ze zgodnymi wersjami protokołu TLS.

Aby uzyskać więcej informacji, zobacz omówienie [obsługi protokołów TLS w systemie Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Następne kroki

- [Omówienie Centrów powiadomień](notification-hubs-push-notification-overview.md)