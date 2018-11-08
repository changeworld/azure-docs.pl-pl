---
title: Włączanie aplikacji wielodostępnych za pomocą Twins cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Opis sposobu rejestrowania dzierżaw usługi Azure Active Directory Twoich klientów za pomocą Twins cyfrowych platformy Azure
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: a2d9ece119003c341f49ee03d735d5636b179a32
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51259891"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Włączanie aplikacji wielodostępnych za pomocą Twins cyfrowych platformy Azure

Deweloperzy, którzy używają bliźniaczych reprezentacji cyfrowych platformy Azure zazwyczaj chcesz tworzyć aplikacje wielodostępne. A *aplikacji wielodostępnej* jest pojedynczym wystąpieniem elastycznie, który obsługuje wielu klientów. Każdy klient ma swoje własne niezależnie od danych i uprawnień.

W tym dokumencie opisano, jak utworzyć aplikację wielodostępną Twins cyfrowych platformy Azure, która obsługuje kilka dzierżaw usługi Azure Active Directory (Azure AD), jak i klientów.

## <a name="scenario-summary"></a>Podsumowanie scenariuszy

W tym scenariuszu należy wziąć pod uwagę D dla deweloperów i klientów C:

- Deweloper D ma subskrypcję platformy Azure z dzierżawą usługi Azure AD.
- Deweloper D wdroży wystąpienie Twins cyfrowych platformy Azure w ramach subskrypcji platformy Azure.
- Użytkowników w ramach dzierżawy usługi Azure AD dla deweloperów D mogą uzyskiwać tokeny korzystająca z usługi Azure cyfrowego bliźniaczych reprezentacji, ponieważ usługa Azure AD utworzono nazwę główną w dzierżawie usługi Azure AD D dla deweloperów usługi.
- Deweloper D utworzy teraz aplikację mobilną, która bezpośrednio integruje się z cyfrowego Twins zarządzania interfejsu API usługi Azure.
- Deweloper D umożliwia C klienta aplikacji mobilnej.
- Musi być autoryzowana klienta C do użycia cyfrowego Twins zarządzania interfejsu API usługi Azure w ramach D dla deweloperów aplikacji.

  > [!IMPORTANT]
  > - Po zalogowaniu klientów języka C do D dla deweloperów aplikacji, aplikacja nie może uzyskać tokeny dla użytkowników klienta C na komunikowanie się z interfejsu API zarządzania.
  > - Usługa Azure AD zgłasza błąd, co oznacza, że Twins cyfrowych platformy Azure nie został rozpoznany w katalogu c. klienta.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać w poprzednim scenariuszu, następujące akcje są wymagane do utworzenia Twins cyfrowych platformy Azure nazwy głównej usługi w ramach dzierżawy usługi Azure AD klienta C:

- Jeśli C klienta nie ma jeszcze subskrypcji platformy Azure z dzierżawą usługi Azure AD:

  - Administrator dzierżawy usługi Azure AD klienta języka C należy uzyskać [płatność za rzeczywiste użycie subskrypcji platformy Azure](https://azure.microsoft.com/offers/ms-azr-0003p/).
  - Administrator dzierżawy usługi Azure AD klienta języka C następnie należy [połączyć ich dzierżawy przy użyciu nowej subskrypcji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

- Na [witryny Azure portal](https://portal.azure.com), administrator dzierżawy usługi Azure AD C klientów wykonuje następujące czynności:

  1. Otwórz **subskrypcje**.
  1. Wybierz subskrypcję, która ma dzierżawy usługi Azure AD, który ma być używany w D dla deweloperów aplikacji.
  1. Wybierz **dostawców zasobów**.
  1. Wyszukaj **Microsoft.IoTSpaces**.
  1. Wybierz pozycję **Zarejestruj**.
  
## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak używać funkcji zdefiniowanych przez użytkownika za pomocą Twins cyfrowych platformy Azure, przeczytaj [Azure cyfrowego Twins UDF](how-to-user-defined-functions.md).

Aby dowiedzieć się, jak można dodatkowo zabezpieczyć aplikację za pomocą przypisań ról za pomocą kontroli dostępu opartej na rolach, przeczytaj [kontrola dostępu oparta na rolach reprezentacje urządzeń cyfrowych platformy Azure](security-create-manage-role-assignments.md).
