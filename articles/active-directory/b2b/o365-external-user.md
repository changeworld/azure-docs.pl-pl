---
title: Udostępnianie zewnętrzne w usłudze Office 365 i współpracy B2B — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: W tym artykule omówiono, udostępnianie zasobów partnerom zewnętrznym przy użyciu usługi Office 365 i Azure Active Directory B2B współpracę zespołową.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 388d42cd41d34a8aebed41dafc48e42006a78457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60355216"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Udostępnianie zewnętrzne w usłudze Office 365 i współpracy B2B usługi Azure Active Directory

Zewnętrzne udostępnianie w usłudze Office 365 (OneDrive, SharePoint Online, grup ujednoliconych, itp.) i współpracy B2B usługi Azure Active Directory (Azure AD) są pod względem technicznym tak samo. Wszystkie zewnętrzne udostępnianie (z wyjątkiem usługi OneDrive/SharePoint Online), łącznie z gośćmi grup usługi Office 365, używa już zaproszenie współpracy B2B usługi Azure AD interfejsów API na potrzeby udostępniania.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Czym różni się B2B usługi Azure AD z udostępnianiem zewnętrznym w usłudze SharePoint Online?

Usługi OneDrive/SharePoint Online ma menedżera oddzielne zaproszenia. Obsługa udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online uruchomione przed usługi Azure AD opracowany jego obsługę. Wraz z upływem czasu udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online jest naliczana kilka funkcji, a wiele miliony użytkowników, którzy korzystają z produktu przez wbudowane udostępnianie wzorca. Istnieją jednak pewne drobne różnice między działaniem udostępnianie zewnętrzne w usłudze OneDrive/SharePoint Online i jak działa współpraca B2B w usłudze Azure AD. Dowiedz się więcej o usłudze OneDrive/SharePoint Online zewnętrznemu udostępnianiu w [zewnętrznych, omówienie udostępniania](https://docs.microsoft.com/sharepoint/external-sharing-overview). Ten proces zwykle różni się od usługi Azure AD B2B w następujący sposób:

- Usługi OneDrive/SharePoint Online doda użytkowników do katalogu po użytkownicy mają zrealizować zaproszenia. Dlatego przed realizacji, widzisz użytkownika w portalu usługi Azure AD. Jeśli inna witryna zaproszenia użytkownika, w tym samym czasie, zostanie wygenerowany nowego zaproszenia. Gdy używasz współpracy B2B usługi Azure AD, użytkownicy zostaną jednak dodane od razu na zaproszenie, dzięki czemu są one wyświetlane wszędzie.

- Środowisko realizacji w usłudze OneDrive/SharePoint Online wygląda inaczej niż środowisko współpracy B2B usługi Azure AD. Po użytkownik umarza zaproszenia, wyglądały doświadczeń.

- Użytkowników we współpracy zaproszenie w usłudze Azure AD B2B mogą zostać pobrane z usługi OneDrive/SharePoint Online i udostępnianie okien dialogowych. Usługi OneDrive/SharePoint Online zaproszonych użytkowników również wyświetlane w usłudze Azure AD po ich realizacji zaproszenia.

- Wymagania licencyjne różnią się. Dla każdego płatną licencję usługi Azure AD, można pozwolić, aby maksymalnie 5 użytkowników-gości, dostęp do Twojego płatnych funkcji usługi Azure AD. Aby dowiedzieć się więcej na temat licencjonowania, zobacz [licencjonowania usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) i ["Co to jest użytkownikiem zewnętrznym?" w usłudze SharePoint Online zewnętrznego, omówienie udostępniania](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-is-an-external-user).

Aby Zarządzanie udostępnianiem zewnętrznym w usłudze OneDrive/SharePoint Online przy użyciu funkcji współpracy B2B usługi Azure AD, należy ustawić usługi OneDrive/SharePoint Online zewnętrznego udostępniania ustawienie, aby **Zezwalaj na udostępnianie tylko w przypadku użytkowników zewnętrznych, które już istnieją w Twojej organizacji katalog**. Użytkownicy mogą przejść do witryny zewnętrznie udostępnione i wybrać z zewnętrznych współpracowników, dodane przez administratora. Administrator może dodać zewnętrznych współpracowników za pomocą zaproszenia współpracy B2B interfejsów API.


![Usługi OneDrive/SharePoint Online zewnętrznego udostępniania ustawienie](media/o365-external-user/odsp-sharing-setting.png)

Po włączeniu udostępniania zewnętrznego możliwość wyszukiwania dla istniejących użytkowników-gości w Selektor osób programu SharePoint Online (SPO) jest domyślnie, aby dopasować starsze zachowanie WYŁĄCZONY.

Aby włączyć tę funkcję, należy za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie kolekcji dzierżawy i witryny. Można ustawić funkcję przy użyciu Set-SPOTenant i SPOSite zestaw poleceń cmdlet, które pozwalają członkom wyszukiwanie wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie wpływają na już aprowizowanych witryn SPO.

## <a name="next-steps"></a>Kolejne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
* [Delegowanie zaproszeń współpracy B2B](delegate-invitations.md)
* [Grupy dynamiczne i współpracy B2B](use-dynamic-groups.md)
* [Rozwiązywanie problemów z współpracy B2B usługi Azure Active Directory](troubleshoot.md)
