---
title: Warunki użytkowania usługi Azure Active Directory | Microsoft Docs
description: Warunki użytkowania usługi Azure AD pozwalają administratorom i firmom udostępnić użytkownikom warunki użytkowania usług Azure AD.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.component: compliance-reports
ms.date: 05/15/2018
ms.author: rolyon
ms.openlocfilehash: 8fea445a4cd02da3cf3c3239a119b491327abf54
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234121"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funkcja Warunki użytkowania usługi Azure Active Directory
Warunki użytkowania usługi Azure AD pozwalają organizacjom przedstawiać informacje użytkownikom w prosty sposób.  Dzięki tej prezentacji użytkownicy mogą zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności.

Zawartość warunków użytkowania usługi Azure AD jest przedstawiana w formacie pdf.   Zawartość ta może być dowolna i obejmować na przykład istniejące dokumenty kontraktowe, umożliwiając gromadzenie umów użytkowników podczas logowania.  Warunki użytkowania mogą dotyczyć aplikacji lub grup użytkowników. Można również przygotować wiele wersji warunków użytkowania przeznaczonych do różnych celów.

W pozostałej części tego dokumentu opisano, jak rozpocząć korzystanie z warunków użytkowania usługi Azure AD.  

## <a name="why-use-azure-ad-terms-of-use"></a>Dlaczego warto używać warunków użytkowania usługi Azure AD
Masz problemy z uzyskaniem zgody pracowników lub gości na Twoje warunki użytkowania, zanim przyznasz dostęp tym osobom? Potrzebujesz pomocy w ustaleniu, które osoby wyraziły zgodę na firmowe warunki użytkowania, a które nie?  Warunki użytkowania usługi Azure AD pozwalają organizacjom przedstawiać informacje użytkownikom w prosty sposób.  Dzięki tej prezentacji mogą oni zapoznać się z istotnymi zastrzeżeniami do wymagań prawnych lub wymagań dotyczących zgodności.

Warunki użytkowania usługi Azure AD mogą być stosowane w następujących scenariuszach:
-   Ogólne warunki użytkowania obowiązujące wszystkich użytkowników w organizacji.
-   Specyficzne warunki użytkowania zależne od atrybutów użytkownika (np. warunki dla lekarzy różnią się od warunków dla pielęgniarek albo pracownicy krajowi mają inne warunki niż pracownicy międzynarodowi) i obsługiwane przez [grupy dynamiczne](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups).
-   Specyficzne warunki użytkowania stosowane podczas korzystania z aplikacji mających duży wpływ na działalność biznesową, takich jak Salesforce.


## <a name="prerequisites"></a>Wymagania wstępne
Aby skonfigurować warunki użytkowania usługi Azure AD, wykonaj następujące kroki:

1. Zaloguj się do usługi Azure AD za pomocą konta administratora globalnego, administratora zabezpieczeń lub administratora dostępu warunkowego do katalogu, dla którego chcesz skonfigurować warunki użytkowania usługi Azure AD.
2. Sprawdź, czy katalog ma subskrypcję usługi Azure AD w wersji Premium P1, Premium P2, EMS E3 lub EMS E5.  Jeśli tak nie jest, [uzyskaj usługę Azure AD — wersja Premium](active-directory-get-started-premium.md) lub [rozpocznij okres próbny](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Wyświetl pulpit nawigacyjny warunków użytkowania usługi Azure AD na stronie [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Kontrolki zasad dostępu warunkowego (w tym warunki użytkowania) nie obsługują wymuszania dla kont usługi.  Zaleca się wykluczenie wszystkich kont usług z zasad dostępu warunkowego.

## <a name="add-company-terms-of-use"></a>Dodawanie firmowych warunków użytkowania
Po zakończeniu pracy nad zawartością warunków użytkowania, dodaj je za pomocą poniższej procedury.

### <a name="to-add-terms-of-use"></a>Dodawanie warunków użytkowania
1. Przejdź do pulpitu nawigacyjnego w lokalizacji [https://aka.ms/catou](https://aka.ms/catou)
2. Kliknij pozycję Add (Dodaj).</br>
![Dodawanie warunków użytkowania](media/active-directory-tou/tou12.png)
3. W polu **Nazwa** wprowadź nazwę warunków użytkowania.
4. W polu **Nazwa wyświetlana** wprowadź nazwę wyświetlaną.  Nagłówek będzie widoczny dla użytkowników po zalogowaniu.
5. **Przejdź** do pliku pdf zawierającego końcową wersję warunków użytkowania i wybierz go.  Zalecany rozmiar czcionki to 24.
6. **Wybierz** język warunków użytkowania.  Opcja wyboru języka umożliwia przekazanie wielu wersji językowych warunków użytkowania.  Wersja warunków użytkowania widoczna dla użytkownika końcowego będzie zależała od preferencji jego przeglądarki.
7. Włącz lub wyłącz ustawienie **Wymagaj od użytkowników rozwinięcia warunków użytkowania**.  Jeśli to ustawienie jest włączone, użytkownicy końcowi będą musieli przejrzeć warunki użytkowania przed ich akceptacją.
8. W obszarze **Dostęp warunkowy** możesz **wymusić** stosowanie przekazanych warunków użytkowania, wybierając z listy rozwijanej szablon lub niestandardowe zasady dostępu warunkowego.  Niestandardowe zasady dostępu warunkowego pozwalają zwiększyć szczegółowość warunków użytkowania, które mogą dotyczyć pojedynczej aplikacji w chmurze lub grupy użytkowników.  Aby uzyskać więcej informacji, zobacz artykuł [Konfigurowanie zasad dostępu warunkowego](active-directory-conditional-access-best-practices.md).
9. Kliknij przycisk **Utwórz**.
10. W przypadku wybrania niestandardowego szablonu dostępu warunkowego pojawi się nowy ekran, na którym można dostosować zasady dostępu warunkowego.
11. Zostaną wyświetlone nowe warunki użytkowania.</br>

![Dodawanie warunków użytkowania](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Usuwanie warunków użytkowania
Stare warunki użytkowania można usunąć, korzystając z następującej procedury:

### <a name="to-delete-terms-of-use"></a>Usuwanie warunków użytkowania
1. Przejdź do pulpitu nawigacyjnego w lokalizacji [https://aka.ms/catou](https://aka.ms/catou)
2. Wybierz warunki użytkowania, które chcesz usunąć.
3. Kliknij polecenie **Usuń**.
4. Warunki użytkowania nie powinny już być widoczne.


## <a name="viewing-current-user-status"></a>Wyświetlanie bieżącego stanu użytkownika
Zauważysz, że warunki użytkowania przedstawiają liczbę użytkowników, którzy je zaakceptowali i odrzucili.

![Zdarzenie inspekcji](media/active-directory-tou/tou15.png)

Możesz kliknąć liczby w obszarze **Zaakceptowane** lub **Odrzucone**, aby wyświetlić bieżący stan użytkowników.

![Zdarzenie inspekcji](media/active-directory-tou/tou16.png)

## <a name="audit-terms-of-use"></a>Przeprowadzanie inspekcji warunków użytkowania
Jeśli chcesz wyświetlić historyczne akceptacje i odrzucenia, a nie tylko bieżący stan, warunki użytkowania usługi Azure AD oferują łatwą w użyciu funkcję inspekcji.  Umożliwia ona sprawdzenie, kto zaakceptował warunki użytkowania i kiedy nastąpiła akceptacja.  

W zależności od czynności, którą akurat próbujesz wykonać, inspekcję można przeprowadzić na dwa sposoby.  


Aby rozpocząć przeprowadzanie inspekcji, skorzystaj z poniższej procedury:

### <a name="to-audit-terms-of-use"></a>Przeprowadzanie inspekcji warunków użytkowania
1. Przejdź do pulpitu nawigacyjnego w lokalizacji [https://aka.ms/catou](https://aka.ms/catou)
2. Kliknij pozycję Wyświetl dzienniki inspekcji.</br>
![Zdarzenie inspekcji](media/active-directory-tou/tou8.png)
3.  Na ekranie dzienników inspekcji usługi Azure AD można filtrować informacje przy użyciu list rozwijanych, aby uzyskać konkretne dane z dzienników inspekcji.
[Zdarzenie inspekcji](media/active-directory-tou/tou9.png)
4.  Można również pobrać informacje w pliku csv do użytku lokalnego.

## 

## <a name="what-users-see"></a>Co widzą użytkownicy
Po utworzeniu warunków użytkowania i wymuszeniu ich stosowania uprawnieni użytkownicy zobaczą następujące informacje.  Ekrany te pojawią się podczas logowania.
-   Najlepszym rozwiązaniem jest użycie czcionki o rozmiarze 24 w pliku PDF.
![Zdarzenie inspekcji](media/active-directory-tou/tou10.png)
-   Tak wygląda ten ekran na urządzeniach przenośnych.</br></br>
![Zdarzenie inspekcji](media/active-directory-tou/tou11.png)

### <a name="review-terms-of-use"></a>Przeglądanie warunków użytkowania
Użytkownicy mogą wyświetlać i przeglądać warunki użytkowania, które zaakceptowali.  Aby zapoznać się z warunkami użytkowania, użyj następującej procedury:

1. Przejdź do lokalizacji [https://myapps.microsoft.com](https://myapps.microsoft.com) i zaloguj się w niej.
2. W prawym górnym rogu kliknij swoją nazwę i wybierz pozycję **Profil** z listy rozwijanej.
![Profil](media/active-directory-tou/tou14.png)

3. Na stronie profilu kliknij pozycję **Przejrzyj warunki użytkowania**.
![Zdarzenie inspekcji](media/active-directory-tou/tou13a.png)

4.  Następnie możesz przejrzeć zaakceptowane warunki użytkowania. 

## <a name="removing-users-from-an-active-terms-of-use"></a>Usuwanie użytkowników z aktywnych warunków użytkowania

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

Domyślnie usunięty użytkownik pozostaje usunięty w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby.  Po 30 dniach użytkownik jest trwale usuwany.  Ponadto przy użyciu portalu usługi Azure Active Directory administrator globalny może jawnie [trwale usunąć niedawno usuniętego użytkownika](active-directory-users-restore.md) przed upłynięciem tego okresu.  Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych warunków użytkowania.  Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.



## <a name="additional-information"></a>Dodatkowe informacje
Te ważne informacje mogą pomóc w korzystaniu z warunków użytkowania.

>[!IMPORTANT]
> W następujących przypadkach uprawnieni użytkownicy muszą się wylogować i zalogować ponownie, aby spełnić wymagania nowych zasad:
> - Dla warunków użytkowania włączono zasady dostępu warunkowego
> - Utworzono drugą wersję warunków użytkowania
>
>Zasady dostępu warunkowego zaczynają obowiązywać natychmiast. W takim przypadku administrator będzie widzieć objawy typu „smutne chmury” lub komunikaty o problemach z tokenami usługi Azure AD. Aby spełnić wymagania nowych zasad, administrator musi się wylogować i zalogować ponownie.





## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt. Jak sprawdzić, czy i kiedy użytkownik zaakceptował warunki użytkowania?**</br>
Odp. Należy kliknąć liczbę w obszarze Zaakceptowane obok warunków użytkowania.  Aby uzyskać więcej informacji, zobacz [Wyświetlanie bieżącego stanu użytkownika](#viewing-current-user-status).  Informacja o zaakceptowaniu warunków użytkowania przez użytkownika jest również zapisywana w dzienniku inspekcji. Aby ją znaleźć, można przejrzeć wyniki wyszukiwania w dzienniku inspekcji usługi Azure AD.  

**Pyt. Czy zmiana warunków użytkowania wymaga ich ponownej akceptacji przez użytkowników?**</br>
Odp. Tak, po zmianie warunków użytkowania przez administratora użytkownicy muszą ponownie zaakceptować nowe warunki.

**Pyt. Czy warunki użytkowania obsługują wiele języków?**</br>
Odp. Tak.  Obecnie istnieje 18 różnych języków, które administrator może skonfigurować na potrzeby pojedynczych warunków użytkowania. 

**Pyt. Kiedy są wyzwalane warunki użytkowania?**</br>
Odp. Warunki użytkowania są wyzwalane podczas logowania.

**Pyt. Jakie aplikacje mogą zostać objęte warunkami użytkowania?**</br>
Odp. Dla aplikacji przedsiębiorstwa można utworzyć zasady dostępu warunkowego korzystające z nowoczesnego uwierzytelniania.  Aby uzyskać więcej informacji, zobacz [aplikacje przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Pyt. Czy dla użytkownika lub aplikacji można określić wiele warunków użytkowania?**</br>
Odp. Tak, można to zrobić, tworząc różne wersje zasad dostępu warunkowego obejmujących grupy lub aplikacje. Jeśli danego użytkownika dotyczy wiele warunków użytkowania, musi on zaakceptować je pojedynczo.
 
**Pyt. Co się stanie, jeśli użytkownik odrzuci warunki użytkowania?**</br>
Odp. Dostęp do aplikacji zostanie zablokowany dla tego użytkownika. Aby uzyskać dostęp, użytkownik ten musi zalogować się ponownie i zaakceptować warunki użytkowania.
