---
title: Rozwiązywanie problemów z zarządzaniem uprawnieniami — usługa Azure AD
description: Dowiedz się więcej o niektórych elementach, które należy sprawdzić, aby ułatwić rozwiązywanie problemów z zarządzaniem uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c38e1a61827da547bb39a699a0e92043e63466c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128474"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Rozwiązywanie problemów z zarządzaniem uprawnieniami usługi Azure AD

W tym artykule opisano niektóre elementy, które należy sprawdzić, aby ułatwić rozwiązywanie problemów z zarządzaniem uprawnieniami usługi Azure Active Directory (Azure AD).

## <a name="administration"></a>Administracja

* Jeśli podczas konfigurowania zarządzania uprawnieniami zostanie wyświetlony komunikat o odmowie dostępu, a administratorem globalnym jest zapewnienie, że w katalogu jest [dostępna licencja Usługi Azure AD Premium P2 (lub EMS E5).](entitlement-management-overview.md#license-requirements)

* Jeśli podczas tworzenia lub wyświetlania pakietów dostępu zostanie wyświetlony komunikat o odmowie dostępu i jesteś członkiem grupy twórców wykazu, przed utworzeniem pierwszego pakietu dostępu należy [utworzyć katalog.](entitlement-management-catalog-create.md)

## <a name="resources"></a>Resources

* Role aplikacji są definiowane przez samą aplikację i są zarządzane w usłudze Azure AD. Jeśli aplikacja nie ma żadnych ról zasobów, zarządzanie uprawnieniami przypisuje użytkowników do roli **dostępu domyślnego.**

    Należy zauważyć, że witryna Azure portal może również wyświetlać jednostki usługi dla usług, których nie można wybrać jako aplikacji.  W szczególności **exchange online** i **SharePoint Online** są usługi, a nie aplikacje, które mają role zasobów w katalogu, więc nie mogą być uwzględnione w pakiecie dostępu.  Zamiast tego użyj licencjonowania opartego na grupach w celu uzyskania odpowiedniej licencji dla użytkownika, który potrzebuje dostępu do tych usług.

* Aby grupa mogła być zasobem w pakiecie dostępu, musi być możliwa do zmodyfikowania w usłudze Azure AD.  Grupy, które pochodzą z lokalnej usługi Active Directory, nie mogą być przypisane jako zasoby, ponieważ nie można zmienić ich atrybutów właściciela ani członków w usłudze Azure AD.   Grupy, które pochodzą z usługi Exchange Online jako grupy dystrybucji, również nie mogą być modyfikowane w usłudze Azure AD. 

* Biblioteki dokumentów usługi SharePoint Online i pojedyncze dokumenty nie mogą być dodawane jako zasoby.  Zamiast tego utwórz [grupę zabezpieczeń usługi Azure AD](../fundamentals/active-directory-groups-create-azure-portal.md), uwzględnij tę grupę i rolę lokacji w pakiecie dostępu, a w usłudze SharePoint Online użyj tej grupy do kontrolowania dostępu do biblioteki dokumentów lub dokumentu.

* Jeśli istnieją użytkownicy, którzy zostali już przypisani do zasobu, którym chcesz zarządzać za pomocą pakietu dostępu, upewnij się, że użytkownicy są przypisani do pakietu dostępu przy użyciu odpowiednich zasad. Na przykład możesz uwzględnić grupę w pakiecie dostępu, który ma już użytkowników w grupie. Jeśli Ci użytkownicy w grupie wymagają ciągłego dostępu, muszą mieć odpowiednie zasady dla pakietów dostępu, aby nie utracić dostępu do grupy. Pakiet dostępu można przypisać, prosząc użytkowników o zażądanie pakietu dostępu zawierającego ten zasób lub przez bezpośrednie przypisanie ich do pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień żądania i zatwierdzania pakietu dostępu](entitlement-management-access-package-request-policy.md).

* Po usunięciu członka zespołu są one również usuwane z grupy usługi Office 365. Usunięcie z funkcji czatu zespołu może być opóźnione. Aby uzyskać więcej informacji, zobacz [Członkostwo w grupie](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

* Upewnij się, że katalog nie jest skonfigurowany do obsługi wielu regionów geograficznych. Zarządzanie upoważnieniami nie obsługuje obecnie wielu lokalizacji geograficznych na potrzeby usługi SharePoint Online. Lokacje usługi SharePoint Online muszą znajdować się w domyślnej lokalizacji geograficznej, aby podlegać zarządzaniu upoważnieniami. Aby uzyskać więcej informacji, zobacz [Możliwości wielu geodezji w usłudze OneDrive i usłudze SharePoint Online](https://docs.microsoft.com/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365).

## <a name="access-packages"></a>Pakiety dostępu

* Jeśli spróbujesz usunąć pakiet dostępu lub zasady i zobaczysz komunikat o błędzie informujący, że istnieją aktywne przypisania, jeśli nie widzisz żadnych użytkowników z przypisaniami, sprawdź, czy ostatnio usunięci użytkownicy nadal mają przypisania. Podczas 30-dniowego okna po usunięciu użytkownika można przywrócić konto użytkownika.   

## <a name="external-users"></a>Użytkownicy zewnętrzni

* Gdy użytkownik zewnętrzny chce zażądać dostępu do pakietu dostępu, upewnij się, że używa **łącza Portalu Mój dostęp** dla pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Udostępnianie łącza, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md). Jeśli użytkownik zewnętrzny po prostu odwiedza **myaccess.microsoft.com** i nie korzysta z pełnego łącza portalu Mój dostęp, zobaczy pakiety dostępu dostępne dla nich we własnej organizacji, a nie w organizacji.

* Jeśli użytkownik zewnętrzny nie może zażądać dostępu do pakietu dostępu lub nie może uzyskać dostępu do zasobów, należy sprawdzić [ustawienia dla użytkowników zewnętrznych](entitlement-management-external-users.md#settings-for-external-users).

* Jeśli nowy użytkownik zewnętrzny, który nie został wcześniej podpisany w Twoim katalogu, odbierze pakiet dostępu zawierający lokację usługi SharePoint Online, jego pakiet dostępu będzie wyświetlany jako nie w pełni dostarczony do momentu aprowizacji konta w usłudze SharePoint Online. Aby uzyskać więcej informacji na temat ustawień udostępniania, zobacz [Przeglądanie ustawień udostępniania zewnętrznego usługi SharePoint Online](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Żądania

* Gdy użytkownik chce zażądać dostępu do pakietu dostępu, upewnij się, że używa **łącza Portalu Mój dostęp** dla pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Udostępnianie łącza, aby zażądać pakietu dostępu](entitlement-management-access-package-settings.md).

* Jeśli otworzysz portal Mój dostęp przy użyciu przeglądarki w trybie prywatnym lub incognito, może to spowodować konflikt z zachowaniem logowania. Zalecamy, aby nie używać trybu prywatnego ani incognito w przeglądarce podczas odwiedzania portalu Mój dostęp.

* Gdy użytkownik, którego nie ma jeszcze w katalogu, loguje się do portalu Mój dostęp w celu zażądania pakietu dostępu, upewnij się, że uwierzytelnia się przy użyciu konta organizacyjnego. Konto organizacyjne może być kontem w katalogu zasobów lub w katalogu, który znajduje się w jednej z zasad pakietu dostępu. Jeśli konto użytkownika nie jest kontem organizacyjnym lub katalog, w którym użytkownik się uwierzytelnia, nie jest uwzględniony w zasadach, wówczas użytkownik nie będzie widział pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md).

* Jeśli użytkownik nie może się zalogować do katalogu zasobów, nie będzie w stanie zażądać dostępu w portalu Mój dostęp. Aby użytkownik mógł zażądać dostępu, musisz usunąć blokadę logowania z profilu użytkownika. Aby usunąć blok logowania, w witrynie Azure Portal kliknij pozycję **Usługa Azure Active Directory**, kliknij pozycję **Użytkownicy**, kliknij pozycję użytkownik, a następnie kliknij pozycję **Profil**. Edytuj sekcję **Ustawienia** i zmień **pozycję Zablokuj zaloguj się** na **Nie**. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu usługi Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Można również sprawdzić, czy użytkownik został zablokowany z powodu [zasad ochrony tożsamości](../identity-protection/howto-unblock-user.md).

* W portalu Mój dostęp, jeśli użytkownik jest zarówno żądający, jak i osoba zatwierdzająca, nie zobaczy swojego żądania pakietu dostępu na stronie **Zatwierdzenia.** Takie zachowanie jest celowe — użytkownik nie może zatwierdzić własnego żądania. Upewnij się, że żądany pakiet dostępu ma dodatkowe osoby zatwierdzające skonfigurowane dla zasad. Aby uzyskać więcej informacji, zobacz [Zmienianie ustawień żądania i zatwierdzania pakietu dostępu](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Wyświetlanie błędów dostarczenia żądania

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Żądania**.

1. Wybierz żądanie, które chcesz wyświetlić.

    Jeśli żądanie ma błędy dostawy, stan żądania będzie **niedostarczony** lub **częściowo dostarczony.**

    Jeśli występują błędy dostarczania, w okienku szczegółów żądania będzie liczba błędów dostarczania.

1. Kliknij liczbę, aby wyświetlić wszystkie błędy dostarczania żądania.

### <a name="reprocess-a-request"></a>Ponowne przetwarzanie żądania

Jeśli żądanie napotka błąd, można ponownie przetworzyć żądanie, aby spróbować ponownie. Można ponownie przetworzyć tylko żądanie, które ma stan **Dostawy nie powiodło się** lub częściowo **dostarczone** i ukończoną datę mniej niż jeden tydzień.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Żądania**.

1. Kliknij żądanie, które chcesz ponownie przetworzyć.

1. W okienku szczegółów żądania kliknij pozycję **Ponowne przetwarzanie żądania**.

    ![Ponowne przetwarzanie żądania nie powiodło się](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Anulowanie oczekującego żądania

Można anulować tylko oczekujące żądanie, które nie zostało jeszcze dostarczone lub którego dostawa nie powiodła się.

**Rola wstępna:** Administrator globalny, administrator użytkownika, właściciel katalogu lub menedżer pakietów programu Access

1. W witrynie Azure portal kliknij pozycję **Usługa Azure Active Directory,** a następnie kliknij pozycję **Zarządzanie tożsamościami**.

1. W menu po lewej stronie kliknij pozycję **Pakiety programu Access,** a następnie otwórz pakiet dostępu.

1. Kliknij **pozycję Żądania**.

1. Kliknij żądanie, które chcesz anulować.

1. W okienku szczegółów żądania kliknij pozycję **Anuluj żądanie**.

## <a name="multiple-policies"></a>Wiele zasad

* Zarządzanie uprawnieniami następuje co najmniej uprzywilejowanych najlepszych praktyk. Gdy użytkownik żąda dostępu do pakietu dostępu, który ma wiele zasad, które mają zastosowanie, zarządzanie uprawnieniami zawiera logikę, aby zapewnić bardziej rygorystyczne lub bardziej szczegółowe zasady są traktowane priorytetowo w stosunku do ogólnych zasad. Jeśli zasada jest ogólna, zarządzanie uprawnieniami może nie wyświetlać zasad żądającyowi lub może automatycznie wybrać bardziej rygorystyczne zasady.

* Rozważmy na przykład pakiet dostępu z dwoma zasadami dla wewnętrznych pracowników, w których obie zasady mają zastosowanie do żądacza. Pierwsza zasada jest dla określonych użytkowników, które zawierają requestor. Druga zasada jest dla wszystkich użytkowników w katalogu, który jest członkiem. W tym scenariuszu pierwsza zasada jest automatycznie wybierana dla żądawcy, ponieważ jest bardziej rygorystyczna. Żądacz nie ma opcji wyboru drugiej zasady.

* Gdy stosuje się wiele zasad, zasady, które są automatycznie wybierane lub zasady, które są wyświetlane do żądacza opiera się na następującej logiki priorytetu:

    | Priorytet polityki | Zakres |
    | --- | --- |
    | P1 | Konkretnie użytkownicy i grupy w katalogu OR Określone połączone organizacje |
    | P2 | Wszyscy członkowie w katalogu (z wyłączeniem gości) |
    | P3 | Wszyscy użytkownicy w katalogu (w tym goście) LUB określone połączone organizacje |
    | P4 | Wszystkie połączone organizacje LUB wszyscy użytkownicy (wszystkie połączone organizacje + nowi użytkownicy zewnętrzni) |
    
    Jeśli którakolwiek z zasad należy do kategorii o wyższym priorytecie, kategorie o niższym priorytecie są ignorowane. Aby uzyskać przykład wyświetlania żądawcy wielu zasad o tym samym priorytecie, zobacz [Wybieranie zasad](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem dla użytkowników zewnętrznych](entitlement-management-external-users.md)
- [Wyświetlanie raportów o tym, jak użytkownicy uzyskali dostęp w zarządzaniu uprawnieniami](entitlement-management-reports.md)
