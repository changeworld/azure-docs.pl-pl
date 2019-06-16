---
title: Zatwierdź lub Odrzuć żądania dostępu w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak korzystać z portalu Moje dostępu akceptują lub odrzucają żądania pakietu dostępu w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541528"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Zatwierdź lub Odrzuć żądania dostępu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Za pomocą funkcji zarządzania uprawnień usługi Azure AD możesz skonfigurować zasady, aby wymagały zatwierdzenia dla dostępu do pakietów i wybrać jeden lub więcej osób zatwierdzających. W tym artykule opisano sposób wyznaczone osoby zatwierdzające mogli zatwierdzać lub odrzucać żądania dotyczące dostępu do pakietów.

## <a name="open-request"></a>Otwórz zapytanie

Pierwszym krokiem, akceptują lub odrzucają żądania dostępu jest znaleźć i otworzyć żądanie dostępu oczekuje na zatwierdzenie. Istnieją dwa sposoby, aby otworzyć żądanie dostępu.

**Rola wymagań wstępnych:** Osoby zatwierdzającej

1. Wyszukaj wiadomości e-mail z programu Microsoft Azure z prośbą o zatwierdzenie lub odrzucenie żądania. Poniżej przedstawiono przykładową wiadomość e-mail:

    ![Zatwierdź żądanie dostęp do poczty e-mail pakiet](./media/entitlement-management-shared/email-approve-request.png)

1. Kliknij przycisk **Zatwierdź lub Odrzuć żądanie** link, aby otworzyć żądanie dostępu.

1. Zaloguj się do portalu Moje dostępu.

Jeśli nie masz wiadomości e-mail można znaleźć żądań dostępu oczekujące na zatwierdzenie, wykonaj następujące czynności.

1. Zaloguj się do portalu Moje dostępu pod adresem [ https://myaccess.microsoft.com ](https://myaccess.microsoft.com).

1. W menu po lewej stronie kliknij **zatwierdzenia** umożliwia wyświetlenie listy żądań dostępu oczekuje na zatwierdzenie.

1. Na **oczekujące** kartę, Znajdź żądania.

## <a name="approve-or-deny-request"></a>Zatwierdź lub Odrzuć żądania

Po otwarciu żądanie dostępu oczekuje na zatwierdzenie, zobaczysz szczegółowe informacje, które pomogą Ci wprowadzić Zatwierdź lub odmówić decyzji.

**Rola wymagań wstępnych:** Osoby zatwierdzającej

1. Kliknij przycisk **widoku** link, aby otworzyć okienko żądania dostępu.

1. Kliknij przycisk **szczegóły** Aby wyświetlić szczegóły dotyczące żądania dostępu.

    Szczegółowe informacje obejmują nazwę użytkownika i organizacji, dostęp datę początkową i końcową, jeśli podano uzasadnienie biznesowe, gdy żądanie zostało przesłane i w przypadku, gdy żądanie wygaśnie.

1. Kliknij przycisk **zatwierdzić** lub **Odmów**.

1. Jeśli to konieczne, podaj przyczynę.

    ![Moje portalu dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kliknij przycisk **przesyłania** przesłać Twoją decyzję.

    Jeśli zasady są skonfigurowane z wieloma osobami zatwierdzającymi, tylko jedna osoba zatwierdzająca musi podjąć decyzję o oczekujących na zatwierdzenie. Po osoba zatwierdzająca zostało przesłane swoją decyzję na żądanie dostępu, żądanie zostanie ukończona i nie jest już dostępny dla osób zatwierdzających zatwierdzić lub odrzucić żądanie. Inne osoby zatwierdzające można zobaczyć decyzja żądania i osobą podejmującą decyzje w portalu Moje dostępu. W tej chwili obsługiwane jest tylko jednego etapu zatwierdzania.

    Jeśli brak skonfigurowanego osób zatwierdzających może zatwierdzić lub odrzucić żądanie dostępu, żądanie wygasa po czasu trwania skonfigurowanego żądania. Użytkownik pobiera powiadomienie wygasło ich żądania dostępu i muszą ponownie przesłać żądanie dostępu.

## <a name="next-steps"></a>Kolejne kroki

- [Żądanie dostępu do pakietów programu access](entitlement-management-request-access.md)
- [Żądanie procesu i powiadomień e-mail](entitlement-management-process.md)
