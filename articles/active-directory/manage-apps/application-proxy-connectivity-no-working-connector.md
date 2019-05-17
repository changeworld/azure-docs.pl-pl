---
title: Grupa łączników nie pracy znaleziono dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów, które mogą wystąpić po nie pracy łącznika w grupie łączników dla aplikacji przy użyciu serwera Proxy aplikacji usługi AD systemu Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f829b8e8a4bc08b43d3c30a6333771ccd4e26e8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783609"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nie znaleziono aplikacji serwera Proxy aplikacji grupy łącznika pracy

Ten artykuł pomaga rozwiązać typowe problemy sterowaną nie jest łącznikiem dla aplikacji serwera Proxy aplikacji zintegrowanych z usługą Azure Active Directory.

## <a name="overview-of-steps"></a>Omówienie kroków
Jeśli nie ma żadnych pracy łącznika w grupie łączników dla aplikacji, istnieją na kilka sposobów, aby rozwiązać ten problem:

-   Jeśli masz nie łączników w grupie możesz wykonywać następujące czynności:

    -   Pobrać nowy łącznik po prawej stronie na serwerze lokalnym, a następnie przypisać ją do tej grupy

    -   Przenieś łącznika usługi active do grupy

-   Jeśli masz nie aktywnych łączników w grupie, możesz wykonywać następujące czynności:

    -   Identyfikowanie powodów, dla którego łącznik jest nieaktywny i rozwiązywanie

    -   Przenieś łącznika usługi active do grupy

Na ustalenie problemu, otwórz menu "Serwer Proxy aplikacji" w aplikacji i spójrz na komunikat ostrzegawczy grupy łączników. Jeśli nie istnieją żadne łączniki w grupie, komunikat ostrzegawczy Określa, że grupa wymaga co najmniej jeden łącznik. Jeśli nie masz żadnych aktywnych łączników, komunikat ostrzegawczy wyjaśnia, który. Jest to często mają nieaktywne łączniki. 

   ![Wybór grupy łącznika w witrynie Azure portal](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Aby uzyskać szczegółowe informacje dotyczące każdej z tych opcji zobacz odpowiedniej sekcji poniżej. Instrukcjach przyjęto założenie, że rozpoczynasz od strony zarządzania łącznika. Jeśli jest wyświetlany komunikat o błędzie powyżej, możesz przejść do tej strony, klikając przycisk w komunikacie ostrzegawczym. Możesz również uzyskać ze stroną, przechodząc do **usługi Azure Active Directory**, klikając na **aplikacje dla przedsiębiorstw**, następnie **serwera Proxy aplikacji.**

   ![Łącznik zarządzania grupami w witrynie Azure portal](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Pobrać nowy łącznik

Aby pobrać nowy łącznik, użyj przycisku "Pobierz łącznik" w górnej części strony.

Łącznik można zainstalować na komputerze przy użyciu bezpośredniego linii wzroku do aplikacji zaplecza. Zazwyczaj łącznik jest zainstalowany na tym samym serwerze co aplikacja. Po pobraniu, łącznik powinna zostać wyświetlona w tym menu. Kliknij łącznik, a następnie użyć listy rozwijanej "łącznik grupy", aby upewnij się, że należy on do odpowiedniej grupy. Zapisz zmianę.

   ![Pobierz łącznik w witrynie Azure portal](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Przenieś łącznika usługi Active

Jeśli masz aktywny łącznik, powinny należeć do grupy, która ma linii wzroku do docelowej aplikacji zaplecza, można przenieść łącznika w przypisanej grupie. Aby to zrobić, kliknij przycisk łącznika. W polu "Grupa łączników" Użyj listy rozwijanej wybierz poprawną grupę, a następnie kliknij przycisk Zapisz.

## <a name="resolve-an-inactive-connector"></a>Rozwiąż nieaktywne łącznika

Jeśli tylko łączników w grupie nie są aktywne, są prawdopodobnie na komputerze, który nie ma wszystkie wymagane porty odblokowane.

znajduje się w dokumencie Rozwiązywanie problemów z portów, aby uzyskać szczegółowe informacje na ten problem zbadaniu.

## <a name="next-steps"></a>Kolejne kroki
[Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)


