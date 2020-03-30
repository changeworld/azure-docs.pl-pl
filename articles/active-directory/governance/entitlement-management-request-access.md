---
title: Żądanie pakietu dostępu — zarządzanie uprawnieniami usługi Azure AD
description: Dowiedz się, jak korzystać z portalu Mój dostęp, aby zażądać dostępu do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure Active Directory.
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
ms.openlocfilehash: 4b1ccde2f1f92237978ab4e68acaa26393bbb9d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261751"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Żądanie dostępu do pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Dzięki zarządzaniu uprawnieniami usługi Azure AD pakiet dostępu umożliwia jednorazową konfigurację zasobów i zasad, które automatycznie administrują dostępem przez cały okres ważności pakietu dostępu. 

Menedżer pakietów dostępu można skonfigurować zasady, aby wymagać zatwierdzenia dla użytkowników, aby mieć dostęp do pakietów dostępu. Użytkownik, który potrzebuje dostępu do pakietu dostępu można przesłać żądanie, aby uzyskać dostęp. W tym artykule opisano sposób przesyłania żądania dostępu.

## <a name="sign-in-to-the-my-access-portal"></a>Logowanie się do portalu Mój dostęp

Pierwszym krokiem jest zalogowanie się do portalu Mój dostęp, gdzie można zażądać dostępu do pakietu dostępu.

**Rola wstępna:** Żądającego

1. Poszukaj wiadomości e-mail lub wiadomości od projektu lub menedżera firmy, z którą pracujesz. Wiadomość e-mail powinna zawierać łącze do pakietu dostępu, do którego będziesz potrzebować dostępu. Łącze rozpoczyna `myaccess`się od , zawiera wskazówkę katalogu, a kończy się identyfikatorem pakietu dostępu.  (W przypadku instytucji rządowych `https://myaccess.microsoft.us` i samorządowych domena może być domeną).
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Otwórz łącze.

1. Zaloguj się do portalu Mój dostęp.

    Upewnij się, że korzystasz z konta instytucji (służbowego) w tej instytucji. Jeśli nie masz pewności, skontaktuj się z menedżerem projektu lub firmy.

## <a name="request-an-access-package"></a>Żądanie pakietu dostępu

Po znalezieniu pakietu dostępu w portalu Mój dostęp można przesłać żądanie.

**Rola wstępna:** Żądającego

1. Znajdź pakiet dostępu na liście.  W razie potrzeby można wyszukiwać, wpisując ciąg wyszukiwania, a następnie wybierając filtr **Nazwa,** **Katalog**lub **Zasoby.**

    ![Mój portal dostępu — wyszukiwanie zasobów](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

1. Kliknij **pozycję Poproś o dostęp,** aby otworzyć okienko Dostęp żądania.

    ![Mój portal dostępu — pakiety dostępu](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Jeśli jest wyświetlane pole **Uzasadnienie biznesowe,** wpisz uzasadnienie potrzeby dostępu.

1. Jeśli **opcja Żądanie na określony okres jest** włączona, wybierz opcję **Tak** lub **Nie**.

1. W razie potrzeby określ datę rozpoczęcia i datę zakończenia.

    ![Mój portal dostępu — prośba o dostęp](./media/entitlement-management-shared/my-access-request-access.png)

1. Po zakończeniu kliknij przycisk **Prześlij,** aby przesłać zgłoszenie.

1. Kliknij **pozycję Historia żądań,** aby wyświetlić listę żądań i stan.

    Jeśli pakiet dostępu wymaga zatwierdzenia, żądanie jest teraz w stanie oczekującego zatwierdzenia.

### <a name="select-a-policy"></a>Wybieranie zasad

Jeśli zażądasz dostępu do pakietu dostępu, który ma wiele zasad, które mają zastosowanie, może zostać poproszony o wybranie zasad. Na przykład menedżer pakietów dostępu może skonfigurować pakiet dostępu z dwiema zasadami dla dwóch grup pracowników wewnętrznych. Pierwsza zasada może zezwalać na dostęp przez 60 dni i wymagać zatwierdzenia. Druga zasada może zezwalać na dostęp przez 2 dni i nie wymaga zatwierdzenia. Jeśli napotkasz ten scenariusz, musisz wybrać zasady, których chcesz użyć.

![Mój portal dostępu — żądanie dostępu — wiele zasad](./media/entitlement-management-request-access/my-access-multiple-policies.png)

## <a name="resubmit-a-request"></a>Ponowne przesłanie żądania

Gdy zażądasz dostępu do pakietu dostępu, żądanie może zostać odrzucone lub żądanie może wygasnąć, jeśli osoby zatwierdzające nie odpowiedzą na czas. Jeśli potrzebujesz dostępu, możesz spróbować ponownie i ponownie przesłać żądanie. W poniższej procedurze wyjaśniono, jak ponownie przesłać żądanie dostępu:

**Rola wstępna:** Żądającego

1. Zaloguj się do portalu **Mój dostęp.**

1. Kliknij **pozycję Żądaj historii** z menu nawigacji po lewej stronie.

1. Znajdź pakiet dostępu, dla którego ponownie należy przesłać żądanie.

1. Kliknij znacznik wyboru, aby wybrać pakiet dostępu.

1. Kliknij niebieskie **łącze Widok** po prawej stronie wybranego pakietu dostępu.
    
    ![Wybierz pakiet dostępu i łącze widoku](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Okienko zostanie otwarte po prawej stronie z historią żądań dla pakietu dostępu.
    
    ![Wybierz przycisk Prześlij ponownie](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Kliknij przycisk **Prześlij ponownie** u dołu okienka.

## <a name="cancel-a-request"></a>Anulowanie żądania

Jeśli prześlesz żądanie dostępu, a żądanie jest nadal w stanie **oczekującego zatwierdzenia,** możesz anulować żądanie.

**Rola wstępna:** Żądającego

1. W portalu Mój dostęp po lewej stronie kliknij pozycję **Historia żądań,** aby wyświetlić listę żądań i stan.

1. Kliknij łącze **Wyświetl** żądanie, które chcesz anulować.

1. Jeśli żądanie jest nadal w stanie **oczekującego zatwierdzenia,** można kliknąć **przycisk Anuluj żądanie,** aby anulować żądanie.

    ![Mój portal dostępu — anulowanie żądania](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kliknij **pozycję Historia żądań,** aby potwierdzić, że żądanie zostało anulowane.

## <a name="next-steps"></a>Następne kroki

- [Zatwierdzanie lub odmawianie żądań dostępu](entitlement-management-request-approve.md)
- [Proces żądania i powiadomienia e-mail](entitlement-management-process.md)
