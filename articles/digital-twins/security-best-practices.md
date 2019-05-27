---
title: Omówienie najlepszych rozwiązań zabezpieczeń Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure cyfrowego Twins najlepszych rozwiązań dotyczących zabezpieczeń.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: v-adgera
ms.openlocfilehash: 1d7194beeac1f6f0034738c842e0fc3a58668a13
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966958"
---
# <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Bezpieczeństwa cyfrowego Twins platformy Azure umożliwia precyzyjne dostępu do określonych zasobów i akcji w grafie IoT. Robi to za pośrednictwem szczegółowe zarządzanie rolami i uprawnieniami o nazwie [kontroli dostępu opartej na rolach](./security-role-based-access-control.md).

Azure Twins cyfrowego używa także inne funkcje zabezpieczeń, które znajdują się w usłudze Azure IoT, w tym usługi Active Directory (Azure AD). Z tego powodu, konfigurowania i zabezpieczania aplikacji utworzonych na Twins cyfrowego Azure polega na użyciu wiele takich samych [rozwiązania w zakresie zabezpieczeń usługi Azure IoT](../iot-fundamentals/iot-security-best-practices.md) aktualnie zalecany.

Ten artykuł zawiera podsumowanie kluczowych najlepszych rozwiązań.

> [!IMPORTANT]
> Aby zapewnić maksymalny zabezpieczeń dla obszaru IoT, przejrzyj zasoby dodatkowe zabezpieczenia. Upewnij się uwzględnić dostawców urządzeń.

## <a name="iot-security-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń IoT

Niektóre rozwiązania kluczy bezpiecznie zabezpieczania urządzeń IoT należą:

> [!div class="checklist"]
> * Zabezpiecz każdego urządzenia, które jest podłączony do obszaru IoT w taki sposób, odporną na manipulacje.
> * Ogranicz roli poszczególnych urządzeń, czujników i osoby w Twojej przestrzeni IoT. W przypadku naruszenia zabezpieczeń jest zminimalizowany wpływ.
> * Rozważ użycie potencjalne urządzenia IP adresu, filtrowania i portu ograniczeń.
> * Ogranicz przepustowość operacji We/Wy i urządzeń w celu zwiększenia wydajności. Ograniczanie szybkości może zwiększyć bezpieczeństwo poprzez zapobieganie atakom typu "odmowa usługi".
> * Aktualizowanie oprogramowania układowego urządzenia.

Niektóre rozwiązania kluczy bezpiecznie zabezpieczyć miejsca IoT obejmują:

> [!div class="checklist"]
> * Szyfruj dane zapisane, przechowywane lub trwałych.
> * Wymagaj hasła lub kluczy w celu można zmienić ani okresowo odświeżane.
> * Dokładnie ograniczyć dostępu i uprawnień według roli. Zobacz sekcję [najlepszymi praktykami kontroli dostępu opartej na rolach](#rbac) poniżej.
> * Należy używać szyfrowania zaawansowane. Długie hasła są wymagane i bezpieczne protokoły i uwierzytelniania dwuskładnikowego.

[Monitor](./how-to-configure-monitoring.md) zasobów IoT, aby wyszukiwać elementy odstające, zagrożeń lub parametry zasobu, które wykraczają poza zakres zwykłych operacji. Monitorowanie zarządzania przy użyciu usługi analiza usługi Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zdarzeń przetwarzania oraz monitorowania, zobacz [kierowanie zdarzeń i komunikatów za pomocą Twins cyfrowego Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Najlepsze rozwiązania platformy Azure Active Directory

Azure Twins cyfrowego używa usługi Azure Active Directory do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure Active Directory obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architektur. Są one wszystkie oparte na protokoły będące standardami branżowymi, takie jak OAuth 2.0 lub OpenID Connect. Kilka kluczowych rozwiązania do zabezpieczania obszaru IoT usługi Azure Active Directory obejmują:

> [!div class="checklist"]
> * Store kluczy tajnych aplikacji w usłudze Azure Active Directory i klucze w bezpiecznej lokalizacji, takich jak [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Używany certyfikat wystawiony przez zaufanego [urząd certyfikacji](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) zamiast kluczy tajnych aplikacji w celu uwierzytelnienia.
> * Ogranicz zakres dostępu dla tokenu protokołu OAuth 2.0.
> * Sprawdź czas, który token jest prawidłowy i czy token jest prawidłowy.
> * Ustaw odpowiednie długości czasu, przez jaki tokeny są prawidłowe dla.
> * Wygasłe tokenów odświeżania.

<div id="rbac"></div>

## <a name="role-based-access-control-best-practices"></a>Najlepszymi praktykami kontroli dostępu opartej na rolach

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat najlepszych rozwiązań Azure IoT, przeczytaj [najlepsze rozwiązania dotyczące zabezpieczeń IoT](../iot-fundamentals/iot-security-best-practices.md).

* Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach, przeczytaj [kontroli dostępu opartej na rolach](./security-role-based-access-control.md).

* Aby uzyskać informacje dotyczące uwierzytelniania, przeczytaj [Uwierzytelnij za pomocą interfejsów API](./security-authenticating-apis.md).
