---
title: Wdrażanie narzędzia do zarządzania — Azure
description: Jak zainstalować narzędzie interfejsu użytkownika w celu zarządzania zasobami programu Windows Virtual Desktop w wersji zapoznawczej.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: e0f9dbd9bf6b0c12d3e3f028ab9cd4c80cdb5124
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816502"
---
# <a name="tutorial-deploy-a-management-tool"></a>Samouczek: Wdrażanie narzędzia do zarządzania

Narzędzie do zarządzania udostępnia interfejs użytkownika służący do zarządzania zasobami programu Microsoft Virtual Desktop w wersji zapoznawczej. W tym samouczku dowiesz się, jak wdrożyć narzędzie do zarządzania i nawiązać z nim połączenie.

>[!NOTE]
>Te instrukcje dotyczą konfiguracji programu Windows Virtual Desktop w wersji zapoznawczej, która może być używana z istniejącymi procesami w organizacji.

## <a name="important-considerations"></a>Ważne zagadnienia

Ponieważ aplikacja wymaga zgody na współdziałanie z pulpitem wirtualnym systemu Windows, to narzędzie nie obsługuje scenariuszy biznes-to-Business (B2B). Każda subskrypcja dzierżawy usługi Azure Active Directory (AAD) będzie potrzebować oddzielnego wdrożenia narzędzia do zarządzania.

To narzędzie do zarządzania jest przykładem. Firma Microsoft zapewni ważne aktualizacje zabezpieczeń i ich jakości. [Kod źródłowy jest dostępny w](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)serwisie GitHub. Zachęcamy klientów i partnerów do dostosowania narzędzia do potrzeb firmy.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Co jest potrzebne do uruchomienia szablonu Azure Resource Manager

Przed wdrożeniem szablonu Azure Resource Manager musisz Azure Active Directory użytkownika, aby wdrożyć interfejs użytkownika zarządzania. Ten użytkownik musi:

- Wyłączanie uwierzytelniania wieloskładnikowego (MFA) w systemie Azure
- Masz uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Masz uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma [wymagane uprawnienia](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

Po wdrożeniu szablonu Azure Resource Manager należy uruchomić interfejs użytkownika zarządzania, aby sprawdzić poprawność. Ten użytkownik musi:
- Przypisywanie roli do wyświetlania lub edytowania dzierżawy pulpitu wirtualnego systemu Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Uruchom szablon Azure Resource Manager, aby zainicjować obsługę interfejsu użytkownika zarządzania

Przed rozpoczęciem upewnij się, że aplikacje serwera i klienta mają zgodę, odwiedzając [stronę zgody na pulpit wirtualny systemu Windows](https://rdweb.wvd.microsoft.com) dla reprezentowanego Azure Active Directory (AAD).

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć szablon usługi Azure Resource Management:

1. Przejdź do [strony usługi GitHub Azure RDS-templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Wdróż szablon na platformie Azure.
    - Jeśli wdrażasz w ramach subskrypcji przedsiębiorstwa, przewiń w dół i wybierz pozycję **Wdróż na platformie Azure**. Zobacz [wskazówki dotyczące parametrów szablonu](#guidance-for-template-parameters).
    - Jeśli wdrażasz w ramach subskrypcji dostawcy rozwiązań w chmurze, postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć platformę Azure:
        1. Przewiń w dół i kliknij prawym przyciskiem myszy pozycję **Wdróż na platformie Azure**, a następnie wybierz pozycję **Kopiuj lokalizację linku**.
        2. Otwórz Edytor tekstu, taki jak Notatnik, i Wklej tam link.
        3. Po <https://portal.azure.com/> prawej stronie i przed wystąpieniem programu (#) wprowadź znak (@), a po nim nazwę domeny dzierżawy. Oto przykład formatu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Zaloguj się do Azure Portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
        5. Wklej skopiowany link do edytora tekstu na pasku adresu.

### <a name="guidance-for-template-parameters"></a>Wskazówki dotyczące parametrów szablonu
Oto jak wprowadzić parametry konfigurowania narzędzia:

- Jest to adres URL brokera usług pulpitu zdalnego\/: https:/rdbroker.WVD.Microsoft.com/
- Jest to adres URL zasobu: https:\//Mrs-prod.AME.GBL/Mrs-RDInfra-prod
- Zalogować się do platformy Azure przy użyciu poświadczeń usługi AAD z wyłączonym uwierzytelnianiem wieloskładnikowym. Zobacz [, co jest potrzebne do uruchomienia szablonu Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Użyj unikatowej nazwy dla aplikacji, która zostanie zarejestrowana w Azure Active Directory dla narzędzia do zarządzania; na przykład Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Wyrażanie zgody na narzędzie do zarządzania

Po zakończeniu szablonu Azure Resource Manager GitHub znajdziesz grupę zasobów zawierającą dwie usługi aplikacji wraz z jednym planem usługi App Service w Azure Portal.

Przed zalogowaniem się i użyciem narzędzia do zarządzania należy wyrazić zgodę na nową aplikację Azure Active Directory, która jest skojarzona z narzędziem do zarządzania. Zapewniając zgodę, zezwalasz narzędziom do zarządzania na wykonywanie wywołań zarządzania pulpitami wirtualnymi systemu Windows w imieniu użytkownika, który jest zalogowany do narzędzia.

![Zrzut ekranu przedstawiający uprawnienia podane podczas wyrażania zgody na narzędzie do zarządzania interfejsami użytkownika.](media/management-ui-delegated-permissions.png)

Aby określić, którego użytkownika możesz użyć do zalogowania się do narzędzia, przejdź do [strony Ustawienia użytkownika Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) i zanotuj wartość dla **użytkowników, którzy będą mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu**.

![Zrzut ekranu przedstawiający, czy użytkownicy mogą udzielać zgody na aplikacje dla samego użytkownika.](media/management-ui-user-consent-allowed.png)

- Jeśli wartość jest równa **tak**, możesz zalogować się przy użyciu dowolnego konta użytkownika w Azure Active Directory i zapewnić zgodę tylko dla tego użytkownika. Jednak w przypadku zalogowania się do narzędzia do zarządzania z innym użytkownikiem później należy ponownie wykonać tę samą zgodę.
- Jeśli wartość jest równa **no**, należy zalogować się jako Administrator globalny w Azure Active Directory i zapewnić zgodę administratora dla wszystkich użytkowników w katalogu. Żaden inny użytkownik nie będzie monitowany na monit o wyrażenie zgody.


Po podjęciu decyzji o tym, którego użytkownika będziesz używać, aby wyrazić zgodę, postępuj zgodnie z tymi instrukcjami, aby wyrazić zgodę na narzędzie:

1. Przejdź do zasobów platformy Azure, wybierz zasób App Services platformy Azure o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do adresu URL skojarzonego z nim. na przykład <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Zaloguj się przy użyciu odpowiedniego konta użytkownika Azure Active Directory.
3. W przypadku uwierzytelnienia za pomocą administratora globalnego możesz teraz zaznaczyć pole wyboru, aby **wyrazić zgodę w imieniu organizacji**. Wybierz pozycję **Akceptuj** , aby wyrazić zgodę.
   
   ![Zrzut ekranu przedstawiający stronę pełna zgoda, którą zobaczy użytkownik lub administrator.](media/management-ui-consent-page.png)

Spowoduje to przejście do narzędzia do zarządzania.

## <a name="use-the-management-tool"></a>Korzystanie z narzędzia do zarządzania

Po udzieleniu zgody na organizację lub dla określonego użytkownika można w dowolnym momencie uzyskać dostęp do narzędzia do zarządzania.

Postępuj zgodnie z poniższymi instrukcjami, aby uruchomić narzędzie:

1. Wybierz zasób App Services platformy Azure o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do adresu URL skojarzonego z nim. na przykład <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Zaloguj się przy użyciu poświadczeń pulpitu wirtualnego systemu Windows.
3. Po wyświetleniu monitu o wybranie grupy dzierżawców wybierz z listy rozwijanej pozycję **Domyślna grupa dzierżawców** .

> [!NOTE]
> Jeśli masz niestandardową grupę dzierżawców, wprowadź nazwę ręcznie zamiast wybierać ją z listy rozwijanej.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz już, jak wdrożyć narzędzie do zarządzania i połączyć się z nim, możesz dowiedzieć się, jak używać Azure Service Health do monitorowania problemów z usługami i klasyfikatorów kondycji.

> [!div class="nextstepaction"]
> [Samouczek konfigurowania alertów usługi](./set-up-service-alerts.md)
