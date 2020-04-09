---
title: Aktualizacje protokołu TLS w centrach powiadomień
description: Dowiedz się więcej o obsłudze protokołu TLS w usłudze Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885756"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Aby zapewnić wyższy poziom zabezpieczeń, centra powiadomień wyłączą obsługę protokołu TLS w wersjach 1.0 i 1.1 31 maja 2020 r. (rozszerzone z 30 kwietnia 2020 r.). Te starsze protokoły zapewniają słabą kryptografię i są narażone na ataki BESTII i PUDEL. Ta zmiana nie ma wpływu na aplikacje wdrożone na urządzeniach z systemem Android w wersji 5 lub większej lub w systemie iOS w wersji 5 lub większej, ponieważ te systemy operacyjne obsługują protokół TLS 1.2, a klient i serwer będą negocjować najwyższą wzajemnie obsługiwaną wersję protokołu po połączeniu.

Zaleca się przejrzenie wszystkich aplikacji korzystających z usługi Azure Notification Hubs, aby upewnić się, że używają one najbardziej odpowiednich bibliotek i stosów TLS obsługujących usługę TLS 1.2.

## <a name="update-apps"></a>Aktualizowanie aplikacji

Aplikacje dla systemu iOS korzystają z protokołu TLS 1.2 przy użyciu funkcji zabezpieczeń sieciowych firmy Apple o nazwie App Transport Security (ATS). Ats nie może być używany dla SDK starszych niż iOS 9.0 lub macOS 10.11, a więcej można przeczytać o tym z [dokumentacji firmy Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

W przypadku aplikacji systemu Android korzystających z wystąpień SSLSocket ustaw włączone protokoły dla każdego wystąpienia SSLSocket, jak wspomniano w [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabela na stronie pomocy technicznej dotyczącej [zgodności protokołów TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ułatwia mapowanie systemów operacyjnych ze zgodnymi wersjami protokołu TLS.

Aby uzyskać więcej informacji, zobacz omówienie [obsługi protokołów TLS w systemie Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Następne kroki

- [Omówienie Centrów powiadomień](notification-hubs-push-notification-overview.md)