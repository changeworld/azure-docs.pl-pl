---
title: Włączanie aplikacji wielodostępnych za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Jak skonfigurować wielodostępne aplikacje usługi Azure Active Directory bliźniaki cyfrowych platformy Azure.
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mavoge
ms.openlocfilehash: 03b5693fe016cfb11d6f685f9088fe6e94f03b90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688599"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Włączanie aplikacji wielodostępnych za pomocą Twins cyfrowych platformy Azure

Deweloperzy rozwiązań, którzy tworzą na Twins cyfrowych platformy Azure może się okazać, że chce obsługiwać wielu klientów przy użyciu jednej usługi lub rozwiązania. W rzeczywistości *wielodostępnej* aplikacji znajdują się wśród najbardziej typowe konfiguracje Twins cyfrowych platformy Azure.

W tym dokumencie opisano sposób konfigurowania aplikacji Twins cyfrowych platformy Azure do obsługi wielu dzierżaw usługi Azure Active Directory i klientów.

## <a name="multitenancy"></a>Wielodostępności

A *wielodostępnej* zasobów jest pojedynczym wystąpieniem elastycznie, który obsługuje wielu klientów. Każdy klient ma swoje własne niezależnie od danych i uprawnień. Środowisko każdego klienta jest odizolowane od siebie nawzajem, tak aby ich "view" aplikacji różni się.

Aby dowiedzieć się więcej na temat wielodostępności, przeczytaj [wielodostępnej aplikacji na platformie Azure](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications).

## <a name="problem-scenario"></a>Scenariusz problemu

W tym scenariuszu należy wziąć pod uwagę deweloperów, tworzenie rozwiązania Azure cyfrowego bliźniaczych reprezentacji (**DEVELOPER**) i klienta, które korzystają z tego rozwiązania (**klienta**):

- **DEWELOPER** ma subskrypcję platformy Azure z dzierżawą usługi Azure Active Directory.
- **DEWELOPER** wdroży wystąpienie Twins cyfrowych platformy Azure w ramach subskrypcji platformy Azure. Usługa Azure Active Directory automatycznie utworzono nazwę główną usługi w **DEVELOPER**przez dzierżawę usługi Azure Active Directory.
- Użytkowników w ramach **DEVELOPER**przez dzierżawę usługi Azure Active Directory można następnie [uzyskiwanie tokenów protokołu OAuth 2.0](./security-authenticating-apis.md) z usługi Azure cyfrowego bliźniaczych reprezentacji.
- **DEWELOPER** utworzy teraz aplikację mobilną, która bezpośrednio integruje się z cyfrowego Twins zarządzania interfejsów API usługi Azure.
- **DEWELOPER** umożliwia **klienta** korzystanie z aplikacji mobilnej.
- **Klient** muszą być autoryzowane do używania cyfrowego Twins zarządzania interfejsu API usługi Azure w ramach **DEVELOPER**w aplikacji.

Problem:

- Podczas **klienta** loguje się do **DEVELOPER**w aplikacji, aplikacja nie może uzyskać tokenów dla **klienta**firmy użytkownikom na uwierzytelnianie za pomocą cyfrowych Twins zarządzania interfejsów API usługi Azure.
- Wyjątek jest wystawiony w usłudze Azure Active Directory wskazujący, że Twins cyfrowych platformy Azure nie został rozpoznany w ramach **klienta**w katalogu.

## <a name="problem-solution"></a>Rozwiązanie problemu

Aby rozwiązać w poprzednim scenariuszu problem, następujące akcje są wymagane do utworzenia Twins cyfrowych platformy Azure nazwy głównej usługi w ramach **klienta**firmy dzierżawy usługi Azure Active Directory:

- Jeśli **klienta** nie ma jeszcze subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory:

  - **Klient**przez administratora dzierżawy usługi Azure Active Directory muszą uzyskać [płatność za rzeczywiste użycie subskrypcji platformy Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - **Klient**użytkownika administratora dzierżawy usługi Azure Active Directory, a następnie musi [połączyć ich dzierżawy przy użyciu nowej subskrypcji](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity).

- Na [witryny Azure portal](https://portal.azure.com), **klienta**przez administratora dzierżawy usługi Azure Active Directory wykonuje następujące czynności:

  1. Otwórz **subskrypcje**.
  1. Wybierz subskrypcję z dzierżawą usługi Azure Active Directory do użycia w **DEVELOPER**w aplikacji.

     ![Subskrypcje usługi Azure Active Directory][1]

  1. Wybierz **dostawców zasobów**.
  1. Wyszukaj **Microsoft.IoTSpaces**.
  1. Wybierz pozycję **Zarejestruj**.

     ![Dostawcy zasobów usługi Azure Active Directory][2]
  
## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej o tym, jak używać funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure, przeczytaj [jak tworzyć funkcje zdefiniowane przez użytkownika Twins cyfrowego Azure](./how-to-user-defined-functions.md).

- Aby dowiedzieć się, jak można dodatkowo zabezpieczyć aplikację za pomocą przypisań ról za pomocą kontroli dostępu opartej na rolach, przeczytaj [sposób tworzenia i zarządzania kontroli dostępu opartej na rolach Twins cyfrowego Azure](./security-create-manage-role-assignments.md).

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png
