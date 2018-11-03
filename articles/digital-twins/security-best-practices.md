---
title: Omówienie najlepszych rozwiązań zabezpieczeń Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure cyfrowego Twins najlepsze rozwiązania dotyczące zabezpieczeń
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959481"
---
# <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Bezpieczeństwa cyfrowego Twins platformy Azure umożliwia precyzyjne dostępu do określonych zasobów i akcji w grafie IoT. Robi to za pośrednictwem szczegółowe zarządzanie rolami i uprawnieniami nazywany kontrolą dostępu opartą na rolach.

Azure Twins cyfrowego używa także inne funkcje zabezpieczeń, które znajdują się w usłudze Azure IoT, w tym usługi Azure Active Directory (Azure AD). Z tego powodu konfigurowania aplikacji Twins cyfrowego Azure polega na użyciu wiele takich samych [rozwiązania w zakresie zabezpieczeń usługi Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) aktualnie zalecany.

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
> * Dokładnie ograniczyć dostępu i uprawnień według roli. Zobacz sekcję "opartej na rolach dostęp najlepszymi praktykami kontroli."
> * Należy używać szyfrowania zaawansowane. Długie hasła są wymagane i bezpieczne protokoły i uwierzytelniania dwuskładnikowego.

Monitorowanie zasobów IoT, aby obejrzeć elementy odstające, zagrożeń lub parametry zasobu, które wykraczają poza zakres zwykłych operacji. Do zarządzania, monitorowania, należy użyć analizy na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zdarzeń przetwarzania oraz monitorowania, zobacz [kierowanie zdarzeń i komunikatów za pomocą Twins cyfrowego Azure](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Najlepsze rozwiązania platformy Azure Active Directory

Azure Twins cyfrowego używa usługi Azure AD do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure AD obsługuje uwierzytelnianie dla szerokiego zakresu nowoczesnych architektur. Są one wszystkie oparte na protokoły będące standardami branżowymi, takie jak OAuth 2.0 lub OpenID Connect. Kilka kluczowych rozwiązania do zabezpieczania obszaru IoT dla usługi Azure AD obejmują:

> [!div class="checklist"]
> * Store kluczy tajnych aplikacji w usłudze Azure AD i klucze w bezpiecznej lokalizacji, takich jak [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Używany certyfikat wystawiony przez zaufanego [urząd certyfikacji](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) zamiast kluczy tajnych aplikacji w celu uwierzytelnienia.
> * Ogranicz zakres dostępu dla tokenu protokołu OAuth 2.0.
> * Sprawdź czas, który token jest prawidłowy i czy token jest prawidłowy.
> * Ustaw odpowiednie długości czasu, przez jaki tokeny są prawidłowe dla.
> * Wygasłe tokenów odświeżania.

## <a name="role-based-access-control-best-practices"></a>Najlepszymi praktykami kontroli dostępu opartej na rolach

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat najlepszych rozwiązań Azure IoT, przeczytaj [najlepsze rozwiązania dotyczące zabezpieczeń IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach, przeczytaj [kontroli dostępu opartej na rolach](./security-role-based-access-control.md).

Aby uzyskać informacje dotyczące uwierzytelniania, przeczytaj [Uwierzytelnij za pomocą interfejsów API](./security-authenticating-apis.md).
