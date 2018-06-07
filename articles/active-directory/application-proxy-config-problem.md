---
title: Problem z tworzeniem aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy dotyczące tworzenia aplikacji serwera Proxy aplikacji w portalu usługi Azure AD administratora
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 953d3399012b92442c0dcb4c0db717f2bf273f1b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591800"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem z tworzeniem aplikacji serwera Proxy aplikacji 

Poniżej przedstawiono niektóre typowe problemy krój osoby podczas tworzenia nowej aplikacji serwera proxy aplikacji.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby dowiedzieć się więcej na temat tworzenia aplikacji serwera Proxy aplikacji za pośrednictwem portalu administratora, zobacz [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md).

Jeśli są następujące kroki w tym dokumencie i uzyskiwania błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu usunięcia aplikacji. Większość komunikaty o błędach obejmują sugerowanej poprawki. 

## <a name="specific-things-to-check"></a>Określone czynności do wykonania

Aby uniknąć typowych błędów, sprawdzić:

-   Administratorzy z uprawnieniami do tworzenia aplikacji serwera Proxy aplikacji

-   Wewnętrzny adres URL jest unikatowa

-   Zewnętrzny adres URL jest unikatowa

-   Adresy URL rozpoczynać się od http lub https i kończyć się "/"

-   Adres URL powinien być nazwą domeny, a nie adres IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Kolejne kroki
[Włącz serwer Proxy aplikacji w portalu Azure](manage-apps/application-proxy-enable.md)
