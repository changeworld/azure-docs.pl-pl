---
title: Rozwiązywanie problemów z licencji dla grupy w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Sposób identyfikacji i rozwiązywania problemów przypisania licencji podczas korzystania z usługi Azure Active Directory na podstawie grupy licencji
services: active-directory
keywords: Licencjonowanie usługi Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e91b0a79e7b45ce7e0de1b7cf4aa3123550692af
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identyfikowanie i rozwiązywanie problemów przypisania licencji dla grupy w usłudze Azure Active Directory

Na podstawie grupy Licencjonowanie w usłudze Azure Active Directory (Azure AD) wprowadza pojęcia użytkowników w stanie błędu licencjonowania. W tym artykule opisano możemy uzasadnienie, dlaczego użytkownicy mogą wystąpić w tym stanie.

Po przypisaniu licencji bezpośrednio do poszczególnych użytkowników bez użycia na podstawie grupy licencjonowania operacji przypisania może zakończyć się niepowodzeniem. Na przykład podczas wykonywania polecenia cmdlet programu PowerShell `Set-MsolUserLicense` w systemie użytkownika, polecenie cmdlet może się nie powieść z wielu powodów związanych z logiki biznesowej. Na przykład może być niewystarczającą liczbę licencji lub konflikt między dwa pakiety usług, których nie można przypisać w tym samym czasie. Problem jest natychmiast raportowane do Ciebie.

Podczas korzystania z na podstawie grupy licencji, mogą wystąpić błędy tego samego, ale wystąpią w tle, gdy usługi Azure AD jest przypisywaniu licencji. Z tego powodu błędy nie komunikowane użytkownikowi bezpośrednio. Zamiast tego są rejestrowane na obiekt użytkownika i następnie zgłoszone za pośrednictwem portalu administracyjnego. Oryginalny zamiaru licencji użytkownika nigdy nie zostaną utracone, ale jest rejestrowany w stanie błędu w przyszłości badania i rozwiązywania.

## <a name="how-to-find-license-assignment-errors"></a>Jak znaleźć błędy przypisania licencji
**Aby znaleźć błędy przypisania licencji**

   1. Aby znaleźć użytkowników w stan błędu w określonej grupie, otwórz okienko dla grupy. W obszarze **licencji**, pojawi się powiadomienie, jeśli wszyscy użytkownicy w stanie błędu.

   ![Powiadomienie o błędzie grupy](media/active-directory-licensing-group-problem-resolution-azure-portal/group-error-notification.png)

   2. Wybierz powiadomienie, aby otworzyć listę wszystkich użytkowników. Możesz wybrać każdy użytkownik pojedynczo, aby zobaczyć więcej szczegółów.

   ![Grupy, listę użytkowników w stan błędu](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-users-with-errors.png)

   3. Można znaleźć wszystkich grup, które zawierają co najmniej jeden błąd, na **usługi Azure Active Directory** wybierz bloku **licencji**, a następnie wybierz **omówienie**. Informacje o okno jest wyświetlane, gdy grupy wymagają Twojej uwagi.

   ![Przegląd informacji na temat grup w stan błędu](media/active-directory-licensing-group-problem-resolution-azure-portal/group-errors-widget.png)

   4. Zaznacz to pole, aby wyświetlić listę wszystkich grup z błędami. Można wybrać więcej szczegółów w każdej grupie.

   ![Przegląd, lista grup błędów](media/active-directory-licensing-group-problem-resolution-azure-portal/list-of-groups-with-errors.png)


W poniższych sekcjach znajdują się opis każdego potencjalny problem i sposobu rozwiązania go.

## <a name="not-enough-licenses"></a>Za mało licencji

**Problem:** nie ma wystarczającej liczby dostępnych licencji na jeden z nich, które jest określone w grupie. Musisz kupić więcej licencji produktu lub zwolnić nieużywanych licencji z innym użytkownikom lub grupom.

Aby sprawdzić, ile licencji są dostępne, przejdź do **usługi Azure Active Directory** > **licencji** > **wszystkie produkty**.

Aby zobaczyć, którzy użytkownicy i grupy zużywają licencji, wybierz produkt. W obszarze **licencjonowani użytkownicy**, wyświetlić listę wszystkich użytkowników, którzy miały przypisane bezpośrednio lub za pośrednictwem jednej lub kilku grup licencji. W obszarze **licencjonowane grup**, zobacz wszystkich grup, które oferują produkty, że przypisane.

**Środowiska PowerShell:** poleceń cmdlet programu PowerShell, zgłoś ten błąd jako _CountViolation_.

## <a name="conflicting-service-plans"></a>Plany usług powodujące konflikty

**Problem:** zawiera jeden z nich, które jest określone w grupie usługi plan, który powoduje konflikt z innego planu usługi, która jest już przypisana do użytkownika za pośrednictwem innego produktu. Niektóre plany usługi są skonfigurowane w taki sposób, że nie można przypisać do tego samego użytkownika planu, pokrewne usługi.

Rozważmy następujący przykład. Użytkownik ma licencję dla Office 365 Enterprise *E1* przypisane bezpośrednio, wszystkie plany włączone. Użytkownik został dodany do grupy, która ma Office 365 Enterprise *E3* produktu przypisane do niej. Produkt E3 zawiera plany usługi, które nie może nakładać się z planami, uwzględnionych w E1, więc przypisania grupy licencji nie powiedzie się z powodu błędu "Powodujące konflikt planów usługi". W tym przykładzie są sprzeczne planów usług:

-   Usługi SharePoint Online (Planowanie 2) powoduje konflikt z usługą SharePoint Online (Planowanie 1).
-   Exchange Online (Planowanie 2) powoduje konflikt z usługą Exchange Online (Planowanie 1).

Aby rozwiązać ten konflikt, należy wyłączyć dwa planów. Możesz wyłączyć licencji E1, która jest bezpośrednio przypisane do użytkownika. Lub, należy zmodyfikować przypisanie licencji całej grupy i wyłączyć plany licencji E3. Alternatywnie można zdecydować usunąć licencji E1 od użytkownika, jeśli jest nadmiarowy w kontekście licencji E3.

Decyzja o tym, jak rozwiązać konflikt licencje produktów zawsze należy do administratora. Usługi Azure AD nie automatycznie rozwiązać konflikty licencji.

**Środowiska PowerShell:** poleceń cmdlet programu PowerShell, zgłoś ten błąd jako _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Inne produkty są zależne od tej licencji

**Problem:** zawiera jeden z nich, które jest określone w grupie planu usług, który musi być włączona dla innego planu usług, w innego produktu, aby funkcja. Ten błąd występuje, gdy próbuje usunąć podstawowego planu obsługi usługi Azure AD. Na przykład przyczyną może być usunięcie użytkownika z grupy.

Aby rozwiązać ten problem, należy się upewnić, że wymagany plan jest ciągle przypisany do użytkowników za pomocą innej metody lub czy zależne usługi zostały wyłączone dla tych użytkowników. Po tym prawidłowo można usunąć grupy licencji od tych użytkowników.

**Środowiska PowerShell:** poleceń cmdlet programu PowerShell, zgłoś ten błąd jako _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Lokalizacja użytkowania nie jest dozwolona

**Problem:** pewnych usług firmy Microsoft nie są dostępne we wszystkich lokalizacjach z powodu lokalnych przepisów eksportowych. Zanim można przypisać licencję do użytkownika, należy określić **lokalizacji użytkowania** właściwości dla użytkownika. Można określić lokalizację, w obszarze **użytkownika** > **profilu** > **ustawienia** sekcji w portalu Azure.

Jeśli usługi Azure AD spróbuje przypisać licencję grupy użytkowników, których lokalizacji użytkowania nie jest obsługiwane, nie powiedzie się i rejestruje błąd użytkownika.

Aby rozwiązać ten problem, należy usunąć użytkowników z nieobsługiwanych lokalizacji z licencjonowanym grupy. Alternatywnie Jeśli bieżące wartości użycia w lokalizacji nie reprezentować lokalizacji rzeczywistego użytkownika, można je zmodyfikować, dzięki czemu licencji są poprawnie przypisać następnym (jeśli jest obsługiwana w nowej lokalizacji).

**Środowiska PowerShell:** poleceń cmdlet programu PowerShell, zgłoś ten błąd jako _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Gdy usługi Azure AD przypisuje grupę licencji, wszyscy użytkownicy bez użycia określonej lokalizacji dziedziczą lokalizację katalogu. Zalecane Administratorzy ustawienie odpowiednie użycie wartości lokalizacji dla użytkowników przed użyciem na podstawie grupy licencji w celu zapewnienia przestrzegania lokalnych przepisów eksportowych.

## <a name="what-happens-when-theres-more-than-one-product-license-on-a-group"></a>Co się stanie, gdy jest kilku licencji produktu grupy?

Do grupy można przypisać kilku licencji produktu. Na przykład Office 365 Enterprise E3 i Enterprise Mobility + Security można przypisać do grupy, aby łatwo włączyć wszystkie dołączone usługi dla użytkowników.

Usługi Azure AD próbuje przypisać wszystkie licencje, które są określone w grupie do poszczególnych użytkowników. Jeśli usługi Azure AD nie można przypisać jeden z nich z powodu problemów logiki biznesowej, on nie albo przypisać innych licencji w grupie. Przykładem jest, jeśli nie ma wystarczającą liczbę licencji dla wszystkich lub jeśli występują konflikty z innymi usługami, które są włączone dla użytkownika.

Widoczny dla użytkowników, którzy nie zostaną przypisane i sprawdzić, które produkty ma wpływ ten problem.

## <a name="how-do-you-manage-licenses-for-products-with-prerequisites"></a>Jak zarządzać licencji na produkty z wymagań wstępnych

Niektóre produkty Online firmy Microsoft, może być właścicielem *dodatki*. Dodatki wymagają planu wymagań wstępnych usługi być włączone dla użytkownika lub grupy, aby przypisać licencję. Z licencją na podstawie grupy, system wymaga zarówno wymagań wstępnych, jak i dodatku planów usługi występuje w tej samej grupie. Można to zrobić, aby upewnić się, że wszyscy użytkownicy, którzy są dodawani do grupy może odbierać produktu pracy pełni. Teraz Rozważmy następujący przykład:

Microsoft pracy Analytics jest produktem dodatek. Zawiera on plan pojedynczą usługę o takiej samej nazwie. Firma Microsoft można przypisać tylko ten plan usługi do użytkownika lub grupy, gdy jeden z następujących warunków wstępnych jest również przypisany:
- Exchange Online (— Plan 1) 
- Exchange Online (— Plan 2)

Portalu zwraca błąd, jeśli spróbujemy można przypisać tego produktu samodzielnie do grupy. Wybranie powiadomienia o błędach zawiera następujące informacje:

![Brak grupy, wymagań wstępnych](media/active-directory-licensing-group-problem-resolution-azure-portal/group-prerequisite-required.png)

Zaznaczenie szczegóły, zawiera następujący komunikat o błędzie:

>Operacja licencji nie powiodło się. Upewnij się, że grupa ma niezbędne usługi przed dodaniem lub usunięciem usług zależnych. **Usługa Microsoft pracy Analytics wymaga usługi Exchange Online (Planowanie 2), można również włączyć.**

Aby przypisać to licencja dodatku do grupy, Upewniamy się, że grupa zawiera również planu obsługi wymagań wstępnych. Na przykład możemy zaktualizować istniejącej grupy, która zawiera już pełnego produktu Office 365 E3, a następnie dodaj produktu dodatek do niego.

Istnieje również możliwość utworzyć grupę autonomiczny zawierający tylko minimalne wymagane produkty dokonanie dodatek pracy. Może służyć do licencji produktu dodatek tylko wybrani użytkownicy. W tym przykładzie przypisano możemy w tej samej grupie następujące produkty:
- Usługa Office 365 Enterprise E3 z tylko usługi Exchange Online (Plan 2) planu obsługi włączone
- Dołączanie Microsoft Analytics

![Grupa, wstępnie wymaganego włączone](media/active-directory-licensing-group-problem-resolution-azure-portal/group-addon-with-prerequisite.png)

Odtąd dodanych do tej grupy użytkowników wykorzystywać jedną licencję produktu E3 i jedną licencję produktu analityka w miejscu pracy. W tym samym czasie Ci użytkownicy mogą być elementami członkowskimi innej grupy, co umożliwia im pełny produkt E3 i nadal korzystać tylko jeden licencji dla tego produktu.

> [!TIP]
> Można utworzyć wiele grup dla każdego planu usługi wymagań wstępnych. Na przykład jeśli używasz zarówno Office 365 Enterprise E1, jak i Office 365 Enterprise E3 dla użytkowników, należy utworzyć dwie grupy do licencji Microsoft Analytics dołączanie:, który używa E1 jako warunek wstępny i innych, która używa E3. Dzięki temu można rozpowszechniać dodatek użytkownikom E1 i E3 bez dodatkowe licencje.

## <a name="license-assignment-fails-silently-for-a-user-due-to-duplicate-proxy-addresses-in-exchange-online"></a>Przypisanie licencji nie powiedzie się dyskretnie dla użytkownika z powodu proxy zduplikowanych adresów w usłudze Exchange Online

Jeśli używasz usługi Exchange Online, w przypadku niektórych użytkowników w dzierżawie może być niepoprawnie skonfigurowana o tej samej wartości adres serwera proxy. Gdy na podstawie grupy licencji spróbuje przypisać licencję do tych użytkowników, nie powiedzie się i nie rejestruje błąd. Niepowodzeniu rejestrowania w tym wystąpieniu błędu jest to ograniczenie w wersji preview tej funkcji i zamierzamy adresów go przed *ogólnej dostępności*.

> [!TIP]
> Jeśli zauważysz, że niektórym użytkownikom nie otrzymały licencji i nie było błędu zarejestrowane dla tych użytkowników, najpierw sprawdź, czy mają one adres proxy zduplikowane.
> Aby sprawdzać, czy adres serwera proxy duplikatów, wykonaj następujące polecenia cmdlet programu PowerShell dla usługi Exchange Online:
```
Run Get-Recipient | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
```
> Aby uzyskać więcej informacji na temat tego problemu, zobacz ["jest już używana adres serwera Proxy" Błąd komunikatów w usłudze Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online). Artykuł zawiera także informacje o [sposób połączenia z usługą Exchange Online przy użyciu zdalnego programu PowerShell](https://technet.microsoft.com/library/jj984289.aspx).

Po rozwiązaniu problemów adres serwera proxy dla określonych użytkowników, upewnij się wymusić przetwarzania licencji w grupie, aby upewnić się, że teraz można zastosować licencji.

## <a name="how-do-you-force-license-processing-in-a-group-to-resolve-errors"></a>Jak wymusić przetwarzania licencji w grupie, aby naprawić błędy?

W zależności od tego, jakie kroki zostały wykonane w celu rozwiązania błędów może być konieczne ręcznie uruchomić przetwarzania grupy, aby zaktualizować stan użytkownika.

Na przykład jeśli niektórych licencji zwolnić przez usunięcie przypisań licencji bezpośrednio od użytkowników, należy do wyzwolenia przetwarzania grup, które wcześniej nie pełni licencji wszystkich członków użytkownika. Aby ponownie przetworzyć grupy, przejdź do okienka grupy, otwórz **licencji**, a następnie wybierz **ponownie przetworzyć** przycisk na pasku narzędzi.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat innych scenariuszy zarządzania licencji za pomocą grup, zobacz następujące tematy:

* [Przypisywanie licencji do grupy w usłudze Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Co to jest oparte na grupach Licencjonowanie w usłudze Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Jak przeprowadzić migrację poszczególnych licencjonowanych użytkowników do licencjonowania oparte na grupach w usłudze Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Usługa Azure Active Directory na podstawie grupy licencjonowania dodatkowe scenariusze](active-directory-licensing-group-advanced.md)
