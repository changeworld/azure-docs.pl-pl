---
title: Konfigurowanie przepływu pracy zgody administratora — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować sposób żądania dostępu użytkowników końcowych do aplikacji wymagających zgody administratora.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b3f0d97daf0b4ac17f74981119b380d1776d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430201"
---
# <a name="configure-the-admin-consent-workflow-preview"></a>Konfigurowanie przepływu pracy zgody administratora (wersja zapoznawcza)

W tym artykule opisano sposób włączania funkcji przepływu pracy zgody administratora (wersja zapoznawcza), która umożliwia użytkownikom końcowym żądanie dostępu do aplikacji wymagających zgody administratora.

Bez przepływu pracy zgody administratora użytkownik w dzierżawie, w której jest wyłączona zgoda użytkownika, zostanie zablokowany podczas próby uzyskania dostępu do dowolnej aplikacji, która wymaga uprawnień dostępu do danych organizacji. Użytkownik zobaczy ogólny komunikat o błędzie informujący, że jest nieupoważniony, aby uzyskać dostęp do aplikacji i powinien poprosić administratora o pomoc. Ale często użytkownik nie wie, z kim się skontaktować, więc albo zrezygnować lub utworzyć nowe konto lokalne w aplikacji. Nawet gdy administrator jest powiadamiany, nie zawsze istnieje usprawniony proces, aby pomóc administratorowi udzielić dostępu i powiadomić ich użytkowników.
 
Przepływ pracy zgody administratora daje administratorom bezpieczny sposób udzielania dostępu do aplikacji, które wymagają zatwierdzenia przez administratora. Gdy użytkownik próbuje uzyskać dostęp do aplikacji, ale nie może udzielić zgody, może wysłać żądanie zatwierdzenia administratora. Żądanie jest wysyłane pocztą e-mail do administratorów, którzy zostali wyznaczeni jako recenzenci. Recenzent podejmuje działania w żądaniu, a użytkownik jest powiadamiany o akcji.

Aby zatwierdzać żądania, recenzent musi być administratorem globalnym, administratorem aplikacji w chmurze lub administratorem aplikacji. Recenzent musi mieć już przypisaną jedną z tych ról administratora; po prostu wyznaczanie ich jako recenzenta nie podnosi ich uprawnień.

## <a name="enable-the-admin-consent-workflow"></a>Włączanie przepływu pracy zgody administratora

Aby włączyć przepływ pracy zgody administratora i wybrać recenzentów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) jako administrator globalny.
2. Kliknij **pozycję Wszystkie usługi** u góry menu nawigacji po lewej stronie. Zostanie otwarte **rozszerzenie usługi Azure Active Directory.**
3. W polu wyszukiwania filtru wpisz **"Azure Active Directory**" i wybierz element **usługi Azure Active Directory.**
4. W menu nawigacyjnym kliknij polecenie **Aplikacje przedsiębiorstwa**. 
5. W obszarze **Zarządzanie**wybierz **pozycję Ustawienia użytkownika**.
6. W obszarze **Żądania zgody administratora (Wersja zapoznawcza)** **ustaw, że użytkownicy mogą żądać zgody administratora do aplikacji, na które nie mogą wyrazić zgody** na **Tak**.

   ![Konfigurowanie ustawień przepływu pracy zgody administratora](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Skonfiguruj następujące ustawienia:

   * **Wybierz użytkowników, aby przejrzeć żądania zgody administratora**. Wybierz recenzentów dla tego przepływu pracy z zestawu użytkowników, którzy mają administratora globalnego, administratora aplikacji w chmurze i administratora aplikacji role.
   * **Wybrani użytkownicy będą otrzymywać powiadomienia e-mail o żądaniach**. Włączanie lub wyłączanie powiadomień e-mail do recenzentów podczas składania żądania.  
   * **Wybrani użytkownicy otrzymają przypomnienia o wygaśnięciu żądania**. Włącz lub wyłącz powiadomienia e-mail z przypomnieniem do recenzentów, gdy żądanie wkrótce wygaśnie.  
   * **Wniosek o zgodę wygasa po (dniach)**. Określ, jak długo żądania pozostają ważne.

7. Wybierz **pozycję Zapisz**. Włączenie tej funkcji może potrwać do godziny.

> [!NOTE]
> Recenzentów dla tego przepływu pracy można dodać lub usunąć, modyfikując listę **Recenzentów żądań zgody administratora wybierz.** Należy zauważyć, że bieżące ograniczenie tej funkcji jest, że recenzenci mogą zachować możliwość przeglądania żądań, które zostały złożone, gdy zostały one wyznaczone jako recenzent.

## <a name="how-users-request-admin-consent"></a>Jak użytkownicy żądają zgody administratora

Po włączeniu przepływu pracy zgody administratora użytkownicy mogą zażądać zatwierdzenia przez administratora aplikacji, na którą mają nieautoryzowane zgody. W poniższych krokach opisano środowisko użytkownika podczas żądania zatwierdzenia. 

1. Użytkownik próbuje zalogować się do aplikacji.

2. Zostanie wyświetlony komunikat **Wymagane zatwierdzenie.** Użytkownik wpisuje uzasadnienie potrzeby dostępu do aplikacji, a następnie wybiera **opcję Zażądaj zatwierdzenia**.

   ![Prośba i uzasadnienie użytkownika zgody administratora](media/configure-admin-consent-workflow/end-user-justification.png)

3. Wiadomość **wysłana żądanie** potwierdza, że żądanie zostało przesłane do administratora. Jeśli użytkownik wyśle kilka żądań, tylko pierwsze żądanie jest przesyłane do administratora.

   ![Prośba i uzasadnienie użytkownika zgody administratora](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. Użytkownik otrzymuje powiadomienie e-mail, gdy jego żądanie zostanie zatwierdzone, odrzucone lub zablokowane. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Przeglądanie i podejmowanie działań w sprawie żądań zgody administratora

Aby przejrzeć żądania zgody administratora i podjąć działania:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako jeden z zarejestrowanych recenzentów przepływu pracy zgody administratora.
2. Wybierz **pozycję Wszystkie usługi** w górnej części menu nawigacji po lewej stronie. Zostanie otwarte **rozszerzenie usługi Azure Active Directory.**
3. W polu wyszukiwania filtru wpisz **"Azure Active Directory**" i wybierz element **usługi Azure Active Directory.**
4. W menu nawigacyjnym kliknij polecenie **Aplikacje przedsiębiorstwa**.
5. W obszarze **Aktywność**wybierz pozycję **Żądania zgody administratora (Wersja zapoznawcza)**.

   > [!NOTE]
   > Recenzenci zobaczą tylko żądania administratora, które zostały utworzone po tym, jak zostały wyznaczone jako recenzent.

1. Wybierz żądaną aplikację.
2. Przejrzyj szczegóły dotyczące żądania:  

   * Aby zobaczyć, kto i dlaczego żąda dostępu, wybierz kartę **Żądanie według.**
   * Aby zobaczyć, jakie uprawnienia są wymagane przez aplikację, wybierz **pozycję Przejrzyj uprawnienia i zgodę**.

8. Oceń wniosek i podejmij odpowiednie działania:

   * **Zatwierdź żądanie**. Aby zatwierdzić żądanie, udziel zgody administratora aplikacji. Po zatwierdzeniu żądania wszyscy żądacze są powiadamiani o przyznaniu im dostępu.  
   * **Odmów żądania**. Aby odrzucić żądanie, należy podać uzasadnienie, które zostanie dostarczone do wszystkich żądań. Po odrzuceniu żądania wszyscy żądacze są powiadamiani, że odmówiono im dostępu do aplikacji. Odmowa żądania nie uniemożliwi użytkownikom ponownego żądania zgody administratora aplikacji w przyszłości.  
   * **Zablokuj żądanie**. Aby zablokować żądanie, należy podać uzasadnienie, które zostanie dostarczone do wszystkich żądań. Po zablokowaniu żądania wszyscy żądacze są powiadamiani, że odmówiono im dostępu do aplikacji. Blokowanie żądania tworzy obiekt jednostkowy usługi dla aplikacji w dzierżawie w stanie wyłączonym. Użytkownicy nie będą mogli żądać zgody administratora aplikacji w przyszłości.
 
## <a name="email-notifications"></a>Powiadomienia e-mail
 
Jeśli jest skonfigurowana, wszyscy recenzenci otrzymają powiadomienia e-mail, gdy:

* Utworzono nowe żądanie
* Wniosek wygasł
* Żądanie zbliża się do daty wygaśnięcia  
 
Żądacze otrzymają powiadomienia e-mail, gdy:

* Przedkładają oni nowy wniosek o dostęp
* Ich wniosek wygasł
* Ich wniosek został odrzucony lub zablokowany
* Ich wniosek został zatwierdzony
 
## <a name="audit-logs"></a>Dzienniki inspekcji 
 
W poniższej tabeli przedstawiono scenariusze i wartości inspekcji dostępne dla przepływu pracy zgody administratora. 

> [!NOTE]
> Kontekst użytkownika aktora inspekcji jest obecnie brakuje we wszystkich scenariuszach. Jest to znane ograniczenie w wersji zapoznawczej.


|Scenariusz  |Usługa audytu  |Kategorii inspekcji  |Działanie inspekcji  |Aktor inspekcji  |Ograniczenia dziennika inspekcji  |
|---------|---------|---------|---------|---------|---------|
|Administrator włączający przepływ pracy żądania zgody        |Przeglądy dostępu           |UserManagement (UserManagement)           |Tworzenie szablonu zasad nadzoru          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika            |
|Administrator wyłącza przepływ pracy żądania zgody       |Przeglądy dostępu           |UserManagement (UserManagement)           |Usuwanie szablonu zasad nadzoru          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Administrator aktualizujący konfiguracje przepływu pracy zgody        |Przeglądy dostępu           |UserManagement (UserManagement)           |Aktualizowanie szablonu zasad nadzoru          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Użytkownik końcowy tworzący żądanie zgody administratora dla aplikacji       |Przeglądy dostępu           |Zasady         |Utwórz żądanie           |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika           |
|Recenzenci zatwierdzający żądanie zgody administratora       |Przeglądy dostępu           |UserManagement (UserManagement)           |Zatwierdzanie wszystkich żądań w przepływie biznesowym          |Kontekst aplikacji            |Obecnie nie można znaleźć kontekstu użytkownika ani identyfikatora aplikacji, któremu udzielono zgody administratora.           |
|Recenzenci odmawiający żądania zgody administratora       |Przeglądy dostępu           |UserManagement (UserManagement)           |Zatwierdzanie wszystkich żądań w przepływie biznesowym          |Kontekst aplikacji            | Obecnie nie można znaleźć kontekstu użytkownika aktora, który odmówił żądania zgody administratora          |

## <a name="faq"></a>Najczęściej zadawane pytania 

**Włączyłem ten przepływ pracy, ale podczas testowania funkcji, dlaczego nie widzę nowego monitu "Wymagane zatwierdzenie", który pozwala mi zażądać dostępu?**

Po włączeniu tej funkcji może upłynąć do 60 minut, aby użytkownicy końcowi zobaczyli aktualizację. Można sprawdzić, czy konfiguracja została poprawnie zaa ukonwersji, wyświetlając `https://graph.microsoft.com/beta/settings` wartość **EnableAdminConsentRequests** w interfejsie API.

**Dlaczego jako recenzent nie widzę wszystkich oczekujących żądań?**

Recenzenci mogą zobaczyć tylko żądania administratora, które zostały utworzone po ich wyznaczeniu jako recenzenta. Jeśli więc zostałeś niedawno dodany jako recenzent, nie zobaczysz żadnych żądań utworzonych przed przypisaniem.

**Dlaczego jako recenzent widzę wiele żądań dla tej samej aplikacji?**
  
Jeśli deweloper aplikacji skonfigurował swoją aplikację do używania statycznej i dynamicznej zgody na żądanie dostępu do danych użytkownika końcowego, zobaczysz dwa żądania zgody administratora. Jedno żądanie reprezentuje uprawnienia statyczne, a drugie reprezentuje uprawnienia dynamiczne.

**Czy jako z żądaniem mogę sprawdzić status mojej prośby?**  

Nie, na razie żądacze mogą otrzymywać aktualizacje tylko za pośrednictwem powiadomień e-mail.

**Czy jako recenzent można zatwierdzić wniosek, ale nie dla wszystkich?**
 
Jeśli obawiasz się o udzielenie zgody administratora i umożliwienie wszystkim użytkownikom w dzierżawie korzystania z aplikacji, zaleca się odrzucenie żądania. Następnie ręcznie udziel zgody administratora, ograniczając dostęp do aplikacji, wymagając przypisania użytkownika i przypisując użytkowników lub grupy do aplikacji. Aby uzyskać więcej informacji, zobacz [Metody przypisywania użytkowników i grup](methods-for-assigning-users-and-groups.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zgody na aplikacje, zobacz [Framework zgody usługi Azure Active Directory](../develop/consent-framework.md).

[Konfigurowanie sposobu wyrażania zgody użytkowników końcowych na aplikacje](configure-user-consent.md)

[Udzielanie zgody administratora dla całej dzierżawy na aplikację](grant-admin-consent.md)

[Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](../develop/active-directory-v2-scopes.md)

[Usługa Azure AD w przepływie stosuPrzepełnienie](https://stackoverflow.com/questions/tagged/azure-active-directory)
