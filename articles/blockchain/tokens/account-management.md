---
title: Zarządzanie kontem tokenów łańcucha bloków azure
description: Korzystając z zarządzania kontem tokenów łańcucha bloków azure, można tworzyć grupy i łączyć konta łańcucha bloków, aby kontrolować dostęp do akcji blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326098"
---
# <a name="azure-blockchain-tokens-account-management"></a>Zarządzanie kontem tokenów łańcucha bloków azure

[!INCLUDE [Preview note](./includes/preview.md)]

W przypadku rozwiązania blockchain użytkownicy mogą wymagać różnych poziomów dostępu do tokenów, które są tworzone za pomocą usługi Azure Blockchain Tokens. W większości scenariuszy blockchain musisz zaplanować i wdrożyć różne konta blockchain, które istnieją w księdze. Należy również zarządzać dostępem między uczestnikami.Korzystając z zarządzania kontem tokenów łańcucha bloków azure, można tworzyć grupy i łączyć konta łańcucha bloków, aby kontrolować dostęp do akcji blockchain.

## <a name="blockchain-networks"></a>Sieci blockchain

Tokeny łańcucha bloków azure umożliwia wdrażanie i zarządzanie tokenami w zestawie sieci blockchain. Możesz połączyć jedną księgę blockchain lub kilka książek blockchain z usługą.

## <a name="accounts"></a>Konta

W przypadku sieci blockchain połączonych z tokenami łańcucha bloków Azure usługa tworzy i zarządza parami kluczy prywatnych i publicznych oraz wykonuje podpisywanie i przesyłanie transakcji. Tokeny łańcucha bloków azure udostępnia również mapowanie tożsamości, aby dopasować konta do tożsamości klucza publicznego w księdze.

## <a name="groups"></a>Grupy

Grupy umożliwiają zarządzanie dużą liczbą kont blockchain w połączonych sieciach. Można śledzić i inspekcji, które aplikacje i użytkownicy w katalogu mają możliwość korzystania z kont za pośrednictwem interfejsów API tokenów azure blockchain tokenów. Na przykład można pogrupować zestaw kont, które reprezentują różne linie biznesowe lub różne role i dostęp do tokenów łańcucha bloków.

Można również skojarzyć grupę z użytkownikiem usługi Azure Active Directory lub jednostką usługi, a ten podmiot zabezpieczeń ma uprawnienia do grupy i skojarzonych z nią kont.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o dostępnych [szablonach tokenów blockchain platformy Azure.](templates.md)
