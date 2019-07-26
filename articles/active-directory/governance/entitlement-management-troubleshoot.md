---
title: Rozwiązywanie problemów z zarządzaniem prawami w usłudze Azure AD (wersja zapoznawcza Azure Active Directory)
description: Dowiedz się więcej na temat niektórych elementów, które należy sprawdzić, aby ułatwić rozwiązywanie problemów Azure Active Directory Zarządzanie prawami (wersja zapoznawcza)
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39ec27c75ff5ba9164b44b0524f90a4e28ab20f1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488975"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Rozwiązywanie problemów z zarządzaniem prawami w usłudze Azure AD

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym artykule opisano niektóre elementy, które należy zaznaczyć, aby pomóc w rozwiązywaniu problemów z zarządzaniem prawami Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Lista kontrolna dotycząca administracji zarządzaniem prawami

* Jeśli zostanie wyświetlony komunikat o odmowie dostępu podczas konfigurowania zarządzania uprawnieniami, a jesteś administratorem globalnym, upewnij się, że katalog ma [licencję na Azure AD — wersja Premium P2 (lub EMS E5)](entitlement-management-overview.md#license-requirements).  
* Jeśli podczas tworzenia lub wyświetlania pakietów dostępu zostanie wyświetlony komunikat Odmowa dostępu, a użytkownik jest członkiem grupy twórców katalogu, należy utworzyć wykaz przed utworzeniem pierwszego pakietu dostępu.

## <a name="checklist-for-adding-a-resource"></a>Lista kontrolna dodawania zasobu

* Aby aplikacja była zasobem w pakiecie dostępu, musi mieć co najmniej jedną rolę zasobu, którą można przypisać. Role są definiowane przez samą aplikację i są zarządzane w usłudze Azure AD. Należy zauważyć, że Azure Portal mogą również wyświetlać jednostki usługi dla usług, których nie można wybrać jako aplikacji.  W szczególności usługi **Exchange Online** i **SharePoint Online** są usługami, a nie aplikacjami, które mają role zasobów w katalogu, więc nie mogą być uwzględnione w pakiecie dostępu.  Zamiast tego należy użyć licencjonowania opartego na grupach w celu nawiązania odpowiedniej licencji dla użytkownika, który potrzebuje dostępu do tych usług.

* Aby grupa była zasobem w pakiecie dostępu, musi być w stanie zmodyfikować ją w usłudze Azure AD.  Grup, które pochodzą z lokalnego Active Directory nie można przypisać jako zasobów, ponieważ nie można zmienić ich właściciela ani atrybutów składowych w usłudze Azure AD.  

* Biblioteki dokumentów usługi SharePoint Online i pojedyncze dokumenty nie mogą być dodawane jako zasoby.  Zamiast tego należy utworzyć grupę zabezpieczeń usługi Azure AD, dołączyć tę grupę i rolę lokacji w pakiecie dostępu, a w usłudze SharePoint Online używać tej grupy do kontrolowania dostępu do biblioteki dokumentów lub dokumentu.

* Jeśli istnieją użytkownicy, którzy zostali już przypisani do zasobu, którym chcesz zarządzać za pomocą pakietu dostępu, upewnij się, że użytkownicy są przypisani do pakietu dostępu przy użyciu odpowiednich zasad. Na przykład możesz chcieć uwzględnić grupę w pakiecie dostępu, który ma już użytkowników w grupie. Jeśli Ci użytkownicy w grupie wymagają ciągłego dostępu, muszą mieć odpowiednie zasady dla pakietów dostępu, aby nie utracić dostępu do grupy. Pakiet dostępu można przypisać, prosząc użytkowników o zażądanie pakietu dostępu zawierającego ten zasób lub przez bezpośrednie przypisanie ich do pakietu dostępu. Aby uzyskać więcej informacji, zobacz [Edytowanie istniejącego pakietu dostępu i zarządzanie nim](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Lista kontrolna udostępniania dostępu użytkownikom zewnętrznym

* Jeśli istnieje [Lista dozwolonych](../b2b/allow-deny-list.md)B2B, użytkownicy, których katalogi są niedozwolone, nie będą mogli żądać dostępu.

* Upewnij się, że nie ma żadnych [zasad dostępu warunkowego](../conditional-access/require-managed-devices.md) , które uniemożliwiają użytkownikom zewnętrznym żądanie dostępu lub możliwość używania aplikacji w pakietach dostępu.

## <a name="checklist-for-request-issues"></a>Lista kontrolna dotycząca problemów z żądaniami

* Gdy użytkownik chce zażądać dostępu do pakietu dostępu, należy się upewnić, że korzysta on z **linku my Access Portal** dla pakietu dostępu. Aby uzyskać więcej informacji, zobacz [łącze Kopiuj mój dostęp do portalu](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Gdy użytkownik loguje się do portalu dostępu w celu zażądania pakietu dostępu, należy się upewnić, że uwierzytelnia się przy użyciu konta organizacyjnego. Konto organizacyjne może być kontem w katalogu zasobów lub w katalogu, który znajduje się w jednej z zasad pakietu dostępu. Jeśli konto użytkownika nie jest kontem organizacyjnym lub katalog nie jest uwzględniony w zasadach, użytkownik nie będzie widział pakietu dostępu. Aby uzyskać więcej informacji, zobacz [żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md).

* Jeśli użytkownik nie może się zalogować do katalogu zasobów, nie będzie w stanie żądać dostępu w portalu My Access. Aby użytkownik mógł zażądać dostępu, należy usunąć blok logowania z profilu użytkownika. Aby usunąć blok logowania, w Azure Portal kliknij pozycję **Azure Active Directory**, kliknij pozycję **Użytkownicy**, kliknij użytkownika, a następnie kliknij pozycję **profil**. Edytuj sekcję **Ustawienia** i Zmień **blok Zaloguj** się na **nie**. Aby uzyskać więcej informacji, zobacz [Dodawanie lub aktualizowanie informacji o profilu użytkownika przy użyciu Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Możesz również sprawdzić, czy użytkownik został zablokowany ze względu na [Zasady ochrony tożsamości](../identity-protection/howto-unblock-user.md).

* Jeśli użytkownik jest obiektem żądającym i osobą zatwierdzającą, w portalu My Access nie zobaczy żądania dotyczącego pakietu dostępu na stronie **zatwierdzenia** . Takie zachowanie jest celowe — użytkownik nie może zatwierdzić własnego żądania. Upewnij się, że żądany pakiet dostępu ma dodatkowe osoby zatwierdzające skonfigurowane dla zasad. Aby uzyskać więcej informacji, zobacz [Edycja istniejących zasad](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Jeśli nowy użytkownik zewnętrzny, który nie został wcześniej podpisany w Twoim katalogu, odbierze pakiet dostępu zawierający witrynę usługi SharePoint Online, jego pakiet dostępu będzie wyświetlany jako nie w pełni dostarczone do momentu aprowizacji konta w usłudze SharePoint Online.

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie raportów dotyczących sposobu, w jaki użytkownicy uzyskują dostęp w usłudze zarządzania uprawnieniami](entitlement-management-reports.md)
