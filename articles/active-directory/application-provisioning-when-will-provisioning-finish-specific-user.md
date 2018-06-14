---
title: Sprawdź, kiedy określony użytkownik będą mogli uzyskać dostęp do aplikacji | Dokumentacja firmy Microsoft
description: Jak ustalić, kiedy użytkownik niezwykle ważne być w stanie uzyskać dostęp do aplikacji, które zostały skonfigurowane do obsługi użytkowników z usługą Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 42c305ad48e6994b1d2c729b849acf665e575807
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26613627"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Ustalić, kiedy określony użytkownik będzie mógł uzyskać dostęp do aplikacji
Używając użytkownika automatycznego inicjowania obsługi administracyjnej za pomocą aplikacji, usługi Azure AD automatycznie udostępniania i aktualizowanie kont użytkowników w aplikacji na podstawie takich elementów, jak [przypisania użytkowników i grup](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) w odstępach zaplanowanego czasu, zazwyczaj co 10 minut.

## <a name="how-long-does-it-take"></a>Jak długo trwa?

Czas, jaki zajmuje dla danego użytkownika na potrzeby aprowizacji zależy od głównie czy już wystąpił początkową synchronizację "pełnej".

Pierwsza synchronizacja między usługą Azure AD i aplikacji może potrwać od 20 minut do kilku godzin, zależnie od rozmiaru katalogu usługi Azure AD i liczbę użytkowników w zakresie obsługi. 

Kolejne synchronizacje po początkowej synchronizacji można szybciej (np. w ciągu 10 minut), jak znaki wodne, przedstawiające stan obu systemów po początkowej synchronizacji, poprawa wydajności kolejne synchronizacje magazyny inicjowania obsługi usługi.

## <a name="how-to-check-the-status-of-a-user"></a>Sposób sprawdzania stanu użytkownika

Aby wyświetlić stan inicjowania obsługi administracyjnej dla wybranego użytkownika, sprawdź w dziennikach inspekcji w usłudze Azure AD.

Inicjowania obsługi administracyjnej dzienników inspekcji można uzyskać w portalu Azure w **usługi Azure Active Directory &gt; aplikacje przedsiębiorstwa &gt; \[Nazwa aplikacji\] &gt; dzienniki inspekcji** kartę. Odfiltruj dzienniki **Inicjowanie obsługi konta** kategorię, aby zobaczyć tylko inicjowania obsługi zdarzeń dla danej aplikacji. Można wyszukiwać użytkowników na podstawie "Dopasowania Identyfikatora" skonfigurowanego dla nich w mapowań atrybutów. 

Na przykład, jeśli skonfigurowane "główna nazwa użytkownika" lub "adres e-mail" jako zgodnego atrybut po stronie usługi Azure AD, a użytkownik nie udostępniania ma wartość "audrey@contoso.com", następnie wyszukaj w dziennikach inspekcji "audrey@contoso.com" i przeglądu, a następnie wpisy zwracane.

Inicjowania obsługi administracyjnej dzienników inspekcji rejestrowania wszystkich operacji wykonywanych przez usługę inicjowania obsługi administracyjnej, w tym:

* Podczas badania usługi Azure AD dla przypisanych użytkowników, które znajdują się w zakresie alokacji
* Wykonywanie zapytania aplikacji docelowej istnienie tych użytkowników
* Porównanie obiektów użytkownika między systemem
* Dodawanie, aktualizowanie lub wyłączenie konta użytkownika w systemie docelowym na podstawie porównania

## <a name="next-steps"></a>Następne kroki
[Automatyzowanie użytkownika alokowania i anulowania alokowania do aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
