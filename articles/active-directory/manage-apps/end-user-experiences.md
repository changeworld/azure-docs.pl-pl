---
title: Środowisko użytkownika końcowego dla aplikacji — usługa Azure Active Directory
description: Usługa Azure Active Directory (Azure AD) udostępnia kilka konfigurowalnych sposobów wdrażania aplikacji dla użytkowników końcowych w organizacji.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266626"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Środowisko użytkownika końcowego dla aplikacji w usłudze Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia kilka konfigurowalnych sposobów wdrażania aplikacji dla użytkowników końcowych w organizacji:

* Panel dostępu usługi Azure AD
* Uruchamianie aplikacji usługi Office 365
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

To, którą metodę wybierzesz do wdrożenia w organizacji, jest twoim dyskrecją.

## <a name="azure-ad-access-panel"></a>Panel dostępu usługi Azure AD

Panel dostępu https://myapps.microsoft.com jest portalem internetowym, który umożliwia użytkownikowi końcowemu z kontem organizacyjnym w usłudze Azure Active Directory wyświetlanie i uruchamianie aplikacji, do których administrator usługi Azure AD uzyskał dostęp. Jeśli jesteś użytkownikiem końcowym za pomocą [usługi Azure Active Directory Premium,](https://azure.microsoft.com/pricing/details/active-directory/)możesz również korzystać z możliwości zarządzania grupami samoobsługowymi za pośrednictwem Panelu dostępu.

![Zrzut ekranu przedstawiający portal Panelu dostępu usługi Azure AD](media/what-is-single-sign-on/azure-ad-access-panel.png)

Domyślnie wszystkie aplikacje są wyświetlane razem na jednej stronie. Ale można użyć kolekcji do grupowania powiązanych aplikacji i prezentować je na osobnej karcie, dzięki czemu łatwiej je znaleźć. Na przykład można użyć kolekcji do tworzenia logicznych grupowania aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. Aby uzyskać więcej informacji, zobacz [Jak dostosować panele dostępu użytkowników za pomocą kolekcji Moje aplikacje.](access-panel-collections.md) 

Panel dostępu jest oddzielony od witryny Azure portal i nie wymaga od użytkowników subskrypcji platformy Azure lub subskrypcji usługi Office 365.

Aby uzyskać więcej informacji na temat panelu dostępu usługi Azure AD, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Uruchamianie aplikacji usługi Office 365

W przypadku organizacji, które wdrożyły usługę Office 365, aplikacje przypisane użytkownikom za pośrednictwem [https://portal.office.com/myapps](https://portal.office.com/myapps)usługi Azure AD będą również wyświetlane w portalu usługi Office 365 pod adresem . Ułatwia to użytkownikom w organizacji uruchamianie aplikacji bez konieczności korzystania z drugiego portalu i jest zalecanym rozwiązaniem do uruchamiania aplikacji dla organizacji korzystających z usługi Office 365.

![Zrzut ekranu przedstawiający portal usługi Office 365](./media/end-user-experiences/microsoft-365-portal-office-com.png)

Aby uzyskać więcej informacji na temat uruchamiania aplikacji usługi Office 365, zobacz [Wyświetlanie aplikacji w witrynie Uruchamianie aplikacji usługi Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Bezpośrednie logowanie do aplikacji federacyjnych

Większość aplikacji federacyjnych, które obsługują SAML 2.0, Federacja WS lub OpenID połączyć również obsługiwać możliwość dla użytkowników, aby rozpocząć w aplikacji, a następnie zalogować się za pośrednictwem usługi Azure AD albo przez automatyczne przekierowanie lub klikając łącze, aby zalogować się. Jest to znane jako dostawcy usług zainicjowane logowania i większość aplikacji federacyjnych w galerii aplikacji usługi Azure AD obsługuje to (zobacz dokumentację połączoną z kreatora konfiguracji logowania jednokrotnego aplikacji w witrynie Azure portal, aby uzyskać szczegółowe informacje).

![Przykład strony logowania do aplikacji mobilnej](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>Bezpośrednie łącza logowania

Usługa Azure AD obsługuje również bezpośrednie łącza logowania jednokrotnego do poszczególnych aplikacji, które obsługują logowanie jednoobrałowe oparte na hasłach, połączone logowanie jednokrotne i dowolną formę rejestracji sfederowanej logowania jednokrotnego.

Te łącza są specjalnie spreparowane adresy URL, które wysyłają użytkownika za pośrednictwem procesu logowania usługi Azure AD dla określonej aplikacji bez konieczności uruchamiania ich przez użytkownika z panelu dostępu usługi Azure AD lub usługi Office 365. Te **adresy URL dostępu użytkowników** można znaleźć pod właściwościami dostępnych aplikacji dla przedsiębiorstw. W witrynie Azure portal wybierz pozycję **Aplikacje usługi Azure Active Directory** > **Enterprise**. Wybierz aplikację, a następnie wybierz pozycję **Właściwości**.

![Przykład adresu URL dostępu użytkownika we właściwościach Twittera](media/end-user-experiences/direct-sign-on-link.png)

Te łącza można kopiować i wklejać w dowolnym miejscu, w dowolnym miejscu, w dowolnym miejscu, aby podać łącze logowania do wybranej aplikacji. Może to być w wiadomości e-mail lub w dowolnym niestandardowym portalu sieci web, który został skonfigurowany do dostępu do aplikacji użytkownika. Oto przykład bezpośredniego adresu URL logowania jednokrotnego usługi Azure AD dla Twittera:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobnie jak w przypadku adresów URL specyficznych dla organizacji w panelu dostępu, można dodatkowo dostosować ten adres URL, dodając jedną z aktywnych lub zweryfikowanych domen dla katalogu po myapps.microsoft.com domenie. Dzięki temu wszelkie znaki firmowe są ładowane natychmiast na stronie logowania bez konieczności wprowadzania identyfikatora użytkownika:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Gdy autoryzowany użytkownik kliknie jedno z tych łączy specyficznych dla aplikacji, najpierw zobaczy swoją stronę logowania organizacyjnego (przy założeniu, że nie jest jeszcze zalogowany), a po zalogowaniu się zostanie przekierowany do aplikacji bez uprzedniego zatrzymania się w panelu dostępu. Jeśli użytkownik nie ma wymagań wstępnych, aby uzyskać dostęp do aplikacji, takich jak rozszerzenie przeglądarki logowania jednokrotnego oparte na hasłach, łącze wyświetli monit o zainstalowanie brakującego rozszerzenia. Adres URL łącza również pozostaje stała, jeśli zmieni się konfiguracja logowania jednokrotnego dla aplikacji.

Te łącza używają tych samych mechanizmów kontroli dostępu co panel dostępu i usługi Office 365 i tylko ci użytkownicy lub grupy, którzy zostali przypisani do aplikacji w witrynie Azure portal, będą mogli pomyślnie uwierzytelnić się. Jednak każdy użytkownik, który jest nieautoryzowany, zobaczy komunikat wyjaśniający, że nie przyznano im dostępu i otrzyma łącze do załadowania panelu dostępu, aby wyświetlić dostępne aplikacje, do których ma dostęp.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z planami wdrażania, zobacz [Plany wdrażania usługi Azure Active Directory](../fundamentals/active-directory-deployment-plans.md)
