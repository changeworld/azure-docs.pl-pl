---
title: Warunki użytkowania usługi Azure Active Directory | Microsoft Docs
description: Warunki użytkowania usługi Azure AD pozwalają administratorom i firmom udostępniać użytkownikom warunki użytkowania usług Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 06/29/2018
ms.author: rolyon
ms.openlocfilehash: f22190d81d8ffbb91e9573974d93d4b1ad35af18
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444348"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkcja Warunki użytkowania usługi Azure Active Directory
Warunki użytkowania usługi Azure AD pozwalają organizacjom przedstawiać informacje użytkownikom końcowym w prosty sposób. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano, jak zacząć korzystać z warunków użytkowania usługi Azure AD.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>Do czego można wykorzystać warunki użytkowania?
Warunki użytkowania usługi Azure AD umożliwiają wykonanie następujących czynności:
- Wymaganie od pracowników i gości zgody na Twoje warunki użytkowania przed uzyskaniem dostępu.
- Prezentowanie ogólnych warunków użytkowania obowiązujących wszystkich użytkowników w organizacji.
- Prezentowanie specyficznych warunków użytkowania zależnych od atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](active-directory-groups-dynamic-membership-azure-portal.md).
- Prezentowanie specyficznych warunków użytkowania podczas korzystania z aplikacji mających duży wpływ na działalność biznesową, takich jak Salesforce.
- Prezentowanie warunków użytkowania w innych językach.
- Wyświetlanie listy użytkowników, którzy wyrazili lub nie wyrazili zgody na warunki użytkowania.
- Wyświetlanie dziennika inspekcji działania warunków użytkowania.

## <a name="prerequisites"></a>Wymagania wstępne
Do skonfigurowania warunków użytkowania usługi Azure AD niezbędne są następujące elementy:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
    - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
    - Administrator globalny
    - Administrator zabezpieczeń
    - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Zawartość warunków użytkowania usługi Azure AD jest przedstawiana w formacie PDF. Plik PDF zawartość może być dowolna, takich jak istniejące dokumenty kontraktowe, umożliwiając gromadzenie przez użytkownika końcowego, który Zaloguj się do niego umów podczas użytkownika. Zalecany rozmiar czcionki w dokumencie PDF to 24.

## <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania
Po zakończeniu pracy nad zawartością dokumentu z warunkami użytkowania dodaj go za pomocą poniższej procedury.

1. Zaloguj się jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.

1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Blok Warunki użytkowania](media/active-directory-tou/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

    ![Dodawanie warunków użytkowania](media/active-directory-tou/new-tou.png)

1. W polu **Nazwa** wprowadź nazwę warunków użytkowania.

2. W polu **Nazwa wyświetlana** wprowadź nazwę wyświetlaną.  Ten nagłówek będzie widoczny dla użytkowników po zalogowaniu.

3. **Przejdź** do pliku PDF zawierającego końcową wersję warunków użytkowania i wybierz go.

4. **Wybierz** język warunków użytkowania.  Opcja wyboru języka umożliwia przekazanie wielu wersji językowych warunków użytkowania.  Wersja warunków użytkowania widoczna dla użytkownika końcowego będzie zależała od preferencji jego przeglądarki.

5. Włącz lub wyłącz ustawienie **Wymagaj od użytkowników rozwinięcia warunków użytkowania**.  Jeśli to ustawienie jest włączone, użytkownicy końcowi będą musieli przejrzeć warunki użytkowania przed ich akceptacją.

6. W obszarze **Dostęp warunkowy** możesz **wymusić** stosowanie przekazanych warunków użytkowania, wybierając z listy rozwijanej szablon lub niestandardowe zasady dostępu warunkowego.  Niestandardowe zasady dostępu warunkowego pozwalają zwiększyć szczegółowość warunków użytkowania, które mogą dotyczyć pojedynczej aplikacji w chmurze lub grupy użytkowników.  Aby uzyskać więcej informacji, zobacz [Konfigurowanie zasad dostępu warunkowego](active-directory-conditional-access-best-practices.md).

    >[!IMPORTANT]
    >Kontrolki zasad dostępu warunkowego (w tym warunki użytkowania) nie obsługują wymuszania dla kont usługi.  Zaleca się wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

7. Kliknij przycisk **Utwórz**.

8. W przypadku wybrania niestandardowego szablonu dostępu warunkowego pojawi się nowy ekran, na którym można dostosować zasady dostępu warunkowego.

    Zostaną wyświetlone nowe warunki użytkowania.

    ![Dodawanie warunków użytkowania](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>Wyświetlanie użytkowników, który zaakceptowali i odrzucili warunki użytkowania
W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczby oraz lista użytkowników, którzy zaakceptowali lub odrzucili warunki użytkowania, jest przechowywana przez cały okres istnienia warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Zdarzenie inspekcji](media/active-directory-tou/view-tou.png)

1. Możesz kliknąć liczby w obszarze **Zaakceptowane** lub **Odrzucone**, aby wyświetlić bieżący stan dla użytkowników.

    ![Zdarzenie inspekcji](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>Wyświetlanie dzienników inspekcji
Jeśli chcesz wyświetlić dodatkową aktywność, warunki użytkowania usługi Azure AD zawierają dzienniki inspekcji. Każdy zgody użytkownika wyzwala zdarzenie w dziennikach inspekcji, które są przechowywane przez 30 dni. Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z dziennikami inspekcji, użyj następującej procedury:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Zdarzenie inspekcji](media/active-directory-tou/audit-tou.png)

1. Na ekranie dzienników inspekcji usługi Azure AD możesz filtrować informacje przy użyciu list rozwijanych, aby uzyskać konkretne dane z dzienników inspekcji.

    ![Zdarzenie inspekcji](media/active-directory-tou/audit-logs-tou.png)

1. Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

## <a name="what-terms-of-use-looks-like-for-users"></a>W jaki sposób warunki użytkowania są wyświetlane użytkownikom
Po utworzeniu warunków użytkowania i wymuszane, użytkownicy, którzy znajdują się w zakresie, zostanie wyświetlony następujący ekran podczas logowania.

![Zdarzenie inspekcji](media/active-directory-tou/user-tou.png)

Na poniższym ekranie przedstawiono, w jaki sposób warunki użytkowania są wyświetlane na urządzeniach przenośnych.

![Zdarzenie inspekcji](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>Jak użytkownicy mogą przeglądać warunki użytkowania
Użytkownicy mogą wyświetlać i przeglądać warunki użytkowania, które zaakceptowali, korzystając z poniższej procedury.

1. Zaloguj się do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

1. W prawym górnym rogu kliknij swoją nazwę i wybierz pozycję **Profil** z listy rozwijanej.

    ![Profil](media/active-directory-tou/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

    ![Zdarzenie inspekcji](media/active-directory-tou/tou13a.png)

1. Następnie możesz przejrzeć zaakceptowane warunki użytkowania. 

## <a name="delete-terms-of-use"></a>Usuwanie warunków użytkowania
Stare warunki użytkowania można usunąć, korzystając z poniższej procedury.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Wybierz warunki użytkowania, które chcesz usunąć.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

    ![Dodawanie warunków użytkowania](media/active-directory-tou/delete-tou.png)

    Warunki użytkowania nie powinny już być widoczne.

## <a name="deleted-users-and-active-terms-of-use"></a>Usunięci użytkownicy i aktywne warunki użytkowania
Domyślnie usunięty użytkownik jest w stanie usunięcia w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby.  Po 30 dniach użytkownik jest trwale usuwany.  Ponadto przy użyciu portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](fundamentals/active-directory-users-restore.md) przed upłynięciem tego okresu.  Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych warunków użytkowania.  Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="policy-changes"></a>Zmiany zasad
Zasady dostępu warunkowego zaczynają obowiązywać natychmiast. W takim przypadku administrator będzie wyświetlane "smutne chmury" lub "Problemy z tokenem usługi Azure AD". Administrator musi się wylogować i zaloguj się ponownie, aby spełnić wymagania nowych zasad.

>[!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
> - dla warunków użytkowania włączono zasady dostępu warunkowego
> - lub utworzono drugą wersję warunków użytkowania

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt. Jak sprawdzić, czy i kiedy użytkownik zaakceptował warunki użytkowania?**</br>
Odp.: zgodnie z wymaganiami bloku użycia, kliknij liczbę w obszarze **zaakceptowano**. Można również wyświetlić lub zaakceptuj działań związanych z wyszukiwaniem w dziennikach inspekcji. Aby uzyskać więcej informacji, zobacz [widoku, który ma zaakceptowali i odrzucili](#view-who-has-accepted-and-declined) i [Wyświetl dzienniki inspekcji](#view-audit-logs).

**Pyt. czy zmiana warunków użytkowania wymaga ich użytkowników o zaakceptowanie ponownie?**</br>
Odp. tak, administrator może zmienić warunków użytkowania, a użytkownicy muszą ponownie zaakceptowali nowe warunki.

**Pyt. Czy warunki użytkowania obsługują wiele języków?**</br>
Odp. Tak.  Obecnie istnieje 18 różnych języków, które administrator może skonfigurować na potrzeby pojedynczych warunków użytkowania. 

**Pyt. Kiedy są wyzwalane warunki użytkowania?**</br>
Odp. Warunki użytkowania są wyzwalane podczas logowania.

**Pyt. Jakie aplikacje mogą zostać objęte warunkami użytkowania?**</br>
Odp. Dla aplikacji przedsiębiorstwa można utworzyć zasady dostępu warunkowego korzystające z nowoczesnego uwierzytelniania.  Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./manage-apps/view-applications-portal.md).

**Pyt. Czy dla użytkownika lub aplikacji można określić wiele warunków użytkowania?**</br>
Odp. Tak, można to zrobić, tworząc różne wersje zasad dostępu warunkowego obejmujących grupy lub aplikacje. Jeśli danego użytkownika dotyczy wiele warunków użytkowania, musi on zaakceptować je pojedynczo.
 
**Pyt. Co się stanie, jeśli użytkownik odrzuci warunki użytkowania?**</br>
Odp. Dostęp do aplikacji zostanie zablokowany dla tego użytkownika. Użytkownik musi zalogować się ponownie i zaakceptować warunki, aby uzyskać dostęp.
 
**P: czy istnieje możliwość unaccept warunki użytkowania, które zostały wcześniej zaakceptowane?**</br>
Odp.: możesz [przeglądu wcześniej zaakceptowane warunki użytkowania](#how-users-can-review-their-terms-of-use), ale obecnie nie istnieje sposób unaccept.
 
**Pyt. Jak długo są przechowywane informacje?**</br>
Odp. Liczba użytkowników oraz lista użytkowników, którzy zaakceptowali lub odrzucili warunki użytkowania, jest przechowywana przez cały okres istnienia warunków użytkowania. Dzienniki inspekcji są przechowywane przez 30 dni.
