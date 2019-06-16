---
title: Jak skonfigurować aplikację serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć i skonfigurować aplikację serwera Proxy aplikacji w kilku prostych krokach
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
ms.openlocfilehash: fc9287aac567c8989564094564b92b82662e603f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825929"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Jak skonfigurować aplikację serwera Proxy aplikacji

Ten artykuł pomaga zrozumieć, jak skonfigurować aplikację serwera Proxy aplikacji w usłudze Azure AD do udostępnienia aplikacji lokalnych do chmury.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby dowiedzieć się więcej o konfiguracji początkowej i tworzenie aplikacji serwera Proxy aplikacji za pośrednictwem portalu administracyjnego, postępuj zgodnie z [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md).

Aby uzyskać więcej informacji na temat konfigurowania łączników, zobacz [Włączanie serwera Proxy aplikacji w witrynie Azure portal](application-proxy-add-on-premises-application.md).

Aby uzyskać informacji na temat przekazywania certyfikatów i używania domen niestandardowych, zobacz [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Tworzenie aplikacji/ustawienia adresów URL

Jeśli postępujesz zgodnie z instrukcjami w [publikowania aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md) dokumentacji i czy występuje błąd tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu rozwiązania aplikacja. Większość komunikaty o błędach obejmują sugerowanej poprawki. Aby uniknąć typowych błędów, należy zweryfikować:

-   Administratorzy z uprawnieniami do tworzenia aplikacji serwera Proxy aplikacji

-   Wewnętrzny adres URL jest unikatowa

-   Zewnętrzny adres URL jest unikatowa

-   Adresy URL, uruchom za pomocą protokołu http lub https, a kończyć się znakiem "/"

-   Adres URL powinien być nazwy domeny, a nie adresu IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu, podczas tworzenia aplikacji. Można również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurowanie grup łączników/łącznika

Jeśli masz problem z konfiguracją aplikacji z powodu ostrzeżenia dotyczące łączników i grupy łączników, zobacz instrukcje na temat włączania serwera Proxy aplikacji, aby uzyskać szczegółowe informacje dotyczące pobierania łączników. Jeśli chcesz dowiedzieć się więcej na temat łączników, zobacz [dokumentacji łączników](application-proxy-connectors.md).

Jeśli łączniki nie są aktywne, oznacza to, że są w stanie uzyskać dostęp do usługi. Jest to często, ponieważ nie wszystkie wymagane porty są otwarte. Aby wyświetlić listę wymaganych portów, zobacz sekcję wymagania wstępne Włączanie dokumentacji serwera Proxy aplikacji.

## <a name="upload-certificates-for-custom-domains"></a>Przekaż certyfikaty dla domen niestandardowych

Domen niestandardowych umożliwiają określenie domeny usługi zewnętrzne adresy URL. Aby Użyj domen niestandardowych, musisz przekazać certyfikat dla tej domeny. Aby uzyskać informacje na temat używania domen niestandardowych i certyfikatów, zobacz [Praca z domenami niestandardowymi na serwerze Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md). 

Jeśli napotkasz problemy z przekazywania certyfikatu poszukaj komunikatów o błędach w portalu, aby uzyskać dodatkowe informacje na temat problemu z certyfikatem. Typowe problemy z certyfikatem obejmują:

-   Wygaśnięcie certyfikatu

-   Certyfikat ma podpis własny

-   Brak klucza prywatnego certyfikatu

Komunikat o błędzie jest wyświetlane w prawym górnym rogu, przy próbie przekazania certyfikatu. Można również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](application-proxy-add-on-premises-application.md)
