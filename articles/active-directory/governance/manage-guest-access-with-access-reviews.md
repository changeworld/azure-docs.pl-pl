---
title: Zarządzanie dostępem gościa za pomocą recenzji dostępu — Azure AD
description: Zarządzanie użytkownikami-gośćmi jako członkami grupy lub przypisanymi do aplikacji za pomocą przeglądów dostępu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bf2f9503ae704110786a1e73aec3da18c17e4ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932423"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD


Dzięki usłudze Azure Active Directory (Azure AD) można łatwo włączyć współpracę ponad granicami organizacji przy użyciu [funkcji usługi Azure AD B2B.](../b2b/what-is-b2b.md) Użytkownicy-goście z innych dzierżaw mogą być [zapraszani przez administratorów](../b2b/add-users-administrator.md) lub [innych użytkowników.](../b2b/what-is-b2b.md) Ta funkcja dotyczy również tożsamości społecznościowych, takich jak konta Microsoft.

Można również łatwo zapewnić, że użytkownicy-goście mają odpowiedni dostęp. Goście mogą poprosić gości lub decydentów o udział w przeglądzie dostępu i ponowne zaświadczenie o dostępie gości do nich. Na podstawie sugestii pochodzących z usługi Azure AD recenzenci mogą wyrazić opinię dotyczącą przedłużenia dostępu poszczególnych użytkowników. Po zakończeniu przeglądu dostępu można wprowadzić zmiany i usunąć dostęp dla gości, którzy nie są już jej potrzebować.

> [!NOTE]
> Ten dokument koncentruje się na przeglądaniu dostępu użytkowników-gości. Jeśli chcesz przejrzeć dostęp wszystkich użytkowników, nie tylko gości, zobacz [Zarządzanie dostępem użytkowników za pomocą opinii o dostępie](manage-user-access-with-access-reviews.md). Jeśli chcesz przejrzeć członkostwo użytkowników w rolach administracyjnych, takich jak administrator globalny, zobacz [Rozpoczynanie przeglądu dostępu w usłudze Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2

Aby uzyskać więcej informacji, [Wymagania licencyjne](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Tworzenie i wykonywanie przeglądu dostępu dla gości

Najpierw jako administrator globalny lub administrator użytkowników przejdź do [strony zarządzania tożsamościami,](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) aby upewnić się, że przeglądy dostępu są gotowe dla Twojej organizacji.

Usługa Azure AD umożliwia kilka scenariuszy do przeglądania użytkowników-gości.

Możesz przejrzeć:

 - Grupa w usłudze Azure AD, która ma co najmniej jednego gości jako członków.
 - Aplikacja połączona z usługą Azure AD, która ma przypisanego do niej co najmniej jednego użytkownika-gościa. 

Następnie możesz zdecydować, czy poprosić każdego gościa o sprawdzenie własnego dostępu, czy poprosić jednego lub więcej użytkowników o sprawdzenie dostępu każdego gościa.

 Te scenariusze są omówione w poniższych sekcjach.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Poproś gości o sprawdzenie własnego członkostwa w grupie

Możesz użyć recenzji dostępu, aby upewnić się, że użytkownicy, którzy zostali zaproszeni i dodani do grupy, nadal potrzebują dostępu. Możesz łatwo poprosić gości o sprawdzenie własnego członkostwa w tej grupie.

1. Aby utworzyć przegląd dostępu dla grupy, wybierz recenzję, aby uwzględnić tylko członków użytkownika-gościa i że członkowie sami się przejrzali. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

2. Poproś każdego gościa, aby zapoznał się z własnym członkostwem. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD z łączem do przeglądu dostępu. Usługa Azure AD ma instrukcje dla gości dotyczące [sposobu przeglądania dostępu do grup lub aplikacji.](perform-access-review.md)

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Finalniej przeglądu dostępu grup lub aplikacji](complete-access-review.md).

4. Oprócz tych użytkowników, którzy odmówili sobie potrzeby ciągłego dostępu, możesz również usunąć użytkowników, którzy nie odpowiedzieli. Użytkownicy, którzy nie odpowiadają, potencjalnie nie otrzymują już wiadomości e-mail.

5. Jeśli grupa nie jest używana do zarządzania dostępem, możesz też usunąć użytkowników, którzy nie zostali wybrani do udziału w recenzji, ponieważ nie przyjęli zaproszenia. Niezaechodnia może wskazywać, że adres e-mail zaproszonego użytkownika miał literówkę. Jeśli grupa jest używana jako lista dystrybucyjna, być może niektórzy użytkownicy-goście nie zostali wybrani do udziału, ponieważ są obiektami kontaktu.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Poproś sponsora o sprawdzenie członkostwa gościa w grupie

Możesz poprosić sponsora, takiego jak właściciel grupy, o sprawdzenie potrzeby gościa w zakresie dalszego członkostwa w grupie.

1. Aby utworzyć przegląd dostępu dla grupy, wybierz recenzję, aby uwzględnić tylko członków użytkownika-gościa. Następnie określ jednego lub więcej recenzentów. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie każdy z nich otrzymuje wiadomość e-mail z usługi Azure AD z łączem do panelu dostępu, w którym [przegląda dostęp do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Finalniej przeglądu dostępu grup lub aplikacji](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Poproś gości o sprawdzenie własnego dostępu do aplikacji

Można użyć przeglądów dostępu, aby upewnić się, że użytkownicy, którzy zostali zaproszeni do określonej aplikacji nadal potrzebują dostępu. Goście mogą z łatwością poprosić gości o sprawdzenie ich potrzeb dostępu.

1. Aby utworzyć przegląd dostępu dla aplikacji, wybierz recenzję, aby uwzględnić tylko gości i aby użytkownicy przeglądali własny dostęp. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

2. Poproś każdego gościa o sprawdzenie własnego dostępu do aplikacji. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD. Ta wiadomość e-mail ma łącze do przeglądu dostępu w panelu dostępu organizacji. Usługa Azure AD ma instrukcje dla gości dotyczące [sposobu przeglądania dostępu do grup lub aplikacji.](perform-access-review.md)

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Finalniej przeglądu dostępu grup lub aplikacji](complete-access-review.md).

4. Oprócz użytkowników, którzy odmówili sobie potrzeby ciągłego dostępu, możesz również usunąć użytkowników-gości, którzy nie odpowiedzieli. Użytkownicy, którzy nie odpowiadają, potencjalnie nie otrzymują już wiadomości e-mail. Możesz też usunąć użytkowników-gości, którzy nie zostali wybrani do udziału, zwłaszcza jeśli nie zostali niedawno zaproszeni. Ci użytkownicy nie zaakceptowali zaproszenia, więc nie mieli dostępu do aplikacji. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Poproś sponsora o sprawdzenie dostępu gościa do aplikacji

Możesz poprosić sponsora, takiego jak właściciel aplikacji, o sprawdzenie potrzeby gościa w zakresie dalszego dostępu do aplikacji.

1. Aby utworzyć przegląd dostępu dla aplikacji, wybierz recenzję, aby uwzględnić tylko gości. Następnie określ jednego lub więcej użytkowników jako recenzentów. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

2. Poproś recenzentów o wyrażenie opinii. Domyślnie każdy z nich otrzymuje wiadomość e-mail z usługi Azure AD z łączem do panelu dostępu, w którym [przegląda dostęp do grup lub aplikacji](perform-access-review.md).

3. Po otrzymaniu opinii recenzentów zakończ przegląd dostępu i zastosuj zmiany. Aby uzyskać więcej informacji, zobacz [Finalniej przeglądu dostępu grup lub aplikacji](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Poproś gości o sprawdzenie, czy potrzebują dostępu, ogólnie

W niektórych organizacjach goście mogą nie być świadomi członkostwa w grupach.

> [!NOTE]
> Starsze wersje witryny Azure portal nie zezwalają na dostęp administracyjny użytkowników z usertype gościa. W niektórych przypadkach administrator w katalogu mógł zmienić wartość UserType gościa na Member przy użyciu programu PowerShell. Jeśli ta zmiana wystąpiła wcześniej w katalogu, poprzednie zapytanie może nie obejmować wszystkich użytkowników-gości, którzy w przeszłości mieli uprawnienia dostępu administracyjnego. W takim przypadku należy zmienić usertype gościa lub ręcznie dołączyć gościa w członkostwie w grupie.

1. Utwórz grupę zabezpieczeń w usłudze Azure AD z gośćmi jako członkami, jeśli odpowiednia grupa jeszcze nie istnieje. Na przykład można utworzyć grupę z ręcznie utrzymywanym członkostwem gości. Można też utworzyć grupę dynamiczną o nazwie "Goście contoso" dla użytkowników w dzierżawie contoso, którzy mają wartość atrybutu UserType gościa.  Aby zwiększyć wydajność, upewnij się, że grupa jest głównie gośćmi — nie wybieraj grupy, która ma użytkowników członkowskich, ponieważ użytkownicy członkowie nie muszą być sprawdzani.  Należy również pamiętać, że użytkownik-gość, który jest członkiem grupy, może zobaczyć innych członków grupy.

2. Aby utworzyć przegląd dostępu dla tej grupy, wybierz recenzentów, którzy mają być członkami. Aby uzyskać więcej informacji, zobacz [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md).

3. Poproś każdego gościa, aby zapoznał się z własnym członkostwem. Domyślnie każdy gość, który zaakceptował zaproszenie, otrzymuje wiadomość e-mail z usługi Azure AD z łączem do przeglądu dostępu w panelu dostępu organizacji. Usługa Azure AD ma instrukcje dla gości dotyczące [sposobu przeglądania dostępu do grup lub aplikacji.](perform-access-review.md)  Goście, którzy nie przyjęli zaproszenia, pojawią się w wynikach recenzji jako "Nie powiadomieni".

4. Po recenzenci podać dane wejściowe, zatrzymać przegląd dostępu. Aby uzyskać więcej informacji, zobacz [Finalniej przeglądu dostępu grup lub aplikacji](complete-access-review.md).

5. Usuń dostęp gościa dla gości, którym odmówiono, nie udało się ukończyć recenzji lub nie przyjęli wcześniej zaproszenia. Jeśli niektórzy goście są kontakty, które zostały wybrane do udziału w przeglądzie lub nie wcześniej zaakceptować zaproszenie, można wyłączyć ich kont przy użyciu witryny Azure portal lub programu PowerShell. Jeśli gość nie potrzebuje już dostępu i nie jest kontaktem, można usunąć ich obiekt użytkownika z katalogu za pomocą witryny Azure portal lub programu PowerShell, aby usunąć obiekt użytkownika-gościa.

## <a name="next-steps"></a>Następne kroki

[Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)







