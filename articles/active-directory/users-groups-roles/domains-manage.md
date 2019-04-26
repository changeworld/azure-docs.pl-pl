---
title: Dodać i zweryfikować niestandardowe nazwy domen — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Zarządzanie pojęcia i kwestie dotyczące zarządzania nazwę domeny w usłudze Azure Active Directory
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
ms.openlocfilehash: 908ae768ae471ab6f49452c99323c31d34772d45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472335"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Zarządzanie niestandardowymi nazwami domen w usłudze Azure Active Directory

Nazwa domeny jest ważną częścią identyfikatora dla wielu zasobów katalogu: jest częścią użytkownika nazwy lub adresu e-mail użytkownika, część adresu w grupie i czasami część aplikacji, identyfikator URI dla aplikacji. Zasób w usłudze Azure Active Directory (Azure AD) mogą obejmować nazwę domeny, który jest własnością katalogu, który zawiera zasób. Tylko administratorzy globalni mogą zarządzać domen w usłudze Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Ustaw nazwę domeny głównej dla katalogu usługi Azure AD

Po utworzeniu katalogu początkowa nazwa domeny, takich jak "contoso.onmicrosoft.com", jest również nazwa domeny głównej. Domena podstawowa jest domyślna nazwa domeny dla nowego użytkownika, po utworzeniu nowego użytkownika. Ustawianie nazwy domeny głównej usprawnia proces dla administratora do tworzenia nowych użytkowników w portalu. Aby zmienić nazwę głównej domeny:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.
2. Wybierz pozycję **Azure Active Directory**.
3. Wybierz pozycję **Niestandardowe nazwy domen**.
  
   ![Otwieranie strony zarządzania użytkownikami](./media/domains-manage/add-custom-domain.png)
4. Wybierz nazwę domeny, która ma być podstawową domeną.
5. Wybierz **Oznacz jako podstawową** polecenia. Potwierdź wybór po wyświetleniu monitu.
  
   ![Wprowadź domenę nazwę podstawową](./media/domains-manage/make-primary-domain.png)

Można zmienić podstawowej nazwy domeny dla katalogu jako zweryfikowanej domenie niestandardowej, która nie jest federacyjna. Domena podstawowa dla katalogu nie będzie zmiana nazwy użytkownika dla wszystkich istniejących użytkowników.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Dodawanie niestandardowych nazw domen do dzierżawy usługi Azure AD

Możesz dodać maksymalnie 900 nazw domen zarządzanych. Jeśli konfigurujesz wszystkie domeny dla federacji z usługą Active Directory w środowisku lokalnym, możesz dodać do 450 nazwy domeny w każdym katalogu.

## <a name="add-subdomains-of-a-custom-domain"></a>Dodać poddomeny domeny niestandardowej

Jeśli chcesz dodać nazwy domen trzeciego poziomu, takie jak "europe.contoso.com" do katalogu, należy najpierw dodać i weryfikowania domeny drugiego poziomu, np. contoso.com. Poddomeny jest automatycznie weryfikowana przez usługę Azure AD. Aby sprawdzić, czy poddomeny, którą dodałeś został zweryfikowany, Odśwież listę domen w przeglądarce.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Co należy zrobić, jeśli zmienisz rejestratora DNS dla swojej niestandardowej nazwy domeny

Jeśli zmienisz rejestratorów DNS, nie ma żadnych zadań dodatkowej konfiguracji w usłudze Azure AD. Aby kontynuować, przy użyciu nazwy domeny w usłudze Azure AD z produktu bez przeszkód. Jeśli używasz niestandardowej nazwy domeny za pomocą usługi Office 365, Intune lub innych usług, które zależą od nazwy domeny niestandardowej w usłudze Azure AD, zobacz dokumentację dla tych usług.

## <a name="delete-a-custom-domain-name"></a>Usuwanie nazwy domeny niestandardowej

Jeśli Twoja organizacja już używa tej nazwy domeny, czy należy użyć tej nazwy domeny za pomocą innej usługi Azure AD, możesz usunąć nazwy domeny niestandardowej z usługi Azure AD.

Aby usunąć niestandardową nazwę domeny, należy się najpierw upewnić, że żadne zasoby w Twoim katalogu zależeć od nazwy domeny. Nie można usunąć nazwy domeny z katalogu, jeśli:

* Każdy użytkownik ma nazwę użytkownika, adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda grupa ma adres e-mail lub adres serwera proxy, który zawiera nazwę domeny.
* Każda aplikacja w usłudze Azure AD ma identyfikator URI, który zawiera nazwę domeny aplikacji.

Należy zmienić ani usunąć tych zasobów w katalogu usługi Azure AD, aby można było usunąć nazwy domeny niestandardowej.

### <a name="forcedelete-option"></a>Opcja ForceDelete

Możesz **ForceDelete** nazwę domeny w [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) lub za pomocą [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Te opcje, użyj operacji asynchronicznej i zaktualizuj wszystkie odwołania, na podstawie nazwy domeny niestandardowej, takich jak "user@contoso.com"Aby nazwa wstępnej domyślnej domeny takie jak"user@contoso.onmicrosoft.com." 

Aby wywołać **ForceDelete** w witrynie Azure portal, upewnij się, że istnieją mniej niż 1000 odwołania do nazwy domeny, a wszelkie odwołania, w przypadku inicjowania obsługi administracyjnej usługi Exchange musi zostać zaktualizowane lub usunięte w [ Centrum administracyjne Exchange](https://outlook.office365.com/ecp/). Obejmuje to Exchange Mail-Enabled grup zabezpieczeń i listy rozproszonych; Aby uzyskać więcej informacji, zobacz [usuwanie grup zabezpieczeń z włączoną obsługą poczty](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Ponadto **ForceDelete** operacja nie powiodła się, jeśli jest spełniony jeden z następujących czynności:

* Zakupić domenę za pośrednictwem usługi domenowe subskrypcji usługi Office 365
* Jesteś partnerem, administrowanie w imieniu innego klienta dzierżawy

Następujące akcje są wykonywane w ramach **ForceDelete** operacji:

* Zmienia nazwę UPN, EmailAddress i ProxyAddress użytkowników za pomocą odwołania do nazwy domeny niestandardowej na początkowej domyślnej nazwy domeny.
* Zmienia nazwę EmailAddress grup zawierających odwołania do nazwy domeny niestandardowej na początkowej domyślnej nazwy domeny.
* Zmienia nazwę identifierUris aplikacji za pomocą odwołania do nazwy domeny niestandardowej na początkowej domyślnej nazwy domeny.

Zwracany jest błąd, gdy:

* Liczba obiektów, których nazwa zostanie zmieniona, jest większa niż 1000
* Jedna z aplikacji, których nazwa zostanie zmieniona jest aplikacji z wieloma dzierżawami

### <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pyt.: Dlaczego jest usunięcie domeny kończy się niepowodzeniem z powodu błędu informujący o tym, mają grupy programu Exchange zarządzanych na tę nazwę domeny?** <br>
**Odp.:** Obecnie pewnych grup, takich jak grupy zabezpieczeń z włączoną obsługą poczty i rozproszonych listy aprowizowanych przez program Exchange i trzeba ręcznie wyczyszczone w [Centrum administracyjnego programu Exchange (EAC)](https://outlook.office365.com/ecp/). Może być lingering ProxyAddresses, które zależą od niestandardowej nazwy domeny, a musi być ręcznie aktualizowane na inną nazwę domeny. 

**Pyt.: Jestem zalogować się jako administrator\@contoso.com, ale I nie można usunąć nazwy domeny "contoso.com"?**<br>
**Odp.:** Nie można odwoływać się nazwy domeny niestandardowej, którą chcesz usunąć nazwę konta użytkownika. Upewnij się, że konto administratora globalnego przy użyciu początkowej domyślnej nazwy domeny (. onmicrosoft.com) takich jak admin@contoso.onmicrosoft.com. Zaloguj się przy użyciu innego administratora globalnego konta, takie jak admin@contoso.onmicrosoft.com lub inną nazwę domeny niestandardowej, takich jak "fabrikam.com" gdy konto jest admin@fabrikam.com.

**Pyt.: Po kliknięciu przycisku domeny Usuń i zobacz `In Progress` stanu operacji usuwania. Jak długo trwa? Co się stanie, jeśli zakończy się niepowodzeniem?**<br>
**Odp.:** Operacja usuwania domeny jest zadanie w tle asynchroniczną, która zmienia nazwę wszystkich odwołań do nazwy domeny. Powinno zająć minutę lub dwie. Jeśli usunięcie domeny zakończy się niepowodzeniem, upewnij się, że nie masz:

* Aplikacje skonfigurowane na nazwy domeny w usłudze appIdentifierURI
* Grupy obsługujące pocztę odwołuje się do nazwy domeny niestandardowej
* Więcej niż 1000 odwołania do nazwy domeny

Jeśli okaże się, czy którykolwiek z warunków nie zostały spełnione, należy ręcznie wyczyścić odwołania i ponownie spróbuj usunąć domenę.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Użyj programu PowerShell lub interfejsu API programu Graph, zarządzanie nazwami domen

Większości zadań administracyjnych dla nazw domen w usłudze Azure Active Directory można również przeprowadzić za pomocą programu Microsoft PowerShell albo programowo z użyciem interfejsu API usługi Azure AD Graph.

* [Zarządzanie nazwami domen w usłudze Azure AD przy użyciu programu PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Zarządzanie nazwami domen w usłudze Azure AD przy użyciu interfejsu API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Kolejne kroki

* [Dodawanie niestandardowych nazw domen](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Usuwanie grupy zabezpieczeń z włączoną obsługą poczty programu Exchange w Centrum administracyjnym programu Exchange na niestandardowej nazwy domeny w usłudze Azure AD](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [ForceDelete niestandardowej nazwy domeny za pomocą interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
