---
title: Zatwierdzanie lub odmawianie żądań dostępu — zarządzanie uprawnieniami usługi Azure AD
description: Dowiedz się, jak za pomocą portalu Mój dostęp zatwierdzać lub odrzucać żądania do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261725"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Zatwierdzanie lub odmawianie żądań dostępu w zarządzaniu uprawnieniami usługi Azure AD

Za pomocą zarządzania uprawnieniami usługi Azure AD można skonfigurować zasady wymagające zatwierdzenia dla pakietów dostępu i wybrać jedną lub więcej osób zatwierdzających. W tym artykule opisano, jak wyznaczone osoby zatwierdzające mogą zatwierdzać lub odrzucać żądania dotyczące pakietów dostępu.

## <a name="open-request"></a>Otwórz żądanie

Pierwszym krokiem do zatwierdzania lub odrzucania żądań dostępu jest znalezienie i otwarcie żądania dostępu oczekującego na zatwierdzenie. Istnieją dwa sposoby otwierania żądania dostępu.

**Rola wstępna:** Osoba zatwierdzająca

1. Poszukaj wiadomości e-mail z platformy Microsoft Azure z prośbą o zatwierdzenie lub odrzucenie żądania. Oto przykładowy adres e-mail:

    ![Zatwierdzanie żądania dostępu do wiadomości e-mail pakietu](./media/entitlement-management-shared/approver-request-email.png)

1. Kliknij **łącze Zatwierdź lub odmów żądanie,** aby otworzyć żądanie dostępu.

1. Zaloguj się do portalu Mój dostęp.

Jeśli nie masz wiadomości e-mail, możesz znaleźć żądania dostępu oczekujące na zatwierdzenie, wykonując następujące kroki.

1. Zaloguj się do portalu [https://myaccess.microsoft.com](https://myaccess.microsoft.com)Mój dostęp pod adresem .  (Dla instytucji rządowych stanów Zjednoczonych domeną `myaccess.microsoft.us`w łączu portalu Mój dostęp będzie domena .)

1. W menu po lewej stronie kliknij pozycję **Zatwierdzenia,** aby wyświetlić listę żądań dostępu oczekujących na zatwierdzenie.

1. Na karcie **Oczekujące** znajdź żądanie.

## <a name="approve-or-deny-request"></a>Zatwierdzanie lub odrzucanie żądania

Po otwarciu żądania dostępu oczekującego na zatwierdzenie można wyświetlić szczegółowe informacje, które pomogą Ci podjąć decyzję o zatwierdzeniu lub odrzuceniu.

**Rola wstępna:** Osoba zatwierdzająca

1. Kliknij łącze **Widok,** aby otworzyć okienko żądania programu Access.

1. Kliknij **przycisk Szczegóły,** aby wyświetlić szczegółowe informacje o żądaniu dostępu.

    Szczegóły obejmują nazwę użytkownika, organizację, datę rozpoczęcia i zakończenia dostępu, jeśli podano, uzasadnienie biznesowe, kiedy żądanie zostało przesłane i kiedy żądanie wygaśnie.

1. Kliknij **przycisk Zatwierdź** lub **Odmów**.

1. W razie potrzeby wprowadź przyczynę.

    ![Mój portal dostępu — żądanie dostępu](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Kliknij **przycisk Prześlij,** aby przesłać swoją decyzję.

    Jeśli zasada jest skonfigurowana z wieloma osobami zatwierdzających, tylko jedna osoba zatwierdzająca musi podjąć decyzję o oczekującej zatwierdzeniu. Po przesłaniu decyzji przez osobę zatwierdzającej do żądania dostępu żądanie jest zakończone i nie jest już dostępne dla innych osób zatwierdzających, aby zatwierdzić lub odrzucić żądanie. Inne osoby zatwierdzające mogą zobaczyć decyzję żądania i decydenta w portalu Mój dostęp. W tej chwili obsługiwane jest tylko jednoetapowe zatwierdzenie.

    Jeśli żadna ze skonfigurowanych osób zatwierdzających nie jest w stanie zatwierdzić lub odrzucić żądania dostępu, żądanie wygasa po czasie trwania skonfigurowanego żądania. Użytkownik otrzymuje powiadomienie, że ich żądanie dostępu wygasło i że musi ponownie przesłać żądanie dostępu.

## <a name="next-steps"></a>Następne kroki

- [Żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
