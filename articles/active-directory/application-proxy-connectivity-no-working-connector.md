---
title: Znaleziono żadnej grupy łącznika pracy dla aplikacji serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Rozwiąż problemy, które mogą wystąpić po nie pracy łącznika w grupie łącznika dla aplikacji z serwer Proxy aplikacji usługi Azure AD
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
ms.openlocfilehash: f6baea8c2702fc2a86c75d32c7b4ca958fd913b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591749"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nie znaleziono aplikacji serwera Proxy aplikacji grupy łącznika pracy

Ten artykuł pomaga rozwiązać typowe problemy, które muszą ponieść gdy nie jest łącznik wykryto dla aplikacji serwera Proxy aplikacji zintegrowany z usługą Azure Active Directory.

## <a name="overview-of-steps"></a>Omówienie kroków
Jeśli nie ma żadnych pracy łącznika w grupie łącznika dla aplikacji, istnieje kilka sposobów, aby rozwiązać ten problem:

-   Jeśli nie łączniki do grupy, można:

    -   Pobierz nowy łącznik na serwerze lokalnego prawo i przypisz je do tej grupy

    -   Przenieś łącznika usługi active do grupy

-   Jeśli nie łączniki active w grupie, można:

    -   Zidentyfikowanie przyczyny, dla której Twojego łącznika jest nieaktywny i rozwiązanie

    -   Przenieś łącznika usługi active do grupy

Aby zdiagnozować problem, otwórz menu "Serwer Proxy aplikacji" w aplikacji i przyjrzyj komunikat ostrzegawczy grupy łącznika. Jeśli w grupie nie żadne łączniki, komunikat ostrzegawczy Określa, że grupa wymaga co najmniej jeden łącznik. Jeśli nie aktywne łączników, komunikat ostrzegawczy wyjaśniono, że. Jest często mają łączniki nieaktywne. 

   ![Wybór grupy łącznika w portalu Azure](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Aby uzyskać szczegółowe informacje na każdej z tych opcji zobacz odpowiedniej sekcji poniżej. Instrukcjach przyjęto założenie, że uruchamiasz na stronie zarządzania łącznika. Jeśli jest wyświetlany komunikat o błędzie powyżej, można przejść do tej strony, klikając w komunikacie ostrzegawczym. Można także uzyskać ze stroną, przechodząc do **usługi Azure Active Directory**, klikając pozycję na **aplikacje dla przedsiębiorstw**, następnie **serwera Proxy aplikacji.**

   ![Łącznik grupy zarządzania w portalu Azure](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Pobierz nowy łącznik

Aby pobrać nowy łącznik, przycisk "Pobierz łącznik" w górnej części strony.

Zainstaluj łącznik na maszynie z bezpośredniego procesów of wiersza do wewnętrznej bazy danych aplikacji. Zazwyczaj łącznik jest zainstalowany na tym samym serwerze co aplikacja. Po pobraniu, łącznik powinna pojawić się w tym menu. Kliknij łącznik i używając listy rozwijanej "łącznik grupy" Upewnij się, że należy on do grupy prawo. Zapisz zmiany.

   ![Pobierz łącznik z portalu Azure](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Przenieś łącznika usługi Active

Jeśli masz łącznika usługi active powinna należeć do grupy i ma procesów z wiersza do zaplecza aplikacji docelowej, można przenieść łącznika w przypisanej grupie. Aby to zrobić, kliknij przycisk łącznika. W polu "Łącznik grupy" Użyj listy rozwijanej wybierz poprawną grupę, a następnie kliknij przycisk Zapisz.

## <a name="resolve-an-inactive-connector"></a>Rozwiąż nieaktywne łącznika

Jeśli nie są aktywne tylko łączników w grupie, prawdopodobnie na komputerze, na którym nie ma wszystkie niezbędne porty odblokowany.

znaleźć w dokumencie Rozwiązywanie problemów dotyczących portów szczegółowe badanie tego problemu.

## <a name="next-steps"></a>Kolejne kroki
[Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-connectors.md)


