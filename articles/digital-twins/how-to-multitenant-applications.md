---
title: Włączanie aplikacji wielodostępnych — Azure Digital Twins | Dokumenty firmy Microsoft
description: Jak skonfigurować wielodostępne aplikacje usługi Azure Active Directory dla usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 6e1321e01d8d12974a2704f4478b02a26c14142f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264937"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Włączanie aplikacji wielodostępnych za pomocą usługi Azure Digital Twins

Deweloperzy rozwiązań, którzy opierają się na platformie Azure Digital Twins, mogą stwierdzić, że chcą obsługiwać wielu klientów za pomocą jednej usługi lub rozwiązania. W rzeczywistości aplikacje *wielodostępne* należą do najpopularniejszych konfiguracji usługi Azure Digital Twins.

W tym dokumencie opisano sposób konfigurowania aplikacji Usługi Azure Digital Twins do obsługi kilku dzierżaw i klientów usługi Azure Active Directory.

## <a name="multitenancy"></a>Wielodostępność

Zasób *wielodostępny* jest pojedynczym aprowizowanym wystąpieniem, które obsługuje wielu klientów. Każdy klient ma własne niezależne dane i uprawnienia. Doświadczenie każdego klienta jest odizolowane od siebie, tak aby ich "widok" aplikacji był odrębny.

Aby dowiedzieć się więcej o wielodostępności, przeczytaj artykuł [Aplikacje wielodostępne na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scenariusz problemu

W tym scenariuszu należy wziąć pod uwagę dewelopera tworzenia rozwiązania Azure Digital Twins **(DEWELOPER)** i klienta, który korzysta z tego rozwiązania **(KLIENT):**

- **DEWELOPER** ma subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory.
- **DEWELOPER** wdraża wystąpienie usługi Azure Digital Twins w swojej subskrypcji platformy Azure. Usługa Azure Active Directory automatycznie utworzyła jednostkę usługi w dzierżawie usługi Azure Active Directory **dewelopera.**
- Użytkownicy w dzierżawie usługi Azure Active Directory **programu DEVELOPER**mogą [następnie nabywać tokeny OAuth 2.0](./security-authenticating-apis.md) z usługi Azure Digital Twins.
- **DEWELOPER** tworzy teraz aplikację mobilną, która bezpośrednio integruje się z interfejsami API zarządzania cyfrowymi urządzeniami przenośnymi usługi Azure.
- **DEWELOPER** umożliwia **KLIENTowi** korzystanie z aplikacji mobilnej.
- **KLIENT** musi być upoważniony do korzystania z interfejsu API zarządzania cyfrowymi obrazami cyfrowymi usługi Azure w aplikacji developer.CUSTOMER must be authorized to use the Azure Digital Twins Management API within **DEVELOPER's**application.

Problem:

- Gdy **klient** loguje się do aplikacji **dewelopera,** aplikacja nie może uzyskać tokenów dla użytkowników **klienta**do uwierzytelniania za pomocą interfejsów API zarządzania usługą Azure Digital Twins Management.
- Wyjątek jest wydawany w usłudze Azure Active Directory wskazujący, że usługa Azure Digital Twins nie jest rozpoznawana w katalogu **CUSTOMER.**

## <a name="problem-solution"></a>Rozwiązanie problemu

Aby rozwiązać poprzedni scenariusz problemu, do utworzenia jednostki usługi Azure Digital Twins w **dzierżawie**usługi Azure Active Directory potrzebne są następujące akcje:

- Jeśli **klient** nie ma jeszcze subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory:

  - Administrator dzierżawy usługi Azure Active Directory firmy **CUSTOMER**musi uzyskać [subskrypcję platformy Azure zgodnie z rzeczywistym użyciem.](https://azure.microsoft.com/offers/ms-azr-0003p/)
  - **Administrator**dzierżawy usługi Azure Active Directory firmy CUSTOMER musi [połączyć dzierżawę z nową subskrypcją.](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)

- W [witrynie](https://portal.azure.com) **CUSTOMER**Azure portal administrator dzierżawy usługi Azure Active Directory klienta wykonuje następujące kroki:

  1. Wyszukaj **subskrypcje** w górnym polu wyszukiwania platformy Azure. Wybierz pozycję **Subskrypcje**.
  1. Wybierz subskrypcję, która ma dzierżawę usługi **DEVELOPER**Azure Active Directory do użycia w aplikacji developer's.

     [![Subskrypcje usługi Azure Active Directory](media/multitenant/ad-subscriptions.png)](media/multitenant/ad-subscriptions.png#lightbox)

  1. Wybierz **pozycję Dostawcy zasobów**.
  1. Wyszukaj **witrynę Microsoft.IoTSpaces**.
  1. Wybierz pozycję **Zarejestruj**.

     [![Dostawcy zasobów usługi Azure Active Directory](media/multitenant/ad-resource-providers.png)](media/multitenant/ad-resource-providers.png#lightbox)
  
## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o korzystaniu z funkcji zdefiniowanych przez użytkownika za pomocą usługi Azure Digital Twins, przeczytaj [artykuł Jak utworzyć funkcje zdefiniowane przez użytkownika usługi Azure Digital Twins](./how-to-user-defined-functions.md).

- Aby dowiedzieć się, jak używać kontroli dostępu opartej na rolach w celu dalszego zabezpieczania aplikacji za pomocą przypisań ról, przeczytaj [artykuł Jak utworzyć kontrolę dostępu opartą na rolach usługi Azure Digital Twins i zarządzać nią.](./security-create-manage-role-assignments.md)
