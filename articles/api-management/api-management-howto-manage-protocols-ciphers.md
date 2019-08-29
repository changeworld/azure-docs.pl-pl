---
title: Zarządzanie protokołami i szyframi na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak zarządzać protokołami (TLS, SSL) i szyfrowania (DES) na platformie Azure API Management.
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
ms.openlocfilehash: f7c7fdd06480ce3da70c86d38ab0685b9b3aaaf2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072402"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Zarządzanie protokołami i szyframi na platformie Azure API Management

Usługa Azure API Management obsługuje wiele wersji protokołu TLS zarówno dla strony klienta, jak i zaplecza, jak również szyfrowania 3DES.

W tym przewodniku przedstawiono sposób zarządzania protokołami i szyfrowania konfiguracji wystąpienia usługi Azure API Management.

![Zarządzanie protokołami i szyframi w APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

* Wystąpienie API Management

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Jak zarządzać protokołami TLS i algorytmem szyfrowania 3DES

1. Przejdź do **wystąpienia API Management** w Azure Portal.
2. Wybierz pozycję **Ustawienia protokołu** z menu.  
3. Włącza lub wyłącza wymagane protokoły lub szyfry.
4. Kliknij polecenie **Zapisz**. Zmiany zostaną zastosowane w ciągu godziny.  

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o protokole [TLS (Transport Layer Security)](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* Poznaj więcej [filmów wideo](https://azure.microsoft.com/documentation/videos/index/?services=api-management) dotyczących API Management.