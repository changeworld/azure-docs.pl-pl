---
title: Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD | Microsoft Docs
description: Jak rozwiązywać typowe problemy związane z niewidocznymi użytkownikami w aplikacji galerii usługi Azure AD skonfigurowanym do aprowizacji użytkowników w usłudze Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: b80539181e6614283b6170b9cd9d4db85f812a5f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879893"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD
Po skonfigurowaniu automatycznej aprowizacji dla aplikacji (w tym sprawdzić, czy poświadczenia aplikacji podane w usłudze Azure AD w celu nawiązania połączenia z aplikacją są prawidłowe), użytkownicy i/lub grupy są udostępniane aplikacji. Obsługa administracyjna jest określana na podstawie następujących elementów:

-   Użytkowników i grup przypisanych do aplikacji. Należy pamiętać, że grupy zagnieżdżone lub grupy pakietu Office 365 nie są obsługiwane. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w Azure Active Directory](assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizowania prawidłowych atrybutów z usługi Azure AD z aplikacją. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
-   Czy istnieje **Filtr zakresu** , który umożliwia filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](define-conditional-rules-for-provisioning-user-accounts.md).
  
Jeśli zauważysz, że użytkownicy nie są administracyjni, zapoznaj się z dziennikami inspekcji w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Dostęp do dzienników inspekcji aprowizacji można uzyskać w Azure Portal na karcie **dzienniki &gt; &gt; inspekcji &gt; \[aplikacji\] Azure Active Directory Enterprise** . Przefiltruj dzienniki w kategorii **aprowizacji konta** , aby zobaczyć tylko zdarzenia aprowizacji dla tej aplikacji. Użytkowników można wyszukiwać na podstawie "zgodnego identyfikatora", który został skonfigurowany dla nich w mapowaniu atrybutów. Na przykład, jeśli skonfigurowano "główną nazwę użytkownika" lub "adres e-mail" jako pasujący atrybut w stronie usługi Azure AD, a użytkownik nie będzie aprowizacji ma wartość "audrey@contoso.com", Wyszukaj w dziennikach inspekcji "audrey@contoso.com" i Przejrzyj wpisy zwracać.

Dzienniki inspekcji aprowizacji rejestrują wszystkie operacje wykonywane przez usługę aprowizacji, w tym Wysyłanie zapytań do usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji, wykonywanie zapytań względem aplikacji docelowej pod kątem istnienia tych użytkowników, porównywanie obiektów użytkownika między systemem. Następnie Dodaj, zaktualizuj lub Wyłącz konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemów z obsługą administracyjną, które należy wziąć pod uwagę
Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść do szczegółów, jeśli masz pomysł, gdzie zacząć.

- [Nie można uruchomić usługi aprowizacji](#provisioning-service-does-not-appear-to-start)
- [Dzienniki inspekcji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani](#audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Nie można uruchomić usługi aprowizacji
W przypadku ustawienia **stanu aprowizacji** na **włączony** w sekcji **Azure Active Directory &gt; aplikacje &gt; \[\] &gt;dla przedsiębiorstw** w Azure Portal . Jednak na tej stronie nie są wyświetlane żadne inne szczegóły stanu po kolejnych ponownych ładowaniach, prawdopodobnie usługa jest uruchomiona, ale nie ukończyła jeszcze początkowej synchronizacji. Sprawdź **dzienniki inspekcji** opisane powyżej, aby określić operacje wykonywane przez usługę, a także błędy.

>[!NOTE]
>Początkowa synchronizacja może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kolejne synchronizacje po synchronizacji początkowej są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po synchronizacji początkowej. Synchronizacja początkowa pozwala zwiększyć wydajność kolejnych synchronizacji.
>


## <a name="audit-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki inspekcji mówią, że użytkownicy są pomijani i nie są obsługiwani, nawet jeśli są przypisani

Gdy użytkownik wyświetla jako "pominięte" w dziennikach inspekcji, ważne jest odczytywanie szczegółowych informacji w komunikacie dziennika w celu ustalenia przyczyny. Poniżej przedstawiono typowe przyczyny i rozwiązania:

- **Skonfigurowano filtr zakresu** **jest to filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [Określanie zakresu filtrów](define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik jest "nieefektywnie uprawniony".** Jeśli zobaczysz ten konkretny komunikat o błędzie, przyczyną jest problem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z aplikacji i ponownie przypisz go. Aby uzyskać więcej informacji na temat przypisywania, zobacz [przypisywanie użytkowników lub grup dostępu](assign-user-or-group-access-portal.md).
- **Brak wymaganego atrybutu lub nie został on wypełniony dla użytkownika.** Ważną kwestią do uwzględnienia podczas konfigurowania aprowizacji jest przejrzenie i skonfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Ta konfiguracja obejmuje ustawienie "dopasowania właściwości" służącego do unikatowego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". IDENTYFIKATORem zgodnym może być nazwa wyświetlana lub alias adresu e-mail. Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość pasująca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

[Synchronizacja w programie Azure AD Connect: omówienie aprowizacji deklaratywnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
