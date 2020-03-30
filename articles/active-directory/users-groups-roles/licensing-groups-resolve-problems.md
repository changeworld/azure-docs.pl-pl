---
title: Rozwiązywanie problemów z przypisywaniem licencji grupowych — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Jak identyfikować i rozwiązywać problemy z przypisywaniem licencji podczas korzystania z licencjonowania opartego na grupach usługi Azure Active Directory
services: active-directory
keywords: Zarządzanie licencjonowaniem w usłudze Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfc4bf7ed3bdf214a44a5dfe03259d32b2f3f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025693"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identyfikowanie i rozwiązywanie problemów z przypisaniem licencji dla grupy w usłudze Azure Active Directory

Licencjonowanie oparte na grupach w usłudze Azure Active Directory (Azure AD) wprowadza koncepcję użytkowników w stanie błędu licencjonowania. W tym artykule wyjaśniamy powody, dla których użytkownicy mogą skończyć w tym stanie.

W przypadku przypisywania licencji bezpośrednio do poszczególnych użytkowników bez korzystania z licencjonowania opartego na grupach operacja przypisania może zakończyć się niepowodzeniem. Na przykład podczas wykonywania polecenia cmdlet `Set-MsolUserLicense` programu PowerShell w systemie użytkownika, polecenie cmdlet może zakończyć się niepowodzeniem z wielu powodów, które są związane z logiką biznesową. Na przykład może istnieć niewystarczająca liczba licencji lub konflikt między dwoma planami usług, których nie można przypisać w tym samym czasie. Problem jest natychmiast zgłaszany z powrotem do Ciebie.

Podczas korzystania z licencjonowania opartego na grupach mogą wystąpić te same błędy, ale występują one w tle, gdy usługa Azure AD przypisuje licencje. Z tego powodu błędy nie mogą być natychmiast przekazane. Zamiast tego są one rejestrowane w obiekcie użytkownika, a następnie zgłaszane za pośrednictwem portalu administracyjnego. Oryginalny zamiar licencjonowanie użytkownika nigdy nie zostanie utracony, ale jest rejestrowany w stanie błędu dla przyszłych badań i rozwiązania.

## <a name="find-license-assignment-errors"></a>Znajdowanie błędów przypisania licencji

### <a name="to-find-users-in-an-error-state-in-a-group"></a>Aby znaleźć użytkowników w stanie błędu w grupie

1. Otwórz grupę na jej stronie przeglądu i wybierz pozycję **Licencje**. Powiadomienie pojawia się, jeśli są użytkownicy w stanie błędu.

   ![Wiadomość z powiadomieniami o grupach i błędach](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Wybierz powiadomienie, aby otworzyć listę wszystkich użytkowników, których dotyczy problem. Możesz wybrać każdego użytkownika indywidualnie, aby wyświetlić więcej szczegółów.

   ![lista użytkowników w stanie błędu licencjonowania grupowego](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Aby znaleźć wszystkie grupy zawierające co najmniej jeden błąd, w bloku **usługi Azure Active Directory** wybierz pozycję **Licencje**, a następnie wybierz **pozycję Przegląd**. Okno informacyjne jest wyświetlane, gdy grupy wymagają uwagi.

   ![Omówienie i informacje o grupach w stanie błędu](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Zaznacz to pole, aby wyświetlić listę wszystkich grup z błędami. Aby uzyskać więcej informacji, można wybrać każdą grupę.

   ![Przegląd i lista grup z błędami](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

W poniższych sekcjach przedstawiono opis każdego potencjalnego problemu i sposób jego rozwiązania.

## <a name="not-enough-licenses"></a>Za mało licencji

**Problem:** Nie ma wystarczającej liczby dostępnych licencji dla jednego z produktów określonych w grupie. Musisz kupić więcej licencji na produkt lub zwolnić nieużywane licencje od innych użytkowników lub grup.

Aby zobaczyć, ile licencji jest dostępnych, przejdź do usługi**Licencje** >  **usługi Azure Active Directory** > Wszystkie**produkty**.

Aby zobaczyć, którzy użytkownicy i grupy korzystają z licencji, wybierz produkt. W obszarze **Licencjonowani użytkownicy**zobaczysz listę wszystkich użytkowników, którzy mieli licencje przypisane bezpośrednio lub za pośrednictwem jednej lub większej liczby grup. W **obszarze Grupy licencjonowane**są widoczne wszystkie grupy, do których przypisano te produkty.

**Program PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Sprzeczne plany usług

**Problem:** Jeden z produktów, który jest określony w grupie zawiera plan usług, który jest w konflikcie z innym planem usług, który jest już przypisany do użytkownika za pośrednictwem innego produktu. Niektóre plany usług są skonfigurowane w taki sposób, że nie można ich przypisać do tego samego użytkownika co inny, powiązany plan usług.

Rozważmy następujący przykład. Użytkownik ma licencję na usługi Office 365 Enterprise *E1* przypisaną bezpośrednio, z włączoną obsługą wszystkich planów. Użytkownik został dodany do grupy z przypisanym produktem Office 365 Enterprise *E3.* Produkt E3 zawiera plany usług, które nie mogą pokrywać się z planami zawartymi w E1, więc przypisanie licencji grupy kończy się niepowodzeniem z błędem "Sprzeczne plany usług". W tym przykładzie sprzeczne plany usług są:

- SharePoint Online (Plan 2) powoduje konflikt z usługą SharePoint Online (plan 1).
- Usługa Exchange Online (plan 2) powoduje konflikt z usługą Exchange Online (plan 1).

Aby rozwiązać ten konflikt, należy wyłączyć dwa plany. Można wyłączyć licencję E1, która jest bezpośrednio przypisana do użytkownika. Należy też zmodyfikować całe przypisanie licencji grupy i wyłączyć plany w licencji E3. Alternatywnie można podjąć decyzję o usunięciu licencji E1 od użytkownika, jeśli jest zbędna w kontekście licencji E3.

Decyzja dotycząca sposobu rozwiązywania sprzecznych licencji produktów zawsze należy do administratora. Usługa Azure AD nie rozwiązuje automatycznie konfliktów licencji.

**Program PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _WzajemniewyłączneWizolacja_.

## <a name="other-products-depend-on-this-license"></a>Inne produkty zależą od tej licencji

**Problem:** Jeden z produktów, który jest określony w grupie zawiera plan usług, który musi być włączony dla innego planu usług, w innym produkcie, do działania. Ten błąd występuje, gdy usługa Azure AD próbuje usunąć podstawowy plan usługi. Na przykład może się to zdarzyć po usunięciu użytkownika z grupy.

Aby rozwiązać ten problem, należy upewnić się, że wymagany plan jest nadal przypisany do użytkowników za pomocą innej metody lub że usługi zależne są wyłączone dla tych użytkowników. Po wykonaniu tej tej pracy można poprawnie usunąć licencję grupy z tych użytkowników.

**Program PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _dependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Lokalizacja użycia jest niedozwolona

**Problem:** Niektóre usługi firmy Microsoft nie są dostępne we wszystkich lokalizacjach ze względu na lokalne przepisy i regulacje. Przed przypisaniem licencji do użytkownika należy określić właściwość **Lokalizacja użycia** dla użytkownika. Lokalizację można określić w sekcji**Ustawienia** **profilu** >  **użytkownika** > w witrynie Azure portal.

Gdy usługa Azure AD próbuje przypisać licencję grupy do użytkownika, którego lokalizacja użycia nie jest obsługiwana, kończy się niepowodzeniem i rejestruje błąd użytkownika.

Aby rozwiązać ten problem, usuń użytkowników z nieobsługiconych lokalizacji z licencjonowanej grupy. Alternatywnie, jeśli bieżące wartości lokalizacji użycia nie reprezentują rzeczywistej lokalizacji użytkownika, można je zmodyfikować, tak aby licencje były poprawnie przypisane następnym razem (jeśli nowa lokalizacja jest obsługiwana).

**Program PowerShell:** Polecenia cmdlet programu PowerShell zgłaszają ten błąd jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Gdy usługa Azure AD przypisuje licencje grupowe, użytkownicy bez określonej lokalizacji użycia dziedziczą lokalizację katalogu. Zaleca się, aby administratorzy ustawiali poprawne wartości lokalizacji użycia użytkowników przed użyciem licencjonowania opartego na grupach w celu zapewnienia zgodności z lokalnymi przepisami i regulacjami.

## <a name="duplicate-proxy-addresses"></a>Zduplikowane adresy proxy

Jeśli używasz usługi Exchange Online, niektórzy użytkownicy w dzierżawie mogą być niepoprawnie skonfigurowane z tej samej wartości adresu serwera proxy. Gdy licencjonowanie oparte na grupach próbuje przypisać licencję do takiego użytkownika, kończy się niepowodzeniem i pokazuje "Adres serwera proxy jest już używany".

> [!TIP]
> Aby sprawdzić, czy istnieje zduplikowany adres serwera proxy, wykonaj następujące polecenie cmdlet programu PowerShell względem usługi Exchange Online:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Aby uzyskać więcej informacji na temat tego problemu, zobacz [komunikat o błędzie "Adres serwera proxy jest już używany" w usłudze Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artykuł zawiera również informacje na temat [łączenia się z usługą Exchange Online przy użyciu zdalnego programu PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

Po rozwiązaniu problemów związanych z adresem serwera proxy dla użytkowników, których dotyczy problem, należy wymusić przetwarzanie licencji w grupie, aby upewnić się, że licencje mogą być teraz stosowane.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Zmiana atrybutu Poczta usługi Azure AD i ProxyAddresses

**Problem:** Podczas aktualizowania przypisania licencji dla użytkownika lub grupy, może się okazać, że poczta usługi Azure AD i proxyAddresses atrybut niektórych użytkowników są zmieniane.

Aktualizowanie przypisania licencji dla użytkownika powoduje wyzwolenie obliczania adresu serwera proxy, co może zmienić atrybuty użytkownika. Aby zrozumieć dokładną przyczynę zmiany i rozwiązać problem, zobacz ten artykuł dotyczący [sposobu wypełniania atrybutu proxyAddresses w usłudze Azure AD.](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException w dziennikach inspekcji

**Problem:** Użytkownik ma LicenseAssignmentAttributeConcurrencyException do przypisania licencji w dziennikach inspekcji.
Podczas licencjonowania opartego na grupach próbuje przetwarzać równoczesne przypisanie licencji tej samej licencji do użytkownika, ten wyjątek jest rejestrowany na użytkownika. Zwykle dzieje się tak, gdy użytkownik jest członkiem więcej niż jednej grupy z tą samą przypisaną licencją. AZure AD ponowi próbę przetworzenia licencji użytkownika i rozwiąże problem. Nie ma żadnych działań wymaganych od klienta, aby rozwiązać ten problem.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Więcej niż jedna licencja na produkt przypisana do grupy

Do grupy można przypisać więcej niż jedną licencję produktu. Na przykład można przypisać usługi Office 365 Enterprise E3 i Enterprise Mobility + Security do grupy, aby łatwo włączyć wszystkie dołączone usługi dla użytkowników.

Usługa Azure AD próbuje przypisać do każdego użytkownika wszystkie licencje określone w grupie. Jeśli usługa Azure AD nie może przypisać jednego z produktów z powodu problemów z logiką biznesową, nie przypisze również innych licencji w grupie. Przykładem jest, jeśli nie ma wystarczającej liczby licencji dla wszystkich lub jeśli istnieją konflikty z innymi usługami, które są włączone na użytkownika.

Możesz zobaczyć użytkowników, którym nie udało się uzyskać przypisania i sprawdzić, które produkty są dotknięte tym problemem.

## <a name="when-a-licensed-group-is-deleted"></a>Po usunięciu grupy licencjonowanej

Przed usunięciem grupy należy usunąć wszystkie licencje przypisane do grupy. Jednak usunięcie licencji od wszystkich użytkowników w grupie może zająć trochę czasu. Podczas usuwania przypisań licencji z grupy mogą wystąpić błędy, jeśli użytkownik ma przypisaną licencję zależną lub jeśli występuje problem z konfliktem adresu serwera proxy, który zabrania usuwania licencji. Jeśli użytkownik ma licencję zależną od licencji, która jest usuwana z powodu usunięcia grupy, przypisanie licencji do użytkownika jest konwertowane z dziedziczonego na bezpośrednie.

Rozważmy na przykład grupę z włączoną usługą Skype 365 E3/E5 z włączonym planem usług programu Skype dla firm. Wyobraź sobie również, że kilku członków grupy ma licencje audiokonferencji przypisane bezpośrednio. Po usunięciu grupy licencjonowanie oparte na grupach spróbuje usunąć usługi Office 365 E3/E5 ze wszystkich użytkowników. Ponieważ konferencje audio są zależne od programu Skype dla firm, dla wszystkich użytkowników z przypisanymi konferencjami audio licencjonowanie oparte na grupach konwertuje licencje usługi Office 365 E3/E5 na bezpośrednie przypisanie licencji.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Zarządzanie licencjami na produkty z wymaganiami wstępnymi

Niektóre produkty firmy Microsoft Online, które możesz *posiadać,* to dodatki . Dodatki wymagają włączenia planu usługi wstępnego dla użytkownika lub grupy, zanim będzie można przypisać im licencję. W przypadku licencjonowania opartego na grupach system wymaga obecności w tej samej grupie zarówno planów usług wymaganych, jak i dodatkowych. Odbywa się to w celu zapewnienia, że każdy użytkownik, którzy są dodawane do grupy mogą otrzymać w pełni działający produkt. Rozważmy następujący przykład:

Microsoft Workplace Analytics to produkt dodatkowy. Zawiera pojedynczy plan usługi o tej samej nazwie. Możemy przypisać ten plan usługi tylko do użytkownika lub grupy, gdy przypisany jest również jeden z następujących wymagań wstępnych:

- Usługa Exchange Online (plan 1)
- Usługa Exchange Online (plan 2)

Jeśli spróbujemy przypisać ten produkt samodzielnie do grupy, portal zwróci komunikat z powiadomieniem. Jeśli wybierzemy szczegóły elementu, zostanie wyświetlony następujący komunikat o błędzie:

  "Operacja licencji nie powiodła się. Upewnij się, że grupa ma niezbędne usługi przed dodaniem lub usunięciem usługi zależnej. Usługa Microsoft Workplace Analytics wymaga również **włączenia usługi Exchange Online (Plan 2).**

Aby przypisać tę licencję dodatku do grupy, musimy upewnić się, że grupa zawiera również plan usługi wymaganego. Na przykład możemy zaktualizować istniejącą grupę, która już zawiera pełny produkt office 365 E3, a następnie dodać do niej produkt dodatkowy.

Istnieje również możliwość utworzenia autonomicznej grupy, która zawiera tylko minimalne wymagane produkty, aby dodatek działał. Może służyć do licencjonowannia tylko wybranych użytkowników dla produktu dodatkowego. Na podstawie poprzedniego przykładu należy przypisać następujące produkty do tej samej grupy:

- Usługa Office 365 Enterprise E3 z włączonym tylko planem usługi Exchange Online (Plan 2)
- Microsoft Workplace Analytics

Od tej pory wszyscy użytkownicy dodana do tej grupy korzystają z jednej licencji produktu E3 i jednej licencji produktu Workplace Analytics. W tym samym czasie ci użytkownicy mogą być członkami innej grupy, która daje im pełny produkt E3 i nadal zużywają tylko jedną licencję dla tego produktu.

> [!TIP]
> Można utworzyć wiele grup dla każdego planu usługi wymaganego. Jeśli na przykład użytkownikom używasz zarówno usługi Office 365 Enterprise E1, jak i usługi Office 365 Enterprise E3 dla użytkowników, możesz utworzyć dwie grupy, które licencjonują usługę Microsoft Workplace Analytics: jedną, która używa E1 jako wymagania wstępnego, a drugą używam E3. Dzięki temu można rozpowszechniać dodatek do użytkowników E1 i E3 bez korzystania z dodatkowych licencji.

## <a name="force-group-license-processing-to-resolve-errors"></a>Wymuszanie przetwarzania licencji grupowej w celu rozwiązania problemów

W zależności od kroków podjętych w celu rozwiązania błędów może być konieczne ręczne wyzwolenie przetwarzania grupy w celu zaktualizowania stanu użytkownika.

Jeśli na przykład niektóre licencje są zwalniane przez usunięcie bezpośrednich przypisań licencji od użytkowników, należy wyzwolić przetwarzanie grup, których wcześniej nie udało się w pełni licencjonować wszystkim członkom użytkownika. Aby ponownie przetworzyć grupę, przejdź do okienka grupy, otwórz **pole licencjonujące**, a następnie wybierz przycisk **Ponownietwórz** na pasku narzędzi.

## <a name="force-user-license-processing-to-resolve-errors"></a>Wymuszanie przetwarzania licencji użytkownika w celu rozwiązania problemów

W zależności od kroków podjętych w celu rozwiązania błędów może być konieczne ręczne wyzwolenie przetwarzania użytkownika w celu zaktualizowania stanu użytkowników.

Na przykład po rozwiązaniu problemu zduplikowanym adresem serwera proxy dla danego użytkownika należy wyzwolić przetwarzanie użytkownika. Aby ponownie przetworzyć użytkownika, przejdź do okienka użytkownika, otwórz **pole licencjonujące**, a następnie wybierz przycisk **Ponownietwórz** na pasku narzędzi.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych scenariuszach zarządzania licencjami za pośrednictwem grup, zobacz następujące elementy:

* [Co to jest licencjonowanie oparte na grupach w usłudze Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](licensing-groups-assign.md)
* [Jak migrować użytkowników z licencjami indywidualnymi do licencji opartych na grupach w usłudze Azure Active Directory](licensing-groups-migrate-users.md)
* [Jak migrować użytkowników między licencjami produktów przy użyciu licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-groups-change-licenses.md)
* [Dodatkowe scenariusze licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-group-advanced.md)
* [Przykłady programu PowerShell dotyczące licencjonowania opartego na grupach w usłudze Azure Active Directory](licensing-ps-examples.md)
