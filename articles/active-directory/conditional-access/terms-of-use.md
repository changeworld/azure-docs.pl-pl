---
title: Warunki użytkowania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Rozpocznij korzystanie z warunków użytkowania usługi Azure Active Directory w celu przedstawienia informacji pracownikom lub gościom przed uzyskaniem dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480967"
---
# <a name="azure-active-directory-terms-of-use"></a>Warunki użytkowania usługi Azure Active Directory

Warunki użytkowania usługi Azure AD zapewnia prostą metodę, której organizacje mogą używać do prezentowania informacji użytkownikom końcowym. Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności. W tym artykule opisano, jak rozpocząć pracę z warunkami użytkowania.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Omówienie filmów

Poniższy film wideo zawiera szybki przegląd warunków użytkowania.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Aby uzyskać dodatkowe filmy, zobacz:
- [Jak wdrożyć warunki użytkowania w usłudze Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Jak wdrożyć warunki użytkowania w usłudze Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Co mogę zrobić z warunkami użytkowania?

Warunki użytkowania usługi Azure AD mają następujące możliwości:

- Wymagaj od pracowników lub gości zaakceptowania warunków użytkowania przed uzyskaniem dostępu.
- Wymagaj od pracowników lub gości zaakceptowania warunków użytkowania na każdym urządzeniu przed uzyskaniem dostępu.
- Wymagaj od pracowników lub gości, aby zaakceptowali Warunki użytkowania zgodnie z harmonogramem cyklicznym.
- Wymagaj od pracowników lub gości zaakceptowania warunków użytkowania przed zarejestrowaniem informacji o zabezpieczeniach w usłudze Azure Multi-Factor Authentication (MFA).
- Wymagaj od pracowników zaakceptowania warunków użytkowania przed zarejestrowaniem informacji o zabezpieczeniach w samoobsługowym resetowaniu haseł usługi Azure AD(SSPR).
- Przedstawianie ogólnych warunków użytkowania dla wszystkich użytkowników w organizacji.
- Przedstawianie określonych terminów użytkowania na podstawie atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek, a pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) za pomocą [grup dynamicznych](../users-groups-roles/groups-dynamic-membership.md).
- Przedstawiaj konkretne warunki użytkowania podczas uzyskiwania dostępu do aplikacji o dużym wpływie na działalność, takich jak Salesforce.
- Obecne warunki użytkowania w różnych językach.
- Wyświetl listę osób, które zaakceptowały warunki użytkowania.
- Pomoc w spełnianiu przepisów dotyczących prywatności.
- Wyświetl dziennik działań dotyczących warunków użytkowania dla zgodności i inspekcji.
- Tworzenie warunków użytkowania i zarządzanie nimi przy użyciu [interfejsów API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (obecnie w wersji zapoznawczej).

## <a name="prerequisites"></a>Wymagania wstępne

Aby używać i konfigurować warunki użytkowania usługi Azure AD, musisz mieć:

- Subskrypcja usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.
   - Jeśli nie masz żadnej z tych subskrypcji, możesz [uzyskać dostęp do usługi Azure AD w wersji Premium](../fundamentals/active-directory-get-started-premium.md) lub [włączyć wersję próbnej usługi Azure AD w wersji Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Jedno z następujących kont administratora katalogu, który chcesz skonfigurować:
   - Administrator globalny
   - Administrator zabezpieczeń
   - Administrator dostępu warunkowego

## <a name="terms-of-use-document"></a>Dokument z warunkami użytkowania

Warunki użytkowania usługi Azure AD używają formatu PDF do prezentowania zawartości. Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników końcowych podczas logowania. Aby obsługiwać użytkowników na urządzeniach przenośnych, zalecany rozmiar czcionki w pliku PDF wynosi 24 punkty.

## <a name="add-terms-of-use"></a>Dodawanie warunków użytkowania

Po sfinalizowaniu dokumentu warunków użytkowania należy użyć poniższej procedury, aby go dodać.

1. Zaloguj się na platformie Azure jako administrator globalny, administrator zabezpieczeń lub administrator dostępu warunkowego.
1. Przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

   ![Dostęp warunkowy — warunki użytkowania](./media/terms-of-use/tou-blade.png)

1. Kliknij pozycję **Nowe warunki**.

   ![Nowy termin użytkowania okienka, aby określić ustawienia warunków użytkowania](./media/terms-of-use/new-tou.png)

1. W polu **Nazwa** wprowadź nazwę warunków użytkowania, które będą używane w witrynie Azure portal.
1. W polu **Nazwa wyświetlana** wprowadź tytuł, który użytkownicy widzą podczas logowania.
1. W przypadku **dokumentu warunków użytkowania**przejdź do sfinalizowanych warunków użytkowania w formacie PDF i wybierz go.
1. Wybierz język dokumentu warunków użytkowania. Opcja wyboru języka umożliwia przekazanie wielu wersji językowych warunków użytkowania. Wersja warunków użytkowania widoczna dla użytkownika końcowego będzie zależała od preferencji jego przeglądarki.
1. Aby wymagać od użytkowników końcowych wyświetlania warunków użytkowania przed ich zaakceptowaniem, ustaw **opcję Wymagaj od użytkowników, aby rozwierzyli warunki użytkowania** na **Włączone**.
1. Aby wymagać od użytkowników końcowych zaakceptowania warunków użytkowania na każdym urządzeniu, z którego uzyskują dostęp, ustaw **wymagaj od użytkowników zgody na każdym urządzeniu,** aby **włączyć**. Użytkownicy mogą być zobowiązani do zainstalowania dodatkowych aplikacji, jeśli ta opcja jest włączona. Aby uzyskać więcej informacji, zobacz [Warunki użytkowania na urządzeniu](#per-device-terms-of-use).
1. Jeśli chcesz wygasnąć warunki użytkowania zgody na harmonogram, ustaw **wygaśnięcia zgody** **na On**. Po ustawieniu na Włączone wyświetlane są dwa dodatkowe ustawienia harmonogramu.

   ![Ustawienia wygaśnięcia zgody na ustawienie daty rozpoczęcia, częstotliwości i czasu trwania](./media/terms-of-use/expire-consents.png)

1. Użyj **expire począwszy od** i **Częstotliwość** ustawienia, aby określić harmonogram wygaśnięcia warunków użytkowania. W poniższej tabeli przedstawiono wynik dla kilku przykładowych ustawień:

   | Wygasa od | Częstotliwość | Wynik |
   | --- | --- | --- |
   | Dzisiejsza data  | Co miesiąc | Od dzisiaj użytkownicy muszą zaakceptować warunki użytkowania, a następnie ponownie zaakceptować co miesiąc. |
   | Data w przyszłości  | Co miesiąc | Od dzisiaj użytkownicy muszą zaakceptować warunki użytkowania. Po wystąpieniu przyszłej daty zgody wygasną, a następnie użytkownicy muszą ponownie przychylić się co miesiąc.  |

   Na przykład jeśli ustawisz datę wygaśnięcia początkową na **1 stycznia** i częstotliwość na **Miesięczny,** oto jak mogą wystąpić wygaśnięcia dla dwóch użytkowników:

   | Użytkownik | Data pierwszej akceptacji | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Trzecia data wygaśnięcia |
   | --- | --- | --- | --- | --- |
   | Alice | 1 sty | 1 lutego | 1 marca | Kwi 1 |
   | Bob | 15 sty | 1 lutego | 1 marca | Kwi 1 |

1. Użyj **duration before re-acceptance wymaga (dni),** aby określić liczbę dni, zanim użytkownik musi ponownie zaakceptować warunki użytkowania. Dzięki temu użytkownicy mogą przestrzegać własnego harmonogramu. Na przykład jeśli ustawisz czas trwania na **30** dni, oto jak mogą wystąpić wygaśnięcia dla dwóch użytkowników:

   | Użytkownik | Data pierwszej akceptacji | Data pierwszego wygaśnięcia | Druga data wygaśnięcia | Trzecia data wygaśnięcia |
   | --- | --- | --- | --- | --- |
   | Alice | 1 sty | 31 stycznia | 2 marca | Kwi 1 |
   | Bob | 15 sty | 14 lutego | 16 marca | Kwi 15 |

   Istnieje możliwość użycia **wygasaj zgody** i **Czas trwania przed ponownej akceptacji wymaga (dni)** ustawienia razem, ale zazwyczaj używasz jednego lub drugiego.

1. W obszarze **Dostęp warunkowy**użyj listy **Szablon zasad Wymuszaj z dostępem warunkowym,** aby wybrać szablon, aby wymusić warunki użytkowania.

   ![Lista rozwijana dostępu warunkowego do wyboru szablonu zasad](./media/terms-of-use/conditional-access-templates.png)

   | Szablon | Opis |
   | --- | --- |
   | **Dostęp do aplikacji w chmurze dla wszystkich gości** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich gości i wszystkich aplikacji w chmurze. Ta zasada ma wpływ na witrynę Azure portal. Po utworzeniu może być konieczne wylogowanie się i zalogowanie. |
   | **Dostęp do aplikacji w chmurze dla wszystkich użytkowników** | Zasady dostępu warunkowego zostaną utworzone dla wszystkich użytkowników i wszystkich aplikacji w chmurze. Ta zasada ma wpływ na witrynę Azure portal. Po utworzeniu tego będzie wymagane wylogowanie i zalogowanie się. |
   | **Zasady niestandardowe** | Wybierz użytkowników, grupy i aplikacje, do których będą stosowane te warunki użytkowania. |
   | **Utwórz zasady dostępu warunkowego później** | Te warunki użytkowania pojawią się na liście kontroli dotacji podczas tworzenia zasad dostępu warunkowego. |

   >[!IMPORTANT]
   >Mechanizmy kontroli zasad dostępu warunkowego (w tym warunki użytkowania) nie obsługują wymuszania na kontach usług. Zaleca się wyłączenie wszystkich kont usług z zasad dostępu warunkowego.

    Niestandardowe zasady dostępu warunkowego umożliwiają szczegółowe warunki użytkowania, aż do określonej aplikacji w chmurze lub grupy użytkowników. Aby uzyskać więcej informacji, zobacz [Szybki start: Wymaganie zaakceptowania warunków użytkowania przed uzyskiwaniem dostępu do aplikacji w chmurze.](require-tou.md)

1. Kliknij przycisk **Utwórz**.

   Jeśli wybrano niestandardowy szablon dostępu warunkowego, zostanie wyświetlony nowy ekran umożliwiający utworzenie niestandardowych zasad dostępu warunkowego.

   ![Nowe okienko Dostępu warunkowego, jeśli wybrano niestandardowy szablon zasad dostępu warunkowego](./media/terms-of-use/custom-policy.png)

   Powinieneś teraz zobaczyć nowe warunki użytkowania.

   ![Nowe warunki użytkowania wymienione w warunkach użytkowania](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Wyświetl raport o tym, kto zaakceptował i odrzucił

W bloku Warunki użytkowania znajduje się liczba użytkowników, którzy je zaakceptowali i odrzucili. Te liczby i kto zaakceptował / odrzucony są przechowywane przez cały okres użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).

   ![Warunki użytkowania ostrza z listą liczby pokazów użytkowników zostały zaakceptowane i odrzucone](./media/terms-of-use/view-tou.png)

1. Aby uzyskać warunki użytkowania, kliknij numery w obszarze **Zaakceptowane** lub **Odrzucone,** aby wyświetlić bieżący stan dla użytkowników.

   ![Warunki użytkowania wyrażają zgodę na wyświetlanie okienek z listą użytkowników, którzy zaakceptowali](./media/terms-of-use/accepted-tou.png)

1. Aby wyświetlić historię dla poszczególnych użytkowników, kliknij wielokropek (**...**), a następnie **wyświetl historię**.

   ![Wyświetl menu kontekstowe Historia dla użytkownika](./media/terms-of-use/view-history-menu.png)

   W okienku historii widoku zobaczysz historię wszystkich akceptuje, odrzuca i wygasa.

   ![Okienko Wyświetl historię zawiera listę akceptowań, odrzuceń i wygaśnięcia dla użytkownika](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Wyświetlanie dzienników inspekcji usługi Azure AD

Jeśli chcesz wyświetlić dodatkowe działania, warunki użytkowania usługi Azure AD zawiera dzienniki inspekcji. Każda zgoda użytkownika wyzwala zdarzenie w dziennikach inspekcji, które jest przechowywane przez **30 dni**. Te dzienniki możesz wyświetlić w portalu lub pobrać jako plik CSV.

Aby rozpocząć pracę z dziennikami inspekcji usługi Azure AD, należy wykonać następującą procedurę:

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania.
1. Kliknij pozycję **Wyświetl dzienniki inspekcji**.

   ![Warunki użytkowania bloku z zaznaczoną opcją Zobacz dzienniki inspekcji](./media/terms-of-use/audit-tou.png)

1. Na ekranie dzienników inspekcji usługi Azure AD można filtrować informacje przy użyciu podanych list do docelowych informacji dziennika inspekcji określonego.

   Można również kliknąć pozycję **Pobierz**, aby pobrać informacje w pliku CSV do użytku lokalnego.

   ![Wyświetlanie daty wyświetlania dzienników inspekcji usługi Azure AD z datą, zasadą docelową, inicjowanymi przez i działaniem](./media/terms-of-use/audit-logs-tou.png)

   Jeśli klikniesz dziennik, pojawi się okienko z dodatkowymi szczegółami aktywności.

   ![Szczegóły działania dziennika przedstawiającego aktywność, stan działania, zainicjowany przez zasady docelowe](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Jakie warunki użytkowania wyglądają dla użytkowników

Po utworzeniu i wymuszaniu warunków użytkowania użytkownicy, którzy znajdują się w zakresie, zobaczą następujący ekran podczas logowania.

![Przykładowe warunki użytkowania, które pojawiają się, gdy użytkownik zaloguje się](./media/terms-of-use/user-tou.png)

Użytkownicy mogą przeglądać warunki użytkowania i, jeśli to konieczne, używać przycisków do powiększania i pomniejszania.

![Widok warunków użytkowania za pomocą przycisków powiększenia](./media/terms-of-use/zoom-buttons.png)

Na poniższym ekranie pokazano, jak wyglądają warunki użytkowania na urządzeniach mobilnych.

![Przykładowe warunki użytkowania wyświetlane, gdy użytkownik loguje się na urządzeniu przenośnym](./media/terms-of-use/mobile-tou.png)

Użytkownicy muszą zaakceptować warunki użytkowania tylko raz i nie zobaczą warunków użytkowania ponownie w kolejnych logowaniach.

### <a name="how-users-can-review-their-terms-of-use"></a>W jaki sposób użytkownicy mogą przeglądać swoje warunki użytkowania

Użytkownicy mogą przeglądać i wyświetlać warunki użytkowania, które zaakceptowali, korzystając z poniższej procedury.

1. Zaloguj się [https://myapps.microsoft.com](https://myapps.microsoft.com)do .
1. W prawym górnym rogu kliknij swoje imię i nazwisko i wybierz **pozycję Profil**.

   ![Witryna MyApps z otwartym okienkiem użytkownika](./media/terms-of-use/tou14.png)

1. Na stronie Profil kliknij pozycję **Przejrzyj warunki użytkowania**.

   ![Strona profilu dla użytkownika z linkiem Przeglądaj warunki użytkowania](./media/terms-of-use/tou13a.png)

1. Następnie możesz przejrzeć zaakceptowane warunki użytkowania.

## <a name="edit-terms-of-use-details"></a>Edytuj szczegóły warunków użytkowania

Można edytować niektóre szczegóły warunków użytkowania, ale nie można zmodyfikować istniejącego dokumentu. W poniższej procedurze opisano sposób edytowania szczegółów.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz edytować.
1. Kliknij pozycję **Edytuj terminy**.
1. W okienku Edytowanie warunków użytkowania zmień nazwę, nazwę wyświetlaną lub wymagaj od użytkowników rozwijania wartości.

   Jeśli istnieją inne ustawienia, które chcesz zmienić, takie jak dokument PDF, wymagają od użytkowników zgody na każdym urządzeniu, wygaśnięcia zgody, czasu trwania przed ponownym przybraniem lub zasad dostępu warunkowego, należy utworzyć nowe warunki użytkowania.

   ![Edytowanie okienek warunków użytkowania z wyświetlaniem opcji nazwy i rozwiń](./media/terms-of-use/edit-tou.png)

1. Kliknij **przycisk Zapisz,** aby zapisać zmiany.

   Po zapisaniu zmian użytkownicy nie będą musieli ponownie przychylić się do tych zmian.

## <a name="add-a-terms-of-use-language"></a>Dodawanie języka warunków użytkowania

W poniższej procedurze opisano sposób dodawania języka warunków użytkowania.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz edytować.
1. W okienku szczegółów kliknij kartę **Języki.**

   ![Wybrane warunki użytkowania i karta Języki w okienku szczegółów](./media/terms-of-use/languages-tou.png)

1. Kliknij **pozycję Dodaj język**.
1. W okienku Dodaj warunki użytkowania prześlij zlokalizowany plik PDF i wybierz język.

   ![Dodawanie okienka języka warunków użytkowania z opcjami przekazywania zlokalizowanych plików PDF](./media/terms-of-use/language-add-tou.png)

1. Kliknij **przycisk Dodaj,** aby dodać język.

## <a name="per-device-terms-of-use"></a>Warunki użytkowania na urządzeniu

**Wymagaj od użytkowników zgody na każde** ustawienie urządzenia, dzięki którego możesz wymagać od użytkowników końcowych zaakceptowania warunków użytkowania na każdym urządzeniu, z którego uzyskują dostęp. Użytkownik końcowy będzie musiał zarejestrować swoje urządzenie w usłudze Azure AD. Po zarejestrowaniu urządzenia identyfikator urządzenia jest używany do wymuszania warunków użytkowania na każdym urządzeniu.

Oto lista obsługiwanych platform i oprogramowania.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Inne |
> | --- | --- | --- | --- | --- |
> | **Aplikacja natywna** | Tak | Tak | Tak |  |
> | **Microsoft Edge** | Tak | Tak | Tak |  |
> | **Internet Explorer** | Tak | Tak | Tak |  |
> | **Chrome (z rozszerzeniem)** | Tak | Tak | Tak |  |

Warunki użytkowania na urządzeniu mają następujące ograniczenia:

- Urządzenie można przyłączać tylko do jednej dzierżawy.
- Użytkownik musi mieć uprawnienia do dołączenia do swojego urządzenia.
- Aplikacja Rejestracja usługi Intune nie jest obsługiwana.
- Użytkownicy usługi Azure AD B2B nie są obsługiwani.

Jeśli urządzenie użytkownika nie zostanie przyłączone, otrzyma komunikat, że musi dołączyć do urządzenia. Ich doświadczenie będzie zależeć od platformy i oprogramowania.

### <a name="join-a-windows-10-device"></a>Dołączanie urządzenia z systemem Windows 10

Jeśli użytkownik korzysta z systemu Windows 10 i przeglądarki Microsoft Edge, otrzyma komunikat podobny do następującego, aby [dołączyć do urządzenia](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 i Microsoft Edge - Komunikat wskazujący, że urządzenie musi być zarejestrowane](./media/terms-of-use/per-device-win10-edge.png)

Jeśli korzystają z Chrome, zostaną poproszeni o zainstalowanie [rozszerzenia Konta systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Rejestrowanie urządzenia z systemem iOS

Jeśli użytkownik korzysta z urządzenia z systemem iOS, zostanie wyświetlony monit o zainstalowanie [aplikacji Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Zarejestruj urządzenie z Androidem

Jeśli użytkownik korzysta z urządzenia z systemem Android, zostanie wyświetlony monit o zainstalowanie [aplikacji Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Przeglądarki

Jeśli użytkownik korzysta z przeglądarki, która nie jest obsługiwana, zostanie poproszony o użycie innej przeglądarki.

![Komunikat informujący, że urządzenie musi być zarejestrowane, ale przeglądarka nie jest obsługiwana](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Usuwanie warunków użytkowania

Stare warunki użytkowania można usunąć, korzystając z poniższej procedury.

1. Zaloguj się do platformy Azure i przejdź do **warunków użytkowania** na stronie [https://aka.ms/catou](https://aka.ms/catou).
1. Wybierz warunki użytkowania, które chcesz usunąć.
1. Kliknij pozycję **Usuń**.
1. W wyświetlonym komunikacie z pytaniem o kontynuowanie kliknij pozycję **Tak**.

   ![Wiadomość z prośbą o potwierdzenie usunięcia warunków użytkowania](./media/terms-of-use/delete-tou.png)

   Nie powinieneś już widzieć swoich warunków użytkowania.

## <a name="deleted-users-and-active-terms-of-use"></a>Usunięci użytkownicy i aktywne warunki użytkowania

Domyślnie usunięty użytkownik jest w stanie usunięcia w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Ponadto za pomocą portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed osiągnięciem tego okresu. Jeden użytkownik został trwale usunięty, kolejne dane o tym użytkowniku zostaną usunięte z aktywnych warunków użytkowania. Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="policy-changes"></a>Zmiany zasad

Zasady dostępu warunkowego wchodzą w życie natychmiast. W takim przypadku administrator zacznie widzieć "smutne chmury" lub "Problemy tokenu usługi Azure AD". Administrator musi wylogować się i zalogować ponownie, aby spełnić nowe zasady.

> [!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
>
> - zasady dostępu warunkowego są włączone na warunkach użytkowania
> - Utworzono drugą wersję warunków użytkowania

## <a name="b2b-guests-preview"></a>Goście B2B (wersja zapoznawcza)

Większość organizacji ma proces, w ramach którego ich pracownicy wyrażają zgodę na warunki użytkowania i zasady zachowania poufności informacji w organizacji. Ale jak można wymusić te same zgody dla gości biznesowych usługi Azure AD (B2B) po dodaniu za pośrednictwem programu SharePoint lub usługi Teams? Korzystając z dostępu warunkowego i warunków użytkowania, można wymusić zasady bezpośrednio do użytkowników-gości B2B. Podczas realizacji zaproszenia użytkownik otrzymuje warunki użytkowania. Ta obsługa jest obecnie w wersji zapoznawczej.

Warunki użytkowania będą wyświetlane tylko wtedy, gdy użytkownik ma konto gościa w usłudze Azure AD. Usługa SharePoint Online ma obecnie obsługę [adresata udostępniania zewnętrznego ad hoc,](/sharepoint/what-s-new-in-sharing-in-targeted-release) aby udostępnić dokument lub folder, który nie wymaga od użytkownika konta gościa. W takim przypadku warunki użytkowania nie są wyświetlane.

![Okienko Użytkownicy i grupy — dołącz kartę z zaznaczoną opcją Wszyscy użytkownicy-goście](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Obsługa aplikacji w chmurze (wersja zapoznawcza)

Warunki użytkowania mogą być używane dla różnych aplikacji w chmurze, takich jak Usługa Azure Information Protection i Microsoft Intune. Ta obsługa jest obecnie w wersji zapoznawczej.

### <a name="azure-information-protection"></a>Azure Information Protection

Można skonfigurować zasady dostępu warunkowego dla aplikacji Usługi Azure Information Protection i wymagać warunków użytkowania, gdy użytkownik uzyskuje dostęp do chronionego dokumentu. Spowoduje to uruchomienie warunków użytkowania przed użytkownikiem uzyskującym dostęp do chronionego dokumentu po raz pierwszy.

![Okienko Aplikacje w chmurze z wybraną aplikacją Microsoft Azure Information Protection](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Rejestracja usługi Microsoft Intune

Można skonfigurować zasady dostępu warunkowego dla aplikacji Rejestracji usługi Microsoft Intune i wymagać warunków użytkowania przed rejestracją urządzenia w usłudze Intune. Aby uzyskać więcej informacji, zobacz czytanie [opcji Wybieranie odpowiednich warunków dla wpisu w blogu organizacji](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Okienko Aplikacje w chmurze z wybraną aplikacją usługi Microsoft Intune](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Aplikacja Rejestracja usługi Intune nie jest obsługiwana dla [warunków użytkowania na urządzeniu](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt. Jak sprawdzić, czy i kiedy użytkownik zaakceptował warunki użytkowania?**<br />
Odp.: W warunkach użytkowania bloku, kliknij numer w obszarze **Zaakceptowane**. Można również wyświetlić lub przeszukać działanie akceptowania w dziennikach inspekcji usługi Azure AD. Aby uzyskać więcej informacji, zobacz Wyświetlanie raportu o tym, kto zaakceptował i odrzucił oraz [Wyświetlanie dzienników inspekcji usługi Azure AD.](#view-azure-ad-audit-logs)

**Pyt. Jak długo są przechowywane informacje?**<br />
Odp.: Użytkownik liczy się w raporcie warunków użytkowania, a kto zaakceptował/odrzucił, są przechowywane przez cały okres użytkowania. Dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni.

**Pyt.: Dlaczego widzę inną liczbę zgód w raporcie warunków użytkowania w porównaniu z dziennikami inspekcji usługi Azure AD?**<br />
Odp.: Raport warunków użytkowania jest przechowywany przez cały okres istnienia tych warunków użytkowania, podczas gdy dzienniki inspekcji usługi Azure AD są przechowywane przez 30 dni. Ponadto raport warunków użytkowania wyświetla tylko stan bieżącej zgody użytkowników. Na przykład jeśli użytkownik odrzuci, a następnie zaakceptuje, raport warunków użytkowania będzie tylko pokazać, że użytkownik akceptuje. Jeśli chcesz wyświetlić historię, możesz użyć dzienników inspekcji usługi Azure AD.

**Pyt.: Jeśli edytuję szczegóły dotyczące warunków użytkowania, czy wymaga to od użytkowników ponownego zaakceptowania?**<br />
Odp.: Nie, jeśli administrator edytuje szczegóły warunków użytkowania (nazwa, nazwa wyświetlana, wymaganie od użytkowników rozwijania lub dodawania języka), nie wymaga od użytkowników ponownego przyakcją nowych warunków.

**Pyt.: Czy mogę zaktualizować istniejący dokument dotyczący warunków użytkowania?**<br />
Odp.: Obecnie nie można zaktualizować istniejącego dokumentu warunków użytkowania. Aby zmienić dokument warunków użytkowania, należy utworzyć nowe wystąpienie warunków użytkowania.

**P: Jeśli hiperłącza znajdują się w warunkach użytkowania dokumentu PDF, czy użytkownicy końcowi będą mogli je kliknąć?**<br />
Odp.: Tak, użytkownicy końcowi mogą wybierać hiperłącza do dodatkowych stron, ale łącza do sekcji w dokumencie nie są obsługiwane.

**Pyt. Czy warunki użytkowania obsługują wiele języków?**<br />
Odp. Tak. Obecnie istnieje 108 różnych języków, które administrator może skonfigurować dla pojedynczych warunków użytkowania. Administrator może przesłać wiele dokumentów PDF i oznaczyć je odpowiednim językiem (do 108). Gdy użytkownicy końcowi się logują, patrzymy na ich preferencje językowe przeglądarki i wyświetlamy pasujący dokument. Jeśli nie ma dopasowania, wyświetlimy domyślny dokument, który jest pierwszym dokumentem, który jest przekazyzony.

**Pyt. Kiedy są wyzwalane warunki użytkowania?**<br />
Odp. Warunki użytkowania są wyzwalane podczas logowania.

**Pyt. Jakie aplikacje mogą zostać objęte warunkami użytkowania?**<br />
Odp.: Można utworzyć zasady dostępu warunkowego dla aplikacji korporacyjnych przy użyciu nowoczesnego uwierzytelniania. Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](./../manage-apps/view-applications-portal.md).

**Pyt. Czy dla użytkownika lub aplikacji można określić wiele warunków użytkowania?**<br />
Odp.: Tak, tworząc wiele zasad dostępu warunkowego kierowanych do tych grup lub aplikacji. Jeśli użytkownik wchodzi w zakres wielu warunków użytkowania, akceptuje jeden warunki użytkowania naraz.

**Pyt. Co się stanie, jeśli użytkownik odrzuci warunki użytkowania?**<br />
Odp. Dostęp do aplikacji zostanie zablokowany dla tego użytkownika. Użytkownik musiałby zalogować się ponownie i zaakceptować warunki, aby uzyskać dostęp.

**P: Czy można zaakceptować warunki użytkowania, które zostały wcześniej zaakceptowane?**<br />
Odp.: Możesz [przejrzeć wcześniej przyjęte warunki użytkowania,](#how-users-can-review-their-terms-of-use)ale obecnie nie ma sposobu na niezaakceptować.

**P: Co się stanie, jeśli używam również warunków usługi Intune?**<br />
Odp.: Jeśli skonfigurowano zarówno warunki użytkowania usługi Azure AD, jak i warunki usługi [Intune,](/intune/terms-and-conditions-create)użytkownik będzie musiał zaakceptować oba te warunki. Aby uzyskać więcej informacji, zobacz [rozwiązanie Wybieranie odpowiednich warunków dla wpisu w blogu organizacji](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**Pyt.: Jakie punkty końcowe używa usługi użytkowania do uwierzytelniania?**<br />
Odp.: Warunki użytkowania wykorzystują następujące punkty https://tokenprovider.termsofuse.identitygovernance.azure.com końcowe do uwierzytelniania: i https://account.activedirectory.windowsazure.com. Jeśli twoja organizacja ma listę dozwolonych adresów URL do rejestracji, należy dodać te punkty końcowe do listy dozwolonych, wraz z punktami końcowymi usługi Azure AD do logowania.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: wymagaj akceptowania warunków użytkowania przed uzyskiwaniem dostępu do aplikacji w chmurze](require-tou.md)
- [Najważniejsze wskazówki dotyczące dostępu warunkowego w usłudze Azure Active Directory](best-practices.md)
