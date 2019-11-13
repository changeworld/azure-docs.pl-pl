---
title: Informacje o najlepszych rozwiązaniach dotyczących zabezpieczeń — Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Poznaj najlepsze rozwiązania w zakresie zabezpieczeń dotyczące usługi Azure Digital bliźniaczych reprezentacji i Internet rzeczy.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9f3f98863f9a7cd0e6328ddc75b1154ee933fe0b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009255"
---
# <a name="azure-digital-twins-security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń usługi Azure Digital bliźniaczych reprezentacji

Usługa Azure Digital bliźniaczych reprezentacji Security umożliwia precyzyjne dostęp do określonych zasobów i akcji w grafie IoT. Odbywa się to za pośrednictwem szczegółowego zarządzania rolami i uprawnieniami o nazwie [Kontrola dostępu oparta na rolach](./security-role-based-access-control.md).

Usługa Azure Digital bliźniaczych reprezentacji korzysta również z innych funkcji zabezpieczeń, które są obecne w usłudze Azure IoT, w tym Azure Active Directory (Azure AD). Z tego powodu Konfigurowanie i Zabezpieczanie aplikacji opartych na usłudze Azure Digital bliźniaczych reprezentacji obejmuje użycie wielu z tych samych obecnie zalecanych praktyk w zakresie [zabezpieczeń Azure IoT](../iot-fundamentals/iot-security-best-practices.md) .

W tym artykule przedstawiono podsumowanie najważniejszych wskazówek dotyczących najważniejszych rozwiązań.

> [!IMPORTANT]
> Aby zapewnić maksymalne zabezpieczenia obszaru IoT, zapoznaj się z dodatkowymi zasobami zabezpieczeń. Upewnij się, że dołączasz dostawców urządzeń.

> [!TIP]
> Użyj [Azure Security Center dla IoT](https://docs.microsoft.com/azure/asc-for-iot/) , aby pomóc wykryć zagrożenia i luki w zabezpieczeniach IoT.

## <a name="iot-security-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń IoT

Niektóre kluczowe metody bezpiecznego zabezpieczania urządzeń IoT obejmują:

> [!div class="checklist"]
> * Zabezpiecz każde urządzenie, które jest połączone z obszarem IoT w sposób zapewniający możliwość manipulacji.
> * Ogranicz rolę każdego urządzenia, czujnika i osoby w obszarze IoT. W przypadku naruszenia zabezpieczeń efekt jest zminimalizowany.
> * Należy wziąć pod uwagę potencjalne użycie filtrowania adresów IP urządzeń i ograniczeń portów.
> * Ogranicz przepustowość operacji we/wy i urządzenia, aby zwiększyć wydajność. Ograniczanie szybkości może zwiększyć bezpieczeństwo, zapobiegając atakom typu "odmowa usługi".
> * Utrzymuj aktualność oprogramowania układowego urządzenia.
> * Okresowe inspekcje i przeglądanie najlepszych rozwiązań w zakresie zabezpieczeń urządzeń, sieci i bram w miarę ich dalszego ulepszania i rozwoju.

Oto kilka najważniejszych metod bezpiecznego zabezpieczania przestrzeni IoT:

> [!div class="checklist"]
> * Szyfruj zapisane, przechowywane lub trwałe dane.
> * Wymagaj, aby hasła lub klucze były okresowo zmieniane lub odświeżane.
> * Należy uważnie ograniczyć dostęp i uprawnienia według roli. Zobacz sekcję dotyczącą [kontroli dostępu opartej na rolach](#role-based-access-control-best-practices) poniżej.
> * Rozważ podzieloną topologię sieci, dzięki czemu urządzenia w każdej sieci są odizolowane od innych.
> * Użyj zaawansowanego szyfrowania. Wymagaj długich haseł i używaj protokołów Secure i uwierzytelniania dwuskładnikowego.

[Monitoruj](./how-to-configure-monitoring.md) Zasoby IoT do śledzenia wartości odstających, zagrożeń lub parametrów zasobów, które wykraczają poza zakres zwykłych operacji. Zarządzanie monitorowaniem za pomocą usługi Azure Analytics.

> [!NOTE]
> Aby uzyskać więcej informacji o przetwarzaniu i monitorowaniu zdarzeń, zobacz temat [kierowanie zdarzeń i komunikatów za pomocą usługi Azure Digital bliźniaczych reprezentacji](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory najlepszych praktyk

Usługa Azure Digital bliźniaczych reprezentacji używa Azure Active Directory do uwierzytelniania użytkowników i ochrony aplikacji. Azure Active Directory obsługuje uwierzytelnianie w różnych nowoczesnych architekturach. Są one oparte na standardach branżowych, takich jak OAuth 2,0 lub OpenID Connect Connect. Oto kilka najważniejszych metod zabezpieczania przestrzeni IoT dla Azure Active Directory obejmuje:

> [!div class="checklist"]
> * Przechowuj Azure Active Directory wpisy tajne i klucze w bezpiecznej lokalizacji, takie jak [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Użyj certyfikatu wystawionego przez zaufany [urząd certyfikacji](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) zamiast kluczy tajnych aplikacji do uwierzytelnienia.
> * Ogranicz zakres dostępu OAuth 2,0 dla tokenu.
> * Sprawdź, jak długo token jest prawidłowy i czy token pozostaje ważny.
> * Ustaw odpowiednie długości ważności tokenów.
> * Odśwież wygasłe tokeny.

## <a name="role-based-access-control-best-practices"></a>Najlepsze rozwiązania dotyczące kontroli dostępu opartej na rolach

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących usługi Azure IoT, zobacz [najlepsze rozwiązania dotyczące zabezpieczeń IoT](../iot-fundamentals/iot-security-best-practices.md).

* Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach, należy przeczytać temat [Kontrola dostępu oparta na rolach](./security-role-based-access-control.md).

* Aby dowiedzieć się więcej na temat uwierzytelniania, przeczytaj artykuł [uwierzytelnianie za pomocą interfejsów API](./security-authenticating-apis.md).