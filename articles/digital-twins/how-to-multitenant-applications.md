---
title: Włączanie wielodostępnych aplikacji za pomocą usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Jak skonfigurować wielodostępne Azure Active Directory aplikacje dla usługi Azure Digital bliźniaczych reprezentacji.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: mavoge
ms.openlocfilehash: 2ee3681640f68839c32e2963b34d5547abb6943b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976877"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Włączanie wielodostępnych aplikacji za pomocą usługi Azure Digital bliźniaczych reprezentacji

Deweloperzy rozwiązań korzystający z usługi Azure Digital bliźniaczych reprezentacji mogą stwierdzić, że chcą obsługiwać wielu klientów w ramach jednej usługi lub rozwiązania. W rzeczywistości aplikacje wielodostępne są wśród najpopularniejszych konfiguracji usługi Azure Digital bliźniaczych reprezentacji.

W tym dokumencie opisano sposób konfigurowania aplikacji Digital bliźniaczych reprezentacji na platformie Azure w celu obsługi kilku dzierżaw Azure Active Directory i klientów.

## <a name="multitenancy"></a>Wielodostępności

Zasób wielodostępny to jedno zainicjowane wystąpienie obsługujące wielu klientów. Każdy klient ma własne niezależne dane i uprawnienia. Środowisko każdego klienta jest odizolowane od siebie, tak aby ich "widok" aplikacji był różny.

Aby dowiedzieć się więcej o wielodostępności, Odczytaj aplikacje wielodostępne [na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scenariusz problemu

W tym scenariuszu Rozważmy Tworzenie rozwiązania Digital bliźniaczych reprezentacji (**Developer**) na platformie Azure i klienta korzystającego z tego rozwiązania (**Klient**):

- **Deweloper** ma subskrypcję platformy Azure z dzierżawą Azure Active Directory.
- **Deweloper** wdraża wystąpienie usługi Azure Digital bliźniaczych reprezentacji w ramach swojej subskrypcji platformy Azure. Azure Active Directory automatycznie utworzyć nazwę główną usługi wdzierżawie Azure Active Directory dewelopera.
- Użytkownicy wramach dzierżawy Azure Active Directory dewelopera mogą następnie [uzyskać tokeny uwierzytelniania OAuth 2,0](./security-authenticating-apis.md) z usługi Azure Digital bliźniaczych reprezentacji.
- **Deweloper** tworzy teraz aplikację mobilną, która bezpośrednio integruje się z interfejsami API usługi Azure Digital bliźniaczych reprezentacji Management.
- **Deweloperzy** umożliwiają **klientom** korzystanie z aplikacji mobilnej.
- **Klient** musi mieć autoryzację, aby używać interfejsu API zarządzania bliźniaczych reprezentacji AzureDigital w aplikacji dewelopera.

Problem:

- Gdy **Klient** logujesię do aplikacji dewelopera, aplikacja nie może uzyskać tokenów użytkowników **klienta**do uwierzytelniania za pomocą interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.
- Wyjątek jest wystawiany w Azure Active Directory wskazujący, że usługa Azure Digital bliźniaczych reprezentacji nie jest rozpoznawana w katalogu **klienta**.

## <a name="problem-solution"></a>Rozwiązanie problemu

Aby rozwiązać poprzedni scenariusz problemu, należy wykonać następujące czynności w celu utworzenia jednostki usługi Digital bliźniaczych reprezentacji na platformie Azure w ramach dzierżawy Azure Active Directory **klienta**:

- Jeśli **Klient** nie ma jeszcze subskrypcji platformy Azure z dzierżawą Azure Active Directory:

  - Administrator dzierżawy Azure Active Directory **klienta**musi uzyskać [subskrypcję platformy Azure z płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/)użyciem.
  - Administrator dzierżawy Azure Active Directory **klienta**musi [połączyć swoją dzierżawę z nową subskrypcją](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Na [Azure Portal](https://portal.azure.com)Administrator dzierżawy Azure Active Directory **klienta**wykona następujące czynności:

  1. Otwórz **subskrypcje**.
  1. Wybierz subskrypcję mającą dzierżawę Azure Active Directory, która ma byćużywana w aplikacji dewelopera.

     ![Subskrypcje Azure Active Directory][1]

  1. Wybierz pozycję **dostawcy zasobów**.
  1. Wyszukaj ciąg **Microsoft. IoTSpaces**.
  1. Wybierz pozycję **Zarejestruj**.

     ![Dostawcy zasobów Azure Active Directory][2]
  
## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat używania funkcji zdefiniowanych przez użytkownika z usługą Azure Digital bliźniaczych reprezentacji, przeczytaj artykuł [jak utworzyć funkcje usługi Azure Digital bliźniaczych reprezentacji zdefiniowane przez użytkownika](./how-to-user-defined-functions.md).

- Aby dowiedzieć się, jak używać kontroli dostępu opartej na rolach, aby dodatkowo zabezpieczyć aplikację za pomocą przypisań ról, przeczytaj artykuł [jak utworzyć kontrolę dostępu opartą na rolach w usłudze Azure Digital bliźniaczych reprezentacji i zarządzać nią](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
