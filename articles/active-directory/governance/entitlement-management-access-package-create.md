---
title: Tworzenie nowego pakietu dostępu w zarządzaniu uprawnieniami — usługa Azure AD
description: Dowiedz się, jak utworzyć nowy pakiet zasobów, które chcesz udostępnić w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262011"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Tworzenie nowego pakietu dostępu w zarządzaniu uprawnieniami usługi Azure AD

Pakiet dostępu umożliwia jednorazową konfigurację zasobów i zasad, które automatycznie administrują dostępem przez cały okres ważności pakietu dostępu. W tym artykule opisano sposób tworzenia nowego pakietu dostępu.

## <a name="overview"></a>Omówienie

Wszystkie pakiety dostępu muszą być umieszczone w kontenerze o nazwie katalog. Katalog definiuje zasoby, które można dodać do pakietu dostępu. Jeśli nie określisz katalogu, pakiet dostępu zostanie umieszczony w katalogu ogólnym. Obecnie nie można przenieść istniejącego pakietu dostępu do innego katalogu.

Jeśli jesteś menedżerem pakietów dostępu, nie możesz dodawać zasobów, które posiadasz do katalogu. Użytkownik jest ograniczony do korzystania z zasobów dostępnych w katalogu. Jeśli chcesz dodać zasoby do katalogu, możesz poprosić właściciela katalogu.

Wszystkie pakiety dostępu muszą mieć co najmniej jedną zasadę. Zasady określają, kto może żądać pakietu dostępu, a także ustawienia zatwierdzania i cyklu życia. Podczas tworzenia nowego pakietu dostępu można utworzyć początkową zasadę dla użytkowników w katalogu, dla użytkowników, którzy nie są w katalogu, tylko dla przypisania bezpośredniego administratora lub można utworzyć zasady później.

![Tworzenie pakietu dostępu](./media/entitlement-management-access-package-create/access-package-create.png)

Oto kroki wysokiego poziomu, aby utworzyć nowy pakiet dostępu.

1. W zarządzaniu tożsamościami uruchom proces, aby utworzyć nowy pakiet dostępu.

1. Wybierz katalog, w którego chcesz utworzyć pakiet dostępu.

1. Dodaj zasoby z katalogu do pakietu dostępu.

1. Przypisz role zasobów dla każdego zasobu.

1. Określ użytkowników, którzy mogą żądać dostępu.

1. Określ wszystkie ustawienia zatwierdzania.

1. Określ ustawienia cyklu życia.

## <a name="start-new-access-package"></a>Uruchamianie nowego pakietu dostępu

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. Kliknij **pozycję Usługa Azure Active Directory,** a następnie kliknij pozycję Zarządzanie **tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access**.

1. Kliknij **pozycję Nowy pakiet dostępu**.
   
    ![Zarządzanie uprawnieniami w witrynie Azure portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Podstawy

Na karcie **Podstawy** nadać pakietowi dostępu nazwę i określić, w którym katalogu chcesz utworzyć pakiet dostępu.

1. Wprowadź nazwę wyświetlaną i opis pakietu dostępu. Użytkownicy zobaczą te informacje podczas przesyłania żądania pakietu dostępu.

1. Z listy rozwijanej **Katalog** wybierz katalog, w którego chcesz utworzyć pakiet dostępu. Na przykład możesz mieć właściciela katalogu, który zarządza wszystkimi zasobami marketingowymi, które mogą być wymagane. W takim przypadku można wybrać katalog marketingowy.

    Zobaczysz tylko katalogi, w których masz uprawnienia do tworzenia pakietów dostępu. Aby utworzyć pakiet dostępu w istniejącym katalogu, musisz być administratorem globalnym lub administratorem użytkownika lub być właścicielem katalogu lub menedżerem pakietów dostępu w tym katalogu.

    ![Pakiet dostępu - Podstawy](./media/entitlement-management-access-package-create/basics.png)

    Jeśli jesteś administratorem globalnym, administratorem użytkownika lub twórcą katalogu i chcesz utworzyć pakiet dostępu w nowym katalogu, którego nie ma na liście, kliknij pozycję **Utwórz nowy katalog**. Wprowadź nazwę i opis katalogu, a następnie kliknij przycisk **Utwórz**.

    Pakiet dostępu, który tworzysz i wszystkie zasoby zawarte w nim zostaną dodane do nowego katalogu. Później można również dodać dodatkowych właścicieli katalogu.

1. Kliknij przycisk **alej**.

## <a name="resource-roles"></a>Role zasobów

Na **karcie Role zasobów** wybierz zasoby, które mają być uwzględnione w pakiecie dostępu. Użytkownicy, którzy zażądają i odbierają pakiet dostępu, otrzymają wszystkie role zasobów w pakiecie dostępu.

1. Kliknij typ zasobu, który chcesz dodać (**Grupy i zespoły**, **Aplikacje**lub **Witryny programu SharePoint**).

1. W wyświetlonym okienku Wybierz wybierz jeden lub więcej zasobów z listy.

    ![Pakiet dostępu — role zasobów](./media/entitlement-management-access-package-create/resource-roles.png)

    Jeśli tworzysz pakiet dostępu w katalogu ogólne lub nowy katalog, będzie można wybrać dowolny zasób z katalogu, który jesteś właścicielem. Użytkownik musi być co najmniej administratorem globalnym, administratorem użytkownika lub twórcą katalogu.

    Jeśli tworzysz pakiet dostępu w istniejącym katalogu, można wybrać dowolny zasób, który jest już w katalogu bez jego posiadania.

    Jeśli jesteś administratorem globalnym, administratorem użytkownika lub właścicielem katalogu, masz dodatkową opcję wyboru zasobów, które nie są jeszcze w katalogu. Jeśli wybierzesz zasoby, które nie są obecnie w wybranym katalogu, te zasoby zostaną również dodane do katalogu dla innych administratorów katalogu do tworzenia pakietów dostępu. Jeśli chcesz wybrać tylko zasoby, które znajdują się obecnie w wybranym katalogu, zaznacz pole wyboru **Tylko zobacz** u góry okienka Zaznacz.

1. Po wybraniu zasobów na liście **Rola** wybierz rolę, którą użytkownicy mają być przypisani do zasobu.

    ![Pakiet dostępu — wybór roli zasobu](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kliknij przycisk **alej**.

## <a name="requests"></a>Żądania

Na karcie **Żądania** należy utworzyć pierwszą zasadę określającą, kto może zażądać pakietu dostępu, a także ustawienia zatwierdzania. Później można utworzyć więcej zasad żądań, aby umożliwić dodatkowym grupom użytkowników żądanie pakietu dostępu z własnymi ustawieniami zatwierdzania.

![Pakiet dostępu — karta Żądania](./media/entitlement-management-access-package-create/requests.png)

W zależności od tego, kto ma być w stanie zażądać tego pakietu dostępu, wykonaj kroki w jednej z poniższych sekcji.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Recenzja + tworzenie

Na karcie **Recenzja + tworzenie** możesz przejrzeć ustawienia i sprawdzić, czy nie ma błędów sprawdzania poprawności.

1. Przeglądanie ustawień pakietu dostępu

    ![Pakiet dostępu — zasady — włączanie ustawień zasad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknij **przycisk Utwórz,** aby utworzyć pakiet dostępu.

    Nowy pakiet dostępu pojawia się na liście pakietów dostępu.

## <a name="creating-an-access-package-programmatically"></a>Programowanie pakietu dostępu

Można również utworzyć pakiet dostępu za pomocą programu Microsoft Graph.  Użytkownik w odpowiedniej roli z aplikacją, `EntitlementManagement.ReadWrite.All` która ma delegowane uprawnienie, może wywołać interfejs API

1. [Lista accessPackageResources w katalogu](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) i [utworzyć accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) dla wszystkich zasobów, które nie są jeszcze w katalogu.
1. [Lista accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) każdego accessPackageResource w accessPackageCatalog. Ta lista ról będzie następnie używany do wyboru roli, podczas późniejszego tworzenia accessPackageResourceRoleScope.
1. [Utwórz accessPackage](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Utwórz accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Utwórz accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) dla każdej roli zasobu potrzebnej w pakiecie dostępu.

## <a name="next-steps"></a>Następne kroki

- [Udostępnij łącze, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md)
- [Zmienianie ról zasobów dla pakietu dostępu](entitlement-management-access-package-resources.md)
- [Bezpośrednie przypisywanie użytkownika do pakietu dostępu](entitlement-management-access-package-assignments.md)
