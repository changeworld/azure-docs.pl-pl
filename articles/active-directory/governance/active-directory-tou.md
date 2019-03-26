---
title: Warunki użytkowania usługi Azure Active Directory | Microsoft Docs
description: W tym artykule opisano, jak rozpocząć pracę, przy użyciu usługi Azure AD z warunkami użytkowania do prezentowania informacji pracowników lub gości, zanim uzyska dostęp.
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
ms.subservice: compliance
ms.date: 03/24/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f6ac8a693e09d0efbac080d7effc6ce21c6a094
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418979"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkcja Warunki użytkowania usługi Azure Active Directory
Warunki użytkowania usługi Azure AD pozwalają organizacjom przedstawiać informacje użytkownikom końcowym w prosty sposób. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano sposób rozpoczęcia pracy z warunkami użytkowania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Filmy wideo z omówieniem

Poniższy klip wideo zawiera krótkie podsumowanie warunków użytkowania.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Aby obejrzeć dodatkowe filmy wideo zobacz:
- [Jak wdrożyć warunki użytkowania usługi Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak wdrażać warunki użytkowania usługi Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Do czego można wykorzystać warunki użytkowania?
Usługa Azure AD z warunkami użytkowania ma następujące możliwości:
- Wymagaj zaakceptowania Twoich warunków użytkowania przed uzyskaniem dostępu do pracowników lub gości.
- Wymagaj pracowników lub gości zaakceptować warunki użytkowania na każdym urządzeniu zanim uzyska dostęp.
- Wymagaj pracowników lub gości zaakceptować warunki użytkowania, zgodnie z cyklicznym harmonogramem.
- Prezentowanie ogólnych warunków użytkowania obowiązujących wszystkich użytkowników w organizacji.
- Prezentowanie specyficznych warunków użytkowania zależnych od atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](../users-groups-roles/groups-dynamic-membership.md).
- Prezentowanie specyficznych warunków użytkowania podczas korzystania z aplikacji mających duży wpływ na działalność biznesową, takich jak Salesforce.
- Prezentowanie warunków użytkowania w innych językach.
- Lista, która ma lub nie zostało zaakceptowane warunki użytkowania.
- Pomaga w osiągnięciu przepisów dotyczących zachowania poufności.
- Wyświetl dziennik postanowień aktywności użycia dotyczących zgodności i inspekcji.
- Tworzenie i zarządzanie nimi za pomocą funkcji warunki [interfejsów API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne
Do skonfigurowania warunków użytkowania usługi Azure AD niezbędne są następujące elementy:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
    - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
    - Administrator globalny
    - Administrator zabezpieczeń
    - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Zawartość warunków użytkowania usługi Azure AD jest przedstawiana w formacie PDF. Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników końcowych podczas logowania. Aby zapewnić obsługę użytkowników na urządzeniach przenośnych, zalecany rozmiar czcionki w pliku PDF jest punktem 24.

## <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania
Po zakończeniu pracy nad zawartością dokumentu z warunkami użytkowania dodaj go za pomocą poniższej procedury.

1. Logowanie do platformy Azure jako globalny, Administrator, Administrator zabezpieczeń lub Administrator dostępu warunkowego.

1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Blok Warunki użytkowania](./media/active-directory-tou/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

    ![Dodawanie warunków użytkowania](./media/active-directory-tou/new-tou.png)

1. W **nazwa** wprowadź nazwę warunków użytkowania, który będzie używany w witrynie Azure portal.

1. W **nazwę wyświetlaną** wprowadź tytuł, którą użytkownicy widzą podczas logowania.

1. Aby uzyskać **dokument z warunkami użytkowania**, przejdź do swojej zawierającego końcową wersję warunków użytkowania PDF i wybierz ją.

1. Wybierz język, dla Twojego dokument z warunkami użytkowania. Opcja wyboru języka umożliwia przekazanie wielu wersji językowych warunków użytkowania. Wersja warunków użytkowania widoczna dla użytkownika końcowego będzie zależała od preferencji jego przeglądarki.

1. Aby wymagać od użytkowników końcowych wyświetlić warunków użytkowania przed ich akceptacją, należy ustawić **Wymagaj od użytkowników rozwinięcia warunków użytkowania** do **na**.

1. Aby wymagać od użytkowników końcowych zaakceptować warunki użytkowania na każdym urządzeniu, które uzyskują dostęp do z, należy ustawić **wymagać od użytkowników akceptacji na każdym urządzeniu** do **na**. Aby uzyskać więcej informacji, zobacz [na urządzenie z warunkami użytkowania](#per-device-terms-of-use).

1. Jeśli chcesz wygasić zgadza się z warunkami użytkowania zgodnie z harmonogramem, ustaw **wygaśnie zgody** do **na**. Jeśli włączona, są wyświetlane dwa ustawienia dodatkowe harmonogramu.

    ![Wygaśnięcie zgód](./media/active-directory-tou/expire-consents.png)

1. Użyj **Wygaś od** i **częstotliwość** ustawień, aby określić harmonogram warunki Użyj wygasanie ważności poświadczeń. W poniższej tabeli przedstawiono wyniki dla kilku przykładowych ustawień:

    | Wygaśnięcie zaczyna się | Częstotliwość | Wynik |
    | --- | --- | --- |
    | Dzisiejsza data  | Miesięczne | Od dzisiaj użytkownicy muszą zaakceptować warunki użytkowania i następnie ponownie zaakceptowali co miesiąc. |
    | W przyszłości  | Miesięczne | Od dzisiaj, użytkownicy muszą zaakceptować warunki użytkowania. W przypadku przyszłych Data wygaśnie zgody i następnie użytkownicy muszą ponownie zaakceptowali co miesiąc.  |

    Na przykład jeśli ustawisz wygasania, począwszy od daty **1 stycznia** i częstotliwość **miesięczne**, Oto jak wygasanie ważności poświadczeń mogą wystąpić w przypadku dwóch użytkowników:

    | Użytkownik | Najpierw zaakceptować daty | Najpierw wygasa dnia | Po drugie wygasa dnia | Trzeci wygasa dnia |
    | --- | --- | --- | --- | --- |
    | Alicja | 1 stycznia | 1 lutego | 1 marca | 1 kwietnia |
    | Bob | 15 stycznia | 1 lutego | 1 marca | 1 kwietnia |

1. Użyj **czas trwania zanim ponownej akceptacji wymaga (w dniach)** ustawienie, aby określić liczbę dni, po którym użytkownik musi ponownie zaakceptowali warunki użytkowania. Dzięki temu użytkownicy mogą postępuj zgodnie z własnym harmonogramem. Na przykład, jeśli zostanie ustawiona na czas trwania **30** dni, Oto jak wygasanie ważności poświadczeń mogą wystąpić w przypadku dwóch użytkowników:

    | Użytkownik | Najpierw zaakceptować daty | Najpierw wygasa dnia | Po drugie wygasa dnia | Trzeci wygasa dnia |
    | --- | --- | --- | --- | --- |
    | Alicja | 1 stycznia | Do 31 stycznia | 2 marca | 1 kwietnia |
    | Bob | 15 stycznia | 14 lutego | 16 marca | Kwi 15 |

    Istnieje możliwość użycia **wygaśnie zgody** i **czas trwania zanim ponownej akceptacji wymaga (w dniach)** ustawienia, ale zazwyczaj można użyć jednej z nich.

1. W obszarze **dostępu warunkowego**, użyj **Wymuś przy użyciu szablonu zasad dostępu warunkowego** listę, aby wybrać szablon, aby wymusić warunki użytkowania.

    ![Szablony dostępu warunkowego](./media/active-directory-tou/conditional-access-templates.png)

    | Szablon | Opis |
    | --- | --- |
    | **Dostęp do aplikacji w chmurze dla wszystkich gości** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich gości i wszystkie aplikacje w chmurze. Te zasady mają wpływ witryny Azure portal. Po utworzeniu, może być wymagane wylogowanie się i zaloguj się. |
    | **Dostęp do aplikacji dla wszystkich użytkowników w chmurze** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich użytkowników i wszystkie aplikacje w chmurze. Te zasady mają wpływ witryny Azure portal. Po utworzeniu, konieczna będzie wylogowanie się i zaloguj się. |
    | **Zasady niestandardowe** | Wybierz użytkowników, grupy i aplikacje, których będą dotyczyć niniejsze warunki użytkowania. |
    | **Tworzenie zasad dostępu warunkowego później** | Te warunki użytkowania zostaną wyświetlone na liście przyznawania kontroli podczas tworzenia zasad dostępu warunkowego. |

    >[!IMPORTANT]
    >Kontrolki zasad dostępu warunkowego (w tym warunki użytkowania) nie obsługują wymuszania dla kont usługi. Zaleca się wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

     Niestandardowe zasady dostępu warunkowego pozwalają zwiększyć szczegółowość warunków użytkowania, które mogą dotyczyć pojedynczej aplikacji w chmurze lub grupy użytkowników. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze wymagają](../conditional-access/require-tou.md).

1. Kliknij pozycję **Utwórz**.

    Jeśli wybrano szablon niestandardowy dostęp warunkowy, nowy ekran pojawi się, która umożliwia tworzenie niestandardowych zasad dostępu warunkowego.

    ![Niestandardowe zasady](./media/active-directory-tou/custom-policy.png)

    Zostaną wyświetlone nowe warunki użytkowania.

    ![Dodawanie warunków użytkowania](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Wyświetl raport, kto został zaakceptowany i odrzucone
W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczby oraz lista użytkowników, którzy zaakceptowali lub odrzucili warunki użytkowania, jest przechowywana przez cały okres istnienia warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

    ![Blok Warunki użytkowania](./media/active-directory-tou/view-tou.png)

1. Warunki użytkowania, kliknij liczby w obszarze **zaakceptowano** lub **odrzucone** do wyświetlania bieżącego stanu użytkownikom.

    ![Zgody na warunki użytkowania](./media/active-directory-tou/accepted-tou.png)

1. Aby wyświetlić historię dla poszczególnych użytkowników, kliknij przycisk wielokropka (**...** ) i następnie **wyświetlić historię**.

    ![Wyświetlić menu Historia](./media/active-directory-tou/view-history-menu.png)

    W okienku widoku Historia Zobacz historię wszystkich akceptuje odmówi i wygasanie ważności poświadczeń.

    ![Wyświetl historię okienko](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Wyświetl usługi Azure AD dzienniki inspekcji
Jeśli chcesz wyświetlić dodatkową aktywność, warunki użytkowania usługi Azure AD zawierają dzienniki inspekcji. Każdy zgody użytkownika wyzwala zdarzenie w dziennikach inspekcji, które są przechowywane przez **30 dni**. Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z usługą Azure AD dzienniki inspekcji, użyj następującej procedury:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Wybierz warunki użytkowania.

1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

    ![Blok Warunki użytkowania](./media/active-directory-tou/audit-tou.png)

1. Ekranie dzienników inspekcji w usłudze Azure AD, można filtrować informacje przy użyciu podanej listy do informacji z dzienników inspekcji określonego docelowego.

    Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

    ![Dzienniki inspekcji](./media/active-directory-tou/audit-logs-tou.png)

    Po kliknięciu przycisku dziennika okienko pojawia się ze szczegółami dodatkowe działania.

    ![Szczegóły działania](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>W jaki sposób warunki użytkowania są wyświetlane użytkownikom
Po utworzeniu warunków użytkowania i wymuszeniu ich stosowania uprawnionym użytkownikom zostanie wyświetlony następujący ekran podczas logowania.

![Logowanie w sieci web użytkownika](./media/active-directory-tou/user-tou.png)

Użytkownicy mogą przeglądać warunki użytkowania i, jeśli to konieczne, użyj przycisków, aby powiększać i pomniejszać.

![Wyświetlanie warunków użytkowania za pomocą przycisków powiększenia](./media/active-directory-tou/zoom-buttons.png)

Na poniższym ekranie przedstawiono, w jaki sposób warunki użytkowania są wyświetlane na urządzeniach przenośnych.

![Przenośne logowanie użytkownika](./media/active-directory-tou/mobile-tou.png)

Tylko użytkownicy muszą zaakceptować warunki użytkowania raz, a użytkownicy nie zobaczą warunki użytkowania ponownie na kolejnych logowań.

### <a name="how-users-can-review-their-terms-of-use"></a>Jak użytkownicy mogą przeglądać warunki użytkowania
Użytkownicy mogą wyświetlać i przeglądać warunki użytkowania, które zaakceptowali, korzystając z poniższej procedury.

1. Zaloguj się do witryny [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. W prawym górnym rogu kliknij swoją nazwę i wybierz **profilu**.

    ![Profil](./media/active-directory-tou/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

    ![Profilu — zapoznaj się z warunkami użytkowania](./media/active-directory-tou/tou13a.png)

1. Następnie możesz przejrzeć zaakceptowane warunki użytkowania.

## <a name="edit-terms-of-use-details"></a>Edytuj szczegóły warunków użytkowania
Możesz edytować niektóre szczegóły warunków użytkowania, ale nie można zmodyfikować istniejący dokument. Poniższa procedura opisuje sposób edytowania szczegółów.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Wybierz warunki użytkowania, które chcesz edytować.

1. Kliknij przycisk **Edycja warunków**.

1. W postanowieniach edycji okienko użycia Zmień nazwę, nazwę wyświetlaną lub Wymagaj od użytkowników rozwinięcia wartości.

    Jeśli istnieją inne ustawienia, które chcesz zmienić, takie jak dokument PDF, Wymagaj od użytkowników akceptacji na każdym urządzeniu, wygasają zgody, czas trwania zanim reacceptance lub zasady dostępu warunkowego, należy utworzyć nowe warunki użytkowania.

    ![Edycja warunków użytkowania](./media/active-directory-tou/edit-tou.png)

1. Kliknij przycisk **Zapisz** Aby zapisać zmiany.

    Po zapisaniu zmian, użytkownicy nie będą musieli akceptować wprowadzone zmiany.

## <a name="add-a-terms-of-use-language"></a>Dodawanie warunków użycia języka
Poniższa procedura opisuje sposób dodawania warunków użycia języka.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Wybierz warunki użytkowania, które chcesz edytować.

1. W okienku szczegółów kliknij **języków** kartę.

    ![Dodawanie warunków użytkowania](./media/active-directory-tou/languages-tou.png)

1. Kliknij przycisk **Dodaj język**.

1. W słowach Dodaj użycie języka okienka przekazać zlokalizowane dokument PDF i wybierz język.

    ![Dodawanie warunków użytkowania](./media/active-directory-tou/language-add-tou.png)

1. Kliknij przycisk **Dodaj** można dodać języka.

## <a name="per-device-terms-of-use"></a>Warunki użytkowania na urządzenie

**Wymagać od użytkowników akceptacji na każdym urządzeniu** ustawienia umożliwia wymaganie od użytkowników końcowych zaakceptować warunki użytkowania na każdym urządzeniu, które uzyskują dostęp do z. Użytkownicy będą musieli proces dołączania urządzenia w usłudze Azure AD. Po dołączeniu urządzenia identyfikator urządzenia jest używana do wymuszania warunki użytkowania na każdym urządzeniu.

Poniżej przedstawiono listę obsługiwanych platform i oprogramowania.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Inne |
> | --- | --- | --- | --- | --- |
> | **Aplikacja natywna** | Yes | Yes | Yes |  |
> | **Microsoft Edge** | Yes | Yes | Yes |  |
> | **Program Internet Explorer** | Yes | Yes | Yes |  |
> | **Chrome (z rozszerzeniem)** | Yes | Yes | Yes |  |

Warunki użytkowania na urządzenie ma następujące ograniczenia:

- Urządzenie może być częścią tylko jednej dzierżawy.
- Użytkownik musi mieć uprawnienia do przyłączania urządzenia.
- Aplikacja rejestracji w usłudze Intune nie jest obsługiwana.

Jeśli urządzenie użytkownika nie jest połączony, otrzymają komunikat, który potrzebują w celu przyłączenia urządzenia. Ich środowisko pracy będzie zależny od platformy i oprogramowania.

### <a name="join-a-windows-10-device"></a>Dołączanie urządzenia z systemem Windows 10

Jeśli użytkownik korzysta z systemu Windows 10 i Microsoft Edge, otrzymają komunikat podobny do następującego [proces dołączania urządzenia](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![System Windows 10 i Microsoft Edge — dołączania urządzenia w wierszu polecenia](./media/active-directory-tou/per-device-win10-edge.png)

W przypadku korzystania z przeglądarki Chrome, zostanie wyświetlony monit do zainstalowania [rozszerzenie systemu Windows 10 kont](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Przeglądarki

Jeśli użytkownik korzysta z przeglądarki, która nie jest obsługiwana, poprosimy do korzystania z innej przeglądarki.

![Nieobsługiwana przeglądarka](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Usuwanie warunków użytkowania
Stare warunki użytkowania można usunąć, korzystając z poniższej procedury.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

1. Wybierz warunki użytkowania, które chcesz usunąć.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

    ![Usuwanie warunków użytkowania](./media/active-directory-tou/delete-tou.png)

    Warunki użytkowania nie powinny już być widoczne.

## <a name="deleted-users-and-active-terms-of-use"></a>Usunięci użytkownicy i aktywne warunki użytkowania
Domyślnie usunięty użytkownik jest w stanie usunięcia w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Ponadto przy użyciu portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed upłynięciem tego okresu. Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych warunków użytkowania. Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="policy-changes"></a>Zmiany zasad
Zasady dostępu warunkowego zaczynają obowiązywać natychmiast. W takim przypadku administrator będzie wyświetlane "smutne chmury" lub "Problemy z tokenem usługi Azure AD". Administrator musi się wylogować i zaloguj się ponownie, aby spełnić wymagania nowych zasad.

>[!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
> - dla warunków użytkowania włączono zasady dostępu warunkowego
> - lub utworzono drugą wersję warunków użytkowania

## <a name="b2b-guests-preview"></a>Goście B2B (wersja zapoznawcza)

Większość organizacji będzie miała procesu w miejscu dla swoich pracowników do wyrażenia zgody na warunki użycia i zasady zachowania poufności w organizacji. Jak można wymusić ten sam zgody dla usługi Azure AD business-to-business, ale goście (B2B), kiedy są dodawane przy użyciu programu SharePoint lub zespoły? Przy użyciu dostępu warunkowego i warunki użytkowania, możesz wymusić zasady bezpośrednio do użytkowników-gości B2B. Podczas realizacji przepływu zaproszenia użytkownik zobaczy warunki użytkowania. Ta funkcja jest obecnie w wersji zapoznawczej.

Warunki użytkowania będzie wyświetlane tylko w przypadku, gdy użytkownik ma konto gościa w usłudze Azure AD. SharePoint Online ma obecnie [obsługi ad-hoc, zewnętrznych, udostępniania, odbiorcy](/sharepoint/what-s-new-in-sharing-in-targeted-release) udostępniania dokumentu lub folderu, który nie wymaga od użytkownika posiadania konta gościa. W tym przypadku warunki użytkowania nie jest wyświetlana.

![Wszyscy użytkownicy-goście](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Obsługa aplikacji w chmurze (wersja zapoznawcza)

Warunki użytkowania może służyć do aplikacji w chmurze innej, takich jak usługi Azure Information Protection i Microsoft Intune. Ta funkcja jest obecnie w wersji zapoznawczej.

### <a name="azure-information-protection"></a>Azure Information Protection

Można skonfigurować zasady dostępu warunkowego dla aplikacji usługi Azure Information Protection i wymagają warunki użytkowania, gdy użytkownik uzyskuje dostęp do chronionego dokumentu. Spowoduje to wyzwolenie warunków użytkowania przed użytkownik uzyskuje dostęp do chronionych dokumentów po raz pierwszy.

![Aplikacja chmury w usłudze Azure Information Protection](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Rejestracja w usłudze Microsoft Intune

Można skonfigurować zasady dostępu warunkowego dla aplikacji rejestracja w usłudze Microsoft Intune i wymagają warunków użytkowania przed zarejestrowaniem urządzenia w usłudze Intune. Aby uzyskać więcej informacji, zobacz odczytu [Wybieranie odpowiedniego warunki rozwiązanie dla Twojej organizacji wpis w blogu](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Aplikacja w chmurze Microsoft Intune](./media/active-directory-tou/cloud-app-intune.png)

> [!NOTE]
> W aplikacji rejestracji w usłudze Intune nie jest obsługiwana dla [na urządzenie z warunkami użytkowania](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Jak sprawdzić, gdy /, jeśli użytkownik zaakceptował warunki użytkowania?**<br />
Odp.: Na warunkach Użyj bloku kliknij liczbę w obszarze **zaakceptowano**. Można również wyświetlić lub zaakceptuj działań związanych z wyszukiwaniem w usłudze Azure AD dzienniki inspekcji. Aby uzyskać więcej informacji, zobacz widok raport, który ma zaakceptowali i odrzucili i [usługi Azure AD Wyświetl dzienniki inspekcji](#view-azure-ad-audit-logs).

**Pyt.: Jak długo jest informacje przechowywane?**<br />
Odp.: Użytkownik jest liczona w warunkach w raporcie użycia i który zaakceptowane odrzucone są przechowywane przez cały okres istnienia warunki użytkowania. Inspekcja usługi Azure AD, dzienniki są przechowywane przez 30 dni.

**Pyt.: Dlaczego widzę różne liczby zgody w warunkach użytkowania raporcie użycia a usługą Azure AD dzienników inspekcji?**<br />
Odp.: Warunki użycia raportu są przechowywane przez okres istnienia tego warunki użytkowania, podczas inspekcji usługi Azure AD, które dzienniki są przechowywane przez 30 dni. Ponadto warunki raporcie użycia są wyświetlane tylko bieżącego stanu zgody użytkowników. Na przykład jeśli użytkownik odmówi, a następnie akceptuje, warunki raporcie użycia zostaną wyświetlone tylko ten użytkownik akceptuje. Jeśli zachodzi potrzeba wyświetlenia historii, możesz użyć usługi Azure AD dzienniki inspekcji.

**Pyt.: Jeśli edytować szczegóły warunków użytkowania wymaga ich użytkowników o zaakceptowanie ponownie?**<br />
Odp.: Nie, jeśli administrator podda edycji szczegóły warunków użytkowania (nazwa, nazwa wyświetlana, Wymagaj od użytkowników rozwinięcia lub Dodaj język), użytkownicy wymagają ponownej akceptacji nowych warunków nie jest wymagane.

**Pyt.: Czy mogę zaktualizować istniejący dokument z warunkami użytkowania?**<br />
Odp.: Obecnie nie można zaktualizować istniejący dokument z warunkami użytkowania. Aby zmienić dokument z warunkami użytkowania, należy utworzyć nowe warunki wystąpienia użycia.

**Pyt.: Jeśli dokument z warunkami użytkowania PDF hiperłącza, czy użytkownicy końcowi będą mogli klikać?**<br />
Odp.: Plik PDF jest renderowany domyślnie w formacie JPEG, dzięki czemu hiperłącza nie są aktywne. Użytkownicy mają możliwość dokonania wyboru **masz problemy z wyświetlaniem? Kliknij tutaj,**, który renderuje plik PDF natywnie których hiperłącza są obsługiwane.

**Pyt.: Warunki użytkowania, może obsługiwać wiele języków?**<br />
Odp.: Tak. Aktualnie nie istnieją 108 różnych języków, administrator może skonfigurować dla pojedynczego warunki użytkowania. Administrator może przekazać wiele dokumentów PDF i oznaczać te dokumenty przy użyciu odpowiedniego języka (maksymalnie 108). Po zalogowaniu się użytkownicy końcowi możemy przyjrzeć się ich preferencji językowych przeglądarki i wyświetlić pasujących dokumentów. W przypadku braku dopasowania wyświetlamy dokument domyślny, który jest pierwszy dokument, który zostanie przekazany.

**Pyt.: Kiedy jest wyzwalane warunki użytkowania?**<br />
Odp.: Warunki użytkowania są wyzwalane podczas logowania.

**Pyt.: Jakie aplikacje mogą kierować warunki użytkowania?**<br />
Odp.: Można utworzyć zasady dostępu warunkowego dla aplikacji przedsiębiorstwa, korzystających z nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./../manage-apps/view-applications-portal.md).

**Pyt.: Do danego użytkownika lub aplikacji można dodać wiele wersji warunków użytkowania?**<br />
Odp.: Tak, tworząc wiele zasad dostępu warunkowego obejmujących grupy lub aplikacji. Jeśli użytkownik mieści się w zakresie wiele wersji warunków użytkowania, akceptują jeden z warunkami użytkowania w danym momencie.

**Pyt.: Co się stanie, jeśli użytkownik odrzuci warunki użytkowania?**<br />
Odp.: Użytkownik zostanie zablokowany dostęp do aplikacji. Użytkownik będzie musiał zalogować się ponownie i zaakceptuj warunki w celu uzyskania dostępu.

**Pyt.: Czy jest możliwe unaccept warunki użytkowania, które wcześniej zostały zaakceptowane**<br />
Odp.: Możesz [przeglądu wcześniej zaakceptowane warunki użytkowania](#how-users-can-review-their-terms-of-use), ale obecnie nie istnieje sposób unaccept.

**Pyt.: Co się stanie, jeśli również używam warunków i postanowień usługi Intune?**<br />
Odp.: Jeśli skonfigurowano zarówno usługi Azure AD z warunkami użytkowania i [Intune warunki i postanowienia](/intune/terms-and-conditions-create), użytkownik będzie musiał zaakceptować oba te elementy. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniego warunki rozwiązanie dla Twojej organizacji wpis w blogu](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Kolejne kroki

- [Szybki start: Wymagane warunki użytkowania, należy zaakceptować przed uzyskaniem dostępu do aplikacji w chmurze](../conditional-access/require-tou.md)
- [Najlepsze rozwiązania dotyczące dostępu warunkowego w usłudze Azure Active Directory](../conditional-access/best-practices.md)
