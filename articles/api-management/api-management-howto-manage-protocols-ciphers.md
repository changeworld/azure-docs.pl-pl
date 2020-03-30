---
title: Zarządzanie protokołami i szyframi w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak zarządzać protokołami (TLS) i szyframi (DES) w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: apimpm
ms.openlocfilehash: 7f87389016286c9f1b91abc77155be9e94005371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335865"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Zarządzanie protokołami i szyfrowaniem w usłudze Azure API Management

Usługa Azure API Management obsługuje wiele wersji protokołu TLS zarówno dla stron klienta i wewnętrznej bazy danych, jak również szyfru 3DES.

W tym przewodniku pokazano, jak zarządzać konfiguracją protokołów i szyfrów dla wystąpienia usługi Azure API Management.

![Zarządzanie protokołami i szyframi w interfejsie APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Wystąpienie usługi API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak zarządzać protokołami TLS i szyfrem 3DES

1. Przejdź do **wystąpienia usługi API Management** w witrynie Azure portal.
2. Wybierz z menu **ustawienia protokołu.**  
3. Włączanie lub wyłączanie żądanych protokołów lub szyfrów.
4. Kliknij przycisk **Zapisz**. Zmiany zostaną wprowadzone w ciągu godziny.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Obejrzyj więcej [filmów](https://azure.microsoft.com/documentation/videos/index/?services=api-management) o zarządzaniu interfejsami API.