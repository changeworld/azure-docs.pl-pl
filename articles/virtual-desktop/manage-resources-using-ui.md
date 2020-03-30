---
title: Wdrażanie narzędzia do zarządzania za pomocą szablonu usługi Azure Resource Manager — Azure
description: Jak zainstalować narzędzie interfejsu użytkownika z szablonem usługi Azure Resource Manager do zarządzania zasobami pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dac17da4aa3d3d73ccbbc101cba4326a17fec9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127770"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Wdrażanie narzędzia do zarządzania za pomocą szablonu usługi Azure Resource Manager

Instrukcje zawarte w tym artykule dowiesz się, jak wdrożyć interfejs użytkownika przy użyciu szablonu usługi Azure Resource Manager.

## <a name="important-considerations"></a>Istotne zagadnienia

Ponieważ aplikacja wymaga zgody na interakcję z pulpitem wirtualnym systemu Windows, to narzędzie nie obsługuje scenariuszy typu Business-to-Business (B2B). Subskrypcja dzierżawy usługi Azure Active Directory (AAD) będzie wymagać własnego oddzielnego wdrożenia narzędzia do zarządzania.

To narzędzie do zarządzania jest próbką. Firma Microsoft zapewni ważne aktualizacje zabezpieczeń i jakości. [Kod źródłowy jest dostępny w usłudze GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Klienci i partnerzy są zachęcani do dostosowywania narzędzia do ich potrzeb biznesowych.

Do następujących przeglądarek są zgodne z narzędziem do zarządzania:
- Google Chrome 68 lub nowsze
- Microsoft Edge 40.15063 lub nowsze
- Mozilla Firefox 52.0 lub nowsza
- Safari 10 lub nowsze (tylko macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co jest potrzebne do wdrożenia narzędzia do zarządzania

Przed wdrożeniem narzędzia do zarządzania, trzeba użytkownika usługi Azure Active Directory (Azure AD) do utworzenia rejestracji aplikacji i wdrożenia interfejsu użytkownika zarządzania. Ten użytkownik musi:

- Wyłączanie uwierzytelniania wieloskładnikowego usługi Azure
- Mieć uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Mieć uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma wymagane uprawnienia, postępując zgodnie z instrukcjami w [Required permissions](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Po wdrożeniu i skonfigurowaniu narzędzia do zarządzania zaleca się poprosić użytkownika o uruchomienie interfejsu użytkownika zarządzania, aby upewnić się, że wszystko działa. Użytkownik uruchamiany interfejsu użytkownika zarządzania musi mieć przypisanie roli, które umożliwia mu wyświetlanie lub edytowanie dzierżawy pulpitu wirtualnego systemu Windows.

## <a name="deploy-the-management-tool"></a>Wdrażanie narzędzia do zarządzania

Przed rozpoczęciem upewnij się, że serwer i aplikacje klienckie mają zgodę, odwiedzając [stronę zgody pulpitu wirtualnego systemu Windows](https://rdweb.wvd.microsoft.com) dla reprezentowanego usługi Azure Active Directory (AAD).

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć szablon zarządzania zasobami platformy Azure:

1. Przejdź do [strony Szablony usług pulpitu zdalnego platformy GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Wdrażanie szablonu na platformie Azure.
    - Jeśli wdrażasz w ramach subskrypcji enterprise, przewiń w dół i wybierz **pozycję Wdrażanie na platformie Azure**. 
    - Jeśli wdrażasz w subskrypcji dostawcy rozwiązań w chmurze, postępuj zgodnie z tymi instrukcjami, aby wdrożyć na platformie Azure:
        1. Przewiń w dół i kliknij prawym przyciskiem myszy **pozycję Wdrażanie na platformie Azure,** a następnie wybierz pozycję **Kopiuj lokalizację łącza**.
        2. Otwórz edytor tekstu, taki jak Notatnik, i wklej tam link.
        3. Zaraz <https://portal.azure.com/> po i przed hasztagiem (#) wprowadź znak (@), a następnie nazwę domeny dzierżawy. Oto przykład formatu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Zaloguj się do witryny Azure portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
        5. Wklej skopiowane łącze do edytora tekstu na pasku adresu.
3. Podczas wprowadzania parametrów wykonaj następujące czynności:
    - Dla parametru **isServicePrincipal** **wybierz**false .
    - W przypadku poświadczeń wprowadź poświadczenia usługi Azure AD z wyłączonym uwierzytelnianiem wieloskładnikowym. Te poświadczenia będą używane do tworzenia aplikacji usługi Azure AD i zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz [co jest potrzebne do wdrożenia narzędzia do zarządzania](#what-you-need-to-deploy-the-management-tool).
    - W przypadku **aplikacjiName**użyj unikatowej nazwy aplikacji, która zostanie zarejestrowana w usłudze Azure Active Directory. Ta nazwa będzie również używana dla adresu URL aplikacji internetowej. Na przykład można użyć nazwy takiej jak "Apr3UX".
4. Po podaniu parametrów zaakceptuj warunki i wybierz **opcję Kup**.

## <a name="provide-consent-for-the-management-tool"></a>Zgoda na narzędzie zarządzania

Po zakończeniu szablonu Usługi Azure Resource Manager usługi GitHub znajdziesz grupę zasobów zawierającą dwie usługi aplikacji wraz z jednym planem usługi aplikacji w witrynie Azure portal.

Przed zalogowaniem się i korzystanie z narzędzia do zarządzania, należy udzielić zgody na nową aplikację usługi Azure AD skojarzone z narzędziem do zarządzania. Udzielanie zgody umożliwia narzędziu do zarządzania pulpitem wirtualnym systemu Windows wywołania w imieniu użytkownika aktualnie zalogowanego do narzędzia.

![Zrzut ekranu przedstawiający uprawnienia udostępniane podczas wyrażania zgody na narzędzie do zarządzania interfejsem użytkownika.](media/management-ui-delegated-permissions.png)

Aby ustalić, którego użytkownika można użyć do zalogowania się do narzędzia, przejdź do [strony ustawień użytkownika usługi Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) i zanotuj wartość, na jaką **użytkownicy mogą wyrazić zgodę**na dostęp do danych firmy w ich imieniu.

![Zrzut ekranu pokazujący, czy użytkownicy mogą udzielić zgody na aplikacje tylko dla swojego użytkownika.](media/management-ui-user-consent-allowed.png)

- Jeśli wartość jest ustawiona na **Tak,** możesz zalogować się przy dowolnym koncie użytkownika w usłudze Azure Active Directory i udzielić zgody tylko dla tego użytkownika. Jeśli jednak zalogujesz się do narzędzia do zarządzania z innym użytkownikiem później, musisz ponownie wykonać tę samą zgodę.
- Jeśli wartość jest ustawiona na **Nie,** musisz zalogować się jako administrator globalny w usłudze Azure Active Directory i udzielić zgody administratora wszystkim użytkownikom w katalogu. Żaden inny użytkownik nie będzie musiał zostać wyświetlony monit o zgodę.


Gdy zdecydujesz, którego użytkownika użyjesz do wyrażenia zgody, postępuj zgodnie z poniższymi instrukcjami, aby wyrazić zgodę na to narzędzie:

1. Przejdź do zasobów platformy Azure, wybierz zasób usługi Azure App Services o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do skojarzonego z nim adresu URL; na przykład <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Zaloguj się przy użyciu odpowiedniego konta użytkownika usługi Azure Active Directory.
3. Jeśli uwierzytelniony u administratora globalnego, można teraz zaznaczyć pole wyboru **Zgoda w imieniu organizacji**. Wybierz **pozycję Zaakceptuj,** aby wyrazić zgodę.
   
   ![Zrzut ekranu przedstawiający stronę pełnej zgody, którą zobaczy użytkownik lub administrator.](media/management-ui-consent-page.png)

Spowoduje to teraz, że przejdziesz do narzędzia do zarządzania.

## <a name="use-the-management-tool"></a>Korzystanie z narzędzia do zarządzania

Po udzieleniu zgody dla organizacji lub dla określonego użytkownika, można uzyskać dostęp do narzędzia do zarządzania w dowolnym momencie.

Aby uruchomić narzędzie, wykonaj następujące instrukcje:

1. Wybierz zasób usługi Azure App Services o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do skojarzonego z nim adresu URL; na przykład <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Zaloguj się przy użyciu poświadczeń pulpitu wirtualnego systemu Windows.
3. Po wyświetleniu monitu o wybranie grupy dzierżawy wybierz z listy rozwijanej pozycję **Domyślna grupa dzierżawców.**
4. Po wybraniu **domyślnej grupy dzierżawy**po lewej stronie okna powinno pojawić się menu. W tym menu znajdź nazwę grupy dzierżawy i wybierz ją.
  
  > [!NOTE]
  > Jeśli masz niestandardową grupę dzierżawy, wprowadź ją ręcznie, zamiast wybierać ją z listy rozwijanej.

## <a name="report-issues"></a>Zgłaszanie problemów

Jeśli natkniesz się na jakiekolwiek problemy z narzędziem do zarządzania lub innymi narzędziami pulpitu wirtualnego systemu Windows, postępuj zgodnie ze [wskazówkami w szablonach usługi Azure Resource Manager dla usług pulpitu zdalnego,](https://github.com/Azure/RDS-Templates/blob/master/README.md) aby zgłosić je w usłudze GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak wdrożyć i połączyć się z narzędziem do zarządzania, możesz dowiedzieć się, jak korzystać z pomocy usługi Azure do monitorowania problemów z usługami i poradatoriów kondycji. Aby dowiedzieć się więcej, zobacz nasz [samouczek Konfigurowanie alertów usługi](./set-up-service-alerts.md).