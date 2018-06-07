---
title: Ruch w sieci Azure wdrożenia stosu | Dokumentacja firmy Microsoft
description: W tym artykule opisano, czego można oczekiwać o sieci procesów wdrażania stosu Azure.
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
ms.date: 05/21/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: b808875e66e867b84e2971c6a5bd031d108d003b
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655428"
---
# <a name="about-deployment-network-traffic"></a>O ruchu sieciowego wdrożenia
Opis sposobu ruch sieciowy przechodzi podczas stosu Azure wdrożenia jest niezbędne do zapewnienia pomyślnego wdrożenia. W tym artykule przedstawiono oczekiwane ruchu sieciowego podczas procesu wdrażania, aby zapewnić zrozumienie, czego można oczekiwać.

Tej ilustracji przedstawiono wszystkie składniki i połączeń związane z procesem wdrażania:

![Topologia sieci wdrożenia w usłudze Azure stosu](media/deployment-networking/figure1.png)

> [!NOTE]
> W tym artykule opisano wymagania dotyczące wdrażania połączonych, aby dowiedzieć się więcej na temat innych metod wdrażania, zobacz [modele połączenia wdrażania stosu Azure](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Wdrożenie maszyny Wirtualnej
Rozwiązanie stosu Azure zawiera grupę serwerów, które są używane do obsługi składniki stosu Azure i dodatkowy serwer o nazwie hosta cyklu życia sprzętu (HLH). Ten serwer jest używany do wdrażania i zarządzania cyklem życia rozwiązania i obsługuje wdrożenia maszyny Wirtualnej (Menedżer DVM) podczas wdrażania.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania
Przed rozpoczęciem wdrażania, istnieją pewne wymagania minimalne, które mogą być sprawdzone przez OEM w celu zapewnienia, że wdrożenie zakończy się pomyślnie. Zrozumienie, że te wymagania ułatwią przygotowanie środowiska i upewnij się, że weryfikacja zakończy się powodzeniem, to:

-   [Certyfikaty](azure-stack-pki-certs.md)
-   [Subskrypcja platformy Azure](https://azure.microsoft.com/free/?b=17.06)
-   Dostęp do Internetu
-   DNS
-   NTP

> [!NOTE]
> Ten artykuł skupia się na trzy ostatnie wymagania. Aby uzyskać więcej informacji na dwa pierwsze Zobacz powyższe łącza.

## <a name="deployment-network-traffic"></a>Ruch sieciowy wdrożenia
Menedżer DVM jest skonfigurowany z adresem IP z sieci BMC i wymaga dostępu do sieci z Internetem. Mimo że nie wszystkie składniki sieciowe BMC wymagają zewnętrznych routing i dostęp do Internetu, niektóre składniki specyficzne dla producenta OEM wykorzystanie adresów IP z tą siecią może wymagać go.

Podczas wdrażania Menedżer DVM uwierzytelniania usługi Azure Active Directory (Azure AD) przy użyciu konta z subskrypcją platformy Azure. Aby to zrobić, Menedżer DVM wymaga dostępu do Internetu na listę określonego portu i adres URL. Pełną listę w można znaleźć [publikowanie punktów końcowych](azure-stack-integrate-endpoints.md) dokumentacji. Menedżer DVM będzie korzystać z serwera DNS do przesyłania żądania DNS wewnętrznych składników do zewnętrznych adresów URL. Wewnętrzny serwer DNS przekazuje te żądania na adres usługi przesyłania dalej DNS osobie OEM przed ich wdrożeniem. To samo dotyczy serwera NTP, serwer niezawodnej czasu jest zobowiązane do zachowania spójności i czas synchronizacji dla wszystkich składników stosu Azure.

Dostęp do Internetu wymagany przez Menedżer DVM podczas wdrażania jest wychodzący tylko, nie przychodzących wywołań podczas wdrażania. Należy pamiętać, że jego adresu IP jest używana jako źródło i ten stos Azure nie obsługuje konfiguracji serwera proxy. W związku z tym jeśli to konieczne, należy podać przezroczystego obiektu pośredniczącego lub translator adresów Sieciowych, aby uzyskać dostęp do Internetu. Po zakończeniu wdrażania, cała komunikacja między Azure i stosu Azure są nawiązywane przy użyciu sieci zewnętrznej za pomocą publicznych adresów VIP.

Konfiguracje sieci dla przełączników stosu Azure zawierają listy kontroli dostępu (ACL) ograniczające ruch między niektórych źródeł sieci i miejsca docelowe. Menedżer DVM jest jedynym składnikiem z nieograniczonym dostępem; nawet HLH jest bardzo ograniczone. Producent OEM poproś o opcji dostosowania, aby ułatwić zarządzanie i uzyskiwaniem dostępu z sieci. Z powodu listy ACL jest ważne uniknąć zmieniania adresów serwerów DNS i protokołu NTP w czasie wdrażania. Można to zrobić, należy zmienić konfigurację wszystkich przełączników dla rozwiązania.

Po zakończeniu wdrażania podanych adresów serwerów DNS i protokołu NTP będzie używany przez składniki systemu bezpośrednio. Na przykład jeśli zaznaczysz żądania DNS po zakończeniu wdrożenia, źródło ulegnie zmianie z IP Menedżer DVM adres z zakresu sieci zewnętrznej.

Po pomyślnym wdrożeniu stosu Azure OEM partner może używać Menedżer DVM dla dodatkowych zadań po wdrożeniu. Jednak po ukończeniu wszystkich zadań wdrażania i konfiguracji po wdrożeniu producenta OEM należy usunąć i usunąć Menedżer DVM z HLH.

## <a name="next-steps"></a>Kolejne kroki
[Sprawdź poprawność rejestracja w usłudze Azure](azure-stack-validate-registration.md)
