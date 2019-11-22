---
title: Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD
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
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1683f645e1ed81a8a415214fbb83ee80572be3ee
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275838"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD
Po skonfigurowaniu automatycznej aprowizacji dla aplikacji (w tym sprawdzić, czy poświadczenia aplikacji podane w usłudze Azure AD w celu nawiązania połączenia z aplikacją są prawidłowe), użytkownicy i/lub grupy są udostępniane aplikacji. Obsługa administracyjna jest określana na podstawie następujących elementów:

-   Użytkowników i grup **przypisanych** do aplikacji. Należy pamiętać, że grupy zagnieżdżone lub grupy pakietu Office 365 nie są obsługiwane. Aby uzyskać więcej informacji na temat przypisywania, zobacz [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw w Azure Active Directory](assign-user-or-group-access-portal.md).
-   Określa, czy **mapowania atrybutów** są włączone i skonfigurowane do synchronizowania prawidłowych atrybutów z usługi Azure AD z aplikacją. Aby uzyskać więcej informacji na temat mapowań atrybutów, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
-   Czy istnieje **Filtr zakresu** , który umożliwia filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](define-conditional-rules-for-provisioning-user-accounts.md).
  
Jeśli zauważysz, że nie zainicjowano obsługi administracyjnej użytkowników, zapoznaj się z [dziennikami aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) w usłudze Azure AD. Wyszukaj wpisy dziennika dla określonego użytkownika.

Możesz uzyskać dostęp do dzienników aprowizacji w Azure Portal, wybierając pozycję **Azure Active Directory** &gt; **aplikacje korporacyjne** &gt; **dzienniki aprowizacji (wersja zapoznawcza)** w sekcji **działanie** . Możesz przeszukiwać dane aprowizacji na podstawie nazwy użytkownika lub identyfikatora w systemie źródłowym lub docelowym. Aby uzyskać szczegółowe informacje, zobacz [dzienniki aprowizacji (wersja zapoznawcza)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Dzienniki aprowizacji rejestrują wszystkie operacje wykonywane przez usługę aprowizacji, w tym kwerendy usługi Azure AD dla przypisanych użytkowników, którzy znajdują się w zakresie aprowizacji, wykonywania zapytań względem aplikacji docelowej w przypadku istnienia tych użytkowników, porównując obiekty użytkownika między System. Następnie Dodaj, zaktualizuj lub Wyłącz konto użytkownika w systemie docelowym na podstawie porównania.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Ogólne obszary problemów z obsługą administracyjną, które należy wziąć pod uwagę
Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść do szczegółów, jeśli masz pomysł, gdzie zacząć.

- [Nie można uruchomić usługi aprowizacji](#provisioning-service-does-not-appear-to-start)
- [Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Nie można uruchomić usługi aprowizacji
Jeśli ustawisz **stan aprowizacji** na **włączony** w **Azure Active Directory &gt; aplikacje dla przedsiębiorstw &gt; \[nazwa aplikacji\] &gt;aprowizacji** . Jednak na tej stronie nie są wyświetlane żadne inne szczegóły stanu po kolejnych ponownych ładowaniach, prawdopodobnie usługa jest uruchomiona, ale nie ukończyła jeszcze cyklu początkowego. Sprawdź **dzienniki aprowizacji (wersja zapoznawcza)** opisane powyżej, aby określić, jakie operacje usługa działa, i wystąpiły błędy.

>[!NOTE]
>Cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym. Cykl początkowy pozwala zwiększyć wydajność kolejnych synchronizacji.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Dzienniki aprowizacji mówią, że użytkownicy są pomijani i nie są administracyjni, nawet jeśli są przypisani

Jeśli użytkownik jest wyświetlany jako "pominięty" w dziennikach aprowizacji, ważne jest, aby sprawdzić, czy jest to karta **kroki** w dzienniku. Poniżej przedstawiono typowe przyczyny i rozwiązania:

- **Skonfigurowano filtr określania zakresu** **, który umożliwia filtrowanie użytkownika na podstawie wartości atrybutu**. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz [Określanie zakresu filtrów](define-conditional-rules-for-provisioning-user-accounts.md).
- **Użytkownik jest "nieefektywnie uprawniony".** Jeśli zobaczysz ten konkretny komunikat o błędzie, przyczyną jest problem z rekordem przypisania użytkownika przechowywanym w usłudze Azure AD. Aby rozwiązać ten problem, Cofnij przypisanie użytkownika (lub grupy) z aplikacji i ponownie przypisz go. Aby uzyskać więcej informacji na temat przypisywania, zobacz [przypisywanie użytkowników lub grup dostępu](assign-user-or-group-access-portal.md).
- **Brak wymaganego atrybutu lub nie został on wypełniony dla użytkownika.** Ważną kwestią do uwzględnienia podczas konfigurowania aprowizacji jest przejrzenie i skonfigurowanie mapowań atrybutów i przepływów pracy, które definiują, które właściwości użytkownika (lub grupy) będą przepływać z usługi Azure AD do aplikacji. Ta konfiguracja obejmuje ustawienie "dopasowania właściwości" służącego do unikatowego identyfikowania i dopasowywania użytkowników/grup między tymi dwoma systemami. Aby uzyskać więcej informacji na temat tego ważnego procesu, zobacz [Dostosowywanie mapowań atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory](customize-application-attributes.md).
- **Mapowania atrybutów dla grup:** Inicjowanie obsługi administracyjnej nazw grup i grup, oprócz członków, jeśli są obsługiwane w przypadku niektórych aplikacji. Tę funkcję można włączyć lub wyłączyć, włączając lub wyłączając **Mapowanie** dla obiektów grupy wyświetlanych na karcie **aprowizacji** . Jeśli włączono grupy aprowizacji, należy sprawdzić mapowania atrybutów, aby upewnić się, że odpowiednie pole jest używane dla "zgodnego identyfikatora". IDENTYFIKATORem zgodnym może być nazwa wyświetlana lub alias adresu e-mail. Grupa i jej elementy członkowskie nie są obsługiwane, jeśli właściwość pasująca jest pusta lub nie została wypełniona dla grupy w usłudze Azure AD.

## <a name="next-steps"></a>Następne kroki

[Azure AD Connect Sync: wyjaśnienie aprowizacji deklaracyjnej](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
