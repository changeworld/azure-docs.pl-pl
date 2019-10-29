---
title: Zażądaj dostępu do pakietu dostępu w usłudze Azure AD uprawnień do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się, jak za pomocą portalu dostępu uzyskiwać żądania dostępu do pakietu dostępu w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e57a216efee39df0ad22712b281073a68600e0df
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990738"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Zażądaj dostępu do pakietu dostępu w usłudze Azure AD uprawnień do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dzięki zarządzaniu prawami usługi Azure AD pakiet dostępu umożliwia jednorazowe skonfigurowanie zasobów i zasad, które automatycznie zarządzają dostępem w okresie istnienia pakietu dostępu. 

Menedżer pakietów dostępu może skonfigurować zasady, aby wymagać zatwierdzenia użytkownikom dostępu do pakietów dostępu. Użytkownik wymagający dostępu do pakietu dostępu może przesłać żądanie uzyskania dostępu. W tym artykule opisano, jak przesłać żądanie dostępu.

## <a name="sign-in-to-the-my-access-portal"></a>Logowanie się do portalu My Access

Pierwszym krokiem jest zalogowanie się w portalu do mojego dostępu, w którym można zażądać dostępu do pakietu dostępu.

**Rola wymagana wstępnie:** Obiektu żądającego

1. Poszukaj wiadomości e-mail lub wiadomości z programu Project lub Business Manager, z którym pracujesz. Wiadomość e-mail powinna zawierać link do pakietu dostępu, do którego będzie potrzebny dostęp. Link rozpoczyna się od `myaccess`, zawiera wskazówkę katalogu i kończą się IDENTYFIKATORem pakietu dostępu.
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otwórz link.

1. Zaloguj się do portalu My Access.

    Upewnij się, że używasz konta organizacji (służbowego). Jeśli nie masz pewności, skontaktuj się z projektem lub menedżerem biznesowym.

## <a name="request-an-access-package"></a>Żądaj pakietu dostępu

Po znalezieniu pakietu dostępu w portalu dostępu można przesłać żądanie.

**Rola wymagana wstępnie:** Obiektu żądającego

1. Znajdź na liście pakiet dostępu.  W razie potrzeby można wyszukać, wpisując ciąg wyszukiwania, a następnie wybierając pozycję **Nazwa**, **wykaz**lub filtr **zasobów** .

    ![Portal dostępu — Wyszukiwanie zasobów](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

1. Kliknij pozycję **Zażądaj dostępu** , aby otworzyć okienko żądania dostępu.

    ![Portal dostępu — dostęp — pakiety](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Jeśli zostanie wyświetlone pole **uzasadnienie biznesowe** , wpisz uzasadnienie dostępu.

1. Jeśli jest włączone **żądanie dla określonego okresu?** , wybierz opcję **tak** lub **nie**.

1. W razie potrzeby określ datę początkową i datę końcową.

    ![Portal dostępu — żądanie dostępu](./media/entitlement-management-shared/my-access-request-access.png)

1. Po zakończeniu kliknij pozycję **Prześlij** , aby przesłać żądanie.

1. Kliknij pozycję **historia żądań** , aby wyświetlić listę żądań i stan.

    Jeśli pakiet dostępu wymaga zatwierdzenia, żądanie jest teraz w stanie oczekiwania na zatwierdzenie.

### <a name="select-a-policy"></a>Wybieranie zasad

Jeśli zażądano dostępu do pakietu dostępu z wieloma zasadami, które mają zastosowanie, może zostać wyświetlony monit o wybranie zasad. Na przykład Menedżer pakietów dostępu może skonfigurować pakiet dostępu z dwiema zasadami dla dwóch grup pracowników wewnętrznych. Pierwsze zasady mogą zezwalać na dostęp przez 60 dni i wymagać zatwierdzenia. Drugie zasady mogą zezwalać na dostęp przez 2 dni i nie wymagać zatwierdzania. Jeśli wystąpi ten scenariusz, należy wybrać zasady, które mają być używane.

![Portal dostępu — dostęp do żądania — wiele zasad](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="cancel-a-request"></a>Anuluj żądanie

W przypadku przesyłania żądania dostępu, gdy żądanie jest nadal w stanie **oczekiwania na zatwierdzenie** , można anulować żądanie.

**Rola wymagana wstępnie:** Obiektu żądającego

1. W portalu mój dostęp po lewej stronie kliknij pozycję **historia żądań** , aby wyświetlić listę żądań i stan.

1. Kliknij link **Wyświetl** dla żądania, które chcesz anulować.

1. Jeśli żądanie jest nadal w stanie **oczekiwania na zatwierdzenie** , możesz kliknąć przycisk **Anuluj żądanie** , aby anulować żądanie.

    ![Portal dostępu — Anuluj żądanie](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknij pozycję **historia żądań** , aby potwierdzić, że żądanie zostało anulowane.

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
