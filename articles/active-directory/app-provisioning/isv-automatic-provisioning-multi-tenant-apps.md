---
title: Włączanie automatycznego inicjowania obsługi administracyjnej dla aplikacji z wieloma dzierżawami — usługa Azure AD
description: Przewodnik dla niezależnych dostawców oprogramowania dotyczący włączania automatycznego inicjowania obsługi administracyjnej
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522397"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Włączanie automatycznego inicjowania obsługi administracyjnej aplikacji wielodostępowej

Automatyczne inicjowanie obsługi administracyjnej użytkowników to proces automatyzacji tworzenia, konserwacji i usuwania tożsamości użytkowników w systemach docelowych, takich jak aplikacje typu software-as-a-service.

## <a name="why-enable-automatic-user-provisioning"></a>Dlaczego warto włączyć automatyczne inicjowanie obsługi administracyjnej użytkowników?

Aplikacje, które wymagają, aby rekord użytkownika był obecny w aplikacji przed pierwszym zalogowaniem się użytkownika wymagają inicjowania obsługi administracyjnej przez użytkownika. Istnieją korzyści dla Ciebie jako usługodawcy i korzyści dla twoich klientów.

### <a name="benefits-to-you-as-the-service-provider"></a>Korzyści dla Ciebie jako usługodawcy

* Zwiększ bezpieczeństwo aplikacji za pomocą platformy tożsamości firmy Microsoft.

* Zmniejsz rzeczywisty i postrzegany wysiłek klienta, aby przyjąć aplikację.

* Zmniejsz koszty integracji z wieloma dostawcami tożsamości (IDPs) w celu automatycznego inicjowania obsługi administracyjnej użytkowników przy użyciu inicjowania obsługi administracyjnej opartej na systemie zarządzania tożsamościami między domenami (SCIM).

* Zmniejsz koszty pomocy technicznej, udostępniając zaawansowane dzienniki, aby pomóc klientom w rozwiązywaniu problemów z inicjowaniem obsługi administracyjnej użytkowników.

* Zwiększ widoczność aplikacji w [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps).

* Uzyskaj priorytetową listę na stronie Samouczki aplikacji.

### <a name="benefits-to-your-customers"></a>Korzyści dla twoich klientów

* Zwiększ bezpieczeństwo, automatycznie usuwając dostęp do aplikacji dla użytkowników, którzy zmieniają role lub pozostawiają organizację aplikacji.

* Uprość zarządzanie użytkownikami aplikacji, unikając błędów ludzkich i powtarzających się prac związanych z ręczną inicjowania obsługi administracyjnej.

* Zmniejsz koszty hostingu i utrzymania niestandardowych rozwiązań aprowizacji.

## <a name="choose-a-provisioning-method"></a>Wybór metody aprowizacji

Usługa Azure AD udostępnia kilka ścieżek integracji, aby włączyć automatyczne inicjowanie obsługi administracyjnej dla aplikacji.

* [Usługa inicjowania obsługi administracyjnej usługi Azure AD](../app-provisioning/user-provisioning.md) zarządza inicjowania obsługi administracyjnej i deprovisioning użytkowników z usługi Azure AD do aplikacji (inicjowania obsługi administracyjnej ruchu wychodzącego) i z aplikacji do usługi Azure AD (inicjowania obsługi przychodzącej). Usługa łączy się z punktami końcowymi interfejsu API zarządzania tożsamościami między domenami (SYSTEM dla wielu domen) udostępnianych przez aplikację.

* Podczas korzystania z [programu Microsoft Graph](https://docs.microsoft.com/graph/)aplikacja zarządza inicjowania obsługi administracyjnej użytkowników i grup wychodzących użytkowników i grup z usługi Azure AD do aplikacji przez kwerendy interfejsu API programu Microsoft Graph.

* Inicjowanie obsługi administracyjnej użytkownika just in time (SAML JIT) oświadczeń o zabezpieczeniach można włączyć, jeśli aplikacja używa SAML dla federacji. Używa informacji o oświadczeniach wysyłanych w tokenie SAML do inicjowania obsługi administracyjnej użytkowników.

Aby ułatwić określenie, która opcja integracji ma być używana dla aplikacji, zapoznaj się z tabelą porównawczą wysokiego poziomu, a następnie zobacz bardziej szczegółowe informacje na temat każdej opcji.

| Możliwości włączone lub ulepszone przez automatyczne inicjowanie obsługi administracyjnej| Usługa inicjowania obsługi administracyjnej usługi Azure AD (SCIM 2.0)| Interfejs API programu Microsoft Graph (OData v4.0)| SAML JIT |
|---|---|---|---|
| Zarządzanie użytkownikami i grupami w usłudze Azure AD| √| √| Tylko użytkownik |
| Zarządzanie użytkownikami i grupami zsynchronizowanymi z lokalnej usługi Active Directory| √*| √*| Tylko użytkownik* |
| Dostęp do danych poza użytkownikami i grupami podczas inicjowania obsługi administracyjnej danych programu Access to O365 (zespoły, program SharePoint, poczta e-mail, kalendarz, dokumenty itp.)| X+| √| X |
| Tworzenie, odczytywanie i aktualizowanie użytkowników na podstawie reguł biznesowych| √| √| √ |
| Usuwanie użytkowników na podstawie reguł biznesowych| √| √| X |
| Zarządzanie automatyczną inicjowanianiem obsługi administracyjnej dla wszystkich aplikacji z witryny Azure Portal| √| X| √ |
| Obsługa wielu dostawców tożsamości| √| X| √ |
| Konta gości pomocy technicznej (B2B)| √| √| √ |
| Obsługa kont innych niż przedsiębiorstwa (B2C)| X| √| √ |

<sup>*</sup>— Konfiguracja usługi Azure AD Connect jest wymagana do synchronizacji użytkowników z usługi AD na usługę Azure AD.  
<sup>+</sup >– Korzystanie scim do inicjowania obsługi administracyjnej nie wyklucza integracji aplikacji z MIcrosoft Graph do innych celów.

## <a name="azure-ad-provisioning-service-scim"></a>Usługa inicjowania obsługi administracyjnej usługi Azure AD (SCIM)

Usługi inicjowania obsługi administracyjnej usługi Azure AD korzystają z [usługi SCIM](https://aka.ms/SCIMOverview), branżowego standardu obsługi administracyjnej obsługiwanego przez wielu dostawców tożsamości (IDPs), a także aplikacji (np. Zaleca się korzystanie z usługi inicjowania obsługi administracyjnej usługi Azure AD, jeśli chcesz obsługiwać dostawcy identyfikatorów oprócz usługi Azure AD, ponieważ każdy identyfikator zgodny ze standardem SCIM może łączyć się z punktem końcowym scim. Tworzenie punktu końcowego proste /User, można włączyć inicjowania obsługi administracyjnej bez konieczności utrzymywania własnego aparatu synchronizacji. 

Aby uzyskać więcej informacji na temat sposobu użytkowników usługi Azure AD inicjowania obsługi administracyjnej SCIM, zobacz: 

* [Dowiedz się więcej o standardzie SCIM](https://aka.ms/SCIMOverview)

* [Używanie funkcji System for Cross-Domain Identity Management (SCIM) do automatycznego udostępniania użytkownikom i grupom z usługi Azure Active Directory do aplikacji](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Opis implementacji scim usługi Azure AD](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Program Microsoft Graph do inicjowania obsługi administracyjnej

Korzystając z programu Microsoft Graph do inicjowania obsługi administracyjnej, masz dostęp do wszystkich bogatych danych użytkownika dostępnych w programie Graph. Oprócz szczegółów dotyczących użytkowników i grup można również pobierać dodatkowe informacje, takie jak role użytkownika, raporty menedżerskie i bezpośrednie, urządzenia będące własnością i zarejestrowane oraz setki innych fragmentów danych dostępnych w [programie Microsoft Graph.](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) 

Ponad 15 milionów organizacji i 90% firm z listy Fortune 500 korzysta z usługi Azure AD, subskrybując usługi w chmurze firmy Microsoft, takie jak Office 365, Microsoft Azure, Enterprise Mobility Suite lub Microsoft 365. Za pomocą programu Microsoft Graph można zintegrować aplikację z administracyjnymi przepływami pracy, takimi jak dołączanie do systemu roboczego (i zakończenie) pracowników, konserwacja profilu i inne. 

Dowiedz się więcej o korzystaniu z programu Microsoft Graph do inicjowania obsługi administracyjnej:

* [Strona główna programu Microsoft Graph](https://developer.microsoft.com/graph)

* [Overview of Microsoft Graph](https://docs.microsoft.com/graph/overview) (Omówienie programu Microsoft Graph)

* [Omówienie auth wykresu firmy Microsoft](https://docs.microsoft.com/graph/auth/)

* [Wprowadzenie do programu Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Używanie SAML JIT do inicjowania obsługi administracyjnej

Jeśli chcesz aprowizować użytkowników tylko przy pierwszym zalogowaniu się do aplikacji i nie trzeba automatycznie deprovision użytkowników, SAML JIT jest opcją. Aplikacja musi obsługiwać SAML 2.0 jako protokół federacji do korzystania z SAML JIT.

SAML JIT używa informacji o oświadczeniach w tokenie SAML do tworzenia i aktualizowania informacji o użytkowniku w aplikacji. Klienci mogą skonfigurować te wymagane oświadczenia w aplikacji usługi Azure AD w razie potrzeby. Czasami inicjowanie obsługi administracyjnej JIT musi być włączone od strony aplikacji, aby klient mógł korzystać z tej funkcji. SAML JIT jest przydatne do tworzenia i aktualizowania użytkowników, ale nie można usunąć lub dezaktywować użytkowników w aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Włączanie logowania jednokrotnego dla aplikacji](../manage-apps/isv-sso-content.md)

* [Prześlij listę aplikacji](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) i skontaktuj się z firmą Microsoft, aby utworzyć dokumentację w witrynie firmy Microsoft.

* [Dołącz do sieci partnerów firmy Microsoft (bezpłatnie) i utwórz plan rynkowy.](https://partner.microsoft.com/en-us/explore/commercial)
