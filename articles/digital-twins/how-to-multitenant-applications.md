---
title: Jak włączyć wielodostępnych aplikacji za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu rejestrowania dzierżaw usługi Azure Active Directory Twoich klientów za pomocą Twins cyfrowych platformy Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324271"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>Jak włączyć wielodostępnych aplikacji za pomocą Twins cyfrowych platformy Azure

Deweloperzy korzystający z platformy Azure cyfrowego bliźniaczych reprezentacji prawdopodobnie będziesz tworzyć aplikacje wielodostępne. A *aplikacji wielodostępnej* jest pojedyncze wystąpienie udostępnione używane do obsługi wielu klientów przy użyciu własnych danych niezależne i uprawnień.

W tym dokumencie opisano, jak utworzyć aplikację wielodostępną Twins cyfrowych platformy Azure, obsługujące wiele dzierżaw usługi Azure Active Directory (AD) i klientów.

## <a name="scenario-summary"></a>Podsumowanie scenariuszy

W tym scenariuszu należy wziąć pod uwagę D dla deweloperów i klientów C:

- Deweloper D ma subskrypcję platformy Azure z dzierżawą usługi Azure AD.
- Deweloper D wdrożył wystąpienia reprezentacje urządzeń cyfrowych, w ramach subskrypcji platformy Azure.
- Użytkowników w ramach dzierżawy usługi Azure AD dla deweloperów D mogą uzyskiwać tokeny korzystająca z usługi cyfrowego Twins, ponieważ usługa Azure AD ma utworzono nazwę główną usługi w dzierżawie usługi Azure AD D dla deweloperów.
- Deweloper D utworzy teraz aplikację mobilną, która bezpośrednio integruje się z interfejsu API zarządzania Twins cyfrowych.
- Deweloper D następnie umożliwia C klienta aplikacji mobilnej.
- Teraz C klienta będzie musiał być autoryzowane do używania interfejsu API zarządzania cyfrowego Twins D dla deweloperów aplikacji.

  > [!IMPORTANT]
  > - Po zalogowaniu klientów języka C do D dla deweloperów aplikacji, aplikacja nie będzie można uzyskać tokenów dla klienta C użytkownikom na komunikowanie się z interfejsu API zarządzania.
  > - Usługa Azure AD następnie zgłosi błąd wskazujący, czy cyfrowego Twins nie został rozpoznany w katalogu c. klienta.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać powyższy scenariusz, następujące akcje są wymagane do tworzenia usług cyfrowych bliźniaczych reprezentacji podmiotu zabezpieczeń w ramach dzierżawy usługi Azure AD klienta C:

- Jeśli C klienta nie ma jeszcze subskrypcji platformy Azure z dzierżawą usługi Azure AD:

  - Administrator dzierżawy usługi Azure AD klientów języka C należy uzyskać [płatność za rzeczywiste użycie subskrypcji platformy Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Administrator dzierżawy usługi Azure AD klientów języka C będzie musiał [połączyć ich dzierżawy przy użyciu nowej subskrypcji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Z [witryny Azure Portal](https://portal.azure.com), administratorem dzierżawy usługi Azure AD C klienta należy następnie:
  1. Otwórz **subskrypcje**.
  1. Wybierz subskrypcję, która ma dzierżawy usługi Azure AD, który ma być używany w D dla deweloperów aplikacji.
  1. Wybierz **dostawców zasobów**.
  1. Wyszukaj **Microsoft.IoTSpaces**.
  1. Kliknij przycisk **zarejestrować**.
  
## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o korzystaniu z funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure, przeczytaj [Azure cyfrowego Twins UDF](how-to-user-defined-functions.md).

Aby dowiedzieć się, jak można dodatkowo zabezpieczyć aplikację za pomocą przypisań ról za pomocą kontroli dostępu opartej na rolach, przeczytaj [cyfrowego Twins opartej na rolach kontrola dostępu](security-create-manage-role-assignments.md).
