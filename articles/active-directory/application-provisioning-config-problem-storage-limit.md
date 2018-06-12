---
title: Problem podczas zapisywania poświadczeń administratora podczas konfigurowania Inicjowanie obsługi użytkowników dla aplikacji w galerii Azure AD | Dokumentacja firmy Microsoft
description: Sposoby rozwiązywania typowych problemów, które muszą ponieść podczas konfigurowania Inicjowanie obsługi użytkowników do aplikacji już wyświetlane w galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: asmalser
ms.openlocfilehash: 8b23ea72a898dc5725c1c82ee2a5cbc06730b5aa
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292994"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem podczas zapisywania poświadczeń administratora podczas konfigurowania aplikacji usługi Azure Active Directory galerii Inicjowanie obsługi użytkowników 

Przy użyciu portalu Azure, aby skonfigurować [użytkownika automatycznego inicjowania obsługi administracyjnej](active-directory-saas-app-provisioning.md) dla aplikacji dla przedsiębiorstw, może wystąpić sytuacja, gdy:

* **Poświadczeń administratora** wprowadzonych dla aplikacji są prawidłowe i **Testuj połączenie** działania przycisku. Nie można zapisać poświadczenia i portalu Azure zwraca ogólny komunikat o błędzie.

Jeśli na języku SAML logowania jednokrotnego został również skonfigurowany dla tej samej aplikacji, najbardziej prawdopodobną przyczyną tego błędu jest limit magazynu dla poszczególnych aplikacji, wewnętrzne tej usługi Azure AD dla certyfikatów i poświadczeń został przekroczony.

Obecnie usługa Azure AD ma pojemność pamięci masowej kilobajtów jeden dla wszystkich certyfikatów, tokeny tajnego poświadczeń i odpowiednie dane konfiguracyjne skojarzone z pojedynczego wystąpienia aplikacji (znanej także jako główny rekord usługi w usłudze Azure AD).

Na podstawie SAML rejestracji jednokrotnej jest skonfigurowany, certyfikat używany do podpisywania tokenów SAML są przechowywane w tym miejscu i często zużywa ponad 50 procent % miejsca.

Wszystkie tokeny tajne, identyfikatory URI, adresy e-mail powiadomienia, nazwy użytkowników i hasła, które uzyskać wprowadzone podczas konfigurowania Inicjowanie obsługi użytkowników może spowodować przekroczenie limitu magazynu.

## <a name="how-to-work-around-this-issue"></a>Jak obejść ten problem 

Istnieją dwa różne sposoby, aby obejść ten problem, obecnie:

1. **Użyj galerii dwóch wystąpień aplikacji, jeden dla logowania jednokrotnego i jeden dla Inicjowanie obsługi użytkowników** -pobieranie aplikacji galerii [LinkedIn podniesienia uprawnień](active-directory-saas-linkedinelevate-tutorial.md) na przykład można dodać podniesienia uprawnień LinkedIn z galerii i skonfigurować go w celu logowania jednokrotnego. Do inicjowania obsługi, Dodaj inne wystąpienie podniesienia uprawnień LinkedIn z galerii aplikacji Azure AD i nadaj jej nazwę na "Podniesienie uprawnień LinkedIn (inicjowania obsługi administracyjnej)". Dla tego drugiego wystąpienia, należy skonfigurować [inicjowania obsługi administracyjnej](active-directory-saas-linkedinelevate-provisioning-tutorial.md), ale nie logowanie jednokrotne. Korzystając z tego rozwiązania, tym samym użytkowników i grup muszą być [przypisane](manage-apps/assign-user-or-group-access-portal.md) na obydwu aplikacji. 

2. **Zmniejszenie ilości przechowywanych danych konfiguracji** — wszystkie dane wprowadzane [poświadczeń administratora](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) karcie inicjowania obsługi administracyjnej są przechowywane w tym samym miejscu jako certyfikat SAML. Nie może być można zmniejszyć długość wszystkich tych danych, takich jak niektóre pola konfiguracji opcjonalnej **wiadomość E-mail z powiadomieniem** mogą zostać usunięte.

## <a name="next-steps"></a>Następne kroki
[Skonfiguruj użytkownika aprowizację i anulowanie obsługi do aplikacji SaaS](active-directory-saas-app-provisioning.md)
