---
title: Omówienie zapory usługi Azure service tags
description: W tym artykule przedstawiono omówienie procedury tagi usługi zapory usługi Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450168"
---
# <a name="azure-firewall-service-tags"></a>Usługa Azure tagi usługi zapory

Tag usługi reprezentuje grupę prefiksów adresów IP, aby zminimalizować złożoność tworzenia reguły zabezpieczeń. Nie można utworzyć własnego tagu usługi ani określić adresów IP uwzględnionych w tagu. Firma Microsoft zarządza prefiksami adresów obejmowanymi przez tag usługi i automatycznie aktualizuje tag usługi, gdy adresy ulegną zmianie.

Tagi usługi w usłudze Azure zapory może służyć w pole docelowe zasady sieciowej. Można je zamiast konkretnych adresów IP.

## <a name="supported-service-tags"></a>Obsługiwane tagi usługi

Zobacz [grup zabezpieczeń](../virtual-network/security-overview.md#service-tags) listę tagów usługi, które są dostępne do użycia w regułach sieciowej zapory usługi Azure.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat reguł zapory usługi Azure, zobacz [reguły zapory na platformie Azure przetwarzanie logiki](rule-processing.md).