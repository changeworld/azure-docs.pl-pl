---
title: Dodawanie i weryfikowanie niestandardowych nazw domen — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Pojęcia dotyczące zarządzania i instrukcje zarządzania nazwą domeny w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559237"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory

Nazwa domeny jest ważną częścią identyfikatora dla wielu zasobów katalogu: jest częścią nazwy użytkownika lub adresu e-mail użytkownika, część adresu grupy i czasami jest częścią identyfikatora URI identyfikatora aplikacji dla aplikacji. Zasób w usłudze Azure Active Directory (Azure AD) może zawierać nazwę domeny należącą do katalogu zawierającego zasób. Tylko administrator globalny może zarządzać domenami w usłudze Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ustawianie podstawowej nazwy domeny katalogu usługi Azure AD

Podczas tworzenia katalogu początkowa nazwa domeny, taka jak "contoso.onmicrosoft.com", jest również podstawową nazwą domeny. Domena podstawowa jest domyślną nazwą domeny dla nowego użytkownika podczas tworzenia nowego użytkownika. Ustawienie podstawowej nazwy domeny usprawnia proces tworzenia nowych użytkowników w portalu przez administratora. Aby zmienić podstawową nazwę domeny:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy za pomocą konta, które jest administratorem globalnym dla katalogu.
2. Wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **Niestandardowe nazwy domen**.
  
   ![Otwieranie strony zarządzania użytkownikami](./media/domains-manage/add-custom-domain.png)
4. Wybierz nazwę domeny, która ma być domeną podstawową.
5. Wybierz polecenie **Dokonaj podstawowego.** Potwierdź swój wybór po wyświetleniu monitu.
  
   ![Nadawanie nazwy domeny jako głównej](./media/domains-manage/make-primary-domain.png)

Możesz zmienić nazwę domeny podstawowej dla katalogu na dowolną zweryfikowaną domenę niestandardową, która nie jest sfederowana. Zmiana domeny podstawowej katalogu nie zmieni nazwy użytkownika dla istniejących użytkowników.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Dodawanie niestandardowych nazw domen do dzierżawy usługi Azure AD

Można dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny dla federacji za pomocą lokalnej usługi Active Directory, możesz dodać maksymalnie 450 nazw domen w każdym katalogu.

## <a name="add-subdomains-of-a-custom-domain"></a>Dodawanie poddomen domeny niestandardowej

Jeśli chcesz dodać do katalogu nazwę domeny trzeciego poziomu, taką jak "europe.contoso.com", należy najpierw dodać i zweryfikować domenę drugiego poziomu, taką jak contoso.com. Poddomena jest automatycznie weryfikowana przez usługę Azure AD. Aby sprawdzić, czy dodana poddomena jest zweryfikowana, odśwież listę domen w przeglądarce.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co zrobić w przypadku zmiany rejestratora DNS dla niestandardowej nazwy domeny

Jeśli zmienisz rejestratorów DNS, nie ma żadnych dodatkowych zadań konfiguracyjnych w usłudze Azure AD. Możesz nadal używać nazwy domeny z usługą Azure AD bez przerwy. Jeśli używasz niestandardowej nazwy domeny z usługą Office 365, intune lub innymi usługami, które opierają się na niestandardowych nazwach domen w usłudze Azure AD, zapoznaj się z dokumentacją tych usług.

## <a name="delete-a-custom-domain-name"></a>Usuwanie niestandardowej nazwy domeny

Możesz usunąć niestandardową nazwę domeny z usługi Azure AD, jeśli twoja organizacja nie używa już tej nazwy domeny lub jeśli chcesz użyć tej nazwy domeny z inną usługą Azure AD.

Aby usunąć niestandardową nazwę domeny, należy najpierw upewnić się, że żadne zasoby w katalogu nie będą zależeć od nazwy domeny. Nie można usunąć nazwy domeny z katalogu, jeśli:

* Każdy użytkownik ma nazwę użytkownika, adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda grupa ma adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda aplikacja w usłudze Azure AD ma identyfikator URI identyfikatora aplikacji, który zawiera nazwę domeny.

Przed usunięciem niestandardowej nazwy domeny należy zmienić lub usunąć dowolny taki zasób w katalogu usługi Azure AD.

### <a name="forcedelete-option"></a>ForceDelete – opcja

Nazwę domeny można **wymusić** w [Centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) lub przy użyciu [interfejsu API programu Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Te opcje używają operacji asynchronicznego i aktualizują wszystkie odwołaniauser@contoso.comod niestandardowej nazwy domeny,user@contoso.onmicrosoft.comtakiej jak " do początkowej domyślnej nazwy domeny, takiej jak "." 

Aby wywołać **ForceDelete** w witrynie Azure Portal, należy upewnić się, że istnieje mniej niż 1000 odwołań do nazwy domeny, a wszelkie odwołania, w których program Exchange jest usługą inicjowania obsługi administracyjnej, muszą zostać zaktualizowane lub usunięte w [Centrum administracyjnym programu Exchange.](https://outlook.office365.com/ecp/) Obejmuje to grupy zabezpieczeń z włączoną funkcją Poczty programu Exchange i listy rozproszone; Aby uzyskać więcej informacji, zobacz [Usuwanie grup zabezpieczeń z obsługą poczty](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Ponadto **ForceDelete** operacji nie powiedzie się, jeśli którykolwiek z następujących jest prawdą:

* Domena została zakupiona za pośrednictwem usług subskrypcji domeny usługi Office 365
* Jesteś partnerem administrującymi w imieniu innej dzierżawy klienta

Następujące akcje są wykonywane w ramach **ForceDelete** operacji:

* Zmienia nazwę nazwy UPN, EmailAddress i ProxyAddress użytkowników z odwołaniami do niestandardowej nazwy domeny do początkowej domyślnej nazwy domeny.
* Zmienia nazwę adresu e-mail grup z odwołaniami do niestandardowej nazwy domeny na początkową domyślną nazwę domeny.
* Zmienia nazwę identyfikatorów aplikacji z odwołaniami do niestandardowej nazwy domeny na początkową domyślną nazwę domeny.

Błąd jest zwracany, gdy:

* Liczba obiektów, które mają zostać zmienione, jest większa niż 1000
* Jedną z aplikacji, które mają zostać zmienione, jest aplikacja wielodostępna

### <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Dlaczego usunięcie domeny kończy się niepowodzeniem z błędem, który stwierdza, że mam grupy opanowane programu Exchange w tej nazwie domeny?** <br>
**Odp.:** Obecnie niektóre grupy, takie jak grupy zabezpieczeń z obsługą poczty i listy rozproszone, są aprowidzone przez program Exchange i muszą być ręcznie czyszczone w [Centrum administracyjnym programu Exchange (EAC).](https://outlook.office365.com/ecp/) Może istnieć utrzymujące się adresy proxy, które opierają się na niestandardowej nazwie domeny i będą musiały zostać zaktualizowane ręcznie do innej nazwy domeny. 

**P: Jestem zalogowany jako\@administrator contoso.com ale nie mogę usunąć nazwy domeny "contoso.com"?**<br>
**Odp.:** Nie można odwoływać się do niestandardowej nazwy domeny, którą próbujesz usunąć w nazwie konta użytkownika. Upewnij się, że konto administratora globalnego używa początkowej domyślnej admin@contoso.onmicrosoft.comnazwy domeny (onmicrosoft.com), takiej jak . Zaloguj się przy innym koncie admin@contoso.onmicrosoft.com administratora globalnego, takim jak "fabrikam.com", na admin@fabrikam.comktórym znajduje się konto.

**P: Kliknąłem przycisk Usuń `In Progress` domenę i zobacz stan operacji Usuń. Jak długo to trwa? Co się stanie, jeśli się nie powiedzie?**<br>
**Odp.:** Operacja usuń domenę jest asynchronicznego zadania w tle, który zmienia nazwę wszystkich odwołań do nazwy domeny. Powinien zakończyć się w ciągu minuty lub dwóch. Jeśli usunięcie domeny nie powiedzie się, upewnij się, że nie masz:

* Aplikacje skonfigurowane w nazwie domeny za pomocą aplikacjiIdentifierURI
* Dowolna grupa z włączoną obsługą poczty odwołująca się do niestandardowej nazwy domeny
* Ponad 1000 odwołań do nazwy domeny

Jeśli okaże się, że którykolwiek z warunków nie zostały spełnione, ręcznie wyczyść odwołania i spróbuj usunąć domenę ponownie.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Zarządzanie nazwami domen za pomocą programu PowerShell lub interfejsu API programu Microsoft Graph

Większość zadań zarządzania nazwami domen w usłudze Azure Active Directory można również wykonać przy użyciu programu Microsoft PowerShell lub programowo przy użyciu interfejsu API programu Microsoft Graph.

* [Zarządzanie nazwami domen w usłudze Azure AD za pomocą programu PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Typ zasobu domeny](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>Następne kroki

* [Dodawanie niestandardowych nazw domen](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Usuwanie grup zabezpieczeń z włączoną pocztą programu Exchange w Centrum administracyjnym programu Exchange przy niestandardowej nazwie domeny w usłudze Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete niestandardowa nazwa domeny z interfejsem API programu Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
