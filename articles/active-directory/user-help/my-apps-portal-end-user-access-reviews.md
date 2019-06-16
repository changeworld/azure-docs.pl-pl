---
title: Wykonywanie przeglądu dostępu z poziomu portalu Moje aplikacje — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlać i zarządzać nimi zabezpieczenia dostępu do aplikacji i grup w organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482884"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Wykonywanie przeglądu dostępu z poziomu portalu Moje aplikacje
Za pomocą swojego konta firmowego lub szkolnego oparta na sieci web **Moje aplikacje** portalu, aby wyświetlić i uruchomić wiele organizacji aplikacji w chmurze, aby zaktualizować niektóre z Twoich informacji profilu i konta, aby wyświetlić swoje **grup** informacji i wykonywać **przeglądów dostępu** dla aplikacji i grup. Jeśli nie masz dostępu do **Moje aplikacje** portal, należy skontaktować się z pomocą techniczną o uprawnienia.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ta zawartość jest przeznaczona dla użytkowników. Jeśli jesteś administratorem, możesz znaleźć więcej informacji na temat sposobu konfigurowania i zarządzania nimi aplikacji opartych na chmurze w [dokumentacja dotycząca zarządzania aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Zarządzanie przeglądów dostępu
Jeśli administrator przyznał Ci uprawnienia do wykonywania własnych przeglądów dostępu, możesz zarządzać dostępu grupy lub aplikacje z **przeglądów dostępu** kafelków na **Moje aplikacje** strony portalu.

>[!Note]
>Jeśli nie widzisz **przeglądów dostępu** sąsiadująco w obu oznacza, że nie masz uprawnień do przeprowadzenia przeglądów dostępu lub że nie masz żadnych oczekujących przeglądów oczekiwanie na zatwierdzenie. Jeśli uważasz, że powinni mieć dostęp do kafelka, skontaktuj się z pomocy technicznej.

### <a name="to-perform-your-access-reviews"></a>Aby wykonać swoje przeglądów dostępu

1.  Zaloguj się do swojego konta firmowego lub szkolnego.

2.  Otwórz przeglądarkę internetową i przejdź do https://myapps.microsoft.com, albo użyj linku udostępnioną przez Twoją organizację. Na przykład możesz mogą być kierowane do dostosowanej strony dla całej organizacji, takich jak https://myapps.microsoft.com/contoso.com.

    **Aplikacje** zostanie wyświetlona strona, przedstawiający wszystkie aplikacje oparte na chmurze własnością Twojej organizacji i dostępna do użycia.

    ![Strona aplikacji w portalu Moje aplikacje](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Wybierz **przeglądów dostępu** Kafelek, aby wyświetlić listę dostępu przeglądy oczekiwania na zatwierdzenie.

    ![Przeglądy dostępu dotyczące strony oczekujących przeglądów dostępu w organizacji](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Wybierz **Rozpocznij Przegląd** można uruchomić usługi przeglądu dostępu.

5. Przejrzeć swój dostęp i określ, czy nadal jest wymagane.

    ![Strona przeglądu dostępu, przedstawiający szczegóły przeglądu](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Jeśli jesteś administratorem, a dozwolone, aby zapoznać się z Twojej organizacji dostęp do grup i aplikacji, zobaczysz innej strony. Aby uzyskać więcej informacji na temat przeglądania grup lub aplikacje dla całej organizacji, zobacz [Przegląd dostępu wszystkich użytkowników do grupy lub aplikacji w przeglądach dostępu usługi Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Wybierz **tak** zapewnienie dostępu do usługi lub **nie** o usunięcie Twojego dostępu.

    Jeśli wybierzesz **tak**, może być konieczne określenie uzasadnienie w **Przyczyna** pole.

    ![Dostęp do przeglądu strona, wyświetlająca pole przyczyny z przykładowym tekstem](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Wybierz **przesłać**.

    Dostęp do zapoznania się z nimi zostało ukończone i powrócić do **Moje aplikacje** portalu.

    >[!Note]
    >Dostęp do usługi można zmienić w dowolnym momencie przed dostępu Przejrzyj od zakończenia okresu. Jeśli usuniesz Ci dostęp do aplikacji lub grupie, go nie jest usuwany natychmiast. Usunięcie ma miejsce, gdy dostęp Przejrzyj od zakończenia okresu lub gdy administrator zamyka przeglądu. 

## <a name="next-steps"></a>Kolejne kroki

- [Dostęp i korzystać z aplikacji w portalu Moje aplikacje](my-apps-portal-end-user-access.md).

- [Zmiana informacji o Twoim profilu](my-apps-portal-end-user-update-profile.md).

- [Wyświetl i zaktualizuj swoje informacje związane z grupami](my-apps-portal-end-user-groups.md).