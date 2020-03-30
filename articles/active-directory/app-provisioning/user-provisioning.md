---
title: Automatyczne inicjowanie obsługi administracyjnej użytkowników aplikacji SaaS w usłudze Azure AD | Dokumenty firmy Microsoft
description: Wprowadzenie do sposobu używania usługi Azure AD do automatycznego inicjowania obsługi administracyjnej, wyrównywalowania i ciągłego aktualizowania kont użytkowników w wielu aplikacjach SaaS innych firm.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454537"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>Automatyzacja inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji za pomocą usługi Azure Active Directory

W usłudze Azure Active Directory (Azure AD) termin **inicjowania obsługi administracyjnej aplikacji** odnosi się do automatycznego tworzenia tożsamości użytkowników i ról w aplikacjach w chmurze[(SaaS),](https://azure.microsoft.com/overview/what-is-saas/)do których użytkownicy potrzebują dostępu. Oprócz tworzenia tożsamości użytkowników automatyczne inicjowanie obsługi administracyjnej obejmuje obsługę i usuwanie tożsamości użytkowników w miarę zmiany stanu lub ról. Typowe scenariusze obejmują inicjowanie obsługi administracyjnej użytkownika usługi Azure AD do aplikacji, takich jak [Dropbox,](../saas-apps/dropboxforbusiness-provisioning-tutorial.md) [Salesforce,](../saas-apps/salesforce-provisioning-tutorial.md) [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)i innych.

![Diagram przeglądu inicjowania obsługi administracyjnej](./media/user-provisioning/provisioning-overview.png)

Ta funkcja umożliwia:

- **Automatyzacja inicjowania obsługi administracyjnej:** automatycznie twórz nowe konta w odpowiednich systemach dla nowych osób, gdy dołączą do twojego zespołu lub organizacji.
- **Automatyzacja anulowania obsługi administracyjnej:** Automatycznie dezaktywuj konta w odpowiednich systemach, gdy użytkownicy opuszczają zespół lub organizację.
- **Synchronizowanie danych między systemami:** Upewnij się, że tożsamości w aplikacjach i systemach są aktualizowane na podstawie zmian w katalogu lub systemie zasobów ludzkich.
- **Grupy rezerw:** Aprowizuj grupy aplikacji, które je obsługują.
- **Zarządzanie dostępem:** Monitoruj i przeprowadzaj inspekcję, która została aprowizowana w aplikacjach.
- **Bezproblemowe wdrażanie w scenariuszach pól brązowych:** Dopasuj istniejące tożsamości między systemami i umożliwiają łatwą integrację, nawet jeśli użytkownicy już istnieją w systemie docelowym.
- **Użyj zaawansowanego dostosowywania:** Skorzystaj z konfigurowalnych mapowań atrybutów, które definiują, jakie dane użytkownika powinny przepływać z systemu źródłowego do systemu docelowego.
- **Otrzymuj alerty o zdarzeniach krytycznych:** Usługa inicjowania obsługi administracyjnej udostępnia alerty dotyczące zdarzeń krytycznych i umożliwia integrację usługi Log Analytics, w której można definiować niestandardowe alerty w celu dostosowania potrzeb biznesowych.

## <a name="benefits-of-automatic-provisioning"></a>Korzyści z automatycznego tworzenia rezerw

Ponieważ liczba aplikacji używanych w nowoczesnych organizacjach stale rośnie, administratorzy IT mają za zadanie zarządzanie dostępem na dużą skalę. Standardy, takie jak Język znaczników zabezpieczeń (SAML) lub Open ID Connect (OIDC) umożliwiają administratorom szybkie konfigurowanie logowania jednokrotnego,ale dostęp wymaga również, aby użytkownicy mogli być aprowizowani w aplikacji. Dla wielu administratorów inicjowanie obsługi administracyjnej oznacza ręczne tworzenie każdego konta użytkownika lub przekazywanie plików CSV co tydzień, ale te procesy są czasochłonne, kosztowne i podatne na błędy. Rozwiązania takie jak SAML just-in-time (JIT) zostały przyjęte w celu automatyzacji inicjowania obsługi administracyjnej, ale przedsiębiorstwa potrzebują również rozwiązania, aby anulować udostępnianie użytkownikom, gdy opuszczają organizację lub nie wymagają już dostępu do niektórych aplikacji na podstawie zmiany roli.

Niektóre typowe motywacje do korzystania z automatycznego inicjowania obsługi administracyjnej obejmują:

- Maksymalizacja wydajności i dokładności procesów inicjowania obsługi administracyjnej.
- Oszczędność na kosztach związanych z hostingiem i utrzymywaniem niestandardowych rozwiązań i skryptów inicjowania obsługi administracyjnej.
- Zabezpieczanie organizacji przez natychmiastowe usuwanie tożsamości użytkowników z kluczowych aplikacji SaaS po opuszczeniu organizacji.
- Łatwe importowanie dużej liczby użytkowników do określonej aplikacji lub systemu SaaS.
- Posiadanie jednego zestawu zasad w celu określenia, kto jest aprowizowany i kto może zalogować się do aplikacji.

Inicjowanie obsługi administracyjnej użytkowników usługi Azure AD może pomóc w rozwiązaniu tych problemów. Aby dowiedzieć się więcej o tym, jak klienci korzystali z inicjowania obsługi administracyjnej użytkowników usługi Azure AD, możesz przeczytać [studium przypadku asosu](https://aka.ms/asoscasestudy). Poniższy klip wideo zawiera omówienie inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Jakich aplikacji i systemów można używać z automatyczną inicjowania obsługi administracyjnej usługi Azure AD?

Usługa Azure AD oferuje wstępnie zintegrowaną obsługę wielu popularnych aplikacji SaaS i systemów zasobów ludzkich oraz ogólną obsługę aplikacji, które implementują określone części [standardu SCIM 2.0.](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)

* **Wstępnie zintegrowane aplikacje (galeria aplikacji SaaS)**. Na [liście samouczków aplikacji do inicjowania obsługi administracyjnej przez użytkowników](../saas-apps/tutorial-list.md)można znaleźć wszystkie aplikacje, dla których usługa Azure AD obsługuje wstępnie zintegrowany łącznik inicjowania obsługi administracyjnej. Wstępnie zintegrowane aplikacje wymienione w galerii zazwyczaj używają interfejsów API zarządzania użytkownikami opartych na 2.0 do inicjowania obsługi administracyjnej. 

   ![Logo Salesforce](./media/user-provisioning/gallery-app-logos.png)

   Jeśli chcesz poprosić o nową aplikację do inicjowania obsługi administracyjnej, możesz [poprosić o zintegrowanie aplikacji z naszą galerią aplikacji.](../develop/howto-app-gallery-listing.md) W przypadku żądania inicjowania obsługi administracyjnej użytkownika wymagamy, aby aplikacja miała punkt końcowy zgodny ze standardem SCIM. Prosimy o podanie, aby dostawca aplikacji przestrzegał standardu SCIM, abyśmy mogli szybko dołączyć aplikację do naszej platformy.

* **Aplikacje obsługujące scim 2.0**. Aby uzyskać informacje na temat ogólnego łączenia aplikacji implementujących interfejsy API zarządzania użytkownikami oparte na technologii SCIM 2.0, zobacz [Tworzenie punktu końcowego scim i konfigurowanie inicjowania obsługi administracyjnej przez użytkowników](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Co to jest system zarządzania tożsamościami między domenami ??'s System for Cross-domain Identity Management (SCIM)?

Aby ułatwić automatyzację inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej, aplikacje udostępniają zastrzeżone interfejsy API użytkowników i grup. Jednak każdy, kto próbował zarządzać użytkownikami w więcej niż jednej aplikacji, powie, że każda aplikacja próbuje wykonać te same proste akcje, takie jak tworzenie lub aktualizowanie użytkowników, dodawanie użytkowników do grup lub anulowanie obsługi administracyjnej użytkowników. Jednak wszystkie te proste akcje są implementowane tylko trochę inaczej, przy użyciu różnych ścieżek punktu końcowego, różne metody, aby określić informacje o użytkowniku i inny schemat do reprezentowania każdego elementu informacji.

Aby sprostać tym wyzwaniom, specyfikacja SCIM zawiera wspólny schemat użytkownika, aby ułatwić użytkownikom przejście do aplikacji, z nich i wokół nich. Scim staje się de facto standardem inicjowania obsługi administracyjnej i, gdy jest używany w połączeniu ze standardami federacji, takimi jak SAML lub OpenID Connect, zapewnia administratorom kompleksowe rozwiązanie oparte na standardach do zarządzania dostępem.

Aby uzyskać szczegółowe wskazówki dotyczące tworzenia punktu końcowego SCIM w celu zautomatyzowania inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej użytkowników i grup do aplikacji, zobacz [Tworzenie punktu końcowego SCIM i konfigurowanie inicjowania obsługi administracyjnej użytkowników](use-scim-to-provision-users-and-groups.md). W przypadku wstępnie zintegrowanych aplikacji w galerii (Slack, Azure Databricks, Snowflake itp.) można pominąć dokumentację dewelopera i skorzystać z samouczków podanych [tutaj](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Aprowizowanie automatyczne a ręczne

Aplikacje w galerii usługi Azure AD obsługują jeden z dwóch trybów inicjowania obsługi administracyjnej:

* **Ręczne** inicjowanie obsługi administracyjnej oznacza, że nie ma jeszcze żadnego łącznika aprowizacji usługi Azure AD dla aplikacji. Konta użytkowników muszą być tworzone ręcznie, na przykład przez dodanie użytkowników bezpośrednio do portalu administracyjnego aplikacji lub przesłanie arkusza kalkulacyjnego ze szczegółami konta użytkownika. Zapoznaj się z dokumentacją dostarczoną przez aplikację lub skontaktuj się z deweloperem aplikacji, aby ustalić, jakie mechanizmy są dostępne.

* **Automatyczne** oznacza, że łącznik inicjowania obsługi administracyjnej usługi Azure AD został opracowany dla tej aplikacji. Należy wykonać samouczek konfiguracji specyficzne dla konfigurowania inicjowania obsługi administracyjnej dla aplikacji. Samouczki dotyczące aplikacji można znaleźć na [liście samouczków dotyczących integracji aplikacji SaaS z usługą Azure Active Directory](../saas-apps/tutorial-list.md).

W galerii usługi Azure AD aplikacje obsługujące automatyczne inicjowanie administracyjne są oznaczone przez ikonę **inicjowania obsługi administracyjnej.** Przełącz się do nowej galerii podglądu, aby zobaczyć te ikony (w banerze w górnej części **strony Dodaj aplikację**, wybierz link, który mówi Kliknij **tutaj, aby wypróbować nową i ulepszoną galerię aplikacji**).

![Ikona inicjowania obsługi administracyjnej w galerii aplikacji](./media/user-provisioning/browse-gallery.png)

Tryb inicjowania obsługi administracyjnej obsługiwany przez aplikację jest również widoczny na karcie **Inicjowanie obsługi administracyjnej** po dodaniu aplikacji do **aplikacji przedsiębiorstwa.**

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Jak skonfigurować automatyczne inicjowanie obsługi administracyjnej aplikacji?

W przypadku wstępnie zintegrowanych aplikacji wymienionych w galerii dostępne są wskazówki krok po kroku dotyczące konfigurowania automatycznego inicjowania obsługi administracyjnej. Zobacz [listę samouczków dla zintegrowanych aplikacji galerii](../saas-apps/tutorial-list.md). W poniższym klipie wideo pokazano, jak skonfigurować automatyczne inicjowanie obsługi administracyjnej dla SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

W przypadku innych aplikacji obsługujących scim 2.0 wykonaj kroki opisane w [artykule Tworzenie punktu końcowego SCIM i konfigurowanie inicjowania obsługi administracyjnej użytkowników](use-scim-to-provision-users-and-groups.md).


## <a name="related-articles"></a>Pokrewne artykuły:

- [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
- [Dostosowywanie mapowań atrybutów do inicjowania obsługi administracyjnej przez użytkowników](customize-application-attributes.md)
- [Pisanie wyrażeń dla mapowań atrybutów](../app-provisioning/functions-for-customizing-application-data.md)
- [Filtry zakresu obsługi administracyjnej użytkowników](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Tworzenie punktu końcowego scim i konfigurowanie inicjowania obsługi administracyjnej użytkowników](use-scim-to-provision-users-and-groups.md)
- [Interfejs API synchronizacji usługi Azure AD — omówienie](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
