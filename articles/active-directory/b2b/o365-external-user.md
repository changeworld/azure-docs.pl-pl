---
title: Udostępnianie zewnętrzne usługi Office 365 i współpraca b2b — usługa Azure AD
description: W tym artykule omówiono udostępnianie zasobów partnerom zewnętrznym przy użyciu usługi O365 i współpracy B2B usługi Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/24/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d70aed6fbe0f09ea6284f913c88186ecf94e297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272265"
---
# <a name="office-365-external-sharing-and-azure-active-directory-b2b-collaboration"></a>Udostępnianie zewnętrzne usługi Office 365 i współpraca usługi Azure Active Directory B2B

Udostępnianie zewnętrzne w usłudze Office 365 (OneDrive, SharePoint Online, Ujednolicone grupy itp.) i współpracy B2B usługi Azure Active Directory (Azure AD) to technicznie to samo. Wszystkie udostępnianie zewnętrzne (z wyjątkiem usługi OneDrive/SharePoint Online), w tym goście w grupach usługi Office 365, już używa interfejsów API zaproszenia do współpracy usługi Azure AD B2B do udostępniania.

## <a name="how-does-azure-ad-b2b-differ-from-external-sharing-in-sharepoint-online"></a>Czym różni się usługa Azure AD B2B od udostępniania zewnętrznego w usłudze SharePoint Online?

Usługa OneDrive/SharePoint Online ma osobnego menedżera zaproszeń. Obsługa udostępniania zewnętrznego w usłudze OneDrive/SharePoint Online rozpoczęta przed rozpoczęciem usługi Azure AD. Z biegiem czasu udostępnianie zewnętrzne usługi OneDrive/SharePoint Online naliczono kilka funkcji i wiele milionów użytkowników korzystających z wbudowanego wzorca udostępniania produktu. Istnieją jednak pewne subtelne różnice między działaniem udostępniania zewnętrznego usługi OneDrive/SharePoint Online a współpracą usługi Azure AD B2B. Więcej informacji na temat udostępniania zewnętrznego usługi OneDrive/SharePoint Online można uzyskać w [omówieniu udostępniania zewnętrznego](https://docs.microsoft.com/sharepoint/external-sharing-overview). Proces zazwyczaj różni się od usługi Azure AD B2B w następujących sposobów:

- Usługa OneDrive/SharePoint Online dodaje użytkowników do katalogu po zrealizowaniu zaproszeń przez użytkowników. Tak przed realizacją nie widać użytkownika w portalu usługi Azure AD. Jeśli w międzyczasie inna witryna zaprosi użytkownika, zostanie wygenerowane nowe zaproszenie. Jednak podczas korzystania z usługi Azure AD B2B współpracy, użytkownicy są dodawane natychmiast na zaproszenie, dzięki czemu są wyświetlane wszędzie.

- Środowisko realizacji w usłudze OneDrive/SharePoint Online wygląda inaczej niż środowisko współpracy usługi Azure AD B2B. Gdy użytkownik zrealizuje zaproszenie, środowiska wyglądają podobnie.

- Zaproszeni użytkownicy korzystający z współpracy usługi Azure AD B2B mogą być wybierani z okien dialogowych udostępniania usługi OneDrive/SharePoint Online. Zaproszeni użytkownicy usługi OneDrive/SharePoint Online są również obecni w usłudze Azure AD po zrealizowaniu zaproszeń.

- Wymagania licencyjne różnią się. Dla każdej płatnej licencji usługi Azure AD można umożliwić maksymalnie 5 użytkownikom-gościom dostęp do płatnych funkcji usługi Azure AD. Aby dowiedzieć się więcej o licencjonowaniu, zobacz [Licencjonowanie usługi Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/licensing-guidance) i ["Co to jest użytkownik zewnętrzny?" w omówieniu udostępniania zewnętrznego usługi SharePoint Online](https://docs.microsoft.com/sharepoint/external-sharing-overview#what-happens-when-users-share).

Aby zarządzać udostępnianiem zewnętrznym w usłudze OneDrive/SharePoint Online za pomocą współpracy usługi Azure AD B2B, ustaw ustawienie udostępniania zewnętrznego usługi OneDrive/SharePoint Online **na Zezwalaj na udostępnianie tylko użytkownikom zewnętrznym, którzy już istnieją w katalogu organizacji.** Użytkownicy mogą przechodzić do witryn udostępnionych zewnętrznie i wybierać spośród zewnętrznych współpracowników, których dodał administrator. Administrator może dodawać zewnętrznych współpracowników za pośrednictwem interfejsów API zaproszenia do współpracy B2B.


![Ustawienie udostępniania zewnętrznego usługi OneDrive/SharePoint Online](media/o365-external-user/odsp-sharing-setting.png)

Po włączeniu udostępniania zewnętrznego możliwość wyszukiwania istniejących użytkowników-gości w selektorze osób usługi SharePoint Online (SPO) jest domyślnie WYŁĄCZONA, aby dopasować starsze zachowanie.

Tę funkcję można włączyć za pomocą ustawienia "ShowPeoplePickerSuggestionsForGuestUsers" na poziomie dzierżawy i zbioru witryn. Funkcję można ustawić za pomocą poleceń cmdlet Set-SPOTenant i Set-SPOSite, które umożliwiają członkom przeszukiwanie wszystkich istniejących użytkowników-gości w katalogu. Zmiany w zakresie dzierżawy nie mają wpływu na już zainicjowane witryny SPO.

## <a name="next-steps"></a>Następne kroki

* [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
* [Dodawanie użytkownika współpracy B2B do roli](add-guest-to-role.md)
* [Delegowanie zaproszeń do współpracy B2B](delegate-invitations.md)
* [Grupy dynamiczne i współpraca B2B](use-dynamic-groups.md)
* [Rozwiązywanie problemów ze współpracą usługi Azure Active Directory B2B](troubleshoot.md)
