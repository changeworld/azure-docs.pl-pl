---
title: Jak dostosować reguły synchronizacji w programie Azure AD Connect | Dokumentacja firmy Microsoft
description: Ten temat zawiera procedurę rozwiązywania problemów z instalacją program Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351072"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Jak dostosować regułę synchronizacji

## <a name="recommended-steps"></a>**Zalecane czynności**

Edytor reguł synchronizacji służy do edytowania lub Utwórz nową regułę synchronizacji. Musisz być użytkownikiem zaawansowanym, aby wprowadzić zmiany reguł synchronizacji. Wszelkie zmiany problem może spowodować usunięcie obiektów z katalogiem docelowym. Przeczytaj [zalecane dokumenty](#recommended-documents) uzyskać wiedzę w reguły synchronizacji. Aby zmodyfikować regułę synchronizacji przejść przez następujące kroki:

* Uruchom Edytor synchronizacji aplikacji w menu pulpitu, jak pokazano poniżej:

    ![Menu Synchronization Rule Editor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Aby dostosować domyślną regułę synchronizacji, można sklonować istniejącą regułę, klikając przycisk "Edytuj" w narzędzia Synchronization Rules Editor, Utwórz kopię standardowego domyślną regułę i go wyłączyć. Zapisz sklonowany reguły o priorytecie mniej niż 100.  Priorytet określa, jakie reguły wins (dolna wartość liczbową) rozwiązywania konfliktów, jeśli istnieje konflikt przepływu atrybutu.

    ![Edytor reguł synchronizacji](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Podczas modyfikowania określony atrybut, najlepiej tylko Zachowaj modyfikowanie atrybutów w regule sklonowane.  Następnie włącz reguły domyślnej, aby modyfikacji atrybut pochodzi z sklonowanym reguły i inne atrybuty są pobierane z standardowe reguły domyślnej. 

* Należy pamiętać, że w przypadku, gdy obliczona wartość atrybutu modyfikacji ma wartość NULL w sklonowanym reguły i nie ma wartości NULL w standardzie domyślne reguły następnie nie wartość NULL, zostanie zarejestrowane i zastąpi wartość NULL. Jeśli nie chcesz, aby wartość NULL, być Zamień na wartość inną niż NULL, a następnie przypisz AuthoritativeNull w sklonowanym reguły.

* Aby zmodyfikować **ruchu wychodzącego** reguły, Zmień filtr, za pomocą edytora reguł synchronizacji.

## <a name="recommended-documents"></a>**Zalecane dokumenty**
* [Synchronizacja w programie Azure AD Connect: zagadnienia techniczne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Synchronizacja w programie Azure AD Connect: opis architektury](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Synchronizacja w programie Azure AD Connect: omówienie aprowizacji deklaratywnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Synchronizacja w programie Azure AD Connect: opis wyrażeń związanych z aprowizacją deklaratywną](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Synchronizacja w programie Azure AD Connect: opis konfiguracji domyślnej](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Synchronizacja w programie Azure AD Connect: opis użytkowników, grup i kontaktów](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Synchronizacja w programie Azure AD Connect: atrybuty w tle](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Następne kroki
- [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md).
- [Co to jest tożsamość hybrydowa? ](whatis-hybrid-identity.md).
