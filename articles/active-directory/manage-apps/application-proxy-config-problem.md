---
title: Problem podczas tworzenia aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy dotyczące tworzenia aplikacji serwera Proxy aplikacji w portalu administratora usługi Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7aad73331d06828a4bfe909dbb3b98350b1d0d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293596"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem podczas tworzenia aplikacji serwera Proxy aplikacji 

Poniżej przedstawiono niektóre typowe problemy twarzy osoby podczas tworzenia nowej aplikacji serwera proxy aplikacji.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby dowiedzieć się więcej na temat tworzenia aplikacji serwera Proxy aplikacji za pośrednictwem portalu administracyjnego, zobacz [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

Jeśli postępujesz zgodnie z instrukcjami w tym dokumencie i wyświetla się błąd tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu rozwiązania aplikacji. Większość komunikaty o błędach obejmują sugerowanej poprawki. 

## <a name="specific-things-to-check"></a>Określone czynności do wykonania

Aby uniknąć typowych błędów, należy zweryfikować:

-   Administratorzy z uprawnieniami do tworzenia aplikacji serwera Proxy aplikacji

-   Wewnętrzny adres URL jest unikatowa

-   Zewnętrzny adres URL jest unikatowa

-   Adresy URL, uruchom za pomocą protokołu http lub https, a kończyć się znakiem "/"

-   Adres URL powinien być nazwę domeny, a nie adres IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu, podczas tworzenia aplikacji. Można również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Kolejne kroki
[Włącz serwer Proxy aplikacji w witrynie Azure portal](application-proxy-add-on-premises-application.md)
