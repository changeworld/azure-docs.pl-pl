---
title: Jak skonfigurować aplikację proxy aplikacji | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować aplikację APplication Proxy w kilku prostych krokach
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
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807857"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Jak skonfigurować aplikację serwera proxy aplikacji

Ten artykuł pomaga zrozumieć, jak skonfigurować aplikację serwera proxy aplikacji w usłudze Azure AD, aby udostępnić aplikacje lokalne w chmurze.

## <a name="recommended-documents"></a>Zalecane dokumenty

Aby dowiedzieć się więcej o początkowych konfiguracjach i tworzeniu aplikacji serwera proxy aplikacji za pośrednictwem portalu administracyjnego, postępuj zgodnie z [publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

Aby uzyskać szczegółowe informacje na temat konfigurowania łączników, zobacz [Włączanie serwera proxy aplikacji w witrynie Azure portal](application-proxy-add-on-premises-application.md).

Aby uzyskać informacje dotyczące przekazywania certyfikatów i używania domen niestandardowych, zobacz [Praca z domenami niestandardowymi w serwerze proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Tworzenie aplikacji/ustawianie adresów URL

Jeśli wykonujesz kroki opisane w [dokumentacji publikowania aplikacji przy użyciu](application-proxy-add-on-premises-application.md) usługi Azure AD Application Proxy i otrzymujesz błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu naprawy aplikacji. Większość komunikatów o błędach zawiera sugerowaną poprawkę. Aby uniknąć typowych błędów, sprawdź:

- Jesteś administratorem z uprawnieniami do tworzenia aplikacji proxy aplikacji
- Wewnętrzny adres URL jest unikatowy
- Zewnętrzny adres URL jest unikatowy
- Adresy URL zaczynają się od http lub https, a kończą na "/"
- Adres URL powinien być nazwą domeny, a nie adresem IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Można również wybrać ikonę powiadomień, aby wyświetlić komunikaty o błędach.

![Pokazuje, gdzie można znaleźć monit o powiadomienie w witrynie Azure portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurowanie łączników/grup łączników

Jeśli masz trudności z skonfigurowaniem aplikacji z powodu ostrzeżenia o łącznikach i grupach łączników, zobacz instrukcje dotyczące włączania serwera proxy aplikacji, aby uzyskać szczegółowe informacje na temat pobierania łączników. Aby dowiedzieć się więcej o łącznikach, zapoznaj się z [dokumentacją łączników](application-proxy-connectors.md).

Jeśli łączniki są nieaktywne, oznacza to, że nie mogą dotrzeć do usługi. Jest to często, ponieważ wszystkie wymagane porty nie są otwarte. Aby wyświetlić listę wymaganych portów, zobacz sekcję wymagań wstępnych dokumentacji włączania serwera proxy aplikacji.

## <a name="upload-certificates-for-custom-domains"></a>Przekazywanie certyfikatów dla domen niestandardowych

Domeny niestandardowe umożliwiają określenie domeny zewnętrznych adresów URL. Aby korzystać z domen niestandardowych, musisz przekazać certyfikat dla tej domeny. Aby uzyskać informacje dotyczące korzystania z domen niestandardowych i certyfikatów, zobacz [Praca z domenami niestandardowymi w usłudze Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

Jeśli występują problemy z przekazywaniem certyfikatu, poszukaj komunikatów o błędach w portalu, aby uzyskać dodatkowe informacje na temat problemu z certyfikatem. Typowe problemy z certyfikatami obejmują:

- Wygasły certyfikat
- Certyfikat jest podpisany samodzielnie
- Brakuje certyfikatu klucza prywatnego

Komunikat o błędzie wyświetlany w prawym górnym rogu podczas próby przekazania certyfikatu. Można również wybrać ikonę powiadomień, aby wyświetlić komunikaty o błędach.

## <a name="next-steps"></a>Następne kroki

[Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
