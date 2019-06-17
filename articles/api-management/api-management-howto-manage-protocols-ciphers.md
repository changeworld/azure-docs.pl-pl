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
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: f809aaf872607e7fa61368832a3df74318f2a858
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66141498"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Zarządzanie protokołów i mechanizmów szyfrowania w usłudze Azure API Management

Usługa Azure API Management jest obsługiwanych wiele wersji protokołu TLS dla stron zarówno klient, jak i wewnętrznej bazy danych, a także szyfrowania 3DES.

Ten przewodnik pokazuje, jak zarządzać protokołów i szyfrów konfiguracji wystąpienia usługi Azure API Management.

![Zarządzanie protokołów i szyfrów w APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

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