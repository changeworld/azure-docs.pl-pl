---
title: Zarządzanie protokołów i mechanizmów szyfrowania w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać protokoły (TLS, SSL) i szyfrów (DES) w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 91b6cd64a42319b2a5307919c2efe6bc8e5dcd64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657822"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Zarządzanie protokołów i mechanizmów szyfrowania w usłudze Azure API Management

Usługa Azure API Management jest obsługiwanych wiele wersji protokołu TLS dla stron zarówno klient, jak i wewnętrznej bazy danych, a także szyfrowania 3DES.

Ten przewodnik pokazuje, jak zarządzać protokołów i szyfrów konfiguracji wystąpienia usługi Azure API Management.

![Zarządzanie protokołów i szyfrów w APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Wystąpienia usługi API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak zarządzać szyfrowania TLS protokołów i 3DES

1. Przejdź do swojej **wystąpienia usługi API Management** w witrynie Azure portal.
2. Wybierz **protokołu ustawienia** z menu.  
3. Włącz lub wyłącz żądane protokołów i szyfrów.
4. Kliknij pozycję **Zapisz**. Zmiany zostaną zastosowane w ciągu godziny.  

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Zapoznaj się z kilku [wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o usłudze API Management.