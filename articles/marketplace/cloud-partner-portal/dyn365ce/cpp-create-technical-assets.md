---
title: Utwórz Dynamics 365 for Customer Engagement, zasoby techniczne | Portal Azure Marketplace
description: Utwórz zasoby techniczne dla Dynamics 365 Customer Engagement aplikacji oferty.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943105"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Utwórz zasoby techniczne w celu skorzystania z oferty aplikacji platformy Azure

Zazwyczaj opracowywania rozwiązań przy użyciu [SDK for Dynamics 365 Customer Engagement aplikacji](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Rozwiązania mieć wiele form, zgodnie z opisem w [modele programowania for Dynamics 365 Customer Engagement aplikacji](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Wybierz formularz, która najlepiej odpowiada wymagań dotyczących rozwiązania.  Podczas opracowywania rozwiązania, istnieje kilka problemów, które muszą spełnić, takich jak opcje rozszerzalności, składniki rozwiązania i zgodność wersji.  Aby uzyskać więcej informacji, zobacz [wprowadzenie do rozwiązań](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

Większość rozwiązań Dynamics 365, opublikowane w usłudze AppSource to zarządzanych aplikacji dystrybuowanych jako pliki pakietu.


## <a name="creating-and-storing-the-package"></a>Tworzenie i przechowywanie pakietu

Dokumentacja równoległego tworzenia Dynamics 365 for Customer Engagement oferuje znajduje się w sekcji [opublikuj swoją aplikację w usłudze AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Następujące zawarte szczegółowe tematy jak utworzyć plik pakietu rozwiązania i przekaż go do usługi Azure storage:

- [Krok 4. Tworzenie pakietu usługi AppSource dla aplikacji](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) — wyjaśnia, jak utworzyć plik skompresowany (zip), która reprezentuje Twoją zarządzaną aplikacją i zawiera: folder zasoby rozwiązania, niestandardowy kod biblioteki DLL, plik informacji o typie MIME, ikona pakietu usługi AppSource, licencji Plik warunki (HTML) i zawartość pliku (XML).
- [Krok 5. Store pakietu usługi AppSource w usłudze Azure Storage i wygenerować adresu URL przy użyciu klucza sygnatury dostępu Współdzielonego](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) — wyjaśnia, jak plik pakietu usługi AppSource jest przechowywany na koncie usługi Microsoft Azure Blob storage, a następnie Udostępnij plik pakietu za pomocą klucza sygnatury dostępu współdzielonego (SAS). Plik pakietu jest pobierana z lokalizacji usługi Azure Storage do certyfikacji, a następnie prób w usłudze AppSource i publikacji.


## <a name="next-steps"></a>Kolejne kroki

Jeśli użytkownik jeszcze nie zrobiono, [tworzenia usługi Dynamics 365 Customer Engagement oferty](./cpp-create-offer.md).  Następnie będzie gotowa do [opublikować ofertę](./cpp-publish-offer.md).
