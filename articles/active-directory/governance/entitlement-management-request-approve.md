---
title: Zatwierdzanie lub odrzucanie żądań dostępu w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak zatwierdzić lub odrzucić żądania do pakietu dostępu przy użyciu portalu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
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
ms.openlocfilehash: da3e1b6439b033742af62d548775cfb628e955b7
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990702"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Zatwierdzanie lub odrzucanie żądań dostępu w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Korzystając z usługi Azure AD uprawnia do zarządzania, można skonfigurować zasady, aby wymagały zatwierdzenia pakietów dostępu, i wybrać co najmniej jedną osobę zatwierdzającą. W tym artykule opisano, jak Wyznaczeni osoby zatwierdzające mogą zatwierdzać lub odrzucać żądania dostępu do pakietów.

## <a name="open-request"></a>Otwórz żądanie

Pierwszym krokiem w celu zatwierdzenia lub odmowy żądań dostępu jest znalezienie i otwarcie żądania dostępu oczekujące na zatwierdzenie. Istnieją dwa sposoby otwierania żądania dostępu.

**Rola wymagana wstępnie:** Osoby zatwierdzającej

1. Poszukaj wiadomości e-mail z Microsoft Azure, która prosi o zatwierdzenie lub odrzucenie żądania. Oto przykład wiadomości e-mail:

    ![Zatwierdź żądanie uzyskania dostępu do poczty e-mail pakietu](./media/entitlement-management-shared/email-approve-request.png)

1. Kliknij link **Zatwierdź lub Odmów żądania** , aby otworzyć żądanie dostępu.

1. Zaloguj się do portalu My Access.

Jeśli nie masz wiadomości e-mail, możesz znaleźć żądania dostępu oczekujące na zatwierdzenie, wykonując następujące kroki.

1. Zaloguj się do portalu My Access w [https://myaccess.microsoft.com](https://myaccess.microsoft.com).

1. W menu po lewej stronie kliknij pozycję **zatwierdzenia** , aby wyświetlić listę żądań dostępu oczekujących na zatwierdzenie.

1. Na karcie **oczekiwanie** Znajdź żądanie.

## <a name="approve-or-deny-request"></a>Zatwierdź lub Odmów żądania

Po otwarciu żądania dostępu oczekującego na zatwierdzenie możesz zobaczyć szczegóły, które ułatwią zatwierdzenie lub odrzucenie decyzji.

**Rola wymagana wstępnie:** Osoby zatwierdzającej

1. Kliknij link **Wyświetl** , aby otworzyć okienko żądanie dostępu.

1. Kliknij przycisk **szczegóły** , aby wyświetlić szczegółowe informacje o żądaniu dostępu.

    Szczegóły obejmują imię i nazwisko użytkownika, organizację, datę rozpoczęcia dostępu i daty zakończenia, jeśli zostały podane, uzasadnienie biznesowe, czas przesyłania żądania oraz czas wygaśnięcia żądania.

1. Kliknij przycisk **Zatwierdź** lub **Odmów**.

1. W razie potrzeby wprowadź przyczynę.

    ![Portal dostępu moje dostęp — żądanie dostępu](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Kliknij pozycję **Prześlij** , aby przesłać swoją decyzję.

    Jeśli zasady są skonfigurowane z wieloma osobami zatwierdzającymi, tylko jedna osoba zatwierdzająca musi podejmować decyzje dotyczące oczekujących zatwierdzeń. Gdy osoba zatwierdzająca przesłała swoją decyzję do żądania dostępu, żądanie zostanie zakończone i nie jest już dostępne dla innych osób zatwierdzających do zatwierdzenia lub odrzucenia żądania. Inne osoby zatwierdzające mogą zobaczyć decyzję o żądaniu i twórcę decyzji w portalu mojego dostępu. W tej chwili obsługiwane jest tylko zatwierdzanie jednoetapowe.

    Jeśli żadna ze skonfigurowanych osób zatwierdzających nie może zatwierdzić lub odrzucić żądania dostępu, żądanie wygasa po upływie skonfigurowanego czasu trwania żądania. Użytkownik otrzymuje powiadomienie o wygaśnięciu żądania dostępu i konieczności ponownego przesłania żądania dostępu.

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
