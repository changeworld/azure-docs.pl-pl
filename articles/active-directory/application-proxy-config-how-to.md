---
title: Jak skonfigurować aplikację serwera Proxy aplikacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć Konfiguruj aplikację serwera Proxy aplikacji w kilku prostych krokach
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 047679b52de1b095112948e869f35811346b846b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331627"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Jak skonfigurować aplikację serwera Proxy aplikacji

Ten artykuł ułatwia zrozumienie, jak skonfigurować aplikację serwera Proxy aplikacji w usłudze Azure AD do udostępnienia aplikacji lokalnych do chmury.

## <a name="recommended-documents"></a>Zalecane dokumenty 

Aby uzyskać informacje dotyczące konfiguracji początkowej i tworzenia aplikacji serwera Proxy aplikacji za pośrednictwem portalu administratora, wykonaj [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md).

Aby uzyskać więcej informacji na temat konfigurowania łączników, zobacz [Włączanie serwera Proxy aplikacji w portalu Azure](manage-apps/application-proxy-enable.md).

Aby uzyskać informacje na przekazywanie certyfikatów i przy użyciu domen niestandardowych, zobacz [Praca z domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Tworzenie aplikacji/ustawienia adresów URL

Jeśli wykonujesz kroki opisane w [publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md) dokumentacji i czy wprowadzenie błąd podczas tworzenia aplikacji, zobacz szczegóły błędu, aby uzyskać informacje i sugestie dotyczące sposobu rozwiązania aplikacja. Większość komunikaty o błędach obejmują sugerowanej poprawki. Aby uniknąć typowych błędów, sprawdzić:

-   Administratorzy z uprawnieniami do tworzenia aplikacji serwera Proxy aplikacji

-   Wewnętrzny adres URL jest unikatowa

-   Zewnętrzny adres URL jest unikatowa

-   Adresy URL rozpoczynać się od http lub https i kończyć się "/"

-   Adres URL powinien być nazwą domeny, a nie adres IP

Komunikat o błędzie powinien być wyświetlany w prawym górnym rogu podczas tworzenia aplikacji. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurowanie grup łączników/łącznika

Jeśli masz trudności w konfigurowaniu aplikacji z powodu ostrzeżenie na temat łączników i grup łącznika, zobacz instrukcje na temat włączania szczegółowe informacje na temat sposobu pobierania łączników serwera Proxy aplikacji. Jeśli chcesz dowiedzieć się więcej na temat łączników, zobacz [dokumentacji łączniki](manage-apps/application-proxy-connectors.md).

Jeśli łączniki nie są aktywne, oznacza to, że są w stanie uzyskać dostęp do usługi. Jest to często spowodowane wymagane porty nie są otwarte. Aby wyświetlić listę wymaganych portów, zobacz sekcję wymagania wstępne włączenie dokumentacji serwera Proxy aplikacji.

## <a name="upload-certificates-for-custom-domains"></a>Przekaż certyfikaty dla domen niestandardowych

Domeny niestandardowe umożliwiają określenie domeny sieci zewnętrznych adresów URL. Aby korzystać z domeny niestandardowe, należy przekazać certyfikatu dla tej domeny. Aby uzyskać informacje na temat używania domeny niestandardowej i certyfikatów, zobacz [Praca z domenami niestandardowymi w serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-configure-custom-domain.md). 

Pojawiły się problemy, przekazywanie certyfikatu, poszukaj komunikatów o błędach w portalu, aby uzyskać dodatkowe informacje o problemie z certyfikatem. Typowe problemy z certyfikatem obejmują:

-   Wygaśnięcie certyfikatu

-   Certyfikat jest certyfikatem z podpisem

-   Certyfikat nie ma klucza prywatnego

Komunikat o błędzie jest wyświetlane w prawym górnym rogu przy próbie przekazania certyfikatu. Możesz również wybrać ikonę powiadomienia, aby wyświetlić komunikaty o błędach.

   ![Wiersz powiadomień](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Kolejne kroki
[Publikowanie aplikacji przy użyciu serwera Proxy aplikacji usługi Azure AD](manage-apps/application-proxy-publish-azure-portal.md)
