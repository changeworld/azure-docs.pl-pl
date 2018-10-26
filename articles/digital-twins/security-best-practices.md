---
title: Opis najlepszych rozwiązań dotyczących zabezpieczeń Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Usługa Azure cyfrowego Twins najlepsze rozwiązania dotyczące zabezpieczeń
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092189"
---
# <a name="security-best-practices"></a>Najlepsze rozwiązania dotyczące zabezpieczeń

Bezpieczeństwa cyfrowego Twins platformy Azure umożliwia precyzyjne dostępu do określonych zasobów i akcji w grafie IoT. Robi to za pośrednictwem szczegółową roli i uprawnienia zarządzania o nazwie kontroli dostępu opartej na rolach.

Azure Twins cyfrowego również korzysta z innych funkcji zabezpieczeń na usługi Azure IoT, w tym usługi Active Directory. Z tego powodu konfigurowania aplikacji Twins cyfrowego Azure polega na użyciu wiele takich samych [rozwiązania w zakresie zabezpieczeń usługi Azure IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/) aktualnie zalecany.

Ten artykuł zawiera podsumowanie kluczowych najlepszych rozwiązań.

> [!IMPORTANT]
> Przejrzyj zasoby dodatkowe zabezpieczenia (w tym z dostawcami urządzeń) do zapewnienia zabezpieczeń maksymalny obszar IoT.

## <a name="iot-security-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń IoT

Niektóre rozwiązania kluczy bezpiecznie zabezpieczania urządzeń IoT należą:

> [!div class="checklist"]
> * Zabezpiecz każdego urządzenia, które jest podłączony do obszaru IoT w taki sposób, odporną na manipulacje.
> * Ogranicz roli poszczególnych urządzeń, czujników i osoby w Twojej przestrzeni IoT. W przypadku naruszenia zabezpieczeń jest zminimalizowany wpływ.
> * Potencjalne wykorzystanie o IP urządzenia adresów ograniczeń filtrowania i port.
> * Ogranicz przepustowość operacji We/Wy i urządzeń w celu zwiększenia wydajności. Ograniczanie szybkości może zwiększyć bezpieczeństwo poprzez zapobieganie atakom typu "odmowa usługi".
> * Aktualizowanie oprogramowania układowego urządzenia.

Niektóre rozwiązania kluczy bezpiecznie zabezpieczyć miejsca IoT obejmują:

> [!div class="checklist"]
> * Szyfruj dane zapisane, przechowywane lub trwałych.
> * Wymagaj hasła lub kluczy w celu można zmienić ani okresowo odświeżane.
> * Dokładnie ograniczyć dostępu i uprawnień według roli (zobacz Kontrola dostępu oparta na rolach zalecenia poniżej).
> * Należy używać szyfrowania zaawansowane. Oznacza to wymaganie długie hasła, przy użyciu bezpiecznych protokołów i uwierzytelniania dwuskładnikowego.

Monitorowanie zasobów IoT, aby wyszukiwać elementy odstające, zagrożeń lub parametry zasobu, które wykraczają poza zakres działania zwykle odbywa się za pośrednictwem analizy na platformie Azure.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zdarzeń przetwarzania i monitorowania, zobacz nasze artykuł w [zdarzeń > routingu](./concepts-events-routing.md).

## <a name="azure-active-directory-best-practices"></a>Najlepsze rozwiązania platformy Azure Active Directory

Azure Twins cyfrowego używa usługi Azure Active Directory do uwierzytelniania użytkowników oraz chronić aplikacji. Usługa Azure Active Directory obsługuje uwierzytelnianie w wielu nowoczesnych architekturach z nich oparte na protokoły będące standardami branżowymi, takie jak OAuth 2.0 lub OpenID Connect. Kilka kluczowych rozwiązania do zabezpieczania obszaru IoT usługi Azure Active Directory obejmują:

> [!div class="checklist"]
> * Store kluczy tajnych aplikacji w usłudze Azure Active Directory i klucze w bezpiecznym miejscu, takich jak [usługi Key Vault](https://azure.microsoft.com/services/key-vault/).
> * Używany certyfikat wystawiony przez zaufanego [urzędu certyfikacji](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started) zamiast kluczy tajnych aplikacji w celu uwierzytelnienia.
> * Ogranicz zakres dostępu dla tokenu protokołu OAuth 2.0.
> * Sprawdź czas, który token jest prawidłowy i czy token jest prawidłowy.
> * Ustaw odpowiednie długości czasu, przez jaki tokeny są prawidłowe dla.
> * Wygasłe tokenów odświeżania.

## <a name="role-based-access-control-best-practices"></a>Najlepszymi praktykami kontroli dostępu opartej na rolach

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat najlepszych rozwiązań Azure IoT, przeczytaj [najlepsze rozwiązania dotyczące zabezpieczeń IoT](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/).

Aby dowiedzieć się więcej na temat kontroli dostępu opartej na rolach, przeczytaj [kontroli dostępu opartej na rolach](./security-role-based-access-control.md).

W przypadku uwierzytelniania odczytu [uwierzytelniania za pomocą interfejsów API](./security-authenticating-apis.md).
