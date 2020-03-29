---
title: Poznaj najważniejsze wskazówki dotyczące zabezpieczeń — usługi Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń dla usługi Azure Digital Twins i Internetu rzeczy.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5fc5ba447557aa89e8f0870c576d6d4c439f3353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122563"
---
# <a name="azure-digital-twins-security-best-practices"></a>Najważniejsze wskazówki dotyczące zabezpieczeń usługi Azure Digital Twins

Zabezpieczenia usługi Azure Digital Twins umożliwiają precyzyjny dostęp do określonych zasobów i akcji na wykresie IoT. Czyni to za pomocą szczegółowej roli i zarządzania uprawnieniami o nazwie [kontrola dostępu oparta na rolach.](./security-role-based-access-control.md)

Usługa Azure Digital Twins używa również innych funkcji zabezpieczeń, które są obecne w usłudze Azure IoT, w tym usługi Azure Active Directory (Azure AD). Z tego powodu konfigurowanie i zabezpieczanie aplikacji utworzonych na podstawie usługi Azure Digital Twins wymaga użycia wielu tych [samych zalecanych obecnie rozwiązań zabezpieczeń Usługi Azure IoT.](../iot-fundamentals/iot-security-best-practices.md)

W tym artykule podsumowano najważniejsze najważniejsze wskazówki do wykonania.

> [!IMPORTANT]
> Aby zapewnić maksymalne bezpieczeństwo przestrzeni IoT, przejrzyj dodatkowe zasoby zabezpieczeń. Pamiętaj, aby uwzględnić dostawców urządzeń.

> [!TIP]
> Użyj [usługi Azure Security Center dla IoT,](https://docs.microsoft.com/azure/asc-for-iot/) aby pomóc w wykrywaniu zagrożeń bezpieczeństwa I luk w zabezpieczeniach IoT.

## <a name="iot-security-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń IoT

Oto kilka kluczowych rozwiązań zapewniających bezpieczne bezpieczeństwo urządzeń IoT:

> [!div class="checklist"]
> * Zabezpiecz każde urządzenie podłączone do przestrzeni IoT w sposób odporny na manipulacje.
> * Ogranicz rolę każdego urządzenia, czujnika i osoby w przestrzeni IoT. Jeśli zostanie naruszony, efekt zostanie zminimalizowany.
> * Należy wziąć pod uwagę potencjalne użycie filtrowania adresów IP urządzenia i ograniczenia portu.
> * Ogranicz przepustowość we/wy i przepustowość urządzenia, aby zwiększyć wydajność. Ograniczenie szybkości może poprawić bezpieczeństwo, zapobiegając atakom typu "odmowa usługi".
> * Dbaj o aktualną aktualizacje oprogramowania układowego urządzenia, systemu operacyjnego i oprogramowania.
> * Okresowo przeprowadzaj inspekcje i recenzuj najlepsze rozwiązania dotyczące zabezpieczeń urządzeń, oprogramowania, sieci i bramy w miarę ich ulepszania i ewoluowania.
> * Używaj zaufanych, certyfikowanych i zgodnych systemów zabezpieczeń, oprogramowania i urządzeń. Na przykład przejrzyj [oferty zgodności](https://azure.microsoft.com/overview/trusted-cloud/compliance/) dla usługi Azure Cloud.

Oto kilka kluczowych praktyk zapewniających bezpieczne zabezpieczenie przestrzeni IoT:

> [!div class="checklist"]
> * Szyfruj zapisane, przechowywane lub trwałe dane.
> * Wymagaj okresowej zmiany lub odświeżania haseł lub kluczy.
> * Ostrożnie ogranicz dostęp i uprawnienia według roli. Przeczytaj sekcję [Najważniejsze wskazówki dotyczące kontroli dostępu oparte na rolach](#role-based-access-control-best-practices) poniżej.
> * Należy wziąć pod uwagę topologii sieci podzielonej, tak aby urządzenia w każdej sieci są odizolowane od innych.
> * Użyj zaawansowanego szyfrowania. Wymagaj długich haseł, zabezpieczaj protokoły i [uwierzytelnianie wieloskładnikowe.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Monitor](./how-to-configure-monitoring.md) Zasoby IoT do obserwowaniu wartości odstania, zagrożeń lub parametrów zasobów, które mieszczą się poza zakresem zwykłej operacji. Użyj usługi Azure Analytics do monitorowania zarządzania.

> [!IMPORTANT]
> Przeczytaj [najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure IoT,](../iot-fundamentals/iot-security-best-practices.md) aby rozpocząć kompleksową strategię zabezpieczeń IoT.

> [!NOTE]
> Aby uzyskać więcej informacji na temat przetwarzania i monitorowania zdarzeń, przeczytaj [temat Rozsyłania zdarzeń i wiadomości za pomocą usługi Azure Digital Twins](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Najważniejsze wskazówki dotyczące usługi Azure Active Directory

Usługa Azure Digital Twins używa [usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/) do uwierzytelniania użytkowników i ochrony aplikacji. Usługa Azure Active Directory obsługuje uwierzytelnianie dla różnych nowoczesnych architektur. Wszystkie są oparte na standardowych protokołach branżowych, takich jak OAuth 2.0 lub OpenID Connect. Kilka kluczowych rozwiązań w celu zabezpieczenia miejsca IoT dla usługi Azure Active Directory obejmują:

> [!div class="checklist"]
> * Przechowuj wpisy tajne i klucze aplikacji usługi Azure Active Directory w bezpiecznej lokalizacji, takiej jak [Usługa Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Do uwierzytelniania należy użyć certyfikatu wystawionego przez zaufany [urząd certyfikacji,](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) a nie wpisów tajnych aplikacji.
> * Ogranicz zakres dostępu OAuth 2.0 dla tokenu.
> * Sprawdź, przez co token jest prawidłowy i czy token pozostaje prawidłowy.
> * Ustaw odpowiednie długości czasu, dla których tokeny są prawidłowe. Odśwież wygasłe tokeny.
> * Usuń nieużywane **identyfikatory URI przekierowania** i uprawnienia według [najlepszych rozwiązań kontroli dostępu opartych na rolach.](#role-based-access-control-best-practices)

## <a name="role-based-access-control-best-practices"></a>Najważniejsze wskazówki dotyczące kontroli dostępu oparte na rolach

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o najlepszych praktykach dotyczących usługi Azure IoT, przeczytaj [najważniejsze wskazówki dotyczące zabezpieczeń IoT.](../iot-fundamentals/iot-security-best-practices.md)

* Aby dowiedzieć się więcej o kontroli dostępu opartej na rolach, przeczytaj artykuł [Kontrola dostępu oparta na rolach](./security-role-based-access-control.md).

* Aby dowiedzieć się więcej o uwierzytelnianiu, przeczytaj artykuł [Uwierzytelnianie przy użyciu interfejsów API](./security-authenticating-apis.md).
