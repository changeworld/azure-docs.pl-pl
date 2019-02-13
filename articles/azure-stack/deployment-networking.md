---
title: Ruch sieciowy wdrożenia w usłudze platformy Azure Stack | Dokumentacja firmy Microsoft
description: W tym artykule opisano, czego można oczekiwać o procesach sieci wdrażania usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 5f4f76f87718ddcc81f8fae8b043b73a4dbd6b0a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56189280"
---
# <a name="about-deployment-network-traffic"></a>Dotyczących ruchu sieciowego wdrożenia
Zrozumienie, jak ruch sieciowy przepływa w usłudze Azure Stack wdrożenia jest niezbędna do zapewnienia pomyślnego wdrożenia. W tym artykule przedstawiono oczekiwanego ruchu sieciowego w procesie wdrażania zapewnienie zrozumienia, czego można oczekiwać.

Ta ilustracja przedstawia wszystkie składniki i połączenia związane z procesem wdrażania:

![Topologii sieci wdrożenia w usłudze Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> W tym artykule opisano wymagania dotyczące wdrożenia podłączone, aby dowiedzieć się więcej na temat innych metod wdrażania, zobacz [modele połączenia wdrażania usługi Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Wdrażanie maszyny Wirtualnej
Rozwiązanie usługi Azure Stack obejmuje grupę serwerów, które są używane do obsługi składników usługi Azure Stack i dodatkowy serwer o nazwie hosta cyklu życia sprzętu (HLH). Ten serwer jest używany do wdrażania i zarządzania cyklem życia rozwiązania i obsługuje wdrożenia maszyny Wirtualnej (Menedżer DVM) podczas wdrażania.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania
Przed rozpoczęciem wdrażania, istnieją niektóre minimalne wymagania, które mogą być sprawdzone przez Twoje OEM, aby upewnić się, że wdrożenie zakończy się pomyślnie. Zrozumienie, że te wymagania ułatwią przygotowanie środowiska i upewnij się, że weryfikacja zakończy się powodzeniem, są:

-   [Certyfikaty](azure-stack-pki-certs.md)
-   [Subskrypcja platformy Azure](https://azure.microsoft.com/free/?b=17.06)
-   Dostęp do Internetu
-   DNS
-   NTP

> [!NOTE]
> Ten artykuł koncentruje się na trzy ostatnie wymagania. Aby uzyskać więcej informacji na dwa pierwsze Zobacz powyższe linki.

## <a name="deployment-network-traffic"></a>Ruch sieciowy wdrożenia
Menedżer DVM jest konfigurowana przy użyciu adresu IP z sieci kontrolera zarządzania płytą GŁÓWNĄ i wymaga dostępu do sieci z Internetem. Mimo że nie wszystkie składniki sieci kontrolera zarządzania płytą GŁÓWNĄ wymagają zewnętrznych routing i dostęp do Internetu, niektóre komponenty specyficzne dla producenta OEM, przy użyciu adresów IP, z tą siecią może również wymagać go.

Podczas wdrażania Menedżer DVM przeprowadza uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu konta platformy Azure z subskrypcji. Aby to zrobić, Menedżer DVM wymaga dostępu do Internetu na listę określonych portów i adresów URL. Można znaleźć pełną listę w [publikowanie punktów końcowych](azure-stack-integrate-endpoints.md) dokumentacji. Menedżer DVM będzie korzystać z serwera DNS do przesyłania żądania DNS wewnętrznych składników do zewnętrznych adresów URL. Wewnętrznego serwera DNS przekazuje te żądania na adres usługi przesyłania dalej DNS zapewniające do producenta OEM przed ich wdrożeniem. To samo dotyczy serwera NTP, niezawodne serwer czasu jest wymagane, aby zachować spójność i czas synchronizacji dla wszystkich składników usługi Azure Stack.

Dostęp do Internetu wymagany przez Menedżer DVM podczas wdrażania wychodzącego tylko, nie dla ruchu przychodzącego wywołań podczas wdrażania. Należy pamiętać, że używa jego adresu IP jako źródło i tej usługi Azure Stack nie obsługuje konfiguracji serwera proxy. W związku z tym jeśli to konieczne, należy podać przezroczystym serwerem proxy lub translator adresów Sieciowych, aby uzyskać dostęp do Internetu. Podczas wdrażania niektóre składniki wewnętrzne zostanie uruchomione, dostęp do Internetu za pośrednictwem sieci zewnętrznej, przy użyciu publicznych adresów VIP. Po zakończeniu wdrażania całej komunikacji między platformą Azure i usługi Azure Stack jest nawiązywane za pośrednictwem sieci zewnętrznej za pomocą publicznych adresów VIP.

Konfiguracje sieci dla usługi Azure Stack przełączników zawierają list kontroli dostępu (ACL), które ograniczają ruch między niektórych sieci źródeł i miejsc docelowych. Menedżer DVM jest jedynym składnikiem z nieograniczonym dostępem; nawet HLH jest bardzo ograniczona. Producent OEM możesz poprosić o opcje dostosowywania, aby ułatwić zarządzanie i dostęp z sieci. Ze względu na te listy ACL jest ważne uniknąć zmieniania adresów serwerów DNS i NTP w czasie wdrażania. Jeśli tak zrobisz, musisz ponownie skonfigurować wszystkie przełączniki dla rozwiązania.

Po zakończeniu wdrażania podanych adresów serwerów DNS i NTP nadal być bezpośrednio używane przez składniki systemu. Na przykład jeśli zaznaczysz żądania DNS po ukończeniu wdrażania, źródło ulegnie zmianie z IP Menedżer DVM adres z zakresu sieci zewnętrznej.

Po zakończeniu wdrażania podanych adresów serwerów DNS i NTP będzie używane przez składniki systemu za pośrednictwem sieci SDN przy użyciu sieci zewnętrznej. Na przykład jeśli zaznaczysz żądania DNS po ukończeniu wdrażania, źródłowy ulegnie zmianie z IP Menedżer DVM do publicznych adresów VIP.

## <a name="next-steps"></a>Kolejne kroki
[Sprawdź poprawność rejestracji platformy Azure](azure-stack-validate-registration.md)
