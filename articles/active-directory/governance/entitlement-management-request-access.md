---
title: Żądanie dostępu do pakietu dostępu w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Dowiedz się, jak korzystać z portalu Moje dostęp na żądanie dostępu do pakietu dostępu w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541558"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Żądanie dostępu do pakietu dostępu w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Zaloguj się do portalu Moje dostępu

Pierwszym krokiem jest zalogować się do portalu Moje dostępu, których może zażądać dostępu do pakietu dostępu.

**Rola wymagań wstępnych:** Obiekt żądający

1. Wyszukaj wiadomości e-mail lub wiadomości z Menedżer projektu lub business, którą pracujesz. Wiadomość e-mail powinna zawierać łącze do pakietu dostępu, który jest potrzebny dostęp do. Łącze rozpoczyna się od:

    `https://myaccess.microsoft.com`

1. Otwórz link.

1. Zaloguj się do portalu Moje dostępu.

    Upewnij się, że używasz konta organizacyjnego. Jeśli nie wiesz, możesz skontaktować się z Menedżer projektu lub business.

## <a name="request-an-access-package"></a>Żądanie dostępu do pakietu

Po znalezieniu pakiet dostępu w portalu Moje dostępu, możesz przesłać żądanie.

**Rola wymagań wstępnych:** Obiekt żądający

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

    ![Moje portalu dostępu — dostęp do pakietów](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kliknij przycisk **żądania dostępu** aby otworzyć okienko żądania dostępu.

1. Jeśli **uzasadnienie biznesowe** zostanie wyświetlone okno, wpisz uzasadnienie konieczności uzyskiwania dostępu.

1. Jeśli **żądanie dla określonego okresu?** jest włączony, wybierz opcję **tak** lub **nie**.

1. W razie potrzeby określ daty rozpoczęcia i Data zakończenia.

    ![Moje portalu dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Po zakończeniu kliknij przycisk **przesyłania** Aby przesłać żądanie.

1. Kliknij przycisk **Historia żądań** umożliwia wyświetlenie listy wykonanych żądań i stanów.

    Jeśli pakiet dostępu wymaga zatwierdzenia, żądanie jest obecnie w stanie oczekuje na zatwierdzenie.

## <a name="cancel-a-request"></a>Anulowanie żądania

Prześlij żądanie dostępu, jeśli żądanie nadal znajduje się w **oczekujące na zatwierdzenie** stanu, można anulować żądania.

**Rola wymagań wstępnych:** Obiekt żądający

1. W portalu Moje dostępu po lewej stronie, kliknij przycisk **Historia żądań** umożliwia wyświetlenie listy wykonanych żądań i stanów.

1. Kliknij przycisk **widoku** link do żądania, aby anulować.

1. Jeśli żądanie nadal znajduje się w **oczekujące na zatwierdzenie** stanu, możesz kliknąć pozycję **żądanie anulowania** można anulować żądania.

    ![Moje portalu dostępu — żądanie anulowania](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknij przycisk **Historia żądań** aby upewnić się, żądanie zostało anulowane.

## <a name="select-a-policy"></a>Wybieranie zasad

Jeśli zażądano dostępu do pakietu dostępu, który ma wiele zasad, które są stosowane, może być proszony o wybranie zasad. Na przykład dostęp do Menedżera pakietów może skonfigurować pakiet dostępu przy użyciu dwóch zasad na dwie grupy pracowników wewnętrznych. Pierwszy zasad może zezwolić na dostęp przez 60 dni i wymagają zatwierdzenia. Drugie zasady mogą zezwolić na dostęp do 2 dni i nie wymagają zatwierdzenia. Jeśli napotkasz ten scenariusz, należy wybrać zasad które chcesz użyć.

**Rola wymagań wstępnych:** Obiekt żądający

## <a name="next-steps"></a>Kolejne kroki

- [Zatwierdź lub Odrzuć żądania dostępu](entitlement-management-request-approve.md)
- [Żądanie procesu i powiadomień e-mail](entitlement-management-process.md)
