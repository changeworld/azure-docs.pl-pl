---
title: Problem z zapisywaniem poświadczeń administratora podczas konfigurowania aplikacji galerii usługi Azure AD
description: Jak rozwiązywać typowe problemy napotykane podczas konfigurowania inicjowania obsługi administracyjnej użytkowników do aplikacji już wymienionej w Galerii aplikacji usługi Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ff80224037001e41daf49cd6fc21439b2cc5cff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522870"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>Problem z zapisywaniem poświadczeń administratora podczas konfigurowania inicjowania obsługi administracyjnej aplikacji usługi Azure Active Directory Gallery 

Podczas korzystania z witryny Azure portal do konfigurowania [automatycznego inicjowania obsługi administracyjnej](user-provisioning.md) dla aplikacji przedsiębiorstwa, może wystąpić sytuacja, w której:

* **Poświadczenia administratora** wprowadzone dla aplikacji są prawidłowe, a przycisk **Testuj połączenie** działa. Jednak poświadczenia nie mogą być zapisywane, a portal Azure zwraca ogólny komunikat o błędzie.

Jeśli samol oparte logowanie jednokrotne jest również skonfigurowany dla tej samej aplikacji, najbardziej prawdopodobną przyczyną błędu jest przekroczenie limitu magazynu wewnętrznego usługi Azure AD dla certyfikatów i poświadczeń.

Usługa Azure AD ma obecnie maksymalną pojemność magazynu 1024 bajtów dla wszystkich certyfikatów, tajnych tokenów, poświadczeń i powiązanych danych konfiguracyjnych skojarzonych z pojedynczym wystąpieniem aplikacji (nazywanym również rekordem jednostkowym usługi w usłudze Azure AD).

Po skonfigurowaniu logowania jednokrotnego opartego na saml certyfikat używany do podpisywania tokenów SAML jest przechowywany w tym miejscu i często zużywa ponad 50% miejsca.

Wszelkie tajne tokeny, identyfikatory URI, adresy e-mail powiadomień, nazwy użytkowników i hasła, które zostały wprowadzone podczas konfigurowania inicjowania obsługi administracyjnej użytkownika, mogą spowodować przekroczenie limitu miejsca.

## <a name="how-to-work-around-this-issue"></a>Jak obejść ten problem 

Istnieją dwa możliwe sposoby obejść ten problem dzisiaj:

1. **Użyj dwóch wystąpień aplikacji galerii, jeden dla logowania jednokrotnego i jeden dla inicjowania obsługi administracyjnej użytkownika** — biorąc aplikację galerii [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) jako przykład, można dodać LinkedIn Elevate z galerii i skonfigurować go do logowania jednokrotnego. W przypadku inicjowania obsługi administracyjnej dodaj kolejne wystąpienie usługi LinkedIn Elevate z galerii aplikacji usługi Azure AD i nadaj mu nazwę "LinkedIn Elevate (provisioning)". W tym drugim wystąpieniu należy skonfigurować [inicjowanie obsługi administracyjnej](../saas-apps/linkedinelevate-provisioning-tutorial.md), ale nie logowania jednokrotnego. Podczas korzystania z tego obejścia, tych samych użytkowników i grup muszą być [przypisane](../manage-apps/assign-user-or-group-access-portal.md) do obu aplikacji. 

2. **Zmniejsz ilość przechowywanych danych konfiguracyjnych** — wszystkie dane wprowadzone w sekcji Poświadczenia administratora na karcie [inicjowanie](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) obsługi administracyjnej są przechowywane w tym samym miejscu co certyfikat SAML. Chociaż może nie być możliwe skrócenie długości wszystkich tych danych, niektóre opcjonalne pola konfiguracji, takie jak **powiadomienie e-mail,** można usunąć.

## <a name="next-steps"></a>Następne kroki
[Konfigurowanie inicjowania obsługi administracyjnej i usuwania obsługi administracyjnej aplikacji SaaS](user-provisioning.md)
