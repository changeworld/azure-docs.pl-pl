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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: a55d16a35b5eec1af2b24d02e158905493615999
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441116"
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
2. Wybierz **SSL** z menu.  
    ![Zarządzanie protokołów i szyfrów w APIM — menu](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Włącz lub wyłącz żądane protokołów i szyfrów.
4. Kliknij pozycję **Zapisz**. Zmiany zostaną zastosowane w ciągu godziny.  
    ![Zarządzanie protokołów i szyfrów w APIM - zapisu](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Zapoznaj się z kilku [wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o usłudze API Management.